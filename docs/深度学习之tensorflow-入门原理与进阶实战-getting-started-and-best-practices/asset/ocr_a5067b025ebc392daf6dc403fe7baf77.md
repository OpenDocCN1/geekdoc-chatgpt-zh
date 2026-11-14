```
202    for batch_idx in xrange(self.batch_size)], dtype=
    np.int32))
203
204    for length_idx in xrange(decoder_size):
205        batch_decoder_inputs.append(
206            np.array([decoder_inputs[batch_idx][length_idx]
207            for batch_idx in xrange(self.batch_size)], dtype=
208            np.int32))
209        # 定义 target_weights 变量，默认是 1，如果对应的 targets 是 padding，
210        # 则 target_weights 就为 0
211        batch_weight = np.ones((self.batch_size, dtype=np.float32)
212        for batch_idx in xrange(self.batch_size):
213            # 如果对应的输出 target 是一个 PAD 符号，就将 weight 设为 0
214            # 将 decoder_input 向前移动 1 位得到对应的 target
215            if length_idx < decoder_size - 1:
216                target = decoder_inputs[batch_idx][length_idx + 1]
217            if length_idx == decoder_size - 1 or target == data_utils.PAD_ID:
218                batch_weight[batch_idx] = 0.0
219        batch_weights.append(batch_weight)
220    return batch_encoder_inputs, batch_decoder_inputs, batch_weights
```

# 15. Seq2Seq框架的迭代更新处理

这部分代码主要是构建输入 feed 数据，即输出的 OP。在输入时，根据传入的 bucket_id 构建相应大小的输入输出 list，通过循环传入 list 中对应的操作符里。由于 decoder_inputs 的长度比 bucket 中的长度大 1，所以需要再多放一位到 decoder_inputs 的 list 中，在前面构建 targets 时，需要将所有的 decoder_inputs 向后移一位，targets 作为标签要与 bucket 中的长度相等。确切地说 target_weights 是与 targets 相等的，所以不需要再输入值。

```
代码9-34 seq2seq_model (续)
220    def step(self, session, encoder_inputs, decoder_inputs, target_
221            weights,
222            bucket_id, forward_only):
223        """'注入给定输入数据步骤
224
225        Args:
226            session: tensorflow 所使用的 session
227            encoder_inputs:用来注入 encoder 输入数据的numpy int vectors 类型的 list
228            decoder_inputs:用来注入 decoder 输入数据的numpy int vectors 类型的 list
229            target_weights:用来注入 target weights 的 numpy float vectors 类型的 list
230            bucket_id: which bucket of the model to use
231            forward_only: 只进行正向传播
232
233        Returns:
234            一个由 gradient norm（不做反向时为 none）, average perplexity, and the
235            outputs 组成的 triple
236
237        Raises:
238            ValueError:如果 encoder_inputs, decoder_inputs, 或者是 target_
```