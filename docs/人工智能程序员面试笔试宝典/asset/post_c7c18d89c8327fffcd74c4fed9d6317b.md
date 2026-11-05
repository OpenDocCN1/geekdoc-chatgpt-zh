
```
def __init__(self):
    self.data = None
    self.lchild = None
    self.rchild = None

class Test:

    def __init__(self):
        self.maxSum = -2**31
        """
        方法功能: 求最大子树
        输入参数: root: 根节点;
        maxRoot: 最大子树的根节点
        返回值: 以 root 为根节点子树所有结点的和
        """
        def findMaxSubTree(self, root, maxRoot):
            if root == None:
                return 0

            # 求 root 左子树所有结点的和
            lmax = self.findMaxSubTree(root.lchild, maxRoot)
            # 求 root 右子树所有结点的和
            rmax = self.findMaxSubTree(root.rchild, maxRoot)
            sums = lmax + rmax + root.data
            # 以 root 为根的子树的和与前面求出的最大值
            if sums > self.maxSum:
                self.maxSum = sums
                maxRoot.data = root.data

            # 返回以 root 为根结点的子树的所所有结点的和
            return sums

        """
        方法功能: 构造二叉树
        返回值: 返回新构造的二叉树的根结点
        """
        def constructTree(self):
            root = BiTNode()
            node1 = BiTNode()
            node2 = BiTNode()
            node3 = BiTNode()
            node4 = BiTNode()
            root.data = 6
            node1.data = 3
            node2.data = -7
            node3.data = -1
            node4.data = 9
            root.lchild = node1
            root.rchild = node2
            node1.lchild = node3
            node1.rchild = node4
            node2.lchild = node2.rchild = node3.lchild = node3.rchild = \
                node4.lchild = node4.rchild = None
            return root

        if __name__ == "__main__":
            # 构造二叉树
            test = Test()
            root = test.constructTree()

            maxRoot = BiTNode()  # 最大子树的根节点
            test.findMaxSubTree(root, maxRoot)
            print("最大子树和为: " + str(test.maxSum))
            print("对应子树的根节点为: " + str(maxRoot.data))
```

程序的运行结果为：

最大子树和为: 11
对应子树的根节点为: 3

## 算法性能分析：

这种方法与二叉树的后序遍历有相同的时间复杂度，即为O(N)，其中，N为二叉树的结点个数。

## 9.14 如何找出排序二叉树上任意两个节点的最近共同父结点

### 题目描述：

对于一棵给定的排序二叉树，求两个节点的共同父结点，例如，在图9-14中，节点1和节点5的共同父结点为3。

![](img/63904e7df2325ca111d7e3f06153a9e2_274_0.png)

图9-14 求公共父结点的二叉树

### 分析与解答：

### 方法一：路径对比法

对于一棵二叉树的两个结点，如果知道从根结点到这两个结点的路径，就可以很容易地找出它们最近的公共父结点。因此，可以首先分别找出从根结点到这两个结点的路径（例如图9-14中从根结点到结点1的路径为6->3->2->1，从根结点到结点5的路径为6->3->5）；然后遍历这两条路径，只要是相等的结点都是它们的父结点，找到最后一个相等的结点即为离它们最近的共同父结点，在这个例子中，结点3就是它们共同的父结点。示例代码如下：

```python
class BiTNode:
    def __init__(self):
        self.data = None
        self.lchild = None
        self.rchild = None

class stack:
    # 模拟栈
    def __init__(self):
        self.items = []
    # 判断栈是否为空
    def isEmpty(self):
        return len(self.items) == 0

    # 返回栈的大小
    def size(self):
        return len(self.items)

    # 返回栈顶元素
    def peek(self):
        if not self.isEmpty():
            return self.items[len(self.items)-1]
        else:
            return None

    # 弹栈
    def pop(self):
        if len(self.items) > 0:
            return self.items.pop()
        else:
            print("栈已经为空")
            return None

    # 压栈
    def push(self, item):
        self.items.append(item)

# 方法功能：把有序数组转换为二叉树
def arraytotree(arr, start, end):
    root = None
    if end >= start:
        root = BiTNode()
        mid = (start+end+1)/2
        # 树的根节点为数组中间的元素
        root.data = arr[mid]
        # 递归用左半部分数组构造 root 的左子树
        root.lchild = arraytotree(arr, start, mid-1)
        # 递归用右半部分数组构造 root 的右子树
        root.rchild = arraytotree(arr, mid+1, end)
    else:
        root = None
    return root

"""
方法功能：获取二叉树从根结点 root 到 node 结点的路径
输入参数：root: 根结点; node: 二叉树中的某个结点; s: 用来存储路径的栈
返回值：node 在 root 的子树上，或 node==root 时返回 true，否则返回 false
"""
def getPathFromRoot(root, node, s):
    if root == None:
        return False
    if root == node:
        s.push(root)
        return True
    """
    如果 node 结点在 root 结点的左子树或右子树上：
    那么 root 就是 node 的祖先结点，把它加到栈里
    """
    if getPathFromRoot(root.lchild, node, s) or getPathFromRoot(root.rchild, node, s):
        s.push(root)
        return True
    return False

"""
方法功能：查找二叉树中两个结点最近的共同父结点
输入参数：root: 根结点; node1 与 node2 为二叉树中两个结点
返回值：node1 与 node2 最近的共同父结点
"""
def FindParentNode(root, node1, node2):
    stack1 = stack()  # 保存从 root 到 node1 的路径
    stack2 = stack()  # 保存从 root 到 node2 的路径
    # 获取从 root 到 node1 的路径
    getPathFromRoot(root, node1, stack1)
    # 获取从 root 到 node2 的路径
    getPathFromRoot(root, node2, stack2)
    commonParent = None
    # 获取最近邻 node1 和 node2 的父结点
    while stack1.peek() == stack2.peek():
        commonParent = stack1.peek()
        stack1.pop()
        stack2.pop()
    return commonParent

if __name__ == "__main__":
    arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    root = arraytotree(arr, 0, len(arr)-1)
    node1 = root.lchild.lchild
    node2 = root.lchild.rchild
    res = None

res = FindParentNode(root, node1, node2)
if res != None:
    print(str(node1.data)+"与"+str(node2.data)+"的最近公共父节点为："+str(res.data),end="")
else:
    print("没有公共父节点")
```

程序的运行结果为：

1 与 5 的最近公共父节点为： 3

### 算法性能分析：

当获取二叉树从根结点root到node结点的路径时，最坏的情况就是把树中所有结点都遍历了一遍，这个操作的时间复杂度为O(N)，再分别找出从根结点到两个结点的路径后，找它们最近的公共父结点的时间复杂度也为O(N)，因此，这种方法的时间复杂度为O(N)。此外，这种方法用栈保存了从根结点到特定结点的路径，在最坏的情况下，这个路径包含了树中所有的结点，因此，空间复杂度也为 O(N)。

很显然，这种方法还不够理想。下面介绍另外一种能降低空间复杂度的方法。

### 方法二：节点编号法

可以把二叉树看成是一棵完全二叉树（不管实际的二叉树是否为完全二叉树，二叉树中的结点都可以按照完全二叉树中对结点编号的方式进行编号），图9-15为对二叉树中的结点按照完全二叉树中结点的编号方式进行编号后的结果，结点右边的数字为其对应的编号。

![](img/63904e7df2325ca111d7e3f06153a9e2_277_0.png)

图9-15 二叉树的编号

根据二叉树的性质可以知道，一个编号为n的结点，它的父亲结点的编号为n/2。假如要求node1与node2的最近的共同父结点，首先把这棵树看成是一棵完全二叉树（不管结点是否存在），分别求得这两个结点的编号n1和n2。然后每次找出n1与n2中较大的值除以2，直到ln1==n2为止，此时n1或n2的值对应结点的编号就是它们最近的共同父结点的编号，接着可以根据这个编号信息找到对应的结点，具体方法为：通过观察二叉树中结点的编号可以发现：首先把根结点root看成1，求root的左孩子编号的方法为把root对应的编号看成二进制，然后向左移一位，末尾补0，如果是root的右孩子，那么末尾补1，因此，通过结点位置的二进制码就可以确定这个结点。例如结点3的编号为2（二进制10），它的左孩子的求解方法为：10，向左移一位末尾补0，可以得到二进制100（十进制4），位置为4的结点的值为2。从这个特性可以得出通过结点位置信息获取结点的方法，例如要求位置4的结点，4的二进制码为100，由于1代表根结点，接下来的一个0代表是左子树root.lchild，最后一个0也表示左子树root.lchild.lchild，通过这种方法非常容易根据结点的编号找到对应的结点。实现代码如下：

```python
import math

class BiTNode:
    def __init__(self):
        self.data = None
        self.lchild = None
        self.rchild = None

# 方法功能：把有序数组转换为二叉树
def arraytotree(arr, start, end):
    root = None
    if end >= start:
        root = BiTNode()
        mid = (start+end+1)/2
        # 树的根节点为数组中间的元素
        root.data = arr[mid]
        # 递归的用左半部分数组构造 root 的左子树
        root.lchild = arraytotree(arr, start, mid-1)
        # 递归的用右半部分数组构造 root 的右子树
        root.rchild = arraytotree(arr, mid+1, end)
    else:
        root = None
    return root

class IntRef:
    def __init__(self):
        self.num = None

"""
方法功能：找出结点在二叉树中的编号
输入参数：root: 根节点, node: 待查找结点, number: node 结点在二叉树中的编号
返回值：true: 找到该结点的位置, 否则返回 false
"""

def getNo(root, node, number):
    if root == None:
        return False
    if root == node:
        return True
    tmp = number.num
    number.num = 2 * tmp
    # node 结点在 root 的左子树中, 左子树编号为当前结点编号的 2 倍
    if getNo(root.lchild, node, number):
        return True
    # node 结点在 root 的右子树中, 右子树编号为当前结点编号的 2 倍加 1
    else:
        number.num = tmp * 2 + 1
        return getNo(root.rchild, node, number)
```

```
'''
方法功能：根据节点的编号找出对应的节点
输入参数：root：根节点；number 为节点的编号
返回值：编号为 number 对应的节点
'''
def getNodeFromNum(root, number):
    if root == None or number < 0:
        return None
    if number == 1:
        return root
    # 节点编号对应二进制制位数（最高位一定为1，因为根节点代表1）
    lens = int(math.log(number) / math.log(2))
    # print(lens)
    # 去掉根节点表示的1
    # number -= 1 << lens
    while lens > 0:
        # 如果这一位二进制的值为1，
        # 那么编号为 number 的节点必定在当前节点的右子树上
        if (1 << (lens - 1)) & (number - 1) == 1:
            root = root.rchild
        else:
            root = root.lchild
        lens -= 1
    return root
```

```
'''
方法功能：查找二叉树中两个节点最近的共同父节点
输入参数：root：根节点；node1 与 node2 为二叉树中两个节点
返回值：node1 与 node2 最近的共同父节点
'''
def FindParentNode(root, node1, node2):
    ref1 = IntRef()
    ref1.num = 1
    ref2 = IntRef()
    ref2.num = 1
    getNo(root, node1, ref1)
    getNo(root, node2, ref2)
    num1 = ref1.num
    num2 = ref2.num
    # 找出编号为 num1 和 num2 的共同父节点
    while num1 != num2:
        if num1 > num2:
            num1 /= 2
        else:
            num2 /= 2
    # num1 就已经是他们最近的公共父节点的编号，通过节点编号找到对应的节点
    return getNodeFromNum(root, num1)
```

```
if __name__ == "__main__":
    arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
    root = arraytotree(arr, 0, len(arr)-1)
    node1 = root.lchild.lchild.lchild
    node2 = root.lchild.rchild
    res = None
    res = FindParentNode(root, node1, node2)
    if res != None:
        print(str(node1.data)+"与"+str(node2.data)+"的最近公共父节点为："+str(res.data),end="")
    else:
        print("没有公共父节点",end="")
```

### 算法性能分析：

这种方法的时间复杂度也为O(N)，与方法一相比，在求解的过程中只用了个别的几个变量，因此，空间复杂度为O(1)。

### 方法三：后序遍历法

很多与二叉树相关的问题都可以通过对二叉树的遍历方法进行改编而求解。对于本题而言，可以通过对二叉树的后序遍历进行改编而得到。具体思路为：查找节点node1与节点node2的最接近共同父结
