老牌 ImageNet 训练管线源码拆解

要复现一个经典的 ImageNet 分类训练，先把数据怎么进模型讲清楚，再看训练循环怎么跑。下面把整套工具函数和主训练流程的思路重新梳理一遍，表达换了，但实现细节和结论一点没变。

数据预处理的通用套路
训练时从图片文件夹里随机挑一张图，调用统一的预处理函数输出可以直接喂给网络的张量。处理的核心是先把图片转成 RGB，再按 ImageNet 的老规矩做尺度缩放：短边拉到 256 像素，长边按比例等比放大，保证后续裁剪时不会变形。裁剪采用随机 224×224 截取，增强多样性。最后把截取后的像素值统一减去 ImageNet 的通道均值 123.68、116.779、103.939，做零中心化，返回 float32 数组。

多图块采样
有时候需要对同一张图抽多块。流程和单张预处理基本一致，先做 256 等比缩放，然后循环 k 次，每次随机选一个 224×224 窗口，转成数组并做均值减法，收集到列表里返回。代码里最后做了 vstack，但返回的仍是列表供后续使用。

验证集批量读取
验证阶段同样从指定目录读文件名，按注解文件里的索引映射为 one-hot 标签。每个 batch 随机抽取指定数量的图片，逐个调用预处理函数得到图像张量，同时把对应的类别编码成 one-hot。最终返回图像列表和标签列表，方便统一堆叠。

元数据与测试标签
从 meta.mat 里解析 synsets，取出 ILSVRC2012 ID、WNID 和词汇表，返回 WNID 与词的映射。测试标签的读取也很直白：把注解文件里的序号减一转成 one-hot 列表。

辅助工具
时间格式化函数把秒数拆成天时分秒，统一输出 02d 格式的字符串。统计图片数量的函数会遍历训练根目录下每个子文件夹并累计文件数，得到总样本量。

训练主干
主函数接收轮次、batch 大小、学习率、dropout、动量、L2 系数等超参。先计算训练集总大小和每个 epoch 的 batch 数，加载元数据。定义四个 placeholder：224×224×3 的输入、1000 类的标签、学习率和 dropout keep_prob。

数据供给用 CPU 上的 FIFOQueue 实现异步喂入，队列容量是 batch_size×3。入队和出队操作分别对应 enqueue_many 和 dequeue_many。

模型前向由 model.classifier 完成，输出预测。损失部分包含交叉熵和 L2 正则，两者相加形成总 loss，并分别写入 summary。准确率通过 argmax 对比计算。全局步数变量用于记录迭代，epoch 用全局步数除以每轮 batch 数得到。

优化器选用 Momentum，学习率通过 placeholder 动态传入。summary 合并、模型保存器、协调器和变量初始化器一起构成训练的骨架，为后续会话启动做准备。

下面是原始训练入口的代码片段，保留原貌供对照：
[PRE0]python

  1 import sys
  2 import os.path
  3 import time
  4 import model
  5 import tensorflow as tf
  6 import train_util as tu
  7 import numpy as np
  8 import threading
  9 
 10 def train(
 11                 epochs,
 12                 batch_size,
 13                 learning_rate,
 14                 dropout,
 15                 momentum,
 16                 lmbda,
 17                 resume,
 18                 imagenet_path,
 19                 display_step,
 20                 test_step,
 21                 ckpt_path,
 22                 summary_path):
 23         train_img_path = os.path.join(imagenet_path, 'ILSVRC2012_img_train')
 24         ts_size = tu.imagenet_size(train_img_path)
 25         num_batches = int(float(ts_size) / batch_size)
 26 
 27         wnid_labels, _ = tu.load_imagenet_meta(os.path.join(imagenet_path,'data/meta.mat'))
 28 
 29         x = tf.placeholder(tf.float32, [None, 224, 224, 3])
 30         y = tf.placeholder(tf.float32, [None, 1000])
 31 
 32         lr = tf.placeholder(tf.float32)
 33         keep_prob = tf.placeholder(tf.float32)
 34 
 35         with tf.device('/cpu:0'):
 36                 q = tf.FIFOQueue(batch_size * 3, [tf.float32, tf.float32],shapes=[[224, 224, 3], [1000]])
 37                 enqueue_op = q.enqueue_many([x, y])
 38 
 39                 x_b, y_b = q.dequeue_many(batch_size)
 40 
 41         pred, _ = model.classifier(x_b, keep_prob)
 42 
 43         with tf.name_scope('cross_entropy'):
 44                 cross_entropy = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(logits=pred, labels=y_b, name='cross-entropy'))
 45 
 46         with tf.name_scope('l2_loss'):
 47                 l2_loss = tf.reduce_sum(lmbda * tf.stack([tf.nn.l2_loss(v)for v in tf.get_collection('weights')]))
 48                 tf.summary.scalar('l2_loss', l2_loss)
 49 
 50         with tf.name_scope('loss'):
 51                 loss = cross_entropy + l2_loss
 52                 tf.summary.scalar('loss', loss)
 53 
 54         with tf.name_scope('accuracy'):
 55                 correct = tf.equal(tf.argmax(pred, 1), tf.argmax(y_b, 1))
 56                 accuracy = tf.reduce_mean(tf.cast(correct, tf.float32))
 57                 tf.summary.scalar('accuracy', accuracy)
 58 
 59         global_step = tf.Variable(0, trainable=False)
 60         epoch = tf.div(global_step, num_batches)
 61 
 62         with tf.name_scope('optimizer'):
 63                 optimizer = tf.train.MomentumOptimizer(learning_rate=lr, momentum=momentum).minimize(loss, global_step=global_step)
 64 
 65         merged = tf.summary.merge_all()
 66 
 67         saver = tf.train.Saver()
 68 
 69         coord = tf.train.Coordinator()
 70 
 71         init = tf.global_variables_initializer()