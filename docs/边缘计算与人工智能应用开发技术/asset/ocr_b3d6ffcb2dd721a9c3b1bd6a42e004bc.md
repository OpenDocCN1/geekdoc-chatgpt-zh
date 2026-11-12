(5) Tenigne Lite。Tenigne Lite是OpenAILab推出的边缘端推理框架，OpenCV在嵌入式设备上首推Tenigne Lite，该框架对RISC-V、CUDA、TensorRT、NPU的支持非常不错。Tenigne Lite的主要调包语言有C/C++、Python，支持的模型包括TensorFlow、ONNX、Darknet、MXNet、NCNN、Caffe、TF-Lite、NCNN。

(6) NNIE。NNIE (Neural Network Inference Engine) 是海思SVP开发框架中的处理单元之一，主要用于深度学习卷积神经网络的加速处理，可用于图像分类、目标检测等AI应用场景。NNIE支持大部分公开的卷积神经网络模型，如AlexNet、VGG16、ResNet18、ResNet50、GoogleNet等分类网络，Faster R-CNN、YOLO、SSD、RFCN等检测目标网络，以及FCN、SegNet等分割场景网络。目前NNIE及工具链仅支持Caffe框架，使用其他框架的模型需要转化为Caffe框架的模型。

(7) RKNN。RKNN (Rockchip Neural Network) 是Rockchip推出的一个用于嵌入式设备的神经网络框架。Rockchip提供的RKNN-Toolkit开发套件可进行模型转换、推理运行和性能评估，支持将Caffe、TensorFlow、TensorFlow Lite、ONNX、Darknet框架的模型转换与RKNN框架的模型。

# 3.4.1.3 NCNN框架

NCNN是由腾讯优图实验室于2017年公布的开源项目，旨在提供一个面向移动端、无第三方依赖、跨平台的高性能神经网络前向推理框架。与TF-lite不同的是，NCNN主要针对CPU进行优化，使用ARM NEON指令集实现CNN中的卷积层、全连接层、池化层等。NCNN有自己的模型文件格式，支持大部分主流深度学习框架训练的模型转换，也支持Int8量化和推理，在最新的版本中实现了Int8 Winograd-f43的内核优化，极大提升了量化模型在ARM上的推理速度。NCNN框架的模型转换与部署流程如图3.41所示，将训练后的模型通过NCNN框架模型转换工具，生成对应格式的文件，再通过模型加载完成部署，对比TF-Lite，NCNN对模型的训练框架和部署平台没有太多要求，极大满足了用户在各类型设备上部署模型的需求。