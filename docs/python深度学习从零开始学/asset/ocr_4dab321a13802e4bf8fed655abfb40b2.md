# Python 深度学习从零开始学

```
# 定义bias函数，用于建立偏移张量
def bias(shape):
    return tf.Variable(tf.constant(0.1, shape=shape)
        , name = 'b')
# 定义conv2d函数，用于进行卷积运算
def conv2d(x, W):
    return tf.nn.conv2d(x, W, strides=[1,1,1,1],
            padding='SAME')
# 定义max_pool_2x2函数，用于建立池化层
def max_pool_2x2(x):
    return tf.nn.max_pool(x, ksize=[1,2,2,1],
            strides=[1,2,2,1],
            padding='SAME')
# 建立输入层
with tf.name_scope('Input_Layer'):
    x = tf.placeholder("float",shape=[None, 784]
        ,name="x")
    #x原本是一维，后续要进行卷积与池化运算，必须转换为四维张量
    x_image = tf.reshape(x, [-1, 28, 28, 1])
# 建立卷积层1
with tf.name_scope('C1_Conv'):
    W1 = weight([5,5,1,16])
    b1 = bias([16])
    Conv1=conv2d(x_image, W1)+ b1
    C1_Conv = tf.nn.relu(Conv1 )
# 建立池化层1
with tf.name_scope('C1_Pool'):
    C1_Pool = max_pool_2x2(C1_Conv)
# 建立卷积层2
with tf.name_scope('C2_Conv'):
    W2 = weight([5,5,16,36])
    b2 = bias([36])
    Conv2=conv2d(C1_Pool, W2)+ b2
    C2_Conv = tf.nn.relu(Conv2)
# 建立池化层2
with tf.name_scope('C2_Pool'):
    C2_Pool = max_pool_2x2(C2_Conv)
# 建立平坦层
with tf.name_scope('D_Flat'):
    D_Flat = tf.reshape(C2_Pool, [-1, 1764])
# 建立隐藏层，加入Dropout避免过拟合
with tf.name_scope('D_Hidden_Layer'):
    W3 = weight([1764, 128])
    b3 = bias([128])
    D_Hidden = tf.nn.relu(
        tf.matmul(D_Flat, W3) + b3)
```