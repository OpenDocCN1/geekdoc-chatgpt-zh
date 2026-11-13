```
reprojected_coor = reprojected_coor[:, :2] / reprojected_coor[:, 2:3]

# 计算重投影误差
error = np.linalg.norm(reprojected_coor - pixel_coor, axis=1)

return error.mean()

# 将内外参矩阵都转化为齐次坐标
K_homo = np.column_stack((K, np.zeros(3)))
K_homo = np.row_stack((K_homo, [0, 0, 0, 1]))

# 计算所有图像重投影误差的均值
mean_error = 0
for j in range(len(imgpoints)):
    extrinsic_homo = np.row_stack((extrinsics_param[j], [0, 0, 0, 1]))

    # 计算每一幅图像的重投影误差
    error = reproject_error(K_homo, extrinsic_homo, imgpoints[j], objpoints[j])
    mean_error += error

# 得到平均重投影误差
mean_error /= len(imgpoints)
print("平均重投影误差为：{}".format(mean_error))
```

```
平均重投影误差为: 2.2527822694246837
```

到这里，我们就完成了基础的照相机标定流程。从重投影误差来看，我们计算出来的照相机参数还是比较精确的，平均误差为 2.25 像素。在实际应用过程中，我们往往直接使用 OpenCV 的标定函数来实现照相机标定：

```
# 转换成 OpenCV 需要的格式
objpoints_cv = [np.hstack((i, np.zeros((i.shape[0], 1)))).astype(
    np.float32) for i in objpoints]
imgpoints_cv = [i[:, None, :] for i in imgpoints]

# 使用 OpenCV 的标定函数来标定照相机
ret, mtx, dist, rvecs, tvecs = cv2.calibrateCamera(objpoints_cv,
    imgpoints_cv, gray.shape[::-1], None, None)

print("照相机内参：")
# 照相机内参，输出格式为[[fx,0,cx],[0,fy,cy],[0,0,1]]
print(mtx,"\n")
print("畸变参数：")
# k1,k2,p1,p2,k3
print(dist)
```

```
照相机内参：
[[530.16217137   0.         341.48703364]
 [  0.         530.21111757 230.10277488]]
```