# 2.4.2 安装 CUDA |

CUDA 是显卡厂商 NVIDIA 推送的基于新的并行编程模型和指令集架构的通用并行计算框架，能利用 NVIDIA GPU 的并行计算引擎进行复杂的并行计算。安装 CUDA 的操作步骤如下：

步骤 01 如果已经确认系统已有支持 CUDA 的显卡，这时可以到 NVIDIA 官方网站 https://developer.nvidia.com/cuda-toolkit-archive 下载 CUDA，如图 2-20 所示。

![](bbox=[0.18, 0.09, 0.78, 0.39])

图 2-19

![](bbox=[0.2, 0.6, 0.77, 0.78])

图 2-20

注意 安装 CUDA Driver 时，需要与 NVIDIA GPU Driver 的版本驱动一致，CUDA 才能找到显卡。

步骤 02 根据实际情况选择合适的版本，这里下载 CUDA10 的本地安装包，如图 2-21 所示选择操作系统版本。