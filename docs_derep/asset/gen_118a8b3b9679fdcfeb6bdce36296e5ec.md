[PRE0]

# 5分钟搭好 dv3 + Python 3.11 环境

conda create --name dv3 python=3.11

conda init

# 刷新 shell 配置使 Conda 生效

source ~/.bashrc

# 激活 dv3 作为当前会话环境

conda activate dv3

[PRE1]

conda install jupyterlab

conda install ipykernel

python -m ipykernel install --user --name dv3 --display-name "Python (dv3)"

[PRE2]

git clone https://github.com/deepseek-ai/DeepSeek-V3.git

[PRE3]

cd DeepSeek-V3/inference

pip install -r requirements.txt

[PRE4]

pip install modelscope

[PRE5]

mkdir ./deepseek

modelscope download --model OPEA/DeepSeek-V3-int4-sym-gptq-inc --local_dir ./deepseek

[PRE6]python
import torch

from modelscope import AutoTokenizer, AutoModelForCausalLM, GenerationConfig

# 模型本地目录，按实际下载位置修改
model_name = "./deepseek"

# 初始化分词器
tokenizer = AutoTokenizer.from_pretrained(model_name)

# 以 bfloat16 加载因果语言模型，自动映射到可用设备
model = AutoModelForCausalLM.from_pretrained(model_name, torch_dtype=torch.bfloat16, device_map="auto")

# 读取生成策略配置
model.generation_config = GenerationConfig.from_pretrained(model_name)

model.generation_config.pad_token_id = model.generation_config.eos_token_id
[PRE7]python
import torch

from modelscope import AutoTokenizer, AutoModelForCausalLM, GenerationConfig

model_name = "./deepseek-v3"

tokenizer = AutoTokenizer.from_pretrained(model_name)