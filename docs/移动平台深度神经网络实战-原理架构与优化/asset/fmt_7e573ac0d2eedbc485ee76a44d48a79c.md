
```
249     status = ANeuralNetworksCompilation_setPreference(compilation_,
250                                                      ANEURALNETWORKS_PREFER_FAST_SINGLE_ANSWER);
251     if (status != ANEURALNETWORKS_NO_ERROR) {
252         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
253                             "ANeuralNetworksCompilation_setPreference failed");
254         return false;
255     }
256 
257     status = ANeuralNetworksCompilation_finish(compilation_);
258     if (status != ANEURALNETWORKS_NO_ERROR) {
259         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
260                             "ANeuralNetworksCompilation_finish failed");
261         return false;
262     }
263 
264     return true;
265 }
266 
267 bool Model::Compute(float inputValue1, float inputValue2,
268                           float *result) {
269     if (!result) {
270         return false;
271     }
272 
273     ANeuralNetworksExecution *execution;
274     int32_t status = ANeuralNetworksExecution_create(compilation_, &execution);
275     if (status != ANEURALNETWORKS_NO_ERROR) {
276         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
277                             "ANeuralNetworksExecution_create failed");
278         return false;
279     }
280 
281     std::fill(inputTensor1_.data(), inputTensor1_.data() + tensorSize_,
282               inputValue1);
283 
284     status = ANeuralNetworksExecution_setInput(execution, 0, nullptr,
285                                                inputTensor1_.data(),
286                                                tensorSize_ * sizeof(float));
287     if (status != ANEURALNETWORKS_NO_ERROR) {
288         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
289                             "ANeuralNetworksExecution_setInput failed for input1");
290         return false;
291     }
292 
293     float *inputTensor2Ptr = reinterpret_cast<float *>(mmap(nullptr, tensorSize_ *sizeof(float),
294                                                   PROT_READ | PROT_WRITE, MAP_SHARED,
295                                                   inputTensor2Fd_, 0));
296     for (int i = 0; i < tensorSize_; i++) {
297         *inputTensor2Ptr = inputValue2;
298         inputTensor2Ptr++;
299     }
300     munmap(inputTensor2Ptr, tensorSize_ * sizeof(float));
301 
302     status = ANeuralNetworksExecution_setInputFromMemory(execution, 1, nullptr,
303                                                          memoryInput2_, 0,
304                                                          tensorSize_ * sizeof(float));
305     if (status != ANEURALNETWORKS_NO_ERROR) {
306         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
307                             "ANeuralNetworksExecution_setInputFromMemory failed for input2");
308         return false;
309     }
310 
311     status = ANeuralNetworksExecution_setOutputFromMemory(execution, 0, nullptr,
312                                                           memoryOutput_, 0,
313                                                           tensorSize_ * sizeof(float));
314     if (status != ANEURALNETWORKS_NO_ERROR) {
315         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
316                             "ANeuralNetworksExecution_setOutputFromMemory failed for output");
317         return false;
318     }
319 
320     ANeuralNetworksEvent *event = nullptr;
321     status = ANeuralNetworksExecution_startCompute(execution, &event);
322     if (status != ANEURALNETWORKS_NO_ERROR) {
323         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
324                             "ANeuralNetworksExecution_startCompute failed");
325         return false;
326     }
327 
328     status = ANeuralNetworksEvent_wait(event);
329     if (status != ANEURALNETWORKS_NO_ERROR) {
330         __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
331                             "ANeuralNetworksEvent_wait failed");
332         return false;
333     }
334 
335     ANeuralNetworksEvent_free(event);
336     ANeuralNetworksExecution_free(execution);
337 
338     const float goldenRef = (inputValue1 + 0.5f) * (inputValue2 + 0.5f);
339     float *outputTensorPtr = reinterpret_cast<float *>(mmap(nullptr,
340                                                        tensorSize_ *sizeof(float),
341                                                         PROT_READ, MAP_SH-ARED,
342                                                    outputTensorFd_, 0));
343     for (int32_t idx = 0; idx < tensorSize_; idx++) {
344         float delta = outputTensorPtr[idx] - goldenRef;
345         delta = (delta < 0.0f) ? (-delta) : delta;
346         if (delta > FLOAT_EPISILON) {
347             __android_log_print(ANDROID_LOG_ERROR, LOG_TAG,
348                                 "Output computation Error: output0(%f), delta(%f) @ idx(%d)",
349                                 outputTensorPtr[0], delta, idx);
350         }
351     }
352     *result = outputTensorPtr[0];
353     munmap(outputTensorPtr, tensorSize_ * sizeof(float));
354     return result;
355 }
356 
357 Model::~Model() {
358     ANeuralNetworksCompilation_free(compilation_);
359     ANeuralNetworksModel_free(model_);
360     ANeuralNetworksMemory_free(memoryModel_);
361     ANeuralNetworksMemory_free(memoryInput2_);
362     ANeuralNetworksMemory_free(memoryOutput_);
363     close(inputTensor2Fd_);
364     close(outputTensorFd_);
365     close(modelDataFd_);
366 }
```

第249行，调用`ANeuralNetworksCompilation_setPreference`将模型的输出方式设置为`FAST_SINGLE`模型，是一种只支持单值输出的快速输出方式。

第257～265行，调用`ANeuralNetworksCompilation_finish`完成整个编译。

第267行，定义了`Model::Compute`成员函数，作为前向计算的实现。

第273～279，调用`ANeuralNetworksExecution_create`创建整个神经网络的执行环境，输入是已编译结果，输出是执行环境对象。

第281～291，定义了`inputTensor1`，调用`fill`将所有的元素都处理完毕。

第293～295行，调用`mmap`，将`inputTensor2Fd_`对应的文件映射到内存中，并将内存指针赋值给`inputTensorPtr2`。

第296～299行，遍历`inputTensorPtr2`指向的内存缓冲区，将缓冲区逐字节填充为`inputValue2`，完成初始化。

第300行，调用`munmap`将`inputTensorPtr2`指向的内存缓冲区解除映射。

第302～309行，调用`ANeuralNetworksExecution_setInputFromMemory`为执行环境设定输入，这里以`memoryInput2_`作为内存数据输入。如果设置输入失败，则输出错误信息并返回`false`。

第311～318行，调用`ANeuralNetworksExecution_setOutputFromMemory`为执行环境设定输出，这里以`memoryOutput_`作为内存数据输入。如果设置输出失败，则输出错误信息并返回`false`。

第320～326行，定义`ANeuralNetworksEvent`类型指针`event`，调用`ANeuralNetworksExecution_startCompute`，在执行环境中启动计算，并将事件存储到`event`指针中。如果执行失败则输出错误信息并返回`false`。

第328～333行，调用`ANeuralNetworksEvent_wait`，在`event`指针上等待网络计算结束。如果等待失败，则输出错误信息并返回`false`。

第335～336行，分别调用`ANeuralNetworksEvent_free`和`ANeuralNetworksExecution_free`释放`event`指针和`execution`指针。

第338～342行，调用`mmap`，将`outputTensorFd_`对应的文件映射到内存中，并将内存指针赋值给`outputTensorPtr`，用于通过输出缓冲区指针将输出数据加载到内存中。

第343～351行，遍历输出缓冲区，将输出缓冲区和阈值进行比较，如果输出的结果比阈值大，则将结果打印出来。

第352～354行，将输出缓冲区的第一个结果赋值给`result`，调用`munmap`将输出缓冲区解除映射，最后将结果指针返回。

第357行，定义了`Model`的析构函数`~Model()`，在析构函数中，调用NNAPI的资源释放函数逐个释放资源并调用`close`关闭输入数据、输出数据和模型数据的文件描述符。

好了，漫长的模型定义和实现到此结束，到这里我们其实有一个清晰的认识，即`NNAPI`作为一个十分原始和底层的API支持，相比我们在前面所了解的`Core ML`是天壤之别。究其主要原因是这两者的定位本身就不同，同时也能看到开发`iOS`（包括`iPadOS`）应用程序和开发`Android`应用程序上的一个显著差异，由两个特色极为鲜明的大型企业为全球开发者提供的技术。

### 12.2.4 JNI封装与调用

我们都知道`Android` App需要使用`Java`开发，因此`C++`开发的接口是无法直接提供给`Java`使用的，需要通过`JNI`进行封装才行。因此我们需要使用`JNI`封装我们刚刚编写的`C++`代码，为`Java`类提供对应的接口，具体代码在`nn_sample.cpp`中，代码如代码清单12-8所示。

#### 代码清单12-8 `nn_sample.cpp`
