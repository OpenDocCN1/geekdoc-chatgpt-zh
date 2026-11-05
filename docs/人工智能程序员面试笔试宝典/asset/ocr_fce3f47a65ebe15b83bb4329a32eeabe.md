```
tmp = slow
slow = slow.next
tmp.next = None
fast.next = head.next
head.next = slow

def ConstructList():
    i = 1
    head = LNode()
    head.next = None
    tmp = None
    cur = head
    # 构造第 一个链表
    while i < 8:
        tmp = LNode()
        tmp.data = i
        tmp.next = None
        cur.next = tmp
        cur = tmp
        i += 1
    return head

# 顺序打印单链表结点的数据
def PrintList(head):
    cur = head.next
    while cur is not None:
        print(cur.data, end=" ")
        cur = cur.next

if __name__ == "__main__":
    head = ConstructList()
    print("旋转前：", end="")
    PrintList(head)
    RotateK(head, 3)
    print("\n旋转后：", end="")
    PrintList(head)
```

程序的运行结果为：

旋转前： 1 2 3 4 5 6 7
旋转后： 5 6 7 1 2 3 4

# 算法性能分析：

这种方法只需要对链表进行一次遍历，因此，时间复杂度为O(N)。另外，由于只需要几个指针变量来保存结点的地址信息，因此，空间复杂度为O(1)。