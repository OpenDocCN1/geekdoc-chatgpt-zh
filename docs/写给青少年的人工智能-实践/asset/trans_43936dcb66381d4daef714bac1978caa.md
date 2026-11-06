

```
1    import numpy as np
2    import os
3    import cv2
4
5    # 人脸数据路径
6    path = 'Facedata'
7
8    # 数据准备函数，在这里读取人脸数据并添加序号标签
9    def prepareData(path):
10       ID_Path = os.listdir(path)
11       faceSamples = []          # 用来存储人脸数据
12       ids = []                  # 用来存储每张人脸数据的ID序号标签
13
14       # 读取所有ID的人脸数据
15       for ID in ID_Path:
16           img_Paths = os.listdir(path + '/' + ID)
17
18           # 遍历每个ID的全部人脸数据
19           for img_name in img_Paths:
20
21               # 读取图像数据，并将其转换为灰度图像
22               img_path = path + '/' + ID + '/' + img_name
23               img = cv2.imread(img_path)
24               gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
25               # 将数据和相应序号标签加入列表
26               faceSamples.Append(gray)
27               ids.Append(int(ID))
28
       return faceSamples, ids
30
   print('数据准备中，请耐心等待....')
32   faces, ids = prepareData(path)          # 准备数据
33
34   # 调用人脸识别器
35   recognizer = cv2.face.LBPHFaceRecognizer_create()
36
37   print('训练中，请耐心等待....')
38   recognizer.train(faces, np.array(ids))  # 训练模型
39
40   # 在程序文件夹下建立"face_trainer"文件夹，存放训练好的模型
41   if not os.path.exists("face_trainer"):
42       os.mkdir("face_trainer")
43   recognizer.write(r'./face_trainer/trainer.yml')
44   print("训练完成")
```

## 人脸识别

得到训练模型后，我们就可以进行人脸识别了。

加载刚才训练好的人脸识别模型，打开摄像头进行拍摄，将拍摄的人脸输入预测函数，就可以得到结果。预测函数会输出拍摄到的人脸最可能的序号（对应录入人脸数据时的序号），并大致给出可信度。程序4.5就是人脸识别的代码。

程序4.5的代码可用于对合影照片进行人脸识别：标注出合影中每一张检测出来的脸，并在人脸上方用红色字标记好这是我们之前采集数据时的几号，在下面用蓝色字注明大概的可信度。你需要把合影照片与程序文件保存在同一个文件夹中，并修改第15行中的图片文件名（`1.png`）。

你可以修改标注文字的颜色和大小。在第38行和第41行中，`cv2.putText`的最后3个参数分别是文字大小、` (R,G,B) `格式文字颜色、文字粗细。例如第38行的`2, (255, 0, 0), 5`表示文字大小为2，颜色为`(255, 0, 0)`也就是红色，粗细为5。

### 人脸识别合影

```
1    import cv2
2
3    print("载入模型")
4    # 调用人脸识别器
5    recognizer = cv2.face.LBPHFaceRecognizer_create()
6    # 加载训练好的人脸识别模型
7    recognizer.read('./face_trainer/trainer.yml')
8    # 加载人脸检测模型
9    faceCascade = cv2.CascadeClassifier(cv2.data.haarcascades
10                           + "haarcascade_frontalface_default.xml")
11
12   font = cv2.FONT_HERSHEY_SIMPLEX  # 设置字体
13
14   print("读取图片")
15   frame = cv2.imread('1.png')
16
17   print("开始识别")
18   # 转为灰度图片
19   gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
20
21   # 检测人脸
22   faces = faceCascade.detectMultiScale(gray, 1.2, 5,
23                                        minSize=(50, 50))
24   # 定位人脸
25   for (x, y, w, h) in faces:
26        cv2.rectangle(frame, (x, y), (x+w, y+w), (255, 0, 0))
27
28        # 识别人脸
29        ID, confidence = recognizer.predict(gray[y:y+h, x:x+w])
30
31        # 判断准确度
32        if confidence > 60:
33            ID = "Unknown"
34        else:
35            confidence = str(100 - confidence**2//100)+'%'
36        # 在图像上显示识别编号
37        cv2.putText(frame, str(ID), (x+5, y-5),
38                    font, 2, (255, 0, 0), 5)
39        # 在图像上显示大概可信度
40        cv2.putText(frame, str(confidence), (x+5, y+h-5),
41                    font, 2, (0, 0, 255), 5)
42   # 显示图像
43   cv2.imshow('camera', frame )
44   cv2.waitKey()
45
46   cv2.destroyAllWindows()
```

至此，我们一起完成了第一个人工智能编程项目。是不是没有想象中那么难？除了用来测试的程序4.1，这个人脸识别项目的完成代码由程序4.2、程序4.3和程序4.4的文件组成。我们只需要把它们放在同一个文件夹，依次执行即可。

不过，这只是最简单的人脸识别程序，它的准确度比较有限，功能也非常单一。相信随着未来的学习和探索，你一定能自己完成更加完善的人脸识别程序。

## Python写诗

### 夏日观雨

雷景濛烟霭，岚氛雾雨氲。晴暮风吹霁，漠霞霏融凝。

你知道上面这首诗是谁写的吗？它描绘了夏天小雨伴着雷声淅淅沥沥，终于在傍晚雨过天晴的景象。如果不是出现在这本书里，任你绞尽脑汁恐怕也猜不到它的作者是一段人工智能程序吧！

如果你看过著名科幻作家刘慈欣的小说《诗云》，一定会为故事中的想法所震撼——用整个太阳系建造超级计算机，写出天下所有的诗。

故事中的主角并没有成功，不过随着人工智能中自然语言处理技术的发展，这个想法似乎在渐渐变成现实。诗歌人工智能“九歌”“诗三百”和“小冰”都已经创作出很多作品，其中有一些确实很不错。

你想不想亲手创造出一个能写诗的人工智能呢？下面我们就一起来完成能写诗的简单人工智能。上面那首诗，就是用我们即将编写的人工智能程序创作出来的。

### 词向量算法和gensim库

如果要将自然语言交给机器学习中的算法来处理，一般来说首先需要将语言变成数学。词向量就是用来将语言中的词进行数学化的一种方式。

在我们说话、写文章的时候，每个词都不是独立出现的，而是互相之间有一定的联系。每一种文体、文风，使用的词语也会有一定的规律和特点。词向量算法就可以发现这种关系、规律，并用数学表示出来。

简单来说，在分析了大量文章、句子等语言资料之后，词向量算法可以计算出每一个词语在这种文体词语库中的位置。位置越近的词语，关系就越密切，越有可能一起出现。

通过这种数学方法，我们可以分析诗中词语间的关系，计算出与某个词语最搭配、最可能一起出现的另一个词语，进而组成诗句，最终写出一首诗。

为了使用词向量算法，我们需要一个新的第三方库`gensim`。`gensim`是一个在自然语言处理方面非常著名的开源库，简单易用，有多种功能。

通过`pip`命令或Thonny的包管理安装`gensim`库：

```
pip install gensim
```

但上面的方法在Windows系统下可能无法顺利完成，那么你需要采用下面的方法手动安装第三方库。

在安装之前，首先要确认你使用的Python版本。你可以用Python运行下面的代码，来查看Python版本。

```
1    import sys
2    print(sys.print(sys.version)
3    print(sys.version_info)
```

执行结果大致如下：

```
3.7.9 (tags/v3.7.9:13c94747c7, Aug 17 2020, 18:01:55) [MSC v.1900 32 bit (Intel)]
('32bit', 'WindowsPE')
```

其中，`3.7.9`就是Python的版本号，`32 bit`表示32位。你可以根据自己计算机上显示的结果来进行判断。

在Thonny的界面中，你还可以依次选择“帮助”→“关于Thonny”来查看，如图4-2所示。

![](Images/4-2.png)

图4-2　“关于Thonny”菜单

例如，下面的内容说明Python版本是3.7.9（32位），如图4-3所示。

![](Images/4-3.jpg)

图4-3　查看Python版本

记住上面查到的这两个数字，然后查找Python第三方库`gensim`的网站，登录并下载与版本号对应的whl文件。文件名中cp后的两个数字是版本号的前两个数字，比如版本号是3.7.9，就应该找`cp37`。`win`后的数字代表多少位，图4-3中Python是32位，那么文件名中就是`win32`。如果是64位，那么文件名中应该是`win_amd64`，如图4-4所示。

![](Images/4-4.jpg)

图4-4　通过文件名找到对应版本

所以，对于版本为3.7.9（32位）的Python，应该选择`gensim-4.0.1-cp37-cp37m-win32.whl`进行下载。注意，下载时，请勿修改文件名。

下载完成后，打开存放whl的文件夹，记录路径。你可以把它放在一个易于查找的地方，比如D盘根目录，这样文件的完整路径就是`d:/gensim-4.0.1-cp37-cp37m-win32.whl`。

接下来，我们使用`pip`命令进行安装。不过这次不是从网络安装，而是安装刚才下载的whl文件，所以要把命令中的Python库名字替换成whl文件的完整路径。例如：

```
pip install d:/gensim-4.0.1-cp37-cp37m-win32.whl
```

一旦出现Successfully的提示，就说明已经安装成功了。如果安装不成功，一般是因为所下载的whl文件和安装的Python版本不匹配。

在Thonny中，你需要打开包管理，选择“从本地文件中安装”，如图4-5所示，然后找到刚才下载的whl文件。

![](Images/4-5.png)

图4-5　从包管理中选择“从本地文件中安装”

运行程序时，可能会弹出像下面这样的警告文字：

/Users/hetao/Library/Python/3.7/lib/Python/site-packages/gensim/
similarities/__init__.py:15: UserWarning: The gensim.similarities.
levenshtein submodule is disabled, because the optional Levenshtein
package <*****//pypi****/project/Python-Levenshtein/> is unavailable.
Install Levenhstein (e.g. 'pip install Python-Levenshtein') to suppress
this warning.
  warnings.warn(msg)

你可以忽略上述文字，但更好的方式是再安装`Python-Levenshtein`库：

```
pip install Python-Levenshtein
```

在Windows系统下，你同样需要用类似安装`gensim`库的方式手动安装。

### 获取语料

自然语言处理领域少不了用到大量的语料数据。语料也就是语言资料，无论是你和同学说的一句话，还是课本上的一篇文章、李白的一首诗，都可以成为语料。对于写诗人工智能来说，所需要的语料就是诗。

你可以通过爬虫技术从各种古诗网站爬取相关内容。爬取到的数据需要去掉作者、注释等和诗句本身无关的内容。

当然，你也可以直接从本书的配套电子资源网站下载已经整理好的古诗语料数据“`古诗词.txt`”。

### 训练模型

导入`gensim`库中的词向量算法`Word2Vec`后，设置好语料数据文件的路径和保存模型的路径。

接下来要做的是读取语料文件，并用列表形式储存每一句诗。由于古汉语、文言文用词精炼灵活，和现代汉语有不小的区别，因此对古诗进行分词和词性分析比较困难。为了方便起见，我们直接按照单字对古诗进行分词，并忽略词性分析。

读取全部语料后，你就可以将数据投入诗词词向量算法训练模型了。完整的程序如程序4.6所示。

程序4.6　诗词词向量算法训练模型

```
1    # 导入gensim的词向量算法
2    from gensim.models import Word2Vec
3
4    poem_path = '古诗词.txt'
5    model_path = 'word2vec'
6
7    poem_word=[]
8    print("读入语料数据")
9    with open(poem_path, encoding='utf-8') as f:
10       for line in f:
11           poem_word.Append(list(line.strip()))
12
13   # 模型训练和保存
14   print("开始训练模型，请等待")
15   model = Word2Vec(poem_word, window=12)
16   print("训练完成，保存模型")
17   model.save(model_path)
18   print("训练结束")
```

读取语料数据文件时（第9～11行），我们使用了新的Python语法：

```
9    with open(poem_path, encoding='utf-8') as f:
10        for line in f:
11            poem_word.Append(list(line.strip()))
```

`open()`函数可以用于打开特定的文件。在默认情况下，它以只读模式打开文件，但是你也可以通过参数`mode = "w"`来把打开方式改变为“用于写入”。表4-1所示的是处理文本时常用的`mode`参数。

表4-1　处理文本时常用的`mode`参数

| 参数 || 作用 |
| --- | --- |
| `mode = "r"` || 以只读方式打开文件。这是默认模式，文件指针（相当于你在打字时的光标）将会放在文件的开头 |
| `mode = "r+"` || 打开一个文件用于读写。文件指针将会放在文件的开头 |
| `mode = "w"` || 打开一个文件只用于写入。如果该文件已存在，则打开文件，并从开头开始编辑，即原有内容会被删除；如果该文件不存在，创建新文件 |
| `mode = "w+"` || 打开一个文件用于读写。如果该文件已存在，则打开文件，并从开头开始编辑，即原有内容会被删除；如果该文件不存在，则创建新文件 |
| `mode = "a"` || 打开一个文件用于追加。如果该文件已存在，文件指针将会放在文件的结尾，即新的内容会被写入已有内容之后。如果该文件不存在，创建新文件进行写入 |
| `mode = "a+"` || 打开一个文件用于读写。如果该文件已存在，文件指针将会放在文件的结尾。文件打开时会是追加模式。如果该文件不存在，创建新文件用于读写 |

另外，通过`with ... as f`，我们将打开的文件在程序中表示为变量`f`。`for`循环会一行一行地从文件中读取内容，并将读取到的字符串转换为列表。`line.strip()`可以去掉字符串前后的空格和换行。

程序中具体用于训练模型的一行代码是第15行，即

```
15    model = Word2Vec(poem_word, window=12)
```

在这里，我们用到了词向量算法的两个参数：第一个参数就是语料数据；第二个参数称为滑动窗口大小。对于某个词语，与它关系最密切的显然是同一个句子或相邻句子中的词，也就是上下文。隔着好几行甚至另一篇文章中的词，可能就没什么瓜葛了。也就是说，在通过语料计算模型时，比较近的词之间的联系会更加密切。

