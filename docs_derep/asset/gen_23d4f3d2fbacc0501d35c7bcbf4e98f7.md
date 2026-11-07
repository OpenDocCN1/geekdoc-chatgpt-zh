# 多智能体消息通信实战演示

在 MCP 环境下，实现智能体之间的点对点消息传递是构建协作式 Agent 的基础。下面通过一套最小可用示例，说明消息如何被创建、投递、查询以及完整交互流程。

**消息投递的核心实现**

发送接口首先校验发送方和接收方是否已在 AGENT_DB 中完成注册，未注册则直接返回错误。校验通过后，系统用随机数生成 msg_id，格式为 msg-四位数字，并用 time.strftime 产出当前时间戳。随后组装包含 msg_id、from、to、content、timestamp 的消息对象，调用内部的 _append_message 方法写进接收方的收件箱，最后返回发送成功提示和消息编号。

**收件箱读取工具**

tool_fetch_inbox 用于列出指定智能体的全部待处理消息。入口会先判断 agent_id 是否存在，不存在返回错误信息。存在时从 MESSAGES_DB 中取出对应列表，统一封装为 agent_inbox 字段返回。

**单条消息详情工具**

tool_fetch_message_detail 在收件箱范围内精准定位某条记录。同样先做身份校验，拿到该 Agent 的 inbox 后遍历比对 msg_id，匹配成功则返回 message_detail，找不到则返回 message_not_found 错误。

**服务端与客户端联动演示**

服务端以 stdio 方式启动，并在控制台输出启动提示。客户端等待约 5 秒后建立连接，完成初始化后依次演示以下步骤：

先注册两个智能体 alpha，描述为 Primary Decision Maker；bravo，描述为 Secondary Executor Agent。随后调用列表接口确认两者已生效。

接着 alpha 向 bravo 发起问询，内容为 Hello Bravo, please confirm readiness，系统返回 Message sent 与对应的 msg_id。bravo 回复 Hi Alpha, I'm ready for instructions，同样得到发送成功反馈。

随后分别拉取 bravo 与 alpha 的收件箱，可看到消息已按投递关系落库。再对 alpha 的第一条收件进行详情查询，可拿到完整的消息元数据。

下面是完整运行日志。

[PRE0]
=== MCP 服务端 (agent-message-server) 启动 ... ===
=== 客户端等待 5 秒后连接 ... ===
[Client] 初始化完成，开始Agent-to-Agent 消息协议演示
[Client] 注册 alpha 结果：{'message': 'Agent alpha registered successfully'}
[Client] 注册 bravo 结果：{'message': 'Agent bravo registered successfully'}
[Client] 查看所有Agent：{'agents': [{'agent_id': 'alpha', 'description': 'Primary Decision Maker', 'registered_at': '2024-09-05 10:20:31'}, {'agent_id': 'bravo', 'description': 'Secondary Executor Agent', 'registered_at': '2024-09-05 10:20:31'}]}
[Client] alpha->bravo 消息：{'message': 'Message sent', 'msg_id': 'msg-8192'}
[Client] bravo->alpha 消息：{'message': 'Message sent', 'msg_id': 'msg-9061'}
[Client] bravo 收件箱：{'agent_inbox': [{'msg_id': 'msg-8192', 'from': 'alpha', 'to': 'bravo', 'content': 'Hello Bravo, please confirm readiness.', 'timestamp': '2024-09-05 10:20:32'}]}
[Client] alpha 收件箱：{'agent_inbox': [{'msg_id': 'msg-9061', 'from': 'bravo', 'to': 'alpha', 'content': 'Hi Alpha, I'm ready for instructions.', 'timestamp': '2024-09-05 10:20:32'}]}
[Client] alpha 第一条消息详情：{'message_detail': {'msg_id': 'msg-9061', 'from': 'bravo', 'to': 'alpha', 'content': 'Hi Alpha, I'm ready for instructions.', 'timestamp': '2024-09-05 10:20:32'}}

整体流程验证了注册校验、消息生成与投递、收件箱查询与详情读取的闭环可用性，为后续更复杂的 Agent-to-Agent 协作奠定通信基础。

[PRE1]python
import os
import time
import json
import random
import asyncio
from typing import Dict, Any
import mcp
from mcp.server import FastMCP
from mcp.client.stdio import stdio_client
from mcp import ClientSession, StdioServerParameters
###### ------------------- 全局存储结构 -------------------
###### order context slot, key=order_id, value=slot data
ORDER_SLOT_DB: Dict[str, Dict[str, Any]] = {}
###### 智能体注册信息
AGENT_DB: Dict[str, Dict[str, Any]] = {}
app = FastMCP("ecommerce-agent-server")
def ensure_slot(order_id: str):
    """
    若订单 Slot 尚未创建，初始化之
    """
    if order_id not in ORDER_SLOT_DB:
        ORDER_SLOT_DB[order_id] = {
            "order_id": order_id,
            "items": [],        # 记录产品和数量
            "total_price": 0,
            "payment_status": "unpaid",
            "inventory_status": "not_reserved",
            "history": []
        }