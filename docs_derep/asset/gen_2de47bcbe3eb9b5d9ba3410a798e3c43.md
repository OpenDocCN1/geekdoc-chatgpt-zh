[PRE0]

达到用量上限时 OpenClaw 会直接停掉模型调用，避免账单爆炸。社区里常有“一觉醒来账单上千”的惊悚案例，提前设个预算上限才安心。

##### 可选项 3：消息渠道

这个选项决定你在哪儿和 OpenClaw 说话。可以用飞书、钉钉、Telegram，也可以就留在浏览器 Web 界面。

在向导里会问你现在是否配置一个渠道。选了比如飞书，就会让你填 App ID 和 App Secret。直接跳过的话只能先用 Web 界面，后续随时用 `openclaw channels add` 命令补上。

不填渠道就只能在浏览器里聊，手机上用不了、飞书里也 @ 不到它。对新手而言，先用 Web 界面摸熟功能也合理，等熟练后再接入别的渠道也不迟。

#### 1.5.4 配置文件位置与格式

所有设置最终都会落到一份 JSON 配置文件里：

- Mac/Linux：~/.openclaw/openclaw.json
- Windows：C:\Users\你的用户名\.openclaw\openclaw.json

文件大致长这样：

[PRE1]

理论上也能手工改，但 JSON 语法很挑：

- 逗号、引号、括号一处错就读不出；
- 字符串只能用双引号；
- 最后一个条目后面不能加逗号；
- 改完必须重启 Gateway。

建议用 VS Code 这类带 JSON 校验的编辑器，改错了会立刻提示，手误概率小很多。

#### 1.5.5 配置后怎么验证

配完先确认是否真生效。

看 Gateway 是否正常：

[PRE2]

理想状态是 Runtime: running 且 RPC probe: ok。

看模型是否加载：

[PRE3]

会列出当前所有已配置的模型。

做一次对话测试：执行 openclaw dashboard 打开 Web 界面，或按 openclaw gateway status 给出的 Dashboard URL 手动访问，发条“你好”或“测试一下”。能返回正常回复说明模型可用；若提示“API Key 无效”，就回头检查 Key 是否填对、账户余额是否充足。

再看整体概览：

[PRE4]

能看到 Gateway 状态、监听端口、已连渠道等信息。

整体流程看起来繁琐，其实就是回答几个向导问题、填几个 Key。向导把复杂部分都封装好了，按提示走就行。配完这一步，OpenClaw 才算真正“活”了，可以开始干活。

### 1.6 API Key 用法与模型选型

装好 OpenClaw 只是搭好“壳”，想让它思考执行，还得接上“大脑”——AI 模型。连接需要 API Key，可以理解为使用某家 AI 服务的通行证。

OpenClaw 本身不是大模型，而是一个把渠道、网关、记忆、技能、模型调用串起来的 Agent 系统。需要模型提供商来做推理，最常见的是 API Key 认证，部分支持 OAuth 或本地模型。官方 FAQ 也明确：OpenClaw 既能用 API Key，也能跑本地模型。

简单说，OpenClaw 管调度、记忆和工具，模型负责推理，两者靠 API Key 衔接。好比你有个秘书 OpenClaw，需要请教专家 AI，API Key 就是专家的会员卡。

Key 一般是 sk-xxxxxxxxxxx 这种随机字符。填进配置后就能调用，调用会扣额度，所以账户要有余额。新用户通常有免费额度，够玩一阵子，用完再充值。

#### 1.6.1 API Key 到底是干嘛的

如上所述，OpenClaw 负责运行 Agent、管理记忆、协调工具，模型只提供推理能力。两者通过 API Key 相连。拿到 Key 后填入配置即可调用。每次调用都消耗额度，建议先用免费额度试水。

#### 1.6.2 模型怎么选，官方支持哪些

选型基本两步：先完成 provider 认证，再把默认模型写成 provider/model 的形式。官方文档中可直接确认的国内模型主要有：

- **MiniMax**：OpenClaw 有独立的 MiniMax provider 页面，推荐 ID 为 minimax/MiniMax-M2.5 和 minimax/MiniMax-M2.5-highspeed。国内直连，速度快、价格适中，适合日常使用。

- **Moonshot（Kimi）**：官方 provider 页面明确支持，推荐写法 moonshot/kimi-k2.5。Kimi 的强项是长文本，上下文可达 200 万字，很适合读文档、分析长材料、整理纪要。

- **Z.AI（GLM）**：智谱 GLM 通过 zai provider 接入，常用模型如 zai/glm-5。清华出品，中文理解强、代码也不错，中文场景体验很好。

- **Qwen（通义千问）**：注意，当前官方 Qwen 页面主推的是 qwen-portal 的 OAuth 登录，而非直接用 DashScope API Key。官方建议先启用 qwen-portal-auth 插件，再执行 `openclaw models auth login --provider qwen-portal --set-default` 登录。

此外还支持 OpenAI GPT 系列、Anthropic Claude 系列（最贵也最强）、Google Gemini 系列，以及 DeepSeek、豆包等。也可以用 Ollama 本地跑开源模型，完全免费，但硬件要求更高。

#### 1.6.3 新手最稳妥的配置方法

不建议新手直接改文件，官方最推荐运行交互式向导：

[PRE5]

向导会带你完成 provider 选择、API Key 输入、默认模型设置的全流程。若已确定要用的厂商，也可以指定认证方式：

[PRE6]

配置完成后会写入 `~/.openclaw/openclaw.json`。以后想换模型或 provider，直接改文件再重启 Gateway 即可生效。

#### 1.6.4 主流模型 Key 获取与填写

##### MiniMax

官方说明：先注册登录，然后在 API Keys 页面创建 Key。按量付费点 “Create new secret key”，订了 Coding Plan 则点 “Create Coding Plan Key”。

拿到 Key 后配置：

[PRE7]

模型 ID 区分大小写。若向导提示缺少认证，通常要补环境变量 MINIMAX_API_KEY。最省事是让 openclaw onboard 向导自动写入。官网 platform.minimax.io，注册后进控制台即可看到 API Keys。新用户有免费额度，充值支持微信、支付宝，充 50~100 元能用很久。

##### Moonshot (Kimi)

对应 provider 为 Moonshot，推荐用 moonshot-api-key 认证流。官方写法：

[PRE8]

默认模型可设为 moonshot/kimi-k2.5。官方文档要求先在 Kimi Open Platform 创建 API Key，再传给客户端。OpenClaw 示例中使用环境变量 MOONSHOT_API_KEY。

获取路径：platform.moonshot.cn 注册登录 → 控制台 → API Keys → 创建新 Key 并保存。Kimi 新用户有免费额度，用完按 token 计费。长文本是其核心优势，处理大段文档很合适。

##### Z.AI (智谱 GLM)

GLM 通过 Z.AI 平台接入，provider 名为 zai。配置写法：

[PRE9]

或：

[PRE10]

模型 ID 写 zai/glm-5，认证方式为 Bearer API Key。流程是注册登录 → 必要时充值 → API Keys 页面创建 Key → 复制使用。入口 open.bigmodel.cn。GLM-5 在中文理解、代码生成、多模态上表现不错，价格比国际大厂低，中文任务性价比高。

##### Qwen (通义千问)

特别提醒：当前官方文档主推 OAuth 登录而非直接填 API Key。步骤：

[PRE11]

## 第一章 Qwen 授权连接流程

这会启动 Qwen 的 device-code OAuth 流程，在浏览器完成登录授权后写进配置文件。官方列出的模型 ID 为 qwen-portal/coder-model 和 qwen-portal/vision-model。

优点是不用手动管理 API Key，授权后即可用。阿里云 DashScope 虽提供 API Key 方式，但在 OpenClaw 官方 Qwen 页面并非主推做法。建议按官方 OAuth 流程走，更稳定省心。

#### 1.6.5 配置后的验证

模型和 Key 配好后先验证：

[PRE12]

列出已配置模型，确认刚才设置的模型在列表中。

再测试调用：

[PRE13]

确认 Gateway 在运行后，打开 Web 界面，地址以 openclaw gateway status 显示的 Dashboard URL 为准，通常是 http://localhost:18789/chat，发条“你好”或“测试一下”。能正常回复说明配置成功、Key 有效。

若报错“API Key 无效”或“认证失败”，检查：

- Key 是否复制完整，有无多余空格或引号；
- 对应平台账户余额是否充足，免费额度是否已用完；
- 网络能否访问模型服务，国际模型可能需要科学上网。

#### 1.6.6 给新手的靠谱建议

第一步：直接跑 openclaw onboard，让向导带完整流程，别自己猜、别手动改文件。

第二步：国内用户优先试 MiniMax、Kimi、GLM，访问快、价格合理、文档友好。

第三步：先用免费额度体验，熟悉后再充值。别一上来充几百，50~100 元足够起步。

第四步：配完一定要先对话测试，确认通畅再往下走，有问题及时解决。

OpenClaw 不自带模型，必须接 provider 才能用。这不是限制而是优势：你可以自由选择最适合需求和预算的模型。今天用 DeepSeek 省钱，明天换 Claude 追求能力，后天切到本地 Ollama 完全离线，都是几行配置的事。这种灵活性，是 OpenClaw 比封闭系统更值钱的地方。

### 1.7 把 OpenClaw 接到微信飞书 Telegram

模型配好后，OpenClaw 已能在浏览器聊天。但日常更便利的是在常用聊天工具里直接 @ 它。需要把 OpenClaw 接到消息渠道上。

#### 1.7.1 渠道接入通用流程

不同平台细节不同，核心大同小异：准备渠道凭证 → 在 OpenClaw 里添加 channel → 确认 Gateway 正常运行 → 完成配对或授权。

主配置文件仍是 ~/.openclaw/openclaw.json，不是 openclaw.yaml，渠道信息写在 channels 字段里。新手不建议手写，用命令行更稳。

最简单的方式：

[PRE14]

会启动交互式向导，列出支持的渠道，按提示输入 Bot Token、App ID 等信息。配置完 Gateway 会自动加载。

如果在 openclaw onboard 初始化时跳过了渠道配置，后续随时可用 openclaw channels add 补上。

#### 1.7.2 Telegram：入门首选

Telegram 是官方最推荐的新手渠道，配置简单、无需公网 IP、无需域名，本地即可跑通。

创建 Telegram Bot

打开 Telegram，搜索 @BotFather，发送 /newbot 命令。

BotFather 会依次询问：

- Bot 显示名称，比如“我的 OpenClaw 助手”；
- Bot username，必须以 bot 结尾，如 my_openclaw_bot。

完成后 BotFather 会返回一个 Bot Token，形如 123456789:ABCdef...，相当于 Bot 密码，务必保存好。

在 OpenClaw 里配置

拿到 Token 后执行：

[PRE15]

选择 Telegram 并输入 Token。向导会问若干问题，其中 DM Policy 私聊策略建议选 pairing，最安全。即陌生人第一次私聊 Bot 时需要你手动批准后才能使用。