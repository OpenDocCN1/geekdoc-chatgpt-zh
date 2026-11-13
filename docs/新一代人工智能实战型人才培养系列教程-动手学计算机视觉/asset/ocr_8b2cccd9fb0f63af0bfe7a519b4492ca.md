锚框坐标生成器，用于生成不同尺寸和高宽比的锚框的坐标

```
```
"""
def __init__(
    self,
    sizes=((128, 256, 512),),
    aspect_ratios=((0.5, 1.0, 2.0),),
):
"""
初始化锚框生成器

参数:
- sizes: 锚框的尺寸
- aspect_ratios: 锚框的高宽比
"""
super(AnchorGenerator, self).__init__()

# 确保 sizes 和 aspect_ratios 是正确的格式，方便后续处理
if not isinstance(sizes[0], (list, tuple)):
    # 将每个尺寸转换为元组形式
    sizes = tuple((s,) for s in sizes)
    # 转换为[(128,), (256,), (512,)]

if not isinstance(aspect_ratios[0], (list, tuple)):
    # 为每个尺寸复制相同的高宽比
    aspect_ratios = (aspect_ratios,) * len(sizes)

# 每个尺寸必须对应一个高宽比设置
assert len(sizes) == len(aspect_ratios)

self.sizes = sizes
self.aspect_ratios = aspect_ratios

# 存放每种尺寸和高宽比组合下的锚框
self.cell_anchors = [
    self.generate_anchors(size, aspect_ratio) for size, aspect_ratio in
    zip(sizes, aspect_ratios)
]
```
```

```
```
def generate_anchors(self, scales, aspect_ratios, dtype=torch.float32,
    device="cpu"):
"""
生成一组锚框的坐标，这些锚框的中心位于(0,0)
"""
scales = torch.as_tensor(scales, dtype=dtype, device=device)
# aspect_ratios 是高宽比
aspect_ratios = torch.as_tensor(aspect_ratios, dtype=dtype, device=device)

# h_ratios=sqrt(aspect_ratios), w_ratios=1/sqrt(aspect_ratios)时
# h_ratios/w_ratios==aspect_ratios
# 根据高宽比计算锚框的宽和高
h_ratios = torch.sqrt(aspect_ratios)
w_ratios = 1 / h_ratios
```
```