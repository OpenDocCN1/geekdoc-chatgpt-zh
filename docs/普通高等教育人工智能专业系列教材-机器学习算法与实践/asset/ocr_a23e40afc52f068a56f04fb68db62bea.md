```
# 记录每组类别中心点 x、y 坐标：
newa_x = []
newa_y = []

# 对每组类别进行类别中心坐标 x、y 循环求取：
for i in range(k):
    axtmp = np.mean(clusterx[i])        # 计算第 i 组类别的 x 坐标平均值
    aytmp = np.mean(clustery[i])        # 计算第 i 组类别的 y 坐标平均值
    newa_x.append(axtmp)                # 将第 i 组类别的 x 坐标平均值添加到列表中
    newa_y.append(aytmp)                # 将第 i 组类别的 y 坐标平均值添加到列表中

return np.array(newa_x), np.array(newa_y) # 返回每个类别的中心点坐标作为数组
```

# （5）聚类主体部分

代码 9-5 实现聚类，并绘制聚类结果。首先，对于给定的数据，通过调用 clust_min_distance_index 函数将数据分成不同的类别，并将结果保存在 clusterx 和 clustery 中。然后，调用 mean_center 函数计算得到每个类别的新中心点。接下来，计算新中心点与原中心点之间的误差，并将误差值打印出来。如果误差小于设定的阈值（0.001），则输出循环次数并提前结束循环；否则，更新中心点，并继续进行下一次迭代。最后，使用散点图展示分组后的数据，不同类别的样本点使用不同的颜色和标记进行表示。

# 代码 9-5 实现聚类，并绘制聚类结果图像

```
for i in range(100):
    clusterx, clustery = clust_min_distance_index(ax, ay)  # 分组后数据
    axtmp, aytmp = mean_center(clusterx, clustery)        # 新类别中心

    # 计算误差：error
    dx = np.power(axtmp - ax, 2)                          # 计算 x 坐标的平方差
    dy = np.power(aytmp - ay, 2)                          # 计算 y 坐标的平方差
    error = np.sum(np.sqrt(dx + dy))                      # 计算误差，将平方差相加并开平方根

    print('当前误差:', error)
    # 停止条件
    if error < 0.001:
        print('循环次数:', i, '精度要求已达标，提前结束！')
        break   # 满足条件停止
    else:
        ax = axtmp  # 不满足条件停止时，存储新类别中心
        ay = aytmp

# 画图显示；不同类别使用不同形状和颜色
plt.scatter(clusterx[0], clustery[0], c="red", marker='o')
plt.scatter(clusterx[1], clustery[1], c="green", marker='*')
plt.scatter(clusterx[2], clustery[2], c="blue", marker='+')

plt.show()
```

代码循环 5 次停止，得到如下结果及图 9-4 和图 9-5（不同次数实验，结果不同）：