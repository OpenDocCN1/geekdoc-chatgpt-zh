[PRE0]
第 249 行这里调用了 `ANeuralNetworksCompilation_setPreference`，把编译器的偏好改成 `FAST_SINGLE`，这种模式走的是单值快速输出路线。
257 到 265 行之间执行 `ANeuralNetworksCompilation_finish`，把前面所有的编译动作收尾。
267 行声明了 `Model::Compute` 成员函数，它就是负责前向推理的核心实现。
273~279 行里用 `ANeuralNetworksExecution_create` 搭建推理执行环境，传入已完成的编译结果，拿到执行对象。
281~291 行先构造 `inputTensor1`，随后通过 `fill` 把全部元素填充完毕。
293~295 行对 `inputTensor2Fd_` 做的 `mmap`，把文件映射进内存，并把指针赋给 `inputTensorPtr2`。
296~299 行遍历该指针指向的缓冲区，逐字节写入 `inputValue2` 来完成初始化。
第 300 行马上调用 `munmap` 把 `inputTensorPtr2` 对应的映射解掉。
302~309 行用 `ANeuralNetworksExecution_setInputFromMemory` 把执行环境的输入指定为 `memoryInput2_`，设置失败会打印错误并返回 `false`。
311~318 行用 `ANeuralNetworksExecution_setOutputFromMemory` 把执行环境的输出指向 `memoryOutput_`，失败同样报错返回 `false`。
320~326 行先定义一个 `ANeuralNetworksEvent` 类型的指针 `event`，然后调用 `ANeuralNetworksExecution_startCompute` 启动计算并把事件句柄存进 `event`，失败则报错退出。
328~333 行通过 `ANeuralNetworksEvent_wait` 在 `event` 上阻塞等待推理结束，等待失败也会输出错误并返回 `false`。
335~336 行分别用 `ANeuralNetworksEvent_free` 与 `ANeuralNetworksExecution_free` 释放事件和执行对象。
338~342 行再次 `mmap`，把 `outputTensorFd_` 映射到内存，指针赋给 `outputTensorPtr`，准备通过它把结果拉回内存。
343~351 行遍历输出缓冲区，把每个值和阈值做对比，大于阈值的直接打印出来。
352~354 行把输出缓冲区的首个结果赋给 `result`，随后 `munmap` 解除映射，最后把结果指针返回。
第 357 行定义了 `Model` 的析构函数 `~Model()`，内部依次调用 NNAPI 的释放接口清理资源，并用 `close` 关闭输入、输出和模型文件的文件描述符。

长篇累牍的模型定义到此打住，整体看下来能很直观感受到 `NNAPI` 本质上是一个非常原始、贴近底层的接口，和之前聊到的 `Core ML` 相比差距明显。根源在于两者定位就不一样，也正好体现出开发 iOS/iPadOS 应用和开发 Android 应用在工程体验上的鲜明分野，是两家风格迥异的大厂为全球开发者提供的技术路径。

### 12.2.4 JNI 封装桥梁
Android 应用的业务层基本都是 Java 来写，直接暴露给 Java 用的 C++ 接口是不通的，必须借助 JNI 做一层封装。所以接下来要把上面写好的 C++ 代码包起来，给 Java 类提供可调用的入口，具体实现放在 `nn_sample.cpp` 里，示例见代码清单 12-8。

#### 代码清单 12-8 `nn_sample.cpp`