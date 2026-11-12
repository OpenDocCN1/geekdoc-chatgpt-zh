[PRE0]

# 核心参数定义

# eps：时间步长（离散精度）
# damping：阻尼系数（能量衰减）

eps = tf.placeholder(tf.float32, shape=())
damping = tf.placeholder(tf.float32, shape=())

# 状态变量初始化

U = tf.Variable(u_init)      # 当前场状态
Ut = tf.Variable(ut_init)    # 状态变化率（速度场）

# 离散化 PDE 迭代公式

# 位置更新：欧拉法积分速度
U_next = U + eps * Ut
# 速度更新：拉普拉斯算子驱动扩散 + 阻尼耗散
Ut_next = Ut + eps * (laplace(U) - damping * Ut)

# 封装状态同步操作
step_op = tf.group(
    U.assign(U_next),
    Ut.assign(Ut_next)
)

[PRE1]

# 启动会话并初始化所有变量
tf.initialize_all_variables().run()

# 主仿真循环：共演化 1000 个时间步
for i in range(1000):
    # 单步推进，注入超参数
    step_op.run({eps: 0.03, damping: 0.04})

    # 每 50 步渲染一次波场快照
    if i % 50 == 0:
        clear_output()
        DisplayArray(U.eval(), rng=[-0.1, 0.1])