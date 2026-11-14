```
textsszen = create_vocabulary(vocabulary_filenaameen
size,Isch=False,normalize_digits = True)
print("training_data",len(training_dataen))
print("dictionary",len(dictionaryen))
########################
#创建中文字典
training_datach, countch, dictionarych, reverse_dictionarych,
textsszch = create_vocabulary(vocabulary_filenametch
size,Isch=True,normalize_digits = True)
print("training_datach",len(training_datach))
print("dictionarych",len(dictionarych))
```

执行完上面的代码后，会在当前目录下的 fanyichina 文件夹里找到 dictent.txt 与 dictch.txt 两个字典文件。

其中所调用的部分代码定义如下，严格来讲本例中生成的应该是词点，因为在中文处理中用了 jieba 分词库将文字分开了，是以词为单位存储对应索引的。

# 代码9-33 datautil（续）

```
import jieba
jieba.load_userdict("myjiebadict.txt")
def fenci(training_data):
    seg_list = jieba.cut(training_data) # 默认是精确模式
    training_ci = " ".join(seg_list)
    training_ci = training_ci.split()
    return training_ci
import collections
#系统字符，创建字典时需要加入
_PAD = "_PAD"
_GO = "_GO"
_EOS = "_EOS"
_UNK = "_UNK"
PAD_ID = 0
GO_ID = 1
EOS_ID = 2
UNK_ID = 3
#文字字符替换，不属于系统字符
_NUM = "_NUM"
#Isch=true 中文，false 英文
#创建词典，max_vocabulary_size=500 代表字典中有 500 个词
def create_vocabulary(vocabulary_file, raw_data_dir, max_vocabulary_size,Isch=True, normalize_digits=True):
    texts,textssz = get_ch_path_text(raw_data_dir, Isch,normalize_digits)
    print( texts[0], len(texts))
    print ("行数", len(textssz),textssz)
# 处理多行文本 texts
```