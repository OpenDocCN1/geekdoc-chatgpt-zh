[PRE0]

#### 5. DeepSpeed 启动全流程

DeepSpeed 参数准备就绪后，就可以开始训练前的初始化环节，整体拆解如下。

- （1）选定执行设备：先判断代码是否通过 args.local_rank 指定了本地 GPU，若未指定则直接落到默认的 CUDA 上，若指定则为当前进程绑定对应的 GPU。
- （2）搭建分布式环境：分布式场景下调用 deepspeed.init_distributed() 来完成各进程间的同步，让分布式运行时就绪。
- （3）获取进程编号：借助 torch.distributed.get_rank() 为当前进程分配唯一的全局 rank 或 ID。
- （4）生成 DeepSpeed 配置：依据用户选项，比如是否开启 offload、选用哪个 Zero Stage 等，拼装出一个 DeepSpeed 配置字典，用来决定后续的训练策略。
- （5）统一进程进度：通过 torch.distributed.barrier() 让所有 worker 等齐再继续，避免后续步骤出现不同步。
- （6）正式初始化：这是核心步骤。调用 deepspeed.initialize 把模型、优化器、调度参数以及前面组好的配置一并交给库处理，函数会返回经过 DeepSpeed 优化后的模型和优化器实例。
- （7）开启梯度检查点：面对超大模型时，梯度检查点能显著降低显存占用，只在需要时重算中间梯度。若用户打开该开关，就执行 model.gradient_checkpointing_enable() 来启用。

具体代码如下所示：

[PRE1]

#### 6. DeepSpeed 训练实战

用 DeepSpeed 跑训练时，整体可以归纳为三个阶段。

- （1）训练前准备：用 print_rank_0 输出当前训练状态，该函数保证只有主进程打印，防止多进程重复刷屏。正式进入循环前，先对模型做一次评估，算出困惑度。
- （2）训练循环：每轮开始时打印当前轮次与总轮次。迭代中先把数据批次搬到对应 GPU，再做前向得到损失；随后用 model.backward(loss) 求梯度，用 model.step() 更新参数。主进程额外调用 print_throughput 打印吞吐量，帮助判断训练速度与效率。
- （3）模型保存：当指定了输出目录时，会保存模型状态与配置。保存格式可选择 HuggingFace 兼容格式或 DeepSpeed Zero Stage 3 专属格式。save_hf_format 用来导出 HuggingFace 格式，导出后可直接用 from_pretrained 加载；Zero Stage 3 场景则调用 save_zero_three_model，因为此时每个 GPU 只持有模型的一部分参数。

具体代码如下所示：