强度 $E_s$ 进行二值化，将其转化为非零即一的边缘图 $E_b$，即如果 $E_b(i,j|I)=1$，那么像素$(i,j)$是边缘点，否则不是边缘点。最简单的二值化方法是单一阈值法，即取一个单一阈值 $T$，对其二值化：

$$E_b(i,j|I) = \begin{cases} 1, & E_s(i,j|I) \ge T \\ 0, & E_s(i,j|I) < T \end{cases}$$

但是，这种单一阈值法很容易造成断裂的边缘。为了解决这个问题，Canny 边缘检测算法使用了双阈值，提出了基于滞后后的阈值化的方法。算法使用一高一低两个阈值，分别记为 $T_h$ 和 $T_l$ （$T_l < T_h$）。如果像素$(i,j)$的边缘强度 $E_s(i,j|I)$ 大于等于高阈值，则该像素被标注为强边缘点，$E_b(i,j|I)=1$；如果边缘强度小于低阈值，则该像素被标注为非边缘点，$E_b(i,j|I)=0$；如果边缘强度在两个阈值之间，则该像素被标注为弱边缘点，需要进一步确定 $E_b(i,j|I)$ 的取值：

$$E_b(i,j|I) = \begin{cases} 1, & E_s(i,j|I) \ge T_h \\ 0, & E_s(i,j|I) < T_l \\ \text{待定}, & T_l \le E_s(i,j|I) < T_h \end{cases}$$

这就是所谓的滞后的阈值化。下面，我们编程实现通过双阈值确定强弱边缘。

```
```python
# 双阈值确定强弱边缘
def double_thresholding(img, high, low):
    # 初始化强弱边缘为布尔矩阵
    strong_edges = np.zeros(img.shape, dtype=np.bool_)
    weak_edges = np.zeros(img.shape, dtype=np.bool_)

    # 获得输入图像大小
    a, b = img.shape
    for i in range(a):
        for j in range(b):
            # 大于等于 Th 阈值则为强边缘点
            if img[i,j] >= high:
                strong_edges[i,j] = 1

            # 小于 Th 阈值、大于等于 Tl 阈值则为弱边缘点
            elif img[i,j] < high and img[i,j] >= low:
                weak_edges[i, j] = 1

    return strong_edges, weak_edges

low_threshold = 3
high_threshold = 6

strong_edges, weak_edges = double_thresholding(nms,
    high_threshold, low_threshold)

# 返回强弱边缘叠加的边缘图
edges = strong_edges * 1.0 + weak_edges * 0.5

plot_image(strong_edges, 'Strong Edges')
plot_image(edges, 'Strong+Weak Edges')
```
```