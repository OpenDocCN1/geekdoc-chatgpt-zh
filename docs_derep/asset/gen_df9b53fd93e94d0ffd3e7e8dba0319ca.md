函数调用实战：从天气查询到 Open WebUI 本地部署

以查询北京当日天气为例，演示模型如何先发起工具调用、拿到外部数据后再生成人话回复，并附上 Open WebUI 的本地环境搭建步骤。一次交互完整呈现：用户提问 → 模型决定调用 get_weather → 工具返回原始气象 JSON → 模型基于结果总结并给出穿衣建议。

本次返回的原始气象样本包含站点坐标、天气主状态、温度体感、湿度气压、能见度风场等全量字段，可直接用于后续解析。

{"coord": {"lon": 116.3972, "lat": 39.9075}, "weather": [{"id": 804, "main": "Clouds", "description": "\u9634\uff0c\u591a\u4e91", "icon": "04d"}], "base": "stations", "main": {"temp": 4.94, "feels_like": 1.77, "temp_min": 4.94, "temp_max": 4.94, "pressure": 1020, "humidity": 25, "sea_level": 1020, "grnd_level": 1014}, "visibility": 10000, "wind": {"speed": 4.03, "deg": 300, "gust": 9.43}, "clouds": {"all": 85}, "dt": 1736239986, "sys": {"type": 1, "id": 9609, "country": "CN", "sunrise": 1736206561, "sunset": 1736240684}, "timezone": 28800, "id": 1816670, "name": "Beijing", "cod": 200}

[PRE0]python

# 追加 function 返回消息

messages.append({

    "role": "tool",

    "content": function_response,

    "tool_call_id": response_message.tool_calls[0].id

})

messages

[PRE1]python

[{'role': 'user', 'content': '请帮我查询北京地区今日天气情况'},

 {'content': '',

  'refusal': None,

  'role': 'assistant',

  'audio': None,

  'function_call': None,

  'tool_calls': [{'id': 'call_0_87e140df-66ef-44dd-813f-45800e169690',

    'function': {'arguments': '{"loc":"Beijing"}', 'name': 'get_weather'},

    'type': 'function',

    'index': 0}]},

 {'role': 'tool',

  'content': '{"coord": {"lon": 116.3972, "lat": 39.9075}, "weather": [{"id": 804, "main": "Clouds", "description": "\u9634\uff0c\u591a\u4e91", "icon": "04d"}], "base": "stations", "main": {"temp": 4.94, "feels_like": 1.77, "temp_min": 4.94, "temp_max": 4.94, "pressure": 1020, "humidity": 25, "sea_level": 1020, "grnd_level": 1014}, "visibility": 10000, "wind": {"speed": 4.03, "deg": 300, "gust": 9.43}, "clouds": {"all": 85}, "dt": 1736239986, "sys": {"type": 1, "id": 9609, "country": "CN", "sunrise": 1736206561, "sunset": 1736240684}, "timezone": 28800, "id": 1816670, "name": "Beijing", "cod": 200}',

  'tool_call_id': 'call_0_87e140df-66ef-44dd-813f-45800e169690'}]

[PRE2]python

second_response = client.chat.completions.create(

    model="deepseek-chat",

    messages=messages)

second_response.choices[0].message

[PRE3]

ChatCompletionMessage(content='北京地区今日天气情况如下：\n\n- **天气状况**：阴，多云\n- **当前温度**：4.94°C\n- **体感温度**：1.77°C\n- **湿度**：25%\n- **风速**：4.03 米/秒\n- **风向**：300 度（西北风）\n- **能见度**：10000 米\n- **气压**：1020 hPa\n\n请注意保暖，适当增添衣物！', refusal=None, role='assistant', audio=None, function_call=None, tool_calls=None)

[PRE4]python

def run_conv(messages,

             api_key,

             tools=None,

             functions_list=None,

             model="deepseek-chat"):

    """

    能够自动执行外部函数调用的 Chat 对话模型

    :param messages: 必要参数，输入到 Chat 模型的 messages 参数对象

    :param api_key: 必要参数，调用模型的 API-KEY

    :param tools: 可选参数，默认为 None，可以设置为包含全部外部函数的列表对象

    :param model: Chat 模型，可选参数，默认模型为 deepseek-chat

    :return: Chat 模型输出结果

    """

    user_messages = messages

    client = OpenAI(api_key=api_key,

                    base_url="https://api.deepseek.com")

    # 如果没有外部函数库，则执行普通的对话任务

    if tools == None:

        response = client.chat.completions.create(

            model=model,

            messages=user_messages

        )

        final_response = response.choices[0].message.content

    # 若存在外部函数库，则需要灵活选取外部函数并进行回答

    else:

        # 创建外部函数库字典

        available_functions = {func.__name__: func for func in functions_list}

        # 创建包含用户问题的 messages

        messages = user_messages

        # first response

        response = client.chat.completions.create(

            model=model,

            messages=user_messages,

            tools=tools,

        )

        response_message = response.choices[0].message

        # 获取函数名

        function_name = response_message.tool_calls[0].function.name

        # 获取函数对象

        fuction_to_call = available_functions[function_name]

        # 获取函数参数

        function_args = json.loads(response_message.tool_calls[0].function.arguments)

        # 将函数参数输入到函数中，获取函数计算结果

        function_response = fuction_to_call(**function_args)

        # messages 中拼接 first response 消息

        user_messages.append(response_message.model_dump())

        # messages 中拼接外部函数输出结果

        user_messages.append(

            {

                "role": "tool",

                "content": function_response,

                "tool_call_id": response_message.tool_calls[0].id

            }

        )

        # 第二次调用模型

        second_response = client.chat.completions.create(

            model=model,

            messages=user_messages)

        # 获取最终结果

        final_response = second_response.choices[0].message.content

    return final_response

[PRE5]python

messages = [{"role": "user", "content": "请问北京今天天气如何？"}]

run_conv(messages=messages,

         api_key = ds_api_key,

         tools=tools,

         functions_list=[get_weather])

[PRE6]

北京今天的天气是多云，气温约为 4.94° C，体感温度约为 1.77° C，湿度为 25%，风速为 4.03 米/秒，风向为 300 度。空气质量良好，能见度为 10000 米。

[PRE7]bash

# 创建一个名为 open-webui 的 Conda 虚拟环境，并指定 Python 版本为 3.11

conda create -n open-webui python=3.11

# 初始化 Conda

conda init

# 重新加载 shell 配置

source ~/.bashrc

# 激活刚刚创建的 open-webui 环境，进入该环境

conda activate open-webui

[PRE8]bash

pip install open-webui -i https://pypi.tuna.tsinghua.edu.cn/simple

[PRE9]bash

open-webui serve