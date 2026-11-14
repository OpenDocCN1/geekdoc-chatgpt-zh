线程使单个节点集群可以并发执行部分Spark任务，以模拟Spark集群提供的并行性。当我们说两个任务并发运行时，意思是它们都在同时取得进展，通常是在短时间内执行一个任务，然后再执行另一个任务。当我们说两个任务并行运行时，意思是在同时执行它们，并行是在基于云的计算机集群上执行的Hadoop和Spark的主要优点之一。

接下来，创建sparkContext，传递sparkConf作为参数：

```
[4]: from pyspark import SparkContext
sc = SparkContext(conf=configuration)
```

# 读取文本文件并将其映射到单词

可使用应用于RDD的函数式编程技术（如过滤、映射和归约）来处理SparkContext。RDD获取存储在Hadoop文件系统集群中的数据，并使用户能经过一系列处理步骤来转换RDD中的数据。这些处理步骤是惰性的（见第5章），除非用户指示Spark应该处理该任务，否则它们不会执行任何工作。

以下代码段指定了三个步骤：

+   - sparkContext方法textFile从RomeoAndJuliet.txt加载文本行，并将其作为表示每行的字符串的RDD（来自PySpark模块）返回。
+   - RDD方法map使用其lambda参数删除TextBlob的strip_punc函数的所有标点，将每行文本转换为小写。这个方法返回一个新的RDD，可在该RDD上指定要执行的其他任务。
+   - RDD方法flatmap使用其lambda参数将每行文本映射成单词并生成单个单词列表，而不是单个文本行。flatMap生成的新的RDD，代表了Romeo and Juliet中的所有单词。

```
[5]: from textblob.utils import strip_punc
tokenized = sc.textFile('RomeoAndJuliet.txt')\
.map(Lambda line: strip.punc(line, all=True).lower())\
.flatMap(Lambda line: line.split())
```

# 移除停用词