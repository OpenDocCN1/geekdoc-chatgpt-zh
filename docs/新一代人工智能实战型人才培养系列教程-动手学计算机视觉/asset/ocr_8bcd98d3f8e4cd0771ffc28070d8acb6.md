```
xy_hessian = hessian[:,2, :2]
xy_hessian_trace = np.trace(xy_hessian)
xy_hessian_det = np.linalg.det(xy_hessian)
# 消除边缘响应
if xy_hessian_det > 0 and \
   (xy_hessian_trace ** 2) / xy_hessian_det < \
   ((gamma + 1) ** 2) / gamma:
   # 极值点坐标
   pt = ((y + update[0]), (x + update[1]))
   # 极值点尺度
   size = sigma * (2 ** *((layer + update[2])))
   # 计算特征点的方向（主方向和辅方向）
   # 利用 computeOrien() 函数进行计算
   orien_list = computeOrien(pt, size, layer, g_image)
   for tmp_orien in orien_list:
       # 尺度
       scales.append(size)
       # 位置
       layers.append(layer)
       corners.append(pt)
       # 方向
       orientations.append(tmp_orien)
return
```

进一步实现上述代码中出现的一些函数。

```
# 编写一些数学函数

# 计算一阶导数
def computelderivative(cube):
    # cube 是由 DoG 空间中相邻的 3 层组成，其中每层的大小被切片成 3*3
    # 需要计算 cube 正中心位置的梯度
    dx = (cube[1, 1, 2] - cube[1, 1, 0]) / 2
    dy = (cube[1, 2, 1] - cube[1, 0, 1]) / 2
    ds = (cube[2, 1, 1] - cube[0, 1, 1]) / 2
    return np.array([dx, dy, ds])

# 计算二阶导数
def compute2derivative(cube):
    # cube 是由 DoG 空间中相邻的 3 层组成，其中每层的大小被切片成 3*3
    # 需要计算 cube 正中心位置的梯度

    # 根据二阶导数的定义写出各个分量
    center = cube[1, 1, 1]

    dxx = cube[1, 1, 2] + cube[1, 1, 0] - 2 * center
    dyy = cube[1, 2, 1] + cube[1, 0, 1] - 2 * center
    dss = cube[2, 1, 1] + cube[0, 1, 1] - 2 * center

    dxy = (cube[1, 2, 2] - cube[1, 2, 0]
```