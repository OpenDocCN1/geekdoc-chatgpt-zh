[PRE0][PRE1]

**小贴士**
拿不准要不要上云，先在本地跑起来最稳妥。等真的遇到出门就联系不上龙虾、定时任务老是中断的情况时再迁移也不晚，配置完全可以一键搬家。

**云部署前要准备什么**
云部署说白了就是在云厂商租一台远程主机，把 OpenClaw 装上去。你不用懂运维，但以下前提得先搞定：

● 系统环境：优先选 Ubuntu 22.04 或 24.04，其次 CentOS 7+，都要 64 位
● 起步配置：2 核 CPU、2~4GB 内存、40GB 硬盘能用；想体验更顺畅，4 核起步、8GB 内存最好，再加张 GPU 更理想
● 网络端口：默认占用 18789，机器买好后务必在控制台的安全组里放行，否则 Web UI 打不开
● 费用：主流厂商入门型每月费用不高，多数有新用户优惠或免费试用期

**部署流程：通用五步模板**
不管选哪家厂商，思路都差不多：

第一步：下单买服务器。去官网注册账号，挑一台轻量应用服务器，入门规格即可，完成实名和支付。
第二步：装镜像。很多厂商已提供 OpenClaw 预装镜像或一键应用模板，直接选中安装最省事。若没有，就选 Ubuntu 22.04 再按 2.4 节的 Linux 安装步骤走。
第三步：远程登录。用 SSH 工具连上，比如 Termius 或 FinalShell，填公网 IP、用户名和密码即可。多数厂商也提供网页终端，直接在浏览器操作。
第四步：配置模型。和本地一样，执行 openclaw onboard 选 AI 模型并填入 API Key，流程参考 2.2 节。
第五步：验证。浏览器打开 http://你的服务器IP:18789，发条消息，龙虾能回就说明成功。

[PRE2]

**小贴士**
第一次上云前先看厂商的官方视频教程，10 分钟基本能避开 90% 的坑，比啃文字快。

**主流厂商参考**
国内多家主流厂商都已上线 OpenClaw 一键部署或预装镜像。这里不点名推荐，因为价格和活动变动快，建议按预算和习惯自行对比。常见厂商汇总见表 2-4。

表 2-4 主流云服务商简介

[PRE3][PRE4]

**注意**
云厂商的价格、活动和产品随时会变，本书不做费用承诺。请以官网实时信息为准。选型建议关注：是否提供 OpenClaw 预装镜像、是否有新人优惠或试用、机房是否靠近你常用地区。

**云端安全底线**
数据放到云上，安全一定要到位，部署完务必自查：

（1）登录密码要强，不要用 123456 这类弱密码。SSH 密钥登录比密码安全，有条件优先用密钥。
（2）端口只开必要的 18789 和 22，其他全部关闭，减少攻击面。
（3）定期备份，建议每周一次。配置文件和记忆文件丢了很麻烦。
（4）选正规大厂。小众或海外不明厂商稳定性与数据安全难保证。
（5）别存高敏感信息。不要放银行卡密码、身份证照片等，OpenClaw 对话会经过模型商服务器，信息边界要清楚。

**常见问题**
**问题 1：云端部署安全吗？**
数据在你自己买的服务器上，只有你能进。但“自己服务器”≠“绝对安全”，仍需做好上述安全配置。停用时可直接删整台机器，数据彻底清除。

**问题 2：会一直扣费吗？**
多数厂商支持按月或按量计费，不用了删机就不会继续扣费。建议先买一个月试用，满意再续。

**问题 3：手机怎么用？**
两种方式：通过飞书、企业微信、QQ 等聊天平台发消息控制龙虾，最方便；或用手机浏览器直接访问 Web UI http://服务器IP:18789。

**问题 4：本地配置能搬到云端吗？**
可以。打包本地 ~/.openclaw 目录，tar -czf backup.tar.gz ~/.openclaw，传到云服务器解压即可，配置、记忆、技能都能无缝迁移。

## **2.6** **更新与维护：让龙虾保持最佳状态**
OpenClaw 迭代很快，平均两周一个版本。保持更新能拿到性能优化、新功能和安全修复，操作很简单，一行命令搞定。

**检查与更新**
# 查看当前版本
openclaw --version

# 查看最新版本
curl -s https://api.github.com/repos/openclaw/openclaw/releases/latest | grep tag_name

# 方式一：安装脚本一键更新（推荐）
curl -fsSL https://openclaw.ai/install.sh | bash

# 方式二：源码手动更新
cd ~/openclaw
git pull origin main
pnpm install
pnpm build

**备份数据**
用久了的记忆和技能很宝贵，建议养成定期备份习惯。

# 备份配置和数据（建议每周）
tar -czf openclaw-backup-$(date +%Y%m%d).tar.gz ~/.openclaw

# 恢复数据
tar -xzf openclaw-backup-20260210.tar.gz -C ~/

[PRE5]

**小贴士**
把备份文件同步到 iCloud、百度网盘等云盘，换电脑或重装系统时能一键恢复，龙虾不会“失忆”。

**监控与日志**
异常时先看日志：

# 实时看运行日志
tail -f ~/.openclaw/logs/gateway.log

# 看系统状态
openclaw gateway status

# 看资源与 API 消耗
openclaw status
openclaw status --usage

## **故障排查**
**Gateway 是否无法启动？**
# 第一步查日志
openclaw logs
# 第二步查端口占用
lsof -i :18789
# 第三步重启
openclaw gateway restart

**API 是否连接失败？**
# 测试连通性
openclaw test api
# 检查 Key 是否配置正确
openclaw config get models.providers

**性能是否变慢？**
# 清理缓存
openclaw cache clear
# 重启服务
openclaw gateway restart

**卸载（彻底删除）**
# 停止服务
openclaw gateway stop
# 删除文件与配置
rm -rf ~/.openclaw
rm -rf ~/openclaw
# 移除全局命令
npm uninstall -g openclaw

[PRE6]

**注意**
卸载前务必备份！尤其是 SOUL.md 和 MEMORY.md，那是龙虾对你的全部记忆。

## **2.7** **API 配置指南：给龙虾选个好大脑**
OpenClaw 本身不是 AI，而是一个代理框架，负责消息路由、工具调用和记忆管理。真正的思考能力来自你接入的模型。

**三类接入方式**
OpenClaw 支持三种接入方式，复杂度与场景不同，见表 2-5。

表 2-5 三类模型接入方式对比

[PRE7]
内置供应商指 OpenClaw 已内置接入逻辑，可在 onboard 向导里直接选择，填 API Key 即可，无需额外配置。

[PRE8]
**小贴士**
新手先用内置供应商跑通全流程，再按需切到国产模型。走顺后换模型只是改一两行配置。

**国内用户的可选供应商**
按接入方式分为两组。

可在 onboard 向导直接选的内置供应商见表 2-6。

表 2-6 可直接在 onboard 向导里选到的供应商

[PRE9]
需手动配置 models.providers 的自定义供应商见表 2-7。

表 2-7 需要手动配置的供应商

[PRE10][PRE11]

**注意**
DeepSeek 社区使用很广，但不在内置列表里，onboard 找不到。需选 Custom Provider 手动填 baseUrl 和 API Key，具体见下文。

**快速上手：内置供应商**
选 Z.AI、Qianfan、Qwen、OpenRouter 等内置供应商，操作很简单：

openclaw onboard
（1）选 QuickStart
（2）供应商列表里点选目标
（3）粘贴 API Key
（4）选择默认模型版本，回车用推荐版即可
（5）完成剩余配置

API Key 获取：注册平台账号 → 控制台 → API Key 管理 → 创建并复制。

[PRE12]

**注意**
API Key 类似 sk-xxxxxxxx，创建时仅显示一次，务必立刻保存，关闭页面后就不可再查看，只能重建。

**手动配置：接入 DeepSeek 等自定义供应商**
以 DeepSeek 为例，兼容 OpenAI 格式的国产模型配置思路一致，在 openclaw.json 的 models.providers 中添加：

{
"models": {
"mode": "merge",
"providers": {
"deepseek": {
"baseUrl": "https://api.deepseek.com",
"apiKey": "${DEEPSEEK_API_KEY}",
"auth": "api-key",
"api": "openai-chat"
}
}
}
}

在 ~/.openclaw/.env 中写入 Key，避免明文：

DEEPSEEK_API_KEY=sk-你的API Key

配置完重启：openclaw gateway restart，再用 openclaw models list 确认是否出现。

[PRE13]

**小贴士**
也可以让龙虾代配，在对话框说“帮我把 DeepSeek 添加为自定义供应商，baseUrl 是 https://api.deepseek.com，API Key 是 sk-xxx”，它会自动写入配置。

**本地模型：用 Ollama 离线运行**
有隐私顾虑或想离线用，可用 Ollama 跑开源模型：

# 安装 Ollama
curl -fsSL https://ollama.ai/install.sh | sh
# 拉取中文优化模型
ollama pull qwen2.5:7b

启动后 OpenClaw 会自动发现 http://127.0.0.1:11434，执行 openclaw models scan 即可列出。本地模型数据不出机，但对内存要求高，70 亿参数建议 16GB 内存，响应比云端慢，适合敏感文件和弱网场景。

**模型故障转移**
OpenClaw 支持备用模型自动切换，在 agents.defaults.model 中配置主模型与 fallback 链：

{
"agents": {
"defaults": {
"model": {
"primary": "zai/glm-5",
"fallbacks": ["openrouter/deepseek/deepseek-chat", "ollama/qwen2.5:7b"]
}
}
}
}

主模型限速或不可用时自动切到 DeepSeek，再不行切到本地 Ollama，形成三层保险。

## **2.8** **本章故障排查手册**
根据大量用户经验汇总，遇到问题先对照，多数 5 分钟内可解。

**安装问题**
**问题 1：Node.js 版本不对**
# 检查版本，必须 ≥22.16.x
node --version
# 低于则升级
nvm install 22
nvm use 22

**问题 2：权限错误**
# macOS/Linux
sudo chown -R $USER ~/.openclaw
# Windows 以管理员 PowerShell 运行
takeown /F "$env:USERPROFILE\.openclaw" /R /D Y
icacls "$env:USERPROFILE\.openclaw" /grant"$env:USERNAME:(OI)(CI)F" /T

**问题 3：网络连接失败、下载慢**
（1）检查网络或开启代理重试
（2）用国内镜像：curl -fsSL https://openclaw.ai/install.sh |bash -s -- --mirror=cn
（3）换手机热点试试

**API 配置问题**
**问题 1：API Key 无效**
（1）确认完整复制，含 sk- 前缀，无多余空格
（2）检查账户余额，余额为 0 无法调用

**问题 2：模型不可用**
（1）核对模型 ID 是否正确，不同平台命名有差异
（2）查看平台状态页是否正常
（3）尝试切换其他版本

**问题 3：Token 消耗快、费用高**
（1）切到更便宜的模型，DeepSeek 性价比高
（2）优化指令，避免每次带大量历史记录
（3）定期清理会话：openclaw cache clear

**Gateway 问题**
**问题 1：Gateway 无法启动**
tail -f ~/.openclaw/logs/gateway.log
openclaw gateway restart

**问题 2：18789 端口被占用**
lsof -i :18789
# 可改端口
openclaw config set gateway.port 18790

[PRE14]

**小贴士**
本书没覆盖的问题，最快办法是把报错截图或复制给大模型如豆包、DeepSeek、ChatGPT，描述操作步骤，通常 10 秒内就能得到解法。

## **2.9** **日常管理命令速查：全平台通用**
装好后最常用的其实是终端命令。Mac、Windows WSL2、Linux 均通用，熟记表 2-8、表 2-9 就掌握日常运维。

[PRE15]

**小贴士**