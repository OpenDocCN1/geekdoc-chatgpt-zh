

## 前方拥堵提醒算法描述

①定义一个函数`doTJW()`，它接受两个参数：`hvInfo`和`rteInfo`。`hvInfo`表示主车信息，`rteInfo`表示`RSI`。
②函数内部首先初始化一个变量：`warning_flag`。`warning_flag`表示预警等级，其中0表示无须预警，1表示前方拥堵提醒。
③从`hvInfo`中提取主车的x坐标和y坐标、速度、航向角、尺寸、车辆类型和所在车道，从`rteInfo`中提取道路拥堵状况和拥堵范围，并将其赋值给相应的变量。
④调用`distance()`函数，计算主车和拥堵路段之间的距离及相对位置关系。
⑤若距离小于所设阈值且主车位于拥堵路段所在车道，则将`warning_flag`设为1。
⑥最终返回`warning_flag`作为函数输出。

下面给出了基于Python的TJW算法实现示例代码，供读者在学习时参考。

![](images/txt007_18.jpg "pagenumber_ebook=161,pagenumber_book=151")
![](images/txt007_19.jpg "pagenumber_ebook=161,pagenumber_book=151")
![](images/txt007_20.jpg "pagenumber_ebook=161,pagenumber_book=151")
![](images/txt007_21.jpg "pagenumber_ebook=161,pagenumber_book=151")

### 前方拥堵提醒算法代码

## 7.6 交通事件场景应用台架实验

### 7.6.1 实验目的

![](images/txt007_22.jpg "pagenumber_ebook=162,pagenumber_book=152")

交通事件场景应用台架实验

①通过对交通事件场景进行实验操作，熟悉不同交通事件场景的关键指标参数。
②了解交通事件场景的算法配置。
③了解交通事件场景算法的代码实现，尝试对场景算法进行修改和设计。

### 7.6.2 实验原理

本实验以道路施工预警、限速预警和紧急车辆预警为例对交通事件场景应用进行说明，前方拥堵提醒与道路施工预警较为类似，此处不再作介绍。

#### (1) 道路施工预警

场景介绍：主车支持通过C-V2X通信，获取路侧设备共享的信息。临近施工区域时，主车接收RSU广播的道路施工RSI，获得RSU和施工区域的位置，判断是否进行道路施工预警，若存在发生事故风险，则对驾驶员进行预警，当主车通过施工区域后，道路施工预警信息取消。

道路施工预警算法的原理如图7.4所示。

![](images/txt007_23.jpg "pagenumber_ebook=162,pagenumber_book=152")

图7.4 道路施工预警算法的原理

其数据流程如图7.5所示。

![](images/txt007_24.jpg "pagenumber_ebook=163,pagenumber_book=153")

图7.5 道路施工预警算法的数据流程图

#### (2) 限速预警

场景介绍：主车支持通过C-V2X通信，获取路侧设备共享的信息。主车接收RSU广播的MAP，获取地图数据以及道路限速。若主车位于地图数据中的某条道路，则判断主车是否超速；若超速，则对驾驶员进行预警。当主车速度低于限速后，预警信息取消。

限速预警算法的原理如图7.6所示。

其数据流程如图7.7所示。

![](images/txt007_25.jpg "pagenumber_ebook=164,pagenumber_book=154")

图7.6 限速预警算法的原理

![](images/txt007_26.jpg "pagenumber_ebook=164,pagenumber_book=154")

图7.7 限速预警算法的数据流程图

#### (3) 紧急车辆预警

场景介绍：主车和远车均支持通过C-V2X通信交互自身位置和运行状态信息。若远车是紧急车辆或特殊车辆，则判断远车是否位于主车同一车道后方且距离小于阈值；若存在影响紧急车辆前行的可能，则显示紧急车辆预警信息，提醒主车驾驶员对紧急车辆让行。

紧急车辆预警算法的原理如图7.8所示。

其数据流程如图7.9所示。

![](images/txt007_27.jpg "pagenumber_ebook=165,pagenumber_book=155")

图7.8 紧急车辆预警算法的原理

![](images/txt007_28.jpg "pagenumber_ebook=165,pagenumber_book=155")

图7.9 紧急车辆预警算法的数据流程图·

### 7.6.3 实验内容

1）步骤一

如图7.10所示，在主菜单下单击“交通事件类场景实验”进入本次实验。

![](images/txt007_29.jpg "pagenumber_ebook=166,pagenumber_book=156")

图7.10 交通事件类场景实验

2）步骤二

进入实验后可以通过单击切换实验场景，分别了解道路施工预警的介绍及算法流程（如图7.11所示）、限速预警的介绍及算法流程（如图7.12所示）和紧急车辆预警的介绍及算法流程（如图7.13所示），单击“场景示例”可以查看对应场景的实验示例。

![](images/txt007_30.jpg "pagenumber_ebook=166,pagenumber_book=156")

图7.11 道路施工预警的介绍及算法流程图

![](images/txt007_31.jpg "pagenumber_ebook=167,pagenumber_book=157")

图7.12 限速预警的介绍及算法流程图

![](images/txt007_32.jpg "pagenumber_ebook=167,pagenumber_book=157")

图7.13 紧急车辆预警的介绍及算法流程图

3）步骤三

选择不同场景查看实验示例。

(1) 道路施工预警

①进入道路施工预警场景示例后，系统首先以默认参数进行仿真，界面主体为仿真场景，左侧显示相关消息，当触发预警时弹出警报，如图7.14所示。

![](images/txt007_33.jpg "pagenumber_ebook=168,pagenumber_book=158")

图7.14 道路施工预警场景示意图

②单击“参数配置”弹出参数编辑窗口，如图7.15所示，输入相关参数后单击“确定”，系统根据新参数重新进行仿真并加载仿真界面。

![](images/txt007_34.jpg "pagenumber_ebook=168,pagenumber_book=158")

图7.15 道路施工预警参数编辑

③通过配置不同参数进行多次实验，观察、记录、比较实验结果，得出实验结论，实验结束后可以单击右上角的“返回”按钮返回上级菜单。

(2) 限速预警

①进入限速预警场景示例后，系统首先以默认参数进行仿真，界面主体为仿真场景，左侧显示相关消息，当触发预警时弹出警报，如图7.16所示。

![](images/txt007_35.jpg "pagenumber_ebook=169,pagenumber_book=159")

图7.16 限速预警场景示意图

②单击“参数配置”弹出参数编辑窗口，如图7.17所示，输入相关参数后单击“确定”，系统根据新参数重新进行仿真并加载仿真界面。

![](images/txt007_36.jpg "pagenumber_ebook=169,pagenumber_book=159")

图7.17 限速预警参数编辑

③通过配置不同参数进行多次实验，观察、记录、比较实验结果，得出实验结论，实验结束后可以单击右上角的“返回”按钮返回上级菜单。

(3) 紧急车辆预警

①进入紧急车辆预警场景示例后，系统首先以默认参数进行仿真，界面主体为仿真场景，左侧显示相关消息，当触发预警时弹出警报，如图7.18所示。

![](images/txt007_37.jpg "pagenumber_ebook=170,pagenumber_book=160")

图7.18 紧急车辆预警场景示意图

②单击“参数配置”弹出参数编辑窗口，如图7.19所示，输入相关参数后单击“确定”，系统根据新参数重新进行仿真并加载仿真界面。

![](images/txt007_38.jpg "pagenumber_ebook=170,pagenumber_book=160")

图7.19 紧急车辆预警参数编辑

③通过配置不同参数进行多次实验，观察、记录、比较实验结果，得出实验结论，实验结束后可以单击右上角的“返回”按钮返回上级菜单。

4）步骤四

可以在实验选择界面多次进入不同实验以加深理解，或者在结束实验后单击“×”返回主界面。

### 思考题

1.总结交通事件场景应用的关键判定过程。
2.请结合前面基本消息类型考虑交通事件场景应用需要哪些基本消息类型做支撑。
3.除了本书介绍的几种交通事件场景，试列举出1到2种其他的交通事件场景，并简要阐述其基本原理。
4.根据交通事件场景应用的工作原理和算法实现，分析它们与碰撞预警场景以及信号灯路口场景的异同。

## 参考文献

\[1\]2022年我国机动车保有量达4.17亿辆\[EB/OL\].http：//www.gov.cn/xinwen/2023-01/11/content\_5736176.htm.
\[2\]陈山枝.蜂窝车联网（C-V2X）及其赋能智能网联汽车发展的辩思与建议\[J\].电信科学，2022，38（7）：17.
\[3\]Levinson J，Askeland J，Becker J，et al.Towards fully autonomous driving:systems and algorithms\[C\]//2011IEEE Intelligent Vehicles Symposium(IV).IEEE，2011:163-168.
\[4\]Nastjuk I，Herrenkind B，Marrone M，et al.What drives the acceptance of autonomous driving?An investigation of acceptance factors from an end-user's perspective\[J\].Technological Forecasting and Social Change，2020，161:120319.
\[5\]杨放春，王尚广，李静林，等.车联网综述（英文）\[J\].中国通信，2014，11（10）：1-15.
\[6\]Kenney J B.Dedicated short-range communications(DSRC)standards in the United States\[J\].Proceedings of the IEEE，2011，99(7):1162-1182.
\[7\]Chen S，Hu J，Shi Y，et al.A vision of C-V2X:technologies，field testing，and challenges with chinese development\[J\].IEEE Internet of Things Journal，2020，7(5):3872-3881.
\[8\]Shadrin S S，Ivanova A A.Analytical review of standard Sae J3016“taxonomy and definitions for terms related to driving automation systems for on-road motor vehicles”with latest updates\[J\].Avtomobil'.Doroga.Infrastruktura.，2019，3(21):10.
\[9\]国家智能网联创新中心.车路云一体化系统白皮书\[R/OL\].https：//www.iotku.com/News/785932613352161280.html.
\[10\]技术分享|无人驾驶的眼睛\[EB/OL\]https：//zhuanlan.zhihu.com/p/586343440.
\[11\]崔胜民.智能网联汽车新技术\[M\].北京：化学工业出版社，2016.
\[12\]Taraba M，Adamec J，Danko M，et al.Utilization of modern sensors in autonomous vehicles\[C\]//2018ELEKTRO.IEEE，2018:1-5.
\[13\]Khan S，Rahmani H，Shah S A A，et al.A guide to convolutional neural networks for computer vision\[J\].Synthesis Lectures on Computer Vision，2018，8(1):1-207.
\[14\]Zou Q，Sun Q，Chen L，et al.A comparative analysis of LiDAR SLAM-based indoor navigation for autonomous vehicles\[J\].IEEE Transactions on Intelligent Transportation Systems，2021.
\[15\]Bishop C M，Nasrabadi N M.Pattern recognition and machine learning\[M\].New York:Springer，2006.
\[16\]Guo Y，Wang H，Hu Q，et al.Deep learning for3d point clouds:a survey\[J\].IEEE Transactions on Pattern Analysis and Machine Intelligence，2020，43(12):4338-4364.
\[17\]Garcia M H C，Molina-Galan A，Boban M，et al.A tutorial on5G NR V2X communications\[J\].IEEE Communications Surveys&Tutorials，2021，23(3):1972-2026.
\[18\]崔胜民.智能网联汽车新技术\[M\].北京：化学工业出版社，2016.
\[19\]Chen S，Hu J，Shi Y，et al.Vehicle-to-everything(V2X)services supported by LTE-based systems and5G\[J\].IEEE Communications Standards Magazine，2017，1(2):70-76.
\[20\]陈山枝，胡金玲，等.蜂窝车联网（C-V2X）\[M\].北京：人民邮电出版社，2021.
\[21\]3GPP S2-153355.New SID on architecture enhancements for LTE support of V2X services\[Z\].2015.
\[22\]3GPP TS23.285，v14.9.0.Architecture enhancements for V2X services\[S\].2015.
\[23\]陈山枝，时岩，胡金玲.蜂窝车联网（C-V2X）综述\[J\].中国科学基金，2020，34（2）：179-185.
\[24\]陈山枝，胡金玲，时岩，等.LTE-V2X车联网技术、标准与应用\[J\].电信科学，2018，34（4）：7-17.
\[25\]3GPP TS36.321，v14.7.0.E-UTRA;Medium access control(MAC)protocol specification\[S\].2018.
\[26\]3GPP TS23.287，v16.2.0.Architecture enhancements for5G system(5GS)to support vehicle-to-everything(V2X)services\[S\].2020.
\[27\]3GPP R1-1905012.Physical layer procedures for sidelink\[Z\].2019.
\[28\]3GPP TS38.213，v16.1.0.Physical layer procedures for control\[S\].2020.
\[29\]3GPP TS38.215，v16.1.0.Physical layer measurements\[S\].2020.
\[30\]Duan W，Gu J，Wen M，et al.Emerging technologies for5G-IoV networks:applications，trends and opportunities\[J\].IEEE Network，2020，34(5):283-289.
\[31\]中国汽车工程学会.合作式智能运输系统车用通信系统应用层及应用数据交互标准\[S\].2017.
\[32\]中国汽车工程学会.合作式智能运输系统车用通信系统应用层及应用数据交互标准（第一阶段）：T/CSAE53-2020\[S\].2020.
\[33\]中国通信标准化协会.基于LTE的车联网无线通信技术消息层技术要求：YD/T 3709-2020\[S\].2020.
\[34\]中国汽车工程学会.合作式智能运输系统车用通信系统应用层及应用数据交互标准（第二阶段）：T/CSAE157-2020\[S\].2020.
\[35\]中国汽车工程学会.基于车路协同的高等级自动驾驶数据交互内容：T/CSAE158-2020\[S\].2020.

## 缩略语

3GPP　第三代合作伙伴计划（The3rd Generation Partnership Project）
ADAS　先进驾驶辅助系统（Advanced Driver Assistance System）
ADS　自动驾驶系统（Automated Driving System）
ALKS　自动车道保持系统（Automated Lane Keeping System）
CCSA　中国通信标准化协会（China Communications Standards Association）
C-ITS　中国智能交通产业联盟（China ITS Industry Alliance）

