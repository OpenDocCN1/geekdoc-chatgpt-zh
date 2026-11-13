![](bbox=[0.262, 0.126, 0.76, 0.362])

![](bbox=[0.132, 0.382, 0.632, 0.622])

| 数据复用模式 | |
|---|---|
| CONV1 | <WR, 8, 8, 16, 1> |
| CONV1 | <WR, 8, 16, 4, 1> |
| CONV2 | <IR, 16, 6, 4, 4> |
| CONV3 | <WR, 4, 4, 64, 3> |
| CONV4 | <IR, 13, 13, 4, 4> |
| CONV5 | <IR, 13, 13, 4, 4> |
| FC1 | <WR, 4, 4, 64, 100> |
| FC2 | <WR, 4, 4, 64, 100> |
| FC3 | <WR, 4, 4, 64, 100> |

图 5.11 访存次数和能耗比较：混合模式以及 3 种基本数据复用模式

# 参考 文 献

+   [1] Cong J, Xiao B. Minimizing computation in convolutional neural networks[C]// International Conference on Artificial Neural Networks. Cham: Springer, 2014: 281-290.

+   [2] Chen Y H, Krishna T, Emer J S, et al. Eyeriss: An energy-efficient reconfigurable accelerator for deep convolutional neural networks[J]. IEEE Journal of Solid-State Circuits, 2017, 52 (1): 127-138.

+   [3] Lee J, Lee J, Han D, et al. 7.7 LNPU: A 25.3 TFLOPS/W sparse deep-neural-network