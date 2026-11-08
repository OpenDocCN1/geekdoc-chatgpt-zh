最近在用 PEFT 做 LoRA 指令微调，整体的配置思路是先把适配器参数定好，再组装 SFT 训练器一键跑起来。

先定 PEFT 的基础：模型类型选 TaskType.CAUSAL_LM，LoRA 的缩放系数 lora_alpha 设为 16，dropout 设为 0.05。配置完成后用 get_peft_model 把原始模型和 peft_config 包装起来，得到可训练的 LoRA 模型。

训练参数方面用 SFTConfig 新建一个配置对象，输出目录指向 /tmp，开启 neftune 噪声，neftune_noise_alpha 为 10。显存友好地把 per_device_train_batch_size 设成 1，max_seq_length 限制在 100，训练轮数 num_train_epochs 为 10，日志打印频率 logging_steps 为 10，日志策略 logging_strategy 用 steps。

为了只对完成部分做监督，response_template 定为 <|start_header_id|>assistant<|end_header_id|>\n\n，然后用 DataCollatorForCompletionOnlyLM 把这个模板和 tokenizer 组合成 collator。

最后组装 SFTTrainer，传入刚才包装好的 model、训练集 train_dataset、上面那份 sft_config 作为 args，以及自定义的 data_collator。实例化完成后直接调用 trainer.train() 开始训练。

[PRE0]

π (left | s_t) = 0.1

π (up | s_t) = 0.2

π (right | s_t) = 0.7

[PRE1]

{s_0, a_0, s_1, a_1, ...}

[PRE2]

[PRE3]

[PRE4]

[PRE5]