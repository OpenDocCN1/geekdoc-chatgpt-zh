![](bbox=[0.26, 0.09, 0.7, 0.38])

```
#参数:
#eps 时间分辨率
#damping 波阻尼
eps = tf.placeholder(tf.float32, shape=())
damping = tf.placeholder(tf.float32, shape=())

#为模拟状态创建变量
U = tf.Variable(u_init)
Ut = tf.Variable(ut_init)

#离散 PDE 更新规则
U_ = U + eps * Ut
Ut_ = Ut + eps * (Laplace(U) - damping * Ut)

#更新状态的操作
step = tf.group(
    U.assign(U_),
    Ut.assign(Ut_))
```

# 10.3 仿真

在仿真过程中，加入 for 循环代码部分来实现仿真效果的清晰化，代码如下。

```
#初始化状态到初始条件
tf.initialize_all_variables().run()

#Run 1000 steps of PDE
for i in range(1000):
    #步骤模拟
    step.run({eps: 0.03, damping: 0.04})
    #Visualize every 50 steps
```