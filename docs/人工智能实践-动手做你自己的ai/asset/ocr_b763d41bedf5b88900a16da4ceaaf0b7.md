# ### 思考与实践

6.1 你可以尝试赋予各损失函数不同的权重比例，观察其对生成图像的影响。

计算内容损失并加到总损失中。

```
# 计算内容损失
def get_content_loss(content_features, output_features):
    return 0.5 * K.sum(K.square(output_features - content_features))

# 取conv4_2的输出作为内容表示
layer_feat = output_dict['block4_conv2']
content_feat = layer_feat[0, :, :, :]
output_feat = layer_feat[2, :, :, :]
# 将内容损失加入到总损失中
loss += content_weight * get_content_loss(content_feat, output_feat)
```

计算风格损失并加到总损失中。

```
# 计算图像张量的格拉姆矩阵，用于捕捉图像的风格
def get_gram_matrix(x):
    if K.image_data_format() == 'channels_first':
        feature_matrix = K.batch_flatten(x)
    else:
        feature_matrix = K.batch_flatten(K.permute_dimensions(x, (2, 0, 1)))
    gram_matrix = K.dot(feature_matrix, K.transpose(feature_matrix))
    return gram_matrix

# 计算单个卷积核的风格损失
def get_style_loss(style_features, output_features):
    G = get_gram_matrix(style_features)
    A = get_gram_matrix(output_features)
    if K.image_data_format() == 'channels_first':
        channel_number = int(style_features.shape[0])
        size = int(style_features.shape[1] * style_features.shape[2])
    else:
        channel_number = int(style_features.shape[2])
        size = int(style_features.shape[0] * style_features.shape[1])
    return K.sum(K.square(G - A)) / (4. * (channel_number ** 2) * (size ** 2))
```