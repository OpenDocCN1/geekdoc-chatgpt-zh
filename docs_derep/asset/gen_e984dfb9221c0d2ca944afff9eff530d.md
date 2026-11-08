轻量部署与加速实战：TFLite 与 TensorRT

模型训好只是起点，真正能跑在设备上才算完成闭环。下面用两条常见路线演示如何把训练好的网络变成高效可用的推理程序，思路清晰、可直接复用。

先看移动端轻量化这条路。先搭一个基于 Keras 的简易序列网络，输入维度 256，经过 128 单元的 ReLU 隐藏层后接 10 维 Softmax 输出。用随机生成的数据做一次模拟训练，采用 Adam 优化器配合稀疏交叉熵损失，批次 32、迭代 1轮即可。训练完借助 TFLiteConverter.from_keras_model 把模型转成 TFLite 格式并写到 model.tflite。随后用 Interpreter 加载文件、调用 allocate_tensors 分配运行时内存，通过 get_input_details 与 get_output_details 拿到张量索引。准备一条 1x256 的随机浮点输入，set_tensor 写入后用 time 记时调用 invoke，完成一次前向。取回输出张量同时打印耗时，就能直观看到转换后的推理延迟。

[PRE0]
推理结果: [[2.1054237e-04 3.1453052e-04 1.2159267e-03 6.2069073e-03
  5.0875170e-03 1.0544764e-01 1.2382839e-01 2.5518432e-03
  2.3557038e-01 5.8977122e-01]]
TensorFlow Lite 推理时间: 0.0213 秒

再看服务器端高性能加速的另一条路。以 PyTorch 为例，设计一个两层卷积的小型 CNN：首层 3 入 32 出 3x3 保持填充，第二层 32 入 64 出同核，中间各接 2x2 最大池化，展平后经 128 维全连接再到 10 类输出，激活使用 ReLU。训练时在指定设备上迭代数据，记录每百批损失并在结束后提示完成；评测时关闭梯度统计准确率。训练完成后用 torch.onnx.export 把权重固化为 ONNX，指定 opset 11 并打开常量折叠。随后用 TensorRT 的 Builder 读取 ONNX，通过 OnnxParser 解析网络，配置 1GB 工作空间并开启 FP16，构建序列化引擎并保存为 model.trt。

[PRE1]python
import torch
import torch.nn as nn
import torch.optim as optim
import torchvision
import torchvision.transforms as transforms
import time
import numpy as np
import tensorrt as trt
import pycuda.driver as cuda
import pycuda.autoinit  # 初始化 CUDA 驱动
# 定义一个简单的卷积神经网络模型
class SimpleCNN(nn.Module):
    def __init__(self):
        super(SimpleCNN, self).__init__()
        # 卷积层
        # 输入通道为 3，输出通道为 32，卷积核为 3*3
        self.conv1=nn.Conv2d(3, 32, kernel_size=3, padding=1)
        # 输入通道为 32，输出通道为 64，卷积核为 3*3
        self.conv2=nn.Conv2d(32, 64, kernel_size=3, padding=1)
        # 全连接层
        self.fc1=nn.Linear(64*8*8, 128)  # 输入特征为 64*8*8，输出特征为 128
        self.fc2=nn.Linear(128, 10)  # 输入特征为 128，输出特征为 10（类别数）
        # 激活函数
        self.relu=nn.ReLU()
        # 池化层
        self.pool=nn.MaxPool2d(2, 2)  # 2*2 最大池化
    def forward(self, x):
        x=self.relu(self.conv1(x))  # 卷积 1+ReLU
        x=self.pool(x)  # 池化 1
        x=self.relu(self.conv2(x))  # 卷积 2+ReLU
        x=self.pool(x)  # 池化 2
        x=x.view(-1, 64*8*8)  # 展平
        x=self.relu(self.fc1(x))  # 全连接 1+ReLU
        x=self.fc2(x)  # 全连接 2
        return x
# 训练函数
def train_model(model, trainloader, criterion, optimizer, device, epochs=5):
    model.to(device)
    model.train()
    for epoch in range(epochs):
        running_loss=0.0
        for i, data in enumerate(trainloader, 0):
            inputs, labels=data[0].to(device), data[1].to(device)
            optimizer.zero_grad()
            outputs=model(inputs)
            loss=criterion(outputs, labels)
            loss.backward()
            optimizer.step()
            running_loss += loss.item()
            if i % 100 == 99:  # 每 100 批输出一次
                print(f"[Epoch {epoch+1}, Batch {i+1}] 损失: {running_loss/100:.3f}")
                running_loss=0.0
    print("训练完成")
# 测试函数
def test_model(model, testloader, device):
    model.to(device)
    model.eval()
    correct=0
    total=0
    with torch.no_grad():
        for data in testloader:
            images, labels=data[0].to(device), data[1].to(device)
            outputs=model(images)
            _, predicted=torch.max(outputs.data, 1)
            total += labels.size(0)
            correct += (predicted == labels).sum().item()
    print(f"测试准确率: {100*correct/total:.2f}%")
# 将 PyTorch 模型导出为 ONNX 格式
def export_onnx(model, device, onnx_file_path='model.onnx'):
    model.to(device)
    model.eval()
    dummy_input=torch.randn(1, 3, 32, 32).to(device)
    torch.onnx.export(model, dummy_input, onnx_file_path,
                      export_params=True,
                      opset_version=11,
                      do_constant_folding=True,
                      input_names=['input'],
                      output_names=['output'])
    print(f"模型已被导出为 ONNX 格式, 路径: {onnx_file_path}")
# 使用 TensorRT 构建优化的推理引擎
def build_engine(onnx_file_path, engine_file_path='model.trt'):
    TRT_LOGGER=trt.Logger(trt.Logger.WARNING)
    builder=trt.Builder(TRT_LOGGER)
    network=builder.create_network(1 << int(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH))
    parser=trt.OnnxParser(network, TRT_LOGGER)
    with open(onnx_file_path, 'rb') as model:
        if not parser.parse(model.read()):
            print('ERROR: Failed to parse the ONNX file.')
            for error in range(parser.num_errors):
                print(parser.get_error(error))
            return None
    config=builder.create_builder_config()
    config.max_workspace_size=1 << 30  # 1GB
    config.set_flag(trt.BuilderFlag.FP16)  # 启用 FP16 模式
    engine=builder.build_engine(network, config)
    if engine is None:
        print("Failed to create the engine")
        return None
    with open(engine_file_path, 'wb') as f:
        f.write(engine.serialize())
    print(f"TensorRT 引擎已被构建并保存, 路径: {engine_file_path}")
    return engine
# 使用 TensorRT 进行推理

推理时从引擎创建执行上下文，在 GPU 上申请输入输出缓冲区并用 CUDA Stream 异步拷贝输入，调用 execute_async_v2 启动计算，完成后把结果拷回 CPU 进行后续处理。这种方式充分利用显存和流并行，是高吞吐部署的常用做法。

[PRE2]python
def infer(engine, inputs):
    TRT_LOGGER = trt.Logger(trt.Logger.WARNING)
    # 创建执行上下文
    context = engine.create_execution_context()
    # 分配 GPU 内存
    d_input = cuda.mem_alloc(1 * inputs.nbytes)
    # 假设输出为 10 个类别，每个类别占用 4 字节的内存空间
    d_output = cuda.mem_alloc(1 * 10 * 4)
    bindings = [int(d_input), int(d_output)]
    stream = cuda.Stream()
    # 将输入数据复制到 GPU 上
    cuda.memcpy_htod_async(d_input, inputs, stream)
    # 执行推理
    context.execute_async_v2(bindings=bindings, stream_handle=stream.handle)
    # 创建输出数组
    output = np.empty(10, dtype=np.float32)
    # 将输出数据复制回 CPU