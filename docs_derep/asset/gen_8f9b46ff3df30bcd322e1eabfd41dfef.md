在贝叶斯框架下，$P(A|B)$表示已知特征$B$出现时，文档落入类别$A$的可能性；$P(B|A)$则反映在类别$A$已确定的前提下特征$B$出现的条件概率；$P(A)$是类别$A$在先验分布中的占比；$P(B)$是特征$B$整体出现的先验频率。

#### 2. 朴素假设
“朴素”二字源自模型对特征独立性的强假设。计算时它默认文本里的各个特征，比如词或短语，彼此互不影响。虽然现实中这种独立性往往不成立，但正是这一简化让计算变得可行且高效。

#### 3. 特征和类别
做文本分类时，特征一般提取自原文中的词语或词组，类别则指文档要归属的标签。比如常见的二分类就是垃圾邮件与正常邮件，或是情感上的正面与负面。

#### 4. 建模
训练阶段，模型会从标注样本里统计两类信息：一是每个类别下各特征出现的条件概率$P(B|A)$，二是各类别本身的先验概率$P(A)$，以此构建参数。

#### 5. 分类
拿到待判样本后，分类器会先算出文中特征的条件似然，再借助贝叶斯公式求出属于各类别的后验概率，最后把概率最高的类别作为预测结果。

注意：朴素贝叶斯在文本任务上应用广泛，按特征类型可选用多项式、伯努利或高斯三种变体，分别适配词频计数、二值化特征和连续数值特征。

### 3.1.2 朴素贝叶斯分类器的应用场景
该方法在自然语言处理与文本挖掘领域渗透很深，典型用途汇总在图3-2中。

[PRE0]

图3-2

归纳来看，它上手快、推理快，在文本分类和自动化判别里表现稳健。实例3-1演示了如何用它把邮件自动划分为垃圾邮件与正常邮件。

#### 实例3-1：使用朴素贝叶斯分类器将电子邮件分类为垃圾邮件和正常邮件
示例代码保存在 pu.py，路径 codes\3\pu.py，核心实现如下。

[PRE1]

代码里先构造了一个小型训练集，包含垃圾邮件和正常邮件样本，随后用 CountVectorizer 把文本转成向量，再以 MultinomialNB 训练并预测，最后输出准确率并对新邮件进行判别。

执行上述代码，输出结果如下。

[PRE2]

## 3.2 支持向量机
支持向量机是一种经典的监督学习方法，常用于分类与回归。其核心思想是寻找一个最优的分离超平面，把不同类别的样本分开。

设想你在运营一个家庭图书角，希望自动把书籍评论归为“推荐”或“不推荐”，以便提升推荐效率。可以用 SVM 来实现。先从评论文本中抽取关键词作为特征训练模型，例如出现“精彩”“有趣”的评论更可能被判为“推荐”，而出现“无聊”“乏味”的则倾向“不推荐”。训练完成后，新评论即可自动打标，提升管理效率。如图3-3所示。

[PRE3]

图3-3

### 3.2.1 SVM介绍
SVM 的核心原理与常见应用领域如图3-4所示。

[PRE4]

总体而言，SVM泛化能力强、适用面广，在不少任务上胜过其他分类器。但它计算负担重，对参数调节要求高，在超大规模数据上资源消耗明显。

### 3.2.2 线性SVM与非线性SVM
线性 SVM 与非线性 SVM 是两种主要分支，针对的数据形态与任务不同，具体说明见表3-1。

表3-1 线性SVM与非线性SVM的对比

| 对比 | 线性SVM | 非线性SVM |
| :--- | :--- | :--- |
| 原理 | 利用线性超平面划分不同类别，适用于数据在原始空间线性可分的情况，可用直线或超平面把样本完全分开 | 通过核技巧把样本映射到更高维空间，再在新空间寻找分隔面，从而处理原始空间中非线性可分的问题 |
| 应用 | 常用于线性可分的二分类等任务，在高维稀疏文本上速度快、效果好 | 擅长处理原始空间中无法线性分离的情形，在图像识别、文本分类、模式识别等领域常用 |
| 特点 | 训练相对迅速，对超参数敏感度低 | 训练开销较大，受核函数选择和参数调优影响明显，尤其在高维大数据上更慢 |

若数据本身线性可分，或样本量不大而特征维度较高，线性 SVM 通常是更省时的选择。如果数据在原始空间不可分，需要处理非线性边界，则非线性 SVM 更合适，此时核函数如多项式核、高斯核的选择与超参数调优至关重要。实例3-2演示了用线性 SVM 与非线性 SVM 做情感分析，即把评论分为正面、负面或中性。

#### 实例3-2：使用线性SVM和非线性SVM进行情感分析
实例文件 svm.py（源码路径：codes\3\svm.py）的具体实现代码如下。

[PRE5]

X_test = vectorizer.transform(X_test)

# 线性 SVM 分类器

linear_svm_classifier = LinearSVC()

linear_svm_classifier.fit(X_train, y_train)

linear_svm_predictions = linear_svm_classifier.predict(X_test)

# 非线性 SVM 分类器（使用高斯核）

nonlinear_svm_classifier = SVC(kernel='rbf')

nonlinear_svm_classifier.fit(X_train, y_train)

nonlinear_svm_predictions = nonlinear_svm_classifier.predict(X_test)

# 评估线性 SVM 和非线性 SVM 的性能

linear_svm_accuracy = accuracy_score(y_test, linear_svm_predictions)

nonlinear_svm_accuracy = accuracy_score(y_test, nonlinear_svm_predictions)

print("Linear SVM Accuracy: {:.2f}%".format(linear_svm_accuracy * 100))

print("Nonlinear SVM Accuracy: {:.2f}%".format(nonlinear_svm_accuracy * 100))

# 输入新评论并进行情感分析

new_reviews = ["This movie was fantastic!",

               "I did not enjoy this film at all.",

               "It was okay, not great but not terrible."]

new_reviews = vectorizer.transform(new_reviews)

linear_svm_sentiments = linear_svm_classifier.predict(new_reviews)

nonlinear_svm_sentiments = nonlinear_svm_classifier.predict(new_reviews)

print("Linear SVM Sentiments:", linear_svm_sentiments)

print("Nonlinear SVM Sentiments:", nonlinear_svm_sentiments)

代码先加载电影评论集并划分为训练集与测试集，用 TF-IDF 向量化文本，随后分别用线性 SVM 与基于高斯核的非线性 SVM 做情感分析，最后比较性能并对新评论进行判别。

执行上述代码，输出结果如下。

[PRE6]

依据评论文本，两个模型给出了情感标签。在此示例中，“This movie was fantastic!”被判为正面，“I did not enjoy this film at all.”被判为负面，“It was okay, not great but not terrible.”被判为中性。

## 3.3 随机森林
随机森林是一种集成学习方法，基于多棵决策树投票来提升分类与回归的准确性和泛化能力。

假设你在维护家庭健康管理系统，想自动把成员的健康反馈归为“良好”或“需要关注”。可以用随机森林实现。先从反馈文本中提取关键词如“疲劳”“精力充沛”“头晕”等作为特征，模型会学习哪些词与健康状态相关。例如含“精力充沛”“心情很好”的反馈多被归为“良好”，含“疲劳”“头晕”的则倾向“需要关注”。训练后即可自动对新的健康反馈分类，便于日常管理。如图3-5所示。

[PRE7]

图3-5

### 3.3.1 随机森林介绍
随机森林的核心概念与主要特性如图3-6所示。

[PRE8]

图3-6

### 3.3.2 随机森林的应用场景
随机森林可部署于多个领域，常见用途如图3-7所示。

[PRE9]

总体来看，随机森林通用性强，适用于各类问题与数据。实例3-3用随机森林构建垃圾邮件分类器，区分邮件是 spam 还是 ham。数据保存在 spam_ham_dataset.csv 中，结构如下。

[PRE10]

一共包含了18条数据，其中 text 列为邮件文本，label 列为标签，标明邮件是垃圾邮件 spam 还是正常邮件 ham。该小样本可用于训练与测试，实际场景中数据集会大得多。

#### 实例3-3：使用随机森林构建一个垃圾邮件分类器
实例文件 you.py（源码路径：codes\3\you.py）的具体实现代码如下所示。