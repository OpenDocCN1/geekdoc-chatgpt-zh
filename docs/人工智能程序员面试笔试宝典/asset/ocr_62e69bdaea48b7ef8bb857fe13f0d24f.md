假设给定数组为：[4, 5, 6, 4, 7, 4, 6, 4, 7, 8, 5, 6, 4, 3, 10, 8]，num1=4，num2=8。
根据以上方法，执行过程如下。

+   1） 在遍历的时候首先会遍历到4，下标为lastPos1=0，由于此时还没有遍历到num2，因此，没必要计算num1与num2的最小距离。
+   2） 接着往下遍历，又遍历到num1=4，更新lastPos1=3。
+   3） 接着往下遍历，又遍历到num1=4，更新lastPos1=7。
+   4） 接着往下遍历，又遍历到num2=8，更新lastPos2=9；此时由于前面已经遍历到过num1，因此，可以求出当前num1与num2的最小距离为|lastPos2-lastPos1|=2。
+   5） 接着往下遍历，又遍历到num2=8，更新lastPos2=15；此时由于前面已经遍历到过num1，因此，可以求出当前num1与num2的最小距离为|lastPos2-lastPos1|=8；由于8>2，所以，num1与num2的最小距离为2。实现代码如下：

```
def minDistance2(array,num1,num2):
    if array == None or len(array) <1:
        print("参数不合理")
        return 2**32
    lastPos1 = -1        # 上次遍历到 num1 的位置
    lastPos2 = -1        # 上次遍历到 num2 的位置
    minDis = 2**32       # num1 和 num2 的最小距离
    i = 0
    while i < len(array):
        if array[i] == num1:
            lastPos1 = i
            if lastPos2 >=0:
                minDis = min(minDis,lastPos1 - lastPos2)
        if array[i] == num2:
            lastPos2 = i
            if lastPos1 >=0:
                minDis = min(minDis,lastPos2-lastPos1)
        i +=1
    return minDis
```

# 算法性能分析：

这种方法只需要对数组进行一次遍历，因此，时间复杂度为O(N)。