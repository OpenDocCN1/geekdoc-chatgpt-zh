❍ 第十七章围绕提示词生成工具的研发展开，利用 GPT-2 技术自建一个预训练模型，并完整实现语料处理、词表构建、分词与标记、模型训练、推理执行及接口封装的闭环，帮助读者掌握 GPT 类模型的训练思路与落地流程。

❍ 第十八章构建 AI 小镇示例，采用 React.js 配合 Phaser3 打造轻量 RPG 风格前端，并接入大语言模型，以小镇为舞台呈现角色随机漫游与多轮对话的交互效果。

## **第九章**
## **对话客户端实战**

### **9.1 目标**

以大语言模型为核心的聊天应用，本质上是依托自然语言处理技术实现人机对话的智能产品。模型经海量数据训练后可理解语义、具备对话能力，用户提问时应用会借助学习到的知识与 NLP 技术解析意图并生成回答。本章以 React.js 构建通用聊天前端，接入 ChatGLM3 项目提供的 OpenAI 兼容接口，完成编译并部署至 Nginx。

### **9.2 原理**

#### **9.2.1 功能概要**

（1）对话交互界面
应用采用标准聊天式布局，接收用户输入并写入对话记录，大模型的返回结果同步回写。双方文本通过左右排布或图标区分。

（2）流式接口
为 OpenAI 兼容的流式调用规范，最早由 ChatGPT 普及，现已成为大模型对话的事实标准。服务端分段推送数据，客户端边收边显，适合大流量或长时间任务。

（3）多轮会话
模型会把当前问题与历史问答合并生成上下文，token 较长时语境更完整，答案通常更合理。代价是轮次增加导致输入 token 持续增长，影响推理效率，因此需对历史记录做截断限制。

（4）打字效果
借鉴 ChatGPT 的逐字呈现方式，先展示部分生成内容降低等待感知，避免一次性输出长文本带来算力压力。实现依赖 SSE 服务端推送，HTML5 机制允许服务端主动推送消息，前端实时渲染出动态打字效果。

#### **9.2.2 系统架构**

整体为 B/S 架构。服务端由大语言模型的 OpenAI API 提供，客户端以 React.js 编写并托管于 Nginx。用户通过浏览器加载页面，JavaScript 将输入投递至服务端并渲染返回结果。

其架构图见图 9-1。

[PRE0]

图 9-1 Chat 应用架构图

（1）大模型服务层
推理由模型代码完成，例如 ChatGLM3-6B 的 modeling_chatglm.py 中 stream_chat 实现流式推理；API 服务由 ChatGLM3 源码 openai_api_demo/api_server.py 暴露。

（2）应用服务层
开发环境下由 npm 启动本地服务，负责静态资源分发与请求代理。生产环境则由 Nginx 的静态部署与反向代理承担，同时需开启对 SSE 推流的支持。

（3）客户端浏览器层
浏览器运行的 HTML5 与 JavaScript 与应用服务层通信，HTTP/HTTPS 请求经代理转发至模型 API。

#### **9.2.3 运行原理**

关键流程如下。

① 浏览器负责页面展示并调用 OpenAI 兼容接口获取生成内容。
② Nginx 提供静态页面并代理对 OpenAI 兼容接口的调用。
③ 页面与接口保持在同一域名或 IP:端口 下，以规避跨域拦截。跨域指前端页面与后端 API 的源不一致时浏览器出于安全策略阻断请求。
④ openai_api.py 完成模型加载并提供 API 服务。

### **9.3 开发过程**

#### **9.3.1 Node.js 安装**

React 项目的调试需 Node.js 环境。可在 https://nodejs.org/ 下载安装，安装完成后在命令行执行 node -v 验证。

#### **9.3.2 chat-app 新建**

（1）新建应用
创建名为 chat-app 的 React 应用，命令如下。

[PRE1]

（2）安装组件
项目依赖两个第三方包，一个是用于对话界面的 ChatUI 框架，另一个是调用模型服务的 Node.js 客户端 openai-node。

[PRE2]

#### **9.3.3 源代码**

Chat 页面与业务逻辑集中在 src/app.js，共约 61 行，完整内容如下。

[PRE3][PRE4]

#### **9.3.4 测试**

**1. 启动大模型 API 服务**
按 5.1 节方式安装 ChatGLM3-6B。API 服务使用 ChatGLM3 代码库 openai_api_demo/api_server.py 启动。该文件 /v1/embeddings 接口依赖 BAAI/bge-large-zh-v1.5 文本向量模型，虽本例不使用，为保证正常运行建议提前下载。

[PRE5]

指定模型与向量模型路径后启动服务，监听 8000 端口。

[PRE6]

**2. 测试 chat-app**
在客户端目录执行 npm start 启动开发服务，浏览器打开 http://localhost:3000，运行效果如图 9-2。

运行可见应用支持流式人机对话、多轮会话与打字视觉效果，已达到预设目标。

[PRE7]

图 9-2 chat-app 运行情况

**3. 界面美化**
chat-app 为通用客户端，通过修改 apiKey 与 baseURL 可接入任意 OpenAI 兼容的大模型服务，包括官方 OpenAI 服务。

为让输入框支持多行，首先在 app.js 同级创建 chatui-theme.css，内容如下。

[PRE8]

随后在 app.js 第 6 行 import OpenAI from 'openai' 后追加引入语句。

[PRE9]

重新执行 npm start 后输入框即变为多行。

#### **9.3.5 应用发布**

开发阶段依赖 Node.js 并以 npm start 运行，发布时需编译为纯 HTML5 与 JavaScript 文件部署到 Nginx。

**1. 编译**
运行命令将源码编译到 build 目录。

[PRE10]

**2. 发布**
把 build 目录文件复制到 Nginx html 目录即可。Windows 下为 Nginx 安装目录下，Linux 常见路径为 /usr/local/share/nginx/html 或 /usr/local/nginx/html。

**3. 运行**
首次启动 Windows 执行 nginx.exe，Linux 执行 sudo nginx。若 Nginx 已运行且配置变更，需重启。

[PRE11]

Linux 环境命令前需加 sudo。

**4. 应用升级**
浏览器会缓存相同 URL 的静态资源。为使更新生效，可修改 package.json 中的 version，编译后生成不同文件名，改变 URL 以绕过缓存。

**5. SSE 配置**
启动后可用 http://127.0.0.1 访问页面，但 Nginx 80 端口提供页面，而 API 在 8000 端口，会触发跨域。解决方式是在 Nginx 80 端口配置对 API 的路径代理，并适配 SSE。

例如 baseURL 为 http://server-llm-dev:8000/v1 时，在 conf/nginx.conf 的 server 块中添加 location。

[PRE12][PRE13]

重启 Nginx，将 app.js 中的 baseURL 改为 http://127.0.0.1/v1，重新编译部署后跨域问题消除。

## **第十章**
## **代码助手实战**

大语言模型的核心能力是预测，即基于提示词生成续写文本，也可称推理或生成。ChatGPT 之前，文本补全是主流场景，GitHub Copilot 为代表。Copilot 在编码时提供行级补全或代码块建议，可提升效率与协作。开源模型兴起后，出现了大量模仿或替代方案，如 Codegen、Llama Code。Stability AI 发布的 Stable Code 3B 拥有 30 亿参数，擅长多语言代码补全与缺失片段填充，且可在无专用 GPU 的笔记本上运行。

本章以 Stable Code 3B 的 API 封装为目标，便于后续客户端调用，在第十一章构建 VS Code 插件形成类 Copilot 产品。

### **10.1 目标**

Stable Code 3B 本身不提供 Web 端 API，仅暴露生成方法。本章在生成方法上封装一层兼容 OpenAI Chat 服务的接口，方便远程调用，并可由第九章的 Chat 客户端直接使用。

### **10.2 原理**

#### **10.2.1 功能概要**

作为推理服务提供者并对外暴露 OpenAI 兼容 API，需具备：

❍ 模型装载：用 Transformers 库将 Stable Code 3B 加载到 GPU 内存以提供生成服务。
❍ OpenAI 接口规范：监听 /v1/chat/completions 等路径，解析请求报文并返回规范响应。
❍ SSE 访问：以 SSE 协议渐进式推送生成内容。
❍ 停止生成策略：模型若不被主动中断会持续生成，需设定停止条件及时结束推理。

#### **10.2.2 系统架构**

包含客户端与 API 服务端。客户端发送含编程提示词的 HTTP 请求，服务端解析后将任务交给 predict 函数并以 SSE 事件流返回结果。predict 循环调用模型的 generate 方法，将增量文本推送给客户端，直至触发停止策略。架构见图 10-1。

[PRE14]

图 10-1 辅助编程应用架构图

#### **10.2.3 运行原理**

基本流程如下。

1）客户端：输入提示词并展示返回结果，通过 OpenAI 兼容接口调用服务，收到增量内容后即时更新。
2）模型服务：使用 Transformers 的 AutoModelForCausalLM 加载 Stable Code 3B，响应 generate 调用。
3）调用过程：
① /v1/chat/completions 接收请求。
② 解析请求提取 prompt、role、max_length 等参数。
③ 委托异步函数 predict 执行生成。
④ 返回 EventSourceResponse。
⑤ predict 循环调用 generate，将返回文本作为下次输入并推送增量内容，满足停止策略后结束。

### **10.3 开发过程**

客户端实现已在第九章介绍，本节以 React.js 为例说明通过 openai.beta.chat.completions.stream 处理请求与 SSE 响应。重点阐述服务端开发。

#### **10.3.1 开发环境准备**

先建测试目录。

[PRE15]

创建 requirements.txt，内容如下。

[PRE16]

随后创建虚拟环境并安装依赖、下载模型。

[PRE17][PRE18]

#### **10.3.2 测试模型准备**

模型下载后，先微调官方示例程序用于验证代码生成效果。示例文件 stable-code-inference.py，内容如下。

[PRE19]