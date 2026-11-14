+   time, batch_size, num_classes]。
+   * sequence_length: 序列长度。
+   * preprocess_collapse_repeated: 是否需要预处理，将重复的 label 合并成一个 label，默认是 false。
+   * ctc_merge_repeated: 在计算时是否将每个 non_blank（非空）重复的 label 当成单独的 label 来解释，默认是 true。
+   * time_major: 决定 inputs 的格式。

对于 preprocess_collapse_repeated 与 ctc_merge_repeated，都是对于 ctc_loss 中重复标签处理的控制，各种情况组合后如表 9-3 所示。

| 参数情况 | 说明 |
|---|---|
| preprocess_collapse_repeated=TRUE; ctc_merge_repeated=TRUE; | 忽略全部重复标签，只计算不重复的标签 |
| preprocess_collapse_repeated=False; ctc_merge_repeated=TRUE; | 标准的CTC模式，也是默认模式，不做预处理，只在运算时重复标签将不再当成独立的标签来计算 |
| preprocess_collapse_repeated=TRUE; ctc_merge_repeated=False; | 忽略全部重复标签，只计算不重复的标签,因为预处理时已经把重复的标签去掉了 |
| preprocess_collapse_repeated=False; ctc_merge_repeated=False; | 所有重复标签都会参加计算 |

对于 ctc_loss 的返回值，仍然属于 loss 的计算模式，当取批次样本进行训练时，同样也需要对最终的 ctc_loss 求均值。

> 注意：对于重复标签方面的 ctc_loss 计算，一般情况下默认即可。
> 另外这里有个隐含的规则，inputs 中的 classes 是指需要输出多少类，在使用 ctc_loss 时，要将 classes+1，即再多生成一个类，用于存放 blank。因为输入的序列与 label 并不是一一对应的，所以需要通过添加 blank 类，当对应不上时，最后的 softmax 就会将其生成到 blank。具体做法就是在最后的输出层多构建一个节点即可。
> 这个规则是 ctc_loss 内置的，否则当标准标签 label 中的类索引等于 inputs 中的 size-1 时会报错。

# 2. SparseTensor类型

前面提到了 SparseTensor 类型，这里主要介绍一下，本来应该将其放在前面章节介绍的，考虑到读者的接受程度，所以就放在这里介绍了。