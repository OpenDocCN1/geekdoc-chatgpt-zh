```
y="86"
z=x+y # z='3786'
#要执行数学计算,首先要使用 int()或 float()等函数将字符串转换为数值
w=int(x)+int(y) #w=123
i="3.14"
j=float(i)#使用 int(i)则会出错
#将数值转换为字符串
s="The value of x is "+str(z)
```

# 5) 列表

Python 语言中的列表是由任意对象组成的序列,把数据放入方括号中就可以创建列表,也可使用 list()函数来创建列表。列表的索引是从 0 开始的整数,使用 append()函数可将新的数值添加到列表末尾,使用 insert()函数可将新的数值插入到列表指定索引的位置。

列表可以包含任意种类的 Python 对象,包括其他列表。嵌套列表中包含的数据需要使用多次索引才能访问到。

我们以一个示例来说明 Python 语言的列表操作,示例代码如下所示。

```
#5)列表及其应用
fruits= ["Apple","Banana","Orange","Pear"]
a=fruits[2] #结果为 Orange
fruits[0]="Cherry" # fruits 的第一项改为"Cherry"
fruits.append("Strawberry") #"Strawberry"被添加到了列表末尾
fruits.insert(2,"Blueberry") #在索引为 2 的列表位置,插入"Blueberry"
b=fruits[0:2] #提取子列表(切片),返回['Cherry', 'Banana']
c=fruits[2:] #提取子列表(切片),返回['Blueberry', 'Orange', 'Pear', 'Strawberry']
d=[1,2]+[3,4,5] #结果是[1,2,3,4,5],用+连接列表
e=[] #创建一个空列表
f=list() #创建一个空列表
g=[1,"Dave",3.14,["Make",7,9,[100,101]],10] #列表中的元素可以包含任意类型的 Python 对象
g[1] #返回 Dave
g[3][2] #返回 9
g[3][3][1] #返回 101
```

# 6) 元组

Python 语言中创建元组非常简单,只需要把一组数值放入圆括号中即可创建元组。我们同样可以创建包含 0 个元素和 1 个元素的元组。和列表一样,也可以使用数字索引来提取元组中的值。然而,更常见的做法是将元组解包为一组变量。