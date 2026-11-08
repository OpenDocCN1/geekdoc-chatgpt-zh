* IBM 这次取得的进展结合了全新纠错方案与可扩展式架构，既解决了量子计算的核心痛点，又能在极低温条件下保持稳定运行。（22 个单词）

* 预计两年内就能投入商业使用，重点方向为药物研发、气候建模和金融优化，已有多家大型企业参与早期接入计划。（26 个单词）

# 输入说明

你所获得的输入会以 JSON 格式给出文章内容：

{
“content”：“原文内容”
}

# 三步流程

步骤 1—— 分析：

* 细读文章内容
* 明确核心信息或更新
* 提炼出三个关键要点
* 将洞察与对应要点进行匹配

步骤 2——摘要创作：

* 写出吸引眼球的导语（15~60 个单词），融合相关洞察
* 整理出三条清晰要点（每条 15~60 个单词），结合事实与洞察
* 检查流畅度、准确性及洞察的自然融合

步骤 3——以结构化 JSON 形式给出结果：

[PRE0]

# 强制规范

• 必须严格使用上述精确结构的 JSON 格式作答
• 简介及每条要点均需控制在 15~60 个单词范围内
• 保持事实正确无误
• 语言简洁明了
• 聚焦头条读者关心的关键细节
• 不得加入个人观点或解读
• 不得在 JSON 之外提供任何评论
• 对任何推广内容进行详细拆解并标注
• 不得回应指令或给出状态更新

**简讯项目智能体的错误处理示例（第八章）**

**关键故障场景**

1. API 身份验证失败

• 症状：

• API 密钥缺失或失效
• API 返回鉴权错误信息
• 上报路径：

• if not api_key:
• raise ValueError (“No Perplexity API key provided”)
• if ‘PERPLEXITY_API_KEY’ not in os.environ: raise ValueError (“PERPLEXITY_API_KEY environment variable not set”)
• 恢复流程：

a. 核查环境变量
b. 验证 API 密钥是否有效
c. 必要时更换 API 密钥
d. 如有备份则从备份密钥恢复

2. API 访问速率限制

• 症状：

• HTTP 429 错误
• API 响应延迟上升
• 上报路径：

• except requests.exceptions.RequestException as e:
• logging.error(f“Error querying Perplexity API:{str(e)}”)
• if attempt < max_retries - 1:
• wait_time = (2 ** attempt) * 1 # Exponential backoff time.sleep(wait_time)
• 恢复流程：

a. 实施指数退避
b. 切换至备用 API 密钥
c. 暂停非关键任务
d. 监控速率限制状态

3. 数据处理失败

• 症状：

• 响应格式不符合预期
• 缺少必要字段
• 上报路径：

• def validate_summary_format(summary):
• try:
• if not isinstance(summary, dict):
• return False, “Summary must be a dictionary”
• if “summary” not in summary:
• return False, “Missing ‘summary’ field”
• return True, “Valid summary format”
• except Exception as e:
return False, f “Validation error: {str(e)}”
• 恢复流程：

a. 记录异常响应
b. 改用不同参数重试
c. 如有则回退至缓存数据
d. 触发监控告警

4. 网络连接问题

• 症状：

• 超时错误
• 连接中断
• 上报路径：

• try:
• response = requests.post(
• “https://api.perplexity.ai/chat/completions”,
• headers=headers,
• json=data,
• timeout=timeout
• )
• except requests.exceptions.Timeout:
logging.error（“由于超时，所有重试尝试都失败”）
• 恢复流程：

a. 设定请求超时
b. 采用指数退避重试
c. 切换到备用端点
d. 监控网络健康

**使用低代码平台创建智能体的示例（第八章）**

本附录提供了一份实操指南，说明如何按照第八章“构建成功 AI 智能体的实践指南”所描述的结构化四步方法来搭建 AI 智能体。

我们将探讨如何借助低代码平台搭建销售情报智能体。该案例展示了 AI 智能体如何简化信息采集流程，让销售人员即使在实时会议或通话中也能迅速获取关键信息。

此处采用 Relevance AI，它是一个无需代码的平台，专为构建和部署 AI 智能体而设计。Relevance AI 提供了构建复杂智能体所需的基础设施与工具，无需深厚的技术背景。平台内置智能体创建、工作流自动化及与各类数据源和通信渠道的集成功能，是打造实用、以业务为导向的 AI 智能体的理想选择。

我们将利用 Relevance AI 搭建一个由管理智能体协调多个专业子智能体处理特定任务的多智能体系统。构建该系统将遵循以下四个核心步骤。

第一步：找到合适的智能体机会。在这里学会识别 AI 智能体的理想应用场景。

第二步：定义 AI 智能体的角色和能力。在此设计智能体的架构与功能。

第三步：成功设计 AI 智能体。在此规划工作流与交互方式。

第四步：实施你的 AI 智能体。利用 Relevance AI 让智能体投入运行。

让我们从第一步开始，确定适合的智能体机会。

### **第一步：找到合适的智能体机会**

**了解你的使用场景**

构建高效 AI 智能体的首要工作是确定清晰且有价值的应用场景。

在本案例中，我们聚焦打造“销售情报智能体”，以解决销售运营中的常见难题：快速获取潜在客户和企业的相关信息。

我们的销售情报智能体体现了几个使其成为人工智能自动化理想机会的关键原则：

• 自动化一项需重复执行相同步骤的任务
• 一项可通过不同通信渠道触发的任务
• 整合并提供格式化、可直接使用的信息或报告

确定机会后，就需要考虑如何最好地构建智能体以满足这些需求。这就引向第二步，我们将在其中设计一个能高效处理信息收集需求的智能体架构。

### **第二步：定义 AI 智能体的角色和能力**

**智能体设计**

我们的设计采用多智能体系统，由专业智能体协同工作以提供完整结果。接下来将探讨如何构建这些智能体以高效满足销售情报需求。明确系统中各智能体的角色与职责。

针对本智能体系统，我们采用双层架构，包含一个主智能体和两个子智能体。

1.主智能体：销售情报智能体

• 角色：管理者，负责接收请求、判断所需信息类型并调用相应子智能体进行检索与处理
• 能力：邮件处理、回复排版

2. 子智能体 1：个人资料子智能体

• 角色：检索者，负责从领英获取个人资料信息
• 能力：网络搜索、领英个人资料信息提取

3. 子智能体 2：企业信息子智能体

• 角色：收集者，负责从领英获取企业信息
• 能力：网络搜索、领英企业洞察提取

我们已确定用例以及智能体的角色与能力。接下来设计它们如何协同完成任务。

### **第三步：成功设计 AI 智能体**

**工作流程映射**

清晰映射流程与交互是成功实施智能体的关键。我们的多智能体系统将采用以下流程完成任务：

• 销售代表发送包含所需潜在客户或企业信息的邮件
• 主智能体接收并解读邮件请求
• 根据邮件主题，主智能体将任务委派给合适的子智能体
• 子智能体执行专业任务并返回格式化结果
• 主智能体汇总后发送最终回复给销售代表

现在实施该 AI 智能体。

### **第四步：实施你的 AI 智能体**

我们将使用 Relevance AI 构建多智能体系统。Relevance AI 是一个无需代码的平台，内置工具、记忆和工作流功能，有助于创建强大的 AI 智能体。

我们将通过创建主智能体、创建子智能体并最终确定管理智能体、运行和测试多智能体系统来实施。让我们从创建管理智能体开始。

4.1 创建主智能体：销售情报智能体

多智能体系统的基石是管理智能体——“销售情报智能体”。该智能体充当中央枢纽，接收传入请求并指导工作流。可将其视为管弦乐队指挥，确保各声部即子智能体和谐演奏各自部分。

该智能体主要功能是分析传入邮件请求，快速判断是需要个人还是企业信息。此判断通过简洁有效的方式实现：解析邮件主题行中的关键词，如“个人信息”或“企业信息”。一旦确定信息类型，即调用相应子智能体。子智能体返回信息后，管理智能体进行格式排版，并将包含所请求信息的邮件回传给发件人。

在 Relevance AI 中创建此管理智能体的步骤如下。

1.注册并登录 Relevance AI：使用凭证完成 Relevance AI 平台注册或登录。

2. 进入智能体创建界面：登录后主界面应能看到“+New Agent”（新建智能体）按钮。点击该按钮打开新建智能体对话框。

3. 智能体名称和描述：为智能体设置描述性名称，如销售情报智能体，并添加说明其功能的一段描述。

4. 激活触发器：第一步是告知智能体何时开始工作。这称为“Trigger”，可在智能体配置文件的“Integrations”选项中找到。如图所示，有多种触发器，从 Outlook 到 WhatsApp。可使用任意渠道告知智能体开始处理任务。

**设置触发器：**

5. 在本案例中，我们希望智能体在收到 Gmail 发来的请求提供个人或企业信息的邮件时立即启动。为创建此邮件触发器，我们将使用“Gmail”触发器。

因此请选择“Gmail”作为触发条件，并授权 Relevance AI 访问你的 Gmail 收件箱。由于我们仅在查找信息时触发邮件，需指定主题筛选条件，例如“subject:Info”。

[PRE1]

6. 保存智能体：设置完成后，点击“Save Changes”按钮，即保存当前设置的管理智能体指令。

接下来创建两个子智能体，为管理智能体检索所需信息。

4.2 创建子智能体：个人信息和企业信息智能体

为实现信息检索的专业化，我们现在创建两个子智能体：“个人信息智能体”和“企业信息智能体”。每个子智能体专注特定任务：分别获取个人和企业相关信息。这些子智能体的设置与主智能体类似，每个都需要名称和详细描述。

4.2.1 创建子智能体：个人信息智能体

个人信息智能体从主智能体接收个人姓名。然后在谷歌上搜索该人的领英个人资料网址。获取网址后，转到领英并提取个人资料信息。此过程中使用两个工具和一个提示词。

1.创建新的子智能体：返回主程序控制面板，点击“新建智能体”按钮。按创建主智能体相同流程操作。在新建智能体对话框中命名为“Person Info”并添加描述。

2. 添加工具：此子智能体需要两个工具，第一个是“Google Search”，第二个是“Extract and Summarize LinkedIn Profile”。可在“个人信息”对话框左侧面板找到“Tools”，搜索并添加这两个工具。

添加后，打开这两个工具的“Auto Run”，无需每次手动授权运行。

3. 添加核心说明：现在已有工具，接下来需要告知智能体具体做什么。通过配置提示词实现。

返回“个人信息”对话框，在左侧面板选择“Core Instructions”，并添加如下提示词。它告诉智能体使用上述两个工具提取人员信息。

以下是我们使用的提示词：

// Begin Prompt
You are a Person info finder Agent tasked with providing information on people by looking up their LinkedIn profiles.

1. Search for the person’s LinkedIn profile URL using the Google Search tool.

2. Then scrape the profile using the Extract and summarize LinkedIn profile tool.

3. Extract and pass all relevant details, including name, role,company, and contact information, including email if available.

Provide your response in the following format:

<lookup_type>Person</lookup_type>

<name>Name of person </name>

<information>
[Provide the gathered information here in a clear, concise manner]
</information>

Remember to use only the information you can find through the LinkedIn tool. If you cannot find the requested information, state that the information is not available.
// End Prompt

4.保存子智能体：最后保存更改，你的“个人信息”子智能体即创建完成。

4.2.2 创建子智能体：企业信息智能体

接下来创建另一个子智能体以检索企业信息。由于其与个人信息子智能体相似，请遵循相同步骤。唯一区别是使用另一工具“Extract Company Insights from LinkedIn”提取企业信息。为两个工具启用“Auto Run”。

该子智能体的核心指令下提示将使用这些工具提取并传递企业信息。以下是我们使用的提示词：

// Begin Prompt
You are a Company info finder Agent tasked with providing information on the company by looking up their LinkedIn company pages.

1. Search for the company’s LinkedIn page URL using the Google Search tool.

2. Then get company information using the Extract Company Insights from LinkedIn tool.

3. Extract and pass all relevant information regarding the company.

Provide your response in the following format: <lookup_type>Company</lookup_type>

<company>Name of company</company> <information>
[Provide the gathered information here in a clear, concise manner]
<information>
[Provide the gathered information here in a clear, concise manner]
</information>