# 2. 显示共享 x 轴多轴图表的标签文字：Ch10_3_2a.py

在多轴图表中因为有 2 个 y 轴，共享 1 个 x 轴，不同于 10.1.3 节，需要调用 set_xlabel() 函数和 set_ylabel() 函数来显示轴的标签文字，如下所示：

```
x = np.linspace(0, 10, 50)
sinus = np.sin(x)
sinhs = np.sinh(x)
fig, ax = plt.subplots()
ax.plot(x, sinus, "r-o")
ax.set_xlabel("x", color="green")
ax.set_ylabel("Sin(x)", color="red")
ax2 = ax.twinx()
ax2.plot(x, sinhs, "g--")
ax2.set_ylabel("Sinh(x)", color="blue")
plt.show()
```

上述程序代码中，ax 轴分别调用 set_xlabel() 函数和 set_ylabel() 函数显示 x 轴和 y 轴的标签文字。因为共享 x 轴，所以 ax2 只需调用 set_ylabel() 函数显示 y 轴的标签文字即可，其中 color 参数是文字色彩。其运行结果如图 10-28 所示。

![](bbox=[0.22, 0.5, 0.72, 0.72])

# 3. 显示共享 x 轴多轴图表的图例：Ch10_3_2b.py

同样可以在共享 x 轴的多轴图表中显示图例。因为共有 2 个 y 轴，所以需要分别自行显示图例，如下所示：

```
x = np.linspace(0, 10; 50)
sinus = np.sin(x)
sinhs = np.sinh(x)
fig, ax = plt.subplots()
```