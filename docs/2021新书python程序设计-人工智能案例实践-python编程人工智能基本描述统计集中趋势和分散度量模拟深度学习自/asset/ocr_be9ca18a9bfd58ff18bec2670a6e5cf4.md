```
26 @property
27 def minute(self):
28     """Return the minute."""
29     return self._minute
30
31 @minute.setter
32 def minute(self, minute):
33     """Set the minute."""
34     if not (0 <= minute < 60):
35         raise ValueError(f'Minute ({minute}) must be 0-59')
36
37     self._minute = minute
38
39 @property
40 def second(self):
41     """Return the second."""
42     return self._second
43
44 @second.setter
45 def second(self, second):
46     """Set the second."""
47     if not (0 <= second < 60):
48         raise ValueError(f'Second ({second}) must be 0-59')
49
50     self._second = second
51
```

# Time类：set_time方法

使用set_time方法以单一的方法调用同时修改3个属性的值，这是一种便利的方式。第54~56行分别调用了hour、minute和second这3个property的setter方法：

```
52 def set_time(self, hour=0, minute=0, second=0):
53     """Set values of hour, minute, and second."""
54     self.hour = hour
55     self.minute = minute
56     self.second = second
57
```

# Time类：__repr__特殊方法

将一个对象传递给内置函数repr时（如在IPython会话中评估一个变量，就会隐式地执行repr函数），对应类的__repr__方法会被调用以得到该对象的字符串表示：

```
58 def __repr__(self):
59     """Return Time string for repr()."""
60     return (f'Time(hour={self.hour}), minute={self.minute}, ' +
61     f'second={self.second})')
62
```