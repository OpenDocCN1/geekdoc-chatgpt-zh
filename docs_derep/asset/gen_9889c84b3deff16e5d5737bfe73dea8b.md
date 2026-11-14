# Python 进阶：多态、鸭子类型与运算符重载实战解析

在 Python 编程中，理解对象如何协作远比死记语法更关键。今天我们通过一系列实战演示，深度剖析**继承体系下的多态行为**、**鸭子类型的动态特性**，以及**运算符重载如何让自定义类支持原生语法**。

---

## 一、 继承树与多态：`is-a` 关系的验证

首先，我们定义了一条薪资计算体系：`CommissionEmployee`（提成员工）作为基类，`SalariedCommissionEmployee`（底薪加提成员工）继承自它。让我们看看这个层级在运行时如何表现。

[PRE0]

```python
In [17]: print(f'{s.earnings():,.2f}')

1,500.00
```

通过 `issubclass` 检查，子类与父类的类层级关系成立：

[PRE1]

```python
In [18]: issubclass(SalariedCommissionEmployee, CommissionEmployee)

Out[18]: True
```

实例层面，`isinstance` 确认对象 `s` 同时属于子类与父类类型，这是多态的基石：

[PRE2]

```python
In [19]: isinstance(s, CommissionEmployee)

Out[19]: True

In [20]: isinstance(s, SalariedCommissionEmployee)

Out[20]: True
```

利用这一特性，我们可以将不同类型的员工对象放入同一容器，统一调用 `earnings()` 方法，无需关心具体子类型：

[PRE3]

```python
In [21]: employees = [c, s]

In [22]: for employee in employees:
    print(employee)
    print(f'{employee.earnings():,.2f}\n')
```

[PRE4]

```python
for employee in employees:
    print(employee)
    print(f'{employee.earnings():,.2f}\n')
```

---

## 二、 鸭子类型：”像鸭子叫、像鸭子走，它就是鸭子“

Python 不强制要求继承自同一基类。只要对象拥有所需的方法（如 `earnings`），它就能融入协作流程。我们定义一个完全无关的 `WellPaidDuck` 类，它只有 `earnings` 和 `__repr__` 方法：

[PRE5]

```python
In [1]: class WellPaidDuck:
    def __repr__(self):
        return 'I am a well-paid duck'
    def earnings(self):
        return Decimal('1_000_000.00')
```

将这个”鸭子”与员工对象混在同一个列表中迭代，代码依然完美运行，毫无压力：

[PRE6]

```python
In [2]: from decimal import Decimal
In [3]: from commissionemployee import CommissionEmployee
In [4]: from salariedcommissionemployee import SalariedCommissionEmployee
In [5]: c = CommissionEmployee('Sue', 'Jones', '333-33-3333',
    Decimal('10000.00'), Decimal('0.06'))
In [6]: s = SalariedCommissionEmployee('Bob', 'Lewis', '444-44-4444',
    Decimal('5000.00'), Decimal('0.04'), Decimal('300.00'))
In [7]: d = WellPaidDuck()
In [8]: employees = [c, s, d]
```

[PRE7]

```python
In [9]: for employee in employees:
    print(employee)
    print(f'{employee.earnings():,.2f}\n')
```

**核心启示**：静态类型语言靠接口/抽象类约束，Python 靠协议。这就是鸭子类型的威力——**关注行为，而非血统**。

---

## 三、 运算符重载：让自定义类支持 `+` 与 `+=`

接下来，我们实现一个复数类 `Complex`，目标是让它能像内置数值类型一样，直接使用 `+` 和 `+=` 运算符。

数学上，复数加法规则为：实部加实部，虚部加虚部。
即：`(a + bi) + (c + di) = (a+c) + (b+d)i`

[PRE8]

```python
realPart + imaginaryPart * i
```

### 1. 类骨架与初始化

[PRE9]

```python
### complexnumber.py
"""Complex class with overloaded operators."""

class Complex:
    """Complex class that represents a complex number
    with real and imaginary parts."""

    def __init__(self, real, imaginary):
        """Initialize Complex class's attributes."""
        self.real = real
        self.imaginary = imaginary
```

### 2. 重载 `+`：`__add__` 返回新对象

实现 `__add__` 方法，接收右操作数 `right`，返回一个**全新的** `Complex` 实例，不修改原对象。

[PRE10]

```python
def __add__(self, right):
    """Overrides the + operator."""
    return Complex(self.real + right.real,
                   self.imaginary + right.imaginary)
```

验证效果：创建两个实例 `x`、`y`，直接相加得到新实例，原对象 `x`、`y` 保持不变。

[PRE11]

```python
In [1]: from complexnumber import Complex
In [2]: x = Complex(real=2, imaginary=4)
In [3]: x
Out[3]: (2 + 4i)
In [4]: y = Complex(real=5, imaginary=-1)
In [5]: y
Out[5]: (5 - 1i)
```

[PRE12]

```python
In [6]: x + y
Out[6]: (7 + 3i)
```

[PRE13]

```python
In [7]: x
Out[7]: (2 + 4i)
In [8]: y
Out[8]: (5 - 1i)
```

### 3. 重载 `+=`：`__iadd__` 就地修改并返回自我

`+=` 期望修改左操作数本身。`__iadd__` 需更新实例属性，并**返回 `self`**。

[PRE14]

```python
def __iadd__(self, right):
    """Overrides the += operator."""
    self.real += right.real
    self.imaginary += right.imaginary
    return self
```

实测：`x += y` 后，`x` 变为相加结果，`y` 不变，且 `x` 仍是同一个对象引用。

[PRE15]

```python
In [9]: x += y
In [10]: x
Out[10]: (7 + 3i)
In [11]: y
Out[11]: (5 - 1i)
```

### 4. 友好显示：`__repr__` 处理正负号格式

重写 `__repr__`，智能处理虚部符号，输出标准数学格式（如 `2 + 4i` 或 `5 - 1i`，而非 `5 + -1i`）。

[PRE16]

```python
def __repr__(self):
    """Return string representation for repr()."""
    return f'({self.real}' +
           ('+' if self.imaginary >= 0 else '-') +
           f'{abs(self.imaginary)}i)')
```

---

## 总结

本文通过代码实战串联了三大核心概念：
1.  **多态与继承**：`isinstance`/`issubclass` 确立类型层级，统一接口调用。
2.  **鸭子类型**：打破继承壁垒，任意拥有 `earnings` 方法的对象皆可参与协作。
3.  **运算符重载**：通过 `__add__`（新建对象）、`__iadd__`（就地修改）、`__repr__`（规范显示），赋予自定义类原生数据类型般的操作体验。

掌握这些，你就能写出更符合 Python 风格、更具扩展性与表达力的代码。