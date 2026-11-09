# ChatGPT驱动的Python办公自动化实战

# 书籍导读

本书聚焦于借助 ChatGPT 完成自动化办公的工作方法。先讲清楚 ChatGPT 的账号注册与使用方式，再说明它如何配合 Python 进行编程实践。读者可学会怎样与模型对话并在它的协助下产出高质量代码。随后围绕数据处理链路展开，涵盖用 ChatGPT 辅助的数据采集、清洗、存储、分析与可视化，并系统介绍它在 Excel、Word、PPT、PDF 与图片处理自动化中的具体用法。

适合 Python 入门者、想了解 ChatGPT 的读者、数据分析与办公自动化爱好者阅读，也可供软件工程师、程序员、教师及企业培训师参考借鉴。

# 写在前面的话
PREFACE

本书想说明的问题是：如何借助 ChatGPT 推动办公自动化。ChatGPT 由 OpenAI 推出，能够理解自然语言并生成自然语言回复。依托这一能力，可完成大量重复性办公任务，显著提升效率。

作为一名自动化办公从业者，掌握好辅助工具很关键。ChatGPT 能加速代码编写、数据清洗、数据可视化，也能辅助 Excel、Word、PPT、PDF 等常用办公软件的自动化操作。通过本书，读者可以系统掌握上述场景的应用技能。

全书共 12 章。

第 1 章简要介绍 ChatGPT 的使用方法，以及 Python 解释器与 PyCharm 开发工具的准备工作。

第 2 章讲解如何借助 ChatGPT 编写高质量代码，包含需求描述、细节补全、语法检查、调试以及对模型生成代码的评审。

第 3 章介绍 ChatGPT 在数据采集中的作用与基本技术，如网页爬取、数据解析、用 Selenium 处理动态页面，并配以示例。

第 4 章阐述 ChatGPT 在数据清洗中的价值，介绍数据质量评估及借助 NumPy、Pandas 等库进行清洗预处理的思路与示例。

第 5 章说明办公自动化中用 MySQL 与 JSON 格式存储数据的方法，包括 MySQL 的安装使用及通过 PyMySQL、Pandas 访问数据库。

第 6 章介绍办公自动化中的统计分析做法，涵盖 Pandas 描述统计与 ChatGPT 辅助分析。

第 7 章讲解使用 Matplotlib 进行数据可视化，包含折线图、柱状图、饼图、散点图等绘制，并给出 ChatGPT 辅助的案例。

第 8 至 11 章分别介绍 ChatGPT 辅助 Excel、Word、PPT、PDF 自动化的思路，每章先介绍相关库，再提供大量示例。

第 12 章介绍 Pillow 库的图片自动化处理及 ChatGPT 的辅助示例。

为便于学习实践，本书提供配套资源：

- (1) 示例代码与数据集；
- (2) 配套软件；
- (3) 学习支持。

这些资源可帮助读者更好地落地书中内容。希望与正文配合，成为掌握 ChatGPT 与办公自动化技能的有效参照。

本书面向的读者：

- (1) Python 初学者与对 ChatGPT 感兴趣的人：可将本书作为入门教程，书中示例详实，上手容易，能快速掌握 ChatGPT 的使用与 Python 办公自动化的基础。
- (2) 数据分析与办公自动化爱好者：书中包含数据采集、清洗、分析、可视化方法，以及对 Excel、Word、PPT、PDF 的自动化技巧，有助于提升效率与挖掘数据价值。
- (3) 软件工程师与程序员：书中代码示例展示了高质量代码编写与调试技巧，有助于提升编码水平与开发效率。
- (4) 教师与企业培训师：内容系统全面，实用性强，可参考用于课程设计或作为教学参考。

希望本书帮助更多人认识这一强大工具并用其解决办公自动化问题。若对您有帮助，不胜荣幸。学习使用过程中如有疑问或建议，欢迎反馈，我会持续更新完善。最后感谢开源社区与各位作者，让案例与库得以实现，亦希望本书能回馈社区。

关东升
2024 年 2 月于鹤城

# 知识图谱
![](img/817f9d0d19c4377d32975098402bcdd3_5_0.png)

# 第 1 章 初识 ChatGPT

本章全面介绍 ChatGPT 的来历、注册方式、操作步骤、主要功能与典型场景，帮助读者建立清晰认知，实现高效协作。

## 1.1 ChatGPT 概览

ChatGPT 是一款由 OpenAI 开发的 AI 写作助手，基于 GPT 模型构建，能够依据人工输入自动产出匹配的文本，如问答、对话、文稿润色或文章草案建议等。它让 AI 技术服务于个人与企业，提升内容创作的自动化水平与效率。

### 1.1.1 注册流程

使用前需在 OpenAI 官网 https://openai.com/ 创建账号，页面上可找到“Try ChatGPT”入口，点击后进入注册登录页。也可直接通过 https://chat.openai.com/auth/login 访问。

在登录页点选 Sign up 进入邮箱填写页，输入有效邮箱并继续，会收到验证邮件完成验证。若已有微软或谷歌账号，建议直接用其登录。

邮箱验证通过后需补充个人详细信息，完成后继续进入手机号码验证，验证成功即注册完成。

> ① GPT 即 Generative Pretrained Transformer，是基于 Transformer 架构预训练的语言生成模型。ChatGPT 的核心能力源于 OpenAI 研发的 GPT-2 语言模型，这是一个含十亿级参数的大规模神经网络，专为文本生成设计。

![图 1-1 OpenAI 官网页面](img/817f9d0d19c4377d32975098402bcdd3_14_0.png)

![图 1-2 注册和登录页面](img/817f9d0d19c4377d32975098402bcdd3_14_1.png)

![图 1-3 输入邮箱页面](img/817f9d0d19c4377d32975098402bcdd3_15_0.png)

![图 1-4 输入详细信息](img/817f9d0d19c4377d32975098402bcdd3_15_1.png)

### 1.1.2 使用方式

注册成功后登录即可使用。登录入口同上，进入 ChatGPT 操作界面。

在“Send a message...”输入框中键入问题，点击发送按钮后等待模型返回结果。

图 1-6 示意了发送“您好”后的返回效果。

![图 1-5 ChatGPT 操作页面](img/817f9d0d19c4377d32975098402bcdd3_16_0.png)

![图 1-6 发送测试消息](img/817f9d0d19c4377d32975098402bcdd3_16_1.png)

## 1.2 与 ChatGPT 高效对话

ChatGPT 能大幅提升创作效率，但要发挥其价值需掌握正确的交互技巧。

### 1.2.1 提问技巧与注意事项

- (1) 问题要具体清晰。过于简略模糊的问题难以被准确理解，应尽量把问题的各要素写完整。
- (2) 避免主观偏见提问。模型基于海量数据训练，无法作主观判断或体现个人偏好，带明显偏见的问题易得不到可靠答案。
- (3) 提供上下文与前提。模型会结合全部相关信息作答，若缺少背景，前提不全，答案准确性会下降。
- (4) 使用精准关键词。具体的关键词或短语有助于模型快速抓住要点，过于笼统的表达会使焦点模糊。
- (5) 保持结构简洁。复杂结构信息量大、逻辑不清，易导致理解不全。可采用简短流畅的表述提升清晰度。
- (6) 人工复核与修正。模型输出仅供参考，需人工评估准确性与合理性，必要时追问或修正，这是发挥最大价值的关键。

总体而言，详尽清晰的输入配合人工监督修正，才能获得理想的交互效果。

### 1.2.2 提问示例

![提问技巧示例](img/817f9d0d19c4377d32975098402bcdd3_17_0.png)

例 1：太空探索最新进展是什么？
这类提问过于宽泛，模型只能给出泛泛的航天事件。

修正：最近 NASA 的火星探测器有哪些新发现？
限定了主体与时间，答案更相关。

例 2：你觉得哪个国家的人民最快乐？
属于主观判断，模型无法回答。

修正：根据最新世界幸福报告，排名靠前的国家有哪些？
转为客观事实查询，模型可准确作答。

例 3：人工智能会取代人类吗？
问题过大，难以在短答中论述清楚。

修正：你如何看待人工智能未来 10～20 年的发展？它可能在哪些领域超越人类智能？
限定时间与范围，讨论更聚焦。

例 4：气候变化的主要原因是什么？政府和公众现在采取了哪些应对举措？
两者逻辑关联不强，易遗漏信息。

修正：科学界确认的气候变化主要原因有哪些？各国政府目前在实施哪些具体的应对措施？
结构清晰，重点突出，回答更全面。

通过以上示例可体会到清晰、去主观、带上下文提问的重要性，这对获得高质量回复至关重要。

## 1.3 Python 解释器

运行 Python 程序需要先安装解释器，常见实现有：

- (1) CPython
官方实现，以 C 语言编写，可将源代码编译为字节码后再由虚拟机执行，相同文件未修改时直接执行字节码，提升运行速度。
- (2) PyPy
基于 Python 实现，速度常优于 CPython，但兼容性稍弱。
- (3) Jython
基于 Java 实现，可把 Python 代码编译为 Java 字节码，在 JVM 下运行。
- (4) IronPython
基于 .NET 平台实现，可使用 .NET 框架库。

本书采用兼容性更好的官方 CPython。主流系统多已预装，但版本可能偏旧。

可在 Python 官网下载对应平台的安装包，点击 Download Python 3.xx.x 即可获取。

![图 1-7 Python 官网下载页面](img/817f9d0d19c4377d32975098402bcdd3_19_0.png)

安装时勾选 Add python.exe to PATH 可将安装路径加入环境变量，便于任意目录下使用 python 命令。选择 Customize installation 可自定义，本例采用默认安装，点击 Install Now 直至完成。

![图 1-8 Python 安装对话框](img/817f9d0d19c4377d32975098402bcdd3_20_0.png)

## 1.4 PyCharm 开发工具

PyCharm 是 JetBrains 公司推出的 Python IDE，可用于编写多种语言。

### 1.4.1 下载与安装

官网提供 Professional 与 Community 两个版本。Professional 需付费，试用 30 天；Community 完全免费，对学习 Python 已足够。

![图 1-9 JetBrains 公司开发的工具](img/817f9d0d19c4377d32975098402bcdd3_21_0.png)

![图 1-10 下载 PyCharm](img/817f9d0d19c4377d32975098402bcdd3_21_1.png)

下载完成后安装过程简便，此处不再赘述。

### 1.4.2 工具配置

首次启动需进行基础设置。设置完成后进入欢迎界面，点击 Customize 打开偏好设置，再点 All settings 进入主设置。

在左侧选择 Python Interpreter，打开解释器配置，在 Interpreter 下拉列表中选择已安装的 Python 解释器，若无则手动添加。

![图 1-11 PyCharm 欢迎界面](img/817f9d0d19c4377d32975098402bcdd3_22_0.png)

![图 1-12 PyCharm 使用偏好设置对话框](img/817f9d0d19c4377d32975098402bcdd3_22_1.png)

![图 1-13 配置 Python 解释器](img/817f9d0d19c4377d32975098402bcdd3_23_0.png)

## 1.5 上手你的第一个 Python 程序

本节以经典的 Hello World 为引子，演示在 PyCharm 里如何新建项目、创建源码文件并成功运行。

### 1.5.1 创建项目

PyCharm 采用项目来统一管理 Python 源码，所以第一步是先搭一个项目，再在项目里建文件。

进入 PyCharm 欢迎页后，可点 New Project 按钮，也可走菜单 File→New Project 打开创建对话框，如图 1-14 所示。在 Location 输入框里填入项目名 HelloProj。若未配置解释器或想更换，可点图 1-15 中的三角展开解释器设置。对只有单一 Python 环境的用户，建议选用 Previously configured interpreter。

选项 Create a main.py welcome script 会自动生成 main.py，个人习惯不使用该文件，所以这里不勾选。最后点 Create 完成创建，效果如图 1-15 所示。

## 12 ChatGPT 极简实操：用 AI 自动生成 Python 代码

![](img/817f9d0d19c4377d32975098402bcdd3_24_0.png)

图 1-14 创建项目

![](img/817f9d0d19c4377d32975098402bcdd3_24_1.png)

图 1-15 项目创建完成

### 1.5.2 创建 Python 代码文件

项目创建后，需要在 HelloProj 目录下新建一个 Python 文件来做控制台输出。打开 HelloProj 文件夹，右键选 New→Python File 进入新建对话框，如图 1-16 所示。在 Name 输入框填入 hello，回车即可生成文件，项目窗口左侧即可看到 hello.py，如图 1-17 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_25_0.png)

图 1-16 新建 Python 文件对话框

![](img/817f9d0d19c4377d32975098402bcdd3_25_1.png)

图 1-17 hello.py 源代码文件

### 1.5.3 编写代码

Python 不需要像 Java 或 C 那样写 main 入口函数，解释器会从上到下直接执行文件内容。

代码如下：

```
string = "Hello, World."
print(string)
```

### 1.5.4 运行程序

代码写完就可以执行。首次运行时，在项目窗口选中 hello.py，右键选择 Run 'hello'，左下方的控制台会输出 "Hello, World."，如图 1-18 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_26_0.png)

图 1-18 运行结果

![](img/817f9d0d19c4377d32975098402bcdd3_26_1.png)

若已运行过一次，也可直接点工具栏的 Run ▶、或选择菜单 Run→Run 'hello'、或使用快捷键 Shift+F10 来重新执行上次的程序。

## 1.6 本章小结

本章覆盖了 ChatGPT 的使用要点、Python 解释器、PyCharm 工具以及首个 Python 程序的落地。

先学习了 ChatGPT 的注册与使用方法、提问技巧及局限性。ChatGPT 是功能强大的对话式 AI，能解答各类问题，但仍需合理把握边界。

接着介绍了 CPython、PyPy、Jython、IronPython 等常见解释器，合适的选择有助于提升运行效率。

随后下载安装并做了基础设置 PyCharm，熟悉了代码补全、调试、版本控制等核心功能，为后续开发打好基础。

最后完整走通了从环境搭建到编写运行 Hello World 的流程，为后续语法学习奠定基础。

经过本章，对 Python 开发环境有了初步认知，也体验了 ChatGPT 的实际能力，为进入 Python 核心语法做好铺垫。

# 第2章 让 ChatGPT 助力写出高质量代码

ChatGPT 能生成自然语言文本，也能输出程序代码。要借助它产出靠谱的代码，可参考以下几个方向：

- 1. 给出详尽准确的需求描述。模型只有充分理解功能目标和业务逻辑，才能生成可用代码，所以需求说明和示例必须具体。
- 2. 明确规范与最佳实践。说明中要指出遵循的语言标准、设计模式和工程化要求，产出质量会明显提升。
- 3. 采用迭代协作方式。一次对话很难一次到位，先让模型出初版，人工评审提修改，再让模型迭代优化，逐步收敛。
- 4. 人工验收与测试。模型生成后仍需人工审阅、运行测试和必要调优，人工把关是质量保障。
- 5. 持续优化交互。给模型的描述越清晰越好，同时要总结协作经验，不断改进提问方式，提升协作效率。
- 6. 融合其他工具。结合静态分析、单元测试、性能测试等工具进行客观评估，能更有效地筛查问题、提升产出质量。

总体而言，高效使用 ChatGPT 写代码需要保持紧密协作。清晰的需求、规范约束、迭代反馈、人工验收、持续改进和工具配合，合在一起才能最大化发挥模型价值，产出高质量代码。

## 2.1 编写代码

用好 ChatGPT，无论是片段还是复杂项目，都能获得有力支持。

### 2.1.1 描述代码需求

写出高质量代码的前提是把需求描述清楚。

在与 ChatGPT 协作时，有助提升效率与质量的做法如下：

- 1. 需求描述清晰明确。用自然语言详细说明要实现的功能或逻辑，模型才能给出更准确完整的框架。
- 2. 先从整体架构入手。先给出总体结构或函数原型，拿到框架后再逐步填充细节，比直接堆细节更容易被理解。
- 3. 用示例解释复杂逻辑。遇到算法或业务较复杂时，用具体例子说明，模型理解会更到位。
- 4. 多轮循环交互。复杂代码很少一次完成，需要持续补充更细的需求，模型会逐步完善；循环反馈也有助于纠正误解。
- 5. 请求解释或修复。若生成代码不合理或有错，可把代码贴回并请模型解释或给出修复建议，避免错误累积。
- 6. 人机结合。复杂逻辑下，人工先搭整体框架，模型填充细节；或人工筛选模型提供的备选方案再优化，优势互补效率更高。

这些技巧能让协作更顺畅。随着练习增多，提问会越来越精准，编程效率也会持续提升。与此同时，模型的语言理解和代码生成能力也在不断进步，为开发者带来更精准的支持。

![](img/817f9d0d19c4377d32975098402bcdd3_29_0.png)

下面给出若干示例，每个示例包含需求描述和模型返回的代码框架。

示例 1. 编写 Python 代码

发送请求：

我想要一段能够打印"Hello World"的 Python 代码

模型回复：

以下是一个简单的 Python 代码，可以打印"Hello World"：

```
def hello_world():
    print("Hello World!")

if __name__ == '__main__':
    hello_world()
```

示例 2. 编写 C++代码

发送请求：

我要一段能够计算两个数之和的 C++ 函数。

执行后模型回答：

以下是一个简单的 C++函数,可以计算两个数之和：

```
int sum(int a, int b) {
    return a + b;
}

int main() {
    int result = sum(1, 2);
    return 0;
}
```

示例 3. 编写 JavaScript 代码

发送请求：

我需要一个下载网络图片并存储到本地的 JavaScript 函数。

执行后模型回答：

以下是一个简单的 JavaScript 函数,可以从 URL 下载网络图片并将其保存到本地文件系统：

```
function downloadImage(url, callback) {
    var request = new XMLHttpRequest();
    request.open('GET', url);
    request.responseType = 'blob';

    request.onload = function() {
        if (request.status === 200) {
            callback(request.response);
        }
    };
    request.send();
}

downloadImage('http://example.com/image.png', function(response) {
    var blob = new Blob([response]);
    var link = document.createElement('a');
    link.href = window.URL.createObjectURL(blob);
    link.download = 'image.png';
    link.click();
});
```

可见，ChatGPT 能根据简短的自然语言描述给出对应框架。随后可在此基础上继续补充更具体的逻辑与参数，模型会继续扩展完善，最终形成完整程序。

这体现了其强大的语言映射和代码生成能力。借助循环交互，不同难度和语言的代码都能较快实现。

当然能力也有边界，更复杂的算法仍需人工设计。AI 更像是一个编程助手，需要与人紧密配合才能发挥最大效能。

希望这些例子能帮助理解如何利用 ChatGPT 编写代码。

### 2.1.2 补全代码细节

在已有框架基础上，可继续向 ChatGPT 描述更细化的逻辑或参数，它会补充相应细节。

![](img/817f9d0d19c4377d32975098402bcdd3_31_0.png)

示例 4. 编写关联代码

在示例 1 的基础上想把输出改为“Hi there!”，可发送：

我想修改如下代码，打印“Hi there!”。

```
def hello_world():
    print("Hello World!")

if __name__ == '__main__':
    hello_world()
```

模型回答：

你可以修改 hello_world()函数内的字符串参数来打印“Hi there!”，如下所示：

```
def hello_world():
    print("Hi there!")

if __name__ == '__main__':
    hello_world()
```

示例 5. 编写关联代码

在示例 4 基础上增加姓名输入并输出“Hello+姓名!”。

发送请求：

我想修改如下代码，打印出“Hello+姓名！”。

```
def hello_world():
    print("Hi there!")

if __name__ == '__main__':
    hello_world()
```

模型的回答：

你可以修改 hello_world()函数并传入一个字符串参数来打印出"Hello+姓名！"的消息，如下所示：

```
def hello_world(name):
    print(f"Hello {name}!")

if __name__ == '__main__':
    name = "John" # 将此行替换为任何你想要的名字
    hello_world(name)
```

示例 6. 编写条件逻辑

发送请求：

我想修改如下代码，如果 name 是“John”或“Mary”，则打印“Hello dear!”，否则打印“Hello name!”。

```
def hello_world(name):
    print(f"Hello {name}!")

if __name__ == '__main__':
    name = "John"
    hello_world(name)
```

模型回答：

你可以在 hello_world() 函数中添加一些条件语句来检查 name 是否为"John"或"Mary"，从而打印不同的消息。以下是修改后的代码：

```
def hello_world(name):
    if name == "John" or name == "Mary":
        print("Hello dear!")
    else:
        print(f"Hello {name}!")
```

示例 7. 编写函数

发送请求：

我想修改如下代码，定义一个函数，参数为姓名，打印问候语，调用函数传入不同姓名。

```
def hello_world(name):
    print(f"Hello {name}!")

if __name__ == '__main__':
    name = "John"
    hello_world(name)
```

模型回答：

你可以使用 def 关键字来定义一个函数，以接受参数并打印出问候语。以下是你

可以尝试的代码：

```
def greet(name):
    print(f"Hello {name}!")

if __name__ == '__main__':
    greet("John")
    greet("Mary")
    greet("Bob")
```

从这些例子可以看出，ChatGPT 会依据具体描述提供相应的逻辑或细节来完善程序。逐步添加更丰富的逻辑与参数，最终即可得到功能完整的代码段。

![](img/817f9d0d19c4377d32975098402bcdd3_33_0.png)

需要注意的是，代码补全依赖多轮交互，持续描述实现细节，模型才会逐步把代码补全到最终需求。

### 2.1.3 代码语法检查

借助 ChatGPT 进行语法检查，能帮助开发者及时发现并修复错误，提升代码质量和稳定性，同时也有助于遵循编码规范，提高可读性和可维护性。

![](img/817f9d0d19c4377d32975098402bcdd3_33_1.png)

操作时可参考以下建议。

（1）先写完整再检查。初稿完成后统一检查，能最大程度避免重复修复同一问题。需要先控制急躁，将逻辑实现完整后再统一校验。

(2) 分模块检查。不要把整段程序一次性提交检查，问题定位会困难。可以按函数或类等逻辑单元分段提交，逐个检查、逐个修复。

(3) 先把重点部位筛一遍。函数声明、条件分支、循环结构这类容易手滑的区域最容易埋语法坑，稍有偏差程序就跑不起来，自己优先把这些关键片段过一遍。

(4) 提交给 ChatGPT 前，先把逻辑捋清楚。亲手再通读一遍代码，弄懂每一段在做什么、数据怎么流动，才能避免因自己疏忽把逻辑错误也一起交出去。现阶段 ChatGPT 对语法的敏感度较高，对业务逻辑的理解却不算深入。

(5) 按建议改完后一定要自己跑一遍。修改完别直接交付，先验证结果是否符合预期，自己做单元测试或手工试运行。完全依赖模型的检查是不安全的，其判断力远不及有经验的人工。

(6) 保持持续互动反馈。在对话过程中，把检查结论和建议的有效性告诉 ChatGPT，这能帮助它定位自身理解的盲区，逐步提升代码分析的稳定性和质量。

![](img/817f9d0d19c4377d32975098402bcdd3_34_0.png)

下面以代码语法核查为例，列几个提问的参考，帮助读者更快上手。

示例 8. 聚焦在一个具体的提问

想拿到可操作的回复，提问最好只锁定一个点。比如：

> 我在定义函数时常常忘记添加“:”，导致语法错误。如何记住并养成正确的习惯？

这个问法就很到位，明确了问题场景“冒号遗漏”，也说明了当前困扰和期望获得的习惯养成建议。

示例 9. 提供具体代码

如果这样问：

> 下面的代码有什么语法错误？如何修复？

```
def sum(a, b)
c = a + b
print(c)
```

把具体代码贴出来，模型能直接定位问题并给出修复思路。相对示例 8 来说，这个提问没有说明想解决的重点，模型在给出答案前会做一定猜测，容易偏离核心。

ChatGPT 的回答：

模型指出代码里漏写了冒号“:”，从而触发语法错误，并给出了修正后的写法。

## 2.2 调试代码

调试是修复缺陷、加深对程序运行机制理解的核心能力。开发者掌握一套调试方法，能大幅提升排错效率。

![](img/817f9d0d19c4377d32975098402bcdd3_35_0.png)

借助 ChatGPT 进行调试，常见的配合方式有：

- 1. 把代码逻辑和期望结果描述给 ChatGPT，让它帮你复核思路并揪出潜在风险。若模型理解偏差，也反向提示你描述不够清晰。
- 2. 在推理过程中提出疑问，请模型解释其判断依据。通过对比判断自己是否想偏，也能检验模型理解的准确性，形成“互相校验”的过程。
- 3. 把代码片段交给模型分析，再对照自己的理解是否一致。若模型出现偏差，也能倒逼自己把概念梳理清楚。
- 4. 遇到顽固 Bug 时，把现象描述给模型，请它提供排查思路和可能方向，能带来新的视角和灵感。若不认同模型的分析，也能促使你更深入地推敲。
- 5. 在模型的回复中寻找漏洞或不足，针对性追问。这既能检验模型的推理质量，也能锻炼自己的洞察力。

总之，掌握必要的调试技巧，加强分析和推理，是成为高效开发者的基础。错误不可怕，可怕的是不去理解。

代码既是生产工具，也是学习载体。调试的过程就是反复推敲、提升洞见的过程，唯有如此才能写出高质量的程序。

![](img/817f9d0d19c4377d32975098402bcdd3_35_1.png)

## 示例 10. 调试代码

下面用一个具体案例说明如何让 ChatGPT 参与调试。假设我们写了一段统计文本中词频前 10 的代码，如下：

```python
text = "Hello from ChatGPT! ChatGPT is an AI assistant created by Anthropic to be helpful, harmless, and honest."

words = text.lower().split()
word_counts = dict()

for word in words:
    if word in word_counts:
        word_counts[word] += 1
    else:
        word_counts[word] = 1

top_10 = sorted(word_counts.items(), key=lambda x: x[1], reverse=True)[:10]
print(top_10)
```

运行结果为：

```
[('hello', 1), ('from', 1), ('chatgpt!', 1), ('chatgpt', 1), ('is', 1), ('an', 1), ('ai', 1), ('assistant', 1), ('created', 1), ('by', 1)]
```

而我们期待结果是：

```
[('chatgpt', 2), ('hello', 1), ('from', 1), ('is', 1), ('an', 1), ('ai', 1), ('assistant', 1), ('created', 1), ('by', 1), ('anthropic', 1)]
```

明显不符合预期，可以这样和 ChatGPT 交互。

我们发出如下请求：

如下代码实际运行结果不符合我的期望,请指出哪里有逻辑错误：

```
[('hello', 1), ('from', 1), ('chatgpt!', 1), ('chatgpt', 1), ('is', 1), ('an', 1), ('ai', 1), ('assistant', 1), ('created', 1), ('by', 1)]

text = "Hello from ChatGPT! ChatGPT is an AI assistant created by Anthropic to be helpful, harmless, and honest."

words = text.lower().split()
word_counts = dict()

for word in words:
    if word in word_counts:
        word_counts[word] += 1
    else:
        word_counts[word] = 1

top_10 = sorted(word_counts.items(), key=lambda x: x[1], reverse=True)[:10]
print(top_10)
```

ChatGPT 的回答：

模型认为代码没有统一大小写，统计时把大小写不同的相同词当成了不同词。建议在计数前先把单词统一转成小写。

注意，如果模型的答案和你的预期不一致也很正常，可以继续追问：

但是为何在结果中会出现“!”？

ChatGPT 的回答：

模型解释说，使用 split() 分割时没有处理标点，字符串 "ChatGPT!" 会被完整保留为一个词，导致脏数据。建议用正则表达式来切分词。

为了避免这种情况，可以用正则表达式 \b\w+\b 来匹配单词，其中 \b 表示词边界，\w+ 表示字母数字下划线序列。这样就能把文本干净地拆成词列表，输出里不再混入标点。

修改后的代码如下：

```python
import re

text = "Hello from ChatGPT! ChatGPT is an AI assistant created by Anthropic to be helpful, harmless, and honest."

words = re.findall(r'\b\w+\b', text.lower())
word_counts = dict()

for word in words:
    if word in word_counts:
        word_counts[word] += 1
    else:
        word_counts[word] = 1

top_10 = sorted(word_counts.items(), key = lambda x: x[1], reverse = True)[:10]
print(top_10)
```

应用正则后重新运行，结果如下：

```
[('chatgpt', 2), ('hello', 1), ('from', 1), ('is', 1), ('an', 1), ('ai', 1), ('assistant', 1), ('created', 1), ('by', 1), ('anthropic', 1)]
```

此时输出已符合预期。

## 2.3 评审 ChatGPT 生成的代码

ChatGPT 能够生成自然语言文本，也包括程序代码。使用时最好做一次代码评审，以保证质量和可信度。

评审的核心是对代码进行系统化审查，确保其符合编码规范、最佳实践和安全要求。由模型生成的代码可能存在语法瑕疵、逻辑偏差或安全隐患，必须通过评审来发现并修正。

评审者需要具备扎实的开发经验，能够识别常见错误和潜在风险，并提出改进意见。同时还要关注可读性、可维护性和执行效率，确保代码长期可靠。

评审可以借助工具实现，常用手段有静态代码分析、逻辑验证如单元测试、性能测试等。

代码静态检查工具能够自动扫描源码，判断是否符合既定规范并找出潜在问题。主流工具包括：

- (1) Checkstyle：主要用于 Java，检查函数长度、命名规范、空格使用等，可按配置规则核查模型生成的 Java 代码是否达标。
- (2) PMD：Java 静态分析工具，能发现未使用变量、未捕获异常等不规范写法，可评估生成的 Java 代码的规范性和健壮性。
- (3) FindBugs：扫描 Java 字节码中的缺陷和不规范情况，涵盖空指针检查、同步问题等，可揪出模型代码里的隐藏风险。
- (4) Cppcheck：面向 C/C++ 的静态检查工具，能发现未初始化变量、内存泄漏等问题，可用于评估模型生成的 C/C++ 代码质量。
- (5) PyLint：基于 PEP 8 规范检查 Python 代码，自动发现风格和潜在问题，可快速评估模型生成的 Python 代码是否合规。

使用这些工具可以客观量化模型代码在规范、健壮性和质量上的表现。把评估报告反馈给模型，要求其生成修订版，反复迭代即可显著提升生成质量。这要求开发者熟悉工具的配置与解读，并据此给出针对性建议。静态分析与模型配合能大幅减轻人工评审负担，尤其在处理复杂和高标准需求时价值明显。

总之，静态检查为评估模型生成能力提供了客观维度。理解并结合使用这些工具，能让 ChatGPT 成为更可靠的开发伙伴。

下面重点介绍 Python 的 PyLint 使用方法。

## 2.4 使用 Python 代码检查工具 PyLint

首先通过 pip 安装 PyLint，在命令行执行安装指令，如图 2-1 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_39_0.png)

若要在 PyCharm 中使用，需要安装 PyLint 插件。PyCharm 与 IntelliJ IDEA 操作类似，在插件市场搜索 PyLint 并安装，界面如图 2-2 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_39_1.png)

安装完成后重启 PyCharm 即可生效。

为验证功能，先准备一个质量较差的示例文件 BadCodeExample.py，代码如下：

```python
def do_something():
    name = "John" # 少了类型提示
    age = 30 # 变量命名不规范

    if age > 20:
        print(f"{name} is older than 20.")
```

在 PyCharm 中创建项目，将文件放入项目并打开，然后在编辑器右键选择 Check Current File 开始检查，检查结果会显示在输出窗口，点击条目即可定位到对应代码，便于逐条修改，如图 2-3 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_40_0.png)

## 2.5 人工评审 ChatGPT 生成的代码

通常情况下，ChatGPT 生成的代码已具备一定可用性。但在对精度、质量要求极高的场景，或需要深度定制时，人工评审仍不可替代。

人工评审能弥补模型在语言理解和业务洞察上的不足，真正实现人机结合的高质量输出。

人工评审需关注的维度包括：

- （1）业务逻辑：模型是否准确理解需求并正确实现？若有偏差需提出修改建议。
- （2）算法与效率：所用算法和数据结构是否最优？是否存在性能瓶颈？需分析并优化。
- （3）可读性：命名、注释、格式是否清晰？需提升代码的易读性。
- （4）规范性：是否符合语言和框架的编码规范？需核查并修正。
- （5）扩展性：架构设计是否便于后续扩展和维护？需评估并给出重构建议。
- （6）安全性：是否存在 SQL 注入、XSS 等风险？是否遵循安全开发流程？
- （7）漏洞与 Bug：仔细阅读和测试，查找模型未发现的缺陷并给出修复方案。

总之，人工评审是高质量软件开发中不可或缺的一环。

![](img/817f9d0d19c4377d32975098402bcdd3_41_0.png)

假设 ChatGPT 生成了一段 SQL 查询代码，如下：

```sql
SELECT * FROM users WHERE name = '$name' AND age > $age;
```

人工评审时可能发现以下问题并要求改进：

- （1）业务逻辑：如果 name 变量来自用户输入，当前写法存在 SQL 注入风险。应改用参数化查询：

```sql
SELECT * FROM users WHERE name = ? AND age > ?;
```

- （2）可读性：变量名 $name 与 $age 过于简略，建议改为语义更清晰的命名：

```sql
SELECT * FROM users WHERE user_name = ? AND user_age > ?;
```

- （3）规范性：SQL 关键字与表名建议大写，增强可读性：

```sql
SELECT * FROM USERS WHERE USER_NAME = ? AND USER_AGE > ?;
```

- (4) 算法与效率：若只需 name 与 age 两个字段，不应使用 SELECT *，应明确指定列：

```sql
SELECT USER_NAME, USER_AGE FROM USERS WHERE USER_NAME = ? AND USER_AGE > ?;
```

- (5) 漏洞与 Bug：如果 user_age 字段为字符串类型，> 比较会报错，需先确认字段类型，字符串比较应使用合适的格式。

通过这一次评审，SQL 注入被修复，可读性和规范性得到提升，查询效率也更合理，同时避免了潜在的类型错误。若模型能吸收这些反馈，接下来生成的 SQL 将更可靠。

## 2.6 本章总结

本章系统介绍了借助 ChatGPT 编写高质量程序代码的思路和方法，涵盖需求描述、细节补全、函数设计等编写环节，也介绍了 Python 语法检查工具和注释规范的维护手段。这些内容有助于读者提升代码可读性和可维护性，更好地把控高质量编程实践。

# 第 3 章 ChatGPT 赋能 Python 数据采集实战

办公自动化与数据采集相互依存，关键联动体现在以下几个层面：

- 1. 数据来源接入：自动化办公需要从数据库、接口、文件等多渠道调取原始信息，数据采集负责把分散的信息集中起来，为后续处理提供原料。
- 2. 实时更新保障：很多办公场景依赖最新数据作决策，采集环节可按固定频率或实时推送刷新数据，保证自动化流程始终基于新鲜信息运行。
- 3. 预处理清洗：采集不只是搬运，还包含缺失值补齐、异常值剔除、格式标准化等初步整理，为办公自动化提供干净可用数据。
- 4. 整合与转换：自动化常需融合多源数据并转为统一结构，采集过程要完成跨源合并与格式适配。
- 5. 决策支持与报表：办公自动化最终要产出分析结论和报表，采集整理好的数据是生成决策材料和可视化报告的基础。

总体来看，数据采集为办公自动化提供燃料与底座，能显著提升处理效率和决策质量。

## 3.1 ChatGPT 在采集流程中的定位

在数据采集环节，ChatGPT 主要承担以下辅助职能：

- 1. 结果解读与处理：用户可以把采集得到的结构化或半结构化结果交给 ChatGPT，让它抽取字段、筛选记录、转换格式或做初步解读。
- 2. 过程辅导与建议：作为智能助手，ChatGPT 能回应关于采集的最佳实践、实现思路和常见坑的问题，提供思路参考和操作建议。

需要明确的是，ChatGPT 本身不具备网络请求、页面抓取或数据读写能力。真正落地的自动化采集仍需结合 Python 生态的抓取库、API 调用库、数据库驱动等工具，通过代码完成获取、处理与存储，再让 ChatGPT 参与分析与决策支持。简言之，ChatGPT 是搭档而非执行者。

## 3.2 数据采集总览

数据采集即利用技术手段从各类来源获取信息的过程，是科研、市场调研、商业分析等领域的核心环节。随着互联网和大数据普及，其重要性持续攀升。

### 3.2.1 价值与难点

采集的价值在于提供洞察，支撑判断、建模和创新。通过汇聚多类型数据可发现规律、趋势和关联，进而优化模型与预测，提升效率与准确度。

同时面临明显挑战：

- (1) 来源多样：网页、数据库、传感器等格式协议各异，整合难度高。
- (2) 体量庞大：数据产生加速，采集与计算需高效算法与架构支持。
- (3) 质量把控：噪声、缺失、错误普遍存在，需清洗验证才能可用。

### 3.2.2 典型工作流

- (1) 确定目标：明确数据类型、范围与业务目的，锁定重点。
- (2) 选定来源：确认网页、API、数据库等具体入口，了解其结构与访问方式。
- (3) 设计方案：依据来源特性选择工具、编写采集脚本、设定参数。
- (4) 执行采集：采用爬虫、接口调用、SQL 查询等手段获取数据。
- (5) 清洗整合：处理缺失、去噪、统一格式，保证一致性。
- (6) 存储管理：使用数据库、数据湖、云存储等保存数据，便于后续分析。

### 3.2.3 常用技术与工具

- (1) 网络爬虫：自动从网页提取信息。
- (2) API 调用：通过接口与远程服务交互获取数据。
- (3) 数据库查询：用 SQL 从关系型数据库检索数据。
- (4) 传感器与设备：采集温度、湿度、位置等实时物理数据。
- (5) 数据处理库：Python 中的 Pandas、NumPy、BeautifulSoup 等用于清洗与分析。

## 3.3 网页数据抓取

从网页提取数据是采集的常见场景，可借助 Python 及相关库实现自动化。

### 3.3.1 静态与动态内容差异

嵌入在 HTML 中的信息属于静态数据，多见于同步请求返回的完整页面。借助 AJAX① 等异步技术，前端可仅请求少量 JSON/XML 结构化数据，再由 JavaScript 动态写入页面，这类为动态数据。

> ① AJAX(Asynchronous JavaScript and XML)可在不刷新页面的情况下异步获取数据，减少传输量并提升体验。

![](img/817f9d0d19c4377d32975098402bcdd3_46_0.png)

同步请求也可能出现“隐藏式动态”，即页面返回完整 HTML 与数据，但数据以隐藏字段或脚本变量形式存在，随后由脚本渲染到标签中。

图 3-1 所示搜狐证券页面，图 3-1(a) 为可直接读取的静态部分，图 3-1(b) 则展示了通过脚本动态注入的内容。

![](img/817f9d0d19c4377d32975098402bcdd3_46_1.png)

### 3.3.2 用 urllib 获取静态页

urllib 是 Python 标准库提供的 HTTP 请求基础模块，无需额外安装，包含多个子模块：

- 1. urllib.request：发送请求并获取响应，提供 urlopen()、urlretrieve() 等。
- 2. urllib.parse：解析、拼接 URL 与编码参数，如 urlparse()、urljoin()、urlencode()。
- 3. urllib.error：定义请求相关的异常，便于错误捕获。
- 4. urllib.robotparser：解析 robots.txt，判断爬取权限。

urllib 功能偏底层，面对异步、复杂参数等场景时，requests、aiohttp 等库更高效。

示例演示了使用 urllib.request 发起 GET 请求并读取响应的基本流程：

```python
import urllib.request

url = 'https://example.com'

# 发送 GET 请求并获取响应
response = urllib.request.urlopen(url)

# 读取响应内容
data = response.read()

# 关闭响应
response.close()

# 处理数据
# ...
```

### 3.3.3 示例1：抓取纳斯达克苹果股价历史页

以纳斯达克苹果公司历史股价页面为例，展示如何用 urllib 获取静态网页。实现代码如下：

```python
# 代码文件:chapter3/3.2.2.py
import os
import urllib.request

# url = 'https://www.nasdaq.com/symbol/aapl/historical#.UWdnJBDMhHk' ①
# 本地文件访问
url = "file:///" + os.path.abspath("./nasdaq-Apple1.html").replace("", "/") ②

req = urllib.request.Request(url)
with urllib.request.urlopen(req) as response:
    data = response.read()
    html_data = data.decode()
    print(html_data)
```

运行后输出：

```html
<!doctype html>
<html lang="en">
<head>
```

极简 ChatGPT：用 AI 驱动 Python 自动编程

![](img/817f9d0d19c4377d32975098402bcdd3_48_0.png)

图 3-2 纳斯达克苹果公司股票历史数据网页

```html
<meta charset = "UTF-8">
<meta name = "Generator" content = "EditPlus">
<meta name = "Author" content = "">
<meta name = "Keywords" content = "">
<meta name = "Description" content = "">
<title> Document </title>
</head>
<body>
<div id = "quotes_content_left_pnlAJAX">
    <table class = "historical-data__table">
        <thead class = "historical-data__table-headings">
            <tr class = "historical-data__row historical-data__row--headings">
                <th class = "historical-data__table-heading" scope = "col"> Date </th>
                <th class = "historical-data__table-heading" scope = "col"> Open </th>
                <th class = "historical-data__table-heading" scope = "col"> High </th>
                <th class = "historical-data__table-heading" scope = "col"> Low </th>
                <th class = "historical-data__table-heading" scope = "col"> Close/Last </th>
                <th class = "historical-data__table-heading" scope = "col"> Volume </th>
            </tr>
        </thead>
        <tbody class = "historical-data__table-body">
```

```html
<tr class = "historical-data__row">
    <th>10/04/2019</th>
    <td>225.64</td>
    <td>227.49</td>
    <td>223.89</td>
    <td>227.01</td>
    <td>34,755,550</td>
</tr>
<tr class = "historical-data__row">
    <th>10/03/2019</th>
    <td>218.43</td>
    <td>220.96</td>
    <td>215.132</td>
    <td>220.82</td>
    <td>30,352,690</td>
</tr>
...
</tbody>
</table>
</div>
</body>
</html>
```

代码说明：

- 代码第①行定义目标 URL。
- 代码第②行指定本地保存文件 nasdaq-Apple1.html。

采用本地文件主要是因为目标站点易改版，为学习方便提供离线样本，读者请按实际路径调整第②行。

![](img/817f9d0d19c4377d32975098402bcdd3_49_0.png)

## 3.4 数据解析

抓取到 HTML 后需抽取有效信息，常用正则、BeautifulSoup、XPath 等解析 HTML/XML，或用 JSON 库解析 JSON。实践中 BeautifulSoup 更易上手，也可让 ChatGPT 辅助生成解析逻辑。

### 3.4.1 BeautifulSoup 库入门

BeautifulSoup 是一款解析网页结构的利器，官网 https://www.crummy.com/software/BeautifulSoup/。

安装方式：

```bash
pip install beautifulsoup4
```

安装界面如图 3-3。

![](img/817f9d0d19c4377d32975098402bcdd3_50_0.png)

常用接口：

- find_all(tagname)：返回所有匹配标签的列表。
- find(tagname)：返回首个匹配元素。
- select(selector)：支持 CSS 选择器定位。
- get(key, default=None)：读取标签属性。

常用属性：

- title：获取页面标题。
- text：提取标签文本。

定位数据时可借助浏览器开发者工具，Chrome 通过“更多工具→开发者工具”，Firefox 通过“Web 开发者→切换工具箱”，快捷键均为 F12。

![](img/817f9d0d19c4377d32975098402bcdd3_51_0.png)

图 3-4 Chrome 浏览器 Web 开发工具箱

![](img/817f9d0d19c4377d32975098402bcdd3_51_1.png)

图 3-5 Firefox 浏览器 Web 开发工具箱

### 3.4.2 示例2：解析纳斯达克苹果股价数据

下面以纳斯达克苹果公司股价页面为例，演示如何用 BeautifulSoup 解析 HTML 中的结构化数据。

在动手写爬虫之前，先把纳斯达克股票页面的结构摸清楚。打开目标网页，按 F12 调出开发者工具，参考图 3-6。进入工具箱后点开“查看器”面板浏览源码，会发现想要的数据都嵌在 `<table>` 元素的 `<tbody>` 内部，每一条记录都单独放在一个 `<tr>` 里面。

![](img/817f9d0d19c4377d32975098402bcdd3_52_0.png)

下面是对应的实现示例：

```python
import urllib.request
from bs4 import BeautifulSoup
# url = 'https://www.nasdaq.com/symbol/aapl/historical#.UWdnJBDMhHk'
# 本地文件访问
url = "file:///" + os.path.abspath("./nasdaq-Apple1.html").replace("", "/")

req = urllib.request.Request(url)

with urllib.request.urlopen(req) as response:
    data = response.read()
    html_data = data.decode()
```

```python
sp = BeautifulSoup(html_data, 'html.parser')
```

```python
# 返回<tbody>标签元素
tbody = sp.find('tbody')
```

```python
# 返回<tbody>标签下所有的<tr>元素
trlist = tbody.select('tr')
```

```python
# 保存股票数据列表
data = []
```

```python
for tr in trlist:
    fields = {}                    # 保存一行数据
    # 获得交易日期<th>元素
    th = tr.find('th')
    fields['Date'] = th.text       # 日期
    # 获得tr下的所有td元素
    tds = tr.select('td')
    fields['Open'] = tds[0].text    # 开盘
    fields['High'] = tds[1].text    # 最高
    fields['Low'] = tds[2].text     # 最低
    fields['Close'] = tds[3].text   # 收盘
    fields['Volume'] = tds[4].text  # 成交量
    data.append(fields)
```

```python
print("解析完成。", data)
```

运行后得到的结果如下：

```python
解析完成。[{'Date': '10/04/2022', 'Open': '225.64', 'High': '227.49', 'Low': '223.89', 'Close': '227.01', 'Volume': '34,755,550'}, {'Date': '10/03/2022', 'Open': '218.43', 'High': '220.96', 'Low': '215.132', 'Close': '220.82', 'Volume': '30,352,690'}, {'Date': '10/02/2022', 'Open': '223.06', 'High': '223.58', 'Low': '217.93', 'Close': '218.96', 'Volume': '35,767,260'}, {'Date': '10/01/2022', 'Open': '225.07', 'High': '228.22', 'Low': '224.2', 'Close': '224.59', 'Volume': '36,187,160'}, {'Date': '09/30/2022', 'Open': '220.9', 'High': '224.58', 'Low': '220.79', 'Close': '223.97', 'Volume': '26,318,580'}, {'Date': '09/27/2022', 'Open': '220.54', 'High': '220.96', 'Low': '217.2814', 'Close': '218.82', 'Volume': '25,361,290'}, {'Date': '09/26/2022', 'Open': '220', 'High': '220.94', 'Low': '218.83', 'Close': '219.89', 'Volume': '19,088,310'}, {'Date': '09/25/2022', 'Open': '218.55', 'High': '221.5', 'Low': '217.1402', 'Close': '221.03', 'Volume': '22,481,010'}, {'Date': '09/24/2022', 'Open': '221.03', 'High': '222.49', 'Low': '217.19', 'Close': '217.68', 'Volume': '31,434,370'}, {'Date': '09/23/2022', 'Open': '218.95', 'High': '219.84', 'Low': '217.65', 'Close': '218.72', 'Volume': '19,419,650'}, {'Date': '09/20/2022', 'Open': '221.38', 'High': '222.56', 'Low': '217.473', 'Close': '217.73', 'Volume': '57,977,090'}, {'Date': '09/19/2022', 'Open': '222.01', 'High': '223.76', 'Low': '220.37', 'Close': '220.96', 'Volume': '22,187,880'}, {'Date': '09/18/2022', 'Open': '221.06', 'High': '222.85', 'Low': '219.44', 'Close': '222.77', 'Volume': '25,643,090'}, {'Date': '09/17/2022', 'Open': '219.96', 'High': '220.82', 'Low': '219.12', 'Close': '220.7', 'Volume': '18,386,470'}, {'Date': '09/16/2022', 'Open': '217.73', 'High': '220.13', 'Low': '217.56', 'Close': '219.9', 'Volume': '21,158,140'}, {'Date': '09/13/2022', 'Open': '220', 'High': '220.79', 'Low': '217.02', 'Close': '218.75', 'Volume': '39,763,300'}, {'Date': '09/12/2022', 'Open': '224.8', 'High': '226.42', 'Low': '222.86', 'Close': '223.085', 'Volume': '32,226,670'}, {'Date': '09/11/2022', 'Open': '218.07', 'High': '223.71', 'Low': '217.73', 'Close': '223.59', 'Volume': '44,289,650'}]
```

代码要点说明如下：• 第①行用 BeautifulSoup 建立解析器对象 sp，把 HTML 文本作为输入，解析引擎选用 'html.parser'。

• 第②行调用 sp.find('tbody') 定位页面中第一个 `<tbody>` 标签，保存到 tbody，`<tbody>` 一般承载表格主体内容。

• 第③行用 tbody.select('tr') 提取该 `<tbody>` 下所有 `<tr>`，存入列表 trlist，每个 `<tr>` 对应表中的一行。

第④行对 trlist 中的每个 `<tr>` 进行循环处理：

• 用 tr.find('th') 取出当前行第一个 `<th>`，存入 th，`<th>` 多用于表头或日期等特殊字段。

• 用 tr.select('td') 抓取当前行全部 `<td>`，存入列表 tds，每个 `<td>` 就是一个单元格。

• 第⑤行把整理好的每行字典 fields 追加到列表 data 中。

最后把解析好的股票数据 data 输出。

### 3.4.3 用 Selenium 获取动态网页数据
用 urllib 去拉数据时很容易被服务器的反爬机制拦住。服务端能判断请求是否来自真实浏览器。有些内容还要先登录才能看到，比如邮箱信息，登录时常会弹出验证码，验证码的作用就是区分人机。强行破解验证码并不推荐，如今的验证码早已不止图片，还会出现语音等多种形式。

如果由真正的浏览器来访问，这些“防线”就不再是问题。Selenium 可以在本地启动浏览器，并用程序来操控它。它直接驱动浏览器，所以能拿到各种动态渲染的数据。操作过程中还可以人工介入，例如登录时需要验证码，就让人工输入，登录成功后再交给 Selenium 继续抓取。

#### 1. 安装 Selenium
想用 Selenium 得先装上，执行下面这条 pip 命令即可安装：

```
pip install selenium
```

安装时的界面见图 3-7。

#### 2. 配置 Selenium
Selenium 运行需要依托本地浏览器，默认选用 Firefox，建议版本在 55.0 以上。为保证兼容，还要下载对应的浏览器引擎 GeckoDriver，可在 https://github.com/mozilla/geckodriver/releases 获取，按系统选择版本，解压后即可使用，无需安装。

随后要把 Firefox 的安装目录和 GeckoDriver 的解压目录加入系统 PATH。以 Windows 10 为例的添加方式如图 3-8 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_55_0.png)

图 3-7 Selenium 安装过程

![](img/817f9d0d19c4377d32975098402bcdd3_55_1.png)

图 3-8 添加 PATH

#### 3. Selenium 常用 API
浏览器操作主要靠 WebDriver 对象完成，它提供了控制浏览器和读取页面元素的函数。

控制浏览器的函数有：

- refresh()：刷新当前页面。
- back()：返回上一页。
- forward()：前进到下一页。
- close()：关闭当前窗口。
- quit()：结束浏览器进程。
- get(url)：打开指定 URL 的页面。

读取页面元素的函数有：

- find_element(By. ID, id)：按 id 查找第一个匹配元素。
- find_elements(By. ID, id)：按 id 查找所有匹配元素。
- find_element(By. NAME, name)：按 name 查找第一个匹配元素。
- find_elements(By. NAME, name)：按 name 查找所有匹配元素。
- find_element(By. LINK_TEXT, link_text)：按链接文本查找第一个匹配元素。
- find_elements(By. LINK_TEXT, link_text)：按链接文本查找所有匹配元素。
- find_element(By. TAG_NAME, name)：按标签名查找第一个匹配元素。
- find_elements(By. TAG_NAME, name)：按标签名查找所有匹配元素。
- find_element(By. XPATH, xpath)：按 XPath 查找第一个匹配元素。
- find_elements(By. XPATH, xpath)：按 XPath 查找所有匹配元素。
- find_element(By. CLASS_NAME, name)：按 class 属性查找第一个匹配元素。
- find_elements(By. CLASS_NAME, name)：按 class 属性查找所有匹配元素。
- find_element(By. CSS_SELECTOR, css_selector)：按 CSS 选择器查找第一个匹配元素。
- find_elements(By. CSS_SELECTOR, css_selector)：按 CSS 选择器查找所有匹配元素。

### 3.4.4 示例 3：抓取搜狐证券贵州茅台行情
下面通过搜狐证券贵州茅台的股票数据来演示 Selenium 的使用。直接用 urllib 基本拿不到内容，因为这些数据是动态加载的，而 Selenium 就能轻松返回。

在写代码前，先分析一下该页面的 HTML。主流浏览器都自带 Web 工具箱，打开图 3-9 所示的界面，在查看器里定位到表格对应的 HTML 标签，选中标签时页面会高亮显示。经过排查，找到对应的 table 标签，记下它的 id 或 class 属性，方便代码中定位。

![](img/817f9d0d19c4377d32975098402bcdd3_57_0.png)

案例实现代码如下：

```
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Firefox()
driver.get('http://q.stock.sohu.com/cn/600519/lshq.shtml')
table_element = driver.find_element(By.ID, 'BIZ_hq_historySearch')
print(table_element.text)
driver.quit()
```

代码说明如下：

- 第①行引入 Selenium 的 webdriver 模块，该模块提供控制不同浏览器的驱动。
- 第②行实例化 Firefox 的 WebDriver，赋给变量 driver，同时会弹出一个 Firefox 窗口。
- 第③行让 WebDriver 加载目标地址 “http://q.stock.sohu.com/cn/600519/lshq.shtml”，即搜狐证券贵州茅台 600519 的历史行情页。
- 第④行调用 find_element() 并以 By.ID 指定，按 id='BIZ_hq_historySearch' 定位页面元素。
- 第⑤行打印该元素的可见文本，text 属性返回元素内容。
- 第⑥行执行 driver.quit()，关闭浏览器并结束会话。

### 3.4.5 示例 4：用 Selenium 解析 HTML 数据
Selenium 不只可以模拟人工点击，也能用一组 find_element() 方法来解析页面结构，用法和 BeautifulSoup 很像。本节以搜狐证券贵州茅台的页面为例，介绍如何用 Selenium 解析 HTML。

案例实现代码如下：

```
from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Firefox()
driver.get('http://q.stock.sohu.com/cn/600519/lshq.shtml')
table_element = driver.find_element(By.ID, 'BIZ_hq_historySearch') ①
tbody = table_element.find_element(By.TAG_NAME, "tbody") ②
trlist = tbody.find_elements(By.TAG_NAME, 'tr') ③
# 股票数据列表
data = []

for idx, tr in enumerate(trlist): ④
    if idx == 0:
        # 跳过 table 第一行
        continue ⑤

    td_list = tr.find_elements(By.TAG_NAME, "td") ⑥
    fields = {}
    fields['Date'] = td_list[0].text    # 日期
    fields['Open'] = td_list[1].text    # 开盘
    fields['Close'] = td_list[2].text   # 收盘
    fields['Low'] = td_list[5].text     # 最低
    fields['High'] = td_list[6].text    # 最高
    fields['Volume'] = td_list[7].text  # 成交量
    data.append(fields)

print(data)
driver.quit()
```

示例运行后，输出结果如下：

```
[{'Date': '2023-06-21', 'Open': '1740.00', 'Close': '1735.83', 'Low': '1735.00', 'High': '1756.60', 'Volume': '17721'}, {'Date': '2023-06-20', 'Open': '1740.00', 'Close': '1743.46', 'Low': '1735.00', 'High': '1765.00', 'Volume': '20947'}, {'Date': '2023-06-19', 'Open': '1790.00', 'Close': '1744.00', 'Low': '1738.00', 'High': '1797.95', 'Volume': '31700'}, {'Date': '2023-06-16', 'Open': '1757.00', 'Close': '1797.69', 'Low': '1750.10', 'High': '1800.00', 'Volume': '37918'}, {'Date': '2023-06-15', 'Open': '1730.34', 'Close': '1755.00', 'Low': '1723.00', 'High': '1755.65', 'Volume': '25223'}, {'Date': '2023-06-14', 'Open': '1719.00', 'Close': '1813.74', 'Low': '1783.30', 'High': '1822.01', 'Volume': '23952'}, {'Date': '2023-02-27', 'Open': '1778.50', 'Close': '1810.41', 'Low': '1775.02', 'High': '1815.00', 'Volume': '22065'}, {'Date': '2023-02-24', 'Open': '1810.11', 'Close': '1788.00', 'Low': '1782.18', 'High': '1810.19', 'Volume': '24635'}]
```

代码解析要点如下：

- 第①行借助 WebDriver 在页面里定位 id 为 'BIZ_hq_historySearch' 的节点并存入 table_element 变量，该节点一般就是承载历史行情的表格。

- 第②行在 table_element 内部查找 tbody 子节点并命名为 tbody，用来锁定表格主体部分，其中存放着行情数据行。

- 第③行从 tbody 中取出全部名为 tr 的子节点组成列表，相当于拿到每一行行情的容器。

- 第④行调用 enumerate 遍历 trlist，为每项分配索引 idx 和元素 tr，以便逐行迭代行情数据。

- 第⑤行当 idx 等于 0 时执行 continue，直接跳过第一行表头，不做数据处理。

- 第⑥行在当前 tr 中收集所有 td 子节点形成列表，用来读取该行各列的具体数值。

循环内部将每行各列的值打包成字典 fields，再 append 到 data 列表，data 最终汇总了所有历史行情的字典记录。最后输出 data，关闭浏览器并结束 WebDriver 会话。

### 3.4.6 示例5：借助 ChatGPT 完成 HTML 解析

编程基础相对薄弱时，也能借助 ChatGPT 解析 HTML，具体做法如下介绍。

![](img/817f9d0d19c4377d32975098402bcdd3_59_0.png)

读者向 ChatGPT 发送如下提问：

根据如下的 HTML 代码，帮我从其中 historical-data__table 表格中解析出数据，注意不要返回程序代码，你解析返回 CSV 表格数据。

```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="Generator" content="EditPlus®">
    <meta name="Author" content="">
    <meta name = "Keywords" content = "">
    <meta name = "Description" content = "">
    <title>Document</title>
</head>
<body>
<div id = "quotes_content_left_pnlAJAX">
    <table class = "historical - data__table">
        <thead class = "historical - data__table - headings">
            <tr class = "historical - data__row historical - data__row -- headings">
                <th class = "historical - data__table - heading" scope = "col">Date</th>
                <th class = "historical - data__table - heading" scope = "col">Open</th>
                <th class = "historical - data__table - heading" scope = "col">High</th>
                <th class = "historical - data__table - heading" scope = "col">Low</th>
                <th class = "historical - data__table - heading" scope = "col">Close</th>
                <th class = "historical - data__table - heading" scope = "col">Volume</th>
            </tr>
        </thead>
        <tbody class = "historical - data__table - body">
            <tr class = "historical - data__row">
                <th>10/04/2022</th>
                <td>225.64</td>
                <td>227.49</td>
                <td>223.89</td>
                <td>227.01</td>
                <td>34,755,550</td>
            </tr>
            ...
            <tr class = "historical - data__row">
                <th>09/11/2022</th>
                <td>218.07</td>
                <td>223.71</td>
                <td>217.73</td>
                <td>223.59</td>
                <td>44,289,650</td>
            </tr>
        </tbody>
    </table>
</div>
</body>
</html>
```

执行上述的提问 ChatGPT 回答如下。

以下是从给定的 HTML 中解析出的数据，并以 CSV 格式返回的结果：

```
Date,Open,High,Low,Close,Volume
10/04/2022,225.64,227.49,223.89,227.01,34,755,550
10/03/2022,218.43,220.96,215.132,220.82,30,352,690
10/02/2022,223.06,223.58,217.93,218.96,35,767,260
10/01/2022,225.07,228.22,224.2,224.59,36,187,160
09/30/2022,220.9,224.58,220.79,223.97,26,318,580
09/27/2022,220.54,220.96,217.2814,218.82,25,361,290
09/26/2022,220,220.94,218.83,219.89,19,088,310
09/25/2022,218.55,221.5,217.1402,221.03,22,481,010
```

09/24/2022,221.03,222.49,217.19,217.68,31,434,370

09/23/2022,218.95,219.84,217.65,218.72,19,419,650

09/20/2022,221.38,222.56,217.473,217.73,57,977,090

09/19/2022,222.01,223.76,220.37,220.96,22,187,880

09/18/2022,221.06,222.85,219.44,222.77,25,643,090

09/17/2022,219.96,220.82,219.12,220.7,18,386,470

09/16/2022,217.73,220.13,217.56,219.9,21,158,140

09/13/2022,220,220.79,217.02,218.75,39,763,300

09/12/2022,224.8,226.42,222.86,223.085,32,226,670

09/11/2022,218.07,223.71,217.73,223.59,44,289,650

这是从表格中提取的日期、开盘价、最高价、最低价、收盘价和成交量的数据。

![](img/817f9d0d19c4377d32975098402bcdd3_61_0.png)

CSV 即 Comma Separated Values，是一种用逗号分隔字段的文本交换格式，常见于电子表格与数据库之间的数据搬运。因为是纯文本文件，记事本等普通编辑器即可打开查看。

## 3.5 本章小结

本章围绕 ChatGPT 在数据采集中的价值展开，并系统介绍了 Python 网页爬虫的采集思路。

先谈 ChatGPT 的辅助作用，它可以提供采集思路、解释业务场景，甚至直接给出采集代码，合理使用能显著提升效率。随后梳理了数据采集的基础概念，强调其重要性、流程、技术与工具，并指出采集是后续分析的前提。

在实操层面，先区分网页静态与动态数据，用 urllib 爬取纳斯达克苹果股票信息，并借助 BeautifulSoup 进行解析。针对动态页面，介绍 Selenium 的部署与使用，并以搜狐证券贵州茅台数据为例演示抓取与解析过程。最后以 ChatGPT 辅助解析 HTML 为例，体现其在采集环节的实用价值。

学完本章后，能够掌握 Python 爬虫采集的核心方法，为后续的数据分析项目奠定基础。

# 第 4 章 ChatGPT 助力 Python 数据清洗

在办公自动化编程里，数据清洗是关键环节，需要对原始数据做预处理与整理，确保信息的准确、统一与完整。

## 4.1 ChatGPT 在清洗环节能做什么

在数据清洗过程中，ChatGPT 主要发挥以下支持作用：

- 1. 给出清洗建议：办公人员可以描述清洗问题或需求，向 ChatGPT 咨询针对特定情况的处理思路。

- 2. 帮助理解清洗难题：清洗中常出现异常值判断、字段转换逻辑等复杂问题，可请 ChatGPT 解释原理，便于理解与处置。

- 3. 提供清洗流程指引：ChatGPT 能给出通用的清洗步骤与方法建议，帮助建立清洗规范并挑选合适的工具。

需要说明的是，ChatGPT 只能提供对话式建议和知识型回答，无法直接执行清洗操作。其价值在于辅导、解释与思路梳理，帮助人员更好地理解和解决清洗问题。

## 4.2 数据清洗与预处理

在办公自动化场景中，清洗与预处理对数据可用性至关重要。

### 4.2.1 数据质量评估

数据质量评估是清洗前的核心动作，用来发现数据中存在的错误与问题。常见评估手段包括：

- 1. 缺失值评估：缺失值指属性或数值未填的情况。可采用以下方式：

  - ① 统计各字段的缺失条数与占比。

  - ② 用热力图或条形图可视化缺失分布。

  - ③ 判断缺失是否呈现规律，例如是否与其它字段相关。

- 2. 异常值评估：异常值指明显偏离整体分布的数值。可采用以下方式：

  - ① 计算各字段的最小值、最大值、均值与标准差，筛查明显离群数值。

  - ② 绘制箱线图或直方图，观察是否存在离群点。

- 3. 重复值评估：重复值指数据集中出现多次的相同记录。可采用以下方式：

  - ① 统计重复观测的总体数量。

  - ② 按字段组合检查是否存在重复条目。

- 4. 数据一致性评估：一致性指数据中命名或逻辑上的矛盾。可采用以下方式：

  - ① 核查命名规范是否统一，如大小写、缩写等。

  - ② 核查逻辑关系是否合理，如日期先后、数值关系等。

### 4.2.2 ChatGPT 辅助数据质量评估

数据质量评估阶段，ChatGPT 可作为辅助工具提供支持：

- 1. 提供评估指南：描述数据集特征与目标后，ChatGPT 能给出常见问题清单与评估方法建议。

- 2. 解释评估技术：对不熟悉的评估算法或技术，可向 ChatGPT 请教原理与操作步骤，加深理解。

- 3. 探究问题根因：发现质量问题后，可与 ChatGPT 讨论可能成因与解决思路，获取分析视角与建议方案。

需要注意，ChatGPT 只能提供参考信息，不能替代人工专业判断，最终评估仍需结合业务经验做出决定。

### 4.2.3 示例 1：利用 ChatGPT 辅助评估学生信息表数据质量

下面通过具体案例说明如何借助 ChatGPT 评估数据质量。办公人员希望评估“学生信息表”数据，该表保存在“学生信息.xlsx”文件中，其内容如图 4-1 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_64_0.png)

图 4-1 学生信息.xlsx 文件内容

下面给出借助 ChatGPT 检验学生信息表质量的一套实操思路：

![](img/817f9d0d19c4377d32975098402bcdd3_64_1.png)

办公人员向 ChatGPT 发送如下提问：

我有如下学生信息表，你帮我评估数据的质量：

| 学号 | 姓名 | 生日 | 年龄 | 成绩 | 年级 |

|---|---|---|---|---|---|

| 100122014 | 张三 | 31/12/1999 | 21 | 85 | 一年级 |

| 100232015 | 李四 | 1-12-1999 | 200 | 60 | 三年级 |

| 100122012 | 王五 | 2019-02-06 | 24 | 100 | 3年级 |

| 100342013 | 小六 | 2019-02-07 | 23 | 300 | 1年级 |

| | 赵八 | 2019-02-09 | 23 | -10 | 7年级 |

| 100982015 | 周五 | 2020-01-20 | 27 | | 9年级 |

执行该提问后，ChatGPT 给出的反馈如下。

基于所给的学生信息表，可以从多维度审视数据质量，对各列的评述如下：

- 1. 学号：学号用于唯一标识学生，本表存在空缺的行，完整性受损。
- 2. 姓名：姓名同样是标识要素，当前有一行姓名为空，存在缺失。
- 3. 生日：日期应保持统一且合理，表中出现了“31/12/1999”、“1-12-1999”、“2019-02-06”三种写法，一致性与准确性存疑。
- 4. 年龄：年龄应为合理正整数且与生日对应，其中一行年龄为 200，明显异常。
- 5. 成绩：成绩需落在合理评分区间内，表中出现负值 -10，不符合预期。
- 6. 年级：年级描述应统一，表中出现“9 年级”与其他写法不一致，影响一致性。

整体看，数据在完整性、准确性、一致性、合理性和唯一性上均存在问题，如缺失值、日期格式混乱、年龄成绩异常、年级表述不统一等，需要清洗修复以保证后续使用可靠。

如果读者觉得描述不够具体，可继续追问。

办公人员向 ChatGPT 发送如下提问：

> 能具体指定行号和列号吗？

执行该提问后，ChatGPT 给出的反馈如下。

针对上述表格，按列给出可定位的行列信息如下：

- 1. 学号，列 A：第 5 行学号为空。
- 2. 姓名，列 B：第 4 行姓名为空。
- 3. 生日，列 C：第 2 行生日写法为“1-12-1999”，与其它行不一致；第 3 行生日写法为“2019-02-06”，同样不一致。
- 4. 年龄，列 D：第 2 行年龄为 200，超出合理范围。
- 5. 成绩，列 E：第 5 行成绩为负数 -10，超出合理范围。
- 6. 年级，列 F：第 6 行年级写成“9 年级”，表述与其他行不统一。

有了具体的行列定位，办公人员可以更快地排查和修正问题。后续仍需做清洗与修复，保证数据质量。

### 4.2.4 借助 ChatGPT 完成数据清洗

在实践中，利用 ChatGPT 辅助清洗数据可按以下流程推进：

- 1. 理解数据与明确问题：先向模型说明数据集的来源、格式及待解决的具体问题，帮助其把握上下文。
- 2. 初步检查与概览：提供样本或摘要，让模型了解结构与特征，协助识别完整性、缺失、异常值及类型问题。
- 3. 清洗方法探讨：就缺失值处理、异常值处置、重复记录剔除等提出咨询，模型可解释不同技术并给出适配建议。
- 4. 实施清洗：依据建议在合适的开发环境中执行，常用 Python 及 Pandas 等库完成具体操作。
- 5. 验证与评估：清洗后可请模型协助校验，并提供评估指标确认效果。

需要说明的是，ChatGPT 本身无法直接操作数据，只能提供理解、解释、建议和指导，实际执行仍需在编程环境中完成。

## 4.3 常用数据清洗工具

Python 生态提供了多款成熟的清洗工具：

- 1. Pandas：Python 数据处理与分析的核心库，支持缺失值处理、重复值剔除、类型转换、数据合并等。
- 2. NumPy：Python 数值计算库，提供高效数组运算，可为清洗提供数值计算与转换基础。

## 4.4 NumPy 库概览

NumPy 即 Numerical Python，是开源的科学计算与数据分析库。其底层由 C 实现，数组访问效率远高于 Python 内置结构，并支持高维数组与矩阵运算及丰富的数学函数。

安装 NumPy 可使用 pip：

```
pip install numpy
```

其他平台的安装方式大同小异，此处不再展开。

### 4.4.1 NumPy 的核心数组对象 ndarray

NumPy 最重要的结构是多维数组对象 ndarray，同一数组内元素类型一致且内存占用规律。

创建数组常用 array() 函数，参数接受 Python 列表或元组。

#### 1. 一维数组的创建

列表生成一维数组的示例代码如下：

```python
import numpy as np

a = np.array([1, 2, 3])
print("a = ", a)
b = np.array((1, 2, 3))
print("b = ", b)
```

示例运行后，输出结果如下：

```
a = [1 2 3]
b = [1 2 3]
```

#### 2. 二维数组的创建

array() 亦可创建任意维度的数组，下面以二维为例。

列表生成二维数组的示例代码如下：

```python
c = np.array([[1, 2, 3],
             [4, 5, 6],
             [7, 8, 9]])
print("c:")
print(c)
T1 = ([1.5, 2, 3], [4, 5, 6], [7, 8, 9])
d = np.array(T1)    # 嵌套元组创建数组
print("d:")
print("d = ", d)
```

示例运行后，输出结果如下：

```
c:
[[1 2 3]
 [4 5 6]
 [7 8 9]]
d:
d = [[1.5 2. 3. ]
 [4. 5. 6. ]
 [7. 8. 9. ]]
```

其中 T1 为元组嵌套列表，因首个元素 1.5 为浮点数，创建时整组元素会被统一转为浮点类型。

### 4.4.2 数组的轴

数组的维度数等于轴的数量，轴索引从 0 开始。示意如图 4-2、图 4-3，一维数组只有轴 0；二维数组有轴 0 与轴 1，其中 0 轴对应行，1 轴对应列。

![](img/817f9d0d19c4377d32975098402bcdd3_68_0.png)

### 4.4.3 一维数组元素的访问

掌握轴的概念后即可访问元素。NumPy 一维数组的下标与切片用法与 Python 列表/元组一致，下面不再赘述。

示例代码如下：

```python
import numpy as np

a = np.array([1, 2, 3, 4, 5, 6])
print("a[5] = ", a[5])          # 正向索引
print("a[-1] = ", a[-1])         # 逆向索引
print("a[1:3] = ", a[1:3])       # 切片访问
```

示例运行后，输出结果如下：

```
a[5] = 6
a[-1] = 6
a[1:3] = [2 3]
```

上述代码创建如图 4-4 的一维数组 a。正向索引与负向索引均可取到最后一个元素，切片操作返回的仍是一维数组。

![](img/817f9d0d19c4377d32975098402bcdd3_68_1.png)

### 4.4.4 二维数组元素的访问

二维访问在两个轴上分别操作，可用下标或切片。

#### 1. 下标索引访问

语法示意：

```
a[所在0轴索引, 所在1轴索引]
```

a 为二维数组，返回指定 0 轴与 1 轴位置的元素。

示例代码如下：

```python
import numpy as np

a = np.array([[1, 2, 3],
             [1, 2, 3],
             [4, 5, 6],
             [7, 8, 9]])

print("a:")
print(a)
print("a[2,1] = ", a[2, 1])
print("a[-1, -2] = ", a[-1, -2])
```

![](img/817f9d0d19c4377d32975098402bcdd3_69_0.png)

代码创建如图 4-5 的二维数组 a。a[2,1] 正向索引取出第 3 行第 2 列的值 8；负向索引 a[-1,-2] 指向同一位置。

#### 2. 切片访问

语法示意：

```
a[所在0轴切片, 所在1轴切片]
```

返回按 0 轴与 1 轴切片后的结果，切片不会降低维度，二维切片后仍为二维。

示例代码如下：

```python
a[1:3, 1:3]
```

![](img/817f9d0d19c4377d32975098402bcdd3_69_1.png)

a 为图 4-5 的数组。a[1:3,1:3] 取第 2 到第 3 行、第 2 到第 3 列的区域，对应图中灰色部分。a[1:,1:] 与之效果相同，均返回从第 2 行第 2 列开始的子数组。

## 4.5 数据分析必备库——Pandas

Pandas 是开源的 Python 数据分析库，广泛用于金融、经济、统计、广告与网络分析等领域。可完成加载、准备、操作、建模与分析五大步骤。

其数据结构建立在 NumPy 数组之上，底层 C 实现保证速度快，提供高效的 Series 与 DataFrame。

安装 Pandas 可使用 pip：

```
pip install pandas
```

其它平台安装流程类似，此处省略。

### 4.5.1 Series 数据结构

Series 是一个带标签的一维数组，可容纳任意类型数据。由数据数组与索引标签两部分组成，数据部分即 NumPy ndarray。

![](img/817f9d0d19c4377d32975098402bcdd3_70_0.png)

#### 1. 创建 Series 对象

构造方法语法：

```
pandas.Series(data, index, dtype, ...)
```

data 为数据，可为列表、数组、标量或字典；index 为标签，与数据等长，默认从 0 开始；dtype 指定类型，不填则自动推断。

列表创建示例：

```
import pandas as pd

apples = pd.Series([3, 2, 0, 1])
print("apples:")
print(apples)

b = pd.Series([3,2,0,1], index = ['a','b','c','d'])
print("b:")
print(b)
```

第①行使用默认整数索引创建；第②行指定标签 ['a','b','c','d'] 创建。

#### 2. 访问 Series 元素

可通过标签下标或切片访问。

```
print("b['c'] = ", b['c'])          # 通过标签下标访问 ①
print(b['a':'c'])                   # 通过标签切片访问 ②
```

示例运行后，输出结果如下：

```
b['c'] = 0
b['a':'c']:
a    3
b    2
c    0
dtype: int64
```

第①行按标签下标取值，返回 0；第②行按标签切片，返回一个 Series。

### 4.5.2 DataFrame 数据结构

DataFrame 由多个 Series 组成，是一个带标签的二维表格。列可为不同类型，行列标签均可变。

![](img/817f9d0d19c4377d32975098402bcdd3_71_0.png)

#### 1. 创建 DataFrame 对象

构造函数语法：

```
pandas.DataFrame( data, index, columns, dtype, ...)
```

data 为数据，可为列表、数组、字典、Series 或其它 DataFrame；index 为行标签，默认 0 起；columns 为列标签，默认 0 起；dtype 指定类型。

列表创建示例：

```
import pandas as pd

L = [[3, 0, 1], [2, 1, 2], [0, 2, 1], [1, 3, 0]]  # ①
df1 = pd.DataFrame(L)                               # ②

print("df1 :")
print(df1)
```

示例运行后，输出结果如下：

```
df1 :
    0  1  2
0   3  0  1
1   2  1  2
2   0  2  1
3   1  3  0
```

第①行定义嵌套列表 L，第②行以 L 创建 DataFrame，其余参数采用默认值。

指定列标签创建示例：

```
df2 = pd.DataFrame(L,columns = ['apples','oranges','bananas'])
print("df2 :")
print(df2)
```

示例运行后，输出结果如下：

```
df2 :
   apples  oranges  bananas
0       3        0        1
1       2        1        2
2       0        2        1
3       1        3        0
```

指定行标签与列标签创建示例：

```
df3 = pd.DataFrame(L,columns = ['apples','oranges','bananas'],
                   index = ['June','Robert','Lily','David'])
print("df3 :")
print(df3)
```

示例运行后，输出结果如下：

```
df3 :
         apples  oranges  bananas
June          3        0        1
Robert        2        1        2
Lily          0        2        1
David         1        3        0
```

#### 2. DataFrame 列的提取方式
DataFrame 的列可以通过单个标签或多个标签进行选取。单标签选取的示例代码如下：

```
print("df3['apples']:")          # 使用单个列标签访问
print(df3['apples'])             # 使用单个列标签访问
```

运行后得到的结果为：

```
df3['apples']:
June      3
Robert    2
Lily      0
David     1
Name: apples, dtype: int64
```

若要一次性取多列，也可以使用多个标签共同指定，示例代码如下：

```
print("df3[['apples', 'bananas']] :")
print(df3[['apples', 'bananas']])    # 使用多个列标签访问
```

执行后的输出如下：

```
df3[['apples', 'bananas']] :
        apples  bananas
June         3        1
Robert       2        2
Lily         0        1
David        1        0
```

### 4.5.3 Pandas 读取 Excel 的方法
pandas.read_excel 能够把 Excel 文档直接载入为 DataFrame。函数的基本语法如下：

```
pandas.read_excel(io, sheet_name=0, header=0, index_col=None, skiprows=None, skipfooter=0)
```

常用参数说明如下：

- io：指定 Excel 输入源，可为文件路径字符串、文件对象、ExcelFile 实例或网络 URL。
- sheet_name：指定工作表，可用名称、从零开始的整数序号，或包含多个工作表的列表。
- header：作为列名的行号，默认 0 表示第一行；设为 None 则不使用列名。
- index_col：作为行索引的列号，默认 None。
- skiprows：忽略开头若干行，默认 None。
- skipfooter：忽略末尾行数，默认 0。

![](img/817f9d0d19c4377d32975098402bcdd3_73_0.png)

依赖方面，Pandas 读取 Excel 需要 xlrd 与 openpyxl 两个库，记得先用 pip 安装。

### 4.5.4 案例2：读取全国十年人口 Excel
read_excel 参数较多，下面用实际文件来熟悉用法。此前已从国家统计局官网下载了“全国总人口 10 年数据.xls”文件（见图 4-9），下面演示如何读取。

![](img/817f9d0d19c4377d32975098402bcdd3_74_0.png)

代码示例：

```
import pandas as pd
f = r'data/全国总人口 10 年数据.xls'
df = pd.read_excel(f)

print(df)
```

运行后的结果：

```
df:
   数据库:年度数据  ...  Unnamed: 10
0  时间:最近 10 年  ...          NaN
1            指标  ...      2009 年
2  年末总人口(万人)  ...      133450
3  男性人口(万人)  ...       68647
4  女性人口(万人)  ...       64803
5  城镇人口(万人)  ...       64512
6  乡村人口(万人)  ...       68938
7  注:1981年及以前人口数据为户籍统计数;1982、1990、2000、2010年数据为当年... ... NaN
8  数据来源:国家统计局 ... NaN

[9 rows x 11 columns]
```

说明如下：

- 第①行用 df = pd.read_excel(f) 把数据载入 DataFrame。
从写法看未传额外参数，得到的 df 如图 4-10 所示。
打印结果显示大量空值且列名均为 Unnamed。原因在于真正需要的数据只是中间部分，需要跳过头部和尾部的冗余行，如图 4-10 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_75_0.png)

利用 skiprows 与 skipfooter 去掉多余行，示例代码如下：

```
# 参数忽略头部行数和尾部行数示例代码
df = pd.read_excel(f, skiprows = 2, skipfooter = 2)  # ①
print("df:")
print(df)
```

运行后输出：

```
df:
指标 2018年 2017年 2016年 ... 2012年 2011年 2010年 2009年
0 年末总人口(万人) 139538 139008 138271 ... 135404 134735 134091 133450
1 男性人口(万人) 71351 71137 70815 ... 69395 69068 68748 68647
2 女性人口(万人) 68187 67871 67456 ... 66009 65667 65343 64803
3 城镇人口(万人) 83137 81347 79298 ... 71182 69079 66978 64512
4 乡村人口(万人) 56401 57661 58973 ... 64222 65656 67113 68938

[5 rows x 11 columns]
```

说明：
第①行调用 read_excel 时传入了 skiprows 和 skipfooter，skiprows=2 表示跳过前两行，skipfooter=2 表示舍弃最后两行。

### 4.5.5 Pandas 读取 CSV 文件
CSV 属于广泛使用的文本格式，pandas.read_csv 可将其转为 DataFrame。

函数定义如下：

```
pandas.read_csv(filepath_or_buffer, sep=',', delimiter=None, header='infer', index_col=None, skiprows=None, skipfooter=0)
```

主要参数包括：

- filepath_or_buffer：CSV 输入，可为路径、文件对象或 URL。
- sep 或 delimiter：字段分隔符或正则表达式。
- header：列标签所在行号，默认 'infer' 自动推断。
- index_col：作为行索引的列号，默认 None。
- skiprows：跳过文件开头行数，默认 None。
- skipfooter：跳过文件末尾行数，默认 0。
- engine：解析引擎，c 或 python，默认 c，c 引擎不支持 skipfooter。

图 4-11 所示为“0600028 股票历史交易数据.csv”文件内容。

读取该文件的代码如下：

```
import pandas as pd
csvfile = r'data\0600028 股票历史交易数据.csv'
df = pd.read_csv(csvfile, sep=',', encoding='gbk', header=None)
print(df)
```

执行后输出如下：

| 0 | 1 | ... | 13 | 14 |

|---|---|---|---|---|

| 0 | 日期 | 股票代码 ... | 总市值 | 流通市值 |

| 1 | 2021-03-23 | '600028 ... | 5.15763353092e+11 | 4.07076104656e+11 |

| 2 | 2021-03-22 | '600028 ... | 5.19395489381e+11 | 4.09942837787e+11 |

| 3 | 2021-03-19 | '600028 ... | 5.15763353092e+11 | 4.07076104656e+11 |

| 4 | 2021-03-18 | '600028 ... | 5.37556170828e+11 | 4.24276503444e+11 |

| ... | ... | ... | ... | ... |

| 4744 | 2001-08-31 | '600028 ... | 2.7898858449e+11 | 6144600000.0 |

| 4745 | 2001-08-30 | '600028 ... | 2.8178546253e+11 | 6206200000.0 |

| 4746 | 2001-08-29 | '600028 ... | 2.7759014547e+11 | 6113800000.0 |

| 4747 | 2001-08-28 | '600028 ... | 2.79687804e+11 | 6160000000.0 |

| 4748 | 2001-08-27 | '600028 ... | 2.7689092596e+11 | 6098400000.0 |

![](img/817f9d0d19c4377d32975098402bcdd3_77_0.png)

图 4-11 0600028 股票历史交易数据.csv 文件

说明：

- 第①行通过 read_csv 读取文件并指定 encoding='gbk' 以匹配 GBK 编码，随后打印 DataFrame 内容。

## 4.6 Pandas 数据清洗实战
数据分析中清洗是常见环节，Pandas 提供丰富方法让清洗、转换更高效。下面列举几类常用操作：

```
import pandas as pd

# 从 CSV 文件导入数据
df = pd.read_csv('data.csv')
```

（1）缺失值处理：用 dropna() 删除含缺失值的行或列，或用 fillna() 进行填充。

```
# 删除包含缺失值的行
df = df.dropna()
# 填充缺失值
df = df.fillna(0) # 填充为 0
```

（2）重复值处理：用 duplicated() 检测重复，用 drop_duplicates() 删除重复。

```
# 检查重复值
duplicates = df.duplicated()
# 删除重复值
df = df.drop_duplicates()
```

（3）类型转换：用 astype() 把列转为目标类型，如把字符串转为数值。

```
# 将列转换为数值类型
df['column_name'] = df['column_name'].astype(float)
```

（4）排序操作：用 sort_values() 按指定列升序或降序排列。

```
# 按列排序
sorted_data = df.sort_values(by='column_name', ascending=False)
```

以上只是常用示例，Pandas 还支持重塑、合并、分组聚合等，根据实际需求选用合适函数即可。

### 4.6.1 案例3：学生信息表清洗
4.2.3 节曾用 ChatGPT 评估学生信息表质量，该表需要清洗，示例代码如下。

```
import pandas as pd

# 从 Excel 文件读取数据
df = pd.read_excel('data/学生信息.xlsx')

# 数据清洗
df['生日'] = pd.to_datetime(df['生日'], dayfirst=True, errors='coerce')
df.loc[df['成绩'] < 0, '成绩'] = None
df.loc[df['年龄'] > 200, '年龄'] = None
df['姓名'] = df['姓名'].replace('', None)

# 将清洗后的数据写入新的 Excel 文件
df.to_excel('data/学生信息(清洗后).xlsx', index=False)
```

运行后会在当前文件 data 目录下生成“学生信息(清洗后).xlsx”，打开后内容如图 4-12 所示。

### 4.6.2 案例4：缺失值填充
图 4-13 为全国人口数据，其中 2010 年和 2015 年存在缺失。

常见的填充策略有：

1. 用固定值填充；
2. 用均值填充；
3. 用中位数填充；
4. 用邻近值填充，即用上一行/下一行或 KNN 算法填充；
5. 用预测值填充，即借助机器学习模型预测。

本例采用邻近值填充，代码如下：

```python
import pandas as pd

# 从 Excel 文件读取数据，跳过头部 2 行和尾部 2 行
df = pd.read_excel('data/全国总人口 10 年数据 - 缺失值.xls', skiprows = 2, skipfooter = 2) ①
# 使用前向填充和后向填充方法填充缺失值
df = df.fillna(method = 'ffill', axis = 1)  # 使用前一年的数据填充缺失值，axis = 1 ②
# df = df.fillna(method = 'bfill', axis = 1)  # 使用后一年的数据填充缺失值
# 输出填充后的数据
print(df)
```

运行后输出：

| 指标 | 2018 年 | 2017 年 | 2016 年 | ... | 2012 年 | 2011 年 | 2010 年 | 2009 年 |

|---|---|---|---|---|---|---|---|---|

| 0 年末总人口(万人) | 139538 | 139008 | 138271 | ... | 135404 | 134735 | 134735 | 133450 |

| 1 男性人口(万人) | 71351 | 71137 | 70815 | ... | 69395 | 69068 | 68748.0 | 68647 |

| 2 女性人口(万人) | 68187 | 67871 | 67456 | ... | 66009 | 65667 | 65343.0 | 64803 |

| 3 城镇人口(万人) | 83137 | 81347 | 79298 | ... | 71182 | 69079 | 66978.0 | 64512 |

| 4 乡村人口(万人) | 56401 | 57661 | 58973 | ... | 64222 | 65656 | 67113.0 | 68938 |

[9 rows x 11 columns]

说明：

- 第①行用 pd.read_excel() 读取 Excel，skiprows=2 跳过前两行，skipfooter=2 跳过后两行。
- 第②行使用 fillna() 填充缺失，method='ffill' 表示用前一列非缺失值填充当前列，axis=1 表示按行操作。处理后打印结果。
若想用后一列填充，可取消注释并将 method 改为 'bfill'，axis 保持 1 不变。

### 4.6.3 案例5：剔除重复行
图 4-14 的全国人口数据中，“城镇人口(万人)”与“女性人口(万人)”存在重复行。

删除重复行的实现代码如下：

```python
import pandas as pd

# 从 Excel 文件读取数据
df = pd.read_excel('data/全国总人口 10 年数据 - 重复数据.xls')
# 删除重复行
df = df.drop_duplicates()
# 输出处理后的数据
print(df)
```

运行后输出如下：

```
数据库:年度数据 ... Unnamed: 10
0 时间:最近 10 年 ... NaN
1 指标 ... 2009 年
2 年末总人口(万人) ... 133450
3 城镇人口(万人) ... 64512
4 男性人口(万人) ... 68647
5 女性人口(万人) ... 64803
6 乡村人口(万人) ... 68938
7 注:1981 年及以前人口数据为户籍统计数;1982、1990、2000、2010 年数据为当年... ... NaN
8 数据来源:国家统计局 ... NaN
[9 rows x 11 columns]
```

从运行结果可以判断，重复的那部分数据已经被清理掉了。

## 4.7 本章小结

本节回顾了 ChatGPT 在数据清洗环节的价值，以及借助 Python 的 NumPy、Pandas 完成清洗的操作路径。

起初谈到 ChatGPT 的辅助作用，像数据质量的判断、清洗思路的提供以及清洗代码的直接生成，都能让清洗效率明显提升。

随后对数据清洗本身做了界定，包含质量评估与预处理两块。质量评估的核心是排查缺失值和重复记录，ChatGPT 能参与评估并给出优化建议。

接着进入 Python 实操。NumPy 负责高效的多维数组与矩阵运算；Pandas 则提供 Series、DataFrame 结构，方便读取和处理表格数据。

具体演示了 NumPy 数组的创建、索引与切片用法；用 Pandas 读取 Excel、CSV 文件，并在其上完成缺失值填充、重复行删除等清洗任务。

最后通过若干示例验证了 ChatGPT 在清洗过程中的辅助效果。

学完这一章，可以掌握用 Python 做数据清洗的基础能力，为后续分析建模打下基础。

# 第5章 办公自动化数据存储方案

在办公自动化场景里，数据存储是承上启下的关键环节。系统产生的数据需要被持久化保存，以支撑后续分析、报表生成和业务流程。

常见的数据存储形态主要有：

1. **数据库**：用数据库统一管理办公系统产生的数据很普遍。数据库提供结构化存储和查询能力，可按需选用关系型如 MySQL、Oracle，或非关系型如 MongoDB、Redis，实现高效存取、更新和检索。
2. **文件存储**：中小型系统常把数据落盘成文件。典型格式有文本 CSV、JSON、Excel、XML 等，适合数据量不大、需要方便导入导出的场景。
3. **云存储**：依托云计算，数据可存放在 Amazon S3、Google Cloud Storage、Microsoft Azure Blob Storage 等云服务中，兼顾远程访问、备份和共享。
4. **数据仓库**：面向海量数据整合的专用系统，通过抽取转换把多源数据汇聚，形成一致可靠、高性能的存储与查询环境，多用于企业级办公系统的数据分析和报表。

无论采用哪种方式，都要兼顾安全性、可靠性、可扩展性和性能，同时合理设计数据模型与结构，以匹配办公自动化系统的存储与操作需求。

本章重点讲解 MySQL 与 JSON 的存储实践。

## 5.1 MySQL 数据库实操

MySQL 是一款开源关系型数据库管理系统，广泛用于金融数据分析和应用开发，下面介绍其使用方法。

### 5.1.1 MySQL 数据库系统概述

MySQL 是目前流行的开源数据库产品，属于 Oracle 生态。官方提供多个版本，其中 MySQL Community Edition 社区版免费，适合中小企业使用，本书即基于该版本展开。

社区版安装包下载界面如图 5-1，可按平台挑选，支持 Windows、Linux、UNIX 等系统。

### 5.1.2 MySQL8 安装步骤

示例环境为 Windows10 64 位，下载的离线安装包为 mysql-installer-community-8.0.28.0.msi，双击即可启动安装。

安装流程如下：

1. **选择安装类型**
安装第一步为类型选择，对话框如图 5-2 所示。若仅为学习用途，建议选择 Server only，即只装 MySQL 服务器。点 Next 进入如图 5-3 的界面，再点 Execute 开始执行。

2. **配置安装**
安装完成后需做基础配置，主要有两点：
(1) 网络通信端口配置，见图 5-4，默认 3306，无冲突建议保留。
(2) 密码配置，见图 5-5，可为 root 用户设密码，也可新增普通用户。

3. **配置 Path 环境变量**
为方便命令行调用，建议把 MySQL 安装路径加入 Path。打开 Windows 环境变量设置，如图 5-6，双击 Path，弹出编辑对话框如图 5-7，在其中添加 MySQL 安装目录。

### 5.1.3 客户端连接服务器

MySQL 服务器装好后即可使用。连接方式可通过命令提示符或 GUI 工具，推荐命令行，操作如下：

```bash
mysql -h 主机 IP 地址(主机名) -u 用户 -p
```

其中 -h、-u、-p 的含义是：

1. -h：目标服务器主机名或 IP，可为远程主机，-h 后可不加空格。本机登录可省略。
2. -u：登录数据库的用户名，需在库中存在并有权限，-u 后可不加空格。
3. -p：对应用户的密码，可直接跟在 -p 后输入，也可在回车后再输入。

图 5-8 为用 mysql 命令登录本机的示例。

### 5.1.4 图形化客户端工具

不习惯命令行的用户可选用图形客户端。综合免费与跨平台考虑，推荐 MySQL Workbench，官方提供、功能完整。

1. **安装 MySQL Workbench**
在 MySQL 安装过程中勾选 MySQL Workbench 组件即可。启动安装器如图 5-9，点 Add 添加组件，进入如图 5-10 的界面，选中 MySQL Workbench 并用 ➔ 加入右侧列表，如图 5-11。

确认组件后点 Next 进入如图 5-12 的界面，点 Execute 开始安装。安装前需下载，如图 5-13，下载完点 Next 继续，完成后点 Finish，如图 5-14。

2. **配置数据库连接**
Workbench 要管理数据库，先需配置连接。启动后进入欢迎页，如图 5-15，点添加按钮 ⊕ 进入 Setup New Connection 对话框，如图 5-16。可为连接命名，并填写主机名、端口、用户名、密码。保存密码需点 Store in Vault，弹出对话框如图 5-17。设置完成后可用 Test Connection 测试，成功会弹出如图 5-18 的提示。返回欢迎页，可看到刚创建的连接，如图 5-19。

3. **管理数据库**
双击连接即可进入工作台，如图 5-20。左侧 SCHEMAS 为数据库列表，粗体为当前默认库。可右键选择 Set as Default Schema 切换默认库，如图 5-21。

注：快捷菜单中还有 Create Schema 创建库、Alter Schema 修改库、Drop Schema 删除库。

例如创建 school 数据库，选择 Create Schema，弹出如图 5-22 的对话框，填写库名并可选字符集，确认后点 Apply。随后弹出 Apply SQL Script to Database 对话框，如图 5-23，确认后点 Apply 创建。完成后界面如图 5-24，点 Finish 结束。

## 4. 执行 SQL 语句

若不使用图形向导，也可用 SQL 直接操作。打开查询窗口可通过 File→New Query Tab 或点快捷按钮 📄，如图 5-25。

在窗口中输入任意 SQL，如图 5-26。点 ▶ 执行，若选中语句则执行选中部分，未选中则执行窗口全部语句。该按钮也可执行到光标位置。

## 5. 执行 SQL 脚本

常把多条 SQL 写进文本文件执行。图 5-27 为 NASDAQ_DB.sql 的打开效果。执行该脚本主要完成：
1. 创建数据库 nasda；
2. 在 nasda 中创建 stocks 表；
3. 向 stocks 表写入数据；
4. 在 nasda 中创建 historicalquote 表；
5. 向 historicalquote 表写入数据。

在 Workbench 中通过 File→Open SQL Script 打开脚本，如图 5-28 所示，再点 ▶ 执行即可，过程不再赘述。

### 5.1.5 安装 PyMySQL 驱动

使用 pip 安装 PyMySQL：

```
pip install PyMySQL
```

Windows 命令提示符下安装过程如图 5-29，其他平台类似。

MySQL8 采用更安全的加密方式，还需安装 cryptography：

```
pip install cryptography
```

Windows 下安装过程如图 5-30，其他平台类似。

### 5.1.6 数据库访问通用流程

数据库操作分为查询与修改两类。

#### 1. 查询数据
查询流程如图 5-31，共六步：
1. 建立连接。首次访问需连接数据库，用 PyMySQL 的 connect(parameters...) 建立连接，成功返回 Connection 对象。
2. 创建游标。通过 Connection 的 cursor() 方法创建游标，用于暂存 SQL 结果。
3. 执行查询。通过游标 execute(sql) 执行 SQL，sql 为语句字符串。
4. 取结果。执行后可通过 fetchall() 取全部记录，或 fetchone() 取一条。
5. 关闭游标。使用完毕释放资源。
6. 关闭连接。操作结束关闭连接释放资源。

#### 2. 修改数据
修改使用 Insert、Update、Delete 等，流程如图 5-32。步骤与查询类似，但成功时需提交事务，失败时回滚。修改不会返回结果集。

（1）连接数据库代码：

```python
import pymysql
# 建立数据库连接
cnx = pymysql.connect(user = 'username', password = 'password',
                     host = 'host_address',
                     database = 'database_name')
```

其中 username、password、host_address、database_name 需替换为实际信息。

（2）创建游标对象：

```python
cursor = cnx.cursor()
```

（3）执行 SQL 查询：

```python
query = "SELECT * FROM table_name"
cursor.execute(query)

# 获取查询结果
result = cursor.fetchall()
```

table_name 需替换为实际表名。

（4）执行 SQL 插入/更新：

```python
insert_query = "INSERT INTO table_name (column1, column2) VALUES (%s, %s)"
data = ('value1', 'value2')
cursor.execute(insert_query, data)
# 提交事务
cnx.commit()
```

table_name、column1、column2 替换为实际列名，value1、value2 为实际值。

（5）关闭游标和连接：

```python
cursor.close()
cnx.close()
```

至此完成基本的读写操作。具体 SQL 需按业务调整，同时注意异常处理与类型转换。

### 5.1.7 案例：查询苹果股票数据

下面以案例演示 Python 访问 MySQL。

案例背景：
数据库设计如图 5-33，包含股票信息表 Stocks 与股票历史价格表 HistoricalQuote。

#### 1. 股票信息表
Stocks 表对应纳斯达克股票，Symbol 为主键，表结构如表 5-1。该项目不维护此表，数据在建表时预先导入。

表 5-1 股票信息表结构

| 字段名 | 数据类型 | 长度 | 精度 | 主键 | 外键 | 备注 |

数据库 × Pandas × JSON 数据流实战

先看基础表的基础字段定义。股票主表里，Symbol 用 varchar(10) 存放股票代号且为必填，Company 用 varchar(50) 存放公司名称，Industry 用 varchar(10) 存放所属行业，三者都不设外键关联。

#### 2. 股票历史价格表
股票历史价格表英文名 HistoricalQuote，按交易日记录单只股票的价格变动，交易日期 HDate 作为主键。表 5-2 股票历史价格表结构如下：

| 字段名 | 数据类型 | 长度 | 精度 | 主键 | 外键 | 备注 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| HDate | date | — | — | 是 | 否 | 交易日期 |
| Open | decimal(8,4) | 8 | 4 | 否 | 否 | 开盘价 |
| High | decimal(8,4) | 8 | 4 | 否 | 否 | 最高价 |
| Low | decimal(8,4) | 8 | 4 | 否 | 否 | 最低价 |
| Close | decimal(8,4) | 8 | 4 | 否 | 否 | 收盘价 |
| Volume | bigint | — | — | 否 | 否 | 成交量 |
| Symbol | varchar(10) | 10 | — | 否 | 是 | 股票代号 |

SQL 脚本的编写与执行过程在此不展开。按股票代码查询历史行情的 Python 示例如下：

```python
import pymysql

# 建立数据库连接
cnx = pymysql.connect(
    host = '127.0.0.1',
    user = 'root',
    password = '12345',
    database = 'nasdaq'
)

# 创建游标对象
cursor = cnx.cursor()
# 定义查询语句和参数
query = "SELECT * FROM historicalquote WHERE Symbol = %s" ①
params = ('AAPL',)

# 执行查询语句
cursor.execute(query, params) ②

# 获取查询结果
result = cursor.fetchall() ③

# 输出查询结果
for row in result: ④
    print(row)

# 关闭游标和数据库连接
cursor.close()
cnx.close()
```

上述代码执行后，输出结果如下。

```
(datetime.date(2018, 2, 1), Decimal('167.1650'), Decimal('168.6200'), Decimal('166.7600'), Decimal('167.7800'), 44453230, 'AAPL')
...
(datetime.date(2023, 1, 31), Decimal('166.8700'), Decimal('168.4417'), Decimal('166.5000'), Decimal('167.4300'), 32234520, 'AAPL')
```

对代码的要点做简要说明：

- 第①行定义了 SQL 语句，其中使用 %s 作为参数占位符。
- 第②行调用 execute() 执行语句，并把 params 中的值绑定到占位符上。
- 第③行通过 fetchall() 取回全部查询结果，返回一个列表。
- 第④行用 for 循环遍历列表并打印每一行。

执行完毕后会调用 close() 关闭游标和数据库连接，释放资源。需要注意的是，连接信息 host、user、password、database 只是示例，需按实际环境修改；表名、列名和参数值也应依据真实库结构调整。

这段逻辑的核心是：连库、执行查询、打印结果、关闭连接。以 Symbol='AAPL' 为例，即从 historicalquote 表中取出股票代号为 AAPL，也就是苹果公司的历史数据。

### 5.2 Pandas 实现 MySQL 的读写

Pandas 内置了与 MySQL 交互的常用接口：

1. pd.read_sql(query, con)：执行 SQL 并把结果转化为 DataFrame，query 为语句，con 为数据库连接对象。
2. df.to_sql(name, con, if_exists='fail', index=False)：把 DataFrame 写入 MySQL 表，name 为目标表名，if_exists 控制已存在表的行为，默认 fail 会报错，index=False 表示不写入索引列。
3. pd.read_sql_table(table_name, con)：直接读取整张表，返回 DataFrame。

建议用 SQLAlchemy 的可连接对象 engine/connection 来建立连接，而不是直接用 pymysql.connect()，前者兼容性更好。

SQLAlchemy 是 Python 的 SQL 工具和 ORM 库，提供统一的高层抽象，支持多种数据库后端。借助它可以：

1. 创建数据库连接：create_engine() 指定方言、主机、账号密码及库名生成连接对象。
2. 执行 SQL 查询：连接对象的 execute() 方法运行语句。
3. 获取结果：结果集对象配合 fetchall()、fetchone() 等取出数据。
4. 事务控制：begin()、commit()、rollback() 管理事务。
5. ORM 映射：将表映射为 Python 类，实现面向对象的操作。

所以使用 SQLAlchemy 可让数据读取、写入、查询更灵活，也更易扩展，被 Django、Flask 等框架广泛采用。

示例：用 Pandas 读取股票历史数据

下面演示使用 pd.read_sql_table(table_name, con) 从 historicalquote 表读取数据，代码如下：

```python
import pandas as pd
import pymysql
from sqlalchemy import create_engine

# 创建数据库连接
engine = create_engine('mysql+pymysql://root:12345@localhost/nasdaq')

# 从 MySQL 数据库中读取数据
df = pd.read_sql_table('historicalquote', engine)
print(df)
```

示例运行后，输出结果如下：

| HDate | Open | High | Low | Close | Volume | Symbol |
|---|---|---|---|---|---|---|
| 0 2018-02-01 | 167.165 | 168.6200 | 166.7600 | 167.78 | 44453230 | AAPL |
| 1 2018-02-02 | 166.000 | 166.8000 | 160.1000 | 160.50 | 85957050 | AAPL |

### 5.3 JSON 数据交换格式

JSON 是一种轻量级的数据交换格式，相较 XML 字符更少，传输更快、流量更省。

#### 5.3.1 JSON 文档结构

Web 和移动端都追求低流量高速度，JSON 因此成为主流交换格式。JSON 文档由对象和数组两种结构构成。对象类似 Python 的字典，由键值对组成；数组则是值的有序集合。

JSON 对象以 { 开始以 } 结束，名称与值之间用 : 连接，多组之间用 , 分隔，名称只能是字符串，值可以是任意合法 JSON 类型。其语法见图 5-34。

![](img/817f9d0d19c4377d32975098402bcdd3_108_0.png)

下面是一个 JSON 对象的例子：

```
{
    "name":"a.htm",
    "size":345,
    "saved":true
}
```

JSON 数组以 [ 开始以 ] 结束，元素之间用 , 分隔，语法见图 5-35。

![](img/817f9d0d19c4377d32975098402bcdd3_108_1.png)

下面是一个 JSON 数组的例子：

```
["text","html","css"]
```

数组元素可以是字符串、数字、对象、数组、true、false 或 null，且可嵌套。值的语法结构见图 5-36。

#### 5.3.2 JSON 数据编码

Python 数据要通过网络传输或落盘时，通常先转成 JSON，称作编码。

![](img/817f9d0d19c4377d32975098402bcdd3_109_0.png)

图 5-36 JSON 值的语法结构图

编码时的映射关系如表 5-3：

表 5-3 Python 数据与 JSON 数据映射关系

| Python | JSON | Python | JSON |
| :--- | :--- | :--- | :--- |
| 字典 | 对象 | True | true |
| 列表、元组 | 数组 | False | false |
| 字符串 | 字符串 | None | null |
| 整数、浮点等数字类型 | 数字 | | |

![](img/817f9d0d19c4377d32975098402bcdd3_109_1.png)

网络传输或存储时推荐使用紧凑的 JSON 对象，偶尔用数组。一般只有字典、列表、元组需要编码，字典对应对象，列表元组对应数组。

Python 内置模块 json 提供 dumps() 和 dump() 实现编码，dumps() 返回字符串，dump() 直接写入文件。

编码示例代码如下：

```
import json

# 准备数据
py_dict = {'name': 'tony', 'age': 30, 'sex': True}  # 创建字典对象
py_list = [1, 3]  # 创建列表对象
py_tuple = ('A', 'B', 'C')  # 创建元组对象

py_dict['a'] = py_list  # 添加列表到字典中
py_dict['b'] = py_tuple  # 添加元组到字典中

print(py_dict)
print(type(py_dict))                  # <class 'dict'>

# 编码过程
json_obj = json.dumps(py_dict)          ①
print(json_obj)
print(type(json_obj))                  # <class 'str'>

# 编码过程
json_obj = json.dumps(py_dict, indent = 4)  ②
# 漂亮的格式化字符串后输出
print(json_obj)

# 写入 JSON 数据到 data1.json 文件
with open('data/data1.json', 'w') as f:
    json.dump(py_dict, f)               ③

# 写入 JSON 数据到 data2.json 文件
with open('data/data2.json', 'w') as f:
    json.dump(py_dict, f, indent = 4)   ④
```

上述代码运行，输出结果如下：

```
{'name': 'tony', 'age': 30, 'sex': True, 'a': [1, 3], 'b': ('A', 'B', 'C')}
<class 'dict'>
{"name": "tony", "age": 30, "sex": true, "a": [1, 3], "b": ["A", "B", "C"]}
<class 'str'>
{
    "name": "tony",
    "age": 30,
    "sex": true,
    "a": [
        1,
        3
    ],
    "b": [
        "A",
        "B",
        "C"
    ]
}
```

解释如下：

- 第①行对 Python 字典 py_dict 编码，得到无空格换行的紧凑字符串，适合传输保存。
- 第②行同样编码，但使用 indent 参数格式化，indent=4 表示缩进四个空格，美观但不适合传输。
- 第③④行使用 dump() 将结果写入文件，与 dumps() 参数类似。

#### 5.3.3 JSON 数据解码

解码是编码的逆过程，把从网络或磁盘读到的 JSON 转回 Python 对象。

解码时的映射关系如表 5-4：

表 5-4 JSON 数据与 Python 数据映射关系

| JSON | Python | JSON | Python |
| :--- | :--- | :--- | :--- |
| 对象 | 字典 | 实数数字 | 浮点 |
| 数组 | 列表 | true | True |
| 字符串 | 字符串 | false | False |
| 整数数字 | 整数 | null | None |

json 模块提供 loads() 和 load() 进行解码，loads() 处理字符串，load() 读取文件或流。

解码示例代码如下：

```
import json

# 准备数据
json_obj = r'{"name": "tony", "age": 30, "sex": true, "a": [1, 3], "b": ["A", "B", "C"]}' ①

py_dict = json.loads(json_obj) ②
print(type(py_dict)) # <class 'dict'>
print(py_dict['name'])
print(py_dict['age'])
print(py_dict['sex'])

py_lista = py_dict['a'] # 取出列表对象
print(py_lista)
py_listb = py_dict['b'] # 取出列表对象
print(py_listb)

# 读取 JSON 数据到 data2.json 文件
with open('data/data2.json', 'r') as f:
    data = json.load(f) ③
    print(data)
    print(type(data)) # <class 'dict'>
```

解释如下：

- 第①行定义一个表示 JSON 对象的字符串。
- 第②行调用 loads() 解码，得到 Python 字典。
- 第③行从 data2.json 文件读取并解码，同样得到 Python 字典。

### 5.3.4 案例2：解析搜狐证券茅台历史行情接口

在 3.3.3 节我们已经演示过用 Selenium 从搜狐证券抓取贵州茅台的股价，事实上页面里展示的行情数据是来自同一个后端接口：

http://q.stock.sohu.com/hisHq?code = cn_600519&stat = 1&order = D&period = d&callback = historySearchHandler&rt = jsonp&0.8115656498417958

把这个链接直接放到浏览器打开，如图 5-37，返回的是一段看似乱码的字符串。仔细看会发现它并不是标准 JSON，而是被包裹在 historySearchHandler(...) 里面的。historySearchHandler 本质上是前端的 JavaScript 回调函数或变量，真正有价值的是它括号内部的那段 JSON 字符串。

![](img/817f9d0d19c4377d32975098402bcdd3_112_0.png)

笔者把接口返回的 JSON 字符串做了清洗后保存为“贵州茅台股票数据.json”，具体清洗步骤这里不再展开，最终文件内容见图 5-38。

对该文件的解码代码如下：

```
import json
import pandas as pd

data = []

with open('data/贵州茅台股票数据.json', 'r') as json_file:
    data_dict = json.load(json_file)
    hqlist = data_dict['hq']

    for item in hqlist:
        fields = {}
        fields['Date'] = item[0]          # 日期
        fields['Open'] = item[1]          # 开盘
        fields['Close'] = item[2]         # 收盘
        fields['Low'] = item[5]           # 最低
        fields['High'] = item[6]          # 最高
        fields['Volume'] = item[7]        # 成交量
        data.append(fields)

df = pd.DataFrame(data)
print(df)
```

运行后得到的结果如下：

| Date | Open | Close | Low | High | Volume |

|---|---|---|---|---|---|

| 2023-07-03 | 3209.16 | 3243.98 | 3209.16 | 3246.86 | 312371392 |

| 2023-06-30 | 3178.92 | 3202.06 | 3177.99 | 3212.99 | 265379888 |

| 2023-06-29 | 3185.42 | 3182.38 | 3179.53 | 3196.50 | 250340064 |

| 2023-06-28 | 3183.49 | 3189.38 | 3157.12 | 3192.66 | 276231936 |

| 2023-06-27 | 3153.31 | 3189.44 | 3148.27 | 3194.41 | 287604320 |

| ... | ... | ... | ... | ... | ... |

| 2023-03-10 | 3255.51 | 3230.08 | 3229.50 | 3262.15 | 281135424 |

| 2023-03-09 | 3285.94 | 3276.09 | 3260.00 | 3289.06 | 264021856 |

| 2023-03-08 | 3271.88 | 3283.25 | 3263.69 | 3283.25 | 260443552 |

| 2023-03-07 | 3320.21 | 3285.10 | 3284.41 | 3342.86 | 389957952 |

| 2023-03-06 | 3332.02 | 3322.03 | 3308.73 | 3333.06 | 340530432 |

[80 rows x 6 columns]

代码说明：

- ① 行用 with 打开‘贵州茅台股票数据.json’，赋值给 json_file，with 能保证处理完自动关闭文件。
- ② 行把文件内容读入并反序列化为 Python 字典，存到 data_dict。
- ③ 行从 data_dict 取出键‘hq’对应的值赋给 hqlist，默认 JSON 中‘hq’是一个列表，里面装着多条行情记录。
- ④ 行 for item in hqlist： 遍历列表，每条记录用 item 表示。
- ⑤ 行把 item 的第 0 个元素取出来作为 fields 字典的‘Date’，这里假设它是日期。
- ⑥ 行 fields['Open'] = item[1] ~ fields['Volume'] = item[7]：依次把 item 的第 1 到第 7 个元素映射到开盘、收盘、最低、最高、成交量等字段。
- ⑦ 行 df = pd.DataFrame(data)：把整理好的 data 列表转成 Pandas DataFrame，方便后续分析。

## 5.4 本章小结

本章围绕办公自动化里两种核心的数据存储方式展开：MySQL 关系型数据库与 JSON 轻量格式，并介绍了 Python 下的操作方法。

先是 MySQL 的基础：数据库特点、安装部署、命令行客户端与图形化工具的使用。接着装上 PyMySQL，通过示例演示了如何用 Python 连接 MySQL、读取苹果股价数据。再用 Pandas 实现对 MySQL 的读写，借助其内置的数据库接口把操作变得非常简洁。

随后讲解了 JSON 这种数据交换格式，它基于 JavaScript 语法来描述对象与数组。重点讲了 JSON 的编码与解码，并以搜狐证券茅台数据的解码为案例，展示如何把爬取到的原始串转成可用的 Python 字典。

总体来看，MySQL 与 JSON 在办公自动化里承担着数据的持久化与流通关键角色，熟练掌握二者的使用能显著提升获取和处理数据的效率。

# 第6章 办公自动化中的数据分析

在办公场景里，数据分析是支撑决策、发现问题、优化流程的重要手段。常见应用大致有：

- 1. **销售数据分析**：追踪销售走势、评估产品表现、洞察客户行为，进而找准瓶颈、制定策略、优化产品组合。
- 2. **市场调研与竞争分析**：基于市场与竞品数据把握需求、格局与定位，为营销、定价、新市场开拓提供依据。
- 3. **客户数据分析**：挖掘行为模式、偏好与忠诚度，实现精细化分群、个性化营销、提升满意度。
- 4. **财务数据分析**：了解财务状况、盈利能力与风险，辅助财务规划、预测与风险管控。
- 5. **人力资源数据分析**：分析离职率、绩效与培训需求，优化招聘、管理与员工发展。
- 6. **运营数据分析**：洞察生产效率、成本与供应链情况，推动计划优化、降本增效。

Python 在这些场景中非常好用，Pandas、NumPy、Matplotlib 等库处理数据，Scikit-learn、TensorFlow 等做建模。再配合 ChatGPT 实现自然语言交互，能让分析更智能、更快，并把结果解释成业务建议。

## 6.1 数据的统计分析方法

统计分析是对收集到的数据进行整理、汇总与剖析，用以把握特征与规律。它帮助我们认识分布、中心趋势、离散程度以及变量间的关联。常用方法包括：

- （1）描述统计分析：用平均值、中位数、众数、标准差、极差、百分位数等指标概括数据的集中与分散情况。
- （2）频数分析：统计各取值出现的次数，通过频数表或直方图直观看出数据集中与稀疏区间。
- （3）相关分析：计算皮尔逊相关系数等，判断两个变量的线性相关方向与强弱，为预测建模提供线索。
- （4）假设检验：设定原假设与备择假设，基于样本推断总体，判断差异是否显著。
- （5）方差分析：比较三组及以上均值差异是否显著，常用于实验与市场研究。
- （6）时间序列分析：研究连续时间观测数据的平稳性、趋势、季节性分解等规律。

## 6.2 描述统计分析

描述统计是对数据做概括性描述，用来揭示基本特征、分布形态与内在关系。它主要提供中心趋势、离散程度与分布信息。

常用手段有：

### 1. 中心趋势度量
- （1）平均值：所有数值的总和除以观测个数，反映整体水平。
- （2）中位数：排序后居中的数值，对偏态数据更稳健。
- （3）众数：出现频率最高的值，适合分类或有明显峰值的连续数据。

### 2. 离散程度度量
- (1) 标准差：衡量数值偏离均值的程度，越大越分散。
- (2) 方差：标准差的平方，同样刻画离散。
- (3) 范围：最大值减最小值，反映波动幅度。

### 3. 数据分布
- (1) 频数统计：每个取值出现的次数。
- (2) 百分位数：将数据排序后定位特定比例的位置。
- (3) 偏度：描述分布的左右不对称程度，正偏为右偏，负偏为左偏。
- (4) 峰度：描述分布尖峰或平坦的程度。

通过这些指标可以快速把握数据的轮廓，是后续深入分析的前提。

### 6.2.1 在 Pandas 中常用的描述统计方法

Pandas 提供了丰富的统计函数，可直接对 Series 或 DataFrame 计算摘要。典型方法有：

count()、mean()、median()、min()、max()、std()、var()、sum()

这些函数可直接作用于整列或整表，也可通过 axis 控制计算方向，axis=0 表示按列、axis=1 表示按行，默认按列。

示例代码如下：

```python
import pandas as pd
data = pd.DataFrame({'A': [1, 2, 3, 4, 5],
                   'B': [5, 6, 7, 8, 9]})
# 汇总统计
print(data.mean())                # 计算平均值
print(data.median())              # 计算中位数
print(data.max())                 # 计算最大值
print(data.min())                 # 计算最小值
print(data.std())                 # 计算标准差
print(data.sum())                 # 计算总和
```

运行输出如下：

```
A    3.0
B    7.0
dtype: float64
A    3.0
B    7.0
dtype: float64
A    5
B    9
dtype: int64
A    1
B    5
dtype: int64
A    1.581139
B    1.581139
dtype: float64
A    15
B    35
dtype: int64
```

上述结果是按行聚合的统计，若想按列聚合则把 axis 换成 1，代码如下：

```python
import pandas as pd
data = pd.DataFrame({'A': [1, 2, 3, 4, 5],
                    'B': [5, 6, 7, 8, 9]})
# 沿着列进行汇总统计
print(data.mean(axis=1))          # 计算平均值
print(data.median(axis=1))        # 计算中位数
print(data.max(axis=1))           # 计算最大值
print(data.min(axis=1))           # 计算最小值
print(data.std(axis=1))           # 计算标准差
print(data.sum(axis=1))           # 计算总和
```

输出如下：

```
0    3.0
1    4.0
2    5.0
3    6.0
4    7.0
dtype: float64
0    3.0
1    4.0
2    5.0
3    6.0
4    7.0
dtype: float64
0    5
1    6
2    7
3    8
4    9
dtype: int64
0    1
1    2
2    3
3    4
4    5
dtype: int64
0    2.828427
1    2.828427
2    2.828427
3    2.828427
4    2.828427
dtype: float64
0    6
1    8
2    10
3    12
4    14
dtype: int64
```

此外，describe() 方法能一次性输出计数、均值、标准差、最小值、25%分位数、中位数、75%分位数和最大值等汇总信息。

示例代码如下：

```python
import pandas as pd

data = pd.DataFrame({'A': [1, 2, 3, 4, 5],
                     'B': [2, 4, 6, 8, 10],
                     'C': [10, 20, 30, 40, 50]})

summary = data.describe()
# 打印统计摘要
print(summary)
```

运行输出如下：

| | A | B | C |

|---|---|---|---|

| count | 5.000000 | 5.000000 | 5.000000 |

| mean | 3.000000 | 6.000000 | 30.000000 |

| std | 1.581139 | 3.162278 | 15.811388 |

| min | 1.000000 | 2.000000 | 10.000000 |

| 25% | 2.000000 | 4.000000 | 20.000000 |

| 50% | 3.000000 | 6.000000 | 30.000000 |

| 75% | 4.000000 | 8.000000 | 40.000000 |

| max | 5.000000 | 10.000000 | 50.000000 |

### 6.2.2 案例1：使用描述统计方法分析贵州茅台股票数据

下面就用一个实操案例，示范描述统计在股票数据上的应用方式。

#### 1. 案例背景

第3章里曾从搜狐证券抓取了贵州茅台的历史行情，并存成了“贵州茅台股票历史交易数据.csv”，文件截图见图6-1。

![](img/817f9d0d19c4377d32975098402bcdd3_120_0.png)

本案例要做的，是借助描述统计对贵州茅台的行情进行解读。茅台作为国内知名白酒龙头，其股价在资本市场关注度很高。我们利用其历史交易记录做统计推断，以把握价格走势与波动规律。

#### 2. 数据准备

贵州茅台的历史行情已整理为CSV，文件名“贵州茅台股票历史交易数据.csv”。字段包含：

- (1) Date：日期。
- (2) Open：开盘价。
- (3) Close：收盘价。
- (4) High：最高价。
- (5) Low：最低价。
- (6) Volume：成交量。

读取与清洗将使用Pandas完成，再以描述统计手段衡量价格与成交量的特征。

#### 3. 分析目标

借助描述统计，我们期望得到：

- (1) 股价的集中趋势：均值、中位数等，以判断价格所处水平。
- (2) 股价的离散程度：标准差、方差等，以反映波动大小。
- (3) 成交量的分布形态：最大值、最小值、分位数等，以了解成交量的区间与集中情况。

上述指标能够帮助更清晰地认识茅台的历史交易特征，并给出基础的数量化结论。

具体代码如下：

```python
import pandas as pd

# 读取数据文件
data = pd.read_csv("data/贵州茅台股票历史交易数据.csv")

# 计算股票价格的中心趋势
price_mean = data["Close"].mean()                # 平均值
price_median = data["Close"].median()            # 中位数

# 计算股票价格的变异程度
price_std = data["Close"].std()                  # 标准差
price_var = data["Close"].var()                  # 方差

# 计算交易量的分布情况
volume_max = data["Volume"].max()                # 最大值
volume_min = data["Volume"].min()                # 最小值
volume_quantile = data["Volume"].quantile([0.25, 0.5, 0.75]) # 分位数

# 输出结果
print("股票价格的中心趋势:")
print("平均值:", price_mean)
print("中位数:", price_median)
print("\n股票价格的变异程度:")
print("标准差:", price_std)
print("方差:", price_var)
print("\n交易量的分布情况:")
print("最大值:", volume_max)
print("最小值:", volume_min)
print("分位数:")
print(volume_quantile)
```

执行后得到的输出为：

```
股票价格的中心趋势：
平均值：2023.6634782608696
中位数：2013.0

股票价格的变异程度：
标准差：52.84031360521276
方差：2792.098741897233

交易量的分布情况：
最大值：82266
最小值：27446
分位数：
0.25    34256.5
0.50    44916.0
0.75    59738.5
Name: Volume, dtype: float64
```

### 6.2.3 案例 2：ChatGPT 辅助分析跨境电商销售数据

接下来以另一个例子说明如何让ChatGPT参与数据解读并输出业务建议。跨境电商销售表如图6-2。

![](img/817f9d0d19c4377d32975098402bcdd3_122_0.png)

图 6-2 跨境电商销售数据

业务人员向ChatGPT提出的需求如下：

请帮我分析如下跨境电商销售数据：Region, Country, Item Type, Sales Channel, Order Priority, Order Date, Order ID, Ship Date, Units Sold, Unit Price, Unit Cost, Total Revenue, Total Cost, Total Profit

Middle East and North Africa, Libya, Cosmetics, Offline, M, 10/18/2014, 686800706, 10/31/2014, 8446, 437.20, 263.33, 3692591.20, 2224085.18, 1468506.02

North America, Canada, Vegetables, Online, M, 11/7/2011, 185941302, 12/8/2011, 3018, 154.06, 90.93, 464953.08, 274426.74, 190526.34

Middle East and North Africa, Libya, Baby Food, Offline, C, 10/31/2016, 246222341, 12/9/2016, 1517, 255.28, 159.42, 387259.76, 241840.14, 145419.62

Asia, Japan, Cereal, Offline, C, 4/10/2010, 161442649, 5/12/2010, 3322, 205.70, 117.11, 683335.40, 389039.42, 294295.98

...

ChatGPT 回复：

基于所给的跨境电商销售数据，我梳理出以下发现：

- (1) 销售区域分布：从Region字段看，交易主要集中在中东北非及北美。
- (2) 商品类型表现：Item Type显示化妆品、蔬菜、婴儿食品、谷物是主要品类。
- (3) 销售渠道构成：Sales Channel包含线下与线上两种形态。
- (4) 订单优先级：Order Priority主要出现M和C两类。

据此可以给出以下建议：

- (1) 市场拓展：可在中东北非、北美之外寻找增量市场，调研当地消费者偏好后进行差异化定位与推广。
- (2) 产品优化：在化妆品、蔬菜、婴儿食品、谷物等热销品上加大投入，结合需求调研做产品迭代与品牌塑造，提升竞争力。
- (3) 渠道优化：对比线下与线上渠道的效率与回报，针对各自特点制定营销打法，提升渠道产出。
- (4) 订单管理与优先级：分析不同优先级订单的履约时长与利润率，优化流程，优先处理高利润订单，提升供应链响应与客户满意度。

需要说明的是，以上结论仅基于提供的部分样本，仅供参考。更全面准确的判断需结合更完整的数据与业务背景。

## 6.3 本章总结

首先，本章介绍了描述统计的核心指标，包括集中趋势、离散程度与分布形态。

## 极简ChatGPT：如何利用AI实现Python自动编程

随后，重点讲解了Pandas中常用的描述统计函数，如mean()求均值、std()求标准差等，并用示例演示了使用方法。

接着，通过两个实战案例深化理解：

- (1) 用Pandas描述统计方法分析贵州茅台的开盘、最高、最低、收盘价及成交量等指标。
- (2) 利用ChatGPT对跨境电商销售数据做统计解读，涵盖总销售额、各国销售额占比及月度销售额变化趋势等。

通过以上练习，读者能够掌握Python数据统计分析的基础技能，为后续的预测建模打下基础。

# 第 7 章 办公自动化中的数据可视化

在办公自动化场景里，数据分析能显著提升决策效率、问题解决能力与业务优化精度。合理运用分析技术与工具，有助于更深入理解数据，从而提高工作效率与业务表现。

## 7.1 Python 数据可视化库

Python生态中有多个成熟的可视化库可供选用：

- 1. Matplotlib：经典的Python绘图库，提供丰富的绘图接口与函数，可绘制多种图表类型。
- 2. Seaborn：基于Matplotlib的统计可视化库，风格更美观，提供分布图、核密度图、箱线图等统计图表的便捷实现，追求简洁美观。
- 3. Plotly：交互式可视化库，支持动态缩放、导出等交互功能，适合构建金融图、地理可视化与时间序列分析等场景。

不同库各有侧重，可按需求与偏好选择。无论选哪种，可视化都是分析与沟通的关键环节，有助于理解数据并传递洞察。

## 7.2 使用 Matplotlib 绘制图表

Matplotlib是Python最常用的可视化库，也是其他图表库的基础，功能丰富且灵活，本章聚焦其使用。

### 7.2.1 安装 Matplotlib

可通过pip执行如下指令安装：

```
pip install matplotlib
```

安装界面如图7-1。

![](img/817f9d0d19c4377d32975098402bcdd3_126_0.png)

### 7.2.2 图表基本构成要素

如图7-2所示的折线图，包含标题、坐标轴及轴标题、可调节的刻度与刻度标签，以及通过不同颜色区分类别并配图例的机制。

![](img/817f9d0d19c4377d32975098402bcdd3_127_0.png)

### 7.2.3 绘制折线图

折线图由线段构成，结构简单。下面以折线图为例介绍Matplotlib用法。

示例代码如下：

```
import matplotlib.pyplot as plt
plt.rcParams['font.family'] = ['SimHei']          # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False       # 设置负号显示
x = [-5, -4, 2, 1]                               # x 轴坐标数据 ①
y = [7, 8, 9, 10]                                # y 轴坐标数据 ②
# 绘制线段
plt.plot(x, y, 'b', label='线 1', linewidth=2)    # ③
plt.title('绘制折线图')                           # 添加图表标题
plt.ylabel('y 轴')                                # 添加 y 轴标题
plt.xlabel('x 轴')                                # 添加 x 轴标题
plt.legend()                                      # 设置图例
# 以分辨率 72 来保存图片
plt.savefig('折线图', dpi=72)                      # ④
plt.show()                                        # 显示图形 ⑤
```

运行后生成图片如图7-3。

代码说明如下：

- 代码第①行定义了x轴数据，即[-5, -4, 2, 1]。
- 代码第②行定义了y轴数据，即[7, 8, 9, 10]。

![](img/817f9d0d19c4377d32975098402bcdd3_128_0.png)

图 7-3 程序运行结果

- 代码第③行调用plot()绘制折线，'b'指定蓝色，label设定图例名称，linewidth控制线宽。
- 代码第④行将图形保存为“折线图”，分辨率为72dpi。
- 代码第⑤行展示绘制结果。

### 7.2.4 绘制柱状图

下面介绍用Matplotlib绘制柱状图，示例代码如下：

```
import matplotlib.pyplot as plt
plt.rcParams['font.family'] = ['SimHei']          # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False        # 设置负号显示

x1 = [1, 3, 5, 7, 9]                              # x1 轴坐标数据
y1 = [5, 2, 7, 8, 2]                              # y1 轴坐标数据

x2 = [2, 4, 6, 8, 10]                             # x2 轴坐标数据
y2 = [8, 6, 2, 5, 6]                              # y2 轴坐标数据

# 绘制柱状图
plt.bar(x1, y1, label = '柱状图 1')                # ①
plt.bar(x2, y2, label = '柱状图 2')                # ②
plt.title('绘制柱状图')                            # 添加图表标题

plt.ylabel('y 轴')                                 # 添加 y 轴标题
plt.xlabel('x 轴')                                 # 添加 x 轴标题

plt.legend()                                       # 设置图例
plt.show()
```

执行后得到的柱状图如图7-4。

![](img/817f9d0d19c4377d32975098402bcdd3_129_0.png)

图 7-4 绘制柱状图

该代码绘制了包含两种图例的柱状图。第①行与第②行分别调用bar()完成两组柱子的绘制。

### 7.2.5 绘制饼状图

饼图用于展示各部分占整体的比例，无坐标系。示例代码如下：

```
import matplotlib.pyplot as plt
plt.rcParams['font.family'] = ['SimHei']  # 设置中文字体
# 股票投资组合数据
portfolio = {
    'AAPL': 30,  # 苹果公司占比 30%
    'GOOGL': 20,  # 谷歌公司占比 20%
    'AMZN': 25,  # 亚马逊公司占比 25%
    'MSFT': 15,  # 微软公司占比 15%
    'FB': 10  # Facebook 公司占比 10%
}

# 提取数据和标签
stocks = list(portfolio.keys())  # ①
weights = list(portfolio.values())  # ②

# 绘制饼状图
plt.pie(weights, labels=stocks, autopct='%1.1f%%')  # ③

# 设置图表标题
plt.title('股票投资组合')

# 显示图形
plt.show()
```

该代码基于matplotlib绘制了股票投资组合中各只股票占比的饼图。运行后效果如图7-5。

![](img/817f9d0d19c4377d32975098402bcdd3_130_0.png)

图 7-5 绘制饼状图

代码说明：

- 代码第①行把投资组合字典的键即股票名称转为列表 list(portfolio.keys())。
- 代码第②行把字典的值即占比转为列表 list(portfolio.values())。

代码里第3行借助 plt.pie() 完成了饼图的绘制，核心参数传进了占比数据 weights 和对应的标签 stocks，同时通过 autopct 指定了百分比的呈现方式，其中格式字符串 %.1f%% 意味着保留一位小数输出，%.1f 负责小数位控制，%% 则用来在结果中显示一个百分号。

### 7.2.6 散点图绘制方法
散点图是用来表现两个变量关联性的常用可视化手段，在 matplotlib 中主要靠 plt.scatter() 实现。下面是一个典型写法：

```
import matplotlib.pyplot as plt
plt.rcParams['font.family'] = ['SimHei']  # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False  # 设置负号显示

# 股票数据
closing_prices = [100, 110, 120, 115, 105]  # 收盘价数据
volume = [1000, 1500, 2000, 1800, 1200]  # 成交量数据

# 绘制散点图
plt.scatter(closing_prices, volume)

# 设置图表标题和轴标签
plt.title('股票收盘价与成交量关系')
plt.xlabel('收盘价')
plt.ylabel('成交量')

# 显示图形
plt.show()
```

上述片段先定义了 closing_prices 和 volume 两个变量，分别对应股票的收盘价和成交量。随后调用 plt.scatter() 把这两组数据映射成散点，再用 plt.title()、plt.xlabel()、plt.ylabel() 分别指定图表标题与坐标轴说明，最后用 plt.show() 呈现图形。

执行后得到的散点图如图 7-6 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_131_0.png)

### 7.2.7 子图排布技巧
同一个画布上可以放多个子图，负责定位的子函数是 subplot()，其调用形式如下：

```
subplot(nrows, ncols, index, **kwargs)
```

nrows 代表总行数，ncols 代表总列数，index 表示要放置的子图序号，从 1 开始递增，最大值等于 nrows × ncols。值得注意的是 subplot(2, 2, 1) 这种写法可以简写为 subplot(221)。

![](img/817f9d0d19c4377d32975098402bcdd3_131_1.png)

图 7-7 示意了 2×2 的子图布局。

下面是一个子图示例：

```
import matplotlib.pyplot as plt
import numpy as np
plt.rcParams['font.family'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

# 绘制柱状图
def drowsubbar():
    x1 = [1, 3, 5, 7, 9]                              # x1 轴坐标数据
    y1 = [5, 2, 7, 8, 2]                              # y1 轴坐标数据
    x2 = [2, 4, 6, 8, 10]                             # x2 轴坐标数据
    y2 = [8, 6, 2, 5, 6]                              # y2 轴坐标数据
    # 绘制柱状图
    plt.bar(x1, y1, label='柱状图 1')
    plt.bar(x2, y2, label='柱状图 2')
    plt.title('绘制柱状图')                            # 添加图表标题
    plt.ylabel('y 轴')                                 # 添加 y 轴标题
    plt.xlabel('x 轴')                                 # 添加 x 轴标题

# 绘制饼状图
def drowsubpie():
    # 各种活动标题列表
    activies = ['工作', '睡', '吃', '玩']
    # 各种活动所占时间列表
    slices = [8, 7, 3, 6]
    # 各种活动在饼状图中的颜色列表
    cols = ['c', 'm', 'r', 'b']
    plt.pie(slices, labels=activies, colors=cols,
            shadow=True, explode=(0, 0.1, 0, 0), autopct='%.1f%%')
    plt.title('绘制饼状图')

# 绘制折线图
def drowsubline():
    x = [5, 4, 2, 1]                                  # x 轴坐标数据
    y = [7, 8, 9, 10]                                 # y 轴坐标数据
    # 绘制线段
    plt.plot(x, y, 'b', label='线 1', linewidth=2)
    plt.title('绘制折线图')                            # 添加图表标题
    plt.ylabel('y 轴')                                 # 添加 y 轴标题
    plt.xlabel('x 轴')
    plt.legend()

# 绘制散点图
def drowssubscatter():
    n = 1024
    x = np.random.normal(0, 1, n)
    y = np.random.normal(0, 1, n)
    plt.scatter(x, y)
    plt.title('绘制散点图')

plt.subplot(2, 2, 1)                                  # ①
drowsubbar()                                          # ②

plt.subplot(2, 2, 2)
drowsubpie()

plt.subplot(2, 2, 3)
drowsubline()

plt.subplot(2, 2, 4)
drowssubscatter()

plt.tight_layout()                                    # ③
plt.show()
```

运行后产生的子图如图 7-8 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_133_0.png)

代码要点如下：

- 第①行通过 plt.subplot(2, 2, 1) 设定当前子图的位置。
- 第②行调用自定义函数 drowsubbar() 来绘制柱状图。
- 第③行对整体布局进行微调，避免子图之间重叠。

![](img/817f9d0d19c4377d32975098402bcdd3_134_0.png)

## 7.3 ChatGPT 助力办公自动化可视化
在办公自动化场景里，数据可视化常用于各类报表与分析，其核心价值是让数据洞察更直观，从而支撑业务决策。

ChatGPT 可以提供数据相关的洞察与分析建议，但面对复杂的数据处理与可视化需求时，专业的分析工具和库仍更有优势。ChatGPT 更适合作为辅助角色，解决一些轻量级的问题。

### 7.3.1 示例1：让 ChatGPT 解析数据结构
开展可视化前，先弄清数据结构。本例使用的是跨境电商销售数据，文件内容可参考图 6-2。

下面演示如何让 ChatGPT 完成结构分析。

办公人员给 ChatGPT 的提问如下：

有如下跨境电商销售数据样本数据，帮我对数据的结构进行分析：

```
Row ID  Order ID  Order Date  Ship Date  Ship Mode  Customer ID  Customer Name  Segment  Country  City  State  Postal Code  Region  Product ID  Category  Sub-Category  Product Name  Sales  Quantity  Discount  Profit
1  CA-2016-152156  2016/11/8  2016/11/11  Second Class  CG-12520  Claire Gute  Consumer  United States  Henderson  Kentucky  42420  South  FUR-BO-10001798  Furniture  Bookcases  Bush Somerset Collection Bookcase  261.96  2  0  41.9136
2  CA-2016-152156  2016/11/8  2016/11/11  Second Class  CG-12520  Claire Gute  Consumer  United States  Henderson  Kentucky  42420  South  FUR-CH-10000454  Furniture  Chairs  Hon Deluxe Fabric Upholstered Stacking
```

### 7.3.2 示例2：分地区分时间看销量柱状图
按地区和时间展示销量，能清晰看到不同维度的销售情况。下面用 Python 的 Matplotlib 生成柱状图的示例代码：

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

data = pd.read_csv('data/1000条跨境电商销售数据.csv')
data['Order Date'] = pd.to_datetime(data['Order Date'])

fig = plt.figure(figsize=(10, 6))
ax = fig.gca()

data.groupby(['Region', data['Order Date'].dt.year])['Units Sold'].sum().unstack().plot(kind='bar', ax=ax)

plt.xlabel('地区')
plt.ylabel('销量')
plt.title('不同地区的销量')

plt.tight_layout()
plt.show()
```

运行后得到的销量柱状图如图 7-9 所示。

代码说明如下：

- 第①行用 pd.read_csv 读取 CSV 文件，将内容加载到名为 data 的 DataFrame。
- 第②行把 data 中 'Order Date' 列转为日期时间类型，便于后续处理与分组。
- 第③行新建 Figure 对象 fig，并通过 figsize 设置画布大小为宽 10 英寸、高 6 英寸。
- 第④行先按 'Region' 和订单年份分组求和 Units Sold，再用 unstack 把年份展开为列、地区作为行，最后用 plot(kind='bar', ax=ax) 在 ax 上绘制柱状图。

从图 7-9 看，x 轴的地区存在两个使用问题：

1. 全是英文，不够直观。
2. 名称过长，显示拥挤。

由于数据源自“1000 条跨境电商销售数据.csv”，数据量很大，逐条人工翻译不现实，可以在代码中建立映射表。

调整后的代码如下：

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['SimHei']
plt.rcParams['axes.unicode_minus'] = False

data = pd.read_csv('data/1000条跨境电商销售数据.csv')
data['Order Date'] = pd.to_datetime(data['Order Date'])

fig = plt.figure(figsize=(10, 6))
ax = fig.gca()

sales_by_region = data.groupby(['Region', data['Order Date'].dt.year])['Units Sold'].sum().unstack()
sales_by_region.plot(kind='bar', ax=ax)

# 地区名称的中英文对应字典
# 自定义地区名称的翻译字典
region_translation = {
    'Middle East and North Africa': '北非',
    'North America': '北美洲',
    'Asia': '亚洲',
    'Sub-Saharan Africa': '南非洲',
    'Europe': '欧洲',
    'Central America and the Caribbean': '中美洲',
    'Australia and Oceania': '大洋洲'
    # 添加更多地区的翻译
}

# 获取当前的x轴刻度标签
x_labels = ax.get_xticklabels()

# 替换刻度标签为中文名称
for label in x_labels:
    label.set_text(region_translation[label.get_text()])

ax.set_xticklabels(x_labels)

plt.xlabel('地区')
plt.ylabel('销量')
plt.title('不同地区的销量')

plt.tight_layout()
plt.show()
```

运行后得到的销量柱状图如图 7-10 所示。

要点说明：

- 第①行定义了地区中英文对照字典 region_translation，用于翻译地区名称。
- 第②行在遍历刻度标签时，通过字典取出对应的中文并设置回刻度文本。

### 7.3.3 示例3：按产品类型看销售占比饼图
了解各产品类型的销售占比有助于判断产品结构。下面是使用 Matplotlib 绘制相关饼图的示例代码：

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['SimHei']  # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False  # 设置负号显示
plt.rcParams['font.size'] = 13

# 读取 CSV 文件
data = pd.read_csv('data/1000条跨境电商销售数据.csv')

fig = plt.figure(figsize=(10, 6))
ax = fig.gca()

# 根据产品类型展示销售占比的饼图
category_sales = data.groupby('Item Type')['Units Sold'].sum()
category_sales.plot(kind='pie', autopct='%1.1f%%')
plt.ylabel('')
plt.title('不同产品类型的销售占比')
plt.show()
```

运行后得到的不同产品类型的销售占比饼图如图 7-11 所示。

代码说明如下：

- 第①行使用 groupby 按“产品类型”列对“Units Sold”列求和，得到各类型的销售总量。
- 第②行调用 plot 绘制饼图，并把百分比格式设为 '%1.1f%%'。

从图 7-11 可见产品类型同样是英文，也可建立翻译表进行本地化。

修改代码如下：

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['SimHei']          # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False        # 设置负号显示
plt.rcParams['font.size'] = 13

# 读取 CSV 文件
data = pd.read_csv('data/1000条跨境电商销售数据.csv')

# 产品类型的英文对应中文翻译字典
category_translation = {
    'Baby Food': '婴儿食品',
    'Beverages': '饮料',
    'Cereal': '谷类食品',
    'Clothes': '服装',
    'Cosmetics': '化妆品',
    'Fruits': '水果',
    'Household': '家居用品',
    'Meat': '肉类',
    'Office Supplies': '办公用品',
    'Personal Care': '个人护理品',
    'Snacks': '零食',
    'Vegetables': '蔬菜'
}

# 将产品类型英文转换为中文
data['产品类型'] = data['Item Type'].map(category_translation)

# 根据产品类型展示销售占比的饼图
category_sales = data.groupby('产品类型')['Units Sold'].sum()

# 绘制饼图
plt.pie(category_sales, labels=category_sales.index, autopct='%1.1f%%')

# 设置图表标题
plt.title('不同产品类型的销售占比')

# 显示饼图
plt.show()
```

执行以上脚本后，产品类别的销售额构成会以饼图形式呈现，结果见图 7-12。

### 7.3.4 示例4：按销售渠道统计占比的饼图

下面给出一个以销售渠道为维度绘制占比饼图的示例代码片段：

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['SimHei']  # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False  # 设置负号显示

# 读取 CSV 文件
data = pd.read_csv('data/1000条跨境电商销售数据.csv')
#
# 销售渠道销售占比饼图
channel_sales = data.groupby('Sales Channel')['Total Revenue'].sum()
channel_sales.plot(kind='pie', autopct='%1.1f%%', startangle=90, title='销售渠道销售占比')
plt.axis('equal')
plt.show()
```

运行后得到的渠道销售占比饼图见图 7-13。

### 7.3.5 示例5：基于时间追踪销售走势的折线图

用折线图展示随时间变化的销售额，有助于把握趋势走向。以下示例借助 Python 的 Matplotlib 库完成折线图的绘制：

```python
import pandas as pd
import matplotlib.pyplot as plt

plt.rcParams['font.family'] = ['SimHei']  # 设置中文字体
plt.rcParams['axes.unicode_minus'] = False  # 设置负号显示
```

图 7-13 销售渠道销售占比饼图

```python
# 读取 CSV 文件
data = pd.read_csv('data/1000 条跨境电商销售数据.csv')

fig = plt.figure(figsize=(10, 6))
ax = fig.gca()

# 销售趋势折线图
data['Order Date'] = pd.to_datetime(data['Order Date'])
monthly_sales = data.groupby(data['Order Date'].dt.to_period('M'))['Total Revenue'].sum()
monthly_sales.plot(kind='line', marker='o', xlabel='日期', ylabel='销售额', title='销售趋势')
plt.show()
```

执行完代码，生成的销售趋势折线图如图 7-14 所示。

图 7-14 销售趋势的折线图

## 7.4 本章要点回顾

本章围绕 Python 的 Matplotlib 库展开数据可视化讲解。先完成库的安装与导入，随后介绍图表的基础要素，如图例、标题、坐标轴等。接着给出折线图、柱状图、饼图、散点图等多种图表的代码实现，并说明子图绘制方法。再结合 ChatGPT 演示了几个实战流程：

- （1）借助 ChatGPT 解析数据结构，提炼可视化维度。
- （2）依托 ChatGPT 的建议，以地区和时间作为维度绘制销量柱状图。
- （3）按产品类型生成销售占比饼图。
- （4）按销售渠道生成占比饼图。
- （5）最后绘制基于时间序列的销售趋势折线图。

与 ChatGPT 配合使用，能显著提升可视化分析效率。掌握 Matplotlib 的使用方式并辅以 ChatGPT，即可具备 Python 数据可视化的基础能力，为后续分析、模型诊断与结果汇报提供支撑。

# 第8章 ChatGPT助力Excel自动化

ChatGPT 依托自然语言处理技术，可作为辅助工具推动 Excel 自动化。在本章中将探讨如何结合 ChatGPT 与 Excel，提升数据处理与分析效率。

## 8.1 xlwings驱动的Excel自动化实践

在 Python 中读写 Excel 数据可选的库有 xlrd/xlwt、openpyxl、xlwings、pywin32 等，其中 xlwings 简洁且功能强大，主要优势如下：

- 1. 能调用 VBA 宏函数，完成复杂 Excel 操作。
- 2. VBA 中也可调用 Python 模块函数，实现双向混合编程。
- 3. 接口丰富，便于与 Pandas、NumPy、Matplotlib 等库集成。
- 4. 批量处理数据时性能表现良好。

xlwings 的安装可通过如下 pip 命令完成：

```
pip install xlwings
```

### 8.1.1 xlwings核心对象层级

使用 xlwings 前需先了解其对象层级，图 8-1 展示了该层级关系。App 代表 Excel 应用程序对象，可通过它打开、关闭、保存文件。一个 App 可包含多个 Book 工作簿对象，一个 Excel 文件即为一个 Book。一个 Book 下可包含多个 Sheet 工作表对象，而 Sheet 中又可包含多个 Range 单元格区域对象。

### 8.1.2 示例1：读取Excel单元格实操

安装完成后即可用 xlwings 访问 Excel 文件。本例打开名为“学生信息.xlsx”的文件并读取 B2 单元格内容，该文件内容如图 8-2 所示。

示例代码如下：

```python
import xlwings as xw

# 设置程序不可见运行
app = xw.App(visible = False, add_book = False)
f = r'data/学生信息.xlsx'
# 打开 Excel 文件返回一个工作簿对象
wb = app.books.open(f)

# 通过工作表名返回工作表对象
sheet1 = wb.sheets['Sheet1']
# 通过工作表索引返回工作表对象
sheet2 = wb.sheets[0]
# 返回活动工作表对象
sheet3 = wb.sheets.active
# 返回单元格 B2 对象
rng = sheet1.range('B2')
# 打印单元格 B2 内容
print('单元格 B2:', rng.value)
# 返回单元格 B2 对象
rng = sheet1.range((2, 2))
print('单元格 B2:', rng.value)

# 返回表头
rng = sheet1.range('a1:f1')
print('返回表头:', rng.value)
# 返回姓名列
rng = sheet1.range('b1:b7')
print('姓名列:', rng.value)

# 返回二维表
rng = sheet1.range('a1:f7')
print(rng.value)

# 返回二维数组
L = rng.value

print(L)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()
print('结束.')
```

运行后控制台输出如下：

```
单元格 B2: 张三
单元格 B2: 张三
返回表头: ['学号', '姓名', '生日', '年龄', '成绩', '年级']
姓名列: ['姓名', '张三', '李四', '王五', '董六', '赵八', '周五']
[['学号', '姓名', '生日', '年龄', '成绩', '年级'], [100122014.0, '张三', '31/12/1999', 21.0, 85.0, '一年级'], [100232015.0, '李四', '1-12-1999', 200.0, 60.0, '三年级'], [100122012.0, '王五', datetime.datetime(2019, 2, 6, 0, 0), 24.0, 100.0, '3年级'], [100342013.0, '董六', datetime.datetime(2019, 2, 7, 0, 0), 23.0, 300.0, '1年级'], [None, '赵八', datetime.datetime(2019, 2, 9, 0, 0), 23.0, -10.0, '7年级'], [100232015.0, '周五', datetime.datetime(2020, 1, 20, 0, 0), 27.0, None, '9年级']]
结束。
```

代码说明如下：

- 第①行导入 xlwings 库并使用别名 xw。
- 第②行创建 App 对象，采用不可见方式运行，构造函数中 visible 参数控制是否可见，add_book 参数用于新建工作簿。
- 第④行通过工作表名称返回工作表对象，也可通过索引获取。只有一个工作表时，sheet1 与 sheet2 指向同一对象。
- 第⑤行通过 sheets.active 获取当前活动工作表，books 也有 active 属性可获取活动工作簿。只有一个工作表时，sheet 与活动表一致。
- 第⑥行获取 B2 单元格对象，其类型为 Range，可通过 value 属性读取内容。
- 第⑦行使用元组(2, 2) 指定单元格，与字符串 B2 效果相同，不区分大小写。
- 第⑧行 a1:f1 指定表头区域，返回一维列表。
- 第⑨行 b1:b7 指定姓名列区域，返回一维列表。
- 第⑩行 a1:f7 指定整张表，返回二维列表。

### 8.1.3 示例2：定位并获取数据表格范围

在 8.1.2 节中表格区域是通过 a1:f7 硬编码指定的，若表格大小未知，如何动态获取？xlwings 提供两种方式：

- （1）基于单元格获取所在区域，先用 range 选择一个单元格，如 B2，再用 current_region 属性获取其所在区域，最后用 address 属性得到地址。
- （2）从单元格向外扩展至表格，先选择单元格，再用 expand 方法扩展，指定参数为 'table' 可按表格形式扩展，最后用 address 获取地址。

示例代码如下：

```python
import xlwings as xw

# 设置程序不可见运行
app = xw.App(visible = False, add_book = False)
f = r'data/学生信息.xlsx'
# 打开 Excel 文件返回一个工作簿对象
wb = app.books.open(f)

# 通过工作表名返回工作表对象
sheet1 = wb.sheets['Sheet1']

# 函数 1、通过单元格获得所在的区域
rng = sheet1.range('A1').current_region

L1 = rng.value

# 打印二维数组 L1
print(" 打印二维数组 L1------------------ ")
for x in L1:
    print(x)

# 函数 2、从单元格扩展至表格区域
rng = sheet1.range('A1').options(expand = 'table')  # expand = 'table'是默认参数,表示向下和
                                                    # 向右扩展
# rng = sheet1.range('A1').options(expand = 'down')  # 向下扩展
# rng = sheet1.range('A1').options(expand = 'right') # 向右扩展

L2 = rng.value

# 打印二维数组 L2
print(" 打印二维数组 L2------------------ ")
# 打印二维数组 L
for x in L2:
    print(x)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()
print('结束。')
```

运行后控制台输出如下：

```
打印二维数组 L1------------------
['学号', '姓名', '生日', '年龄', '成绩', '年级']
[100122014.0, '张三', '31/12/1999', 21.0, 85.0, '一年级']
[100232015.0, '李四', '1-12-1999', 200.0, 60.0, '三年级']
[100122012.0, '王五', datetime.datetime(2019, 2, 6, 0, 0), 24.0, 100.0, '3 年级']
[100342013.0, '小六', datetime.datetime(2019, 2, 7, 0, 0), 23.0, 300.0, '1 年级']
[None, '赵八', datetime.datetime(2019, 2, 9, 0, 0), 23.0, -10.0, '7 年级']
[100982015.0, '周五', datetime.datetime(2020, 1, 20, 0, 0), 27.0, None, '9 年纪']
打印二维数组 L2------------------
['学号', '姓名', '生日', '年龄', '成绩', '年级']
[100122014.0, '张三', '31/12/1999', 21.0, 85.0, '一年级']
[100232015.0, '李四', '1-12-1999', 200.0, 60.0, '三年级']
[100122012.0, '王五', datetime.datetime(2019, 2, 6, 0, 0), 24.0, 100.0, '3 年级']
[100342013.0, '小六', datetime.datetime(2019, 2, 7, 0, 0), 23.0, 300.0, '1 年级']
结束。
```

代码说明如下：

- 第①行获取 A1 单元格所在表格区域，current_region 为当前区域属性。
- 第②行读取该表格区域内容，返回二维列表。
- 第③行遍历 L1 列表，x 为取出元素。
- 第④行对 A1 单元格进行扩展，options(expand='table') 表示向下向右扩展，expand 参数可选 'down'、'down' 与 'table'，其中 'table' 为默认值。

### 8.1.4 示例3：获取区域的行列数量

在办公场景中常需获知单元格区域的行数和列数，xlwings 可通过 columns 与 rows 属性获取区域的列集合与行集合，进而计算行列数。

示例代码如下：

```python
import xlwings as xw

# 设置程序不可见运行
app = xw.App(visible=False, add_book=False)
f = r'data/学生信息.xlsx'
# 打开 Excel 文件返回一个工作簿对象
wb = app.books.open(f)

# 通过工作表名返回工作表对象
sheet1 = wb.sheets['Sheet1']

# 函数 1、通过单元格获得所在的区域
print('---------- 函数 1 选择区域 ----------')
rng1 = sheet1.range('A1').current_region
rows = rng1.rows.count  # 获取区域行数
print('行数', rows)

columns = rng1.columns.count  # 获取区域列数
print('列数', columns)

print('---------- 函数 2 选择区域 ----------')
rng2 = sheet1.range("a1").expand()

rows = rng2.rows.count  # 获取区域行数
print('行数', rows)

columns = rng2.columns.count  # 获取区域列数
print('列数', columns)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()

print('结束。')
```

运行该示例后，控制台的输出信息如下：

```
---------- 函数 1 选择区域 ----------
行数 7
列数 6
---------- 函数 2 选择区域 ----------
行数 5
列数 6
结束。
```

代码说明如下：

- 第①行代码借助函数1来获取单元格区域的行数，运行结果表明该区域共有7行。
- 第②行代码同样调用函数1来获取单元格区域的列数，运行结果表明该区域共有6列。
- 第③行代码借助函数2来获取单元格区域的行数，运行结果显示该区域的列数为5。
- 第④行代码借助函数2来获取单元格区域的列数，运行结果显示该区域的列数为6。

### 8.1.5 示例4：表格转置技巧
开发时经常需要把表格进行转置，让行变成列、列变成行。xlwings 里可以用 transpose() 函数来达成这一目的，该函数会交换 Range 对象的行列，实现行列互换的效果。

示例代码如下：

```python
import xlwings as xw

# 设置程序不可见运行
app = xw.App(visible=False, add_book=False)
f = r'data/学生信息.xlsx'
# 打开 Excel 文件返回一个工作簿对象
wb = app.books.open(f)

# 通过工作表名返回工作表对象
sheet1 = wb.sheets['Sheet1']

rng1 = sheet1.range('A1').current_region

# 转置表格区域
rng2 = rng1.options(transpose=True) ①

# 返回二维数组
L = rng2.value
print(" 打印二维数组 L------------------ ")
for x in L:
    print(x)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
```

### 8.1.6 示例5：定制单元格读取类型
回顾 8.1.5 节示例的输出，存在几个不顺手的点：

- （1）张三的学号是 100122014，读取后变成了 100122014.0。
- （2）希望日期类型的单元格能得到 datetime 类型。
- （3）空白单元格被转成了 None。

xlwings 允许通过 option 函数为单元格读取指定格式，从而解决 8.1.5 节提到的问题。

示例代码如下：

```python
from datetime import date

import xlwings as xw

app = xw.App(visible=False, add_book=False)
f = r'data/学生信息.xlsx'
# 打开Excel文件返回一个工作簿对象
wb = app.books.open(f)

# 通过工作表名返回工作表对象
sheet1 = wb.sheets['Sheet1']

rng1 = sheet1.range('a1:f7')  # ①
rng2 = rng1.options(numbers=int, dates=date, empty='NA')  # ②

# 返回二维数组
L = rng2.value
print(" 打印二维数组 L----------------- ")
for x in L:
    print(x)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()

print('结束。')
```

运行后控制台的输出如下：

```
打印二维数组 L-----------------
['学号', '姓名', '生日', '年龄', '成绩', '年级']
[100122014, '张三', '31/12/1999', 21, 85, '一年级']
[100232015, '李四', '1-12-1999', 200, 60, '三年级']
[100122012, '王五', datetime.date(2019, 2, 6), 24, 100, '3年级']
[100342013, '小六', datetime.date(2019, 2, 7), 23, 300, '1年级']
['NA', '赵八', datetime.date(2019, 2, 9), 23, -10, '7年级']
[100982015, '周五', datetime.date(2020, 1, 20), 27, 'NA', '9年纪']
结束。
```

代码说明如下：

- 第①行获取了 a1:f7 这个区域。
- 第②行为该 Range 对象配置读取选项，其中 numbers=int 表示把 Excel 中的数字转为 Python int；dates=datetime.date 表示把 Excel 日期转为 Python datetime.date；empty='NA' 指定空值用 NA 表示，也可换成 NaN，NaN 在数据分析里常用来标记空值，这样 Excel 的空单元格就会被标记为 NA。

### 8.1.7 示例6：向单元格写入数据
前面演示了读取单元格，这里演示写入。如下图 8-3①，单个值可以直接写入单元格；一维列表可以写入一行或一列，如图 8-3②和 8-3③；二维列表则可以写入一个连续区域，如图 8-4 所示。

示例代码如下：

```python
import xlwings as xw

# 设置程序不可见运行
app = xw.App(visible=False, add_book=True)  # ①
# 创建一个新的工作簿对象，并且返回该工作表工作簿对象
wb = xw.Book()

sheet = wb.sheets.active
sheet.range('a1').value = 'Hello xlwings'  # ②

# 写入一维列表
sheet.range("a2:c2").value = [1, 2, 3]  # ③

wb.sheets['Sheet1'].autofit('rows')  # rows 或 r
wb.sheets['Sheet1'].autofit('c')  # columns 或 c

sheet.range("a2:a4").options(transpose = True).value = [1, 2, 3]

# 添加工作表
sheet3 = wb.sheets.add(name = 'Sheet3', after = 'Sheet1')

# 写入二维列表
sheet3.range('A1').value = [[1, 2], [3, 4]]
# 替换写法 sheet3.range('C5').value = [[1, 2], [3, 4]]

f = r'data/temp.xlsx'
# 保存文件
wb.save(path = f)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()
print('结束。')
```

代码说明如下：

- 第①行新建 Excel 应用对象，注意构造时 add_book 设为 True，表示可以新建工作簿。
- 第②行在当前工作表的 A1 单元格写入字符串 Hello xlwings。
- 第③行给 a2:c2 区域赋值，图中 a2:c2 是横向相连的三个单元格，所以需要用一维列表来赋值。
- 第④行调用工作表的 autofit 方法实现自动缩放，参数 rows 或 r 控制行自动缩放，columns 或 c 控制列自动缩放。
- 第⑤行给 a2:a4 区域赋值，默认是横向排布。若想纵向排布，可在 options 中设置 transpose=True。transpose=True 表示对单元格区域做转置，由横向变为纵向。
- 第⑥行通过工作簿的 add 方法新增工作表，name 指定表名，after='Sheet1' 表示在 Sheet1 之后插入，类似参数还有 before，表示在指定表之前插入。
- 第⑦行把二维列表写入以 A1 为左上角的区域，写入时区域会根据数据自动扩展。
- 第⑧行调用 save 方法保存文件，path 参数指定保存路径。

### 8.1.8 示例7：美化单元格样式
xlwings 也能设置单元格样式，下面举例说明如何对区域进行格式设置。如图 8-5 所示，表格文字加粗；成绩列保留两位小数；生日列显示为 yyyy-mm-dd，即四位年份、两位月份、两位日期并用横线连接。

示例代码如下：

```python
from datetime import date
import xlwings as xw
# 设置程序不可见运行
app = xw.App(visible = False, add_book = False)

f = r'data/学生信息.xlsx'
# 打开 Excel 文件返回一个工作簿对象
wb = app.books.open(f)

# 通过工作表名返回工作表对象
sheet1 = wb.sheets['Sheet1']

END_ROW_NO = rng.last_cell.row + 1  # 结束行号
END_COL_NO = rng.last_cell.column + 1  # 结束列号

# 按行遍历
for row in range(2, END_COL_NO):  # ①
    # 按列遍历
    for col in range(1, END_COL_NO):  # ②

        # 获得单元格对象
        cell = sheet1.range((row, col))  # ③
        # 如果是[生日]列设置日期格式为"yyyy - mm - dd"
        if col == 3:
            cell.number_format = 'yyyy - mm - dd'  # ④
        cell.font.size = 10  # 设置字体的大小  # ⑤
        cell.font.bold = True  # 设置为粗体  # ⑥
        # 如果是成绩列设置数字格式
        if col == 5:
            cell.number_format = '0.00'  # ⑦

        # 取出单元格数据
        data = cell.options(numbers = int, dates = date).value
        # 判断单元格是否为空值
        if data is None:
            # 设置 range 的颜色
            cell.color = (255, 255, 0)  # 设置为黄色 ⑧

# 保存修改后的文件另存为[文件学生信息 2.xlsx]
f = r'data/学生信息 2.xlsx'
wb.save(path=f)
# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()
print('结束。')
```

代码说明如下：

- 第①②行是两层嵌套 for 循环，用于遍历表格中的单元格，注意行从第 2 行开始，因为第 1 行是表头，列从 1 开始而不是 0。
- 第③行通过元组(row, col)定位出对应的单元格区域对象。
- 第④行设置单元格的日期格式，若单元格为日期则按 yyyy-mm-dd 显示。
- 第⑤行设置单元格字体大小。
- 第⑥行设置单元格字体加粗。
- 第⑦行设置单元格数字格式，若为数字则保留两位小数显示。
- 第⑧行设置单元格背景色为黄色，其中 (255, 255, 0) 是用三元组表示的颜色。

### 8.1.9 示例8：用VBA宏批量去重
xlwings 可以调用 VBA 宏来批量删除重复数据。如图 8-6 所示，学号列存在多条重复记录。若要删除重复学号对应的学生信息，需要在学号列第 2 列查找重复项并删除整行。

VBA 即 Visual Basic for Applications，是一种宏语言，主要用于扩展 Microsoft Office 软件功能。

删除重复数据的 Python 实现代码如下：

```python
import xlwings as xw

# 设置程序不可见运行
app = xw.App(visible = False, add_book = False)
f = r'data/学生信息 - 带有宏.xlsm'

# 打开 Excel 文件返回一个工作簿对象
wb = app.books.open(f)  # ①

# 通过工作表索引返回工作表对象
sheet1 = wb.sheets[0]

rng1 = sheet1.range('A1').current_region

# 获得 VBA 宏对象
run_macro = app.macro('MyModule1.Duplicates_Rows')  # ②
run_macro(rng1, 1)  # ③

f = r'data/学生信息 - 删除重复数据后.xlsm'

wb.save(path = f)

# 关闭工作簿对象
wb.close()
# 退出 Excel 应用程序
app.quit()

print('结束。')
```

代码说明如下：

- 第①行打开学生信息表文件，该文件为 xlsm 类型，即带有宏的 Excel 文件。
- 第②行获取 VBA 宏对象 macro，这是 xlwings 提供的对象。构造 macro('MyModule1.Duplicates_Rows') 时，字符串参数指定了 VBA 模块名和模块中的过程。具体地，MyModule1 为 VBA 模块名，Duplicates_Rows 为用于删除重复数据的子过程。
- 第③行通过 macro 对象运行 VBA 宏，run_macro(rng1, 1) 将 rng1 和 1 作为参数传入宏子过程。

删除重复数据的 VBA 模块实现代码如下：

```vb
' 删除重复的行数据
' 参数 rng 是要删除数据的区域
' 参数 col_no 查找重复数据列

Sub Duplicates_Rows(rng, col_no)  # ①
'
'
With ActionSheet

'RemoveDuplicates 是 Excel 提供删除重复数据函数

rng.RemoveDuplicates Columns:=Array(col_no)  # ②

End With

End Sub
```

代码第①行在 MyModule1.bas 模块文件中定义了一个带两个参数的子过程。
代码第②行在子过程中调用 VBA 提供的 RemoveDuplicates 函数来删除重复数据。

### 8.1.10 示例9：插入单元格与区域
插入单元格和单元格区域可通过 xlwings 的 Range 对象的 insert() 方法实现。例如图 8-7 展示了一张员工信息表。

在该表中插入单元格和单元格区域的示例代码如下：

```python
import xlwings as xw

app = None
try:
    # 创建 App 对象
    f = r'data/北京分公司 - 员工信息.xlsx'

    app = xw.App(visible = True, add_book = False)
    wb = app.books.open(f)
    sheet = wb.sheets['北京分公司']
    sheet.range('B1').insert(shift = 'down')          # 在 b1 单元格上方插入单元格,单元格下移
    sheet.range('b1').value = '大家好'                # 在 b1 单元格添加数据
    sheet.range('b1').insert(shift = 'down')
    sheet.range('b1').value = '大家好'
    sheet.range('b1').insert(shift = 'down')
    sheet.range('b1').value = '大家好'
    sheet.range('c1:e1').insert(shift = 'down')       # 在 c1:e1 单元格区域上方插入单元格区域,
                                                       # 整个区域下移
    sheet.range("c1:e1").value = ['刘备', '关羽', '张飞']  # 在 c1:e1 单元格中添加区域内容
    sheet.range('f1:f3').insert(shift = 'right')      # 在 f1:f3 单元格区域左方插入单元格区域,
                                                       # 整个区域右移
    sheet.range("f1:f3").value = [[100], [200], [6]]  # 在 f1:f3 单元格中添加区域内容
```

### 8.1.11 示例10：删除单元格与区域
在 xlwings 里要去掉单个单元格或连续区域，最直接的方式是调用 Range 对象的 delete() 方法。

示例代码如下：

```python
# coding = utf-8
# 13.2.3 删除单元格和单元格区域

import xlwings as xw

app = None
try:
    # 创建 App 对象
    f = r'data/北京分公司 - 员工信息 2.xlsx'

    app = xw.App(visible = True)
    wb = app.books.open(f)
    sheet = wb.sheets['北京分公司']
    sheet.range('b1').delete()          # 在删除 b1 单元格, 下方单格上移
    sheet.range('b1').delete()
    sheet.range('b1').delete()
    sheet.range('c1:e1').delete()       # 删除 c1:e1 单元格区域下方单元格区域上移
    sheet.range('f1:f3').delete()       # 删除 f1:f3 单元格区域, 删除后右侧单元格左移

    f = r'data/北京分公司 - 员工信息 3.xlsx'
    wb.save(path = f)                   # 保存文件
    wb.close()                          # 关闭工作簿对象
finally:
    app.quit()                          # 退出 Excel 应用程序
print('写入完成。')
```

运行后脚本会打开“北京分公司-员工信息 2.xlsx”，对指定的单元格和区域执行删除操作，最后保存为“北京分公司-员工信息 3.xlsx”。

### 8.1.12 示例11：工作表插入操作
新增工作表可借助工作表集合的 add() 方法完成，演示代码如下：

```python
import xlwings as xw

app = None
try:
    # 创建 App 对象
    app = xw.App(visible = False, add_book = True)  # add_book 参数设置为 True
    # 创建一个新的工作簿对象，并且返回该工作表工作簿对象
    wb = xw.Book()
    sheet = wb.sheets.active
    sheet.name = '北京分公司'                       # 为工作表改名
    sheet.range('a1').value = '北京分公司同事大家好！'
    # 在"北京分公司"工作表之前添加"上海分公司"工作表
    sheet2 = wb.sheets.add(name = '上海分公司', after = '北京分公司')
    sheet2.range('a1').value = '上海分公司同事大家好！'
    # 在"北京分公司"工作表之后添加"天津分公司"工作表
    sheet3 = wb.sheets.add(name = '天津分公司', before = '北京分公司')
    sheet3.range('a1').value = '天津分公司同事大家好！'

    f = r'data/插入工作表.xlsx'
    wb.save(path = f)                           # 保存文件
    wb.close()                                  # 关闭工作簿对象
finally:
    app.quit()                                  # 退出 Excel 应用程序
print('写入完成。')
```

执行结果如图 8-9 所示，最终保留三张工作表。

图 8-9 运行结果

整体流程是先新建一个空工作簿，默认自带一张表，代码第①行把这张表改名为“北京分公司”。第②行在“北京分公司”之前插入“上海分公司”。第③行在“北京分公司”之后插入“天津分公司”。

### 8.1.13 示例12：工作表删除操作
移除工作表同样使用工作表集合的 delete() 方法，示例代码如下：

```python
import xlwings as xw

app = None
try:
    # 创建 App 对象
    app = xw.App(visible = False, add_book = True)  # add_book 参数设置为 True
    # 创建一个新的工作簿对象，并且返回该工作表工作簿对象
    f = r'data/插入工作表.xlsx'
    wb = app.books.open(f)  # 打开 Excel 文件返回一个工作簿对象
    wb.sheets['北京分公司'].delete()  # 删除“北京分公司”工作表
    wb.sheets['上海分公司'].delete()
    wb.sheets.add()                # 添加一个空的工作表
    f2 = r'data/删除工作表.xlsx'
    wb.save(path = f2)              # 保存文件
    wb.close()                      # 关闭工作簿对象
finally:
    app.quit()                  # 退出 Excel 应用程序
print('写入完成。')
```

运行后如图 8-10 所示，只剩下两个工作表。

## 8.2 pywin32 批量操控 Excel
当对 Excel 的控制需求更强时，可以引入 pywin32。借助该库能通过 COM 组件对象模型直接操控 Windows 上的 Microsoft 应用程序，对批量打开、保存、格式转换等场景非常友好。

在命令行中安装 pywin32 的 pip 指令为：

```
pip install pywin32
```

Windows 下的安装过程如图 8-11 所示。

图 8-11 安装过程

下面通过几个实操示例说明如何用 pywin32 批量处理 Excel 文件。

### 8.2.1 示例13：xls 批量转 xlsx
Excel 常见两种后缀：

1. .xls 对应 1997-2003 版本，采用二进制复合文档结构。
2. .xlsx 使用基于 XML 的压缩格式，文件更小、响应更快。

手工打开再另存的方式在少量文件时可行，批量场景下效率很低。

示例代码如下：

```python
import os
import win32com.client

# 指定输入文件夹
input_dir = r'<您的输入文件夹>'

# 指定输出文件夹
output_dir = r'<您的输出文件夹>'

try:
    excelapp = win32com.client.Dispatch('Excel.Application')
    excelapp.Visible = False
    excelapp.DisplayAlerts = False

    # 遍历输入文件夹
    for root, dirs, files in os.walk(input_dir):
        for name in files:
            if name.endswith('.xls'):
                infile = os.path.join(root, name)
                filename = os.path.splitext(name)[0]
                wb = excelapp.Workbooks.Open(infile)
                outfile = os.path.join(output_dir, filename + '.xlsx')
                outfile = outfile.replace('/', '\\')
                wb.SaveAs(outfile, FileFormat = 51)
                print(outfile, '转换成功')
                wb.Close()
finally:
    excelapp.Quit()
print('全部转换完成')
```

执行后输出目录会出现转换成功的.csv文件。

要点拆解如下：

- 代码第④行关闭 Excel 的提示与警告。
- 代码第⑤行用 os.walk 递归遍历输入文件夹。
- 代码第⑥行筛选文件名以 .xls 结尾的文件。
- 代码第⑦行拼装输入文件的完整路径。
- 代码第⑧行去掉文件名中的扩展名。
- 代码第⑨行打开对应的工作簿对象。
- 代码第⑩行拼装输出 CSV 的路径与文件名。
- 代码第⑪行调用 SaveAs，格式参数设为 51 保存为 .xlsx。
- 代码第⑫行关闭工作簿并释放资源。

### 8.2.2 示例14：Excel 批量转 csv
Excel 与 CSV 都是电子表格格式，Excel 本身就支持互转。本节演示批量把 Excel 转为 CSV，思路与 8.2.1 接近，区别在于另存时的格式参数。

示例代码如下：

```python
import os
import csv
import win32com.client

# 指定输入文件夹
input_dir = r'<您的输入文件夹>'

# 指定输出文件夹
output_dir = r'<您的输出文件夹>'

try:
    excelapp = win32com.client.Dispatch('Excel.Application')
    excelapp.Visible = False

    for root, dirs, files in os.walk(input_dir):
        for name in files:
            if name.endswith('.xls'):
                infile = os.path.join(root, name)
                filename = os.path.splitext(name)[0]

                wb = excelapp.Workbooks.Open(infile)

                # 另存为 CSV 文件
                outfile = os.path.join(output_dir, filename + '.csv')
                wb.SaveAs(outfile, FileFormat=6) ①

                print(outfile, '转换成功')

                wb.Close()

finally:
    excelapp.Quit()

print('全部转换完成')
```

其中第①行调用 wb.SaveAs() 时需要指定 FileFormat=6。

本例运行效果不再赘述。

### 8.2.3 示例15：按表拆分 Excel
有时需要把一个多表文件拆成多个独立文件。例如图 8-12 的“股票历史交易数据.xlsx”包含 4 个工作表，目标是每个表单独存成一个文件，文件名取自工作表名称。

示例实现代码如下：

```python
import os
import xlwings as xw

# 原始 Excel 文件路径
original_file = r"<您的输入文件路径>"
# 输出文件夹路径
output_folder = r"<您的输出文件夹>"
# 创建输出文件夹(如果不存在)
os.makedirs(output_folder, exist_ok=True) ①

# 打开原始 Excel 文件
workbook = xw.Book(original_file)

try:
    # 遍历每个工作表
    for sheet in workbook.sheets:
        # 获取工作表名字
        sheet_name = sheet.name
        # 创建新的 Excel 文件
        new_workbook = xw.Book()
        # 将当前工作表复制到新的 Excel 文件中
        sheet.api.Copy(Before = new_workbook.sheets[0].api)
        # 删除默认的 Sheet1 工作表
        new_workbook.sheets['Sheet1'].delete()
        # 保存新的 Excel 文件,文件名为工作表名字
        output_file = os.path.join(output_folder, f"{sheet_name}.xlsx")
        new_workbook.save(output_file)
        new_workbook.close()
finally:
    # 关闭原始 Excel 文件
    workbook.close()

print('完成。')
```

代码说明如下：

- 代码第①行用 os.makedirs 创建输出文件夹，exist_ok=True 表示已存在也不报错。
- 代码第②行用 xlwings 的 Book 函数打开源文件，得到工作簿对象。
- 代码第③行 sheet.api.Copy(Before=new_workbook.sheets[0].api) 将当前表复制到新工作簿的首位。
- 代码第④行删除新工作簿自带的默认 Sheet1。
- 代码第⑤行拼接输出文件完整路径，目录与表名组合。
- new_workbook = xw.Book() 创建新的工作簿对象用于存放当前表数据。
- 代码第⑥行把新工作簿保存到指定路径。
- 代码第⑦行关闭新工作簿对象。

### 8.2.4 示例16：合并 Excel 文件
拆分之外，合并也是常见需求。例如把图 8-13 输入目录下的多个 Excel 合并成一个文件，合并后每个工作表的名称与原文件名保持一致。

合并示例实现代码如下：

```python
import os
import xlwings as xw

# 输入目录路径
input_folder = r"<您的输入文件夹>"
# 合并后的输出文件路径
output_file = r"<合并后的输出文件路径>"
# 创建一个新的 Excel 工作簿对象
merged_workbook = xw.Book()

try:
    # 遍历输入目录中的所有文件
    for file_name in os.listdir(input_folder):
        file_path = os.path.join(input_folder, file_name)
        if os.path.isfile(file_path) and file_name.endswith(".xlsx"):
            # 打开每个 Excel 文件
            workbook = xw.Book(file_path)
            # 获取文件名(不包含扩展名)
            sheet_name = os.path.splitext(file_name)[0]
            # 将工作表复制到合并后的工作簿中
            workbook.sheets[0].api.Copy(Before=merged_workbook.sheets[0].api)
            # 将工作表名称设置为文件名
            merged_workbook.sheets[0].name = sheet_name
            # 关闭当前打开的 Excel 文件
            workbook.close()

    # 删除默认的 Sheet1 工作表
    merged_workbook.sheets['Sheet1'].delete()

    # 保存合并后的 Excel 文件
    merged_workbook.save(output_file)
    print("合并完成。")

finally:
    # 关闭合并后的 Excel 工作簿
    merged_workbook.close()
```

## 8.3 ChatGPT 助力 Excel 自动化的全流程打法

在 Excel 自动化工作中，ChatGPT 可以覆盖从想法到落地的多个环节：

1. 快速生成代码片段：遇到读取、写入、插入、删除等常规操作时，直接描述需求就能拿到可用代码，无需从零手写。
2. 给出编程思路：面对批量处理、跨文件汇总等复杂场景，可以请它梳理整体实现路径。
3. 定位代码错误：把报错信息贴给它，能分析原因并给出修正方向。
4. 协助代码优化：成稿后让它复盘，提出可读性、效率方面的改进建议。
5. 自动生成注释文档：让它为自动化脚本补充说明，提升后期维护体验。
6. 传递最佳实践：避免重复造轮子，学习业界常用的自动化做法。
7. 解答日常问题：遇到 Excel 使用或自动化相关疑问时，可直接对话获取答案。

总体来看，ChatGPT 能在思路梳理、代码编写、调试优化三个层面全程陪跑，让 Excel 自动化效率倍增。

### 8.3.1 示例17：借助 AI 快速产出写入代码

下面用一个具体场景说明如何让 ChatGPT 生成代码片段。

示例背景：
一位销售数据分析师每天需从多个 Excel 提取销售数据并统计月度总额，希望借助 ChatGPT 快速拿到读写数据的代码。

示例步骤：
用户先提问：
请给我一段在 Excel 里写入数据的代码片段。

ChatGPT 回复：
没问题，请告诉我写入的具体数据以及目标单元格位置。

用户补充：
数据是列表 [1,2,3,4,5]，要写到 A1:E1。

ChatGPT 回复：
下面是生成的写入代码，可把指定数据写入目标单元格：

```
import xlwings as xw
# 打开 Excel 文件
book = xw.Book("data.xlsx")
sheet = book.sheets[0]
# 要写入的数据
data = [1, 2, 3, 4, 5]
# 目标单元格位置
target_range = sheet.range("A1:E1")
# 写入数据到目标单元格
target_range.value = data
# 保存并关闭 Excel 文件
book.save()
book.close()
```

### 8.3.2 示例18：用 AI 拆解批量处理思路

下面演示如何让 ChatGPT 提供编程思路。

示例背景：
某跨国公司财务分析师每月要从各分公司 Excel 抽取销售数据汇总分析，希望得到可行的自动化思路。

示例步骤：
用户提问：
我需要批量处理多个 Excel 文件的数据，有什么实现思路？

ChatGPT 回复：
批量处理可以按以下思路展开：
1. 收集文件清单：用 Python 的 os 模块或其他文件操作库先获取待处理文件列表。
2. 打开与关闭：借助 xlwings 等库遍历清单，逐个打开文件，处理完及时关闭释放资源。
3. 读取与处理：在每个文件中按需读取区域，对数据做计算、筛选、格式转换等操作。
4. 结果归集：把处理结果暂存到列表或 DataFrame 中，按需导出到文件、数据库或其他目标。
5. 异常防护：加入错误处理，对文件缺失、格式异常等情况做兜底。

具体实现要结合实际需求细化，上面是一个基础框架，可在此上扩展。

用户回复：
谢谢，这个思路很有帮助，我会按此开发批量任务。

ChatGPT 回复：
很×！后续有其他需求随时交流，我会继续支持。

### 8.3.3 示例19：让 AI 解释报错原因

下面展示如何借助 ChatGPT 排查代码错误。

示例步骤：
用户提问：
我在写自动化代码时报错，不太清楚原因，能帮忙解读吗？

ChatGPT 回复：
可以，请把完整的错误信息发给我，我来分析原因并给出解决建议。

用户提供：
错误信息是「AttributeError: 'Sheet' object has no attribute 'rangs'」。

ChatGPT 回复：
这个报错说明 Sheet 对象上没有 rangs 属性，基本是属性名写错了，rangs 很可能是 range 的拼写错误，请检查代码中的相关位置。

用户回复：
明白了，确实是我把属性名写错了，多谢解释！

### 8.3.4 示例20：让 AI 优化已有代码

下面演示如何用 ChatGPT 改进代码。

示例步骤：
用户提问：
我已写好一个处理 Excel 数据的自动化脚本，感觉还能更好，请帮忙优化。

ChatGPT 回复：
好的，请把代码贴出来，我会审阅并给出优化建议。

用户提供：
这是我的自动化代码：
```
import xlwings as xw

# 打开 Excel 文件
book = xw.Book("data.xlsx")
sheet = book.sheets[0]

# 获取数据范围
data_range = sheet.range("A1:C10")

# 进行数据处理
for row in data_range.rows:
    row[2].value = row[0].value + row[1].value

# 保存并关闭 Excel 文件
book.save()
book.close()
```

ChatGPT 回复：
整体已经不错了。注意到你是用循环逐行处理，在 xlwings 中可以直接操作整块数据范围，避免逐行循环。优化示例如下：
```
import xlwings as xw

# 打开 Excel 文件
book = xw.Book("data.xlsx")
sheet = book.sheets[0]

# 获取数据范围
data_range = sheet.range("A1:C10")

# 进行数据处理(优化)
data_range.columns[2].value = data_range.columns[0].value + data_range.columns[1].value

# 保存并关闭 Excel 文件
book.save()
book.close()
```

优化后直接对数据范围的列进行向量化赋值，去掉了逐行循环，执行更快、代码更简洁。

用户回复：
非常感谢，这个版本简洁高效多了。

ChatGPT：
不客气，有其他问题随时说，我会继续协助。

## 8.4 本章要点回顾

本章围绕 Python 实现 Excel 自动化介绍了两种常用方式：
1. xlwings 库：提供贴合 Excel 对象模型的接口，方便完成读取、写入、插入、删除等基础操作，并涵盖单元格、区域、工作表等多种对象。
2. pywin32 库：可实现 Excel 文件的批量处理，如格式转换、拆分合并等，显著提升办公效率。

同时也展示了 ChatGPT 在 Excel 自动化中的多个应用场景。通过本章学习，可掌握 Python 驱动 Excel 自动化的核心方法，并体会到 AI 助手对开发效率的加速作用。

# 第 9 章 ChatGPT 协同 Word 自动化实战

ChatGPT 作为基于自然语言的模型，能作为 Word 自动化的得力助手。本章探讨如何把 ChatGPT 与 Word 结合，提升 Word 自动化的开发与应用效率。

在 Python 侧，常用的 Word 操作库有 python-docx 与 pywin32。

1. python-docx：最主流的跨平台文档处理库，支持 Windows 与 macOS，可处理 .docx 格式，不支持旧版 .doc。
2. pywin32：通过调用 Windows 底层 API 操作 Word，仅限 Windows 平台使用。

## 9.1 python-docx 入门与使用

python-docx 是创建、读取、修改 Word 文档的强大工具，提供丰富的 API，可操作段落、表格、标题等各类元素。

安装方式：
使用 pip 安装：
```
pip install python-docx
```

安装完成后即可在脚本中导入并开始操作文档。

### 9.1.1 python-docx 核心对象概览

库中对象采用层次结构管理，如图 9-1 所示。

主要对象说明：
- Document：代表整个 Word 文档，包含段落 Paragraph 或表格 Table 等。
- Paragraph：对应文档中的段落。
图 9-1 python-docx 库中对象层次结构
- Table：文档中的表格，内部包含行、列、单元格。
- Run：文本片段，携带字体、样式等信息，一个段落由多个 Run 组成。如图 9-2，①和②属于不同 Run，样式各异。
此外还有 Section、Style、Inline_shape 等对象。
图 9-2 python-docx 库中对象

### 9.1.2 示例1：打开并读取 Word 文档

python-docx 可打开已有文档并提取内容，图 9-3 为 “K-12 投资前景分析报告.docx” 的示例。

示例代码：
```
from docx import Document

document = Document(r'data/K-12 投资前景分析报告.docx')
all_paras = document.paragraphs

print('段落个数:', len(all_paras))

# 遍历所有段落
for para in all_paras:
    text = para.text
    # 去掉空白
    text = text.strip()
    # 如果是空白则跳过
    if text == '':
        continue
    print(text)

print("-------------------")
print('完成。')
```

运行后控制台输出：
段落个数：10
2020-2026全球与中国K-12学生信息系统市场现状及投资前景分析报告
...
完成。

代码要点：
- 第①行导入 Document 类。
- 第②行用 Document 打开指定路径的 Word 文件。
- 第③行通过 paragraphs 属性获取所有段落对象。
- 第④行遍历段落。
- 第⑤行用 para.text 读取文本。
- 第⑥行去除首尾空白字符。
- 第⑦行跳过空段落。

### 9.1.3 示例2：向 Word 文档写入数据

本节演示如何用 python-docx 向文档写入文本和图片等内容，如图 9-4 所示。

示例代码：
```
from docx import Document
from docx.shared import Inches

# 创建文档对象
document = Document()

# 添加段落
document.add_heading('北京房价相关信息', level = 1)
# 添加段落
document.add_paragraph('第 45 周北京新房成交排名 TOP20 热盘中限竞房占近半', style = 'Intense Quote')
# 添加图片
document.add_picture('data/北京各城区最高房价柱状图.png', width = Inches(6.0))

# 保存文件
document.save(r'data/temp.docx')

print("保存文档成功.")
```

要点说明：
- 第①行创建空白文档对象，构造函数无参数。
- 第②行用 add_heading 在文档末尾追加标题段落，level 控制标题层级，取值 0～9。

- 第③行负责新建一段普通段落，style 参数把段落样式指定为 'Intense Quote'，即明显引用；明显引用和标题 1 都属于 Word 自带的内置样式，参见图 9-5。
- 第④行调用 add_picture 在文档中追加图片，第一个参数是图片的路径，第二个参数 width 控制宽度，Inches(6.0) 表示宽度设为 6 英寸。
- 第⑤行通过文档对象执行保存，把文件写出。

图 9-5 Word 内置段落样式

### 9.1.4 示例3：在 Word 文档中插入表格
前一节用 python-docx 演示了写入文本和图片，这里演示如何程序化地在 Word 里生成一个表格，效果如图 9-6。

示例代码如下：

```
from docx import Document

# 打开文档
document = Document('data/temp.docx')

records = [
    ('西城', 201991),
    ('海淀', 129508),
    ('东城', 130969),
    ('朝阳', 119170),
    ('丰台', 79933),
]

# 中等深浅网格 1 - 着色 5
table = document.add_table(rows=1, cols=2, style='Medium Grid 1 Accent 5')
hdr_cells = table.rows[0].cells
hdr_cells[0].text = '城区'
hdr_cells[1].text = '单价(元)'

for addr_dist, price in records:
    row_cells = table.add_row().cells
    row_cells[0].text = addr_dist
    row_cells[1].text = str(price)

document.save('data/temp.docx')
print("保存文档成功.")
```

图 9-6 添加表格

| 城区 | 单价（元） |
|---|---|
| 西城 | 201991 |
| 海淀 | 129508 |
| 东城 | 130969 |
| 朝阳 | 119170 |
| 丰台 | 79933 |

代码说明如下：

- 第①行先声明 records 列表，用来存放表格的数据，每个元素是一个元组，对应表格中的一行。
- 第②行通过 doc 对象的 add_table 在文档末尾追加表格，rows 控制行数，col 控制列数，style 指定表格样式为 'Medium Grid 1 Accent 5'，即 Word 内置的“中等深浅网格 1-着色 5”。
- 第③行用 table.rows 取到所有行，table.rows[0] 取出第一行，再用 table.rows[0].cells 获取该行的全部单元格对象。

### 9.1.5 示例4：设置文档默认样式
9.1.3 节里的段落仍在使用默认字体，观感一般。python-docx 可以通过文档对象的 styles 属性修改默认字体。

示例代码如下：

```python
from docx import Document
from docx.oxml.ns import qn
from docx.shared import Inches
from docx.shared import Pt

# 创建文档对象
document = Document()
# 定义样式
style = document.styles['Normal'] ①
# 设置英文字体
style.font.name = 'Times New Roman' ②
style.font.size = Pt(12)
# 设置中文字体
style.element.rPr.rFonts.set(qn('w:eastAsia'), '幼圆') ③
records = [
    ('西城', 201991),
    ('海淀', 129508),
    ('东城', 130969),
    ('朝阳', 119170),
    ('丰台', 79933),
]

# 添加段落
p = document.add_paragraph('第 45 周北京新房成交排名 TOP20 热盘中限竞房占近半')

# 添加图片
document.add_picture('data/北京各城区最高房价柱状图.png', width=Inches(6.0))

# 添加表格
table = document.add_table(rows=1, cols=2, style='Medium Grid 1 Accent 5')
hdr_cells = table.rows[0].cells
hdr_cells[0].text = '城区'
hdr_cells[1].text = '单价(元)'

for addr_dist, price in records:
    row_cells = table.add_row().cells
    row_cells[0].text = addr_dist
    row_cells[1].text = str(price)
# 保存文件
document.save(r'data/temp.docx')
print("保存文档成功.")
```

代码说明如下：

- 第①行拿到文档的默认样式对象。
- 第②行设置英文字体名称，name 属性必须填写。
- 第③行把中文字体设为“幼圆”，东亚字体要写成 style.element.rPr.rFonts.set(qn('w:eastAsia'), '幼圆') 这种形式才生效。

### 9.1.6 示例5：修改已有文档样式
下面再给一个修改已存在文档样式的例子，实现代码如下：

```python
from docx import Document
from docx.oxml.ns import qn
from docx.shared import Pt

document = Document(r'data/K-12 投资前景分析报告.docx')
all_paras = document.paragraphs

# 遍历所有段落
for paragraph in document.paragraphs:  # ①

    # 遍历段落中 runs 对象
    for run in paragraph.runs:  # ②
        run.font.size = Pt(12)  # ③
        run.font.name = 'Times New Roman'

        run.element.rPr.rFonts.set(qn('w:eastAsia'), '微软雅黑')  # ④

document.save(r'data/K-12 投资前景分析报告 2.docx')

print("结束")
```

代码说明如下：

- 第①行获取文档中全部段落并逐个遍历。
- 第②行在每个段落内部遍历所有的 Run 对象。
- 第③、④行为 Run 对象设置字体、字号等格式属性。

## 9.2 使用 pywin32 库处理 Word 文档
第 8 章已用 pywin32 转换过 Excel 格式，同样可以用来批量处理 Word，本节介绍几个典型用法。

### 9.2.1 示例6：批量把 .doc 转换为 .docx
工作中经常需要把老版 .doc 文件转成 .docx，下面是实现思路：

```python
import os
from win32com import client as wc

# 查找dir目录下ext后缀名的文件列表
# dir参数是文件所在目录，ext参数是文件后缀名
def findext(dir, ext):
    allfile = os.listdir(dir)
    # 返回过滤器对象
    files_filter = filter(lambda x: x.endswith(ext), allfile)
    # 从过滤器对象提取列表
    list2 = list(files_filter)
    return list2

if __name__ == '__main__':
    # 设置输入目录
    indir = r'C:\Users\tony\OneDrive...\in'
    # 设置输出目录
    outdir = r'C:\Users\tony\OneDrive\...\out'
    wordapp = wc.Dispatch("Word.Application")
    # 查找indir目录中所有.doc文件
    list2 = findext(indir, '.doc')
    for name in list2:
        infile = os.path.join(indir, name)
        name = name.replace('.doc', '.docx')
        outfile = os.path.join(outdir, name)
        document = wordapp.Documents.Open(infile)
        document.SaveAs(outfile, FileFormat=12)
        print(outfile, "转换OK。")
        document.Close(0)
    wordapp.Quit()
print("完成。")
```

代码说明如下：

- 第①行从 pywin32 库导入 win32com 模块。
- 第②行定义 findext 函数，按指定目录查找指定后缀的文件，dir 为目录，ext 为后缀名。
- 第③行用 os 模块的 listdir(dir) 获取目录下的所有文件名。
- 第④行用 filter 按后缀过滤 allfile，返回过滤器对象。
- 第⑤行把过滤器转为列表，因为 filter 本身不是列表。
- 第⑥行通过 Dispatch 创建 Word 应用程序对象。
- 第⑦行打开 Word 文件，注意打开路径需用绝对路径。
- 第⑧行调用 SaveAs 另存为 .docx，outfile 为目标文件名，FileFormat 为 12 表示 wdFormatXMLDocument 即 .docx 格式，保存路径也需绝对路径。
- 第⑨行关闭文件，Close 参数 0 表示不保存改动，因为只是另存。
- 第⑩行退出 Word 应用程序。

![](img/817f9d0d19c4377d32975098402bcdd3_186_0.png)

另存时常量 12 代表 docx 格式，这个常量的定义在 VBA 中，可以在如图 9-7 的页面查到对应关系。

![](img/817f9d0d19c4377d32975098402bcdd3_186_1.png)

图 9-7 常量与文件格式的对应关系

### 9.2.2 示例7：用模板批量生成证书
假设每月要根据绩效结果给员工生成获奖证书，希望用模板自动化。准备好“证书模板.docx”，里面已做好排版，只缺少获奖人姓名、日期、颁奖人等信息。思路是从 Excel 读取姓名，填入模板，为每人生成独立证书，如图 9-8。

![](img/817f9d0d19c4377d32975098402bcdd3_187_0.png)

生成 Word 模板常用 python-docx-template 库，使用前要完成两件事。

#### 1. 安装python-docx-template库
可通过 pip 或 PyCharm 安装，pip 命令如下：

```
pip install docxtpl
```

#### 2. 准备模板文件
模板不仅要设计好，还要把动态内容标记出来。如图 9-9，变量位置用 {{变量}} 标识，程序会把值传入，表达式也会被计算后填入。

![](img/817f9d0d19c4377d32975098402bcdd3_188_0.png)

示例实现代码如下：

```python
from datetime import datetime

import xlwings as xw
from docxtpl import DocxTemplate

# 读取 Excel 文件获得员工数据
def readdata():
    # 设置程序不可见运行
    app = xw.App(visible=False, add_book=False)
    f = r'data/员工信息.xlsx'
    print('打开文件：', f)
    wb = app.books.open(f)

    # 通过工作表名返回工作表对象
    sheet1 = wb.sheets[0]

    # 选择姓名单元格区域
    rng = sheet1.range('B2').expand('down')

    # 单元格区域数组
    names = rng.value

    # 关闭工作簿对象
    wb.close()
    # 退出 Excel 应用程序
    app.quit()

    print('关闭文件:', f)
    # 返回姓名列表
    return names

if __name__ == '__main__':
    # 获得员工姓名列表
    emps = readdata()
    date = datetime.now().strftime('%Y-%m-%d')
    # 设置输出目录
    outdir = r'C:\Users\tony\OneDrive\...\out'

    for emp in emps:
        tpl = DocxTemplate('data/证书模板.docx')

        # 传递给模板的字典对象
        context = {}
        context['name'] = emp
        context['title'] = '关总经理'
        context['date'] = date

        # 渲染模板生成 docx 文档
        tpl.render(context)

        file = ('{0}/证书-{1}.docx'.format(outdir, emp))
        tpl.save(file)
        print('{0}证书生成完成.'.format(emp))

print('完成。')
```

代码说明如下：

- 第①行从 docxtpl 导入 DocxTemplate。
- 第②行定义 readdata，从 Excel 读取员工信息并返回姓名列表。
- 第③行获取当前日期，按 YYYY-MM-DD 格式整理。
- 第④行遍历员工列表 emps。
- 第⑤行用模板文件路径创建 DocxTemplate 对象。
- 第⑥行新建字典 context，用来向模板传递参数。
- 第⑦、⑧行把数据按键写入字典，键名需与模板中 {{变量}} 名称一致。
- 第⑨行调用 render 渲染模板，生成文档对象。
- 第⑩行把渲染后的文档保存为 docx 文件。

运行后会在输出目录生成多个 docx，如图 9-10。打开其中一个文件效果如图 9-11。

![](img/817f9d0d19c4377d32975098402bcdd3_190_0.png)

### 9.2.3 示例8：批量统计文档页数和字数
需要统计大量分公司 Word 文件的页码和字数，手动打开统计效率低且易错。

![](img/817f9d0d19c4377d32975098402bcdd3_191_0.png)

图 9-11 生成好的 docx 文件

借助 pywin32 可以读取这些信息，提升效率。

示例实现代码如下：

```python
import os
from win32com.client import Dispatch

# 查找 dir 目录下 ext 后缀名的文件列表
# dir 参数是文件所在目录，exts 参数是指定文件后缀名列表
def findext(dir, exts):
    allfile = os.listdir(dir)
    # 返回过滤器对象
    files_filter = filter(lambda x: os.path.splitext(x)[1] in exts, allfile)
    # 从过滤器对象提取列表
    list2 = list(files_filter)
    return list2  # 返回过滤后条件文件名

if __name__ == '__main__':
    # 设置输入目录
    indir = r'C:\Users\tony\OneDrive\...\test\in'
    # 通过 findext 函数查找指定目录下的 .doc 和 .docx 文件
    list2 = findext(indir, ['.doc', '.docx'])

    # 打开 Word 文档
    wordapp = Dispatch('Word.Application')
    wordapp.Visible = False

    # 遍历文件列表
    for name in list2:
        infile = os.path.join(indir, name)          # 将目录和文件名连接起来
        document = wordapp.Documents.Open(infile)    # 打开 Word 文件
        # 重新编排页面
        document.Repaginate()
        pagenum = document.ComputeStatistics(2)
        wordnum = document.ComputeStatistics(6)
        # 2    页数
        # 1    行数
        # 3    字符数
        # 4    段落数
        # 6    亚洲语言字符数
        # 0    单词数

        print('文{0}文件:页数是:{1},字符数:{2}'.format(name, pagenum, wordnum))

        document.Close()                            # 关闭 Word 文档

    wordapp.Quit()                                  # 退出 Word 应用
print('完成。')
```

代码说明如下：

- 第①行负责从 pywin32 引入 win32com 模块。
- 第②行声明了 findext 函数。注意与 7.2.1 节的同名函数不同，本版支持多类型文件筛选，而 7.2.1 节的版本仅能识别一种固定格式。这里的 dir 参数指文件所在目录，exts 参数是允许的多个后缀名列表。
- 第③行逻辑稍显复杂。os.path.splitext(x) 用来拆分文件名与后缀，会返回一个列表，第 1 个元素是文件名，第 2 个元素是后缀。因此 os.path.splitext(x)[1] 取出的是后缀名，而 os.path.splitext(x)[1] in exts 就是判断该后缀是否在 exts 列表之中。
- 第④行打开指定的 Word 文档。
- 第⑤行让 Word 以不可见方式运行。
- 第⑥行调用 Repaginate 重新排版。因为分页、样式调整等原因页数可能变动，统计前最好先重排。
- 第⑦行通过 ComputeStatistics 获取文档页数，其中参数 2 表示统计页数。这些常量来自 VBA，类似的还有 4 代表段落数、6 代表亚洲语言字符数等。

言字符数等。

运行示例代码后，控制台输出如下：

文 K-12 投资前景分析报告.doc 文件:页数是:2,字符数:207

文 K-12 投资前景分析报告.docx 文件:页数是:2,字符数:207

文 temp.docx 文件:页数是:1,字符数:44

文第一章 Linux 简介.doc 文件:页数是:1,字符数:722

文第一章 Linux 简介.docx 文件:页数是:1,字符数:722

文第一章 Linux 简介 2.doc 文件:页数是:1,字符数:722

文证书模板.docx 文件:页数是:1,字符数:29

完成。

### 9.2.4 示例9：Word 批量转 PDF 方案
假设手头有大量 .doc 和 .docx 文件需要统一转为 PDF，手动操作费时。借助 Python 的 pywin32 库操控 Word 应用，即可实现自动保存为 PDF，省时省力。

示例实现代码如下：

```python
import os
from win32com import client as wc  # 导入模块

# 查找 dir 目录下 ext 后缀名的文件列表
# dir 参数是文件所在目录，exts 参数是指定文件后缀名列表

def findext(dir, exts):
    allfile = os.listdir(dir)
    # 返回过滤器对象
    files_filter = filter(lambda x: os.path.splitext(x)[1] in exts, allfile)
    # 从过滤器对象提取列表
    list2 = list(files_filter)
    return list2  # 返回过滤后条件文件名

if __name__ == '__main__':
    # 设置输入目录
    indir = r'C:\Users\tony\...\data\test\in'
    # 设置输出目录
    outdir = r'C:\Users\tony\...\test\out'

    wordapp = wc.Dispatch("Word.Application")  # 创建 Word 应用程序对象

    # 查找 indir 目录中所有 .doc 文件
    # 通过 findext 函数查找指定目录下的 .doc 和 .docx 文件
    list2 = findext(indir, ['.doc', '.docx'])

    for name in list2:
        # 将目录和文件名连接起来
        infile = os.path.join(indir, name)
        # 将文件后缀名.docx替换为.pdf
        name = name.replace('.docx', '.pdf')
        # 将文件后缀名.doc替换为.pdf
        name = name.replace('.doc', '.pdf')
        outfile = os.path.join(outdir, name)
        document = wordapp.Documents.Open(infile)  # 打开Word文件
        document.SaveAs(outfile, FileFormat=17)  # ①
        print(outfile, "转换OK。")
        document.Close(0)  # 关闭Word文件
    wordapp.Quit()  # 退出Word应用
    print("完成。")
```

代码说明如下：

- 第①行中 SaveAs 用于把 Word 文档另存为 PDF，格式参数为 17。不同格式对应的常量可参考图 9-7。

## 9.3 ChatGPT 助力 Word 自动化实战技巧
ChatGPT 能在 Word 自动化流程中提供全面支持，提升效率与准确度。常见应用场景包括：

1. 生成代码片段：根据需求快速产出操作 Word 文档的代码，如新建、打开、保存、插入文字、图片、表格等。
2. 提供编程思路：遇到复杂需求时可请教实现思路，比如批量处理多文档、文本查找替换等。
3. 解释代码错误：把报错信息丢给 ChatGPT，可快速定位原因并给出修复建议。
4. 优化代码：完成初版后请其评审，帮助重构、提效、提升健壮性。
5. 生成注释文档：自动为自动化脚本生成函数说明、参数解释和逻辑概述，增强可读性与可维护性。
6. 提供最佳实践：学习处理段落、样式、表格等的高效写法，提升自动化质量。
7. 回答 Word 相关问题：使用或开发过程中遇到疑问，可直接对话获取解答。

总体来看，从写代码到排错、优化、文档化，ChatGPT 都能全程陪跑，是 Word 自动化的得力助手。

### 9.3.1 示例10：让 ChatGPT 写 Word 代码片段
背景：一位办公室职员需频繁在 Word 里插入固定文本和表格，想借助 ChatGPT 快速生成代码。

对话过程：

用户提问：我需要一段代码在 Word 文档中插入指定文本和一个表格，可以帮忙生成吗？

ChatGPT 回复：可以，请提供要插入的文本内容以及表格的行列数。

用户补充：文本是 “Hello, ChatGPT!” ，表格为 3 行 4 列。

ChatGPT 回复：好的，以下是生成的片段：

```python
import docx

# 打开 Word 文档
doc = docx.Document("document.docx")

# 插入文本
doc.add_paragraph("Hello, ChatGPT!")

# 插入表格
table = doc.add_table(rows=3, cols=4)

# 设置表格内容
table.cell(0, 0).text = "Row 1, Column 1"
table.cell(0, 1).text = "Row 1, Column 2"
table.cell(0, 2).text = "Row 1, Column 3"
table.cell(0, 3).text = "Row 1, Column 4"
table.cell(1, 0).text = "Row 2, Column 1"
table.cell(1, 1).text = "Row 2, Column 2"
table.cell(1, 2).text = "Row 2, Column 3"
table.cell(1, 3).text = "Row 2, Column 4"
table.cell(2, 0).text = "Row 3, Column 1"
table.cell(2, 1).text = "Row 3, Column 2"
table.cell(2, 2).text = "Row 3, Column 3"
table.cell(2, 3).text = "Row 3, Column 4"

# 保存并关闭 Word 文档
doc.save("document.docx")
```

该片段会打开 document.docx，在文档中写入指定文本并插入 3×4 表格后保存，可按需修改。

### 9.3.2 示例11：ChatGPT 给出 Word 自动化思路
背景：研究员希望自动化实验报告生成，从数据源提取数据、绘图并写入 Word。

对话过程：

用户提问：我想自动化生成实验报告，不知道如何从数据源提取数据并生成统计图表，能给点思路吗？

ChatGPT 回复：可以考虑以下思路：

1. 提取数据：
   - 用 Pandas 读取 CSV 或 Excel 等数据源文件。
   - 借助 Pandas 的筛选与处理功能提取所需数据。
2. 生成统计图表：
   - 用 Matplotlib 绘制柱状图、折线图、散点图等。
   - 先用 Pandas 预处理数据，再交给 Matplotlib 绘图。
3. 插入分析结果：
   - 用 python-docx 打开并操作 Word 文档。
   - 将图表插入文档合适位置，并配上文字分析。

具体实现可根据数据格式、分析需求和报告样式调整。如有细节问题可继续提问。

### 9.3.3 示例12：ChatGPT 帮你定位 Word 代码错误
背景：开发人员在编写 Word 自动化代码时报错，希望通过对话获得解释与修复建议。

对话过程：

用户提问：我的 Word 自动化代码报错了，代码如下：

```python
import docx
def insert_text(document, text):
    paragraph = document.add_paragraph(text)
    return paragraph

doc = docx.Document("document.docx")
insert_text(doc, "Hello, ChatGPT!")
doc.save("document.docx")
```

运行时错误：

```
AttributeError: 'Document' object has no attribute 'add_paragraph'
```

这是什么意思？

ChatGPT 回复：错误提示 Document 对象没有 add_paragraph 属性。你应使用 Document 对象的 add_paragraph() 方法来添加段落。修改后如下：

```python
import docx

def insert_text(document, text):
    paragraph = document.add_paragraph(text)
    return paragraph

doc = docx.Document("document.docx")
insert_text(doc, "Hello, ChatGPT!")
doc.save("document.docx")
```

修改后即可正常运行。

用户反馈：非常感谢，已按建议修改，代码现在可以正常运行了。

### 9.3.4 示例13：ChatGPT 优化 Word 自动化代码
对话过程：

用户提问：我写了以下代码插入文本并设置字体样式，能否优化？

```python
import docx

def insert_text(document, text, font_name, font_size):
    paragraph = document.add_paragraph(text)
    run = paragraph.runs[0]
    font = run.font
    font.name = font_name
    font.size = docx.shared.Pt(font_size)
    return paragraph

doc = docx.Document("document.docx")
insert_text(doc, "Hello, ChatGPT!", "Arial", 12)
doc.save("document.docx")
```

ChatGPT 回复：代码已能实现功能，建议做如下改进：

1. 将字体样式的设置抽离成独立函数，方便复用。

2. 建议引入异常捕获机制，用来拦截运行中可能出现的异常并给出更友好的错误提示。
3. 建议采用更直观、更易维护的变量命名，让后续阅读者能迅速理解代码意图。

下面是优化后的代码片段：

```python
import docx

def insert_text(document, text, font_name, font_size):
    paragraph = document.add_paragraph(text)
    set_font_style(paragraph, font_name, font_size)
    return paragraph

def set_font_style(paragraph, font_name, font_size):
    run = paragraph.runs[0]
    font = run.font
    font.name = font_name
    font.size = docx.shared.Pt(font_size)

try:
    doc = docx.Document("document.docx")
    insert_text(doc, "Hello, ChatGPT!", "Arial", 12)
    doc.save("document.docx")
except Exception as e:
    print("An error occurred:", str(e))
```

经过这些调整，代码的可读性、可维护性和鲁棒性都得到了提升。

用户随后向 ChatGPT 发出如下反馈：

非常感谢你的优化建议！我会按照你给出的建议进行修改，以提高代码的质量和效率。

## 9.4 本章要点回顾

本章围绕用 Python 实现 Word 自动化展开，并介绍了 ChatGPT 在其中的配合价值。

起初借助 python-docx 来掌握 Word 的基础操作，包括打开文件、读取文本、写入内容、插入表格以及设置格式样式，为后续搭建自动化流程打下基础。

随后使用 pywin32 来完成 Word 文档的批量化处理，涵盖格式转换、基于模板批量生成文件以及文档信息统计等任务，显著提升了办公效率。

整个学习过程中 ChatGPT 的作用非常突出：

1. 能够迅速生成 Word 自动化的代码片段，辅助编程实现。
2. 能够提供思路与方法，帮助设计自动化流程。
3. 能够解释报错原因，协助调试程序。
4. 能够对代码进行优化，提升稳定性与运行效率。

总的来说，学完本章即可掌握 Python 实现 Word 自动化的核心方法，并善用 ChatGPT 这位“编程搭档”，将工作效率和交付质量推向新水平。

# 第 10 章 ChatGPT 赋能 PPT 自动化

ChatGPT 可以作为强有力的助手来达成 PPT 自动化的目标。本章将介绍如何把 ChatGPT 与 PPT 开发结合起来，在自动化构建与应用中发挥关键作用。

在 Python 生态中，操作 PPT 文件的常用库主要有 python-pptx 和 pywin32。

1. python-pptx：目前使用最广泛的 PPT 处理库，优点是跨平台，兼顾 Windows 与 macOS，缺点是仅支持 .pptx 新格式，无法处理旧版 .ppt 文件。
2. pywin32：通过调用 Windows 底层 API 来操作 PPT，因而只能在 Windows 环境下使用，不具备跨平台能力。

## 10.1 python-pptx 入门：打开 PowerPoint 文件

python-pptx 是一个用于创建、修改和读取 Microsoft PowerPoint 文件的 Python 库，提供了丰富的接口，让开发者能够在 Python 中灵活处理演示文稿。

开始使用前需要先安装该库，可通过 pip 包管理器执行如下命令：

```
pip install python-pptx
```

需要注意的是，当前版本的 python-pptx 对 Python 3.11 的兼容性还不完善，在该版本上加载时容易出错。建议切换到 Python 3.10 或更早版本运行。

### 10.1.1 PPT 核心概念速览

在动手进行 PPT 自动化前，先熟悉 PowerPoint 的基本结构会更高效。常见概念如下：

1. PPT 文档对象，一个 PowerPoint 实例可同时打开多个文档。
2. 幻灯片对象，每个 PPT 文档由多个幻灯片构成，如图 10-1 所示。
3. 幻灯片母版，通过母版可统一更换主题，一个文档可包含多个母版。
4. 幻灯片母版版式，如图 10-2 所示，母版中包含若干版式，也称布局，用于生成页面。
5. 占位符，如图 10-2 所示，版式中预置多个占位符，创建幻灯片时由实际内容填充。
6. 形状，可理解为 PPT 中的图层，包含图片、边框、表格、图表等元素。图 10-1 中主标题和副标题即为两种形状，母版中的占位符也属于形状的一种。

![](img/817f9d0d19c4377d32975098402bcdd3_202_0.png)

![](img/817f9d0d19c4377d32975098402bcdd3_203_0.png)

图 10-2 PPT 中幻灯片母版视图

### 10.1.2 python-pptx 常用对象一览

python-pptx 提供了与 PPT 概念一一对应的对象，主要包括：

- Presentation：PPT 文档对象。
- Slide：幻灯片对象。
- Slides：幻灯片集合。
- SlideLayout：母版中的一个版式。
- SlideShape：形状对象。
- SlideShapes：幻灯片形状集合。
- SlidePlaceholder：占位符对象。
- Table：表格对象。
- Chart：图表对象。

### 10.1.3 示例1：从零创建 PPT 文件

本节演示如何借助 python-pptx 创建一份新的演示文稿，示例代码如下：

```python
from pptx import Presentation

# 创建空 PPT 文档对象
ppt = Presentation()

# 选择母版中的第一个幻灯片版式,它是带有标题的版式
title_slide_layout = ppt.slide_layouts[0]

# 在 PPT 添加一页幻灯片
slide = ppt.slides.add_slide(title_slide_layout)

# 获得幻灯片的标题
title = slide.shapes.title
# 获得幻灯片中第 2 个占位符
subtitle = slide.placeholders[1]
# 设置标题文本
title.text = 'Hello, World! '

# 设置副标题文本
subtitle.text = '这是 python-pptx 库'

f = r'data/temp.pptx'
# 保存文件
ppt.save(f)

print('完成。')
```

代码说明如下：

- 第①行从 pptx 模块导入 Presentation 类。
- 第②行调用 Presentation 类生成空的 PPT 文档对象。该类提供无参构造函数用于新建文档，有参构造函数用于打开已有文件，本例使用无参形式。
- 第③行从母版中选取第一个版式作为模板。ppt.slide_layouts 可获取全部版式集合，默认母版共 11 个版式，如图 10-3 所示，第一个版式包含主标题和副标题。
- 第④行基于母版第 2 个版式新建一页幻灯片并加入文档，返回值 slide 为幻灯片对象。
- 第⑤行获取幻灯片的标题，占位符 SlidePlaceholder 同时也是形状对象。
- 第⑥行获取幻灯片中第二个占位符，即副标题形状。
- 第⑦行通过形状的 text 属性写入标题文本。
- 第⑧行通过形状的 text 属性写入副标题文本。
- 第⑨行保存生成的 PPT 文件。

![](img/817f9d0d19c4377d32975098402bcdd3_205_0.png)

图 10-3 默认母版的 11 个版式

### 10.1.4 示例2：在现有的演示文稿中新增幻灯片

本节介绍如何在已有 PPT 文档（如图 10-4 所示）中追加两页幻灯片。第一页仅含标题，第二页含标题与一张图片。

![](img/817f9d0d19c4377d32975098402bcdd3_205_1.png)

图 10-4 带图片的幻灯片

实现代码如下：

```python
from docx.shared import Cm
SLD_LAYOUT_TITLE_ONLY = 5  # 只有标题的版式

from pptx import Presentation

f = r'data/temp.pptx'
# 打开 PPT 文档
ppt = Presentation(f)

# 选择母版中的第六个幻灯片版式,只有标题的版式
title_slide_layout = ppt.slide_layouts[SLD_LAYOUT_TITLE_ONLY]

# 添加一页幻灯片
print('添加一页幻灯片。')
slide = ppt.slides.add_slide(title_slide_layout)

# 获得幻灯片的标题
title = slide.shapes.title

# 设置标题文本
title.text = '这是一个只有标题的版式'

image_url = r'data\北京各城区最高房价柱状图.png'

# 在幻灯片中添加图片
slide.shapes.add_picture(image_file=image_url,
                        left=Cm(0),
                        top=Cm(4.54),
                        width=Cm(25.4),
                        height=Cm(12.7))

# 保存文件
ppt.save(f)
print('完成。')
```

代码说明如下：

- 第①行创建 PPT 文档对象，注意此时 Presentation 构造函数带参数，参数为要打开的文件路径。
- 第②行选择母版中第 6 个版式，该版式为仅含标题的布局。
- 第③行新增一页幻灯片。
- 第④行通过幻灯片形状集合 slide.shapes 的 add_picture 方法插入图片，image_file 指定图片路径，left、top、width、height 分别控制位置与大小，Cm 表示厘米单位。

### 10.1.5 示例3：为幻灯片插入表格

本节演示如何在 PPT 中添加表格。示例如图 10-5 所示，在文档末尾追加一页仅含标题的幻灯片，并在其中插入表格。

示例代码如下：

![](img/817f9d0d19c4377d32975098402bcdd3_207_0.png)

图 10-5 添加表格

```python
from docx.shared import Cm
SLD_LAYOUT_TITLE_ONLY = 5  # 只有标题的版式

from pptx import Presentation

f = r'data/temp.pptx'
# 打开 PPT 文档
ppt = Presentation(f)

# 选择母版中的第六个幻灯片版式,只有标题的版式
title_slide_layout = ppt.slide_layouts[SLD_LAYOUT_TITLE_ONLY] ①

# 添加一页幻灯片
print('添加一页幻灯片。')
slide = ppt.slides.add_slide(title_slide_layout) ②

# 获得幻灯片的标题
title = slide.shapes.title

# 设置标题文本
title.text = '北京各城区最高房价'
# 在幻灯片中添加表格
shape = slide.shapes.add_table(rows=6, ③
cols = 2,
left = Cm(3.89),
top = Cm(5.21),
width = Cm(17.63),
height = Cm(10.66))

table = shape.table

records = [
    ('西城', 201991),
    ('海淀', 129508),
    ('东城', 130969),
    ('朝阳', 119170),
    ('丰台', 79933),
]

hdr_cells = table.rows[0].cells
hdr_cells[0].text = '城区'
hdr_cells[1].text = '单价(元)'

for index, value in enumerate(records):
    # 拆解元组中元素
    addr_dist, price = value
    row_cells = table.rows[index + 1].cells
    row_cells[0].text = addr_dist
    row_cells[1].text = str(price)

# 保存文件
ppt.save(f)

print("Game Over")
```

代码说明如下：

- 第①行选取仅含标题的版式。
- 第②行添加一页新幻灯片。
- 第③行调用 slide.shapes.add_table 添加表格，rows 与 cols 分别设定行列数，left、top、width、height 控制位置尺寸，Cm 为单位。
- 第④行通过 shape 对象的 table 属性获取创建的表格对象。
- 第⑤行准备表格数据源 records，为一个列表，每个元素为对应一行的元组。
- 第⑥行获取表格首行所有单元格，table.rows[0] 为表头行，.cells 获取该行单元格。
- 第⑦行遍历 records 列表，enumerate 同时提供索引 index 与值 value。
- 第⑧行对 value 进行解包，将元组元素分别赋给 addr_dist 与 price。
- 第⑨行获取当前数据行单元格，table.rows[index + 1] 获取第 index+1 行，因首行为表头，数据从第二行开始写入。

### 10.1.6 示例4：向幻灯片添加图表

本节说明如何通过代码在幻灯片中插入图表，示例如图 10-6 所示，在文档中追加一页仅含标题的幻灯片并加入图表。

![](img/817f9d0d19c4377d32975098402bcdd3_209_0.png)

示例代码如下：

```python
from docx.shared import Cm
from pptx.chart.data import CategoryChartData
from pptx.enum.chart import XL_CHART_TYPE
SLD_LAYOUT_TITLE_ONLY = 5  # 只有标题的版式
from pptx import Presentation

f = r'data/temp.pptx'
# 打开PPT文档
ppt = Presentation(f)

# 选择母版中的第六个幻灯片版式，只有标题的版式
title_slide_layout = ppt.slide_layouts[SLD_LAYOUT_TITLE_ONLY]
# 添加一页幻灯片
print('添加一页幻灯片。')
slide = ppt.slides.add_slide(title_slide_layout)

# 获得幻灯片的标题
title = slide.shapes.title
# 设置标题文本
title.text = '北京各城区最高房价'
chart_data = CategoryChartData()

# 设置 x轴数据
chart_data.categories = ['西城', '海淀', '朝阳', '丰台', '东城']
# 设置 y轴数据
chart_data.add_series('平均价', (201991, 129508, 130969, 119170, 79933))
# 获得图表对象
shape = slide.shapes.add_chart(chart_type=XL_CHART_TYPE.COLUMN_CLUSTERED,
                               x=Cm(3.89),
                               y=Cm(5.21),
                               cx=Cm(17.63),
                               cy=Cm(10.66),
                               chart_data=chart_data)
agechart = shape.chart
# x轴对象
x_axis = agechart.category_axis

# x轴标题
x_axis_title = x_axis.axis_title
# 设置x轴标题
x_axis_title.text_frame.text = '城区'

# y轴对象
y_axis = agechart.value_axis

# y轴标题
y_axis_title = y_axis.axis_title
# 设置 y轴标题
y_axis_title.text_frame.text = '单价(元)'

# 保存文件
ppt.save(f)

print('完成。')
```

下面对这段生成柱状图的流程做逐行拆解：

- 第①行调用 CategoryChartData() 来实例化数据工厂，并由此得到一张可供图表读取的表格数据对象。
- 第②行负责组织将作为横坐标使用的序列。
- 第③行负责组织将作为纵坐标使用的序列。
- 第④行通过 slide.shapes.add_chart 向幻灯片里添加图表，其中 chart_type 决定图表样式，这里赋值 XL_CHART_TYPE.COLUMN_CLUSTERED 即为簇状柱形图；x、y 指定图表左上角的定位坐标，cx、cy 指定右下角的定位坐标，chart_data 则传入前面构造好的表格数据。
- 第⑤行借助 shape.chart 取回本次创建的图表对象。
- 第⑥行通过该图表对象的 category_axis 属性拿到横轴对象。
- 第⑦行经由轴对象的 axis_title 属性拿到横轴标题对象。
- 第⑧行对横轴标题进行文字赋值。

![](img/817f9d0d19c4377d32975098402bcdd3_211_0.png)

python-pptx 在向 PPT 中写入图表时，会调用到 xlsxwriter 作为底层依赖。先执行 pip 安装该依赖即可。

```
pip install xlsxwriter
```

## 10.2 用 pywin32 批量操控 PowerPoint

第 8 章里 pywin32 已用于 Excel 格式转换，同样也能驱动 PowerPoint。下面演示两类批量处理的典型做法。

### 10.2.1 示例 5：将旧版 .ppt 统一转为 .pptx

日常办公中常需要把 .ppt 升级为 .pptx，下面这段脚本实现了批量转换：

```python
import os

from win32com import client as wc

# 查找 dir 目录下 ext 后缀名的文件列表
# dir 参数是文件所在目录,ext 参数是文件后缀名

def findext(dir, ext):
    allfile = os.listdir(dir)

    # 返回过滤器对象
    files_filter = filter(lambda x: x.endswith(ext), allfile)
    # 从过滤器对象提取列表
    list2 = list(files_filter)
    return list2 # 返回过滤后条件文件名

if __name__ == '__main__':
    # 设置输入目录
    indir = r'C:\Users\tony\OneDrive\书\...\data\in'
    # 设置输出目录
    outdir = r'C:\Users\tony\OneDrive\书\...\out'

    pptapp = wc.Dispatch('PowerPoint.Application') # 创建 PPT 应用程序对象 ②

    # 查找 indir 目录中所有 .ppt 文件
    list2 = findext(indir, '.ppt')

    for name in list2:
        infile = os.path.join(indir, name) # 将目录和文件名连接起来
        name = name.replace('.ppt', '.pptx')
        outfile = os.path.join(outdir, name)
        print(outfile)
        ppt = pptapp.Presentations.Open(infile) # 打开 PPT 文件 ③
        ppt.SaveAs(outfile, FileFormat=24) # 24 ppSaveAsOpenXMLPresentation ④

        print(outfile, "转换 OK。")
        ppt.Close() # 关闭 PPT 文件 ⑤

    pptapp.Quit() # 退出 PPT 应用 ⑥

    print('完成。')
```

各行作用解释如下：

- 第①行从 pywin32 包里引入 win32com 模块。
- 第②行用 Dispatch 新建一个 PowerPoint 应用程序对象。
- 第③行启动 PowerPoint 并打开目标文件，注意打开路径请写绝对路径，不能使用相对路径。
- 第④行调用 SaveAs 将文件另存，参数 outfile 为新文件名，FileFormat 指定保存格式，填 24 等价于 ppSaveAsOpenXMLPresentation，也就是 .pptx；保存路径同样建议使用绝对路径。
- 第⑤行关闭当前演示文稿。
- 第⑥行退出 PowerPoint 进程。

保存时的数值 24 对应 .pptx 格式，具体对照可查看图 10-7 的常量映射表。

一个缩进层级通常用一个 Tab 或 4 个空格表示，鉴于不同编辑器对 Tab 的显示宽度不一致，多数学科规范建议统一采用 4 个空格作为一个缩进。

图 10-7 常量与文件格式的对应关系

### 10.2.2 示例 6：把大量 PPT 批量导出为 PDF

需求是把包含 .ppt 与 .pptx 的一批文件全部转成 PDF，以提高处理效率。

实现思路是借助 Python 的 pywin32 库调用本地的 Microsoft PowerPoint，让其完成保存为 PDF 的操作。

示例代码如下：

```python
import os
from win32com import client as wc
# 查找 dir 目录下 ext 后缀名的文件列表
# dir 参数是文件所在目录,exts 参数是指定文件后缀名列表
def findext(dir, exts):
    allfile = os.listdir(dir)
    # 返回过滤器对象
    files_filter = filter(lambda x: os.path.splitext(x)[1] in exts, allfile)
    # 从过滤器对象提取列表
    list2 = list(files_filter)
    return list2 # 返回过滤后条件文件名

if __name__ == '__main__':
    # 设置输入目录
    indir = r'C:\Users\tony\OneDrive...\in'
    # 设置输出目录
    outdir = r'C:\Users\tony\OneDrive\...\out'

    pptapp = wc.Dispatch('PowerPoint.Application') # 创建 PPT 应用程序对象

    # 查找 indir 目录中所有 PPT 文件
    # 通过 findext 函数查找指定目录下的 .ppt 和 .pptx 文件
    list2 = findext(indir, ['.ppt', '.pptx'])

    for name in list2:
        infile = os.path.join(indir, name) # 将目录和文件名连接起来
        name = name.replace('.pptx', '.pdf')
        name = name.replace('.ppt', '.pdf')
        outfile = os.path.join(outdir, name)
        print(outfile)
        ppt = pptapp.Presentations.Open(infile) # 打开 PPT 文件
        ppt.SaveAs(outfile, FileFormat = 32) # 32 ppSaveAsPDF

        print(outfile, "转换 OK。")
        ppt.Close() # 关闭 PPT 文件

    pptapp.Quit() # 退出 PPT 应用
    print('完成。')
```

各行作用解释如下：

- 第①行使用 SaveAs 把演示文稿另存为 PDF，这里 FileFormat 设为 32，表示 ppSaveAsPDF，即 .pdf 格式；格式与常量的对应关系可对照图 10-7。

## 10.3 ChatGPT 在 PPT 自动化里的实战用法

ChatGPT 能在整个 PPT 自动化链路中提供多维度支持，提升效率和准确率。常见应用场景包括：

1. 生成可用代码片段：根据需求产出创建、打开、保存演示文稿，以及插入幻灯片、文字、图片等操作的示例代码。
2. 给出编程思路：遇到批量处理、动画添加等复杂需求时，快速获得可行的实现思路。
3. 定位并解释错误：把报错信息贴给模型，获取原因分析和解决建议。
4. 代码复审与优化：完成初版脚本后请模型review，提供结构优化和性能改进建议。
5. 自动生成注释文档：基于脚本生成函数说明、参数释义和逻辑概述，提升可读性和维护性。
6. 分享最佳实践：学习页面布局、样式统一、动画使用等自动化中的规范做法。
7. 实时答疑：使用 PPT 或自动化过程中遇到问题时，直接对话获取指导。

借助自然语言对话，用户可以从片段生成到思路拆解、排错优化到文档产出全流程获得支持。

### 10.3.1 示例 7：让 ChatGPT 直接产出代码片段

背景：一位演讲者希望减少重复劳动，想自动完成新建演示文稿、写入文本、插入图片等操作，并希望 ChatGPT 帮忙生成可直接运行的片段。

对话过程：

用户向 ChatGPT 发出需求：我需要一段代码来新建一个演示文稿并在里面加入文本内容和图片，请问你可以...

### 10.3.2 示例 8：向 ChatGPT 寻求编程思路

背景：一位市场人员需要为产品推广制作大量幻灯片，希望能批量插入产品图并加上淡入动画，想先获取思路再动手实现。

对话过程：

用户提问：我想在演示文稿里批量插入产品图片并加上渐入动画效果，能给我一些实现思路吗？

ChatGPT 给出的思路示例：

(1) 批量插入产品图片：

- 用 Python 的文件操作遍历存放产品图片的文件夹。
- 借助 python-pptx 新建幻灯片并在指定位置插入图片。

(2) 添加渐入动画效果：

- 使用 PPT 库的动画接口，如 AnimationSettings 与 AnimationSequence，为对象添加淡入。
- 可根据需要调整动画类型、延迟时间、持续时间等参数。

随后给出一个编程思路的示例框架：

```python
from pptx import Presentation
from pptx.util import Inches

def batch_insert_images(presentation, image_folder):
    slide_layout = presentation.slide_layouts[1]  # 根据需要选择幻灯片布局
    slides = presentation.slides

    for image_file in os.listdir(image_folder):
        slide = slides.add_slide(slide_layout)
        slide.shapes.add_picture(os.path.join(image_folder, image_file), Inches(1), Inches(1), width=Inches(4), height=Inches(3))

def add_fade_in_animation(slide):
    shapes = slide.shapes
    for shape in shapes:
        animation = shape.animation
        animation.add(fade_in_animation)  # 根据需要选择不同的动画效果

# 创建演示文稿
presentation = Presentation()

# 批量插入产品图片
batch_insert_images(presentation, "product_images_folder")

# 添加渐入动画效果
for slide in presentation.slides:
    add_fade_in_animation(slide)

# 保存演示文稿
presentation.save("presentation.pptx")
```

以上思路可根据图片目录结构、动画需求和幻灯片风格灵活调整。如有具体细节问题，可进一步向模型追问。

用户反馈：非常感谢你提供的思路，对我帮助很大，我会按此尝试实现自动化制作流程。

### 10.3.3 示例 9：用 ChatGPT 解释运行错误

背景：开发人员在编写 PPT 自动化脚本时碰到报错，希望模型帮助定位原因并给出解决方向。

对话过程：

用户向 ChatGPT 发出提问：

在做 PPT 自动化脚本时，我的代码里出现了异常，片段如下：

```python
from pptx import Presentation

def add_slide(prs, title, content):
    slide_layout = prs.slide_layouts[1]
    slide = prs.slides.add_slide(slide_layout)
    title_placeholder = slide.shapes.title
    title_placeholder.text = title
    content_placeholder = slide.placeholders[1]
    content_placeholder.text = content

prs = Presentation("presentation.pptx")
add_slide(prs, "Slide Title", "Slide Content")
prs.save("presentation.pptx")
```

执行后报的错是：

AttributeError: 'SlideLayout' object has no attribute 'placeholders'

我对这个提示不太理解，想请人帮忙分析原因。

ChatGPT 给出的解答是：

这个报错说明 SlideLayout 对象本身并不具备 placeholders 这个属性。按照提示，可以改走 slide.shapes 来获取幻灯片上的图形对象，再按需要筛选操作。调整后的写法如下：

```python
from pptx import Presentation

def add_slide(prs, title, content):
    slide_layout = prs.slide_layouts[1]
    slide = prs.slides.add_slide(slide_layout)
    title_placeholder = slide.shapes.title
    title_placeholder.text = title
    content_shape = slide.shapes.add_textbox(left=Inches(2), top=Inches(1), width=Inches(6), height=Inches(4))
    content_frame = content_shape.text_frame
    content_frame.text = content

prs = Presentation("presentation.pptx")
add_slide(prs, "Slide Title", "Slide Content")
prs.save("presentation.pptx")
```

改动之后，脚本就能正常跑起来了。

用户接着向 ChatGPT 发问：

非常感谢你的说明！我明白问题所在了，也按你的思路改了代码，现在可以正常运行了。

### 10.3.4 示例 10：ChatGPT 辅助优化代码

下面用一个具体案例说明如何借助 ChatGPT 对代码做优化。

案例背景：

提问者是一名演讲者，经常需要批量制作演示文稿。已有一段用于 PPT 自动化的代码，希望借助 ChatGPT 进行检查和改进，提升质量和运行效率。相关代码已提供，期待具体的优化建议。

案例步骤：

用户向 ChatGPT 提问：

我写了下面这段代码来新建演示文稿并添加标题与正文：

```python
from pptx import Presentation

def add_slide(prs, title, content):
    slide_layout = prs.slide_layouts[1]
    slide = prs.slides.add_slide(slide_layout)
    title_placeholder = slide.shapes.title
    title_placeholder.text = title
    content_placeholder = slide.placeholders[1]
    content_placeholder.text = content

prs = Presentation()
add_slide(prs, "Slide Title", "Slide Content")
prs.save("presentation.pptx")
```

想请教一下还能怎么优化和改进。

ChatGPT 的回复：

你的实现已经很清晰了，这里再提供两个改进方向：

(1) 建议加入异常捕获机制，对可能出现的异常情况做处理并给出友好的提示。

(2) 建议把幻灯片布局的选择与内容填充封装成一个可复用的通用函数，方便以后重复调用。

优化后的示例代码：

```python
from pptx import Presentation

def add_slide(prs, title, content):
    slide_layout = prs.slide_layouts[1]
    slide = prs.slides.add_slide(slide_layout)
    title_placeholder = slide.shapes.title
    title_placeholder.text = title
    content_placeholder = slide.placeholders[1]
    content_placeholder.text = content

def create_presentation():
    try:
        prs = Presentation()
        add_slide(prs, "Slide Title", "Slide Content")
        prs.save("presentation.pptx")
        print("Presentation created successfully!")
    except Exception as e:
        print("An error occurred:", str(e))

create_presentation()
```

这样处理后，代码的健壮性会更好，也更灵活地生成演示文稿。

用户再次反馈：

非常感谢你的优化建议！我会按你的方案修改，进一步提升代码质量和效率。

## 10.4 本章总结

本章围绕 Python 实现 PPT 自动化展开，并结合了 ChatGPT 的辅助应用。

先通过 python-pptx 库掌握了 PPT 的基础操作，比如新建文件、添加幻灯片、插入表格和图表，为自动化打下基础。

再借助 pywin32 库完成了 PPT 文档的批量处理，包括格式转换、批量导出 PDF 等，明显提升了效率。

在学习过程中，ChatGPT 的作用很明显：

1. 能迅速生成 PPT 自动化的代码片段，辅助编程实现。
2. 能依据需求给出自动化思路，帮助设计解决方案。
3. 能解释错误根源，协助调试程序。
4. 能对代码做优化，增强程序的鲁棒性。

总之，掌握本章的 Python PPT 自动化方法，并善用 ChatGPT 这个编程助手，可以把工作效率和产出质量推向新水平。

## 第 11 章

## ChatGPT 辅助 PDF 自动化

PDF 是一种跨平台可靠的电子文档格式，在办公自动化里，PDF 的处理需求非常常见。可以通过编程实现 PDF 自动化处理流程，常见的任务和做法如下：

- （1）PDF 文件合并：将多个 PDF 合并成一个。可用 Python 的 PyPDF2 库或 pdftk 工具，先读取多个文件再把内容写入新文件。
- （2）PDF 文件拆分：把一个 PDF 拆成多个文件。可按页码、书签或文本内容拆分，用 PyPDF2 或 pdftk 读取源文件并按规则生成新文件。
- （3）PDF 文件提取：从 PDF 中提取页面、文本、图片或元数据等。可使用 PyPDF2、pdftotext 或 pdfplumber 读取并取出所需内容。
- （4）PDF 文件转换：把 PDF 转为文本 txt、图片 jpg/png 或 HTML 等格式。可借助 PyPDF2、pdftotext、pdfminer 等把内容转换出来。
- （5）PDF 文件加密和解密：对 PDF 加锁保护或解除已加密文件。可使用 PyPDF2 或 pdftk 进行加密解密操作。
- （6）PDF 表单处理：处理含表单字段的 PDF，填写字段、提取数据或生成报表。可使用 PyPDF2、pdfminer 或 pdfforms 读取和处理表单。
- （7）PDF 文件编辑：对 PDF 进行页面增删改、书签、链接、注释等编辑。可使用 PyPDF2、ReportLab 或专业 PDF 编辑工具实现。
- （8）PDF 文件文本识别：从 PDF 中提取文本并做后续处理分析。可使用 PyPDF2、pdftotext、pdfminer 或 OCR 工具读取文本内容。

编程实现 PDF 自动化能提高效率、减少重复劳动并保证一致性和准确性。根据具体场景选择合适的库、工具或服务编写脚本即可。

## 11.1 操作 PDF 文件

Python 里有多个常用库可以操作 PDF，常见的有：

- （1）PyPDF2：常用的 PDF 操作库，支持合并、拆分、旋转、提取文本和图像等功能。
- （2）pdftotext：基于 XPDF 的库，把 PDF 转为纯文本并返回字符串。
- （3）pdfminer：解析 PDF 文档的库，可提取文本、元数据和图像等信息。
- （4）PyMuPDF：基于 MuPDF 的库，提供文本提取、图像提取、页面合并拆分旋转等功能。

这些库都能实现提取文本图像元数据、合并拆分、旋转页面、创建新 PDF 等需求。可按需要选择合适的库并组合使用以满足复杂处理。

## 11.2 使用 PyPDF2 库

本节聚焦 PyPDF2，重点介绍如何读取、写入、拆分和合并 PDF 文件。开始使用前先安装：

```
pip install PyPDF2
```

### 11.2.1 PyPDF2 库中的那些对象

PyPDF2 结构简洁，主要包含 4 个核心类：

- （1）PdfFileReader：用于读取 PDF 文件，提供读取内容等属性和方法。
- （2）PdfFileMerger：用于合并多个 PDF 文件为一个。
- （3）PageObject：表示 PDF 中的单个页面对象。
- （4）PdfFileWriter：用于输出 PDF 文件，提供写入相关属性和方法。

### 11.2.2 示例1：读取 PDF 文件信息

下面以读取 xlwings Make Excel Fly.pdf 为例，文件内容如图 11-1 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_224_0.png)

实现代码如下：

```
from PyPDF2 import PdfReader

f = r'data/xlwings Make Excel Fly.pdf'
pdf_reader = PdfReader(f)  # 获取一个 PdfReader 对象
pageCount = len(pdf_reader.pages)  # 获取 PDF 的页数
print('pageCount:', pageCount)

page = pdf_reader.pages[0]  # 获得 PDF 文件的第一个页对象
text = page.extract_text()  # 提取第一页的文本
print(text)

outlines = pdf_reader.outline
print('完成。')
```

要点说明：

- 代码第①行从 PyPDF2 导入 PdfFileReader。
- 代码第②行用文件路径创建 PdfFileReader 实例。
- 代码第③行通过 numPages 属性获取页数。
- 代码第④行用 getPage 方法获取第一页的 PageObject，参数为页索引。
- 代码第⑤行调用 extractText 从页面提取文本。
- 代码第⑥行调用 getOutlines 获取 PDF 大纲。

### 11.2.3 示例2：拆分 PDF 文件

下面演示如何把一个 PDF 按页拆分成多个独立文件，如图 11-2 所示，源文件共 4 页，本例每页生成一个 PDF。

![](img/817f9d0d19c4377d32975098402bcdd3_225_0.png)

实现代码如下：

```
import os
from PyPDF2 import PdfReader, PdfWriter

# 设置输入目录
indir = r'data\in'
# 设置输出目录
outdir = r'data\out'

infile = os.path.join(indir, 'temp.pdf')
pdfReader = PdfReader(infile)
for page_no in range(len(pdfReader.pages)):
    page = pdfReader.pages[page_no]
    pdf_writer = PdfWriter()
    pdf_writer.add_page(page)
    file = '{0}.pdf'.format(page_no + 1)
    outfile = os.path.join(outdir, file)
    with open(outfile, 'wb') as output_pdf:
        pdf_writer.write(output_pdf)
print('完成。')
```

这段代码会遍历输入 PDF 的所有页面，为每一页新建一个 PDF 文件，文件名带页码信息并保存到指定目录，从而实现页面拆分。主要依赖 PyPDF2 解析原文件并创建新文件，os 模块处理路径。

要点说明：

- 代码第①行导入 PyPDF2 和 os。
- 代码第②行定义输入输出目录及目标 PDF。
- 代码第③行遍历 PDF 的每一页。
- 代码第④行获取当前页面对象。
- 代码第⑤行创建 PdfWriter 用于写入新 PDF。
- 代码第⑥行把当前页加入新 PDF。
- 代码第⑦行按页码生成新文件名。
- 代码第⑧行打开新 PDF 文件准备写入。
- 代码第⑨行用 PdfWriter 把页面写入新文件。

运行后输出目录会生成 4 个独立的 PDF。

### 11.2.4 示例3：更多方法拆分 PDF 文件

下面再演示一种选择性拆分，从源 PDF 中挑选指定页面组成新文件，如图 11-3 所示，新文件包含原文件的第 4、1、3 页，共 3 页。

示例实现代码如下：

Python 自动化处理 PDF：从页面重排到批量加水印、加密实战

```
import os
from PyPDF2 import PdfReader, PdfWriter

# 设置输入目录
indir = r'data\in'
# 设置输出目录
outdir = r'data\out'

infile = os.path.join(indir, 'temp.pdf')

pdfReader = PdfReader(infile)
for page_no in range(len(pdfReader.pages)):
    pdf_writer = PdfWriter()
    page = pdfReader.pages[page_no]
    pdf_writer.add_page(page)                    # 使用 add_page

    file = '{0}.pdf'.format(page_no + 1)
    outfile = os.path.join(outdir, file)
    with open(outfile, 'wb') as output_pdf:
        pdf_writer.write(output_pdf)

print('拆分成!')

pdf_writer = PdfWriter()
pdf_writer.add_page(pdfReader.pages[3])          # ①
pdf_writer.add_page(pdfReader.pages[0])          # ②
pdf_writer.add_page(pdfReader.pages[2])          # ③

file = '4,3,1.pdf'
outfile = os.path.join(outdir, file)
with open(outfile, 'wb') as output_pdf:
    pdf_writer.write(output_pdf)
    print('再次拆分完成!')
```

![](img/817f9d0d19c4377d32975098402bcdd3_227_0.png)

图 11-3 生成后 PDF 文件 4.3.1.pdf

代码说明如下：

- 第①行把源 PDF 的第 4 页放到新建文档里，这页在新文件里排第一。
- 第②行把源 PDF 的第 1 页追加进来，成为新文件的第二页。
- 第③行把源 PDF 的第 3 页再追加进来，成为新文件的第三页。

运行后会在输出目录得到一个名为 4,3,1.pdf 的文档。

### 11.2.5 示例4：PDF 合并实操

前面讲了拆分，这次演示如何把多个 PDF 合在一起。参考实现如下：

```
import os
from PyPDF2 import PdfReader, PdfWriter

# 设置输入目录
indir = r'data\in'
# 设置输出目录
outdir = r'data\out'

# 查找 dir 目录下 ext 后缀名的文件列表
def findext(dir, ext):
    allfile = os.listdir(dir)
    files_filter = filter(lambda x: x.endswith(ext), allfile)
    list2 = list(files_filter)
    return list2

# 合并 PDF 函数
def merge_pdfs(namelist, output):
    pdf_writer = PdfWriter()

    for name in namelist:
        infile = os.path.join(indir, name)
        pdf_reader = PdfReader(infile)

        for page_no in range(len(pdf_reader.pages)): # 修改变量名
            page = pdf_reader.pages[page_no]
            pdf_writer.add_page(page)

    with open(output, 'wb') as out:
        pdf_writer.write(out)

if __name__ == '__main__':
    list2 = findext(indir, '.pdf')
    outfile = os.path.join(outdir, '合并后.pdf')
    merge_pdfs(list2, output=outfile)
    print('合并完成!')
```

实现要点如下：

- 第①行定义 findext，用来在指定目录里筛选出后缀符合要求的 PDF 列表。
- 第②行定义合并用的主函数。
- 第③行实例化 PdfFileWriter 作为写入器 pdf_writer。
- 第④行循环遍历待合并的文件清单。
- 第⑤行用 PdfFileReader 读取当前输入文件。
- 第⑥行逐页遍历读取到的页面并取出页面对象。
- 第⑦行把读取到的页面添加到新的 PDF 文档中。
- 第⑧行把合并结果写出到磁盘。
- 第⑨行调用自定义的 merge_pdfs 完成整体合并。

执行后，输出目录里所有 PDF 会被合成成一个文件。

### 11.2.6 示例5：批量给 PDF 加水印

办公场景里常需要统一打标，下面介绍批量添加水印的做法。

先准备好一个水印用的 PDF，图 11-4 就是本例使用的水印文件。

示例代码如下：

```
import os
from PyPDF2 import PdfReader, PdfWriter

# 设置输入输出目录
indir = r'data\in'
outdir = r'data\out'

# 查找指定后缀的文件
def findext(dir, ext):
    files = os.listdir(dir)
    return list(filter(lambda x: x.endswith(ext), files))

# 添加水印
def add_watermark(filelist):
    # 水印文件
    watermark = r'data/水印.pdf'
    watermark_reader = PdfReader(watermark)
    watermark_page = watermark_reader.pages[0]

    for name in filelist:
        infile = os.path.join(indir, name)
        outfile = os.path.join(outdir, name)

        pdf_reader = PdfReader(infile)
        pdf_writer = PdfWriter()

        for page_no in range(len(pdf_reader.pages)):
            page = pdf_reader.pages[page_no]
            page.merge_page(watermark_page)
            pdf_writer.add_page(page)

        with open(outfile, 'wb') as out:
            pdf_writer.write(out)

if __name__ == '__main__':
    files = findext(indir, '.pdf')
    add_watermark(files)

    print('添加水印完成!')
```

![](img/817f9d0d19c4377d32975098402bcdd3_230_0.png)

图 11-4 水印.pdf 文件

代码说明：

- 第①行定义 add_watermark 函数，负责水印处理。
- 第②行创建读取水印模板的 PdfFileReader 对象。
- 第③行取出水印文件第一页，设计时水印 PDF 只需一页即可。
- 第④行遍历所有需要打标的 PDF。
- 第⑤行调用页面对象的 mergePage 方法把水印叠加到当前页，这是核心步骤。

处理完成后文件会被加上水印，效果见图 11-5 所示。

![](img/817f9d0d19c4377d32975098402bcdd3_231_0.png)

图 11-5 添加水印后的.pdf文件

### 11.2.7 示例6：批量加密 PDF 文件

为防止未授权访问和篡改，需要对 PDF 做加密保护，下面演示批量加密的方法。

## 极简 ChatGPT：用 AI 驱动 Python 自动化编程

加密本身很简单，核心是对 PdfFileWriter 对象调用加密方法即可。代码示例如下：

```python
import os

from PyPDF2 import PdfFileReader, PdfFileWriter

# 设置输入目录
indir = r'data\in'
# 设置输出目录
outdir = r'data\out'

# 查找 dir 目录下 ext 后缀名的文件列表
# dir 参数是文件所在目录, ext 参数是文件后缀名
def findext(dir, ext):
    allfile = os.listdir(dir)
    # 返回过滤器对象
    files_filter = filter(lambda x: x.endswith(ext), allfile)
    # 从过滤器对象提取列表
    list2 = list(files_filter)
    return list2  # 返回过滤后条件文件名

# 加密 PDF 函数
# namelist 要加密文件列表
def encrypt_pdf(namelist):
    # 遍历每一个要加密的 PDF 文件
    for name in namelist:
        infile = os.path.join(indir, name)
        outfile = os.path.join(outdir, name)
        pdf_reader = PdfFileReader(infile)

        pdf_writer = PdfFileWriter()
        pdf_writer.encrypt(user_password='qwerty', use_128bit=True)

        for page_no in range(pdf_reader.getNumPages()):
            # 获得输入的 PDF 文档一个页面对象
            page = pdf_reader.getPage(page_no)
            pdf_writer.addPage(page)

        # 写入文件
        with open(outfile, 'wb') as out:
            pdf_writer.write(out)

if __name__ == '__main__':
    # 查找 indir 目录中所有 PDF 文件
    list2 = findext(indir, '.pdf')
    encrypt_pdf(list2)
    print('加密完成!')
```

代码要点：

- 第①行新建一个 PdfFileWriter 实例。
- 第②行通过 encrypt 方法加密文档。参数 user_password 设为 qwerty 作为用户密码，use_128bit 指定使用 128 位加密算法。

### 11.2.8 示例7：批量解密 PDF 文件

11.2.7 节介绍了加密，这里介绍对应的解密。解密可通过 PdfFileReader 完成，代码如下：

```python
import os
from PyPDF2 import PdfReader, PdfWriter

# 设置目录
indir = r'data\in\加密'
outdir = r'data\out'

# 查找指定后缀文件
def findext(dir, ext):
    files = os.listdir(dir)
    return list(filter(lambda x: x.endswith(ext), files))

# 解密 PDF
def decrypt_pdf(namelist):
    for name in namelist:
        infile = os.path.join(indir, name)
        outfile = os.path.join(outdir, name)

        # 使用 PdfReader 读对象
        pdf_reader = PdfReader(infile)

        if pdf_reader.decrypt('qwerty'):
            print('解密成功!')

            pdf_writer = PdfWriter()
            # 获取页数
            page_count = len(pdf_reader.pages)

            for page_no in range(page_count):
                # 获取某页
                page = pdf_reader.pages[page_no]
                pdf_writer.add_page(page)

                with open(outfile, 'wb') as out:
                    pdf_writer.write(out)

if __name__ == '__main__':
    files = findext(indir, '.pdf')
    decrypt_pdf(files)
    print('解密完成!')
```

代码说明：

- 第①行调用 PdfReader 的 decrypt 方法传入密码 qwerty。返回值为 0 或 False 表示密码不匹配，返回 1 或 True 表示密码正确解密成功。

## 11.3 ChatGPT 在 PDF 自动化中的最佳实践

ChatGPT 能在 PDF 自动化流程中提供全流程支持，提升效率与准确度。常见应用场景包括：

- (1) 快速生成代码片段：根据需求产出打开、保存、合并、拆分、抽取文本等操作的代码。
- (2) 给出编程思路：遇到复杂需求时提供批量处理、文本搜索替换等实现思路。
- (3) 排错指导：把报错信息交给 ChatGPT 分析原因并给出修复建议。
- (4) 代码优化：对已完成的自动化脚本进行评审，提出质量和性能改进点。
- (5) 生成注释文档：自动生成函数说明、参数解释和逻辑描述，提升可读性与可维护性。
- (6) 提供最佳实践：学习文本提取、合并拆分、加水印等任务的推荐做法。
- (7) 回答专项问题：直接就 PDF 使用与自动化问题进行问答和指导。

借助自然语言对话，用户可以从代码片段生成、思路拆解、错误解释、优化建议、文档生成到最佳实践获取等环节获得一站式支持。ChatGPT 作为智能助手，在 PDF 自动化的开发与应用中作用明显。

### 11.3.1 示例8：借助 ChatGPT 生成代码片段

下面以一个具体案例说明如何让 ChatGPT 辅助写代码。

案例背景：
用户想从 document.pdf 提取文字并保存到文本文件，需要一段可直接运行的代码。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_235_0.png)

用户提问：
我有一个名为“document.pdf”的 PDF 文件，里面包含了一些文本内容，我想将这些文本内容提取出来，并保存到一个文本文件中。能否帮我生成一个代码片段，实现这个功能？

ChatGPT 回复：
当然可以，以下是一个示例代码片段，可帮助你实现文本提取的功能：

AI协同Python自动化：PDF与图片处理实战
```python
import PyPDF2

def extract_text_from_pdf(pdf_path, output_file):
    pdf_file = open(pdf_path, 'rb')
    pdf_reader = PyPDF2.PdfFileReader(pdf_file)

    text = ""
    for page_num in range(pdf_reader.numPages):
        page = pdf_reader.getPage(page_num)
        text += page.extract_text()

    with open(output_file, 'w', encoding = 'utf-8') as output:
        output.write(text)

    pdf_file.close()

pdf_path = "document.pdf"
output_file = "extracted_text.txt"

extract_text_from_pdf(pdf_path, output_file)
```
可以根据实际场景自行修改代码中的参数，比如输入 PDF 的路径和保存位置，以满足个性化的需求。希望这个片段对你有用。

用户向 ChatGPT 发起如下对话：
非常感谢你给的代码！我会按需调整，如果后续还有疑问再来请教。

### 11.3.2 示例 9：ChatGPT 辅助提供编程思路
下面用一个具体案例说明如何借助 ChatGPT 梳理编程思路。

示例背景：
用户有一份 PDF，想把其中每一页单独导出为 PDF 文件，并以页码命名，期望得到实现自动化的思路。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_236_0.png)
用户向 ChatGPT 发起如下提问：
我有一份名为 document.pdf 的多页文件，希望把每页拆出来各自保存成 PDF，文件名用页码表示，例如第一页是 page1.pdf，第二页是 page2.pdf，依次类推。请提供一下自动化的实现思路。

ChatGPT 回复如下：
可以给你一个可行的思路。你可以按下面步骤来拆分 PDF：

1. 选用 PDF 处理库，例如 PyPDF2，打开源文件。
2. 读取源文件的总页数。
3. 按页遍历，把当前页写入单独的 PDF，并用页码作为文件名。
4. 调用库方法把当前页保存为新文件。
5. 重复第4步，直到所有页面处理完毕。

这个框架可以作为起步，后续可根据实际需求填充代码并优化细节。

用户向 ChatGPT 发起如下提问：
编程思路很清晰！我明白了，会按这个方案编写代码实现拆分功能，后续需要指导再来咨询。

### 11.3.3 示例 10：ChatGPT 辅助解释代码错误
下面用一个具体案例说明如何借助 ChatGPT 定位并解释代码错误。

示例背景：
用户在写 PDF 文本提取的自动化脚本时出现报错，不清楚原因与解决办法，希望 ChatGPT 解释错误并给出方向。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_237_0.png)
用户向 ChatGPT 发起如下提问：
我在写一个自动提取 PDF 文本的脚本，运行后报错 AttributeError: 'NoneType' object has no attribute 'extract_text'。不太理解这个提示，也不知道怎么排查，能否解释并给出解决思路？

ChatGPT 回复如下：
我来帮你解读。报错的意思是代码尝试对一个值为 None 的对象调用 extract_text 方法，但 None 并没有这个属性。

常见原因是打开文件或获取页面时失败，导致拿到的对象是 None。随后调用 extract_text 就会触发这个错误。

可尝试的排查方向是：
1. 确认 PDF 路径正确，文件确实存在且可读。
2. 用合适的库如 PyPDF2 打开文件，并检查是否打开成功。
3. 用正确的方式取出页面，并确认返回的是有效的页面对象。
4. 在调用 extract_text 前先判断页面对象是否为 None，避免对空对象操作。

仔细检查打开文件和获取页面的逻辑即可。如果问题仍在，可贴出代码片段，我可以进一步分析。

用户向 ChatGPT 发起如下提问：
解释和方向非常有帮助，我会按建议检查代码并尝试修复错误，之后有问题再来请教。

ChatGPT 回复如下：
很高兴能帮上忙，后续有任何疑问或需要进一步说明，随时提问，我会继续支持。祝你顺利解决！

### 11.3.4 示例 11：ChatGPT 辅助优化代码
下面用一个具体案例说明如何借助 ChatGPT 对代码进行优化。

示例背景：
用户已实现多份 PDF 合并为一个文件的自动化脚本，运行正常但希望提升效率与可读性，寻求改进建议。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_238_0.png)
用户向 ChatGPT 发起如下提问：
> 我写了一个脚本把多个 PDF 合并成一个，功能正常，但感觉还能优化。能否给出一些建议，让执行更快、代码更好读？

ChatGPT 回复如下：
当然可以，下面是几个优化角度供参考：

1. 采用批量操作：合并多个文件时尽量减少重复的打开与保存次数，提升整体效率。
2. 选择合适的数据结构：管理文件路径时使用列表或集合，便于快速查找、增删。
3. 使用生成器：文件较大时可逐页读取并合并，减少内存占用。
4. 增加异常处理：文件缺失或无法打开等情况需要捕获，避免程序中断，提升健壮性。
5. 补充注释与文档：对逻辑和功能做说明，有助于后续维护和他人阅读。

具体改进要结合你的代码结构和需求来细化，希望对你有帮助。

## 11.4 本章总结
本章主要围绕用 Python 操作 PDF 以及 ChatGPT 在其中的辅助作用展开。

首先通过 PyPDF2 学习了 PDF 的基础操作，包括读取信息、拆分与合并，为自动化打下基础。

随后介绍了批量加水印、加密解密等实用技巧，大幅提升处理效率。

在学习过程中 ChatGPT 的作用体现在：
1. 快速生成代码片段，辅助编程。
2. 根据需求给出自动化思路，设计方案。
3. 解释错误原因，指导调试。
4. 提出优化建议，提升性能与可维护性。

总之，掌握 Python 处理 PDF 的核心方法，并善用 ChatGPT 作为编程助手，能显著提高 PDF 处理的效率和质量。

# 第 12 章 ChatGPT 辅助图片自动化
办公自动化中图片处理需求很常见，可用于优化文档、报告和演示素材。常见任务包括：

1. 调整图片尺寸与比例。
2. 转换图片格式。
3. 裁剪与旋转。
4. 添加水印或标记。
5. 应用滤镜与增强效果。

## 12.1 图像处理库——Pillow
Pillow 是 Python 领域功能强大且流行的图像处理库，支持加载、处理和保存多种格式，提供简洁易用的接口。

使用 Pillow 前需先安装，可通过 pip 执行安装指令：
```
pip install pillow
```

### 12.1.1 示例1：读取图片文件信息
下面介绍如何用 Pillow 读取文件信息，示例图片为“北京各城区最高房价柱状图.png”和“GP28060.jpg”。

![](img/817f9d0d19c4377d32975098402bcdd3_241_0.png)
图 12-1 北京各城区最高房价柱状图.png 文件

![](img/817f9d0d19c4377d32975098402bcdd3_241_1.png)
图 12-2 GP28060.jpg 文件

示例实现代码如下：
```python
from PIL import Image

f1 = r'images/GP28060.jpg'
f2 = r'images/北京各城区最高房价柱状图.png'

# 打印图片信息函数
def print_image_info(im):
    message = '''
```

## 极简ChatGPT：如何利用AI实现Python自动编程
```python
# 打印图片信息
print(message.format(im.format, im.size, im.mode))
```
```python
if __name__ == '__main__':
    try:
        # 打开 GP28060.jpg 文件
        im = Image.open(f1)
        print_image_info(im)
        # 显示图片
        im.show()

        print_image_info(im)
        # 显示图片
        im.show()

        # 打开北京各城区最高房价柱状图.png 文件
        im = Image.open(f2)
        print_image_info(im)
        # 显示图片
        im.show()

    except IOError as e:
        print('打开文件失败!')
```

示例代码运行后，在控制台输出结果如下：
图片格式: JPEG
图片尺寸: (800, 800)
图片模式: RGB
图片格式: JPEG
图片尺寸: (800, 800)
图片模式: RGB
图片格式: PNG
图片尺寸: (2000, 1000)
图片模式: RGBA
图片格式: PNG
图片尺寸: (2000, 1000)
图片模式: RGBA

代码说明如下：
- 第①行从 Pillow 导入 Image 模块。
- 第②行定义 print_image_info 函数，用来打印图片信息。
- 第③行是信息模板，使用三重单引号，可包含换行排版。
- 第④行输出信息。其中 format 表示源格式，运行后有时会变成 None；size 返回尺寸元组；mode 返回色彩模式，JPEG 默认 RGB，PNG 示例为 RGBA，RGBA 包含 Alpha 通道支持透明。
- 第⑤行用 Image.open 打开文件，返回 Image 对象但不显示。
- 第⑥行调用 im.show() 通过系统默认工具打开图片。

### 12.1.2 示例2：批量转换图片格式
办公中常需批量转格式，本例把多种格式统一转为 png。

示例实现代码如下：
```python
import os

from PIL import Image

# 设置输入目录
indir = r'images\in'
# 设置输出目录
outdir = r'images\out'

if __name__ == '__main__':

    # 查找 indir 目录中的所有文件
    allfile = os.listdir(indir)

    for name in allfile:
        infile = os.path.join(indir, name)

        # 去掉文件后缀名，提取文件名
        filename = os.path.splitext(name)[0]
        # 添加文件后缀名
        filename = filename + '.png'
        outfile = os.path.join(outdir, filename)
        try:
            # 打开图片文件
            im = Image.open(infile)
            # 保存文件,指定文件格式为 PNG,执行文件格式转换
            im.save(outfile, 'png')
            print('保存{0}文件成功.'.format(name))

        except IOError as e:
            print(e)
            print('打开{0}文件失败!'.format(name))
            # 继续转换下一个文件
            continue

    print('转换完成!')
```
代码说明如下：
- 第①行用 os.listdir 列出输入目录下所有文件。
- 第②行遍历文件列表。
- 第③行通过 os.path.splitext 去掉后缀，拿到文件名。
- 第④行为文件名加上目标后缀。
- 第⑤行拼接输出目录与文件名，得到完整路径。
- 第⑥行打开图片文件。
- 第⑦行调用 save 保存，第一个参数是路径，第二个参数是目标格式。

### 12.1.3 示例3：批量设置文件图片大小
办公中常需把不同规格图片统一缩放，本例统一调整为 500×500 像素。

示例实现代码如下：
```python
import os

from PIL import Image

# 设置输入目录
indir = r'images\in'
# 设置输出目录
outdir = r'images\out'

if __name__ == '__main__':

    # 查找 indir 目录中的所有文件
    allfile = os.listdir(indir)

    for name in allfile:
        infile = os.path.join(indir, name)
        outfile = os.path.join(outdir, name)

        try:
            # 打开图片文件
            im = Image.open(infile)
            # 重新设置图片大小
            w = 500
            h = 500
            factor = 0.5  # 缩放因子

            # w = round(im.size[0] * factor)
            # h = round(im.size[1] * factor)

            resized_im = im.resize((w, h))
            # 保存图片
            resized_im.save(outfile)

        except IOError as e:
            print(e)
            # 继续转换下一个文件
            continue

    print('转换完成!')
```
代码说明如下：
- 第①②行声明目标宽度和高度变量。
- 第③行设置缩放因子，本例未使用，若需按比例缩放可启用。
- 第④⑤行根据因子重新计算宽度和高度。

第6行调用 Image 模块里的 resize 方法来设定图片尺寸，传入的参数是一个二元组，其中第一个位置的 w 代表目标宽度，第二个位置的 h 代表目标高度。

![](img/817f9d0d19c4377d32975098402bcdd3_245_0.png)

运行后得到 500×500 像素的示例见图 12-3，可以明显看到部分图片的宽高比例被破坏了。若要保持等比缩放，就需要先设定一个缩放系数，第3行就负责定义这个系数，本例中暂时没有使用它。需要等比处理时可以启用该系数，第4行用 round 四舍五入函数算出缩放后的宽度，第5行则算出缩放后的高度。

![](img/817f9d0d19c4377d32975098402bcdd3_246_0.png)

### 12.1.4 示例4：图片旋转与翻转
办公场景里批量旋转图片很常见，Pillow 库就能胜任，它同时支持旋转和镜像。图 12-4 以 GP28060.jpg 为例，(a) 是原图，(b) 是水平镜像的效果，(c) 是顺时针旋转 90° 的效果。

![](img/817f9d0d19c4377d32975098402bcdd3_246_1.png)

示例实现代码如下：

```python
from PIL import Image

# 原始图片
f = 'images/GP28060.jpg'

if __name__ == '__main__':
    try:
        # 打开图片文件
        im = Image.open(f)
        # 显示图片
        im.show()
        im2 = im.transpose(Image.FLIP_LEFT_RIGHT)
        # 显示翻转后的图片
        im2.show()
        im3 = im.transpose(Image.ROTATE_90)
        # 显示旋转后的图片
        im3.show()

    except IOError as e:
        print(e)

    print('转换完成!')
```

代码说明如下：

- 第1行负责把原始图片展示出来。
- 第2行使用 Image 模块的 transpose，传入常量 FLIP_LEFT_RIGHT 实现水平翻转，类似的常量还有 FLIP_TOP_BOTTOM 实现垂直翻转、ROTATE_90、ROTATE_180 和 ROTATE_270 等。
- 第3行完成 90° 的旋转。

### 12.1.5 示例5：为图片添加水印
Pillow 还能给图片盖上水印，下面以图 12-5 为例演示操作流程。

![](img/817f9d0d19c4377d32975098402bcdd3_247_0.png)

示例实现代码如下：

```python
from PIL import Image
from PIL import ImageDraw
from PIL import ImageFont

f = 'images/GP28060.jpg'          # 原始图片
text = "小小程序员"               # 水印的文本
# 水印文本字体
ft = ImageFont.truetype(r'C:\WINDOWS\Fonts\msyh.ttc', 50)
if __name__ == '__main__':
    im = Image.open(f)            # 打开图片文件
    draw = ImageDraw.Draw(im)     # ImageDraw对象
    width, height = im.size       # 获得原始图片的宽和高

    # 使用 textbbox 方法获取文本的边界框
    textbbox = draw.textbbox((0, 0), text, font = ft)
    # 获得文本的宽度和高度
    textwidth = textbbox[2] - textbbox[0]
    textheight = textbbox[3] - textbbox[1]
    margin = 50                   # 设置空白,距离底边框和右边框 50 像素
    x = width - textwidth - margin # 获得 x轴坐标
    y = height - textheight - margin # 获得 y轴坐标
    # 绘制水印
    draw.text((x, y), text,
              fill = (255, 0, 0),
              font = ft)
    im.show()                     # 显示图片
```

代码说明如下：

- 第1行引入 Image 类，用来打开和创建图像。
- 第2、3行引入 ImageDraw 和 ImageFont 类，用于文字绘制和字体选择。
- 第4行设定水印文字的字体为 msyh.ttc，字号为 50。
- 第5行创建 ImageDraw 对象，用于在图像上进行绘制。
- 第6行调用 textbbox 方法获取水印文字的边界框。
- 第7行把水印文字与底部和右侧边缘的间距设为 50 像素。
- 第8行计算水印文字绘制的 x 轴坐标。
- 第9行计算水印文字绘制的 y 轴坐标。
- 第10行执行文字绘制，并指定填充色为红色以及使用的字体。

请确认代码中的图片路径和字体路径无误，按需调整。执行后原图上会叠加水印并显示结果。

## 12.2 ChatGPT 助力图片自动化实战
ChatGPT 能作为图片自动化的得力助手，提升效率和准确率。常见的应用方向有：

（1）自动化处理：可协助实现图像识别、编辑、合成等流程，提高效率和正确率。
（2）格式转换：提供示例代码和建议，方便在 JPEG、PNG、GIF 等格式间相互转换。
（3）压缩优化：生成代码片段实现体积压缩，减少文件大小并加快加载。
（4）批量操作：提供思路实现批量重命名、统一尺寸、批量加水印等。
（5）识别标注：协助自动检测图像中的对象、场景或特征并进行标注。
（6）生成合成：提供示例生成特定风格或特效图片，并实现多图合成一图。
（7）问答支持：遇到处理难题时可直接提问，获取解释和指导。

借助 ChatGPT，用户可以用自然语言对话，从处理、转换、压缩、批量、识别标注到生成合成，获得全流程支持，打造高效精准的自动化体系，ChatGPT 在开发和应用中扮演着重要智能助手角色。

### 12.2.1 示例 6：ChatGPT 生成代码片段
示例背景：
用户想给名为 image.jpg 的图片右下角加上“Confidential”红色水印，字号 14，希望 AI 输出实现代码。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_249_0.png)

用户向 ChatGPT 发出如下提问：
我有一张文件名为“image.jpg”的图片，想在右下角添加水印文字“Confidential”，颜色红色，字体大小 14，请帮我生成实现代码片段。

ChatGPT 回复如下：
当然可以，以下示例代码可帮助你为图片添加水印：

```python
from PIL import Image, ImageDraw, ImageFont

def add_watermark(image_path, watermark_text, position, color, font_size):
    image = Image.open(image_path)
    width, height = image.size
    watermark_font = ImageFont.truetype("arial.ttf", font_size)
    watermark_image = Image.new("RGBA", image.size, (0, 0, 0, 0))
    draw = ImageDraw.Draw(watermark_image)
    text_width, text_height = draw.textsize(watermark_text, font=watermark_font)
    watermark_position = (width - text_width - position[0], height - text_height - position[1])
    draw.text(watermark_position, watermark_text, font=watermark_font, fill=color)
    watermarked_image = Image.alpha_composite(image.convert("RGBA"), watermark_image)
    watermarked_image.save("watermarked_image.jpg")

image_path = "image.jpg"
watermark_text = "Confidential"
position = (10, 10)          # Distance from the bottom-right corner
color = (255, 0, 0)          # Red color
font_size = 14

add_watermark(image_path, watermark_text, position, color, font_size)
```

参数如水印文字、位置、颜色、字体和大小可按需修改，以满足具体需求。希望对你有帮助。

### 12.2.2 示例7：ChatGPT 提供编程思路
示例背景：
假设正在开发图片自动化处理程序，需要批量调整图片尺寸和质量，希望向 ChatGPT 请教思路。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_250_0.png)

用户向 ChatGPT 发出如下提问：
我正在开发一个图片自动化处理程序，需要批量调整图片的大小和质量，能给我一些建议和编程思路吗？

ChatGPT 回复如下：
当然！以下是一些可参考的思路：

### 12.2.3 示例8：ChatGPT 解释代码错误
示例背景：
开发图片自动化程序时遇到加载失败的错误，希望 ChatGPT 解释原因和解决办法。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_251_0.png)

用户向 ChatGPT 发出如下提问：
我在开发图片自动化处理程序时出现了错误，提示是 “FileNotFoundError: No such file or directory: 'image.jpg'”，原因是什么？

ChatGPT 回复如下：
出现该错误通常表示程序找不到指定图片。可能原因包括：
- (1) 路径错误：请核对图片路径和存储位置是否正确，文件是否在此路径下。
- (2) 文件名拼写错误：检查大小写和扩展名是否与实际一致。
- (3) 权限问题：读取受保护文件或目录可能因权限不足失败，请确认读取权限。
- (4) 文件缺失：文件可能被移动、删除或改名，请确认文件存在。

解决办法：
- (1) 核对路径和文件名与实际一致。
- (2) 确认有足够读取权限，或尝试以管理员身份运行。
- (3) 若文件不存在，检查是否被移动、删除或改名并更新路径。

希望以上说明能帮你解决问题，如有其它疑问随时提问。

### 12.2.4 示例9：ChatGPT 优化代码
示例背景：
已完成批量调整图片尺寸和质量的程序，希望借助 ChatGPT 提升效率和可读性。

示例步骤：
![](img/817f9d0d19c4377d32975098402bcdd3_252_0.png)

用户向 ChatGPT 发出如下提问：
我已完成一个批量调整图片大小和质量的自动化程序，想请 ChatGPT 帮忙优化代码，提升效率和可读性，有何建议？

ChatGPT 回复如下：
当然！以下建议可供参考：
- (1) 避免重复代码：将重复操作抽取为函数或方法。
- (2) 使用合适数据结构：用列表或字典管理大量图片数据，提升效率和可读性。
- (3) 减少不必要循环：确保循环只执行必要操作，尽量降低循环次数。
- (4) 增加错误处理：添加异常处理机制，增强鲁棒性和用户体验。
- (5) 代码注释与文档：为复杂代码块和关键函数添加注释说明用途、参数和返回值。
- (6) 测试与性能优化：测试各种场景，定位性能瓶颈并采用更高效算法或技巧。

希望这些建议能帮助你优化程序，如需进一步帮助随时提问。

## 12.3 本章小结
本章主要围绕 Python Pillow 库实现图片处理自动化，以及 ChatGPT 在其中的应用展开。

首先介绍了 Pillow 的核心功能，如尺寸调整、格式转换和水印添加，并给出代码示例，为构建自动化奠定基础。

随后通过多个案例展示 ChatGPT 的作用：
- (1) 快速生成图片处理代码片段，辅助编程。
- (2) 根据需求提供处理方案和思路。
- (3) 解释代码错误，帮助调试。
- (4) 对代码进行优化，提升处理效率。

总之，掌握本章内容即可了解 Python 图片自动化核心知识，并善用 ChatGPT 这一智能编程助手，打造智能高效的图片处理自动化系统。

# 极简ChatGPT 如何利用AI实现Python自动编程

《极简ChatGPT：如何利用AI实现Python自动编程》是一本面向效率提升读者的前沿教程，深入讲解 ChatGPT 的注册使用，并结合 Python 编程实现与 AI 流畅对话、快速生成高质量代码。书籍特色如下：

- **实战导向，案例丰富**：理论结合大量实战案例，边学边做，快速掌握 ChatGPT 在办公自动化中的技巧。
- **资源丰富，学习无忧**：配套示例代码、数据集、软件及学习支持，助力顺畅学习与应用。
- **轻松上手，快速掌握**：语言通俗、逻辑清晰，便于找到适合路径，迅速掌握 ChatGPT 与办公自动化的技能。
- **适用人群广泛**：适合 Python 初学者、ChatGPT 爱好者、数据分析与办公自动化爱好者、软件工程师及程序员、教师和企业培训师等。

![](img/817f9d0d19c4377d32975098402bcdd3_254_0.png)

![](img/817f9d0d19c4377d32975098402bcdd3_254_1.png)

![](img/817f9d0d19c4377d32975098402bcdd3_254_2.png)

资源下载·扩展阅读

清华大学出版社

上架指导：计算机/ChatGPT/Python

书 圈

官方微信号

ISBN 978-7-302-66310-2

9 787302 663102>

定价：69.00元