
# 3. 如何使用编译器

你可以在此下载编译器及以预编译形式存储的可用于 Vision Kit 的 TensorFlow 模型（人脸检测模型除外）。

运行 `tar -zxvf bonnet_model_compiler_yyyy_mm_dd.tgz` 以解压文件，解压后会得到 `bonnet_model_compiler.par` 文件（通过命令 `chmod u+x bonnet_model_compiler.par` 来运行此文件）。

**注意**：编译器只能在 x86 架构 64 位 Linux 上运行，可在 Ubuntu 14.04 上测试成功，不要在 Vision Kit 上运行。

![](EPUB/images/image_435.jpg)

以 `mobilenet_v1_160res_0.5_imagenet.pb` 作为例子，将此文件放在与 `bonnet_model_compiler.par` 相同的目录下，运行：

![](EPUB/images/image_436.jpg)

其中，`input_tensor_name` 指的是 TensorFlow 模型的推理部分中输入节点的名字，`output_tensor_names` 指的是 TensorFlow 模型的推理部分中输出节点的名字。下载文件中的 `Readme.md` 包含了这些相关信息。

**注意**

针对基于 MobileNet SSD 的模型（`mobilenet_ssd_256res_0.125_person_cat_dog.pb`），TensorFlow 模型包含 3 部分：预处理、推理和后处理。你需要用到的输入和输出指的是推理部分的输入和输出。看一下 TF 模型中的节点名字，其中有以 `Preprocessor`、`FeatureExtractor` 和 `Postprocessor` 作为前缀的，分别代表不同的阶段。这就是为什么输入和输出张量名字没有在 TensorFlow 模型的前面和后面几个节点中出现的原因。

# 4. 限制条件

模型的输入图像是正方形的 RGB 图像，并且其边长必须为 8 的倍数。注意：Vision Bonnet 会将输入图像按比例缩小，因此推理的时候你可以上传比模型要求的输入尺寸更大的图像，而且边长也不需要是 8 的倍数。

其中，模型的第一个运算符必须是 `tf.nn.conv2d`，应按照 `NHWC`（TensorFlow 的一种数据格式）顺序进行训练，架构应是非循环的，推理过程的批大小（batch size）必须是 1。

# 5. 支持的运算符和配置

下面列出的 TensorFlow 运算符可以通过模型编译器处理并且在设备上运行。由于需要这些运算在 Vision Bonnet 处理器上高效运行，因此对其中一些运算的输入和参数有额外的限制。

支持的 TF 运算符及配置：

*   **tf.nn.conv2d** ![](EPUB/images/image_323.jpg)
    *   输入张量的深度必须可以被 8 整除，除非它是模型的第一个运算。 ![](EPUB/images/image_323.jpg)
    *   `filter: [k, k, in_channels, out_channels], k = 1, 2, 3, 4, 5`。 ![](EPUB/images/image_323.jpg)
    *   `strides: [1, s, s, 1], s = 1, 2`。 ![](EPUB/images/image_323.jpg)
    *   `padding: VALID or SAME`。 ![](EPUB/images/image_323.jpg)
    *   `data_format: NHWC`。

*   **tf.nn.depthwise_conv2d** ![](EPUB/images/image_323.jpg)
    *   `filter: [k, k, in_channels, channel_multiplier], k = 3, 5, channel_multiplier = 1`。 ![](EPUB/images/image_323.jpg)
    *   `strides: [1, s, s, 1], s = 1, 2`。 ![](EPUB/images/image_323.jpg)
    *   `padding: VALID or SAME`。 ![](EPUB/images/image_323.jpg)
    *   `data_format: NHWC`。

*   **tf.nn.max_pool** ![](EPUB/images/image_323.jpg)
    *   输入张量的深度必须可以被 8 整除。 ![](EPUB/images/image_323.jpg)
    *   `ksize: [1, k, k, 1], k = 2, 3, 4, 5, 6, 7`。 ![](EPUB/images/image_323.jpg)
    *   `strides: [1, s, s, 1], s <= k`。 ![](EPUB/images/image_323.jpg)
    *   `padding: VALID or SAME`。 ![](EPUB/images/image_323.jpg)
    *   `data_format: NHWC`。

*   **tf.nn.avg_pool** ![](EPUB/images/image_323.jpg)
    *   `ksize: [1, k, k, 1], k = 2, 3, 4, 5, 6, 7`。 ![](EPUB/images/image_323.jpg)
    *   `strides: [1, s, s, 1], s <= k`。 ![](EPUB/images/image_323.jpg)
    *   `padding: VALID or SAME`。 ![](EPUB/images/image_323.jpg)
    *   `data_format: NHWC`。

*   **tf.matmul** ![](EPUB/images/image_323.jpg)
    *   假设 a 是 M×K 矩阵，b 是 K×N 矩阵，K 必须是 8 的倍数。 ![](EPUB/images/image_323.jpg)
    *   `a: rank-1 or rank-2 tensor`。 ![](EPUB/images/image_323.jpg)
    *   `b: rank-1 or rank-2 tensor`。 ![](EPUB/images/image_323.jpg)
    *   `transpose_a: False`。 ![](EPUB/images/image_323.jpg)
    *   `transpose_b: False`。 ![](EPUB/images/image_323.jpg)
    *   `adjoint_a: False`。 ![](EPUB/images/image_323.jpg)
    *   `adjoint_b: False`。 ![](EPUB/images/image_323.jpg)
    *   `a_is_sparse: False`。 ![](EPUB/images/image_323.jpg)
    *   `b_is_sparse: False`。

*   **tf.concat** ![](EPUB/images/image_323.jpg)
    *   `axis: 1 or 2 or 3`。

*   **tf.add**

*   **tf.multiply**

*   **tf.nn.softmax** ![](EPUB/images/image_323.jpg)
    *   `dim: -1`。

*   **tf.sigmoid** ![](EPUB/images/image_323.jpg)
    *   `x: 张量大小必须为 [1, 1, 1, k]`。

*   **tf.nn.l2_normalize** ![](EPUB/images/image_323.jpg)
    *   输入张量的深度必须可以被 8 整除。 ![](EPUB/images/image_323.jpg)
    *   `dim: -1`。

*   **tf.nn.relu**

*   **tf.nn.relu6**

*   **tf.tanh**

*   **tf.reshape** ![](EPUB/images/image_323.jpg)
    *   张量的第一维不可以改变，即 `shape[0] = tensor.shape[0]`。

# 6. 可能会出现的问题

**Q**：按照 TensorFlow 的目标检测教程，重新训练一个目标检测模型，并没有成功，可能是哪里的原因呢？

**A**：这里列出的基于 MobileNet 的预训练模型要求输入为 300×300 且深度乘数为 1.0，这样的模型要在 Vision Kit 上运行太大了。你可以训练一个在支持的配置范围内较小的模型（MobileNet+SSD，输入大小为 256×256，深度乘数为 0.125），需要修改输入大小和深度乘数。不幸的是，基于他们的重训练教程，你不能用一个新的深度乘数来重训练（微调）一个深度乘数为 1.0 的模型，而必须从头开始训练。

**Q**：如何训练和部署使用 TensorFlow 的对象检测 API 训练的自定义对象检测模型？

**A**：已知模型架构为 MobileNet+SSD，已证明可用的模型配置为：

![](EPUB/images/image_437.jpg)

使用嵌入式版本的训练配置 `embedded_ssd_mobilenet_v1_coco.config`，以训练 PASCAL VOC 数据集为例。

1.  参考此处的描述安装目标检测 API。
2.  准备 PASCAL VOC 数据集的训练集和验证集。
3.  修改 `embedded_ssd_mobilenet_v1_coco.config`，主要改变为 `num_classes` 设置为 20（原始值为 90）。
4.  注释 `fine_tune_checkpoint`、`label_map_path`、`input_path` 以及 `PATH_TO_BE_CONFIGURED`。
5.  根据这里的描述开始训练。
6.  根据此处的说明导出用于推理的模型，`trained_checkpoint_prefix` 参数通常设置为 `model.ckpt-${CHECKPOINT_NUMBER}`。注意，当训练第 0 步完成后，强烈推荐验证一下模型可以在 Vision Bonnet 上运行。
7.  使用 `bonnet_model_compiler` 编译模型：

    ```
    ./bonnet_model_compiler.par \
    --frozen_graph_path=frozen_inference_graph.pb \
    --output_graph_path=customized_detector.binaryproto \
    --input_tensor_name="Preprocessor/sub" \
    --output_tensor_names="concat,concat_1" \
    --input_tensor_size=256 \
    --debug
    ```

8.  运行如下脚本，以确保模型可以在 Vision Bonnet 上运行：

    ```
    ~/AIY-projects-python/src/examples/vision/any_model_camera.py \
    --model_path \
    --input_height \
    --input_width
    ```

9.  写一段 Python 代码来展示推理结果，可以参考 `src/aiy/vision/models/object_detection.py`。如果你使用的是 `embedded_ssd_mobilenet_v1_coco.config` 中的默认 SSD 配置，可以复用 `src/aiy/vision/models/object_detection_anchors.py`；否则，就需要理解 anchor 生成的原理，并使用此脚本来重新生成 anchor。

## 6.4.4 GPIO 引脚扩展外设

如果想要在 Vision Kit 之外扩展你的项目，那么可能想要知道哪些 GPIO 引脚可以用于连接其他硬件。图 6-111 展示 Vision Bonnet 使用了树莓派的哪些引脚。

### 1. GPIO 扩展引脚

Vision Bonnet 还包括一个专用微控制器（MCU），可实现以下附加功能：

*   控制额外的 4 个 GPIO 引脚，以释放树莓派的 GPIO。

![](EPUB/images/image_438.jpg)

**图 6-111**

*   对伺服器/电机控制提供 PWM 支持，减轻对树莓派 CPU 造成的负担。
*   通过板上的逻辑到数字转换器（ADC）支持逻辑输入。
*   控制在 Vision Bonnet 上的两个 LED。

一些 GPIO 扩展引脚在 Vision Bonnet 的顶部（见图 6-112），你可以借助 `gpiozero` 库、使用名为 `PIN_A`、`PIN_B`、`PIN_C`、`PIN_D`、`LED_1` 和 `LED_2` 的引脚来控制 GPIO 和 LED。

![](EPUB/images/image_439.jpg)

**图 6-112**

与 `gpiozero` 相容的引脚定义由 `aiy.pins` 包提供。你可以使用这些定义来构建一些基本的 `gpiozero` 设备（比如 `LED`、伺服器和按钮等）。

**警告**

在将 Vision Bonnet 连线之前，请确保树莓派未连接电源。如果没有做到，可能会导致单击、严重损伤、死亡、火灾或电路板及连接电路相关设备的损伤。

### 2. 点亮 LED 样例

**注意**：下面的代码可能没有安装在你的 SD 卡上，请确保你运行的是最新系统镜像。

尽管 Bonnet 上的 LED 很容易使用，但是你可能想要让你的灯在其他地方也亮，所以请参考图 6-113，将 LED 连接至 `PIN_A` 和 `GND` 引脚。（确保较长的 LED 腿连接 `PIN_A` 引脚，电阻需要保证大于 50Ω。）然后运行 `led_chaser.py` 脚本：

```
cd ~/AIY-projects-python/src/examples/gpiozero
./led_chaser.py
```

脚本开始运行需要几秒钟的时间。脚本开始运行后，LED 灯会闪烁。可以通过 `Ctrl+C` 组合键来停止运行。

如果 LED 不闪烁，继续等待大概 15 秒钟。如果依然不闪烁，看一下终端是否输出错误信息。然后按 `Ctrl+C` 组合键使其停止，关闭 Kit，检查所有的连线是否正确，然后再试一次。

![](EPUB/images/image_440.jpg)

**图 6-113**

`led_chaser.py` 脚本会依次点亮 4 个 LED，代码如下：

![](EPUB/images/image_441.jpg)

当然，只连接一个 LED 时代码依然可以运行。当连接一个 LED 运行时，可以尝试将其他的 LED 灯分别按照同样的方式连接至 `PIN_B`、`PIN_C` 和 `PIN_D`，然后运行代码。

### 3. 控制伺服器样例

Vision Bonnet 上的 GPIO 引脚是由一个板上的微处理器来控制的，通过这些引脚来执行脉冲宽度调制（PWM）要比树莓派更为精确，因此这些引脚对于控制伺服器是非常有帮助的。

如图 6-114 所示，将一个伺服器连接至 `GND`、`PIN_B` 和 `5V` 引脚，然后运行 `servo_example.py` 脚本：

```
cd ~/AIY-projects-python/src/examples/gpiozero
./servo_example.py
```

脚本开始运行需要几秒钟的时间，运行后伺服器会在最大、最小和中间位置之间来回旋转。但是每个伺服器都有不同之处，因此你需要微调代码的参数来使伺服器的运动范围达到最大。

如果伺服器没有反应，继续等待大概 15 秒钟。如果依然不闪烁，看一下终端中是否输出错误信息。然后按 `Ctrl+C` 组合键使其停止，关闭 Kit，检查所有的连线是否正确，然后再试一次。

![](EPUB/images/image_442.jpg)

**图 6-114**

`servo_example.py` 脚本使用 `gpiozero` 库的 `Servo` 类来控制伺服器。脚本的主要部分如下：

![](EPUB/images/image_443.jpg)

如果想要调整伺服器旋转的范围，打开 Python 脚本，调整 `Servo()` 构造器中的参数。

更多使用 GPIO 引脚的样例，参考 GitHub 上的 AIY 样例。所有样例文件都可以在 Vision Kit 的 `~/AIY-projects-python/src/examples/` 目录下找到，请确保 SD 卡上安装的是最新版本的系统镜像。

### 4. 按钮连接引脚

如果你想要修改按钮接口（比如说改变实际的按钮），请参考图 6-115。

![](EPUB/images/image_444.jpg)

**图 6-115**

## 6.4.5 修改启动时运行 APP 机制

Vision Kit 在启动时会默认运行笑脸检测器程序，这是通过 `systemd` 服务来实现的。服务是由一个 `~/AIY-projects-python/src/examples/vision/joy/joy_detection_demo.service` 配置文件来定义的，文件形式如下：

![](EPUB/images/image_445.jpg)

`.service` 文件接收一系列的配置选项作为输入，但上述文件已经可以提供大多数启动时运行的程序所需的配置。

如果想要创建一个在启动时运行应用程序的服务，只需要将这个配置文件复制并粘贴为一个 `.service` 文件（如 `my_program.service`），然后修改 `ExecStart` 选项，使其指向程序的 Python 文件（并传递相应的参数），以及修改 `Description` 选项来描述你的程序。

然后将此文件放置到 `/lib/systemd/system/` 目录下。你可以在 `/lib/systemd/system/` 目录下创建符号链接来链接到当前文件，而不是直接将文件移动过去。假设配置文件为 `~/Programs/my_program.service`，你可以通过以下命令来创建符号链接：

```
# Create the symlink
```
