

图5-1 思维链提示和标准提示的对照图（Wei等人，2022年）

此外，他们还发现当使用思维链提示时，随着模型规模增大，出现了如图5-2所示的相变现象。这进一步导致后面大模型涌现能力的发现。思维链提示是展示模型规模扩大后出现涌现能力的典型案例，从图5-2中可以看到，当模型规模较小时，解决率几乎没有变化；但是当模型规模很大时，解决率提升非常明显。这十分清晰地说明了某些能力可能并不存在于小模型中，而只在大模型中可以获取。

![](images/image_035.jpg)

图5-2 基于思维链的复杂推理能力是模型规模扩大后的涌现能力（Wei等人，2022年）

通过下面这个统计奇偶数的例子，读者可以进一步感受思维链的魅力。

首先是标准提示，如下所示。

### 标准提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "在数字3、56、35、96、40、10、84、923、32、20 中，有多少个奇数，多少个偶数？"},
    ],
    temperature=0,
)
print(response["choices"][0]["message"]["content"])
```

ChatGPT输出如下。

奇数有4个：3、35、96、923。

偶数有6个：56、40、10、84、32、20。

正确答案是：3个奇数，7个偶数。这里不知道为什么ChatGPT把96也当成奇数了，可能它觉得96和3相关，其他几个和3相关的数字都是奇数，于是它便把96也列为奇数。

我们再来看看思维链提示的效果，如下所示。

### 思维链提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "在数字38、31、89、224中，有多少个奇数，多少个偶数？"},
        {"role": "assistant", "content": "我们一个一个地数：\n 1. 38是偶数，此时累计：偶数1个，奇数0个；\n 2. 31是奇数，此时累计：偶数1个，奇数1个；\n 3. 89是奇数，此时累计：偶数1个，奇数2个；\n 4. 224是偶数，此时累计：偶数2个，奇数2个；\n 所以,一共有2个偶数，2个奇数。"},
        {"role": "user", "content": "在数字3、56、35、96、40、10、84、923、32、20 中，有多少个奇数，多少个偶数？"},
    ],
    temperature=0,
)
print(response["choices"][0]["message"]["content"])
```

ChatGPT输出如下。

我们一个一个地数：

1. 3是奇数，此时累计：偶数0个，奇数1个；
2. 56是偶数，此时累计：偶数1个，奇数1个；
3. 35是奇数，此时累计：偶数1个，奇数2个；
4. 96是偶数，此时累计：偶数2个，奇数2个；
5. 40是偶数，此时累计：偶数3个，奇数2个；
6. 10是偶数，此时累计：偶数4个，奇数2个；
7. 84是偶数，此时累计：偶数5个，奇数2个；
8. 923是奇数，此时累计：偶数5个，奇数3个；
9. 32是偶数，此时累计：偶数6个，奇数3个；
10. 20是偶数，此时累计：偶数7个，奇数3个；

所以，一共有7个偶数，3个奇数。

可以看到，在使用思维链提示后，模型对奇偶数统计问题很快就改善了。目前，思维链提示已被证明能改善包括算术、常识判断和符号推理等多种任务的结果。

## 2. 天生的零样本推理器

虽然思维链提示的效果非常好，但构建一条优质的思维链是十分伤脑的，有没有更简单的方法呢？Kojima 等人（2022年）进一步发现，大语言模型天生具备零样本推理的能力（即Zero-CoT）^[³](#fn3)

![](images/image_036.jpg)

图5-3 零样本思维链提示示例（Kojima等人，2022年）

像少样本思维链提示这种精心构造思维链来显式地告知模型进行复杂推理的技术，通常需要耗费不少的人力和物力。相比之下，零样本思维链提示仅仅添加一条魔法语句就能达到非常不错的效果，让模型进行复杂推理，显得十分神奇。

下面让我们通过一个数学问题来熟悉一下零样本思维链提示。

首先，依然是标准提示，如下所示。

### 标准提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "用一只水桶装水, 把水加到原来的2倍, 连桶重10千克, 如果把水加到原来的5倍, 连桶重22千克。桶里原来有水多少千克?"},
    ],
    temperature=0,
)
print(response["choices"][0]["message"]["content"])
```

ChatGPT输出如下。

设桶里原来有水`x`千克，加水后水的重量为2`x`千克，加水前桶的重量为`y`千克，则有

2`x` + `y` = 10（把水加到原来的2倍，连桶重10千克）

5`x` + `y` = 22（把水加到原来的5倍，连桶重22千克）

化简得

`x` = 6^[⁴](#fn4)

因此，桶里原来有水6千克。

这个答案不对，应该是4千克。我们再来看看零样本思维链提示，如下所示。

### 零样本思维链提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "用一只水桶装水, 把水加到原来的2倍, 连桶重10千克, 如果把水加到原来的5倍, 连桶重22千克。桶里原来有水多少千克? Let's think step by step."},
    ],
    temperature=0,
)
print(response["choices"][0]["message"]["content"])
```

ChatGPT输出如下。

设桶里原来有水`x`千克，桶本身重`y`千克，则有

第一步：把水加到原来的2倍

桶里的水变成2`x`千克，连桶重10千克，桶重 `y` = 10−2`x`。

第二步：把水加到原来的5倍

桶里的水变成5`x`千克，连桶重22千克，桶重 `y` = 22−5`x`。

由此，我们可以列出一个方程：

10−2`x` = 22−5`x`

化简得

3`x` = 12

`x` = 4

所以，桶里原来有水4千克。

通过这个例子我们发现，在给问题加上魔法语句“Let’s think step by step”后，ChatGPT的回答明显变长了，而且思路清晰、步骤规范。Zero-CoT论文中提到，与没有加上魔法语句的解答相比，逐步解答的准确率更高，更符合常规逻辑。

使用GPT-3在MultiArith数学数据集上进行测试，结果显示，在加上“Let’s think step by step”这条提示语句后，准确率提高到原来的4倍多，从17.7%上涨到78.7%。此外，也可以尝试其他提示语句，如“Let’s think about this logically”、“Let’s solve this problem by splitting it into steps”等，这些提示语句也能提升模型的推理能力。读者可以自行尝试构建属于自己的魔法语句来提高模型的表现。

### 5.2.3 大语言模型复杂推理能力的改善

现在，我们已经知道了通过构建思维链提示或者使用零样本思维链的方式，可以激活大语言模型的复杂推理能力。那么，我们还可以继续提升模型的复杂推理能力吗？答案是肯定的，下面我们就来看看进一步改善大语言模型复杂推理能力的方法。

#### 1. 复杂问题分解，逐个击破

如前所述，我们经常使用一种思维技巧，即分析和综合，通过将问题分解成更小的部分，并对每一部分进行分析，最终综合各个部分的结果得出整体结论。那么，这种思维技巧是否可以应用于大语言模型的复杂推理呢？答案是肯定的。Zhou等人（2022年）基于这种思维技巧，提出了最少到最多（least-to-most）提示技术，如图5-4所示^[⁵](#fn5)

![](images/image_037.jpg)

图5-4 最少到最多提示示例（Zhou等人，2022年）

利用最少到最多提示技术，就可以有效地提升大语言模型的复杂推理能力。它能使大语言模型在处理过程中逐步增加对问题的理解和认识，避免受到过多信息和复杂性的困扰。这种分解和逐步解决的方式使整个推理过程更加可控和可管理，有助于提高推理的准确性和效率。类似地，Khot等人（2022年）提出了分解提示（decomposed prompting）技术，这种技术通过将复杂任务分解为更简单的子任务，然后逐一解决这些子任务，来提升大语言模型处理复杂任务的能力。^[⁶](#fn6)

下面我们尝试问ChatGPT一个稍微复杂的问题：如何拼接所给单词的最后一个字母？

首先，依然是标准提示，如下所示。

##### 标准提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": 'Q: think, machine \n A: "ke". \n Q: learning, reasoning, generalization \n A: "ggn". \n Q: artificial, intelligence  \n A: "le". \n Q: foo, bar, baz, blip, learn, prompting, world, shaking, event, dancefloor ,prisma, giraffe\n A:'},
    ],
    temperature=0,
)
print(response['choices'][0]['message']['content'])
```

ChatGPT输出如下。

"fbbeplwse".

可以看到，当使用标准提示时，ChatGPT似乎没有明白我们要找单词的最后一个字母。

接下来试试标准提示+零样本思维链提示，如下所示。

##### 标准提示+零样本思维链提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": 'Q: think, machine \n A: "ke". \n Q: learning, reasoning, generalization \n A: "ggn". \n Q: artificial, intelligence  \n A: "le". \n Q: foo, bar, baz, blip, learn, prompting, world, shaking, event, dancefloor, prisma, giraffe  \n A: Let"s think step by step.'},
    ],
    temperature=0,
)
print(response["choices"][0]["message"]["content"])
```

ChatGPT输出如下。

Q: foo
A: "f".
Q: bar
A: "b".
Q: baz
A: "z".
Q: blip
A: "b".
Q: lear
A: "l".
Q: prom
A: "p".
Q: worl
A: "w".
Q: shak
A: "s".
Q: even
A: "e".
Q: danc
A: "d".
Q: pris
A: "p".
Q: gira
A: "g".

当使用标准提示+零样本思维链提示时，ChatGPT的思维路径清晰了很多，但它似乎完全搞混了我们所要完成的任务。我们的任务是拼接所给单词的最后一个字母，但ChatGPT找的全都是单词的第一个字母，而且最后也没有将它们拼接起来。

我们再来试试思维链提示，如下所示。

##### 思维链提示

```
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": 'Q: think, machine \n A: The last letter of "think" is "k". The last letter of "machine" is "e". So "think, machine" is "ke". \n \n Q: learning, reasoning, generalization \n A: The last letter of "learning" is "g". The last letter of "reasoning" is "n". The last letter of "generalization" is "n". So "learning, reasoning, generalization" is "ggn". \n \n Q: artificial, intelligence  \n A: The last letter of "artificial" is "l". The last letter of "intelligence" is "e". So "artificial, intelligence" is "le". \n \n Q: foo, bar, baz, blip, learn, prompting, world, shaking, event, dancefloor, prisma, giraffe  \n A:'},
    ],
    temperature=0,
)
print(response['choices'][0]['message']['content'])
```

ChatGPT输出如下。

The last letter of "foo" is "o". The last letter of "bar" is "r". The last letter of "baz" is "z". The last letter of "blip" is "p". The last letter of "learn" is "n". The last letter of "prompting" is "g". The last letter of "world" is "d". The last letter of "shaking" is "g". The last letter of "event" is "t". The last letter of "dancefloor" is "r". The last letter of "prisma" is "a". The last letter of "giraffe" is "e". So "foo, bar, baz, blip, learn, prompting, world, shaking, event, dancefloor, prisma, giraffe" is "orzpngdtragae".

