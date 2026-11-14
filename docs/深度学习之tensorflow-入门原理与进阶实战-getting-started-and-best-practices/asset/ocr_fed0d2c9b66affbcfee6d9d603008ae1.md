得到相关包后解压，直接复制到 cuda 路径对应的文件夹下面就行，如图 2-9 所示。

![](bbox=[0.135, 0.132, 0.875, 0.322])

# 图 2-9 安装 cuDNN

# 2.3.3 测试显卡

这里再额外介绍两个小命令，它可以检测出在安装过程中产生的问题。

# 1. 使用nvidia-smi命令查看显卡信息

nvidia-smi 指的是 NVIDIA System Management Interface。在安装完成 NVIDIA 显卡驱动之后，对于 Windows 用户而言，cmd 命令行界面还无法识别 nvidia-smi 命令，需要将相关环境变量添加进去。如果将 NVIDIA 显卡驱动安装在默认位置，nvidia-smi 命令所在的完整路径应为：

```
C:\Program Files\NVIDIA Corporation\NVSMI
```

将上述路径添加进 Path 系统环境变量中。之后在 cmd 中运行 nvidia-smi 命令，可以看到显卡信息如图 2-10 所示。

![](bbox=[0.22, 0.682, 0.785, 0.918])

# 图 2-10 显卡信息