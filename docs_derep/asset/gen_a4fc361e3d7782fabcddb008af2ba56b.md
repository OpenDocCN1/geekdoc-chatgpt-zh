在主机环境里，为保护私密数据目录的访问权限，可以通过 chmod 700 来做强制限制：

sudo chmod 700 /path/to/your/workspace

五、技能来源审查
在安装新技能之前，先执行信息查询指令：

openclaw skills info

随后打开 ~/.openclaw/skills//SKILL.md 文件进行人工核查，重点排查是否包含恶意操作，例如 curl、bash 等命令。建议优先使用官方内置的 55 个 Skill，或参考社区维护的精选合集 awesome-openclaw-skills。

六、定期安全检查
养成周期性执行安全审计的习惯：

openclaw security audit

审计结果一旦提示风险，例如网关鉴权泄露、浏览器控制端口暴露等情况，请依据前文的安全基线、配置参考以及官方文档及时修复。

七、版本升级
保持工具处于最新状态，执行更新指令即可：

openclaw update

八、完整卸载
打开终端运行卸载命令：

openclaw uninstall

按上下方向键移动光标，用空格键勾选全部选项后回车确认。随后选择 yes 并回车，系统会自动清除 OpenClaw 的工作目录。

全局包的移除方式根据安装工具不同：
1. 通过 npm 安装的用户执行：
npm rm -g openclaw
2. 通过 pnpm 安装的用户执行：
pnpm remove -g openclaw
3. 通过 bun 安装的用户执行：
bun remove -g openclaw

# 附录 B
# 资源领取指引
本书为读者准备了 4 份 OpenClaw 实操配套资料，扫描封底二维码关注公众号，发送关键词“龙虾”即可获取。

1. OpenClaw 常用 Skill 推荐清单
一份按使用场景梳理的常用 Skill 汇总，方便按需挑选、快速匹配工作需求。

2. OpenClaw 常见报错速查手册
覆盖全流程高频报错的排查指南，遇到程序异常时可直接定位条目获取标准化处理方法。

3. OpenClaw 本地部署图文与视频教程
提供文字版部署流程与视频演示双版本，兼顾逻辑梳理与同步实操，帮助顺利完成本地环境搭建。

4. OpenClaw 接入飞书图文与视频教程
详解 OpenClaw 与飞书平台对接的全流程，文字教程帮助理清关键节点，视频教程支持上手实操，快速实现功能联动。

扫描下方二维码，回复“龙虾”，即可领取
1. OpenClaw 常用 Skill 推荐清单
2. OpenClaw 常见报错速查手册
3. OpenClaw 本地部署文字教程与视频教程
4. OpenClaw 接入飞书文字教程与视频教程

[PRE0]

*文中代码字体版权说明 ^(¹)

* * *

1.  1.  Copyright Copyright 2010, 2012 Adobe Systems Incorporated (http://www.adobe.com/), with Reserved Font Name ‘Source’. License This Font Software is licensed under the SIL Open Font License, Version 1.1. This license is copied below, and is also available with a FAQ at: http://scripts.sil.org/OFL SIL OPEN FONT LICENSE Version 1.1 - 26 February 2007 PREAMBLE The goals of the Open Font License (OFL) are to stimulate worldwide development of collaborative font projects, to support the font creation efforts of academic and linguistic communities, and to provide a free and open framework in which fonts may be shared and improved in partnership with others. The OFL allows the licensed fonts to be used, studied, modified and redistributed freely as long as they are not sold by themselves. The fonts, including any derivative works, can be bundled, embedded, redistributed and/or sold with any software provided that any reserved names are not used by derivative works. The fonts and derivatives, however, cannot be released under any other type of license. The requirement for fonts to remain under this license does not apply to any document created using the fonts or their derivatives. DEFINITIONS "Font Software" refers to the set of files released by the Copyright Holder(s) under this license and clearly marked as such. This may include source files, build scripts and documentation. "Reserved Font Name" refers to any names specified as such after the copyright statement(s). "Original Version" refers to the collection of Font Software components as distributed by the Copyright Holder(s). "Modified Version" refers to any derivative made by adding to, deleting, or substituting â€” in part or in whole â€” any of the components of the Original Version, by changing formats or by porting the Font Software to a new environment. "Author" refers to any designer, engineer, programmer, technical writer or other person who contributed to the Font Software. PERMISSION & CONDITIONS Permission is hereby granted, free of charge, to any person obtaining a copy of the Font Software, to use, study, copy, merge, embed, modify, redistribute, and sell modified and unmodified copies of the Font Software, subject to the following conditions: 1) Neither the Font Software nor any of its individual components, in Original or Modified Versions, may be sold by itself. 2) Original or Modified Versions of the Font Software may be bundled, redistributed and/or sold with any software, provided that each copy contains the above copyright notice and this license. These can be included either as stand-alone text files, human-readable headers or in the appropriate machine-readable metadata fields within text or binary files as long as those fields can be easily viewed by the user. 3) No Modified Version of the Font Software may use the Reserved Font Name(s) unless explicit written permission is granted by the corresponding Copyright Holder. This restriction only applies to the primary font name as presented to the users. 4) The name(s) of the Copyright Holder(s) or the Author(s) of the Font Software shall not be used to promote, endorse or advertise any Modified Version, except to acknowledge the contribution(s) of the Copyright Holder(s) and the Author(s) or with their explicit written permission. 5) The Font Software, modified or unmodified, in part or in whole, must be distributed entirely under this license, and must not be distributed under any other type of license. The requirement for fonts to remain under this license does not apply to any document created using the Font Software. TERMINATION This license becomes null and void if any of the above conditions are not met. DISCLAIMER THE FONT SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO ANY WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT OF COPYRIGHT, PATENT, TRADEMARK, OR OTHER RIGHT. IN NO EVENT SHALL THE COPYRIGHT HOLDER BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, INCLUDING ANY GENERAL, SPECIAL, INDIRECT, INCIDENTAL, OR CONSEQUENTIAL DAMAGES, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF THE USE OR INABILITY TO USE THE FONT SOFTWARE OR FROM OTHER DEALINGS IN THE FONT SOFTWARE.↩︎