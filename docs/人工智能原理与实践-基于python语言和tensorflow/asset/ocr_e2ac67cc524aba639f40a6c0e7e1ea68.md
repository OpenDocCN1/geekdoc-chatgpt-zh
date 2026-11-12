# 9.2 会话和变量初始化

为了操作的方便，通常会使用交互式会话（ Interactive Session ），但是一般性的会话（ Regular Session ）也可以被正常使用，代码如下。

```
sess=tf.InteractiveSession()
Y,X=np.mgrid[-1.3:1.3:0.005, -2:1:0.005]
Z=X+1j*Y
xs=tf.constant(Z.astype("complex64"))
zs = tf.Variable(xs)
ns=tf.Variable(tf.zeros_like(xs,"float32"))
tf.initialize_all_variables().run()
```

源程序代码内容中，通过第 1 行内容的使用，来指定一个交互式会话；通过第 2～3 行内容的使用，来创建一个数组，接着使用 NumPy 模块来创建一个 2 维的复数数组；通过第 4～6 行内容的使用，来定义一组 TensorFlow 的张量，并对这个张量进行初始化；通过第 7 行内容的使用，将变量的初始值进行确定。

# 9.3 定义并运行计算

接下来指定更多的计算内容，代码如下。

```
zs = zs*zs+xs
not_diverged=tf.abs(zs_)<4
step=tf.group(
    zs.assign(zs_),
    ns.assign_add(tf.cast(not_diverged, tf.float32))
)
for i in range(200):step.run()
DisplayFractal(ns.eval())
```

源程序代码内容中，通过第 1 行内容的使用，来进行一个新值 zs 的计算：zs²+xs；通过第 2 行内容的使用，来对新值 zs 是否发散进行判定；通过第 3～6 行内容的使用，用来对 zs 进行更新并迭代计算；通过第 7 行内容的使用，使用 for 循环来进行一个 200 次的重复执行；通过第 8 行内容的使用，完成最终运行结果图案的呈现。最终结果图案如图 9-3 所示。

![](bbox=[0.59, 0.605, 0.9, 0.785])

图 9-3