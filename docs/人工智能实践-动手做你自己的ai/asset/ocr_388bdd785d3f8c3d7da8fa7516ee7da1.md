+   - numpy：用于处理数值计算。
+   - load_img, img_to_array, preprocess_input, imsave：用于图像处理和保存。
+   - VGG16：预训练的用于图像识别的卷积神经网络模型。
+   - fmin_l_bfgs_b：scipy提供L-BFGS算法接口。

```
import numpy as np
from keras.preprocessing.image import load_img, img_to_array
from keras import backend as K
from keras.applications.vgg16 import preprocess_input, VGG16
from scipy.optimize import fmin_l_bfgs_b
from scipy.misc import imsave
import time

# 设置内容图像和风格图像的路径
content_image_path = 'content.jpg'
style_image_path = 'style.jpg'

# 设置固定的图像大小
w, 1 = load_img(content_image_path).size
length = 512
width = int(w * length / 1)
```

# （一） 预处理

加载图像，并将图像转化为张量。

```
# 预处理图像
def preprocess_image(image_path):
    image = load_img(path=image_path, target_size=(length, width))
    image = img_to_array(image)
    image = np.expand_dims(image, axis=0)
    image = preprocess_input(image)
    return image
```

将内容图像、风格图像和生成图像转化为张量并拼接在一起。

```
# 设置内容图像和风格图像的路径
content_image_path = 'content.jpg'
style_image_path = 'style.jpg'
```