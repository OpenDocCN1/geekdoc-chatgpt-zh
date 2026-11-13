```
ax.plot(x, sinus, "r-o", label="Sin(x)")
ax.set_xlabel("x", color="green")
ax.set_ylabel("Sin(x)", color="red")
ax.legend(loc="best")
ax2 = ax.twinx()
ax2.plot(x, sinhs, "g--", label="Sinh(x)")
ax2.set_ylabel("Cos(x)", color="blue")
ax2.legend(loc="best")
plt.show()
```

上述程序代码分别调用 ax.legend() 函数和 ax2.legend() 函数显示 2 条线的图例，因为调用 2 次，所以是分开显示的 2 个图例。其运行结果如图 10-29 所示。

![](bbox=[0.28, 0.335, 0.76, 0.555])

# 图 10-29

# 4. 在共享 x 轴多轴图表显示单一图例: Ch10_3_2c.py

在 Python 程序: Ch10_3_2b.py 的图例有 2 个，也可以修改程序代码，让 2 条线显示在同一个图例，如下所示：

```
x = np.linspace(0, 10, 50)
sinus = np.sin(x)
sinhs = np.sinh(x)
fig, ax = plt.subplots()
lns1 = ax.plot(x, sinus, "r-o", label="Sin(x)")
ax.set_xlabel("x", color="green")
ax.set_ylabel("Sin(x)", color="red")
ax2 = ax.twinx()
lns2 = ax2.plot(x, sinhs, "g--", label="Sinh(x)")
ax2.set_ylabel("Sinh(x)", color="blue")
# 自行建立图例来显示所有标签
```