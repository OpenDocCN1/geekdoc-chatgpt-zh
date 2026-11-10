[PRE0]

第 6 行定义了 Convolution 类，它继承自 Layer，并声明了构造、析构、load_param、load_model、forward 等层级必须的成员接口。

第 18 到 33 行集中列出了类的成员变量。num_output 表示要输出的特征块个数；kernel_w 与 kernel_h 分别是卷积核的宽度和高度；stride_w、stride_h 控制每次滑动的步距；pad_w、pad_h 用来描述输入矩阵在计算前需要做的延展填充大小；bias_term 标记是否启用偏置；weight_data_size 统计权重数据的总量；weight_data 保存权重矩阵；bias_data 保存偏置矩阵。

## 源文件 convolution.cpp 解析
接下来编写 convolution.cpp，完成 Convolution 类的具体实现，参考代码清单 4-8。

### 代码清单 4-8 convolution.cpp 实现
[PRE1]

第 5 行借助 DEFINE_LAYER_CREATOR 宏把自定义的层注册到系统中。

第 16 到 31 行实现了 load_param，从 ParamDict 对象里取出 num_output、kernel_w、kernel_h、dilation_w、dilation_h、stride_w、stride_h、pad_w、pad_h、bais_term 和 weight_data_size 等参数。

第 33 到 47 行实现了 load_model，先按 weight_data_size 把 weight_data 初始化，随后根据 bias_term 判断是否需要偏置，若需要则从 ModelBin 中读取偏置数据并初始化 bias_data。

第 49 到 181 行是 forward 函数，构成了卷积层的核心逻辑，代码量较大，下面分段说明。

第 51 行先判断输入维度以及卷积核宽高是否都为 1，这种情况可走优化路径。此时卷积等价于输入与核的全连接，于是新建一个 InnerProduct 全连接层，用本层的 num_output、bias_term、weight_data_size 为其设定对应参数，调用 load_param 完成参数注入，再把模型数据传过去调用 load_model，最后执行 forward 得到结果并 delete 释放对象。

第 81 行开始进入标准卷积的计算流程。

第 81 到 84 行从输入数据块解析出宽度 w、高度 h、通道数 channels 以及每个元素的字节数 elemsize。理论上每通道占用 w*h*elemsize，但为了行对齐，实际每通道的字节数通常会大于理论值。

第 86 到 87 行依据 dilation 参数算出卷积核的实际展开宽度 kernel_extent_w 和展开高度 kernel_extent_h。

第 90 到 97 行处理填充需求。当 pad_w 或 pad_h 大于 0 时，需要对原始矩阵做零填充，调用 copy_make_border 在左右各扩展 pad_w、上下各扩展 pad_h，并用 0 填充新增区域，后续运算使用的矩阵尺寸即为扩展后的尺寸。

第 99 到 108 行处理特殊标记。若 pad_w 和 pad_h 同时为 -233，表示计算时需要跳跃，此时要根据 stride_w 和 stride_h 推导出矩阵的扩展宽高。