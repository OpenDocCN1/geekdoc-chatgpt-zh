# 2.5.3 姿态估计

姿态估计（Pose Estimation）可视为图像或视频中人体关节位置（也称为关键点，如手肘、膝盖、肩膀等）的定位问题，因此也称为人体关键点检测。在 MSCOCO 数据集中，人体姿态关键点格式如图 2-52 所示。

![](bbox=[0.095, 0.225, 0.37, 0.435])

+   0: nose
+   1: neck
+   2: right shoulder
+   3: right elbow
+   4: right wrist
+   5: left shoulder
+   6: left elbow
+   7: left wrist
+   8: right hip

+   9: right knee
+   10: right ankle
+   11: left hip
+   12: left knee
+   13: left ankle
+   14: right eye
+   15: left eye
+   16: right ear
+   17: left ear

图 2-52 MSCOCO 数据集中的人体关键点

姿态估计一般分为单人姿态估计（Single Person Pose Estimation，SPPE）和多人姿态估计（Multiple Person Pose Estimation，MPPE）。姿态估计算法与姿态估计数据集密切相关，早期的姿态估计数据集一般都是单人的，因此当时的姿态估计主要集中于 SPPE，但 MPPE 更符合实际情况，随着更多 MPPE 数据集的出现，针对 MPPE 的研究也越来越多。

多人姿态估计方法又分为两类：1）Top-Down 方法。首先利用目标检测算法检测图像中的多个人，然后使用 SPPE 模型估计每一个人的姿态，这种方法的优点是精度高、易于实现，缺点是依赖于目标检测算法的效果，无法很好地解决遮挡问题，并且速度会随着图像场景中人数的增加而变慢。2）Bottom-Up 方法。首先检测出图像中所有人体关键点，然后将关键点分配给不同的人，这种方法的优点是整个图像只需要处理一次，且速度不受图像场景中人数变化影响，缺点是精度不如 Top-Down 方法，同样无法很好地解决遮挡问题。

DeepPose[33] 是使用深度神经网络进行人体姿态识别的开山之作，它将姿态估计问题转换为图像的卷积特征提取与关键点坐标位置的回归问题。DeepPose 的结构由 AlexNet 加上额外的全连接层组成，将输出投影到 2k 维度，直接对表征人体 k 个关键点的坐标进行回归预测，如图 2-53 所示。不同于分类任务，DeepPose 使用 L2 作为损失函数进行训练。

DeepPose 将姿态估计问题看作回归任务，使用函数 \(\varphi(x;\theta) \in \mathbb{R}^{2k}\)，\(x\) 代表图像，\(\theta\) 代表模型的参数，\(k\) 个关键点的坐标 \(y=(y_1^T,y_2^T,\cdots,y_i^T,\cdots,y_k^T)^T\)，其中 \(y_i\) 表示第 \(i\) 个关键点的坐标，其中包含横坐标和纵坐标。损失函数采用 L2 损失：

```
\(\arg\min \sum_{(x_i) \in \Omega_y} \sum_{i=1}^k \| y_i - \varphi_i(x;\theta) \|_2^2 \quad (2-47)\)
```