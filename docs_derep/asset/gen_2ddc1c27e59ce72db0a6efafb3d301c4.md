# EdgeRec与DCCL：端云协同下的实时推荐新范式

## RS模块：云端召回与算力分担

RS（Recommender System）模块托管于云端，在EdgeRec架构中扮演召回组件的角色，专门响应CN模块的调用，将候选集下发至移动终端。除输出候选物品外，RS还能为MS模块分摊模型存储与计算的开销。

### (4) OT离线训练模块

OT（Offline Training）模块负责汇聚MS上报的日志数据，并将其加工转化为可训练样本。模型训练完成后，系统提供拆解能力：将大规模稀疏参数剥离单独存储，便于后续云端部署；其余参数则下发至移动端，并按需进一步切分。

## 算法设计：实时重排核心逻辑

EdgeRec的构建初衷，是为信息流场景注入实时重排能力。当CN模块触发重排流程时，模型服务层首先完成特征工程，继而基于神经网络 $ \phi(\mathbf{x}_i, \mathbf{s}, \mathbf{C}) $ 对候选物品进行复核打分。其中，$ x_i $ 表征物品特征，$ s $ 代表局部排序上下文，$ C $ 对应实时用户行为上下文。

### (1) 特征工程：全面捕捉用户意图

主流个性化推荐系统虽重视用户行为建模，却多聚焦于正向反馈（如点击、购买），鲜少纳入间接负向反馈。正向信号虽明确且低噪，但在信息流场景下，负向反馈同等关键——例如同类物品连续曝光却无正向交互，继续推荐将导致点击率显著下滑。

此外，现有方案多局限于品类、品牌等基础属性，忽视了用户对物品的多元“动作”。点击后加购、收藏，乃至未点击但长时停留，均能折射偏好程度。EdgeRec的特征体系（如图7-21所示）致力于全方位覆盖正向、负向及多元动作行为，具体设计如下：

- **主页曝光动作特征（Item Exposure User Action）**：$ e_1、e_2 $ 记录曝光时长与次数；$ e_3 \sim e_5 $ 统计滑动速度、占比与次数；$ e_6、e_7 $ 捕捉删除原因及曝光衰减。拼接向量表示为：

[PRE0]

- **详情页浏览动作特征（Item Page-View User Action）**：$ d_1 $ 为停留总时长；$ d_2 \sim d_{11} $ 标识各功能区点击情况（加购、购买、收藏、评价、选SKU、客服咨询、详情/店铺/推荐区块浏览）；$ d_{12} $ 为浏览衰减。拼接向量表示为：

[PRE1]

| 变量 | 特征字段 | 特征描述 | 类型 |
|---|---|---|---|
| $e_1$ | exposure_duration | 物品总曝光时长 | Bucketize |
| $e_2$ | exposure_count | 物品总曝光次数 | Bucketize |
| $e_3$ | scroll_speed | 物品滑屏速度 | Bucketize |
| $e_4$ | scroll_duration | 物品滑屏占用曝光总时长 | Bucketize |
| $e_5$ | scroll_count | 物品滑屏行为总次数 | Bucketize |
| $e_6$ | delete_reason | 物品是否被长按后删除 | One-hot |
| $e_7$ | expose_decay | 物品最后一次曝光距今时长 | Bucketize |
| $d_1$ | ipv_duration | 物品详情浏览总时长 | Bucketize |
| $d_2$ | cart | 加购物车 | Binarize |
| $d_3$ | buy | 立即购买 | Binarize |
| $d_4$ | favorite | 收藏 | Binarize |
| $d_5$ | comment | 查看评价 | Binarize |
| $d_6$ | select_SKU | 选择物品sku | Binarize |
| $d_7$ | WDJ | 咨询客服 | Binarize |
| $d_8$ | wangwang | 点击客服按钮 | Binarize |
| $d_9$ | detail | 浏览物品详情区块 | Binarize |
| $d_{10}$ | shop | 浏览物品所在店铺 | Binarize |
| $d_{11}$ | recommendation | 浏览个性化推荐区块 | Binarize |
| $d_{12}$ | ipv_decay | 物品最后一次浏览距今时长 | Bucketize |
| $p_1$ | category | 物品所属品类 | Embedding |
| $p_2$ | brand | 物品所属品牌 | Embedding |
| $p_3$ | gender | 物品适用性别 | Embedding |
| $p_4$ | price_level | 物品所属价格段 | Embedding |
| $p_5$ | age_level | 物品适用年龄段 | Embedding |
| $p_6$ | bc_type | 物品bc类型 | Embedding |
| $p_7$ | scores | 物品特征分数（如ctr/cvr分数等） | Raw |

图7-21 EdgeRec的特征工程总结

- **物品基础特征**：$ p_1 \sim p_7 $ 包含品类、品牌、性别、价格段、年龄段、BC类型及CTR/CVR预估分，拼接为 $ \mathbf{p}^i $。

图7-22 展示了EdgeRec中异构行为序列建模与上下文感知重排的行为注意力网络架构。

### (2) HUBSM：异构用户行为序列建模

HUBSM（Heterogeneous User Behavior Sequence Modeling）旨在建模实时用户上下文 $ C $。异构性体现在两方面：主页与详情页行为空间差异巨大、稀疏度迥异；用户“动作”与物品属性属于不同特征空间。HUBSM分别建模两端序列，场景内再区分“动作序列”与“物品序列”编码后融合，序列编码统一采用GRU：

$(\hat{X}, s) = \text{GRU}(X)$

[PRE2]

其中 $ X = \{ x^i \}_{1 \leqslant i \leqslant n} $ 为输入序列，$ s $ 为GRU最终隐状态。融合采用简单拼接：$ Z = \text{CONCAT}(X, Y) $。主页与详情页编码流程具体为：

$ (\hat{A}_{\text{IE}} = \{ \hat{a}'_{\text{IE}} \}_{1 \leqslant i \leqslant m}) = \text{GRU}(A_{\text{IE}}) $  
$ (\hat{P}_{\text{IE}} = \{ \hat{p}'_{\text{IE}} \}_{1 \leqslant i \leqslant m}) = \text{GRU}(P_{\text{IE}}) $  
$ \hat{B}_{\text{IE}} = \{ \hat{b}'_{\text{IE}} \}_{1 \leqslant i \leqslant m} = \text{CONCAT}(\hat{A}_{\text{IE}}, \hat{P}_{\text{IE}}) $  

$ (\hat{A}_{\text{IPV}} = \{ \hat{a}'_{\text{IPV}} \}_{1 \leqslant i \leqslant n}) = \text{GRU}(A_{\text{IPV}}) $  
$ (\hat{P}_{\text{IPV}} = \{ \hat{p}'_{\text{IPV}} \}_{1 \leqslant i \leqslant n}) = \text{GRU}(P_{\text{IPV}}) $  
$ \hat{B}_{\text{IPV}} = \{ \hat{b}'_{\text{IPV}} \}_{1 \leqslant i \leqslant n} = \text{CONCAT}(\hat{A}_{\text{IPV}}, \hat{P}_{\text{IPV}}) $

$m, n$ 为序列最大长度，不足补零。

候选物品序列同理用GRU建模局部上下文 $ s $。云端下发候选序列 $ P_{CND} $，GRU输出为：

$(\hat{P}_{CND} = \{ \hat{p}_{CND}^i \}_{1 \le i \le k}, s) = GRU(P_{CND}) \quad (7-95)$

参考DIN思路，EdgeRec引入注意力机制融合上下文。以候选物品编码为Query，历史行为编码为Key/Value。主页侧注意力输出：

$att_{IE}^j = softmax(v_1^T tanh(W_1 \hat{p}_{CND}^i + W_2 \hat{p}_{IE}^j)), 1 \le j \le m \quad (7-96)$
$c_{IE}^i = \sum_{j=1}^m att_{IE}^j \hat{b}_{IE}^j \quad (7-97)$

详情页侧注意力输出：

$att_{IPV}^j = softmax(v_2^T tanh(W_3 \hat{p}_{CND}^i + W_4 \hat{p}_{IPV}^j)), 1 \le j \le n \quad (7-98)$
$c_{IPV}^i = \sum_{j=1}^n att_{IPV}^j \hat{b}_{IPV}^j \quad (7-99)$

$W_1 \sim W_4$ 为可训练参数。最终将 $ c_{IE}^i、c_{IPV}^i、s $ 输入多层MLP，基于交叉熵损失优化。

## 实验效果：双赢的业务与工程收益

实验验证了EdgeRec的显著价值：

1.  **业务指标向好**：端侧重排带来显著正向收益，特征工程与HUBSM模块有效性获证。
2.  **工程极致优化**：用户行为采集延迟从分钟级（1min）骤降至300ms以内；系统响应耗时从1s压缩至100ms内；单位时间内用户反馈交互次数从3次跃升至15次（详见表7-2）。

表7-2 端重排引入前后的系统响应对比

| 指标 | 引入 EdgeRec 后 | 引入 EdgeRec 前 |
|---|---|---|
| 用户延迟时间 | <=300ms | <=1min |
| 系统响应时间 | <=100ms | <=1s |
| 系统反馈次数 | 15 | 3 |

EdgeRec作为端侧重排的里程碑探索，在效果与效率维度均实现突破，架构设计通用性强，极具参考价值。

## 7.4.2 DCCL：端云协同学习，成就“千人千模”

DCCL（Device-Cloud Collaborative Learning for Recommendation）直指痛点：纯云训模型追求全局最优，参数有偏且易忽略长尾；纯端训虽捕捉实时信号强，但数据稀疏易陷局部最伏。DCCL提出“端云协同训练”，融合双方优势，整体提升推荐质量。

EdgeRec解决了“端侧推理重排”，DCCL更进一步攻克“端侧参与训练”，落地“千人千模”个性化愿景。

### (1) 端侧训练：MetaPatch轻量化方案

常规思路是云端下发预训练模型，端侧全量微调后回传参数。但大模型全量更新对移动端存储、带宽压力过大；仅微调部分层又效果受限。Patch Learning提供破局思路：在云端基座模型 $ f $ 中植入Patch实现个性化适配（图7-24）。第 $ m $ 设备第 1 层引入Patch后的输出定义为：

$ f_l^{(m)}(\cdot) = f_l(\cdot) + \underbrace{h_l^{(m)}(\cdot)}_{\text{Patch}} \circ f_l(\cdot) \quad (7-100) $

$ f_1(\cdot) $ 为基座模型输出，$ h_l^{(m)}(\cdot) $ 为可训练Patch函数，$ \circ $ 表示函数复合。此结构易退化为基座模型（如加门控机制）。Patch架构沿用文献[12]设计。

[PRE3]

引入Patch后，端侧仅更新Patch参数，资源消耗大幅下降。但端侧样本稀疏，难以充分训练新增参数。MetaPatch应运而生：引入元学习生成参数，通过共享全局偏置压缩参数空间，适配端侧个性化训练。假设Patch参数扁平化向量为：

$ \theta_l^{(m)} = \Theta_i \cdot \hat{\theta}^{(m)} \quad (7-101) $

$ \Theta_1 \in \mathbb{R}^{K_Y \times K} $ 为设备间共享的全局偏置（仅云端更新，端侧冻结）；$ \hat{\theta}^{(m)} \in \mathbb{R}^{K_P} $ 参与端侧训练。满足 $ \sum K_j \gg \hat{K} $ 时，端侧参数量大幅精简。MetaPatch流程见图7-24。端侧本地训练损失定义为：

[PRE4] (7-102)

$ \ell $ 为交叉熵损失，$ f^{(m)}(\cdot) $ 为最终预测输出。优化目标驱动 $ \hat{\theta}^{(m)} $ 更新，全局偏置 $ \Theta_1 $ 云端优化。

### (2) 云端训练：MoModistill模型蒸馏协同

传统云训遵循“model-over-data”范式，收集端侧数据更新参数：

[PRE5] (7-103)

$ W_f $ 为云端模型参数。此模式忽视端侧训练存在。DCCL创设“model-over-model”蒸馏机制（图7-25），以端侧个性化模型反哺指导云端中心模型训练：

[PRE6]

图7-25 增强云端模型的MoModistill方法（详见彩插）