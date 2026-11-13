

```python
res = pts / pts[:, -1, None]
return res[:, :, :-1].squeeze()
```

```python
def euco_pts_2_homoco_pts(pts):
    """
    欧几里得坐标转换为齐次坐标
    """
    if len(pts.shape) == 1:
        pts = pts.reshape(1, -1)
    one = np.ones(pts.shape[0])
    res = np.c_[pts, one]
    return res.squeeze()
```

```python
def normalize(pts, T=None):
    """
    对点集进行归一化
    """
    # 如果 T 参数为空
    if T is None:
        # 求点集的平均值
        u = np.mean(pts, 0)
        # 求点集中每个点与原点之间的距离之和
        d = np.sum(np.sqrt(np.sum(np.power(pts, 2), 1)))
        # 计算归一化矩阵
        T = np.array([
            [np.sqrt(2) / d, 0, -(np.sqrt(2) / d * u[0])],
            [0, np.sqrt(2) / d, -(np.sqrt(2) / d * u[1])],
            [0, 0, 1]
        ])
    # 对点集进行归一化
    return homoco_pts_2_euco_pts(np.matmul(T,
        euco_pts_2_homoco_pts(pts).T).T), T
```

到这里我们就已经得到了两幅图之间的对应像素关系，接下来使用八点法求解基础矩阵。

```python
def estimate_fundamental(pts1, pts2, num_sample=8):
    """
    求解基础矩阵

    参数:
    - pts1: (np.ndarray): 匹配特征所得到的训练点集
    - pts2: (np.ndarray): 匹配特征所得到的查询点集

    返回:
    - f: (np.ndarray): 基础矩阵
    """
    n = pts1.shape[0]
    pts_index = range(n)
    sample_index = random.sample(pts_index, num_sample)
    p1 = pts1[sample_index, :]
    p2 = pts2[sample_index, :]
    n = len(sample_index)
    # 归一化点集坐标
    p1_norm, T1 = normalize(p1, None)
    p2_norm, T2 = normalize(p2, None)
    w = np.zeros((n, 9))
    # 构建 A 矩阵
    for i in range(n):
        w[i, 0] = p1_norm[i, 0] * p2_norm[i, 0]
        w[i, 1] = p1_norm[i, 1] * p2_norm[i, 0]
        w[i, 2] = p2_norm[i, 0]
        w[i, 3] = p1_norm[i, 0] * p2_norm[i, 1]
        w[i, 4] = p1_norm[i, 1] * p2_norm[i, 1]
        w[i, 5] = p2_norm[i, 1]
        w[i, 6] = p1_norm[i, 0]
        w[i, 7] = p1_norm[i, 1]
        w[i, 8] = 1
    # SVD
    U, sigma, VT = np.linalg.svd(w)
    f = VT[-1, :].reshape(3, 3)
    U, sigma, VT = np.linalg.svd(f)
    sigma[2] = 0
    f = U.dot(np.diag(sigma)).dot(VT)
    # 逆归一化
    f = T2.T.dot(f).dot(T1)

    ### 随机计算的 F 矩阵误差较大，使用 OpenCV 中的函数(RANSAC)计算 F 矩阵
    pts1 = pts1.astype(np.float32)
    pts2 = pts2.astype(np.float32)
    f, mask = cv2.findFundamentalMat(pts1, pts2, cv2.FM_RANSAC)

    return f
```

```python
def convert_F_to_E(F_single, K):
    """
    根据 F 矩阵计算 E 矩阵
    """
    inverse_K = np.linalg.inv(K)
    E_single = inverse_K.T.dot(F_single).dot(inverse_K)
    return E_single
```

```python
def get_Rt_from_E(E_single, K, pts1, pts2):
    """
    根据 E 矩阵计算 Rt，使用 OpenCV 的函数 recoverPose
    """
    # OpenCV 的 recoverPose 函数已经自动去除了不合理的 3 个解
    _, R, t, _ = cv2.recoverPose(E_single, pts1, pts2, K)
    return R, t
```

```python
def build_F_E_matrix(feats, K):
    """
    计算基础矩阵 F 和本质矩阵 E，从 E 中分离出 Rt 并返回
    """
    pair = dict()
    match = dict()
    Rts = dict()

    for i in range(len(feats)):
        for j in range(i + 1, len(feats)):
            matches = get_matches(
                feats[i]['des'], feats[j]['des'])
            pts1, pts2 = get_match_point(
                feats[i]['kpt'], feats[j]['kpt'], matches)
            assert pts1.shape == pts2.shape

            # 至少需要 8 个点来计算 F 矩阵
            if pts1.shape[0] < 8:
                continue
            # 计算 F 矩阵
            F_single = estimate_fundamental(pts1, pts2)
            # 根据 F 矩阵计算 E 矩阵
            E_single = convert_F_to_E(F_single, K)
            # 从 E 矩阵中得到相对位姿
            R, t = get_Rt_from_E(E_single, K, pts1, pts2)

            if pts1.shape[0] < 8:
                continue

            pair.update({(i, j): {'pts1': pts1, 'pts2': pts2}})
            match.update({(i, j): {'match': matches}})
            Rts.update({(i, j): {'R': R, 't': t}})

    return F_single, E_single, pair, match, Rts
```

```python
img_root = 'learncv_img/19_3D_recon/images'
imgs, feats, K = construct_img_info(img_root)
F, E, pair, match, Rts = build_F_E_matrix(feats, K)
print("The Fundamental Matrix is:\n", F)
print("The Essential Matrix is:\n", E)

# 不妨假定第一幅图为世界坐标系
R_t_0 = np.array([[1,0,0,0], [0,1,0,0], [0,0,1,0]])
R_t_1 = np.empty((3, 4))
R_t_1[:, :3] = Rts[(0, 1)]['R']
R_t_1[:, 3] = Rts[(0, 1)]['t'].reshape(3)
P_0 = K.dot(R_t_0)
P_1 = K.dot(R_t_1)
print("The camera matrix of the first image is:\n", P_0)
```

```
[[534.81896973  0. 320.  0. ]
 [  0. 533.75 213.  0. ]
 [  0.   0.   1.   0. ]]
```
The camera matrix of the second image is:
```
[[ 6.02699217e+02 -1.32348682e+02 -8.75717417e+01 5.27600711e+02]
 [ 1.51538241e+02 -1.73321278e+02  5.26549104e+02  8.74049809e+01]
 [ 3.23134701e-01 -9.46352981e-01  1.82851868e-05  3.84515925e-06]]
```

在得到了基础矩阵之后，我们就可以通过三角测量计算出三维空间点的坐标。

```python
def filter_matches_by_epipolar(pts1, pts2, F, threshold=3.0):
    """
    使用对极约束过滤匹配点

    参数:
    - pts1, pts2: 匹配点对
    - F: 基础矩阵
    - threshold: 对极误差阈值

    返回:
    - pts1_filtered, pts2_filtered: 过滤后的匹配点
    """
    pts1_reshaped = pts1.T.reshape(-1,1,2)
    pts2_reshaped = pts2.T.reshape(-1,1,2)

    # 计算两幅图像的极线
    lines1 = cv2.computeCorrespondEpilines(pts2_reshaped, 2, F)
    lines2 = cv2.computeCorrespondEpilines(pts1_reshaped, 1, F)

    dist1 = np.abs(np.sum(lines1.reshape(-1,3) *
                   np.hstack((pts1.T, np.ones((pts1.shape[1],1)))), axis=1))
    dist2 = np.abs(np.sum(lines2.reshape(-1,3) *
                   np.hstack((pts2.T, np.ones((pts2.shape[1],1)))), axis=1))

    mask = (dist1 < threshold) & (dist2 < threshold)
    return pts1[:,mask], pts2[:,mask]

pts1 = np.transpose(pair[(0,1)]['pts1'])
pts2 = np.transpose(pair[(0,1)]['pts2'])
# 过滤匹配点
pts1_filtered, pts2_filtered = filter_matches_by_epipolar(pts1, pts2, F)
print("过滤前的匹配点数量为: ", pts1.shape[1])
print("过滤后的匹配点数量为: ", pts1_filtered.shape[1])

# 三角测量获得空间点坐标
points_3d = cv2.triangulatePoints(P_0, P_1, pts1_filtered, pts2_filtered)
# 齐次坐标的最后一行为 1，需要除以最后一行
points_3d /= points_3d[3]
points_3d = points_3d[3:, :].T
print("得到的三维点为:\n", points_3d)

# 计算有效点占像素比例
print("检测到的点占图像像素的比例为: {:.2f}%".format(
    points_3d.shape[0]/len(imgs[0][...,0].flatten())*100))
```

过滤前的匹配点数量为： 16
过滤后的匹配点数量为： 13
得到的三维点为：
```
[[-0.60458395 -0.01198244  0.0969504 ]
 [-0.58099777 -0.21461379  0.05341815]
 [-0.47523613 -0.39286399 -0.15945556]
 [-0.56740703 -0.24286074 -0.00477856]
 [-0.49754951 -0.36653935 -0.14429421]
 [-0.47079379 -0.40467599 -0.19146369]
 [-0.55889738 -0.26988774 -0.09548055]
 [-0.60761656 -0.14041958 -0.12032324]
 [-0.56458993 -0.29775277 -0.2171921 ]
 [-0.6082215  -0.14859759 -0.13404173]
 [-0.43299244 -0.4819224  -0.33418579]
 [-0.5657818  -0.3328847  -0.29034157]
 [-0.5657818  -0.3328847  -0.29034157]]
```
检测到的点占图像像素的比例为: 0.004757%

可以发现，存在部分匹配点不严格遵循对极约束。接下来我们可视化极线和匹配点。

```python
def draw_epipolar_lines(pts1, pts2, img1, img2, F):
    """
    绘制极线
    参数:
    - pts1: (np.ndarray): 第一幅图像中的匹配点
    - pts2: (np.ndarray): 第二幅图像中的匹配点
    - img1: (np.ndarray): 第一幅图像
    - img2: (np.ndarray): 第二幅图像
    - F: (np.ndarray): 基础矩阵
    """
    # 将点坐标转换为整数
    pts1 = np.int32(pts1)
    pts2 = np.int32(pts2)

    # 选取前 8 个点进行可视化
    idx = np.arange(8)
    pts1 = pts1[idx]
    pts2 = pts2[idx]

    # 计算对应的极线
    lines1 = cv2.computeCorrespondEpilines(pts2.reshape(-1,1,2), 2, F)
    lines1 = lines1.reshape(-1,3)

    # 绘制极线
    imgl, imgr = drawlines(img1, img2, lines1, pts1, pts2)

    # 将两幅图拼接在一起
    vis = np.concatenate((imgl, imgr), axis=1)

    # 展示图像
    plt.figure(figsize=(10, 10))
    plt.imshow(vis)
    plt.axis('off')
    plt.show()
```

```python
def drawlines(imgl, imgr, lines, pts1, pts2):
    '''
    绘制极线

    参数:
    - imgl: (np.ndarray): 左图
    - imgr: (np.ndarray): 右图
    - lines: (np.ndarray): 极线
    - pts1: (np.ndarray): 左图对应的匹配点
    - pts2: (np.ndarray): 右图对应的匹配点
    '''
    # 获取图像尺寸
    r, c, _ = imgl.shape

    for r, pt1, pt2 in zip(lines, pts1, pts2):
        color = tuple(np.random.randint(0, 255, 3).tolist())
        # 计算直线上的两个点
        x0, y0 = map(int, [0, -r[2]/r[1]])
        x1, y1 = map(int, [c, -(r[2]+r[0]*c)/r[1]])
        # 在imgl 中绘制直线
        imgl = cv2.line(imgl, (x0, y0), (x1, y1), color, 1)
        # 在imgl 中绘制对应的特征点
        imgl = cv2.circle(imgl, tuple(pt1), 5, color, -1)
        # 在imgr 中绘制对应的特征点
        imgr = cv2.circle(imgr, tuple(pt2), 5, color, -1)
    return imgl, imgr

# 在原图上可视化特征点对应的极线
draw_epipolar_lines(pair[(0,1)]['pts1'], pair[(0,1)]['pts2'],
    imgs[0], imgs[1], F)
```

![](img/0e497601c0ca26be98c5399cbd72eb4c_309_0.png)

(另见彩插图 25)
