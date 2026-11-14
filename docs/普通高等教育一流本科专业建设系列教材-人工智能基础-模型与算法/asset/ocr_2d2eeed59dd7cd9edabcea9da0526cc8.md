```
H\left(S_x\right)=-\left[P\left(A\right)lbP\left(A\right)+P\left(B\right)lbP\left(B\right)+P\left(C\right)lbP\left(C\right)\right]
=-\left[\frac{4}{6}\times lb\left(\frac{4}{6}\right)+\frac{0}{6}\times lb\left(\frac{0}{6}\right)+\frac{2}{6}\times lb\left(\frac{2}{6}\right)\right]
=-\left[\frac{4}{6}\times\left(-0.5850\right)+0+\frac{2}{6}\times\left(-1.5850\right)\right]
=-\left(-0.5283-0.39\right)
=0.9183
```

又有

```
\frac{\left|S_{男}\right|}{|S|}=\frac{\left|S_{女}\right|}{|S|}=\frac{6}{12}
```

将以上 3 式代入式（8.6）得

```
H\left(S|性别\right)=\frac{6}{12}\times H\left(S_{男}\right)+\frac{6}{12}\times H\left(S_{女}\right)=\frac{6}{12}\times 0.9183+\frac{6}{12}\times 0.9183=0.9183
```

用同样的方法可求得

```
H\left(S|年龄段\right)=\frac{4}{12}\times H\left(S_{大}\right)+\frac{4}{12}\times H\left(S_{中}\right)+\frac{4}{12}\times H\left(S_{小}\right)=1.1035
H\left(S|婚状\right)=\frac{6}{12}\times H\left(S_{未}\right)+\frac{6}{12}\times H\left(S_{已}\right)=1.5062
```

可见，条件熵 H(S|性别) 为最小。所以，应取“性别”这一属性对实例集进行分类，即以“性别”作为决策树的根节点。

根节点的属性确定后，再用同样的方法确定其他节点的属性，直到构造出整个决策树。本例中其余节点的属性选择留给读者完成。

# (3) 决策树学习的发展

决策树学习是一种很早就出现的归纳学习方法，至今仍然在不断发展。据文献记载，20 世纪 60 年代初的基本的感知器（elementary perceiver and memorizer，EPAM）中就使用了决策树学习，稍后的概念学习系统（concept learning system，CLS）则使用启发式的前瞻方法来构造决策树。继 1979 年的 ID3 算法之后，人们又于 1986 年、1988 年相继提出了 ID4 和 ID5 算法。1993 年昆兰进一步将 ID3 算法发展成 C4.5 算法。另一类著名的决策树学习算法称为分类回归树（classification and regression trees，CART）算法。

近年来，随着决策树算法的广泛应用，包括 C4.5 和 CART 的各种算法得到了进一步改进，如多变量决策树算法，将遗传算法、神经网络和 C4.5 算法相结合的 GA-NN-C4.5 算法和支持向量机决策树算法等。这些改进算法结合各种方案的优势，以获得更合理的分类效果和更通用的决策规则。

# 4. 决策树的优缺点

# (1) 决策树的优点

决策树易于理解和实现，能够同时处理数据型和常规型属性；它对缺失值不敏感，可以处理不相关特征数据；决策树只需要一次构建即可反复使用，每一次预测的最大计算次数不超过决策树的深度，效率高；决策树挖掘出来的分类规则准确性高，便于理解，可以清晰地显示哪些字段比较重要，生成易于理解的规则；决策树不需要任何领域知识和参数假设，适合应用于高维数据。