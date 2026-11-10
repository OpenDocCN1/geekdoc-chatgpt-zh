[PRE0]

### 9.2.2 OpenClaw 真正驱动 STM32 执行

9.2.1 已经把 OpenHarmony 的文本送到了 OpenClaw。这里要解决的下一步更关键：AI 说要做什么，STM32 到底有没有按要求去做。

整体流程拆解成下面几步：

[PRE1]

#### 1. 入口：把大脑和手脚接起来
要搞清网关如何把 AI 能力与串口能力连通，先看 main() 在初始化时做了哪些事。这一步决定系统有没有“脑”和“手”。

[PRE2]

#### 2. 规划阶段：从意图到结构化计划
代码里负责这件事的是 OpenClawBridge.parse_text_to_plan()。它会整理提示词、拉起 OpenClaw CLI，再把模型返回的内容解析成 plan。重点有三点：

- ( 1 ) 网关不无条件信任模型，必须让模型输出固定格式的 JSON。
- ( 2 ) 支持两种执行模式：按顺序走 commands，或按组并行执行 parallel。
- ( 3 ) 此时仅完成规划，串口命令还没有真正发出。

[PRE3]

#### 3. 安全闸门：白名单二次校验
即便模型已经吐出 JSON，也必须经过一次 _normalize_commands() 的白名单检查。这一层保证从 AI 到 STM32 的指令链路是干净的，不在清单里的命令一律禁止下发。

[PRE4]

#### 4. 执行阶段：本地处理与串口下发分离
真正的动作发生在 GatewayHandler._execute_command_sequence() 内部。其中 WAIT/wait 和 SERVO SWEEP 由网关本地完成，其余指令才通过串口发给 STM32。