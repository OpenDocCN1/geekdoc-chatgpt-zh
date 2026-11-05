# 9.19 如何求解最小三元组距离

# 题目描述：

已知三个升序整数数组a[1]、b[m]和c[n]，请在三个数组中各找一个元素，使得组成的三元组距离最小。三元组距离的定义是：假设a[i]、b[j]和c[k]是一个三元组，那么距离为：Distance=max(|a[i]-b[j]|, |a[i]-c[k]|, |b[j]-c[k]|)，请设计一个求最小三元组距离的最优算法。

# 分析与解答：

最简单的方法就是找出所有可能的组合，从所有的组合中找出最小的距离，但是显然这种方法的效率比较低下。通过分析发现，当ai≤bi≤ci时，此时它们的距离肯定为Di=ci-ai。此时就没必要求bi-ai与ci-ai的值了，从而可以省去很多没必要的步骤，下面分别详细介绍这两种方法。

# 方法一：蛮力法

最容易想到的方法就是分别遍历三个数组中的元素，对遍历到的元素分别求出它们的距离，然后从这些值里面查找最小值，实现代码如下：

```
def maxs(a,b,c):
    Maxs = b if a < b else a
    Maxs = c if Maxs < c else Maxs
    return Maxs

def MinDistance1(a,b,c):
    aLen = len(a)
    bLen = len(b)
    cLen = len(c)
    minDist = maxs(abs(a[0]-b[0]),abs(a[0]-c[0]),abs(b[0]-c[0]))
    dist = 0
    i = 0
    while i < aLen:
        j = 0
        while j < bLen:
            k = 0
            while k < cLen:
                dist = maxs(abs(a[i]-b[j]),abs(a[i]-c[k]),abs(b[j]-c[k]))
                # 找出最小距离
                if minDist > dist:
                    minDist = dist
                k += 1
            j += 1
        i += 1
    return minDist

if __name__ == "__main__":
    a = [3,4,5,7,15]
    b = [10,12,14,16,17]
    c = [20,21,23,24,37,30]

print("最小距离为：",MinDistance1(a,b,c))
```

# 程序的运行结果为：

最小距离为: 5

# 算法性能分析：

这种方法的时间复杂度为O(1×m×n)，显然这种方法没有用到数组升序这一特性，因此，该方法肯定不是最好的方法。