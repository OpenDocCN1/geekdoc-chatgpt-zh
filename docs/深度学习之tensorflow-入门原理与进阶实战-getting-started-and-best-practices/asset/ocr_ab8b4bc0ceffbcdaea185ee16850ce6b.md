```
print ("Salt and Pepper Noise")
noisyvec = testvec
rate = 0.15
noiseidx = np.random.randint(test_X.shape[1]
    , size=int(test_X.shape[1]*rate))
noisyvec[0, noiseidx] = 1-noisyvec[0, noiseidx]
outvec = sess.run(reconstruction, feed_dict={x: noisyvec,
    dropout_keep_prob: 1})
outimg = np.reshape(outvec, (28, 28))
# 可视化
plt.matshow(np.reshape(orgvec, (28, 28)), cmap=plt.get_cmap('gray'))
plt.title("Original Image")
plt.colorbar()
plt.matshow(np.reshape(noisyvec, (28, 28)), cmap=plt.get_cmap
    ('gray'))
plt.title("Input Image")
plt.colorbar()
plt.matshow(outimg, cmap=plt.get_cmap('gray'))
plt.title("Reconstructed Image")
plt.colorbar()
plt.show()
```

执行上面的代码，会生成如下信息，生成的图片如图 10-9 所示。

label is 9
Salt and Pepper Noise

![](bbox=[0.09, 0.545, 0.88, 0.725])

图 10-9 去噪自编码结果 2

可以看出，使用 Salt and Pepper 噪声对原始图片进行干扰后，仍然可以得到很好的效果。

# 10.5.2 练习题

试着将 dropout 的值改成 0.5，看一下模型训练出来的样子，然后再将 Salt and Pepper 噪声变换后的图片放到模型里，观察输出的图片。想想这是为什么？为什么要在去噪声自编码网络里加入 dropout。