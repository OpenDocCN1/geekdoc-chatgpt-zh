
### 4.2.8 错误类

作为一个完整的框架，有它自己的错误处理。TensorFlow 中的错误类如表 4-16 所示，该部分不常用，可以作为工具，使用时查询一下即可。

表 4-16 错误类

| 操作 | 描述 |
|---|---|
| class tf.OpError | 一个基本的错误类型，在当TF执行失败时报错 |
| tf.OpError.op | 返回执行失败的操作节点，有的操作如Send或Recv可能不会返回，则要用到node_def方法 |
| tf.OpError.node_def | 以NodeDef proto形式表示失败的OP |
| tf.OpError.error_code | 描述该错误的整数错误代码 |
| tf.OpError.message | 返回错误信息 |
| class tf.errors.CancelledError | 当操作或者阶段取消时报错 |
| class tf.errors.UnknownError | 未知错误类型 |
| class tf.errors.InvalidArgumentError | 在接收到非法参数时报错 |
| class tf.errors.NotFoundError | 当发现不存在所请求的一个实体时，比如文件或目录 |
| class tf.errors.AlreadyExistsError | 当创建的实体已经存在时报错 |
| class tf.errors.PermissionDeniedError | 没有执行权限做某操作时报错 |
| class tf.errors.ResourceExhaustedError | 资源耗尽时报错 |
| class tf.errors.FailedPreconditionError | 系统没有条件执行某个行为时报错 |
| class tf.errors.AbortedError | 操作中止时报错，常常发生在并发情形 |
| class tf.errors.OutOfRangeError | 超出范围报错 |
| class tf.errors.UnimplementedError | 某个操作没有执行时报错 |
| class tf.errors.InternalError | 当系统经历了一个内部错误时报出 |
| class tf.errors.DataLossError | 当出现不可恢复的错误，例如在运行 tf.WholeFileReader.read 读取整个文件的同时文件被删除 |
| tf.errors.XXXX.__init__(node_def, op, message) | 使用该方式创建以上各种错误类 |

## 4.3 共享变量

下面来到本章的重点——共享变量。共享变量在复杂的网络中用处非常之广泛，所以读者一定要学好。

### 4.3.1 共享变量用途

在构建模型时，需要使用 tf.Variable 来创建一个变量（也可以理解成节点）。例如代码：

biases = tf.Variable(tf.zeros([2]), name="biases")
#创建一个偏置的学习参数，在训练时，这个变量不断地更新

但在某些情况下，一个模型需要使用其他模型创建的变量，两个模型一起训练。比如，对抗网络中的生成器模型与判别器模型（后文 12 章会有详细讲解）。如果使用 tf.Variable，将会生成一个新的变量，而我们需要的是原来的那个 biases 变量。这时怎么办呢？

这时就是通过引入 get_variable 方法，实现共享变量来解决这个问题。这种方法可以使用多套网络模型来训练一套权重。

### 4.3.2 使用 get_variable 获取变量

get_variable 一般会配合 variable_scope 一起使用，以实现共享变量。variable_scope 的意思是变量作用域。在某一作用域中的变量可以被设置成共享的方式，被其他网络模型使用。后文的 4.3.4 节中会有共享变量的实例。下面先介绍下 get_variable 的详细使用。

get_variable 函数的定义如下：
tf.get_variable(<name>, <shape>, <initializer>)

在 TensorFlow 里，使用 get_variable 生成的变量是以指定的 name 属性为唯一标识，并不是定义的变量名称。使用时一般通过 name 属性定位到具体变量，并将其共享到其他模型中。

下面通过两个例子来深入介绍。

### 4.3.3 实例 14: 演示 get_variable 和 Variable 的区别

### 实例描述

分别使用 Variable 定义变量和使用 get_variable 来定义变量。请读者仔细观察它们的用法区别。

### 1. Variable的用法

首先先来看一下 Variable 的用法。

代码4-9 get_variable和Variable的区别

```
import tensorflow as tf

var1 = tf.Variable(1.0 , name='firstvar')
print ("var1:",var1.name)
var1 = tf.Variable(2.0 , name='firstvar')
print ("var1:",var1.name)
var2 = tf.Variable(3.0 )
print ("var2:",var2.name)
var2 = tf.Variable(4.0 )
print ("var1:",var2.name)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print("var1=",var1.eval())
    print("var2=",var2.eval())
```

上面的代码运行后输出如下：

```
var1: firstvar:0
var1: firstvar_1:0
var2: Variable:0
var1: Variable_1:0
var1= 2.0
var2= 4.0
```

上面代码中定义了两次 var1，可以看到在内存中生成了两个 var1（因为它们的 name 不一样），对于图来讲后面的 var1 是生效的（var1=2.0）。

var2 表明了：Variable 定义时没有指定名字，系统会自动给加上一个名字 Variable:0。

### 2. get_variable用法演示

接着上面的代码，使用 get_variable 添加 get_var1 变量。

代码4-9 get_variable和Variable的区别（续）

```
get_var1 = tf.get_variable("firsttvar",[1], initializer=tf.constant_initializer(0.3))
print ("get_var1:",get_var1.name)

get_var1 = tf.get_variable("firsttvar",[1], initializer=tf.constant_initializer(0.4))
print ("get_var1:",get_var1.name)
```

代码运行之后结果如下：

```
var1: firstvar:0
var1: firstvar_1:0
var2: Variable:0
var1: Variable_1:0
var1= 2.0
var2= 4.0
get_var1: firsttvar_2:0
Traceback (most recent call last):
......
```

可以看到，程序在定义第 2 个 get_var1 时发生崩溃了。这表明，使用 get_variable 只能定义一次指定名称的变量。同时由于变量 firsttvar 在前面使用 Variable 函数生成过一次，所以系统自动变成了 firsttvar_2:0。

如果将崩溃的句子改成下面的样子：

代码4-9 get_variable和Variable的区别（续）

```
get_var1 = tf.get_variable("firstvar", [1], initializer=tf.constant_initializer(0.3))
print ("get_var1:",get_var1.name)

get_var1 = tf.get_variable("firstvar1", [1], initializer=tf.constant_initializer(0.4))
print ("get_var1:",get_var1.name)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print("get_var1=",get_var1.eval())
```

运行代码，输出如下：（部分内容）

```
get_var1: firstvar_2:0
get_var1: firstvar1:0
get_var1= [ 0.40000001]
```

可以看到，这次仍然是又定义了一个 get_var1，不同的是改变了它的名字 firstvar1，这样就没有问题了。同样，新的 get_var1 会在图中生效，所以它的输出值是 0.4 而不是 0.3。

### 4.3.4 实例 15：在特定的作用域下获取变量

### 实例描述

在作用域下，使用 get_variable，以及嵌套 variable_scope。

在前面的例子中，大家已经知道使用 get_variable 创建两个同样名字的变量是行不通的，如下代码会报错。

```
var1 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)
var2 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)
```

如果真的想要那么做，可以使用 variable_scope 将它们隔开，代码如下。

代码4-10 get_variable配合variable_scope

```
import tensorflow as tf
with tf.variable_scope("test1", ):     #定义一个作用域 test1
    var1 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)

with tf.variable_scope("test2"):
    var2 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)

print ("var1:",var1.name)
print ("var2:",var2.name)
```

运行代码，输出结果如下：

```
var1: test1/firstvar:0
var2: test2/firstvar:0
```

var1 和 var2 都使用 firstvar 的名字来定义。通过输出可以看出，其实生成的两个变量 var1 和 var2 是不同的，它们作用在不同的 scope 下，这就是 scope 的作用。

scope 还支持嵌套，将上面代码中的第二个 scope 缩进一下，得到如下代码：

代码4-11 get_variable配合variable_scope2

```
with tf.variable_scope("test1", ):
    var1 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)

    with tf.variable_scope("test2"):
        var2 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)

print ("var1:",var1.name)
print ("var2:",var2.name)
```

运行代码，输出结果如下：

var1: test1/firstvar:0
var2: test1/test2/firstvar:0

### 4.3.5 实例 16：共享变量功能的实现

### 实例描述

使用作用域中的 reuse 参数来实现共享变量功能。

费了这么大的劲来使用 get_variable，目的其实是为了要通过它实现共享变量的功能。

variable_scope 里面有个 reuse=True 属性，表示使用已经定义过的变量。这时 get_variable 将不会再创建新的变量，而是去图（一个计算任务）中 get_variable 所创建过的变量中找与 name 相同的变量。

在上文代码中再建立一个同样的 scope，并且设置 reuse=True，实现共享 firstvar 变量。

代码4-11 get_variable配合variable_scope2（续）

```
with tf.variable_scope("test1",reuse=True) :
    var3= tf.get_variable("firstvar",shape=[2],dtype=tf.float32)
    with tf.variable_scope("test2") :
        var4 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)

print ("var3:",var3.name)
print ("var4:",var4.name)
```

运行上面代码，输出如下：

var1: test1/firstvar:0
var2: test1/test2/firstvar:0
var3: test1/firstvar:0
var4: test1/test2/firstvar:0

var1 和 var3 的输出名字是一样的，var2 和 var4 的名字也是一样的。这表明 var1 和 var3 共用了一个变量，var2 和 var4 共用了一个变量，这就实现了共享变量。在实际应用中，可以把 var1 和 var2 放到一个网络模型里去训练，把 var3 和 var4 放到另一个网络模型里去训练，而两个模型的训练结果都会作用于一个模型的学习参数上。

⚠️注意：如果读者使用的是 Anaconda 工具包里面的 Spyder 工具（第 2 章介绍过）运行，该代码只能运行一次，第二次会报错。解决办法：需要在 Anaconda 的 Consoles 菜单里退出当前的 kernel，再重新进入一下。再运行才不会报错。否则会提示已经有这个变量了。为什么会这样呢？tf.get_variable 在创建变量时，会去检查图（一个计算任务）中是否已经创建过该变量。如果创建过并且本次调用时没有被设为共享方式，则会报错。明白原理后可以加一条语句 tf.reset_default_graph()，将图（一个计算任务）里面的变量清空，就可以解决这个问题了。图（一个计算任务）的更多内容将在后面章节介绍。

### 4.3.6 实例 17：初始化共享变量的作用域

### 实例描述

演示 variable_scope 中 get_variable 初始化的继承功能，以及嵌套 variable_scope 的继承功能。

variable_scope 和 get_variable 都有初始化的功能。在初始化时，如果没有对当前变量初始化，则 TensorFlow 会默认使用作用域的初始化方法对其初始化，并且作用域的初始化方法也有继承功能。下面演示代码。

代码4-12 共享变量的作用域与初始化

```
import tensorflow as tf
with tf.variable_scope("test1", initializer=tf.constant_initializer(0.4) ):
    var1 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)
with tf.variable_scope("test2"):
    var2 = tf.get_variable("firstvar",shape=[2],dtype=tf.float32)
    var3 = tf.get_variable("var3",shape=[2],initializer=tf.constant_initializer(0.3))
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print("var1=",var1.eval()) #作用域 test1 下的变量
    print("var2=",var2.eval()) #作用域 test2 下的变量，继承 test1 初始化
    print("var3=",var3.eval()) #作用域 test2 下的变量
```

上述代码大致操作如下：

- 将 test1 作用域进行初始化为 4.0，见代码第 3 行。
- var1 没有初始化，见代码第 4 行。
- 嵌套的 test2 作用域也没有初始化，见代码第 6 行。
- test2 下的 var3 进行了初始化，见代码第 8 行。

运行代码，输出如下：

```
var1= [ 0.40000001 0.40000001]
var2= [ 0.40000001 0.40000001]
var3= [ 0.30000001 0.30000001]
```

var1 数组值为 0.4，表明继承了 test1 的值；var2 数组值为 0.4，表明其所在的作用域 test2 也继承了 test1 的初始化；变量 var3 在创建时同步指定了初始化操作，所以数组值为 0.3。

> 注意：在多模型训练中，常常会使用 variable_scope 对模型间的张量进行区分。同时，统一为学习参数进行默认的初始化。在变量共享方面，还可以使用 tf.AUTO_REUSE 来为 reuse 属性赋值。tf.AUTO_REUSE 可以实现第一次调用 variable_scope 时，传入的 reuse 值是 False；再次调用 variable_scope 时，传入 reuse 的值就会自动变为 True。

### 4.3.7 实例 18： 演示作用域与操作符的受限范围

### 实例描述

演示 variable_scope 的 as 用法，以及对应的作用域。
variable_scope 还可以使用 with variable_scope ("name") as xxxscope 的方式定义作用域，当使用这种方式时，所定义的作用域变量 xxxscope 将不再受到外围的 scope 所限制。
看下面的例子。

代码4-13 作用域与操作符的受限范围

```
import tensorflow as tf

with tf.variable_scope("scope1") as sp:
    var1 = tf.get_variable("v", [1])

print("sp:",sp.name) #作用域名称
print("var1:",var1.name)

with tf.variable_scope("scope2") :
    var2 = tf.get_variable("v", [1])

    with tf.variable_scope(sp) as spl:
        var3 = tf.get_variable("v3", [1])

print("spl:",spl.name)
```
