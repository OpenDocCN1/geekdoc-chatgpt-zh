## 提示词加词就能换风格

在编写提示词时，核心主题是必不可少的，其余描述都是可选择的。

举个例子，想画一只猫站在书上，可以这样写提示词：

[PRE0]

在 Web UI 上按下生成按钮后，得到的效果如图 8-3 所示。

[PRE1]

如果希望把画面转为油画质感，只需要在原词后面补上对应的风格词即可，这时提示词变成：

a cat standing on a book, **oil painting**

按下生成后，效果如图 8-4 所示。

[PRE2]

图 8-4

画面初看效果一般，但已经能看出关键词确实把风格推向了油画。

可以再深入一点，模拟梵高的笔触，把关键词改成：

a cat standing on a book, oil painting, **Vincent van Gogh**

按下生成后，效果如图 8-5 所示。

[PRE3]

图 8-5

可以看到笔触明显带有梵高的特征。继续叠加关键词，还能得到更多风格变体。

### 8.2.2 关键词加权玩法

提示词里每个词的重要性都可以调节。用小括号包住的词权重会提升，用中括号包住的词权重会降低。

具体规则如下。

- 1. 提升权重

想让某个词更突出，就用半角小括号括起来，例如“（关键词）”。

默认情况下，小括号里的词权重会提升到原来的 1.1 倍，也可以在括号末尾直接写数字来指定倍数。

表 8-1 列出了常见写法。

表 8-1

| 示例 | 说明 |
| :--- | :--- |
| (关键词) | 权重为 1.1 |
| (关键词:1.1) | 权重为 1.1，与上一条效果相同 |
| (关键词:1.5) | 权重为 1.5 |
| ((关键词)) | 权重为 1.1×1.1=1.21 |
| (((关键词))) | 权重为 1.1×1.1×1.1=1.331 |

- 2. 降低权重

想让某个词作用变弱，就用半角中括号括起来，例如“[ 关键词 ]”。

默认情况下，中括号里的词权重会降到原来的 90%。用法与提升权重类似，见表 8-2。

表 8-2

| 示例 | 说明 |
| :--- | :--- |
| [关键词] | 权重为 0.9 |
| [关键词:0.9] | 权重为 0.9，与上一条效果相同 |
| [关键词:0.5] | 权重为 0.5 |
| [[关键词]] | 权重为 0.9×0.9=0.81 |
| [[[关键词]]] | 权重为 0.9×0.9×0.9=0.729 |

- 3. 用例

比如想生成一张有猫和花的照片，提示词为：

A cat, flower, photo

得到的图片如图 8-6 所示。

[PRE4]

如果想让花在画面里更明显，就提升花的权重，同时保持其他设置和随机种子不变。新的提示词为：

A cat, (flower:1.2), photo

新图如图 8-7 所示。

[PRE5]

可以看到花的占比确实变大了。

### 8.2.3 渐变融合技巧

提示词还支持渐变语法，可以让一个元素平滑过渡到另一个元素。格式为 [ 关键词 1: 关键词 2: 因子 ]。

因子取值在 0 到 1 之间，表示关键词 1 的占比。数值越小结果越偏向关键词 1，数值越大越偏向关键词 2。

用这个方法可以合成两位名人之间的面孔，比如“[ 名人 1: 名人 2:0.5]”会生成介于两者之间的新面孔，前提是模型里有这两人的数据。

也可以这么写：[ 老人 : 名人 :0.5]，前者是泛称如“old man”，模型会先生成一个老年男子的样貌，再向指定的具体人名如“Albert Einstein”进行渐变。

图 8-8 ～图 8-10 就是一个示例，从左到右，渐变因子从 0.75 降到 0.25，第二个关键词“Albert Einstein”的影响越来越强，人物的长相也越来越接近爱因斯坦。

[PRE6]

[PRE7]

[PRE8]

### 8.2.4 LoRA 模型调用

在提示词里接入 LoRA 来调整内容或风格，语法为 <lora: 文件名 : 权重 >。

“文件名”是不带扩展名的 LoRA 文件名；权重默认为 1，设为 0 表示关闭，可大于 1 来增强效果，但过大会适得其反。多个 LoRA 可以叠加使用。

部分 LoRA 仅调用语法即可，部分 LoRA 还需要配合触发词一起使用，具体说明通常在下载页或文档里。

记不住文件名也没关系，点击生成按钮下方“显示 / 隐藏额外网络”按钮，在提示词下方打开额外网络面板，切换到“Lora”标签，就能看到已安装的全部 LoRA，如图 8-11 所示。

[PRE9]

点开某个 LoRA 卡片，提示词框会自动插入引用，例如“<lora:add_detail:1>”，表示调用 add_detail 这个 LoRA，权重为 1，后续可以手动改数字。

当基础模型效果不理想时，尝试合适的 LoRA 往往能带来明显提升。

## 8.3 实战创作流程

下面用一个具体案例演示 Stable Diffusion 的基本流程。

### 8.3.1 首次生成

如果要画一个女孩在图书馆读书的场景，可以用以下设置：

- 模型：Reliberate ①

- 采样方法：DPM++ 2M Karras

- 迭代步数：25

[PRE10]

提示词：

a girl, setting in a library, reading a book, smile

反向提示词：

disfigured, ugly, bad, immature, cartoon, anime, 3d, painting, b&w

提示词描述了主体：一个女孩、图书馆环境、读书、微笑。反向提示词用来规避不想出现的内容：disfigured 毁容、ugly 丑陋、bad 质量差、immature 不成熟、cartoon 卡通、anime 动画、3d 三维、painting 绘画、b&w 黑白。

根据需求可以增减反向词，这里为了更像照片，去掉了卡通、动画、3D、绘画等倾向。

生成的图片如图 8-12 和图 8-13 所示。

[PRE11]

[PRE12]

图 8-13 中小女孩手指出现了问题，这是 AI 绘画的常见通病。对策主要有：

- 避免手指入镜，如只画半身或把手背在身后。
- 在反向词里加入 “bad hand”“extra fingers”等，但仍可能出错。
- 多次生成挑一个最好的版本。
- 用 Web UI 的“局部重绘”功能修复手部。
- 导出到 Photoshop 等软件手动修改。

下面重点讲局部重绘的使用。

### 8.3.2 细节调整

整体很满意但局部有瑕疵时，重绘比完全重来更省心。在“文生图”界面生成后，点击预览区下方的 “>> 重绘”按钮，就能把当前图送到“图生图”重绘面板，如图 8-14 所示。

也可以直接进入“图生图”界面，选择“局部重绘”标签，上传需要修改的图片。

商用级AIGC绘画创作与技巧 (Midjourney+Stable Diffusion)

图 8-14

在局部重绘面板，用鼠标涂黑需要修改的区域，如图 8-15 红框所示。下方还有诸多参数，可按需调整。

图 8-15

局部重绘带随机性，不能保证一次成功，有时效果会更差，需要多试几次。图 8-16 和图 8-17 是原图与重绘后的对比。

图 8-16 原图

图 8-17 局部重绘后的图

### 8.3.3 其他调整

模型 Reliberate 画人时默认偏向欧美白人风格，若想更换种族，只需在提示词里加上 “Asian”。

提示词：

a girl, Asian, setting in a library, reading a book, smile

反向提示词：

disfigured, ugly, bad, immature, cartoon, anime, 3d, painting, b&w, bad hand, extra fingers

保持之前的随机数种子，按下生成，得到如图 8-18 所示的图片。

图 8-18

场景基本保留，人物变成了亚洲女孩。手指问题依然存在，还需继续局部修正。

以上就是 Stable Diffusion 绘制图片的常用流程，有时一次成功，有时需要反复微调。

## 8.4 人物肖像

Stable Diffusion 有很多擅长人像的第三方模型，下面以两例说明。

### 8.4.1 示例 1：年轻女子 1

模型：

Reliberate

提示词：

A close up portrait of young woman looking at camera, long haircut, bangs, slim body, dark theme, soothing tones, muted colors, high contrast, (natural skin texture, hyperrealism, soft light, sharp), (((perfect face))), grunge clothes, Sony a7ii

反向提示词：

nsfw, disfigured, ugly, bad, immature, cartoon, anime, 3d, painting, b&w, bad anatomy, wrong anatomy, ((bad hand)), ((extra fingers))

生成的图片如图 8-19 和图 8-20 所示。

### 8.4.2 示例 2：年轻女子 2

提示词和反向提示词与示例 1 完全一致，仅把模型换成 majicMIX realistic ①，生成的图片如图 8-21 和图 8-22 所示。

即使参数一样，模型不同结果也大不相同。Reliberate 默认偏欧美面孔，majicMIX realistic 则偏亚洲面孔，这与训练素材有关。

## 8.5 动漫风格

Stable Diffusion 也能很好地生成动漫风格画面。

### 8.5.1 示例 1：红发少年

模型：

MeinaMix①

提示词：

1boy, portrait, pompadour hair, red hair, little smile, yellow eye, black jacket, medieval, village burning at night

反向提示词：

EasyNegativeV2

采样方法：

DPM++ SDE Karras

生成的图片如图 8-23 和图 8-24 所示。

### 8.5.2 示例 2：紫发少女

模型：

GhostMix②

提示词：

(masterpiece, top quality, best quality, official art, beautiful and aesthetic:1.2), (fractal art:1.3), 1girl, beautiful, high detailed, purple hair with a hint of pink, pink eyes, dark lighting, serious face, looking the sky, sky, medium shot, black sweater, jewelry

反向提示词：

badhandv4, easynegative, ng_deepnegative_v1_75t, semi-realistic, sketch, duplicate, ugly, huge eyes, text, logo, worst face, (bad and mutated hands:1.3), (worst quality:2.0), (low quality:2.0), (blurry:2.0), horror, ((geometry)), bad_prompt, (bad hands), (missing fingers), multiple limbs, bad anatomy, (interlocked fingers:1.2), Ugly Fingers, (extra digit and hands and fingers and legs and arms:1.4), ((2girl)), (deformed fingers:1.2), (long fingers:1.2),(bad-artist-anime), bad-artist, bad hand, extra legs, furry, animal_ear, nsfw

采样方法：

DPM++ 2M Karras

为避免生成不当内容，反向词里加入了“nsfw”。

生成的图片如图 8-25 和图 8-26 所示。

### 8.5.3 示例 3：迪士尼 3D 风格

模型：

Disney Pixar Cartoon Type A ①

提示词：

1girl, holding a camera, bangs, beach, blue_sky, blush, bow, checkered, checkered_shirt, checkered_skirt, cloud, cloudy_sky, grass, hair_bow, heart, holding, holding_letter, horizon, masterpiece, high quality best quality, leaning_forward, lens_flare, light_rays, long_hair, looking_at_viewer, mountain, mountainous_horizon, ocean, outdoors, plaid, plaid_background, plaid_bow, plaid_bowtie, plaid_dress, plaid_headwear, plaid_jacket, plaid_legwear, plaid_necktie, plaid_neckwear, plaid_panties, plaid_pants, plaid_ribbon, plaid_scarf, plaid_shirt, plaid_skirt, plaid_vest, pov, shirt, skirt, sky, smile, solo, sun, sunbeam, sunlight, tree, unmoving_pattern

反向提示词：