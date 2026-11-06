

```
  1 #ifndef TENSORFLOW_LITE_KERNELS_INTERNAL_OPTIMIZED_NEON_TENSOR_UTILS_H_
  2 #define TENSORFLOW_LITE_KERNELS_INTERNAL_OPTIMIZED_NEON_TENSOR_UTILS_H_
  3 
  4 #include "tensorflow/lite/c/builtin_op_data.h"
  5 #include "tensorflow/lite/kernels/internal/optimized/cpu_check.h"
  6 #include "tensorflow/lite/kernels/internal/optimized/tensor_utils_impl.h"
  7 
  8 namespace tflite {
  9 namespace tensor_utils {
 10 
 11 void MatrixBatchVectorMultiplyAccumulate(const float* matrix, int m_rows,
 12                                          int m_cols, const float* vector,
 13                                          int n_batch, float* result,
 14                                          int result_stride) {
 15   NEON_OR_PORTABLE(MatrixBatchVectorMultiplyAccumulate, matrix, m_rows, m_cols,
 16                    vector, n_batch, result, result_stride);
 17 }
 18 
 19 void MatrixBatchVectorMultiplyAccumulate(
 20     const int8_t* __restrict__ matrix, const int m_rows, const int m_cols,
 21     const int8_t* __restrict__ vectors, const float* scaling_factors,
 22     int n_batch, float* __restrict__ result, int result_stride) {
 23   NEON_OR_PORTABLE(MatrixBatchVectorMultiplyAccumulate, matrix, m_rows, m_cols,
 24                    vectors, scaling_factors, n_batch, result, result_stride);
 25 }
 26 
 27 void SparseMatrixBatchVectorMultiplyAccumulate(
 28     const float* matrix, const uint8_t* ledger, const int m_rows,
 29     const int m_cols, const float* vector, int n_batch, float* result,
 30     int result_stride) {
 31   NeonSparseMatrixBatchVectorMultiplyAccumulate(
 32       matrix, ledger, m_rows, m_cols, vector, n_batch, result, result_stride);
 33 }
 34 
 35 void SparseMatrixBatchVectorMultiplyAccumulate(
 36     const int8_t* __restrict__ matrix, const uint8_t* ledger, const int m_rows,
 37     const int m_cols, const int8_t* __restrict__ vectors,
 38     const float* scaling_factors, int n_batch, float* __restrict__ result,
 39     int result_stride) {
 40   NeonSparseMatrixBatchVectorMultiplyAccumulate(matrix, ledger, m_rows, m_cols,
 41                                                 vectors, scaling_factors,
 42                                                 n_batch, result, result_stride);
 43 }
 44 
 45 void VectorVectorCwiseProduct(const float* vector1, const float* vector2,
 46                               int v_size, float* result) {
 47   NEON_OR_PORTABLE(VectorVectorCwiseProduct, vector1, vector2, v_size, result);
 48 }
 49 
 50 void VectorVectorCwiseProductAccumulate(const float* vector1,
 51                                         const float* vector2, int v_size,
 52                                         float* result) {
 53   NEON_OR_PORTABLE(VectorVectorCwiseProductAccumulate, vector1, vector2, v_size,
 54                   result);
 55 }
 56 
 57 void VectorBatchVectorCwiseProduct(const float* vector, int v_size,
 58                                    const float* batch_vector, int n_batch,
 59                                    float* result) {
 60   NEON_OR_PORTABLE(VectorBatchVectorCwiseProduct, vector, v_size, batch_vector,
 61                   n_batch, result);
 62 }
 63 
 64 void VectorBatchVectorCwiseProductAccumulate(const float* vector, int v_size,
 65                                              const float* batch_vector,
 66                                              int n_batch, float* result) {
 67   NEON_OR_PORTABLE(VectorBatchVectorCwiseProductAccumulate, vector, v_size,
 68                    batch_vector, n_batch, result);
 69 }
 70 
 71 float VectorVectorDotProduct(const float* vector1, const float* vector2,
 72                              int v_size) {
 73   return NEON_OR_PORTABLE(VectorVectorDotProduct, vector1, vector2, v_size);
 74 }
 75 
 76 void BatchVectorBatchVectorDotProduct(const float* vector1,
 77                                       const float* vector2, int v_size,
 78                                       int n_batch, float* result,
 79                                       int result_stride) {
 80   NEON_OR_PORTABLE(BatchVectorBatchVectorDotProduct, vector1, vector2, v_size,
 81                    n_batch, result, result_stride);
 82 }
 83 
 84 void VectorBatchVectorAdd(const float* vector, int v_size, int n_batch,
 85                           float* batch_vector) {
 86   PortableVectorBatchVectorAdd(vector, v_size, n_batch, batch_vector);
 87 }
 88 
 89 void VectorBatchVectorAssign(const float* vector, int v_size, int n_batch,
 90                              float* batch_vector) {
 91   PortableVectorBatchVectorAssign(vector, v_size, n_batch, batch_vector);
 92 }
 93 
 94 void ApplySigmoidToVector(const float* vector, int v_size, float* result) {
 95   PortableApplySigmoidToVector(vector, v_size, result);
 96 }
 97 
 98 void ApplyActivationToVector(const float* vector, int v_size,
 99                              TfLiteFusedActivation activation, float* result) {
100   PortableApplyActivationToVector(vector, v_size, activation, result);
101 }
102 
103 void CopyVector(const float* vector, int v_size, float* result) {
104   PortableCopyVector(vector, v_size, result);
105 }
106 
107 void Sub1Vector(const float* vector, int v_size, float* result) {
108   NEON_OR_PORTABLE(Sub1Vector, vector, v_size, result);
109 }
110 
111 void ZeroVector(float* vector, int v_size) {
112   PortableZeroVector(vector, v_size);
113 }
114 
115 float Clip(float f, float abs_limit) { return PortableClip(f, abs_limit); }
116 
117 bool IsZeroVector(const float* vector, int v_size) {
118   return NEON_OR_PORTABLE(IsZeroVector, vector, v_size);
119 }
120 
121 void VectorScalarMultiply(const int8_t* vector, int v_size, float scale,
122                           float* result) {
123   NEON_OR_PORTABLE(VectorScalarMultiply, vector, v_size, scale, result);
124 }
125 void ClipVector(const float* vector, int v_size, float abs_limit,
126                 float* result) {
127   NEON_OR_PORTABLE(ClipVector, vector, v_size, abs_limit, result);
128 }
129 
130 void SymmetricQuantizeFloats(const float* values, const int size,
131                              int8_t* quantized_values, float* min_value,
132                              float* max_value, float* scaling_factor) {
133   NEON_OR_PORTABLE(SymmetricQuantizeFloats, values, size, quantized_values,
134                    min_value, max_value, scaling_factor);
135 }
136 
137 void VectorShiftLeft(float* vector, int v_size, float shift_value) {
138   NEON_OR_PORTABLE(VectorShiftLeft, vector, v_size, shift_value);
139 }
140 
141 void ReductionSumVector(const float* input_vector, float* output_vector,
142                         int output_size, int reduction_size) {
143   NEON_OR_PORTABLE(ReductionSumVector, input_vector, output_vector, output_size,
144                    reduction_size);
145 }
146 
147 void MeanStddevNormalization(const float* input_vector, float* output_vector,
148                              int v_size, int n_batch,
149                              float normalization_epsilon) {
150   PortableMeanStddevNormalization(input_vector, output_vector, v_size, n_batch,
151                                 normalization_epsilon);
152 }
153 
154 }  // namespace tensor_utils
155 }  // namespace tflite
156 
157 #endif  // TENSORFLOW_LITE_KERNELS_INTERNAL_OPTIMIZED_NEON_TENSOR_UTILS_H_
```

我们发现几乎每个函数定义都会调用一个`NEON_OR_PORTABLE`函数去实现的应用场景。以根据系统定义宏自动切换不同版本的实现。但是如果这个函数只有通用版，那么就会直接在函数前加上`Portbale`；如果只有`Neon`版本，就会直接在函数前加上`Neon`。

此处我们关注的是乘法运算，因此我们只关注其中的计算，最后`Neon`的实现如代码清单5-7所示。

### 代码清单5-7　矩阵乘法

