# 从零实战：YOLOv3-tiny 训练交通信号灯检测模型全流程指南

## 核心代码解析：基于 IOU 的 K-means 锚框生成器

在正式开启训练前，我们需要一套贴合数据集分布的先验框。下面这段 Python 代码实现了 YOLO 专用的 K-means 聚类逻辑，核心亮点在于使用 **IOU（交并比）** 作为距离度量，并采用 **中位数** 而非均值更新簇心，有效规避了异常框对聚类中心的干扰。

### 1. IOU 矩阵的向量化计算
`iou` 方法通过 NumPy 广播机制，一次性构建出所有真实框与聚类中心的宽高矩阵，利用 `np.minimum` 求取交集宽高，进而得到交集面积，最后套用公式 `inter / (box + cluster - inter)` 高效产出 IOU 矩阵。

### 2. 平均 IOU 评估指标
`avg_iou` 函数沿轴取最大值再求均值，直观反映当前聚类中心对整个数据集的覆盖能力，数值越接近 1 代表锚框越优。

### 3. K-means 迭代流程
`kmeans` 函数流程如下：
- **初始化**：从标注框中随机抽取 `k` 个作为初始簇心。
- **分配步**：计算 `1 - IOU` 作为距离，将每个框归属到最近的簇。
- **收敛判断**：若本轮归属结果与上一轮完全一致，提前终止循环。
- **更新步**：对每个簇内的框取**中位数**（`np.median`）作为新簇心，这比均值更鲁棒。
- 循环直至收敛，返回最终锚框。

### 4. 数据流转工具链
- `txt2boxes`：解析 Darknet 格式标注（`class x_center y_center w h` 或 `xmin,ymin,xmax,ymax`），提取归一化/绝对宽高。
- `result2txt`：将聚类结果按宽度排序后写入 `yolo_anchors.txt`，格式兼容 Darknet 直接读取。
- `txt2clusters`：串联上述流程，输出最终锚框尺寸与精度报告。

[PRE0]python

> **代码逻辑保持不变，此处保留原始实现供参考。**

---

## 实战演练：环境搭建与数据准备

一切从构建干净的工作空间开始。

### Step 1：创建工作目录并解压资源包
在家目录下建立实验文件夹，并依次解压模型代码包与数据集包。

[PRE1]

[PRE2]

[PRE3]

> **注意**：解压数据集后，需将 `dataset` 文件夹拷贝至 `object_detection_darknet` 目录下，确保后续脚本能正确定位图片与标注。

### Step 2：确认类别定义
本任务共涉及 5 个交通信号灯类别，顺序务必与后续配置文件、标注文件严格对齐：

[PRE4]

[PRE6]

---

## 配置文件定制：从通用模板到专用配置

### 1. 复制并重命名模板
进入代码根目录，复制 `yolov3-tiny-pill` 相关配置作为起点：

[PRE7]

### 2. 修改 `.data` 文件（`yolov3-tiny-traffic.data`）
核心字段调整如下：
- `classes=5`：匹配实际类别数。
- `train` / `valid`：指向训练集与验证集的索引文件（每行一张图片的相对路径）。
- `names`：指向类别名称文件（每行一个类名，顺序同 Step 2）。
- `backup`：权重保存目录。

[PRE8]

### 3. 修改 `.cfg` 文件（`yolov3-tiny-traffic.cfg`）
关键点只改三处：
- **第 1 行附近**：`classes=5`（每个 YOLO layer 之前的 `[convolutional]` filters 也要同步修改为 `(classes+5)*3`）。
- **两处 `[yolo]` 层**：`classes=5`、`mask` 保持默认即可（tiny 版通常为 `mask = 3,4,5` 和 `mask = 0,1,2`）。
- **anchors**：暂时保留原值，稍后用聚类结果替换。

> **提示**：`classes` 定义在 `[net]` 之外的全局变量中，同时每个 `[yolo]` 层内部也需单独设置。

---

## 关键步骤：生成专属锚框

锚框质量直接决定收敛速度与最终精度，必须在自有数据上重新聚类。

### 1. 格式转换：VOC XML → Darknet TXT
运行 `voc2yolo.py`，将 `Annotations` 下的 XML 转为 Darknet 所需的 `labels` 目录及 `object_train.txt` / `object_val.txt` 索引。

[PRE5]

脚本会遍历所有 XML，按类别映射写入归一化坐标，控制台会打印处理过的文件名列表供核对。

### 2. 执行 K-means 聚类
运行 `kmeans.py`，读取训练集标注框宽高，迭代输出 6 组锚框：

[PRE10]

[PRE11]

**输出解读**：
- 控制台逐行打印解析进度（图片索引、框数量、路径）。
- 最终给出 **6 组锚框尺寸（宽 高）**，已按宽度升序排列。
- **Accuracy: 84.59%** 表示平均 IOU 达标，可直接写入 `.cfg` 的 `anchors` 字段（按 `w1,h1, w2,h2, ...` 顺序填入）。

> **最佳实践**：将打印出的 6 对数值填入 `yolov3-tiny-traffic.cfg` 的 `[net]` 段 `anchors=` 后，并确保两个 `[yolo]` 层的 `mask` 正确划分大中小三组。

---

## 启动训练：CPU 环境下的暗网启动

一切就绪，开启训练循环。

### 1. 赋予暗网可执行权限
[PRE12]

### 2. 激活虚拟环境并启动
切换至配置好依赖的 Conda 环境（Ubuntu 20.04 必需），调用 `darknet-cpu` 启动检测器训练：

[PRE13]

**参数详解**：
- `detector train`：训练模式。
- `cfg/yolov3-tiny-traffic.data`：数据配置。
- `cfg/yolov3-tiny-traffic.cfg`：网络结构。
- `yolov3-tiny.conv.15`：预训练权重（仅含前 15 层卷积，加速收敛）。
- `15`：`gpus` 参数，此处传 `15` 或留空均表示 CPU 模式（源码中 `-gpus` 解析逻辑）。

**控制台关键日志**：
- `GPU isn't used` / `OpenCV isn't used`：确认纯 CPU 运行，数据增强会变慢但可跑通。
- `mini_batch = 16, batch = 16`：批次大小由 `cfg` 中 `batch` / `subdivisions` 决定。
- 后续每个 batch 会打印 `Region Avg IOU`, `Class`, `Obj`, `No Obj`, `Avg Recall`, `count` 等指标，观察 `Avg IOU` 上升、`loss` 下降即为正常。

---

## 避坑指南与经验总结

1. **路径相对性**：`.data` / `train.txt` / `val.txt` 中的路径均相对于 **darknet 可执行文件运行时的工作目录**（即 `object_detection_darknet`）。
2. **类别顺序一致性**：`traffic.names`、标注文件 class_id、`.data` 中 `classes`、`.cfg` 中 `classes`、**五位一体**，一旦对不上要么报错要么训练出乱码模型。
3. **锚框聚类不可省略**：通用锚框在特定场景（如远近车灯尺度差大）下 IOU 往往 < 60%，自聚类通常能提 10~20 个点。
4. **CPU 训练耐心**：`OpenCV isn't used` 意味着数据增强退化为纯 Python/NumPy 实现，单批次耗时可能达数秒，建议减小 `batch` / `subdivisions` 或挂载 GPU 版 `darknet`。
5. **权重保存**：训练过程中会在 `backup/` 目录周期性生成 `yolov3-tiny-traffic_last.weights` 与 `yolov3-tiny-traffic_<iter>.weights`，测试时优先用 `_last` 或 loss 最低的那版。

---

至此，从裸数据到可训练模型的完整链路已跑通。下一步可编写推理脚本、部署至边缘设备或导出 ONNX 进一步加速。祝调参顺利，mAP 飙升！