Agent执行轨迹沉淀方案

Agent跑完一次任务，真正值钱的不是日志本身，而是能被复用的经验。下面这套结构就是把原始执行记录提炼成知识资产的最小闭环。

先用 ExecutionTrace 完整记录一次运行：谁执行的 agent_id、任务是什么 task_description、具体走了哪些步骤 steps、最终结果 outcome、质量评分 quality_score 以及时间戳 timestamp。原始轨迹保留上下文，便于溯源。

再用 DistilledKnowledge 做提炼后的载体：唯一知识ID knowledge_id、任务概括 summary、关键步骤 key_steps、可适用场景 applicable_scenarios、成功模式 success_patterns，额外带上价值分 value_score 和访问级别 access_level，区分 shared 共享还是 private 私有。

负责过滤和转化的就是 KnowledgeDistiller。它内置两个门槛，VALUE_THRESHOLD_HIGH 0.8 和 VALUE_THRESHOLD_MID 0.5，并依赖一个 vector_store 做存储与检索。distill 方法先做质量守门，trace.quality_score 低于 0.5 的直接丢弃，不浪费算力；通过门槛后才进入摘要生成等后续提炼流程。

整体思路很直接：记录 -> 评估 -> 过滤 -> 沉淀，只有高质量的执行才值得变成组织的共同知识。

[PRE0]python

from dataclasses import dataclass

from typing import List, Optional, Dict

from datetime import datetime

import hashlib

@dataclass

class ExecutionTrace:

    agent_id: str

    task_description: str

    steps: List[Dict]

    outcome: str

    quality_score: float

    timestamp: datetime

@dataclass

class DistilledKnowledge:

    knowledge_id: str

    summary: str

    key_steps: List[str]

    applicable_scenarios: List[str]

    success_patterns: List[str]

    value_score: float

    access_level: str  # "shared" or "private"

class KnowledgeDistiller:

    VALUE_THRESHOLD_HIGH = 0.8

    VALUE_THRESHOLD_MID = 0.5

    def __init__(self, vector_store):

        self.vector_store = vector_store

    def distill(self, trace: ExecutionTrace) -> Optional[DistilledKnowledge]:

        if trace.quality_score < self.VALUE_THRESHOLD_MID:

            return None  # 低价值，丢弃

        summary = self._generate_summary(trace)