```
In [6]: square??
Signature: square(number)
Source:
def square(number):
    """Calculate the square of number."""
    return number ** 2
File:      ~/Documents/examples/ch04/<ipython-input-1-7268c8ff93a9>
Type:      function
```

如果不允许从 IPython访问源代码， “??” 只显示其文档字符串。

如果文档字符串的内容可以在当前窗口中完整显示， IPython将显示下一个In[]提示符。如果文档字符串太长无法在当前窗口全部显示， IPython会在窗口底部显示冒号（:）表示还有更多内容，按空格键可以显示下一屏。使用向上或向下方向键可以在文档字符串中向后或向前导航。 IPython在文档字符串的末尾显示（END）。在任何 “:” 或 （END）提示符处按q （表示quit）可返回到下一个In[]提示。如需了解IPython的功能，可以在任何In[]提示符下键入？, 按Enter键，然后阅读帮助文档的概述。