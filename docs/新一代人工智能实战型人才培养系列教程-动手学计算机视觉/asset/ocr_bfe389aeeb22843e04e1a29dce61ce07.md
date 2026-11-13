参数:
- weights: 编码和解码时使用的权重，用于平衡不同尺寸包围盒的影响
- bbox_xform_clip: 编码过程中限制坐标偏移量的最大值，防止过大的偏移量导致不稳定
"""

```
self.weights = weights
self.bbox_xform_clip = bbox_xform_clip
```

```
# encoder 模块
####################################################################
def encode(self, reference_boxes, proposals):
    boxes_per_image = [len(b) for b in reference_boxes]
    reference_boxes = torch.cat(reference_boxes, dim=0)
    proposals = torch.cat(proposals, dim=0)
    targets = self.encode_single(reference_boxes, proposals)

    return targets.split(boxes_per_image, 0)
```

```
def encode_single(self, reference_boxes, proposals):
    """
    编码：计算包围盒（proposals）相对于参考包围盒（reference_boxes）的坐标偏移量
    参数：
    - reference_boxes：参考包围盒的坐标
    - proposals：需要被编码的包围盒的坐标

    返回：
    - 编码后的偏移量
    """
    dtype = reference_boxes.dtype
    device = reference_boxes.device
    weights = torch.as_tensor(self.weights, dtype=dtype, device=device)
    # 调用 encode_boxes 函数对包围盒进行编码
    targets = encode_boxes(reference_boxes, proposals, weights)
    return targets
```

```
# decoder 模块
####################################################################
def decode(self, rel_codes, boxes):
    assert isinstance(boxes, (list, tuple))
    assert isinstance(rel_codes, torch.Tensor)
    boxes_per_image = [b.size(0) for b in boxes]

    concat_boxes = torch.cat(boxes, dim=0)
    box_sum = 0

    for val in boxes_per_image:
        box_sum += val

    if box_sum > 0:
        rel_codes = rel_codes.reshape(box_sum, -1)
```