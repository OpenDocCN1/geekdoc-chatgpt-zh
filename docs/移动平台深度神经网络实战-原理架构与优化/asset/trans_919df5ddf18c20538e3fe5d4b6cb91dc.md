

第47～63行，定义了`loadBinaryBlob`函数，用于根据给定的二进制输入流从中加载二进制数据，并返回对应的矩阵对象。

第72行，定义了`bboxRegression`函数，该函数用于实现检测框的回归。

第130～134行，定义了默认构造函数。

第136～179行，定义了构造函数，采用`PackageManager`从打包的文件中获取数据流，然后调用网络的`loadBinaryParams`和`loadModel`加载网络层参数和模型参数。

第181～189行，设置检测一些参数，比如缩放因子等。

第194～273行，完成目标对象检测，返回检测框。① 根据设定的缩放因子缩放图像以加快检测速度；② 对输入数据进行预处理，包括灰度化、减均值、标准化等；③ 调用`extract`完成检测框提取；④ 通过循环选定符合要求的检测框；⑤ 根据检测框的分数排序，并筛选分数最高的几个框作为最后的结果。

### 8.5.3 基于AlexNet的识别分类实现

首先我们定义识别分类类`extractor.h`，如代码清单8-15所示。

代码清单8-15 基于C++实现的识别分类器定义

```
 1 #include "types.h"
 2 #include "net.h"
 3 
 4 namespace learnml {
 5 
 6 class ExtractNet
 7 {
 8 public:
 9     ExtractNet();
10     ExtractNet(const char* modelPath);
11 
12     int ExtractFeature(unsigned char* imageData, int width, int height, int pitch, int bpp,
13         MlLandmarkResult* objectLandMarkResults, unsigned char* feature);
14 
15 private:
16     Net* extractNet;
17 };
18 
19 }
```

第6行，定义了`ExtractNet`类。

第9～10行，声明了构造函数。

第12行，声明了特征提取成员函数。

第16行，定义了成员变量`extractNet`，适用于提取特征的网络。

最后实现识别分类类`extractor.cpp`，如代码清单8-16所示。

代码清单8-16 基于C++实现的识别分类器的具体实现

```
 1 #include "extractNet.h"
 2 #include "cropImage.h"
 3 #include "Exception.h"
 4 
 5 #include "resunpack/resunpack.hpp"
 6 
 7 #include <string>
 8 #include <algorithm>
 9 #include <fstream>
10 #include <iostream>
11 #include <sstream>
12 
13 namespace learnml {
14 
15 ExtractNet::ExtractNet(const char* modelPath)
16 {
17     using learnml::resunpack::PackageManager;
18     using learnml::resunpack::Package;
19     using learnml::resunpack::ResunpackException;
20 
21     try {
22         PackageManager& packageManager = PackageManager::GetInstance();
23 
24         std::string modelDirPath = modelPath;
25         Package& extractPackage = packageManager.openPackage(modelDirPath + "/Extractor.pkg");
26         std::istream& extractNetParamsFile = dynamic_cast<std::istream&>(extractPackage.openResource("Extractor.parambin"));
27         std::istream& extractModelFile = dynamic_cast<std::istream&>(extractPackage.openResource("Extractor.bin"));
28 
29         extractNet = new Net();
30         extractNet->loadBinaryParams(extractNetParamsFile);
31         extractNet->loadModel(extractModelFile);
32 
33         extractPackage.closeResource(extractNetParamsFile);
34         extractPackage.closeResource(extractModelFile);
35         packageManager.closePackage(extractPackage);
36 
37         CropImageUtil::GetInstance();
38     }
39     catch ( const ResunpackException& ) {
40         throw Exception(AMCORE_INVALID_MODEL, "Open model of extractor failed");
41     }
42 }
43 
44 int ExtractNet::ExtractFeature(unsigned char* imageData, int width, int height, int pitch, int bpp,
45     MlLandmarkResult* faceLandMarkResults, unsigned char* feature)
46 {
47     minicv::Mat image;
48     if ( bpp == 8 )
49     {
50         image = minicv::Mat(height, width, CV_8UC1, imageData);
51         minicv::cvtColor(image, image, CV_GRAY2BGR);
52     }
53     else if ( bpp == 24 )
54     {
55         image = minicv::Mat(height, width, CV_8UC3, imageData);
56     }
57     else
58     {
59         return -1;
60     }
61 
62     int isMirror = 0;
63     TLandmarks1 lms;
64     lms.count = 25;
65 
66     for ( int k = 0; k < lms.count; k++ )
67     {
68         lms.pts[k].id = landmark_ids[k];
69         lms.pts[k].x = faceLandMarkResults->landmarks[k * 2];
70         lms.pts[k].y = faceLandMarkResults->landmarks[k * 2 + 1];
71     }
72 
73     vector<minicv::Mat> patches = CropImageUtil::PreparePatches(image, &lms, isMirror);
74     if ( patches.size() == 0 )
75         return -2;
76 
77     minicv::Mat bgr = patches[0];
78 
79     Mat in =
80         Mat::from_pixels(bgr.data, Mat::PIXEL_BGR, bgr.cols, bgr.rows);
81 
82     int total = in.total();
83     for ( int i = 0; i < total; i++ ) {
84         in[i] = (in[i] - 127.5f) * 0.0078125f;
85     }
86 
87     Extractor extractEngine = extractNet->create_extractor();
88     extractEngine.set_num_threads(THREAD_COUNT);
89 
90     Mat out;
91     extractEngine.input("Data1", in);
92     extractEngine.extract("L2", out);
93 
94     memcpy(feature, out.data, sizeof(float)* 256);
95 
96     return 0;
97 }
98 
99 }
```

第15～42行，定义了类的构造函数，参数是模型地址。我们使用`PackageManager`从打包文件中打开对应文件的二进制数据流，然后调用网络的`loadBinaryParams`和`loadModel`成员函数加载网络的层参数和模型参数。

第44～97行，定义了`ExtractFeature`函数，参数是输入图像、宽度、高度、每个通道的字节数、位深、检测结果和特征缓冲区。首先将图像进行灰度化，然后调用`PreparePatches`，从图像中将需要分类的位置图切割出来，最后将这些切片组合，再一起输入特征、提取网络、提取图像特征。提取后将特征写入缓冲区中。

### 8.5.4 接口设计封装

由于C++的ABI问题，如果在JNI中直接使用C++的类并不是特别合适，因此我们会再封装一个C接口。检测的C接口实现如代码清单8-17所示。

代码清单8-17 检测的C接口

