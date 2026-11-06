```
import matplotlib.pyplot as plt
num_points=1000
vectors_set=[]
for i in range(num_points):
    x1=np.random.normal(loc=0.0,scale=0.66)
    y1=x1*0.1+0.3+np.random.normal(0.0,0.03)
    vectors_set.append([x1,y1])
x_data=[v[0] for v in vectors_set]
y_data=[v[1] for v in vectors_set]
W=tf.Variable(tf.random_uniform([1], minval=-1.0, maxval=1.0))
b=tf.Variable(tf.zeros([1]))
y=W*x_data+b
loss=tf.reduce_mean(tf.square(y-y_data))
optimizer=tf.train.GradientDescentOptimizer(learning_rate=0.5)
train=optimizer.minimize(loss)
init=tf.global_variables_initializer()
sess=tf.Session()
sess.run(init)
for step in range(8):
    sess.run(train)
    print("step={}, sess.run(W)={}, sess.run(b)={}".format(step, sess.run(W), sess.run(b)))
    plt.subplot(4,2,step+1)
    plt.plot(x_data,y_data,'ro')
    plt.plot(x_data,sess.run(W)*x_data+
    sess.run(b),label=step)
    plt.legend()
plt.show()
```

与 Caffe、Theano、Torch、MXNet 等框架相比，TensorFlow 在 GitHub 上更受关注，而且在图形分类、音频处理、推荐系统和自然语言处理等场景下都有丰富的应用。最近流行的 Keras 框架底层同样支持使用 TensorFlow，AlphaGo 开发团队——Deepmind 也计划将神经网络应用迁移到 TensorFlow。本文所选的例子是入门级别的案例，更多的深度学习方法等你去开启。