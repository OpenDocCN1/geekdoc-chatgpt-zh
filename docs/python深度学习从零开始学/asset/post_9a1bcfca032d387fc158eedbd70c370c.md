
## Python 深度学习从零开始学

```
my_face = face_emotion()
my_face.learning_face()
#####################################################################
```

程序运行结果如图 11-7 所示。

![](img/470b883072d48f1f2917a784b39d4327_194_0.png)

图 11-7

## 11.4 我能认识你——人脸识别实战

本节的实战将使用 Dlib 自带的面部识别模块 face_recognition。想要顺利调用 face_recognition 这个库，首先需要安装好两个依赖库 Dlib 和 OpenCV。

face_recognition 是 GitHub 上主流的人脸识别工具包之一，该软件包使用 Dlib 库中最先进的人脸识别深度学习算法，在 LFW 数据集中有 99.38% 的准确率。

face_recognition 实现人脸识别的思路：

- （1）给定想要识别的人脸的图片并对其进行编码（每个人只需要一幅），并将这些不同的人脸编码构建成一个列表。编码其实就是将人脸图片映射成一个 128 维的特征向量。
- （2）OpenCV 读取视频并循环每一帧图片，将每一帧图片编码后的 128 维特征向量与前面输入的人脸库编码列表里的每个向量内积来衡量相似度，根据阈值来计算是否同一个人。
- （3）对识别出来的人脸打标签。

人脸识别实现的思路是采用 HOG 方法检测输入图像中的人脸。虽然使用 CNN 或 HOG 方法在量化面部之前（对面部编码）都可以检测输入图像中的人脸，但 CNN 方法更准确（但更慢），而 HOG 方法更快（但不太准确）。虽然 CNN 人脸检测更准确，但在没有使用 GPU 运行的情况下实时检测速度太慢。

人脸识别实际上是对人脸进行编码后再去计算两张人脸的相似度，每张人脸是一个128维的特征向量，最后利用两个向量的内积来衡量相似度。

本示例程序代码（包含详细注释）如下：

```
##################################################
import os
import face_recognition
path = "d:\\facelib"
# 已知人脸照片的文件目录
files=os.listdir(path)
# 从目录读取所有文件到files中
known_names=[] # 已知人名
known_faces=[] # 已知人脸
for file in files:
    filename=str(file)                # 从files中循环读取每个文件名
                                      # 得到当前文件的名字
    known_names.append(filename)      # 当前文件名加入到已知人名清单
    image=face_recognition.load_image_file(path+"\\"+filename)
    encoding=face_recognition.face_encodings(image)[0]
    # 对当前人脸图像进行识别，识别的特征存在 encoding 中
    known_faces.append(encoding)
    # 把识别到的当前人脸特征保存在已知人脸中
unknown_image=face_recognition.load_image_file("d:\\testface\\testface1.jpg")
# 调入一幅不知人名的 人脸照片 testface1.jpg
unknown_face=face_recognition.face_encodings(unknown_image)[0]
# 识别这幅人脸的特征
results=face_recognition.compare_faces(known_faces,unknown_face,tolerance=0.36)
    # 通过未知人脸和已知人脸进行比较
print("识别结果如下：")
for i in range(len(known_names)):   # 显示未知照片人脸与每一幅已知人脸的比较结果
    print(known_names[i]+":",end="")
    if results[i]:
        print("相同") # 识别结果是 true，就显示相同
    else:
        print("不同")
##################################################
```

上面这个程序代码的逻辑是：首先将已知人脸特征存入变量中，读入未知人脸，识别未知人脸特征，判断未知人脸特征与已知人脸特征是否相符，相符返回 true 值，不相符返回 false 值，输出结果为“相同”或“不同”。注意，需要提前把人脸图片存入文件夹 D 盘的 facelib 文件夹里（这个文件夹路径可以根据你的实际路径修改）。face_recognition.compare_faces() 函数的默认阈值为 0.6，阈值太低容易造成无法成功识别人脸，阈值太高容易造成人脸识别混淆，我们选择的阈值是 0.36，这里需要注意阈值的选取。

调用摄像头实时识别人脸的代码（包含详细注释）如下：

```
#####################
# -*- coding: UTF-8 -*-
import face_recognition
import cv2
import os
import numpy as np
from PIL import Image, ImageDraw, ImageFont
# 在计算机摄像头上实时运行人脸识别
video_capture = cv2.VideoCapture(0)
# 加载示例图片并学习如何识别它
path ="d:\\facelib\\" # 在同级目录下的 images 文件中存放需要被识别出的人物图
total_image=[]
total_image_name=[]
total_face_encoding=[]
for fn in os.listdir(path):                     # fn 表示的是文件名
    total_face_encoding.append(face_recognition.face_encodings
        (face_recognition.load_image_file(path+fn))[0])
    fn=fn[: (len(fn)-4)]    # 截取图片名 (这里应该把 images 文件中的图片名命名为人物名)
    total_image_name.append(fn)                     # 图片名字列表
while True:
    # 抓取一帧视频
    ret, frame = video_capture.read()               # 捕获一帧图片
    small_frame = cv2.resize(frame, (0, 0), fx=0.25, fy=0.25)
    # 将图片缩小 1/4, 为人脸识别提速
    rgb_small_frame = small_frame[:, :, ::-1]  # 将 opencv 的 BGR 格式转为 RGB 格式
    # 发现视频帧中的所有的脸和 face_encodings
    #face_locations = face_recognition.face_locations(frame)
    #face_encodings = face_recognition.face_encodings(frame, face_locations)
    #face_locations = face_recognition.face_locations(rgb_small_frame)
    face_locations = face_recognition.face_locations(rgb_small_frame)
    face_encodings = face_recognition.face_encodings(rgb_small_frame, face_locations)
    # 在这个视频帧中循环遍历每个人脸
    for (top, right, bottom, left), face_encoding in zip(face_locations,
face_encodings):
        top *= 4
        # 还原人脸的原始尺寸
        right *= 4
        bottom *= 4
        left *= 4
        # 看看面部是否与已知人脸相匹配
        for i,v in enumerate(total_face_encoding):
            match = face_recognition.compare_faces([v], face_encoding,
tolerance=0.42)
            name = "Unknown"
            if match[0]:
                name = total_image_name[i]
                break
# 画出一个框, 框住脸
cv2.rectangle(frame, (left, top), (right, bottom), (0, 0, 255), 2)
# 画出一个带名字的标签, 放在框下
img_PIL=Image.fromarray(cv2.cvtColor(frame,cv2.COLOR_BGR2RGB))
# 转换图片格式
position = (left + 6, bottom - 6)                     # 指定文字输出位置
draw = ImageDraw.Draw(img_PIL)
font1 = ImageFont.truetype('simhei.ttf', 20)
draw.text((20,20),'按Q键退出',font=font1,fill=(255,255,255))
font2 = ImageFont.truetype('simhei.ttf', 40)          # 加载字体
draw.text(position, name, font=font2, fill=(255, 255, 255))  # 绘制文字
frame = cv2.cvtColor(np.asarray(img_PIL),cv2.COLOR_RGB2BGR)
# 将图片转回 OpenCV 格式
# 显示结果图像
cv2.imshow('Video', frame)
# 按 q 键退出
if cv2.waitKey(1) & 0xFF == ord('q'):
    break
# 释放摄像头中的流
video_capture.release()
cv2.destroyAllWindows()
#####################################################
```

以上就是人脸识别的入门知识，Dlib 库已经替我们做好了绝大部分的工作，我们只需要去调用就行了。Dlib 库里面有人脸检测器，有训练好的人脸关键点检测器，也有训练好的人脸识别模型。face_recognition 是最简单、最容易上手的人脸识别工具和 Python 库，是国外开源的项目，但 face_recognition 对于小孩和亚洲人的人脸识别准确率有待提升，我们可以把容错率调低一些，使识别结果更加严格。

## 第 12 章 图像风格迁移

风格迁移（Style Transfer）最近几年非常火，是深度学习领域很有创意的研究成果之一。所谓图像风格迁移（Neural Style），是指利用算法学习著名画作的风格，然后再把这种风格应用到另外一幅图片上的技术。著名的图像处理应用 Prisma 就是利用了风格迁移技术，使普通用户的照片自动变换为具有艺术家风格的图片。

### 12.1 图像风格迁移简介

我们将图像风格迁移定义为改变图像风格同时保留它的内容的过程，即给定一幅输入图像和样式图像，我们就可以得到既有保留图像中原始内容信息，又有新样式的输出图像。

所谓图像风格迁移，是指将一幅内容图 A 的内容和一幅风格图 B 的风格融合在一起，从而生成一幅具有 A 图内容和 B 图风格的图片 C 的技术。

作为非艺术专业的人，我们就不纠结艺术风格是什么了，如图 12-1 所示。每个人都有每个人的见解，有些东西大概在艺术界也没有明确的定义。如何把一种图像风格变成另一种风格更是难以定义的问题。

对于程序员，特别是对于机器学习方面的程序员来说，这种模糊的定义简直就是噩梦。到底怎么把一个说都说不清的东西变成可执行的程序，这是困扰很多图像风格迁移方面的研究者的难题。

所谓的艺术风格是一种抽象的难以定义的概念，因此，如何将一种图像风格转换成为另一种图像风格更是一个复杂抽象的问题。尤其是对于机器程序而言，解决一个定义模糊不清的问题几乎不可行。

图像风格迁移这个领域，在 2015 年之前连个合适的名字都没有，因为每种风格的算法都是各管各的，互相之间并没有太多的共同之处。比如油画风格迁移，又比如头像风格迁移，没一个重样的。可以看出这时的图像风格处理的研究基本都是各自为战，通常采用的思路是：分析一种风格的图像，为这种风格建立一个数学统计模型，改变要做迁移的图像，使它的风格符合建立的模型。该种方法可以取得不同的效果，但是有一个较大的缺陷：一个模型只能足够实现一种图像风格的迁移。因此，基于传统方法的风格迁移的模型应用十分有限，揭鼓出来的算法也没引起业界的注意。

![](img/470b883072d48f1f2917a784b39d4327_199_0.png)

图 12-1

在实践过程中，人们又发现图像的纹理可以在一定程度上代表图像的风格。这又引入了和风格迁移相关的另一个领域——纹理生成。这个时期，该领域虽然已经有了一些成果，但是通用性也比较差。早期纹理生成的主要思想是：纹理可以用图像局部特征的统计模型来描述。

如图 12-2 所示，这个图片可以称为栗子的纹理，这个纹理有个特征，就是所有的栗子都有个开口，用简单的数学模型表示开口的话，就是两条某个弧度的弧线相交，统计学上来说就是这种纹理有两条这个弧度的弧线相交的概率比较大，这种特征可以称为统计特征。有了这个前提或者思想之后，研究者成功地使用复杂的数学模型和公式来归纳生成了一些纹理，但毕竟手工建模耗时耗力，当时计算机的计算能力还不太强，这方面的研究进展缓慢。

![](img/470b883072d48f1f2917a784b39d4327_199_1.png)

图 12-2

同一时期，计算机领域进展最大的研究之一可以说是计算机图形学了。游戏机从刚诞生开始就伴随着显卡，显卡最大的功能是处理和显示图像。不同于 CPU 的是，CPU 早期是单线程的，也就是一次只能处理一个任务，GPU 可以一次同时处理很多任务，虽然单个任务的处理能力和速度比 CPU 差很多。比如一个 128×128 的超级马里奥游戏，用 CPU 处理的话，每一帧都需要运行 128×128=16384 步，而 GPU 因为可以同时计算所有像素点，时间上只需要 1 步，速度比 CPU 快很多。显卡计算能力的爆炸式增长，直接导致了神经网络的复活和深度学习的崛起，因为神经网络和游戏图形计算的相似之处是两者都需要对大量数据进行重复单一的计算。

随着神经网络的发展，在某些视觉感知的关键领域，比如物体和人脸识别等，基于深度神经网络的机器学习模型——卷积神经网络有着接近于人类甚至超越人类的表现。人们发现，以图像识别为目的而训练出来的卷积神经网络也可以用于图像风格迁移。

## Python 深度学习从零开始学

当时，卷积神经网络最出名的一个物体识别网络之一叫作 VGG19，结构如图 12-3 所示。

![](img/470b883072d48f1f2917a784b39d4327_200_0.png)

图 12-3

每一层神经网络都会利用上一层的输出来进一步提取更加复杂的特征，直到复杂到能被用来识别物体为止，所以每一层都可以看作很多个局部特征的提取器。VGG19 在物体识别方面的精度甩了之前的算法一大截。VGG19 具体内部在做什么其实很难理解，因为每一个神经元内部参数只是一堆数字而已。每个神经元有几百个输入和几百个输出，一个一个去梳理清楚神经元和神经元之间的关系太难了。于是，有人想出来一种办法：虽然我们不知道神经元是怎么工作的，但是如果我们知道了它的激活条件，会不会对理解神经网络更有帮助呢？于是他们编了一个程序（这个程序用的算法就是 Back Propagation 算法即反向传播，和训练神经网络的方法一样，只是倒过来生成图片），把每个神经元所对应的能激活它的图片找了出来，特征图就是这么生成的。特征图蕴含着提取出图像的信息，当卷积神经网络用于物体识别时，随着网络的层次越来越深，网络层产生的物体特征信息越来越清晰。这意味着，沿着网络的层级结构，每一个网络层的输出越来越关注于输入图片的实际内容，而不是其它具体的像素值。利用卷积神经网络提取图像内容和风格，通过对特征图进行适当处理，将提取出来的内容表示和风格表示分别用于重建图像的内容和风格。

2015 年，德国图宾根大学（University of Tuebingen）的 Leon A. Gatys 撰写了两篇基于神经网络图像风格迁移的论文：在第一篇论文中，Gatys 从各层 CNN 中提取纹理信息，于是就有一个不用手工建模就能生成纹理的方法；在第二篇论文中，Gatys 更进一步指出，纹理能够描述一个图像的风格。第一篇论文比之前的纹理生成算法的创新点只有一个，它提出了一种用深度学习来给纹理建模的方法。之前说到纹理生成的一个重要的假设是纹理能够通过局部统计模型来描述，而手动建模方法太麻烦。于是 Gatys 看了物体识别论文，发现大名鼎鼎的 VGG19 卷积神经网络模型，其实就是一堆局部特征识别器。他把事先训练好的网络拿过来，发现这些识别器还挺好用。因此，Gatys 使用格拉姆矩阵（Gram Matrix）演算了一下那些不同局部特征的相关性，把它变成了一个统计模型，于是就有了 一个不用手工建模就能生成纹理的方法。
