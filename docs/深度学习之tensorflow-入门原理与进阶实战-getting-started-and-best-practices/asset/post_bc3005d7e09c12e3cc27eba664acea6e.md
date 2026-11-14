
```
'vgg_19/fc8'
]
self.assertSetEqual(set(end_points.keys()), set(expected_names))
```

这是一个单元测试函数，里面列举了 VGG19 网络结构中所有的节点名称。如上代码 'vgg_19/conv5/conv5_4' 就是本例中想要的节点，直接将该字符串复制放到本例代码中，如下所示。

### 代码12-9 rsgan (续)

```
50 #vgg 特征值
51 _, end_points = vgg.vgg_19(rgbmean, num_classes=1000, is_training=False,spatial_squeeze=False)
52 conv54=end_points['vgg_19/conv5/conv5_4']
53 print("vgg.conv5_4",conv54.shape)
54 fmap=tf.split(conv54,2)
55
56 content_loss=tf.losses.mean_squared_error(fmap[0],fmap[1])
```

由于前面通过 concat 将两个图片放在一起来处理，得到结果后，还要使用 split 将其分开，接着通过平方差算出基于特征空间的 loss。

### 5. 判别器的构建

判别器主要是通过一系列卷积层组合起来所构成的，最终使用两个全连接层实现映射到一维的输出结果。具体函数实现如下：

### 代码12-9 rsgan (续)

```
57 def Discriminator(dbatch, name ="Discriminator"):
58     with tf.variable_scope(name):
59         net = slim.conv2d(dbatch, 64, 1,activation_fn = leaky_relu)
60         .
61         ochannels=[64,128,128,256,256,512,512]
62         stride=[2,1]

64         for i in range(7):
65             net = slim.conv2d(net, ochannels[i], 3,stride = stride[i%2],activation_fn = leaky_relu,normalizer_fn=slim.batch_norm, scope='block'+str(i))

67         dense1 = slim.fully_connected(net, 1024, activation_fn=leaky_relu)
68         dense2 = slim.fully_connected(dense1, 1, activation_fn=tf.nn.sigmoid)

70     return dense2
```

### 6. 计算loss，定义优化器

将判别器的结果分开，分别得到真实图片与生成图片判别的结果，以 LSGAN 的方式计算生成器与判别器的 loss，在生成器 loss 中加入基于特征空间的 loss。按照前面第 3 步中所讲的训练参数的获取方式获得判别器训练参数 disc_var_list，使用 AdamOptimizer 优化 loss 值。代码如下：

### 代码12-9 rsgan (续)

```
71 disc=Discriminator(dbatch)
72 D_x,D_G_z=tf.split(tf.squeeze(disc),2)

74 adv_loss=tf.reduce_mean(tf.square(D_G_z-1.0))

76 gen_loss=(adv_loss+content_loss)
77 disc_loss=(tf.reduce_mean(tf.square(D_x-1.0)+tf.square(D_G_z)))

79 disc_var_list=tf.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)
80 print("len------",len(disc_var_list),len(gen_var_list))
81 for x in gen_var_list:
82     disc_var_list.remove(x)

84 learn_rate=0.001
85 global_step=tf.Variable(0,trainable=0,name='global_step')
86 gen_train_step=tf.train.AdamOptimizer(learn_rate).minimize(gen_loss,global_step,gen_var_list)
87 disc_train_step=tf.train.AdamOptimizer(learn_rate).minimize(disc_loss,global_step,disc_var_list)
```

### 7. 指定准备载入的预训练模型路径

这次需要对 3 个检查点路径进行配置，第一个是本程序的 SRGAN 检查点文件，第二个是 srResNet 检查点文件，最后一个是 VGG 模型文件。

### 代码12-9 rsgan (续)

```
88 #残差网络检查点文件相关定义
89 flags='b'+str(batch_size)+'r'+str(np.int32(height/4))+'_r'+str(learn_rate)+'rsgan'
90 save_path='save/srgan_'+flags
91 if not os.path.exists(save_path):
92     os.mkdir(save_path)
93 saver = tf.train.Saver(max_to_keep=1) # 生成 saver

95 srResNet_path='./save/tf_b16_h64.0_r0.001_res/'
96 srResNetloader = tf.train.Saver(var_list=gen_var_list) # 生成 saver
```

```
#VGG 检查点
checkpoints_dir = 'vgg_19_2016_08_28'
init_fn = slim.assign_from_checkpoint_fn(
os.path.join(checkpoints_dir, 'vgg_19.ckpt'),
slim.get_model_variables('vgg_19'))
```

注意：这里对于 srResNet 的变量恢复，要在建立 Saver 时传入 var_list 参数来指定好所要恢复的变量列表，否则默认是恢复所有变量，但是模型里却找不到其他变量，会报错误。

### 8. 启动session从检查点恢复变量

对于 VGG 模型的恢复，可以参考第 11 章中的内容。其他的检查点恢复的写法与前面一样。代码如下：

### 代码12-9 rsgan (续)

```
103 log_steps=100
104 training_epochs=16000

106 with tf.Session() as sess:

108 sess.run(tf.global_variables_initializer())

110 init_fn(sess)

112 kpt = tf.train.latest_checkpoint(srResNet_path)
113 print("srResNet_path",kpt,srResNet_path)
114 startepo= 0
115 if kpt!=None:
116 srResNetloader.restore(sess, kpt)
117 ind = kpt.find("-")
118 startepo = int(kpt[ind+1:])
119 print ("srResNetloader global_step=",global_step.eval(),startepo)

122 kpt = tf.train.latest_checkpoint(save_path)
123 print ("srgan",kpt)
124 startepo = 0
125 if kpt!=None:
126 saver.restore(sess, kpt)
127 ind = kpt.find("-")
128 startepo = int(kpt[ind+1:])
129 print ("global_step=",global_step.eval(),startepo)
```

### 9. 启动带协调器的队列线程，开始训练

本例中涉及的参数比较多，模型比较大，会导致每次迭代时间都很长，所以加入检测点是非常有必要的。这里涉及检查点保存的粒度，如间隔太短，因为频繁地写文件会减慢训练速度，如果设置的间隔太长，中途如发生意外暂停会导致浪费了一部分训练时间，可以通过 try 的方式在异常捕获时再保存一次检查点，这样可以把中途的训练结果保存下来。

### 代码12-9 rsgan (续)

```
129 coord = tf.train.Coordinator()
130     threads = tf.train.start_queue_runners(sess, coord)

132     try:
133         def train(endpoint, gen_step, disc_step):
134             while global_step.eval() <=endpoint:

136             if((global_step.eval() //2)%log_steps==0): # 一次走两步

138             d_batch=dbatch.eval()
139             mse,psnr=batch_mse_psnr(d_batch)
140             ssim=batch_ssim(d_batch)
141             s=time.strftime(' %Y-%m-%d %H:%M:%S ',time.localtime(time.time())) +'step='+str(global_step.eval())+'\nmse='+str(mse)+' psnr='+str(psnr)+' ssim='+str(ssim)+' gen_loss ='+str(gen_loss.eval())+' disc_loss='+str(disc_loss.eval())
142             print(s)
143             f=open('info.train_'+flags,'a')
144             f.write(s+'\n')
145             f.close()
146             saver.save(sess, save_path+"/srgan.ckpt", global_step=global_step.eval())

148             sess.run(disc_step)
149             sess.run(gen_step)

151     train(training_epochs,gen_train_step,disc_train_step)
152     print('训练完成')
153 ...... #显示部分同 resEspcn 例子，代码省略
154     except tf.errors.OutOfRangeError:
155         print('Done training -- epoch limit reached')
156     except KeyboardInterrupt:
157         print("Ending Training...")
158         saver.save(sess, save_path+"/srgan.ckpt", global_step=global_step.eval())
159     finally:
160         coord.request_stop()
161         coord.join(threads)
```

运行代码，生成结果如图 12-17 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_504_0.png)

图 12-17 中最后一张是模型生成的结果，每张图片上都有其评分值，可以看到 SRGAN 得到的 PSNR 和 SSIM 评价值不是最高的。但是我们肉眼看上去确实清晰了不少，并且通过有关机构对其进行 MOS（Mean Opinion Score）的评价也表明，SRGAN 生成的高分辨率图像看起来更真实。

> 注意：MOS（mean opinion score）采用主观评定和技术评定相结合的方式。所谓主观评定就是有人为的参与，用人来评定。

该例中只演示了运行迭代 1 万多次的效果。如果将 ResEspcn 的模型与 srGAN 的模型分别加一个数量级，还会得到效果更优质的图片，有兴趣的读者可以自行尝试。

## 12.9 GAN 网络的高级接口 TFGAN

TFGAN 是一个训练和评估生成式对抗网络（GAN）的轻量级库。它的初衷是为了让基于 GAN 的实验更加容易。

TFGAN 也是基于估算器开发的一种应用接口，使用 GANEstimator 类来进行模型训练的。在 TFGAN 中，会使用很多已经集成的技巧（tricks）来稳定和提升 GAN 网络的训练效果。同时也集成了对 GAN 训练步骤的监视和可视化操作，以及训练后的模型评估操作，为开发者节省了大量的编码和调参时间。

TFGAN 接口为开发者规范了开发 GAN 网络模型的标准步骤，每一个步骤都提供了全面的组件封装，使得开发者在开发 GAN 网络时，就像拼积木一样，按步骤选择不同的组件拼接起来即可。

TFGAN 接口中规范的 GAN 网络开发步骤如下：

- (1) 指定网络的输入。
- (2) 使用 GANModel 函数来设置生成器和判别器模型。
- (3) 使用 GANLoss 函数来指定 loss 值。
- (4) 使用 GANTrainOps 函数来创建训练操作。
- (5) 运行训练操作。

当然开发者也可以将 TFGAN 中已经实现了的损失值和惩罚处理（包括推土机距离损失、梯度惩罚、互信息惩罚等），集成到原生的 GAN 网络或是其他框架中。

更多关于 TFGAN 的信息见如下链接：

https://github.com/tensorflow/tensorflow/tree/master/tensorflow/contrib/gan。

该链接中包含的不仅仅是 TFGAN 的介绍，更多的是关于 TFGAN 实现各种 GAN 网络的例子代码。

在本书中，给出的均属于原生的 GAN 网络实例。目的是让读者掌握更底层的原理，以便于能够驾驭更有挑战性的任务。在实际应用中，使用 TFGAN 高级接口，可以起到事半功倍的效果，因此强烈推荐读者使用 TFGAN 高级接口。

## 12.10 总结

GAN 网络可以说是 2017 年深度学习领域最热门的技术，从图 12-18 中可以看出 2017 年起 GAN 的种类已经由 40 多种发展到了 250 多种，而且速度越来越快。

![](img/d841d305683d65f55b29f4e3f25e532c_505_0.png)

图 12-18 GAN 种类的发展

让网络来监督网络，使得深度学习在人工智能方向大踏步前进，由于篇幅所限，本书关于 GAN 网络的介绍只是冰山一角，GAN 还可以实现通过文字描述生成图片；将图片生成文字，进行图像风格的转移：为人脸生成带眼镜的照片，或将戴眼镜的照片还原成没带眼镜的照片；编写小说、诗歌等。另外，GAN 在通信加密、文本分类等领域也广泛应用。

在 TensorFlow 的官方 GitHub 中甚至还有使用 GAN 生成技术来扩充样本的工程。希望读者在结束本书的学习后不要停止脚步，人工智能的领域永无止尽，让我们一起努力用科学改变世界。

## 本书特色

- 内容全面，应用性强，从有监督学习到半监督学习，从简单的数据分类到语音、语言和图像分类乃至样本生成等一系列前沿技术均有介绍
- 介绍的96个典型实战案例，均有较高的应用价值
- 内容全面、深入、实用，讲解由浅入深、循序渐进
- 语言通俗易懂，拒绝生僻的术语，拒绝生僻的公式符号，落地性强
- 穿插了几十个需要读者注意的知识要点和难点，分享作者积累的宝贵经验
- 提供了12套配套教学视频，配合书籍讲解重点知识
- 提供了完整的源代码和训练数据集

## 业内点评

要学习AI，首先必须了解TensorFlow，它是Google开源的第二代深度学习系统。本书深入浅出地介绍了深度学习的算法和模型，书中提供的大量实例代码是一大特色。对于AI的初学者来说，本书值得一读。

> ——磁云科技创始人/京东终身荣誉技术顾问 李大学

TensorFlow是Google开源的一套深度学习框架。本书用通俗易懂的语言介绍了深度学习的算法和模型，并穿插了大量的实例代码。对于TensorFlow入门读者和深度学习的研究人员来说，本书是一本非常好的参考书。

> ——创客总部/创客共赢基金合伙人 李建军
