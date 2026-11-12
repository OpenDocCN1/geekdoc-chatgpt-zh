式中， \( u_i \) 为物品 \( w_i \) 作为中心词的向量； \( v_j \) 为物品 \( w_j \) 作为上下文词的向量； \( v_k \) 为抽样的负样本物品对应的作为上下文词的向量。

需要注意的是，因为Item2vec完全沿用了Word2vec的算法框架，所以每个物品 i 也都有两个向量：作为中心词的向量 \( u_i \) 和作为上下文词的向量 \( v_i \)，分别来自隐藏层和输出层的参数矩阵。在实际的应用中，我们可以沿用Word2vec的做法把中心词的向量作为物品最终的向量表达，也可以使用上下文词的向量，甚至可以将两个向量的均值或者向量拼接来作为最终的向量表达。

最后，如图3-14所示，利用Item2vec对XBOX Music数据集建构音乐人的Embedding，并与SVD的方法进行效果对比实验。

![](bbox=[0.14, 0.255, 0.87, 0.46])

图3-14 Item2vec和SVD的可视化效果对比（详见彩插）

实验把Item2vec和SVD生成的Embedding用t-SNE降到二维后分别进行数据可视化，平面上的不同的点都代表不同音乐人的Embedding，同类型的音乐人用同颜色表示，结果显示，Item2vec中同类型的音乐人更加聚集，SVD中则出现更多同类音乐人被打散的情况，因此Item2vec具有更好的向量化表示学习的效果。

# 3.3.2 YouTube DNN

Deep Neural Networks for YouTube Recommendation [8] 是YouTube推荐团队发表在2016年Rec Sys会议上的论文，它的Candidate Generation（召回建模）部分介绍了另一种不同于Item2vec的用户和物品向量化建模方法。

+   1.YouTube DNN的召回模型结构

YouTube DNN的召回建模可以定义为，给定一个用户 U 、上下文信息 C 和视频集合 V，预测用户观看某个视频的概率。对应的数学表达：

```
P(w_t=i | U,C)=\frac{e^{v_i^u}}{\sum_{j \in V} e^{v_j^u}}
(3-46)
```

与Item2vec一样，YouTube DNN通过对用户观看视频的概率进行建模，最终使用的是模型的中间结果，即用户和视频的向量表达，再通过向量的相似性来进行个性化推荐。

式 (3-46) 中， \( u \in \mathbb{R}^N \)，表示用户向量； \( v_j \in \mathbb{R}^N \)，表示视频集合 V 中的视频向量；用户观看视频概率的logits（深度学习中的logits通常是指未归一化的概率，即Softmax之前的值）为两者的内