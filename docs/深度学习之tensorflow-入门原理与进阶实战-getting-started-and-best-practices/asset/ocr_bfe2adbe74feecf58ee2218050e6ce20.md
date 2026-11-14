```
#VGG 检查点
checkpoints_dir = 'vgg_19_2016_08_28'
init_fn = slim.assign_from_checkpoint_fn(
os.path.join(checkpoints_dir, 'vgg_19.ckpt'),
slim.get_model_variables('vgg_19'))
```

注意：这里对于 srResNet 的变量恢复，要在建立 Saver 时传入 var_list 参数来指定好所要恢复的变量列表，否则默认是恢复所有变量，但是模型里却找不到其他变量，会报错误。

# 8. 启动session从检查点恢复变量

对于 VGG 模型的恢复，可以参考第 11 章中的内容。其他的检查点恢复的写法与前面一样。代码如下：

# 代码12-9 rsgan (续)

```
103 log_steps=100
104 training_epochs=16000
105
106 with tf.Session() as sess:
107
108 sess.run(tf.global_variables_initializer())
109
110 init_fn(sess)
111
112 kpt = tf.train.latest_checkpoint(srResNet_path)
113 print("srResNet_path",kpt,srResNet_path)
114 startepo= 0
115 if kpt!=None:
116 srResNetloader.restore(sess, kpt)
117 ind = kpt.find("-")
118 startepo = int(kpt[ind+1:])
119 print ("srResNetloader
120 global_step=",global_step.eval(),startepo)
121
122 kpt = tf.train.latest_checkpoint(save_path)
123 print ("srgan",kpt)
124 startepo = 0
125 if kpt!=None:
126 saver.restore(sess, kpt)
127 ind = kpt.find("-")
128 startepo = int(kpt[ind+1:])
129 print ("global_step=",global_step.eval(),startepo)
```