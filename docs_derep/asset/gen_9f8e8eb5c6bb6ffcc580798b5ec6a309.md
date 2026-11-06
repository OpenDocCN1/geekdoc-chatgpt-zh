#### 6.1.2 外形打磨与身份标签
有了基础的人物锚点后，下一步就是对长相进行精细化刻画。整体可以拆成外貌、体型、衣装三条线来写。

外貌主要落在发型、五官、肤色上。发型还能再细化成长短、发色、造型，比如“棕色短卷发女孩”，对应的英文提示是 a girl, brown short curly hair，效果如图 6-7 所示。体型可以用身高体重来量化，也可以用 tall、short、giant、dwarf 这类形容词，1.76 米这种具体数值同样可用。衣装部分既能写具体单品 T-shirt、coat、jeans，也可以用概括词 regular clothes、sleepwear、armor、robe。

[PRE0]

图 6-7 棕色短卷发女孩

身份和职业本身就是很强的造型线索，从身份可以直接反推服饰和面部特征。比方唐代仕女，常见的是色彩艳丽的交领襦裙，外搭飘逸披帛，发髻上插金玉簪或鲜花，面部点花钿，手持团扇或执壶，体态丰腴温婉、面孔偏东亚。由此就能提炼出 Tang dynasty noble lady、cross-collar ruqun、flowing pibo、jade hairpin、flower hair accessory、huadian face decoration、round fan 等关键词，组合后能稳定还原如图 6-8 的风格。

[PRE1]

图 6-8 仕女

想做欧洲中世纪骑士，就把民族、时代、职业放一起。骑士多是白人形象，配合盔甲，再加上金发蓝眼的常见特征，一套可用提示是 masterpiece, highres, 8k, a boy, gold hair, blue eyes, medieval knight, wearing armor，生成效果如图 6-9。

[PRE2]

图 6-9 欧洲中世纪骑士

#### 6.1.3 细节质感 让角色更可信
基础造型确定后，增加皮肤、纹理、材质等细节，能让人物更饱满真实。常见的人物特征有 tattoo、scar、the texture of the skin，异种族角色还能写鳞片等种族特征。服装材质和光影反馈也属于细节层。

比如一只穿盔甲的蜥蜴人，重点在粗糙绿鳞与金属反光。提示词可写 lizardman, roughness green scale, steel armor, reflecting the dazzling sunlight，得到如图 6-10 的结果。

[PRE3]

图 6-10 身穿钢制盔甲的蜥蜴人

#### 6.1.4 动作神态注入故事感
主体形象确定后，加上动作和表情能立刻提升叙事性。动作类常用的有 standing、sitting、lying、half squat，肢体细节可以用 spread arms、手放在脸上 hand on own face、手臂在脑后 arms behind head 等来丰富体态。dynamic pose、floating hair 这类动态词也能增强张力，如图 6-11。

[PRE4]

图 6-11 肢体动作表现体态

特写时表情词非常关键，能避免呆板。基础情绪有 calmly、happy、sad、furioso，微表情可以写 cocked eyebrow、frown 等，效果如图 6-12。颜文字和 emoji 也能作为正向或反向提示词使用。

[PRE5]

图 6-12 表情特写

#### 6.1.5 从零到一的完整提示词案例
图 6-13 是用 Stable Diffusion 生成的古代剑客，拆解如下：

先定主体：ancient Chinese swordsman 或 male swordsman。
再写五官发型：tied-up black hair、sharp black eyes、下颌 light stubble on jaw。
接着写衣装：dark tight-fitting warrior outfit，细节化为 dark blue warrior robe with black waist sash、wearing black boots with leather straps、carrying a long sword on back。
最后加动态情绪：standing with one hand on sword hilt、focused gaze，再补光感 soft martial arts movie lighting。

完整写法：masterpiece, intricate details, an ancient Chinese swordsman with tied-up black hair, sharp black eyes, light stubble on jaw, dark blue warrior robe with black waist sash, wearing black boots with leather straps, carrying a long sword on back, soft martial arts movie lighting, standing with one hand on sword hilt, focused gaze，中文意思为杰作、精细细节、束黑发的中国古代剑客、锐利黑眸、下颌微胡、深蓝武士袍配黑腰带、皮革绑带黑靴、背挎长剑、柔和武侠电影光、单手按剑柄站立、眼神专注。

[PRE6]

图 6-13 古代剑客人物

想让风格更独特，可以引用艺术家或作品。像俄罗斯插画师 Ilya Kuvshinov，曾参与《攻壳机动队》角色绘制，风格常把欧美特征融进日系。加入 by Ilya Kuvshinov 和 Ghost in the shell 后，画面会带上他的倾向，如图 6-14。

[PRE7]

图 6-14 带有伊利亚·库夫希诺夫插画师风格倾向的人物

综合来看，人物的通用模板是：角色基础提示词 + 外貌特征即发型五官服饰 + 面部与服饰细节 + 动态表情 + 参考示例，实际使用时可灵活调换顺序。

#### 6.1.6 人物三视图快速搭建
做角色设定常需要三视图，即正面、侧面、背面的组合，包含主视图、侧视图、后视图。

在 Stable Diffusion 里，核心词是 three views、full body、front view、side view、back view、multiple views，侧视图可换成 left view/right view。加 white background 或 simple background 能压住背景干扰，保证画面聚焦人物。三视图中权重最高的是 three views。

其余部分照旧写体貌、着装和画质。以黑发校服女孩为例：(three views of character:1.2), full body, front view, left view, back view, multiple views, a girl, black hair, school uniform, simple background, masterpiece, best quality，生成如图 6-15。

[PRE8]

图6-15 黑发校服女孩三视图

图 6-15 里左视图眼色和正视图不一致，这时要通过追加关键词微调。若正向词反复试仍不稳，可用反向提示词屏蔽不想要的元素。调整后结果如图 6-16。

[PRE9]

图 6-16 调整后的人物三视图

### 6.2 场景构建的元素堆叠
人物之外，场景是画面另一大支柱。通过分层堆叠环境要素，能搭建出逻辑清晰、有氛围、有叙事感的空间。合理的堆叠能让模型更准确理解画面，避免元素打架或空洞。

#### 6.2.1 由大到小分层递进
场景提示词也有阅读顺序，既影响模型解析优先级，也决定画面完整性。

第一步，确定核心主题，并与人物匹配，是未来科技工厂还是森林魔法小屋，这是基础。
第二步，搭建骨架。最基础的四要素是时间、环境、地点、风格。环境和地点作为主体，跟在时间后面，一起围绕核心主题做粗线条构思。
第三步，用风格词收束整体调性，保证画面统一。

框架确定后，再细化。时间能改变氛围，既可以是中世纪、蒸汽时代、石器时代这类大时代，也可以是早上、上午、下午、晚上这类具体时段，如图 6-17。

[PRE10]

[PRE11]

早上

晚上

[PRE12]

[PRE13]

上午

下午

图 6-17 不同时间段（点）提示词生成的场景

环境常写天气季节，比如晴天、雨天、雾天，天气对色彩情绪影响很大。季节非必需，按需增删，四季植被与色调差异明显，如图 6-18。

[PRE14]

[PRE15]

春季

夏季

[PRE16]

[PRE17]

秋季

冬季

图 6-18 不同季节提示词生成的场景

地点权重很高。室内可写布局、装饰、装修风格；室外可写地形、地貌、植被、人造建筑。

把上述要素串联，例如初夏午后花园：early summer afternoon in the garden, green moss grows on the towering stone walls, thick grass, flowers of all colors in full bloom, mountain，中文为初夏下午花园，高耸石墙长青苔、草茂、花繁、山景，效果如图 6-19。

[PRE18]

图 6-19 初夏花园场景

串联时可微调顺序，但整体场景词始终优先于细节词。

#### 6.2.2 空间方位与层次关系
场景成型后，需要避免元素杂乱。明确方位和层次，能让模型合理排布。

常用方位词有上下、左右、前后、中间、边缘、角落，比如“左侧窗边放白色烤漆实木书桌”“咖啡店前种各式花”等。空间层次可用前景、中景、背景区分。

例如：the foreground is a painter sitting on a chair, with unfinished canvases on the easel to the left of the middle scene, three abstract paintings hanging on the background wall，中文是前景画家坐木椅，中景左侧画架放未完成画布，背景墙挂三幅抽象画，生成如图 6-20。

[PRE19]

图 6-20 场景的空间层次排布

此外可用环绕、交错、遮挡等关系词，让元素互相参照，保持画面秩序。

### 6.3 多风格混搭创造独特性
Stable Diffusion 自由度高，完全可以跨领域、超现实、文化混搭。关键是先锁定主体，避免风格冲突覆盖主体。

风格融合有三种常见写法：比例加权、分步控制、层级叠加。比例融合如 gongbi style::oil painting style::2，表示工笔画与油画 2:1；或用步数控制 [gongbi style:oil painting style:20]。层级叠加则写 gongbi style AND oil painting style，生成如图 6-21。

[PRE20]

图 6-21 工笔画风格和油画风格

同理，波西米亚风与极繁主义可写 bohemian style::maximalism style::比例数值，或 [bohemian style:maximalism style:步数]，层级叠加写 bohemian style AND maximalism style，效果如图 6-22。

[PRE21]