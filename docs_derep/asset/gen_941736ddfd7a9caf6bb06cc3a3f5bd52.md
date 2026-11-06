Midjourney进阶参数全解与ChatGPT协同创作实战

先看一个实操例子：
提示词：illustration of a man in the stranger in moscow video clip in the style of the french cómic artist Jean giraud also know as moebius, surreal ambient, Rain, Russian architecture.--ar 4 : 7

图5-45 不同的风格化参数

把风格化强度从0拉到200，画面精致度会明显上扬。可这并不等于数值越大越好。拉得过高会让细节堆到冗余，同时画面与原提示词的贴合度也会走低，这点在图5-46里能看得清楚。

图5-46 高风格化参数的生成结果

一旦用到--stylize 500，不必要的元素就开始冒头，比如过量的光源。到了1000，建筑造型会发生根本性偏移，已经认不出是最初想要的场景了。所以日常出图默认的100通常就够用了，只在画面有些许粗糙时才去调用--stylize。想提质感就适当上调，画面出现过度装饰就往下调。

#### 5.5.13 平铺拼贴参数 --tile 的妙用
“--tile”算是一个很有趣的开关，用它生成的图可以实现无缝拼接，直接看例子。

提示词：a pattern of colorful watercolor fall leaves--tile

图5-47 平铺样图

挑一张图像贴瓷砖那样重复排列，效果如图5-48，拼起来完全不露痕。靠这个参数能做很漂亮的印花、网页背景、桌面壁纸或电子书底纹。值得注意的是，“--tile”在所有模型版本上都通用。

图5-48 平铺拼贴的效果

#### 5.5.14 模型版本选择参数 --version
通过“--version <版本号>”或简写“--v <版本号>”可以切换绘图所用的模型。Midjourney会周期性上线新模型，更新后在连贯性、效率、画质和风格表现上都有提升，但老版本并未失去价值，每个版本都有独特的味道，这也是平台保留多版本的原因。

当前主流默认是2024年7月发布的V6.1。--v 可选的取值有1、2、3、4、5、5.0、5.1、5.2、6和6.1，各自对应不同世代。

Niji模型是Midjourney与Spellbrush联合打造，擅长动漫和插画类画面，自带丰富的动漫知识库和美学体系，在动态镜头和角色主体构图上表现突出。启用方式是在提示词末尾加上“--niji 6”。

用同一条简单提示做对比：
提示词

生动的郁金香。--ar 4:3

vibrant tulips. --ar 4:3

图5-49 模型生成风格对比

V6.1在画面连贯性、细节纹理和生成速度上更胜一筹，比V6.0快约25%。V6.0则在长文本理解、知识库覆盖和图像混合能力上做了优化。Niji 6更偏向漫画气质。再看一组高度抽象的概念对比：

提示词

高对比度超现实拼贴。

high contrast surreal collage.

图5-50 抽象绘画风格对比

#### 5.5.15 生成过程视频参数 --video
加上“--video”就能把整段生成过程导出成短视频，参数放在提示词最后即可。下面是获取视频的路径。

图5-51

图成后点对话区右侧的笑脸图标，再点邮件标识，Midjourney Bot会私信发来下载链接。随后点界面左上角的私信按钮，进入Midjourney Bot的私信就能找到链接，详见图5-52。

视频时长大约4秒，能完整呈现从噪点到成图的演变，单帧视觉与--stop的效果相近，只是被动态化了。

图5-52

提示词：Tilt shift Photography of Peking buildings, aerial angle.--ar 4 : 3--video

图5-53 图像生成动画

#### 5.5.16 怪异度参数 --weird
怪异度控制参数“--weird <0～3000>”，简写为“--w <0～3000>”，默认值为0。

如果说--stylize是往传统美感上加分，那么--weird就是反向操作，会让画面变得灵异、暗黑，形成一种另类的怪异美学。哪怕提示词里没写，只要加上--w，构图和风格也会变得奇诡。

图5-54

提示词：Three puppies playing.

图5-54 怪异度参数不同取值带来的变化

想做暗黑主题，仅靠提示词往往做不到，这时--weird就派上用场。实践中常与--stylize搭配使用，既保持暗黑气质又不失艺术度。下面用哥特场景做验证：

提示词：Depict the ballroom scene from Edgar Allan Poe's'The Masque of the RedDeath'with everyone wearing bizarre masks, the setting filled with shades of red.Deathstands next to a grand clock, gazing at the attendees.In the background, the lighting isdim.4k

图5-55 暗黑风格作品的绘制

为方便记忆，把三个常用参数整理如下：

表5-4 三个参数对比

| 对比项目 | --stylize | --chaos | --weird |
| :--- | :--- | :--- | :--- |
| 名称 | 风格化参数 | 混沌度参数 | 怪异度参数 |
| 缩写 | --s | --c | --w |
| 取值范围 | 0～1000 | 0～100 | 0～3000 |
| 初始默认值 | 100 | 0 | 0 |
| 功能 | 控制风格化程度与细节精细度 | 控制绘画的多样性和随机性 | 控制绘画的怪异程度 |

## 第6章
ChatGPT联动Midjourney，让创作效率翻倍

我们已经熟悉AI写文和AI画图的用法，都是靠文字驱动。那能不能把两者串起来，让文本模型先把思路整理好再交给绘图模型？本章讲的就是文生文和文生图结合的实战方法。

### 6.1 文本生成遇到图像生成
用Midjourney绘图最卡人的点，往往是把脑中画面翻译成地道的英文。翻译工具能改单词，却处理不了语法的自然度。相较之下，大语言模型在四个层面做到了超越。

#### 6.1.1 易用性
ChatGPT、通义千问这类模型能和人自然对话，可用性远高于传统工具。它还能主动修正提示词里的错误，这种交互是翻译软件不具备的。

#### 6.1.2 段落级翻译能力
这再次体现了易用性。提示词不用写得多严谨，只要固定好输出格式，模型就能把零散的描述整理成通顺的英文。对上下文的理解也更到位。

#### 6.1.3 个性化格式输出
大模型基于海量文本预训练，可以先喂入特定格式的样例，让它以后按固定格式返回翻译结果。

#### 6.1.4 提示词扩写能力
模型收到提示词后会自行补全细节，这种扩写既是优势也是风险。我们可以先让它扩写、展示，再人工把控方向，避免失控。

### 6.2 翻译与扩写实操
从最基础的翻译开始。

问：请将下面的提示词翻译为英文：
3/4侧面镜头，现代中国美女在上海购物中心，年轻女性时尚外观，浅景深，高对比度。

ChatGPT: The translation of your prompt into English is:
“3/4 profile shot, modern Chinese beauty in a Shanghai shopping mall, young woman with a stylish appearance, shallow depth of field, high contrast.”

当灵感还零碎时，也可以让模型先梳理。

问：我正在使用绘画AI进行创作，现在我有一些零碎的灵感，请你帮我将这些灵感进行整理并填充得更加丰满，使之能直接用于绘画AI的作品生成：
一只小狗站在巨大的废墟之中，这片废墟兼具未来感与破败感。画面中要有一些零星的光亮，要体现出小狗的孤独感，废墟中还要有巨大的机体残骸。

ChatGPT给出的扩写包含场景设定、主体构成、光源设定和细节丰富化，把末日废墟与高科技残骸结合，并用光影强化孤独感。

扩写后需要微调，再让模型整理成英文。

问：小狗应该背对着我们，处于画面的中央，并且要显得比较渺小，其余的内容就按照你的思路进行创作。现在请你将其整理为一句简洁凝练的提示词，使用英文输出。

ChatGPT: Here’s a concise prompt in English that captures all the essential details for your AI painting creation:
“A small, gray dog stands alone in the center of a vast, futuristic and dilapidated ruins, back facing us, appearing tiny against the backdrop of giant mechanical debris and scattered light from broken structures and dimly glowing tech remnants.”

这条提示可用，但结构并不符合Midjourney的规范，很多可控信息缺失。如何让模型输出更专业的写法？下一节给出解法。

### 6.3 教会ChatGPT Midjourney语法
文本大模型可以进行预训练，只要把Midjourney的提示词结构规则喂进去，它就能按规范输出。我们把之前总结的语法框架输入模型，告诉它以后严格按此结构生成，允许它根据知识储备补充艺术流派、光线等细节。

ChatGPT确认记忆后，上下文窗口约两万字，足够支撑一次完整的绘图任务。对话容量接近上限时最好重新训练。

把上一节的提示词交给训练后的模型改造：

问：请你将下面的提示词进行一些更改，使其符合标准的绘画提示词格式，其中所涉及的具体设置请按照你的知识储备进行选择，提示词输出为英文：
“A small, gray dog stands alone in the center of a vast, futuristic and dilapidated ruins, back facing us, appearing tiny against the backdrop of giant mechanical debris and scattered light from broken structures and dimly glowing tech remnants.”

ChatGPT: Here’s a concise and structured prompt for your AI painting model, crafted in English:“A small, gray dog, appearing tiny and alone, stands with its back to us in the center of vast, futuristic ruins.The scene blends post-modern, futurist illustration styles with scattered light from broken structures and dimly glowing backdrop of giant mechanical debris.Kodak Portra 800, 50mm f2.0, 8K.”

用这条提示生成最终效果，见图6-1。

### 6.4 创作实战：短视频插画风格
插画起初服务书籍，后随广告营销进入商业化，如今广泛应用于网站、社交媒体、游戏等数字场景，成为高效的视觉传达手段。短视频中精美的插画能瞬间抓住注意力。本节介绍三种适配短视频的风格及关键词。

#### 6.4.1 木刻版画风格插画创作
木刻版画是经典表现形式，图案刻在木板上通过压印成图，呈现独特纹理和质感。关键词可用“woodcut print”。

把主题交给已训练的模型：

问：根据你所记忆的提示词生成规则，请你将下面的内容整理为完整的提示词，输出为英文：
黑夜里，福尔摩斯独自走在昏暗的贝克街上，木刻版画风格（woodcut print）。

ChatGPT: Here’s a concise and structured prompt for your AI painting model in English:
“Sherlock Holmes walks alone on dimly lit Baker Street at night, depicted in a Victorian-era woodcut print style. The scene features atmospheric street lighting with Holmes in his iconic deerstalker and long coat. Kodak Tri-X 400, 50mm f1.4, 3:2, 4K.”

把提示词丢进Midjourney，生成结果如图6-2。