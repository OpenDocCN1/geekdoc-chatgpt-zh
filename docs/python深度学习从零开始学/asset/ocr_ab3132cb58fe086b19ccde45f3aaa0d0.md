# Python深度学习从零开始学

```
Train Epoch: 13 Loss= 59.840492249  Accuracy= 0.9632
Train Epoch: 14 Loss= 52.204738617  Accuracy= 0.9672
Train Epoch: 15 Loss= 55.506675720  Accuracy= 0.9654
Train Finished takes: 396.94424772262573
Accuracy: 0.9624
```

从结果中可以看出，随着训练的进行，误差越来越小，准确率越来越高，最终模型准确率为 0.9624。画出准确率的执行结果，如图 5-13 所示。

![](bbox=[0.28, 0.24, 0.71, 0.45])

图 5-13

另外，查看预测结果的前 10 项数据，结果为 “[7 2 1 0 4 1 4 9 6 9]”，查看预测结果的第 248 项数据，结果为 “2”，查看测试数据的第 248 项数据，结果却是 “4”。

程序输出找出预测错误的结果，如下所示：

```
i=8      label= 5 predict= 6
i=62     label= 9 predict= 5
i=64     label= 7 predict= 3
i=125    label= 9 predict= 4
i=241    label= 9 predict= 8
i=247    label= 4 predict= 2
i=259    label= 6 predict= 0
i=320    label= 9 predict= 7
i=321    label= 2 predict= 8
i=340    label= 5 predict= 3
i=341    label= 6 predict= 8
i=359    label= 9 predict= 4
i=381    label= 3 predict= 7
i=417    label= 9 predict= 7
i=432    label= 4 predict= 5
i=444    label= 2 predict= 8
i=445    label= 6 predict= 0
i=447    label= 4 predict= 9
i=448    label= 9 predict= 8
i=460    label= 5 predict= 9
```