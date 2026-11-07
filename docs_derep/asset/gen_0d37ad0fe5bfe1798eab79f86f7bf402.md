(2) 增强异常处理能力：在代码中加入 try-except 结构，专门拦截 subprocess.CalledProcessError，这样在执行过程中出现异常时可以稳妥应对，并返回清晰可读的错误提示。

(3) 简化文本编码：设置 text=True 参数后，subprocess.run 会自动完成编码与解码，返回结果直接是字符串而非字节流，代码更简洁易读。

（4）规范输出格式：借助 strip() 方法对模型返回内容进行修剪，去除首尾多余的空白与换行，让输出更干净整洁。

（5）增加开始引导：在程序启动阶段加入退出说明，使用户明确如何结束对话。

（6）提升结束体验：在对话收尾时给出结束提示，让交互过程更友好自然。

执行上述代码，输出结果如下，

[PRE0]

## 9.2 Chatbox 可视化知识库接入

把 DeepSeek 接入 Chatbox 即可打造一个可视化知识库，用户能够直接在 Chatbox 界面与 DeepSeek 交流，实现查询与管理。

### 9.2.1 DeepSeek 接入概述

DeepSeek 在全球引发热议后，众多科技厂商纷纷宣布自家产品已接入该模型。所说的接入，本质上是将 DeepSeek 的大语言模型嵌入到自家产品或服务平台，用以提升智能交互能力和整体服务水平。具体优势见表 9-1。

表9-1 DeepSeek接入的优点

| 优点 | 说明 | 举例 |

|---|---|---|

| 提升产品智能化交互水平 | 通过接入DeepSeek，产品能够利用其强大的语言理解和生成能力，为用户提供准确、流畅且富有逻辑的对话服务 | 字节跳动的火山引擎接入了DeepSeek R1模型，为开发者提供了高性能的AI推理服务，支持多种应用场景，如智能对话、文本生成、代码生成等 |

| 拓展产品功能与应用场景 | 借助DeepSeek的高性能和低使用成本优势，产品能够在市场上更具竞争力，同时为用户提供多种创新的功能和更好的使用体验 | TCL通过接入DeepSeek，实现了AI助手的全面升级，覆盖电视、空调、手机等多个品类产品，提升了家电说明书问答、产品控制、风险预警等方面的能力 |

| 增强产品竞争力与用户体验 | 接入DeepSeek可以为产品带来新的功能和更广泛的应用场景 | 海信电视接入DeepSeek后，用户可以通过语音对话便捷地查询信息、控制设备等，提升了智慧生活体验 |

总体来看，接入 DeepSeek 后开发者可借助其强大的自然语言处理能力，为应用增添智能对话、文本生成、代码生成等功能。对企业机构而言，这不仅能提高内部知识管理与协作效率，还能通过定制化微调打造贴合业务的智能方案，推动数字化转型。

### 9.2.2 Chatbox 接入实操

下载安装 Chatbox 后，按以下步骤接入 DeepSeek API。

（1）启动 Chatbox，点击“使用自己的API key或本地模型”按钮，如图9-8所示。

（2）在弹出的“选择并配置AI模型提供方”对话框中，选择“DeepSeek API”选项，如图9-9所示。

[PRE1]

图9-8

[PRE2]

图9-9

（3）弹出“设置”对话框，将个人的 DeepSeek API key 输入“API密钥”文本框中，如图9-10所示。

（4）单击“保存”按钮完成配置，即可通过 Chatbox 调用 DeepSeek 实现聊天功能，如图9-11所示。

[PRE3]

图9-10

微积分是数学的核心分支，核心在于研究函数的**变化**即微分与**累积**即积分，由牛顿和莱布尼茨于 17 世纪独立创立。以下梳理其核心内容与应用：

## 一、两大核心概念

### 1. 微分（导数）

- **本质**：刻画函数在某一点的**瞬时变化速率**，如速度、斜率。
- **表示**：$f'(x) = \lim_{h \to 0} \frac{f(x+h) - f(x)}{h}$
- **例子**：
  - 位移的导数是速度，速度的导数是加速度。
  - 经济学中边际成本是总成本函数的导数。

### 2. 积分

- **本质**：求函数在某区间内的**累积总量**，如面积、总量。
- **表示**：$\int_a^b f(x)dx$
- **例子**：
  - 对速度函数积分可得路程。
  - 概率密度函数的积分表示概率。

## 二、微积分基本定理

微分与积分之间的桥梁：

[PRE4]

即导数的积分等于原函数的净变化。

## 9.3 NextChat 实战部署

NextChat，全称 ChatGPT Next Web，是一个开源项目，旨在让用户轻松把 ChatGPT 等大型 AI 模型集成到网页应用中。

### 9.3.1 NextChat 核心功能

NextChat 的主要功能如图 9-12 所示。

### 9.3.2 源码本地运行

实际使用 NextChat 有两种方式：运行本地源码和本地安装。下面介绍运行源码的流程。

（1）访问 NextChat 的 GitHub 项目页面，按说明把源码克隆或下载到本地。

（2）确认本机已安装 Node.js 和 npm 等必要开发工具。

（3）在源码根目录打开命令行或终端，执行以下命令安装依赖。

[PRE5]

或

[PRE6]

（4）从 DeepSeek 平台获取专属 API key，并在 NextChat 配置文件中填写密钥及相关模型信息。

（5）在终端运行以下命令，启动 NextChat 的本地开发服务器。

[PRE7]

打开本地服务器地址，通常为 http://localhost:3000，即可查看 NextChat 界面。

[PRE8]

图9-12

### 9.3.3 安装包本地部署

本地安装 NextChat 的步骤如下。

（1）访问 GitHub 项目页进入“Releases”区域，按操作系统版本选择并下载对应安装文件，如图 9-13 所示。

（2）以 Windows 为例，下载完成后双击 .exe 文件，随即弹出“NextChat Setup”对话框，如图 9-14 所示。

（3）单击“Next”按钮后进入“Choose Install Location”界面，如图 9-15 所示。

（4）单击“Next”按钮后进入“Choose Start Menu Folder”界面，如图 9-16 所示。

Releases · ChatGPTNextWeb

github.com/ChatGPTNextWeb/NextChat/releases

| File | Size | Date |

|---|---|---|

| latest.json | 2.43 KB | Nov 25, 2024 |

| next-chat_2.15.8_amd64.AppImage | 87.2 MB | Nov 11, 2024 |

| next-chat_2.15.8_amd64.AppImage.tar.gz | 86.3 MB | Nov 11, 2024 |

| next-chat_2.15.8_amd64.AppImage.tar.gz.sig | 432 Bytes | Nov 11, 2024 |

| next-chat_2.15.8_amd64.deb | 7.9 MB | Nov 11, 2024 |

| NextChat_2.15.8_universal.dmg | 13.5 MB | Nov 25, 2024 |

| NextChat_2.15.8_x64-setup.exe | 6.1 MB | Nov 11, 2024 |

| NextChat_2.15.8_x64-setup.nsis.zip | 6.1 MB | Nov 11, 2024 |

| NextChat_2.15.8_x64-setup.nsis.zip.sig | 428 Bytes | Nov 11, 2024 |

| NextChat_2.15.8_x64_en-US.msi | 6.57 MB | Nov 11, 2024 |

| Source code (zip) | | Nov 11, 2024 |

| Source code (tar.gz) | | Nov 11, 2024 |

Show all 16 assets

18 3 4 22 people reacted

Nov 5, 2024

lloydzhou

v2.15.7

08d6cb2

# v2.15.7 Add Claude haiku 3.5 and Support ChatGLM

What's Changed

图9-13

[PRE9]

# Welcome to NextChat Setup

Setup will guide you through the installation of NextChat.

It is recommended that you close all other applications before starting Setup. This will make it possible to update relevant system files without having to reboot your computer.

Click Next to continue.

图9-14

# Choose Install Location

Choose the folder in which to install NextChat.

Setup will install NextChat in the following folder. To install in a different folder, click Browse and select another folder. Click Next to continue.

Destination Folder

C:\Users\AppData\Local\NextChat

Space required: 11.7 MB

Space available: 409.3 GB

2023, Zhang Yifei All Rights Reserved.

图9-15

[PRE10]

图9-16

（5）单击“Install”按钮后进入“Installation Complete”界面，进度条展示实时安装进度，如图9-17所示。

（6）单击“Next”按钮，进入“Completing NextChat Setup”界面，单击“Finish”按钮完成整个安装工作，如图9-18所示。

[PRE11]

图9-17

[PRE12]

图9-18

（7）NextChat 的初始界面为聊天界面，单击左下角的“设置”图标按钮⚙，如图9-19所示。

（8）在“设置”界面中依次配置以下项。

- ◎ **模型服务商**：选择对应的模型服务提供商。
- ◎ **接口地址**：输入模型服务的API接口地址。
- ◎ **API Key**：填入你的API密钥。
- ◎ **自定义模型名**：根据需要输入自定义的模型名称。
- ◎ 模型（model）：选择或输入模型的具体名称。

例如，使用“deepseek-coder”模型进行对话，如图9-20所示。

[PRE13]

图9-19

[PRE14]

图9-20

（9）设置完成后即可成功调用 DeepSeek 进行对话，如图9-21所示。

# 自我介绍

共 2 条对话

你好呀！我是 DeepSeek Chat，由深度求索（DeepSeek）公司研发的智能 AI 助手。我可以帮你解答各种问题，包括学习、工作、编程、写作、生活百科等，还能阅读和总结上传的文件（如 PDF、Word、Excel 等）。

# ✨ 我的特点

- ✅ **免费使用**：目前无需付费，随时向我提问！

- ✅ **知识丰富**：我的知识截止到 2024 年 7 月，能提供较新的信息。

- ✅ **超长上下文**：支持 128K 超长文本理解，适合处理大段资料。

- ✅ **文件阅读**：可以解析 PDF、Word、Excel、PPT、TXT 等文件内容。

- ✅ **中文优化**：对中文理解和生成特别友好，适合中文用户。

# 🚀 我能帮你做什么？

- 📚 **学习辅导**：解题思路、论文润色、知识点讲解

- 💼 **工作效率**：写邮件、做 PPT、数据分析

- ✍️ **创意写作**：故事、诗歌、广告文案

- 💻 **编程助手**：代码调试、算法优化、技术问答

- 🌍 **生活百科**：旅行建议、健康小贴士、美食推荐

你可以随时向我提问，我会尽力提供最准确、有用的答案！😊 你今天想了解什么呢？

图9-21

## 9.4 OfficeAI 接入 Office

在信息爆炸的今天，将 DeepSeek 接入 Office 意义重大。Office 用户需处理大量数据并追求高效办公。DeepSeek 凭借强大的语言理解与生成能力，能为 Word 撰写、Excel 数据解读等提供有力支持，帮助用户快速生成优质文本、精准解读数据并设计更具创新性的演示方案，从而提升效率与质量，满足数字化办公对智能辅助的迫切需求。

### 9.4.1 OfficeAI 功能概览

OfficeAI 是一款免费的 AI 办公软件，面向 Microsoft Office 与 WPS 用户设计，旨在借助 AI 技术提升办公效率。OfficeAI 的功能如图 9-22 所示。

[PRE15]

图9-22

### 9.4.2 OfficeAI 下载安装

（1）访问 OfficeAI 官网，单击“立即下载”按钮下载安装包，如图 9-23 所示。

（2）确保计算机中 Office 程序均已关闭，按安装向导完成安装，安装完成的界面如图 9-24 所示。

# OfficeAI助手

一款提升50%办公效率的智能助手软件，适合使用Microsoft Office 和 WPS Office 办公的用户，提供与 Office Copilot类似的功能和使用体验

[PRE16]

# OFFICEAI助手

[PRE17]

图9-24

### 9.4.3 Word 中启用 DeepSeek

（1）安装 OfficeAI 后，打开 Word 可在顶部菜单看到新增的“OfficeAI”标签。单击该标签可在“OfficeAI”选项卡中看到“快捷功能”“一键排版”“AI创作”“万能翻译”“图片转文字”“表格”等功能，如图 9-25 所示。

[PRE18]

图9-25

（2）单击“OfficeAI”选项卡最左侧的“右侧面板”按钮，即可展开“海鹦OfficeAI助手”界面，在该界面中可与 AI 大模型聊天，如图 9-26 所示。单击“Office AI”选项卡最右侧的或“海鹦OfficeAI助手”界面中聊天框左下角的“设置”按钮⚙，可打开大模型的“设置”对话框。

（3）在大模型的“设置”对话框中切换到“ApiKey”选项卡，进行相关配置。在“API_KEY”文本框中填入自己的 DeepSeek API key，如图 9-27 所示。

[PRE19]

图9-26

[PRE20]

图9-27

（4）设置完成后，即可在右侧对话框中与 DeepSeek 对话。例如发送“我是一名家长，马上要召开家长会了，请帮我写一篇演讲稿”，即可获得回复，如图 9-28 所示。

（5）单击对话下面的“导出到左侧”按钮后，即可将对话内容快速复制到 Word 中，如图 9-29 所示。

# 家长会发言稿

尊敬的各位老师、家长朋友们：

大家好！我是（孩子姓名）的家长，非常荣幸能在这里和大家交流。首先，请允许我代表所有家长，向辛勤付出的老师们致以衷心的感谢！正是你们的悉心教导和无私奉献，才让孩子们在学习和成长的道路上不断进步。