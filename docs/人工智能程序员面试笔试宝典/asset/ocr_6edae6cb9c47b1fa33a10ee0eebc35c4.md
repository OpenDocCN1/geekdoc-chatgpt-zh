```
def MinDistance3(a,b,c):
    MinSum = 0                # 最小的绝对值和
    Sum = 0                   # 计算三个绝对值的和，与最小做比较
    MinOfabc = 0              # a[i],b[j], c[k]的最小值
    cnt = 0                   # 循环次数统计，最多是 1+m+n 次
    i = j = k = 0
    MinSum = (abs(a[i]-b[j])+abs(a[i]-c[k])+abs(b[j]-c[k])) // 2
    while cnt <= len(a)+len(b)+len(c):
        Sum = (abs(a[i]-b[j])+abs(a[i]-c[k])+abs(b[j]-c[k])) // 2
        MinSum = MinSum if MinSum < Sum else Sum
        MinOfabc = mins(a[i],b[j],c[k])      #找到 a[i],b[j],c[k]的最小值
        # 判断哪个是最小值，做相应的索引移动
        if MinOfabc == a[i]:
            i += 1
            if i >= len(a):
                break
        if MinOfabc == b[j]:
            j += 1
            if j >= len(b):
                break
        if MinOfabc == c[k]:
            k += 1
            if k >= len(c):
                break
        cnt += 1
    return MinSum
```

程序的运行结果为：

最小距离为: 5

# 算法性能分析：

与方法二类似，这种方法最多需要执行(1+m+n)次循环，因此，时间复杂度为O(1+m+n)。