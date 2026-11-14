经过预处理，然后放到判别器中），所以就一起打包，这相当于两个 batch 的数据进行处理然后塞进判别式中，得到结果后再按照打包的先后顺序将它们分开即可。这种做法只用一套代码就可以完成真实图片和生成图片的处理。

# ## 4. 计算VGG特征空间的loss

VGG 中的前 5 个卷积层用于特征提取，所以在使用时，只取其第 5 个卷积层的输出节点，其他的节点可以全部忽略。那么问题来了，如何能拿到模型中的指定节点呢？可以通过 slim 中 nets 文件夹下对应的 VGG 源码找到对应节点的名称。这里使用了一个更简单的方法：直接在 models\slim\nets 文件夹下打开 “vgg_test.py” 文件，在第 50 行中可以找到 testEndPoints 函数，其内容如下：

......

```
def testEndPoints(self):
    batch_size = 5
    height, width = 224, 224
    num_classes = 1000
    with self.test_session():
        inputs = tf.random_uniform((batch_size,height, width, 3))
        _, end_points = vgg.vgg_19(inputs, num_classes)
        expected_names = [
            'vgg_19/conv1/conv1_1',
            'vgg_19/conv1/conv1_2',
            'vgg_19/pool1',
            'vgg_19/conv2/conv2_1',
            'vgg_19/conv2/conv2_2',
            'vgg_19/pool2',
            'vgg_19/conv3/conv3_1',
            'vgg_19/conv3/conv3_2',
            'vgg_19/conv3/conv3_3',
            'vgg_19/conv3/conv3_4',
            'vgg_19/pool3',
            'vgg_19/conv4/conv4_1',
            'vgg_19/conv4/conv4_2',
            'vgg_19/conv4/conv4_3',
            'vgg_19/conv4/conv4_4',
            'vgg_19/pool4',
            'vgg_19/conv5/conv5_1',
            'vgg_19/conv5/conv5_2',
            'vgg_19/conv5/conv5_3',
            'vgg_19/conv5/conv5_4',
            'vgg_19/pool5',
            'vgg_19/fc6',
            'vgg_19/fc7',
        ]
```