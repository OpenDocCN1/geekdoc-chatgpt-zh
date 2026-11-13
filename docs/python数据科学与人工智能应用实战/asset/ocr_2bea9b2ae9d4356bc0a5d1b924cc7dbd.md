# 3. 显示自定义的刻度（一）：Ch10_3_3b.py

在成功显示图表的刻度列表后，如果对刻度的范围不满意，还可以调用相同函数来指定自定义刻度。本范例为 2 个数据集的折线图，如下所示：

```
days = range(0, 22, 3)
celsius = [25.6, 23.2, 18.5, 28.3, 26.5, 30.5, 32.6, 33.1]
plt.plot(days, celsius)
plt.xlabel("Day")
plt.ylabel("Celsius")
plt.xticks(range(0, 25, 2))
plt.yticks(range(15, 35, 3))
plt.show()
```

上述程序代码中，xticks() 函数和 yticks() 函数指定参数的新刻度范围。其运行结果如图 10-33 所示。

![](bbox=[0.281, 0.411, 0.666, 0.612])

图 10-33

# 4. 显示自定义的刻度（二）：Ch10_3_3c.py

本范例为共享 x 轴的多轴图表，同样可以指定自定义刻度，调用的是 set_xticks() 函数和 set_yticks() 函数，如下所示：

```
x = np.linspace(0, 10, 50)
sinus = np.sin(x)
sinh = np.sinh(x)
fig, ax = plt.subplots()
ax.plot(x, sinus, "r-o")
ax.set_xlabel("x", color="green")
ax.set_ylabel("Sin(x)", color="red")
ax2 = ax.twinx()
ax2.plot(x, sinh, "g--")
```