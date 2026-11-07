【实战拆解】MCP 驱动的无人机农场全流程与智能体消息中台

用 MCP 把无人机的巡检做成状态机，整个作业从创建到归档都变得可追溯、可校验。下面这套最小可运行的演示，展示了状态守卫、日志追加、无人机状态重置以及客户端如何按序调用工具完成一次完整的 Farm Sector A 巡检。

报告生成环节设置了严格的前置检查，只有任务当前处于 REPORTING 才会放行，否则直接返回错误提示当前状态与期望不符。进入允许区间后会模拟一次耗时约一秒的生成过程，随后把任务状态改写为 DONE，写一条带时间戳的日志记录报告完成并注明所属无人机，最后把该无人机状态更新为 idle 并释放当前任务，返回任务 ID 与最终状态。

查询侧提供了两个只读工具。查看任务会先判断任务是否在本地字典中存在，不存在则返回 task_not_found，找到后直接回传完整记录；查看无人机同理，先校验 drone_id 是否在状态表里，不在返回 drone_not_found，否则回传当前状态快照。

服务端启动后通过 stdio 暴露工具，客户端延迟五秒连接，随后按顺序触发一次完整的链路：先创建巡检任务锁定 Farm Sector A 与 drone_001，随后依次推进准备、起飞、扫描、数据处理、生成报告，最后分别拉取任务详情与无人机状态确认收尾。整条链路的日志与状态变化都完整保留，能够清晰看到任务 ID SCAN-7493 如何从 PREPARATION 逐步演进至 DONE，无人机最终回到 idle 且不再挂起任何任务。

[PRE0]
=== MCP 服务端 (drone-farm-server) 启动 ... ===
=== 客户端等待 5 秒后再连接 ... ===
[Client] 初始化完成，开始演示状态驱动任务流 ...
[Client] 创建任务：{'task_id': 'SCAN-7493', 'status': 'PREPARATION', 'drone': 'drone_001'}
[Client] 任务准备：{'task_id': 'SCAN-7493', 'new_status': 'TAKEOFF'}
[Client] 起飞：{'task_id': 'SCAN-7493', 'new_status': 'SCANNING'}
[Client] 扫描：{'task_id': 'SCAN-7493', 'new_status': 'PROCESSING'}
[Client] 数据处理：{'task_id': 'SCAN-7493', 'new_status': 'REPORTING'}
[Client] 生成报告：{'task_id': 'SCAN-7493', 'status': 'DONE'}
[Client] 任务详情：{'task_id': 'SCAN-7493', 'status': 'DONE', 'field_location': 'Farm Sector A', 'log': ['2024-08-12 10:35:17 - Task created for field=Farm Sector A, assigned to drone=drone_001', '2024-08-12 10:35:18 - Preparation done, next=TAKEOFF, drone=drone_001', '2024-08-12 10:35:19 - Drone took off, next=SCANNING, drone=drone_001', '2024-08-12 10:35:21 - Scanning done, data collected, next=PROCESSING, drone=drone_001', '2024-08-12 10:35:23 - Data processing done, next=REPORTING, drone=drone_001', '2024-08-12 10:35:24 - Report generated, status=DONE, drone=drone_001']}
[Client] 无人机状态：{'drone_id': 'drone_001', 'status': 'idle', 'current_task': None}

同套思路也可以迁移到智能体间通信。另一份示例实现了 agent-message-server，核心是用两张全局表维护注册信息与消息收件箱。注册时校验 ID 是否重复，重复则返回错误；成功后写入 ID、描述与注册时间戳，同时为该智能体初始化空收件箱。列出智能体的工具直接返回已注册列表。收件箱的初始化与追加操作被封装成内部函数，保证每次发信前收件箱一定存在。tool_send_message 的入口签名为 sender_id、receiver_id、content，用于在收件箱中落盘消息。

[PRE1]python
import os
import time
import json
import random
import asyncio
from typing import Dict, List, Any
import mcp
from mcp.server import FastMCP
from mcp.client.stdio import stdio_client
from mcp import ClientSession, StdioServerParameters
################ 全局数据结构 ################
###### 记录已注册的智能体
AGENT_DB: Dict[str, Dict[str, Any]] = {}
###### 存储智能体之间的消息
###### 格式：MESSAGES_DB[agent_id] = list of message dict
MESSAGES_DB: Dict[str, List[Dict[str, Any]]] = {}
app = FastMCP("agent-message-server")
def _init_agent_inbox(agent_id: str):
    """
    若对应智能体收件箱未初始化，则创建空列表
    """
    if agent_id not in MESSAGES_DB:
        MESSAGES_DB[agent_id] = []
def _append_message(receiver_id: str, msg: Dict[str, Any]):
    """
    向 receiver_id 的收件箱追加一条消息
    """
    _init_agent_inbox(receiver_id)
    MESSAGES_DB[receiver_id].append(msg)
@app.tool()
def tool_register_agent(agent_id: str, description: str) -> Dict[str, Any]:
    """
    注册智能体，记录其 ID 与描述信息，
    并在 MESSAGES_DB 中初始化收件箱
    """
    if agent_id in AGENT_DB:
        return {"error": "Agent ID already registered"}
    AGENT_DB[agent_id] = {
        "agent_id": agent_id,
        "description": description,
        "registered_at": time.strftime("%Y-%m-%d %H:%M:%S")
    }
    _init_agent_inbox(agent_id)
    return {"message": f"Agent {agent_id} registered successfully"}
@app.tool()
def tool_list_agents() -> Dict[str, Any]:
    """
    查看所有已注册的智能体信息
    """
    return {"agents": list(AGENT_DB.values())}
@app.tool()
def tool_send_message(sender_id: str, receiver_id: str, content: str) -> Dict[str, Any]:
    """