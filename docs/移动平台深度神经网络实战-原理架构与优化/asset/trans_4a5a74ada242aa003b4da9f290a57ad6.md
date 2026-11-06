

第16行，定义了`LoadTensorsTables`函数，用于加载模型中的所有`Tensor`的基本信息。

第25行，定义了`LoadOperatorsTable`函数，用于加载模型中用到的所有操作符对象的基本信息。

第40行，定义了`ImportTensors`函数，用于从模型中读取所有`Tensor`的数据。

第60～76行，从导入的`Tensor`数据中获取`quantization`，表示数据是否量化，如果量化了就读取量化的最小值与最大值，根据参数对读取的数据进行量化。

第80行，定义了`ImportOperators`函数，用于从模型中读取操作符的详细数据。首先遍历模型中的所有操作符，如果操作符名称在系统中尚未注册则抛出错误，否则调用`Deserialize`反序列化模型数据，如果TensorFlow不支持这种自定义操作符，那么也会抛出错误。如果没问题就创建`Op`对象并初始化数据，添加到模型中。

第122～134行，从`Tensor`记录中查找该操作符关联的所有输入`Tensor`，并将`Tensor`添加到操作符中。

第135～140行，从`Tensor`记录中查找该操作符关联的所有输出`Tensor`，并将`Tensor`添加到操作符中。

第144行，定义了`ImportIOTensors`函数，该函数用于导入模型中涉及数据输出的`Tensor`。

第169行，定义了`Verify`函数，该函数用来检查缓冲区数据是否属于正常的`flatbuffers`格式缓冲区，如果是的话就返回校验后的缓冲区。

第175行，定义了`Import`函数，该函数用来从文件内容中导入TensorFlow模型，首先调用`Verify`校验缓冲区模型，然后调用`GetModel`函数读取TensorFlow模型，接着调用`LoadTensorsTable`、`LoadOperatorsTable`加载向量表和操作符表，然后调用`ImportTensors`和`ImportOperators`读取向量和操作符数据，最后调用`ImportIOTensors`导入输出`Tensor`，并返回加载后的模型对象。

`Export`函数的代码较为庞杂，这里就不罗列了，有兴趣的读者可以自行阅读TensorFlow Lite的代码作为了解这部分内容的扩展知识补充。

## 11.5 本章小结

本章的重点是集成TensorFlow Lite，并详细分析了TensorFlow Lite的代码。首先讲解了TensorFlow Lite的构建问题。因为TensorFlow Lite采用了`Bazel`构建系统，因此我们首先介绍了`Bazel`，然后讲解了TensorFlow Lite中的`Bazel`构建文件。接着讨论了如何直接在我们自己的系统中集成TensorFlow Lite，以简单的C++代码为例介绍了TensorFlow Lite的简单调用方式。然后讲解了TensorFlow Lite的内部结构和核心组件，并讨论了如何根据实际需求完成TensorFlow Lite的修改。最后讨论了TensorFlow Lite的模型处理代码，完成了TensorFlow的模型转换与压缩，实现了在移动平台使用TensorFlow训练的模型，这些也是TensorFlow Lite的关键内容。

# 第12章 移动平台框架与接口实战

我们已经在本书的前序章节中涵盖了移动计算平台和嵌入式平台实现机器学习解决方案的方方面面，理解和掌握这些内容对于研发移动平台机器学习系统或深度学习系统极为重要，这不仅仅是从理论层面如此考虑，事实上这更是在实际产品研发过程中透彻理解系统架构、鉴别系统关键优化指标的基石。从知识体系结构完整性出发，我们有必要针对移动计算平台接口进行介绍。在本章中，我们将针对`Core ML`和`NNAPI`进行阐述，辅以实战内容。并在最后做出总结，探讨研发移动平台机器学习系统以及未来展望。

## 12.1 Core ML

在结束整个移动平台深度神经网络理论和实战的内容以前，还有一些重要内容尚未涉及。那就是通用流行的用于机器学习开发的移动平台框架与接口。这些技术相对易于掌握和使用，对快速开发和构建应用于经典使用场景的机器学习应用程序来说尤为有用。比如，想开发一个能够做图像识别的App，通过图片的输入（比如使用摄像头）来识别所拍摄的花朵的种类，那么使用本节介绍的框架`Core ML`和相应的工具就显得尤为方便。开发者可以通过`Core ML`开发图像识别功能、自然语言处理系统以及执行训练好的决策树。`Core ML`框架基于苹果自有的`Accelerate`、`BNNS`和`Metal Performance Shaders`等技术构建，并针对性能、存储和功耗等方面进行了优化。通过使用`Core ML`，一般的开发人员也可以轻松简单地在iOS平台部署离线机器学习应用。

当然，这并不是说`Core ML`无法胜任复杂的机器学习任务或深度神经网络的运算，Amazon和Apple在MXNet机器学习框架中投入了大量精力，通过使用Amazon AWS平台或直接使用MXNet来进行训练而产生的模型可以经过简单的配置和开发工作，使用`Core ML`在iOS或iPad OS平台运行。因此，对于典型的通用机器学习任务来说，使用`Core ML`能够帮助我们在以考虑优化为前提的条件下，快速构建运行在iOS或iPad OS设备上的应用程序，对实际的生产环境和具体应用具有一定价值，值得我们对其进行介绍，并针对典型的工作流进行阐述。

我们会在本节当中创建一个可以识别花的品种的iOS App，使用`Create ML`来创建模型，并使用`Core ML`作为前向引擎做最终的预测，最后部署应用程序到设备上。

### 12.1.1 准备数据和生成模型

我们反复强调数据的重要性，这不仅仅在于数据的容量，我们还需要对收集的数据进行清洗等操作才能达到一个数据可用的状态。在本节中，为了方便起见，我们选择由Kaggle收集整理好的数据作为训练和测试数据，并使用`Create ML`来创建我们的模型。

首先，下载本书的参考代码（见第12章代码和数据），我们会得到以下准备好的数据。其中每个目录下包含了一定数量的对应花的品种的图片，这些图片已经过清洗和预处理可供我们直接使用，如图12-1所示。

![000](OEBPS/Images/427-1.jpg)

图12-1 用于`Create ML`的训练和测试数据

创建这样的目录结构是有原因的，我们需要保持数据的摆放结构与`MLImageClassifier.DataSource`类型兼容，该类型的数据源是供`Create ML`工具的图像分类器使用。`Create ML`主要用来创建典型的机器学习模型，其包含了计算机视觉、自然语言处理等各个方面的机器学习功能，如表12-1所示。

表12-1 `Create ML`主要类型

![000](OEBPS/Images/428-1.jpg)

MXNet模型可以兼容`Core ML`，因此可以在Amazon SageMaker上训练模型或自己训练MXNet模型供`Core ML`使用。

接下来，我们来创建一个Xcode项目，选择play-ground类型，如图12-2所示。

![000](OEBPS/Images/428-2.jpg)

图12-2 创建一个playground项目

然后在macOS平台下创建一个Blank项目，并将其命名为“`FlowerExpert`”，如图12-3所示。我们在macOS平台下创建该项目的目的在于训练数据，而非开发基于`Core ML`的预测程序，并且训练数据和产生模型的工作需要预先准备好，以便于后续工作的展开。

![000](OEBPS/Images/428-3.jpg)

图12-3 在macOS系统下创建空白项目

编写一小段代码，用来初始化`Create ML`的图像分类构建器，如代码清单12-1所示。

代码清单12-1 构建分类器

```
import CreateMLUI

let builder = MLImageClassifierBuilder()
builder.showInLiveView()
```

与此同时，确保Xcode Live View已被开启，我们可以看到通过以上一段代码我们初始化了一个名为`ImageClassifier`的图像分类器视图，如图12-4所示。

![000](OEBPS/Images/429-1.jpg)

图12-4 开启Xcode Live View功能

接着，将已经准备好的训练数据通过鼠标拖动的方式拖入图12-4所示的虚线框当中，开始训练并等待训练结束，我们可以从底端的控制台输出来查看处理图片的数量、所消耗的时间以及完成百分比，如图12-5示。

![000](OEBPS/Images/429-2.jpg)

图12-5 开始训练Training Data

当训练集计算完成后，再使用相同的方法将测试集拖入虚线框中完成测试，结果如图12-6所示。

![000](OEBPS/Images/430-1.jpg)

图12-6 测试集的预测结果

保存训练后的模型文件，我们可以为其设定一些基本信息，如图12-7所示。保存后的模型文件以`.mlmodel`结尾。这样一来，我们就通过`Create ML`创建完成一个图像分类器。

![000](OEBPS/Images/430-2.jpg)

图12-7 为模型设置基本信息并保存

### 12.1.2 App实战：引入Core ML实现

现在，我们已经创建好供`Core ML`使用的机器学习模型了。接下来看一下如何让这个快速训练出来的机器学习模型付诸实践。在本节中，我们会创建一个简单的iOS或iPadOS兼容的识花App，这个App的主视图由一个摄像头捕捉的实时画面构成，并在其视图的正下方有一个半透明的文字输入框，用来显示由摄像头画面作为输入的数据和由`Core ML`预测出的结果，由于我们训练的模型是用来识别几种不同的花的类别，因此我们将App命名为“`FlowerDetect`”。实际的运行效果如图12-8所示。从图中可以得知，摄像头取景框中的实时场景通过预测识别出蒲公英（dandelion）的准确率为99.97%。

![000](OEBPS/Images/430-3.jpg)

图12-8 基于`Core ML`的识花实时预测App

创建这个App十分简单。首先，我们只需要创建一个全新的Swift项目，选择Single View App选项，然后将其命名为`FlowerDetect`，如图12-9所示。

![000](OEBPS/Images/431-1.jpg)

图12-9 创建iOS运行的Single View App程序

我们将前面创建好的机器学习模型拖入Xcode的项目列表中，将其命名为`ImageClassifier.mlmodel`，供后续代码使用。接着，我们先来创建App图形界面相关的代码和逻辑，如代码清单12-2所示。

代码清单12-2 创建App图形界面相关代码

```
  1 import UIKit    
  2 import AVFoundation
  3 import CoreML
  4 import Vision
  5 import ImageIO
  6 
  7 Class ViewController: UIViewController {
  8     @IBOutlet weak var previewView : UIView!
  9 
 10     var timepoint: Int64 = 0
 11     var boxView:UIView!
 12     let boxTextView: UITextView = UITextView()
 13 
 14     var videoDataOutput: AVCaptureVideoDataOutput!
 15     var videoDataOutputQueue: DispatchQueue!
 16     var previewLayer:AVCaptureVideoPreviewLayer!
 17     var captureDevice : AVCaptureDevice!
 18     let session = AVCaptureSession()
 19 
 20     override func viewDidLoad() {
 21         super.viewDidLoad()
 22 
 23         timepoint = Int64(Date().timeIntervalSince1970 * 1000)
 24         previewView.contentMode = UIView.ContentMode.scaleAspectFit
 25         view.addSubview(previewView)
 26 
 27         boxView = UIView(frame: self.view.frame)
 28         boxTextView.text = "Classifying..."
 29         boxTextView.font = UIFont.init(name: "Helvetica", size: 16)
 30         boxTextView.textContainerInset = UIEdgeInsets.init(top: 10, left: 10, bottom: 0, right: 0)
 31         boxTextView.frame = CGRect(x: 0, y: 0, width: 300, height: 80)
 32         boxTextView.frame.origin = CGPoint(x: 30, y: 90)
 33         boxTextView.backgroundColor = UIColor.white
 34         boxTextView.alpha = 0.8
 35         boxTextView.layer.masksToBounds = true
 36         boxTextView.layer.cornerRadius = 6
 37         boxTextView.layer.position = CGPoint(x: self.view.frame.width / 2, y: self.view.frame.height - 100)
 38 
 39         view.addSubview(boxView)
 40         view.addSubview(boxTextView)
 41 
 42         self.setupAVCapture()
 43     }
 44 
 45     override var shouldAutorotate: Bool {
 46         if (UIDevice.current.orientation == UIDeviceOrientation.landscapeLeft ‖
 47             UIDevice.current.orientation == UIDeviceOrientation.landscapeRight ‖
 48             UIDevice.current.orientation == UIDeviceOrientation.unknown) {
 49             return false
 50         }
 51         else {
 52             return true
 53         }
 54     }
```

第1～6行，我们导入了一些必需的头文件，其中`UIKit`用于有关UI界面操作的接口，`AVFoundation`和`ImageIO`用于处理摄像头设备、音频、视频以及图像处理相关的功能。`Core ML`则不必多说，使用该框架来对输入的数据进行分类，而导入的`Vision`框架则与`Core ML`协同工作，用于处理图像信息并输入`Core ML`做预测动作。

第8行，我们创建了一个名为`previewView`的`UIView`，并将其定义为一个`IBOutlet`，这意味着我们会在`Main.storyboard`当中将其定义与视图建立一个连接，读者可以自己从头开始创建连接，也可参考本章代码。该视图的作用是用来显示预览画面，即我们稍后会将摄像头捕捉到的画面投射在这个视图之上。现在，我们只需创建好这个`IBOutlet`，以供后续编码使用。

第10行，我们创建一个名为`timepoint`的数值型变量。我们会定时向`Core ML`中输入摄像头捕捉到的图像数据供其预测，因此我们在这里创建一个成员变量，用来记录上次输入图片数据的时间点，每隔一段时间更新这个变量并再次向`Core ML`中输入数据。

第11～12行，我们又创建了一套用于显示的工具，名为`boxView`和`boxTextView`，其中`boxView`为图13-8当中展示的文本框的容器，而`boxTextView`则主要负责显示具体信息。这里的代码跟我们创建`previewView`的方式稍有不同，我们直接在代码当中创建了`UITextView`对象，并在稍后的代码当中直接以编码方式将其插入视图。

第14～18行，我们创建了与视频输入输出和图像捕捉、会话等相关的变量供后续代码使用。

接下来，我们在`viewDidLoad()`方法当中初始化我们的App视图。

第24行，我们将`previewView`的视图模式设置为`scaleAspectFit`，这样就能确保其包含的内容能填充满整块设备屏幕。

第27～37行，我们初始化了一系列有关显示信息的文字框的功能，这其中包含设置的字体，文本框的位置、大小、背景、alpha通道以及矩形框的圆角等。

第39～40行，我们将创建好的视图用编码的方式插入主视图中。

第42行，调用`setupAVCapture()`方法初始化音视频设备。具体可见代码清单12-4。

第45行，我们重载`shouldAutorotate`方法来支持设备的自动翻转功能，由于这是涉及具体设备编程的知识点，超出了本书讨论的范围，感兴趣的读者可以参考苹果官方的开发文档[\1](../Text/Section_0012_0005.xhtml#b01)来了解更多信息。

接下来的内容十分关键，我们要在这里编写有关`Core ML`的代码，这是整个项目的核心部分，没了这部分逻辑，机器学习和预测就无从谈起。我们会创建`Core ML`请求并使用它，如代码清单12-3所示。

代码清单12-3 创建`Core ML`请求
