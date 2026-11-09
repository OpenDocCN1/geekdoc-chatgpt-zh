ChatGPT 的输出采用流式逐字呈现的逻辑，所以内容一旦出现明显偏离或完全不可用，直接点按“Stop generating”就能立刻中断，不必等它跑完。

□ Stop generating

# 图2-14 叫停生成的入口

当生成文本过长出现停顿或卡带般的中断时，只需在页面上点一下“Continue”按钮，模型就会继续把剩余内容补完。

# 第三节 插件功能的使用方法

2023 年 3 月，OpenAI 正式发布了插件能力。

在纯文本之外，借助插件 ChatGPT 能完成更多实际操作。例如通过浏览器类插件获取实时信息，这对降低本章第六节谈到的“幻觉”问题很有帮助。有了插件后，还能协助用户处理预订机票、安排餐厅等复合型任务。像 Wolfram 这类插件还能补强计算弱项，让算术与科学计算更可靠。

需要说明的是，插件商店目前仅向 ChatGPT Plus 订阅用户开放。

# 一、使用的步骤

开启插件功能需要按下面顺序操作：

+   1.  点左下角的“Settings”进入设置页，在“Beta features”里打开“Plugins”。

[PRE0]

# 图2-15 插件入口位置

顺带说明，打开插件入口时可以同时启用“Code interpreter”。该功能在 2023 年 7 月上线，可实现自动写代码、生成图片、数据分析等更复杂操作。原本的官方浏览器“Browse with Bing”因版权争议被下架，待越权访问问题解决后有望重新回归 Plus 用户。

2.  新建一个 GPT-4 对话窗口，点击对话区的“Plugins”按钮。

+   

# 图2-16 对话界面中打开插件

3.  在“Plugins”下拉列表里选择“Plugin store”。

+   

# 图2-17 进入插件商店

[PAGE 49]

# 第二章 使用ChatGPT前的准备 | 037

4.  在“Plugin store”里挑选符合需求的插件，点击“install”，即可将插件保留在当前 GPT-4 窗口中使用。

![](img/53521336fcceb5f7048683

# 038 || 法律人ChatGPT应用指南

提出计算需求时，ChatGPT 会在已选的三个插件中自动调用 Wolfram 来完成。

地球到冥王星的距离约 5.056×10^9 公里，地球赤道周长约 40075.017 公里。两者之间的换算相当于多少圈赤道，可用以下式子求得：

5.056×10^9 / 40075.017

开始计算。

Used Wolfram

结果约为环绕赤道 126163 次。

# 图2-20 调用Wolfram插件后的答案

随后经过三次插件调用，ChatGPT 给出结论：地球到冥王星的距离约等于绕地球赤道 126163 圈。生成结果的同时，Wolfram 图标也自动变为 GPT-4 的紫色标识。

至此，一次 Plus 账户下的插件使用就完成了。

# 二、实用插件推荐

了解基本原理后，介绍几款常用插件。

# (一) AskYourPDF

AskYourPDF 的核心是从 PDF 文档中提取信息。启用后把 PDF 链接丢进 GPT-4 对话框，插件会将内容写入向量数据库。后续针对文档提问时，答案会基于原文给出，并用加粗标注出页码。

提供可下载链接有两种做法：

+   1.  直接使用已有的 PDF 链接。
在网上找到链接后复制到 ChatGPT 直接提问即可。

+   2.  上传本地文件获取链接。
插件提供上传入口。先在 GPT-4 对话框中输入提示词“请给出 AskYourPDF 的上传链接”，会返回一个可点页面。

[PRE1]

# Upload Your Document

Click here to upload your document and then paste your Doc ID in the chat box

# 图2-21 AskYourPDF上传页

点“蓝色云朵”图标跳转到上传页。将“.pdf”“ .txt”“ .ppt”“ .pptx”“ .csv”“ .epub”“ .rtf”等支持格式拖到“Upload Document Here”。上传成功后会生成 ChatGPT 可识别的“doc_id”，复制该编码到对话即可提问。

[PAGE 51]

# 第二章 使用ChatGPT前的准备 || 039

[PRE2]

# 图2-22 拖拽上传文件

[PRE3]

# 图2-23 上传后复制识别编码

(二) Prompt Perfect

Prompt Perfect 是一款提示词润色的轻量插件。

[PRE4]

# Prompt Perfect

Type 'perfect' to craft the perfect prompt, every time.

# 图2-24 Prompt Perfect插件介绍

第三章会详细讲提示词技巧，如设定身份、少样本学习、用标点、任务分解、格式说明等。Prompt Perfect 能在对话中实时提升提示质量。触发方式很简单，在对话末尾加上“perfect”即可。例如：

[PRE5]

我想知道如何烹饪意大利面 perfect

[PRE6]

Using Prompt Perfect... ✦ ▼

# 图2-25 输入 perfect 启动插件

润色后的提示词不会直接展示给用户。指令发出后，插件会把请求交给 GPT-3.5-turbo 模型优化，再把优化结果传给 GPT-4，由 GPT-4 直接生成最终内容。

[PRE7]

我想知道如何烹饪意大利面 perfect

[PRE8]

Used Prompt Perfect

好的，你想了解意大利面的做法。下面是一种基础做法：

+   - 煮面：大锅水加盐煮沸，下面条。时间随种类厚度而定，通常 8 到 12 分钟，可尝一小段判断是否达到“al dente”。
- 备酱：同步准备喜爱的酱料，如番茄酱、奶油酱、蒜蓉橄榄油等。
- 拌合：面煮好后滤水，保留少量面汤。将面与酱混合，用面汤调节浓稠度。
- 调味装盘：按口味加入香草、奶酪或橄榄油，最后撒奶酪和香草点缀。

这只是基础做法，可按个人喜好加入蔬菜、肉类、海鲜或更换酱料。

# 图2-26 使用Prompt Perfect后的回答

若想确认提示词是否被改写，可在“我想知道如何烹饪任意意大利面 perfect”后追加一句：“回答前先把 perfect 插件改写后的提示词给我”，即可验证效果。

# (三) KeyMate.AI Search

此前官方的 Browse with Bing 已关闭，但并不代表 ChatGPT 完全离线。用第三方插件 KeyMate.AI Search 仍能让模型联网。

[PRE9]

KeyMate.AI Search

Search & Browse the web by using Google

Search results with KeyMate.Al, your AI-powered web crawler.

# 图2-27 KeyMate.AI Search插件

使用前先在插件商店找到并下载。点击下载后会跳转到官网，需要用 Google 账号登录并授权，授权成功后插件会出现在列表中。

使用时无需手动激活，直接在 GPT-4 对话框提问即可。模型会自行判断是否需要联网搜索，并把结果用于作答。

[PAGE 55]

# 第二章 使用ChatGPT前的准备 043

查询“今日广州的台风天气预报”时，模型自动调用了插件：

[PRE10]

请查询今日广州的台风天气预报

[PRE11]

Using KeyMate.AI Search...

# 图2-28 调用KeyMate.AI Search过程

检索完成后开始作答：

[PRE12]

请查询今日广州的台风天气预报

[PRE13]

Used KeyMate.AI Search

根据最新信息，2023 年 7 月 16 日广州受台风影响，预计有强风大雨，请注意防范尽量避免外出。可能伴随暴雨、雷电和强风。广州市政府已发布台风预警，建议市民留在家中并关注最新预报。

以上信息来源于这里，情况会随台风变化，请持续关注。

[PRE14]

# 图2-29 调用插件后给出答案

可点击答案中的“这里”验证来源。也可点击“Used KeyMate.AI Search”查看插件执行细节：

[PAGE 56]

[PRE15]

[PRE16]

# 图2-30 查看KeyMate.AI Search运行记录

# 第四节 配合Bing、Claude处理不同场景

ChatGPT 表现强劲，但 BingAI、Claude 等工具同样值得关注。

# 一、BingAI

BingAI，即 New Bing，是微软的搜索增强版。ChatGPT 爆火后不久微软便上线该产品，基于 GPT-4 模型并保持实时联网，相比 ChatGPT 姗姗来迟又下架的浏览器插件更受欢迎。

# (一)注册

通过搜索引擎页面的“聊天”入口访问。至少满足：

+   

[PRE17]

图2-31 在Microsoft Edge浏览器打开bing.com

2.点击页面右上角“登录”，用 Microsoft 账户注册或登录即可使用 BingAI。

[PAGE 58]

# 046 || 法律人ChatGPT应用指南

[PRE18]

# 图2-32 注册或登录Microsoft账户

注册流程较为简单，读者可按微软指引操作。

[PRE19]

# 图2-33 账户注册登录页

注意，若无法在页面找到“聊天”或“Chat”按钮，可在个人设置中修改“国家/地区”，刷新后即可正常使用。

[PAGE 59]

# (二)使用

BingAI 有两种交互方式。一种是在搜索页向下滚动出现“聊天”界面：

[PRE20]

# 图2-34 BingAI交互页

另一种是点击浏览器右上角图标打开侧边栏：

[PRE21]

# 图2-35 右上角图标调出交互页

[PAGE 60]

两种模式下都提供三种对话风格：“更有创造力”、“更平衡”、“更精确”，由用户自选。侧边栏模式可同步读取当前网页内容，按网页信息执行指令，准确性和效率更高，后续章节会结合场景介绍技巧。

# (三)BingAI的特点

优点与缺点都很突出。它擅长搜索相关任务，但遇到无法处理的提问时，通常会提示“是时候开启一个新主题了”，强制结束当前话题。

# 二、Claude

由人工智能初创公司 Anthropic 发表的产品，使用体验与 ChatGPT 相近。一个优势是内网直连，无需翻墙访问。

# (一)注册

1. 访问 slack官网 slack.com，点击“创建一个新的工作区”。