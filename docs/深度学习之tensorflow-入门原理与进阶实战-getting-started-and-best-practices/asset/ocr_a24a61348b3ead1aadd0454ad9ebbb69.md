```
for i in range(0, 10):
    print ("------------------------------")
    print(sess.run(queue.dequeue()))
```

coord.request_stop() #通知其他线程关闭 其他所有线程关闭之后，这一函数才能返回

运行以上代码，输出如下信息：（可以看到并没有报错）

```
3.0
----------------------------------
405.0
----------------------------------
410.0
----------------------------------
413.0
----------------------------------
420.0
----------------------------------
475.0
----------------------------------
478.0
----------------------------------
896.0
----------------------------------
902.0
----------------------------------
904.0
```

这里还可以使用 coord.join(enqueue_threads)指定等待某个进程结束。

# 8.5.8 实例 43: 为 session 中的队列加上协调器

这里将上例中的 coord 放到启动队列里即可。

# 实例描述

在 with tf.Session 函数中加入启动队列，并通过加入 coord 协调器的方式使 session close 时同步内部线程一起退出。

修改 “8-5cifar” 代码如下。

# 代码8-8 cifar队列协调器（部分代码）

```
with tf.Session() as sess:
    tf.global_variables_initializer().run()
    #定义协调器
    coord = tf.train.Coordinator()
    threads = tf.train.start_queue_runners(sess, coord)

    image_batch, label_batch = sess.run([images_test, labels_test])
    print(" \n",image_batch[0])
    print(" \n",label_batch[0])
```