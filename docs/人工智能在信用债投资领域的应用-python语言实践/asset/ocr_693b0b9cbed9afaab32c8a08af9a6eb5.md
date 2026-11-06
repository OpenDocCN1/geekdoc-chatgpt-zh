```
p #输出 747.93
p=prices.get("600518",0.0) #in 运算符的简洁形式
p #输出 0.0
```

# 9) 迭代和循环

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

# 10) 函数

Python 语言中使用 def 关键字创建函数。函数有返回值时,使用 return 语句,如果存在多个返回值,可返回列表或元组。在函数体中创建的变量或给变量赋值时,该变量的作用域是局部的。也就是说,该变量只定义在函数体内部,当函数返回值后会立即销毁该变量。如果想在函数体内部修改某个全局变量的值,可以使用 global 语句。

我们以一个示例来说明 Python 语言的函数操作,示例代码如下所示。

```
# 10)函数
def remainder(a,b):
    q=a//b # //是截断除法运算符
    r=a-q*b
    return r
```