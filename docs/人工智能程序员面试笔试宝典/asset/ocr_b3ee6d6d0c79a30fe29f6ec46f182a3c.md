```
n = len(strs)
if n < 1:
    return
i = 0
while i < n-1:
    # 找回子串长度为奇数的情况
    self.expandBothSide(strs,i,i)
    # 找回子串长度为偶数的情况
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
```

程序的运行结果为：

```
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
```

从运行结果可以看出，模式串P="abaabc"的next数组为[-1,0,0,1,1]，next[3]=1，说明p[0]=p[2]。当i=3，j=3的时候S[i]!=P[j]，此时主串S不需要回溯，跟模式串位置j=next[j]=next[3]=1的字符继续进行比较。因为此时S[i-1]一定与P[0]相等，所以，就没有必要再比较了。

# 算法性能分析：

这种方法在求next数组的时候循环执行的次数为n(n为模式串的长度)，在模式串与主串匹配的过程中循环执行的次数为m(m为主串的长度)。因此，算法的时间复杂度为O(m+n)。但是由于算法中请了额外的n个存储空间来存储next数组，因此，算法的空间复杂度为O(N)。