```
40  optimizer = tf.train.GradientDescentOptimizer(learning_rate).
41  minimize(cost,global_step=global_step)　#梯度下降
42  saver = tf.train.Saver(max_to_keep=1)
43  merged_summary_op = tf.summary.merge_all()　#合并所有 summary
44
45  init = tf.global_variables_initializer()
```

为了使载入检查点文件时能够同步循环次数，这里加了一个 global_step 变量，并将其放到优化器中。这样，每次运行一次优化器，global_step 就会自动获得当期迭代的次数。

> 注意：init = tf.global_variables_initializer()这个代码是将其前面的变量全部初始化，如果后面再有变量，则不会被初始化。所以，一般要将 init = tf.global_variables_initializer()这个代码放在最后。这是个很容易出错的地方，常常令开发者找不到头绪。读者也可以试着在最前面运行，看看会发生什么。

# 4. 创建Supervisor，管理session

# 代码4-15 ps（续）

```
46 # 定义参数
47 training_epochs = 2200
48 display_step = 2
49
50 sv = tf.train.Supervisor(is_chief=(task_index == 0),#0 号 worker 为 chief
51    logdir="log/super/",
52    init_op=init,
53    summary_op=None,
54    saver=saver,
55    global_step=global_step,
56    save_model_secs=5)
57
58 #连接目标角色创建 session
59 with sv.managed_session(server.target) as sess:
```

在 tf.train.Supervisor 函数中，is_chief 表明了是否为 chief supervisors 角色。这里将 task_index=0 的 worker 设置成 chief supervisors。

logdir 为检查点文件和 summary 文件保存的路径。

init_op 表示使用初始化变量的函数。

saver 需要将保存检查点的 saver 对象传入，supervisor 就会自动保存检查点文件。如果不想自动保存，可以设为 None。

同理，summary_op 也是自动保存 summary 文件。这里设为 None，表示不自动保存。

save_model_secs 为保存检查点文件的时间间隔。这里设为 5，表示每 5 秒自动保存一次检查点文件。以上代码，为了让分布运算的效果明显一些，将迭代次数改成了 2200，