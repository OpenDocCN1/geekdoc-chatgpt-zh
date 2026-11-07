**【例 12-5】 Node.js Axios 双层缓存实战**

想减少重复的 API 消耗并提升响应速度，可以用 Node.js 搭配 axios 发起请求，并在内存缓存与文件缓存之间建立协同机制。先在本地检索已有结果，有命中就直接返回；未命中时再组装鉴权头向 DeepSeek 接口提交，完成后再把结果写入缓存供后续复用。

[PRE0]

下面是核心的请求与缓存流程，先查缓存再请求，成功后落库，失败时给出兜底提示。

[PRE1]python
# 检查缓存

const cachedResult = checkCache(query);

if (cachedResult) {

    console.log('从缓存中获取数据:', cachedResult);

    return cachedResult; // 如果缓存存在，直接返回缓存的结果

}

// 构建API请求

const response = await axios.post(apiEndpoint, {

    headers: {

        'Authorization': `Bearer ${apiKey}`,

        'Content-Type': 'application/json',

    },

    data: {

        prompt: query, // 用户的查询

        model: 'deepseek-v3', // 使用DeepSeek-V3模型

        max_tokens: 100,

        temperature: 0.7,

    }

});

// 获取API返回结果

const completion = response.data.choices[0].text;

// 缓存API结果

saveCache(query, completion);

console.log('API返回数据并已缓存:', completion);

return completion;

} catch (error) {

    console.error('API调用失败:', error.message);

    return '调用失败，请稍后再试';

}

为了验证缓存是否生效，可以连续发送几组典型提问，观察第二次相同查询是否走了本地数据。

[PRE2]python
async function runExample() {

    const queries = [

        '如何在VS Code中创建一个新项目？',

        'JavaScript中的箭头函数是什么？',

        'DeepSeek模型的应用场景有哪些？'

    ];

    // 多次调用同一个查询，观察缓存效果

    for (let query of queries) {

        console.log(` 查询: $${query}`);

        const result = await fetchCompletion(query);

        console.log('结果:', result);

        console.log('-----');

    }

}

// 执行示例

runExample();

下面补充一个 Python 侧的 DeepSeek 代码补全调用示例，展示如何用 requests 直接对接模型接口并获取补全文本。

[PRE3]python
import openai

import os

import json

import requests

# 配置DeepSeek API Key

API_KEY="your_deepseek_api_key"

API_URL="https://api.deepseek.com/v3/completion"  # DeepSeek API URL

# 设置请求头部

headers={

    "Content-Type": "application/json",

    "Authorization": f"Bearer {API_KEY}",

}

# 函数：调用DeepSeek API进行代码补全

def get_code_suggestion(prompt: str, max_tokens: int=100):

    # 请求体

    data = {

        "model": "deepseek-v3-code",   # 使用DeepSeek代码补全模型

        "prompt": prompt,

        "max_tokens": max_tokens,

        "temperature": 0.7,             # 控制生成的创意程度

        "top_p": 1.0,

        "n": 1,                         # 返回一个补全结果

    }

    # 发送请求并获取响应

    response = requests.post(API_URL, headers=headers, json=data)

    # 解析响应结果

    if response.status_code == 200:

        result = response.json()

        return result['choices'][0]['text'].strip() # 返回补全后的代码

    else:

        print(f"API请求失败，状态码：{response.status_code}")

        return None

# 测试代码补全功能

def test_code_completions():

    # 示例代码片段，用户输入的部分代码

    prompt = """

def calculate_area(radius):

    import math

    area=math.pi * radius ** 2

    return area

result=calculate_area(5)

print(result)

"""

    # 调用DeepSeek API进行代码补全

    suggestion = get_code_suggestion(prompt)

    # 输出补全的代码

    if suggestion:

        print("补全建议：")

        print(suggestion)

    else:

        print("没有返回补全结果。")

# 执行测试

if __name__ == "__main__":

    test_code_completions()

示例中使用的待补全片段如下：

[PRE4]python
python

def calculate_area(radius):

    import math

    area = math.pi * radius ** 2

    return area

result = calculate_area(5)

print(result)

完整项目通常还会引入日志与时间戳等通用依赖：

[PRE5]python
import openai

import os

import requests

import json

from datetime import datetime

# 配置DeepSeek API Key