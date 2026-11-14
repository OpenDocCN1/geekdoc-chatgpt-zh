| 操 作 | 描 述 |
|---|---|
| tf.reduce_min(input_tensor, axis=None, keep_dims=False, name=None, reduction_indices=None) | 求tensor中的最小值 |
| tf.reduce_max(input_tensor,  axis=None,  keep_dims=False, name=None, reduction_indices=None) | 求tensor中的最大值 |
| tf.reduce_mean(input_tensor, axis = None , keep_dims=False, name=None, reduction_indices=None) | 求tensor中的平均值 |
| tf.reduce_all(input_tensor, axis=None, keep_dims=False, name=None, reduction_indices=None) | 对tensor中的各个元素求逻辑'与'。例如：
x = [[True, True],[False, False]]
tf.reduce_all(x) ==> False
tf.reduce_all(x, 0) ==> [False, False]
tf.reduce_all(x, 1) ==> [True, False] |
| tf.reduce_any(input_tensor, axis=None, keep_dims=False, name=None, reduction_indices=None) | 对tensor中各个元素求逻辑'或' |

# 4.2.6 分割

分割操作是 TensorFlow 不常用的操作，在复杂的网络模型里偶尔才会用到。如表 4-14 所示为分割操作的相关函数。

# 表 4-14 分割相关函数

| 操 作 | 描 述 |
|---|---|
| tf.segment_sum(data, segment_ids, name=None) | 按照segment_ids指定的维度，分割张量data中的值，还可以返回data中指定片段的累加和。例如：
c = tf.constant([[1,2,3,4], [-1,-2,-3,-4], [5,6,7,8]])
tf.segment_sum(c, tf.constant([0, 0, 1]))
其输出如下：
[[0 0 0 0]
 [5 6 7 8]]
这个例子表明：将c按照 [0, 0, 1]的维度来分割，并将c中的头两行加起来作为结果的第1行，将c中的第3行作为结果的第2行 |
| tf.segment_prod(data, segment_ids, name=None) | 根据segment_ids的分段计算各个片段的积 |
| tf.segment_min(data, segment_ids, name=None) | 根据segment_ids的分段计算各个片段的最小值 |
| tf.segment_max(data, segment_ids, name=None) | 根据segment_ids的分段计算各个片段的最大值 |
| tf.segment_mean(data, segment_ids, name=None) | 根据segment_ids的分段计算各个片段的平均值 |
| tf.unsorted_segment_sum(data, segment_ids, num_segments, name=None) | 与tf.segment_sum函数类似，不同在于segment_ids中id顺序可以是无序的 |