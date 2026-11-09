[PRE0]python

# 单轮模型交互

def __step(self,

           question: str,

           short_term_memory: List[str],

           long_term_memory: ChatMessageHistory,

           ) -> Tuple[List[Action], str]:

    """发起一轮与大模型对话"""

    inputs = {

        "input": question,

        "agent_scratchpad": "\n".join(str(short_term_memory)),

        "long_term_memory": long_term_memory.messages,

    }

    chain = self.prompt | self.llm | StrOutputParser()

    response = ""

    for s in chain.stream(inputs):

        response += s

        if self.verbose:

            print_green(s, end="")

    # 把模型回复写入短时记忆

    short_term_memory.append(response)

    # 提取回复中的行动指令

    actions = self.__extract_actions(response)

    return actions, response

[PRE1]python

# 动作指令解析

def __extract_actions(self, text: str) -> List[Action]:

    actions = self.__extract_python_actions(text)

    action = self.__extract_json_action(text)

    if action:

        actions.append(action)

    return actions

[PRE2]python

# 检测 Python 格式调用

def __extract_python_actions(self, text: str) -> List[Action]:

    if text.find("tool_call") >= 0:

        raise ActionFormatException(

            "不要使用 tool_call 的形式发起工具调用。"

        )

    return []

[PRE3]python

# 提取 JSON 格式动作

def __extract_json_action(self, text: str) -> Action:

    last_json = get_last_json(text)

    if last_json:

        try:

            # 转为 JSON 结构

            json_data = parse_nested_json(last_json)

            # 生成 Action 实例

            action = Action(name=json_data['Action'],

                            args=json_data['args'])

            return action

        except json.JSONDecodeError as e:

            raise ActionFormatException(e)

        except TypeError as e:

            raise ActionFormatException(e)

    else:

        return None

[PRE4]python

# 多步推理入口

def invoke(self,

          question: str,

          long_term_memory: ChatMessageHistory,

          verbose=False,

          ) -> str:

    self.verbose = verbose

    # 初始化短时记忆用于记录推演过程

    short_term_memory = []

    # 当前思考轮数

    step_count = 0

    # 最终应答

    reply = ""

    if self.verbose:

        print_yellow(f"\n 当前长时记忆:\n{long_term_memory}\n")

    while step_count < self.max_steps:

        step_count += 1

        if self.verbose:

            print_green(f"\n\n【开始第{step_count}轮思考】\n")

            # print_yellow(f"\n 当前短时记忆:\n{short_term_memory}\n")

        try:

            actions, response = self.__step(

                question=question,

                short_term_memory=short_term_memory,

                long_term_memory=long_term_memory,

            )

        except ActionFormatException as e:

            observation = f"""你应当以合法的 JSON 格式返回工具调用指令。

                参考以下错误信息修正调用指令:{e}"""

            short_term_memory.append(observation)

            if self.verbose:

                print_red("\n 工具调用指令格式错误,要求修正后调用")

            continue

        if actions is None or len(actions) == 0:

            break

        for action in actions:

            # 执行该动作

            observation = self.__exec_action(action)

            short_term_memory.append(

                f"工具[{action.name}]执行结果:{observation}"

            )

            if self.verbose:

                print_cyan(

                    f"\n 工具[{action.name}]执行结果:{observation}\n"

                )

    if step_count >= self.max_steps:

        # 已达最大步数，返回提示

        reply = "抱歉,我未能完成任务,请您重新提交您的问题。"

    else:

        reply = response

    # 刷新长时记忆

    long_term_memory.add_user_message(question)

    long_term_memory.add_ai_message(reply)

    return reply

[PRE5]python

# 执行工具调用

def __exec_action(self, action: Action) -> str:

    if action is None:

        return f"Error: 工具名称不能为空。"

    tool_name = action.name

    tool = None

    for t in self.tools: