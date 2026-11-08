要让模型把检索结果浓缩成用户直接可用的答案，可以先用 GPT-3 API 做摘要。思路是先准备一个模板字符串 summary_prompt，模板里预留两个占位位置 SEARCH_QUERY_HERE 和 SEARCH_RESULT_HERE，分别对应原始搜索问题和检索到的内容。随后借助 Python 字符串的 replace 把这两个占位替换成实际值：前者换成变量 f1_query，后者换成结果 DataFrame 里第一条记录的文本。最后调用 openai.Completion.create，传入使用的引擎模型、填完的模板以及最大 token 限制，模型就会按这个提示生成一段回应客户查询的摘要文本。

[PRE0]

官网的运行结果为：

- Breach of FIA regulations can lead to disqualification

- Imposed sanctions may include suspension of Competitor's Staff Certificate of Registration

- Competitor's Staff Certificate of Registration may also be withdrawn for remaining duration of the season

- Competitor has right to appeal the IT's decision

- Competitor must make written request to be heard

- FIA will support Competitor's request

- Stewards have no authority to suspend or withdraw Competitor's Staff Certificate of Registration for any breach or alleged breach of the FIA Code of Good Standing

谈到护城河，目前知识库已经向量化存进了 Redis，内部搜索应用已经能跑起来。单纯的问答虽然方便，但在交互深度上比较吃亏，如果用户提问不够精准，系统就无力追问、澄清或引导对话，难以把用户带到正确路径。

更灵活的做法是用聊天自动补全的终端做一个对话机器人，它会：

- 先接收定义行为方式和用户目标的系统指令
- 主动收集开展任务所需的关键信息
- 和客户多轮往返，直到信息收集完整
- 发出约定的触发词，启动语义检索与摘要流程

这里展示了如何与 ChatCompletion 端点交互，核心是一份消息列表，每条消息都包含 role 角色，取值系统、用户或助手，以及对应的 content 内容。

[PRE1]

官网的运行结果为：

assistant: As an AI language model developed by OpenAI, I can help you in several ways. Here are some examples:

- Answer questions: You can ask me any questions you have, and I'll do mybest to provide accurate and helpful answers. I can help you with anything from math problems to historical facts to recommendations for restaurants or movies.

- Assist in writing: Whether you need help with grammar, sentence structure, or word choice, I can assist you in writing. I can also help you generate ideas for writing assignments and provide writing prompts for inspiration.

- Provide information: If you need information on a specific topic or want to learn more about a particular subject, I can provide you with useful information and resources.

- Give suggestions: If you're unsure about something or need advice, I can give you suggestions and recommendations based on my knowledge and experience.

- Chat and entertain: If you're feeling bored or lonely, I can chat with you and try to entertain you with jokes, stories, or interesting facts.

下面进入实操，用 GPT-3 API 做聊天自动补全，并借助 termcolor 给终端输出上色，搭建一个可用的聊天机器人。

先从 termcolor 引入 colored，用来给打印内容加颜色。接着定义一个 Message 类，作为聊天消息的基础结构，内部保存 role 和 content 两个字段。额外提供一个 message 工厂方法，返回符合 {role, content} 格式的字典。

随后封装 Assistant 类来承载对话。实例内部维护 conversation_history 用来记录历史。_get_assistant_response 负责真正打 API，入参 prompt 其实就是上轮助手的回复加上本轮用户的提问。调用成功就把结果封装成 Message 返回，失败则返回带异常信息的字符串。

ask_assistant 是对外接口，接收用户的新提问 next_user_prompt，先写入历史，再调用内部生成方法拿到回复，同样把回复写回历史并返回。

另外提供 pretty_print_conversation_history，用来把整段对话以易读的方式输出到控制台。

[PRE2]

随后实例化 Assistant 类，开始与机器人进行对话

[PRE3]

官网的运行结果为：

As a business assistant with innovative ideas, I can:

1. Conduct market research: I can carry out thorough research on your target market, analyze consumer behavior, and gather insights to help you make informed decisions about your business.

2. Develop a strategic plan: I can help you come up with a comprehensive business plan that includes a clear vision, goals, and actionable steps to help you achieve your business objectives.

3. Create a strong online presence: I can help you create a compelling website, develop a social media strategy, and create engaging online content to help you connect with your customers.

4. Improve customer service: I can help you identify areas where your customer service needs improvement and help you develop strategies for how to provide excellent service to your customers.

5. Optimize your operations: I can help you streamline your business processes, identify inefficiencies, and create systems that will improve productivity, reduce costs, and increase profitability.

6. Explore new revenue streams: I can help you identify new opportunities for revenue generation, such as introducing new products or services, or expanding into new markets.

7. Develop innovative marketing: I can help you come up with innovative marketing strategies that will help you stand out from competitors and resonate with your target audience.

再追问下一个问题：

[PRE4]

官网的运行结果为：

Developing a strategic plan is one of the most critical aspects of running a successful business. A strategic plan outlines a clear vision for your business and provides a roadmap for achieving your goals. As a business assistant, I can help you develop a comprehensive strategic plan that includes: