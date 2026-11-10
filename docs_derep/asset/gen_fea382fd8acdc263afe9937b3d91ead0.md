[PRE0]

## 瓶颈模块与 FPN 架构解析

第 8 行引入了 Bottleneck 类，它直接继承自 Module。类的构造方法在第 11 行给出，按顺序搭建了整组层：最先是一个 Conv2d 卷积层，随后接 BatchNorm2d 归一化层，再来一轮卷积层与归一化层。之后用 Sequential 包裹了一个负责降采样的子结构，里面只有一层卷积和一层 BatchNorm。

第 27 到 33 行写出了 forward 前向逻辑。特征先经过卷积加 BatchNorm 再做一次 ReLU，紧接着再走一组卷积、BatchNorm、ReLU，随后是另一组卷积、BatchNorm的处理，经过前面搭好的降采样子网络后，最后以一次 ReLU 收尾得到输出。

第 36 行声明了 FPN 类，同样继承自 Module。第 37 行的 __init__ 先放一个 Conv2d 和一个 BatchNorm2d，之后连续接入四个 block，再接两个卷积层。随后代码额外创建了三组侧边层和两组顶部层。

第 60～66 行是 _make_layer 方法，它负责按需求批量生成若干 block 并用 Sequential 串起来返回。第 68～70 行的 _upsample_add 方法很简洁，先调用 upsample 做插值，再把外部传入的 y 向量加进去。

前向方法定义在第 72～86 行，整体流程与前面网络相近，就不逐行展开了。值得注意的是，这条网络并非单线串行，而是借助 latlayer 和 toplayer 形成了多条分支，具体的融合方式可以在代码里直观看到。

第 89～90 行的 FPN50 工厂函数会返回一个基于 Bottleneck 的 FPN，四个阶段的 block 数量分别是 3、4、6、3。第 92～93 行的 FPN101 则使用同样的 Bottleneck，数量为 2、4、23、3。

在 FPN 的基础上，我们进一步搭建 RetinaNet 检测网络，具体实现见下述代码清单。

## 代码清单 8-11 retinanet.py 框架拆解

[PRE1]
第 8 行定义了 RetinaNet 类，父类仍是 Module。第 11 行的初始化函数首先实例化一个 FPN50 作为主干，随后设定类别数 num_classes，接着两次调用 _make_head 分别生成负责回归定位的子网和负责分类的子网。

第 18 行的 forward 先把输入送入 FPN 提取特征，再分别送进定位分支和分类分支，经过一次向量形状的调整后把结果累加到预测输出里，最终同时返回位置预测和类别预测。

第 31～37 行的 _make_head 用来按参数拼装头部网络：先有四层卷积配合 ReLU，再额外接一层卷积，最后用 Sequential 组织返回。第 39 行的 freeze_bn 函数专用于在测试时冻结 BatchNorm 层。

接下来就是训练脚本的编写，核心仍然是调用 retinanet.py 里定义的网络，其它部分与先前示例差异不大，具体见下一清单。

## 代码清单 8-12 train.py

[PRE2]