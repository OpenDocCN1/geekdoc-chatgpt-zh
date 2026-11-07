这套框架的设计主线很清晰：先把单个智能体的“思考—行动”做成可循环复用的闭环，再用流和工厂完成多智能体的组织，最后由规划流把任务真正跑起来。

底层代理把运行状态初始化为 IDLE，默认的最大步数是10，当前步计数从0开始。think 和 act 两个方法被抽象出来，前者负责在既有状态下判断下一步是否需要动作，后者负责把决定好的动作执行掉。step 方法把两者串联，先 await think，返回 False 时直接输出提示，否则进入 act。

    state: AgentState = AgentState.IDLE

    max_steps: int = 10

    current_step: int = 0

    @abstractmethod

    async def think(self) -> bool:

        # 处理当前状态并决定下一步行动

    @abstractmethod

    async def act(self) -> str:

        # 执行决定的行动

    async def step(self) -> str:

        # 执行单个步骤：思考和行动

        should_act = await self.think()

        if not should_act:

            return "思考完成 - 不需要行动"

        return await self.act()

在浏览器代理之上，Manus 增加了一层上下文感知能力。名称、描述、系统提示都是固定的，可观察长度提升到10000，步数上限调到20，并内置了代码执行、浏览器操作、文本替换、终止等通用工具。think 被重写，先备份当下的 next_step_prompt，随后只检查记忆里最近3条记录是否提到 browser_use，若命中就临时把提示换成 BROWSER_NEXT_STEP_PROMPT，调用完父类 think 后再把提示恢复。

[PRE0]python

class Manus(BrowserAgent):

    name: str = "Manus"

    description: str = ("一个多功能的代理，能够使用多种工具解决各种任务")

    system_prompt: str = SYSTEM_PROMPT.format(

        directory=config.workspace_root)

    next_step_prompt: str = NEXT_STEP_PROMPT

    max_observe: int = 10000

    max_steps: int = 20

    # 向工具集合中添加通用工具

    available_tools: ToolCollection = Field(

        default_factory=lambda: ToolCollection(

            PythonExecute(), BrowserUseTool(), StrReplaceEditor(),

            Terminate()))

    async def think(self) -> bool:

        # 处理当前状态并根据适当的上下文决定下一步行动

        # 存储原始提示

        original_prompt = self.next_step_prompt

        # 只检查最近的消息（最后 3 条）以查看是否涉及浏览器活动

        recent_messages = (self.memory.messages[-3:]

                           if self.memory.messages else [])

        browser_in_use = any(

            "browser_use" in msg.content.lower()

            for msg in recent_messages

            if hasattr(msg, "content") and isinstance(msg.content, str))

        if browser_in_use:

            # 临时覆盖为浏览器特定的提示，以获取浏览器上下文

            self.next_step_prompt = BROWSER_NEXT_STEP_PROMPT

        # 调用父类的 think 方法

        result = await super().think()

        # 恢复原始提示

        self.next_step_prompt = original_prompt

        return result

多智能体协作的容器是 BaseFlow。它继承自 BaseModel 与 ABC，支持任意类型字段。agents 以字典形式保存，同时支持可选的 tools 和 primary_agent_key。构造时接受单例、列表或字典三种输入，列表会自动按 agent_0、agent_1 命名；若未显式指定主代理，则默认取字典首个。可通过 primary_agent 属性获取主代理，用 get_agent 按键取值，用 add_agent 追加新成员。

[PRE1]python

class BaseFlow(BaseModel, ABC):

    # 执行流的基类，支持多个代理的执行

    agents: Dict[str, BaseAgent]

    tools: Optional[List] = None

    primary_agent_key: Optional[str] = None

    class Config:

        arbitrary_types_allowed = True

    def __init__(

        self,

        agents: Union[BaseAgent, List[BaseAgent], Dict[str, BaseAgent]],

        **data):

        # 处理不同的代理提供方式

        if isinstance(agents, BaseAgent):

            agents_dict = {"default": agents}

        elif isinstance(agents, list):

            agents_dict = {

                f"agent_{i}": agent for i, agent in enumerate(agents)}

        else:

            agents_dict = agents

        # 如果未指定主代理，则使用第一个代理

        primary_key = data.get("primary_agent_key")

        if not primary_key and agents_dict:

            primary_key = next(iter(agents_dict))

            data["primary_agent_key"] = primary_key

        # 设置代理字典

        data["agents"] = agents_dict

        # 使用 BaseModel 的初始化方法

        super().__init__(**data)

    @property

    def primary_agent(self) -> Optional[BaseAgent]:

        # 获取流的主代理

        return self.agents.get(self.primary_agent_key)

    def get_agent(self, key: str) -> Optional[BaseAgent]:

        # 通过键获取指定的代理

        return self.agents.get(key)

    def add_agent(self, key: str, agent: BaseAgent) -> None:

        # 向流中添加新代理

        self.agents[key] = agent

流的实例化统一交给 FlowFactory。目前只挂载了 PLANNING 一种类型，调用 create_flow 时传入 flow_type、agents 及其他参数即可得到对应流；若类型不被识别则抛出 ValueError。

[PRE2]python

class FlowType(str, Enum):

    PLANNING = "planning"

class FlowFactory:

    # 用于创建不同类型执行流的工厂，支持多个代理

    @staticmethod

    def create_flow(

        flow_type: FlowType,

        agents: Union[BaseAgent, List[BaseAgent], Dict[str, BaseAgent]],

        **kwargs, ) -> BaseFlow:

        flows = {FlowType.PLANNING: PlanningFlow, }

        flow_class = flows.get(flow_type)

        if not flow_class:

            raise ValueError(f"未知的流类型：{flow_type}")

        return flow_class(agents, **kwargs)

为了让计划进度一目了然，PlanStepStatus 用枚举定义了未开始、进行中、已完成、被阻塞四种阶段。提供类方法列出全部状态、筛选活跃状态，以及把状态映射为 [ ]、[→]、[✓]、[!] 的符号，方便在界面上快速标记。

[PRE3]python

class PlanStepStatus(str, Enum):

    # 定义计划步骤可能的状态

    NOT_STARTED = "not_started"

    IN_PROGRESS = "in_progress"

    COMPLETED = "completed"

    BLOCKED = "blocked"

    @classmethod

    def get_all_statuses(cls) -> list[str]:

        # 返回所有可能的步骤状态值

        return [status.value for status in cls]

    @classmethod

    def get_active_statuses(cls) -> list[str]:

        # 返回表示活动状态（未开始或进行中的状态）值的列表

        return [cls.NOT_STARTED.value, cls.IN_PROGRESS.value]

    @classmethod

    def get_status_marks(cls) -> Dict[str, str]:

        # 返回状态到标记符号的映射

        return {

            cls.COMPLETED.value: "[✓]",

            cls.IN_PROGRESS.value: "[→]",

            cls.BLOCKED.value: "[!]",

            cls.NOT_STARTED.value: "[ ]",

        }

PlanningFlow 是可运行的规划执行器，继承 BaseFlow 并内置 LLM 与 PlanningTool。active_plan_id 默认按当前时间戳生成，current_step_index 用来记录执行位置。初始化阶段会把 executors 转为 executor_keys，把 plan_id 转为 active_plan_id，若 planning_tool 未提供则自建；executor_keys 为空时默认使用全部代理。get_executor 优先按 step_type 匹配代理键，匹配不到则按 executor_keys 顺序选取，最终回退到主代理。execute 先校验主代理是否存在，有输入文本时创建初始计划，检查计划是否落到工具中，随后在循环里取当前步骤信息并派给合适的执行者。

[PRE4]python

class PlanningFlow(BaseFlow):

    # 一个流，用于管理通过代理执行任务的规划和执行

    llm: LLM = Field(default_factory=lambda: LLM())

    planning_tool: PlanningTool = Field(default_factory=PlanningTool)

    executor_keys: List[str] = Field(default_factory=list)

    active_plan_id: str = Field(

        default_factory=lambda: f"plan_{int(time.time())}"

    )

    current_step_index: Optional[int] = None

    def __init__(

        self,

        agents: Union[BaseAgent, List[BaseAgent], Dict[str, BaseAgent]],

        **data):

        # 在调用 super().__init__ 之前设置执行者键

        if "executors" in data:

            data["executor_keys"] = data.pop("executors")

        # 如果提供了plan_id，则设置

        if "plan_id" in data:

            data["active_plan_id"] = data.pop("plan_id")

        # 如果没有提供planning_tool，则初始化它

        if "planning_tool" not in data:

            planning_tool = PlanningTool()

            data["planning_tool"] = planning_tool

        # 使用处理后的数据调用父类的初始化方法

        super().__init__(agents, **data)

        # 如果没有指定executor_keys，则将所有代理的键作为执行者

        if not self.executor_keys:

            self.executor_keys = list(self.agents.keys())

    def get_executor(self, step_type: Optional[str] = None) -> BaseAgent:

        """

        获取适合当前步骤的执行者代理

        可以扩展为根据步骤类型 / 要求选择代理

        """

        # 如果提供了步骤类型，并且与代理的键匹配，则使用该代理

        if step_type and step_type in self.agents:

            return self.agents[step_type]

        # 否则使用第一个可用的执行者或回退到主代理

        for key in self.executor_keys:

            if key in self.agents:

                return self.agents[key]

        # 回退到主代理

        return self.primary_agent

    async def execute(self, input_text: str) -> str:

        # 通过代理执行规划流程

        try:

            if not self.primary_agent:

                raise ValueError("没有可用的主代理")

            # 如果提供了输入文本，创建初始计划

            if input_text:

                await self._create_initial_plan(input_text)

            # 验证计划是否成功创建

            if self.active_plan_id not in self.planning_tool.plans:

                logger.error(f"计划创建失败。计划 ID {self.active_plan_id} 在规划工具中未找到。")

                return f"创建计划失败：{input_text}"

            result = ""

            while True:

                # 获取当前步骤以执行

                self.current_step_index,

                step_info = await self._get_current_step_info()

                # 如果没有更多步骤或计划完成，则退出

                if self.current_step_index is None:

                    result += await self._finalize_plan()

                    break

                # 使用适当的代理执行当前步骤