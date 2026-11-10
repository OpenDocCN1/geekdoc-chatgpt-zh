

# 绘图分类与风格分类

## 引言

在为这个任务创建App之前，首先看看AI工具包，因为在创建这个App时，我们实际上只需要遵循构建App的过程。我们在这个任务中使用的主要工具是`Turi Create`、`CoreML`和`Vision`。

首先，让我们使用`Turi Create`（这是苹果基于任务的Python工具包），用于创建机器学习模型）来训练一个可以对绘图行分类的模型。

然后，我们使用`CoreML`和`Vision`来处理模型，对允许用户拍照的图像进行分类。

要制作一个能对图像进行分类的App，我们需要一个图像数据集。我们可以画几百万个不同事物的草图，希望App能够识别它们，但这需要一段时间。

正如你会发现的那样，通常情况下，研究人员会照顾我们。这次的研究人员来自谷歌。快速绘图数据集是一个超过5000万草图的集合，这些草图已经分类（345个类别），所有的草图都是来自世界各地的玩谷歌的“Quick, Draw!”在线游戏（谷歌非常擅长让人们贡献数据）的用户绘制的，如图4-27所示。

![](images/7d8a6c1a-2dee-456b-8f57-63d67da64d7f.jpeg)

图4-27：谷歌的“Quick, Draw!”游戏

![](images/a3592fe1-b60c-49fb-9e91-c4e9efc52d65.png)我们知道，英国和澳大利亚以外的人可能不知道什么是boffin。请参阅这篇文章以获得更多关于boffin的细节。正如一位明智的思想领袖曾经说过的：书是用来学习的。现在你知道了！

因为Quick Draw数据集有太多的类别，而用这么多样本训练分类器需要一段时间（可以修改我们的脚本并尝试一下），我们将数据集限制在以下23个类别中：苹果、香蕉、面包、花椰菜、蛋糕、胡萝卜、咖啡杯、饼干、甜甜圈、葡萄、热狗、冰激凌、棒棒糖、蘑菇、花生、梨、菠萝、比萨、土豆、三明治、牛排、草莓和西瓜。

你可以在图4-28中看到一个App能够分类的绘图类型的示例。

![](images/4e36b5ac-63a8-4c3f-b513-0187cca12d7d.jpeg)

图4-28：绘图分类器能够处理的图像示例

![](images/375d4f9e-2d9d-4355-b310-005f7fd4c231.png)你不需要下载Quick Draw数据集，它非常庞大。我们将其作为脚本的一部分来下载，这个脚本是在下面的“创建模型”中用来训练模型的。

## 创建模型

我们将使用苹果的`Turi Create`来训练这个模型。这意味着需要一个`Python`环境：

1.按照我们在2.5.1节中介绍的步骤设置`Python`环境，并且激活环境，然后使用`pip`安装`Turi Create`，如图4-29所示：

![](images/3e424c73-d7f3-46e3-bb51-9fc3dcb2402b.jpeg)![](images/873dd143-24f9-48ba-9c73-5fcbd4d8118a.jpeg)

图4-29：创建环境

2.创建一个名为`train_drawing_classifer.py`的新`Python`脚本，并添加以下内容：

![](images/9a6f0292-645f-4226-a232-fc9ffab4ce2d.jpeg)

3.添加一些配置变量，包括我们想训练的类别列表：

![](images/15f0d84b-9a1a-479a-9a1f-c1f674694d76.jpeg)

4.添加以下函数来生成存放训练数据的目录：

![](images/1a02e47f-1654-4603-80cd-cadf05a10021.jpeg)

5.获取要用于训练的位图：

![](images/6536ad53-3dea-40e8-9207-938bb0ce25c0.jpeg)

6.添加一个从图像生成`SFrame`的函数：

![](images/741851ad-9449-4a51-86d1-b91b8d1b3ae3.jpeg)

7.添加一些内容以将这些`SFrame`保存到文件中：

![](images/27a22127-a794-4a8f-a293-86c3c618a6c1.jpeg)

8.实际训练绘图分类器：

![](images/1013711b-9146-40d9-ad55-548dc3ce35ca.jpeg)

9.导出为`CoreML`格式：

![](images/f42d7d5f-1275-4b5e-80f5-75d2cfaf7e59.jpeg)

![](images/b31a3f78-17bd-49b9-a03a-6053faafbc9e.png)如果你想让不同于我们所选的图形也能被你的绘图分类器进行分类，请查看类别列表并选择一些不同的类别。

10.运行脚本：

![](images/bdcf4d19-821d-47fb-9af9-b9684058b614.jpeg)

你应该能看到类似于图4-30的内容。正如前面提到的，你不需要手动下载Quick Draw数据集，因为脚本会做这些。

![](images/67f6eab6-c004-47e5-9e16-c29e1a43034a.jpeg)

图4-30：训练绘图分类器

在获取数据并将它们解析为`Turi Create`内部格式后，你将看到如图4-31所示的内容弹出，供你浏览图像。

![](images/ce5faba5-5bde-4a43-bbf7-2e61ec5a717.jpeg)

图4-31: `Turi Create`的图像可视化

当它训练模型的时候，你可以随意地浏览它。

![](images/2f317490-803e-4b0e-aac2-06f41d3591ca.png)这项训练可能需要一段时间。在MacBook Pro上用了几个小时。

当训练完成后，你可以查看完成此工作的文件夹，你会发现一个全新的`Drawing-ClassifierModel.mlmodel`文件，如图4-32所示。你可以同使用其他的`CoreML`文件一样使用这个模型。碰巧的是，这正是4.7.3节中要做的。

![](images/7d1e888b-5af9-49d3-926b-d4595f7dce3.png)我们在2.3.3节中提到了`Turi Create`的可视化特性。我们还在3.4.1节中讨论了了解数据集的广泛重要性。

![](images/e28c82f6-1916-4373-9031-6e55000ecff5.jpeg)

图4-32：最终转换的`CoreML`模型

### 4.7.3 构建应用程序

同样，我们将使用苹果最新的UI框架`SwiftUI`来构建绘图检测App的界面。

我们要构建的用于计算人脸数量的App的最终形式可以在图4-1中看到。它由以下`SwiftUI`组件组成：

• 用于显示App标题的`NavigationView`，以及用于选择照片的按钮。

• 显示所选图像（包含绘图）的`Image`, App将尝试对其进行分类。

• 触发绘图分类的`Button`。

• 一些显示人脸计数的`Text`。

我们会通过从多个子视图构造这个视图。如果你不想手动创建绘图检测iOS App，也可以从我们的网站下载代码，寻找名为`DDDemo`的项目。

做完这些之后，继续完成剩下的部分（我们不建议跳过），然后在4.7.4节和我们汇合。

![](images/95f1c79f-79c0-4fbf-b81a-9a9b0e5c44a9.png)你可能会注意到，这个App在结构上与本书中构建的其他一些`SwiftUI` App非常相似。我们试图保持事情的一致性并让它们尽可能简单。

要自己制作绘图检测iOS App，需要执行以下操作。

1.启动`Xcode`。

2.创建一个新的iOS App项目，选择“`Single View App`”模板。如前所述，我们使用`SwiftUI`。

3.拖入我们之前在4.7.2节中创建的`.mlmodel`文件，让`Xcode`根据需要复制它。

4.将一个新的Swift文件添加到项目，并命名为`Image.swift`.我们使用它在`UIImage`上添加一个扩展，这样我们就可以对它进行过滤，以便使分类效果更好。

5.首先，我们也需要一个`CIFilter`上的扩展：

![](images/56c31abe-7c29-47c4-abbb-bea5c4f0283a.jpeg)

该扩展允许我们创建一个`CIFilter`，它是一个Core Image过滤器，可以操作图像，并要求它是单色、黑色或多色调的。你可以在苹果的文档中了解有关这些过滤器的更多信息，以及如何创建自己的过滤器。

6.同样也在`UIImage`上添加扩展：

![](images/8aba07cc-9044-49e9-977a-a99be6104836.jpeg)

该扩展添加了两个方法：一个用于应用`CIFilter`，另一个用于固定图像的方向。我们还添加了常见的方向修正。

7.创建另一个新的Swift文件，命名为`Drawing.swift`，然后添加下列`import`：

![](images/635c381f-f36e-48eb-afbe-38e9c481e890.jpeg)

8.添加下列`enum`：

![](images/9eb2c71e-61ca-4423-a516-aced631a3063.jpeg)![](images/4de662e8-805f-4b7d-9eaf-3adcfa4de94a.jpeg)

`enum`允许我们从一个`String`（通过创建的`init()`函数）创建一个`Drawing`（`enum`的名称）。每种类型的`Drawing` `enum`都有一个图标，这个图标是一个赋值给它的表情符号。

9.你还需要`VNImageRequestHandler`的扩展：

![](images/d2539e39-6e6b-4b3c-9412-f69858b72417.jpeg)

该扩展扩展了`VNImageRequestHandler`，添加了一个方便的构造器，允许用`UIImage`而不是`CIImage`创建。如需回忆`VNImageRequestHandler`是做什么的，请查看苹果的文档。

10.在`DrawingClassifierModelBitmap`上添加另一个扩展，它是我们之前创建的模型的名字（`Xcode`自动为拖进来的模型创建一个类）：

![](images/8cd96500-e7f9-4c5-8bca-cb178da49f43.jpeg)

这一大段代码扩展了我们的模型`DrawingClassifierModel`，添加了一个`configure()`函数，该函数接受一个`UIImage`，并返回一个已被过滤为灰度、变亮和对比度增强的版本。它还添加了一个`classification()`函数，该函数在`DispatchQueue`上运行`VNCoreMLRequest`，试图使用`VNImageRequestHandler`和我们的模型（在此上下文中为`self`，因为这是模型的扩展）对图像进行分类（绘图）。

11.在`Collection`上添加一个扩展：

![](images/6c68272a-b871-4e3c-bd65-c47e56fe1b73.jpeg)

该扩展为`VNClassificationObservation`（这是你在使用苹果的`Vision`框架执行图像分析时得到的）的`Collection`的扩展添加了一个名为`list`的`var`，它的类型为`String`，它允许我们从`VNClassificationObservation`获得标识符和可信度。

12.要添加一些自定义视图，请添加一个名为`Views.swift`的文件，导入`SwiftUI`，然后添加以下`ImagePicker`结构体：

![](images/bf8a26f6-144a-4415-99ae-65aa5e449657.jpeg)![](images/8590a75d-5013-4bd4-959c-780346286c07.jpeg)

就像我们在4.2.2节中使用`SwiftUI`创建人脸检测应用一样，这在`SwiftUI`中伪造了一个`ViewController`，允许我们使用`UIKit`功能来获得一个图像选择器。

13.添加以下`TwoStateButton`视图：

![](images/24104608-504d-4a6f-a88a-f981b3845ad7.jpeg)

这个`TwoStateButton`应该看起来很熟悉：它为一个`Button`定义了一个`SwiftUI`视图，该视图可以被禁用并且用可视化的方式表示出来。

14.添加以下`MainView`视图：

![](images/fd66a20a-de59-44f0-8111-a9a5ae887b6f.jpeg)

此`MainView`定义了一个`VStack`，其中包含一个`Image`、一个`Spacer`、一些`Text`和一个`TwoStateButton`。

15.接下来，打开`ContentView.swift`，然后添加以下`@State`变量：

![](images/c4f30efb-9e75-4eaa-af98-423da3b3b98e.jpeg)

16.添加以下变量：

![](images/e32cfc1d-b02b-455d-9d91-31b26451a67f.jpeg)

17.添加一个函数去执行分类：

![](images/d4b76d6c-9090-408d-8a51-cfcc80b987bb.jpeg)

18.添加一个函数去返回控件：

![](images/9950f8ad-f65c-47d8-a126-197b2519401c.jpeg)

19.添加一个函数去唤起图像选择器：

![](images/57b74a85-2a1e-4f10-9f73-ed4aad438579.jpeg)

20.添加一个函数去唤起相机：

![](images/7439247c-5788-418d-bae7-c17ac7ea5406.jpeg)

21.在`ContentView`上添加一个扩展，它能按需返回正确的视图：

![](images/a2f67fc5-606e-4b62-a864-0a4ed2b65b7f.jpeg)

22.如下所示，更新`body`视图：

![](images/382970cd-f949-4f8b-be1a-857cf9807220.jpeg)

你现在可以启动绘图分类器App，在纸上画一些东西，拍照，然后让App识别绘图（只要这些绘图与你训练的模型所使用的分类相匹配）。图4-33展示了一些作者的手绘示例。

![](images/ffdb5ed6-78d1-46bb-a7f9-a81e1fa303de.jpeg)

图4-33：我们的App识别了作者的神奇作品

### 4.7.4 下一步

这只是创建绘图分类功能的一种方法。绘图通常是在iOS设备上创建的，这意味着我们要经历一些可能不必要的步骤来拍摄或选择照片。为什么不允许用户直接在App中绘图呢？

我们将在7.3节中介绍为在设备上绘制的图形而创建图形分类器。

脚注

## 4.8 任务：风格分类

在最后一个与视觉相关的任务中，我们修改了4.6节中构建的App，使其能够识别所提供图像的风格。我们将以我们知道的最直接、最实用的方式迅速地做到这一点：将现有的模型转换为苹果的`CoreML`格式。

我们需要一个能够识别风格的模型。幸运的是，研究人员已经为我们提供了。“Fine-tuning CaffeNet on Flickr Style”是一个分类器模型，它已经在许多不同类别的图像上训练过，可以识别各种图像风格。

![](images/e9599f19-b85c-42fc-ae32-85f672569b34.png)模特能辨别出的风格是：详细、柔和、忧郁、黑色、HDR、复古、长曝光、恐怖、阳光、纹理、明亮、朦胧、散景、宁静、缥缈、微距、景深、几何构图、简约和浪漫。我们在这里使用的模型基于这篇研究论文。

### 4.8.1 转换模型

我们需要使用`Python`来将原始模型转换为可以使用的模型。

1.创建一个新的`Python`环境，然后激活它：

![](images/d5765780-97b8-4725-a7bc-f217bf36e0e7.jpeg)

2.安装苹果的`CoreML`工具：

![](images/13cc95b5-c4ea-4fed-babb-8d08dc28e7db.jpeg)

3.创建一个名为`styles.txt`的文件，包含以下内容：

![](images/09482c11-30ac-4575-8c2e-43e3f7afb2ba.jpeg)

4.从Berkeleyvision网站以`Caffe`格式下载我们正在使用的训练的模型。

将此模型文件（几百兆字节）保存在`styles.txt`文件旁边。

5.在它旁边下载并保存这个文件。`Deploy.prototxt`文件为我们需要的模型指定参数，以便能够将其转换为`CoreML`格式。

6.在同一个文件夹中创建一个新的`Python`脚本（我们的脚本名为`convert_style-classifier.py`），然后添加以下代码：

![](images/282967c0-a7a5-4877-85c7-a7eaca1a9441.jpeg)

此代码导入`CoreML`工具，加载`CoreML`工具提供的`Caffe`转换器，并指向我们下载的`finetune_flickr_style.caffemodel`模型。在这里也可以找到`deploy.prototxt`参数文件，该文件提供一些元数据，并且保存名为`Style.mlmodel`的`CoreML`格式的模型。

这些应该看起来和图4-34一样。

![](images/dfb575da-3ddc-4202-b949-5fd29004877e.jpeg)

图4-34：转换风格分类器所需的文件

7.运行`Python`脚本：

![](images/cff5f437-095b-4eb4-a8cb-d49f56676db0.jpeg)

你将看到类似于图4-35的内容，并最终得到一个在文件中的`Style.mlmodel`文件（见图4-36）。

![](images/6e281032-861e-4d51-ad6a-1ad2c0d81a7e.jpeg)

图4-35：将风格分类器从`Caffee`模型转换为`CoreML`模型

![](images/42fd7e9a-d58c-4292-8d23-43d3aedcc5d7.jpeg)

图4-36：新的`CoreML`风格分类器模型

### 4.8.2 使用模型

首先，你要在4.6节中复制我们为分类任务创建的项目的最终版本。如果你不想这样做，可以从我们的网站下载，查找名为`StyleClassifier`的项目。

要使用刚刚转换的`Style.mlmodel`文件，请执行以下操作：

1.打开你复制（或从我们的资源下载）的`Xcode`项目。

