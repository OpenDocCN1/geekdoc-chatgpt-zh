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

# 11）对象和类

Python 语言中一切都是对象。对象由内部数据和各种方法组成,这些方法