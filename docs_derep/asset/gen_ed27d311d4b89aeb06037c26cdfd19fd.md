[PRE0]response = session.post(API_URL, json=payload)

if response.status_code == 200:

  reply = response.json().get("choices", [{}])[0].get("message", {}).get("content", "").strip()

  context_cache.append({"role": "user", "content": messages})

  context_cache.append({"role": "assistant", "content": reply})

  return reply, context_cache

else:

  return f"错误:{response.status_code}", context_cache

# 批量调用与KV缓存实战代码
if __name__ == "__main__":

    # 批量请求示例

    print("批量请求优化示例:")

    prompts = [

        "解释什么是机器学习", 

        "深度学习和传统机器学习的区别", 

        "大模型的应用场景"

    ]

    results = batch_requests(prompts)

    for i, result in enumerate(results):

        print(f"请求{i+1}:{result}")  # KV Cache示例

    print("\n上下文缓存优化示例:")
[PRE1]

## 核心优化思路拆解

> (1) **合并批量请求**：把若干独立任务集中打包发送，减少网络往返次数与服务器压力。
>
> (2) **上下文缓存 KV Cache**：将历史对话保留下来，避免重复生成已有内容，响应明显更快。
>
> (3) **连接复用**：使用 `requests.Session` 维持长连接，省去频繁 TCP 握手的开销。
>
> (4) **参数精简**：适当调低 `max_tokens` 限制输出长度，能有效缩短推理耗时。

# 实测输出展示

**批量调用效果：**

*   **请求 1**: 机器学习的核心是通过数据训练模型，实现预测与决策。
*   **请求 2**: 深度学习依托神经网络处理非结构化数据，传统机器学习则更依赖人工特征设计。
*   **请求 3**: 大模型常被用于文本生成、代码补全、智能问答等场景。

**上下文缓存效果：**

用户：你好，什么是 API？
DeepSeek 回复: API 是应用程序接口，负责不同软件之间的数据交互。

用户: API 的优点是什么？
DeepSeek 回复：API 有助于简化开发流程、提升效率，并让系统具备更好的模块化和可扩展性。

用户：如何优化 API 的性能？
DeepSeek 回复：常见的优化手段包括引入缓存机制、采用批量处理、复用连接以及减少单次请求的数据量。

批量调用、KV 缓存、长连接复用等技术组合，能明显降低调用延迟，加快模型响应。上述实践展示了这些技巧在真实开发中的落地价值，为高效使用 DeepSeek API 提供了参考。它们特别适合对实时性要求高的应用，例如在线问答、智能对话和多任务并发处理。

# 5.3.2 数据安全与访问权限管控

在面向用户的 AI 产品中，特别是处理敏感信息时，数据保护与调用权限管理是保障隐私与安全的基础。要防止信息泄露或非法访问，系统必须实现细粒度的权限控制，并对每一次 API 调用进行严格的身份核验与权限校验。

在 DeepSeek 的开发实践里，关键要素包括 API 密钥管理、访问控制列表 ACL、访问 Token 以及权限的动态调整。开发者可通过平台配置策略，限制不同用户或应用的访问边界，确保请求始终在授权范围内运行。

落地通常包含以下流程。

1.  **身份鉴别**：借助 API Key、OAuth 等认证方式确认调用者的真实身份。
2.  **动态授权**：在请求处理过程中实时调整权限，仅允许符合策略的操作访问特定数据或功能。
3.  **日志审计**：完整记录每次请求的时点、调用者、访问内容等信息，便于后续审计与合规审查。

【例 5-8】在 DeepSeek 开发中实现数据保护与调用权限管理，以及通过 API 调用创建权限验证的接口，并对每次请求进行身份验证。
[PRE2]

# 调用 DeepSeek 的 Chat API 进行聊天对话生成

response = requests.post(f"{api_base_url}/api/create-chat-completion", headers=headers, json=data)

# 解析返回结果

if response.status_code == 200:

    result = response.json()

    return result.get("choices", [{}])[0].get("message", "未能生成有效的回复")

else:

    return f"错误：{response.status_code}, {response.text}"

# 创建完成请求，带有权限控制

def create_completion(user_role, prompt):

    if not check_permissions(user_role, "create-completion"):

        return "权限不足，无法访问此接口"

    # 模拟请求数据

    data = {

        "model": "deepseek-v3",

        "prompt": prompt,

        "max_tokens": 100

    }

    # 调用 DeepSeek 的 Completion API 进行文本生成

    response = requests.post(f"{api_base_url}/api/create-completion", headers=headers, json=data)
[PRE3]python

import requests

# DeepSeek API 配置

API_URL = "https://api.deepseek.com/v1/chat/completions"

API_KEY = "your_api_key_here"  # 替换为用户的 API 密钥

HEADERS = {

    "Authorization": f"Bearer {API_KEY}",  # 身份验证

    "Content-Type": "application/json"     # 数据格式为 JSON

}

# 定义对话功能

def chat_with_DeepSeek(context):

    """    调用 DeepSeek 实现对话功能

    :param context: 上下文对话记录，格式为 JSON 数组

    :return: 模型生成的回复

    """

    payload = {

        "model": "deepseek-v3",

        "messages": context,