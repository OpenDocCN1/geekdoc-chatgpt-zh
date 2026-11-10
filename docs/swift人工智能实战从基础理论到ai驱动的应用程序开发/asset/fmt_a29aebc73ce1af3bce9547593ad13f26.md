
对于下一个音频任务，我们希望你去想象你是在为动物园构建应用程序。你需要创建的功能之一是一个系统，当用户听到远处的动物发出声音时，可以打开该应用程序，并且该应用程序可以识别并告知用户是哪种动物的声音。这就是声音分类问题。

## 5.3.1 问题与方法

对于给定的声音，声音分类器会分配预定的标签集合里面的一个标签给它。它们是分类器，因此它们像其他任何分类器一样工作（我们在11.3.1节中讨论它们的工作原理）。

![](images/f41e9a4b-b483-483f-a524-eb520e1f3df9.png)苹果的机器学习工具提供的声音分类功能均未设计用于人类语音。你可以将它们用于怪异声音，但声音分类并不是针对语音识别而设计。

在本章中，我们将构建一个应用程序，其最终版本如图5-3所示，该应用程序可以将其听到的声音分类到9个不同存储桶之一。

![](images/d3d97eec-d50c-482d-b005-02e7b4a13de7.jpeg)

图5-3：声音分类器应用程序的最终版本

对于此任务，我们通过执行以下操作来探索声音分类的实践方面。

• 构建一个应用程序，它可以录制一些音频，对录音进行声音分类，并告知我们是哪种动物发出声音。
• 选择一个工具包来创建声音分类模型，并为问题组装数据集。
• 建立和训练声音分类模型。
• 将声音分类模型整合到应用中。
• 改进应用程序。

之后，我们将快速探讨声音分类的工作原理，并分享一些其他资源，你可以自行改进。让我们开始吧。

## 5.3.2 构建应用程序

声音分类应用程序将使用苹果的`iOS`旧版`UI`框架`UIKit`。此应用程序对本机`iOS`视图进行了更高级的使用，包括`UICollectionView`和`UIProgressView`在内，因此，如果你不熟悉这些内容，那么这个应用程序最后可能会看起来有些吓人。

一点都不用害怕。相对于我们一直在使用的其他`iOS`视图，我们在使用过程中将会更详细地讲解这些内容。

图5-3显示了我们将在此处构建的应用程序的界面样式，即使只是一个最初形式。该样式将包含以下组件。

• `UIButton`，触发声音的录制（以及之后的自动分类）。
• `UICollectionView`，以`emoji`形式显示不同动物的集合（每个动物都包含在`UICollectionViewCell`中），根据听到的动物声音类型而亮起。
• `UIProgressView`，显示应用程序在其录制（收听）过程中进行到了什么程度。
• `AVAudioRecorder`（及其关联的`AVAudioRecorderDelegate`），录制音频。

![](images/1b2f196e-9f3c-4b5b-9cdc-1790b367b0a8.png)如果你不想手动构建这个初始版`iOS`应用，则可以从我们的网站下载代码，然后找到名为`SCDemo-Starter`的项目。完成后，请浏览本节的其余部分（不要跳过！），然后在5.3.3节中与我们会面。

要自己动手实践声音分类的读者，请按照下列步骤操作。

1.在`Xcode`中创建一个`iOS`应用项目，选择“`Single View App`”模板。不要选中`Language`下拉菜单下方的任何复选框（它们通常设置为“`Swift`”）。

![](images/afc79826-1592-4427-9be4-da49cf6d57f8.png)我们将从代码而不是`storyboard`开始。这是因为我们正在创建一些从标准`UI`对象继承的自定义类。

2.将一个新的`Swift`文件添加到项目中，并将其命名为`Animals.swift`。在该文件中，添加以下枚举：

![](images/38693eaa-d602-4976-881b-ce30f77b2eb9.jpeg)

我们将使用这种枚举类型来表示应用可以检测到的动物声音。请注意，我们创建的名为`Animal`的枚举同时符合`String`和`CaseIterable`。`String`的含义很明显：这是字符串的枚举，但是符合`CaseIterable`使我们可以使用`.allCases`属性访问`Animal`的所有枚举值的集合。

![](images/5324a8d8-17f2-4f81-ab83-acb316e56e68.png)你可以在苹果文档中阅读有关`CaseIteratble`协议的更多信息。

3.设置好`Animal`类型后，我们需要添加一些枚举值。在`Animal`类型内，将以下内容添加到顶部：

![](images/fda64229-becf-47a2-bdec-934d80b9a00d.jpeg)

这是9种不同的动物枚举值，我们可以对它们进行声音分类。

4.添加一个构造器，以便在需要一个`Animal`时可以正确分配枚举值。

![](images/138a5622-fced-43a8-b8c4-02a606e4c612.jpeg)

这会将传入的原始值与其中一种情况进行匹配，但传入的原始值是字符串“`rooster`”或字符串“`hen`”的情况除外，这两种情况下也都与`chicken`匹配，因为它们都是不同类型的鸡（该说明只适用于此应用）

5.针对每种枚举值返回一个漂亮的图标（将只是一个`emoji`）：

![](images/ec8e4049-54c4-4913-9129-8b440129cf54.jpeg)

6.为每种动物分配一种颜色，以便最终可以用一个好看的界面来显示它们：

![](images/22781536-b702-4c4c-9374-037721b951f0.jpeg)

我们只是在这里随意选择了一些颜色，如果你有比我们更好的主意，请自行决定。

这就是我们在`Animals.swift`中需要做的所有事情，因此请确保已保存文件，然后继续执行`ViewController.swift`文件。这部分还有很多工作要做。

我们需要在`ViewController.swift`中做的第一件事就是创建一个按钮，它可以在三种不同的状态之间切换。我们将使用此按钮让用户录制声音，最终将该声音分类。

该按钮需要能够以一个用户友好的方式邀请用户触发录音，然后要能够切换到正在进行录音的状态显示。我们还希望将其设置为禁用状态，以防无法进行录制或应用程序正在忙于对声音进行分类的情况（这可能需要花费一些时间）。

![](images/506a85a4-4a4b-4989-9d70-8471efa85e17.png)我们可以在标准的`UIButton`上手动执行所有这些状态更改，但是我们想确保以后连接到`AI`功能的代码尽可能简洁明了，因此我们以一些方式抽象一些内容，使代码更明显。另外，这样做是个好习惯！

1.在`ViewController.swift`文件中添加一个新的类：

![](images/37793ff9-c777-424b-9d9d-0608b8164547.jpeg)

这只是一个从`UIButton`继承的名为`ThreeStateButton`的新类。此时，我们可以实现一些`ThreeStateButton`，它们只是`UIButton`。

2.添加一个`enum`来表示按钮的不同状态：

![](images/a5d32d83-f7c2-428c-8e0e-056d0ab8cd2b.jpeg)

3.添加一个函数更改按钮的状态：

![](images/61e7370b-4583-40e8-8310-5b52bb714830.jpeg)

该函数采用一个状态（就是我们刚才创建的`ButtonState`枚举），并将`ThreeStateButton`更改为该状态。每个状态都涉及标题的更改（在调用时提供，未预定义）、新的背景色（也在调用时提供），以及按钮的实际启用或禁用情况。

现在是构建`UI` `storyboard`的时候了，但是我们还需要做一件事情。因为我们将使用`UICollectionView`，它由单元格的集合组成，所以我们将`UICollectionViewCell`子类化，并根据应用检测到的声音，使用它来显示每种动物类型。

4.将以下代码添加到`ViewController.swift`文件中，在所有现有类或定义之外（建议将其添加到最底部）：

![](images/09ff9406-4d6b-495e-aaf4-769321b62cf4.jpeg)

这将创建一个`UICollectionViewCell`的子类，名为`AnimalCell`，并提供一个标识符，我们可以在`storyboard`中引用该标识符。

现在，你可以打开`Main.storyboard`文件，并创建一个`UI`。

5.将以下组件添加到`storyboard`中。

• `UIButton`，用于触发录音（并显示录音正在进行中）。
• `UIProgressView`，显示录音的长度。
• `UICollectionView`，其中包含单元格，以显示该应用程序可以检测到的每种动物的声音类型。
• 在`UICollectionView`中，有一个原型`UICollectionViewCell`，它显示所有动物。你可以在图5-4中看到`storyboard`的图像。确保你已添加必要的约束！

![](images/cef276c2-171d-4b0a-a936-40d8da397529.jpeg)

图5-4：声音分类应用程序`storyboard`

6.我们需要将其中一些组件的类更改为先前在代码中创建的自定义类型。在`UICollectionView`中选择`UICollectionViewCell`，然后在打开`Identity Inspector`的情况下，将其类更改为`Animal Cell`（它应该会自动完成），如图5-5所示。

![](images/c1a184da-6846-4c66-a04f-6de073a84326.jpeg)

图5-5：更改`UICollectionViewCell`的类

7.在单元格的视图内，在`storyboard`中添加一个大的`UILabel`，并使用约束适当地将其居中。

8.我们需要为`AnimalCell`添加一些输出口。在`ViewController.swift`的`AnimalCell`类定义中添加以下输出口，分别将它们连接到“`Cell View`”（单元格中预先制成的`UIView`）和你创建的`UILabel`：

![](images/43ce94ce-df02-499e-a41f-f594e077d2b6.jpeg)

9.选择我们第一次使用此`storyboard`时创建的`UIButton`，并在其标识检查器中将其类更改为`ThreeStateButton`（就像我们对`UICollectionViewCell`/`AnimalCell`所做的那样）。

10.将一些输出口连接到`ViewController`类：

![](images/1ab22f64-e7ac-4975-b588-3ccf8550b533.jpeg)

这些输出口用于`UICollectionView`本身、`UIProgressView`，以及刚刚将它的类更改为`ThreeStateButton`的`UIButton`。

11.添加一个连接到`ThreeStateButton`的动作：

![](images/37274fa2-8eac-48f6-91a7-72683cbd4aa5.jpeg)

12.将以下属性添加到`ViewController`类：

![](images/a3b478f6-b5fe-4bc0-8eb1-9e757ef02524.jpeg)

这些属性定义了录音长度、用于存储声音的最终分类的变量、`AVAudioRecorder`，以及用于存储录音的文件的文件名。

13.更新`viewDidLoad()`函数，使其和以下内容一致：

![](images/fe39489b-a32e-47a7-bb0b-8793b96a57db.jpeg)

14.添加一个函数，使用我们之前创建的属性来访问`AVAudioRecorder`，以开始音频录制：

![](images/9b43d461-ecc8-4542-9565-b14ae0089966.jpeg)

15.你还需要一个函数来完成录制，以`Bool`作为参数来指示录制是否成功，以防录制失败的情况（默认为`true`）：

![](images/4b7fb679-5143-4cae-9631-5fef5e0d7a63.jpeg)

16.添加一个函数来更新`UICollectionView`，以显示我们认为该声音是哪种动物发出来的。此函数将`Animal`作为输入：

![](images/99a0832b-8ccc-40c8-8577-239d4ddc14c2.jpeg)

17.在`ViewController`类中（但不在`ViewController.swift`文件中），向`AVAudioFile`（录音的结果）添加一个函数，并对其进行处理（添加`AI`功能后，它可以做更多的事情）：

![](images/1a23ca6a-5074-4c21-aa04-32e3dc1a0521.jpeg)

目前，我们已经完成了`ViewController`类的工作。现在需要在该类中添加一个或三个扩展。

1.在`ViewController`类的末尾下方（但仍在`ViewController.swift`文件中）添加以下扩展，在有问题出现时，它将显示提示信息（弹窗）：

![](images/a1df16a3-0697-4c28-90e3-6e0d57372bfc.jpeg)

2.在其下方添加另一个扩展，以使我们能够遵守`AVAudioRecorderDelegate`，以便与`AVAudioRecorder`一起使用：

![](images/36b5a80d-a8d8-473e-bd64-06ece33a4c4a.jpeg)

3.再次在其下添加最后一个扩展，让应用符合`UICollectionViewDataSource`，该扩展提供了填充`UICollectionView`的功能（用`AnimalCell`填充）：

![](images/61d31e5e-07d5-4cad-931d-39e3a89f1599.jpeg)

4.如果你愿意的话，可以再添加一个启动屏幕和一个图标（与之前的实践任务一样，我们在可下载的资源中提供了一些资源），然后你可以在模拟器中启动该应用。你将会看到类似于图5-3所示的内容。

这个应用确实可以录制声音，但是却没有任何作用。你无法播放录音，并且它显然还没有与任何形式的机器学习模型相关联。回看我们编写的`classifySound()`函数，我们每次其实只是随机选择了一只动物。

让我们继续下去并添加一些`AI`功能。

## 5.3.3 AI工具包和数据集

与常规的`AI`任务一样，我们需要组装一个工具包来解决问题。在这种情况下，我们使用的主要工具有：用于准备训练数据的`Python`、用于进行训练的`CreateML`应用程序，以及用于读取应用程序中的模型的`CoreML`。

为了建立模型来增强应用程序对动物声音分类的能力，我们需要一个充满动物声音的数据集。与往常的机器学习和`AI`数据集任务一样（你可能已经注意到了一种模式），科学家们已经为我们准备好了。

如图5-6所示，环境声音分类数据集(`ESC`)是各种声音的简短环境录音的集合，涵盖了5个主要类别。

前往`ESC-50` `GitHub`存储库并下载数据集的副本。将其保存在安全的地方。

![](images/ac3efa5d-0c7e-48ab-98c2-12c648de97bf.jpeg)

图5-6: `ESC`的主要类别

![](images/205c74c5-2817-4e88-907a-6775904aca03.png)你可以自己完成`Python`脚本手动执行的所有操作，但可能要比使用脚本花费更多的时间。在处理机器学习问题时，始终使事情可重复是一种很好的做法。

按照2.5.1节中的说明启动新的`Python`环境，然后执行以下操作。

1.使用文本编辑器（我们偏好`Visual Studio Code`和`BBEdit`，但其他也可以使用）创建以下`Python`脚本（我们的叫作`preparation.py`）：

![](images/ecddcd18-fd02-45c4-95f6-c025acf0e9d1.jpeg)

该脚本导入`Pandas`（如图2-18所示），创建一个包含每个类的子文件夹的输出文件夹，然后解析以逗号分隔的值(`CSV`)文件，并以新格式写出文件。

![](images/c9ee717d-583f-46e3-ba63-88050b16d726.png)我们在这里使用`Pandas`框架来访问其`CSV`读取功能。

2.要将脚本指向相应的类和输入文件，请在`Python`脚本的顶部（即在导入语句之后但在实际脚本开始之前）添加以下配置变量：

![](images/23388371-05f2-4d96-8b57-41822e6aeb29.jpeg)

更新每个路径以指向系统上的正确位置：`input_classes_filename`应该指向数据集副本随附的`esc50.csv`文件；`sounds_directory`指向`/audio/`文件夹；`output_directory`指向脚本将其训练所需的文件子集放置到的位置（我们在下载的数据集中创建了一个名为`/classes/`的文件夹）。

`classes_to_include`列表包含应用程序可以对其声音进行分类的所有动物（并非数据集中存在的所有动物声音）。

通过在命令行(`python prepare.py`)上执行脚本来运行准备`Python`脚本。现在你应该已经准备好了数据，并且将其保存在类似于图5-7的文件夹结构中。

![](images/b0d288a0-2339-4e23-9adb-3c29b2c95821.jpeg)

图5-7：用于训练声音分类器的准备数据

## 5.3.4 创建模型

准备好动物声音数据集后，让我们前往苹果的`CreateML`应用程序，就像在第4章中所做的那样，建立声音分类模型。

![](images/858def98-1574-4d36-acba-1c3583ee7cb4.png)要了解有关`CreateML`的各种形式的更多信息，请返回2.3.2节。

让我们建立动物声音分类器模型。

1.启动`CreateML`，并通过选择合适的模板来创建一个新的`Sound Classifier`项目。如图5-8所示。

![](images/9079da59-01aa-4aa2-a113-4b45274184a2.jpeg)

图5-8: `CreateML`模板选择器中的声音分类器

2.为项目提供一些详细信息后，你将看到一个新的空`Sound Classifier`项目，如图5-9所示。

![](images/2acbf2da-b0e8-40c8-a7f3-fbe18b1e562f.jpeg)

图5-9: `CreateML`项目已准备就绪，可以录制声音
