接着演示了如何访问该图中的变量：将 c1 的名字放到 get_tensor_by_name 里来反向得到其张量（见代码第 19 行），通过对 t 的打印可以看到所得的 t 就是前面定义的张量 c1。

> 注意：不必花太多精力去关注 TensorFlow 中默认的命名规则。一般在需要使用名字时，都会在定义的同时为它指定好固定的名字。如果真的不清楚某个元素的名字，可将其打印出来，回填到代码中，再次运行即可。

# 4.4.3 获取节点操作

获取节点操作 OP 的方法和获取张量的方法非常类似,使用的方法是 get_operation_by_name。下面将获取张量和获取 OP 的例子放在一起比较一下，具体代码如下。

```
代码4-14 图的基本操作（续）
21 a = tf.constant([[1.0, 2.0]])
22 b = tf.constant([[1.0], [3.0]])
23
24 tensor1 = tf.matmul(a, b, name='exampleop')
25 print(tensor1.name,tensor1)
26 test = g3.get_tensor_by_name("exampleop:0")
27 print(test)
28
29 print(tensor1.op.name)
30 testop = g3.get_operation_by_name("exampleop")
31 print(testop)
32
33 with tf.Session() as sess:
34     test = sess.run(test)
35     print(test)
36     test = tf.get_default_graph().get_tensor_by_name("exampleop:0")
37     print (test)
```

上面示例中，先将张量及其名字打印出来，然后使用 g3 图的 get_tensor_by_name 函数又获得了该张量,此时 test 和 tensor1 是一样的。为了证明这一点,直接把 test 放到 session 的 run 里，发现它运行后也能得到正确的结果。

> 注意：使用默认的图时，也可以用上述代码中的 tf.get_default_graph 函数获取当前图，然后可以调用 get_tensor_by_name 函数获取元素。

上面代码运行后会显示如下信息:

```
exampleop:0 Tensor("exampleop:0", shape=(1, 1), dtype=float32)
Tensor("exampleop:0", shape=(1, 1), dtype=float32)
exampleop
name: "exampleop"
op: "MatMul"
input: "Const"
input: "Const_1"
```