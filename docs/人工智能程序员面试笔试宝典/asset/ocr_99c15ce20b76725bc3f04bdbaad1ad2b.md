```
def getCharFromIndex(i):
    return '!' if i==10 else ('0'+ str(i))

class DNSCache:
    def __init__(self):
        self.CHAR_COUNT=11    # IP 地址最多有 11 个不同的字符
        self.root=TrieNode() # IP 地址最大的长度
    def insert(self,ip,url):
        # IP 地址的长度
        lens = len(ip)
        pCrawl = self.root
        level=0
        while level<lens:
            # 根据当前遍历到的 IP 中的字符，找出子结点的索引
            index = getIndexFromChar(ip[level])
            # 如果子结点不存在，则创建一个
            if pCrawl.child[index] ==None:
                pCrawl.child[index] = TrieNode()
            # 移动到子结点 */
            pCrawl = pCrawl.child[index]
            # 在叶子结点中存储 IP 对应的 URL
            pCrawl.IsLeaf = True
            pCrawl.url = url
            level +=1

    # 通过 IP 地址找到对应的 URL
    def searchDNSCache(self,ip):
        pCrawl = self.root
        lens = len(ip)
        # 遍历 IP 地址中所有的字符
        level=0
        while level<lens:
            index = getIndexFromChar(ip[level])
            if pCrawl.child[index] ==None:
                return None
            pCrawl = pCrawl.child[index]
            level +=1

        # 返回找到的 URL
        if pCrawl!=None and pCrawl.IsLeaf:
            return pCrawl.url
        return  None

if __name__ == "__main__":
    ipAdds=["10.57.11.127","121.57.61.129","66.125.100.103"]
    url =["www.samsung.com", "www.samsung.net","www.google.in"]
    n = len(ipAdds)
    cache=DNSCache()
    for i in range(n):
        cache.insert(ipAdds[i],url[i])

        i+=1

    ip="121.57.61.129"
    res_url = cache.searchDNSCache(ip)
    if res_url != None:
        print("找到了 IP 对应的URL:\n"+ip+"-->"+ res_url)
    else:
        print("没有找到对应的URL\n")
```

程序的运行结果为：

```
找到了 IP 对应的 URL:
121.57.61.129 --> www.samsung.net
```

显然，由于上述算法中涉及的IP地址只包含特定的11个字符（数字和.），所以，该算法也有一些异常情况不能处理，例如不能处理用户输入的不合理的IP地址的情况，有兴趣的读者可以继续朝着这个思路完善后面的算法。