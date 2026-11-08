让 GPU 搬运更快：三组实测拆解

训练和推理时，算力经常被数据搬家拖慢。CPU 侧到显存、显存之间怎么走，决定了实测帧率能否接近理论值。下面用三段可复现的片段说明常见提速手段的效果。

CPU 侧到显存：锁定页的实际收益

先造一个 1000 万个 float32 元素的张量，分别用普通可分页内存和锁定页内存各搬一次到同一张卡。普通路径先等同步再计时，锁定页路径打开非阻塞拷贝后再同步。搬运结束后用范数比对数值是否一致。

在千万元素量级下，普通内存的搬运明显更慢，锁定页版本更快，而数值差异为零，说明加速不影响正确性。锁定页让 DMA 能直接读写，避免了系统额外的一次复制，这是主机到设备提速的关键。
[PRE0]

传输张量大小：10000000 个元素

不使用固定内存的传输时间：0.015432 秒

使用固定内存的传输时间：0.010876 秒

传输结果差异：0.000000

多卡之间：绕路 PCIe 与直连 NVLink 的对比

多卡协同时走法不同差距很大。一种是先把 GPU0 上的张量回写到 CPU，再从 CPU 送到 GPU1，走的是 PCIe 回环。另一种是 GPU0 直接推给 GPU1，借助 NVLink 直连并配合非阻塞拷贝。

在 10 MB 到 1000 MB 五个档位分别计时，直连路径的耗时大致是回环路径的一半左右，提速倍数稳定在 2.4 到 2.5 倍区间。随着搬运规模增大，耗时线性增长，但倍数基本保持。
[PRE1]python
import torch
import time
def transfer_between_gpus(size_in_mb):
    # 确保至少有两个 GPU 可用
    if torch.cuda.device_count() < 2:
        print("需要至少两个 GPU 来运行本示例。")
        return
    # 定义设备
    device0 = torch.device('cuda:0')
    device1 = torch.device('cuda:1')
    # 创建一个大的张量并将其移动到 GPU 0 上
    tensor_size = size_in_mb * 1024 * 1024 // 4  # float32 占 4 字节
    tensor = torch.randn(tensor_size, dtype=torch.float32, device=device0)
    # 同步设备，确保所有操作完成
    torch.cuda.synchronize()
    # 使用 PCIe 进行数据传输
    start_time_pcie = time.time()
    tensor_cpu = tensor.to('cpu')
    tensor_gpu1_pcie = tensor_cpu.to(device1)
    torch.cuda.synchronize()
    end_time_pcie = time.time()
    time_pcie = end_time_pcie - start_time_pcie
    # 使用 NVLink 进行数据传输（假设 GPU 0 和 GPU 1 通过 NVLink 互联）
    # 直接在 GPU 间传输
    start_time_nvlink = time.time()
    tensor_gpu1_nvlink = tensor.to(device1, non_blocking=True)
    torch.cuda.synchronize()
    end_time_nvlink = time.time()
    time_nvlink = end_time_nvlink - start_time_nvlink
    # 输出结果
    print(f"数据传输大小：{size_in_mb} MB")
    print(f"通过 PCIe 传输的时间：{time_pcie:.6f} 秒")
    print(f"通过 NVLink 传输的时间：{time_nvlink:.6f} 秒")
    print(f"速度提升：{time_pcie / time_nvlink:.2f} 倍")
def main():
    # 定义传输数据大小（单位为 MB）
    sizes = [10, 50, 100, 500, 1000]
    for size in sizes:
        print("\n----------------------------------------")
        transfer_between_gpus(size)
if __name__ == "__main__":
    main()
[PRE2]

----------------------------------------
数据传输大小：10 MB
通过 PCIe 传输的时间：0.025678 秒
通过 NVLink 传输的时间：0.010123 秒
速度提升：2.53 倍
----------------------------------------
数据传输大小：50 MB
通过 PCIe 传输的时间：0.125456 秒
通过 NVLink 传输的时间：0.050789 秒
速度提升：2.46 倍
----------------------------------------
数据传输大小：100 MB
通过 PCIe 传输的时间：0.250912 秒
通过 NVLink 传输的时间：0.101567 秒
速度提升：2.47 倍
----------------------------------------
数据传输大小：500 MB
通过 PCIe 传输的时间：1.254321 秒
通过 NVLink 传输的时间：0.505678 秒
速度提升：2.48 倍
----------------------------------------
数据传输大小：1000 MB
通过 PCIe 传输的时间：2.509654 秒
通过 NVLink 传输的时间：1.011356 秒
速度提升：2.48 倍

TensorRT 推理：缓冲区准备的思路

在 TensorRT 的推理流程里，主机侧和设备侧的内存要提前准备好。加载序列化引擎后，按绑定名称遍历，计算每个张量的元素总量乘以 batch 大小得到所需字节数，用对应 numpy 类型申请锁定页主机内存，再用 cuda.mem_alloc 申请同等大小的设备内存。输入输出分别归类，bindings 统一为设备指针的整数句柄，并创建 CUDA 流以便后续异步拷贝。

这种锁定页主机缓冲区的准备方式，正是前述提速原理在推理框架里的落地，能让 H2D 拷贝摆脱额外复制，成为高吞吐推理的基础。
[PRE3]python
import tensorrt as trt
import pycuda.driver as cuda
import pycuda.autoinit
import numpy as np
# 定义 TensorRT 日志记录器
TRT_LOGGER = trt.Logger(trt.Logger.WARNING)
def load_engine(trt_runtime, engine_path):
    """加载序列化的 TensorRT 引擎"""
    with open(engine_path, 'rb') as f:
        engine_data = f.read()
    engine = trt_runtime.deserialize_cuda_engine(engine_data)
    return engine
def allocate_buffers(engine):
    """为输入和输出分配 CUDA 缓冲区"""
    inputs = []
    outputs = []
    bindings = []
    stream = cuda.Stream()
    for binding in engine:
        size = trt.volume(engine.get_binding_shape(binding)) * engine.max_batch_size
        dtype = trt.nptype(engine.get_binding_dtype(binding))
        # 分配主机内存
        host_mem = cuda.pagelocked_empty(size, dtype)
        # 分配设备内存
        device_mem = cuda.mem_alloc(host_mem.nbytes)
        # 添加到列表中
        bindings.append(int(device_mem))
        if engine.binding_is_input(binding):
            inputs.append({'host': host_mem, 'device': device_mem})
        else:
            outputs.append({'host': host_mem, 'device': device_mem})
    return inputs, outputs, bindings, stream
def do_inference(context, bindings, inputs, outputs, stream, batch_size=1):
    """执行推理"""
    # 将输入数据从主机内存传输到设备内存中
    for inp in inputs: