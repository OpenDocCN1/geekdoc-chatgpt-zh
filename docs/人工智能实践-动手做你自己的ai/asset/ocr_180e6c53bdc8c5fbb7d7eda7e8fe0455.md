```
if split_mode == "char":
    word_list = [char for char in start_text]
elif split_mode == "jieba":
    word_list = [word for word in jieba.cut(start_text)]

# gen_len是我们期望的生成字数或词数
for i in range(gen_len):
    temp_list = []
    # 统计给定前小于等于n-1个词的情况下，下一个词的词频分布
    for j in range(1, ngram_len):
        if j >= len(word_list):
            continue
        prefix = "".join(word_list[-(j + 1):])
        if prefix in ngram_dict:
            temp_list.extend(ngram_dict[prefix].items())
    # 按词频对词排序
    temp_list = sorted(temp_list, key=lambda d: d[1], reverse=True)
    next_word = ""
    # 如果最高频词是标点，则选择最高频词
    if temp_list[0] == ', ' or temp_list[0] == '。' or temp_list[0] == '\\n':
        next_word = temp_list[0]
    else:
        # 否则从前topn中随机选一个
        next_word = random.choice(sorted(temp_list, key=lambda d: d[1], reverse=True)[:topn])[0]
    word_list.append(next_word)

print("".join(word_list))
```

# ## 四、结果

在古诗数据集上训练一个4-gram模型后，用“明月”作为开头生成了如下图所示的一段诗歌。

明月，万事不堪思。登山屐，春风吹。树老，何处，风雨夜来风。江南春。不见。上客如先起，还应照所思香何处是，谁知苦寒歌。春来半月度，漾影逐波深。山色满轩白马津。云山，南山，苍苍非一朝。怨咽空自悲泪，不见，秋风。江上秋。不见君。如逢旧友无由。苍苍松桂阴，残月，千里。不见。君看白日暮铜台雨，秋风吹不尽，春风。江上月，独立望江南。江山。白发生。人间。天地有归人。江山此地，不见，秋风吹，门外，孤云。山中。水流。今