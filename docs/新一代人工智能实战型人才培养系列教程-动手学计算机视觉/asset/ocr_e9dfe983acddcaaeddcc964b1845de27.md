```
computeBlobAttribute(x, y, layer+1, dog_images,
    sigma, threshold, border,
    num_layers, g_images[layer],
    corners, scales, orientations,
    layers)

return g_images, corners, scales, orientations, layers
```

接下来，对检测到的特征点生成描述符。

# # SIFT 特征点描述符的生成

```
def compute_descriptors(g_images, corners, scales,
    orientations, layers):

    if len(corners) != len(scales) \n    or len(corners) != len(orientations):
        raise ValueError(
        '\'corners'，\'scales' and \'orientations' \n        must all have the same length.')

    descriptors_list = []

    for pt, size, orien, layer in \n    zip(corners, scales, orientations, layers):

        # 读取特征点的各项信息
        g_image = g_images[layer]
        x, y = np.round(np.array(pt)).astype(np.int32)
        orien = 360 - orien

        # 计算块状区域大小
        win_s = 3 * size
        win_l = int(round(min(2**0.5 * win_s * (4+1) / 2,
        np.sqrt(g_image.shape[0]**2+g_image.shape[1]**2))))

        # 用列表依次存储块状区域内所有点的信息
        i_index = []
        j_index = []
        value_list = []
        orien_index = []

        # 三维数组存储 16 个窗口的 8 个方向，为防止计算时边界溢出,
        # 在行、列的首尾各扩展一次
        result_cube = np.zeros((4 + 2, 4 + 2, 8))

        # 统计一个块状区域内 16 个子区域的像素梯度直方图
        for i in range(-win_l, win_l+1):
            for j in range(-win_l, win_l+1):

                # 获得旋转之后的坐标
                i_rotate = j * np.sin(np.deg2rad(orien)) "
```