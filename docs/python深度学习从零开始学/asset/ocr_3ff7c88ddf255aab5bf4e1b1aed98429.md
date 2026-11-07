# 6.4.5 模型评估

Train on 930 samples, validate on 104 samples
Epoch 1/30
 - 0s - loss: 0.6812 - acc: 0.5882 - val_loss: 0.6160 - val_acc: 0.7885
Epoch 2/30
 - 0s - loss: 0.6159 - acc: 0.6602 - val_loss: 0.4893 - val_acc: 0.8077
Epoch 3/30
 - 0s - loss: 0.5342 - acc: 0.7667 - val_loss: 0.4607 - val_acc: 0.7788
Epoch 4/30
 - 0s - loss: 0.5026 - acc: 0.7484 - val_loss: 0.4611 - val_acc: 0.7885
Epoch 5/30
 - 0s - loss: 0.4862 - acc: 0.7656 - val_loss: 0.4534 - val_acc: 0.7885
Epoch 6/30
 - 0s - loss: 0.4799 - acc: 0.7688 - val_loss: 0.4382 - val_acc: 0.7981
Epoch 28/30
 - 0s - loss: 0.4525 - acc: 0.7839 - val_loss: 0.4195 - val_acc: 0.7981
Epoch 29/30
 - 0s - loss: 0.4454 - acc: 0.7957 - val_loss: 0.4216 - val_acc: 0.8173
Epoch 30/30
 - 0s - loss: 0.4555 - acc: 0.7935 - val_loss: 0.4254 - val_acc: 0.8173

图 6-13

之前的训练步骤会将每一个训练周期的准确率与误差记录在 train_history 变量中，定义函数 show_train_history 读取 train_history 以图表显示训练过程。画出准确率评估的执行结果，可以发现无论是训练还是验证，准确率都越来越高，如图 6-14 所示。

```
In [9]: import matplotlib.pyplot as plt
def show_train_history(train_history, train, validation):
    plt.plot(train_history.history[train])
    plt.plot(train_history.history[validation])
    plt.title('Train History')
    plt.ylabel('train')
    plt.xlabel('Epoch')
    plt.legend(['train','validation'], loc='upper left')
    plt.show()
show_train_history(train_history,'acc','val_acc')
```

![](bbox=[0.42, 0.51, 0.88, 0.68])

图 6-14

画出误差的执行结果，如图 6-15 所示，一共执行 30 个训练周期，可以发现，无论是训练还是验证，误差都越来越小。

训练完模型，现在要使用测试数据集来评估模型的准确率。使用 model.evaluate 评估模型的准确率，从执行结果可知准确率是 0.80，如图 6-16 所示。

```
In [10]: show_train_history(train_history,'loss','val_loss')
```

![](bbox=[0.42, 0.75, 0.88, 0.92])

图 6-15