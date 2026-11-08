# 客服智能分流与高效交接实操指南

意图标签与业务含义对照

LOGISTICS 物流追踪类咨询
PRODUCT 产品信息咨询类
COMPLAINT 投诉与纠纷处理类
PROMOTION 促销活动相关类
TECH_SUPPORT 技术使用与故障支持类
AFTER_SALE 售后服务类
VIP 专属会员服务类
FAQ 常见问题解答类
OTHER 其他无法归类类

# 输出范式（JSON）

统一以结构化 JSON 回传，字段如下：

{
    "intent": "意图类别",
    "confidence": 置信度数值,
    "entities": {"entity_type": "entity_value"},
    "need_clarification": true/false,
    "clarification_question": "澄清问题（如需要）"
}

# 决策原则

- 优先判定主要意图，次级意图作为补充参考。
- 当最高置信度低于 0.7 时建议主动澄清，避免误判。
- 注意挖掘隐含诉求，例如表面投诉背后可能存在退款需求。

[PRE0]python

from typing import Dict, List, Optional

import numpy as np

from openai import OpenAI

class SemanticRouter:

    def __init__(self, threshold: float = 0.75):

        self.client = OpenAI()

        self.threshold = threshold

        self.agent_vectors: Dict[str, np.ndarray] = {}

        self.agent_descriptions: Dict[str, str] = {}

    def register_agent(self, agent_id: str, description: str):

        """注册 Agent 并生成能力向量"""

        embedding = self._get_embedding(description)

        self.agent_vectors[agent_id] = embedding

        self.agent_descriptions[agent_id] = description

    def _get_embedding(self, text: str) -> np.ndarray:

        """获取文本的向量表示"""

        response = self.client.embeddings.create(

            model="text-embedding-3-small",

            input=text

        )

        return np.array(response.data[0].embedding)

    def route(self, query: str) -> Dict:

        """执行语义路由决策"""

        query_vector = self._get_embedding(query)

        scores = {}

        for agent_id, agent_vector in self.agent_vectors.items():

            similarity = np.dot(query_vector, agent_vector) / (

                np.linalg.norm(query_vector) * np.linalg.norm(agent_vector)

            )

            scores[agent_id] = similarity

        best_agent = max(scores, key=scores.get)

        best_score = scores[best_agent]

        if best_score >= self.threshold:

            return {"agent": best_agent, "confidence": best_score, "method": "semantic"}

        else:

            return self._llm_route(query, scores)
[PRE1]

【压缩后的交接包】(约 280 Tokens)

{
    "user_id": "U12345",
    "emotion": "焦虑",
    "urgency": "高",
    "problem_summary": "App 闪退无法打开，影响订单处理",
    "device_info": "iPhone 15, App 最新版本",
    "tried_solutions": ["重启手机(三次)", "卸载重装 App"],
    "handoff_reason": "基础方案无效，需技术排查",
    "suggested_direction": "检查账户数据或服务端异常"
}
[PRE2]python

from typing import Dict, List

from pydantic import BaseModel

from openai import OpenAI

class HandoffPackage(BaseModel):

    user_id: str

    emotion: str

    urgency: str

    problem_summary: str

    device_info: str

    tried_solutions: List[str]

    handoff_reason: str

    suggested_direction: str

class ContextCompressor:

    def __init__(self):

        self.client = OpenAI()

    def compress(self, conversation: List[Dict]) -> HandoffPackage:

        """压缩对话历史生成交接包"""

        prompt = f"""分析以下客服对话，提取关键信息生成交接摘要。

对话历史: {conversation}

请以 JSON 格式输出，包含:

- emotion: 用户情感状态

- urgency: 紧急程度(高/中/低)

- problem_summary: 问题一句话摘要

- tried_solutions: 已尝试的解决方案列表

- handoff_reason: 交接原因

- suggested_direction: 建议的处理方向"""

        response = self.client.chat.completions.create(

            model="gpt-4o-mini",

            messages=[{"role": "user", "content": prompt}],

            response_format={"type": "json_object"}

        )

        return HandoffPackage.model_validate_json(

            response.choices[0].message.content

        )