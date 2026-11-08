# DeepSeek 填空补全与多风格生成实战

想让模型既能读懂前后文进行中间补全，又能按需控制输出风格和格式？下面这套组合把 Fill-In-Middle 填空、DeepSeek 前缀续写和结构化生成串起来了。先用一个命令行编辑助手自动识别 `[填空]` 占位，基于前后语境发起补全，再用最小可用样例展示连贯续写、风格化创意和格式约束。

实现上用了一个 TextEditingAssistant。fill_in_the_middle 负责把前文和后文打包成 fill_in_the_middle 请求并取回补全结果；detect_blank_positions 用正则扫描全文，定位所有 `[填空]` 并把每个位置切分成 context_before 与 context_after；perform_fim 遍历这些位置逐个调用补全并回填；start_fim 启动交互循环，提示用户用 `[填空]` 标记需要补写的段落，输入退出指令即可结束；reset_conversation 用于清空对话历史。整体跑通逻辑是定位占位 → 抽取语境 → 发起补全 → 回填结果，然后持续交互编辑。

先看最简的前缀续写调用，通过 temperature、top_p、max_tokens 调节连贯度与创造力。

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

如果希望输出更可控，换成带格式约束的调用会更稳。同一套接口，改动提示词就能稳定产出 JSON、表格或 Markdown。

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

下面是两种典型结构化输出的参考结果，可直接拿来校验调用效果。

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