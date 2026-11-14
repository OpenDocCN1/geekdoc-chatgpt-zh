
## 3. 建立ESPCN网络结构

网络结构与上例一样，不同的是输入的图片做了归一化处理，统一除以 255，使其变为 0～1 之间的数。最后一个卷积成输出的为 12 通道，代表 2×2 的缩放比例，一共 3 个通道，所以再乘以 3。另外，各层均使用了 Tanh 函数，最后一层没有使用激活函数。

```代码12-7 tfrecoderSRESPCN (续)
31 net = slim.conv2d(x_smalls2, 64, 5,activation_fn = tf.nn.tanh)
32 net =slim.conv2d(net, 32, 3,activation_fn = tf.nn.tanh)
33 net = slim.conv2d(net, 12, 3,activation_fn = None)#2*2*3
34 y_predt = tf.depth_to_space(net,2)
35
36 y_pred = y_predt*255.0
37 y_pred = tf.maximum(y_pred,0)
38 y_pred = tf.minimum(y_pred,255)
39
40 dbatch=tf.concat([tf.cast(images,tf.float32),y_pred],0)
```

y_pred 是由 y_predt 转化而来的，通过 tf.maximum 与 tf.minimum 函数将内部的值都变为 0～255 之间的数字。y_predt 会参与损失值的计算。

dbatch 是将生成的 y_pred 与 images 按照批次的维度合并起来，该张量是为了后面进行图片质量评估使用的。

> 注意：上面例子中 y_pred 进行了最大值和最小值的规整处理，这是个很常用的技巧。
> 如果不处理，那么生成的图片会在显示时看到有亮点，使图片显得不清晰。读者可以试着将这段代码去掉，看看效果。

## 4. 构建loss及优化器

对于全彩色训练的学习率设定还是需要非常小心的，在这里设置为 0.000001，让其缓慢地变化。由于输入的样本归一化了，所以计算 loss 时的 images 也需要归一化。代码如下：

```代码12-7 tfrecoderSRESPCN (续)
41 cost = tf.reduce_mean(tf.pow( tf.cast(images,tf.float32)/255.0 - y_predt, 2))
42 optimizer = tf.train.AdamOptimizer(0.000001 ).minimize(cost)
```

## 5. 建立session，运行

启动 session，运行 15 0000 次。代码如下：

```代码12-7 tfrecoderSRESPCN (续)
43 training_epochs =150000
44 display_step =200
45
46 sess = tf.InteractiveSession()
47 sess.run(tf.global_variables_initializer())
48
49 #启动队列
50 tf.train.start_queue_runners(sess=sess)
51
52 # 启动循环开始训练
53 for epoch in range(training_epochs):
54
55 _, c = sess.run([optimizer, cost])
56 # 显示训练中的详细信息
57 if epoch % display_step == 0:
58 d_batch=dbatch.eval()
59 mse,psnr=batch_mse_psnr(d_batch)
60 ypsnr=batch_y_psnr(d_batch)
61 ssim=batch_ssim(d_batch)
62 print("Epoch:", '%04d' % (epoch+1),
63 "cost=", "{:.9f}".format(c),"psnr",psnr,"ypsnr",ypsnr,"ssim",ssim)
64
65 print("完成!")
```

在显示评估结果时，使用 batch_mse_psnr、batch_y_psnr 和 batch_ssim 这 3 个函数分别对节点 dbatch 的值进行运算，得到图片的质量评估值。

## 6. 构建图片质量评估函数

SR 图片质量有其自己的一套评估质量算法：常用的两个指标是 PSNR (Peak Signal-to-Noise Ratio) 和 SSIM (Structure Similarity Index)。这两个值越高，代表重建结果的像素值和标准越接近。对于 PSNR 的计算有两个方法：

- 基于 R、G、B，分别计算三通道中的 MSE 值再求平均值，然后再将结果代入求 PSNR。
- 基于 YUV，求图像 YUV 空间中的 Y 分量，计算 Y 分量的 PSNR 值。

对于 YUV 的介绍如下：
YUV (亦称 YCrCb) 是另一种颜色编码方法，常被欧洲电视系统所采用。Y 代表亮度信号，U (R-Y) 与 V (B-Y) 分别代表两个色差信号。在没有 U 和 V 时，就会表现为只有亮度的黑白色，彩色电视采用 YUV 空间正是为了用亮度信号 Y 解决彩色电视机与黑白电视机的兼容问题，使黑白电视机也能接收彩色电视信号。

YUV 和 RGB 互相转换的公式如下（RGB 取值范围均为 0～255）：
Y = 0.299R + 0.587G + 0.114B
U = -0.147R-0.289G + 0.436B
V = 0.615R-0.515G-0.100B
R = Y + 1.14V
G = Y-0.39U – 0.58V
B = Y + 2.03U

将这 3 个指标用代码实现如下。

## 代码12-7 tfrecoderSRESPCN (续)

```代码12-7 tfrecoderSRESPCN (续)
66 def batch_mse_psnr(dbatch):
67 im1,im2=np.split(dbatch,2)
68     mse=((im1-im2)**2).mean(axis=(1,2))
69     psnr=np.mean(20*np.log10(255.0/np.sqrt(mse)))
70     return np.mean(mse),psnr
71 def batch_y_psnr(dbatch):
72     r,g,b=np.split(dbatch,3,axis=3)
73     y=np.squeeze(0.3*r+0.59*g+0.11*b)
74     im1,im2=np.split(y,2)
75     mse=((im1-im2)**2).mean(axis=(1,2))
76     psnr=np.mean(20*np.log10(255.0/np.sqrt(mse)))
77     return psnr
78 def batch_ssim(dbatch):
79     im1,im2=np.split(dbatch,2)
80     imgsize=im1.shape[1]*im1.shape[2]
81     avg1=im1.mean((1,2),keepdims=1)
82     avg2=im2.mean((1,2),keepdims=1)
83     std1=im1.std((1,2),ddof=1)
84     std2=im2.std((1,2),ddof=1)
85     cov=((im1-avg1)*(im2-avg2)).mean((1,2))*imgsize/(imgsize-1)
86     avg1=np.squeeze(avg1)
87     avg2=np.squeeze(avg2)
88     k1=0.01
89     k2=0.03
90     c1=(k1*255)**2
91     c2=(k2*255)**2
92     c3=c2/2
93     return np.mean((2*avg1*avg2+c1)*2*(cov+c3)/(avg1**2+avg2**2+c1)/(
    (std1**2+std2**2+c2)))
```

## 7. 图示结果

与前面例子类似，将原始图片与函数变化的图片及模型输出的图片一并显示。这里先定义一个函数用来统一显示。

注意：必须要将其转化为 UINT8 的形式，否则图片会显示不出来。

## 代码12-7 tfrecoderSRESPCN (续)

```代码12-7 tfrecoderSRESPCN (续)
94 def showresult(subplot,title,orgimg,thisimg,dopsnr = True):
95     p =plt.subplot(subplot)
96     p.axis('off')
97     p.imshow(np.asarray(thisimg[0],dtype='uint8'))
98     if dopsnr :
99         conimg = np.concatenate((orgimg,thisimg))
100        mse,psnr=batch_mse_psnr(conimg)
101        ypsnr=batch_y_psnr(conimg)
102        ssim=batch_ssim(conimg)
103        p.set_title(title+str(int(psnr))+" y:"+str(int(ypsnr))+" s:"+str(ssim))
104    else:
105        p.set_title(title)
```

接着取一批次的图片放入模型，调用 Showresult 函数将生成的结果及评分值全部显示出来。

## 代码12-7 tfrecoderSRESPCN (续)

```代码12-7 tfrecoderSRESPCN (续)
106 imagesv, label_batch, x_smallv, x_nearestv, x_bilinv, x_bicubicv, y_predv = sess.run([images, labels, x_smalls, x_nearests, x_bilins, x_bicubics, y_pred])
107 print("原",np.shape(imagesv),"缩放后的",np.shape(x_smallv),label_batch)
108
109 # 显示
110 plt.figure(figsize=(20,10))
111
112 showresult(161,"org",imagesv,imagesv,False)
113 showresult(162,"small/4",imagesv,x_smallv,False)
114 showresult(163,"near",imagesv,x_nearestv)
115 showresult(164,"biline",imagesv,x_bilinv)
116 showresult(165,"bicubicv",imagesv,x_bicubicv)
117 showresult(166,"pred",imagesv,y_predv)
118
119 plt.show()
```

运行代码，输出如下，输出图片如图 12-14 所示。

......

```
Epoch: 144801 cost= 0.003637410 psnr 23.8877 ypsnr 24.0189 ssim 0.96434
Epoch: 145001 cost= 0.008538806 psnr 25.0453 ypsnr 25.3529 ssim 0.91564
Epoch: 145201 cost= 0.005899625 psnr 28.1946 ypsnr 29.1575 ssim 0.975755
Epoch: 145401 cost= 0.002309756 psnr 25.6251 ypsnr 25.5808 ssim 0.95208
Epoch: 145601 cost= 0.004211991 psnr 25.2114 ypsnr 25.2179 ssim 0.947036
Epoch: 145801 cost= 0.002519545 psnr 27.9464 ypsnr 28.9226 ssim 0.973354
Epoch: 146001 cost= 0.005268521 psnr 20.8838 ypsnr 20.7228 ssim 0.9175
Epoch: 146201 cost= 0.002536027 psnr 23.988 ypsnr 24.3302 ssim 0.934929
Epoch: 146401 cost= 0.003322446 psnr 28.2296 ypsnr 29.4929 ssim 0.908311
Epoch: 146601 cost= 0.007955125 psnr 25.5261 ypsnr 26.271 ssim 0.948738
Epoch: 146801 cost= 0.002779651 psnr 29.1436 ypsnr 30.8613 ssim 0.983795
Epoch: 147001 cost= 0.005602385 psnr 24.6309 ypsnr 25.1222 ssim 0.920429
Epoch: 147201 cost= 0.004883423 psnr 25.3241 ypsnr 25.7193 ssim 0.964581
Epoch: 147401 cost= 0.005192784 psnr 26.5626 ypsnr 26.9226 ssim 0.952263
Epoch: 147601 cost= 0.006907145 psnr 27.7884 ypsnr 28.4125 ssim 0.96983
Epoch: 147801 cost= 0.008132000 psnr 26.7713 ypsnr 27.9356 ssim 0.976656
Epoch: 148001 cost= 0.008132160 psnr 24.6795 ypsnr 26.011 ssim 0.96252
Epoch: 148201 cost= 0.003620633 psnr 25.5258 ypsnr 24.9886 ssim 0.943705
Epoch: 148401 cost= 0.008644918 psnr 21.6561 ypsnr 21.704 ssim 0.90348
Epoch: 148601 cost= 0.003554154 psnr 25.849 ypsnr 25.9136 ssim 0.97194
Epoch: 148801 cost= 0.003299494 psnr 23.6707 ypsnr 24.2183 ssim 0.959333
Epoch: 149001 cost= 0.003197462 psnr 23.7814 ypsnr 24.1327 ssim 0.913214
Epoch: 149201 cost= 0.001375712 psnr 26.5407 ypsnr 26.7266 ssim 0.957788
Epoch: 149401 cost= 0.003641539 psnr 25.5488 ypsnr 26.2268 ssim 0.925159
Epoch: 149601 cost= 0.003025041 psnr 25.2158 ypsnr 25.65 ssim 0.969086
Epoch: 149801 cost= 0.001514586 psnr 27.9188 ypsnr 28.6265 ssim 0.976076
```

完成!

原 (4, 200, 200, 3) 缩放后的 (4, 100, 100, 3) [3 0 0 1]

![](img/d841d305683d65f55b29f4e3f25e532c_491_0.png)

图 12-14 ESPCN 实例 flowers 结果

结果可见，使用 ESPCN 可以实现很好的 SR 效果。

本例仅将图片分辨率放大了 2 倍，而且与 BILINEAR 的比较优势不大，但没关系，下面就来演示一个更明显的例子，通过进一步优化网络将图片分辨率放大 4 倍。

### 12.8.4 实例 95: 使用残差网络的 ESPCN

在实例 94 中 ESPCN 与 BILINEAR 的结果比较优势没有那么明显，这是因为普通算法在仅仅放大两倍的图片处理上是很优秀的，另一个原因也是由于例子中的网络结构过于简单（仅三层）。下面通过对实例 94 的网络结构优化，实现在分辨率放大 4 倍任务上的图片重建。

#### 实例描述

将 flowers 数据集中的图片转成低分辨率，再通过使用带残差网络的 ESPCN 网络复原成高分辨率图片，并与其他复原函数的生成结果进行比较。

具体实现步骤如下。

- 1. 修改输入图片分辨率

在代码 “12-7 tfrecoderSRESPCN.py” 的基础上进行修改，将原来的输入尺寸由长宽各缩小一半变为长宽各缩小为原来的 1/4。

## 代码12-8 resESPCN

```代码12-8 resESPCN
01 ......
02 images, labels = tf.train.batch([distorted_image, label], batch_size=batch_size)
03 print(images.shape)

04
05 x_smalls = tf.image.resize_images(images, (np.int32(height/4), np.int32(width/4))) # 尺寸变为原来的 1/16
06 x_smalls2 = x_smalls/255.0
07 ......
```

## 2. 添加残差网络

添加两个函数，一个是 leaky_relu 为 leaky relu 激活函数，另一个是用于生成网络残差块的函数 residual_block，实现一个中间有两层卷积的残差块。接着在整个网络构造中，通过一个卷积层与一个残差层完成图像特征的转换。残差层是由 16 个残差块与一个卷积层组成的网络。特征转换之后再通过 5 层神经网络完成最终的特征修复处理过程，如图 12-15 所示。

![](img/d841d305683d65f55b29f4e3f25e532c_492_0.png)

图 12-15 ResESPCN 例子子结构

图 12-15 中，最下面的 5 层为修复特征数据，第 1 层是一个卷积层，第 2 层会按照 2×2 大小的像素块将第一层的结果展开，第 3 层与第 1 层一样，第 4 层与第 2 层一样，第 5 层也是个卷积层。连续 2 次变换进行放大 4 倍的处理，最终通过输出 3 通道的卷积生成最终修复图片。
