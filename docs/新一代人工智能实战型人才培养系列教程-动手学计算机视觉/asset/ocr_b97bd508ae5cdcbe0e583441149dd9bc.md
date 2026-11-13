```
# 计算高斯核 G
G = np.exp(-x ** 2 / (2 * sigma ** 2))
G = G / np.sum(G)
# 对图像进行二维卷积
return cv2.sepFilter2D(img, -1, G, G)
```

```
def gaussderiv(img, sigma):
"""
高斯求导函数

参数：
- param img: 输入图像
- param sigma: 高斯核的标准差

返回：
- Dx: x 轴方向的导数图像
- Dy: y 轴方向的导数图像
"""
# 创建 x 数组，范围为[-3sigma,3sigma]
x = np.array(list(range(math.floor(-3.0 * sigma + 0.5),
                     math.floor(3.0 * sigma + 0.5) + 1)))

# 计算高斯核 G
G = np.exp(-x ** 2 / (2 * sigma ** 2))
G = G / np.sum(G)

# 计算高斯求导核 D
D = -2 * (x * np.exp(-x ** 2 / (2 * sigma ** 2))) / (\
    np.sqrt(2 * math.pi) * sigma ** 3)
D = D / (np.sum(np.abs(D)) / 2)

# 分别对图像进行 x 方向和 y 方向二维卷积
Dx = cv2.sepFilter2D(img, -1, D, G)
Dy = cv2.sepFilter2D(img, -1, G, D)

return Dx, Dy
```

```
def calculate_derivatives(img1, img2, smoothing, derivation):
"""
计算图像的导数

参数：
- img1: 前一帧图像
- img2: 后一帧图像
- smoothing: 高斯模糊的标准差
- derivation: 高斯求导的标准差

返回：
- i_x: x 轴方向的导数图像
```