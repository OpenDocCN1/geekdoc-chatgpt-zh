[PRE0]

## 关键流程拆解

194～206 行先组装出 `add2InputOperands` 数组，用来收纳 `add2` 所需的操作数，紧接着执行 `addOperation` 把一个加法算子写进模型里，并把该数组中的元素接入为输入端，同时把 `intermediateOutput1` 指定为该算子的输出端。

208～219 行的做法类似，先搭建 `mulInputOperands` 数组来描述 `MUL` 的输入需求，再调用 `addOperation` 在网络中新增一个乘法算子，将 `mulInputOperands` 里的操作数绑定为输入，将 `multiplierOutput` 绑定为输出。

221～223 行创建了 `modelInputOperands` 数组来明确模型入口的操作数，随后通过 `identifyInputsAndOutputs` 把这组操作数确立为整个模型的输入，并把 `multiplierOutput` 设为整个模型的最终输出。

235～240 行执行 `ANeuralNetworksModel_finish`，标志着模型结构的搭建到此结束。

242～247 行调用 `ANeuralNetworksCompilation_create`，以 `model_` 中的网络定义为蓝本生成编译对象 `compilation_`，并保存编译结果。