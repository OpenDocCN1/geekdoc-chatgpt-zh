```
In [1]: from dataclasses import dataclass

In [2]: @dataclass
    ...: class Demo:
    ...:     x # attempting to create a data attribute x
    ...:
    ...:

NameError                             Traceback (most recent call last)

<ipython-input-2-79ffe37b1ba2> in <module>()
      1 @dataclass
      2 class Demo:
----> 3     x # attempting to create a data attribute x
      4

<ipython-input-2-79ffe37b1ba2> in Demo()
      1 @dataclass
      2 class Demo:
----> 3     x # attempting to create a data attribute x
      4

NameError: name 'x' is not defined
```

类似于类属性，数据类中的每个数据属性在声明时都必须使用变量注释。第13和14行代码定义了数据属性face和suit。变量注释 “:str” 表示每一个数据属性都应该引用一个字符串对象：

```
13    face: str
14    suit: str
```

# 定义property和其他方法

数据类也是类，所以数据类可以包含property和方法，也可以作为类层次结构中的一员。与本章前面给出的Card类相同，对于这个Card数据类，我们也定义了image_name这个只读property以及自定义特殊方法__str__和__format__：

```
15    @property
16    def image_name(self):
17        """Return the Card's image file name."""
18        return str(self).replace(' ', '_') + '.png'
19
20    def __str__(self):
21        """Return string representation for str()."""
22        return f'{self.face} of {self.suit}'
23
24    def __format__(self, format):
25        """Return formatted string representation."""
26        return f'{str(self):{format}}'
```

# 关于变量注释的说明