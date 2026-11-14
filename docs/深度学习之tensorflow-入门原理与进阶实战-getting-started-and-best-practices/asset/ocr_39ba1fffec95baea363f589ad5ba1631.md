```
return idsdata

#将文件中的内容转成 ids，不是Windows下的文件要使用utf8 编码格式
def textfile_to_idsfile(data_file_name, target_file_name, vocab,
    normalize_digits=True,Isch=True):
    if not gfile.Exists(target_file_name):
        print("Tokenizing data in %s" % data_file_name)
        with gfile.GFile(data_file_name, mode="rb") as data_file:
            with gfile.GFile(target_file_name, mode="w") as ids_file:
                counter = 0
                for line in data_file:
                    counter += 1
                    if counter % 100000 == 0:
                        print(" tokenizing line %d" % counter)
                        token_ids = sentence_to_ids(line.decode('utf8'), vocab,
                            normalize_digits,Isch)
                        ids_file.write( " ".join([str(tok) for tok in token_ids]) +
                        "\n")
def ids2texts( indices,rev_vocab):
    texts = []
    for index in indices:
        texts.append(rev_vocab[index])
    return texts
```

运行上述代码后，可以在本地路径 fanyichina\\fromids、fanyichina\\toids 文件夹下面找到同名的 txt 文件，打开后能够看到里面全是索引值。

# 4. 对样本文件进行分析图示

为了使 bucket 的设置机制较合理，我们把样本的数据用图示方式显示出来，直观地看一下每个样本的各个行长度分布情况，在 main 函数中接着添加以下代码：

# 代码9-33 datautil (续)

```
def main():
......
#分析样本分布
filesfrom,_getRawFileList(data_dir+"fromids/")
filesto,_getRawFileList(data_dir+"toids/")
source_train_file_path = filesfrom[0]
target_train_file_path= filesto[0]
analysissfile(source_train_file_path,target_train_file_path)
if __name__=="__main__":
    main()
```

最后两行为启动 main 函数。analysisfile 为文件的分析函数，实现如下：