

```
"""
根据计算奖励的方式来记录相应信息
"""
return [self.eng.get_lane_waiting_vehicle_count(),
self.eng.get_lane_vehicles()]
```

## 七、训练 Q 值网络

在智能体接收到环境反馈的奖励后，训练便开始了。

智能体训练Q值网络的过程如下：首先，智能体会将同环境交互的信息存储在经验池中。
然后在训练时，从经验池中随机抽取若干状态转移，通过 Deep Q-Network 的更新公式，用目标网络为状态转移计算目标Q值，并把目标Q值作为Q值网络的训练标签。其间，要注意维护目标网络与Q值网络的同步。

```
def train_network(self):
    """
    采样经验，计算目标Q值，训练Q值网络
    """
    # 当经验池容量过小时不进行训练
    if (len(self.memory) < self.training_start):
        return

    # 随机采样经验池中的历史交互数据(s, a, r, s')
    sampled_memory = self._sample_memory()

    # 计算目标Q值，作为q值网络的训练标签
    # 对采样的数据进行格式转换。将一批训练数据(s, a)堆叠成X，并让标签Y与之对应，整理为训练数据X与训练标签Y的格式
    X, Y = self.get_training_sample(sampled_memory)

    # 增加目标网络的同步计数器
    self.q_bar_outdated += 1
    # 训练Q值网络
    self._fit_network(X, Y)
```

这一部分的关键是计算目标Q值，生成训练标签的过程，主要的难点在于数据格式的转换。

神经网络的一次训练是对一批训练数据同时做计算与更新。Q值网络是用 Keras 实现的，它要求每个输入节点的数据格式从列表转换为numpy数组，并且将一批训练数据堆叠在numpy数组的第0号维度上。

让我们了解目前拥有的经验池中数据的格式，每一份“经验”的内容是形如(s, a, r, s')的状态转移，它们以列表的形式被存放着。

```
def remember(self, state, action, reward, next_state):
    """
    智能体将状态转移存放在经验池中
    """
    self.memory.append([state, action, reward, next_state])
```

下面的函数在经验池中随机采样经验，用以训练。

```
def _sample_memory(self):
    """
    随机采样用于训练的经验
    """
    sample_size = min(len(self.memory), 256)
    sampled_memory = random.sample(self.memory, sample_size)
    return sampled_memory
```

我们将用到一个特殊的python语法 —— list(zip(*memory_slices))。以存放着同学姓名与年龄的列表为例：name_age_list = [['小明', 12], ['小芳', 13], ['小华', 11]],经过list(zip(*name_age_list))的操作后,分别得到姓名与年龄的两个列表 [['小明', '小芳', '小华'], [12, 13, 11]]。类似的道理,在这里我们把经验池中的一列状态转移展开,分别得到状态、动作、奖励、下一个状态的列表。注意,两个状态列表中的数据仍然是State类型,需要继续把它转换成numpy数组。

```
def get_training_sample(self, memory_slices):
    # 将一批记忆(s, a, r, s')的展开为s, a, r, s'，将它们解压缩为若干列表,
    [state_list, action_list, reward_list, next_state_list] =
    list(zip(*memory_slices))

    # 将state_list与next_state_list中的每一个状态从State类型转换成列表类型
    state_input_list = [self.convert_state_to_input(state) for state in
    state_list]

    next_state_input_list = [self.convert_state_to_input(next_state) for
    next_state in next_state_list]

    # 再次进行解压缩操作，所得到的state_input是一个有三个元素的列表，其中三个元素是三个
    # 状态特征的列表，
    # 它们分别存放着车道车辆数量、车道等待车辆数、当前信号灯状态
    state_input = list(zip(*state_input_list))
    next_state_input = list(zip(*next_state_input_list))

    # 将三个状态特征列表转换为numpy数组，以便作为输入供给Q值网络
    X = [np.array(state_feature) for state_feature in state_input]
    X_next = [np.array(state_feature) for state_feature in next_state_input]

    # 分别用目标网络和当前网络输出新老状态的Q值，用于计算目标Q值
    target = self.q_network.predict(X)
    bootstrap = self.target_q_network.predict(X_next)

    # 通过deep Q-learning的更新公式，计算目标Q值，作为训练数据的标签
    for i in range(target.shape[0]):
        target[i][action_list[i]] = reward_list[i] + self.gamma *
    np.amax(bootstrap[i])
    Y = np.array(target)
    # 返回训练输入和标签
    return X, Y
```

从完整性考虑，这里顺带说明用Keras训练Q值网络以及更新目标网络的过程。

```
def _fit_network(self, X, Y):
    """
    训练Q值网络
    """
    epochs = 3
    batch_size = min(32, len(Y))
    self.q_network.fit(X, Y, batch_size=batch_size, epochs=epochs,
verbose=False)

def synchronize_target_network(self):
    """
    同步目标网络。每20次训练将目标网络与Q值网络同步，并重置目标网络计数器
    """
    if self.q_bar_outdated >= 20:
        self._update_target_model()
        self.q_bar_outdated = 0
```

## 八、剖析网络结构

现在我们可以探讨一下Q值网络的结构。

如下图所示，Q值网络将智能体的状态——两个车道特征和路口红绿灯设置作为输入，输出所有可选动作的Q值。

我们观察到在路口红绿灯状态不同的情况下，智能体的红绿灯调度策略也应当相应调整，并且倘若仅仅将当前红绿灯状态作为输入状态的一维，它可能没有足够的能力去充分影响网络的Q值输出，导致模型无法有效拟合动作值函数，因此在模型中使用状态选择器来应对这个问题。

状态选择器的意思是为每个红绿灯状态创建一个独立的网络，每次选择输入红绿灯状态所对应的网络进行输出。具体来说，输入的状态先经过一层共享全连接层对输入进行特征提取，再创建各自的从提取后的特征到动作Q值的独立全连接层，最终选取输入红绿灯状态所对应的独立全连接层的结果进行输出。

![](img/9b291315612c9c478ad54afa29381fce_182_0.png)

```
def build_network(self):
    """
    使用Keras搭建Q值网络
    """
    # 为每个状态特征创建输入节点
    input_node_dict = {}
    for name, dimension in self.feature_list:
        input_node_dict[name] = Input(shape=[dimension])

    # 将智能体的输入状态的多种特征拼接为一长条向量
    flatten_feature_list = [input_node for input_node in
    input_node_dict.values()]
    flatten_feature = concatenate(flatten_feature_list, axis=1)

    # 通过一层全连接神经网络进行特征提取
    shared_dense = Dense(20, activation="sigmoid")(flatten_feature)
    selected_q_values_list = []
    for phase in range(self.num_phases):
        # 为每个当前信号灯状态分别构建独立的全连接层，将第一层全连接提取到的特征关联至输出动
        # 作的Q值
        separate_hidden = Dense(20, activation="sigmoid")(shared_dense)
        q_values = Dense(self.num_phases, activation='linear')(separate_hidden)

    # 信号灯状态选择层，Q值网络只会输出当前信号灯状态所对应的独立全连接层的输出
    # 当phase与输入节点cur_phase对应同一个信号灯状态时，selector为1，否则为0
    selector = Selector(phase)(input_node_dict['cur_phase'])

    # 同样的，当phase与cur_phase相同时，selected_q_values与输出Q值q_values相
    # 同，否则为0
    selected_q_values = Multiply()([q_values, selector])
    selected_q_values_list.append(selected_q_values)

    # 通过将选择后的Q值相加，来达到只选择当前状态对应的Q值的效果
    q_values = Add()(selected_q_values_list)

    network = Model(inputs=[input_node_dict[name] for name, _ in
    self.feature_list], outputs=q_values)
    network.compile(optimizer=RMSprop(lr=0.001), loss="mean_squared_error")

    return network
```

为了实现Q值网络，我们使用Keras自定义了名为Selector的层。

```
class Selector(Layer):
    """
    状态选择层
    功能说明:
    若select与输入x相同，则输出1，否则输出0，其中select是创建该层时需要给定的参数
    """
    def __init__(self, select, **kwargs):
        super(Selector, self).__init__(**kwargs)
        self.select = select
        self.select_neuron = K.constant(value=self.select)

    def build(self, input_shape):
        super(Selector, self).build(input_shape)

    def call(self, x):
        return K.cast(K.equal(x, self.select_neuron), dtype="float32")

    def compute_output_shape(self, input_shape):
        return input_shape
```

## 九、结果展示与讨论

我们将强化学习智能体的红绿灯调度与其他多种调控方法进行了对比。对每个调控方法，将它应用到2×2的道路场景中运行一轮，持续4 000个单位时间，每隔100个单位时间采集当前车辆行进的平均用时，最终将各个方法的表现绘制成下图。

![](img/9b291315612c9c478ad54afa29381fce_184_0.png)

多种红绿灯调度方法效果对比图

从上图可以看到，进行周期性轮换，即让每种红绿灯状态维持同样的时间间隔是效果不佳的方法，延长了车辆的平均通行时间。在使用实时路况信息的调控策略中，基于规则的方法可以优先放行具有最多等待车辆数或是最多运行车辆数的车道，通行效率得到了改善。而实验表明，使用强化学习的红绿灯调度取得了最小平均通行时间，从下图更能看出，智能体可以灵活考虑多变的路况，更好地提升通行效率。

![](img/9b291315612c9c478ad54afa29381fce_185_0.png)

智能体红绿灯调度下流畅的运行与周期性轮换调度导致的拥堵

智能红绿灯不仅在测试阶段表现优秀，它还能够快速地从环境中学习调控策略。从下图可以看到，一个从未学习的智能体在第一轮蓝色的训练中习得的策略，就足以在第二轮绿色的训练中取得接近最优的表现，这样迅捷的学习速度能够帮助智能体适应多变的交通路况。

![](img/9b291315612c9c478ad54afa29381fce_185_1.png)

智能红绿灯训练表现

## 本部分小结

本部分依次引入红绿灯调度的问题背景，对强化学习及其基本概念作了简要介绍，由浅入深地学习了Q-learning和Deep Q-learning两个经典算法，最终在编程实践中基于CityFlow模拟器实现了智能信号灯。由于篇幅限制，强化学习的诸多内容无法一一展开，希望通过本部分的学习，激发你对强化学习的兴趣，在课外积极拓展，同时也要注重亲自动手编程实现，进一步加深理解。

## 结语

太棒了！恭喜你读完了本书。这意味着你已经对人工智能应用有了更深入的了解。你接触了5个有趣的人工智能项目，了解了许多人工智能领域的著名算法，并揭开了这些算法的神秘面纱，能够了解它们的原理，还能自己编写代码实现。希望这本书能够让你体验到人工智能的趣味与神奇，并对它产生兴趣。如果想要了解更多其他有趣的应用，欢迎访问本书配套实验平台 www.boyu.ai/playground。平台上会不定时更新更多的应用供你体验，你和伙伴们可以阅读相应的代码，甚至更改代码创造自己的人工智能算法。

本书作为青少年人工智能实践入门书籍，更多地侧重于每个项目的必备知识及代码实现，但还有许多其他知识没有涉及。若对这个领域感兴趣，建议你去阅读本系列丛书的第三册《人工智能技术入门：让你也看懂的AI“内幕”》，你将会对人工智能知识有一个更系统的了解。

除了本套丛书，人工智能领域中还有许多讲解更深入的教材和教程。当你掌握了扎实的数学基础后，可以尝试接触高级算法，实现更难的任务，甚至尝试改进算法。人工智能领域的发展非常迅速，每年都会有大量的新算法出现、大量的新任务被攻克，希望你多思考多练习，未来可以成为新算法的发明者，解决更多有趣、有意义的问题！

最后，非常感谢你阅读本书，希望你有所收获！

## 附录一 “思考与实践”解答参考

- 1.1 RGB图像中每个像素需要3个数字表达，100×100的RGB图像需要100×100×3=30 000个数字。
- 1.2 还可以存储透明度信息。比如常见的PNG格式的图片，就包含了每个像素的RGB颜色信息和透明度。
- 1.3 能。一张灰度图可以被表示为一个形状为（长，宽，1）的三阶张量，通道数为1。事实上，一个形状为（x，y，1）的三阶张量与形状为（x，y）的矩阵是等价的。但是在某些程序中，输入图像要求用三阶张量而非矩阵表示，在后面的代码实战会遇到。
- 2.1 见2.4 K近邻算法的缺点中的第二项。
- 3.1 （1）对图像色彩对比度、亮度变换；（2）裁剪图片；（3）对图片的值加上噪声扰动。
- 6.1 增大内容权重，生成图片会更多地保留内容图片的内容。增大风格权重，生成图片的风格化就愈发浓郁。
- 6.2 采用梯度下降算法，收敛速度较慢，损失在下降过程中会伴随有一定幅度的振荡。采用Adam收敛速度较快，损失稳步下降。Adam的效果优于梯度下降，与L-BFGS相当。
- 6.3 可以将目标图片初始化为内容图化。这种初始化方法的效果比随机初始化更好。
- 7.1 颜色最容易被学习到，纹理其次，几何形状最难学到。
- 7.2 （a）可行；（b）可行；（c）效果一般，颜色和纹理可以转换，形状比较困难；（d）不可行，猫和狗形状差异过大；（e）不可行，因为毕加索的绘画大多牵涉到形状的变换。
- 7.3 首先循环一致性损失占的比重较大。在这种情况下，反色两次之后恢复出来的图像与原图差异不大，并不能产生较大的循环一致性损失。解决方法可参考：https://ssnl.github.io/better_cycles/report.pdf
- 8.1 这里所说的统计语言模型有一个很直观的缺陷——当前词的概率仅与之前的词有关，事实上在实际的语言中，上下文都是重要的语境，这一点大家做英语语法填空题的时候就可以感受到。比如说动词的时态，很多时候决定性的时间状语可能出现在动词之后。
- 9.1 n较大：能够包含更多的语境信息，但是在语料库中出现的频率较低。n较小：在语料库中出现的频率较高，但是语境信息不足。
- 10.1 编程实践。一般来说，长短期记忆的训练时间更长一些，因为计算复杂。
- 13.1 本题为开放性问题，这里提供一种可能的思路：将双方下一步可以落子的位置数
