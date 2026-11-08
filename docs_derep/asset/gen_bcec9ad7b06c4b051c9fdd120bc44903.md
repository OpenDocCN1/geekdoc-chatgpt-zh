[PRE0]

# 新建 dv3 环境，Python 3.11

先建一个干净的 conda 隔离环境，Python 版本固定为 3.11。
conda create --name dv3 python=3.11

对 conda 做一次初始化，方便后续使用。
conda init

# 刷新 shell 配置

让刚完成的初始化生效。
source ~/.bashrc

# 激活环境

把当前终端切到 dv3，让它成为活动的 Python 运行环境。
conda activate dv3

[PRE1]

装上交互式开发工具，先 JupyterLab。
conda install jupyterlab

再装内核包，方便在 Jupyter 里调用。
conda install ipykernel

把当前环境注册为 Jupyter 可用内核，名字显示为 Python (dv3)。
python -m ipykernel install --user --name dv3 --display-name "Python (dv3)"

[PRE2]

拉取官方推理仓库源码。
git clone https://github.com/deepseek-ai/DeepSeek-V3.git

[PRE3]

进入推理目录并安装依赖。
cd DeepSeek-V3/inference
pip install -r requirements.txt

[PRE4]

安装模型下载与加载工具。
pip install modelscope

[PRE5]

准备本地存放目录。
mkdir ./deepseek

使用 modelscope 把量化模型拉到本地。
modelscope download --model OPEA/DeepSeek-V3-int4-sym-gptq-inc --local_dir ./deepseek

[PRE6]python
import torch

from modelscope import AutoTokenizer, AutoModelForCausalLM, GenerationConfig

# 指定模型存储路径（修改为上一步中模型权重下载到的本地路径）

model_name = "./deepseek"

# 加载对应的分词器（Tokenizer）

tokenizer = AutoTokenizer.from_pretrained(model_name)

# 加载 CausalLM（因果语言模型），设置数据类型为 bfloat16，并自动映射到可用设备（如 GPU）

model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype=torch.bfloat16, device_map="auto")

# 加载生成配置（GenerationConfig），用于调整模型的生成策略

model.generation_config = GenerationConfig.from_pretrained(model_name)

model.generation_config.pad_token_id = model.generation_config.eos_token_id
[PRE7]python
import torch

from modelscope import AutoTokenizer, AutoModelForCausalLM, GenerationConfig

model_name = "./deepseek-v3"

tokenizer = AutoTokenizer.from_pretrained(model_name)

model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype=torch.float16, device_map="auto")

model.generation_config = GenerationConfig.from_pretrained(model_name)

model.generation_config.pad_token_id = model.generation_config.eos_token_id
[PRE8]python
# 构造对话消息列表
messages = [