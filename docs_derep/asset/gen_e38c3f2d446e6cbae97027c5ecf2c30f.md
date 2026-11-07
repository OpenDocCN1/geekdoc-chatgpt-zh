[PRE0][PRE1]

（7）convert_asis 把用户提交的原文原封不动地封装成 HTML 段落，输出时使用 ＜p＞ 标签并设定 white-space: pre-wrap 样式，同时完成 HTML 实体转换，并在末尾加上转换标记，从而保证输入内容能够逐字原样显示。

[PRE2]

（8）is_stop_word_or_prefix 用来检查字符串 s 的结尾是否落在停用词列表 stop_words 的任一项上，匹配成功返回 True，未匹配返回 False，主要配合截断或停止处理的判断逻辑使用。

[PRE3]

（9）detect_converted_mark 会判定用户输入是否以预置的转换标记 ALREADY_CONVERTED_MARK 收尾，以此确认内容是否已经过格式转换，已转换返回 True，未转换返回 False。

[PRE4]

（10）detect_language 从代码片段中取出首行，判断该行是否属于语言提示，识别成功后以小写形式返回语言名称，并把提示行剔除后返回净化后的代码文本。

[PRE5][PRE6]

（11）convert_to_markdown 负责把普通文本转为规范 Markdown，先对特殊符号如 $ 进行 HTML 实体转义，再处理换行，并把每行的前导空格与制表符统一转换，同时对标题等部分 Markdown 语法做转义，最终得到可用 Markdown。

[PRE7][PRE8]

（12）add_language_tag 通过正则定位代码块，借助 Pygments 库推断代码语言，并在 Markdown 代码块语法中补上对应的语言标识，以提升前端语法高亮效果。

[PRE9][PRE10]

（13）is_variable_assigned 在当前局部作用域中探测是否存在指定名称 var_name 的变量，存在则返回 True，不存在返回 False，常用于调试或动态变量状态检测。

[PRE11]

（14）pil_to_base64 将 PIL 图像转成 Base64 编码的 HTML ＜img＞ 标签字符串，可按参数决定是否先缩放，随后写入内存缓冲区编码，并带上指定的 alt 文本生成标签。

[PRE12][PRE13]

（15）parse_ref_bbox 解析模型回复中的视觉定位标记 ＜|ref|＞ 与 ＜|det|＞，提取边界框坐标及对应标签并在传入图像上绘制标注，解析失败时返回 None。

[PRE14][PRE15]

（16）display_example 接收图片路径列表，逐张打开并转成 Base64 的 ＜img＞ 标签，再拼合成 flex 布局的 HTML 字符串，供前端统一展示示例图。

上述工具共同组成了 DeepSeek-VL2 项目中面向前端展示与数据预处理的辅助集合，覆盖日志、文本与 Markdown 转换、图像处理与编码、视觉定位解析与可视化等环节。

### **6.6.3 Gradio 工具**

gradio_utils.py 提供了 Gradio 应用的常用辅助与状态管理能力，包含生成函数包装、对话历史的维护与重置、文本框清空、输出中断控制等，State 类专门负责全局中断状态的开关。通过这些封装，界面各组件的交互逻辑得以大幅简化。

[PRE17][PRE18][PRE19][PRE20]

借助上述封装，Gradio 相关工具函数被统一收口，前端组件的调用与配置变得更简洁，也便于快速搭建交互式演示界面。

### **6.6.4 模板覆盖与扩展**

overwrites.py 主要对 DeepSeek-VL2 的文本处理与界面输出进行个性化扩展，并提供 JavaScript 加载与模板覆盖能力，以改善交互体验。核心能力包括：

● 文本块压缩 compact_text_chunks：对输入文本块合并再重切分，使其适配模型处理粒度。
● 后处理 postprocess：把用户与模型产生的 Markdown 消息统一转为 HTML。
● JavaScript 文件加载 reload_javascript：动态加载自定义 JS 并注入 Gradio 模板。
● 覆盖 Gradio 模型响应 GradioTemplateResponseOriginal：重定义模板响应方法以支持自定义脚本。

[PRE21][PRE22][PRE23]

### **6.6.5 Web 前端**

serve/assets/ 目录存放 DeepSeek-VL2 Web 界面的静态资源，包含 Kelpy-Codos.js、custom.js 等脚本、custom.css 样式表、simsun.ttc 字体、favicon.ico 图标以及 avatar.png 等图像，为交互与展示提供资源支撑。

其中 Kelpy-Codos.js 的作用是为页面中所有 ＜pre＞ 代码块动态挂载“复制”按钮，单击即可把代码内容写入剪贴板。脚本使用 MutationObserver 监听 DOM 变化，保证新插入的代码块也能自动获得复制按钮，并通过图标变化反馈复制状态。

[PRE24][PRE25][PRE26]

### **6.6.6 模型推理**

inference.py 实现了 DeepSeek-VL2 的推理主流程，涵盖模型加载、会话转模型输入、停止条件定义以及多模态生成。内部调用 generate 完成流式输出，支持图像输入与多种生成参数调节。

上述实现的核心点如下：

● 模型加载 load_model：加载模型、处理器与分词器，支持 GPU 推理。
● 会话转换 convert_conversation_to_prompts：将图文对话整理成模型可读的输入格式。
● 文本生成 deepseek_generate：基于多模态内容调用模型生成文本，支持流式与温度、重复惩罚等超参调节。
● 流式输出 generate：借助 TextIteratorStreamer 逐步输出，提升交互即时性。
● 停止条件控制 StoppingCriteria：自定义终止条件，在特定词表出现时结束生成。

总体来看，该文件完成了从数据预处理到多模态生成输出的完整推理闭环。

## **6.7** **图文对话推理**

项目根目录的 inference.py 用于基于 DeepSeek-VL2 进行图文对话推理，涵盖模型加载、会话输入处理、多模态生成与结果呈现，并支持视觉目标定位等高级能力，结合图像与文本生成新的对话结果。

[PRE31][PRE32][PRE33][PRE34]

### **注意**

主目录的 inference.py 更偏向离线实验与本地测试；serve 目录下的 inference.py 则面向 Web 部署与在线服务，具备流式输出与更强的交互灵活性。

## **6.8** **Web 测试**

DeepSeek-VL2 提供 Web 体验入口，web_demo.py 可快速搭建基于 Web 的交互界面，用于体验与测试模型，支持实时对话与文本、图像输入的响应观察。

### **6.8.1** **Web 前端实现**

web_demo.py 基于 Gradio 构建前端，实现了模型加载、对话生成、参数配置、图片上传展示、预测与重生成等功能。用户输入文本与图像后，系统调用模型返回对应结果。

具体流程如下：

（1）定义 MODELS 列表，收纳 DeepSeek-VL2 的多种变体名称，涵盖 tiny、small 及标准版，并给出 Hugging Face 完整路径，便于集中管理与动态切换。

[PRE35]

（2）声明两个全局变量，DEPLOY_MODELS 为空字典用于缓存已部署模型；IMAGE_TOKEN 设为字符串 "＜image＞"，作为图像占位符。

[PRE36]

（3）examples_list 列表收纳多个示例，主要用于视觉引导与视觉问答，每个元素为图像路径与对应文本指令的组合。

[PRE37][PRE38]

（4）按 model_name 加载模型，若已在 DEPLOY_MODELS 中则直接返回，否则加载并缓存；支持通过 args.local_path 指定本地路径，或使用默认仓库；加载过程会输出提示信息。

[PRE39][PRE40]

（5）根据文本、图片、历史对话及 max_length 构建带历史的提示，图片存在时自动校准 ＜image＞ 标记与图片数量的对应关系；提示过长时尝试剔除较早历史以压缩，若仍不可行则返回 None。

[PRE41][PRE42][PRE43]

（6）把对话 conv 转为 Gradio 聊天机器人格式，遍历每条消息按角色处理，含图片时转为 Base64 字符串嵌入，最终得到 Gradio 可渲染的格式。

[PRE44]

（7）把对话转为 Gradio 历史记录状态，返回从偏移量开始的所有消息。

[PRE45]

（8）生成用于推理的提示，若采用 DeepSeek 风格分隔符 SeparatorStyle.DeepSeek，则按角色与内容拼接并加入分隔符；否则调用 get_prompt 获取默认提示。

[PRE46]

（9）对输入文本与图片做转换并返回更新后的文本框与按钮状态，返回元组包含输入文本、输入图像、清空文本、清空图像以及按钮可见性。

[PRE47]

（10）predict() 为用户输入生成响应，先加载处理图像并生成含历史的提示，再调用生成模型产出内容，过程中不断刷新并把结果推送到 Gradio 界面实现实时展示。

[PRE48][PRE49][PRE50]

（11）retry() 用于重新预测，若聊天记录为空则给出提示；若有历史则删除最后一条记录并回退到上一个有效输入，随后调用 predict 重新生成。

[PRE51][PRE52]

（12）接收一组文件并返回其路径列表，遍历提取路径并汇总，便于图片预览。

[PRE53]

（13）build_demo() 构建 Gradio 应用 GUI，包含用户输入区、图片上传、历史记录展示、模型选择与参数调节等，用户可输入文本、上傳圖片、调整生成参数并交互，通过组件查看对话记录、预览图片、发送、重试、清空对话等操作。

[PRE54][PRE55][PRE56][PRE57][PRE58]