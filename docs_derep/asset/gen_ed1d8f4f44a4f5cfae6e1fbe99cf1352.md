ColossalAI ViT 流水线并行训练拆解

下面是一套基于 ColossalAI 实现 Vision Transformer 流水线并行的完整配置与搭建思路，从并行策略、训练超参到模型分段构建和分布式启动，关键点都保留在代码里。

先看并行与混合精度相关的基础设定，pipeline 规模、微批次划分、张量形状以及梯度裁剪策略都在这里统一：
from colossalai.amp import AMP_TYPE

parallel = dict(

    pipeline=2

)

# 流水线并行设置

NUM_MICRO_BATCHES = parallel['pipeline']

TENSOR_SHAPE = (BATCH_SIZE // NUM_MICRO_BATCHES, SEQ_LENGTH, HIDDEN_SIZE)

fp16 = dict(mode=AMP_TYPE.NAIVE)

clip_grad_norm = 1.0

随后是训练和模型结构的全局超参，包含每个 GPU 的批次大小、优化器参数、训练轮数、热身策略以及 ViT 的具体结构尺寸：
[PRE0]python
# 超参数

# 批量数是每个 GPU 的

# global batch size = BATCH_SIZE x data parallel size

BATCH_SIZE = 256

LEARNING_RATE = 3e-3

WEIGHT_DECAY = 0.3

NUM_EPOCHS = 300

WARMUP_EPOCHS = 32

# model config

IMG_SIZE = 224

PATCH_SIZE = 16

HIDDEN_SIZE = 768

DEPTH = 12

NUM_HEADS = 12

MLP_RATIO = 4

NUM_CLASSES = 10

CHECKPOINT = True

SEQ_LENGTH = (IMG_SIZE // PATCH_SIZE) ** 2 + 1 # 为 cls token 加 1

ColossalAI 提供的流水线模型构建入口随后被引用，用于后续的工厂化创建：
[PRE1]python
colossal.builder.build_pipeline_model_from_cfg

colossalai.builder.build_pipeline_model

接下来是 PipelineVisionTransformer 所需的基础依赖，包含通用工具和注册机制：
[PRE2]python
import math

from typing import Callable

import inspect

import torch

from colossalai import nn as col_nn

from colossalai.registry import LAYERS, MODELS

模型类本身在注册表里实现，支持首尾阶段的裁剪嵌入与分类头，并通过 start_idx / end_idx 控制分段：
[PRE3]python
from colossalai.logging import get_dist_logger

from colossalai.core import global_context as gpc

from colossalai.context import ParallelMode

from colossalai.builder.pipeline import partition_uniform

from torch import dtype, nn

from model_zoo.vit.vit import ViTBlock, ViTEmbedding, ViTHead

@ MODELS.register_module

class PipelineVisionTransformer(nn.Module):

    def __init__(self,

                 img_size: int = 224,

                 patch_size: int = 16,

                 in_chans: int = 3,

                 num_classes: int = 1000,

                 depth: int = 12,

                 num_heads: int = 12,

                 dim: int = 768,

                 mlp_ratio: int = 4,

                 attention_dropout: float = 0.,

                 dropout: float = 0.1,

                 drop_path: float = 0.,

                 layernorm_epsilon: float = 1e-6,

                 activation: Callable = nn.functional.gelu,

                 representation_size: int = None,

                 dtype: dtype = None,

                 bias: bool = True,

                 checkpoint: bool = False,

                 init_method: str = 'torch',

                 first_stage=True,

                 last_stage=True,

                 start_idx=None,

                 end_idx=None,):

        super().__init__()

        layers = []

        if first_stage:

            embed = ViTEmbedding(img_size=img_size,

                                 patch_size=patch_size,

                                 in_chans=in_chans,

                                 embedding_dim=dim,

                                 dropout=dropout,

                                 dtype=dtype,

                                 init_method=init_method)

            layers.append(embed)

        # stochastic depth decay rule

分段的核心构造逻辑：随机深度、块切片、尾部归一化和头部：
[PRE4]python
dpr = [x.item() for x in torch.linspace(0, drop_path, depth)]

if start_idx is None and end_idx is None:

    start_idx = 0

    end_idx = depth

blocks = [

    ViTBlock(

        dim=dim,

        num_heads=num_heads,

        mlp_ratio=mlp_ratio,

        attention_dropout=attention_dropout,

        dropout=dropout,

        drop_path=dpr[i],

        activation=activation,

        dtype=dtype,

        bias=bias,

        checkpoint=checkpoint,

        init_method=init_method,

    ) for i in range(start_idx, end_idx)

]

layers.extend(blocks)

if last_stage:

    norm = col_nn.LayerNorm(normalized_shape=dim, eps=layernorm_epsilon, dtype=dtype)

    head = ViTHead(dim=dim,

                    num_classes=num_classes,

                    representation_size=representation_size,

                    dtype=dtype,

                    bias=bias,

                    init_method=init_method)

    layers.extend([norm, head])

self.layers = nn.Sequential(

    *layers

)

def forward(self, x):

    x = self.layers(x)

    return x

def _filter_kwargs(func, kwargs):

    sig = inspect.signature(func)

    return {k: v for k, v in kwargs.items() if k in sig.parameters}

def _build_pipeline_vit(module_cls, num_layers, num_chunks, device=torch.device('cuda'),

                        **kwargs):

    logger = get_dist_logger()

    if gpc.is_initialized(ParallelMode.PIPELINE):

        pipeline_size = gpc.get_world_size(ParallelMode.PIPELINE)

根据流水线 rank 进行均匀切分并实例化对应段的模型：
[PRE5]python
pipeline_rank = gpc.get_local_rank(ParallelMode.PIPELINE)

else:

    pipeline_size = 1

    pipeline_rank = 0

rank = gpc.get_global_rank()

parts = partition_uniform(num_layers, pipeline_size, num_chunks)[pipeline_rank]

models = []

for start, end in parts:

    kwargs['first_stage'] = start == 0

    kwargs['last_stage'] = end == num_layers

    kwargs['start_idx'] = start

    kwargs['end_idx'] = end

    logger.info(f'Rank{rank} build layer {start}-{end}, {end-start}/{num_layers} layers')

    chunk = module_cls(**_filter_kwargs(module_cls.__init__, kwargs)).to(device)

    models.append(chunk)

if len(models) == 1:

    model = models[0]

else:

    model = nn.ModuleList(models)

return model

def build_pipeline_vit(num_layers, num_chunks,

device=torch.device('cuda'), **kwargs):

    return _build_pipeline_vit(PipelineVisionTransformer, num_layers, num_chunks, device,

    **kwargs)

训练脚本的依赖集合，包含调度器、日志、损失、学习率调度以及数据加载工具：
[PRE6]python
from colossalai.engine.schedule import (InterleavedPipelineSchedule,

                                      PipelineSchedule)

from colossalai.utils import MultiTimer

import os

import colossalai

import torch

from colossalai.context import ParallelMode

from colossalai.core import global_context as gpc

from colossalai.logging import get_dist_logger

from colossalai.nn import CrossEntropyLoss

from colossalai.nn.lr_scheduler import CosineAnnealingWarmupLR

from colossalai.utils import is_using_pp, get_dataloader

from model.vit import build_pipeline_vit

from model_zoo.vit.vit import _create_vit_model

from tqdm import tqdm

from torchvision import transforms

from torchvision.datasets import CIFAR10

分布式环境初始化与日志准备，判断是否启用流水线并行：
[PRE7]python
from colossalai.engine.schedule import (InterleavedPipelineSchedule,

                                        PipelineSchedule)

from colossalai.utils import MultiTimer

import os

import colossalai

import torch

from colossalai.context import ParallelMode

from colossalai.core import global_context as gpc

from colossalai.logging import get_dist_logger

from colossalai.nn import CrossEntropyLoss

from colossalai.nn.lr_scheduler import CosineAnnealingWarmupLR

from colossalai.utils import is_using_pp, get_dataloader

from model.vit import build_pipeline_vit

from model_zoo.vit.vit import _create_vit_model

from tqdm import tqdm

from torchvision import transforms

from torchvision.datasets import CIFAR10

# initialize distributed setting

parser = colossalai.get_default_parser()

args = parser.parse_args()

# launch from torch

colossalai.launch_from_torch(config=args.config)

# get logger

logger = get_dist_logger()

logger.info("initialized distributed environment", ranks=[0])

if hasattr(gpc.config, 'LOG_PATH'):

    if gpc.get_global_rank() == 0:

        log_path = gpc.config.LOG_PATH

        if not os.path.exists(log_path):

            os.mkdir(log_path)

        logger.log_to_file(log_path)

use_pipeline = is_using_pp()

最后根据配置组装模型参数，流水线模式下会按层切分到不同设备：
[PRE8]python
# create model

model_kwargs = dict(img_size=gpc.config.IMG_SIZE,

                    patch_size=gpc.config.PATCH_SIZE,

                    dim=gpc.config.HIDDEN_SIZE,

                    depth=gpc.config.DEPTH,

                    num_heads=gpc.config.NUM_HEADS,

                    mlp_ratio=gpc.config.MLP_RATIO,

                    num_classes=gpc.config.NUM_CLASSES,

                    init_method='jax',

                    checkpoint=gpc.config.CHECKPOINT)

if use_pipeline: