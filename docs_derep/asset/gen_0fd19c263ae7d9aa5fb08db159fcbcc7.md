需要先明确一点，GPT家族各成员在文本生成的底层逻辑上是共通的，具体换用哪款模型，读者自行替换即可。

回溯第14章介绍的GPT-2训练思路，针对评论任务做微调后，只要给一个很短的提示词，模型就能顺着往下写，如图15-10所示。

> > 酒店名字是起的。房间布置很欧洲的风格，周围环

> 酒店先生开通商网店，越低越省钱越靠谱转发公众

> 酒店具体是房客们集体聚餐之用，自驾前往。打电

> 酒店称是住宿部的第三开间房型，设施挺齐全的，

图15-10 使用文本提示继续生成一段文字

可这份续写的只是普通描述，当前的基础GPT并不具备情绪判别能力，上面的例子都偏离了正向表达。想让模型学会按要求产出正面情感的评论，就得借助RLHF来对模型进行进化。

具体做法是：每次模型依据提示词生成结果后，都要对该结果打分作为反馈，生成流程见图15-11。

[PRE0]

图15-11 生成过程

为了简化，这里用评价均值作为反馈分数，并把训练过程中的评价结果画成曲线，评分走势如图15-12。

[PRE1]

图15-12 评分结果

从图15-12可以看出，随着训练推进，正向评价分持续上升，说明训练方向是正确的。

### 15.3.2 ChatGPT评分模块简介

前文已介绍ChatGPT的基本用法，本节说明实验中采用的评分组件。这里选用Huggingface提供的中文二分类情感分类模型，基于网评语料训练，能对句子评论的情感倾向进行判断，如图15-13所示。

[PRE2]

图15-13 对句子的评论情感进行判别

输入一段评论后，下方会返回评分，其中positive代表正向得分，negative代表负向得分。

由于该模型来源于Huggingface，下面采用本地部署的方式在本地机器上运行，代码如下：

[PRE3]

输出结果如图15-14所示。

[PRE4]

从结果可见，此时只展示了正向情感的评分，score即为具体数值。

额外提示一下，反馈函数的设计并非唯一，条件允许的话也可以直接调用OpenAI提供的ChatGPT接口，通过拼接合适的提示词获得更精确的评判。

### 15.3.3 带有评分函数的ChatGPT模型的构建

回到GPT模型，结合第14章可再训练的GPT实现，其forward部分原本仅输出预测logits。按照前文思路，还需要一个评分网络来接收对模型输出的评价反馈。

这里可以用一个全连接层来承担评分功能，代码如下：

[PRE5]

此时模型输出会得到反馈，进而调整整体生成，而输入的embedding正是GPT-2主体计算得到的。完整的GPT-2实现如下：

[PRE6]def generate(self, continue_buildingsample_num, prompt_token=None, temperature=1., top_p=0.95):

    """

    :param continue_buildingsample_num: 这个参数指的是在输入的prompt_token后再输出多少个字符

    :param prompt_token: 这是需要转换成Token的内容，这里需要输入一个list

    :param temperature:

    :param top_k:

    :return: 输出一个Token序列

    """

    prompt_token_new = list(prompt_token)  #使用这行代码，在生成的Token里面有102个分隔符

    for i in range(continue_buildingsample_num):

        _token_inp = torch.tensor([prompt_token_new]).to("cuda")

        if self.use_rlhf:

            result, _ = self.forward(_token_inp)

        else:

            result = self.forward(_token_inp)

        logits = result[:, -1, :]

        probs = torch.softmax(logits / temperature, dim=-1)

        next_token = self.sample_top_p(probs, top_p)  # 预设的top_p = 0.95

        next_token = next_token.reshape(-1)

        prompt_token_new.append(next_token.item())

    return prompt_token_new

def sample_top_p(self, probs, p):

    probs_sort, probs_idx = torch.sort(probs, dim=-1, descending=True)

    probs_sum = torch.cumsum(probs_sort, dim=-1)

    mask = probs_sum - probs_sort > p

    probs_sort[mask] = 0.0

    probs_sort.div_(probs_sort.sum(dim=-1, keepdim=True))

    next_token = torch.multinomial(probs_sort, num_samples=1)

    next_token = torch.gather(probs_idx, -1, next_token)

    return next_token

### 15.3.4 RLHF中的PPO算法——KL散度

本节介绍训练时使用的PPO2。相对第13章的PPO，这里还涉及actor与reward两部分。因此实践中采用两个相同的GPT-2模型分别承担策略执行与策略更新，代码如下：

[PRE7]

上述为已定义好的GPT-2模型。为便于理解，均使用带有评分函数的GPT-2版本。

下面对整体PPO模型做说明，这里自定义了PPOTrainer类来统一操作，简化代码如下：

[PRE8]

接下来讲解相对简单的散度计算模块AdaptiveKLController，即PPO2算法。无论是经典PPO还是自定义PPO，KL散度的计算都至关重要，它用于衡量两个分布之间的距离。

本实现采用AdaptiveKLController，在梯度计算中加入clip操作，形成PPO2。

其原理是：当优势函数为正，需要提高当前动作的选中概率时，会限制当前状态下新旧分布比值的最大值，超过阈值则停止策略更新；当优势函数为负，需要降低选中概率时，则限制比值的最小值，超过阈值同样停止更新。这样可在参数更新的同时保证新旧分布距离保持在设定范围内，如图15-15所示。

[PRE9]

图15-15 PPO2算法演示

该方法能通过动态调整KL约束项的惩罚系数，在保证训练稳定的前提下让策略分布产生足够变化，即更新幅度要小以保证稳定性，又要足够大以改变策略。

如图15-16所示的策略，对更新前后的KL距离设定一个可调超参数target作为目标约束，直接限制KL散度的最大变化。

与直接约束KL散度不同，该做法对优势函数进行了裁剪。其中当重要性采样系数超出固定区间，一般设为[-0.2, 0.2]，见下方代码时，该次更新会被丢弃，裁剪后的损失与参数无关，不产生梯度。本质上是忽略新旧策略差异过大带来的优势值，以保证训练稳定和梯度更新的单调性。

[PRE10]

图15-16 对KL散度有约束的PPO优化策略

[PRE11]

### 15.3.5 RLHF中的PPO算法——损失函数

RLHF的目标是最大化生成模型获得的奖励，同时希望经过PPO反馈后的输出不要偏离原始模型太远。因此需要借助不同的损失函数来约束反馈结果。

这项工作由PPO算法中的损失函数完成。正如前文所述，PPO的损失是通过比较当前策略与旧策略的差异来计算的，以确保更新幅度可控，避免策略迭代中的过拟合问题。

本节损失函数的具体实现如下：