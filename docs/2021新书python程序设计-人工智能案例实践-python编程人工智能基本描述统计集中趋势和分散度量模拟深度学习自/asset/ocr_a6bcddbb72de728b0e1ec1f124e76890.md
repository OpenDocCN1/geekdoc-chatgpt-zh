(self) 创建hour、minute和second这3个属性，但实际上，这些语句调用了实现类的hour、minute和second这3个property的方法（第13～50行）。然后，这些方法再去创建名字分别为_hour、_minute和_second的3个属性，属性名以下划线（_）开始意味着这些属性应仅在类内使用：

```
# timewithproperties.py
"""Class Time with read-write properties."""
class Time:
    """Class Time with read-write properties."""
    def __init__(self, hour=0, minute=0, second=0):
        """Initialize each attribute."""
        self.hour = hour # 0-23
        self.minute = minute # 0-59
        self.second = second # 0-59
```

# Time类：命名为hour的可读写property

第13～24行代码定义了可公开访问的、命名为hour的可读写property，可操作名字为_hour的数据属性。以单下划线（_）开始的命名约定，意味着客户端代码不应该直接访问_hour。如前面的代码段[5]和[8]所看到的，对于正在使用Time对象的编程人员来说，property就像是数据属性。但需要注意，property被实现为方法。每个property都定义了一个getter方法，用于获取（即返回）一个数据属性的值，并可以选择性地定义一个setter方法，用于设置一个数据属性的值：

```
@property
def hour(self):
    """Return the hour."""
    return self._hour

@hour.setter
def hour(self, hour):
    """Set the hour."""
    if not (0 <= hour < 24):
        raise ValueError(f'Hour ({hour}) must be 0-23')
    self._hour = hour
```

getter方法以@property装饰器开始，它仅有一个self参数。装饰器将代码添加到被装饰的函数中，该装饰器使得hour函数能够像属性一样被操作。getter方法的名字就是property的名字，这个getter方法返回了_hour数据属性的值。下面的客户端代码表达式会调用这个getter方法：