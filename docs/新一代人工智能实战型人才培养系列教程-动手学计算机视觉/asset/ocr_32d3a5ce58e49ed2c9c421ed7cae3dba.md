![](bbox=[0.306, 0.093, 0.469, 0.222])

![](bbox=[0.537, 0.093, 0.709, 0.222])

# ## 11.5 小结

本章介绍了语义分割的相关知识。我们介绍了分割网络与分类网络的区别，在此基础上讲解了用于语义分割的网络 FCN，之后动手实现了 FCN 的相应功能并在 PASCAL VOC 数据集上进行了训练和测试。作为计算机视觉的重要研究内容之一，语义分割为后续的图像语义理解，如目标检测、实例分割等奠定了一定的基础。

+   ### 习题
+   
+   （1）在 FCN 中，融合主干网络不同层级输出的特征图是通过跳跃连接后逐像素相加。还能想到别的融合方式吗？动手实现你想到的融合方式，并和原来的融合方式进行比较。
+   
+   （2）如果不同类别的像素差别很大，采用本章中介绍的逐像素交叉熵损失函数训练 FCN 做语义分割可能存在什么问题？如果存在问题，应该如何修改逐像素交叉熵损失函数？
+   
+   （3）在 11.1 节中，我们说语义分割需要建模条件概率 P(Y|I)，但是在 FCN 中建模的是每个像素的条件概率 P(y_i | I)。这是为了规避 Y 的求解空间太大所采取的一种近似求解。这种近似会导致 FCN 的分割结果出现什么问题？
+   
+   （4）在第 9 章中，我们介绍了无监督图像分割。无监督图像分割中的思想或者方法是否有助于改进 FCN，提升语义分割的结果？给出解决思路，并动手实现予以验证。

# ## 11.6 参考文献

+   [1] EVRINGHAM M, VAN GOOL L, WILLIAMS C K I, et al. The pascal visual object classes (voc) challenge[J]. International Journal of Computer Vision, 2010, 88:303-338.
+   
+   [2] CORDTS M, OMRAN M, RAMOS S, et al. The cityscapes dataset for semantic urban scene understanding[C]//IEEE Conference on Computer Vision and Pattern Recognition, 2016:3213- 3223.
+   
+   [3] ZHOU B, ZHAO H, PUIG X, et al. Semantic understanding of scenes through the ade20k dataset[J]. International Journal of Computer Vision, 2019, 127:302-321.
+   
+   [4] LONG J, SHELHAMER E, DARRELL T. Fully convolutional networks for semantic segmentation [C]//IEEE Conference on Computer Vision and Pattern Recognition, 2015: 3431-3440.
+   
+   [5] DUMOULIN V, VISIN F. A guide to convolution arithmetic for deep learning[EB/OL]. (2016-03-23)[2024-09-25]. arXiv: 1603.07285.