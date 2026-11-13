```
```python
ws = (w_ratios[:, None] * scales[None, :]).view(-1)
hs = (h_ratios[:, None] * scales[None, :]).view(-1)

# x1,y1,x2,y2 (左上角右下角)，中心点为 (0,0)
# 生成中心在原点的锚框的坐标
base_anchors = torch.stack([-ws, -hs, ws, hs], dim=1) / 2
return base_anchors.round()

def set_cell_anchors(self, dtype, device):
    # 初始化锚框
    self.cell_anchors = [cell_anchor.to(dtype=dtype, device=device)
                         for cell_anchor in self.cell_anchors]

def grid_anchors(self, grid_sizes, strides):
    """
    在每个点处生成的锚框的坐标

    参数：
    - grid_size: 特征图的空间分辨率
    - strides: 锚框中心点移动的步长
    """
    anchors = []
    # 导入生成好的每种尺寸和高宽比组合的锚框
    cell_anchors = self.cell_anchors

    # 判断锚框是否为空
    assert cell_anchors is not None

    for size, stride, base_anchors in zip(grid_sizes, strides, cell_anchors):
        grid_height, grid_width = size
        stride_height, stride_width = stride
        device = base_anchors.device

        # 计算所有点处的锚框中心坐标偏移量
        shifts_x = torch.arange(0, grid_width, dtype=torch.float32, device=
                                device) * stride_width
        shifts_y = torch.arange(0, grid_height, dtype=torch.float32, device=
                                device) * stride_height
        shift_y, shift_x = torch.meshgrid(shifts_y, shifts_x)

        shift_x = shift_x.reshape(-1)
        shift_y = shift_y.reshape(-1)
        shifts = torch.stack((shift_x, shift_y, shift_x, shift_y), dim=1)

        # shifts 为偏移量：表示锚框中心点从(0,0)移到(shift_x,shift_y)
        # 将锚框平移到所有点处
        anchors.append((shifts.view(-1, 1, 4) + base_anchors.view(1, -1, 4)).
                       reshape(-1, 4))

    return anchors

def forward(self, image_list, feature_maps):
```
```