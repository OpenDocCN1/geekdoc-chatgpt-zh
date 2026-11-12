![](bbox=[0.19, 0.08, 0.78, 0.24])

# 训练数据集中的前10张图像

与前一小节中MNIST数据集相似，我们为每一类物体画出 10 张图像。

```
fig, axes = plt.subplots(10, 10, figsize=(20, 20)) # 新建一个包含100张子图的10行
10列的画布
for i in range(10): # 对于每一类物体
    indice = np.where(y_train == i)[0] # 找到标签为1的图像下标
    for j in range(10): # 输出前10张图像
        axes[i][j].imshow(x_train[indice[j]], cmap="gray_r")
        axes[i][j].set_xticks([])
        axes[i][j].set_yticks([])
plt.tight_layout()
plt.show()
```

![](bbox=[0.25, 0.555, 0.72, 0.885])

# 为每类物体分别画出 10 张图像