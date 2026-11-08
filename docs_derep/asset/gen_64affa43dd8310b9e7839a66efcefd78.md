# Hugging Face datasets 一把梭：从加载到改造的完整手感

想快速上手读数据、切分数据、改数据？datasets 库就是那把万能钥匙。下面用 SQuAD 这个经典问答集做主线，把核心用法串成一条可直接复用的流水线。

先装载库并直接拉取公开集，默认会得到包含训练集和验证集的集合对象。

[PRE0]python
ds = load_dataset('squad')

ds
[PRE1]
DatasetDict({
    train: Dataset({
        features: ['id', 'title', 'context', 'question', 'answers'],
        num_rows: 87599
    })
    validation: Dataset({
        features: ['id', 'title', 'context', 'question', 'answers'],
        num_rows: 10570
    })
})
加载完后你能看到两个子集，字段都是 id、title、context、question、answers，训练部分约 8.7 万条，验证约 1 万条。

如果只想要单一分片训练，指定 split 参数即可，返回的就是普通的 Dataset 而不是字典。

[PRE2]python
ds = load_dataset('squad', split='train')

ds
[PRE3]
Dataset({
    features: ['id', 'title', 'context', 'question', 'answers'],
    num_rows: 87599
})
这一步很适合做单分片训练或调试，结构更简洁。

自己的 CSV/JSON 也能无缝接入，通过 data_files 映射文件名到分片名即可。

[PRE4]python
data_files = {'train': 'train.csv', 'test': 'test.csv'}

ds = load_dataset('namespace/your_dataset_name', data_files=data_files)
常见的数据格式示意如下，instruction-input-output 三列很适合 Instruction Tuning。

[PRE5]json
{"instruction": " 在以下文本中提取所有的日期。 ", "input": "6 月 21 日是夏至，这是一年中白天最长的一天。 ", "output": "6 月 21 日 "}

{"instruction": "", "input": " 请生成一个新闻标题，描述一场正在发生的大型自然灾害。\n\n", "output": "" 强烈飓风肆虐，数百万人疏散！ ""}
SQuAD 样例单条的字段长这样，answers 同时保留起止位置和文本。

[PRE6]json
{
    "answers": {
        "answer_start": [1],
        "text": ["This is a test text"]
    },
    "context": "This is a test context.",
    "id": "1",
    "question": "Is this a test?",
    "title": "train test"
}
把数据集赋值给变量后，再次调用可以看到完整的 DatasetDict 结构，和最开始的加载结果一致。

[PRE7]python
from datasets import load_dataset

dataset = load_dataset("squad")

dataset
DatasetDict({
    train: Dataset({
        features: ['id', 'title', 'context', 'question', 'answers'],
        num_rows: 87599
    })
    validation: Dataset({
        features: ['id', 'title', 'context', 'question', 'answers'],
        num_rows: 10570
    })
})
通过键名就能取出特定分片，返回的是只包含该分片的 Dataset。

[PRE8]python
dataset['train']

Dataset({
    features: ['id', 'title', 'context', 'question', 'answers'],
    num_rows: 87599
})
索引第一条可以快速验样，看到真实的上下文、问题和答案结构。

[PRE9]python
dataset['train'][0]

{'id': '5733be284776f41900661182', 'title': 'University_of_Notre_Dame', 'context': 'Architecturally, the school has a Catholic character. Atop the Main Building\'s gold dome is a golden statue of the Virgin Mary. Immediately in front of the Main Building and facing it, is a copper statue of Christ with arms upraised with the legend "Venite Ad Me Omnes". Next to the Main Building is the Basilica of the Sacred Heart. Immediately behind the basilica is the Grotto, a Marian place of prayer and reflection. It is a replica of the grotto at Lourdes, France where the Virgin Mary reputedly appeared to Saint Bernadette Soubirous in 1858. At the end of the main drive (and in a direct line that connects through 3 statues and the Gold Dome), is a simple, modern stone statue of Mary.', 'question': 'To whom did the Virgin Mary allegedly appear in 1858 in Lourdes France?', 'answers': {'text': ['Saint Bernadette Soubirous'], 'answer_start': [515]}}
最后一步是批量改造数据。map 方法可以遍历每条样例做格式转换，这里把它改写成 Human-Assistant 对话范式。

[PRE10]python
def map_function(example):
    # Get the question and model output
    question = f"#### Human: {example['question'].strip()}"
    output = f"#### Assistant: {example['answers']['text'][0].strip()}"
    print(question, output)

data_train = dataset["train"].map(map_function)
运行时的进度和打印效果如下，说明映射已正常生效。

[PRE11]
Map:   0%|          | 0/87599 [00:00<?, ? examples/s]
#### Human: To whom did the Virgin Mary allegedly appear in 1858 in Lourdes France? #### Assistant: Saint Bernadette Soubirous

#### Human: What is in front of the Notre Dame Main Building? #### Assistant: a copper statue of Christ
从加载、切分、自定义数据源到 map 改造，整套流程就串起来了，直接拿去做微调预处理就行。