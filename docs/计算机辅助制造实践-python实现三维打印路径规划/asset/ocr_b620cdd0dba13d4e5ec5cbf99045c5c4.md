面我们就是需要从角度表中找出相交次数最少的角度值，并将它作为最少跳刀填充角度。在_initAngleTable 函数中,我们用到了 round 函数确保角度表中的各角度值保留的小数位数是可控的,这一点其实非常重要,因为在后续代码中,我们正是根据可控的小数位数在角度表(字典)中查询相应角度的交点个数的。

试想: 如果小数位数不可控,尽管数值相似,但我们可能根本无法从角度表中找到目标键值。

在得到了_fingAnglePairs(四顶点角度对计算函数)和_initAngleTable(扫描角度表构建函数)两个函数之后,下面我们结合上一小节最后讨论的Ψ取值的 3 种情况给出最少跳刀填充角度的计算函数,函数名为 findOptFeedDir,注意,这个函数没有下划线前缀,它是一个对外输出的接口函数,其具体实现代码如下:

| FindOptFeedDir.py | 最少跳刀优化模块,寻找最少跳刀填充角度函数 | 9 - 16 |
|---|---|---|
| 1 ...... | # 导入相关库,忽略其他代码 | |
| 2 def findOptFeedDir(polygons, digit = 0, adjustPolyDirs = False): # 定义函数 | | |
| 3 anglePairs = _findAnglePairs(polygons, adjustPolyDirs) | # 获取四顶点角度对列表 | |
| 4 angleTable = _initAngleTable(digit) | # 获取扫描角度表(字典) | |
| 5 delta = math.pow(10, -digit) | # 扫描角度增量 | |
| 6 for a, b in anglePairs: | # 遍历角度对,获取切线角度 | |
| 7 if b <= 180: | # 情况 1:[a, b] | |
| 8 key = round(a + delta, digit) | # 从切线角度上限开始 | |
| 9 while key <= round(b - delta, digit): | # 遍历直至切线角度下限 | |
| 10 angleTable[key] += 1 | # 当前角度上的交点个数累加 1 | |
| 11 key = round(key + delta, digit) | # 角度自增 delta | |
| 12 elif a >= 180: | # 情况 2:[a-180, b-180] | |
| 13 key = round(a - 180 + delta, digit) | # 同上情况 1 | |
| 14 while key <= round(b - 180 - delta, digit): | | |
| 15 angleTable[key] += 1 | | |
| 16 key = round(key + delta, digit) | | |
| 17 elif a <= 180 and b >= 180: | # 情况 3:[0, a'] U [b', 180) | |
| 18 b = b - 180 | # 先对 a,b 值进行调整 | |
| 19 a, b = min(a, b), max(a, b) | | |
| 20 key = round(delta, digit) | # 前半段 [0, a'] | |
| 21 while key < a: | | |
| 22 angleTable[key] += 1 | | |
| 23 key = round(key + delta, digit) | | |
| 24 key = round(b + delta, digit) | # 后半段 [b', 180) | |
| 25 while key < 180: | | |
| 26 angleTable[key] += 1 | | |
| 27 key = round(key + delta, digit) | | |
| 28 return angleTable | # 返回角度表 | |