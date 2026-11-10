

## 7.2 行为分类

它是许多App的有用模块，包括`Pokemon Go`之类的游戏以及各种健身App。

![](images/9a57916f-cc0a-4d9f-b5e1-cace3a533ce4.png)行为分类是机器学习中通常被称为序列分类的一个特定的子领域。行为分类是序列分类的一种实用的、基于任务的应用。

### 7.2.1 问题与方法

除了跟踪你正在做的每件事之外，`Apple Watch`还可以检测出你可能在锻炼的时间，并提供跟踪一项专门的锻炼的计划。对于这个任务，我们通过以下操作，探索了检测用户在拥有`iOS`设备时可能在做什么活动的实用方面。

• 创建一个`App`，该`App`能识别用户可能正在进行的行为。
• 构建一个`App`，该`App`能够说出并显示（以文本形式）它认为用户当前正在进行的身体活动。
• 使用苹果的工具来实现这一点，而不需要训练模型。
• 探索行为识别的潜在下一步。

图7-1显示了我们将在此任务中构建的最终版本的`App`。

![](images/eb3e4af7-df92-4448-8fb3-375b39757d76.jpeg)

图7-1：最终的行为识别`App`

### 7.2.2 构建应用程序

让我们使用苹果最新的用户界面框架`SwiftUI`来构建行为识别`App`。

`App`的最终形式由以下`SwiftUI`组件组成：

• 一个`View`
• 一些`Text`

就是这样！当意识到有不同的行为发生时，这个`App`的大部分用户界面实际上是一个口头通知—在用户界面上的`Text`中显示的内容。

如果你不想手动创建`iOS`应用程序，可以从我们的网站下载代码，找到名为`ARDemo`的项目。在这之后，我们强烈要求你继续完成本节，并将这里的注释同你下载的代码进行比较。

要自己创建`App`，需要做以下工作。

1.在`Xcode`中创建一个`iOS`应用项目，选择“`Single View App`”模板，然后选择“`SwiftUI`”复选框。
2.当你的项目创建好之后，添加一个名为`Tracking.swift`的新`Swift`文件到项目中，并且添加下列`import`到文件中：

![](images/ae9dbe80-8a88-48de-90db-cdcc5f4f3f9d.jpeg)

`CoreMotion`是苹果的运动框架，我们马上就会用到它。
3.创建一个名为`ActivityTracker`的`final class`，它继承自`BindableObject`：

![](images/3f6a7315-d163-4f5b-9859-90b26b002ef4.jpeg)
4.在`ActivityTracker`中添加一些变量：

![](images/4cc9741e-47e2-4345-8e7a-88a6d2c5f1bf.jpeg)
5.添加一个`init()`函数：

![](images/7a2ffa08-57db-40c0-9ff7-edb50bd9c0c4.jpeg)
6.添加一个`startTracking()`函数：

![](images/a7ddc50a-310c-4d2d-af45-cd9d3a126ce8.jpeg)
7.添加一个`stopTracking()`函数：

![](images/46f8426f-2cf9-41e3-a7b7-e7ad5d32d194.jpeg)
8.添加一个名为`Motion.Swift`的文件到项目中，然后添加以下`import`：

![](images/4c75042b-04ef-4df6-b685-f5c827117521.jpeg)
9.在`CMMotionActivity`上创建一个扩展：

![](images/f864f933-2eaf-475d-bc2c-f40e4c8e5cd4.jpeg)
10.添加一个扩展到`CMMotionActivityManager`：

![](images/85249471-faf5-4df9-ab03-9a9e97c5a736.jpeg)
11.在这个扩展中，给`Error`创建一个`enum`：

![](images/66c382f7-c71a-43ac-bace-8c90fd5cf5e7.jpeg)
12.添加一个函数去开始追踪：

![](images/704bf12b-5a2f-4855-b183-6e4173f843a3.jpeg)
13.添加一个函数去停止追踪：

![](images/053c9dad-9dfc-4bff-bf30-7cd78be4cd57.jpeg)
14.打开`ContentView.swift`文件，然后添加下列`import`：

![](images/e6d2fe8d-e256-4801-a20b-afa31311728a.jpeg)
15.在`AVSpeechSynthesizer`上添加下列扩展：

![](images/a900b1a7-5521-42a4-bb9e-f9f61e70dbfb.jpeg)
16.使得`ContentView` `struct`如下所示：

![](images/c21bc8fb-4e7b-4fc0-9f8f-38dacdfcbded.jpeg)
17.将`Privacy - Motion Usage Description`添加到`Info.plist`文件中，说明为什么要访问设备的运动使用情况。我们给这个键赋值，声明为：“We need this to see what motions the device is doing, so we can speak them!”如图7-2所示。

![](images/560d971c-28e8-4441-bcaf-e08510ea163d.jpeg)

图7-2：用于运动使用的`Info.plist`

文件

现在，如果你在物理`iOS`设备上运行该应用程序，则可以在移动设备时听到自己正在进行的运动。你的`App`将类似于图7-3。

![](images/70a53d70-0b8a-4a9a-b3c6-3607270b9890.jpeg)

图7-3：行为识别器正在工作

### 7.2.3 刚才发生了什么，这是如何工作的

我们刚刚使用苹果的`CoreMotion`框架创建了一个应用程序，可以检测当前正在进行的身体活动。`CoreMotion`提供与运动和环境相关的数据，这些数据来自现代`iOS`设备上的大量传感器：加速度计、陀螺仪、计步器、磁力计、气压计等。

对于这个行为识别`App`，我们使用了`CMMotionActivityManager`，它可以让我们访问由`iOS`设备存储的运动数据和追踪到的`CMMotionActivity`（代表单个运动更新事件）。基于获得的`CMMotionActivity`，我们返回了一个字符串，用于说明用户是走路、跑步、开车、骑自行车还是保持静止。因此，我们无须构建模型就可以报告设备用户可能在做什么。

在第11章中，我们将看到苹果提供的运动追踪是如何工作的。在7.4节中，我们通过训练自己的行为分类模型，从零开始创建了一个类似的应用程序。

脚注

## 7.3 任务：绘图手势分类

在这本书的前面部分，我们创建了一个绘画识别应用程序，它允许用户对一幅绘画拍照并进行分类。在这个任务中，我们将做一些类似的事情，但是我们对`iOS`设备屏幕上绘制的图形进行分类，而不是照片。

### 7.3.1 问题与方法

正如4.7.1节中所说的，绘画是很有趣的，而且能够画出一些东西并让计算机识别，就像是一种魔法。

在这个任务中，我们从一个与上次略有不同的角度来探索绘图检测的实用方面。我们构建了一个`App`，让用户在他们的`iOS`设备上绘制一个简单的黑白图像，然后让`App`进行分类。

图7-4展示了完成的`App`。

![](images/8ff71dc7-0ac2-44f8-93bd-609ebfbc5b09.jpeg)

图7-4：绘图检测器

### 7.3.2 AI工具包和数据集

我们在这个任务中使用的AI工具包与在4.7.2节中使用的完全相同。事实上，我们使用相同的模型和相同的数据集，如图7-5所示。

![](images/a9529abb-dbab-496d-a7c7-034411f5e738.jpeg)

图7-5：谷歌的“Quick, Draw!”游戏

在4.7.2节中，我们使用了`DrawingClassifierModel.mlmodel`模型文件。我们在这里再次使用这个模型。

### 7.3.3 构建应用程序

我们已经准备好了模型，可以直接开始构建`App`。我们的`App`使用`UIKit`，包括以下功能。

• 一个用于绘制图形的`UIImageView`。
• 一个用于触发绘图分类的`UIButton`。
• 一个具有一些按钮的`UINavigationBar`，让我们可以清除绘图或者撤销。

![](images/546f958e-933a-408a-a41b-7b82e869cbf1.png)如果你不想手动创建起始`iOS` `App`，可以从我们的网站下载代码，找到名为`DDDemo-Drawing`的项目。当你完成这些之后，浏览本节的其余部分，然后在4.6.3节与我们汇合。

要自己构建`App`，需要执行下列操作。

1.在`Xcode`中创建一个`iOS` `App`项目，选择“`Single View App`”模板。
2.创建项目后，打开`Main.storyboard`文件，并且创建一个具有以下组件的UI：
• 一个`UINavigationBar`，它包含一个`UIBarButtonItem`（在左边）用于清除视图，一个`UIBarButtonItem`（在右边）用于撤销。
• 一个`UIImageView`，用于绘制。
• 一个`UIButton`去执行分类。你可以在图7-6看到我们的`storyboard`的示例。

![](images/29b40c5b-1849-46b1-b723-3a3898231b3d.jpeg)

图7-6：绘图检测器`storyboard`
3.在布局好必要的元素之后，请确保添加了适当的约束。
4.连接`ViewController.swift`文件中UI对象的输出口，如下所示：

![](images/92eb1d27-ab79-437f-81a6-1b47e93847b4.jpeg)
5.连接UI对象的运动，如下所示：

![](images/7f892a99-5c89-46d1-a3f5-2ec5b3efff0c.jpeg)
6.从7.3.2节中拖入模型文件`DrawingClassifierModel.mlmodel`，将它放入项目中，允许`Xcode`将其按需复制，如图7-7所示。

![](images/1dd2e31a-1d5e-4b84-a66f-327c66d31912.jpeg)

图7-7：模型正在被添加到项目中

接下来创建一个名为`Extensions.swift`的新文件，去保存一些我们需要让项目正常运行的扩展（稍后回到`ViewController.swift`文件）。

1.添加一个名为`Extensions.swift`的新文件到项目中。
2.添加以下`import`。

![](images/bab8c579-636c-4cc6-80b6-c202710c8004.jpeg)
3.在`CGContext`上添加扩展：

![](images/1b996ce7-4b31-4012-a29b-6308d764894b.jpeg)

这个扩展扩展了`CGContext`，添加了一个`create()`函数，该函数接受一个`CGSize`并返回一个`UIImage`。`CGContext`是一个可以在苹果的渲染子系统中绘制的目标（不一定是字面上的绘制，只是一个可以用于图形的地方）。你可以在苹果的文档中阅读关于`CGContext`的更多信息。
4.在`UIButton`上添加扩展：

![](images/1e19b241-65bd-4697-85fe-3f074e5ea942.jpeg)

如果你已经完成了本书中的一些其他任务，这应该看起来比较熟悉：它可以让我们在`UIButton`上调用`enable()`或`disable()`来设置其状态和颜色。
5.在这个`Swift`文件中，在`UIBarButtonItem`上添加一个扩展：

![](images/379343b4-e505-4d8f-bb58-a8f03bca392.jpeg)

与在`UIButton`（我们将使用的另一种类型的按钮）上添加扩展是一样的。

接下来，添加另一个名为`Image.swift`的新文件，我们将使用该文件提供一些遍历代码来帮助处理图像。

1.添加一个名为`Image.swift`的新文件到项目中。
2.添加下列`import`：

![](images/1e7e5ad4-14c2-4c34-b117-7c40d1108b42.jpeg)
3.在`CIFilter`上添加以下扩展：

![](images/062f4f7c-ded5-473d-855e-658af5e7c783.jpeg)
4.在`UIImage`上添加一个扩展，我们马上会为它添加两个函数：

![](images/7a957297-aa77-4f4a-9cfd-7c0588a3c62c.jpeg)
5.需要添加到`UIImage`扩展中的第一个函数允许我们应用`CIFilter`：

![](images/c079b483-d324-4a7c-a2b5-0c1454fde1b3.jpeg)
6.第二个函数是图像方向辅助函数：

![](images/408f5b5b-d046-4f9d-857d-b5f5e572208a.jpeg)

在我们最后的新文件`Drawing.swift`中，添加一些辅助函数来和AI组件一起工作。

1.添加一个名为`Drawings.swift`的新文件到项目中。
2.添加下列`import`：

![](images/ab4cf705-c779-458a-b556-1b6091c9ab2b.jpeg)
3.创建一个名为`Drawing`的`enum`，实现`CaseIterable`协议，并涵盖绘图分类器所知的全部类型的情况：

![](images/729e92d8-dfa6-4713-a385-d122f4523e21.jpeg)
4.在`Drawing` `enum`中添加一个`init()`函数：

![](images/b59b4ac0-6162-4e7b-8f92-7dbde2223f34.jpeg)

这个`init()`函数接受一个字符串，并将它与我们刚才提到的一种情况进行匹配。我们对一些事情进行特殊处理，比如“coffe cup”匹配`.coffee`、“hot dog”匹配`.hotdog`、“ice cream”匹配`.icecream`。
5.在`Drawing` `enum`中，给每个情况设置一个附加的`emoji`图标：

![](images/d095010e-9630-4f88-8be2-d824c60dfea7.jpeg)
6.在`VNImageRequestHandler`上添加一个扩展，增加一个便利构造器：

![](images/afa83b33-885e-4148-af18-fc188df37052.jpeg)

这允许我们用`UIImage`创建一个`VNImageRequestHandler`。`VNImageRequestHandler`是苹果`Vision`框架的一部分，允许我们在图像发生一些事情时发出请求。
7.在模型上添加下列扩展：

![](images/a9dda3d3-c971-4ff5-a5aa-4d3031c7af0f.jpeg)

这个`DrawingClassifierModel`的扩展添加了一个名为`classify()`的函数，该函数允许我们传入一个`UIImage`用于分类。这个`UIImage`将是我们想要分类的绘图。`Classify()`函数的作用是检查是否收到了图像，然后检查它是否连接到`CoreML`模型，该模型可用于通过苹果的`Vision`框架(`VNCoreMLModel`)发出请求，然后在队列上启动`VNClassificationObservation`的请求。我们使用之前创建的`Drawing` `enum`作为结果。

![](images/71019fe5-13a7-4024-a77b-042276e80304.png)如果由于某种原因，你的模型文件被命名为不同的名称，那么你的扩展将改为扩展对应的模型。
8.在`VNClassificationObservation`的`Collection`上添加一个扩展：

![](images/dab042ce-0ad3-442c-91c8-46cb6ba7fd47.jpeg)

这个扩展添加了一个`list` `var`，它允许我们在需要时获取包含分类可信度的`string`。

现在，我们可以回到`ViewController.swift`文件，并把它们放在一起。

1.在输出口和运动下面添加一些有用的变量：

![](images/1bb099ce-b04c-4aad-aa4b-49eb613c38b4.jpeg)

在这里，我们有一个存储分类的地方、一个用于存储笔画的`CGMutablePath`（图形由多个笔画组成，而`CGMutablePath`是一种存储笔画的方式）、一个表示图像视图大小的`CGSize`以及一个模型文件的句柄。
2.重写`viewDidLoad()`：

![](images/8479f2c5-6dfc-4046-8809-56392f385ebd.jpeg)

这样做允许我们禁用视图中的按钮，它们在视图加载后不需要立即处于激活状态。
3.现在，我们进入实际的绘图代码。首先，重写`touchesBegan()`：

![](images/94d7ae8e-9c8c-4790-af82-3553f923fb1b.jpeg)

这允许我们检测视图中何时发生触摸。我们获取触摸并使用它附加到我们之前创建的`CGMutablePath`变量（用于笔画）。
4.重写`touchesMoved()`：

![](images/30e3e8dc-d260-40e5-9be0-c234368ad42f.jpeg)

这段代码做同样的事情，只不过是发生在触摸移动而不是开始时。
5.重写`touchesEnded()`：

![](images/c77b3390-0e69-4c18-9786-c647a95a78b7.jpeg)
6.添加一个`undo()`函数，如果需要的话，我们可以调用它去移除最近的笔画：

![](images/a4054fa9-74d2-4e02-8729-b376daa4d094.jpeg)

![](images/078dd8c7-08c4-4fd8-9784-f3ba04dd2e07.png)我们在这里没有以“正确”的方式实现撤销，因为撤销需求很简单，而本书不是教你如何使用苹果的非AI框架。如果你想知道在`Swift`应用程序中更全面的撤销方法，可以在苹果的文档中查看`UndoManager`。
7.添加一个`clear()`函数，如果需要移除所有笔画，可以调用它：
