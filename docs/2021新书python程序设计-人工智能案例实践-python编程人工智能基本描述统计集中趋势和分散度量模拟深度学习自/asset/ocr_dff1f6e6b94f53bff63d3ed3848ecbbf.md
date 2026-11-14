# update函数：配置柱状图和文本

update函数中的第16行调用matplotlib.pyplot模块的cla（清除坐标轴）函数，以在为当前动画帧绘制新的柱状图元素之前删除现有的柱状图元素。前一章的数据科学入门部分讨论了第17～27行中的代码。第17～20行代码创建柱状图，设置柱状图的标题，设置x轴和y轴的标签，并缩放柱状图，为每个柱子上方显示每种点数的出现次数及其百分比的文本留出空间。第23～27行显示每种点数的出现次数及其百分比文本。

```
15    # reconfigure plot for updated die frequencies
16    plt.cla()   # clear old contents contents of current Figure
17    axes = sns.barplot(faces, frequencies, palette='bright')  # new bars
18    axes.set_title(f'Die Frequencies for {sum(frequencies):,} Rolls')
19    axes.set(xlabel='Die Value', ylabel='Frequency')
20    axes.set_ylim(top=max(frequencies) * 1.10)  # scale y-axis by 10%
21
22    # display frequency & percentage above each patch (bar)
23    for bar, frequency in zip(axes.patches, frequencies):
24        text_x = bar.get_x() + bar.get_width() / 2.0
25        text_y = bar.get_height()
26        text = f'{frequency:,}\n{frequency / sum(frequencies):.3%}'
27        axes.text(text_x, text_y, text,
28                ha='center', va='bottom')
```

# 用于配置图形和维护状态的变量

第30和31行使用sys模块的argv列表获取脚本的命令行参数。第33行指定了Seaborn的'whitegrid'样式。第34行调用matplotlib.pyplot模块的figure函数来获取Figure对象，Figure对象的FuncAnimation函数显示动画。函数的参数是窗口的标题。你很快会看到，这是FuncAnimation所需的参数之一。第35行创建一个列表，其中包含要显示在绘图的x轴上的每面的点数值1～6。第36行创建了包含6个元素的frequencies列表，每个元素初始化为0—我们使用每个面出现的次数更新该列表的计数：

```
29    # read command-line arguments for number of frames and rolls per frame
30    number_of_frames = int(sys.argv[1])
31    rolls_per_frame = int(sys.argv[2])
32
33    sns.set_style('whitegrid')   # white background with gray grid lines
34    figure = plt.figure('Rolling a Six-Sided Die')   # Figure for animation
35    values = list(range(1, 7))   # die faces for display on x-axis
36    frequencies = [0] * 6   # six-element list of die frequencies
37
```