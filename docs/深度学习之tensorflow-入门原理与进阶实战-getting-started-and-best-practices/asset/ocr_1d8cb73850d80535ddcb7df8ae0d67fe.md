沿着 y 轴的方向平行画一条线经过该点，可以看到它在第一条线（蓝线）的下方，并且离蓝线的距离是最远的，所以它就属于第一条线对应的红色分类。说明对于这类的数据集，仍然可以使用线性可分的方法将其分开。本例也展示了线性可分在多分类问题上的应用与原理。

# 5. 模型可视化

前面介绍了线性与模型的关系，现在把整个坐标系放到模型里，会得到一个更直观的模型分类可视化。

为了方便演示，还是在图像上生成 200 个点并显示出来。然后按照坐标系的排列，把 x1,x2 放到模型里，见如下代码。

```
代码7-2 线性多分类（续）

69 train_X, train_Y = generate(200,num_classes, [[3.0],[3.0,0]], False)
70 aa = [np.argmax(l) for l in train_Y]
71 colors =['r' if l == 0 else 'b' if l==1 else 'y' for l in aa[:]]
72 plt.scatter(train_X[:,0], train_X[:,1], c=colors)
73
74 nb_of_xs = 200
75 xs1 = np.linspace(-1, 8, num=nb_of_xs)
76 xs2 = np.linspace(-1, 8, num=nb_of_xs)
77 xx, yy = np.meshgrid(xs1, xs2) # 创建网格
78 # 初始化和填充 classification plane
79 classification_plane = np.zeros((nb_of_xs, nb_of_xs))
80 for i in range(nb_of_xs):
81     for j in range(nb_of_xs):
82
83     classification_plane[i,j] = sess.run(al, feed_dict={input_
features: [[ xx[i,j], yy[i,j] ]] })
84
85 # 创建 color map 用于显示
86 cmap = ListedColormap([
87     colorConverter.to_rgba('r', alpha=0.30),
88     colorConverter.to_rgba('b', alpha=0.30),
89     colorConverter.to_rgba('y', alpha=0.30)])
90 # 图示各个样本边界
91 plt.contourf(xx, yy, classification_plane, cmap=cmap)
92 plt.show()
```

上面代码的运行结果如图 7-6 所示。

图 7-6 中将三分类模型用了不同的颜色区域进行区分，这样就是符合人眼规律的一个比较直观的可视化图样了。