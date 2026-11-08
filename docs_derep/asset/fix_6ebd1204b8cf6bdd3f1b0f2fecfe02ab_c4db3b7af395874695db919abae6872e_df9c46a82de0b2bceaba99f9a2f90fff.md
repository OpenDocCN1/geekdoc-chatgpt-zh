# 本地跑通 DeepSeek-R1 蒸馏版

想把 DeepSeek-R1 的蒸馏模型放到自己的电脑上用，下面是一套从环境准备到首次编码验证的完整实操路径，步骤清晰可以直接复现。

## 环境准备

先把加速库 Unsloth 装好，推荐用强制重装的方式锁定版本，再补上实验记录和模型下载工具。
pip install unsloth

pip install --force-reinstall --no-cache-dir --no-deps git+https://github.com/unslothai/unsloth.git

[PRE0]
pip install wandb

[PRE1]
pip install modelscope

[PRE2]

## 模型下载

为 Qwen-7B 和 Llama-8B 两个蒸馏分支分别建本地目录，通过 ModelScope 把权重拉到本机。
mkdir ./DeepSeek-R1-Distill-Qwen-7B

[PRE3]
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Qwen-7B --local_dir ./DeepSeek-R1-Distill-Qwen-7B

[PRE4]
mkdir ./DeepSeek-R1-Distill-Llama-8B

[PRE5]
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Llama-8B --local_dir ./DeepSeek-R1-Distill-Llama-8B

[PRE6]

## 载入模型

借助 Unsloth 的 FastLanguageModel.from_pretrained 加载本地的 Llama-8B 蒸馏权重，序列长度取 2048，显存紧张时可把 load_in_4bit 打开使用 4bit 量化。
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

## 架构预览

直接打印模型对象，确认加载成功且结构符合预期。
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

## 进入推理模式

调用 for_inference 切换到推理友好的配置，整体结构保持不变。
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

## 编码测试

用一段中文数学提问检验 tokenizer 是否能正常映射并放到 GPU。
question = "请问如何证明根号 2 是无理数？"

inputs = tokenizer([question], return_tensors="pt").to("cuda")

inputs

[PRE13]
{'input_ids': tensor([[128000,  15225,  57107, 109425, 125544, 102831,  18476,     17,  21043,

         43292,  22649,   9039,  11571]], device='cuda:0'), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1,

        1, 1, 1]], device='cuda:0')}

[PRE14]