
## 训练集建立
在建立训练集时，首先使用人脸校准算法自动检测位于眉毛、眼睛、鼻子、嘴唇和下巴附近的面部基准点;然后通过二维仿射变换，基于内眼点和下嘴唇位置对人脸图像进行剪裁和校准，并缩放为64像素×64像素大小;接着对像素值和面部基准点的位置进行规范化，使其值位于`[-1,1]`范围内；最后，建立三元组![img](Images/txt006_99.jpg)作为训练样本，使用所有用户身份的所有表情对，并从随机分布中采样作为参考的形状张量。

## 2. 情感生成结果
在`Multi-PIE`和`CK+`数据库上进行表情生成实验。从`Multi-PIE`数据库中选择由全部377人的3种头部姿态(正面和±15°姿态)、20种光照条件和6种表情(中性、微笑、惊讶、眯眼、厌恶和尖叫)组成的150～244张人脸图像作为实验数据。其中，输入图像和目标图像具有相同姿态和光照条件。从`CK+`数据库中，选择由118人的7种表情(愤怒、蔑视、厌恶、恐惧、高兴、悲伤和惊讶)组成327个图像序列。每个序列起始于中性表情，终止于峰值表情。根据一种被广泛接受的数据协议\[18\]，将`Multi-PIE`和`CK+`中选择的实验数据按照用户编号分为10个子集，其中9个子集用于训练，1个子集用于测试。

### 1) 表情图像生成
静态表情图像的生成结果如图6.13所示，从上到下的各行分别对应输入图像、生成图像和目标图像。从生成图像中可以看出，用户身份的一致性可以被很好地保持，并且能够获得与目标图像保持一致的面部表情，表明了形状引导的有效性。
![img](Images/txt006_100.jpg)
图6.13　静态表情图像的生成结果

### 2) 表情序列生成
为了得到平滑过渡的面部表情序列，对`Multi-PIE`中的`RGB`图像进行连续图像生成。给定一张输入人脸图像、使用形状张量表示的相同用户不同表情![img](Images/txt006_101.jpg)和![img](Images/txt006_102.jpg)以及生成序列的长度`N`，按照下式进行线性插值，产生`N`个中间形状张量。
![img](Images/txt006_103.jpg)
将它们连同一组相同的输入图像一起输入生成模型，由形状张量引导关键帧的生成，从而产生一组面部表情序列。图6.14为在`Multi-PIE`数据集上生成平滑过渡的表情图像序列。其中，图6.14(a)为起始表情形状，图6.14(b)为生成的表情图像序列，图6.14(c)为目标表情图像。图中，每行均展示了从一种表情到另一种表情的过渡，例如第一行为从微笑表情到尖叫表情的过渡。图6.14表明，通过在两种表情的形状张量之间进行线性插值，可以为不同用户生成表情平滑过渡的图像序列。
![img](Images/txt006_104.jpg)
图6.14　在`Multi-PIE`数据集上生成平滑过渡的表情图像序列

### 3) 表情生成的细节控制
对于某个确定的用户，使用一些关键帧来引导其面部表情过渡。这些关键帧对应的人脸形状，既可以从某种表情的一些图像中自动获取，也可以通过对已有形状进行手工调节来获得。例如，提高或降低眼睑位置对应的面部关键点，或者通过移动嘴角附近的像素来较为精细地操纵人脸形状的变化。面部表情序列生成过程中的细节控制如图6.15所示。其中，第一行展示了关键帧`t`、`t+t1`、`t+t2`、`t+t3`及其之间的插值。这些关键帧使用人脸形状表示目标表情，其中(`t+t2`)帧的形状受手工调节。第二行是对应的生成图像，其中一些图像的眼部和嘴部细节被放大，展示在它们的下方。从图中可以看出，通过形状引导，表情生成模型不仅能够生成完整的表情图像和序列，还能够对局部细节进行较为精细的控制。随着引导形状的细微改变，通过嵌入形状信息隐变量`z`，生成图像的细节也随之改变。此外，在表情变化的过程中，瞳孔、牙齿、舌头等隐藏部分也能够被生成，增加了生成序列的逼真程度。
![img](Images/txt006_105.jpg)
图6.15　面部表情序列生成过程中的细节控制

## 6.5 实例
本节以一个具体的情感交互界面作为实例，对它的界面元素和技术原理进行详细解析。通过该界面，用户可以直接与计算机进行情感交互，即计算机从实时捕获的用户面部表情视频中识别用户情感后，将推断结果以直观的方式呈现给用户；用户在收到反馈信息之后，调整自身表情，继续下一轮交互操作。
情感交互界面实例如图6.16所示。它由以下四部分组成。
(1)视觉采集信息反馈区：将摄像头捕获的视频数据逐帧回显在用户界面上，使得用户可以像照镜子一样看到自己的面部表情，便于进行表达调整。
(2)人脸校准结果反馈区：对输入的视频帧进行在线人脸校准和面部关键点跟踪后，逐帧地将人脸和面部关键点位置显示在原始视频帧上，便于用户观察计算系统对人脸的感知结果。
(3)情感识别结果反馈区：在计算系统识别出用户的情感类型之后，将识别结果以含有情感隐喻的表情图标、情感标签和识别概率相结合的方式输出，便于用户观察计算系统对面部表情的感知结果，识别概率越高的情感类型所对应的界面视觉效果越高亮，如图6.16中的惊讶表情。
(4)一般操作区：提供用户控制应用程序的一些基本操作。
![img](Images/txt006_106.jpg)
图6.16　情感交互界面实例
上述界面的计算框架由五个主要模块组成，如图6.17所示。下面分别对它们的主要功能、输入/输出信息和关键实现方法进行介绍。
![img](Images/txt006_107.jpg)
图6.17　情感交互界面的计算框架

### 1) 数据获取模块
该模块的主要功能是驱动摄像头捕捉用户的面部表情视频后，将捕获的视频帧回显到用户界面，同时将该帧的一个副本输入情感感知与识别过程的预处理模块。
![img](Images/txt006_108.jpg)

### 2) 预处理模块
该模块将彩色人脸图像转换为灰度图像，使用`ERT`算法进行人脸校准，定位42个人脸内部的面部关键点，按照内眼角和下嘴唇位置对齐；将人脸区域缩放到256像素×256像素，使用直方图均衡化进行数据正规化后，将其输入特征提取模块。
![img](Images/txt006_109.jpg)

### 3) 特征提取模块
该模块的主要功能是对输入的正规化人脸图像进行特征提取。为了利用动态视觉特征，该模块首先将由当前帧及其前9帧组成的子序列融合在一起。
![img](Images/txt006_110.jpg)
然后对融合图像中面部关键点邻域范围内的像素提取128维`SIFT`特征，将它们连接起来，形成一个42×128维的表情特征向量，经过`PCA`降维之后，输入分类识别模块。
![img](Images/txt006_111.jpg)![img](Images/txt006_112.jpg)

### 4) 分类识别模块
该模块通过支持向量机对输入的表情视觉特征进行模式分类，识别情感类型。由于需要识别`NUMS_OF_EMOTION = 6`种情感，故使用15个`1v1`支持向量机进行综合投票，将各类得票数量进行归一化，得到各分类概率，输出到反馈模块。
![img](Images/txt006_113.jpg)

### 5) 反馈模块
该模块可以根据推断的用户情感状态和基于情感的决策结果来呈现各种各样的反馈内容。本节直接将情感识别结果以柱形图和情感图标的直观形式反馈给用户。其中，柱形图反映出了识别为各类情感的可能性，柱形的高度与可能性大小成正比。对于具有最大可能性的情感类型，我们将其图标高亮显示，表明它是推断出的与真实情感最接近的情感类型。
表情识别结果以柱形图的形式反馈给用户，柱形图上的数值表示识别概率，数值越大柱形越高，表示该类表情的可能性越大。

## 参考文献
\[1\]PICARD R W. Affective computing\[M\]. MIT Press, 1997.
\[2\]胡包钢, 谭铁牛, 王珏. 情感计算——计算机科技发展的新课题\[J\]. 科学时报, 2000, 3.
\[3\]EKMAN P. Facial action coding system\[M\]. Consulting Psychologists Press, 1978.
\[4\]RUSSELL J. Affective space is bipolar\[J\]. Journal of Personality and Social Psychology, 37(3): 345-356, 1979.
\[5\]MEHRABIAN A. Pleasure-Arousal-Dominance: A General Framework for Describing and Measuring Individual Differences in Temperament\[J\]. Current Psychology, 14(4): 261-292, 1996.
\[6\]OJALA T, PIETIKINEN M, MENP T. Multiresolution gray-scale and rotation invariant texture classification with local binary patterns\[J\]. IEEE Transactions on Pattern Analysis and Machine Intelligence, 24(7): 971-987, 2002.
\[7\]BARTLETT M S, LITTLEWORT G, FASEL I R, et al. Real time face detection and facial expression recognition: Development and applications to human computer interaction \[C\]//Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR) Workshops. Madison, USA: IEEE Computer Society: 42-47, 2003.
\[8\]DHALL A, GOECKE R, LUCEY S, et al. Collecting large, richly annotated facial-expression databases from movies\[J\]. IEEE Multimedia, 19(3): 34-41, 2012.
\[9\]VALSTAR M F, PANTIC M. Combined support vector machines and hidden Markov models for modeling facial action temporal dynamics\[C\]//Proceedings of IEEE International Workshop on Human-Computer Interaction. Rio de Janeiro, Brazil: Springer: 118-127, 2007.
\[10\]LEMAIRE P, ARDABILIAN M, CHEN L, et al. Fully automatic 3D facial expression recognition using differential mean curvature maps and histograms of oriented gradients\[C\]// Proceedings of the 10th IEEE International Conference and Workshops on Automatic Face and Gesture Recognition. Shanghai, China: IEEE Computer Society: 1-7, 2013.
\[11\]CHANGK I, BOWYER K W, FLYNN P J. Multiple nose region matching for 3D face recognition under varying facial expression\[J\]. IEEE Transactions on Pattern Analysis and Machine Intelligence, 28(10): 1695-1700, 2006.
\[12\]FANELLI G, DANTONE M, GALL J, et al. Random forests for real time 3D face analysis\[J\]. International Journal of Computer Vision, 101(3): 437-458, 2013.
\[13\]WU T, BARTLETT M S, MOVELLAN J R. Facial expression recognition using Gabor motion energy filters\[C\]//Proceedings of the 23rd IEEE Conference on Computer Vision and Pattern Recognition (CVPR), SanFrancisco, USA: IEEE Computer Society: 42-47, 2010.
\[14\]ZHAO G, PIETIKINEN M. Dynamic texture recognition using local binary patterns with an application to facial expressions\[J\]. IEEE Transactions on Pattern Analysis and Machine Intelligence (TPAMI), 29(6): 915-928, 2007.
\[15\]SUK M, PRABHAKARAN B. Real-time facial expression recognition on smartphones\[C\]//Proceedings of IEEE Winter Conference on Applications of Computer Vision (WACV). Waikoloa, USA: IEEE Computer Society: 1054-1059, 2015.
\[16\]YANG S, BHANU B. Facial expression recognition using emotion avatar image\[C\]//Proceedings of the 9th International Conference on Automatic Face and Gesture Recognition (FG). Santa Barbara, USA: IEEE Computer Society: 866-871, 2011.
\[17\]HASSANI B, MAHOOR M H. Facial expression recognition using enhanced deep 3D convolutional neural networks\[C\]//Proceedings of the 30th IEEE Conference on Computer Vision and Pattern Recognition (CVPR) Workshops. Honolulu, USA: IEEE Computer Society: 2278-2288, 2017.
\[18\]JUNG H, LEE S, YIM J, et al. Joint fine-tuning in deep neural networks for facial expression recognition\[C\]//Proceedings of the 15th IEEE International Conference on Computer Vision (ICCV). Santiago, Chile: IEEE Computer Society: 2983-2991, 2015.
\[19\]KIM B K, LEE H, ROH J, et al. Hierarchical committee of deep CNNs with exponentially-weighted decision fusion for static facial expression recognition\[C\]//Proceedings of the 17th ACM International Conference on Multimodal Interaction (ICMI). Seattle, USA: ACM: 427-434, 2015.
\[20\]BARGAL S A, BARSOUM E, CANTON-FERRER C, et al. Emotion recognition in the wild from videos using images\[C\]//Proceedings of the 18th ACM International Conference on Multimodal Interaction (ICMI). Tokyo, Japan: ACM: 433-436, 2016.
\[21\]LIU M, LI S, SHAN S, et al.AU-aware deep networks for facial expression recognition\[C\]//Proceedings of the 10th IEEE International Conference and Workshops on Automatic Face and Gesture Recognition (FG). Shanghai, China: IEEE Computer Society: 1-6, 2013.
\[22\]YAO N, CHEN H, GUO Q, et al. Non-frontal facial expression recognition using a depth-patch based deep neural network\[J\]. Journal of Computer Science and Technology (JCST), 2017, 32(6): 1172-1185.
