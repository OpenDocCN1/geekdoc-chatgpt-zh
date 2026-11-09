对话智能系统三件套：路由、状态、分析

一个靠谱的智能客服，离不开精准的意图分发、清晰的会话跟踪和可读的业务洞察。下面的实现围绕这三件事展开，先给用户意图找对执行器，再让对话状态可控可回溯，最后把运营数据自动变成周报。

路由决策的第一原则是保底，再是特权。意图识别的置信度不够高时，系统不会硬猜，而是退回到路由配置里的默认处理器，并把原因记为低置信度触发的知识库兜底。随后会做一次 VIP 校验，如果用户信息表明是 VIP 客户，就调用专属的 VIP 处理器覆盖之前的选择，并将决策理由更新为 VIP 专属处理。最终一次完整的路由记录会落盘，包含选中的处理器和决策理由。

会话的骨架由 ConversationStateManager 负责。它把一次对话拆成十个可追踪的阶段，从开场问候、意图识别、信息收集、槽位填充、确认，再到知识检索、任务执行、答案生成、回访检查，最后到对话结束。初始化时会自动重置上下文，生成唯一的会话 ID，当前状态固定在问候，历史记录、槽位、实体、主题、情感、系统动作、外联上下文以及状态快照全部清空，方便回退。

状态更新是增量的。用户消息和系统回复都会带时间戳写入历史，意图数据到来时当前意图会被写入并触发状态迁移，实体信息也会同步到槽位和记忆中。整个状态对象始终对外可读，保证上下游组件看到的是同一份快照。

[PRE0]python

class ConversationStateManager:

    def __init__(self):

        # 定义状态类型

        self.States = {

            "GREETING": "greeting",

            "INTENT_RECOGNITION": "intent_recognition",

            "INFO_COLLECTION": "info_collection",

            "SLOT_FILLING": "slot_filling",

            "CONFIRMATION": "confirmation",

            "KNOWLEDGE_RETRIEVAL": "knowledge_retrieval",

            "TASK_EXECUTION": "task_execution",

            "ANSWER_GENERATION": "answer_generation",

            "FOLLOW_UP_CHECK": "follow_up_check",

            "CLOSING": "closing"

        }

        # 初始化新会话状态

        self.reset()

    def reset(self):

        """初始化/重置会话状态"""

        self.state = {

            "conversation_id": str(uuid.uuid4()),

            "current_state": self.States["GREETING"],

            "conversation_history": [],

            "slots": {}, # 槽位信息

            "current_intent": None,

            "entities": {}, # 实体记忆

            "topics": [], # 主题跟踪

            "sentiment": {"score": 0, "trend": "neutral"},

            "system_actions": [],

            "external_context": {},

            "state_snapshots": [] # 状态快照,用于回退

        }

        return self.state

    def update_state(self, user_message, system_response=None, intent_data=None, entities=None):

        """更新对话状态"""

        # 更新对话历史

        if user_message:

            self.state["conversation_history"].append({

                "role": "user",

                "content": user_message,

                "timestamp": datetime.now().isoformat()

            })

        if system_response:

            self.state["conversation_history"].append({

                "role": "system",

                "content": system_response,

                "timestamp": datetime.now().isoformat()

            })

        # 更新意图和状态

        if intent_data:

            self.state["current_intent"] = intent_data

            self._transition_state_by_intent(intent_data)

        # 更新槽位和实体

        if entities:

            # [实体处理代码省略]

            pass

        return self.state

    # [其他方法省略]

从运营视角看，数据需要变成语言。ServiceAnalyticsReportGenerator 封装了周报自动生成的全流程。初始化时加载 deepseek-ai/deepseek-7b 的分词器和模型，自动映射到可用设备并以半精度运行。生成周报时先把结构化指标拼成提示词，再用温度 0.7、top_p 0.9、采样开启的方式生成约 2048 token 的内容，解码后剥离提示词部分，整理成 Markdown，最后返回 Markdown 文本、纯文本和带时间戳的元信息，方便直接分发。

[PRE1]python

# 智能客服数据分析报告生成器

class ServiceAnalyticsReportGenerator:

    def __init__(self, model_name="deepseek-ai/deepseek-7b"):

        self.tokenizer = AutoTokenizer.from_pretrained(model_name)

        self.model = AutoModelForCausalLM.from_pretrained(

            model_name,

            device_map="auto",

            torch_dtype=torch.float16

        )

    async def generate_weekly_report(self, analytics_data):

        """

        生成周度分析报告

        Args:

            analytics_data: 包含各项指标和数据的字典

        """

        # 构建报告提示词

        prompt = self._build_report_prompt(analytics_data)

        # 调用 DeepSeek 模型生成报告

        inputs = self.tokenizer(prompt, return_tensors="pt").to(self.model.device)

        outputs = self.model.generate(

            **inputs,

            max_new_tokens=2048,

            temperature=0.7,

            do_sample=True,

            top_p=0.9

        )

        report = self.tokenizer.decode(outputs[0], skip_special_tokens=True)

        # 提取报告部分(去除提示词)

        report_only = self._extract_report_content(report, prompt)

        # 格式化为 Markdown

        markdown_report = self._format_to_markdown(report_only)

        return {

            "report_markdown": markdown_report,

            "report_text": report_only,

            "report_timestamp": datetime.now().isoformat()