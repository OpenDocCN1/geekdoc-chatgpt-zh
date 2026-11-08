# 手写神经网络：从前向到 Adam 的完整实战

下面是一套从零搭建的极简深度学习框架拆解，涵盖激活基类、网络编排、损失与优化器以及在 MNIST 上的真实训练闭环。思路清晰、可直接复用。

先看激活层的通用骨架与 ReLU 的具体实现。

        raise NotImplementedError

    def backward_func(self, x):    #具体的backward实现函数

        raise NotImplementedError

class ReLU(Activation):

    """ReLU activation function"""

    def __init__(self):

        super().__init__("ReLU")

    def forward_func(self, x):

        return np.maximum(x, 0.0)

    def backward_func(self, x):

        return x > 0.0

接下来是网络容器，负责把多层串起来做前向和反向。

[PRE0]python

# Net

class Net(object):

    def __init__(self, layers):

        self.layers = layers

    def forward(self, inputs):

        for layer in self.layers:

            inputs = layer.forward(inputs)

        return inputs

    def backward(self, grad):

        all_grads = []

        for layer in reversed(self.layers):

            grad = layer.backward(grad)

            all_grads.append(layer.grads)

        return all_grads[::-1]

    def get_params_and_grads(self):

        for layer in self.layers:

            yield layer.params, layer.grads

    def get_parameters(self):

        return [layer.params for layer in self.layers]

    def set_parameters(self, params):

        for i, layer in enumerate(self.layers):

            for key in layer.params.keys():

                layer.params[key] = params[i][key]

损失的抽象与交叉熵的实现。

[PRE1]python

# loss

class BaseLoss(object):

    def loss(self, predicted, actual):

        raise NotImplementedError

    def grad(self, predicted, actual):

        raise NotImplementedError

[PRE2]python

# optimizer

class BaseOptimizer(object):

    def __init__(self, lr, weight_decay):

        self.lr = lr

        self.weight_decay = weight_decay

    def compute_step(self, grads, params):

        step = list()

        # flatten all gradients

        flatten_grads = np.concatenate(

            [np.ravel(v) for grad in grads for v in grad.values()])

        # compute step

        flatten_step = self._compute_step(flatten_grads)

        # reshape gradients

        p = 0

        for param in params:

            layer = dict()

            for k, v in param.items():

                block = np.prod(v.shape)

                _step = flatten_step[p:p+block].reshape(v.shape)

                _step -= self.weight_decay * v

                layer[k] = _step

                p += block

            step.append(layer)

        return step

    def _compute_step(self, grad):

        raise NotImplementedError

[PRE3]python

class CrossEntropyLoss(BaseLoss):

    def loss(self, predicted, actual):

        m = predicted.shape[0]

        exps = np.exp(predicted - np.max(predicted, axis=1,

keepdims=True))

        p = exps / np.sum(exps, axis=1, keepdims=True)

        nll = -np.log(np.sum(p * actual, axis=1))

        return np.sum(nll) / m

    def grad(self, predicted, actual):

        m = predicted.shape[0]

        grad = np.copy(predicted)

        grad -= actual

        return grad / m

常用的 one-hot 工具。

[PRE4]python

def get_one_hot(targets, nb_classes=10):

    return np.eye(nb_classes)[np.array(targets).reshape(-1)]

优化器部分给出 Adam 的自适应步长实现。

[PRE5]python

class Adam(BaseOptimizer):

    def __init__(self, lr=0.001, beta1=0.9, beta2=0.999, eps=1e-8, weight_decay=0.0):

        super().__init__(lr, weight_decay)

        self._b1, self._b2 = beta1, beta2

        self._eps = eps

        self._t = 0

        self._m, self._v = 0, 0

    def compute_step(self, grad):

        self._t += 1

        self._m = self._b1 * self._m + (1 - self._b1) * grad

        self._v = self._b2 * self._v + (1 - self._b2) * (grad ** 2)

        # bias correction

        m = self._m / (1 - self._b1 ** self._t)

        v = self._v / (1 - self._b2 ** self._t)

        return -self.lr * m / (v ** 0.5 + self._eps)

模型层把网络、损失、优化器串成训练闭环。

[PRE6]python

class Model(object):

    def __init__(self, net, loss, optimizer):

        self.net = net

        self.loss = loss

        self.optimizer = optimizer

    def forward(self, inputs):

        return self.net.forward(inputs)

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

最后是 MNIST 的完整训练脚本，200-100-70-30 的多层 MLP，分批训练 20 轮并实时打印损失与准确率。

[PRE7]python

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

        loss, grads = model.backward(pred,targets)

        model.apply_grads(grads)

        if (i + 1) %10 == 0:

            test_pred = model.forward(inputs)

            test_pred_idx = np.argmax(test_pred, axis=1)

            real_pred_idx = np.argmax(targets, axis=1)

            counter = 0

            for pre,rel in zip(test_pred_idx,real_pred_idx):

                if pre == rel:

                    counter += 1

            print("train_loss:", round(loss, 2), "accuracy:", round(counter/128., 2))