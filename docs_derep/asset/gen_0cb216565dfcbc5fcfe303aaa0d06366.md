# 一文拆解神经风格迁移的损失拼装逻辑

想让一张照片保留原图结构、又染上另一张画的笔触，关键就在于把内容保真、风格迁移和图像平滑三项目标揉进同一个可优化的损失里。下面这套实现思路，把每一步都拆得清清楚楚，方便直接复用到自己的项目中。

先搭好模型各层的引用，再锁定负责内容的那一层，用像素级特征差异来衡量结构是否走样。核心就是把目标特征和合成特征的平方差累加，并用 CONTENT_WEIGHT 控制它的比重。

[PRE0]

def content_loss(content, combination):

    return backend.sum(backend.square(combination - content))

layers = dict([(layer.name, layer.output) for layer in model.layers])

content_layer = "block2_conv2"

layer_features = layers[content_layer]

content_image_features = layer_features[0, :, :, :]

combination_features = layer_features[2, :, :, :]

loss = backend.variable(0.)

loss += CONTENT_WEIGHT * content_loss(content_image_features, combination_features)

风格部分则依赖 Gram 矩阵来捕捉纹理相关性。先把特征图在通道维度上搬出来做 flatten，再自相关得到 Gram，再用归一化的平方误差衡量两张图的风格距离。VGG 的多层特征会被统一加权，STYLE_WEIGHT 决定整体风格强度。

[PRE1]

def gram_matrix(x):

    features = backend.batch_flatten(backend.permute_dimensions(x, (2, 0, 1)))

    gram = backend.dot(features, backend.transpose(features))

    return gram

def compute_style_loss(style, combination):

    style = gram_matrix(style)

    combination = gram_matrix(combination)

    size = IMAGE_HEIGHT * IMAGE_WIDTH

    return backend.sum(backend.square(style - combination)) / (4. * (CHANNELS ** 2) * (size ** 2))

style_layers = ["block1_conv2", "block2_conv2", "block3_conv3", "block4_conv3", "block5_conv3"]

for layer_name in style_layers:

    layer_features = layers[layer_name]

    style_features = layer_features[:, :, :, :]

    combination_features = layer_features[:, :, :, :]

    style_loss = compute_style_loss(style_features, combination_features)

    loss += (STYLE_WEIGHT / len(style_layers)) * style_loss

光靠内容和风格容易产生噪点，需要全变差正则来压平不必要的抖动。水平和垂直方向的邻域差被平方后求和，再用幂次因子调节平滑强度，最后乘上 TOTAL_VARIATION_WEIGHT 加入总损失。

[PRE2]

def total_variation_loss(x):

    a = backend.square(x[:, :IMAGE_HEIGHT-1, :IMAGE_WIDTH-1, :] - x[:, 1:, :IMAGE_WIDTH-1, :])

    b = backend.square(x[:, :IMAGE_HEIGHT-1, :IMAGE_WIDTH-1, :] - x[:, :IMAGE_HEIGHT-1, 1:, :])

    return backend.sum(backend.pow(a + b, TOTAL_VARIATION_LOSS_FACTOR))

loss += TOTAL_VARIATION_WEIGHT * total_variation_loss(combination_image)

有了总目标函数，就需要能同时拿到损失值和梯度。为此先把损失和对合成图求梯度打包成输出，再封装一个评估函数做形状调整、喂入后端计算图并把梯度展平返回。Evaluator 类把梯度缓存起来，方便优化器按需调用。

[PRE3]

outputs = [loss]

outputs += backend.gradients(loss, combination_image)

def evaluate_loss_and_gradients(x):

    x = x.reshape((1, IMAGE_HEIGHT, IMAGE_WIDTH, CHANNELS))

    outs = backend.function([combination_image], outputs)([x])

    loss = outs[0]

    gradients = outs[1].flatten().astype('float64')

    return loss, gradients

class Evaluator:

    def loss(self, x):

        loss, gradients = evaluate_loss_and_gradients(x)

        self._gradients = gradients

        return loss

    def gradients(self, x):

        return self._gradients

evaluator = Evaluator()

[PRE4]
[PRE5]
[PRE6]

训练收敛后，习惯性地把输入、风格源和最终合成结果并排保存，方便肉眼对比效果。创建一个三倍宽的画布，依次粘贴三张图，再导出即可。

# Visualizing combined results

combined = Image.new("RGB", (IMAGE_WIDTH*3, IMAGE_HEIGHT))

x_offset = 0

for image in map(Image.open, [input_image_path, style_image_path, output_image_path]):

    combined.paste(image, (x_offset, 0))

    x_offset += IMAGE_WIDTH

combined.save(combined_image_path)

combined