
```
 1 #ifndef TENSORFLOW_LITE_KERNELS_INTERNAL_REFERENCE_INTEGER_OPS_SOFTMAX_H_
 2 #define TENSORFLOW_LITE_KERNELS_INTERNAL_REFERENCE_INTEGER_OPS_SOFTMAX_H_
 3 
 4 #include "tensorflow/lite/kernels/internal/common.h"
 5 
 6 namespace tflite {
 7 namespace reference_integer_ops {
 8 
 9 // 输入输出为int8类型的量化版本softmax
10 inline void Softmax(const SoftmaxParams& params,
11                 const RuntimeShape& input_shape, const int8* input_data,
12                 const RuntimeShape& output_shape, int8* output_data) {
13     const int32 input_beta_multiplier = params.input_multiplier;
14     const int32 input_beta_left_shift = params.input_left_shift;
15     const int diff_min = params.diff_min;
16 
17     static const int kScaledDiffIntegerBits = 5;
18     static const int kAccumulationIntegerBits = 12;
19     using FixedPointScaledDiff =
20         gemmlowp::FixedPoint<int32, kScaledDiffIntegerBits>;
21     using FixedPointAccum = gemmlowp::FixedPoint<int32, kAccumulationIntegerBits>;
22     using FixedPoint0 = gemmlowp::FixedPoint<int32, 0>;
23 
24     const int trailing_dim = input_shape.DimensionsCount() - 1;
25     const int outer_size =
26             MatchingFlatSizeSkipDim(input_shape, trailing_dim, output_shape);
27     const int depth =
28             MatchingDim(input_shape, trailing_dim, output_shape, trailing_dim);
29 
30     for (int i = 0; i < outer_size; ++i) {
31         int8 max_in_row = -128;
32         for (int c = 0; c < depth; ++c) {
33             max_in_row = std::max(max_in_row, input_data[i * depth + c]);
34         }
35 
36         FixedPointAccum sum_of_exps = FixedPointAccum::Zero();
37         for (int c = 0; c < depth; ++c) {
38             int32 input_diff =
39                     static_cast<int32>(input_data[i * depth + c]) - max_in_row;
40             if (input_diff >= diff_min) {
41                 const int32 input_diff_rescaled =
42                     MultiplyByQuantizedMultiplierGreaterThanOne(
43                         input_diff, input_beta_multiplier, input_beta_left_shift);
44                 const FixedPointScaledDiff scaled_diff_f8 =
45                     FixedPointScaledDiff::FromRaw(input_diff_rescaled);
46                 sum_of_exps = sum_of_exps + gemmlowp::Rescale<kAccumulation IntegerBits>(
47                                         exp_on_negative_values(scaled_diff_f8));
48             }
49         }
50 
51         int num_bits_over_unit;
52         FixedPoint0 shifted_scale = FixedPoint0::FromRaw(GetReciprocal(
53             sum_of_exps.raw(), kAccumulationIntegerBits, &num_bits_over_unit));
54 
55         for (int c = 0; c < depth; ++c) {
56             int32 input_diff =
57                 static_cast<int32>(input_data[i * depth + c]) - max_in_row;
58             if (input_diff >= diff_min) {
59                 const int32 input_diff_rescaled =
60                     MultiplyByQuantizedMultiplierGreaterThanOne(
61                         input_diff, input_beta_multiplier, input_beta_left_shift);
62                 const FixedPointScaledDiff scaled_diff_f8 =
63                     FixedPointScaledDiff::FromRaw(input_diff_rescaled);
64 
65                 FixedPoint0 exp_in_0 = exp_on_negative_values(scaled_diff_f8);
66                 const int32 unsat_output = gemmlowp::RoundingDivideByPOT(
67                     (shifted_scale * exp_in_0).raw(), num_bits_over_unit+ 31 - 8);
68                 const int32 shifted_output = unsat_output - 128;
69 
70                 output_data[i * depth + c] = static_cast<int8>(
71                     std::max(std::min(shifted_output, static_cast<int32>(127)),
72                            static_cast<int32>(-128)));
73 
74             } else {
75                 output_data[i * depth + c] = -128;
76             }
77         }
78     }
79 }
80 
81 }  // namespace reference_integer_ops
82 }  // namespace tflite
83 
84 #endif  // TENSORFLOW_LITE_KERNELS_INTERNAL_REFERENCE_INTEGER_OPS_SOFTMAX_H_
```
第10行，定义了`Softmax()`函数，用于根据8位整型输入输出`Softmax`激活值。
第13～15行，从输入的`SoftmaxParams`参数中获取各个参数。
第70～75行，将最后的`acc`结果转化为8位整数类型并存储在输出向量的指定位置。这样就完成了`Softmax()`计算。

## 9.5.3　量化矩阵计算

需要注意的是，在层实现中都调用了`MultiplyByQuantizedMultiplier()`函数用来执行量化后的矩阵乘法，因此该函数就是量化加速的实现核心。该函数定义在TensorFlow Lite的`common.h`文件内，这个函数的定义如代码清单9-12所示。

### 代码清单9-12　TensorFlow Lite `common.h`中 `MultiplyByQuantizedMultiplier()`函数的定义
```
inline int32 MultiplyByQuantizedMultiplier(int32 x, int32 quantized_multiplier,
                                           int shift) {
    using gemmlowp::RoundingDivideByPOT;
    using gemmlowp::SaturatingRoundingDoublingHighMul;
    int left_shift = shift > 0 ? shift : 0;
    int right_shift = shift > 0 ? 0 : -shift;
    return RoundingDivideByPOT(SaturatingRoundingDoublingHighMul(
                                 x * (1 << left_shift), quantized_multiplier),
                             right_shift);
}
```
可以看到，该函数调用了`RoundingDivideByPOT()`和`SaturatingRoundingDoublingHighMul()`两个函数，这两个函数并不是由TensorFlow Lite实现的，而是使用了Google的`gemmlowp`库，该库实现了强大的定点数计算，而且为ARM等嵌入式平台提供了加速功能，我们看一下`gemmlowp`中是如何实现加速的。

我们用到的函数定义在`fixedpoint.h`中，其中`SaturatingRoundingDoublingHighMul()`函数的代码实现如代码清单9-13所示。

### 代码清单9-13　`SaturatingRoundingDoublingHighMul()`函数实现
```
319 template <typename IntegerType>
320 IntegerType SaturatingRoundingDoublingHighMul(IntegerType a, IntegerType b) {
321     static_assert(std::is_same<IntegerType, void>::value, "unimplemented");
322     (void)b;
323     return a;
324 }
325 
326 // 该函数实现了与ARMv7 NEON VQRDMULH指令相同的
327 // 计算功能
328 template <>
329 inline std::int32_t SaturatingRoundingDoublingHighMul(std::int32_t a,
330                                              std::int32_t b) {
331     bool overflow = a == b && a == std::numeric_limits<std::int32_t>::min();
332     std::int64_t a_64(a);
333     std::int64_t b_64(b);
334     std::int64_t ab_64 = a_64 * b_64;
335     std::int32_t nudge = ab_64 >= 0 ? (1 << 30) : (1 - (1 << 30));
336     std::int32_t ab_x2_high32 =
337             static_cast<std::int32_t>((ab_64 + nudge) / (1ll << 31));
338     return overflow ? std::numeric_limits<std::int32_t>::max() : ab_x2_high32;
339 }
340 
341 template <>
342 inline std::int16_t SaturatingRoundingDoublingHighMul(std::int16_t a,
343                                                 std::int16_t b) {
344     bool overflow = a == b && a == std::numeric_limits<std::int16_t>::min();
345     std::int32_t a_32(a);
346     std::int32_t b_32(b);
347     std::int32_t ab_32 = a_32 * b_32;
348     std::int16_t nudge = ab_32 >= 0 ? (1 << 14) : (1 - (1 << 14));
349     std::int16_t ab_x2_high16 =
350             static_cast<std::int16_t>((ab_32 + nudge) / (1 << 15));
351     return overflow ? std::numeric_limits<std::int16_t>::max() : ab_x2_high16;
352 }
```
第329～339行，实现了32位整数的双倍乘法操作，具体来说，首先是将`a`与`b`相乘，然后乘以2，如果结果溢出，那么就取32位整数的最大值。
第341～352行，实现了16位整数的双倍乘法操作，具体来说，首先是将`a`与`b`相乘，然后乘以2，如果结果溢出，那么就取16位整数的最大值。

`RoundingDivideByPOT()`函数的代码实现如代码清单9-14所示。

### 代码清单 9-14　`RoundingDivideByPOT()`函数实现
```
356 template <typename IntegerType, typename ExponentType>
357 inline IntegerType RoundingDivideByPOT(IntegerType x, ExponentType exponent) {
358     assert(exponent >= 0);
359     assert(exponent <= 31);
360     const IntegerType mask = Dup<IntegerType>((1ll << exponent) - 1);
361     const IntegerType zero = Dup<IntegerType>(0);
362     const IntegerType one = Dup<IntegerType>(1);
363     const IntegerType remainder = BitAnd(x, mask);
364     const IntegerType threshold =
365         Add(ShiftRight(mask, 1), BitAnd(MaskIfLessThan(x, zero), one));
366     return Add(ShiftRight(x, exponent),
367              BitAnd(MaskIfGreaterThan(remainder, threshold), one));
368 }
```
我们看到该函数调用了`BitAnd()`、`Add()`、`ShiftRight()`、`MaskIfGreaterThan()`等函数，这些函数被`gemmlowp`封装在不同的实现中，比如AVX的实现封装在`fixedpoint_avx.h`中，Neon的实现封装在`fixedpoint_neon.h`中，比如我们现在关心的是ARM中的实现，就可以看到`fixedpoint_neon.h`中对这几个函数的定义。代码实现如代码清单9-15至代码清单9-18所示。

### 代码清单9-15　`BitAnd`定义
