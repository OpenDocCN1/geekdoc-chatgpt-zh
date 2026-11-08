从零搭建 Python 开发环境：Miniconda + PyCharm 实战笔记

| Python 3.9 | Miniconda3 Windows 32-bit | 67.8 MB | ... |

| Python 3.8 | Miniconda3 Windows 32-bit | 66.8 MB | ... |

| Python 3.7 | Miniconda3 Windows 32-bit | 65.5 MB | ... |

图2-2 Miniconda官网网站提供的下载

下载完成后得到的文件是 .exe 安装包，双击运行即可进入安装向导。安装结束后再打开对应的安装目录，如果看到如图2-3的目录结构，就说明部署成功。

[PRE0]

| 名称 | 修改日期 | 类型 |

|------|----------|------|

| condabin | 2021/8/6 16:11 | 文件夹 |

| conda-meta | 2022/12/12 10:07 | 文件夹 |

| DLLs | 2021/8/6 16:11 | 文件夹 |

| envs | 2021/8/6 16:11 | 文件夹 |

| etc | 2021/11/16 16:55 | 文件夹 |

| include | 2021/8/6 16:11 | 文件夹 |

| Lib | 2022/1/13 14:46 | 文件夹 |

图2-3 Miniconda安装目录

- 2. 打开控制台

随后依次点开“开始”→“所有程序”→Miniconda3→Miniconda Prompt，就能唤出一个与 CMD 类似的终端窗口。通过这个窗口输入指令即可对 Python 进行控制和配置。Miniconda 里最常用的就是 conda 命令，可完成环境管理等基础操作。

- 3. 验证Python

接下来在终端里输入 python，若安装正常会直接输出版本信息并进入交互提示符。在提示符下执行代码：

[PRE1]

[PRE2]

图2-4 验证Miniconda Python安装成功

- 4. 使用pip命令

Miniconda 的优势在于能很方便地管理大量第三方库。查询已安装库的代码如下：

[PRE3]

注意： 如果此时命令行仍停留在 >>> 状态，可以输入 exit() 退出。

在 Miniconda Prompt 中执行 pip list，得到的结果如图2-5所示，为局部截图。

[PRE4]

图2-5 列出已安装的第三方类库

在 Miniconda 里用 pip 的操作方式很多，核心是安装第三方库，命令格式为：

[PRE5] 这里的 name 指代需要安装的库名。举例来说，要装 NumPy 包（该包已预装），输入的命令如下：

[PRE6]

执行结果如图2-6所示。

[PRE7]

Miniconda 的另一个好处是默认自带了学习常用的诸多第三方库，能避免在安装某个特定库时因依赖缺失而报错。

### 2.1.2 PyCharm的下载与安装

和其它语言一样，Python 代码可以用 Windows 自带的控制台编写，但面对复杂的工程时容易混淆层级和关联文件，因此建议使用专用的 Python IDE PyCharm。

- 1. PyCharm的下载和安装

(1) 进入 PyCharm 官网的 Download 页面后，找到 Other versions 链接并打开，在该页面根据系统选择免费的社区版 Community Edition 下载，如图2-7所示。

| PyCharm Professional Edition | PyCharm Community Edition |

|------------------------------|---------------------------|

| 2023.2.2 - Linux (tar.gz)    | 2023.2.2 - Linux (tar.gz) |

| 2023.2.2 - Linux ARM64 (tar.gz) | 2023.2.2 - Linux ARM64 (tar.gz) |

| 2023.2.2 - Windows (exe)     | 2023.2.2 - Windows (exe)  |

| 2023.2.2 - Windows ARM64 (exe) | 2023.2.2 - Windows ARM64 (exe) |

| 2023.2.2 - macOS (dmg)       | 2023.2.2 - macOS (dmg)    |

| 2023.2.2 - macOS Apple Silicon (dmg) | 2023.2.2 - macOS Apple Silicon (dmg) |

图2-7 选择PyCharm Community Edition下载

(2) 下载完成后双击安装包进入 PyCharm 安装界面，如图2-8所示。点 Next 继续即可。

(3) 如图2-9所示，在配置界面上建议勾选全部复选框，这些选项能让后续使用更顺手。

# PyCharm Community Edition Setup

# Welcome to PyCharm Community Edition Setup

Setup will guide you through the installation of PyCharm Community Edition.

It is recommended that you close all other applications before starting Setup. This will make it possible to update relevant system files without having to reboot your computer.

Click Next to continue.

[PRE8]

Next >

Cancel

图2-8 安装界面

# Installation Options

Configure your PyCharm Community Edition installation

- Create Desktop Shortcut: ☑ PyCharm Community Edition

- Update PATH Variable (restart needed): ☑ Add "bin" folder to the PATH

- Update Context Menu: ☑ Add "Open Folder as Project"

- Create Associations: ☑ .py

图2-9 勾选所有复选框

（4）安装完成后，点击 Finish 按钮，如图2-10所示。

[PRE9]

图2-10 安装完成

## 2. 使用PyCharm创建程序

- 1. 单击桌面上新生成的 [PRE10] 图标启动 PyCharm，首次启动会出现导入设置的选择界面，如图2-11所示。这里是对项目存储位置的设定，建议选择第二个 Do not import settings。

- 2. 点 OK 后进入 PyCharm 配置界面，如图2-12所示。

[PRE11]

图2-11 由PyCharm自动指定

[PRE12]

图2-12 配置界面

（3）在配置界面可以调整 PyCharm 的外观，选择自己习惯的使用风格。若不熟悉，直接保留默认配置即可。如图2-13所示，可把界面背景改成白色。

[PRE13]

图2-13 对PyCharm的界面进行配置

（4）新建一个工程，如图2-14所示，也可以把本书配套源码导入为新工程。

[PRE14]

图2-14 创建一个新的工程

在工程目录下，右键打开菜单，选择 New→Python File，新建一个 helloworld.py 文件，打开编辑页并输入代码，如图2-16所示。

[PRE15]

图2-15 新建一个PyCharm的工程文件

[PRE16]

图2-16 helloworld.py

通过菜单栏 Run→run… 运行代码，或直接右键 helloworld.py 选择 run。若成功输出 hello world，则说明 Python 与 PyCharm 已配置完成。

### 2.1.3 Python代码小练习：计算Softmax函数

对于 Python 科学计算，最直接的思路就是把数学公式直接翻译成代码，Python 正好满足这点。本节用 Python 实现深度学习中常见的一个函数——Softmax。先不展开其作用，只带领读者完成实现练习。

Softmax 的计算公式如下：

[PRE17]

其中 $V_i$ 是长度为 $j$ 的数列 $V$ 中的一个元素，Softmax 的结果是对每个 $V_i$ 先取以 e 为底的指数再做非负化，随后除以所有项之和完成归一化。这样每个 $V_i$ 就可解释为在观测到的数据集类别中，该元素属于某一类别的概率，也称似然。

提示： Softmax 用于缓解概率计算中数值过大导致绝对主导的问题。例如结果中有两个值 $a$ 和 $b$，且 $a>b$，若仅按大小衡量，后续使用中 $a$ 会总被选中而 $b$ 因数值小被忽略，但有时也需要用到较小的 $b$，Softmax 能解决这个问题。

Softmax 按概率挑选 $a$ 和 $b$，由于 $a$ 的概率大于 $b$，所以计算时 $a$ 更常被选中，而 $b$ 因概率小被选中的机会也小，但并非为零。

Softmax 公式对应的代码如下：

[PRE18]

代码接受一个数列作为输入，分别计算每个数值的指数函数值，求和后得到每个数值在总和中的概率。

[PRE19]

具体结果请读者自行打印验证。

## 2.1 环境搭建1：安装Python

### 2.1.1 Miniconda的下载与安装

- 1. 下载和安装

打开 Miniconda 官方网站，其下载页面如图2-1所示。

读者可依据自己的操作系统挑选对应平台的 Miniconda，目前提供的是集成 Python 3.10 的版本。若使用的是旧版 Python，例如 Python 3.9 也是完全可用的，经测试，3.10 或 3.9 版本的 Python 都不影响 PyTorch 的使用。

[PRE20]

图2-1 Miniconda下载页面

（1）这里推荐 Windows Python 3.9 版本，相较 3.10 版本，3.9 经过更长时间的验证，稳定性更好。当然也可以按个人喜好选择。集成 Python 3.9 的 Miniconda 可在官网下载，打开后如图2-2所示。注意：若系统为 64 位，则应选择以 Miniconda3 开头、以 64 结尾的安装包，不要选错。

[PRE21]

图2-2 Miniconda官网网站提供的下载

（2）下载完成后得到的文件是 .exe 版本，直接运行即可进入安装过程。安装完成后，出现如图2-3所示的目录结构，说明安装正确。

[PRE22]

图2-3 Miniconda安装目录

## 2. 打开控制台

之后依次单击“开始”→“所有程序”→Miniconda3→Miniconda Prompt，打开 Miniconda Prompt 窗口，它与 CMD 控制台类似，输入命令就可以控制和配置 Python。在 Miniconda 中常用的是 conda 命令，该命令可以执行一些基本操作。

## 3. 验证Python

接下来，在控制台中输入 python，如果安装正确，就会打印出版本号和控制符号。在控制符号下输入代码：

[PRE23]

结果如图2-4所示。

[PRE24]

## 4. 使用pip命令

使用 Miniconda 的好处在于，它能够很方便地帮助读者安装和使用大量第三方类库。查看已安装的第三方类库的代码如下：

[PRE25]

注意： 如果此时 CMD 控制台命令行还在 >>> 状态，可以输入 exit() 退出。

在 Miniconda Prompt 控制台输入 pip list 命令，结果如图2-5所示（局部截图）。

[PRE26]

Miniconda 中使用 pip 进行操作的方法还有很多，其中最重要的是安装第三方类库，命令如下：

[PRE27]

这里的 name 是需要安装的第三方类库名，假设需要安装 NumPy 包（这个包已经安装过），那么输入的命令如下：

[PRE28]

结果如图2-6所示。

[PRE29]

使用 Miniconda 的一个好处是默认安装了大部分学习所需的第三方类库，这样能够避免使用者在安装和使用某个特定的类库时出现依赖类库缺失的情况。

### 2.1.2 PyCharm的下载与安装

和其他语言类似，Python 程序的编写可以使用 Windows 自带的控制台进行编写。但是这种方式对于较为复杂的程序工程来说，容易混淆相互之间的层级和交互文件，因此在编写程序工程时，建议使用专用的 Python 编译器 PyCharm。

- 1. PyCharm的下载和安装

(1) 进入 PyCharm 官网的 Download 页面后，可以找到 Other versions 链接并打开，在这个页面上根据自己的系统选择免费的社区版（Community Edition）下载，如图2-7所示。

[PRE30]

图2-7 选择PyCharm Community Edition下载

(2) 下载安装文件后双击运行进入 PyCharm 安装界面，如图2-8所示。单击 Next 按钮继续安装即可。

(3) 如图2-9所示，在配置界面上勾选所有的复选框，这些配置项方便我们使用 PyCharm。

# PyCharm Community Edition Setup

# Welcome to PyCharm Community Edition Setup

Setup will guide you through the installation of PyCharm Community Edition.

It is recommended that you close all other applications before starting Setup. This will make it possible to update relevant system files without having to reboot your computer.

Click Next to continue.

图2-8 安装界面

# PyCharm Community Edition Setup

# Installation Options