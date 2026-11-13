```
......
def adjustPolygonDirs( polygons ):
    for i in range( len( polygons ) ):
        pt = polygons[ i ].startPoint( )
        insideCount = 0
        for j in range( len( polygons ) ):
            if j == i: continue
            restPoly = polygons[ j ]
            if 1 == pointInPolygon( pt, restPoly ):
                evenCount += 1
    if evenCount % 2 == 0:
        polygons[ i ].makeCCW( )
    else:
        polygons[ i ].makeCW( )
```

值得一提的是，识别多边形轮廓集合中各个多边形方向的方法不止一种，这里之所以倾向于选择上述方法完全是基于我们之前在 GeomAglo 模块中建立的 pointInPolygon 函数。假设我们没有先前基础，绕开 pointInPolygon 函数，那么这个问题又该如何解决呢？一种可行的方案是使用射线法，如图 4.10 所示，从当前多边形轮廓上的任意一（顶）点出发作射线，计算该射线和其余多边形的交点个数。如果交点个数为偶数，则该多边形为外边界，方向应为逆时针；反之如果交点个数为奇数，则该多边形为内边界，方向应为顺时针。这个方案的核心思想以及包含的基础运算和 pointInPolygon 函数十分相似，有兴趣的读者不妨去实现一下，或者思考更为简洁的识别方法。

![](bbox=[0.235, 0.608, 0.715, 0.792])

图 4.10 使用射线法识别轮廓内、外属性及方向

# 4.6 切片数据存取

现在，我们已经可以对一个 STL 模型进行切片，最终得到一系列 layers，其中每个 layer 又包含一系列封闭的 contours。这时，我们可以继续往下推进我们的工作，或者可以考虑