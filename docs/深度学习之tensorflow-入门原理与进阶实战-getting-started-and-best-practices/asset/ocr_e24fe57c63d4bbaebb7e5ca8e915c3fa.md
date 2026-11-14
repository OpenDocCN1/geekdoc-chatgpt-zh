# 代码9-34 seq2seq_model

```
"""带有注意力机制的 Sequence-to-sequence 模型."""

from __future__ import absolute_import
from __future__ import division
from __future__ import print_function

import random

import numpy as np
from six.moves import xrange # pylint: disable=redefined-builtin
import tensorflow as tf
datautil = __import__("9-33 datautil")
import datautil as data_utils

class Seq2SeqModel(object):
    """带有注意力机制并且具有 multiple buckets 的 Sequence-to-sequence 模型.
    这个类实现了一个多层循环网络组成的编码器和一个具有注意力机制的解码器.完全是按照论文:
    http://arxiv.org/abs/1412.7449 - 中所描述的机制实现。更多细节信息可以参看论文内容
    这个 class 除了使用 LSTM cells 还可以使用 GRU cells, 还使用了 sampled
    softmax 来
    处理大词汇量的输出. 在论文 http://arxiv.org/abs/1412.2007 中的第三节描述了
    sampled softmax. 在论文 http://arxiv.org/abs/1409.0473 里面还有一个关于
    这个模型的一个单层的使用双向 RNN 编码器的版本

    def __init__(self,
                 source_vocab_size,
                 target_vocab_size,
                 buckets,
                 size,
                 num_layers,
                 dropout_keep_prob,
                 max_gradient_norm,
                 batch_size,
                 learning_rate,
                 learning_rate_decay_factor,
                 use_lstm=False,
                 num_samples=512,
                 forward_only=False,
                 dtype=tf.float32):
    """创建模型

    Args:
        source_vocab_size:源词汇的大小.
        target_vocab_size:目标词汇的大小.
        buckets: 一个 (I, O)的 list, I 代表输入的最大长度, O 代表输出的最大长度, 例如
        [(2, 4), (8, 16)].
        size: 模型中每层的 units 个数.
```