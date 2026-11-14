一开始，TensorFlow 仅仅是 Keras 的后台（Backend）之一。但在 TensorFlow 1.10 版本之后，TensorFlow 开始引入 tf.keras 子模块，这是将 Keras 集成在 TensorFlow 中的第一步。2019 年 6 月，Google 发布 TensorFlow 2，直接宣布 Keras 成为 TensorFlow 的官方高级 API 的一部分。

鉴于 Keras 和 TensorFlow 有“合二为一”的趋势，所以，在本书后续章节的代码，将会是这两大框架的混合使用。

# 3.3.3 Caffe

Caffe 的成名，最早源于在加州大学伯克利分校（University of California, Berkeley）博士生贾扬清撰写的一篇论文——Caffe: Convolutional architecture for fast feature embedding[3]。顾名思义，Caffe 的主要用途体现在“用于快速特征提取的卷积架构”。这篇有关 Caffe 的论文，一经面世，就受到世人的极大关注，“谷歌学术”上显示它的引用次数已超过 14 000 次，可见其受关注程度之高。目前，Caffe 已被广泛应用在工业界和学术界，Caffe 的图标如图 3-5 所示。

设计之初，Caffe 仅关注计算机视觉领域问题的处理，因此，可将其视为一个面向图像处理的专用卷积神经网络（CNN）框架。但随着它的开源（遵循 BSD 协议），社区人员不断“添砖加瓦”，让它的通用性日臻完美，它也开始在文本、语音及时间序列数据等领域，有着非凡的表现。

Caffe 的一个突出优势是，它有一个模型百宝箱——Model Zoo（模型动物园），在这个园子里，汇集了大量已经训练好的经典模型，如 AlexNet、VGG、Inception、ResNet 等。这样一来，一些常用模型根本无须用户训练，选好拿来就可使用，节省了大量的模型训练时间。如果说高度封装性成就了 Keras 的“极简主义”，那么 Model Zoo 成就了 Caffe 的“极速主义”。

“黄金无足色，白璧有微瑕”。Caffe 自然也有其不足之处。首先，它不够灵活，模型虽然可以拿来就用，但倘若需要对模型做一些小的变更，都需要用户利用 C++ 和 CUDA 更新底层代码。

其次，源于其诞生基因，Caffe 天生对卷积神经网络有着卓越的支持，但对于递归神经网络（Recurrent Neural Network，RNN）和“长短期记忆”（Long Short-Term Memory，LSTM）的支持，表现不佳。

目前，Caffe 慢慢退出深度学习框架的前台，逐渐变成 PyTorch 的后台（Backend）。

![](bbox=[0.78, 0.38, 0.98, 0.46])