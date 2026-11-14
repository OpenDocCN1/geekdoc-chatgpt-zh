```
```python
# fig06_02.py
"""Tokenizing a string and counting unique words."""
text = ('this is sample text with several words '
        'this is more sample text with some different words')
word_counts = {}
# count occurrences of each unique word
for word in text.split():
    if word in word_counts:
        word_counts[word] += 1 # update existing key-value pair
    else:
        word_counts[word] = 1 # insert new key-value pair
print(f'{"WORD":<12}{"COUNT"}')
for word, count in sorted(word_counts.items()):
    print(f'{word:<12}{count}')
print('\nNumber of unique words:', len(word_counts))
```
```

```
```
WORD         COUNT
different    1
is           2
more         1
sample       2
several      1
some         1
text         2
this         2
with         2
words        2
Number of unique words: 10
```
```

第10行通过调用字符串方法split来标记text，该方法使用定界符参数来分隔单词。如果不提供参数，默认使用空格来拆分。该方法返回标记列表（即文本中的单词）。第10～14行遍历单词列表。对于每个单词，第11行确定该单词（键）是否已经在字典中。如果是，第12行给该单词的计数加一；否则，第14行将为该单词插入一个新的键-值对，初始计数为1。

第16～21行将结果汇总到一个包含两个列的表中，分别表示每个单词及其对应的计数。第18和19行中的for语句遍历字典的键-值对。它将每个键和值解压缩到变量word和count中，然后在两列中显示它们。第21行显示有多少个不重复的单词。

# # Python标准库collections模块

Python标准库已经包含了第10～14行中使用字典和循环实现的计数功能。collections模块包含类型计数器，用于接收一个可递归的对象并汇总计算它的元素。使用计数器可以让我们用更少的代码实现前述功能：