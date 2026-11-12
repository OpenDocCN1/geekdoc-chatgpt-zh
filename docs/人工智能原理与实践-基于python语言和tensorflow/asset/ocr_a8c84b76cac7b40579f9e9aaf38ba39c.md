序将删除词汇表内容以便节省内存资源； print('Most common words (+UNK)', count[:5])表示程序将输出最常出现的 5 个单词； print('Sample data', data[:10], [reverse_dictionary[i] for i in data[:10]])表示程序将输出转换后的数据库 data 和原来的前 10 个单词； data_index = 0 表示程序将使用 data 来制作训练集。

# ## 7.2.2 模型结构

关于词向量的模型结构，在初始阶段，每个词汇都会被表示为一个随机向量。通过函数 generate_batch 的定义，来生成 Skip-Gram 模型用的 batch，代码如下。

```
def generate_batch(batch_size, num_skips, skip_window):
  global data_index
  assert batch_size % num_skips == 0
  assert num_skips <= 2 * skip_window
  batch = np.ndarray(shape=(batch_size), dtype=np.int32)
  labels = np.ndarray(shape=(batch_size, 1), dtype=np.int32)
  span = 2 * skip_window + 1 # [ skip_window target skip_window ]
  buffer = collections.deque(maxlen=span)

  for _ in range(span):
    buffer.append(data[data_index])
    data_index = (data_index + 1) % len(data)
  for i in range(batch_size // num_skips):
    target = skip_window # target label at the center of the buffer
    targets_to_avoid = [ skip_window ]
    for j in range(num_skips):
      while target in targets_to_avoid:
        target = random.randint(0, span - 1)
      targets_to_avoid.append(target)
      batch[i * num_skips + j] = buffer[skip_window]
      labels[i * num_skips + j, 0] = buffer[target]
      buffer.append(data[data_index])
      data_index = (data_index + 1) % len(data)

  data_index = (data_index + len(data) - span) % len(data)
  return batch, labels

batch, labels = generate_batch(batch_size=8, num_skips=2, skip_window=1)
for i in range(8):
  print(batch[i], reverse_dictionary[batch[i]], '->', labels[i, 0], reverse_dictionary
  [labels[i, 0]])
```

源程序代码内容中，global data_index 表示 data_index 是一个指针，它的初始值为 0； data_index = (data_index + 1) % len(data)表示 data_index 从当前数据位置开始，每生成一个 batch， data_index 的数值就会相应地进行加 1 操作； targets_to_avoid = [skip_window]实现样本不重复的保障目标； data_index = (data_index + 1) % len(data)表示每利用 buffer 生成 num_skips 个样本， data_index 就将进行加 1 操作； batch, labels = generate_batch(batch_size=8, num_skips=2, skip_window=1) 表示 generate_batch 函数每进行一次运行，就将产生一个 batch 以及它所对应的标签 labels，这个函数包含有 3 个参数，其中，参数 batch_size 表示的是一个 batch 中单词对的个数；num_skips=2, skip_window=1 表示程序会先选取长度为 3 的 buffer，假设它的内容是['onion', 'love', 'sampan']，单词 love 位于中间位置，onion 和 sampan 这两个单词作为单词 love 的上下文，在这两个单词中选择 num_skips 来生成标签，因为 num_skips=2，所以会将两个单词都选上，从而形成 love->onion 和 love->sampan 的形式，