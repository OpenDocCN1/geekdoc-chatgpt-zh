```
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

# 6. 构建图片质量评估函数

SR 图片质量有其自己的一套评估质量算法：常用的两个指标是 PSNR (Peak Signal-to-Noise Ratio) 和 SSIM (Structure Similarity Index)。这两个值越高，代表重建结果的像素值和标准越接近。对于 PSNR 的计算有两个方法：

+   • 基于 R、G、B，分别计算三通道中的 MSE 值再求平均值，然后再将结果代入求 PSNR。
+   • 基于 YUV，求图像 YUV 空间中的 Y 分量，计算 Y 分量的 PSNR 值。

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

# 代码12-7 tfrecoderSRESPCN (续)

```
66 def batch_mse_psnr(dbatch):
67 im1,im2=np.split(dbatch,2)
```