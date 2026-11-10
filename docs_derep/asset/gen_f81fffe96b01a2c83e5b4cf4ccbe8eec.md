CycleGAN 训练流程拆解：从优化器到可视化快照

生成器与判别器的优化器先行配置。生成侧使用 Adam，学习率跟随 lrG，动量系数 beta_1 固定为 0.5，更新目标合并 GA 与 GB 的可训练参数，驱动信号是 lossG。判别侧同理，Adam 的学习率是 lrD，beta_1 仍为 0.5，更新范围是 DA 与 DB 的可训练参数，损失为 lossD。

基于这两个 updater，封装出可调用的训练接口。trainG 接收 realA 与 realB 作为输入，输出四个监控量 lossGA、lossGB、lossCycA、lossCycB，并执行生成器的更新。trainD 同样接收 realA、realB，输出 lossDA 与 lossDB，完成判别器的更新。

损失计算集中在 get_loss 方法。判别损失由真实样本向 1 的拟合与伪造样本向 0 的拟合叠加而成，生成损失则让伪造样本尽可能被判为 1。循环一致性采用像素绝对差的均值衡量 real 与 rec 的偏离程度。

模型提供了保存与单步训练的工具。save 会分别导出 GA、GB、DA、DB 四个网络，文件名以 -GA.h5、-GB.h5、-DA.h5、-DB.h5 命名。train 方法先调用判别训练得到 errDA、errDB，再调用生成训练得到 errGA、errGB、errCycA、errCycB。
[PRE0]
这六项误差作为本次迭代的返回值，供外部统计与打印。

[PRE1]
数据与超参数部分先行设定。输入图像统一裁剪到 128×128，选用 vangogh2photo 这组数据集，根目录拼接为 ./data/{DATASET}/。训练集 A 的图片路径为 trainA/*.jpg，训练集 B 为 trainB/*.jpg，分别用 DataSet 包装。

train_batch 负责取样，每次从 train_A 与 train_B 各取一批，返回两个 epoch 指针的较大值以及对应的图像批次 a、b。gen 函数用于效果预览，把一批图像送入指定生成器后取第一通道作为生成图，第二通道作为重构图返回。snapshot 负责拼图：先用 gen 得到 cycleA 对 A 的生成与重构、cycleB 对 B 的生成与重构，再按原图、生成图、重构图三行拼接，最终转成可显示图像。拼图布局为左列放域 A 的内容，右列放域 B 的内容。

实例化阶段创建 CycleGAN 模型，image_size 设为 128。记录开始时间，设置总轮数 EPOCH_NUM 为 100，初始 epoch 为 0。信息打印间隔 DISPLAY_INTERVAL 为 5 次迭代，快照间隔 SNAPSHOT_INTERVAL 为 50 次迭代，模型保存间隔 SAVE_INTERVAL 为 200 次迭代，批大小 BATCH_SIZE 设为 1。迭代计数 iter_cnt 从 0 开始，误差累加器 err_sum 初始化为长度 6 的零向量。

主循环在 epoch 未达上限时持续运行。每次先通过 train_batch 获取数据，再调用 model.train 完成一次判别与生成更新，将返回的六项误差累加到 err_sum，iter_cnt 自增。当 iter_cnt 为 DISPLAY_INTERVAL 的倍数时，用最近几轮的平均误差打印判别损失 A、B、生成损失 A、B、循环损失 A、B 并清空累加器。
[PRE3]
误差累加器被重置为零向量。

达到 SNAPSHOT_INTERVAL 时，从两个训练集各取 4 张图，通过 snapshot 并用 display 展示 A->B->A 与 B->A->B 的循环效果。达到 SAVE_INTERVAL 时调用 model.save，将当前四个子网络按迭代次数命名写入 ./models/model-{iter_cnt}。

整个流程在迭代结束或达到预设轮数后收敛，模型与可视化快照同步保存。
[PRE4]