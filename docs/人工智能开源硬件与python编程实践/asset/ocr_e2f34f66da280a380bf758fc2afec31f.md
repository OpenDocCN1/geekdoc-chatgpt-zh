# 在每个比例下，使用 x_overlap(0 - 1) 和 y_overlap(0 - 1) 作为指导，在 ROI 中移动检测窗口。
# 如果将 overlap 设置为 0.5，则每个检测窗口将与前一个检测窗口重叠 50%。
# 请注意，计算工作重叠越多，负载越多。
# 最后，对于在 x/y 维度上滑动网络之后的多尺度匹配，检测窗口将通过 scale_mul(0 - 1) 缩小到 min_scale(0 - 1)。
# 例如，如果 scale_mul 为 0.5，则检测窗口将缩小 50%。
# 请注意，在较低比例下，如果 x_overlap 和 y_overlap 较小，则搜索区域会更多...
# contrast_threshold 会跳过平坦区域。

# 设置 x_overlap = -1 会强制窗口始终保持在 x 方向的 ROI 中心。
# 如果 y_overlap 不为 -1，则该方法将搜索所有垂直位置。
# 设置 y_overlap = -1 会强制窗口始终在 y 方向的 ROI 中居中。
# 如果 x_overlap 不是 -1，则该方法将在所有水平位置搜索。

```
for obj in net.search(img, threshold = 0.6, min_scale = 0.5, scale_mul = 0.5, \
x_overlap = 0.5, y_overlap = 0.5, contrast_threshold = 0.5) :
print ( " Detected % s - Confidence % f%% % " % ( labels [ obj . index
( ) ] , obj . value ( ) ) )
img . draw_rectangle ( obj . rect ( ) , color = ( 255 , 0 , 0 ) )
print(clock . fps ( ))
```

图像分类程序运行情况如图 13.4 所示，右上区域显示出了摄像头捕捉到的视频图像，其中叠加显示了实时检测出的目标图像区域及得分情况。10 种物体的分类检测情况如图 13.5 所示。
利用 cifar10 卷积神经网络模型，能够快速检测出图像中存在的其他 10 种目标物体，分类识别情况如图 13.5 所示。