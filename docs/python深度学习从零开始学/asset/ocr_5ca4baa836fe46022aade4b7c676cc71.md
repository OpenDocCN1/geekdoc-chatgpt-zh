```
return errDA, errDB, errGA, errGB, errCycA, errCycB
```

# 13.4.6 训练代码

训练代码，这里使用 snapshot 函数，用于在训练的过程中生成预览效果，代码如下：

```
# 输入神经网络的图片尺寸
IMG_SIZE = 128
# 数据集名称
DATASET = "vangogh2photo"
# 数据集路径
dataset_path = "./data/{}/".format(DATASET)
trainA_path = dataset_path + "trainA/*.jpg"
trainB_path = dataset_path + "trainB/*.jpg"
train_A = DataSet(trainA_path, image_size = IMG_SIZE)
train_B = DataSet(trainB_path, image_size = IMG_SIZE)
def train_batch(batchsize):
    """
    从数据集中取出一个 Batch
    """
    epa, a = train_A.get_batch(batchsize)
    epb, b = train_B.get_batch(batchsize)
    return max(epa, epb), a, b
def gen(generator, X):
    # 用于生成效果图
    r = np.array([generator([np.array([x])]) for x in X])
    g = r[:,0,0]
    rec = r[:,1,0]
    return g, rec
def snapshot(cycleA, cycleB, A, B):
    """
    产生一个快照
    A、B是两个图片列表
    cycleA 是 A->B->A 的一个循环
    cycleB 是 B->A->B 的一个循环

    输出一幅图片:
    +------------+    +------------+
    | X (in A) | ... | Y (in B) | ...
    +------------+    +------------+
    |   GB(X)   | ... |   GA(Y)   | ...
    +------------+    +------------+
    | GA(GB(X)) | ... | GB(GA(Y)) | ...
    +------------+    +------------+
    """
    gA, recA = gen(cycleA, A)
```