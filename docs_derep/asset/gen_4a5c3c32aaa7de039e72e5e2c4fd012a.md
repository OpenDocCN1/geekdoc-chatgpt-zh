DeepSeek API 实战调用合集，一套写法搞定创作与工程

如果你想快速验证 DeepSeek 的能力，最直接的方式就是用官方兼容的 OpenAI SDK 统一接入，下面这组片段展示了从文学创作到营销文案再到提示词工程的典型调用路径，代码结构完全一致，只换消息内容就能切换任务。

先看纯创作类的单轮调用。第一个片段只用 user 角色，让模型完成一篇指定主题的夜景散文，重点在于情绪描写与城市寂静带来的感悟。

[PRE0]python

from openai import OpenAI

client=OpenAI(

    base_url="https://api.deepseek.com/",

    api_key=""

)

completion=client.chat.completions.create(

    model="deepseek-chat",

    messages=[

        {

            "role": "user", 

            "content": "以“孤独的夜行者”为题写一篇750字的散文，描绘一个人在夜晚的城市中漫无目的地行走的心情与所见所感，以及夜的寂静给予的独特感悟。"

        }

    ]

)

print(completion.choices[0].message.content)

紧接着是风格模仿的用法。同样是单轮 user 提问，但要求模型模仿特定古诗风，输出一首以现代题材为内容的七律。

[PRE1]python

from openai import OpenAI

client=OpenAI(

    base_url="https://api.deepseek.com/",

    api_key=""

)

completion=client.chat.completions.create(

    model="deepseek-chat",

    messages=[

        {

            "role": "user", 

            "content": "模仿李白的风格写一首七律.飞机"

        }

    ]

)

print(completion.choices[0].message.content)

当任务需要明确的身份和输出规范时，就引入 system 角色。第三个例子用 system 把模型设定为宣传标语专家，规定创意、押韵、简洁且只输出标语，随后用 user 指定产品，得到可直接使用的希腊酸奶广告语。

[PRE2]python

from openai import OpenAI

client = OpenAI(

    base_url="https://api.deepseek.com/",

    api_key=""

)

completion = client.chat.completions.create(

    model="deepseek-chat",

    messages=[

        {

            "role": "system", 

            "content": "你是一个宣传标语专家，请根据用户需求设计\n一个独具创意且引人注目的宣传标语，需结合该产品/活动的核心价值和特点，同时\n融入新颖的表达方式或视角。请确保标语能够激发潜在客户的兴趣，并留下深刻印\n象，可以考虑采用比喻、双关或其他修辞手法来增强语言的表现力。标语应简洁明了\n了，需要朗朗上口，易于理解和记忆，一定要押韵，不要太过书面化。只输出宣传标\n语，不用解释。"

        },

        {

            "role": "user", 

            "content": "请生成"希腊酸奶"的宣传标语。"

        }

    ]

)

print(completion.choices[0].message.content)

环境准备也很简单，Linux 下先更新包源即可。

[PRE3]bash

    sudo apt update

提示词工程本身也可以交给模型来做。最后一个片段把模型定位成提示词生成专家，要求输出 Markdown 格式、清晰简洁且无额外解释，最后让它为 Linux 助手生成一份可直接使用的系统提示词。

[PRE4]python

from openai import OpenAI

client = OpenAI(

    base_url="https://api.deepseek.com/",

    api_key=""

)

completion = client.chat.completions.create(

    model="deepseek-chat",

    messages=[

        {

            "role": "system", 

            "content": "你是一位大模型提示词生成专家，请根据用户的需求编写一个智能助手的提示词，指导大模型进行内容生成，要求：\n1. 以Markdown格式输出\n2. 贴合用户需求，描述智能助手的定位、能力、知识储备\n3. 提示词应清晰、精确、易于理解，在保持质量的同时，尽可能简洁\n4. 只输出提示词，不要输出多余解释"

        },

        {

            "role": "user", 

            "content": "请帮我生成一个\"Linux助手\"的提示词"

        }

    ]

)

print(completion.choices[0].message.content)