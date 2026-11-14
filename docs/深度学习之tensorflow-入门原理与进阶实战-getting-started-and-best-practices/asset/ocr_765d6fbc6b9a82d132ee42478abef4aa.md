```
Epoch: 0016 cost= 0.067146875
Epoch: 0017 cost= 0.065363437
Epoch: 0018 cost= 0.066899218
Epoch: 0019 cost= 0.065677144
Epoch: 0020 cost= 0.064701408
完成!
```

可以看出，通过自编码网络将 784 维的数据压缩成了二维，用二维数据来代替 784 维，这就是自编码网络的神奇之处！

# 4. 对比输入和输出

同样我们再添加一些代码将效果显示出来。随意取出 10 张图片，并将图片输入模型中，得到输出图片。同时比对一下输入与输出的图片。

# 代码10-2 自编码进阶（续）

```
# 可视化结果
show_num = 10
encode_decode = sess.run(
    y_pred, feed_dict={x: mnist.test.images[:show_num]})
# 将自编码输出结果和原始样本显示出来
f, a = plt.subplots(2, 10, figsize=(10, 2))
for i in range(show_num):
    a[0][i].imshow(np.reshape(mnist.test.images[i], (28, 28)))
    a[1][i].imshow(np.reshape(encode_decode[i], (28, 28)))
plt.show()
```

执行上面的代码，生成如图 10-5 所示图片。

![](bbox=[0.102, 0.567, 0.876, 0.697])

# 图 10-5 自编码进阶实例结果 1

# 5. 显示数据的二维特征

接着就是比较好玩的事情了。我们要把数据压缩后的二维特征显示出来。

# 代码10-2 自编码进阶（续）

```
aa = [np.argmax(l) for l in mnist.test.labels]#将 onehot 转成一般编码
encoder_result = sess.run(encoder_op, feed_dict={x: mnist.test.
    images})
plt.scatter(encoder_result[:, 0], encoder_result[:, 1], c=aa)
plt.colorbar()
plt.show()
```