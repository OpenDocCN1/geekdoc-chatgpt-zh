# 用 DeepSeek 搞定填空补全和多风格生成

想让模型既能理解上下文前后又能按指定格式输出？下面这套实现把填空生成 Fill-In-Middle 和 DeepSeek 的前缀续写、结构化输出串起来了。先用一个文本编辑助手识别 `[填空]` 标记，按前后文调用补全，再用几段最小可用示例演示连贯续写、创意写作和格式约束。

核心流程是：检测标记 → 截取前后上下文 → 调用 fill_in_the_middle → 逐个替换，最后进入交互式命令行持续编辑。

            "context_after": context_after

        }

        response = self.create_completion("fill_in_the_middle", parameters)

        if response:

            generated_text = response.get("choices", [{}])[0].get("text", "").strip()

            return generated_text

        else:

            return "无法生成填空内容。"

    def detect_blank_positions(self, text: str) -> List[Dict[str, str]]:

        """

        检测文本中的填空位置，使用特殊标记如 [填空]

        """

        pattern = r"\[填空\]"

        matches = list(re.finditer(pattern, text))

        blanks = []

        last_end = 0

        for match in matches:

            start, end = match.span()

            context_before = text[last_end:start].strip()

            context_after = text[end:].strip()

            blanks.append({

                "context_before": context_before,

                "context_after": context_after

            })

            last_end = end

        return blanks

    def perform_fim(self, text: str) -> str:

        """

        执行填空生成，替换[填空]标记

        """

        blanks = self.detect_blank_positions(text)

        if not blanks:

            print("系统: 文本中没有检测到填空标记 [填空]。")

            return text

        filled_text = text

        for blank in blanks:

            generated = self.fill_in_the_middle(blank["context_before"], blank["context_after"])

            # 只替换第一个 [填空] 标记

            filled_text = filled_text.replace("[填空]", generated, 1)

            print(f"系统: 已填充内容: {generated}")

        return filled_text

    def start_fim(self):

        """

        启动填空生成流程

        """

        print("\n欢迎使用填空生成 (FIM) 文本编辑助手！")

        print("请在需要填空的地方使用[填空]标记。输入'退出'结束程序。")

        while True:

            user_input = input("\n请输入您的文本（使用[填空]标记需要填补的部分）：").strip()

            if user_input.lower() in ["退出", "exit", "quit"]:

                print("系统: 感谢使用，再见！")

                break

            if "[填空]" not in user_input:

                print("系统: 请在需要填空的地方使用[填空]标记。")

                continue

            print("系统: 正在生成填空内容，请稍候...")

            filled_text = self.perform_fim(user_input)

            print(f"\n填空生成后的文本:\n{filled_text}")

    def reset_conversation(self):

        """

        重置对话历史

        """

        self.conversation_history = []

# 主程序入口

if __name__ == "__main__":

    assistant = TextEditingAssistant()

    assistant.start_fim()

下面是直接调用 DeepSeek API 做前缀续写的最小示例，可以根据 temperature、top_p 控制连贯性与创造力。

[PRE0]python

import requests

# DeepSeek API配置

API_ENDPOINT = "https://api.deepseek.com/chat/completions"

HEADERS = {"Authorization": "Bearer YOUR_API_KEY", "Content-Type": "application/json"}

# 前缀续写函数

def generate_completion(prompt, temperature=0.7, top_p=0.9, max_tokens=100):

    payload = {

        "model": "deepseek-chat",

        "prompt": prompt,

        "temperature": temperature,

        "top_p": top_p,

        "max_tokens": max_tokens

    }

    response = requests.post(API_ENDPOINT, headers=HEADERS, json=payload)

    if response.status_code == 200:

        return response.json().get("choices", [{}])[0].get("text", "").strip()

    else:

        return f"Error: {response.status_code}-{response.text}"

# 示例1：前缀续写（连贯性）

prefix1 = "人工智能是一种能够模仿人类智能的技术，它的核心目标是"

response1 = generate_completion(prompt=prefix1, temperature=0.7, top_p=0.9, max_tokens=50)

print("前缀续写结果：")

print(response1)

# 示例2：创意生成（幽默风格）

prefix2 = "如果机器人学会了讲笑话，那么"

response2 = generate_completion(prompt=prefix2, temperature=0.9, top_p=0.8, max_tokens=50)

print("\n创意生成结果（幽默风格）：")

print(response2)

# 示例3：创意生成（诗意风格）

prefix3 = "星空下的未来科技仿佛在诉说"

response3 = generate_completion(prompt=prefix3, temperature=0.8, top_p=0.85, max_tokens=50)

print("\n创意生成结果（诗意风格）：")

print(response3)

如果要固定输出结构，换成带格式约束的调用更稳。JSON、表格、Markdown 都能按提示生成。

[PRE1]python

import requests

import json

# DeepSeek API配置

API_ENDPOINT="https://api.deepseek.com/chat/completions"

HEADERS={"Authorization": "Bearer YOUR_API_KEY", "Content-Type": "application/json"}

# 特殊格式生成函数

def generate_formatted_output(prompt, temperature=0.7, top_p=0.9, max_tokens=100):

    payload={

        "model": "deepseek-chat",

        "prompt": prompt,

        "temperature": temperature,

        "top_p": top_p,

        "max_tokens": max_tokens

    }

    response=requests.post(API_ENDPOINT, headers=HEADERS, json=payload)

    if response.status_code == 200:

        return response.json().get("choices", [[]])[0].get("text", "").strip()

    else:

        return f"Error: {response.status_code}-{response.text}"

# 示例1: 生成JSON格式

prompt_json="""请生成一个包含以下内容的JSON结构:

{

  "name": "产品名称",

  "price": "价格",

  "features": ["特性1", "特性2"]

}

"""

response_json=generate_formatted_output(prompt=prompt_json, max_tokens=50)

print("JSON格式输出: ")

print(response_json)

# 示例2: 生成表格数据

prompt_table="""请生成一张包含三行数据的表格:

| 名称 | 类型 | 数量 |

|----------|----------|------|

"""

response_table=generate_formatted_output(prompt=prompt_table, max_tokens=50)

print("\n表格格式输出: ")

print(response_table)

# 示例3: 生成Markdown文档

prompt_markdown="""请以Markdown格式生成一段介绍人工智能的文本，并包含一个标题和一个项目列表:

"""

response_markdown=generate_formatted_output(prompt=prompt_markdown, max_tokens=100)

print("\nMarkdown格式输出: ")

print(response_markdown)

以下是两种典型结构化输出的参考结果，可直接用于验证调用是否符合预期。

[PRE2]json

{

  "name": "智能助手",

  "price": "199.99",

  "features": ["语音识别", "任务管理"]

}

[PRE3]markdown

# 人工智能简介

人工智能（AI）是指模拟人类智能的技术，主要包括以下领域：

- 机器学习

- 自然语言处理

- 计算机视觉