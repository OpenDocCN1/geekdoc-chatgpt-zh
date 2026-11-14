plot 函数定义如下：

```
def plot(loss_list, predictions_series, batchX, batchY):
    plt.subplot(2, 3, 1)
    plt.cla()
    plt.plot(loss_list)

    for batch_series_idx in range(batch_size):
        one_hot_output_series = np.array(predictions_series)[:, batch_series_idx, :]
        single_output_series = np.array([(1 if out[0] < 0.5 else 0) for out in one_hot_output_series])

        plt.subplot(2, 3, batch_series_idx + 2)
        plt.cla()
        plt.axis([0, truncated_backprop_length, 0, 2])
        left_offset = range(truncated_backprop_length)
        left_offset2 = range(echo_step,truncated_backprop_length+echo_step)

    label1 = "past values"
    label2 = "True echo values"
    label3 = "Predictions"
    plt.plot(left_offset2, batchX[batch_series_idx, :]*0.2+1.5, "o--b", label=label1)
    plt.plot(left_offset, batchY[batch_series_idx, :]*0.2+0.8, "x--b", label=label2)
    plt.plot(left_offset, single_output_series*0.2+0.1 , "o--y", label=label3)

    plt.legend(loc='best')
    plt.draw()
    plt.pause(0.0001)
```

函数中将输入的 x 序列、回声 y 序列和预测的序列同时打印到图像中。按照批次的个数生成图像。为了让 3 个序列看起来更明显，将其缩放 0.2，并且调节每个图像的高度。同时将第一个原始序列的 x 在显示中滞后 echo_step 个序列，将 3 个图像放在同一序列顺序中比较。

运行代码，生成如下结果，如图 9-9 所示。

```
New data, epoch 0
Step 0 Loss 0.760327
Step 100 Loss 0.462219
Step 200 Loss 0.364076
......

New data, epoch 4
Step 0 Loss 0.324354
Step 100 Loss 0.103451
Step 200 Loss 0.0894693
Step 300 Loss 0.0940791
Step 400 Loss 0.09462
Step 500 Loss 0.10184
Step 600 Loss 0.0910746
```