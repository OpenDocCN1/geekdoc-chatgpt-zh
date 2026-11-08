[PRE0]

-   Index.ets

Index.ets 作为交互入口，用按钮来触发问答调用，并把后端回传的流式内容实时呈现在页面上。请求由 EmotionService 发起，结果分段到达时页面会逐步刷新，保持展示与生成同步：

##### 3. 效果演示

打开前端后，首屏会出现一个“Fetch Data”按钮。按下后应用会向 Python 端的 QA 接口发起问答请求，返回内容以流式方式一点点拼接到页面上。模拟器上的运行截图见图4-6，生成结果清晰可见。

在 BasicInteractionFrontend 这一轮优化后，前后端在流式数据上的配合变得高效。新的分层设计让问答结果可以边生成边呈现，用户等待感明显降低。模块边界清晰，代码更好维护，也为后续功能拓展保留了空间。

[PRE1]

## 图4-6

#### 4.2.2 情感驱动前端升级

本部分在 BasicInteractionFrontend 的基础上，对 EmotionDrivenFrontend 做情感化扩展，新增情感句生成与安慰诗生成能力。前端可以依据用户情绪实时产出对应句子和诗歌并即时展示。主要新增点有：引入 EmotionSentencesRequest 和 SoothingPoetryRequest 两个请求体结构；在 EmotionService 中补充 generateEmotionSentences 与 generateSoothingPoetry 方法以完成与后端的交互；复用 HealingPoetryApp 中的 Index.ets、CustomButton.ets、ButtonManager.ets 并对 Index.ets 升级，从而强化情感互动体验。

##### 1. 目录规划

EmotionDrivenFrontend 在原有结构上补充了新文件，调整后的目录如下：

[PRE2]

重点更新的文件说明如下。

ApiTypes.ets：用来描述 EmotionSentencesRequest 和 SoothingPoetryRequest 两类请求的结构。

EmotionService.ets：新增 generateEmotionSentences 与 generateSoothingPoetry，用于发起情感句子和安慰诗歌的生成请求。

Index.ets：升级后的主页面，加入了情感句子与安慰诗句的生成与展示交互。

ButtonManager.ets 和 CustomButton.ets：负责按钮的渲染与交互管理。

##### 2. 代码改造详情

本项目在前端侧的改动集中在以下几处。

-   ● ApiTypes.ets

补充了情感句子生成请求与安慰诗句生成请求的接口定义：

[PRE3]

-   ● EmotionService.ets

EmotionService.ets 中新增 generateEmotionSentences 与 generateSoothingPoetry 方法，完成与后端的数据交互并获取情感句子与安慰诗句：

[PRE4]

-   ● Index.ets

Index.ets 提供情感句子生成和安慰诗句生成的交互入口，通过按钮操作控制页面内容的展示与更新：