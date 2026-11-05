![](bbox=[0.35, 0.08, 0.7, 0.23])

图9-8 链表实现队列

```
class MyQueue(object):
    def __init__(self):
        self.pHead = None
        self.pEnd = None

    # 判断队列是否为空
    def isEmpty(self):
        return self.pHead is None

    # 获取队列中元素的个数
    def size(self):
        size = 0
        p = self.pHead
        while p is not None:
            p = p.next
            size += 1
        return size

    # 入队列，把元素 e 加到队列尾
    def enQueue(self, elem):
        p = LNode(elem)
        if self.isEmpty():
            self.pHead = p
            self.pEnd = p
        else:
            self.pEnd.next = p
            self.pEnd = p

    # 出队列，删除队列首元素
    def deQueueHead(self):
        if self.isEmpty():
            print("出队列失败，队列已经为空")
        self.pHead = self.pHead.next
        if self.pEnd == None:
            self.pEnd = None
```