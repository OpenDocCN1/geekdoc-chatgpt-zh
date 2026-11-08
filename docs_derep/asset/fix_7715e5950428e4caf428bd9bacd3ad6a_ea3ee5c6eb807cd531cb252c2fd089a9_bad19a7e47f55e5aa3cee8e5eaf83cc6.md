[PRE0]
完整实现可扫码获取。

# 测试集预处理与批次统计

测试标签先转为长整型并搬到目标设备，y_test 的维度与样本总数一致。

num_train_batches = x_train.shape[0] // batch_size    # 训练集拆分出的批次总数
num_train_examples = num_train_batches * batch_size    # 实际参与训练的样本总数
num_test_batches = x_test.shape[0] // batch_size      # 测试集拆分出的批次总数
num_test_examples = num_test_batches * batch_size      # 实际参与测试的样本总数

...

## 注意力机制

class Attention(torch.nn.Module):

    def __init__(self, fill: bool = True):

        super(Attention, self).__init__()

        self.scaling_coeff = 1.0 / torch.sqrt(torch.tensor(d_main, device=device))  # 点积缩放因子，用于稳定 softmax
        self.lower_tri = torch.tril(torch.ones(1, 1, device=device)) if fill else None  # 因果掩码用的下三角模板
        self.linear_k = torch.nn.Linear(in_features=d_main, out_features=d_main, bias=False)  # Key 投影层
        self.linear_q = torch.nn.Linear(in_features=d_main, out_features=d_main, bias=False)  # Query 投影层
        self.linear_v = torch.nn.Linear(in_features=d_main, out_features=d_main, bias=False)  # Value 投影层
        self.linear_attention = torch.nn.Linear(in_features=d_main, out_features=d_main, bias=False)  # 注意力输出的投影层

    def forward(self, x, x_lengths):

        examples, length, dim = x.shape  # 解析输入张量的三维大小
        k = self.linear_k(x)  # Key 投影后形状 [B, L, D]
        q = self.linear_q(x)  # Query 投影后形状 [B, L, D]
        v = self.linear_v(x)  # Value 投影后形状 [B, L, D]

        scaled_dotproduct = (q @ k.transpose(-1, -2)) * self.scaling_coeff  # 缩放点积，形状 [B, L, L]

        if x_lengths is not None:  # 当批次内序列长度不统一时
            numbers = torch.arange(length, device=device).unsqueeze(dim=0)  # 位置索引 [1, L]
            seq_mask_vector = x_lengths.unsqueeze(dim=-1) > numbers  # 有效位置掩码 [B, L]
            seq_mask_matrix = seq_mask_vector.unsqueeze(dim=-1) * seq_mask_vector.unsqueeze(dim=1)  # 二维掩码 [B, L, L]
            seq_mask_matrix = seq_mask_matrix * self.lower_tri[:length, :length] if self.lower_tri is not None else seq_mask_matrix  # 融合因果下三角并屏蔽填充
            masked_scaled_dotproduct = scaled_dotproduct.masked_fill(seq_mask_matrix == 0, float('-inf'))  # 屏蔽位置置 -inf
            weight = torch.nn.functional.softmax(masked_scaled_dotproduct, dim=-1)  # Softmax 后权重 [B, L, L]
            weight = weight.masked_fill(seq_mask_vector.unsqueeze(dim=-1) == 0, 0)  # 填充位置的权重置零
        else:  # 当批次内序列等长时
            masked_scaled_dotproduct = scaled_dotproduct.masked_fill(self.lower_tri[:length, :length] == 0, float('-inf')) if self.lower_tri is not None else scaled_dotproduct  # 因果掩码，非法位置置 -inf
            weight = torch.nn.functional.softmax(masked_scaled_dotproduct, dim=-1)  # Softmax 得到权重 [B, L, L]

        agg = weight @ v  # 加权求和，输出 [B, L, D]