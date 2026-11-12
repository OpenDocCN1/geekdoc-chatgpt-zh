```
33 continue outer
34
35 # 无法围绕当前圆盘放置子圆盘
36 active.remove(current)
37
38 return placed.all()
```

# 3. 数据结构

该算法需要两个数据结构：一个用于存储活动位置的列表；一个用于保存放置的圆盘并检测重叠情况的数据结构。

活动列表具有以下结构：

```
1 class ActiveList:
2 function add(disk: Disk)
3 function remove(disk: Disk)
4 function isEmpty() -> bool
5 function getNext() -> Disk
```

这可以使用语言的常规可增长数组来实现，并在每次迭代中选择一个随机元素；也可以将其实现为大多数语言都提供的先进先出（First-In First-Out，FIFO）队列。在后一种情况下，早放置的圆盘将首先被处理，因此其放置方式是从中心开始增大的。

用于保存已放置的圆盘的数据结构：

```
1 class PlacedDisks:
2 function add(disk: Disk)
3 function empty(disk: Disk) -> bool
4 function all() -> Disk[]
```

其性能的关键部分是检查重叠情况。通常将其实现为网格（Grid），网格中放置圆盘并存储其位置和半径的记录，以检查重叠情况。

由于圆盘一旦放置就不会被删除，因此可以使用两个数据结构来实现此目的：一个是用于检查重叠情况的网格，另一个是用于在算法完成后返回放置圆盘的简单数组。具体实现如下：

```
1 class PlacedDisks:
2 cells: bool[GRID_WIDTH * GRID_HEIGHT]
3 disks: Disk[]
4
5 # 返回给定圆盘的单元索引
6 function allCells(disk: Disk) -> int[]
```