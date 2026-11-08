从零手写神经网络训练闭环

想真正理解深度学习，先把前向、反向、参数更新这条主线自己跑通。下面的片段把整个闭环拆开：先算损失与梯度，再把梯度传回网络，最后按优化器给出的步长改写参数。apply_grad 则是逐层逐参数把梯度累加到权重上，是训练可执行的关键。

    def backward(self, preds, targets):

        loss = self.loss.loss(preds, targets)

        grad = self.loss.grad(preds, targets)

        grads = self.net.backward(grad)

        params = self.net.get_parameters()

        step = self.optimizer.compute_step(grads, params)

        return loss, step

    def apply_grad(self, grads):

        for grad, (param, _) in zip(grads, self.net.get_params_and_grads()):

            for k, v in param.items():

                param[k] += grad[k]

实战里用 MNIST 做演示，先把标签转成 one-hot，再把 60000 张 28x28 图像拍平成 784 维向量。网络结构是 200-100-70-30-10 的全连接堆叠，中间夹 ReLU，损失用 SoftmaxCrossEntropy，优化器 Adam 学习率 2e-4。训练以 128 为批，跑 20 个 epoch，每隔 10 个批打印当批损失与即时准确率，直观感受收敛。

[PRE0]python

import numpy as np

def get_one_hot(targets, nb_classes=10):

    return np.eye(nb_classes)[np.array(targets).reshape(-1)]

train_x = np.load("../../dataset/mnist/x_train.npy");

train_x = np.reshape(train_x, [60000, 784])

train_y = get_one_hot(np.load("../../dataset/mnist/y_train_label.npy"))

import net, model, layer, loss, optimizer

net = net.Net([

    layer.Dense(200),

    layer.ReLU(),

    layer.Dense(100),

    layer.ReLU(),

    layer.Dense(70),

    layer.ReLU(),

    layer.Dense(30),

    layer.ReLU(),

    layer.Dense(10)

])

model = model.Model(net=net, loss=loss.SoftmaxCrossEntropy(),

optimizer=optimizer.Adam(lr=2e-4))

loss_list = list()

train_num = 60000 // 128

for epoch in range(20):

    train_loss = 0

    for i in range(train_num):

        start = i * 128

        end = (i + 1) * 128

        inputs = train_x[start:end]

        targets = train_y[start:end]

        pred = model.forward(inputs)

        loss, grads = model.backward(pred, targets)

        model.apply_grads(grads)

        if (i + 1) % 10 == 0:

            test_pred = model.forward(inputs)

            test_pred_idx = np.argmax(test_pred, axis=1)

            real_pred_idx = np.argmax(targets, axis=1)

            counter = 0

            for pre, rel in zip(test_pred_idx, real_pred_idx):

                if pre == rel:

                    counter += 1

            print("train_loss:", round(loss, 2), "accuracy:", round(counter/128., 2))

抽象出来，模型的使用形态非常简洁：先建网，再前向算预测，反向算损失与梯度，最后用优化器更新。推理时只需要一次前向。

[PRE1]python

# define model

    net = Net(Activity([layer1, layer2, ...]))    #数据的激活与计算

    model = Model(net, loss_fn, optimizer)

    # training                                    #训练过程

    pred = model.forward(train_X)                 #前向计算

    loss, grads = model.backward(pred, train_Y)   #反向计算

    model.apply_grad(grads)                       #参数优化

    # inference                                   #预测过程

    test_pred = model.forward(test_X)

数据维度是实现细节的核心，典型的卷积前输入常是批大小×高×宽×通道，训练前需要展平。

[PRE2]python

import numpy as np

tensor = np.random.random(size=(10,28,28,1))

层基类负责统一参数容器、初始化时机与训练/推理状态切换。is_init 控制首次前向时根据输入形状创建权重，is_training 用于 BatchNorm、Dropout 这类行为随模式变化的层。

[PRE3]python

class Layer:

    """Base class for layers."""

    def __init__(self):

        self.params = {p: None for p in self.param_names}

        self.nt_params = {p: None for p in self.nt_param_names}

        self.initializers = {}

        self.grads = {}

        self.shapes = {}

        self._is_training = True  # used in BatchNorm/Dropout layers

        self._is_init = False

        self.ctx = {}

    def __repr__(self):

        shape = None if not self.shapes else self.shapes

        return f"layer: {self.name}\tshape: {shape}"

    def forward(self, inputs):

        raise NotImplementedError

    def backward(self, grad):

        raise NotImplementedError

    @property

    def is_init(self):

        return self._is_init

    @is_init.setter

    def is_init(self, is_init):

        self._is_init = is_init

        for name in self.param_names:

            self.shapes[name] = self.params[name].shape

    @property

    def is_training(self):

        return self._is_training

    @is_training.setter

    def is_training(self, is_train):

        self._is_training = is_train

    @property

    def name(self):

        return self.__class__.__name__

    @property

    def param_names(self):

        return ()

    @property

    def nt_param_names(self):

        return ()

    def _init_params(self):

        for name in self.param_names:

            self.params[name] = self.initializers[name](self.shapes[name])

        self.is_init = True

全连接层的实现遵循 outputs = X @ W + b。第一次前向根据输入维度补全权重形状并初始化，随后保存输入到 ctx 供反向使用。

[PRE4]python

class Dense(Layer):

    """A dense layer operates `outputs = dot(inputs, weight) + bias`

    :param num_out: A positive integer, number of output neurons

    :param w_init: Weight initializer

    :param b_init: Bias initializer

    """

    def __init__(self,

                 num_out,

                 w_init=XavierUniform(),

                 b_init=Zeros()):

        super().__init__()

        self.initializers = {"w": w_init, "b": b_init}

        self.shapes = {"w": [None, num_out], "b": [num_out]}

    def forward(self, inputs):

        if not self.is_init:

            self.shapes["w"][0] = inputs.shape[1]

            self._init_params()

        self.ctx = {"X": inputs}

        return inputs @ self.params["w"] + self.params["b"]

一个易错点是输入形状。把 10 张 28×28 灰度图展平成 10×784 后才能喂给 Dense。

[PRE5]python

tensor = np.random.random(size=(10, 28, 28, 1))

tensor = np.reshape(tensor,newshape=[10,28*28])

res = Dense(512).forward(tensor)

激活层则把前向与反向解耦为 forward_func 与 backward_func，底座只需要保存输入，子类实现具体函数即可。

[PRE6]python

#activity_layer

import numpy as np

class Layer(object):

    def __init__(self, name):

        self.name = name

        self.params, self.grads = None, None

    def forward(self, inputs):

        raise NotImplementedError

    def backward(self, grad):

        raise NotImplementedError

class Activation(Layer):

    """Base activation layer"""

    def __init__(self, name):

        super().__init__(name)

        self.inputs = None

    def forward(self, inputs):    #下面调用具体的forward实现函数

        self.inputs = inputs

        return self.forward_func(inputs)

    def backward(self, grad):    #下面调用具体的backward实现函数

        return self.backward_func(self.inputs) * grad

    def forward_func(self, x):    #具体的forward实现函数