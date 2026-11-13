在该系统架构图中，按照输出数据复用的模式，输出缓存器通过 Crossbar 向计算单元提供数据，计算单元不断地导入输入特征值和权重值，完成输出部分通道维度（循环 M）的累加，将计算结果缓存到输出缓冲器中。

中国科学院计算技术研究所提出的 DianNao 架构[6]也采用此类计算模式。其中的卷积层计算顺序将输出特征图中的通道数作为最外层循环，这也正是输出数据复用的显著特征。在 DianNao 的架构中，SB 和 NBin 分别用来存储权重和输入神经元信息，NFU 单元用来进行点乘和累加的运算，输出值存储在 NBout 中。在计算过程中，输出特征值会寄存在输出缓存器中直到完成所有的部分和累加运算，才会经过 DMA 搬出。

```
for(row=0; row<R; row+=Tr){
for(col=0; col<C; col+=Tc){
for(to=0; to<M; to+=Tm){
for(ti=0; ti<N; Ti+=Tn){
//load output feature maps
//load weights
//load input feature maps

L: foo(output_fm(to, row, col)),
    weights(to, ti),
    input_fm(ti, row, col),
//store output feature maps
}
}
}}}
↑
```

图 5.5 基于 FPGA 架构实现的输出数据复用[5]

# 5.3 权重数据复用

图 5.1 (e) 所示为一种基本的 WR 模式，总共分为 3 个步骤：①计算核心读取 Tn 个通道的输入特征图分块到局部的输入寄存器；②计算核心利用这些输入数据更