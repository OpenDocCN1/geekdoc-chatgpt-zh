```
self.name = name
self.grade = grade
```

上述程序代码是在构造器指定数据字段的初始值，name 和 grade 就是类的数据字段。

# 5. 方法

类的方法就是 Python 函数，只是第 1 个参数必须为 self 变量，而且在存取数据字段时必须使用 self 变量（因为只有 self 才是存取数据字段），如下所示：

```
def displayStudent(self):
    print("姓名 = " + self.name)
    print("成绩 = " + str(self.grade))
```

# 6. 使用类创建对象

定义类后，可以使用类创建对象，也称为实例（Instances）。同一个类可以如同工厂生产一般创建多个对象，如下所示：

```
s1 = Student("陈会安", 85)
```

上述程序代码创建对象 s1，Student() 就是调用 Student 类的构造器方法，其使用 2 个参数来创建对象。可以使用“.”运算符调用对象方法，如下所示：

```
s1.displayStudent()
print("s1.whoami() = " + s1.whoami())
```

同样的语法，可以存取对象的数据字段，如下所示：

```
print("s1.name = " + s1.name)
print("s1.grade = " + str(s1.grade))
```

# 2.6.2 隐藏数据字段

Python 类定义的数据字段和方法默认可以被其他 Python 程序代码存取和调用，对比其他面向对象程序语言就是指 public 公开成员。

如果数据字段需要隐藏，或方法只能在类中调用，并不是类对外的使用接口，需要使用 private 私有成员。Python 数据字段和方法名称只要使用 2 个底线“_”开头，就表示是私有（Private）数据字段和方法（Python 程序：Ch2_6_2.py），如下所示：

```
def __init__ (self, name, grade):
    self.name = name
    self.__grade = grade
```

上述构造器的 __grade 数据字段是隐藏的数据字段。

也可以创建只能在类中调用的私有方法（Private Methods），如下所示：