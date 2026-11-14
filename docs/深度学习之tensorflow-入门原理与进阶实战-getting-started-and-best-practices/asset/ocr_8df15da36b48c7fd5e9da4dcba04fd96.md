```
79 hidden_size,
80 num_layers,
81 dropout,
82 grad_clip,
83 batch_size,
84 learning_rate,
85 lr_decay_factor,
86 forward_only=forward_only,
87 dtype=tf.float32)
88
89 print("model is ok")
90
91 ckpt = tf.train.latest_checkpoint(checkpoint_dir)
92 if ckpt!=None:
93 model.saver.restore(session, ckpt)
94 print ("Reading model parameters from {0}".format(ckpt))
95 else:
96 print ("Created model with fresh parameters.")
97 session.run(tf.global_variables_initializer())
98
99 return model
```

通过 latest_checkpoint 发现检查点文件。如果有检查点文件，就将其恢复到 session 中。

读取文件的函数定义如下：为了适用带有 bucket 机制的网络模型，按照 bucket 的大小序列读取数据，先按照 bucket 的个数定义好数据集 data_set，然后在读取每一对输入、输出时，都会比较其适合哪个 bucket，并将其放入对应的 bucket 中，最后返回 data_set。

# 代码9-35 train (续)

```
100 def readData(source_path, target_path, max_size=None):
101 '''
102 这个方法来自于 tensorflow 中的 translation 例子
103 '''
104 data_set = [[] for _ in buckets]
105 with tf.gfile.GFile(source_path, mode="r") as source_file:
106 with tf.gfile.GFile(target_path, mode="r") as target_file:
107 source, target = source_file.readline(), target_file.readline()
108 counter = 0
109 while source and target and (not max_size or counter < max_size):
110 counter += 1
111 if counter % 100000 == 0:
112 print("  reading data line %d" % counter)
113 sys.stdout.flush()
114 source_ids = [int(x) for x in source.split()]
115 target_ids = [int(x) for x in target.split()]
116 target_ids.append(vocab_utils.EOS_ID)
117 for bucket_id, (source_size, target_size) in enumerate(
118 (_buckets):
119 if len(source_ids) < source_size and len(target_ids) < target_size:
120 data_set[bucket_id].append([source_ids,
121 target_ids])
122 break
```