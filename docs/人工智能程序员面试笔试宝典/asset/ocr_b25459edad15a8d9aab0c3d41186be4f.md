```
class LNode:
    def __init__(self):
        self.data = None
        self.next = None

"""
*** 方法功能: 找出链表 head 的中间结点，把链表从中间断成两个子链表
*** 输入参数: head: 链表头结点
*** 返回值: 链表中间结点
"""
def FindMiddleNode(head):
    if head is None or head.next is None:
        return head
    fast = head        # 遍历链表的时候每次向前走两步
    slow = head        # 遍历链表的时候每次向前走一步
    slowPre = head     # 当 fast 到链表尾时，slow 恰好指向链表的中间结点
    while fast is not None and fast.next is not None:
        slowPre = slow
        slow = slow.next
        fast = fast.next.next
    # 把链表断开成两个独立的子链表
    slowPre.next = None
    return slow

"""
*** 方法功能:对不带头结点的单链表翻转
*** 输入参数: head: 链表头结点
"""
def Reverse(head):
    if head is None or head.next is None:
        return head
    pre = head         # 前驱结点
    cur = head.next    # 当前结点
    next = cur.next    # 后继结点
    pre.next = None
    # 使当前遍历到的结点 cur 指向前驱结点
    while cur is not None:
        next = cur.next
        cur.next = pre
        pre = cur
        cur = cur.next
        cur = next
    return pre

"""
*** 方法功能:对链表进行排序
*** 输入参数: head: 链表头结点
```