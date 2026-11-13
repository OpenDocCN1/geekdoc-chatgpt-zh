像素值为 0 的黑点，“盐”是指像素值为 255 的白点。椒盐噪声就是在图像中随机出现的黑色和白色的像素，其产生的原因可能是图像传感器突然受到强烈的干扰，使传感器中模数转换器发生错误。添加椒盐噪声的算法比较简单，只需在图像中随机加入一些全黑或全白的像素即可。下面，我们编程实现椒盐噪声。

```
# 在第 2 章中编写了 utils 函数包文件，本书编写的函数都会封装到 utils 函数包中
from utils import *

# 添加椒盐噪声
def add_Salt(img, pro):
    # img 为输入图像
    # pro 为椒盐噪声的比例

    # 添加黑色像素
    noise = np.random.uniform(0, 255, img[:, :, 0].shape)
    # mask 为添加噪声的掩模
    mask = noise < pro * 255
    # 扩展 mask 的维度
    mask = np.expand_dims(mask, axis=2)
    mask = np.repeat(mask, 3, axis=2)
    img = img * (1 - mask)

    # 添加白色像素
    mask = noise > 255 - pro * 255
    mask = np.expand_dims(mask, axis=2)
    mask = np.repeat(mask, 3, axis=2)
    noise_img = 255 * mask + img * (1 - mask)

return noise_img

img = cv2.imread('lenaface.jpg')
# 添加椒盐噪声的图像
img_salt = add_Salt(img, 0.05)
plt.imshow(img_salt[:,:,::-1])
plt.axis('off')
plt.show()
```

![](bbox=[0.35, 0.72, 0.65, 0.92])