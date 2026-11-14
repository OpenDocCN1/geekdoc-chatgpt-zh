```
08 def leaky_relu(x,alpha=0.1,name='lrelu'):
09     with tf.name_scope(name):
10         x=tf.maximum(x,alpha*x)
11         return x
12 def residual_block(nn,i,name='resblock'):
13     with tf.variable_scope(name+str(i)):
14         conv1=slim.conv2d(nn, 64, 3,activation_fn = leaky_relu,
15                          normalizer_fn=slim.batch_norm)
15         conv2=slim.conv2d(conv1, 64, 3,activation_fn = leaky_relu,
16                          normalizer_fn=slim.batch_norm)
17         return tf.add(nn,conv2)
18 net = slim.conv2d(x_smalls2, 64, 5,activation_fn = leaky_relu)
19 block=[]
20 for i in range(16):
21     block.append(residual_block(block[-1] if i else net,i))
22 conv2=slim.conv2d(block[-1], 64, 3,activation_fn = leaky_relu,
23                  normalizer_fn=slim.batch_norm)
24 sum1=tf.add(conv2,net)
25 conv3=slim.conv2d(sum1, 256, 3,activation_fn = None)
26 ps1=tf.depth_to_space(conv3,2)
27 relu2=leaky_relu(ps1)
28 conv4=slim.conv2d(relu2, 256, 3,activation_fn = None)
29 ps2=tf.depth_to_space(conv4,2) #再放大两倍
30 relu3=leaky_relu(ps2)
31 y_predt=slim.conv2d(relu3, 3, 3,activation_fn = None) #输出
```

# 3. 修改学习率，进行网络训练

将学习率改为 0.001，同样使用 AdamOptimizer 优化方法，循环迭代 100 000 次开始训练。

```
32 ......
33 learn_rate = 0.001
34 
35 cost = tf.reduce_mean(tf.pow( tf.cast(images,tf.float32)/255.0 -
36     y_predt, 2))
37 optimizer = tf.train.AdamOptimizer(learn_rate ).minimize(cost)
37 training_epochs =10000
```

# 4. 添加检测点

网络结构的修改会使单次训练的时间变长，因此有必要添加检查点文件保存功能。先