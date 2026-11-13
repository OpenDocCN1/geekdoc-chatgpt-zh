```
ratings = [5, 6, 15, 3, 12, 4]
explode = (0, 0, 0, 0.2, 0, 0.2)
plt.pie(ratings,
   labels=labels,
   explode=explode)
plt.title("Programming Language Usage")
plt.axis("equal")
plt.show()
```

上述程序代码中，explode 元组值是每一对应切片的突增值，pie() 函数使用 explode 参数来指定突增值。其运行结果如图 10-22 所示。

![](bbox=[0.15, 0.33, 0.85, 0.59])

# 3. 在饼图显示切片色彩的图例：Ch10_2_4b.py

同样地，在饼图中也可以显示切片色彩的图例，如下所示：

```
labels = ["Python", "C++", "Java", "JS", "C", "C#"]
ratings = [5, 6, 15, 3, 12, 4]
explode = (0, 0, 0, 0.2, 0, 0.2)
patches, texts = plt.pie(ratings,
   labels=labels,
   explode=explode)
plt.legend(patches, labels, loc="best")
plt.title("Programming Language Usage")
plt.axis("equal")
plt.show()
```