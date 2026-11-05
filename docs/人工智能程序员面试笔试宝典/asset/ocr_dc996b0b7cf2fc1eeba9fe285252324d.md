```
if list(s1)[s1begin+j] == list(s2)[s2begin+j]:
    tmpmaxLen += 1
else:
    if tmpmaxLen > maxLen:
        maxLen = tmpmaxLen
        maxLenEnd = s1begin + j
    else:
        tmpmaxLen = 0
    j += 1
if tmpmaxLen > maxLen:
    maxLen = tmpmaxLen
    maxLenEnd = s1begin + j
    i += 1
i = maxLenEnd - maxLen
while i < maxLenEnd:
    sb = sb + list(s1)[i]
    i += 1
return sb

if __name__ == "__main__":
    str1 = "abcade"
    str2 = "dgcade"
    print(getMaxSubStr(str1,str2))
```

# 算法性能分析：

这种方法用双重循环来实现，外层循环的次数为m+n（其中，m和n分别为两个字符串的长度），内层循环最多执行n次，算法的时间复杂度为O（(m+n)×n）。此外，这种方法只使用了几个临时变量，因此算法的空间复杂度为O(1)。