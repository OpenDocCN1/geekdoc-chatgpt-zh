# 检索增强 ChatGPT：原理与冰壶案例拆解

在检索增强的思路里，模型本身不必记住所有事实，只要能在提问时把相关外部文本找来、喂给模型即可。下面以一个可自动化的 Ask 函数为例，说明检索如何与 GPT 结合，以及在真实问答中的表现。

## Ask 函数的核心流程

可以封装一个名为 ask 的调用，负责完成检索到回答的闭环，具体分为五步：

- 接收用户输入的查询字符串；
- 根据该查询检索与之相关的文本；
- 把检索到的文本拼接到发给 GPT 的提示消息中；
- 将拼接后的消息发送给 GPT；
- 把 GPT 的回复原样返回。

把搜索与问答串联起来后，就能批量应对各种问题。先检索相关材料再让模型阅读，模型对问题的理解会更充分，答案的准确度也会提升。尤其在处理非公开或时效性强的主题时，这种先找证据再推理的方式，能显著降低幻觉。

## 冰壶金牌问答的实测

下面用 2022 年冬奥会冰壶金牌的问题来演示，示例代码如下：

[PRE0]

我们问 “哪位运动员在 2022 年冬奥会冰壶比赛中获得金牌？”，示例代码如下：

[PRE1]

运行结果显示，在 2022 年冬奥会冰壶项目中出现了两支金牌队伍：男子组由瑞典队夺得，成员为尼克拉斯·埃丁、奥斯卡·埃里克森、拉斯穆斯·沃兰弗、克里斯托弗·桑德格伦和丹尼尔·马格努松；女子组由英国队夺得，成员为伊芙·穆尔黑德、维姬·赖特、詹妮弗·多兹、海莉·达夫和米莉·史密斯。

There were two gold medal-winning teams in curling at the 2022 Winter Olympics: the Swedish men's team consisting of Niklas Edin, Oskar Eriksson, Rasmus Wranå, Christoffer Sundgren, and Daniel Magnusson, and the British women's team consisting of Eve Muirhead, Vicky Wright, Jennifer Dodds, Hailey Duff, and Mili Smith.

虽然 gpt-3.5 turbo 本身并不掌握 2022 年冬奥会的信息，但检索系统能把参考文本取出来供模型阅读，使得男子和女子金牌得主被正确列出。不过效果仍不完美，混合双打项目的金牌得主并未被提及。

## 如何定位错误原因

要判断失误来自检索不全还是推理不可靠，可以打开参数 print_message=True 查看模型实际看到的完整提示，示例代码如下：

[PRE2]

以上代码的运行结果如下：

https://openaipublic.blob.core.windows.net/encodings/cl100k_base.tiktoken

Use the below articles on the 2022 Winter Olympics to answer the subsequent question. If the answer cannot be found in the articles, write "I could not find an answer."

Wikipedia article section:

"""

List of 2022 Winter Olympics medal winners

==Curling==

{{main|Curling at the 2022 Winter Olympics}}

{{MedalistTable| type = Event | columns = 1 | width = 225 | labelwidth = 200}}

|-valign = "top"

|Men<br/>{{DetailsLink|Curling at the 2022 Winter Olympics - Men's tournament}}

|{{flagIOC| SWE| 2022 Winter}}<br/>[[Niklas Edin]]<br/>[[Oskar Eriksson]]<br/>[[Rasmus Wranå]]<br/>[[Christoffer Sundgren]]<br/>[[Daniel Magnusson (curler)|Daniel Magnusson]]

|{{flagIOC|GBR|2022 Winter}}<br/>[[Bruce Mouat]]<br/>[[Grant Hardie]]<br/>[[Bobby Lammie]]<br/>[[Hammy McMillan Jr.]]<br/>[[Ross Whyte]]

|{{flagIOC| CAN| 2022 Winter}}<br/>[[Brad Gushue]]<br/>[[Mark Nichols (curler)|Mark Nichols]]<br/>[[Brett Gallant]]<br/>[[Geoff Walker (curler)|Geoff Walker]]<br/>[[Marc Kennedy]]

|-valign = "top"

|Women<br/>{{DetailsLink|Curling at the 2022 Winter Olympics - Women's tournament}}

|{{flagIOC| GBR| 2022 Winter}}<br/>[[Eve Muirhead]]<br/>[[Vicky Wright]]<br/>[[Jennifer Dodds]]<br/>[[Hailey Duff]]<br/>[[Mili Smith]]

|{{flagIOC|JPN|2022 Winter}}<br/>[[Satsuki Fujisawa]]<br/>[[Chinami Yoshida]]<br/>[[Yumi Suzuki]]<br/>[[Yurika Yoshida]]<br/>[[Kotomi Ishizaki]]

|{{flagIOC|SWE|2022 Winter}}<br/>[[Anna Hasselborg]]<br/>[[Sara McManus]]<br/>[[Agnes Knochenhauer]]<br/>[[Sofia Mabergs]]<br/>[[Johanna Heldin]]

|- valign = "top"

|Mixed doubles<br/>{{DetailsLink|Curling at the 2022 Winter Olympics - Mixed doubles tournament}}

|{{flagIOC|ITA|2022 Winter}}<br/>[[Stefania Constantini]]<br/>[[Amos Mosaner]]

|{{flagIOC|NOR|2022 Winter}}<br/>[[Kristin Skaslien]]<br/>[[Magnus Nedregotten]]

|{{flagIOC|SWE|2022 Winter}}<br/>[[Almida de Val]]<br/>[[Oskar Eriksson]]

|}

"""

Wikipedia article section:

"""

Curling at the 2022 Winter Olympics

== Results summary ==

=== Women's tournament ===

==== Playoffs ====

===== Gold medal game =====

"Sunday, 20 February, 9:05"

{{#lst:Curling at the 2022 Winter Olympics - Women's tournament|GM}}

{{Player percentages

| team1 = {{flagIOC|JPN|2022 Winter}}

| [[Yurika Yoshida]] | 97 %

| [[Yumi Suzuki]] | 82 %

| [[Chinami Yoshida]] | 64 %

| [[Satsuki Fujisawa]] | 69 %

| teampct1 = 78 %

| team2 = {{flagIOC|GBR|2022 Winter}}

| [[Hailey Duff]] | 90 %

| [[Jennifer Dodds]] | 89 %

| [[Vicky Wright]] | 89 %

| [[Eve Muirhead]] | 88 %

| teampct2 = 89 %

}}

"""

Wikipedia article section:

"""

Curling at the 2022 Winter Olympics

== Medal summary ==

=== Medal table ===

{{Medals table

| caption       =

| host          =

| flag_template = flagIOC

| event         = 2022 Winter

| team          =

| gold_CAN = 0 | silver_CAN = 0 | bronze_CAN = 1

| gold_ITA = 1 | silver_ITA = 0 | bronze_ITA = 0

| gold_NOR = 0 | silver_NOR = 1 | bronze_NOR = 0

| gold_SWE = 1 | silver_SWE = 0 | bronze_SWE = 2

| gold_GBR = 1 | silver_GBR = 1 | bronze_GBR = 0

| gold_JPN = 0 | silver_JPN = 1 | bronze_JPN = 0

}}

"""

Wikipedia article section:

"""

Curling at the 2022 Winter Olympics

== Results summary ==

=== Men's tournament ===

==== Playoffs ====

===== Gold medal game =====

"Saturday, 19 February, 14:50"

{{#lst:Curling at the 2022 Winter Olympics – Men's tournament|GM}}

{{Player percentages

| team1 = {{flagIOC|GBR|2022 Winter}}

| [[Hammy McMillan Jr.]] | 95 %

| [[Bobby Lammie]] | 80 %

| [[Grant Hardie]] | 94 %

| [[Bruce Mouat]] | 89 %

| teampct1 = 90 %

| team2 = {{flagIOC|SWE|2022 Winter}}

| [[Christoffer Sundgren]] | 99 %

| [[Rasmus Wranå]] | 95 %

| [[Oskar Eriksson]] | 93 %

| [[Niklas Edin]] | 87 %

| teampct2 = 94 %

}}

"""

Wikipedia article section:

"""

Curling at the 2022 Winter Olympics

== Medal summary ==

===Medalists===

{| {{MedalistTable|type = Event|columns = 1}}

|-

|Men<br/>{{DetailsLink|Curling at the 2022 Winter Olympics - Men's tournament}}

|{{flagIOC|SWE|2022 Winter}}<br>[[Niklas Edin]]<br>[[Oskar Eriksson]]<br>[[Rasmus Wranå]]<br>[[Christoffer Sundgren]]<br>[[Daniel Magnusson (curler)|Daniel Magnusson]]

|{{flagIOC|GBR|2022 Winter}}<br>[[Bruce Mouat]]<br>[[Grant Hardie]]<br>[[Bobby Lammie]]<br>[[Hammy McMillan Jr.]]<br>[[Ross Whyte]]