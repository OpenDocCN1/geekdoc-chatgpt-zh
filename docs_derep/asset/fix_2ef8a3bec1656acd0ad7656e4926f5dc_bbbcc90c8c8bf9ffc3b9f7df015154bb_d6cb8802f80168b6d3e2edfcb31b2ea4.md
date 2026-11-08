# 本地扩写 LLaMA-Factory 实操

推理时把已经编码好的输入序列交给模型的 generate 方法，令牌上限设为 512。得到完整 id 后以原输入长度为基准切片，只保留模型新补全的部分，再统一解码并过滤特殊符号，首条解码结果即视为最终回复。

训练数据统一采用 instruction、input、output 三字段的格式，input 多数情况为空。下面两段记录展示了问答类条目的组织方式。

[PRE0]

{

    "instruction": "保持健康的三个提示。",

    "input": "",

    "output": "以下是保持健康的三个提示：\n\n1. 保持身体活动。每天做适当的身体运动，如散步、跑步或游泳，能促进心血管健康，增强肌肉力量，并有助于减少体重。\n\n2. 均衡饮食。每天食用新鲜的蔬菜、水果、全谷物和脂肪含量低的蛋白质食物，避免高糖、高脂肪和加工食品，以保持健康的饮食习惯。\n\n3. 睡眠充足。睡眠对人体健康至关重要，成年人每天应保证 7-8 小时的睡眠。良好的睡眠有助于减轻压力，促进身体恢复，并提高注意力和记忆力。"

},

{

    "instruction": "三原色是什么？",

    "input": "",

    "output": "三原色通常指的是红色、绿色和蓝色（RGB），它们是通过加色混合原理创建色彩的三种基础颜色。在以发光为基础的显示设备中（如电视、计算机显示器、智能手机和平板电脑显示屏），三原色可混合产生大量色彩。其中红色和绿色可以混合生成黄色，红色和蓝色可以混合生成品红色，蓝色和绿色可以混合生成青色。当红色、绿色和蓝色按相等比例混合时，可以产生白色或灰色。\n\n此外，在印刷和绘画中，三原色指的是以颜料为基础的红、黄和蓝颜色（RYB），这三种颜色用通过减色混合原理来创建色彩。不过，三原色的具体定义并不唯一，不同的颜色系统可能会采用不同的三原色。"

}

在本地搭建工作流大致分为准备环境、拉取仓库、安装依赖、制作数据。

[PRE1]
apt install git
[PRE2]
cd

git clone https://github.com/hiyouga/LLaMA-Factory.git
[PRE3]
python -m pip install --upgrade pip
[PRE4]
pip install -r requirements.txt --index-url https://mirrors.huaweicloud.com/repository/pypi/simple

需要中文自我介绍风格的样本时，可用 Python 把问题列表和回答列表随机配对，生产一百条记录并写成 JSON，后续直接用于训练。

[PRE5]
import json
import random
# 定义问题和回答
questions = [
    "请你告诉我你是谁？", "你能介绍一下自己吗？", "你是谁？", "请介绍一下你自己。",
    "能告诉我你的身份吗？", "你的职业是什么？", "你有什么特长？", "你来自哪里？",
    "你的工作是什么？", "你的兴趣爱好是什么？"
]
responses = [
    "我是小林，一个致力于帮助人们解决问题的人工智能。",
    "我叫小林，是一名人工智能助手，专门处理数据分析任务。",
    "我是小林，一个从事教育支持的智能助理。",
    "名字叫小林，我是一款专为提供即时信息而设计的智能系统。",
    "我是小林，我能够协助处理各种复杂的数据问题。",
    "我叫小林，是一个人工智能，帮助人们进行科研和数据分析。",
    "我是小林，我的工作是通过机器学习和人工智能技术来支持决策制定。",
    "我是小林，一个设计用来进行语言翻译和沟通辅助的人工智能。",
    "作为小林，我提供基于云的数据处理服务。",
    "我是小林，主要负责数据挖掘和洞察发现。"
]
# 生成 100 条数据
extended_data_set = []
for _ in range(100):
    q = random.choice(questions)
    r = random.choice(responses)
    extended_data_set.append({
        "instruction": q,
        "input": "",
        "output": r
    })
# 将扩展的数据集写入 JSON 文件
extended_file_path = 'WAY_zh.json'
with open(extended_file_path, 'w', encoding='utf-8') as file:
    json.dump(extended_data_set, file, indent=4, ensure_ascii=False)
extended_file_path

生成好的文件拷贝到项目 data 目录，并在清单里注册后框架才能识别。

[PRE6]
cp WAY_zh.json ~/LLaMA-Factory/data
[PRE7]
cd ~/LLaMA-Factory/data

vim dataset_info.json
[PRE8]json
"WAY_zh": {
  "file_name": "WAY_zh.json"
}
[PRE9]
cd ..