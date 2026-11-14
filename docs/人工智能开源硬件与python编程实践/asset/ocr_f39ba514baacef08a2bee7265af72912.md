-vec vec 文件名；
-bg 负样本描述文件名称，也就是负样本的说明文件( .dat)；
-nstage 20 指定训练层数，推荐 15 ~ 20，层数越高，耗时越长；
-nsplits 分裂子节点数目，选取默认值2；
-minhitrate 最小命中率，即训练目标准确度；
-maxfalsealarm 最大误检率，每一层训练到这个值小于 0.5 时训练结束，
进入下一层训练；

-nps 在每个阶段用来训练的正样本数目；
-nneg 在每个阶段用来训练的负样本数目这个值可以设置大于真正的负
样本图像数目，程序可以自动从负样本图像中切割出和正样本大小一致的，
这个参数一般设置为正样本数目的 1 ~ 3 倍；-w，-h 样本尺寸，与前面对应；
-mem 程序可使用的内存，这个设置为 256 即可，实际运行时根本就不怎
么耗内存，以 MB 为单位；
-mode ALL 指定 haar 特征的种类，BASIC 仅仅使用垂直特征，ALL 表示使
用垂直以及 45 度旋转特征；

-w 与 -h 表示样本的宽与高，必须跟 vec 中声明保持一致。
实际训练中，需要要把负样本和 bg. txt 文件复制到 build\x64\vc14\bin
目录下，还需要在 data 文件夹下新建文件 HaarReslut 来保存训练的结果，这样
才能完成训练工作。实验中，输入以下参数启动分类器训练：
opencv_traincascade.exe -data C:\Users\Administrator\Desktop\data\Haar-
Reslut -vec ;C:\Users\Administrator\Desktop\data\mysamples_341. vec -bg bg.
txt -numPos 170 -numNeg 500 -numStages 12 -featureType HAAR -w 24 -h 24 -
minHitRate 0.996 -maxFalseAlarmRate 0.5 -mode ALL

训练情况如下。训练结束后 HaarReslut 文件会告诉训练结果，cascade.
xml 是训练得到的模型参数，其他的的是训练过程中的产生的文件，应用中并不
需要。

E：\Program\OpenCV\opencv\build\x64\vc14\bin > opencv_traincascade -
data;C:\Users\Administrator\Desktop\data-vec C:\Users\Administrator\Desktop\
data\mysamples_1204. vec -bg bg. txt-numPos 170-numNeg 500-numStages 12-
featureType LBP -w 24 -h 24 -minHitRate 0.997-maxFalseAlarmRate 0.5
因为数据集比较少，所以训练速度应该会很快，大概 2 min 左右即可训练
完成。如果想要提高准确度，可以把-numNeg 500 改为-numNeg 1000，扩大负
样本的数据这样就可以提高准确率了。