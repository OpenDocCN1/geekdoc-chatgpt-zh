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
        作的Q值
        separate_hidden = Dense(20, activation="sigmoid")(shared_dense)
        q_values = Dense(self.num_phases, activation='linear')
        (separate_hidden)

    # 信号灯状态选择层，Q值网络只会输出当前信号灯状态所对应的独立全连接层的输出
    # 当phase与输入节点cur_phase对应同一个信号灯状态时，selector为1，否则为0
    selector = Selector(phase)(input_node_dict['cur_phase'])

    # 同样的，当phase与cur_phase相同时，selected_q_values与输出Q值q_values相
    同，否则为0
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