

由于不需要支持Windows平台，我们裁剪掉了所有与Windows相关的部分，只留下了核心的构建配置。

### 11.2　集成TensorFlow Lite

集成TensorFlow Lite非常简单，只需要复制TensorFlow Lite的所有文件，然后链接`Bazel`生成的`.a`文件即可。我们现在来编写TensorFlow Lite的调用代码`main.cpp`，如代码清单11-3所示。

代码清单11-3　调用TensorFlow Lite的`main.cpp`

```
 1 #include <cstdio>
 2 #include "tensorflow/lite/interpreter.h"
 3 #include "tensorflow/lite/kernels/register.h"
 4 #include "tensorflow/lite/model.h"
 5 #include "tensorflow/lite/optional_debug_tools.h"
 6 
 7 using namespace TensorFlow Lite;
 8 
 9 #define TENSORFLOW LITE_MINIMAL_CHECK(x)                              \
10     if (!(x)) {                                                \
11         fprintf(stderr, "Error at %s:%d\n", __FILE__, __LINE__); \
12         exit(1);                                                 \
13     }
14 
15 int main(int argc, char* argv[]) {
16   if(argc != 2) {
17      fprintf(stderr, "minimal <TensorFlow Lite model>\n");
18      return 1;
19     }
20     const char* filename = argv[1];
21 
22     std::unique_ptr<TensorFlow Lite::FlatBufferModel> model =
23         TensorFlow Lite::FlatBufferModel::BuildFromFile(filename);
24     TENSORFLOW LITE_MINIMAL_CHECK(model != nullptr);
25 
26     TensorFlow Lite::ops::builtin::BuiltinOpResolver resolver;
27     InterpreterBuilder builder(*model, resolver);
28     std::unique_ptr<Interpreter> interpreter;
29     builder(&interpreter);
30     TENSORFLOW LITE_MINIMAL_CHECK(interpreter != nullptr);
31 
32     TENSORFLOW LITE_MINIMAL_CHECK(interpreter->AllocateTensors() == kTensorFlow LiteOk);
33     printf("=== Pre-invoke Interpreter State ===\n");
34     TensorFlow Lite::PrintInterpreterState(interpreter.get());
35 
36     TENSORFLOW LITE_MINIMAL_CHECK(interpreter->Invoke() == kTensorFlow LiteOk);
37     printf("\n\n=== Post-invoke Interpreter State ===\n");
38     TensorFlow Lite::PrintInterpreterState(interpreter.get());
39 
40     return 0;
41 }
```

该文件会读取我们指定的模型文件并执行模型，最后输出预测的结果。我们现在已经了解了如何构建使用TensorFlow Lite，接下来对TensorFlow Lite进行进一步分析，以指导对TensorFlow Lite的改造与裁剪。由于TensorFlow Lite代码非常庞杂，我们只抽取最关键部分的代码进行解释，同时讲解一下如何扩展操作符并针对特定平台优化的方法。

### 11.3　核心实现分析

在了解了TensorFlow Lite的构建方法和简单集成方法后，我们现在来深入分析一下TensorFlow Lite的核心代码，充分了解嵌入式框架的实现原理和技巧。

#### 11.3.1　解释器代码分析

使用TensorFlow Lite的时候我们需要先创建一个解释器，然后加载模型，生成图与操作符实例，最后输入数据，完成前向传播过程。因此，整个TensorFlow Lite的入口就是解释器，这里我们先讲解一下TensorFlow Lite的解释器代码。

解释器声明在`interpreter.h`中，如代码清单11-4所示。

代码清单11-4　TensorFlow Lite `interpreter.h`

