
# 整数池化实现

```
 1 #ifndef TENSORFLOW_LITE_KERNELS_INTERNAL_REFERENCE_INTEGER_OPS_POOLING_H_
 2 #define TENSORFLOW_LITE_KERNELS_INTERNAL_REFERENCE_INTEGER_OPS_POOLING_H_
 3 
 4 #include "tensorflow/lite/kernels/internal/common.h"
 5 
 6 namespace tflite {
 7 namespace reference_integer_ops {
 8 
 9 inline void AveragePool(const PoolParams& params,
10                       const RuntimeShape& input_shape, const int8* input_data,
11                       const RuntimeShape& output_shape, int8* output_data) {
12     TFLITE_DCHECK_LE(params.quantized_activation_min,
13                   params.quantized_activation_max);
14     TFLITE_DCHECK_EQ(input_shape.DimensionsCount(), 4);
15     TFLITE_DCHECK_EQ(output_shape.DimensionsCount(), 4);
16     const int batches = MatchingDim(input_shape, 0, output_shape, 0);
17     const int depth = MatchingDim(input_shape, 3, output_shape, 3);
18     const int input_height = input_shape.Dims(1);
19     const int input_width = input_shape.Dims(2);
20     const int output_height = output_shape.Dims(1);
21     const int output_width = output_shape.Dims(2);
22     const int stride_height = params.stride_height;
23     const int stride_width = params.stride_width;
24     for (int batch = 0; batch < batches; ++batch) {
25         for (int out_y = 0; out_y < output_height; ++out_y) {
26             for (int out_x = 0; out_x < output_width; ++out_x) {
27                 for (int channel = 0; channel < depth; ++channel) {
28                     const int in_x_origin =
29                         (out_x * stride_width) - params.padding_values.width;
30                     const int in_y_origin =
31                         (out_y * stride_height) - params.padding_values.height;
32                     // 计算滤波器区域边界，
33                     // 确保滤波器窗口能够适应输入数据
34                     const int filter_x_start = std::max(0, -in_x_origin);
35                     const int filter_x_end =
36                         std::min(params.filter_width, input_width - in_x_origin);
37                     const int filter_y_start = std::max(0, -in_y_origin);
38                     const int filter_y_end =
39                         std::min(params.filter_height, input_height - in_y_origin);
40                     int32 acc = 0;
41                     int filter_count = 0;
42                     for (int filter_y = filter_y_start; filter_y < filter_y_end;
43                               ++filter_y) {
44                         for (int filter_x = filter_x_start; filter_x < filter_x_end;
45                                   ++filter_x) {
46                             const int in_x = in_x_origin + filter_x;
47                             const int in_y = in_y_origin + filter_y;
48                             acc +=
49                                     input_data[Offset(input_shape, batch, in_y, in_x, channel)];
50                             filter_count++;
51                         }
52                     }
53                     // 近似取整
54                     acc = acc > 0 ? (acc + filter_count / 2) / filter_count
55                                 :(acc - filter_count / 2) /filter_count;
56                     acc = std::max(acc, params.quantized_activation_min);
57                     acc = std::min(acc, params.quantized_activation_max);
58                     output_data[Offset(output_shape, batch, out_y, out_x, channel)] =
59                         static_cast<int8>(acc);
60                 }
61             }
62         }
63     }
64 }
65 
66 }     // namespace reference_integer_ops
67 }     // namespace tflite
68 
69 #endif  // TENSORFLOW_LITE_KERNELS_INTERNAL_REFERENCE_INTEGER_OPS_POOLING_H_
```

## 代码说明

第9行，定义了`AveragePool`函数，用于计算每个通道的池化结果。

第16～23行，从输入的池化层参数中获取各个参数。

第24～27行，遍历输入矩阵中的所有元素（依次遍历`Batch`、行、列），计算池化结果。

第29～30行，计算待池化矩阵中需要池化部分的起始位置。

第34～39行，计算滤波器区域边界，确保滤波器窗口能够适应输入数据。

第42～45行，遍历用于进行池化计算的滤波器（先行后列）。

第46～47行，计算当前循环中需要处理的元素的位置（`x`，`y`）。

第48～50行，将输入数据中当前位置的数据累加到`acc`中，并将`filter_count`加1。

第54～55行，得到的`acc`是当前位置窗口数据的和，需要除以窗口中的实际元素数量（`filter_count`）得到平均值，然后再近似取整，将其转换成整数。

第56～57行，将计算出来的`acc`与输出激活值的最小值、最大值进行比较，确保最后的结果在激活值的最小值与最大值之间。

第58～59行，将最后的`acc`结果转化为8位整数类型并存储在输出向量的指定位置。这样就得到了量化矩阵的池化结果，不过需要注意这里用的是平均池化而不是最大池化。如果想要实现最大池化，只需要将45～51行循环内部的累加替换成比较，获取滤波器窗口范围内的最大值即可。

最后是`softmax.h`，该文件封装了`Softmax`的量化实现。具体实现如代码清单9-11所示。

### 代码清单9-11 `softmax.h`
