# 表 3-1 Mermaid 中常见的图表类型

| 英文单词 | 中文翻译 |
| :--- | :--- |
| Flowchart | 流程图 |
| Sequence Diagram | 序列图 |
| Gantt Chart | 甘特图 |
| State Diagram | 状态图 |
| User Journey Diagram | 用户旅程图 |
| Class Diagram | 类图 |
| Entity-Relationship Diagram (ERD) | 实体关系图 |
| Mind Map | 思维导图 |
| Pie Chart | 饼图 |
| Tree Diagram | 树状图 |

# 提示词：

制定一份新用户注册软件的流程图，用 Mermaid 格式输出。

# 输出结果：

```
graph TD
A[ 启动注册流程 ] ---> B[ 显示注册表单 ]
B ---> C[ 用户填写信息 ]
C ---> D{ 前端验证 }
D ---> | 通过 | E[ 提交服务器 ]
D ---> | 失败 | F[ 显示错误 ]
F ---> B
E ---> G{ 后端验证 }
G ---> | 邮箱重复 | H[ 返回错误 1 ]
G ---> | 用户名重复 | I[ 返回错误 2 ]
G ---> | 通过 | J[ 创建账户 ]
H ---> B
I ---> B
J ---> K[ 发送验证邮件 ]
K ---> L[ 显示成功页面 ]
L ---> M{ 点击验证链接 }
M ---> | 已点击 | N[ 激活账户 ]
```