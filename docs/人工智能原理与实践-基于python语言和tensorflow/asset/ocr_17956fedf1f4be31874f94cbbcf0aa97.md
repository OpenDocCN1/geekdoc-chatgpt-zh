在终端启动 TensorBoard，并加载之前定义的数据流图

```
<tensorboard --logdir=name_scope_2
```

在 Transformation 名称作用域内有另外 4 个命名空间被安排到两个“层”中。第 1 层由作用域 A 和 B 构成，该层将 A 和 B 的输出传给下一层 C 和 D。最后的节点会将来自最后一层的输出作为其输入。在 TensorBoard 中展开 Transformation 名称作用域，将看到类似图 3-44 所示的效果。

![](bbox=[0.296, 0.203, 0.735, 0.893])

图 3-44