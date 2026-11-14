# 2. 批量归一化定义

先来看看 TensorFlow 中自带的 BN 函数定义：

```
tf.nn.batch_normalization(x,mean,variance,offset,scale,variance_epsilon,name=None)
```

它的参数很简单，各参数说明如下。

+   - x: 代表输入。
+   - mean: 代表样本的均值。
+   - variance: 代表方差。
+   - offset: 代表偏移，即相加一个转化值，后面我们会用激活函数来转换，所以这里不需要再转化，直接使用 0。
+   - scale: 代表缩放，即乘以一个转化值，同理，一般都用 1。
+   - variance_epsilon: 是为了避免分母为 0 的情况，给分母加一个极小值。默认即可。

要想使用这个函数，必须由另一个函数配合——tf.nn.moments，由它来计算均值和方差，然后就可以使用 BN 了。tf.nn.moments 定义如下：

```
tf.nn.moments(x, axes, name=None, keep_dims=False)
```

axes 主要是指定哪个轴来求均值与方差。

⚠ 注意：axes 在使用过程中经常容易犯错。这里提供一个小技巧，为了求样本的均值和方差，一般都会设为保留最后一个维度，对于 x 来讲可以直接使用公式 axis = list(range(len(x.get_shape()) - 1))即可。例如，[128,3,3,12] axes 就为[0,1,2]，输出的均值方差维度为[12]

有了上面的两个函数还不够，为了有更好的效果，我们希望使用平滑指数衰减的方法来优化每次的均值与方差，于是就用到了 tf.train.ExponentialMovingAverage 函数。它的作用是让上一次的值对本次的值有个衰减后的影响，从而使每次的值连起来后会相对平滑一些。展开后可以用下面的代码来表示：

```
shadow_variable = decay * shadow_variable + (1 - decay) * variable
```

各参数说明如下。

+   - decay: 代表衰减指数，是在 ExponentialMovingAverage 中指定的，比如 0.9。
+   - variable: 代表本批次样本中的值。
+   - 等式右边的 shadow_variable: 代表上次总样本的值。
+   - 等式左边 shadow_variable: 代表计算出来的本次总样本的值。

# 3. 批量归一化的简单用法

上面的函数虽然参数不多，但需要几个函数联合起来使用，于是 TensorFlow 中的 layers 模块里又实现了一次 BN 函数，相当于把几个函数合并到了一起，使用起来更加简单。下