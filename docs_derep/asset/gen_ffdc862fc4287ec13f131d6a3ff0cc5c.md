[PRE0]

Out

助手的信息：

Assistant(id='asst_pF2pMtIHOL4CpXpyUdHkoKG3', created_at=1705829118, description=None, file_ids=[], instructions=' 你是一个很会鼓励人的助手！ ', metadata={}, model='gpt-4-turbo-preview', name=' 鼓励 Agent', object='assistant', tools=[ToolFunction(function=FunctionDefinition(name='get_encouragement', description=' 根据用户的心情提供鼓励信息 ', parameters={'type': 'object', 'properties': {'mood': {'type': 'string', 'description': ' 用户当前的心情，例如：开心，难过，压力大，疲倦 '}, 'name': {'type': 'string', 'description': ' 用户的名字，用来个性化鼓励信息 '}}, 'required': ['mood']}), type='function')])

Thread 的信息：

Thread(id='thread_TnsAI7DToiGrxfa62aRmsRtu', created_at=1705852335, metadata={}, object='thread', tool_resources=[])

Message 的信息：

ThreadMessage(id='msg_iyk4UR0xhEP6c9BV3LCJM8EA', assistant_id=None, content=[MessageContentText(text=Text(annotations=[], value=' 你好，花语秘境是什么公司？ '), type='text')], created_at=1705852335, file_ids=[], metadata={}, object='thread.message', role='user', run_id=None, thread_id='thread_xfEdaRucaeXBKIyMMAXDzT1r')

## Run 的初始信息：

[PRE1]

## Run 的第 1 次轮询信息：

[PRE2]

## Run 的第 2 次轮询信息：

[PRE3]

小雪惊讶道，咖哥这是什么情况，轮询怎么跑了一百多次。

咖哥回，确实卡住了。程序一直在死循环，Run 始终停在 requires_action，也就是等待函数调用的阶段，最后只能按 Ctrl+C 强制中断。

这次打印出来的关键数据见表 5.4。

## 表 5.4 助手、Thread、Message 以及 Run 的状态信息（2）

| 类型 | ID | 状态 | 创建时间 | 完成时间 | 用户消息 | 系统响应 | Token使用 |

|------|-----|------|----------|----------|----------|----------|-----------|

| 助手 | asst_pF2pMtIHOL4CpXpyUdHkoKG3 | — | 1705829118 | — | — | — | — |

| Thread | thread_TnsAI7DToiGrxfa62aRmsRtu | — | 1705852335 | — | — | — | — |

| Thread Message | msg_iyk4UR0xhEP6c9BV3LCJM8EA | — | 1705852335 | — | 你好，请你安慰一下伤心的小雪吧！ | — | — |

| Run | run_FliM9ltqgE5rwuQLfm0B07KC | queued in_progress requires_action | 1705852336 | — | 你好，请你安慰一下伤心的小雪吧！ | — | — |

| ... | ... | ... | ... | ... | ... | ... | ... |

咖哥问小雪，这次表现为什么和之前不一样，助手没能迅速返回并把 Run 推到 completed，反而像掉进无尽循环里。

小雪也注意到 Run 一直徘徊在 requires_action，没必要吊胃口，直接说原因。

咖哥解释，因为这次提问涉及情绪安抚。助手被设定为鼓励型，并且已经挂载了工具，它判断应该调用 get_encouragement 来完成，而不是直接生成一段普通回复。

小雪一下子站起来，啊，我懂了。可代码里并没有后续把 get_encouragement 的调用结果提交回去的逻辑，所以 Run 永远卡在 requires_action，情感支持的内容也就出不来，见图 5.12。

因此轮询的退出条件必须调整，不能只等 completed，遇到 requires_action 也要主动退出。

[PRE4]

图5.12 Run在这个示例中的流程：无休止等待

### 5.3.3 让 requires_action 也能中断轮询

下面对轮询逻辑进行重构，只要 Run 进入 requires_action 就退出循环。重构后会重新创建 Thread 和 Run，所以后续的 ID 都会发生变化。

[PRE5]

执行完改动后的代码，Run 先从 queue 变为 in_progress，再变成 requires_action，随后循环被退出。再次查询时，Run 依旧保持在 requires_action。这里不再贴出与表 5.4 形式相同的完整输出。

小雪连说知道了咖哥。这时候助手显然在等 Function Calling 的返回，只是我们改动的是退出时机，所以虽然 Thread 和 Run 都换了，状态演进的路径还是原来的样子。

### 5.3.4 读取函数调用的元数据

接下来进入核心操作。此时要把助手准备调用时返回的 function_name、arguments、function_id 等关键字段取出来，代码示例如下。

[PRE6]

[PRE7]

小雪点评，function_name、arguments、function_id 这些字段正是本地执行函数所必需的输入。