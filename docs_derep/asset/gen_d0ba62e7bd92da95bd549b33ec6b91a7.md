[PRE0]

4) Web 服务启动

[PRE1]
- **--host** 用来决定服务监听的地址，这里填 0.0.0.0 就意味着服务可以被任何网络地址访问，可接入范围更广。
- **--port** 用来指定服务绑定的端口。
- **--model_path** 用来指向转换完成的 CTranslate2 模型所在位置。
- **--num_workers** 用来设定并发推理时所使用的线程数量。

5) 调用语音识别接口
服务跑起来后会对外提供两个入口：普通的识别接口 `/recognition` 与流式返回接口 `/recognition_stream`。两者都需要用户上传一份完整的音频文件，区别在于流式接口会在识别过程中逐步推送结果，更适合处理长音频；普通接口则要等整段音频处理完毕后一次性返回完整结果。

两个接口的请求参数完全一致，主要包括：

- **audio**：必填项，用于上传待识别的音频文件。
- **to_simple**：可选项，默认值 1，控制是否把识别结果中的繁体字转为简体。
- **remove_pun**：可选项，默认值 0，控制是否去掉识别结果中的标点。
- **task**：可选项，默认值 transcribe，用于指定任务类型，可选 transcribe 转录 和 translate 翻译。
- **language**：可选项，默认值 zh，用于指定识别语言，留空时系统会自动探测语言。

接口返回的数据包含以下字段。

- **results**：识别结果列表，每个元素包含 result 文本内容、start 开始时间秒 和 end 结束时间秒。
- **code**：错误码，0 代表识别成功。

例如返回结果示意如下：

[PRE2]

下面给出了两段 Python 调用示例。第一个示例演示如何调用 `/recognition`，第二个示例演示如何调用 `/recognition_stream` 并实时消费流式返回。

第一个示例：

[PRE3]

第二个示例：

[PRE4]

## 9.3.3 语音合成方案 Sambert-Hifigan
前面提到，Sambert-Hifigan 依赖的 kantts 与 ttsfrd 对运行环境有限制，其中 ttsfrd 仅支持 Linux x86_64 架构且 Python 版本在 3.6 到 3.10 之间，其他平台或版本暂不支持。因此需要把 Sambert-Hifigan 单独部署到满足条件的环境中并提供对外调用接口。功能本身比较单一，用 Django 作为 Web 框架会显得臃肿，这里选用 FastAPI 来搭建服务。

### 1. FastAPI 概览
FastAPI 是 Python 社区目前很受关注的 Web 框架，基于 Python 3.6+ 构建，充分发挥类型提示的优势，结合 Starlette 异步能力与 Pydantic 数据校验，成为 Python 中性能领先的框架之一，速度可以媲美 Node.js 与 Go。

FastAPI 的目标是简单、高速、高效，帮助开发者提升效率并减少出错。核心特性如下。

- **降低错误**：借助类型提示和自动校验，能在运行前捕获大量常见问题，可减少约 40% 人为失误。
- **易学易用**：设计注重直观，文档清晰，上手门槛低。
- **生产就绪**：可直接生成可在生产环境使用的代码，并自动生成交互式文档，便于理解 API。
- **标准兼容**：完全遵循 OpenAPI 规范，原名 Swagger，以及 JSON Schema。

FastAPI 获得广泛好评。微软的 Kabir Khan 表示计划将其用于团队所有机器学习服务。总体来看，FastAPI 是现代、高性能、易用的 Python Web 框架，能提高开发效率、降低错误率、性能优秀，已在业界得到认可。

Sambert-Hifigan 可以通过下列代码封装为 FastAPI 接口，接收 GET 请求的 query 参数 text，经模型推理生成音频并保存，最后把音频返回给前端。

[PRE5]

## 2. Sambert-Hifigan 部署
Sambert-Hifigan 必须在特定平台上运行，直接采购新机器当然可行，更省事的做法是使用 Docker。

Docker 是开源容器引擎，可将应用及其依赖打包成可移植容器后发布到主流系统如 Linux 或 Windows，实现虚拟化。容器基于沙箱隔离，互不干扰。

Docker 的核心优势是把应用、依赖、库、系统工具和代码打包进独立镜像，镜像可在任何运行 Docker 的主机上启动，操作系统不同也不影响运行。它还能提供高效的部署与扩容，容器间可通过网络和磁盘共享服务，便于将应用服务模块化、轻量化。

因此即便只有一台 Windows 电脑，也能通过 Docker 运行一个满足部署条件的 Linux 容器，大幅节省成本。更详细的 Docker 学习可自行前往官网 https://www.docker.com/ 查阅。

构建镜像使用的 Dockerfile 如下：

[PRE6]

其中 requirements.txt 声明了该应用依赖的第三方库：

[PRE7]

随后可通过 docker-compose 启动容器。Docker Compose 用 YAML 来描述多容器应用的配置，一条命令即可创建并启动全部服务。

[PRE8]

该文件定义了一个名为 `tts_sambert_hifigan` 的服务，主要构成说明如下：

- `version: "3"`：Docker Compose 配置文件版本，版本 3 支持更多特性。
- `services`：用于声明所有服务，对应每个容器。此例仅定义 `tts_sambert_hifigan` 一个服务。
- `build: .`：指示在当前目录查找 Dockerfile 并构建镜像。
- `command`：容器启动时执行的命令，这里启动 uvicorn 服务器，uvicorn 是 ASGI 项目的高性能轻量级服务器。
- `restart: always`：容器退出时总是自动重启。
- `volumes`：定义挂载卷用于存储数据或共享数据。此处将当前目录映射到容器 `/app`，并将主机的 `/etc/localtime` 映射到容器内，实现时间同步。
- `ports`：定义端口映射，把容器 8001 端口映射到主机 8001 端口。

总体而言，该 Compose 文件使用当前目录的 Dockerfile 构建镜像，运行 uvicorn 并始终重启，将当前目录与本地时间文件映射到容器，并把 8001 端口暴露到主机。

容器启动成功后，可在浏览器访问 http://127.0.0.1:8001/tts?text= 你好世界 下载生成的音频。首次访问需要下载模型文件，耗时较长，具体取决于网络，可通过容器日志查看下载进度。

## 9.4 后端应用搭建
本节简要介绍后端搭建的整体流程，实际开发时可按需求自定义。

### 9.4.1 创建项目

#### 1. 安装 Django 及相关依赖

[PRE9]

以上仅列出核心功能所需的依赖，真实开发中可能需要更多依赖，按需依次安装即可。

#### 2. 创建 Django 项目

[PRE10]

#### 3. 创建应用（用户、绘本、模型）

[PRE11]

StoryBook 应用负责绘本相关功能，Account 应用负责用户认证相关模块。

#### 4. 添加依赖项
在 `CartoonistBackend/settings.py` 的 `INSTALLED_APPS` 列表中加入所需依赖的应用以及上面创建的两个应用。