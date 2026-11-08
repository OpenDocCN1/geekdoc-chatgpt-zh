[PRE0]

##### 3. 配置项设定
`main_service_config.json` 主要用来登记模型接口地址、模型参数以及服务监听端口等核心配置。

##### 4. 依赖安装
`requirements.txt` 集中罗列了 Flask 与其他运行所必需的组件。请务必在启动服务前先完成这些依赖的安装：

[PRE1]

##### 5. 启动及校验
先安装依赖：

[PRE2]

启动Flask服务：

[PRE3]

使用curl验证服务是否正常运行：

[PRE4]

图4-1 展示了基于 Flask 的流式问答服务的运行效果。

[PRE5]

#### 4.1.2 情感驱动实现
本节在 BasicInteractionBackend 的基础上进行功能叠加，引入情感驱动能力。新增了 `prompt_service.py` 专门负责情感语句与抚慰诗歌的提示构建，同时对 `main_service.py` 做了升级，使其能够接收与情感相关的请求并以流式方式返回结果。这一扩展让模型可以根据用户指定的主题创作情感句子或抚慰性诗歌，满足情绪表达与安抚方面的需求。

##### 1. 目录结构搭建
EmotionDrivenBackend 在保留基础问答能力的同时，补充了情感语句与抚慰诗歌的生成模块。以下是其整体目录布局：

[PRE6]

##### 2. 情感语句与抚慰诗创作
新加入的 `prompt_service.py` 负责定义生成情感句子和抚慰诗歌的提示模板，而 `main_service.py` 则依据这些模板调用 DeepSeek 模型完成内容生成，并以流式形式回传。

下面是 `prompt_service.py` 的具体实现片段：

[PRE7]

该模块中的 `PromptService` 类主要提供两个方法：
`create_emotion_sentences_prompt` 用来组装生成情感句子的提示；
`create_soothing_poetry_prompt` 用来组装生成抚慰诗歌的提示。

##### 3. 主服务升级
`main_service.py` 经过升级，在维持基础问答处理的基础上，额外接入了情感句子与抚慰诗歌的生成接口。以下为其核心代码片段：