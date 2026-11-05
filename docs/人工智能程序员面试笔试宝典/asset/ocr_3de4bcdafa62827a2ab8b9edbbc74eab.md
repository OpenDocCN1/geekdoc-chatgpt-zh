然后开始遍历数组。

+   1）如果当前值tmp大于r1： r3=r2， r2=r1， r1=tmp。
+   2）如果当前值tmp大于r2且不等于r1： r3=r2， r2=tmp。
+   3）如果当前值tmp大于r3且不等于r2： r3=tmp。

实现代码如下：

```
def findTop3(array):
    if array == None or len(array) < 3:
        print("参数不合理")
        return
    r1 = r2 = r3 = -2**31
    i = 0
    while i < len(array):
        if array[i] > r1:
            r3 = r2
            r2 = r1
            r1 = array[i]
        elif array[i] > r2 and array[i] != r1:
            r3 = r2
            r2 = array[i]
        elif array[i] > r3 and array[i] < r2:
            r3 = array[3]
        i += 1
    print("前三名分别为：" + str(r1) + "," + str(r2) + "," + str(r3))

    if __name__ == "__main__":
        array = [4,7,1,2,3,5,3,6,3,2]
        findTop3(array)
```

程序的运行结果为：

前三名分别为:7,6,5

# 算法性能分析：

这种方法虽然能够在O(N)的时间复杂度求出前三名，但是当k取值很大的时候，比如求前10名，这种方法就不是很好了。比较经典的方法就是维护一个大小为k的小顶堆来保存最大的k个数，具体思路为：维护一个大小为k的小顶堆用来存储最大的k个数，堆顶保存了堆中最小的数，每次遍历一个数m，如果m比堆顶元素小，那么说明m肯定不是最大的k个数，因此，不需要调整堆，如果m比堆顶元素大，则用这个数替换堆顶元素，替换后重新调整堆为小顶堆。这种方法的时间复杂度为O(Nlog2k)。这种方法适用于数据量大的情况。