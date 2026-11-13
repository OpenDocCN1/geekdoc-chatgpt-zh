
## 12.3.3 Faster R-CNN

尽管 Fast R-CNN 相较于 R-CNN 在测试时间上实现了显著的提升，从每幅图像的 47 s 缩减至 0.32 s，测试速度提升了 146 倍，但需要注意的是，这个测试时间并未包含候选区域提取所用的时间。实际上，候选区域提取过程正是目标检测速度的瓶颈所在。

为了进一步提升目标检测的速度，任少卿、何恺明、Ross Girshick 和孙剑于 2015 年提出了 Faster R-CNN[4]。该方法通过引入候选区域网络（region proposal network，RPN）实现了高效的候选区域提取，将目标检测的测试时间缩短到每幅图像 0.2 s（包含候选区域提取过程），相较于 R-CNN 实现了近 250 倍的速度提升。相较于 Fast R-CNN，Faster R-CNN 通过 RPN 直接从图像的特征图生成若干候选区域，取代了之前传统的候选目标检测方法（如 Selective Search），实现了端到端的训练模式。简单地说，Faster R-CNN 可以被理解为“RPN + Fast R-CNN”，其整体流程如图 12-9 所示，主要包括以下两个步骤：

- (1) 通过 RPN 从输入图像中提取候选区域；
- (2) 基于 Fast R-CNN 的框架对这些候选区域进行目标类别分类与包围盒回归；

![](img/0e497601c0ca26be98c5399cbd72eb4c_186_0.png)

图 12-9 Faster R-CNN 流程（插图素材源自参考文献[4]）

RPN 是 Faster R-CNN 中引入的核心模块，它采用轻量级的卷积神经网络作用于特征图上，利用卷积操作的滑动窗口特性，实现了滑动窗口检测策略，从而取代了传统的候选区域生成方法，其过程如图 12-10 所示。RPN 以主干网络中的卷积层输出的特征图为输入，利用轻量级的卷积神经网络在特征图上滑动，每滑动到一点处，将以当前点为中心的空间分辨率为 s×s（一般 s = 3 像素）的滑动窗口内的特征映射成一个维度为 d（通常，d = 256 或者 512）的特征向量。该特征向量被输入到两个并行的全连接层——一个分类层和一个回归层，实现候选区域生成。注意，目前我们只提及了候选区域的位置，即当前滑动窗口的中心，而候选区域的尺寸仍然是未知的。为此，RPN 中引入了一个重要概念——锚框（anchor）。锚框是居中于当前滑动窗口的一个矩形区域，通过预定义的尺寸和高宽比确定其空间分辨率。这样，上述分类层输出两个分数，即当前锚框中存在目标的置信度和不存在目标的置信度；回归层输出 4 个包围盒的坐标偏移量参数，即当前锚框（锚框是矩形区域，也是一个包围盒）与其对应目标的真实包围盒之间的坐标偏移量。由于图像中的目标尺寸是未知的，为了实现多尺寸目标检测，RPN 在特征图的每个点生成多个候选区域，生成的候选区域个数 k 由预定义的锚框种类决定，即由基础空间分辨率的锚框种类决定。RPN 中通常采用 3 种锚框尺寸和 3 种锚框高宽比，所以总共有 k = 9 种预定义的锚框。因为定义了 k 种锚框，上述的分类层和回归层分别在每个滑动窗口处输出 2k 个置信度分数和 4k 个包围盒的坐标偏移量参数。若主干网络卷积层输出的特征图的大小为 H×W （一般约为 2400），其中 H 和 W 分别是特征图的高和宽，则该图像中锚框的总数为 \( H \times W \times k \) 个。

![](img/0e497601c0ca26be98c5399cbd72eb4c_187_0.png)

### 图 12-10 RPN 的网络结构（插图源自参考文献[4]）

Faster R-CNN 同样包括训练和测试两个阶段。在训练阶段，相比 Fast R-CNN 的训练阶段，Faster R-CNN 增加了 RPN 的训练。类似于 R-CNN 和 Fast R-CNN 的训练过程，为了训练 RPN，需要给每个锚框赋予一个真实类别标签，该真实类别标签是一个二类标签，表示该锚框中是否存在需要检测的目标。真实类别标签的赋予仍然基于 IoU 的准则。满足以下两个条件之一，一个锚框就可以被赋予正的真实类别标签（该锚框为正锚框，其中存在需要检测的目标）：

- (1) 该锚框内部的像素集合与某个真实包围盒内部的像素集合之间的 IoU 是所有锚框中最大的；
- (2) 该锚框内部的像素集合与任意一个真实包围盒内部的像素集合之间的 IoU 大于 0.7。

通常来说，根据条件 2 已经足够确定一个锚框的真实类别标签为正标签，但在有些极端情况下，对一个真实包围盒，可能无法找到任何一个锚框满足条件 2，这时就需要条件 1 作为补充。如果一个锚框不满足上述任意一个条件，并且该锚框内部的像素集合与每个真实包围盒内部的像素集合之间的 IoU 都小于 0.3，那么这个锚框将被赋予负的真实类别标签（该锚框为负锚框，其中不存在需要检测的目标）。如果一个锚框既没有被赋予正标签，也没有被赋予负标签，那么这个锚框将不参与 RPN 的训练过程。

RPN 也为每个锚框指定了一个真实包围盒，用于后续包围盒回归任务。具体而言，首先计算每个真实包围盒内部的像素集合与所有锚框内部的像素集合的 IoU，找到最大 IoU 对应的真实包围盒与锚框，并将它们匹配；接着，对剩下未匹配的锚框，先寻找与其内部的像素集合 IoU 最大的像素集合对应的真实包围盒，若该 IoU 大于设定的阈值（一般为 0.5），则将该真实包围盒匹配给该锚框。反之，则不进行匹配。

在上述定义的基础上，对一幅图像而言，RPN 的损失函数定义如下：

\( L(q^i, u^i, t^i, v^i) = \frac{1}{N_{cls}} \sum_i L_{cls}(q^i, u^i) + \frac{\lambda}{N_{reg}} \sum_i u^i L_{reg}(t^i, v^i) \)

其中，\( i \) 是一个由这幅图像中锚框组成的小批次（mini-batch）中的锚框索引。理论上，该小批次可以包含这幅图像中所有的锚框，但这样计算损失函数会导致类别样本数量失衡，因为通常一幅图像中负锚框的个数远多于正锚框。为此，RPN 在一幅图像中按正负锚框最大比例为 1：1 随机采样 256 个锚框来进行损失函数的计算，这 256 个锚框便是这幅图像产生的“小批次”。如果一幅图像中的正锚框数量少于 128，那么将使用负锚框对这一小批次进行填充。\( q^i \) 为该锚框为正锚框的预测分数，\( u^i \) 是该锚框被赋予的真实类别标签。若锚框为正锚框，则 \( u^i = 1 \)，反之若锚框为负锚框，\( u^i = 0 \)。\( t^i \) 和 \( v^i \) 分别是真实包围盒相对于该锚框的坐标偏移量的预测值和真实值。包围盒回归损失使用平滑函数 \( smooth_{L_1} \) 进行计算，另外，\( u^i L_{reg} \) 表明只有正锚框才需要进行包围盒回归。\( N_{cls} \) 是小批次的大小，即 \( N_{cls} = 256 \)；\( N_{reg} \) 是特征图上点的总数，\( N_{reg} \) 近似为 2400；\( \lambda \) 是一个超参数，默认 \( \lambda = 10 \)，这样，分类损失和包围盒回归损失的权重近似相等。

RPN 产生的一些候选区域之间存在较高的重叠。为了减少冗余，RPN 根据它们的置信度分数采用非极大值抑制（NMS）来减少重叠的候选区域。其中，NMS 的 IoU 阈值固定为 0.7，这样，每幅图像大约会保留 2000 个候选区域。NMS 不但不会降低最终的检测准确性，还减少了候选区域的数量。在进行 NMS 后，RPN 选取分数排名靠前的 \( N \) 个候选区域用于 Fast R-CNN 的训练。由于 RPN 和 Fast R-CNN 的分别训练会以不同的方式影响主干网络的参数，因此 Faster R-CNN 采用了一种“四步交替训练”的方式实现训练时主干网络的参数共享，而不是学习两组单独的主干网络参数。四步交替训练流程如下。

- (1) RPN 所接的主干网络参数由在 ImageNet 数据集上预训练好的主干网络参数初始化，并按上述过程训练 RPN 和与之所接的主干网络；
- (2) Fast R-CNN 所接的主干网络参数同样由在 ImageNet 数据集上预训练好的主干网络参数初始化。然后使用第一步 RPN 生成的候选区域训练 Fast R-CNN 和与之所接的主干网络。注意，此时 RPN 和 Fast R-CNN 不共享主干网络的参数，即它们所接的主干网络的参数是不同的；
- (3) 使用 Fast R-CNN 所接的主干网络参数来初始化 RPN 所接的主干网络参数，并固定主干网络参数，微调只属于 RPN 的网络层级。此时，RPN 和 Fast R-CNN 共享主干网络；
- (4) 固定共享的主干网络的参数，微调只属于 Fast R-CNN 的网络层级。

Faster R-CNN 在测试阶段的步骤与训练阶段类似，也是包含了上述几个步骤。对一幅测试图像，Faster R-CNN 先利用 RPN 生成一系列候选区域，利用 NMS 对这些候选区域进行筛选，然后利用 Fast R-CNN 对这些候选区域进行包围盒的预测。

接下来我们来看 RPN 的代码实现中的一些细节。在代码中，RPN 被定义为 RegionProposalNetwork，输入是使用 CNN 主干网络提取训练图像得到的特征图，输出是该特征图上的候选区域。

## 12.4 RPN 代码整体框架

先概览 RPN 代码的整体框架。

### class RegionProposalNetwork(torch.nn.Module):

"""
RPN 的实现。该网络负责生成候选区域
这些区域随后被传给 Fast R-CNN 模块
"""

```python
def __init__(
    self,
    # 生成锚框的坐标的模块
    anchor_generator,
    # 用于对锚框进行正、负分类和包围盒回归的网络头
    head,
    # 锚框被判定为正锚框的 IoU 阈值，默认值为 0.7
    # 若锚框内部的像素集合与任意一个真实包围盒内部的像素集合的 IoU 都大于 fg_iou_thresh
    # 则认为该锚框中存在需要检测的目标
    fg_iou_thresh,
    # 锚框被判定为负锚框的 IoU 阈值，默认值为 0.3
    # 若锚框内部的像素集合与所有真实包围盒内部的像素集合的 IoU 都小于 bg_iou_thresh
    # 则认为该锚框中不存在需要检测的目标
    bg_iou_thresh,
    # 每幅图像采样的锚框数目
    batch_size_per_image,
    # 采样的正锚框的比例
    # 正锚框数=batch_size_per_image*positive_fraction
    positive_fraction,
    # NMS 前候选区域数目
    # 最多选取前 pre_nms_top_n 个候选区域
    pre_nms_top_n,
    # NMS 后候选区域数目
    # 最多选取前 post_nms_top_n 个候选区域送入 roi_head
    post_nms_top_n,
    # NMS 时，设定的阈值
    nms_thresh,
    # 最终返回分数大于该阈值的候选区域
    score_thresh=0
):
    super(RegionProposalNetwork, self).__init__()
    self.anchor_generator = anchor_generator
    self.head = head
    # 对锚框的坐标进行编码和解码的编解码器
    self.box_coder = det_utils.BoxCoder(weights=(1.0, 1.0, 1.0, 1.0))
    # 定义 IoU 的函数
    self.box_similarity = box_ops.box_iou
    # 定义用于锚框和真实包围盒匹配的匹配器
    self.proposal_matcher = det_utils.Matcher(
    fg_iou_thresh,
    bg_iou_thresh,
    allow_low_quality_matches=True,
)
# 定义正负锚框采样器，确保正负样本均衡
self.fg_bg_sampler = det_utils.BalancedPositiveNegativeSampler(
    batch_size_per_image, positive_fraction
)

self._pre_nms_top_n = pre_nms_top_n
self._post_nms_top_n = post_nms_top_n
self.nms_thresh = nms_thresh
self.score_threshold = score_thresh
# 定义去除面积过小的候选区域的阈值
self.min_size = 1e-3

def pre_nms_top_n(self):
    # 返回训练或测试阶段前 NMS 操作的候选区域数量
    pass

def post_nms_top_n(self):
    # 返回训练或测试阶段后 NMS 操作的候选区域数量
    pass

def assign_targets_to_anchors(self, anchors, targets):
    # 为每个锚框赋予正、负标签和与其匹配的真实包围盒
    pass

def filter_proposals(self, proposals, objectness, image_shapes,
    num_anchors_per_level):
    # 对候选区域进行筛选，得到高质量的候选区域
    pass

def compute_loss(self, objectness, pred_bbox_deltas, labels,
    regression_targets):
    # 计算训练时的损失函数
    pass

def forward(self, images, features, targets=None):
    '''
```

RPN 的前向传播。输入图像和特征图，生成候选区域

- 参数：
- images: 输入的图像
- features: 从每幅图像计算得到的不同网络层级输出的特征图，该变量的数据结构为字典
- targets: 每幅图像中的真实包围盒。该参数可以为空，如果不为空，则需要提供每个真实包围盒的坐标

- 返回：
- boxes: RPN 预测的候选区域
- losses: 训练时的损失，测试时该项为空

```python
# 得到主干网络不同层级输出的特征图
features = list(features.values())
```

```python
# 预测每个锚框是否包含目标的置信度和与其匹配的真实包围盒相对该锚框的坐标偏移量
objectness, pred_bbox_deltas = self.head(features)

# 生成锚框的坐标
anchors = self.anchor_generator(images, features)

num_images = len(anchors)
num_anchors_per_level_shape_tensors = [o[0].shape for o in objectness]
num_anchors_per_level = [s[0] * s[1] * s[2] for s in num_anchors_per_level_shape_tensors]
objectness, pred_bbox_deltas = concat_box_prediction_layers(objectness,
pred_bbox_deltas)

# 注意这里每层的 H、W 不全相同

# 这里使用 pred_bbox_deltas.detach 阻断梯度传播
# 在训练后续网络（Fast R-CNN）时可以保持 RPN 的参数不更新

# 解码得到候选区域的包围盒的坐标
proposals = self.box_coder.decode(pred_bbox_deltas.detach(), anchors)
proposals = proposals.view(num_images, -1, 4)

# 先按分数排序，选最大的前 pre_nms_top_n 个，并对越界的候选区域进行剪裁
# 筛选候选区域并应用 NMS 去除冗余的候选区域
boxes, scores = self.filter_proposals(proposals, objectness,
images.image_sizes, num_anchors_per_level)

losses = {}
# 注意：只有在训练时才进行以下步骤
if self.training:

    # 确保训练时提供了目标
    assert targets is not None
    # 为锚框赋予标签和与其匹配的真实包围盒
    labels, matched_gt_boxes = self.assign_targets_to_anchors(anchors,
targets)

    # regression_targets 为真实包围盒相对于与之匹配的锚框的坐标偏移量
    # 对此偏移量进行编码
    regression_targets = self.box_coder.encode(matched_gt_boxes, anchors)

    # 计算损失函数
    loss_objectness, loss_rpn_box_reg = self.compute_loss(objectness,
pred_bbox_deltas, labels, regression_targets)

    losses = {
    "loss_objectness": loss_objectness,
    "loss_rpn_box_reg": loss_rpn_box_reg,
    }

return boxes, losses

def concat_box_prediction_layers(box_cls, box_regression):
```
