```
minsd = 0
minDist = 2**32
i = 0                # 数组 a 的下标
j = 0                # 数组 b 的下标
k = 0                # 数组 c 的下标
while True:
    curDist = maxs(abs(a[i] - b[j]),abs(a[i] - c[k]),abs(b[j] - c[k]))
    if curDist < minDist:
        minDist = curDist
    # 找出当前遍历到三个数组中的最小值
    minsd = mins(a[i],b[j],c[k])
    if minsd == a[i]:
        i += 1
        if i >= len(a):
            break
    elif minsd == b[j]:
        j += 1
        if j >= len(b):
            break
    else:
        k += 1
        if k >= len(c):
            break
return minDist
```

# 算法性能分析：

采用这种算法最多只需要对三个数组分别遍历一遍，因此，时间复杂度为O(1+m+n)。

# 方法三：数学运算法

采用数学方法对目标函数变形，有两个关键点，第一个关键点：

max{|x1-x2|,|y1-y2|} = (|x1+y1-x2-y2|+|x1-y1-(x2-y2)|)/2 （公式1）

我们假设x1=a[ i ]，x2=b[ j ]，x3=c[ k ]，则

Distance=max(|x1-x2|,|x1-x3|,|x2-x3|)

= max( max(|x1-x2|,|x1-x3|), |x2-x3|) （公式2）

根据公式1，max(|x1-x2|,|x1-x3|)=1/2(|2x1-x2-x3|+|x2-x3|)，带入公式2，得到

Distance=max(1/2(|2x1-x2-x3|+|x2-x3|),|x2-x3|)
=1/2*max(|2x1-x2-x3|,|x2-x3|)+1/2*|x2-x3| //把相同部分1/2*|x2-x3|分离出来
=1/2*max(|2x1-(x2+x3)|,|x2-x3|)+1/2*|x2-x3| //把(x2+x3)看成一个整体，使用公式1
=1/2*1/2*((|2x1-2x2|+|2x1-2x3|)+1/2*|x2-x3|
=1/2*|x1-x2|+1/2*|x1-x3|+1/2*|x2-x3|
=1/2*(|x1-x2|+|x1-x3|+|x2-x3|) //求出等价公式，完成

第二个关键点：如何设计算法找到(|x1-x2|+|x1-x3|+|x2-x3|)的最小值，x1、x2、x3分别是三个数组中的任意一个数，算法思想与方法二相同，用三个下标分别指向a、b、c中最小的数，计算一次它们最大距离的Distance，然后再移动三个数中较小的数组的下标，再计算一次，每次移动一个，直到其中一个数组结束为止。

示例代码如下：