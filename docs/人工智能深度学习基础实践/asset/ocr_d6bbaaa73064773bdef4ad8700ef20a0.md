② 在打开的命令提示符窗口中运行以下命令，确认 Python 的版本是否满足要求，要求 Python 版本为 3.6/3.7/3.8/3.9。

```
python --version
```

输出结果如下。

```
Python 3.8.1
```

③ 运行以下命令，确认 pip 的版本是否满足要求，要求 pip 版本为 20.2.2 或更高版本。

```
python -m pip --version
```

④ 若 pip 版本未满足要求，则可运行以下命令更新 pip 版本。

```
python -m pip install --upgrade pip
```

⑤ 运行以下命令，确认 Python 和 pip 是否为 64 位，并且处理器架构是 x86_64（或称作 x64、Intel64、AMD64）架构，目前 PaddlePaddle 不支持 arm64 架构。

```
python-c "import platform;print (platform.architecture() [0]);print (platform.machine()) "
```

输出结果如下。

```
64bit
AMD64
```

# 步骤 3：安装 PaddlePaddle

所需 Python 和 pip 环境安装和验证完成后，即可在 Windows 系统上使用 pip 安装 PaddlePaddle，具体步骤如下。

+   （1）选择需要安装的 PaddlePaddle 版本，以下是相关说明。

+   ① 如果计算机没有 NVIDIA GPU，请安装 CPU 版的 PaddlePaddle。

+   ② 如果计算机有 NVIDIA GPU，请确保满足以下条件并且安装 GPU 版的 PaddlePaddle。

+   * CUDA 工具包 10.1/10.2 配合深度学习加速库 cuDNN v7.6.5+。注意，有关 CUDA 的知识在知识拓展部分有相关解释。
+   * CUDA 工具包 11.2 配合 cuDNN v8.1.1。
+   * GPU 运算能力超过 3.0 的硬件设备。

注意事项如下。

+   * 目前官方发布的 Windows 安装包仅包含 CUDA 10.1/10.2/11.2，如需使用其他 CUDA 版本，请通过源码自行编译。

+   * 如果使用的是安培架构的 GPU，推荐使用 CUDA 11.2；如果使用的是非安培架构的 GPU，推荐使用 CUDA 10.2，性能更优。设备 GPU 的架构类型可以通过搜索引擎查找。

确认需要安装 PaddlePaddle 的 Python 处于所预期的位置，因为计算机里可能有多个 Python。可能需要根据环境将所有命令行中的 Python 替换为具体的 Python 路径。具体的 Python 路径可通过如下命令查看。

```
where python
```

+   （2）接下来通过以下步骤来查看是否装有 GPU。

+   ① 在本地计算机上搜索“任务管理器”，单击打开它。