# 调用animation模块的FuncAnimation函数

第39~41行调用Matplotlib的animation模块的FuncAnimation函数来动态更新柱状图。函数返回一个表示动画的对象。虽然没有显式地使用这个动画对象，但必须存储动画对象的引用；否则，Python将立即终止动画并将其内存释放给系统：

```
38 # configure and start animation that calls function update
39 die_animation = animation.FuncAnimation(
40 figure, update, repeat=False, frames=number_of_frames, interval=33,
41 fargs=(rolls_per_frame, values, frequencies))
42 plt.show() # display window
```

FuncAnimation有两个必需的参数：

+   - figure—要在其中显示动画的Figure对象；
+   - update—每个动画帧都要调用一次的函数。

在这个例子里，我们还使用以下可选关键字参数：

+   - repeat—如果为False，则在指定的帧数之后终止动画。如果为True（默认值），则当动画完成时，它将从头开始重新播放。
+   - frames—动画帧的总数，它控制FunctAnimation调用update的次数。传递一个整数相当于传递一个范围—例如，600表示range(600)。FuncAnimation将此范围中的一个值作为每次update调用的第一个参数传递。
+   - interval—动画帧之间的毫秒数（本例中为33，缺省值为200）。在每次调用update之后，FuncAnimation将等待33ms，然后再执行下一次调用。
+   - fargs（function arguments的缩写）—传递给FuncAnimation的第二个参数中指定的函数的其他参数的元组。在fargs元组中指定的参数对应于update的参数roll、faces和frequency（第9行）。

有关FuncAnimation的其他可选参数的列表，请参见 https://matplotlib.org/api/_as_gen/matplotlib.animation.FuncAnimation.html。

最后，第43行显示窗口。