OpenManus 规划流运行机制与本地起步指南

PlanningFlow 负责把一个自然语言任务拆成可执行的步骤并按序推进，整体是循环取步、执行、累积结果，并在代理主动结束时退出。

主循环会先根据步骤信息取出类型，拿到对应的执行器后调用内部的执行方法，把每次返回的内容拼接到总结果中。执行过程中会检查执行器的状态属性，一旦状态变为 FINISHED 就立即跳出循环。任何异常都会被记录并返回统一的执行失败提示。

初始计划的生成依赖大模型和 PlanningTool。创建时先记录计划 ID，随后构造系统提示，要求模型扮演规划助手，输出简洁可操作、聚焦关键里程碑而非细碎子任务的方案，并强调清晰高效。用户提示则要求为具体请求制定合理且步骤明确的计划。

随后以自动工具选择的方式调用 LLM。模型如果返回工具调用且函数名为 planning，就解析参数为 JSON，补上当前计划 ID，再通过工具集合执行创建操作。规划失败或未产生工具调用时，会走兜底分支，发出警告并用工具创建一个默认计划，标题截取请求前 50 字符并在超长时补省略号，步骤固定为分析请求、执行任务、验证结果。

定位当前步骤时，首先校验计划 ID 是否存在且在工具存储中可查。读取计划数据中的 steps 和 step_statuses 列表后，依次遍历步骤。若状态列表不足则视为未开始，否则取对应状态。遇到属于活跃状态的步骤就认为它需要处理。此时会从步骤文本中用正则提取形如 [SEARCH]、[CODE] 的类型标记并转为小写，组装步骤信息并尝试通过工具将该步骤标记为进行中。若标记接口异常，则直接在内存状态中补齐并更新为进行中，最后返回索引与信息。若遍历完没有活跃步骤则返回空。

单步执行会先获取当前计划的文本快照，取出步骤描述并拼装提示，提示中包含计划全貌和当前步骤编号与内容，要求执行器使用适当工具完成并给出总结。调用执行器的 run 方法后，成功则标记步骤完成并返回结果，失败则记录错误并返回步骤执行失败的提示。

本地部署提供 Conda 与 uv 两套路径，均以克隆仓库开始，后续分别激活环境、安装依赖并启动 Playwright，最后用 python 启动主程序或相关脚本。

Conda 方案
[PRE0]
conda create -n open_manus python=3.12
conda activate open_manus
[PRE1]
git clone https://github.com/mannaandpoem/OpenManus.git
cd OpenManus
[PRE2]
pip install -r requirements.txt

uv 方案
[PRE3]
curl -LsSf https://astral.sh/uv/install.sh | sh
[PRE4]
git clone https://github.com/mannaandpoem/OpenManus.git
cd OpenManus
[PRE5]
uv venv --python 3.12
source .venv/bin/activate  # Unix/macOS 系统
# Windows 系统使用：
# .venv\Scripts\activate
[PRE6]
uv pip install -r requirements.txt
[PRE7]
playwright install

启动入口
[PRE8]
python main.py
[PRE9]
python run_mcp.py
[PRE10]
python run_flow.py