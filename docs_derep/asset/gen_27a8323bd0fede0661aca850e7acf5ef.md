# ImageNet CNN 训练与评估流程拆解

这是一套基于 TensorFlow 1.x 的经典 ImageNet 卷积网络训练与测试实现，核心思路是先用多线程队列持续喂数据，再在固定步数上做学习率衰减、日志记录与验证，最后用多块裁剪平均的策略做 Top-1 / Top-5 评测。

**训练侧的整体安排**

脚本会先创建会话，判断是否续训。如果传了 -resume，就直接把 ckpt 目录下的 cnn.ckpt 恢复进来；如果是 -scratch 则执行全局初始化。

数据供给采用生产者模式，自行定义 enqueue_batches 从 tu.read_batch 读入图片与标签并喂入队列，启动 3 条守护线程同时运行，保证流水线不空转。

训练日志用 tf.summary.FileWriter 写入 summary 目录，完整记录计算图。起跑时间被记录下来，训练循环从会话里取到的当前 epoch 开始跑到 90 个完整轮次。

每一步会同时取到合并的 summary、优化器更新和全局步数，并把 summary 写进文件。学习率在全局步数到达 170000 或 350000 时直接除以 10，做两次阶梯衰减。

监控频率分两层：步数对齐 DISPLAY_STEP 时用 keep_prob=1.0 计算一次 loss 与训练准确率并打印 Epoch、Step、Loss、Training accuracy；步数对齐 TEST_STEP 时会读取 ILSVRC2012 的验证集与标签，用相同的网络做验证准确率评估，同时打印耗时并把模型保存到 ckpt 目录的 cnn.ckpt。训练结束后再保存一次最终权重并请求协调器停止。

命令行默认超参为：DROPOUT 0.5、MOMENTUM 0.9、权重衰减 LAMBDA 5e-04、初始学习率 1e-03、EPOCHS 90、BATCH_SIZE 128。路径方面 ckpt 放在 ckpt，summary 放在 summary，数据根目录为 ILSVRC2012。显示间隔 DISPLAY_STEP 设为 10，验证间隔 TEST_STEP 设为 500，启动方式通过 -resume 或 -scratch 决定。

[PRE0]
**测试侧的多裁剪评测**

测试函数接收 top_k、k_patches、显示间隔、数据路径和检查点路径。先把验证集目录下的文件名排序，并读取对应 ground_truth 标签文件得到真实类别。网络输入是 [None, 224, 224, 3] 的浮点占位符，标签占位符是 [None, 1000] 的 one-hot。

评测时调用 model.classifier 得到预测分布，对同一个图片做 k_patches 次裁剪推理后把结果求和再除以 k_patches，得到平均预测。Top-1 正确性通过平均预测的 argmax 与标签 argmax 是否相等来判断并求均值；Top-K 正确性则使用 tf.nn.in_top_k 判定真实标签是否落在前 k 名并求均值。

会话恢复后逐张遍历验证集，每张图读取 k_patches 个裁剪块后喂入网络累加 Top-1 与 Top-K 的累计准确率，并在达到显示间隔时打印已处理的样本数及当前平均 Top-1 与 Top-K。全部样本跑完后输出最终 Top-1 与 Top-K 准确率，以及对应的错误率 1-准确率。

默认测试参数为 TOP_K 5、K_PATCHES 5，即常见的五裁剪五榜单评估方式，给出最终的 Top-1 准确率、Top-5 准确率及各自错误率作为模型表现的定量结论。