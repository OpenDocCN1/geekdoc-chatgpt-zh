# 字符串算法实战速记

下面把几类常见的字符串处理思路用 Python 的实现方式做一次梳理，重点展示编辑距离、子串定位、回文查找、KMP 匹配轨迹以及最长公共子串的 DP 写法。代码保留原逻辑，文字部分做了重新组织，方便在自媒体场景快速复现。

编辑距离的核心是用二维表记录两串之间的最小操作代价，插入、删除和替换三种操作在表格里对应不同的递推。示例里分别令替换权重为 1 和 2，待比较的两串是 bciln 与 fciling，运行后会打印出完整的距离矩阵并给出最终距离。

            while j < len(s2)+1:

                if list(s1)[i-1] == list(s2)[j-1]:

                    D[i][j] = self.mins(D[i-1][j]+1,D[i][j-1]+1,D[i-1][j-1])

                else:

                    D[i][j] = min(D[i-1][j]+1,D[i][j-1]+1,D[i-1][j-1]+replaceWeight)

                j += 1

            i += 1

        print("-----------------------------")

        i = 0

        while i < len(s1)+1:

            j = 0

            while j < len(s2)+1:

                print(D[i][j],end=' ')

                j += 1

            print()

            i += 1

        i += 1

        print("-----------------------------")

        dis = D[len(s1)][len(s2)]

        return dis

if __name__ == "__main__":

    s1 = "bciln"

    s2 = "fciling"

    ed = EditDistance()

    print("第一问：")

    print("编辑距离为：",str(ed.edit(s1,s2,1)))

    print("第二问：")

    print("编辑距离为：",str(ed.edit(s1,s2,2)))

[PRE0]

第一问：

-----------------------------

01234567

11234567

22123456

33212345

44321234

55432223

-----------------------------

编辑距离为： 3

第二问：

-----------------------------

01234567

12345678

23234567

34323456

45432345

56543434

-----------------------------

编辑距离为： 4

子串定位提供一个简易的暴力回溯实现，先校验参数合法性，再用双指针尝试对齐。匹配失败时指针会回退到下一个可能起点，最终返回模式串首次出现的位置下标，否则返回 -1。

[PRE1]

"""

**** 方法功能：判断 p 是否是 s 的子串，如果是返回 p 在 s 中第一次出现的下标，否则返回-1

**** 输入参数：s 和 p 分别为主串和模式串

"""

def match(s,p):

    # 检查参数是否合理

    if s == None or p == None:

        print("参数不合理")

        return -1

    slength = len(s)

    plength = len(p)

    # p 肯定不是 s 的子串

    if slength < plength:

        return -1

    i = 0

    j = 0

    while i < slength and j < plength:

        if list(s)[i] == list(p)[j]:

            # 如果相同，那么继续比较后面的字符

            i += 1

            j += 1

        else:

            # 后退回去重新比较

            i = i - j + 1

            j = 0

            if i > (slength - plength):

                return -1

    if j >= plength:      # 匹配成功

        return i - plength

    return -1

if __name__ == "__main__":

    s = "xyzsabcd"

    p = "abc"

    print(match(s,p))

回文查找采用中心扩展策略。Test 类维护当前最长回文的起始下标与长度，expandBothSide 方法以给定左右中心向外扩展，getLongestPalindrome 遍历所有奇数和偶数中心。

[PRE2]

class Test:

    def __init__(self):

        self.startIndex = None

        self.lens = 0

    def getStartIndex(self):

        return self.startIndex

    def getLens(self):

        return self.lens

    # 对字符串 str，以 c1 和 c2 为中心向两侧扩展寻找回文字符串

    def expandBothSide(self,strs,c1,c2):

        n = len(strs)

        while c1 >= 0 and c2 < n and list(strs)[c1] == list(strs)[c2]:

            c1 -= 1

            c2 += 1

        tmpStartIndex = c1 + 1

        tmpLen = c2 - c1 - 1

        if tmpLen > self.lens:

            self.lens = tmpLen

            self.startIndex = tmpStartIndex

    # 方法功能：找出字符串最长的回文子串

    def getLongestPalindrome(self,strs):

        if strs == None:

            return

[PRE3]

n = len(strs)

if n < 1:

    return

i = 0

while i < n-1:

    # 寻找子串长度为奇数的情况

    self.expandBothSide(strs,i,i)

    # 寻找子串长度为偶数的情况

    self.expandBothSide(strs,i,i+1)

    i += 1

if __name__ == "__main__":

    strs = "abcdefgedxyz"

    t = Test()

    t.getLongestPalindrome(strs)

    if t.getStartIndex() != -1 and t.getLens() != -1:

        print("最长的回文字符串为：",end="")

        i = t.getStartIndex()

        while i < t.getStartIndex()+t.getLens():

            print(list(strs)[i],end="")

            i += 1

else:

    print("查找失败")

KMP 的 next 数组示例展示了在匹配过程中 i、j 指针的移动轨迹，记录每次比较与回溯的状态，最终给出匹配位置。

[PRE4]

next 数组为: -1,0,0,1,1

i=0,j=0

i=1,j=1

i=2,j=2

i=3,j=3

i=3,j=1

i=4,j=2

i=5,j=3

i=5,j=1

i=6,j=2

i=7,j=3

i=8,j=4

i=9,j=5

匹配结果为: 4

最长公共子串的经典 DP 做法是用二维表记录以对应位置结尾的最长相等前缀长度，相等时继承左上值加一，否则置零，最后回溯得到最长片段。

[PRE5]

**** 方法功能：获取两个字符串的最长公共子串

**** 输入参数：str1 和 str2 为指向字符的指针

[PRE6]

def getMaxSubStr(str1,str2):

    len1 = len(str1)

    len2 = len(str2)

    sb = ""

    maxs = 0

    maxI = 0

    # 申请新的空间来记录公共子串长度信息

    M = [[0 for i in range(len1+1)] for j in range(len2+1)]

    # 通过利用通用公式写新建立的二维数组（公共子串的长度信息）

    i = 0

    while i < len1+1:

        M[i][0] = 0

        i += 1

    j = 0

    while j < len2+1:

        M[0][j] = 0

        j += 1

    i = 1

    while i < len1+1:

        j = 1

        while j < len2+1:

            if list(str1)[i-1] == list(str2)[j-1]:

                M[i][j] = M[i-1][j-1] + 1

                if M[i][j] > maxs:

                    maxs = M[i][j]

                    maxI = i

            else:

                M[i][j] = 0

            j += 1

        i += 1

    # 找出公共子串

    i = maxI - maxs

    while i < maxI:

        sb += "".join(list(str1)[i])

        i += 1

    return sb

if __name__ == "__main__":

    str1 = "abcade"

    str2 = "dcadde"

    print(getMaxSubStr(str1,str2))

另一种基于对齐起点的逐行比较实现，遍历所有可能的起始偏移，统计连续相等字符的最大长度并记录结束位置，思路等价但遍历方式不同。

[PRE7]

def getMaxSubStr(s1,s2):

    len1 = len(s1)

    len2 = len(s2)

    maxLen = 0

    tmpmaxLen = 0

    maxLenEnd = 0

    sb = ""

    i = 0

    while i < len1 + len2:

        s1begin = s2begin = 0

        tmpmaxLen = 0

        if i < len1:

            s1begin = len1 - i

        else:

            s2begin = i - len1

        j = 0

        while (s1begin + j < len1) and (s2begin + j < len2):

            if list(s1)[s1begin+j] == list(s2)[s2begin+j]:

                tmpmaxLen += 1

            else:

                if tmpmaxLen > maxLen: