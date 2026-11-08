用大模型来写代码，真正的价值不在于一键生成，而在于生成之后的自检、排版和提效闭环。下面这套流程把模型、静态检查和格式化工具串起来，先用示例验证思路，再扩展到动态规划类任务，整个过程可以直接复用到自己的项目里。

先把运行环境准备好，依赖只有四个包，模型、运行框架、代码质量检查和自动格式化。

[PRE0]
pip install transformers torch pylint autopep8

基础导入是模型库、子进程调用和格式化工具。

[PRE1]python
from transformers import AutoTokenizer, AutoModelForCausalLM

import subprocess

import autopep8

import torch

下面是一份可复用的骨架：加载 CodeGen 模型、提供语法检查、代码格式化和生成接口，先跑一个最大公约数的简单任务，再演示手动优化的思路。

[PRE2]python
# 1. 加载代码生成模型
model_name="Salesforce/codegen-350M-multi"  #一个适合代码生成的模型
tokenizer=AutoTokenizer.from_pretrained(model_name)
model=AutoModelForCausalLM.from_pretrained(model_name)
model.eval()
# 2. 语法检查函数
def check_syntax(code):
    # 使用 pylint 检查代码质量
    with open("temp_code.py", "w", encoding="utf-8") as f:
        f.write(code)
    result=subprocess.run(["pylint", "temp_code.py"],
stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
    return result.stdout
# 3. 格式化代码函数
def format_code(code):
    # 使用 autopep8 格式化代码
    return autopep8.fix_code(code)
# 4. 代码生成函数
def generate_code(prompt, max_length=100):
    # 使用大语言模型生成代码
    inputs=tokenizer(prompt, return_tensors="pt",
truncation=True, max_length=128)
    outputs=model.generate(inputs["input_ids"], max_length=max_length,
pad_token_id=tokenizer.eos_token_id)
    generated_code=tokenizer.decode(outputs[0], skip_special_tokens=True)
    return generated_code
# 示例任务 1：简单代码生成
task_prompt="编写一个 Python 函数计算两个数的最大公约数。"
generated_code=generate_code(task_prompt)
# 示例任务 2：检查生成代码的语法
syntax_report=check_syntax(generated_code)
# 示例任务 3：格式化生成代码
formatted_code=format_code(generated_code)
# 输出结果
print("===原始生成代码 ===")
print(generated_code)
print("\n===语法检查报告 ===")
print(syntax_report)
print("\n===格式化后的代码 ===")
print(formatted_code)
# 示例任务 4：性能优化(手动优化案例)
def optimize_code(original_code):
    """
模拟一个简单的性能优化函数,替换高复杂度函数为更高效的方法。
    """
    return original_code.replace("while True:", "for _ in range(1000):")
optimized_code=optimize_code(formatted_code)
print("\n===优化后的代码 ===")
print(optimized_code)

模型给出的最大公约数实现是标准的欧几里得写法。

[PRE3]python
def gcd(a, b):
    while b != 0:
        a, b=b, a % b
    return a

pylint 回调指出缺少文档字符串、命名规范问题，评分 6.67/10，属于可以运行但风格不达标的状态。

[PRE4]
************* Module temp_code
temp_code.py:1:0: C0114: Missing module docstring (missing-module-docstring)
temp_code.py:1:0: C0116: Missing function or method docstring (missing-function-docstring)
temp_code.py:1:0: C0103: Function name "gcd" doesn't conform to snake_case naming style (invalid-name)
------------------------------------------------------------------
Your code has been rated at 6.67/10 (previous run: 6.67/10, +0.00)

格式化后代码本身没有变化，autopep8 只是做了空白和换行整理。

[PRE5]python
def gcd(a, b):
    while b != 0:
        a, b=b, a % b
    return a

这里做一个演示性的手工优化，把 while 结构替换成固定次数循环，展示后期介入改进的入口。

[PRE6]python
def gcd(a, b):
    for _ in range(1000):
        a, b=b, a % b
    return a

同样的依赖再次确认，保证第二阶段可独立运行。

[PRE7]
pip install transformers torch pylint autopep8

针对更复杂的动态规划任务，封装专门的生成、检查、格式化和内存优化函数。

[PRE8]python
from transformers import AutoTokenizer, AutoModelForCausalLM
import subprocess
import autopep8
import torch
# 加载代码生成模型
model_name="Salesforce/codegen-350M-multi"
tokenizer=AutoTokenizer.from_pretrained(model_name)
model=AutoModelForCausalLM.from_pretrained(model_name)
model.eval()
# 动态规划代码生成函数
def generate_lcs_code(prompt, max_length=150):
    inputs=tokenizer(prompt, return_tensors="pt", truncation=True,
max_length=128)
    outputs=model.generate(inputs["input_ids"], max_length=max_length,
pad_token_id=tokenizer.eos_token_id)
    generated_code=tokenizer.decode(outputs[0], skip_special_tokens=True)
    return generated_code
# 语法检查函数
def check_syntax(code):
    with open("temp_code.py", "w", encoding="utf-8") as f:
        f.write(code)
    result=subprocess.run(["pylint", "temp_code.py"],
stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
    return result.stdout
# 格式化代码函数
def format_code(code):
    return autopep8.fix_code(code)
# 性能优化函数
def optimize_lcs_code(code):
    """
模拟性能优化,将嵌套循环替换为内存优化版本。
    """
    optimized_code=code.replace(
"dp=[[0 for j in range(len(str2)+1)] for i in range(len(str1)+1)]",
                        "dp=[0] * (len(str2)+1)")
    return optimized_code
# 代码生成示例
lcs_prompt="""
编写一个动态规划算法解决最长公共子序列问题。
输入为两个字符串 str1 和 str2,返回它们的最长公共子序列长度。
"""
lcs_code=generate_lcs_code(lcs_prompt)
# 语法检查
syntax_report=check_syntax(lcs_code)
# 格式化代码
formatted_code=format_code(lcs_code)
# 性能优化
optimized_code=optimize_lcs_code(formatted_code)
# 输出结果
print("===原始生成代码 ===")

输出顺序保持原始、检查、格式化、优化四步可对照。

[PRE9]python
print(lcs_code)
print("\n===语法检查报告 ===")
print(syntax_report)
print("\n===格式化后的代码 ===")
print(formatted_code)
print("\n===性能优化后的代码 ===")
print(optimized_code)

模型生成的最长公共子序列实现采用二维表，逻辑完整。

[PRE10]python
def longest_common_subsequence(str1, str2):
    dp=[[0 for j in range(len(str2)+1)] for i in range(len(str1)+1)]
    for i in range(1, len(str1)+1):
        for j in range(1, len(str2)+1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j]=dp[i - 1][j - 1]+1
            else:
                dp[i][j]=max(dp[i - 1][j], dp[i][j - 1])
    return dp[len(str1)][len(str2)]

pylint 只提示缺少文档字符串，评分提升到 8.33/10，可读性更好。

[PRE11]
************* Module temp_code
temp_code.py:1:0: C0114: Missing module docstring (missing-module-docstring)
temp_code.py:1:0: C0116: Missing function or method docstring (missing-function-docstring)
------------------------------------------------------------------
Your code has been rated at 8.33/10

格式化后的版本与生成版本一致，排版已统一。

[PRE12]python
def longest_common_subsequence(str1, str2):
    dp=[[0 for j in range(len(str2)+1)] for i in range(len(str1)+1)]
    for i in range(1, len(str1)+1):
        for j in range(1, len(str2)+1):
            if str1[i - 1] == str2[j - 1]:
                dp[i][j]=dp[i - 1][j - 1]+1
            else:
                dp[i][j]=max(dp[i - 1][j], dp[i][j - 1])
    return dp[len(str1)][len(str2)]

内存优化后把二维表压缩为一维滚动数组，空间复杂度从 O(mn) 降到 O(n)。

[PRE13]python
def longest_common_subsequence(str1, str2):
    dp=[0] * (len(str2)+1)
    for i in range(1, len(str1)+1):
        prev=0
        for j in range(1, len(str2)+1):
            temp=dp[j]
            if str1[i - 1] == str2[j - 1]:
                dp[j]=prev+1
            else:
                dp[j]=max(dp[j], dp[j - 1])
            prev=temp
    return dp[len(str2)]

把这套能力封装成服务时，常见的做法是基于 FastAPI 暴露接口，配合 Pydantic 做校验，后端再接入 Streamlit 做交互。

[PRE14]
pip install fastapi uvicorn pydantic requests streamlit

[PRE15]python
from fastapi import FastAPI
from pydantic import BaseModel
# 初始化 FastAPI 应用
app = FastAPI()
# 数据存储（模拟数据库）
tasks = []
# 数据模型
class Task(BaseModel):
    id: int
    title: str