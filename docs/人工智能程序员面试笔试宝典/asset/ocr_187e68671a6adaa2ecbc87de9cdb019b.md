```
class NoisyLinear(nn.Linear):
    def __init__(self, in_features, out_features, sigma_init=0.01):
        super(NoisyLinear, self).__init__(in_features, out_features)
        self.sigma_init = sigma_init
        self.sigma_weight = Parameter(torch.Tensor(out_features, in_features))
        self.register_buffer('epsilon_weight', torch.zeros(out_features, in_features))

        self.reset_parameters()

    def reset_parameters(self):
        """
        Initialize the biases and weights
        """
        init.constant_(self.sigma_bias, self.sigma_init)
        init.constant_(self.sigma_weight, self.sigma_init)
        std = math.sqrt(3/self.in_features)
        init.uniform_(self.weight, -std, std)
        init.uniform_(self.bias, -std, std)

    def forward(self, input):
        self.epsilon_bias.data.normal_()
        bias = self.bias + self.sigma_bias*self.epsilon_bias
        self.epsilon_weight.data.normal_()
        # 带有噪声的新的权重
        weight = self.weight + self.sigma_weight*self.epsilon_weight
        # create the linear layer it the added noise
        return F.linear(input, weight, bias)
    if noisy_net:
        # In case of NoisyNet use noisy linear layers
        self.fc = nn.Sequential(
            NoisyLinear(conv_out_size, 512),
            nn.ReLU(),
            NoisyLinear(512, n_actions))
    else:
        self.fc = nn.Sequential(
            nn.Linear(conv_out_size, 512),
            nn.ReLU(),
            nn.Linear(512, n_actions))
```