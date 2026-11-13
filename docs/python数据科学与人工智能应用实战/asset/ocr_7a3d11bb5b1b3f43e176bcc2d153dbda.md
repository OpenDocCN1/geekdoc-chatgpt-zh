上述程序代码调用 set_title() 函数显示标题文字，其参数 fontsize 是字型尺寸；使用 for/in 循环获取各轴的标签文字，并更改字型、尺寸、色彩和样式。其运行结果如图 10-31 所示。

![](bbox=[0.338, 0.129, 0.735, 0.307])

图 10-31

# 2. 显示图表的刻度：Ch10_3_3a.py

调用 x 轴的 xticks() 函数和 y 轴的 yticks() 函数返回刻度和标签文字列表，如下所示：

```
days = range(0, 22, 3)
celcius = [25.6, 23.2, 18.5, 28.3, 26.5, 30.5, 32.6, 33.1]
plt.plot(days, celcius)
plt.xlabel("Day")
plt.ylabel("Celcius")
locs, labels = plt.xticks()
print(locs, labels)
locs, labels = plt.yticks()
print(locs, labels)
plt.show()
```

上述程序代码中，xticks() 函数和 yticks() 函数可以返回 locs 刻度和 labels 标签列表（这是 xticklabel 对象）。在其运行结果中可以显示刻度列表，如图 10-32 所示。

```
[ -5. 0. 5. 10. 15. 20. 25.] <a list of 7 Text ticklabel objects>
[ 16. 18. 20. 22. 24. 26. 28. 30. 32. 34.] <a list of 10 Text yticklabel objects>
```

![](bbox=[0.326, 0.747, 0.633, 0.903])

图 10-32