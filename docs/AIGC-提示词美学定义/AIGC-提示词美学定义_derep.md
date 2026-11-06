# AIGC提示词美学实战

## 内容速览

这本手册聚焦生成式人工智能产出物的美学塑造路径，试图从审美定义的视角，给创作者一套可落地的审美要素、风格要素和应用方向要素的指引。随着AIGC技术加速迭代，本书反复强调，工具本身只是手段，能否产出高质高阶的作品，关键在于对美的深度认知与高水准的创意思考。培养精准的美学定义能力，才能让AI生成更具水准、更富层次的优质画面。

全书共八章。第一章概览AIGC的基本概念与演进脉络，第二章至第八章分别围绕光、材质、艺术流派、插画、游戏、女性之美、流行趋势七个维度，系统示范在用AIGC生成图像时如何拆解审美要素、锁定风格要素、明确应用方向要素。设计师、艺术家以及所有对AIGC艺术创作感兴趣的创意从业者，都能从中获得参考。

## 序

生成式人工智能并非近几年才出现的概念，早在二十世纪五十年代，学界就已启动相关探索。历经半个多世纪的积累，再叠加互联网海量影像的沉淀，AIGC近期进入爆发期，成为舆论热点。

每一次技术浪潮都伴随焦虑。有人担心大批岗位消失，有人则联想到从马车到汽车的转型——马车夫减少了，司机却出现了。2023年4月1日晚，我与两位师弟夜谈，其中师弟徐涵神秘地说，AIGC很可能催生“提示词工程师”这一新岗位。那晚话题很多，我却未意识到AIGC与我的关联。

4月10日，师弟慈思远主理的设计社群“集创堂”举办“Design Tomorrow”分享会。印隽老师讲述AIGC的发展与他的实践尝试，我和师弟沈毅在台下小声交流：AIGC生成作品，是让设计彻底平权，还是依旧保持精英内核？听印隽老师讲到文生图时，我们认为门槛被拉低了，不懂设计的人也能通过文字描述得到漂亮画面；但当讲到提示词的精细与艺术性时，我们立刻达成共识：精英化并未消失，只是迁移了位置。专业性从手工制作转向了对画面的定义，设计师的角色从创意执行变为向AI下指令，实质上成了提示词工程师。

用提示词驱动AI创作，本质上是对画面进行美学定义。美学定义包含三大子任务：审美要素的定义、风格要素的定义、应用方向要素的定义。巧合的是，我过去二十多年的研究一直围绕消费者审美特征，帮助企业做品牌与产品的美学定义。工业设计的美学方法论能否平移到AIGC？我先让学生验证。我带八位同学对绘画、游戏、电影等艺术门类的画面风格进行归类，再拆解各自的色彩、光线、质感差异。打下基础后，我想用职业设计师来检验。2023年6月16-17日，我组织七位专业设计师以Midjourney和Stable Diffusion为工具实践这套方法，印隽老师全程参与。结果证明，这套方法论是可行的。

看到我朋友圈的测试图，人民邮电出版社的蒋艳老师鼓励我整理成书。随后，我与平面设计师于春雨用了两个月时间，完整复盘一次方法论，并制作了书中全部图像。在此过程中，我们反复试验提示词，对光、材质、艺术流派、插画、游戏、女性美、流行趋势等风格线索进行分类，检验不同表述的效果。

第一章主要讲用AIGC生成画面的基本原理。尽管尽量通俗，仍涉及一些专业术语与技术描述。若阅读有压力也可跳过，就像开车不必懂发动机也能享受驾驶。后续章节则以风格分类为骨架，展示如何用提示词指挥模型并附上案例。行文平实，偏重实验与实操。希望这些初步研究能为你产出更优雅的图像提供启发。

本书是我写作速度最快的一本。感谢师弟徐涵、沈毅提供灵感与勇气；感谢印隽老师在理论与实践上的引领；感谢早期参与的八位学生徐建华、金安安、魏堃、蒋依林、李朋卉、韦宇棒、蔡文浩、姜宇航，他们共同搭建了初步框架；感谢于春雨两个月的高强度制作；感谢团队成员孔莹、杜文锦、郭聪儿全程参与研究与写作，在AIGC场景下对我们美学定义能力进行了一次实战操练。最后感谢挚友陈蔚武老师全程指导图像生成与文字撰写，帮我们把控图文逻辑与美感。

希望这本书能给你带来一点启发。

傅炯
2024年1月9日

## 第一章
AIGC简介
AIGC Overview

### AIGC的定义

AIGC即人工智能生成内容，指借助AI技术自动产出内容。从广义看，它既是一种内容形态，也是一套内容生成技术集合。狭义上，AIGC是继PGC专业生产内容与UGC用户生成内容之后的第三种内容形态，特指由人工智能产出的内容；广义上，则涵盖基于生成算法、训练数据与算力芯片所驱动的文本、音乐、图像、代码、视频等多模态自动化生产技术体系。

### AIGC 的发展历程

AIGC根源可追溯至二十世纪五十年代，2022年起相关产品集中面世，引发社会广泛讨论。

#### 1.技术萌芽期（20世纪50年代至90年代）

五十年代起，AIGC初现端倪。莱杰伦·希勒与伦纳德·艾萨克森完成了首部计算机创作的音乐《伊利亚克组曲》。1966年，约瑟夫·魏岑鲍姆与肯尼斯·科尔比合作推出首个可对话机器人“伊莉莎”，通过关键词匹配与重组实现交互。八十年代中期，IBM依托隐马尔可夫模型推出语音打字机“坦戈拉”，可识别约两万个词汇。八十年代末至九十年代中，因成本高昂难以变现，各国削减AI投入，进展停滞。

#### 2.技术积累期（20世纪90年代至21世纪初）

九十年代到二十一世纪初，AIGC进入沉淀期，从实验走向实用，但受算法限制效果有限。转机出现在2006年，深度学习取得突破，GPU、TPU等算力设备提升，互联网数据规模爆发，为模型训练提供养料。2007年，纽约大学研究员罗斯·古德温通过AI记录公路旅行见闻，写出小说《1 The Road》，成为首部AI小说，象征意义大于可读性，存在拼写、空洞、逻辑问题。2012年，微软展示全自动同声传译系统，基于深度神经网络完成语音识别、翻译与合成的中文输出。

#### 3.技术拓展期（2010~2020年）

从2010年起，生成算法、预训练模型与多模态技术迭代，AI生成内容逼近人类水平。2014年，伊恩·古德费洛提出生成对抗网络，在AI生成领域开辟新路。2017年，基于注意力机制的Transformer架构出现，随后流式生成模型与扩散模型相继涌现。2017年，微软小冰发布诗集《阳光失了玻璃窗》，宣称百分百AI创作。2018年，英伟达推出的StyleGAN可自动生成图像，其第四代StyleGAN-XL产出的高分辨率图以假乱真。2019年，DeepMind发布DVD-GAN，可生成连续视频，在草地、广场等场景表现突出。

#### 4.技术爆发期（21世纪20年代至今）

算法成熟后，多款AIGC产品进入商业化，效果惊艳引发关注。2021年，OpenAI发布DALL·E，一年后推出DALL·E 2，支持文字到图像的高质量生成，涵盖卡通、写实、抽象等风格。2022年8月，Stability AI发布Stable Diffusion，为后续绘画模型奠基。同年3月，Midjourney首次公开，8月迭代至V3并引发热议，由其绘制的《太空歌剧院》在科罗拉多艺术博览会斩获数字艺术奖。2022年11月，OpenAI推出基于GPT-3.5与人类反馈强化学习的ChatGPT，两月日活达1300万。2023年2月7日，谷歌发布对话系统Bard，同日百度宣布大模型“文心一言”。全球范围内AIGC产品密集上线。

### 国内外生成式绘画工具

了解演进脉络后，本节简要介绍几款主流绘画工具，帮助读者快速判断特性并选择适合自己的起点。

#### 1.国外典型生成式绘画工具

- Midjourney
由同名实验室开发的文本生成图像程序，2022年7月12日开启公开测试，通过Discord机器人操作。学习成本低，对新手友好，风格选择丰富，输出艺术感强。但可调性有限，不支持外部插件，精细控制弱于Stable Diffusion。

- Stable Diffusion
2022年发布的开源文本生成图像模型，属于潜变量扩散模型范畴，由慕尼黑大学CompVis团队研发，Stability AI等推动。代码与权重开放，可在配备适度GPU的本地环境部署。除文生图外，还支持图生图、局部重绘、外扩、提示词反推等功能，插件生态丰富，可操作性强。

- DALL·E 3
OpenAI于2022年发布的文本生成图像程序，可与GPT大语言模型协同，通过自然语言对话不断微调画面。

- Imagen
谷歌推出的文本到图像模型，能生成高度贴合描述且接近照片真实感的图像。2023年10月更新后，用户可在搜索框直接生成图像，并在谷歌图片探索中通过“Create something new”按钮即时创图。

#### 2.国内典型生成式绘画工具

- 文心一格
基于百度文心大模型的艺术创作辅助平台，2022年8月19日上线。用户输入描述并选择方向、风格、尺寸即可生成画作，平台还能推荐风格并批量输出多种方案。

- 即时设计
提供AIGC作画插件，无美术基础的用户也能使用。打开“即时AI”后描述画面，结合基础图形、色彩与布局控制，即可快速得到结果。

- 神采PromeAI
主打草图渲染，可上传线稿生成建筑或室内效果图，提供五大场景模板，无需关键词与复杂参数即可上手。还支持图片转线稿，反向流程顺畅，出图迅速。

### 生成式绘画的底层技术逻辑

生成式绘画工具为何能产出令人惊叹或令人失望的画面？把握底层原理有助于更好地控制结果。以开源代表Stable Diffusion为例说明通用逻辑。该系统基于CLIP模型构建，融合扩散模型等组件。

可用“大厨”作比喻：Stable Diffusion像一名受训有素的大厨，用户输入的自然语言提示词是“食材”，文本编码器负责“备菜”，图像信息创建器负责“烹饪”，图像解码器负责“摆盘”，最终呈现成图。

核心在于扩散模型。它基于Transformer技术，通过不断向训练数据加入高斯噪声破坏数据，再训练模型逆向去噪恢复原图的机制学习分布。训练分为加噪过程与去噪过程：前者逐步加入噪声至数据不可辨，后者学习从噪声中重建图像。经海量预训练后，模型内化大量图像特征。用户输入提示词后，文本编码器将其转为机器可读表征，模型从噪声出发逐步去噪、调整，最终生成画面。整个生成过程本质上是一次受控的扩散与重建。

### AIGC行业发展展望

AIGC具备真实性、多样性、可控性与可组合性，能显著提升内容生产效率，为企业提供丰富可交互的内容。营销、零售、娱乐等数字化程度高且内容需求旺盛的领域有望率先迎来跃迁。

在营销端，AIGC可拓展创意边界，辅助信息收集、归纳与灵感生成，并基于品牌调性自动产出视觉物料，实现快速迭代。单向广告有望演变为双向互动场景，内容可根据用户反馈实时变化，为互动营销与个性化营销打开新空间。

在零售端，AIGC加速商品与卖场的3D化，可生成商品模型与材质质感，提升线上体验与转化率。基于视觉、语音、文本的生成技术还能低成本打造品牌虚拟IP，安全性与可控性高于真人代言，有助于拉近品牌与消费者距离，推动沉浸式多感官购物体验。

在娱乐端，AI换脸、风格化滤镜等功能满足用户猎奇与分享欲，提升参与感。用户可自主定义线上形象与角色，参与多场景共创。未来AIGC将进一步融合虚拟技术，推动虚拟商品经济成长。

除了前面已经聊到的细分赛道，生成式AI在教育、金融、医疗、工业等多个行业的落地节奏也在明显加快。

在教育里，AIGC为老师提供了更多元的表达手段，知识传递变得更直观、更生动，虚拟讲师有望进入公众视野，数字化的教学模式也在加快普及。在医疗里，AIGC可以提升影像质量并抽取基础图像信息，为医生诊断提供辅助，节省出更多用于临床治疗的时间精力，虚拟康复师等新岗位也有诞生的可能。在工业里，把AIGC嵌入设计软件能够推动自动化程度提升，削减大量重复、低价值且耗时的操作。生成式设计可以为工程师和设计师提供灵感供给，并引入丰富变化实现多状态动态模拟，最终拉高整体生产效率。

把焦点放到个体职业上。随着生成式AI能力在迭代中持续增强，内容创作门槛被不断拉低，个人创作能量得到释放，内容生产范式正经历深层重塑。ChatGPT、Midjourney 等代表性模型已趋成熟，做一张视觉作品的难度大幅下降，但要稳定产出高质量成果仍不容易。正因如此，专门负责和AI对话、提炼需求的角色——提示词工程师应运而生。

该岗位的核心是熟练驾驭各类生成工具，把复杂的业务目标拆解成模型可识别的语言表述，精准下达指令并依据反馈持续优化输出。市场上已有受益者。2022 年 11 月，硅谷的 Riley Goodside 靠自学摸索出的提示技巧加入 AI 独角兽 Scale AI，很可能成为业内首个被正式招聘的提示词工程师，其年薪据估算或超过人民币百万元。

对这一职业的判断目前分化明显。一方认为提示词工程师只是模型训练阶段的过渡性分工，随着系统自我完善会逐步淡出，未来人人皆需掌握与AI高效沟通的能力。另一方则视其为技术分层的必然阶段。生成式AI确实把图像、影视乃至 3D 建模的参与门槛拉低，无专业背景者也能快速产出作品，但要长期稳定输出高质高品的内容，对创作者的复合素养提出了更高要求。招聘市场的反馈也在印证这一点：大模型仍处演进期，不少企业对提示词工程师的编程素养抱有较高期待，认为优秀的使用者既要懂自然语言，也要懂编程语言。

不少尝试过生成绘图工具的创作者会发现，除运气因素外，扎实的知识储备同样关键。提示中加入焦距、光圈等摄影专业术语，往往能让画面立刻摆脱平庸。这说明即便在输入阶段，对摄影原理的理解也会直接影响生成效果。

在创意供给井喷的时代，创作者要保持竞争力需要具备哪些能力？

当前的主流生成式模型均基于海量预训练数据构建，提示词所能承载的信息维度远超个人直觉。优秀创作者不仅要了解摄影，还需对光学原理、材质特性、艺术技法、流派、题材乃至当下审美潮流都有基本涉猎。由此可见，在AIGC 普及的背景下，“技术高手”与“业务专家”的边界在逐渐模糊。技术、业务、学术、研究、个人品味等多项要素形成水桶效应，共同决定作品质量，也意味着面向AIGC的人才必须是跨界复合的高阶人才。

与此同时也要清醒认识到，尽管工具拉低了门槛，大多数生成式产出仍徘徊在视觉艺术的平均水准。因生成效率骤增，缺乏成熟的生成策略和对美学的定义能力支撑时，同质化与庸俗化很难避免。

当视觉产出的时间成本与人力成本趋近于零时，美学定义能力将成为个体创作者的核心竞争力。

美学定义即设计师对画面美学的把控与界定能力，主要包含三大维度：

- 审美要素界定：涵盖造型、色彩、光影、材质等。
- 风格要素界定：涉及艺术流派与流行趋势的定位。
- 应用场景界定：明确作品服务于插画、游戏等哪个方向。

本书将逐一展开三大要素的内涵，提出视觉细分思路，并以具体生成图像为案例，拆解每张图的美学定义逻辑，提供可直接参考的提示词，帮助读者在实操中落地。

全书以美学定义的视角梳理知识，拓展创作思路。这类系统化信息能提升读者对审美表达多样性的认知，避免陷入同质化陷阱，也为培养高水平的美学定义人才、产出审美精良的内容提供方法启发。

设计行业正从创意驱动转向定义驱动。生成式AI可提升团队沟通效率，厘清需求、锚定方向，大幅扩展方案可能性并加速视觉产出。具备优秀美学定义能力的设计师、营销人员和艺术创作者，借助生成式AI加持将在未来工作中更具优势。

## 第二章

## 光

### Light
光是决定画面氛围的关键要素，本章重点拆解光的使用逻辑，帮助创作者在AIGC中更精准地控制画面表现。

| 自然光 | 人工光 |
|--------|--------|
| 硬光 | 柔光 |
| 逆光 | 全侧光 | 面光 |
| 顶光 | 冷光 | 底光 | 暖光 |

光大致可分为自然光和人工光。自然光的强弱、色温与方向会随天气、季节、时间波动；人工光的强度、角度、色彩则可人为调控，稳定性与灵活性更高。

质感上可归为柔光与硬光。柔光为散射光，方向感弱，阴影不明显，画面对比度低，过渡柔和，层次细腻，色彩饱满，带来温暖亲切、舒适浪漫的观感。硬光为直射光，受光面与阴影区反差大，亮部突出、阴影浓重，画面立体感和冲击力强，呈现硬朗粗犷、鲜明有力的气质。

按入射方向可分为面光、全侧光、逆光、顶光、底光。面光正面均匀布光，降低对比、弱化明暗差异，使主体趋于平面化，观感平和、自然、安全、传统。全侧光自侧方入射，以强烈明暗强化立体，清晰勾勒形体与纹理，带来强烈鲜明、压迫震撼的情绪。逆光由后方打亮边缘，形成明亮轮廓，增强主体与背景的分离感，营造层次，氛围神秘梦幻且富有魅力。顶光自上方投射，下方形成阴影，强化明暗与立体，气质偏向严肃威严、神秘庄重。底光自下而上，底面与面部阴影加深，产生不稳定扭曲感，放大不安紧张氛围，视觉冲击强，戏剧张力足，常传递惊悚神秘的情绪。

色温上分为冷光与暖光。冷光偏蓝绿，带来清冷疏离、冷漠的氛围；暖光偏红黄，带来亲切温暖、柔和的感受。

光源：自然光

#### 风格定义
为了营造舒适的自然光感，加入“Kodak film”“1980s”后画面年代气息增强，人情味更浓。“105mm f/1.8”属于相机参数，控制曝光与景深，能让主体清晰、背景虚化。

#### 图片效果
整体为纪实摄影风格，纪实摄影以真实呈现生活引发共鸣。画面中夕阳下独自回家的孩子与熟悉的乡间石径构成一景，温馨而带点乡愁。

### 提示词
Golden hour, Kodak film, an oriental boy walking on the stone road with his school bag, 1980s, Chinese rural environment, Kodak Portra 800, 105 mm f1.8

### 中文翻译
金色时光，柯达胶片，背着书包走在石板路上的东方男孩，20世纪80年代，中国农村环境，柯达 Portra 800，105mm f/1.8

光源：人工光

#### 风格定义
人工光源可调节强度角度色彩，营造动态与科技感，因此选用“laser effects”“cinematic lighting”等关键词来表现人工光。加入“UHD”可提升画质清晰度。

#### 图片效果
作品为CG风格，精细光线刻画机械厂场景，突出塑料玻璃皮革的材质感，丰富画面层次。背景虚化进一步突出主体。

### 提示词
Mechanical maiden, artificial intelligence, laser effects, goggles, sunglasses, Unreal Engine, cinematic lighting, Sony FE, atmospheric perspective, UHD, super detail

### 中文翻译
机械少女，人工智能，激光特效，护目镜，太阳镜，虚幻引擎，电影照明，索尼 FE，大气透视，超高清，超级细节

质感：柔光

#### 风格定义
纳比派是法国以室内场景为主的艺术团体，追求诗意写实与平面装饰性与象征表达。配合“les nabis”和“diffuse light”关键词，很容易得到韵味十足的画面。

#### 图片效果
此图可视为美学方法的直接体现，冷暖强烈对比与色彩碰撞在严格控比下，呈现出同一世界下的双重心境。

### 提示词
A woman reading a book in a grey hallway, in the style of color reversal film, diffuse light, les nabis, solarized master film, light yellow and cyan, vignettes of Paris, movie still, grainy

### 中文翻译
一位女士在灰色走廊里阅读书籍，采用彩色反转片风格，漫射光，纳比派，日光母版胶片，浅黄与青色，巴黎剪影，电影静帧，颗粒感

质感：硬光

#### 风格定义
商业摄影中硬光能增强产品立体感，使其更鲜明突出，故广告常采用硬光。

#### 图片效果
此商业摄影作品背景自然，产品占比虽小却充满生命力，源于直射光对主体的强调。突出而不突兀，主体与背景融合，观者自然联想到安全、修护、有效等属性。

### 提示词
Essential oil, hard light, commercial photography, product poster, f/4.0, cinematic lighting, Sony FE, atmospheric perspective, UHD, super detail, 8K

### 中文翻译
精油，硬光，商业摄影，产品海报，f/4.0，电影照明，索尼 FE，大气透视，超高清，超级细节，8K

光位：面光

#### 风格定义
面光属于最易产生平面感的打光方式之一，阴影与明暗差异被弱化后人物细节更清晰。加入“headlight”“Minolta Hi-Matic 7sII”可赋予画面年代感。Minolta Hi-Matic 7sII是1977年发布的便携相机，当时人们偏爱轻松生活，喜欢捕捉生活片段。

#### 图片效果
仍为纪实摄影风格，面光突出面部表情，更生动呈现人物特质，情绪表达到位，慈祥感扑面而来。

### 提示词
Headlight, photo of a Korean woman eating watermelons, in the style of Minolta Hi-Matic 7sll, grandparentcore, candid portraits

### 中文翻译
前照灯，韩国女性品尝西瓜的照片，美能达Hi-Matic 7sII相机风格，祖父母核，抓拍肖像（注：“core”作为后缀的词语，代表一种网络流行的"核类美学"，是受互联网共享艺术强烈影响而诞生的，其代表如梦核（Dreamcore）、池核（Poolcore）、天使核（Angelcore）等。每种"核类美学"都代表一类艺术风格。）

光位：全侧光

#### 风格定义
全侧光可增强主体层次，以强明暗对比营造紧张氛围，适合表现冲击与压抑感的场景。

#### 图片效果
全侧光精准传达人物杀意，配合雨天场景，紧张与压迫感进一步加强。

### 提示词
Stunning ancient Chinese themed ink style, a martial arts character meets a killer, rainy day, bamboo forest, very detailed, dynamic and expressive, clean lines, cinematic, realistic lighting effects, vivid, sidelights, vibrant 8K, Ov Rendering, Unreal Engine, very detailed concept art, realistic

### 中文翻译
惊艳的中国古代主题水墨风格，武侠角色遭遇杀手，雨天，竹林，极其细腻，富有动感与表现力，线条干净，电影感，逼真光影，生动，侧光，鲜艳8K，Ov渲染，虚幻引擎，概念艺术，写实

光位：逆光

#### 风格定义
若需主体拥有明亮轮廓，逆光是首选；想要闪耀感时也优先考虑逆光。

#### 图片效果
此人物摄影借助逆光营造甜蜜愉快的氛围，柔和光晕勾勒轮廓，使画面更温情。

### 提示词
Two people eating ice cream on the outskirt of Xinhua, in the style of high speed film, light white and light orange, domestic intimacy, cinematic lighting, character portrait, Sony FE GM, UHD, super detail

### 中文翻译
两人在新化郊外享用冰淇淋，高速胶片风格，浅白与浅橙，家庭亲密感，电影照明，人像，索尼 FE GM，超高清，超级细节（注：新化隶属湖南省娄底市。）

光位：顶光

#### 风格定义
顶光来自主体上方，在影视与人像摄影中常用于刻画反派形象，或在演唱会、演讲等需要聚焦的场景。

#### 图片效果
此例采用顶光聚焦人物，强化了严肃与压迫感。

### 提示词
A man in a suit is sitting in the front of a dark room, top light, old-timey, negotiating table, 1980s, shot on Kodak, movie color, lense, close-up, red

### 中文翻译
身着西装的男子坐在昏暗房间前部，顶光，老式风格，谈判桌，80年代，柯达拍摄，电影色调，镜头，特写，红色

光位：底光

#### 风格定义
底光通常带来恐怖阴森的感受，若需更惊悚可加入“in the style of demonic photograph”。“cabincore”可营造人烟稀少的环境，暗示不明物体与突发事件来临的氛围。

#### 图片效果
底光准确传达人物紧张情绪，并强化整体恐怖效果。

### 提示词
Two tiny girls in red dresses holding candles, in the style of demonic photograph, emotive lighting, criterion collection, shiny eyes, cabincore, news photography, 8K

### 中文翻译
两个穿红裙的小女孩手持蜡烛，恶魔照片风格，情绪化照明，标准收藏，闪亮眼眸，木屋核，新闻摄影，8K

光色：暖光

#### 风格定义
光会影响情绪，暖色光适合用于住宅等营造温馨舒适的空间。可通过“orange”“beige”“yellow”“red”等暖色关键词生成暖光画面。

#### 图片效果
此静物摄影在暖光下显柔和安宁，一抹绿色平衡暖调，增加通透感，使画面更协调。

### 提示词
A book opened and some flowers in the transparent vase, in the style of light orange and light beige, wavy resin sheets, lively interiors, light yellow and red, natural simplicity, serene feeling

### 中文翻译
一本摊开的书与透明花瓶中的花朵，浅橙浅米色风格，波浪树脂桌布，生动的室内，浅黄红，自然简约，宁静感

光色：冷光

#### 风格定义

冷调光的冰感营造法

冷色光自带清冽通透的情绪，很适合塑造冰川、矿洞这类环境。

#### 图片效果
整体偏向蓝调，水域呈现出晶莹剔透的质感，画面干净利落。

### 提示词
Glacier, cold light source, clear water with ice, Unreal Engine, cinematic lighting, wide shot, UHD, super detail

### 中文翻译
冰川，冷光源，清澈的冰水，虚拟引擎，电影照明，远景镜头，超高清，超级细节

## 第三章

## 材质

### Material Quality
结合当下流行趋势的观察，受众对材质偏好相当明确。经大量案例收集与归纳，材质氛围可归纳为八大方向。

这八项分别是活泼的“欢趣色彩”；华美的“迷幻光影”“鎏金光芒”；带有科技感或神秘气质的“末世箔银”“坚硬岩铁”“梦幻薄纱”；以及散发自然气息的“轻软棉绒”“原始朴真”。

## 轻软棉绒

#### 风格定义
此类调性多选用纯棉、羊毛、牛奶绒、马海毛及各类纱线等亲肤柔软面料，触感温暖舒适、松弛柔和。加入提示词“Chen Zhen（陈箴）”，可进一步强化松软惬意的氛围。

陈箴（1955—2000）是中国装置艺术的先行者之一，擅长以日常物件构建出别致的装置作品。

#### 图片效果
蓬松的棉絮在空中轻舞，整体自在舒适。

### 提示词
The image shows a woman surrounded by cotton, in the style of soft sculptures, Chen Zhen, nature-inspired, pop culture infused

### 中文翻译
图片呈现了一位女性置于棉花中，软雕塑风格，陈箴，自然灵感，注入流行文化

## 原始朴真

#### 风格定义
这类材质常以棉麻、香云纱、莨绸、涤棉为基础，可搭配龟裂纹等立体肌理，营造朴实自然的舒适感。

使用提示词“linen（亚麻布）”“earthy color palette（大地色系）”“natural crude ore（天然原矿）”有助于塑造真实自然的氛围。

#### 图片效果
布料柔软且布满褶皱，层层叠置并配以柔和色调，舒适感很强。

### 提示词
Grey linen twills, ivy linen, the artisan shop, rustic linen, shabby chic linen, white and brown, earthy color palette, striped, creased crinkled wrinkled, rustic still life

### 中文翻译
灰色亚麻斜纹布，常春藤亚麻布，工匠商店，乡村风亚麻，破旧但别致的亚麻布，白色和棕色，大地色系，条纹，起皱的，宁静的乡村生活

#### 图片效果
主体材质看似硬朗，创作时采用柔光来中和，茶这种有温度的主题借助柔光更显温情，有助于产品表达。

### 提示词
The tea pot is sitting on a tea serving tray, in the style of organic sculpting, texture, natural crude ore

### 中文翻译
茶壶在盛茶的托盘上，采用有机雕刻，纹理，天然原矿

## 坚硬岩铁

#### 风格定义
该质感多选用水洗牛皮纸、树脂、金属布等基材，并通过硬质涂层或混入铁屑实现挺阔效果。加入提示词“volcanic（火山）”“dark silver（暗银色）”“liquid metal（液态金属）”可拓展创意空间。

厄休拉·冯·莱丁斯瓦德（Ursula von Rydingsvard）是知名女性雕塑家，主要以雪松等木材创作大型作品。加入“Ursula von Rydingsvard”提示词，服装会呈现更强的雕塑感。

#### 图片效果
银色金属与雕塑元素的结合让整体显得坚硬有力。

### 提示词
Surrounded by volcanic rocks, wearing a distinctive gray jacket, appearing on the rocks, in the style of Ursula von Rydingsvard, with iridescent/fluorescent colors, densely textured or tactile, close-up shots, liquid metal, dark silver, panoramic views, wide-angle shots, and distant vistas

### 中文翻译
周围是火山岩，身着独特的灰色夹克，出现在岩石上，厄休拉·冯·莱丁斯瓦德风格，虹彩/荧光，表面纹理密集或有触感，特写镜头，液态金属，暗银色，全景，广角镜头，远景

## 梦幻薄纱

#### 风格定义
材质多为欧根纱、水光纱、双层纱、镂空网纱及特种透明纱，呈现通透细腻并带有微弱光泽的特质。

提示词“the stars art group (xing xing)”是一个虚构的艺术团体名，“xing xing”可替换为任意词，使用后可生成团体感画面。再结合以下两位艺术家名字，纱感会更梦幻。

萨曼莎·基利·史密斯（Samantha Keely Smith）1972年生于英格兰，作品常以强烈色彩对比表现翻腾的海浪。

托马斯·萨拉切诺（Tomás Saraceno）1973年生于阿根廷，雕塑与装置艺术家，善于将高科技融入创作。

#### 图片效果
轻薄纱幔层层堆叠，使无形的风变得可见，灵动飘逸，带来轻快、梦幻、轻奢的感受。

### 提示词
Gorgeous colored veils, colored textures on satin, long shot, full-body view, delicate facial details, eyes were full of unspeakable longing and sadness, dynamic posture, oriental minimalism, environmental awareness, soft and rounded forms

### 中文翻译
华丽的彩色面纱，绸缎上的彩色纹理，长镜头，全身视角，精致的面部细节，眼神中充满了难以言喻的渴望和悲伤，动态的姿态，东方极简主义，环境意识，柔和圆润的形式

## 梦幻薄纱

#### 图片效果
纱幔漫天飘飞，柔光透过针织孔隙，营造出梦幻而神圣的场面。

### 提示词
People covered with colored gauze with different decorations, in the style of Samantha Keely Smith, dream-like atmosphere, translucent water, flowing fabrics, whimsical animation, Tomás Saraceno, the stars art group (xing xing)

### 中文翻译
人们身上覆盖着带有不同装饰的彩纱，萨曼莎·基利·史密斯的风格，梦幻般的氛围，半透明的水，流动的织物，异想天开的动画，托马斯·萨拉切诺，星星艺术团

## 鎏金光芒

#### 风格定义
常用绸缎、复合编织面料、网纱等，配以金色、香槟色并点缀亮片等反光辅料。

加入提示词“in the style of baroque religious scenes（巴洛克式宗教场景风格）”“golden light（金色的灯光）”“handcrafted beauty（手工美感）”，可营造高贵奢华的氛围。

#### 图片效果
暖调柔光落在人物身上，金色服饰增强了反射，发丝也熠熠发亮。

### 提示词
In the style of baroque religious scenes, golden light, handcrafted beauty, sun-kissed palettes, porcelain, UHD, super detail, best quality, textured skin

### 中文翻译
巴洛克式宗教场景风格，金色的灯光，手工美感，阳光亲吻过般的色调，瓷器，超高清，超级细节，最佳品质，质感皮肤

## 迷幻光影

#### 风格定义
材质偏向带透明胶感或仿皮革感的TPU、PU等，配合镭射、渐变或不规则纹理，呈现迷幻炫彩效果。使用提示词“glitter powder（闪粉）”“translucent water（半透明的水）”可增强奇幻感。

佩特拉·科特赖特（Petra Cortright）是跨学科艺术家，擅长用数码软件融合具象与抽象后转印于布面，形成繁复形态。

乔斯林·霍比（Jocelyn Hobbie）是美国艺术家，画面常融合人物、植物、花卉元素，色彩浓郁艳丽。

玛尔塔·贝瓦夸（Marta Bevacqua）是意大利女摄影师，擅长拍摄情绪饱满、故事性强的人像。

#### 图片效果
AI可生成水下般的清透画面，色彩绚烂，闪耀材质与柔和水感交织，既迷幻又增添神秘浪漫。

### 提示词
One of the models is painted with magic, in the style of Petra Cortright, close-up shots, Jocelyn Hobbie, subversive film, glitter powder, Marta Bevacqua, translucent water

### 中文翻译
其中一位模特身上用魔法作画，佩特拉·科特赖特风格，特写镜头，乔斯林·霍比，颠覆性电影，闪粉，玛尔塔·贝瓦夸，半透明的水

## 迷幻光影

#### 图片效果
光滑塑料质感搭配绚丽色光，能进一步凸显服饰的迷幻特质。

### 提示词
A man in a rainbow plastic jacket walking on runway, in the style of refractive surfaces, silver and aquamarine, and raw metallicity, reimagined by industrial light and magic, sparkling water reflections

### 中文翻译
一位身着彩虹色塑料夹克的男子走在跑道上，折射面、银色和海蓝色、原始金属质感风格，通过工业光和魔法元素进行再创造，波光粼粼的水面倒影

## 欢趣色彩

#### 风格定义
材质多为长毛绒、珊瑚绒、毛线、毛毡及胶感面料，触感柔软。本章两件作品分别使用了“in the style of Chinapunk（中国朋克风格）”和“psychedelic punk（迷幻朋克）”提示词，带来两种截然不同的欢乐色彩。

“Spopa”是一个自拟品牌名，可根据需要替换，画面文字会随之变化，但偶尔会出现生成错误。

#### 图片效果
作品带有东方潮牌气质，高饱和的多巴胺粉色充满活力且时尚。

### 提示词
An ad for Spopa pop-up shop, in the style of Chinapunk, dark pink, ultra realistic, furry art, low-angle shots, made of rubber, pop colorism

### 中文翻译
Spopa快闪店广告，中国朋克风格，深粉色，超逼真，毛茸艺术，低角度拍摄，橡胶制成，流行色彩主义

## 欢趣色彩

#### 图片效果
缤纷色彩与多元造型结合毛线材质，整体有趣活泼。

### 提示词
A sculpture made by colorful yarns in a museum, wandering eye, psychedelic punk, embroidery, oversized objects

### 中文翻译
博物馆中的彩纱雕塑，具有游离之眼，迷幻朋克，刺绣，超大物件

## 第四章

## 艺术流派

## Artistic Genres
近现代设计深受文艺复兴及之后流派影响，艺术的思想与审美早已渗透到日常生活的各个角落。

我们尝试借助AIGC对主要艺术流派进行再创作，以另一种方式帮助大家感知各流派风格，获取更多灵感。

- 宋代山水 10~13世纪

- 文艺复兴 14~16世纪

- 明代线描 14~17世纪

- 巴洛克 17世纪

- 洛可可 18世纪

- 新古典主义 18世纪

- 浪漫主义 19世纪

- 印象派 19世纪

- 立体主义 20世纪

- 超现实主义 20世纪

- 抽象表现主义 20世纪

## 宋代山水

#### 风格定义
宋代是传统山水画发展的顶峰。要生成水墨风格，可使用提示词“mountains and streams in the Northern Song Dynasty（北宋的山川）”“960 to 1279（公元960年至1279年）”。若效果不足，可加入“::2”提升权重，水墨韵味更足。加入“Fan Kuan（范宽）”可使风格更明确。

#### 图片效果
宋代山水画细腻丰富、写实庄重，透出宁静之美。

### 提示词
Ink painting ::2, 960 to 1279 AD, I traveled through the mountains and streams in the Northern Song Dynasty, Fan Kuan, ink, contrast

### 中文翻译
水墨画，公元960年至1279年，我游历北宋的山川，范宽，水墨，对比

## 明代线描

#### 风格定义
此处加入提示词“1470（即年份）”和“Zhu Zhanji（朱瞻基）”，更有利于锁定明代画风。

#### 图片效果
明代线描已趋成熟，融合历代技法，风格多样。图中人物朴实清朗，景致简练，笔触遒劲。

### 提示词
Chinese painting, a person holding a scroll, clear lines, 1470, Zhu Zhanji, ink painting --ar 5:3

### 中文翻译
中国绘画，一人拿着卷轴，线条清晰，1470年，朱瞻基，水墨画，宽高比5:3

## 文艺复兴

#### 风格定义
文艺复兴是14～16世纪欧洲的文化运动，艺术崇尚人体之美，主张人体比例最和谐，绘画摆脱中世纪呆板，透视更科学，色彩更出色。

加入提示词“Michelangelo Buonarroti（米开朗琪罗·博纳罗蒂）”“Renaissance style（文艺复兴风格）”可得到类似风格图像。也可使用“Leonardo da Vinci（列奥纳多·达·芬奇）”“Raffaello Santi（拉斐尔·桑蒂）”等，按喜好选择即可。

#### 图片效果
画面精细，明暗对比强烈，呈现出文艺复兴特有的唯美气质。

### 提示词
Michelangelo Buonarroti, a girl sitting on a pile of cloth, Renaissance style

### 中文翻译
米开朗琪罗·博纳罗蒂，坐在布堆上的女孩，文艺复兴风格

## 巴洛克

#### 风格定义
巴洛克指17世纪欧洲盛行的艺术风格，常大量使用金色，在边框或内容处留白，同时在可装饰处填入方形、圆形、三角形等几何装饰，显出严肃与对称。巴洛克承袭文艺复兴，强调动感与华丽，反对简单对称。我们输入提示词“Baroque”“pearls（珍珠）”，生成了一件符合此风格的工艺品图像。

#### 图片效果
作品以大量几何图形与复杂曲线装饰，华丽宏大，极具戏剧性。

### 提示词
Oddly shaped pearls, lavish architecture, sacred Baroque

### 中文翻译
奇形怪状的珍珠，奢华的建筑，充满神圣感的巴洛克

## 洛可可

#### 风格定义
洛可可起源于18世纪法国，特点是粉嫩柔软，结合不对称、曲线与雕刻手法，使静态物品呈现动感，常被视作巴洛克的极致。相比巴洛克，洛可可更注重留白，将色彩粉白化、尖角柔和化、直线条曲线化。为更好表现风格，加入提示词“Francois Boucher（弗朗索瓦·布歇）”“Rococo（洛可可）”“flowing fabrics（流动的织物）”“angelcore（天使核）”。

Francois Boucher：弗朗索瓦·布歇，法国画家，洛可可代表人物之一。

flowing fabrics：流动的织物，指质地轻盈柔软，能随动作自然弯折移动的面料。

angelcore：借用欧洲常见天使形象创作，不含特定宗教含义，常出现蝴蝶、天空、巴洛克或洛可可元素。

#### 图片效果
柔和色彩搭配华丽装饰，画面优雅轻盈。

### 提示词

Francois Boucher, paintings of woods in the garden, in the style of Rococo portraitures, energetic figures, light amber and azure, spectacular backdrops, flowing fabrics, angelcore

### 中文翻译

弗朗索瓦·布歇，描绘花园里树林的画作，洛可可式肖像画风格，充满活力的人物，浅琥珀和天蓝色，壮观的背景，流动的织物，天使核

## 新古典主义

#### 风格定义
这一脉络在18世纪的罗马萌芽，19世纪初进入高峰，本质上是对巴洛克与洛可可繁复手法的反思，力图复兴古希腊、古罗马那种以几何秩序为核心的简洁美学，让画面更清晰通透。创作时主体部位克制简练，装饰性细节多集中在边缘与留白处，既不走向繁复堆砌，也不滑向过度寡淡。提示里加入 ancient Greco-Roman art style（古希腊罗马艺术风格） 能更有力地塑造这种气质。

#### 图片效果
背景往往信息密集、华丽繁复，而人物的刻画则偏向精炼，二者形成鲜明的反差张力。

### 提示词
A woman, knife in hand, ancient Greco-Roman art style, Jean Auguste Dominique Ingres, imposing, grand setting, swirling vortex

### 中文翻译
一个妇女手拿刀，古希腊罗马艺术风格，让·奥古斯特·多米尼克·安格尔，气势磅礴，宏伟的场景，漩涡

## 浪漫主义

#### 风格定义
19世纪的浪漫主义是对新古典主义和学院派的突破，更强调情绪宣泄、个体自由与自然之美，摒弃理性规约，偏向凭直觉与激情创作。Midjourney对浪漫主义的理解容易流于字面，若采用主体加情节再加代表艺术家的组合写法，呈现的效果会更贴近浪漫精神。法国画家欧仁·德拉克鲁瓦的名字能显著提升浪漫属性。

#### 图片效果
笔触舒展勾勒风的形态，山坡上微风拂动，衣袂随风飘扬，人群举止闲适自在，整幅画面弥漫着浪漫气息。

### 提示词
They walked to the top of the castle, Eugène Delacroix

### 中文翻译
他们走向城堡的顶端，欧仁·德拉克鲁瓦

## 印象派

#### 风格定义
印象派诞生于19世纪末法国，创作起点是主观印象与瞬间观察，注重对自然景象光影的记录。作品笔触明显不加掩饰，核心在于表现光线变化与时间感。代表人物有奥斯卡-克劳德·莫奈、埃德加·德加、皮埃尔-奥古斯特·雷诺阿，在提示中加入 Monet 即可快速获得印象派质感。

#### 图片效果
捕捉转瞬即逝的印象，呈现光与色的流转，色块明亮、笔触短促，画面生动富有活力。

### 提示词
View of a lake with two swans, complementary colors, Monet

### 中文翻译
湖上有两只天鹅，互补色，莫奈

## 立体主义

#### 风格定义
立体主义兴起于20世纪初法国，毕加索等艺术家是主要推动者。该流派打破传统绘画秩序，通过多面体、复视点与透视重构来组织空间。巴勃罗·毕加索、乔治·布拉克是关键人物，输入 Cubist style（立体主义风格） 与 Picasso 就能得到典型的立体主义效果。

#### 图片效果
以几何形体搭配高饱和色彩，营造出抽象且具立体感的构图。

### 提示词
Cubist style, two swans on a lake, Picasso, multi-dimensional, abstract, vivid picture

### 中文翻译
立体主义风格，湖上有两只天鹅，毕加索，多维，抽象，生动的画面

## 超现实主义

#### 风格定义
超现实主义在20世纪初于欧洲兴起，强调潜意识与想象力高于理性逻辑，常以非常规与超自然题材挑战传统表现。提示里加入 surrealist collage（超现实主义拼贴画） Carrie Ann Baade（卡丽·安·巴德） romanticized（浪漫化的） 等词，可生成非正统形式的图像。卡丽·安·巴德是上世纪70年代出生的美国超现实主义者，作品多融合当代与古典符号并使用高亮色彩。

#### 图片效果
画面充斥不连贯、荒诞的细节，观感充满不确定与疏离。

### 提示词
Two men holding flowers standing by a lake, in the style of a surrealist collage, Carrie Ann Baade, romanticized depiction of wilderness, composed of vines, surrealist femininity, multilayered collage

### 中文翻译
两名手持鲜花的男子站在湖边，超现实主义拼贴画风格，卡丽·安·巴德，对荒野的浪漫化的描绘，由藤蔓构成，超现实主义的女性气质，多层拼贴画

## 超现实主义

#### 图片效果
此作将超现实主义与波普艺术融合，既保留超现实的奇异元素，又借波普的图形与色彩强化主题的夸张梦幻感。即便未明确写入 Salvador Dalí（萨尔瓦多·达利），画面仍呈现明显的达利特质，反映出训练库的丰富度与计算能力。

### 提示词
The artist is shown holding a wooden eye, in the style of surreal architectural landscapes, surrealist pop art, light sky-blue and orange, security camera art, multiple perspectives, Art Deco influence, sophisticated surrealism

### 中文翻译
图中艺术家手持一只木制的眼睛，超现实建筑景观风格，超现实主义波普艺术，浅天蓝色和橙色，安全摄像头艺术，多角度，装饰派艺术的影响，精致的超现实主义

## 抽象表现主义

#### 风格定义
抽象表现主义是20世纪中期在美国兴起的抽象潮流，强调情感外化与艺术家的直觉，通过色彩、线条与形体的自由运用传达内心体验。提示中配合角色、风格与画家名，通常能得到典型作品。杰克逊·波洛克生于1912年，美国画家，作画不做事先设计，常在画布四周移动，以反复的无意识动作堆出错综的网状肌理，被称作行动绘画。

#### 图片效果
借助点线面、色彩形体与构图传递洒脱情绪，视觉上动感十足、生命力强。

### 提示词
Sporty, Abstract Expressionism, Jackson Pollock

### 中文翻译
运动型，抽象表现主义，杰克逊·波洛克

# 第五章

## 插画

### Illustration
从洞穴壁画到数字创作，插画历经漫长演化，风格极度个性化且多样。因此在AIGC创作中，输入一位代表性画家的名字常能精准锁定目标画风。经大量梳理，挑选出插画领域最核心的13种画风作示范：东方韵味的现代类型如水墨动漫、剪纸画；西方复古类型如穆夏风、复古美式、莫里斯风格；轻松多彩的类型如涂鸦风、莫比厄斯风格；简约现代的类型如色彩几何建筑、自由线条、简单趣味；精细的类型如水彩画、细腻花卉、暗系色调。

## 穆夏风

#### 风格定义
加入 Alphonse Mucha（阿尔丰斯·穆夏） 可触发穆夏特质。穆夏生于1860年，捷克斯洛伐克画家，作品柔和优雅且具装饰性，多以女性为主角展现高雅之美。为加强效果可追加 mural painting（装饰画） culturally diverse（多元文化） 等词。提示中的 --niji 代表动漫风格。

#### 图片效果
画面出现明显的流动曲线、淡雅色调与丰富元素，既有日本浮世绘的韵味，又兼具巴洛克的唯美，东西方风格融合。

### 提示词
An Art Deco poster featuring a woman in a dress, in the style of Alphonse Mucha, light crimson and light aquamarine, decorative paintings, abundant symbolism, mural painting, culturally diverse elements --niji

### 中文翻译
一幅装饰派艺术海报，以一位身着礼服的女士为主题，采用阿尔丰斯·穆夏风格，浅深红色和浅海蓝色，装饰画，丰富的象征意义，壁画，多元文化元素，动漫风格

## 复古美式

#### 风格定义
复古美式源自美国历史文化，整体偏深调，兼具历史厚重感与现代舒适感，常搭配硬朗线条与复古美国元素，营造怀旧氛围。输入 Norman Rockwell（诺曼·洛克威尔） 可生成该风格画面。诺曼·洛克威尔是美国画家与插画家，作品记录了20世纪美国社会变迁。

#### 图片效果
以红蓝绿等高饱和色形成强烈对比，配合硬朗线条，呈现复古美式画风，画面热闹情绪饱满。

### 提示词
Vintage American wallpaper illustration by Norman Rockwell

### 中文翻译
诺曼·洛克威尔绘制的复古美式壁纸图片

## 莫里斯风格

#### 风格定义
威廉·莫里斯是19世纪英国设计师，主张手工艺复兴，反对工业化带来的机械化与标准化。其设计注重细节与工艺，题材多取自自然尤其是植物纹样，带有中世纪艺术气息，创作的家具、纺织品、壁纸与装饰品极具艺术性。

#### 图片效果
模仿19世纪英国工艺美术运动的书籍装帧排版，色彩清新，藤蔓花卉自然气息浓郁，细节丰富线条流畅，典雅精致。

### 提示词
Delicate vines and flower patterns around the text, William Morris

### 中文翻译
精致的藤蔓和花朵图案环绕在文字周围，威廉·莫里斯

## 莫里斯风格

#### 图片效果
使用相同提示生成的另一张作品，画面自然优美，AI随机加入了哥特建筑元素后更显独特。

### 提示词
Delicate vines and flower patterns covering pages around the text, William Morris

### 中文翻译
精致的藤蔓和花朵图案环绕在文字周围，威廉·莫里斯

## 细腻花卉

#### 风格定义
罗伯特·约翰·桑顿为植物学家，擅长捕捉花朵、叶片等植物细节的复杂性。加入 Robert John Thornton 可生成细腻柔美的花卉插画。

#### 图片效果
深色背景凸显植物细节，画面细腻柔润立体感强，真实度高。

### 提示词
Watercolor flowers, light silver and dark bronze, colorful, landscape painting, vivid imagery, striped arrangements, Robert John Thornton

### 中文翻译
水彩花卉，浅银色和深青铜色，色彩斑斓，风景画，生动的意象，条纹排列，罗伯特·约翰·桑顿

#### 图片效果
相较上一张表现力更强，更奔放冲击感十足。

### 提示词
Watercolor flowers, light silver and dark bronze, colorful, landscape painting, vivid imagery, striped arrangements, Robert John Thornton

### 中文翻译
水彩花卉，浅银色和深青铜色，色彩斑斓，风景画，生动的意象，条纹排列，罗伯特·约翰·桑顿

#### 风格定义
皮埃尔-约瑟夫·雷杜德是比利时植物插画家，以精美细腻的植物写生著称，常用水彩与水粉表现植物。

#### 图片效果
光影描绘精准，夕阳下的花房宁静美丽。

### 提示词
In a room filled with lush herbs, Pierre-Joseph Redouté

### 中文翻译
在一间种满郁郁葱葱的草本植物的房间里，皮埃尔-约瑟夫·雷杜德

## 暗系色调

#### 风格定义
阿瑟·拉克姆生于1867年，英国插画家，作品多采用暗系色调并融入奇幻与神话元素。此处除画家名外还加入了 flowers（花卉） dark tones（暗系色调） 等提示。

#### 图片效果
呈现神秘、黑暗、略带颓败的美感。

### 提示词
Arthur Rackham, floral poster material, flowers, watercolor, dark tones, no characters

### 中文翻译
阿瑟·拉克姆，花卉海报素材，花，水彩，暗系色调，无人物

## 莫比厄斯风格

#### 风格定义
莫比厄斯风格以连续循环的曲线贯穿画面为特征。

#### 图片效果
通过构图与线条营造无限循环感，鲜艳的红黄蓝等色彩提升视觉吸引力。

### 提示词
In the style of bold graphic comic art, vibrant cartography, text and emoji, explosion, expressionism, firecore, colorful, Moebius

### 中文翻译
大胆的图形漫画艺术风格，生动的制图，文字和表情符号，爆炸，表现主义，火核，色彩斑斓，莫比厄斯

## 涂鸦风

#### 风格定义
让-米歇尔·巴斯奎特是20世纪美国涂鸦艺术家，常以鲜艳色彩、符号与文字创作。加入 Jean-Michel Basquiat 可生成层次丰富复杂的涂鸦作品。

#### 图片效果
色彩浓郁且协调，夸张造型增强趣味性。

### 提示词
Exaggerated and frenetic graffiti, vibrant paints and distinctive lines intertwine to create unforgettable images, Jean-Michel Basquiat

### 中文翻译
夸张、狂热的涂鸦，鲜艳的颜料和独特的线条交织在一起，创造出令人难忘的画面，让-米歇尔·巴斯奎特

## 水彩画

#### 风格定义
水彩画以水溶性颜料创作，擅长表现光线与色彩。以下画师名可作为提示触发水彩效果。海伦妮·谢尔夫贝克生于1862年，芬兰画家，擅长以简化的人物与静物传达内心世界。理查德·斯凯瑞是20世纪美国儿童图书作家与插画家，作品色彩明亮描绘细致且富幽默感。奥斯卡·施莱默生于1888年，德国画家，风格介于抽象表现主义与构成主义之间，常融入舞蹈与戏剧元素，以人体姿态为灵感创作动感戏剧化的形象。

#### 图片效果
保留水彩透明感与笔触流动感，整体偏平面化立体感较弱。

### 提示词
A big bunny and a big-eyed cat drinking coffee, imaginative characters, photorealistic detail, Helene Schjerfbeck, Richard Scarry, Oskar Schlemmer, realistic watercolor paintings

### 中文翻译
一只大兔子和一只大眼猫在喝咖啡，富有想象力的角色，逼真的细节，海伦妮·谢尔夫贝克，理查德·斯凯瑞，奥斯卡·施莱默，写实的水彩画

#### 图片效果
与上一幅相比主体人物描绘更细致立体，色彩更为柔和。

### 提示词
A drawing of a girl and a rabbit having tea at the table, imaginative characters, photorealistic detail, Helene Schjerfbeck, Richard Scarry, Oskar Schlemmer, realistic watercolor paintings

### 中文翻译
一个女孩和一只兔子在桌边喝茶的图画，富有想象力的角色，逼真的细节，海伦妮·谢尔夫贝克，理查德·斯凯瑞，奥斯卡·施莱默，写实的水彩画

## 自由线条

#### 风格定义

## 线条的两种诗意

昆汀·布莱克是上世纪三十年代出生的英国画家，以钢笔、芦苇笔与羽毛笔见长。用极简的线去捕捉人物的神情、姿态和情绪，笔锋粗糙带棱角，对细节和比例并不较真。

安德烈亚·安蒂诺里生于1992年的意大利插画师，运笔洒脱干净，偏好扁平化的表现，用浓烈明快的色彩构建奇思妙想的脑洞世界。

两位创作者都以线取胜，但美感的指向截然不同。

#### 图片效果
线条舒展自如，笔意松散轻快，整体色调清新宜人，童趣浓郁。

### 提示词
Hand-drawn illustration, childlike, bright colors, simple lines, Quentin Blake --niji

### 中文翻译
手绘插画，童趣，明亮的色彩，简单的线条，昆廷·布莱克，动漫风格

#### 图片效果
笔触带有律动感，色块相互碰撞带来鲜活的张力，画面灵动有趣。

### 提示词
Hand-drawn illustration, childlike, bright colors, simple lines, Andrea Antinori --niji

### 中文翻译
手绘插画，童趣，明亮的色彩，简单的线条，安德烈亚·安蒂诺里 --niji

## 色彩几何建筑

#### 风格定义
荷兰建筑插画师桑德·帕特尔斯基擅长以中世纪现代主义与包豪斯的语言演绎当代室内，用概念性强的色彩与形体进行组合。带入提示词Sander Patelski即可得到极具潮流感的建筑插画。

#### 图片效果
大面积使用强烈红黄蓝黑，辅以粉彩模拟混凝土质感。去装饰的纯粹立方体营造出意外的建筑力量。

### 提示词
Graphic poster of modernist architecture, concrete, Sander Patelski, strong and slightly muted shades of red, yellow, blue and black complemented by pastels

### 中文翻译
现代主义建筑图形海报，混凝土，桑德·帕特尔斯基，强烈而略显柔和的红色、黄色、蓝色和黑色色调，辅以粉彩

## 简单趣味

#### 风格定义
加入 in the style of color-blocked textiles（色块纺织品风格）、vibrant pastels（鲜艳粉彩）、elongated figures（拉长的人物）后，画面的趣味会明显提升。

#### 图片效果
主体形态被夸张拉伸，整体呈现无厘头风格，粉调配色增强了俏皮感。

### 提示词
In the style of color-blocked textiles, vibrant pastels, elongated figures, close-up, eclectic curatorial style, gouache, pink and cyan, solid color background, Crayola texture

### 中文翻译
色块纺织品风格，鲜艳粉彩，拉长的人物，特写镜头，折衷主义策展风格，水粉，粉色和青色，纯色背景，蜡笔质感

#### 风格定义
使用 fun exaggerated characters（有趣夸张的人物）、vibrant pastels（鲜艳的粉彩）、solid background（纯色背景）等关键词，可得到大胆怪诞的造型与色彩。first edition of annual illustrations（年度插画第一版）是在创作中偶然出现的提示语，却意外提高了画面颗粒感。

#### 图片效果
表现力充沛，轻松逗趣，视觉冲击强，细节与叙事性比前者更丰富。

### 提示词
First edition of annual illustrations, fun exaggerated characters, vibrant pastels, close-up, pink and teal, solid background, simple crayon drawing

### 中文翻译
年度插画第一版，有趣夸张的人物，鲜艳的粉彩，特写，粉色和青色，纯色背景，简单蜡笔画

## 水墨动漫

#### 风格定义
Midjourney对中国绘画的理解尚需打磨，这组水墨动漫在多次调试后成型，核心提示是Chinese ink（中国水墨）。

#### 图片效果
黑白对比强烈，笔势劲道，气势十足。

### 提示词
Chinese warrior movie poster, swordsman movie poster art, Chinese knife art, light white and black, Chinese ink, action painter

### 中文翻译
中国武侠电影海报，武侠电影海报艺术，中国刀艺，浅黑白，中国水墨，动作画家

#### 图片效果
既保留漫画的叙事性，又融合传统水墨晕染的飘逸特质。

### 提示词
Chinese ancient painting style/Ancient Chinese traditional ink painting, white background, distant view shot, beautiful natural lighting, best masterpiece, 8K, exquisite and delicate details

### 中文翻译
中国古代绘画风格/中国古代传统水墨画，白色背景，远景拍摄，美丽的自然光，最佳杰作，8K，细节精致细腻

## 剪纸画

#### 风格定义
在提示中加入 in the style of captivating light（具有迷人光线的风格）、papercut scene（剪纸场景）、papercut art（剪纸艺术），可让剪纸风格图像更立体、层次更丰富。

#### 图片效果
光影对比强烈，纸片叠加形成强烈的空间感，各元素错落有致。

### 提示词
A papercut scene in oriental style, in the style of captivating light, grandeur of scale, gongbi

### 中文翻译
东方风格的剪纸场景，具有迷人光线的风格，宏大的规模，工笔的风格

#### 图片效果
选用浅色卡纸，色彩偏柔和，呈现出近似仙境的氛围。

### 提示词
The oriental themed papercut art is lit up, in the style of light white and light brown, realistic depiction of light, multilayered dimensions

### 中文翻译
点亮东方主题的剪纸艺术，浅白色和浅棕色的风格，对光线的逼真描绘，多层次维度

# 第六章

## 游戏

### Game
The Game Awards由索尼、微软、任天堂与Valve赞助，是游戏界的重要评选。随着发展，已成为全球瞩目的重量级盛会。我们梳理了2018至2022年70余部获奖作品并归类：热血澎湃的“澎湃战场”“狂热盛夏”；暗黑气质的“奇诡地狱”“暗黑哥特”；沉浸冒险的“夜幕降临”；轻松欢乐的“活力星球”“煦日海风”“治愈之旅”。

## 奇诡地狱

#### 风格定义
借助 heavy metal embroidery（重金属刺绣）、Art Nouveau（新艺术风格）、dark violet and red（深紫色和红色）等提示词，可呈现带有英雄主义色彩的画面。

#### 图片效果
整体色调传递出强烈的血腥氛围，画面中心人物气势非凡，营造出地狱求生与奇幻交织的激情。

### 提示词
Game poster, dark violet and red, lively poses, heavy metal embroidery, 32K UHD, Art Nouveau, dimensional multilayering

### 中文翻译
游戏海报，深紫色和红色，生动的姿势，重金属刺绣，32K超高清，新艺术风格，各维度多层次叠加

## 暗黑哥特

#### 风格定义
“暗黑哥特”指黑暗、邪恶、神秘且极端的氛围。可使用 horror film（恐怖电影）或具体恐怖片名提升惊悚感，再加入 couple（情侣）等温暖元素制造冲突。

#### 图片效果
浓郁背景、古怪造型与黯淡灯光共同营造恐怖氛围，而主体人物的亲密动作形成强烈反差，产生独特的戏剧感。

### 提示词
Dark, a couple eating together in Year Of The Ladybug, horror film, horror academia

### 中文翻译
黑暗，《瓢虫之年》里一起吃饭的情侣，恐怖电影，恐怖学术

## 夜幕降临

#### 风格定义
Vanitas源自拉丁语的虚无，是一种象征性绘画风格。creepypasta（恐怖都市传说）指网络流传的恐怖怪谈，常涉及超自然与诡异人物。Ogham scripts（奥格玛文字）是爱尔兰神秘文字，能增强图像神秘感。结合Death Stranding风格，使用Vanitas、panorama（全景）与creepypasta，可营造夜幕降临的不安氛围。

#### 图片效果
场景辽阔悠远，黑夜海岸与阴沉天空透出孤独感，独行旅人前往彼岸探索未知，悬疑感强烈，令人期待。

### 提示词
Death Stranding is a video game, in the style of minimalist typography, outdoor beach scenery, grandiose ruins, Ogham scripts, Vanitas, panorama, creepypasta

### 中文翻译
《死亡搁浅》是一款视频游戏，极简排版风格，户外海滩风光，宏伟废墟，奥格玛文字，虚空派，全景，恐怖都市传说

## 活力星球

#### 风格定义
为生成活力场景，作者使用了laughter（欢声笑语）、sunshine（阳光）等提示词。

#### 图片效果
画面温暖活泼，正能量充盈。两位选手拥抱与背景观众席相互呼应，欢乐气氛扑面而来，仿佛能听见赛场呐喊。

### 提示词
The whole tennis court was filled with laughter as the two men interacted with each other. The sunshine brought them a positive energy that made the match more enjoyable and exhilarating. Their bodies and minds were relaxed and rejuvenated by the sunshine, leaving all their worries and stresses behind and focusing on the game and appreciating each other's performance. ultra wide angle, UHD

### 中文翻译
当两人互动时，整个网球场充满了欢声笑语。阳光给他们带来了正能量，正能量让比赛变得更加愉快和精彩。在阳光的照耀下，他们的身心都得到了放松，恢复了精神，将所有的烦恼和压力都抛在了脑后，专注于比赛和欣赏对方的表现。超广角，超高清

## 煦日海风

#### 风格定义
提示词中加入Disney（迪士尼）、3D character（3D角色）、surfing（冲浪）等，可呈现充满活力与童话气息的卡通夏日海滩场景。

#### 图片效果
色彩鲜艳活泼，仿佛能感受到夏季海风拂面，卡通人物的圆润造型增添可爱感，描绘出向往的无拘无束童年乐趣。

### 提示词
3D art, 3D character, Disney Pixar animation, Pop Mart, C4D, surfing, the movie poster for Crazy Summer Kids, ZBrush, cartoon characters, Disney animation, airy and light, game art, cartoon scene, best quality, 8K

### 中文翻译
3D艺术，3D角色，迪士尼皮克斯动画，泡泡玛特，C4D，冲浪，《疯狂夏日小子》的电影海报，ZBrush（数字雕刻和绘画软件），卡通角色，迪士尼动画，轻盈明亮，游戏艺术，卡通场景，最佳质量，8K

## 治愈之旅

#### 风格定义
“治愈”指缓解身心疲惫、恢复平衡的过程。我们使用Fairytale world of children's playground（儿童乐园的童话世界）、cute plush monsters（可爱的毛绒怪兽）和cotton candy（棉花糖）等词，让作品带来心灵抚慰。

#### 图片效果
色彩粉嫩梦幻，调皮小怪兽采用柔软毛绒材质，整体让人沉浸于童话般的治愈氛围。

### 提示词
Fairytale world of children's playground with cute plush monsters and lots of hairballs, surrounded by flower arrangement with balls of wool, cotton candy, UHD

### 中文翻译
儿童乐园的童话世界有可爱的毛绒怪兽和许多毛球，周围是有毛线球的插花，棉花糖，超高清

#### 图片效果
典型的日漫画风，色彩干净清爽，形象十分可爱。

### 提示词
A girl with long flowing hair, in the style of cartoon mise-en-scene, Hallyu, dark yellow and light blue, social documentary, childlike innocence, serene face, gamecore

### 中文翻译
长发飘飘的女孩，卡通风格的场景，"韩流"，深黄和浅蓝，社会纪实，童真，恬静的面孔，游戏核

## 澎湃战场

#### 风格定义
澎湃战场是激烈热血的游戏风格。为弱化血腥感，画面对天空进行定义，加入a clear day（晴朗的一天），使画面多一丝生机。

#### 图片效果
避开血迹与人物细节，配以明亮天空，场面不显惨烈，飞扬尘土表现了战斗的激烈。

### 提示词
An image showing troopers being attacked by tanks on a clear day, Nikon D 850, Precisionist Art, ultra wide angle, UHD

### 中文翻译
显示部队在晴朗的一天遭到坦克袭击的图片，尼康 D850，精确主义艺术，超广角，超高清

## 狂热盛夏

#### 风格定义
使用midsummer（盛夏）、all-terrain vehicle（全地形车）、high-speed movie（高速电影）、cinematic lighting（电影照明）等词，可生成炽烈狂热且充满活力的夏日场景。

#### 图片效果
画面明媚风景优美，整体透露出即将展开畅快冒险的期待感。

### 提示词
Midsummer, all-terrain vehicle, high-speed movie, grandeur of scale, majestic ports, Unreal Engine, cinematic lighting, editorial illustrations, lively movement, joyful celebration of nature, Kawacy, natural scenery

### 中文翻译
盛夏，全地形车，高速电影，宏大的规模，雄伟的港口，虚幻引擎，电影照明，编辑插图，生动的动作，大自然的欢乐庆典，Kawacy（日本人气插画师），自然风光

#### 图片效果
画面虽无人物与情节，但凭巨大的西瓜与飞向空中的红色小球，已营造出欢快氛围。前中后景层次递进增强视觉冲击。

### 提示词
Watermelon cars, high-speed movie, grandeur of scale, majestic ports, Unreal Engine, cinematic lighting, Sony FE GM, UHD, super detail

### 中文翻译
西瓜车，高速电影，宏大的规模，雄伟的港口，虚幻引擎，电影照明，索尼 FE GM，超高清，超级细节

# 第七章

## 女性美

Feminine beauty
女性美的演变与流行趋势息息相关。如今文化、经济、生活方式的改变，为女性自我表达提供了更友好的社会环境，女性形象愈发多元，每个人都能挑选适配自己的潮流方向。

- 大女主：气场强大、干练美丽的女性形象在社会中逐渐增多。

- 自然主义：主张返璞归真克制修饰，呈现朝气蓬勃且气场稳重的女性。
- 性感魅力：敢于用身体语言进行表达，内外气质同频，营造出独立且具诱惑力的存在感。
- 唤醒独特性：带点叛逆锋芒的艺术气息，审美自成一派，穿搭见解鲜明，文艺韵味浓郁。
- 活力四射：热衷于运动与奔放的生活方式，满溢着生命张力。
- 东方美：兼顾西方视野下的东方意涵、传统柔情的东方韵致，以及时尚高级的当代东方表达。
- 甜美可爱：散发天真浪漫的柔软气质。
- 元宇宙：代表活泼年轻富有想象力的新世代，乐于拥抱前卫艺术的时尚派。

## 大女主

#### 风格定义
该方向借助白丝绸衬衫、鳄鱼皮等高级材质相关词，以及曝光大师等质感词，整体拉升画面精致度。

#### 图片效果
模特肢体语言笃定，从时髦的廓形到浓重的背景质感，都在彰显女性历经沉淀后的独特魅力。

### 提示词
A white silk blouse, brown leather shoes, high heel boots, crocodile strappy heel, in the style of soft color blending, minimalist palette, solarizing master, exquisite clothing detail

### 中文翻译
一款白色丝绸上衣，棕色皮鞋，高跟短靴，鳄鱼皮细跟，以柔和颜色混合的风格，简约的色调，曝光大师，精致的服装细节

## 自然主义

#### 风格定义
选用彩色着装、快乐核、俏皮线条、彩色条纹等词来铺垫氛围，制造出明亮写意的自然感。

#### 图片效果
海风轻拂、笑意自然、围巾随风，画面显得灵动自在，突显人与自然融为一体的和谐。

### 提示词
Wearing colored clothing, including a patterned bandana, in the style of happycore, beach portraits, playful use of line, color-streaked, poolcore, soft-focus

### 中文翻译
彩色衣服，包括带图案的头巾，快乐核的风格，海滩肖像，俏皮的线条运用，有彩色条纹的，池核，柔焦

#### 风格定义
刻意加入女脸特写并点缀褐色雀斑的描述，让肌肤质感更趋真实，通过细腻描写达成自然动人的面容。

#### 图片效果
阳光洒落脸颊，白皙肌肤上细碎雀斑成为点睛之处，展现出素净不加修饰的美感。

### 提示词
Close-up of a woman with some brown freckles on her face, light silver and light red, wandering eye, clear edge definition, shiny/glossy

### 中文翻译
一个脸上有一些褐色斑点的女人的特写，浅银色和浅红色，眼神游离，边缘清晰，闪亮的/有光泽的

## 性感魅力

#### 风格定义
为营造大胆性感的形象，提示中引入蕾丝、粉色、精工服装等元素，用以突出女性的诱人特质。

#### 图片效果
画面里符合当下审美的女性身姿妩媚，佩戴金属配饰坐于象征权力的座椅。硬朗黑调与柔和粉调交锋，塑造出别具韵味的气场。

### 提示词
A lady in lace clothing sitting on a chair, in the style of dark black and pink, Bettina Rheims, detailed costumes, Bella Kotak, smooth and shiny, Japonism influenced pieces

### 中文翻译
一位穿着蕾丝服装的女士坐在椅子上，深黑色和粉色风格，贝蒂娜·兰斯（摄影师），细致的服装，贝拉·科塔克（摄影工作室），光滑闪亮，受日本主义影响的作品（注："日本主义"特指日本艺术、时尚与审美对于欧洲西方文化的影响。）

#### 风格定义
加入华伦天奴、复古美学、空灵质感等词汇展现奢侈品牌的古典风采，两张画面分别诠释不同张力的性感，进一步丰富魅力层次。

#### 图片效果
模特眼神坚定沉稳，散发出兼具力量感的性感气质。

### 提示词
Valentino 2017 Spring/Summer Collection, in the style of light bronze and white, Antebellum Gothic, delicate etchings, vintage aesthetics, 1960s, gemstone, ethereal quality

### 中文翻译
华伦天奴 2017 春夏系列，浅古铜色和白色风格，美国南北战争战前哥特式，精致蚀刻，复古美学，20 世纪 60 年代，宝石，空灵品质

## 唤醒独特性

#### 风格定义
画面呈现青春洋溢的状态，借助俏皮挪用、派对嘻哈、浅黄绿等表达，塑造时尚活泼的视觉调性。

#### 图片效果
背景点缀橙黄水果，女孩眼神透出不羁，妆发趣味十足，整体释放出独一无二的自信气场。

### 提示词
A blonde lady, strawberries, in the style of light aquamarine and orange, playful appropriation, party hiphop, the snapshot aesthetic, clowncore, detailed costumes, light yellow and green

### 中文翻译
一位金发女郎，草莓，浅海蓝和橙色风格，俏皮的挪用，派对嘻哈，快照美学，小丑核，细致的服装，浅黄色和绿色

#### 风格定义
本作把民族编织的手工质感与时尚结合，通过分层纤维、多层次、磨破感等关键词实现统一。

#### 图片效果
面料融入毛毡、编织、流苏，并搭配民族图案色调，既保有传统手作质感，又以模特表情带出当代时尚感。

### 提示词
Two models in different outfits on a desert, in the style of layered fibers, colorful, eye-catching composition, frayed, playful and whimsical designs, exaggerated features, appropriation, multilayered

### 中文翻译
两位身着不同服装的模特在沙漠上，分层纤维艺术风格，色彩丰富，构图抢眼，磨破的，俏皮且异想天开的设计，夸张的特征，挪用，多层次

## 活力四射

#### 风格定义
为增强轻松年轻感，引入有趣的新波普艺术提示，使整体氛围更显前卫活泼。

#### 图片效果
模特穿着亮色滑雪装，镜片反射出同伴嬉戏的身影，与冷调背景形成鲜明对比，青春活力扑面而来。

### 提示词
The model in ski gear wearing colorful ski goggles, in the style of onii kei, playful Neo-Pop, hard-edged, Voigtlander Brilliant

### 中文翻译
模特身着滑雪装备，戴着彩色滑雪镜，哥哥系风格，有趣的新波普艺术，锋利的，福伦达小百灵镜头

## 东方美

#### 风格定义
依靠新中式风格与好莱坞魅力两大关键词，已能让模型准确理解东方美感需求，故未额外加入相机描述。

#### 图片效果
服饰、场景、配色均具传统中国风特色，而人物面容采用欧美特征，塑造出钟爱东方美学的西方女性形象。

### 提示词
New Chinese style, a woman in green is standing on the stairs, in the style of gritty Hollywood glamour, Chinese cultural themes, dark orange and red, claire-obscure lighting, 1960s, photobash, lifelike figures

### 中文翻译
新中式风格，一位绿衣女子站在楼梯旁，坚韧不拔的好莱坞魅力风格，中国文化主题，暗橙色和红色，明暗对照式灯光，20 世纪 60 年代，照片拼贴，栩栩如生的人物

#### 风格定义
本图特意加入森养AF 14mm f/2.8 RF镜头提示，以帮助模型更精准捕捉东方之美。

#### 图片效果
林间绿意盎然，清风拂面，身着汉服的东方面孔女孩静立，发髻高挽，柔美优雅的东方气质跃然其上。

### 提示词
New Chinese style, a Chinese girl in traditional gown is watching grass near a tree, in the style of light green and dark gold, soft and dreamy depictions, fairycore, elegant clothing, dragon art, Samyang AF 14mm f/2.8 RF

### 中文翻译
新中式风格，身着传统长袍的中国女孩在树旁看草，浅绿色和暗金色的风格，柔美梦幻的描绘，仙女核，优雅的服装，龙的艺术，森养镜头AF 14mm f/2.8 RF

#### 风格定义
为让肌肤质感更逼真，加入写实主义风格提示，并保留森养AF 14mm f/2.8 RF镜头，以更精准呈现东方之美。

#### 图片效果
画面中东方女性眉眼利落，眼神凌厉，着装现代时尚，红唇与青绿竹林形成强烈碰撞，融合出典型东方意境。

### 提示词
In the style of contemporary Chinese art, a woman in a white dress with red lipstick standing against some bamboos, in the style of photorealist, rural China, haunting visuals, detailed facial features, film/video, luminous reflections, Orientalism, Samyang AF 14mm f/2.8 RF

### 中文翻译
当代中国艺术风格，一位身着白色连衣裙、涂着红色唇膏的女士站在竹子旁，写实主义风格，中国乡村，令人难忘的视觉效果，细致的面部特征，胶片/视频，明亮的反光，东方风格，森养镜头 AF 14mm f/2.8 RF

## 甜美可爱

#### 风格定义
运用可爱美学、仙女学院派、蕾丝花边、田园核等提示，生成甜美可爱的少女形象。

#### 图片效果
女孩怀抱白山羊，整体妆造以粉紫梦幻色调为主，萌趣叠加，甜美感被无限放大。

### 提示词
A woman holds a white goat in a field, in the style of kawaii aesthetic, webcam photography, violet and azure, fairy academia, shiny/glossy

### 中文翻译
一个女人在田野里抱着一只白山羊，可爱美学风格，网络摄像头摄影，紫色和蔚蓝色，仙女学院派，闪亮的/有光泽的

#### 图片效果
甜美少女身着洛丽塔雪纺裙，妆容清新粉嫩。

### 提示词
Wearing white dress with lace sleeves and a white bonnet, in the style of anime aesthetic, light pink and dark brown, matte photo, cottagecore

### 中文翻译
身穿白色连衣裙，袖子饰有蕾丝花边，头戴白色小帽，动漫美学风格，浅粉色和深棕色，哑光照片，田园核

## 元宇宙

#### 风格定义
为营造未来感与先锋气息，加入镀铬、详细科幻插图风格等关键词，用以构建未来想象的画面。

#### 图片效果
沙漠中出现形态奇异的植被与装置，人物服装在阳光下泛着金属光泽，宛若来自虚拟宇宙的女战士，整体散发外星来客般的氛围。

### 提示词
New single release — Desert, in the style of detailed science fiction illustrations, light green and pink, colorful animation stills, chrome-plated, sculptural costumes, made of flowers

### 中文翻译
新单曲发布——《沙漠》，详细的科幻插图风格，浅绿色和粉红色，彩色动画剧照，镀铬的，雕塑式的服装，花朵制成

# 第八章

## 流行趋势

# Popular Trends

流行趋势受政治、经济、文化、科技等多重因素驱动，既反映社会发展脉络，也映射生活理想。虽然潮流瞬息万变，但可提炼归纳，且每年关注焦点各不相同。

基于研究，我们将当下流行趋势概括为五大方向。年轻趣味：深受年轻人青睐，追求愉悦自由与多变体验。自然之境：强调贴近自然的悠闲本真。自在简行：注重舒适安心与简约无压。豪华经典：传递权力、金钱与奢华气场。动感休闲：融合运动元素与轻奢质感。

## 年轻趣味

#### 风格定义
本段引入西式肖像、Y2K美学、未来主义光学等元素，多重风格交织形成年轻快乐且具冲击感的画面。

"Y2K aesthetic（Y2K美学）"是以金属、科技、透明等为核心的穿搭风格，也叫"千禧风"。该风格的服饰具有一定的神秘感，其颜色大胆绚丽，迷幻和科技气息强烈，兼有复古和未来感。

为增添中国韵味，还加入清朝提示，使人物形象更具东方气质，服饰图案更显东方韵味。

#### 图片效果
绿色与粉色激烈碰撞，造型大胆前卫，眉形漂染处理，整体充满奇趣美感。

### 提示词
Featuring a woman in pink, in the style of Psychadelic Surrealism, Hayao Miyazaki, color clash, pink and emerald, Western-style portraits, Y2K aesthetic, Qing Dynasty, futuristic optics

### 中文翻译
画面主要呈现一个粉色系的女性，迷幻超现实主义风格，宫崎骏，色彩冲突，粉红色与翠绿色，西式肖像，Y2K 美学，清朝，未来主义光学

#### 风格定义
"old memecore（旧版迷因核）"是一种美学风格，是把一些经典形象与当下时尚元素结合，从而形成一种怪异、有趣又时尚的效果。

马克·奎因（Marc Quinn）出生于1964年，是来自英国的当代艺术家。他的作品通常探索身体、自然和人类存在的主题，在作品中，人工手段和现代技术介入自然生命，形成迷人又疯狂的植物个体。在提示词中加入"Marc Quinn"，可以生成具有雕塑感的物品和植物元素，使画面更丰富。

#### 图片效果
同框中放大原本体积较小的物体，形成别样的时尚趣味。

### 提示词
Green boots and a model in black, in the style of absurdist installations, Marc quinn, old memecore, elongated figures, soggy

### 中文翻译
绿色靴子和穿黑衣的模特，荒诞派装置艺术风格，马克·奎因，旧版迷因核，拉长的人物，湿漉漉的

#### 风格定义

## 几何拼贴DIY
#### 创作思路
想让自制日用品的画面更抓人，特意引入新造型主义、凌乱质感和旧迷因核的引导，产出偏向简化抽象、线条明显的几何感画面。
#### 视觉呈现
色块与材质层层堆叠，植感与虫感并置，碰撞出奇特又带点戏谑的视觉趣味。
### 提示词
High heel boots, in the style of Neoplasticism, dark navy and light green, oversized objects, messy, old memecore, celebrity image mashup
### 中文翻译
高跟靴，新塑造主义风格，深蓝色和浅绿色，超大物件，凌乱，旧版迷因核，名人形象混搭

## 霓虹未来风
#### 风格定义
为凸显霓虹光泽，将灯光的绚烂质感与时装元素融合，再叠加酸雨、未来朋克的调性，视觉冲击更强。
#### 视觉呈现
模特选用透或半透的亚克力、塑料面料，在灯光折射下泛起霓虹色彩，整体氛围浓郁未来朋克。
### 提示词
Laser material fashion show, techno, acid rain, future punk, Sony FE GM, symmetrical composition, panorama, sparkle, UHD, super detail
### 中文翻译
激光材料时装秀，科技舞曲，酸雨，未来朋克，索尼 FE GM，对称构图，全景，闪光，超高清，超级细节

## 都市牧场工装
#### 风格定义
受城市园艺与露营热潮带动，田园感轻工装走红。引入牧场、田园等词，帮助营造闹中取静的自然氛围。
#### 视觉呈现
两位男模身着清新色调的轻工装，衣料上的淡雅印花与周边的花卉相互呼应，广告画报般的质感让时尚与自然更贴近。
### 提示词
Pastures, idyllic, the men in shorts and a vest stand against a building and flowers, in the style of light indigo and light green, Eastern and Western fusion, Y2K aesthetic, advertisement inspiration, color materiality, English countryside scenes --ar 73:100
### 中文翻译
牧场，田园的，穿短裤、马甲的男人背靠建筑和鲜花站立，浅靛和浅绿风格，东西方融合，Y2K美学，广告灵感，色彩物质性，英国乡村场景，宽高比73:100

## 自然之境·生物互动
#### 风格定义
皮埃尔·于热生于1962年，是观念艺术领域代表人物。他的作品常糅合生物元素，聚焦生命行为与彼此互动。为了强化环境对话感，可补充互动装置的指令。

## 自然之境·雾境快照
#### 风格定义
天气核美学模拟风动瞬间，能赋予画面云雾缭绕的空气感。配合快照美学的指引，艺术张力得到提升，形成独有的观看方式。
#### 视觉呈现
在寒冷的雪山脚下，一座暖调小屋点亮画面，宁静安然，仿佛远离喧嚣。
### 提示词
The building is next to a snow covered mountain, in the style of photo-realistic landscapes, snapshot aesthetic, weathercore, shoot, poster, detailed world-building
### 中文翻译
该建筑毗邻一座雪山，写实主义风景的风格，快照美学，天气核，拍摄，海报，细节丰富的世界建筑

## 自然之境·树脂悬崖
#### 风格定义
浇注树脂是一种透明或半透明的液态材料，加入后建筑显得通透灵动。搭配海边悬崖酒店的设定，能展现自然景观与现代设计的融合之美。
#### 视觉呈现
画面呈现丛林深处的一座现代建筑，线条柔和，仿佛隐身于自然之中，带着高级共生的质感。
### 提示词
A hotel built on a cliff by the sea, in the style of mysterious jungle, nature-inspired compositions, 32K UHD, poured resin, romantic riverscapes, rounded forms
### 中文翻译
建在海边悬崖上的酒店，神秘丛林风格，自然灵感构图，32K超高清，浇注树脂，浪漫河景，圆形造型

## 自然之境·野性妆效
#### 风格定义
Midjourney在刻画肌肤细腻度上表现突出。为生成自然安全的面部产品视觉，可用眼影与自然迷彩的关键词，将产品与自然特质关联，从而更好地呈现面部细节，提升吸引力。
#### 视觉呈现
妆效像是把藻泥或泥土涂于肌肤，粗犷而具野性，冲击力十足。
### 提示词
A brown and black mix of eyeshadow, in the style of rough clusters, nature-inspired camouflage
### 中文翻译
棕色和黑色混合的眼影，粗犷团块风格，自然迷彩

## 自然之境·柔光乡野
#### 风格定义
追求事物本真的姿态与原始特征。加入可爱梦幻、柔美有机等指引，能让女性形象更显柔情且时尚。
#### 视觉呈现
柔和阳光洒在古朴村落，幽静的环境与女性温柔的气质相合，整体氛围安宁自然。
### 提示词
A woman standing on the rooftop of a house, cute and dreamy, rural China, softly organic, rusticcore, travel
### 中文翻译
一个站在房子的屋顶上的女人，可爱梦幻，中国乡村，柔美有机，乡村核，旅行

## 自然之境·原石家居
#### 风格定义
装饰的设计灵感多取自自然界岩石、树木的形态与肌理。
#### 视觉呈现
墙面石块与地面的石凳都保留了原始形态，粗糙的质感营造出朴素自然的居家氛围。
### 提示词
The living room has decorative rocks, large potted plants and a rocking chair, in the style of earth tone, indoor still life, woven/perforated, wood
### 中文翻译
起居室摆放着装饰性的石块、大型盆栽和摇椅，大地色风格，室内静物，编织的/穿孔的，木质

## 刺绣家居风
#### 风格定义
碎花图案的灵感源于传统家居装饰。棉布、府绸上常布满立体刺绣与清晰线条。配合浅橙绿配色、拜占庭风格以及白色刺绣衬衫的提示，可生成简约而美观的服饰。
#### 视觉呈现
人物身着棉麻质感的衬衫，造型简洁却很舒适，传统家居风格的图案增添自在安心的感受。
### 提示词
A woman wearing white embroidered shirts in front of a wall, in the style of whimsical folk, light orange and green, birds and flowers, Byzantine-inspired, photo taken with Provia, playful and whimsical
### 中文翻译
一位身穿白色刺绣衬衫的妇女在墙壁前，奇异民俗风情，浅橙色和绿色，鸟类和花卉，拜占庭风格，使用普罗维亚胶片拍摄的照片，俏皮而异想天开

## 格纹田园餐桌
#### 风格定义
马德拉斯格纹由多彩方格构成，嘉顿格纹则以等大的正方形呈现，既有古典优雅也有现代活力。将它们作为关键词能让主体与画面更协调。Cottagecore，即田园核风格，体现对乡村生活与自然简约的审美追求。
#### 视觉呈现
简洁的织物与朴素的生活用品搭配柔和色调，令人放松，桌上的野花则带来一丝自在愉悦感。
### 提示词
Rustic dining room in Madras check and Gingham check, cottagecore and rosy rustic tablecloth, linen fabrics, add a new twist
### 中文翻译
采用马德拉斯格纹和嘉顿格纹的乡村餐厅，田园核和玫瑰色质朴桌布，亚麻面料，添加新的变化

## 金白奢华烛台
#### 风格定义
加入极致奢华、宝石等关键词可提升画面华丽度，方形指令让主体更具棱角，二者结合能呈现豪华与经典兼具的气质。
#### 视觉呈现
眼前出现由金色金属与白大理石打造的烛台，用料考究。搭配鲜花后，整体显得优雅且富贵。
### 提示词
The white and golden square candle sits on a mantel, ultimate luxury :1.5, gemstone, suspended/hanging, decorative details, romantic charm
### 中文翻译
白色与金色的方形蜡烛放在壁炉架上，极致奢华:1.5，宝石，悬挂着的，装饰细节，浪漫魅力

## 撞色运动混搭
#### 风格定义
使用“男子坐在绿色椅子上，脚穿彩色运动鞋”的指引，画面大概率以黄绿为主调。模特放松的坐姿与运动风混搭服饰形成有趣组合。
#### 视觉呈现
模特身着印花西装，搭配运动鞋与冷帽，色彩明快风格年轻，与奢华场景融合后传递出享乐氛围，整体类似奢侈品牌与运动品牌联名的广告海报。
### 提示词
The man is sitting in a green chair and is wearing colorful sneakers, in the style of layered textures and patterns, commercial imagery, white and gold, bold patterns, UE5 --ar 3:2
### 中文翻译
男子坐在绿色椅子上，穿着彩色运动鞋，采用分层纹理和图案风格，商业图像，白色和金色，大胆的图案，虚幻引擎5，宽高比3:2

## 高山轻奢运动
#### 风格定义
随着消费者户外运动需求变化，奢侈品牌的运动服饰与装备正趋向时尚化。加入有限色域的提示，可控制整体饱和度，更显高级质感。
#### 视觉呈现
画面中人物以悠闲姿态坐于雪山之上，松弛的气质与周围纯净的环境自然融合。
### 提示词
Actress models sport outfit for fall/winter ad, in the style of light beige and white, mountainous vistas, refined aesthetic sensibility, snow scenes, ready-made, limited color range
### 中文翻译
女演员为运动品牌秋冬服装广告的拍摄做模特，浅米色和白色的风格，山间美景，精致的审美感受，雪景，现成的，有限的颜色范围