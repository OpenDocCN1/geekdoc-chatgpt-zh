# 11.4 使用Textatistic库进行可读性评估

自然语言处理的一个有趣用途是评估文本可读性。可读性受所使用的词汇、句子结构、句子长度、主题等方面的影响。在编写本书时，我们使用了付费工具Grammarly来调整文字以确保文本的可读性。

本节将使用Textatistic库来评估文本可读性。有很多用于计算自然语言处理的可读性的公式，Textatistic使用五种流行的公式—Flesch Reading Ease、Flesch-Kincaid、Gunning Fog、Simple Measure of Gobbledygook（SMOG）和Dale-Chall。

# 安装Textatistic

为了安装Textatistic，需打开Anaconda Prompt（Windows）、Terminal（macOS/Linux）或shell（Linux）并输入以下命令：

```
pip install textatistic
```

Windows用户可能需要以管理员身份运行Anaconda Prompt，才能获得正确的软件安装权限。为此，请在开始菜单中右键单击Anaconda Prompt选项，然后选择More > Run as administrator。

# 计算统计得分和可读性得分

首先，将Romeo and Juliet文本加载到text变量中：

```
In [1]: from pathlib import Path

In [2]: text = Path('RomeoAndJuliet.txt').read_text()
```

为了计算文本统计数据和可读性得分，需要将其转化为Textatistic对象：

```
In [3]: from textatistic import Textatistic

In [4]: readability = Textatistic(text)
```