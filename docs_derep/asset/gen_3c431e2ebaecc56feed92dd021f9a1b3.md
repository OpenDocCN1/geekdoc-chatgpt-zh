[PRE0]

图 8-3：故事板

3. 布局好所需组件后，请记得补充合适的约束。

先新增一个名为 `StyleModel.swift` 的文件，后续的风格迁移模型会用到它。现阶段能用的“风格”仅限于图像的旋转或翻转。新建 `StyleModel.swift` 并写入以下内容：

[PRE1]

# iOS 风格迁移与文本生成实践

## StyleModel 枚举及 UIImage 扩展

这里创建了一个名为 `StyleModel` 的枚举，它同时遵循 `String` 与 `CaseIterable` 协议，提供了仅涉及翻转和旋转的若干风格案例，并为每个案例设定了对应的名称与索引等信息。

接下来准备一个体积较大的 `UIImage` 扩展，用来处理风格转换时需要的 `UIImage` 对象。新建一个文件命名为 `Image.swift`。

### 4. 为 UIImage 补充扩展方法

[PRE2]

你会发现代码中还存在一个占位用的 `UIImage`，它指向项目资源目录里的占位图。该图片可以在我们提供的下载素材的 Assets 目录中找到。

### 5. 新增 styled() 方法

[PRE3]

该方法接受 `StyleModel` 作为输入，返回一个 `UIImage`，并据此执行风格转换；在当前初版应用里，主要是依据 `StyleModel` 选择旋转方向。

### 6. 新增 aspectFilled() 方法

[PRE4]

方法会返回图像的副本，以 `self` 即原图为基础，将其填充到指定尺寸，多出的部分会被裁剪掉。

### 7. 提供按指定尺寸缩放图像的函数

[PRE5]

### 8. 再提供一个按指定尺寸裁剪图像的函数

[PRE6]

### 9. 为本文件中的 UIImage 扩展追加创建并返回 CVPixelBuffer 的函数

[PRE7]

`CVPixelBuffer` 是苹果 Core Video 框架定义的一种像素缓冲区，可让我们直接在内存中操作图像数据。稍后会说明为何需要它。

接下来新建 `Utils.swift` 文件，里面存放一些辅助扩展和必备代码，保证风格转换应用正常运行。

在项目中创建 `Utils.swift` 新文件，然后按以下步骤操作：

## Utils.swift 辅助扩展集合

### 1. 加入必要的 import 语句

[PRE8]

### 2. 为 MLMultiArray 添加扩展

[PRE9]

此步旨在方便初始化一个类型为 `Double` 且初始值为 `0.0` 的 `MLMultiArray`，并把指定索引的位置改为 `1.0`。这常用于多选项的 `MLModel`，非零索引对应特定选项。被扩展的 `MLMultiArray` 属于 CoreML，用作模型特征输入输出，详见第 2 章。

### 3. 为 CVPixelBufferLockFlags 添加扩展

[PRE10]

该扩展把像素缓冲区的锁定标志设为 `0`，表示通过 Core Video 框架同时进行读写。若确定不会写入，可设为 `1`，Core Video 即可做优化，因为它知道数据不会被修改。

### 4. 为 CVPixelBuffer 添加另一层扩展

[PRE11]

此扩展为 `CVPixelBuffer` 提供了便于访问的属性如 `width`、`height` 等，并实现了对像素的操作。虽然和 AI 流程关联不大，但若想了解如何操作 `CVPixelBuffer`，可查阅苹果官方文档。

### 5. 在 CGContext 中扩展，基于指定像素缓冲区尺寸创建 CGContext 并提供实用默认值

[PRE12]

`CGContext` 属于苹果 Core Graphics 框架，是绘制目标。若熟悉 OpenGL 或 DirectX，会对上下文概念不陌生。简单说，`CGContext` 就是执行特定绘制操作的工作区域。更多细节见苹果文档，本书不展开。

本扩展还提供了从中生成 `UIImage` 的能力。

### 6. 为 CGSize 扩展，使其符合 CustomStringConvertible

[PRE13]

该扩展可把 `CGSize` 的宽度和高度以字符串形式返回，方便描述用途。

### 7. 分别为 UIButton 与 UIBarButtonItem 添加扩展

[PRE14]

用于启用或禁用控件，并在 `UIButton` 上调整颜色。到这里，你应该对这两个控件的扩展写法感到熟悉了。

## ViewController.swift 配置步骤

完成上述工作后，便可在 `ViewController.swift` 中开始配置。

### 1. 连接界面对象的 outlet

[PRE15]

### 2. 连接界面按钮的 action

[PRE16]

### 3. 声明若干属性

[PRE17]

我们保留两个 `UIImage`：一个是原始输入图像，一个是风格转换后的结果。还有一个 `StyleModel`，它依据 `UIPickerView` 当前选中的行决定要应用的风格，并在界面中放置了该选择器并创建了对应的 outlet。

### 4. 重写 viewDidLoad() 方法

[PRE18]

### 5. 编写根据输入或输出图像是否存在来启用或禁用控件的函数

[PRE19]

### 6. 编写真正执行风格转换的函数

[PRE20]

现在在 `ViewController.swift` 中额外加入一些扩展，位于 `ViewController` 类之外但扩展该类。

### 7. 添加遵循 UINavigationControllerDelegate 的扩展

[PRE21]

此扩展允许以 `UINavigationController` 的方式弹出分享表单和警告对话框。我们用它来实现导航栏按钮的分享功能以及错误提示。

### 8. 添加遵循 UIImagePickerControllerDelegate 的扩展

[PRE22]

此扩展使我们能调用 `UIImagePickerController` 打开用户相册，并让用户从中选取图片。

### 9. 添加遵循 UIPickerViewDelegate 的扩展

[PRE23]

该扩展定义了 `UIPickerView` 的行为，让用户挑选希望应用于输入图像的风格。

[PRE24]别把 `UIPickerView` 与 `UIImagePicker` 混为一谈。前者是列表选择控件，常用于日期等多分量选择；后者用于从 iOS 相册中选取图片。

此时可以运行应用，选择一张图片，再从“向上翻转”“向左旋转”“向右旋转” 中挑选一种“风格”应用到图片上。图 8-4 展示了初始应用完成后的效果。

[PRE25]

图 8-4：初始应用最终形态，演示以翻转作为“风格”

到此基础版本已完成，接下来进入 AI 部分。

## 8.2.3 AI 工具与数据集准备

本次任务使用的软件是苹果的 Turi Create Python 库和 CoreML，同时需要用于训练的图像。

我们借助 Turi Create 进行模型训练。Python 环境的搭建可回顾 2.5.1 节。训练需要两类截然不同的图像：风格图像和内容图像，详见 2.3.3 节回顾 Turi Create 工作原理。

风格图像代表目标风格的视觉元素，每种风格仅需一张源图。建议选用有趣的马赛克、纹理或抽象图案。

新建一个文件夹作为风格转换的工作目录，并在其中创建两个子文件夹：`style` 与 `content`。准备好风格图后，放入约 6 张左右到 style 文件夹。

内容图像代表用户实际会处理的图像类型集合。内容图可以是任意图像，但整体类型应与目标用户的使用场景匹配。

[PRE26]若你想做一个适配自拍的风格转换应用，内容图就应是自拍照。若目标是汽车照片，就需要一个装满汽车照片的文件夹。以此类推。

缺乏内容图时，可使用公开的通用物体图像库，例如 Common Objects in Context 数据集。

准备好后，把内容图放到 `content` 文件夹。我们使用了多种图像，以提升模型的泛化能力。

## 8.2.4 模型训练流程

准备好风格图和内容图后，使用 Turi Create 构建风格转换模型。

按 2.5.1 节说明启动 Python 环境，然后执行以下操作。

1. 使用你喜欢的编辑器，在刚才创建的文件夹中新建一个 Python 脚本（我们命名为 `training.py`），参考 8.2.3 节内容，写入以下代码：

[PRE27]

2. 若文件夹名称或 `style`、`content` 子文件夹命名不同，请相应修改路径映射。

3. 在命令行执行该 Python 脚本，风格转换模型的训练便开始了。

训练期间，终端会输出 Turi Create 的运行日志。

[PRE28]训练耗时可能很长。在本书中最久的一次训练大约在 2019 年款 MacBook Pro 上花费了 24 小时。

训练完成后，脚本会在 iOS 应用中生成一个 `.mlmodel` 文件，可供 CoreML 使用。

## 8.2.5 将模型接入应用

到目前为止，如果你一路跟进，已完成一个基础 iOS 应用，能够选择图片或拍照，并通过翻转或旋转执行占位风格转换。

你也已挑选风格图和内容图，并用 Turi Create 训练出风格转换模型。本节将基础应用与模型结合，用真实模型风格替换翻转旋转占位符。图 8-5 展示了下一阶段效果。

[PRE29]

图 8-5: NST 应用的进阶阶段

[PRE30]若未按 8.2.2 节自行搭建起点应用，可从官网下载代码，项目名为 `NSTDemoStarter`。本节即从此开始。同目录下也会提供已训练好的 `.mlmodel` 文件。若不想手动修改，也可直接下载 `NSTDemo-Complete` 项目。

与书中其他 AI 实践相比，把起点应用迁移到 AI 场景所需改动其实很小。

[PRE31]即使选择直接下载 `NSTDemo-Complete` 项目，仍建议阅读本节并对照项目源码。

开始操作。

1. 将训练好的或下载的 `.mlmodel` 文件拖入项目根目录，让 Xcode 自动处理。

2. 打开 `StyleModel.swift` 文件，用以下内容整体替换：

[PRE32]

这里用模型内嵌的真实风格替换了之前的示例，并提供了风格模型所需的各种参数。

3. 打开 `Image.swift` 文件，更新 `UIImage` 扩展中的 `styled()` 方法，如下所示：

[PRE33]

现在该方法基于像素缓冲区和模型中的真实风格工作，并将风格化后的图像从像素缓冲区输出，而非简单旋转。

4. 打开 `ViewController.swift`，在 `ViewController` 的 `UIImagePickerControllerDelegate` 扩展中修改 `imagePickerController()` 方法，如下所示：

[PRE34]

此改动使图像以填充方式显示，更适合与风格转换模型配合使用。现在可以运行应用，选择输入图像并应用其中一种风格，如图 8-6 所示。

[PRE35]

图 8-6：NST 应用运行效果

[PRE36]请注意，与训练时内容图相似的图像，风格转换效果会更好。

风格转换是一个巧妙技巧，也可用于音频甚至文本。Intel 有几篇有趣文章，可作为音频风格转换的入门参考。

脚注

## 8.3 任务：文本生成实操

有时生成一些文本很有用，无论是占位文字、游戏内容，还是其他用途，马尔可夫链都是最快捷的文本生成方法之一。

由于该任务仅涉及文本，不便做成应用形式，这里改在 Playground 中实现：新建一个 iOS Playground，并在 Sources 文件夹中添加一个新文件（我们命名为 `Extensions.swift`）。

1. 为 `Collection` 添加扩展：

[PRE37]

该扩展为 `Collection` 增加 `randomIndex()` 方法，方便从集合中取随机元素。

2. 为 `NSRegularExpression` 添加扩展：

[PRE38]

该扩展新增一个方法，用来判断附加的正则表达式是否匹配传入的 `String`，即新的 `matchs()` 函数。