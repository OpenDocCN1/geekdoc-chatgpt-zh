
# TensorFlow Lite Tensor Utils 头文件

```
 1 #ifndef TENSORFLOW_LITE_KERNELS_INTERNAL_TENSOR_UTILS_H_
 2 #define TENSORFLOW_LITE_KERNELS_INTERNAL_TENSOR_UTILS_H_
 3 
 4 #include "tensorflow/lite/c/builtin_op_data.h"
 5 
 6 #if defined(_MSC_VER)
 7 #define __restrict__ __restrict
 8 #endif
 9 
10 namespace tflite {
11 namespace tensor_utils {
12 
13 float Clip(float f, float abs_limit);
14 
15 bool IsZeroVector(const float* vector, int v_size);
16 
17 void SymmetricQuantizeFloats(const float* values, const int size,
18                              int8_t* quantized_values, float* min_value,
19                              float* max_value, float* scaling_factor);
20 
21 void MatrixBatchVectorMultiplyAccumulate(const float* matrix, int m_rows,
22                                          int m_cols, const float* vector,
23                                          int n_batch, float* result,
24                                          int result_stride);
25 
26 void SparseMatrixBatchVectorMultiplyAccumulate(
27     const float* matrix, const uint8_t* ledger, int m_rows, int m_cols,
28     const float* vector, int n_batch, float* result, int result_stride);
29 
30 void MatrixBatchVectorMultiplyAccumulate(
31     const int8_t* __restrict__ matrix, const int m_rows, const int m_cols,
32     const int8_t* __restrict__ vectors, const float* scaling_factors,
33     int n_batch, float* __restrict__ result, int result_stride);
34 
35 void SparseMatrixBatchVectorMultiplyAccumulate(
36     const int8_t* __restrict__ matrix, const uint8_t* ledger, const int m_rows,
37     const int m_cols, const int8_t* __restrict__ vectors,
38     const float* scaling_factors, int n_batch, float* __restrict__ result,
39     int result_stride);
40 
41 void VectorVectorCwiseProduct(const float* vector1, const float* vector2,
42                               int v_size, float* result);
43 
44 void VectorVectorCwiseProductAccumulate(const float* vector1,
45                                         const float* vector2, int v_size,
46                                         float* result);
47 
48 float VectorVectorDotProduct(const float* vector1, const float* vector2,
49                              int v_size);
50 
51 void BatchVectorBatchVectorDotProduct(const float* vector1,
52                                       const float* vector2, int v_size,
53                                       int n_batch, float* result,
54                                       int result_stride);
55 
56 void VectorBatchVectorCwiseProduct(const float* vector, int v_size,
57                                    const float* batch_vector, int n_batch,
58                                    float* result);
59 
60 void VectorBatchVectorCwiseProductAccumulate(const float* vector, int v_size,
61                                              const float* batch_vector,
62                                              int n_batch, float* result);
63 
64 void VectorBatchVectorAdd(const float* vector, int v_size, int n_batch,
65                           float* batch_vector);
66 
67 void VectorBatchVectorAssign(const float* vector, int v_size, int n_batch,
68                              float* batch_vector);
69 
70 void ApplySigmoidToVector(const float* vector, int v_size, float* result);
71 
72 void ApplyActivationToVector(const float* vector, int v_size,
73                              TfLiteFusedActivation activation, float* result);
74 
75 void CopyVector(const float* vector, int v_size, float* result);
76 
77 void Sub1Vector(const float* vector, int v_size, float* result);
78 
79 void ZeroVector(float* vector, int v_size);
80 
81 void VectorScalarMultiply(const int8_t* vector, int v_size, float scale,
82                           float* result);
83 
84 void ClipVector(const float* vector, int v_size, float abs_limit,
85                 float* result);
86 
87 void VectorShiftLeft(float* vector, int v_size, float shift_value);
88 
89 void ReductionSumVector(const float* input_vector, float* output_vector,
90                         int output_size, int reduction_size);
91 
92 void MeanStddevNormalization(const float* input_vector, float* output_vector,
93                              int v_size, int n_batch,
94                              float normalization_epsilon);
95 }  // namespace tensor_utils
96 }  // namespace tflite
97 
98 #endif  // TENSORFLOW_LITE_KERNELS_INTERNAL_TENSOR_UTILS_H_
```

## 函数说明

第13行，声明了`Clip`函数，该函数的作用是进行输入转换，确保输入参数在-`abs_limit`和+`abs_limit`之间。
第15行，声明了`IsZeroVector`函数，该函数的作用是检查向量中的数字是否全部为0。
第17行，声明了`SymmetricQuantizeFloats`函数，该函数的作用是使用对称量化方法（没有偏置的线性量化）量化浮点数缓冲区，将其转换为8位整数数组，同时输出浮点数缓冲区的最小值和最大值，以及量化缩放系数。
第21行，声明了`MatrixBatchVectorMultiplyAccumulate`函数，该函数的作用是将组成一个多维向量的一批矩阵和另一个矩阵相乘，并将每个矩阵的乘法计算结果累加到用户传递的结果缓冲区中。
第26行，声明了`SparseMatrixBatchVectorMultiplyAccumulate`函数，该函数的作用和`MatrixBatchVectorMultiplyAccumulate`类似，唯一区别是该函数的输入矩阵是采用压缩系数矩阵方式存储中的稀疏矩阵，因此计算方法也就和普通矩阵有所区别。
第30行，声明了`MatrixBatchVectorMultiplyAccumulate`函数，该函数的作用与`MatrixBatchVectorMultiplyAccumulate`一样，唯一区别在于该函数的输入是采用量化算法处理后的整型输入，因此调用者除了传入缓冲区还需要传递一个缩放因子，最后的输出是浮点类型的缓冲区。
第35行，声明了`SparseMatrixBatchVectorMultiplyAccumulate`函数，该函数的作用与`SparseMatrixBatchVectorMultiplyAccumulate`相同，唯一区别在于该函数的输入是采用量化算法处理后的整型输入，因此调用者不仅需要传入缓冲区还需要传递一个缩放因子，最后的输出是浮点类型的缓冲区。
第41行，声明了`VectorVectorCwiseProduct`函数，该函数的作用是计算两个向量的系数乘积。
第44行，声明了`VectorVectorCwiseProductAccumulate`函数，该函数的作用是计算两个向量的系数乘积，并累加到结果向量中，要求输出缓冲区的数据必须是合法数据。
第48行，声明了`VectorVectorDotProduct`函数，该函数的作用是计算两个向量的点乘结果。
第51行，声明了`BatchVectorBatchVectorDotProduct`函数，该函数的作用是计算两个批次向量的点乘结果，并将点乘结果存储到目标缓冲区中。
第56行，声明了`VectorBatchVectorCwiseProduct`函数，该函数的作用是计算两个批次向量的系数乘积，并将结果存储到目标缓冲区中。
第60行，声明了`VectorBatchVectorCwiseProductAccumulate`函数，该函数的作用是计算两个批次向量的系数乘积，并累加到结果向量中，要求输出缓冲区的数据必须是合法数据。
第64行，声明了`VectorBatchVectorAdd`函数，该函数的作用是逐一计算一批向量中每个向量与另一个向量的加法。
第67行，声明了`VectorBatchVectorAssign`函数，该函数的作用是使用某一个向量初始化一个批次的向量（所有向量都等于我们给定的向量）。
第70行，声明了`ApplySigmoidToVector`函数，该函数的作用是计算向量中每个元素的Sigmoid的结果。
第72行，声明了`ApplyActivationToVector`函数，该函数的作用是计算向量中的每个元素调用特定的激活函数处理后的结果。
第75行，声明了`CopyVector`函数，该函数的作用是将一个向量复制到另一个向量中。
第77行，声明了`Sub1Vector`函数，该函数的作用是计算向量中每个元素和1.0之差，输出是1.0 -向量元素。
第79行，声明了`ZeroVector`函数，该函数的作用是将向量中的所有元素清零。
第81行，声明了`VectorScalarMultiply`函数，该函数的作用是将向量中的所有元素都乘上一个固定的系数。
第84行，声明了`ClipVector`函数，该函数的作用是将向量中的每个元素都转换限制在-`abs_limit`和`abs_limit`之间。
第87行，声明了`VectorShiftLeft`函数，该函数的作用是将向量中的元素左移固定的位数。
第89行，声明了`ReductionSumVector`函数，该函数的作用是将某个向量的特定数量元素累加到另一个缓冲区中，假如`reduction_size`是5，那么我们就会从输入向量中以每5个作为一组，并计算每组之和，存储到目标缓冲区中。

这里每个函数都需要一个对应版本的实现，那么这些实现在哪里呢？`tensor_utils.cc`是一个非常关键的代码，如代码清单5-5所示。

## 实现文件

代码清单5-5　`tensor_utils.cc`

```
 1 #include "tensorflow/lite/kernels/internal/tensor_utils.h"
 2 #include "tensorflow/lite/kernels/internal/common.h"
 3 
 4 #ifndef USE_NEON
 5 #if defined(__ARM_NEON__) ‖ defined(__ARM_NEON)
 6 #define USE_NEON
 7 #endif  // defined(__ARM_NEON__) ‖ defined(__ARM_NEON)
 8 #endif  // USE_NEON
 9 
10 #ifdef USE_NEON
11 #include "tensorflow/lite/kernels/internal/optimized/neon_tensor_utils.h"
12 #else
13 #include "tensorflow/lite/kernels/internal/reference/portable_tensor_utils.h"
14 #endif  // USE_NEON
```

我们发现这里其实是根据`USE_NEON`直接使用了两个不同的版本。每个版本都有一份独立的声明和定义，比如在Neon中，我们就定义了`kernels/internal/optimized/neon_tensor_utils.h`，部分代码如代码清单5-6所示。

代码清单5-6　`neon_tensor_utils.h`
