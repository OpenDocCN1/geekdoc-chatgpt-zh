![](bbox=[0.12, 0.11, 0.88, 0.41])

总体来说，基于 eDRAM 的神经网络加速框架从三个层面——训练层次、调度层次、架构层次进行优化，从而来缓解片上缓存需求以及计算能耗问题，与同面积下采用 SRAM 的神经网络加速器（S+ID）相比，如图 7.19 所示，RANA*（E-5）在执行实验选取的 4 种典型神经网络运算时，平均节省了 65.2%片外访存以及 61.7%的系统能耗，使人工智能芯片的能量效率获得大幅提高。

# 参考 文献

+   [1] He K, Zhang X, Ren S, et al. Deep residual learning for image recognition[C]. IEEE Conference on Computer Vision and Pattern Recognition. IEEE Computer Society, Las Vegas, 2016:770-778.
+   [2] Azarkhish E, Rossi D, Loi I, et al. Neurostream: Scalable and energy efficient deep learning with smart memory cubes[J]. IEEE Transactions on Parallel & Distributed Systems, 2017, (99):1.
+   [3] Lau J H. Evolution, challenge, and outlook of TSV, 3D IC integration and 3d silicon integration[C]. International Symposium on Advanced Packaging Materials, Xiamen, 2011:462-488.
+   [4] Dong U L, Kim K W, Kim K W, et al. A 1.2V 8Gb 8-channel 128GB/s high-bandwidth memory (HBM) stacked DRAM with effective I/O test circuits[J]. IEEE Journal of Solid-State Circuits, 2014, 50 (1):191-203.