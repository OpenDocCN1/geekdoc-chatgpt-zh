[PRE0]

# 第2章 扩散模型核心原理

[PRE1]

## 扩散模型：从理论到代码的全景解析

else:
    raise ValueError(f'unknown objective {self.objective})
    loss = self.loss_fn(model_out, target, reduction='none')
    #对损失函数加权
    loss = loss * extract(self.p2_loss_weight, t, loss.shape)
    return loss.mean()

[PRE2]

+   1.将含噪样本 x_t 送入网络以获得噪声估计
2.依据该噪声估计反推出干净样本 x_0 的猜测
3.在 x_t 与预测的 x_0 之间做一步逆向转移采样得到 x_{t-1}

[PRE3]
#类方法：一个迭代去噪的采样函数

def p_sample_loop(self, shape):

    #参数

    # shape: 输出数据的维度，如果数据是图片，则维度是[batch,channels,256,256]

        batch, device = shape[0], self.betas.device

        #进行高斯采样获得初始值

        img = torch.randn(shape, device=device)

        x_start = None

        #迭代采样，共进行num_timesteps次迭代

        for t in tqdm(reversed(range(0, self.num_timesteps)), total=self.num_timesteps):

            img, x_start = self.p_sample(img, t, self.cond)

            img = unnormalize_to_zero_to_one(img)

        return img

[PRE4]
#类方法：采样函数。采样时不计算梯度

@torch.no_grad()

def p_sample(self, x, t, x_self_cond=None, clip_denoised=True):

    #参数

    # x: 上一步数据

    # t: 时间点

    # x_self_cond: 是否为条件扩散生成

    # clip_denoised: 是否进行clip

        b, _, device = *x.shape, x.device

        batched_times = torch.full((x.shape[0],), t, device=x.device, dtype=torch.long)

        #用训练好的模型预测下一步数据的期望和方差

        model_mean, _, model_log_variance, x_start = self.p_mean_variance(

            x = x, t = batched_times, x_self_cond = x_self_cond,

            clip_denoised = clip_denoised)

        noise = torch.randn_like(x) if t > 0 else 0. # no noise if t == 0

        #使用预测的期望和方差对下一步数据进行采样

        pred_img = model_mean + (0.5 * model_log_variance).exp() * noise

        return pred_img, x_start

[PRE5]python
def p_mean_variance(self, x, t, x_self_cond=None, clip_denoised=True):
    # 参数
    # x: 上一步数据
    # t: 时间点
    # x_self_cond: 是否为条件扩散生成
    # clip_denoised: 是否进行 clip
    # 计算预测结果
    preds = self.model_predictions(x, t, x_self_cond)
    # 根据预测的噪声和公式计算出（预测的）原始图片
    x_start = preds.pred_x_start
    if clip_denoised:
        x_start.clamp_(-1., 1.)
    # 使用时间 t 预测原始图片和当前数据 x_t，计算下一步转移核的期望和方差
    model_mean, posterior_variance, posterior_log_variance = self.q_posterior(x_start=x_start, x_t=x, t=t)
    return model_mean, posterior_variance, posterior_log_variance, x_start
    # 使用预测的噪声计算预测的原始图片，从而获得预测的下一步去噪数据。每一步去噪都需要进行计算

[PRE6]python
def model_predictions(self, x, t, x_self_cond=None, clip_x_start=False):
    # 参数
    # x: 上一步数据
    # t: 时间点
    # x_self_cond: 是否为条件扩散生成
    # clip_denoised: 是否进行 clip
    model_output = self.model(x, t, x_self_cond)
    maybe_clip = partial(torch.clamp, min=-1., max=1.) if clip_x_start else identity
    if self.objective == 'pred_noise':
        pred_noise = model_output
        # 根据公式（2.4）可以直接计算预测的原始图片 x_0
        x_start = self.predict_start_from_noise(x, t, pred_noise)
        x_start = maybe_clip(x_start)
        return namedtuple('ModelPrediction', ['pred_noise', 'pred_x_start'])
    # 使用时间 t 预测原始图片和当前数据 x_t，计算下一步转移核的期望和方差

[PRE7]python
# 使用时间 t 预测原始图片和当前数据 x_t，计算下一步转移核的期望和方差
def q_posterior(self, x_start, x_t, t):
    # 参数
    # x_start: 预测的原始图片
    # x_t: 当前的数据来自上一步采样
    # t: 时间

[PRE8]python
# 计算下一步转移核的期望
posterior_mean = (
    extract(self.posterior_mean_coef1, t, x_t.shape) * x_start +
    extract(self.posterior_mean_coef2, t, x_t.shape) * x_t
)
# 下一步转移核的方差默认是固定的参数
posterior_variance = extract(self.posterior_variance, t, x_t.shape)
posterior_log_variance_clipped =
    extract(self.posterior_log_variance_clipped,t,x_t.shape)
return posterior_mean, posterior_variance,
    posterior_log_variance_clipped

[PRE9]python
E_{t~U[1,T],x0,xt} [λ(t)σ_t^2||∇_{x_t}log q(x_t) - s_θ(x_t,t)||^2]

[PRE10]
公式（2.12）的推导可追溯至文献[238]，其要点是分数匹配与去噪式分数匹配仅相差一个与参数无关的常数；公式（2.13）建立在条件分布 q(x_t|x_c)=N(x_t;x_c,σ²I) 的设定之上；公式（2.14）则源于加噪表达式 x_t = x₀ + σ_t ε。我们把 λ(t) 视作正值的权重函数，常数项记为 const。把 (2.14) 与 (2.10) 对照可见，DDPM 与 SGM 在优化目标上实质相同，只要令 ε_θ = -σ_t s_θ，学习分数就等价于学习噪声预测。

## 扩散模型：从直觉到数学的贯通解读

直观而言，分数函数指向似然更高的、低噪声的数据区域，对原始样本施加的噪声被去除后即可恢复数据并提升似然。由 Tweedie 公式可知，若 y = x + σ ε，则 E(x|y) = y + σ²∇_y log p(y)。该式表明分数函数完整地包含了对原始数据的最小二乘估计信息。对含噪观测 y 加上 σ²∇_y log p(y) 后，噪声在一定程度上被消除，这从另一角度刻画了分数与去噪的联系，沿着更高似然的方向移动数据即实现去噪。Tweedie 公式的证明并不复杂。首先，
\nabla_y p(y) = \frac{1}{\sigma^2} \int (x - y) g(y - x) p(x) dx\n= \frac{1}{\sigma^2} \int (x - y) p(y, x) dx
其中 g(·) 为标准高斯密度，p(x)、p(y)、p(x,y) 分别为 x、y、(x,y) 的密度。其次，
\sigma^2 \frac{\nabla_y p(y)}{p(y)} = \int xp(x|y) dx - \int yp(x|y) dx = \hat{x}(y) - y
又因 ∇_y log p(y) = \frac{\nabla_y p(y)}{p(y)}，代入即可得证。

在样本生成侧，SGM 可借助迭代方式依次使用 s_θ(x_T,T)、s_θ(x_{T-1},T−1)、…、s_θ(x_1,1) 逐步合成样本。SGM 将训练与生成解耦，因此衍生出多种采样策略，下节再展开。最先被引入的采样方法为退火朗之万动力学 ALD[220]。设每步迭代 N 次，步长 s_t>0，先
ε^t ← N(0,I)
x_t^{i+1} ← x_t^i + \frac{1}{2} s_t s_θ(x_t^i,t) + \sqrt{s_t} ε^i
依据朗之万蒙特卡洛的相关理论[176]，当 s_t → 0 且 N → ∞ 时，x_0^N 将收敛于数据分布 q(x₀) 的真实样本。

# 第2章 扩散模型核心原理
# 2.3 随机微分方程

DDPM 与 SGM 均可向时间步无限、噪声强度连续的情形延拓，此时前向扰动与后向去噪都可视为某随机微分方程的解。这种通用表述常被称为 Score SDE[225]，其核心是用 SDE 描述加噪与生成，去噪时须估计加噪数据的分数。相应的生成与去噪示意见图 2-5。

[PRE11]
来源：Yang Song, Jascha Sohl-Dickstein, Diederik P Kingma, Abbishek Kumar, Stefano Ermon, and Ben Poole. Score-Based Generative Modeling

Score SDE 用下列的随机微分方程对数据进行扰动[225]:
[PRE12]
这里 f(x,t) 与 g(t) 分别担任漂移项和扩散项的系数，w 代表标准维纳过程即布朗运动。DDPM 与 SGM 的前向加噪均可看作该 SDE 的离散版本。Song 等[225]已证明 DDPM 对应的 SDE 形式为：
[PRE13]