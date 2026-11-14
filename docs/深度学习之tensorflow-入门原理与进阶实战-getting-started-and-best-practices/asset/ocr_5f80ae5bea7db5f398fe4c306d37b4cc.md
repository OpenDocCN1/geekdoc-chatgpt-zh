```
target_train_file_path= filesto[0]
return vocab_sizech,vocab_sizech,vocabch,rev_vocabch
def main():
    vocab_sizeen,vocab_sizech,vocaben,rev_vocabch= getdialogInfo()
    ......
    while True:
        prompt = "请输入: "
        sentence = input(prompt)
        conversation_history.append(sentence.strip())
        conversation_history = conversation_history[-convo_hist_limit:]

        token_ids=list(reversed(datautil.sentence_to_ids ("".join
        (conversation_history) ,vocaben,normalize_digits=True,
        Isch=True ) ))
    ......
```

同样在 main 函数的第一行中修改代码，调用 getdialogInfo 获得信息。

整个代码完成后运行程序，并输入类似样本中简单的对话，可以看到如下结果：

请输入: 你好
[20]
outputs [30, 2, 2, 2, 2] 2
您好

请输入: 你吃了吗
[12, 7, 4, 6]
outputs [5, 4, 7, 2, 2] 2
我 吃 了

请输入: 吃的啥
[3, 10, 4]
outputs [5, 4, 10, 2, 2] 2
我 吃 的

请输入: 你吃啥
[3, 4, 6]
outputs [5, 4, 17, 2, 2] 2
我 吃 三文鱼

请输入: 还有吗
[12, 11]
outputs [5, 29, 13, 16, 2] 2
我 没吃够 呢 不能

可以看到，在样本里最后一句的回答完全不一样，但是神经网络仿佛学到了里面的语义。在简单的问话：“还有吗？”可以读懂说话人的意思是想要，于是输出：“我 没吃够 呢 不能”。从那天机器人的例子可以看出，通过学习某个专业方面的对话样本（如某个业务的客服对话），会在该业务下产生很好的语义，并有很好的专业交流。