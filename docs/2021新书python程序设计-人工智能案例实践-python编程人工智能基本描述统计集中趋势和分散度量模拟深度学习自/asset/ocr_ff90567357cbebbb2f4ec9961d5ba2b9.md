资料来源：Jefficus提供，见 https://commons.wikimedia.org/w/index.php?
title=User:Jefficus&action=edit&redlink=1

![](bbox=[0.275, 0.185, 0.715, 0.525])

维吉尼亚鸢尾

资料来源： Christer T Johansson

# 14. 7. 1 加载Iris数据集

使用ipython --matplotlib启动IPython，然后使用 sklearn.datasets模块的load_iris函数获取包含数据集的Bunch对象：

```
In [1]: from sklearn.datasets import load_iris
In [2]: iris = load_iris()
```

Bunch对象的DESCR属性表示有150个样本（Number of Instances），每个样本有4个特征（Number of Attributes）。该数据集中没有缺失值。数据集通过用整数0、1和2标记样本来对样本进行分类，整数0、1和2分别代表山鸢尾、变色鸢尾和维吉尼亚鸢尾三种鸢尾