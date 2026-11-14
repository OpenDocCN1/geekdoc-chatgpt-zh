In [18]处的代码表示的功能是，提取从起始位置（pos）0 开始，截取的长度（len）为 3 的子字符串。Out[18]给出了对应的字符子数组。

在 TensorFlow 的字符串模块 tf.strings 中，还提供了很多常见的字符串操作方法，如小写字母 lower()方法、拼接字符串 join()方法、求字符串长度 length()方法、字符串分割 split()方法等。下面我们举一两例说明之。

```
In [18]: b_str = tf.constant(["abcdabcd bc", "abda c"])
In [19]: tf.strings.split(b_str, sep = 'd') #根据'd'的位置分割字符串
out[19]: <tf.RaggedTensor [["b'abc', b'abc', b'a bc'], [b'ab', b'a c']]]>
```

In [19] 完成的功能是把字符串向量 b_str 中的两个字符串根据字母 d 分隔开。从上面的字符串操作来看，好像 Python 中内置的字符串操作也能做到，并没有什么高明之处。的确是这样，如果处理的是简单字符串，TensorFlow 似乎并没有什么优势，但如果放眼于字符串类型张量的“批处理”操作，Python 中的字符串操作方法就“黔驴技穷”了。下面举例说明，假设我们有如下的字符串类型张量。

```
In [20]:
input_strs = [b'ten', b'eleven', b'twelve'],
[b'thirteen', b'fourteen', b'fifteen'],
[b'sixteen', b'seventeen', b'eighteen']]
```

现在，假设我们要提取 input_strs 所示字符串的子字符串，且每个子字符串的起始位置、提取长度都不一样。这类问题放在 Python 中，处理起来比较烦琐，不得不借助 for 循环逐一处理。这样一来，难以发挥张量的并行处理优势。

而在 TensorFlow 中，利用张量思维就方便得多。首先，我们把每个字符串的起始位置汇集在一起，形成位置张量。然后，再把每个字符串的提取长度也汇集在一起，形成长度张量；最后，利用 substr()方法，一行代码即可批量完成多个子字符串的提取任务，如下所示。

```
In [21]:
position = [[1, 2, 3],    #起始位置张量
[1, 2, 3],
[1, 2, 3]]
In [22]:
length = [[2, 3, 4],      #读取长度张量
[4, 3, 2],
[5, 5, 5]]
In [23]:                 #张量思维：同时处理多个字符串
tf.strings.substr(input = input_strs, pos = position, len = length)
```