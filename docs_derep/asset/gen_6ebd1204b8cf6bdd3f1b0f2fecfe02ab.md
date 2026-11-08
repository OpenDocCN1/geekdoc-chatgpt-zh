本地跑 DeepSeek-R1 Distill 实战全记录

想在自己机器上快速启动 DeepSeek-R1 的蒸馏版做推理？下面这套流程把环境准备、模型拉取、加载与首轮推理串起来，实测可用。

依赖先行
先把加速库 Unsloth 装上，推荐用强制重装方式保证版本新鲜，顺手把实验记录和 ModelScope 工具链也装好。
pip install unsloth

pip install --force-reinstall --no-cache-dir --no-deps git+https://github.com/unslothai/unsloth.git

[PRE0]
pip install wandb

[PRE1]
pip install modelscope

[PRE2]

模型目录与下载
为 Qwen 和 Llama 两个蒸馏分支分别建目录，再用 ModelScope 把权重拉到本地。
mkdir ./DeepSeek-R1-Distill-Qwen-7B

[PRE3]
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Qwen-7B --local_dir ./DeepSeek-R1-Distill-Qwen-7B

[PRE4]
mkdir ./DeepSeek-R1-Distill-Llama-8B

[PRE5]
modelscope download --model deepseek-ai/DeepSeek-R1-Distill-Llama-8B --local_dir ./DeepSeek-R1-Distill-Llama-8B

[PRE6]

用 Unsloth 加载模型
用 FastLanguageModel.from_pretrained 载入本地的 Llama-8B 蒸馏版，序列长度设 2048，显存吃紧时可把 load_in_4bit 换成 True。
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

切换到推理模式
调用一次 for_inference，模型结构保持不变，只是内部配置切到推理友好状态。
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

首轮 Tokenize 测试
用一句中文数学问题验证 tokenizer 能否正常映射到 GPU。
question = "请问如何证明根号 2 是无理数？"

inputs = tokenizer([question], return_tensors="pt").to("cuda")

inputs

[PRE13]
{'input_ids': tensor([[128000,  15225,  57107, 109425, 125544, 102831,  18476,     17,  21043,

         43292,  22649,   9039,  11571]], device='cuda:0'), 'attention_mask': tensor([[1, 1, 1, 1, 1, 1, 1, 1, 1, 1,

        1, 1, 1]], device='cuda:0')}

[PRE14]