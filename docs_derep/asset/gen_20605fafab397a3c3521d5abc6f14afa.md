## Net 类实现全拆解

Net 是整个推理框架的中枢，代码体量很大，下面按功能块来拆。

构造函数和析构函数落在第 8～16 行，析构时核心动作就是调用 clear 来回收资源。

第 18～41 行是第一版 register_custom_layer，用来给网络临时挂载自定义层。实现思路很直白，先用 layer_to_index 校验用户层名是否与内置层重名，重名直接返回 -1 报错；再用 custom_layer_to_index 在自定义数组里定位索引。若索引存在就原地替换，若不存在就 push_back 追加到末尾。

第 43～56 行是第二版 register_custom_layer，和上一版签名不同，这次是直接按指定索引写入自定义层数组。层编号通过 index 与 LayerType::CustomBit 做位运算生成，确保自定义层编号前缀与内置层不冲突，冲突则返回 -1。索引越界时先 resize 拉长数组，越界内就直接写入；若写入位置已有内容会发出警告提示覆盖。

第 67～165 行是 load_params，负责把网络层的超参数从二进制里读出来。第 69～74 行先做指针对齐检查，用指针地址与 0x3 做位与判断是否 4 字节对齐，不对齐会报错返回，影响计算效率。

第 78～85 行读取前 4 字节魔数，合法模型应等于预定义的 7767517， mismatch 就判定文件类型错误。

第 87～94 行读取层总数和数据块总数，并据此初始化网络的层数组与数据块数组。

第 96～159 行进入逐层解析。每个层在文件里先存 4 字节类型编码，再 4 字节输入块数，再 4 字节输出块数。随后创建 ParamDict 接收本层的超参数。层对象先尝试 create_layer，失败则尝试 create_custom_layer，两个都失败就报错返回。

第 121～132 行初始化输入数据块，先申请数组，再循环读取每个输入块的 4 字节索引，把网络数据块的索引映射到层输入。

第 134～144 行同理处理输出数据块。

第 145～159 行调用 ParamDict::load_param 读取剩余超参数，再调用层的 load_param 完成初始化。

第 167～197 行是 load_model，先判断层数组是否为空判别网络结构是否已初始化，空则报错。再做一次指针 4 字节对齐检查。然后用 ModelBinFromMemory 把内存数据封装成二进制模型对象，遍历所有层逐个调用 load_model 读取权重，最后返回实际读取字节数 mem - _mem。

第 199～207 行是 clear，先清理所有数据块，再遍历层数组 delete 层对象，最后清空层数组。

第 209～212 行是 create_extractor，用于生成求解用的 Extractor。

第 214～276 行是一批层与数据块的辅助函数，逻辑比较简单就不展开。

第 278～340 行是 forward_layer，前向计算的核心。参数 layer_index 指层编号，blob_mats 是输入数据。第 280 行取到对应层。第 282 行判断是否单输入单输出，是则走 283～304 行，否则走 306～337 行。

单输入输出时，第 285～286 行直接取第一个输入输出块。第 288～293 行若输入块维度为 0 说明未初始化，通过 producer 找到生成该块的上游层递归调用 forward_layer。第 295～303 行若维度已存在，直接调用层的 forward 执行，前向结果写回对应块。

多输入输出时，第 308～322 行先按输入维度调整输入块数组大小，遍历每个输入块获取网络全局索引，若块维度为 0 就递归触发上游计算，然后把全局块拷贝到临时数组。第 324～336 行调用层的 forward，得到输出块后写入网络数据块。

凭借 producer 递归和已计算块的跳过机制，只需指定最后一层即可自动触发依赖链，既能算单层也能算全网，灵活性很高。

### Extractor 类工作机制

Extractor 本质是 Net 的封装，负责输入初始化并提供 extract 接口，内部转调 Net 的 forward_layer。

第 342 行是构造函数，net 是计算网络指针，blob_count 是结果块尺寸，内部会 resize blob_mats。

第 348～356 行是 input 成员函数，按索引指定输入。blob_index 指定矩阵位置，in 是输入矩阵，直接赋值给内部矩阵。

第 358～374 行是按索引的 extract。首先检查索引是否越界，越界返回 -1。正常时通过数据块的 producer 取到生成层的索引，调用 forward_layer 递归算到目标层，结果写入 blob_mats，最后把结果存到 feat 返回。

第 376 行是按名称的 input，先用 find_blob_index_by_name 把名称映射成索引，再赋值。

第 358～404 行是按名称的 extract，先根据名称找索引，不存在返回 -1，存在就通过 producer 定位层，调用 forward_layer 递归计算，结果同样写入 blob_mats 并返回 feat。整体思路与索引版一致，只是入口换成了名称。

### 4.3.3 全连接层落地

CNN 里主要的全连接、卷积、池化都继承自 Layer，这里先看全连接，即 InnerProductLayer。

先写 innerproduct.h 定义 InnerProduct 类，如代码清单 4-5。

代码清单 4-5 innerproduct.h

[PRE0]

类继承自 Layer，声明了构造析构、load_param、load_model、forward 等必备接口。

第 18～25 行定义成员：num_output 输出块数，bias_term 是否含偏置，weight_data_size 权重数据量，weight_data 权重矩阵，bias_data 偏置矩阵。

再写 innerproduct.cpp 实现，如代码清单 4-6。

代码清单 4-6 innerproduct.cpp

[PRE1]

第 4 行用 DEFINE_LAYER_CREATOR 注册层，InnerProduct 对应注册名。

第 15～22 行 load_param 从 ParamDict 取 num_output、bias_term、weight_data_size。

第 24～35 行 load_model 先按权重数据量初始化 weight_data，再根据 bias_term 判断是否从 ModelBin 读取偏置并初始化 bias_data。

第 40～74 行 forward：从输入块取出宽 w、高 h、通道 channels、元素尺寸 elemsize，注意为提升效率每行做了对齐，实际每通道字节数可能大于理论值。

随后按输出数和参数数创建输出块，用三重循环遍历块、通道、行，计算 bottom × weight + bias 的线性结果并写入输出块，完成全连接计算。

### 4.3.4 卷积层落地

接下来实现卷积层，类名为 ConvolutionLayer。

先写 convolution.h 定义类，如代码清单 4-7。

代码清单 4-7 convolution.h

# 卷积层实现

## 头文件 `layer_convolution.h`