# 6. 计算loss，定义优化器

将判别器的结果裁开，分别得到真实图片与生成图片判别的结果，以 LSGAN 的方式计算生成器与判别器的 loss，在生成器 loss 中加入基于特征空间的 loss。按照前面第 3 步中所讲的训练参数的获取方式获得判别器训练参数 disc_var_list，使用 AdamOptimizer 优化 loss 值。代码如下：

```
代码12-9 rsgan (续)

71 disc=Discriminator(dbatch)
72 D_x,D_G_z=tf.split(tf.squeeze(disc),2)

73
74 adv_loss=tf.reduce_mean(tf.square(D_G_z-1.0))
75
76 gen_loss=(adv_loss+content_loss)
77 disc_loss=(tf.reduce_mean(tf.square(D_x-1.0)+tf.square(D_G_z)))
78
79 disc_var_list=tf.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)
80 print("len------",len(disc_var_list),len(gen_var_list))
81 for x in gen_var_list:
82     disc_var_list.remove(x)

83
84 learn_rate=0.001
85 global_step=tf.Variable(0,trainable=0,name='global_step')
86 gen_train_step=tf.train.AdamOptimizer(learn_rate).minimize
(gen_loss,global_step,gen_var_list)
87 disc_train_step=tf.train.AdamOptimizer(learn_rate).minimize
(disc_loss,global_step,disc_var_list)
```

# 7. 指定准备载入的预训练模型路径

这次需要对 3 个检查点路径进行配置，第一个是本程序的 SRGAN 检查点文件，第二个是 srResNet 检查点文件，最后一个是 VGG 模型文件。

```
代码12-9 rsgan (续)

88 #残差网络检查点文件相关定义
89 flags='b'+str(batch_size)+'r'+str(np.int32(height/4))+'_r'+str
(learn_rate)+'rsgan'
90 save_path='save/srgan_'+flags
91 if not os.path.exists(save_path):
92     os.mkdir(save_path)
93 saver = tf.train.Saver(max_to_keep=1) # 生成 saver
94
95 srResNet_path='./save/tf_b16_h64.0_r0.001_res/'
96 srResNetloader = tf.train.Saver(var_list=gen_var_list) # 生成 saver
```