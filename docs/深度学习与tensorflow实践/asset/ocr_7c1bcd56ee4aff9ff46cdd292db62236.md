```
10
11 def predict(self, image_path):
12     # 以方式读取图片
13     img = Image.open(image_path).convert('L')
14     flatten_img = np.reshape(img, (28, 28, 1))
15     x = np.array([1 - flatten_img])
16     y = self.network.model.predict(x)
17     print(image_path)
18     print(y[0], '-> 预测数字为: ', np.argmax(y[0]))
19 
20 if __name__ == "__main__":
21     = Predict()
22     app.predict('./test_images/0_57.png')
23     app.predict('./test_images/1_32.png')
24     app.predict('./test_images/3_59.png')
```

# 运行结果

./test_images/0_57.png
[1. 0. 0. 0. 0. 0. 0. 0. 0. 0.] -> 预测数字为: 0
./test_images/1_32.png
[0. 1. 0. 0. 0. 0. 0. 0. 0. 0.] -> 预测数字为: 1
./test_images/3_59.png
[0. 0. 0. 0. 1. 0. 0. 0. 0. 0.] -> 预测数字为: 3

# 代码分析

首先我们要保证本范例的 predict.py 和前面范例的 mnist_cnn.py 处于同一个路径下，这样便于第 03 行导入前面设计的 CNN 类，这里主要是读取前面设计的模型结构。

第 09 行比较关键，从前面可视化范例可以观察到，当网络训练到第 4 轮时，其训练性能是可以接受的（损失较小，预测精度较高），故将第 4 轮训练的网络权值导入。需要说明的是，模型的性能并不是训练的轮数越高越好，而是应该“适可而止”。

有了模型结构，有了导入的网络参数，那么这个网络模型就具备预测作用了。

这里我们随机挑选了 3 个手写数字图片（28×28，格式为.png），如图 7-31 所示。

![](bbox=[0.44, 0.82, 0.82, 0.87])

图 7-31 测试集合中的三张图片

在导入这些图片时，需要特别注意的是，必须把它们转换为模型需要