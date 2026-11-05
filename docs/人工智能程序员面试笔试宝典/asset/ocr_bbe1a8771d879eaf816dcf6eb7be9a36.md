```
def rightRotate(self,y):
    x = y.left
    T2 = x.right
    # 旋转
    x.right = y
    y.left = T2
    y.height = max(self.getHeight(y.left),self.getHeight(y.right)) + 1
    x.height = max(self.getHeight(x.left),self.getHeight(x.right)) + 1
    # 返回新的根节点
    return x
    # 把以 x 为根的子树向右旋转
def leftRotate(self,x):
    y = x.right
    T2 = y.left
    y.left = x
    x.right = T2
    x.height = max(self.getHeight(x.left),self.getHeight(x.right)) + 1
    y.height = max(self.getHeight(y.left),self.getHeight(y.right)) + 1
    return y

# 获取树的平衡因子
def getBalance(self,N):
    if N == None:
        return 0
    return self.getHeight(N.left) - self.getHeight(N.right)

# ""
# 如果 data 在 AVL 树中不存在，则把 data 插入到 AVL 树中
# 若则把这个节点对应的 count 加 1
# ""
def insert(self,root,data):
    if root == None:
        return AVLNode(data)
    # data 在树中存在，把对应的节点的 count 加 1
    if data == root.data:
        root.count += 1
        return root
    # 在左子树中继续查找 data 是否存在
    if data < root.data:
        root.left = self.insert(root.left,data)
    # 在右子树中继续查找 data 是否存在
    else:
        root.right = self.insert(root.right,data)
    # 插入新的节点后更新 root 节点的高度
    root.height = max(self.getHeight(root.left),self.getHeight(root.right)) + 1
    # 获取树的平衡因子
    balance = self.getBalance(root)
    # 如果树不平衡，根据数据结构中学过的 4 种情况进行调整
    # LL 型
    if balance > 1 and data < root.left.data:
        return self.rightRotate(root)
    # RR 型
```