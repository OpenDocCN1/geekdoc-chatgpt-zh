```
```python
class LNode:
    def __init__(self):
        self.data = None  # 数据域
        self.next = None  # 指针域

def Reverse(head):
    # 判断链表是否为空
    if head == None or head.next == None:
        return
    pre = None  # 前驱节点
    cur = None  # 当前节点
    next = None  # 后继节点
    # 把链表首节点变为尾结点
    cur = head.next
    next = cur.next
    cur.next = None
    pre = cur
    cur = next
    # 是当前遍历到的节点 cur 指向其前驱节点
    while cur.next != None:
        next = cur.next
        cur.next = pre
        pre = cur
        cur = next
    # 链表的头结点指向倒数第二个节点
    cur.next = pre
    # 链表的头结点指向原来链表的尾结点
    head.next = cur

if __name__ == '__main__':
    i = 1
    # 链表头节点
    head = LNode()
    head.next = None
    tmp = None
    cur = head
    # 构造单链表
    while i < 8:
        tmp = LNode()
        tmp.data = i
        tmp.next = None
        cur.next = tmp
        cur = tmp
        i += 1
    print('逆序前：', end='')
    cur = head.next
    while cur != None:
        print(cur.data, end=' ')
        cur = cur.next

    print('\n逆序后：', end='')
    Reverse(head)
    cur = head.next
    while cur != None:
        print(cur.data, end=' ')
        cur = cur.next
```
```

程序的运行结果为：

逆序前: 1 2 3 4 5 6 7
逆序后: 7 6 5 4 3 2 1

# 算法性能分析：

这种方法只需要对链表进行一次遍历，因此，时间复杂度为O(N)，其中，N为链表的长度。但是需要常数个额外的变量来保存当前节点的前驱节点与后继节点，因此，空间复杂度为O(1)。

# 方法二：递归法