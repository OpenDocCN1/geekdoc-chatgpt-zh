![](bbox=[0.133, 0.098, 0.879, 0.419])

图4.19 手势识别示意图

本项目采用深度学习算法实现人手检测和手势识别。

(1) 人手检测。YOLO、SSD、Faster R-CNN等模型或算法在目标检测方面的速度较快、精度较高，但是这些模型或算法比较大，不适合移植到移动端或嵌入式设备。

本项目的人手检测是基于纳米检测网络

(Nano Detecting Network, NanoDet) 实现的，NanoDet是一种 FCOS式的单阶段无锚节点（Anchor-Free）的目标检测模型，它使用 ATSS（Adaptive Training Sample Selection）方法进行目标采样，使用Generalized Focal Loss函数执行分类和边框回归（Box Regression），实现了高性能的目标检测，同时保持了模型的小尺度和低计算复杂性，特别适用于嵌入式设备和移动端应用。 NanoDet模型的结构如图4.20所示