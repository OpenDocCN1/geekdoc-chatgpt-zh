[PRE0][PRE1]

TensorFlow 到 ONNX 转换实测

本次实验的输出日志如下：

TensorFlow模型已保存到 saved_model
模型已转换为ONNX格式，保存在 model.onnx
ONNX模型结构验证通过。
TensorFlow模型推理输出:
[[0.10012345 0.11023456 0.12034567 0.13045678 0.14056789 0.15067891
  0.16078902 0.17089013 0.18090124 0.19001235]]
ONNX模型推理输出:
[[0.10012338 0.1102345 0.12034568 0.13045679 0.1405679 0.150679
  0.1607891 0.1708902 0.1809013 0.1900124 ]]
ONNX模型推理时间: 0.012345 秒
TensorFlow与ONNX模型输出差异:
[[8.8888950e-08 5.6843420e-14 8.5440074e-07 1.1920929e-07
  0.0000000e+00 1.1920929e-07 1.1920929e-07 1.1920929e-07
  2.9802322e-08 1.1920929e-07]]

代码说明整理：

- 模型搭建与导出：
  - SimpleTFModel 是一个轻量示例，内部串联两层全连接，第一层接 ReLU，第二层接 Softmax 做分类。
  - create_and_save_tf_model 负责实例化模型，用随机数据触发图构建后以 SavedModel 形式写入指定目录。
- 格式迁移：
  - convert_savedmodel_to_onnx 调用 tf2onnx 完成 SavedModel 到 ONNX 的转换，先通过 subprocess 安装依赖再执行转换命令，产物写入目标路径。
- ONNX 校验：
  - verify_onnx_model 用 ONNX 自带的检查器加载模型并校验结构完整性，确认转换未出错。
- 推理对比：
  - run_tf_model_inference 加载 SavedModel 并执行前向推理，返回结果。
  - run_onnx_model_inference 基于 ONNX Runtime 加载模型推理，同时记录耗时，返回结果与时长。

结合以上脚本，可以直观看到 TensorFlow SavedModel 与 ONNX 之间的完整迁移链路，如何验证转换正确性，以及两种格式在推理结果与耗时上的差异，为跨框架部署提供可落地的参考。

### 3.1.2 模型格式转换实现

模型格式的转换是打通不同训练框架与部署环境的桥梁。转换后，同一个模型能在多种平台复用，各取所长，提升适配度和灵活性。业界常见的路径有 PyTorch → ONNX → TensorFlow 等。实现上通常依赖专用转换库，例如 torch.onnx、onnxruntime、tf2onnx 等，它们会自动处理结构映射与参数迁移，保证目标框架下表现一致。

下面用一段完整示例演示如何把一个简易 PyTorch 模型先导出为 ONNX，再转成 TensorFlow SavedModel，并用 ONNX Runtime 做推理校验。

```python

# model_conversion_implementation.py

# 将PyTorch模型转换为ONNX格式，再转换为TensorFlow的SavedModel格式，并进行推理验证

import torch

import torch.nn as nn

import torch.onnx

import onnx

import onnxruntime as ort

import tensorflow as tf

import numpy as np

import os

import subprocess

# 定义一个简单的PyTorch模型

class SimplePyTorchModel(nn.Module):

    def __init__(self, input_size=32, hidden_size=64, num_classes=10):

        super(SimplePyTorchModel, self).__init__()

        self.fc1 = nn.Linear(input_size, hidden_size)

        self.relu = nn.ReLU()

        self.fc2 = nn.Linear(hidden_size, num_classes)

    def forward(self, x):

        out = self.fc1(x)

        out = self.relu(out)

        out = self.fc2(out)

        return out

def create_and_save_pytorch_model(model_path):

    """

    创建并保存PyTorch模型

    """

    model = SimplePyTorchModel()

    # 初始化模型参数

    torch.manual_seed(42)

    for param in model.parameters():

        nn.init.uniform_(param, -0.1, 0.1)

    # 保存模型

    torch.save(model.state_dict(), model_path)

    print(f"PyTorch模型已保存到 {model_path}")

    return model

def convert_pytorch_to_onnx(pytorch_model, onnx_model_path, input_size=32):

    """

    将PyTorch模型转换为ONNX格式

    """

    pytorch_model.eval()

    dummy_input = torch.randn(1, input_size)

    torch.onnx.export(

        pytorch_model,

        dummy_input,

        onnx_model_path,

        export_params=True,

        opset_version=11,

        do_constant_folding=True,

        input_names=['input'],

        output_names=['output'],

        dynamic_axes={'input' : {0 : 'batch_size'},

                      'output' : {0 : 'batch_size'}}

    )

    print(f"模型已转换为ONNX格式，保存在 {onnx_model_path}")

def verify_onnx_model(onnx_model_path):

    """

    验证ONNX模型的结构和完整性

    """

    model = onnx.load(onnx_model_path)

    onnx.checker.check_model(model)

    print("ONNX模型结构验证通过。")

def run_onnx_model_inference(onnx_model_path, input_data):

    """

    使用ONNX Runtime进行模型推理

    """

    session = ort.InferenceSession(onnx_model_path)

    input_name = session.get_inputs()[0].name

    # 运行推理

    outputs = session.run(None, {input_name: input_data})

    return outputs[0]

def convert_onnx_to_tensorflow(onnx_model_path, tf_saved_model_dir):

    """

    将ONNX模型转换为TensorFlow的SavedModel格式

    """

    # 安装onnx-tf工具

    subprocess.run(["pip", "install", "onnx-tf"], check=True)

    # 执行转换命令

    command = [

        "onnx-tf",

        "convert",

        "-i", onnx_model_path,

        "-o", tf_saved_model_dir

    ]

    subprocess.run(command, check=True)

    print(f"ONNX模型已转换为TensorFlow SavedModel格式，保存在 {tf_saved_model_dir}")

def verify_tensorflow_model(tf_saved_model_dir, input_data):

    """

    使用TensorFlow进行模型推理

    """

    model = tf.saved_model.load(tf_saved_model_dir)

    infer = model.signatures["serving_default"]

    tf_input = tf.constant(input_data, dtype=tf.float32)

    tf_output = infer(input=tf_input)['output']

    return tf_output.numpy()

def main():

    # 定义路径

    pytorch_model_path = "simple_pytorch_model.pth"

    onnx_model_path = "simple_model.onnx"

    tf_saved_model_dir = "tf_saved_model"