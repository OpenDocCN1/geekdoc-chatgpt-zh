TensorFlow Lite 内置加法算子的执行路径是按输出数据类型来分流的，量化与非量化走的是两条不同的准备流程。

在 uint8 量化分支里，代码会先把来自 OpData 的第二个输入乘数、第二个输入移位、输出偏移、输出乘数、输出移位等五个标量全部填进 ArithmeticParams 结构体，随后调用 SetActivationParams 把输出激活的下限和上限写入。接着用 optimized_ops::ProcessBroadcastShapes 拿两个输入的张量形状去判断是否需要广播，得到一个布尔标记。为了统一调用，定义了一个 TF_LITE_ADD 宏，把参数、两个输入的形状与 uint8 数据、输出的形状与 uint8 数据一起传给 reference_ops 的实现。判断结果为真时走 BroadcastAdd4DSlow，否Selection 时走普通的 Add，宏用完后立即取消定义。int16 量化分支的思路类似但更简洁：重新构造一个 ArithmeticParams，只设置 input1_shift、input2_shift 并写入激活范围，然后直接用 reference_ops 的 Add 处理 int16 数据。这里注释说明量化版的 Add 不支持激活，因此实际总是采用广播路径。两个量化分支最后都返回 kTensorFlow LiteOk。

Eval 入口负责调度。首先把节点里的 builtin_data 解释为 TensorFlow LiteAddParams，把 user_data 解释为 OpData，再取出输入一、输入二和输出张量。输出类型是 Float32 或 Int32 时，直接调用 EvalAdd；输出类型是 UInt8 或 Int16 时，通过 TF_LITE_ENSURE_OK 调用 EvalAddQuantized。其它类型会调用 context->ReportError 提示 “Inputs and outputs not all float|uint8|int16 types.” 并返回 kTensorFlow LiteError，正常路径返回 kTensorFlow LiteOk。

算子注册部分在 add 命名空间下提供 Register_ADD，返回一个静态的 TensorFlow LiteRegistration 实例，Init、Free、Prepare、Eval 四个回调全部指向 add 命名空间的同名函数，随后依次关闭 add、builtin、ops、TensorFlow Lite 命名空间。

[PRE0]cpp

#include "tensorflow/lite/kernels/register.h"

#include "tensorflow/lite/util.h"

namespace TensorFlow Lite {

namespace ops {

namespace custom {

TensorFlow LiteRegistration* Register_AUDIO_SPECTROGRAM();

TensorFlow LiteRegistration* Register_MFCC();

TensorFlow LiteRegistration* Register_DETECTION_POSTPROCESS();

TensorFlow LiteRegistration* Register_IF();

}  // namespace custom

namespace builtin {

TensorFlow LiteRegistration* Register_ABS();

TensorFlow LiteRegistration* Register_RELU();

TensorFlow LiteRegistration* Register_RELU_N1_TO_1();

TensorFlow LiteRegistration* Register_RELU6();

TensorFlow LiteRegistration* Register_TANH();

TensorFlow LiteRegistration* Register_LOGISTIC();

TensorFlow LiteRegistration* Register_AVERAGE_POOL_2D();

TensorFlow LiteRegistration* Register_MAX_POOL_2D();

TensorFlow LiteRegistration* Register_L2_POOL_2D();

TensorFlow LiteRegistration* Register_CONV_2D();

TensorFlow LiteRegistration* Register_DEPTHWISE_CONV_2D();

TensorFlow LiteRegistration* Register_SVDF();

TensorFlow LiteRegistration* Register_RNN();

TensorFlow LiteRegistration* Register_BIDIRECTIONAL_SEQUENCE_RNN();

TensorFlow LiteRegistration* Register_UNIDIRECTIONAL_SEQUENCE_RNN();

TensorFlow LiteRegistration* Register_EMBEDDING_LOOKUP();

TensorFlow LiteRegistration* Register_EMBEDDING_LOOKUP_SPARSE();

TensorFlow LiteRegistration* Register_FULLY_CONNECTED();

TensorFlow LiteRegistration* Register_LSH_PROJECTION();

TensorFlow LiteRegistration* Register_HASHTABLE_LOOKUP();

TensorFlow LiteRegistration* Register_SOFTMAX();

TensorFlow LiteRegistration* Register_CONCATENATION();

TensorFlow LiteRegistration* Register_ADD();

TensorFlow LiteRegistration* Register_SPACE_TO_BATCH_ND();

TensorFlow LiteRegistration* Register_DIV();

TensorFlow LiteRegistration* Register_BATCH_TO_SPACE_ND();

TensorFlow LiteRegistration* Register_MUL();

TensorFlow LiteRegistration* Register_L2_NORMALIZATION();

TensorFlow LiteRegistration* Register_LOCAL_RESPONSE_NORMALIZATION();

TensorFlow LiteRegistration* Register_LSTM();

TensorFlow LiteRegistration* Register_BIDIRECTIONAL_SEQUENCE_LSTM();

TensorFlow LiteRegistration* Register_UNIDIRECTIONAL_SEQUENCE_LSTM();

TensorFlow LiteRegistration* Register_PAD();

TensorFlow LiteRegistration* Register_PADV2();