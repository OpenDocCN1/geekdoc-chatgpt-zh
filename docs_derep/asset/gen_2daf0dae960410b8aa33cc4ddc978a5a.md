训练-导出-加速：一次搞懂 TF / PyTorch → ONNX → TensorRT 的完整链路

想把模型从实验室搬到生产，绕不开两件事：先让不同框架的模型能互通，再用 TensorRT 把推理压到极致。下面这套流程把这两件事串起来，从随机数据的演示模型开始，先导出为 ONNX 再用 ONNX Runtime 验证，最后编译成 TensorRT 引擎并用异步流跑推理。

先看 TensorRT 推理时的骨架。整个过程围绕一个 CUDA 流展开，输入先从主机异步搬到设备，执行上下文在流上触发异步推理，输出再异步搬回主机，最后同步流拿到完整结果。主程序负责加载已编译的 engine、分配输入输出缓冲区、创建执行上下文、填入随机测试数据并调用推理函数，只输出前几个值用于快速检查。

        cuda.memcpy_htod_async(inp['device'], inp['host'], stream)

    # 执行推理

    context.execute_async(batch_size=batch_size, bindings=bindings,

                          stream_handle=stream.handle)

    # 将输出数据从设备内存传回主机内存

    for out in outputs:

        cuda.memcpy_dtoh_async(out['host'], out['device'], stream)

    # 同步流

    stream.synchronize()

    # 返回输出数据

    return [out['host'] for out in outputs]

def main():

    engine_path = "optimized_model.engine"  # 指定 TensorRT 引擎路径

    trt_runtime = trt.Runtime(TRT_LOGGER)  # 创建 TensorRT 运行时

    engine = load_engine(trt_runtime, engine_path)  # 加载引擎

    inputs, outputs, bindings, stream = allocate_buffers(engine)  # 分配缓冲区

    context = engine.create_execution_context()  # 创建推理上下文

    # 准备输入数据（示例：随机数据）

    input_data = np.random.random(size=inputs[0]['host'].shape).astype(np.float32)

    inputs[0]['host'] = input_data

    # 执行推理

    output = do_inference(context, bindings, inputs, outputs, stream)

    # 输出结果

    print("推理结果:", output[0][:5])  # 输出前 5 个推理结果

if __name__ == "__main__":

    main()

下面是跨框架导出的演示。先用 TensorFlow 搭建一个简单的两层全连接网络，用随机数据做一次快速拟合，然后把模型保存并转成 ONNX。再用 PyTorch 定义同等结构的 SimpleModel，通过 torch.onnx.export 直接导出。导出完成后统一用 ONNX Runtime 加载，构造随机输入、获取输入输出名称、计时执行并打印结果。

[PRE0]python

import tensorflow as tf

import torch

[PRE1]python

import torch.onnx

import numpy as np

import onnx

import onnxruntime as ort

import time

# 1. 使用 TensorFlow 训练并导出模型

def tensorflow_export_model():

    model=tf.keras.Sequential([

        tf.keras.layers.Dense(64, activation='relu', input_shape=(256,)),

        tf.keras.layers.Dense(10, activation='softmax')

    ])

    # 使用随机数据训练模型

    x_train=np.random.rand(1000, 256).astype(np.float32)

    y_train=np.random.randint(0, 10, 1000)

    model.compile(optimizer='adam', loss='sparse_categorical_crossentropy',

                  metrics=['accuracy'])

    model.fit(x_train, y_train, epochs=1, batch_size=32)

    # 导出模型为 ONNX 格式

    onnx_model_path="tensorflow_model.onnx"

    model.save("tensorflow_model.h5")

    # 将 TensorFlow 模型转换为 ONNX 格式

    model_onnx=tf.saved_model.load("tensorflow_model")

    tf.export_to_saved_model(model_onnx, onnx_model_path)

# 2. 使用 PyTorch 训练并导出模型

def pytorch_export_model():

    class SimpleModel(torch.nn.Module):

        def __init__(self):

            super(SimpleModel, self).__init__()

            self.fc1=torch.nn.Linear(256, 64)

            self.fc2=torch.nn.Linear(64, 10)

        def forward(self, x):

            x=torch.relu(self.fc1(x))

            return self.fc2(x)

    # 初始化模型

    model=SimpleModel()

    dummy_input=torch.randn(1, 256)

    # 导出为 ONNX 格式

    onnx_model_path="pytorch_model.onnx"

    torch.onnx.export(model, dummy_input, onnx_model_path)

# 3. 加载 ONNX 模型并进行推理

def inference_with_onnx_model(model_path):

    # 使用 ONNX Runtime 进行推理

    ort_session=ort.InferenceSession(model_path)

    # 创建随机输入数据

    input_data=np.random.randn(1, 256).astype(np.float32)

    # 获取输入和输出名称

    input_name=ort_session.get_inputs()[0].name

    output_name=ort_session.get_outputs()[0].name

    # 推理并记录时间

    start_time=time.time()

    result=ort_session.run([output_name], {input_name: input_data})

    end_time=time.time()

    print(f"推理结果: {result[0]}")

    print(f"推理时间: {end_time-start_time:.4f}秒")

# 4. 执行导出和推理

def main():

    # 导出 TensorFlow 模型并进行推理

    tensorflow_export_model()

    print("TensorFlow 模型已被导出为 ONNX 格式")

    inference_with_onnx_model("tensorflow_model.onnx")

    # 导出 PyTorch 模型并进行推理

    pytorch_export_model()

    print("PyTorch 模型已被导出为 ONNX 格式")

    inference_with_onnx_model("pytorch_model.onnx")

if __name__ == "__main__":

    main()

实际运行的效果如下，说明两条路径都成功转成 ONNX 并能在 ONNX Runtime 下跑通，耗时在毫秒级。

[PRE2]

TensorFlow 模型已被导出为 ONNX 格式

推理结果：[[-0.10240851  0.13483295  0.08943611 ...  0.22133872  0.03187955

  0.21149679]]

推理时间：0.0221 秒

PyTorch 模型已被导出为 ONNX 格式

推理结果：[[-0.13224456  0.11265323  0.08592474 ...  0.23813489  0.05672891

  0.21123167]]

推理时间：0.0243 秒

验证通过后就可以进入加速阶段。用 TensorRT 把 ONNX 编译成可在 GPU 上高效运行的引擎。这里先创建 Builder、Network 和 OnnxParser，限制工作空间和批次大小，解析 ONNX 文件，构建 CUDA 引擎并序列化保存；加载时则反序列化得到可用的 engine。缓冲区分配函数负责为输入输出准备好对应的设备内存列表，为后续异步拷贝和推理做准备。

[PRE3]python

import tensorrt as trt

import pycuda.driver as cuda

import pycuda.autoinit

import numpy as np

import time

import onnx

import sys

# 定义 TensorRT 日志记录器

TRT_LOGGER=trt.Logger(trt.Logger.WARNING)

def build_engine(onnx_file_path, engine_file_path="optimized_model.trt"):

    """从 ONNX 模型构建 TensorRT 引擎"""

    with trt.Builder(TRT_LOGGER) as builder, builder.create_network(

        1 << int(trt.NetworkDefinitionCreationFlag.EXPLICIT_BATCH)) as network, trt.OnnxParser(network, TRT_LOGGER) as parser:

        builder.max_workspace_size=1 << 30  # 设置最大工作空间为 1GB

        builder.max_batch_size=1  # 设置最大批次大小

        # 读取 ONNX 模型文件

        with open(onnx_file_path, 'rb') as model:

            if not parser.parse(model.read()):

                print('ERROR: Failed to parse the ONNX file.')

                for error in range(parser.num_errors):

                    print(parser.get_error(error))

                return None

        # 构建 TensorRT 引擎

        engine=builder.build_cuda_engine(network)

        if engine is None:

            print("Failed to create the engine")

            return None

        # 将引擎保存到文件中

        with open(engine_file_path, "wb") as f:

            f.write(engine.serialize())

        print(f"TensorRT 引擎已被保存至 {engine_file_path}")

        return engine

def load_engine(engine_file_path):

    """加载已保存的 TensorRT 引擎"""

    with open(engine_file_path, "rb") as f, trt.Runtime(TRT_LOGGER) as runtime:

        engine=runtime.deserialize_cuda_engine(f.read())

        return engine

def allocate_buffers(engine):

    """为 TensorRT 引擎分配输入/输出缓冲区"""

    inputs=[]

    outputs=[]

    bindings=[]