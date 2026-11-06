
```
p #输出 747.93
p=prices.get("600518",0.0) #in 运算符的简洁形式
p #输出 0.0
```

## 9) 迭代和循环

Python 语言中最常用的迭代和循环结构是 for 语句,它可以用来对容器成员进行迭代操作。for 语句的应用在前述案例中已有涉及,此处不再赘述,仅以如下案例总结下 for 语句的应用方法。

```
# 9)迭代和循环
for n in range(1,20):
    print("2 to the %d power is %d"%(n,2**n))
#打印出 a 中的每个字符
a="Hello World!"
for c in a:
    print(c)
#打印出列表 b 的所有成员
b=["Apple","Pear","Banana","Orange","Grape"]
for fruit in b:
    print(fruit)
#打印字典 prices 的所有成员
prices={"600519":747.93,"603444":204.74,"603833":139.03,"603180":
137.65,"603160":93.35}
for key in prices:
    print(key,prices[key])
#打印文件中所有的行
for line in open("news-today.txt","r",encoding='UTF-8'):
    print(line,end='\n')
```

## 10) 函数

Python 语言中使用 def 关键字创建函数。函数有返回值时,使用 return 语句,如果存在多个返回值,可返回列表或元组。在函数体中创建的变量或给变量赋值时,该变量的作用域是局部的。也就是说,该变量只定义在函数体内部,当函数返回值后会立即销毁该变量。如果想在函数体内部修改某个全局变量的值,可以使用 global 语句。

我们以一个示例来说明 Python 语言的函数操作,示例代码如下所示。

```
# 10)函数
def remainder(a,b):
    q=a//b # //是截断除法运算符
    r=a-q*b
    return r
```

```
def divide(a,b):
    q=a//b
    r=a-q*b
    return (q,r)
# 调用函数
result=remainder(93,20)
quotient,remainder=divide(93,20)
result #结果为 13
quotient #结果为 4
remainder #结果为 13
count=0
def modifyGlobal():
    global count #修改全局变量的值时,需要首先用 global 关键字声明
    count +=1#修改全局变量的值
    return count
modifyGlobal() #返回值为 1,表明全局变量修改成功
```

Python 语言中 有一种函数被称为匿名函数或者 lambda 函数,这其实是一种非常简单的函数: 仅由单条语句组成,该语句的结果就是函数的返回值。匿名函数是通过 lambda 关键字定义的,这个关键字没有别的含义,仅仅是说明“我们正在声明的是一个匿名函数”。
如下,介绍 3 个匿名函数应用的示例。

```
#匿名函数示例1
def short_func(x):
    return x * 2

equiv_short_func=lambda x:x * 2
#如下两种调用方法,输出结果相同
result1=short_func(2)
result2=equiv_short_func(2)
#匿名函数示例2
def apply_to_list(a_list,f):
    return [f(x) for x in a_list]
ints=[3,6,1,9,0,18]
apply_to_list(ints,lambda x:x * 2) #输出结果为: [6, 12, 2, 18, 0, 36]
#匿名函数示例3
strings=['foo','card','bar','bbbb','cddc']
strings.sort(key=lambda x:len(set(list(x))))
strings #输出结果为: ['bbbb', 'foo', 'cddc', 'bar', 'card']
```

## 11）对象和类

Python 语言中一切都是对象。对象由内部数据和各种方法组成,这些方法会执行与这些数据相关的各种操作。前述示例中，在处理像字符串和列表这样的内置类型时，已经用到了对象和方法。Python 语言中使用 dir() 函数可列出对象上的可用方法。使用 class 语句可定义新的类，以实现面向对象编程。类定义中使用 def 语句定义新的方法，每个方法中的第一个参数始终指向对象本身，根据约定，该参数的名称为 self。涉及对象属性的所有操作都必须显式地引用 self 变量。以双下划线开始和结束的方法是特殊的方法，如 __init__ 用于创建对象后初始化该对象。

object 类型是所有 Python 类型的根类型。

我们以一个示例来说明 Python 语言的对象和类的操作,示例代码如下所示。

```
# 3.11 对象与类
items=[1,45,67,89] # 创建一个列表
items.append(67)
items
dir(items) # 列出对象 items 中可用的方法
items.__add__([112,113]) # 返回 [1, 45, 67, 89, 67, 112, 113]
# class 语句定义新的对象类型
class stack(object):
    def __init__(self):
        self.stack=[]
    def push(self,object):
        self.stack.append(object)
    def pop(self):
        return self.stack.pop()
    def length(self):
        return len(self.stack)
s=stack()
s.push("David")
s.push(34)
s.push([1,2,3])
x=s.pop() # 返回 [1, 2, 3]
y=s.pop() # 返回 34
del s # 删除对象 s
dir(stack) # 从返回的结果中我们可以看出,dir() 函数首先列出的是继承自 object 类的一些方法,最后才是此处我们自定义的 push(),pop() 和 length() 三个方法。
```

## 12) 异常

Python 语言中如果程序运行出现错误,通常会导致程序终止。但是,我们可以使用 try 和 except 语句来捕捉并处理这些可能产生的异常。通常将可能产生异常的代码段放在 try 语句中,这样如果这些代码产生异常,则会将程序的控制权传递给 except 代码块中的代码。如果没有出现错误,except 代码块中的代码将被忽略。处理完异常后,程序将会继续执行紧跟在最后一个 except 代码块后面的语句。

我们以一个示例来说明 Python 语言的异常操作,示例代码如下所示。

```
#12)异常
#方式 1: 处理异常;输出信息不同,且程序继续执行
try:
    f=open("result.csv","r")
except IOError as e:
    print(e)
print("Program end! \n")#该语句会被执行,程序没中断
#方式 2: 不处理异常;输出信息不同,且程序中断执行
f=open("result.csv","r")
print("Program end! \n")#该语句不会被执行,程序已经中断
```

## 13) 模块

随着程序变得越来越大,为了便于维护,我们通常需要把它分为多个文件。为此,Python 允许把定义放入一个文件中,然后在其他程序和脚本中将其作为模块导入。要创建模块,可将相关的语句和定义放入与模块同名的文件中(注意,该文件的后缀必须是 .py)。

要在其他程序中使用该模块,需要使用 import 语句。import 语句创建了一个新的命名空间,并在该命名空间中执行与 .py 文件相关的所有语句。要在导入后访问该命名空间的内容,只要使用该模块的名称作为前缀即可。如果要使用不同的名称导入模块,只需要在 import 语句后加上 as 限定符即可。如果要将具体的定义导入到当前的命名空间中,需要使用 from 语句。

与对象一样,使用 dir 函数也可以列出模块的内容。

我们以一个示例来说明 Python 语言模块的操作,示例代码如下所示。

```
# 文件:div.py
def divide(a,b):
    q=a//b
    r=a-q*b
    return (q,r)
#13)模块
import div
a,b=div.divide(1879,51)
import div as fo
a,b=fo.divide(1879,51)
dir(div)
dir(fo)
```

## 附录 B

### 本书用到的 Python 包简介

### 1. numpy

numpy 的主要对象是同类型元素的多维数组，这是一个所有的元素都是一种类型、通过一个正整数元组索引的元素表格（通常是元素是数字）。在 numpy 中维度（dimensions）叫作轴（axes），轴的个数叫作秩（rank）。

如下示例中，我们用 arr 表示 numpy 数组对象，同时需要做如下引入。

```
import numpy as np
```

### 1) 数据的输入和输出
- np.loadtxt('file.txt')：从文件“file.txt”中导入数据并返回 ndarray 对象
- np.genfromtxt('file.csv', delimiter=',')：从文件“file.csv”中导入数据并返回 ndarray 对象
- np.savetxt('file.txt', arr, delimiter=' ')：将数组保存为“file.txt”文件
- np.savetxt('file.csv', arr, delimiter=',')：将数组保存为“file.csv”文件

### 2) 创建数组
- np.array([1,2,3,4,5,6])：创建一维数组并返回 ndarray 对象
- np.array([(1.2,3),(4.5,6)])：创建二维数组并返回 ndarray 对象
- np.zeros(3)：创建长度为 3 的一维数组且数组元素均为 0,返回 ndarray 对象
- np.ones((3,4))：创建 3×4 的二维数组且数组元素均为 1,返回 ndarray 对象
- np.eye(5)：创建一个 5×5 的二维数组且对角线元素均为 1,其他元素均为 0,返回 ndarray 对象
- np.linspace(0,100,6)：创建一维数组且其元素为 0 到 100 之间的 6 等分数字,返回 ndarray 对象
- np.arange(0,10,3)：创建一维数组且其元素为以 0 为起点,步长为 3,直到小于 10 的所有数值,返回 ndarray 对象
- np.full((2,3),8)：创建 2×3 二维数组且其所有元素均为 8,返回 ndarray 对象
- np.random.rand(4,5): 创建 4×5 二维数组且其所有元素为 0～1 的随机小数,返回 ndarray 对象
- np.random.rand(6,7) * 100: 创建 6×7 二维数组且其所有元素为 0～100 的随机小数,返回 ndarray 对象
- np.random.randint(5,size=(2,3)): 创建 2×3 二维数组且其所有元素为 0～4 的随机整数,返回 ndarray 对象

### 3) 获取数组属性
- arr.size: 返回数组 arr 中元素的个数
- arr.shape: 返回数组 arr 的维数(行数和列数)
- arr.dtype: 返回数组 arr 中元素的类型
- arr.astype(dtype): 强制转换数组 arr 中元素的类型为 dtype
- arr.tolist(): 将数组 arr 强制转换为 Python 列表
- np.info(np.eye): 查看关于 np.eye 的文档

### 4) 复制、排序和调整数组
- np.copy(arr): 复制数组 arr,返回 ndarray 对象
- arr.view(dtype): 以指定 dtype 类型为数组 arr 每个元素建立视图
- arr.sort(): 排序数组,返回 ndarray 对象
- arr.sort(axis=0): 按指定的轴排序数组,返回 ndarray 对象
- two_d_arr.flatten(): 将二维数组 two_d_arr 转换为一维数组,返回 ndarray 对象
- arr.T: 返回数组 arr 的转置(行与列互换)
- arr.reshape(3,4): 将数组 arr 调整为 3 行 4 列,并不改变数据,返回 ndarray 对象
- arr.resize((5,6)): 将数组 arr 调整为 5 行 6 列,空值用 0 填充,返回 ndarray 对象

### 5) 增加、删除元素
- np.append(arr,values): 为数组 arr 增加元素 values,返回 ndarray 对象
- np.insert(arr,2,values): 为数组 arr 在索引为 2 的元素之前插入元素 values,返回 ndarray 对象
- np.delete(arr,3,axis=0): 删除数组 arr 第 3 行所有的元素,返回 ndarray 对象
- np.delete(arr,4,axis=1): 删除数组 arr 第 4 列所有的元素,返回 ndarray 对象

### 6) 组合与拆分
- np.concatenate((arr1,arr2),axis=0): 将数组 arr2 按行序拼接在数组 arr1 的末尾,返回 ndarray 对象
- np.concatenate((arr1,arr2),axis=1): 将数组 arr2 按列序拼接在数组 arr1 的右侧,返回 ndarray 对象
- np.split(arr,3): 将数组 arr 拆分为 3 个子数组,返回 ndarray 对象
- np.hsplit(arr,5): 将数组 arr 从索引为 5 的元素后水平拆分,返回 ndarray 对象

### 7) 数组的索引、切片、子集
- arr[5]: 返回数组 arr 的索引为 5 的元素
- arr[2,5]: 返回二维数组 arr 行索引为 2,列索引为 5 的元素
- arr[1]=4: 将数组 arr 索引为 1 的元素赋值为 4
- arr[1,3]=10: 将二维数组 arr 行索引为 1,列索引为 3 的元素赋值为 10
- arr[0:3]: 返回数组 arr 索引从 0 开始的 3 个元素,如果 arr 为二维数组则返回索引从 0 开始的 3 行全部元素
- arr[0:3,4]: 返回二维数组 arr 中索引从 0 开始的 3 个行中 4 列的所有元素
- arr[0:2]: 返回数组 arr 索引从 0 开始的 2 个元素,如果 arr 为二维数组则返回索引从 0 开始的 2 行全部元素
- arr[:,1]: 返回数组 arr 中第 1 列中所有行的元素
- arr<5: 返回布尔型数组,其中数组 arr 中小于 5 的元素为 True,大于等于 5 的元素为 False
- (arr1<3)&(arr2>5): 返回布尔型数组
- ~arr: 返回布尔型数组的逆(True 变为 False、False 变为 True)
- arr[arr<5]: 返回数组中小于 5 的元素

### 8) 标量数学
- np.add(arr,1): 为数组 arr 的每个元素都加 1,并返回 ndarray 对象
- np.subtract(arr,2): 为数组 arr 的每个元素都减 2,并返回 ndarray 对象
- np.multiply(arr,3): 为数组 arr 的每个元素都乘 3,并返回 ndarray 对象
- np.divide(arr,4): 为数组 arr 的每个元素都除以 4,并返回 ndarray 对象
- np.power(arr,5): 为数组 arr 的每个元素都计算 5 次方,并返回 ndarray 对象

### 9) 向量数学
- np.add(arr1,arr2): 将数组 arr1 和 arr2 的对应元素相加,并返回 ndarray 对象
- np.subtract(arr1,arr2): 将数组 arr1 和 arr2 的对应元素相减,并返回 ndarray 对象
- np.multiply(arr1,arr2): 将数组 arr1 和 arr2 的对应元素相乘,并返回 ndarray 对象
- np.divide(arr1, arr2): 将数组 arr1 和 arr2 的对应元素相除，并返回 ndarray 对象
- np.power(arr1,arr2): 将数组 arr2 中的元素作为数组 arr1 中对应元素的指数，并返回 ndarray 对象
- np.array_equal(arr1,arr2): 判断数组 arr1 和 arr2 中对应元素是否相同，并返回 ndarray 对象的布尔型数组
- np.sqrt(arr): 计算数组 arr 中每个元素的平方根，并返回 ndarray 对象
- np.sin(arr): 计算数组 arr 中每个元素的正弦值(sin()),并返回 ndarray 对象
- np.log(arr): 计算数组 arr 中每个元素的自然对数,并返回 ndarray 对象
- np.abs(arr): 计算数组 arr 中每个元素的绝对值,并返回 ndarray 对象
- np.ceil(arr): 对数组 arr 中每个元素向上取整,并返回 ndarray 对象
- np.floor(arr): 对数组 arr 中每个元素向下取整,并返回 ndarray 对象
- np.round(arr): 对数组 arr 中每个元素四舍五入取整,并返回 ndarray 对象

### 10）统计数值
- np.mean(arr,axis=0): 计算数组 arr 中指定轴的均值,并返回 ndarray 对象
- arr.sum(): 返回数组 arr 中所有元素的和
- arr.min(): 返回数组 arr 中最小的元素
- arr.max(axis=0): 返回数组 arr 中指定轴的最大元素
- np.var(arr): 返回数组 arr 中所有元素的方差
- np.std(arr,axis=1): 返回数组 arr 中指定轴的标准差
- arr.corrcoef(): 返回数组 arr 中所有元素的相关系数

### 2. Pandas

Pandas 提供了使我们能够快速便捷地处理结构化数据的大量数据结构和函数，其中两个主要数据结构是 DataFrame 和 Series。此处，我们以 df 代表任意的 Pandas DataFrame 对象,s 代表任意的 Pandas Series 对象,同时我们需要做如下的引入：

```
import pandas as pd
import numpy as np
```

### 1）导入数据
- pd.read_csv(filename): 从 CSV 文件导入数据
- pd.read_table(filename): 从限定分隔符的文本文件导入数据
- pd.read_excel(filename): 从 Excel 文件导入数据
