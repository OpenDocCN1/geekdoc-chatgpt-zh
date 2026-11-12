同的变量，但是有一组匹配不会发生冲突，所以连接节点可以正常输出它。

连接节点将生成自己的匹配列表，其中包含它接收的匹配输入事实和变量绑定列表。它将 Rete 向下传递给其他连接节点或规则节点。

如果连接节点从其输入接收到多个可能的绑定，那么它需要计算出可能正确的所有可能的绑定组合。现在仍采用前面的示例，假设我们将处理以下 AND 连接：

```
1 (?person (health < 15))
2 AND
3 (?radio (held-by ?person))
```

对比以下数据库：

```
1 (Whisker (health 12))
2 (Captain (health 9))
3 (radio-1 (held-by Whisker))
4 (radio-2 (held-by Sale))
```

来看下面的模式：

```
(?person (health < 15))
```

该模式有两种可能的匹配：

```
?person = Whisker
```

和

```
?person = Captain
```

再来看下面这个模式：

```
(radio (held-by ?person-1))
```

该模式也有两种可能的匹配：

```
?person = Whisker, ?radio = radio-1
```

和

```
?person = Sale, ?radio = radio-2
```

因此，连接节点有两组可能的两个绑定，因此有 4 种可能的组合，但只有一种是有