最后我们讲一下射线的构造。在以上讨论中,我们认为射线的方向是任意的,但这样会给我们的实际计算带来不少麻烦,比如判断点和射线的位置关系。简单的做法就是在不影响结果的前提下找一个特例,而最简单的特例就是让射线方向和 X 轴方向平行,这样要判断点和射线的相对关系只需比较两者 Y 坐标的高低即可。

![](bbox=[0.26, 0.18, 0.7, 0.36])

# ○ 射线与多边形交点
图 2.15 落在射线上的多边形顶点配对

以上我们从实际实现的角度介绍了 XY 平面上点和多边形关系的判断方法,这个方法的时间复杂度为 O(n),其中 n 为多边形顶点数。

# 2.5.4 几何算法模块

接下来,我们将根据以上介绍的实体距离、实体求交、点和多边形关系判断等算法内容,在 GeomAlgo.py 文件中实现相应函数。由于篇幅限制,我们并未实现每个函数的所有细节。GeomAlgo 模块代码如下:

| GeomAlgo.py | 几何算法模块, 封装一些基础计算几何算法 | 2-11 |
|---|---|---|
| 1 | ...... | # 导入必要模块 |
| 2 | def nearZero(x): | # 判断一个数是否接近 0 |
| 3 | return True if math.fabs(x) < epsilon else False | # 判断该数和给定极小数的关系 |
| 4 | def distance(obj1, obj2): | # 实体距离计算 |
| 5 | if isinstance(obj1, Point3D) and isinstance(obj2, Line): | # 情况 1:点和直线距离计算 |
| 6 | P, Q, V = obj2.P, obj1, obj2.V | |
| 7 | t = P.pointTo(Q).dotProduct(V) | |
| 8 | R = P + V.amplified(t) | |
| 9 | return Q.distance(R) | |
| 10 | elif isinstance(obj1, Point3D) and isinstance(obj2, Ray): | # 情况 2:点和射线距离计算 |
| 11 | P, Q, V = obj2.P, obj1, obj2.V | |
| 12 | t = P.pointTo(Q).dotProduct(V) | |
| 13 | if t >= 0: | |
| 14 | R = P + V.amplified(t) | |
| 15 | return Q.distance(R) | |
| 16 | return Q.distance(P) | |