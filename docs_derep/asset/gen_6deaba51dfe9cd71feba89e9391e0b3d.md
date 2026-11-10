半监督实战里，标签传播类模型最核心的思路就是：先把未标注样本的标签置为未知，再让模型在图结构上扩散已有信息。下面这几段测试代码展示了 LabelPropagation、LabelSpreading 的标准用法，以及一个手工实现的范数计算工具，逻辑清晰可以直接复用。

[PRE0]
def test_LabelPropagation( * data):
    X, Y, unlabeled_index = data
# 解析输入元组，得到特征矩阵 X、标签向量 Y 与待预测索引
    Y_train = np.copy(Y)            # 复制原始标签形成可修改的训练副本 Y_train
    Y_train[ unlabeled_index ] = -1    # 用 -1 标记所有未标注位置，表示未知
    cls = LabelPropagation( max_iter=100, kernel='rbf', gamma=0.1)
# 构造一个基于 RBF 核的 LabelPropagation 分类器
    cls.fit( X, Y_train)            # 用处理后的特征和标签完成模型训练
    accuracy = cls.score( X[ unlabeled_index ], Y[ unlabeled_index ])
# 在未标注子集上计算预测的正确率
    print( " Accuracy: %f" % accuracy)
# 输出最终的准确率数值

[PRE1]
def test_LabelSpreading( * data):
    X, Y, unlabeled_index = data
# 拆解输入，取出特征 X、标签 Y 以及未标注样本的索引
    Y_train = np.copy(Y)            # 对 Y 进行深拷贝得到训练标签 Y_train
    Y_train[ unlabeled_index ] = -1    # 将待标注样本的标签统一设为 -1
    cls = LabelSpreading( max_iter=100, kernel='knn', alpha=0.2, n_neighbors=7)
# 创建参数化后的 LabelSpreading 分类器实例

[PRE2]
cls.fit(X, Y_train)                                   # 基于训练集完成模型拟合
predicted_labels = cls.transduction_[unlabeled_index] # 从模型透导结果中提取未标注样本的预测标签
true_labels = Y[unlabeled_index]                      # 获取对应样本的真实标签用于对比
accuracy = metrics.accuracy_score(true_labels, predicted_labels)  # 计算预测与真实的匹配准确率
print(" Accuracy: %f" % accuracy)                     # 打印准确率结果供观察

[PRE3]
def distanceNorm(Norm, D_value):
    if Norm == '1':                     # 进入 L1 范数计算分支
        counter = np.absolute(D_value)  # 先对元素取绝对值
        counter = np.sum(counter)       # 再求和得到 L1 值
    elif Norm == '2':                   # 进入 L2 范数计算分支
        counter = np.power(D_value, 2)  # 将元素平方
        counter = np.sum(counter)       # 累加平方和
        counter = np.sqrt(counter)      # 最后开根号
    elif Norm == 'Infinity':            # 进入无穷范数计算分支
        counter = np.absolute(D_value)  # 先取绝对值
        counter = np.max(counter)       # 取最大值即为无穷范数
    else:
        raise Exception('We will program this later......')  # 其他类型范数尚未实现，抛出异常提示
    return counter