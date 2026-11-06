```
[[0 1]
[1 0]]

[[0 1]
[0 1]]

[1 0]

[0 1]
```

# （3）numpy.lexsort 排序

lexsort 支持对数组按指定行或列的顺序排序，是间接排序。lexsort 不修改原数组，返回数组索引。对应 lexsort 一维数组是 argsort，argsort 也不修改原数组，返回数组索引。默认按最后一行元素有小到大排序，返回最后一行元素排序后索引所在位置。

# 【例 3-41】numpy.lexsort 排序

输入：

```
#Sort two columns of numbers
a = [1,5,1,4,3,4,4] # First column
b = [9,4,0,4,0,2,1] # Second column
ind = np.lexsort((b,a)) # Sort by a, then by b
print(ind)
print([(a[i],b[i]) for i in ind])
print([(a[i],b[i]) for i in np.argsort(a)])
```

输出：

```
[2 0 4 6 5 3 1]

[(1, 0), (1, 9), (3, 0), (4, 1), (4, 2), (4, 4), (5, 4)]

[(1, 9), (1, 0), (3, 0), (4, 4), (4, 2), (4, 1), (5, 4)]
```

输入：

```
a=np.array([[ 2, 7, 4, 2],
            [35, 9, 1, 5],
            [22, 12, 3, 2]])
#按最后一列顺序排序
print(a[np.lexsort(a.T)])
#按最后一列逆序排序
print(a[np.lexsort(-a.T)])
#按第一列顺序排序
print(a[np.lexsort(a[:,::-1].T)])
#按最后一行顺序排序
```