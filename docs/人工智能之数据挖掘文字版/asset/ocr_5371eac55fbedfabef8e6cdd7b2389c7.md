的时候，所有样本的均值偏差满足正态分布。论文主要探讨了 Delta 方法，以及该方法的 4 个应用。相比于传统的 Fielder 方法，Delta 方法提供了一个更简单的解法，而得到的精度基本和传统方法相当。

“Web-Scale Responsive Visual Search at Bing” 主要介绍了在 Microsoft Bing 里面上线的一个可视化搜索。如下图所示，给定一个图片（左图是桌面版），系统可以自动检测图片中的重要元素，用户也可以根据自己的喜好选择图片中的对象，系统则自动找出和对象相关的产品；下图右边是一个手机版。

![](bbox=[0.156, 0.263, 0.838, 0.501])

# 图 42 Microsoft Bing 的可视化搜索

该系统已经索引了数十亿图片，每个图片也预先抽取了上千特征，针对用户定制的可视化搜索可以做到 200 毫秒。算法则使用了 DNN 来抽取特征，然后用 N-nearest 方法进行匹配。从应用的角度，这还是一个很有意思的工作。

![](bbox=[0.263, 0.623, 0.738, 0.763])

# 图 43 算法展示

“BigIN4: Instant, Interactive Insight Identification for Multi-Dimensional Big Data.” 介绍了一个从高维大数据中通过交互方式进行快速查询的方法， BigIN4 可以在 data cube 中做到快速查询（比传统抽样方法快 50 倍）。该方法的本质是做了一个降维处理，见下图。第 2 个图则给出了 BigIN4 可能挖掘出来的 Insights: 包括 Outstanding（就是某些数据模式和其他