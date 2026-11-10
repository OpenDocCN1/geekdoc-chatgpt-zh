数组最大子段和与两数最小距离 实战拆解

在数组相关面试题里，连续最大和和两数最近距离是高频考点。下面用几组递进的实现把思路讲清楚，从暴力枚举到线性动态规划，再到带位置追踪和多数组扩展，代码可以直接对照运行。

### 连续最大和的四种写法

先看最直观的双指针枚举思路，通过外层起点内层终点累加求和，保留全局最大值。

            k = i

            while k < j:

                ThisSum += array[k]

                k += 1

            if ThisSum > MaxSum:

                MaxSum = ThisSum

            j += 1

        i += 1

    return MaxSum

if __name__ == "__main__":

    arr = [1, -2, 4, 8, -4, 7, -1, -5]

    print("连续最大和为：", maxSubArray1(array))

[PRE0]
接下来是同样暴力的累计写法，每次从 i 开始向右累加 sums 并即时比较。

def maxSubArray2(array):

    if array == None or len(array) < 1:

        print("数组不存在")

        return

    maxSum = -2**31

    i = 0

    while i < len(array):

        sums = 0

        j = i

        while j < len(array):

            sums += array[j]

            if sums > maxSum:

                maxSum = sums

            j += 1

        i += 1

    return maxSum

[PRE1]
用辅助数组记录状态的动态规划版本，End 存放以当前位结尾的最大和，All 存放遍历到当前位置的全局最优。

def maxSubArray3(array):

    if array == None or len(array) < 1:

        print("数组不存在")

        return

    End = [None]*len(array)

    All = [None]*len(array)

    End[len(array)-1] = array[len(array)-1]

    All[len(array)-1] = array[len(array)-1]

    End[0] = All[0] = array[0]

    i = 1

    while i < len(array):

        End[i] = max(End[i-1]+array[i], array[i])

        All[i] = max(End[i], All[i-1])

        i += 1

    return All[len(array)-1]

[PRE2]
把两个数组压缩成两个变量的空间优化版，nEnd 表示包含末尾的最大子段和，nAll 记录全局最优。

def maxSubArray4(array):

    if array == None or len(array) < 1:

        print("数组不存在")

        return

    nAll = array[0]      # 最大子数组和

    nEnd = array[0]      # 包含最后一个元素的最大子数组和

    i = 1

    while i < len(array):

        nEnd = max(nEnd+array[i], array[i])

        nAll = max(nEnd, nAll)

        i += 1

    return nAll

[PRE3]python
如果还要知道最大子段的起止位置，可以在 Kadane 过程中记录起点。

class Test:

    def __init__(self):

        self.begin = 0        # 记录最大子数组起始位置

        self.end = 0          # 记录最大子数组结束位置

    def maxSubArray(self, array):

        maxSum = -2**31       # 子数组最大值

        nSum = 0              # 包含子数组最后一位的最大值

        nStart = 0

        i = 0

        while i < len(array):

            if nSum < 0:

                nSum = array[i]

                nStart = i

            else:

                nSum += array[i]

                if nSum > maxSum:

                    maxSum = nSum

                    self.begin = nStart

                    self.end = i

            i += 1

        return maxSum

    def getBegin(self):

        return self.begin

    def getEnd(self):

        return self.end

if __name__ == "__main__":

    t = Test()

    arr = [1, -2, 4, 8, -4, 7, -1, -5]

    print ("连续最大和为: " + str(t.maxSubArray(arr)))

    print ("最大和对应的数组起始与结束坐标分别为: " + str(t.getBegin()) + "," + str(t.getEnd()))

[PRE4]
### 两数最小距离的两种策略

第一种是双层遍历，凡是遇到 num1 就再扫一遍找 num2，记录最短下标差。

def minDistance1(array,num1,num2):

    if array == None or len(array) < 1:

        print("参数不合理")

        return 2**32

    minDis = 2**32          # num1 和 num2 的最小距离

    dist = 0

    i = 0

    while i < len(array):

        if array[i] == num1:

            j = 0

            while j < len(array):

                if array[j] == num2:

                    dist = abs(i - j)          # 当前遍历的 num1 和 num2 的距离

                    if dist < minDis:

                        minDis = dist

                j += 1

        i += 1

    return minDis

if __name__ == "__main__":

    array = [4,6,7,4,7,4,6,7,8,5,6,4,3,10,8]

    num1 = 4

    num2 = 8

    print(minDistance1(array,num1,num2))

[PRE5]
单次扫描记录最近出现位置即可把复杂度降到线性，更新 lastPos1 与 lastPos2 时即时计算距离。

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

[PRE6]
### 三数组最小距离的暴力扩展

当要找三个数组两两之间的最小距离时，先写一个三数取最大辅助函数，再三层循环枚举所有组合。

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