# 本地跑 DeepSeek-R1 蒸馏版全流程

想在自己的机器上体验 DeepSeek-R1 的蒸馏版本，这篇笔记把从装包到首次推理验证的步骤整理成可直接复现的顺序，整体思路清晰，落地很快。

## 搭建运行环境
先把推理加速框架 Unsloth 部署好，建议用强制重装的写法把版本锁住，之后再补上实验记录工具 wandb 和模型拉取工具 modelscope。
pip install unsloth

pip install --force-reinstall --no-cache-dir --no-deps git+https://github.com/unslothai/unsloth.git

[PRE0]
pip install wandb

[PRE1]
pip install modelscope

[PRE2]

## 获取权重
为两个蒸馏分支各自建一个本地目录，再通过 ModelScope 把官方权重同步到本机。
mkdir ./DeepSeek-R1-Distill-Qwen-7B

[PRE3]
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Qwen-7B --local_dir ./DeepSeek-R1-Distill-Qwen-7B

[PRE4]
mkdir ./DeepSeek-R1-Distill-Llama-8B

[PRE5]
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Llama-8B --local_dir ./DeepSeek-R1-Distill-Llama-8B

[PRE6]

## 初始化模型
用 Unsloth 的 FastLanguageModel.from_pretrained 读取刚才下载的 Llama-8B 蒸馏权重，max_seq_length 设为 2048，dtype 保持默认，load_in_4bit 暂时关闭，显存紧张时可以打开 4bit 量化。
from unsloth import FastLanguageModel

max_seq_length = 2048

dtype = None

load_in_4bit = False  # 若显存不足，可以将此设置为 True，运行 4bit 量化版

model, tokenizer = FastLanguageModel.from_pretrained(

    model_name = "./DeepSeek-R1-Distill-Llama-8B",

    max_seq_length = max_seq_length,

    dtype = dtype,

    load_in_4bit = load_in_4bit,

)

[PRE7]
==((====))==  Unsloth 2025.1.8: Fast Llama patching. Transformers: 4.48.2.

\    /|    GPU: NVIDIA H800 PCIe. Max memory: 79.205 GB. Platform: Linux.

O^O/ \_/ \    Torch: 2.5.1+cu124. CUDA: 9.0. CUDA Toolkit: 12.4. Triton: 3.1.0

\        /    Bfloat16 = TRUE. FA [Xformers = 0.0.29.post1. FA2 = False]

 "-____-"     Free Apache license: http://github.com/unslothai/unsloth

Unsloth: Fast downloading is enabled - ignore downloading bars which are red colored!

Loading checkpoint shards:   0%|          | 0/2 [00:00<?, ?it/s]

./DeepSeek-R1-Distill-Llama-8B does not have a padding token! Will use pad_token = <|finetune_right_pad_id|>.

[PRE8]

## 结构检查
直接输出模型对象，确认加载成功且结构符合预期。
model

[PRE9]
LlamaForCausalLM(
  (model): LlamaModel(
    (embed_tokens): Embedding(128256, 4096, padding_idx=128004)
    (layers): ModuleList(
      (0-31): 32 x LlamaDecoderLayer(
        (self_attn): LlamaAttention(
          (q_proj): Linear(in_features=4096, out_features=4096, bias=False)
          (k_proj): Linear(in_features=4096, out_features=1024, bias=False)
          (v_proj): Linear(in_features=4096, out_features=1024, bias=False)
          (o_proj): Linear(in_features=4096, out_features=4096, bias=False)
          (rotary_emb): LlamaRotaryEmbedding()
        )
        (mlp): LlamaMLP(
          (gate_proj): Linear(in_features=4096, out_features=14336, bias=False)
          (up_proj): Linear(in_features=4096, out_features=14336, bias=False)
          (down_proj): Linear(in_features=14336, out_features=4096, bias=False)
          (act_fn): SiLU()
        )
        (input_layernorm): LlamaRMSNorm((4096,), eps=1e-05)
        (post_attention_layernorm): LlamaRMSNorm((4096,), eps=1e-05)
      )
    )
    (norm): LlamaRMSNorm((4096,), eps=1e-05)
    (rotary_emb): LlamaRotaryEmbedding()
  )
  (lm_head): Linear(in_features=4096, out_features=128256, bias=False)
)

[PRE10]

## 切换推理模式
调用 for_inference 把模型调整为推理友好的状态，整体结构保持不变。
FastLanguageModel.for_inference(model)

[PRE11]
LlamaForCausalLM(
  (model): LlamaModel(
    (embed_tokens): Embedding(128256, 4096, padding_idx=128004)
    (layers): ModuleList(
      (0-31): 32 x LlamaDecoderLayer(
        (self_attn): LlamaAttention(
          (q_proj): Linear(in_features=4096, out_features=4096, bias=False)
          (k_proj): Linear(in_features=4096, out_features=1024, bias=False)
          (v_proj): Linear(in_features=4096, out_features=1024, bias=False)
          (o_proj): Linear(in_features=4096, out_features=4096, bias=False)
          (rotary_emb): LlamaRotaryEmbedding()
        )
        (mlp): LlamaMLP(
          (gate_proj): Linear(in_features=4096, out_features=14336, bias=False)
          (up_proj): Linear(in_features=4096, out_features=14336, bias=False)
          (down_proj): Linear(in_features=14336, out_features=4096, bias=False)
          (act_fn): SiLU()
        )
        (input_layernorm): LlamaRMSNorm((4096,), eps=1e-05)
        (post_attention_layernorm): LlamaRMSNorm((4096,), eps=1e-05)
      )
    )
    (norm): LlamaRMSNorm((4096,), eps=1e-05)
    (rotary_emb): LlamaRotaryEmbedding()
  )
  (lm_head): Linear(in_features=4096, out_features=128256, bias=False)
)

[PRE12]

## Tokenizer 快速验证
用一段中文数学提问测试分词器是否能正常映射，并把结果搬到 GPU。
question = "请问如何证明根号 2 是无理数？"

inputs = tokenizer([question], return_tensors="pt").to("cuda")

inputs

[PRE13]
{'input_ids': tensor([[128000,  15225,  57107, 109425, 125544, 102831,  18476,     17,  21043,

         43292,  22649,   9039,  11571]], device='cuda:0'), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1,

        1, 1, 1]], device='cuda:0')}

[PRE14]