```
# 定义测试样本和迭代次数
num_examples= 10000
num_iter= int(math.ceil(num_examples / batch_size))
true_count= 0
total_sample_count= num_iter * batch_size
step= 0
# 在测试集上进行预测
while step < num_iter:
    image_batch, label_batch= sess.run([images_test, labels_test])
    # 运行预测操作，计算正确预测的数量
    predictions = sess.run([top_k_op], feed_dict={image_holder: image_batch,
    label_holder: label_holder})
    true_count+= np.sum(predictions)
    step+= 1
    # 计算和打印预测精度
    precision= true_count / total_sample_count
    print('precision @ 1 = %.3f '%precision)
```

# 8.3 PyTorch

# 8.3.1 PyTorch 简介

PyTorch 诞生于 2017 年 1 月，是由 Facebook 人工智能研究院开发的深度学习框架[2]。PyTorch 的历史可以追溯到早在 2002 年由纽约大学发布的一款机器学习框架 Torch，Torch 采用了较为小众的 Lua 语言作为接口，其知名度并不高。PyTorch 在 Torch 的基础上使用 Python 语言对其进行了封装和重构，经过数个版本的迭代和完善后，至今已经成为最流行的深度学习框架之一。PyTorch 的官方网站：https://pytorch.org/。Github 主页：https://github.com/pytorch/pytorch。

PyTorch 保留了 Torch 中的一些基本概念，比如张量和模块，成为 PyTorch 的重要组成部分。在后续的版本更新中，PyTorch 逐步引入了对并行计算、异构计算和分布式计算的支持，通过集成 CuDNN 的 GPU 深度学习计算库，极大地丰富了张量运算操作和深度学习模块的种类。现阶段的 PyTorch 能够支持绝大多数种类的神经网络，在保持高速更新的同时兼顾了 API 的稳定性，拥有旺盛的生命力。

PyTorch 的设计思路全面继承了 Python 语言的编程风格，线性、直观且易于使用，相较于 TensorFlow 而言更加简洁直观。另一方面，PyTorch 继承了大多数热门的 Python 库，包括用于科学计算的 Numpy 和 Scipy 等，对数据处理的支持非常完善。不像 TensorFlow 为了追求高度工业化而牺牲了底层代码的可读性，PyTorch 的源代码比较友好，更容易理解和使用，而且