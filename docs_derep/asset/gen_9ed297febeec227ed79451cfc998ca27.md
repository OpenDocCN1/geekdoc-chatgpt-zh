[PRE0]

## 接口速览

第13行给出 `Clip`，主要负责对传入参数做范围约束，把数值限定在 -`abs_limit` 到 +`abs_limit` 之间。

第15行给出 `IsZeroVector`，用来判断一个向量里是否全部元素都是 0。

第17行给出 `SymmetricQuantizeFloats`，采用无偏置的对称线性量化，把浮点缓冲区转成 8 位整型数组，同时会给出该缓冲区的最小值、最大值以及量化用的缩放系数。

第21行给出 `MatrixBatchVectorMultiplyAccumulate`，它把一组构成多维向量的矩阵与另一矩阵相乘，并把每组乘法的结果累加到用户指定的结果缓冲区。

第26行给出 `SparseMatrixBatchVectorMultiplyAccumulate`，整体逻辑与前者相近，区别在于输入矩阵使用压缩系数矩阵方式存储的稀疏矩阵，所以计算路径有所不同。

第30行又声明了一个 `MatrixBatchVectorMultiplyAccumulate`，功能与第21行的保持一致，差异是输入经量化处理后变为整型，调用方需要额外传一个缩放因子，最终返回的是浮点类型的缓冲区。

第35行给出 `SparseMatrixBatchVectorMultiplyAccumulate`，与第26行的作用相同，只是输入同样是量化后的整型，调用时要带缩放因子，输出仍为浮点缓冲区。

第41行给出 `VectorVectorCwiseProduct`，实现两个向量的逐元素相乘。

第44行给出 `VectorVectorCwiseProductAccumulate`，同样做逐元素相乘，但结果会累加到输出向量上，且要求该输出缓冲区已有合法数据。

第48行给出 `VectorVectorDotProduct`，计算两个向量的点积。

第51行给出 `BatchVectorBatchVectorDotProduct`，对两个批次向量做点积运算，并把结果写入目标缓冲区。

第56行给出 `VectorBatchVectorCwiseProduct`，对两个批次向量进行逐元素相乘，结果存入目标缓冲区。

第60行给出 `VectorBatchVectorCwiseProductAccumulate`，在逐元素相乘的基础上累加到结果向量，要求输出缓冲区数据有效。

第64行给出 `VectorBatchVectorAdd`，逐一将一批向量中的每个向量与另一个向量相加。

第67行给出 `VectorBatchVectorAssign`，用一个向量去初始化整个批次，所有向量都被赋成同样的值。

第70行给出 `ApplySigmoidToVector`，对向量每个分量单独求 Sigmoid。

第72行给出 `ApplyActivationToVector`，让向量每个分量经过指定的激活函数处理。

第75行给出 `CopyVector`，把一个向量整体复制到另一个向量。

第77行给出 `Sub1Vector`，计算 1.0 减去每个元素，输出为 1.0 - 原值。

第79行给出 `ZeroVector`，把向量的全部元素置零。

第81行给出 `VectorScalarMultiply`，用同一个固定系数乘以向量的所有元素。

第84行给出 `ClipVector`，把向量每个元素夹持到 -`abs_limit` 与 `abs_limit` 之间。

第87行给出 `VectorShiftLeft`，把向量的元素整体左移指定位数。

第89行给出 `ReductionSumVector`，按指定长度把输入向量分组求和写入另一缓冲区，例如 `reduction_size` 为 5 时，每 5 个为一组求和后存出。

以上接口都有对应的实现落在哪里？核心实现集中在 `tensor_utils.cc`，详见代码清单 5-5 所示。

## 实现位置

代码清单 5-5　`tensor_utils.cc`

[PRE1]

经查看，代码是依据 `USE_NEON` 宏直接切换两套实现。两套版本各自拥有独立的声明与定义，比如 Neon 路径下会使用 `kernels/internal/optimized/neon_tensor_utils.h` 中的实现，部分内容见代码清单 5-6。

代码清单 5-6　`neon_tensor_utils.h`