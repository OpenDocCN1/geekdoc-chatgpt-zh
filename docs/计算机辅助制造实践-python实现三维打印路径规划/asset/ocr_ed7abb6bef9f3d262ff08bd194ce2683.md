我们去额外安装。对函数 makeMessySegs，它有 3 个参数，其中 circleNum 用于指定生成的轮廓数量，默认为 10；segNumPerCircle 用于指定对每个圆的细分份数，默认为 100000 份，即每个圆最终可得到 100000 条线段；radius 用于指定同心圆的最小半径，默认为 100 毫米，其余圆半径以 10 毫米递增。

在 makeMessySegs 函数中，我们打印了两条信息，第一条是生成的线段总条数，第二条是生成的线段列表中最短的线段长度。我们必须知道生成的最小线段长度是多少，这是因为我们之前介绍的暴力拼接法和字典序排序拼接法都会用到点的重合判断函数 isCoincide，而在这个函数中，我们认为只要点的距离小于一个极小的给定值( 默认为 10^-7 毫米)，就认为两个点是重合的。因此，在这里生成的线段的最小长度不能小于 10^-7 毫米，至少要比这个数大一个数量级，即 10^-6 毫米以上，否则将引起拼接算法失效。

最后，我们来看一下如何打乱生成的线段列表 segs。和之前介绍的一样，我们先对 segs 进行循环遍历。对当前线段，首先使用 Python 的 random 库中的 randint 函数生成一个随机整数 rd，显然该 rd 作为序号不能越界，且必须大于 0，因此我们给的随机数生成范围是 [0, len( segs) - 1]；然后我们对当前线段和第 rd 条线段进行交换。经过这样的处理，当遍历完成后，我们就可以得到在平面上散乱分布的线段了。

# 6.5.2 拼接算法测试

接下来我们使用 makeMessySegs 生成的平面散乱线段来测试我们已经实现的 3 种线段拼接算法，包括暴力拼接法、字典序排序拼接法以及字典查询拼接法，测试用主机 CPU 为 Intel Core i7-8700@ 3.20GHz CPU。我们将测试代码写在一个临时 Test_linkSegs.py 文件中，具体如下：

| Test_linkSegs.py | 测试 3 种拼接算法的性能 | 6 - 10 |
|---|---|---|
| 1 | from SliceAlgo import * | # 导入 SliceAlgo 库 |
| 2 | from MakeMessySegs import * | # 导入散乱线段生成库 |
| 3 | segs = makeMessySegs(10, 10000) | # 生成散乱线段，保存到 segs |
| 4 | start = time.clock() | |
| 5 | contours1 = linkSegs_dorder( segs) | # 1. 字典序排序法拼接，dorder，并计时 |
| 6 | end = time.clock() | |
| 7 | print( "dorder: %f CPU seconds" % ( end - start)) | |
| 8 | start = time.clock() | |
| 9 | contours2 = linkSegs_dlook( segs) | # 2. 字典查询法拼接，dlook，并计时 |
| 10 | end = time.clock() | |
| 11 | print( "dlook: %f CPU seconds" % ( end - start)) | |
| 12 | start = time.clock() | |
| 13 | contours3 = linkSegs_brutal( segs) | # 3. 暴力法拼接，brutal，并计时 |
| 14 | end = time.clock() | |
| 15 | print( "brutal: %f CPU seconds" % ( end - start)) | |

我们可以根据测试需要修改 makeMessySegs 函数中的参数。这里，我们将第 1 个参数