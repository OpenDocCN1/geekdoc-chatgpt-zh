```
print('Average loss at step ', step, ': ', average_loss)
average_loss = 0

if step % 10000 == 0:
    sim = similarity.eval()
    for i in xrange(valid_size):
        valid_word = reverse_dictionary[valid_examples[i]]
        top_k = 8
        nearest = (-sim[i, :]).argsort() [1:top_k + 1]
        log_str = 'Nearest to %s:' % valid_word
        for k in xrange(top_k):
            close_word = reverse_dictionary[nearest[k]]
            log_str = '%s %s,' % (log_str, close_word)
        print(log_str)
final_embeddings = normalized_embeddings.eval()
```

源程序代码内容中,if step > 0:average_loss /= 2000 表示的是求取 2000 个 batch 的平均损失;if step % 10 000 == 0:表示每执行 10 000 步, 就进行一次验证, 选取一些验证单词, 挑选出在当前嵌入空间中与其距离最接近的一些单词, 并将这些单词进行输出, 输出结果是完全随机的, 没有什么特殊的意义; sim = similarity.eval()表示 sim 是验证单词与所有单词之间的相似度; for i in xrange(valid_size):表示有验证单词的取值范围是 valid_size; top_k = 8 表示输出的结果是最相邻的 8 个单词; final_embeddings = normalized_embeddings.eval()表示 final_embeddings 是最后得到的词嵌入向量, 它是一个归一后的词嵌入向量形式, 它的形状是[vocabulary_size, embedding_size], 每一行所代表的是对应 index 词的词嵌入表示。例如 final_embeddings[1, :]所表示的就是 id 为 1 的单词所对应的词嵌入表示, final_embeddings[53, :]所表示的就是 id 为 53 的单词所对应的词嵌入表示, 所有单词都以上述相同的方法进行类推。经过此部分代码块的运算, 词嵌入空间中的向量表示已经具备了一定的含义。单词与单词之间的相似性更加容易理解。如果增加训练的步数, 并对模型中所出现的参数进行更加合理的微调, 输出的词嵌入表示也将会更加精确。

# 7.3 嵌套学习可视化与评估

当程序进行到 final_embeddings 时, 可求得输出内容, 运行基本结束了, 如果还想对运算的结果进行可视化表示, 则可以继续输入可视化功能的代码, 具体代码如下。

```
def plot_with_labels(low_dim_embs, labels, filename='tsne.png'):
    assert low_dim_embs.shape[0] >= len(labels), 'More labels than embeddings'
    plt.figure(figsize=(18, 18)) #in inches
    for i, label in enumerate(labels):
        x, y = low_dim_embs[i, :]
        plt.scatter(x, y)
        plt.annotate(label, xy=(x, y), xytext=(5, 2), textcoords='offset points', ha='right', va='bottom')
    plt.savefig(filename)

try:
    from sklearn.manifold import TSNE
    import matplotlib.pyplot as plt
    tsne = TSNE(perplexity=30, n_components=2, init='pca', n_iter=5000)
    plot_only = 300
    low_dim_embs = tsne.fit_transform(final_embeddings[:plot_only, :])
    labels = [reverse_dictionary[i] for i in xrange(plot_only)]
```