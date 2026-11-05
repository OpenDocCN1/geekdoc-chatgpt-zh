```
def forward(self, x):
    # 进行 residual 层的空洞卷积
    out = self.net(x)
    # 若输入和输出通道数不同则进行 1d 卷积降采样，否则还是输入 x
    res = x if self.downsample is None else self.downsample(x)
    # 结果相加
    return self.relu(out + res)

# 定义时间卷积网络的整体架构
class TemporalConvNet(nn.Module):
def __init__(self, num_inputs, num_channels, kernel_size, dropout):
    # 卷积核、通道数、dropout 都是需要通过多次调参来选择最适合数据的
    # num_inputs 的格式类似于[256, 256, 256, 256]
    # 这意味着一共四层残差模块，每层通道数均为 256
    super(TemporalConvNet, self).__init__()
    layers = []
    num_levels = len(num_channels)
    for i in range(num_levels):
        dilation_size = 2 ** i
        in_channels = num_inputs if i == 0 else num_channels[i - 1]
        out_channels = num_channels[i]
        # 初始化每个残差模块，堆叠成 num_levels 层
        layers += [TemporalBlock(in_channels, out_channels, kernel_size,
        stride=1, dilation=dilation_size, padding=(kernel_size - 1) * dilation_size, dropout=dropout)]
    # 将所有残差模块堆叠起来组成一个深度卷积网络
    self.network = nn.Sequential(*layers)

    def forward(self, x):
        net = torch.nn.DataParallel(self.network, device_ids=[0,1,2,3])
        return net(x)
```