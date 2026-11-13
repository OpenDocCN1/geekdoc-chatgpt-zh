```
```python
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

#### 随机计算的 F 矩阵误差较大，使用 OpenCV 中的函数(RANSAC)计算 F 矩阵
pts1 = pts1.astype(np.float32)
pts2 = pts2.astype(np.float32)
f, mask = cv2.findFundamentalMat(pts1, pts2, cv2.FM_RANSAC)

return f
```
```

```
```python
def convert_F_to_E(F_single, K):
    '''\n    根据 F 矩阵计算 E 矩阵\n    '''
    inverse_K = np.linalg.inv(K)
    E_single = inverse_K.T.dot(F_single).dot(inverse_K)
    return E_single
```
```

```
```python
def get_Rt_from_E(E_single, K, pts1, pts2):
    '''\n    根据 E 矩阵计算 Rt，使用 OpenCV 的函数 recoverPose\n    '''
    # OpenCV 的 recoverPose 函数已经自动去除了不合理的 3 个解
    _, R, t, _ = cv2.recoverPose(E_single, pts1, pts2, K)
    return R, t
```
```

```
```python
def build_F_E_matrix(feats, K):
    '''\n    计算基础矩阵 F 和本质矩阵 E，从 E 中分离出 Rt 并返回\n    '''
    pair = dict()
    match = dict()
    Rts = dict()
```
```