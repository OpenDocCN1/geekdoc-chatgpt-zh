```
rimg_raw = np.asanyarray(Image.open(
r"./learncv_img/stereo/scenel.row3.col3.ppm"))

# 将图像转换为灰度图
limg = cv.cvtColor(limg_raw, cv.COLOR_BGR2GRAY)
rimg = cv.cvtColor(rimg_raw, cv.COLOR_BGR2GRAY)

# 将图像转换为 double 类型
limg = np.asanyarray(limg, dtype=np.double)
rimg = np.asanyarray(rimg, dtype=np.double)

# 定义一个和输入图像大小相同的数组
img_size = np.shape(limg)[0:2]

# 简单可视化左图像和右图像
plt.figure(dpi=100)
plt.title("binocular images")
plt.imshow(np.hstack([limg_raw, rimg_raw]))
plt.axis('off')
plt.show()
```

# binocular images

![](bbox=[0.246, 0.447, 0.745, 0.576])

接下来，我们实现生成视差图的函数。此函数首先计算左、右图像的整体差距，然后对每个像素进行视差搜索，并选择最小匹配代价所对应的视差作为最终视差。

```
def get_disp_map(max_D, win_size, img_size, match_mode="SAD"):
    """
    根据左、右图像，获取视差图

    参数：
    - max_D: 左、右图像之间最大可能的视差
    - win_size: 块尺寸
    - img_size: 双目图像尺寸
    - match_mode: 匹配模式，可选"SAD"或"SSD"
    """
    # 记录初始时间
    t1 = time.time()

    # 初始化视差图
    imgDiff = np.zeros((img_size[0], img_size[1], max_D))

    def SAD(x, y, error_map, win_size):
    return np.sum(error_map[(x - win_size):(x + win_size),
```