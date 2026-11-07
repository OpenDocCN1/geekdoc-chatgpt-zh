发起请求后，Agent 会在后台自动抓取目标网页，调用 MCP 工具把原始内容同步到 NotebookLM，同时生成摘要并写入笔记。几秒之内，机器人就会回复“已成功将 DeepSeek API 计费标准保存到笔记”，完整的操作演示见图 14-3。

[PRE0]
图 14-3 飞书发送知识收集请求

从触发到入库全程无需离开飞书，采集效率因此大幅提升。采集完成的资料会在 NotebookLM 中留下痕迹，后续打开即可查看笔记列表和具体正文，对应图 14-4 和图 14-5。

[PRE1]
图 14-4 NotebookLM 中创建的笔记

[PRE2]
图 14-5 NotebookLM 笔记内容详情

如果之后记不清某份文档的细节，直接在飞书里向机器人提问即可。依托这套机制，你能沉淀出容量几乎无限且长期留存的专属知识库，同时避免让大模型反复处理长文档所带来的 Token 成本。

## 14.5 总结

本章聚焦 OpenClaw 与外部优质 AI 服务的协同方案。利用 MCP 协议，我们将 Google NotebookLM 接入 OpenClaw，弥补了本地 Agent 在超长文本解析和低成本知识存储方面的短板。内容涵盖 MCP Server 的安装与配置，以及在 SOUL.md 中设置 Agent 优先查询外部知识库的规则逻辑，并以飞书搭建起简洁的信息输入-智能检索闭环。

至此，本书第五篇的五个核心实战场景已全部展开，依次涉及公众号自动化运营、小红书文案自动发布与运营、多智能体协作开发、开发者工具链自动化，以及数字第二大脑的构建。希望这些案例能为你在实际工作中运用 OpenClaw 提供灵感与坚实的技术支撑。

配套资源验证码 260925

*文中代码字体版权说明 ^(¹)

* * *

1.  1.  Copyright Copyright 2010, 2012 Adobe Systems Incorporated (http://www.adobe.com/), with Reserved Font Name ‘Source’. License This Font Software is licensed under the SIL Open Font License, Version 1.1. This license is copied below, and is also available with a FAQ at: http://scripts.sil.org/OFL SIL OPEN FONT LICENSE Version 1.1 - 26 February 2007 PREAMBLE The goals of the Open Font License (OFL) are to stimulate worldwide development of collaborative font projects, to support the font creation efforts of academic and linguistic communities, and to provide a free and open framework in which fonts may be shared and improved in partnership with others. The OFL allows the licensed fonts to be used, studied, modified and redistributed freely as long as they are not sold by themselves. The fonts, including any derivative works, can be bundled, embedded, redistributed and/or sold with any software provided that any reserved names are not used by derivative works. The fonts and derivatives, however, cannot be released under any other type of license. The requirement for fonts to remain under this license does not apply to any document created using the fonts or their derivatives. DEFINITIONS "Font Software" refers to the set of files released by the Copyright Holder(s) under this license and clearly marked as such. This may include source files, build scripts and documentation. "Reserved Font Name" refers to any names specified as such after the copyright statement(s). "Original Version" refers to the collection of Font Software components as distributed by the Copyright Holder(s). "Modified Version" refers to any derivative made by adding to, deleting, or substituting â€” in part or in whole â€” any of the components of the Original Version, by changing formats or by porting the Font Software to a new environment. "Author" refers to any designer, engineer, programmer, technical writer or other person who contributed to the Font Software. PERMISSION & CONDITIONS Permission is hereby granted, free of charge, to any person obtaining a copy of the Font Software, to use, study, copy, merge, embed, modify, redistribute, and sell modified and unmodified copies of the Font Software, subject to the following conditions: 1) Neither the Font Software nor any of its individual components, in Original or Modified Versions, may be sold by itself. 2) Original or Modified Versions of the Font Software may be bundled, redistributed and/or sold with any software, provided that each copy contains the above copyright notice and this license. These can be included either as stand-alone text files, human-readable headers or in the appropriate machine-readable metadata fields within text or binary files as long as those fields can be easily viewed by the user. 3) No Modified Version of the Font Software may use the Reserved Font Name(s) unless explicit written permission is granted by the corresponding Copyright Holder. This restriction only applies to the primary font name as presented to the users. 4) The name(s) of the Copyright Holder(s) or the Author(s) of the Font Software shall not be used to promote, endorse or advertise any Modified Version, except to acknowledge the contribution(s) of the Copyright Holder(s) and the Author(s) or with their explicit written permission. 5) The Font Software, modified or unmodified, in part or in whole, must be distributed entirely under this license, and must not be distributed under any other license. The requirement for fonts to remain under this license does not apply to any document created using the Font Software. TERMINATION This license becomes null and void if any of the above conditions are not met. DISCLAIMER THE FONT SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO ANY WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT OF COPYRIGHT, PATENT, TRADEMARK, OR OTHER RIGHT. IN NO EVENT SHALL THE COPYRIGHT HOLDER BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, INCLUDING ANY GENERAL, SPECIAL, INDIRECT, INCIDENTAL, OR CONSEQUENTIAL DAMAGES, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF THE USE OR INABILITY TO USE THE FONT SOFTWARE OR FROM OTHER DEALINGS IN THE FONT SOFTWARE.↩︎