模型，并将其用作创建更强大模型的基础，这个过程称为迁移学习 — 将现有模型的知识转换为新模型。Keras模型中的save方法以HDF5 (Hierarchical Data Format) 的格式存储模型的体系结构和状态信息。默认情况下，此类文件使用.h5文件扩展名：

```
[51]: cnn.save('mnist_cnn.h5')
```

可以使用tensorflow.keras.models模块中的load_model函数加载已保存的模型：

```
from tensorflow.keras.models import load_model
cnn = load_model('mnist_cnn.h5')
```

然后，可以调用其fit函数。例如，如果已获得更多数据，则可以调用predict函数对新数据额外进行预测，或者可以调用fit函数使用额外的数据对该模型开始训练。

Keras还提供了几个附加功能来保存和加载模型的各个方面，更多信息，请参阅https://keras.io/getting-started/faq/#how-can-i-save-a-keras-model。