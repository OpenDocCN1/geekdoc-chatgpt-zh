
```
 60 void NeonMatrixBatchVectorMultiplyAccumulate(
 61     const int8_t* __restrict__ matrix, const int m_rows, const int m_cols,
 62     const int8_t* __restrict__ vectors, const float* scaling_factors,
 63     int n_batch, float* __restrict__ result, int result_stride) {
 64   const int kWeightsPerUint32 = 4;
 65   const int kWeightsPerNeonLane = 16;
 66   bool unaligned = false;
 67   int8_t* aligned_row = nullptr;
 68   void* aligned_row_free = nullptr;
 69   if ((m_cols & (kWeightsPerUint32 - 1)) != 0) {
 70     unaligned = true;
 71     aligned_row = (int8_t*)aligned_alloc(kWeightsPerUint32, m_cols,  // NOLINT
 72                                          &aligned_row_free);
 73   }
 74   void* aligned_vec_free = nullptr;
 75   int8_t* aligned_vec =
 76       (int8_t*)aligned_alloc(kWeightsPerUint32, m_cols,  // NOLINT
 77                              &aligned_vec_free);
 78 
 79   const int postamble_start = m_cols - (m_cols & (kWeightsPerNeonLane - 1));
 80 
 81   int batch, row, col;
 82   for (batch = 0; batch < n_batch; ++batch) {
 83     const float batch_scaling_factor = scaling_factors[batch];
 84     memcpy(aligned_vec, vectors + batch * m_cols, sizeof(int8_t) * m_cols)
 85     for (row = 0; row < m_rows; ++row, result += result_stride) {
 86       int8_t* row_ptr = (int8_t*)matrix + row * m_cols;  // NOLINT
 87       if (unaligned) {
 88         memcpy(aligned_row, row_ptr, sizeof(int8_t) * m_cols);
 89         row_ptr = aligned_row;
 90       }
 91 
 92       int32x4_t dotprod = vmovq_n_s32(0);
 93 
 94       __builtin_prefetch(row_ptr, 0, 3);
 95 
 96       col = 0;
 97       for (; col < postamble_start; col += kWeightsPerNeonLane) {
 98         TFLITE_DCHECK_EQ(
 99             (uintptr_t)(&row_ptr[col]) & (kWeightsPerUint32 - 1), 0);
100         const int8x16_t s1_8x16 = vld1q_s8((const int8_t*)(aligned_vec + col));
101         const int8x16_t s2_8x16 = vld1q_s8((const int8_t*)(row_ptr + col));
102         int16x8_t prod_16x8 =
103             vmull_s8(vget_low_s8(s1_8x16), vget_low_s8(s2_8x16));
104         prod_16x8 =
105             vmlal_s8(prod_16x8, vget_high_s8(s1_8x16), vget_high_s8(s2_8x16));
106 
107         dotprod = vpadalq_s16(dotprod, prod_16x8);
108       }
109 
110       int32 postable_sum = 0;
111       if (postamble_start < m_cols) {
112         col = postamble_start;
113         if ((m_cols - postamble_start) >= (kWeightsPerNeonLane >> 1)) {
114           TFLITE_DCHECK_EQ(
115               (uintptr_t)(&row_ptr[col]) & (kWeightsPerUint32 - 1), 0);
116           const int8x8_t s1_8x8 = vld1_s8((const int8_t*)(aligned_vec + col));
117           const int8x8_t s2_8x8 = vld1_s8((const int8_t*)(row_ptr + col));
118           const int16x8_t prod_16x8 = vmull_s8(s1_8x8, s2_8x8);
119           dotprod = vpadalq_s16(dotprod, prod_16x8);
120           col += (kWeightsPerNeonLane >> 1);
121         }
122         for (; col < m_cols; ++col) {
123           postable_sum += row_ptr[col] * aligned_vec[col];
124         }
125       }
126       int64x2_t pairwiseAdded = vpaddlq_s32(dotprod);
127       int32 neon_sum =
128           vgetq_lane_s64(pairwiseAdded, 0) + vgetq_lane_s64(pairwiseAdded, 1);
129 
130       *result += ((neon_sum + postable_sum) * batch_scaling_factor);
131     }
132   }
133 
134   if (unaligned) {
135     free(aligned_row_free);
136   }
137   free(aligned_vec_free);
138 }
```

第64～65行，定义了两个常量，分别是每个元素的尺寸和每个lane的尺寸。一个lane也就是我们使用Neon可以一次性处理的元素向量。

第66～77行，根据内存对齐要求来分配结果缓冲区的存储空间，如果缓冲区不符合要求就重新分配内存，否则返回原来的缓冲区。俗话说“工欲善其事，必先利其器”，在Neon里，我们只有按照Neon的要求处理好缓冲区的对齐问题，才能调用Neon指令完成一系列的运算，加快后续的计算过程，为了加快计算，在内存分配上多花一点时间是非常值得的。这里要求每个矩阵都按照`kWeightsPerUint32`对齐，也就是4字节对齐，同时矩阵中的每一行也需要按照`kWeightsPerUint32`对齐，也就是每行的字节数必须是`kWeightsPerUint32`的整数倍。

第79行，由于`TensorFlow Lite`在计算主循环中会按照16字节进行批量计算，但是输入的缓冲区长度不可能永远是16字节的整数倍，因此有部分元素需要安排在循环之外计算，这部分元素的起点就是`postamble_start`。

第81行，开始计算这批矩阵中每一个矩阵的乘积。

第84行，调用`memcpy`将向量数据复制到对齐后的向量中。

第85行，开始计算矩阵中每一列的点乘结果。

第86～90行，获取每一行中第一个元素的地址，这里需要考虑内存对齐问题。

第92行，将每一行的点积结果初始化为0，准备计算。

第94行，调用`__builtin_prefetch`将需要计算的这一行的数据预取到临时局部缓冲区中，以加快计算速度。

第97行，开始计算每16个8位元素的点乘。

第100行，调用`vld1q_s8`指令从每一行和待乘向量中加载16个8位整数，这里假定所有缓冲区都是4字节对齐，我们后面调用的都是对齐版本指令，以加快计算速度。

第103行，调用`vget_low_s8`从两个待乘向量中获取低字节的数据，并调用`vmull_s8`实现两个向量的乘法操作，将得到的结果存储到向量`prod_16x8`中。

第104～105行，调用`vget_high_s8`从两个待乘向量中获取高字节的数据，并调用`vmlal_s8`实现两个向量的乘法操作，并累加到向量`prod_16x8`中。

第107行，调用`vpadalq_s16`，将向量中相邻的元素对相加，并将结果的绝对值累加到目标向量的元素中。

第111行，开始处理16字节的整数倍以外的那些多余元素。

第114行，调用`vld1q_s8`指令从每一行和待乘向量中加载8个8位整数，这里假定所有缓冲区都是4字节对齐，我们后面调用的都是对齐版本指令，以加快计算速度。

第118～119行，调用`vmull_s8`，直接计算`s1_8x8`和`s2_8x8`两个向量的乘积，存储到`prod_16x8`中，最后调用`vpadalq_s16`，将向量中相邻的元素对相加，并将结果的绝对值累加到目标向量的元素中。

第126行，将计算后的结果合并到输出缓冲区中。

第134～137行，如果分配了对齐缓冲区，调用`free`释放掉这些缓冲区，避免内存泄漏。

`ApplyActivationToVector`采用通用版实现，因此我们就不阐述如何处理了。

### 5.5　本章小结

本章我们主要介绍了移动平台深度学习开发的基础知识。首先介绍了移动平台的深度学习系统，然后介绍了ARM Linux基础开发知识，接着介绍了`ARM`的体系结构、`Neon`指令集、内存模型与加速策略。最后以`TensorFlow Lite`为例讲解了如何运用这些基础知识裁剪实现一个适用于移动平台的深度学习框架。

* * *

[\[1\]](`../Text/Section_0005_0001.xhtml#a01`) `ARM`（Advanced RISC Machine）处理器是英国Acorn有限公司设计的低功耗成本的第一款RISC微处理器。

[\[2\]](`../Text/Section_0005_0002.xhtml#a02`) `Xcode`是由苹果公司开发的、运行在Mac OS X/macOS上的集成开发工具（IDE）。

[\[3\]](`../Text/Section_0005_0002.xhtml#a03`) `Android NDK`是一套使开发者可以在Android中使用C和C++等语言，以原生代码实现部分应用的工具集。

[\[4\]](`../Text/Section_0005_0002.xhtml#a04`) GNU编译器套件（GNU Compiler Collection）包括C、C++等语言前端，也包括了这些语言的库。

[\[5\]](`../Text/Section_0005_0002.xhtml#a05`) `Clang`是一个C/C++语言、Objective-C语言的轻量级编译器，是目前非常流行的开源编译器。

[\[6\]](`../Text/Section_0005_0002.xhtml#a06`) 可以访问`https://developer.arm.com/open-source/gnu-toolchain/gnu-rm/downloads`下载ARM官方编译固件。

[\[7\]](`../Text/Section_0005_0002.xhtml#a07`) 可以访问`https://developer.android.google.cn/ndk/downloads/`来下载Android NDK。

[\[8\]](`../Text/Section_0005_0002.xhtml#a08`) 可以访问`https://github.com/raspberrypi/tools`来下载树莓派工具链。

[\[9\]](`../Text/Section_0005_0004.xhtml#a09`) TensorFlow的GitHub仓库参见：`https://github.com/tensorflow/tensorflow`。

[\[10\]](`../Text/Section_0005_0004.xhtml#a10`) 可以访问`https://github.com/xianyi/OpenBLAS`来获取相关函数库。

[\[11\]](`../Text/Section_0005_0004.xhtml#a11`) 可以访问`https://github.com/projectNe10/Ne10`来获取Ne10相关函数库。

## 第6章　移动平台轻量级网络实战

在第5章中，我们主要介绍了移动平台深度学习开发的基础知识，其核心内容是与ARM相关的基础开发知识，这包括工具链、体系结构、Neon指令集还有内存模型和加速策略等技术细节。最后介绍了TensorFlow，并介绍了适用于ARM架构的TensorFlow Lite。

我们曾提到过，为了让机器学习框架适用于移动平台，一方面，我们不仅需要在深度学习的实现技术上充分利用硬件资源来实现硬件加速，另一方面，还需要在深度学习的模型本身做出改进，现在已经有很多经过改造后适用于移动平台的成熟模型了。在本章中，我们将会学习这些适用于移动平台的轻量级网络，而这些网络就是针对高实时性场景提出的模型。除了学习相关的理论知识，我们还需要了解实现这些模型的方法，深刻体会这些模型相对于普通模型的不同之处。

### 6.1　适用于移动平台的轻量级网络

首先我们来探讨一下什么才是适用于移动平台的轻量级网络。众所周知，在有超巨大数据集的情况下，深度学习网络提升自身准确度的最好方法就是加深网络，因为网络层次越深，理论上我们可以提取的高层特征就越多。除了加深网络外，我们还可以选择增加网络的参数数量，特征越多，我们用来进行分类或者计算的参考就越多。这在一个巨大的分布式系统里是非常合适的，如果数据量太小可能会发生过拟合的情况除外，没有其他太大的弊端。

当然深层网络和大量特征也为深度学习带来了一个副作用，即速度慢。网络层次越深，参数数量越多，那么训练或者预测时所需要的时间也就越多。在一个巨大的分布式集群里，我们可能有非常多的计算资源来帮助我们完成这种计算量巨大的任务，但是到了移动平台这些事情就不一样了。

我们知道，移动平台的计算处理能力是非受限的，而我们需要解决的问题又会对计算的速度有一定要求，如果将一个层次与参数数量不受限制的网络直接移植到移动平台，势必会导致计算速度非常慢，而无法满足我们的速度要求，甚至无法接受移动平台的内存限制。

因此深度学习面临的主要问题是如何尽量使用层次浅、参数少的网络来替代原来的深层次、多参数的网络。这种网络我们就称之为轻量级网络。通过轻量级网络我们可以加快训练和预测的速度并节省系统资源。

接下来我们以几个典型的轻量级网络为例，介绍一下轻量级网络发展的来龙去脉。

### 6.2　SqueezeNet

`SqueezeNet`可以说是最早公开的轻量级网络，是在非常著名的论文《SqueezeNet: AlexNet-level accuracy with 50x fewer parameters and<0.5MB model size》[\[1\]](`../Text/Section_0006_0006.xhtml#b01`)中公开的。这个论文的名字也非常简单直接，说明“`SqueezeNet`”和“`AlexNet`”具有相同的精确度，但是参数数量却仅为`AlexNet`的1/50，同时还可以将模型压缩到0.5 MB以下。这可以说是一件非常激动人心的事情了，那么`SqueezeNet`是怎么做到的？下面让我们逐一解释其中的关键技术。

首先是模型压缩。模型压缩的思路非常多，包括奇异值分解、权值剪枝、系数矩阵、霍夫曼编码等各类方法，这些方法都是常用的有损压缩方法，这将会在进阶章节中进行介绍。这些方法的思路就是通过有损的方法，在保持精度的条件下大比例的压缩网络。

除了模型压缩，我们还希望能够通过调整网络的内部结构提升网络的准确率。结构调整主要包括宏观结构调整和微观结构调整两种。

#### 6.2.1　微观结构

所谓微观结构调整一般是指调节网络中的某些组成模块。比如最初的`LeNet`做手写数字识别使用的是5×5的卷积核，后来的`VGGNet`使用了3×3的卷积核，甚至在Google的`Inception`中使用了1×1的卷积核。理论上相同层数的网络，卷积核越小，参数就越少。发展到后来，Google甚至提出了`Inception`模块，模块中包含了不同维度的滤波器（比如1×1、5×5、1×3、3×1等），然后选用不同的模块组成不同的网络，经过不断的实验，希望通过多个小卷积核替代单一的大卷积核，以带来更好的效果。

#### 6.2.2　宏观结构

宏观结构调整其实就是调整网络的层次数和模块的组织方式，这在本章开头已经提到过。关于宏观结构调整有一种与众不同的结构调整方式就是将跨越多个层次的不同层的输出连接到同一个层，比如将来自第6层和第9层的激活值输入到一个新层中，这种思路我们称之为旁路连接，形象一点说这种方法可以发现更多潜在的特征组合形式。

#### 6.2.3　核心思路
