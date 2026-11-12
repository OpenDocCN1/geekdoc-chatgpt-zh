25010: 0.110372, 0.089306 avg loss, 0.000010 rate, 0.119459 seconds, 400160 images, 0.002427 hours left
Saving weights to /backup/yolov3-tiny-traffic_final.weights
If you want to train from the beginning, then use flag in the end of training command: -clear 3)

训练完成后，可以在backup目录下看到训练完成的模型文件yolov3-tiny-traffic_final.weights，如图3.31所示。

![](bbox=[0.125, 0.245, 0.88, 0.585])

图3.31 训练完成的模型文件

# 3. 2. 2. 5 模型验证

通过前面的步骤可完成交通标志识别模型的训练，得到yolov3-tiny-traffic_final.weights。如果没有条件训练出最终的模型，也可以解压本项目提供的最终模型文件，将其复制到backup目录下。

+   (1) 在数据集中选择一幅测试样图，这里选择的是 dataset/JPEGImages/10050.jpg。

+   (2) 在SSH终端输入以下命令对测试样图进行测试，执行命令与结果如下，成功地将目标（交通标志）识别为“right”。