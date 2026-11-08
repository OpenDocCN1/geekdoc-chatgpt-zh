任务复杂度拆解：让 Agent 协作不再靠感觉

多 Agent 协作乱不乱，关键看任务先有没有被拆准。凭感觉分工很容易出现重复或掉项，做一个可量化的复杂度评估器，就能把“要做什么、需要谁”变得清晰。

下面的实现用领域词表、复杂度信号和工具需求三条线来给任务打分，先判断难度等级，再据此匹配人手。

[PRE0]python

from enum import Enum

from typing import List, Dict, Set

from dataclasses import dataclass

import re

class ComplexityLevel(Enum):

    SIMPLE = 1

    MODERATE = 2

    COMPLEX = 3

@dataclass

class ComplexityScore:

    level: ComplexityLevel

    domain_count: int

    reasoning_depth: int

    tool_requirements: List[str]

class TaskComplexityAnalyzer:

    DOMAIN_KEYWORDS = {

        "code": ["编程", "代码", "函数", "API"],

        "data": ["数据", "分析", "统计", "图表"],

        "writing": ["撰写", "文档", "报告", "总结"],

        "reasoning": ["推理", "分析", "判断", "决策"]

    }

    COMPLEXITY_MARKERS = ["多步骤", "综合", "复杂", "深度", "全面"]

    def analyze(self, task: str) -> ComplexityScore:

        domains = self._detect_domains(task)

        depth = self._estimate_reasoning_depth(task)

        tools = self._identify_tool_needs(task)

        level = self._compute_level(len(domains), depth, len(tools))

        return ComplexityScore(level, len(domains), depth, tools)

    def _detect_domains(self, task: str) -> Set[str]:

        domains = set()

        for domain, keywords in self.DOMAIN_KEYWORDS.items():

            if any(kw in task for kw in keywords):

                domains.add(domain)

        return domains if domains else {"general"}

    def _estimate_reasoning_depth(self, task: str) -> int:

        depth = 1

        if any(m in task for m in self.COMPLEXITY_MARKERS):

            depth += 1

        if len(task) > 200:

            depth += 1

        return min(depth, 3)

    def _compute_level(self, domains: int, depth: int, tools: int) -> ComplexityLevel:

        score = domains + depth + tools

        if score <= 3: return ComplexityLevel.SIMPLE

        elif score <= 5: return ComplexityLevel.MODERATE

        else: return ComplexityLevel.COMPLEX

核心逻辑很直接：先扫任务文本看命中了几个专业领域，代码、数据、写作、推理各有关键词；再看是否有复杂度提示词以及描述是否冗长来推断推理层级；最后把领域数量、推理深度和工具需求加总，3 分以内为简单，4 到 5 分为中等，超过 5 分就归为复杂。返回的结构体包含等级、领域数、深度和工具清单，方便后续做能力匹配。

下面是一次真实的调度流程，任务同时涉及数据分析、可视化、报告撰写和脚本开发，属于典型的跨域综合需求。

[PRE1]

[协调 Agent] 收到任务：分析销售数据并生成可视化报告，同时编写数据处理脚本。

[协调 Agent → 能力匹配模块] 请求分析任务需求，识别所需能力。

[能力匹配模块] 任务需求分析完成：

- 数据分析能力：高需求（销售数据分析）

- 代码编写能力：高需求（数据处理脚本）

- 文档撰写能力：中需求（生成报告）

- 可视化能力：高需求（可视化报告）

[协调 Agent → Agent 画像库] 查询满足上述能力需求的候选 Agent。

[Agent 画像库] 返回候选列表：

- DataAnalyst Agent：数据分析 0.95，可视化 0.88

- CodeWriter Agent：代码编写 0.92，数据处理 0.85

- ReportGenerator Agent：文档撰写 0.90，可视化 0.75

[协调 Agent] 基于能力互补性评估，确定团队配置：

- DataAnalyst Agent：负责数据分析和可视化

- CodeWriter Agent：负责脚本编写

- ReportGenerator Agent：负责报告整合

[协调 Agent → 各成员] 团队组建完成，分配任务并启动协同执行。

先量化再组队，难度不被低估、能力不被浪费。把复杂度评估做成标准前置环节，Agent 协作的效率和交付质量会明显提升。