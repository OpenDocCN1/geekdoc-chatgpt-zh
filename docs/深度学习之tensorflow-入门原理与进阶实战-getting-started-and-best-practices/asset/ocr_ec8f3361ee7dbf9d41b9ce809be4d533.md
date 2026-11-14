| 函数 | 描述 |
|---|---|
| tf.multiply(x, y, name=None) | 乘法 |
| tf.divide(x, y, name=None) | 除法,也可以使用tf.div函数 |
| tf.mod(x, y, name=None) | 取模 |
| tf.abs(x, name=None) | 求绝对值 |
| tf.negative(x, name=None) | 取负 (y = -x) |
| tf.sign(x, name=None) | 返回输入x的符号。如果x小于0，则返回-1；如果x=0，则返回0；如果x大于0，则返回-1 |
| tf.inv(x, name=None) | 对取反操作 |
| tf.square(x, name=None) | 计算平方 (y = x * x = x^2) |
| tf.round(x, name=None) | 含入最接近的整数。例如：<br>a=[0.9, 2.5, 2.3, 1.5, -4.5]<br>tf.round(a) ==>[ 1.0, 2.0, 2.0, 2.0, -4.0 ]<br>如果需要真正的四舍五入，可以用tf.int32类型强制转换 |
| tf.sqrt(x, name=None) | 开根号 (y = \sqrt{x} = x^{1/2}) |
| tf.pow(x, y, name=None) | 幂次方计算。例如：<br>x=[[2,2],[3,3]]<br>y=[[8,16],[2,3]]<br>tf.pow(x,y)==> [[256, 65536], [9, 27]] #[2的2次方，2的16次方]，[3的2次方,3的3次方] |
| tf.exp(x, name=None) | 计算e的次方 |
| tf.log(x, name=None) | 计算log，一个输入计算e的ln，两输入以第二输入为底 |
| tf.maximum(x, y, name=None) | 返回最大值 (x > y ? x : y) |
| tf.minimum(x, y, name=None) | 返回最小值 (x < y ? x : y) |
| tf.cos(x, name=None) | 三角函数cosine |
| tf.sin(x, name=None) | 三角函数sine |
| tf.tan(x, name=None) | 三角函数tan |
| tf.atan(x, name=None) | 三角函数ctan |
| tf.cond(pred, true_fn=None, false_fn=None, strict=False, name=None, fn1=None, fn2=None) | 满足条件就执行fn1,否则执行fn2。例如：<br>x = tf.constant(2)<br>y = tf.constant(5)<br>def f1(): return tf.multiply(x, 17)<br>def f2(): return tf.add(y, 23)<br>r = tf.cond(tf.less(x, y), f1, f2)<br>则r的值为34 |

# 4.2.3 矩阵相关的运算

矩阵相关的操作函数如表 4-11 所示。