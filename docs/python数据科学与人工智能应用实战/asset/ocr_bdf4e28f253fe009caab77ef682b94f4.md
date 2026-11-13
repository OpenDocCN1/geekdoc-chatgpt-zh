```
a=[1 2 3 4 5 6]
b=np.reshape(a, (3, 2))->
[[1 2]
 [3 4]
 [5 6]]
c=b.T->
[[1 3 5]
 [2 4 6]]
c=b.transpose()->
[[1 3 5]
 [2 4 6]]
c=np.transpose(b)->
[[1 3 5]
 [2 4 6]]
```

# 动手学——新增数组的维度： Ch8_6_1b.py

NumPy 数组的索引可以使用 np.newaxis 对象来新增数组的维度，如下所示：

```
a = np.array([1, 2, 3])
print ("a=" + str(a))
b = a[:, np.newaxis]
print ("b=a[:, np.newaxis]->")
print (b)
print (b.shape)
b = a[np.newaxis, :]
print ("b=a[np.newaxis, :]->")
print (b)
print (b.shape)
```

上述程序代码建立一维数组 a，然后使用 np.newaxis 对象新增维度。第 1 次改成形状 (3,1)，即 3x1；第 2 次改成 (1,3)，即 1x3。其运行结果如下所示：

```
a=[1 2 3]
b=a[:, np.newaxis]->
[[1]
 [2]
 [3]]
(3, 1)
b=a[np.newaxis, :]->
[[1 2 3]]
(1, 3)
```