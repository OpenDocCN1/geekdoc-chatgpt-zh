```
v4 = np.random.randint(1, 101)
print(v3, v4)
```

上述程序代码调用 seed() 函数指定随机数的种子数后，调用 2 次 random() 函数产生 2 个 0~1 的浮点数，然后是 5~9 和 1~100 的整数值。其运行结果如下所示：

```
0.3367724725390667 0.5269343749958971
6 46
```

# 动手学——使用随机函数产生数组元素值：Ch8_6_2a.py

可以使用 random 模块的 rand() 函数来产生浮点数数组值，randint() 函数产生整数值数组，如下所示：

```
a = np.random.random.rand(5)
print("np.random.random.rand(5)=")
print(a)
b = np.random.random.rand(3, 2)
print("np.random.random.rand(3, 2)=")
print(b)
c = np.random.random.randint(5, 10, size=5)
print("np.random.random.randint(5, 10, size=5)")
print(c)
d = np.random.random.randint(5, 10, size=(2, 3))
print("np.random.random.randint(5, 10, size=(2, 3))")
print(d)
```

上述程序代码调用 2 次 rand() 函数，第 1 次是 5 个元素的一维数组，第 2 次是 3×2 的二维数组，元素值是 0~1 的浮点数；然后调用 2 次 randint() 函数，使用 size 属性指定产生 5 个元素的一维数组和 2×3 的二维数组（size 属性值是元组），元素值是 5~9 的整数。其运行结果如下所示：

```
np.random.random.rand(5)=
[ 0.14827974  0.56508804  0.81026024  0.35193408  0.67416147]
np.random.random.rand(3, 2)=
[[ 0.3857823   0.75199775]
 [ 0.68688449  0.11550551]
 [ 0.74191692  0.59480112]]
np.random.random.randint(5, 10, size=5)
[7 5 5 7 8]
np.random.random.randint(5, 10, size=(2, 3))
[[7 5 7]
 [5 9 7]]
```