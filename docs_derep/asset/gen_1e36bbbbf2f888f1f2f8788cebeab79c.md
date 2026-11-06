配置完成后，进入 AutoGen Studio 的 Playground 页签，点击 Sessions 旁的 +New 按钮，选择刚建好的工作流，把视频链接填入输入框即可开始调试。

### 4.2.4 AutoGen Studio 本地化配置

#### 1. 如何在 AutoGen Studio 中加载本地大语言模型

要接入本地大模型，首先部署 LM Studio。按图 4-44 选择对应系统版本下载，本例以 Windows 为例。

[PRE0]

安装包解压后即可打开。受网络限制，LM Studio 在国内无法直接拉取模型，需先到 Hugging Face 手动获取再导入。

打开 Hugging Face 官网，进入 Models，搜索 Qwen。本案例选用阿里通义千问的 Qwen/Qwen1.5-0.5B-Chat-GGUF，GGUF 是 LM Studio 支持的格式。模型体积方面，0.5B 约 5 亿参数，适合普通笔记本。

进入 Qwen/Qwen1.5-0.5B-Chat-GGUF 页面，切换到 Files and versions，选择 qwen1_5-0_5b-chat-q8_0.gguf，点击 Download 图标下载。

[PRE1]

下载完成后回到 LM Studio，左侧点 My Models，设置 Local models folder。需要在 lm-studio\models 下按 Qwen/Qwen1.5-0.5B-Chat-GGUF 逐层建目录，最终放置路径为 lm-studio\models\Qwen\Qwen1.5-0.5B-Chat-GGUF，才能被识别，如图 4-46。

[PRE2]

加载成功后点左侧 AI Chat，在顶部下拉选择刚导入的通义千问模型。随意输入问题，得到应答即说明加载正常，如图 4-47。

[PRE3]

LM Studio 右侧提供较多设置，可自行调整 GPU 占用、提示词等。

验证通过后点左侧 Local Server，点击 Start Server 启动本地服务。可选 chat（python）复制测试代码，结果如图 4-48。

在 PyCharm 新建项目，新建 test.py，粘贴代码后运行。若提示缺 OpenAI，打开终端执行 pip install openai。

[PRE4]

安装完再次运行，若输出 ChatCompletionMessage(content="My name is Alex, and I'm a software engineer living in New York City. I enjoy solving problems and creating innovative solutions to real-world challenges. Whether it's coding my way through a complex project or working on a challenging new feature, I always strive to be the best at what I do.", role='assistant', function_call=None, tool_calls=None)，说明服务已正常启动。

此时回到 LM Studio，Server logs 也会打印相同返回，本地服务运行无误，如图 4-49。

随后回到 AutoGen Studio，将上面启动的本地模型接入 Models。需先修改 Anaconda 环境下 \Lib\site-packages\openai\_client.py，由于当前版本不稳定，加载本地模型易掉线，在第 112 行后追加两行代码：

[PRE5]

保存后打开 AutoGen Studio 的 Build 页签下的 Models，点击 +New Model，填写模型名称、API Key 与 Base URL，点击 Test Model，成功后保存，如图 4-50。

再到 Build 页签下的 Workflows，新建一个基于本地大模型的流，命名为 qwen，只需把 Model 配置好，如图 4-51。

图 4-51

切换到 Playground，点击 +New 载入刚创建的 qwen 流，在对话区输入“你是谁”，期望得到“我是来自阿里云的大语言模型，我叫通义千问。”，调试完成，如图 4-52 和图 4-53。

图 4-52

下面演示 Ollama 的本地部署，加深对本地模型服务的理解。访问 Ollama 官网，点击 Download for Windows (Preview) 下载安装包，双击安装。安装后可按电脑配置挑选模型，如图 4-54。

打开 cmd，执行 ollama run llama3 等待模型拉取完成，用“你是谁”测试，如图 4-55。

再开一个 cmd，执行 pip install litellm 安装，如图 4-56。litellm 可统一调用各类 LLM 接口，包括 Bedrock、Huggingface、VertexAI、TogetherAI、Azure、OpenAI 等。

安装完成后执行 litellm --model ollama/llama3，如图 4-57。

图 4-57

若出现报错，按提示补装依赖直至服务正常，如图 4-58。

图 4-58

回到 AutoGen Studio，进入 Build 选项卡的 Models，点击 +New Model，名称改为 Llama 3，Base URL 设为 http://0.0.0.0:4000，点击 Save 保存。

最后新建工作流，并在 Playground 中新建 Llama 3 完成调试。

## 第4章 AutoGen Agent 开发框架实战

### 2. AutoGen Studio 本地化服务部署

将 AutoGen Studio 的界面封装成服务，供外部请求调用。下面以一个实战案例说明封装思路。

案例背景：迪哥运营公众号，手动看视频、整理要点、写成文章耗费时间很大。他希望用 AutoGen Studio 做一个 Agent，输入视频地址后自动抽取内容并生成公众号稿，提升效率。

第一步，迪哥已准备好部署环境，如图 4-59。

图 4-59

Replit 是一款在线 IDE，也是代码协作与云服务平台，支持 Python、JavaScript、Java 等语言。无需本地安装，通过浏览器即可运行、协作、部署，内置代码补全、调试、版本控制等工具，并支持 React、Flask 等框架，一键部署 GitHub 开源项目。

点击 Fork 按钮复制迪哥的本地化部署代码到个人仓库，需先在 Replit 注册登录。登录后点击 Fork，在 Fork Repl 对话框填入名称与描述，点击 Fork Repl 创建，如图 4-60。

创建成功后，回到 AutoGen Studio，导出之前配置好的工作流，例如 MyGroup Workflow，下载 write.json。

把 write.json 导入 Replit 工程，点击 Run 启动服务。main.py 核心代码如下：

[PRE6]

其中 task_query = f"请根据视频地址 '{workflow_params.youtube_video_id}'，先利用 youtube-transcript-api 工具包来获取视频内容，再进行公众号文章的生成"，需要配套前端让用户输入视频地址并触发服务生成文章。

打开前端文件 index.html，代码如下：

[PRE7]

注意把 fetch(`https://5ccb1a61-fa99-41dd-9dc5-2afb26d7ffae-00-pe6drxhw0tz1.***er.replit.dev/run_workflow/`) 中的域名替换为自己的 Replit 地址。

回到 Replit，点击右上角 new tab，浏览器会打开并显示专属 Replit 地址，如图 4-61，复制后替换到前端代码中。

图 4-61

替换保存后打开 index.html，填入视频地址，点击“生成公众号文章”按钮即可。

按此流程，读者可用 AutoGen Studio 制作的工作流导出 json 导入 Replit，实现前后端联动，完成项目闭环。

## 第 5 章 生成式代理——以斯坦福 AI 小镇为例