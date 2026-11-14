
```
40  optimizer = tf.train.GradientDescentOptimizer(learning_rate)
41  minimize(cost, global_step=global_step) # 梯度下降
42  saver = tf.train.Saver(max_to_keep=1)
43  merged_summary_op = tf.summary.merge_all() # 合并所有 summary
44
45  init = tf.global_variables_initializer()
```

为了使载入检查点文件时能够同步循环次数，这里加了一个 global_step 变量，并将其放到优化器中。这样，每次运行一次优化器，global_step 就会自动获得当前迭代的次数。

> 注意：init = tf.global_variables_initializer()这个代码是将其前面的变量全部初始化，如果后面再有变量，则不会被初始化。所以，一般要将 init = tf.global_variables_initializer()这个代码放在最后。这是个很容易出错的地方，常常令开发者找不到头绪。读者也可以试着在最前面运行，看看会发生什么。

## 4. 创建Supervisor，管理session

### 代码4-15 ps（续）

```
46 # 定义参数
47 training_epochs = 2200
48 display_step = 2
49
50 sv = tf.train.Supervisor(is_chief=(task_index == 0), # 0 号 worker 为 chief
51    logdir="log/super/",
52    init_op=init,
53    summary_op=None,
54    saver=saver,
55    global_step=global_step,
56    save_model_secs=5)
57
58 # 连接目标角色创建 session
59 with sv.managed_session(server.target) as sess:
```

在 tf.train.Supervisor 函数中，is_chief 表明了是否为 chief supervisors 角色。这里将 task_index=0 的 worker 设置成 chief supervisors。

logdir 为检查点文件和 summary 文件保存的路径。

init_op 表示使用初始化变量的函数。

saver 需要将保存检查点的 saver 对象传入，supervisor 就会自动保存检查点文件。如果不想自动保存，可以设为 None。

同理，summary_op 也是自动保存 summary 文件。这里设为 None，表示不自动保存。

save_model_secs 为保存检查点文件的时间间隔。这里设为 5，表示每 5 秒自动保存一次检查点文件。以上代码，为了让分布运算的效果明显一些，将迭代次数改成了 2200，使其运算时间变长。

## 5. 迭代训练

session 中的内容与以前一样，直接迭代训练即可。由于使用了 supervisor 管理 session，将使用 sv.summary_computed 函数来保存 summary 文件。同样，如想要手动保存检查点文件，也可以使用 sv.saver.save。代码如下：

```
60 print("sess ok")
61 print(global_step.eval(session=sess))
62 
63 for epoch in range(global_step.eval(session=sess), training_epochs*len(train_X)):
64 
65 for (x, y) in zip(train_X, train_Y):
66 _, epoch = sess.run([optimizer, global_step] ,feed_dict={X: x, Y: y})
67 # 生成 summary
68 summary_str = sess.run(merged_summary_op, feed_dict={X: x, Y: y});
69 # 将 summary 写入文件
70 sv.summary_computed(sess, summary_str, global_step=epoch)
71 if epoch % display_step == 0:
72 loss = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
73 print ("Epoch:", epoch+1, "cost=", loss, "W=", sess.run(W), "b=", sess.run(b))
74 if not (loss == "NA" ) :
75 plotdata["batchsize"].append(epoch)
76 plotdata["loss"].append(loss)
77 
78 print (" Finished!")
79 sv.saver.save(sess, "log/mnist_with_summaries/" + "sv.ckpt", global_step=epoch)
80 
81 sv.stop()
```

- 注意：
  - (1) 在设置自动保存检查点文件后，手动保存仍然有效。
  - (2) 在运行一半后终止，再运行 supervisor 时会自动载入模型的参数，不需要手动调用 saver.restore。
  - (3) 在 session 中，不需要再运行 tf.global_variables_initializer 函数。原因是 supervisor 在建立时会调用传入的 init_op 进行初始化，如果加了 sess.run(tf.global_variables_initializer())，则会导致所载入模型的变量被二次清空。

## 6. 建立worker文件

将文件复制两份，分别起名为“4-16 worker.py”与“4-17 worker2.py”，将角色名称修改成 worker，并将“4-17 worker2.py”中的 task_index 设为 1。

### 代码4-16 worker

```
......
# 定义角色名称
strjob_name = "worker"
task_index = 0
......
```

### 代码4-17 worker2

```
......
# 定义角色名称
strjob_name = "worker"
task_index = 1
......
```

注意：这个例子中使用了 summary 的一些方法将运行时态的数据保存起来，以便于使用 TensorBoard 进行查看（见 4.1.16 节）。但在分布式部署时，使用该功能还需要注意以下几点：
- 不能使用 sv.summary_computed，因为 worker2 不是 chief supervisors，在 worker2 中是不会为 supervisor 对象构造默认 summary_writer 的(所有的 summary 信息都要通过该对象进行写入)，所以即使调用 summary_computed 也无法执行下去，程序会报错。
- 手写控制 summary 与检查点文件保存时，需要将 chief supervisors 以外的 worker 全部去掉才可以。可以使用 supervisor 按时间间隔保存的形式来管理，这样用一套代码就可以解决了。

## 7. 部署运行

（1）在 Spyder 中先将“4-15 ps.py”文件运行起来，选择菜单 Consoles|Open an IPython console 命令，新打开一个 Consoles，如图 4-7 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_98_0.png)

图 4-7 Consoles 菜单

（2）在 Spyder 面板的右下角（见图 2-13 中的输出栏），可以看到到原有标题为“Console 1/A”标签旁边又多了一个“Console 2/A”标签（如图 4-8 所示），单击该标签，使其处于激活状态。

![](img/d841d305683d65f55b29f4e3f25e532c_99_0.png)

图 4-8 Consoles 2/A 标签

（3）运行 4-17 worker2.py 文件。最后按照 “4-17worker2.py” 文件启动的方式，启动 4-16 worker.py 文件，这时 3 个窗口的显示内容分别如下：

- “4-16worker.py” 文件对应窗口显示正常的训练信息。

```
......
Epoch: 8000 cost= 0.0754263 W= [ 2.01029539] b= [-0.00388618]
Epoch: 8002 cost= 0.074845 W= [ 2.00651097] b= [ 0.00453186]
Epoch: 8003 cost= 0.0748089 W= [ 2.00529122] b= [ 0.00281144]
Epoch: 8005 cost= 0.0747555 W= [ 2.00324082] b= [ 0.00635108]
Epoch: 8007 cost= 0.075026 W= [ 2.00662613] b= [-0.00956773]
Epoch: 8009 cost= 0.0749311 W= [ 2.00585985] b= [-0.006533]
Epoch: 8010 cost= 0.0748186 W= [ 2.00469637] b= [-0.00152527]
Epoch: 8011 cost= 0.0750369 W= [ 2.0065136] b= [-0.02676161]
Epoch: 8012 cost= 0.0758979 W= [ 2.0068512] b= [-0.02852018]
Epoch: 8013 cost= 0.0759059 W= [ 2.00671506] b= [-0.02870713]
Epoch: 8015 cost= 0.0753608 W= [ 2.0055182] b= [-0.01959283]
Epoch: 8018 cost= 0.0760464 W= [ 2.00559783] b= [-0.03230772]
Epoch: 8021 cost= 0.0758819 W= [ 2.00522184] b= [-0.02836083]
Epoch: 8023 cost= 0.0758949 W= [ 2.00778055] b= [-0.01191433]
Epoch: 8026 cost= 0.0752242 W= [ 2.00646138] b= [-0.01574964]
Epoch: 8028 cost= 0.0751021 W= [ 2.00708318] b= [-0.01172168]
Epoch: 8030 cost= 0.0749788 W= [ 2.0083425] b= [-0.00503741]
Epoch: 8034 cost= 0.0750521 W= [ 2.00837708] b= [-0.0084667]
Epoch: 8035 cost= 0.0750075 W= [ 2.01157689] b= [ 0.00467709]
Epoch: 8037 cost= 0.0751661 W= [ 2.01191807] b= [ 0.0159377]
Epoch: 8038 cost= 0.0750556 W= [ 2.01164842] b= [ 0.01059892]
Epoch: 8040 cost= 0.0753085 W= [ 2.01313496] b= [ 0.01954099]
Epoch: 8042 cost= 0.0753466 W= [ 2.01260543] b= [ 0.02123925]
......
```

可以看到循环的次数并不是连续的，跳过的步骤被分配到 worker2 中去运算了。

- “4-17worker2.py” 文件对应窗口显示的信息如下：

```
INFO:tensorflow:Waiting for model to be ready. Ready_for_local_init_op: None, ready: Variables not initialized: weight, bias, global_step
INFO:tensorflow:Starting queue runners.
......
Epoch: 8003 cost= 0.0977818 W= [ 2.00529122] b= [ 0.00281144]
Epoch: 8005 cost= 0.0979236 W= [ 2.00324082] b= [ 0.00635108]
Epoch: 8007 cost= 0.0978101 W= [ 2.0065136] b= [-0.01009204]
Epoch: 8012 cost= 0.0985371 W= [ 2.00671506] b= [-0.02870713]
Epoch: 8015 cost= 0.0981559 W= [ 2.0055182] b= [-0.01959283]
Epoch: 8017 cost= 0.0986897 W= [ 2.00519013] b= [-0.02992464]
Epoch: 8018 cost= 0.0987787 W= [ 2.00559783] b= [-0.03128441]
Epoch: 8020 cost= 0.0988223 W= [ 2.00550485] b= [-0.02906012]
Epoch: 8022 cost= 0.0985962 W= [ 2.00522184] b= [-0.02918861]
Epoch: 8024 cost= 0.0982481 W= [ 2.00616717] b= [-0.02256276]
Epoch: 8025 cost= 0.0977918 W= [ 2.00778055] b= [-0.01191433]
Epoch: 8026 cost= 0.0979684 W= [ 2.00646138] b= [-0.01574964]
Epoch: 8028 cost= 0.0978234 W= [ 2.00708318] b= [-0.01172168]
Epoch: 8030 cost= 0.0976372 W= [ 2.00842071] b= [-0.00485691]
Epoch: 8031 cost= 0.0976208 W= [ 2.00859952] b= [-0.00408681]
Epoch: 8032 cost= 0.0976431 W= [ 2.0083425] b= [-0.00503741]
Epoch: 8034 cost= 0.097557 W= [ 2.01164842] b= [ 0.01059892]
Epoch: 8039 cost= 0.0975473 W= [ 2.01065278] b= [ 0.00720035]
Epoch: 8040 cost= 0.0977502 W= [ 2.01313496] b= [ 0.01954099]
Epoch: 8042 cost= 0.0978443 W= [ 2.01260543] b= [ 0.02123925]
......
```

显示结果中有警告输出，这是因为在构建 supervisor 时没有填写 local_init_op 参数，该参数的意思是在创建 worker 实例时，初始化本地变量。由于例子中没有填，系统就会自动初始化，并给出警告提示。

从日志中可以看到 worker2 与 chief supervisors 的迭代序号近似互补，为什么没有绝对互补呢？可能与 supervisor 中的同步算法有关。

分布运算目的是为了提高整体运算速度，如果同步 epoch 的准确度需要以牺牲总体运算速度为代价，自然很不合适。所以更合理的推断是因为单机单次的运算太快迫使算法使用了更宽松的同步机制。

重要的一点是对于指定步数的学习参数 b 和 w 是一致的（如第 8040 步，学习参数是相同的，都为 W=[ 2.01313496] b=[ 0.01954099]），这表明两个终端是在相同的起点上进行运算的。

- 对于 4-15ps.py 文件，其对应窗口则是一直静默着只显示打印的那句 wait，因为它只负责连接不参与运算。

## 4.6 动态图（Eager）

动态图是相对于静态图而言的。所谓的动态图是指在 Python 中代码被调用后，其操作立即被执行的计算。其与静态图最大的区别是不需要使用 session 来建立会话了。即，在静态图中，需要在会话中调用 run 方法才可以获得某个张量的具体值；而在动态图中，直接运行就可以得到具体值了。

动态图是在 TensorFlow 1.3 版本之后出现的。它使 TensorFlow 的入门变得更简单，也使研发更直观。

启用动态图只需要在程序的最开始处加上两行代码：

```
import tensorflow.contrib.eager as tfe
tfe.enable_eager_execution()
```

这两行代码的作用就是开启动态图计算功能。例如，调用 tf.matmul 时，将会立即计算两个数组相乘的值，而不是一个 op。

Eager 还处于一个试用阶段，也是 TensorFlow 大力推广的新特性，未来或许会成为趋势。想了解更多内容，可以参考如下网址：

https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/eager。

在创建动态图的过程中，默认也建立了一个 session。所有的代码都在该 session 中进行，而且该 session 具有进程相同的生命周期。这表明一旦使用动态图就无法实现静态图中关闭 session 的功能。这便是动态图的不足之处：无法实现多 session 操作。如果当前代码只需要一个 session 来完成的话，建议优先选择动态图 Eager 来实现。

## 4.7 数据集（ tf.data ）

TensorFlow 中有 3 种数据输入模式：

- 直接使用 feed_dict 利用注入模式进行数据输入（见 4.1.4 节），适用于少量的数据集输入；
- 使用队列式管道（见 11.5.3 节），适用于大量的数据集输入；
- 性能更高的输入管道，适用于 TensorFlow 1.4 之后的版本，是为动态图（见 4.6 节）功能提供的大数据集输入方案（动态图的数据集输入只能使用该方法），当然也支持静态图。

关于第 3 种方式的更多介绍，请参考以下链接：

https://github.com/tensorflow/tensorflow/blob/master/tensorflow/contrib/eager/python/g3doc/guide.md#input-pipelines
