这套编排体系的核心思路是把“思考-行动”拆成可复用循环，再用流和工厂把多个代理组织起来，最后用规划流落地执行。

最底层的代理状态先设定为待机，默认允许 10 步，当前步数从 0 开始。思考和行动被设计成抽象接口，思考负责判断是否需要下一步行为，行动负责真正执行。step 把两者串起来：先 await think，若返回 False 直接给出“思考完成 - 不需要行动”，否则进入 act。

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

在通用浏览器代理之上，Manus 做了一层上下文感知。它的名字、描述、系统提示都固定下来，可观察上限提到 10000，最大步数调到 20，并默认挂载 Python 执行、浏览器使用、文件替换和终止这几类通用工具。think 的重写逻辑是：先保存当前 next_step_prompt，只看记忆里最后 3 条消息里是否出现 browser_use 关键字，若出现就临时把提示换成 BROWSER_NEXT_STEP_PROMPT，再调用父类 think，最后把提示恢复原样。

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

多代理协作靠 BaseFlow 承载。它基于 BaseModel 和 ABC，允许任意类型并存。agents 用字典保存，可选 tools、primary_agent_key。初始化时能接受单个代理、代理列表或字典三种写法，列表会被自动命名为 agent_0、agent_1... 若没有指定主代理就取字典里的第一个作为默认。提供 primary_agent 属性、按键取值 get_agent 以及增删 add_agent 的能力。

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

流的创建交由 FlowFactory 统一入口。目前只注册了 PLANNING 类型，传入 flow_type、agents 以及额外参数即可实例化对应的流，不匹配时直接抛 ValueError。

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

为了让计划可视化，PlanStepStatus 用枚举定义了四种状态：未开始、进行中、已完成、被阻塞。类方法提供获取全部状态、活动状态的列表，以及状态到符号的映射，方便在界面上快速标记。

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

PlanningFlow 是具体可执行的规划流，继承 BaseFlow 并内置 LLM 与 PlanningTool。active_plan_id 默认按时间戳生成，current_step_index 记录当前进度。构造时会自动把 executors 重命名为 executor_keys，plan_id 重命名为 active_plan_id，若未提供 planning_tool 则新建。executor_keys 为空时默认把所有代理都作为执行者。get_executor 会优先匹配 step_type 与代理键名，找不到就按 executor_keys 顺序取，回退到主代理。execute 方法先校验主代理是否存在，有输入就创建初始计划，验证计划是否在工具中存在，随后循环取当前步骤信息并交由合适的代理执行。

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