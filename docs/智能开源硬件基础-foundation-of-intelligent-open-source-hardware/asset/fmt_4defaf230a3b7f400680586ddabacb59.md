
## 2.3.1 电容元件

将电容元件的伏库特性关系代入上式，就可以得到电容元件的伏安特性关系

![](images/txt003_45.jpg "pagenumber_ebook=46,pagenumber_book=34")

上式表明：某一时刻电容电流的大小取决于该时刻电容电压的变化率。电容电压的变化率越大则电容电流也就越大。电容电压的变化率越小，电容电流也越小。当电容电压不变化时，电容电流就变为零。一个元件上有电压却没有电流，这和断路的效果相同，所以说电容有隔直的作用。如果电压发生跳变，就会出现无穷大的电流，这在一般条件下是不可能的，即电容电压不会发生跳变，称为有惯性。

要声明的是，式（2 - 25）是在电流电压参考方向关联情况下的公式。如果参考方向非关联，公式前面要加负号，即

![](images/txt003_46.jpg "pagenumber_ebook=46,pagenumber_book=34")

如果用电压表示电流，将得到伏安关系的另一种表达形式，对上式进行积分：

![](images/txt003_47.jpg "pagenumber_ebook=46,pagenumber_book=34")

设`t=0`为电路工作的初始时刻，则

![](images/txt003_48.jpg "pagenumber_ebook=46,pagenumber_book=34")

可见，电容元件在`t`时刻的电压大小不仅与该时刻的电容电流大小有关，还与之前各个时刻电容电流的大小有关，所以电容是一种记忆元件或动态元件。

## 2.3.2 电感元件

电感元件是实际电感线圈的理想化模型。通过物理知识可知，当电感线圈中通入电流`i(t)`时，线圈中产生磁场，磁场在线圈截面上的通量叫作磁通量，用字母`Φ`表示，单位为韦伯（`Wb`）。若线圈有`N`匝，每匝线圈磁场相互交链，则磁链为

![](images/txt003_49.jpg "pagenumber_ebook=46,pagenumber_book=34")

显然，磁链`Ψ`是电流`i(t)`的函数。当元件周围的媒质为非铁磁物质的时候，磁链`Ψ`与电流`i(t)`成正比例关系。这一关系可表示为

![](images/txt003_50.jpg "pagenumber_ebook=46,pagenumber_book=34")

式中`L`为正值常数，它是用来度量特性曲线斜率的，称为电感或自感。在国际单位制中，`L`的单位为亨利（`H`），磁链的单位为韦伯（`Wb`）。这种把电能转换成磁场能的理想元件称为电感元件。由于线圈中磁链`Ψ`与流过它的电流`i(t)`的关系（韦安特性）是线性的，故称这种电感元件为线性电感元件，其符号如图2 - 10所示。

理想的电感线圈中通入直流电流，线圈中就产生不随时间变化的稳恒磁场，由于它不会再在线圈中感应出电压来，因此线圈两端电压为零。但是，当电感线圈中通入交变电流时，线圈中就会出现交变的磁链，而交变的磁链又会在线圈两端感应出电压，即

![](images/txt003_51.jpg "pagenumber_ebook=47,pagenumber_book=35")

图2-10　电感元件的符号

![](images/txt003_52.jpg "pagenumber_ebook=47,pagenumber_book=35")

将`ψ`与`i`的关系代入上式，并考虑到线性电感的电感量`L`不随时间变化，得：

![](images/txt003_53.jpg "pagenumber_ebook=47,pagenumber_book=35")

此式就是电感元件的伏安特性关系，也就是电感元件电流、电压的瞬时关系表达式，在电流、电压参考方向一致时使用。在非关联参考方向下，那么在公式前必须加一个负号，即

![](images/txt003_54.jpg "pagenumber_ebook=47,pagenumber_book=35")

当然，电感元件的伏安特性表达式也可以写成积分的形式：

![](images/txt003_55.jpg "pagenumber_ebook=47,pagenumber_book=35")

如果`t=0`时刻为初始时刻，则

![](images/txt003_56.jpg "pagenumber_ebook=47,pagenumber_book=35")

此表达式说明：电感元件在某个时刻的电流的大小不仅和这个时刻的电压有关，还跟之前每时每刻的电压有关。因此通常称电感元件为记忆元件或动态元件。

## 2.4 电路定理的相量形式

### 2.4.1 基尔霍夫定律的相量形式

在线性正弦电路中，所有电流和电压都是同频率的正弦量，因此基尔霍夫定律也可以用相量表示，见表2 - 2。

表2-2　基尔霍夫定律的相量形式

![](images/txt003_57.jpg "pagenumber_ebook=47,pagenumber_book=35")

### 2.4.2 电路元件的相量模型

在正弦交流电路中各支路的电流、电压都是同频率的正弦量，为了用相量法分析正弦交流电路，首先研究电路中3个基本元件的相量模型。

#### 1. 电阻元件

图2 - 11（a）所示为电阻元件的时域伏安关系，电阻值为`R`，电流和电压的参考方向关联。设流过电阻的电流为`i`，其伏安特性的时域表示为

![](images/txt003_58.jpg "pagenumber_ebook=48,pagenumber_book=36")

在正弦交流电路中，电阻的电流和电压为同频率的正弦量，有效值关系为

![](images/txt003_59.jpg "pagenumber_ebook=48,pagenumber_book=36")

电阻元件的电压有效值和电流有效值的关系与直流电阻电路中的欧姆定律完全相同，电压与电流始终同相位，用相量形式表示这种关系为

![](images/txt003_60.jpg "pagenumber_ebook=48,pagenumber_book=36")

电阻元件的相量伏安关系如图2 - 11（b）所示，相量图如图2 - 11（c）所示。

![](images/txt003_61.jpg "pagenumber_ebook=48,pagenumber_book=36")

图2-11　电阻元件的相量模型

（a）时域伏安关系；（b）相量伏安关系；（c）相量图

#### 2. 电容元件

图2 - 12（a）所示为电容元件的时域伏安关系，电容量为`C`，电流与电压参考方向关联。设电容的电压为`u(t)`，则电容元件的伏安特性的瞬时表达式为

![](images/txt003_62.jpg "pagenumber_ebook=48,pagenumber_book=36")

在正弦交流电路中，电容的电流和电压是同频率的正弦量，有效值关系为

![](images/txt003_63.jpg "pagenumber_ebook=48,pagenumber_book=36")

相位关系为

![](images/txt003_64.jpg "pagenumber_ebook=48,pagenumber_book=36")

这表明电容元件的电压有效值和电流有效值也具有类似于电阻电路中的欧姆定律的关系，比例系数为容抗；电容电压初相位落后于电流初相位90°，用相量形式表示这种关系为

![](images/txt003_65.jpg "pagenumber_ebook=48,pagenumber_book=36")

电容元件的相量伏安关系如图2 - 12（b）所示，相量图如图2 - 12（c）所示。

#### 3. 电感元件

电感元件是实际电感线圈的理想化模型。电感元件是存储磁能的器件。

图2 - 13（a）所示为电感元件的时域伏安关系，电感量为`L`，电流和电压的参考方向关联。设流过电感的电流为`i(t)`，则电感元件伏安特性的瞬时表达式为

![](images/txt003_66.jpg "pagenumber_ebook=49,pagenumber_book=37")

图2-12　电容元件的相量模型

（a）时域伏安关系；（b）相量伏安关系；（c）相量图

![](images/txt003_67.jpg "pagenumber_ebook=49,pagenumber_book=37")

上式表明，在某一时刻电感的电压取决于这个时刻电流的变化率，而不是该时刻电流的数值。在稳恒直流电路中，流过电感的电流为常数，随时间的变化率为零，所以电感两端电压也一定为零。对于外电路来说，工作在稳恒直流电路中的电感元件相当于一根导线。当电感中有变化的电流通过时，线圈两端将会出现电压，而且电流变化越快，电感两端电压越高。

在正弦交流电路中，电感的电流和电压为同频率的正弦量，有效值关系为

![](images/txt003_68.jpg "pagenumber_ebook=49,pagenumber_book=37")

相位关系为

![](images/txt003_69.jpg "pagenumber_ebook=49,pagenumber_book=37")

这表明电感元件的电压有效值和电流有效值的关系类似于直流电阻电路中的欧姆定律，比例系数是感抗`Lω`。电感电压的初相位超前电流初相位90°。用相量形式表示这种关系为

![](images/txt003_70.jpg "pagenumber_ebook=49,pagenumber_book=37")

电容元件的相量伏安关系如图2 - 13（b）所示，相量图如图2 - 13（c）所示。

![](images/txt003_71.jpg "pagenumber_ebook=49,pagenumber_book=37")

图2-13　电感元件的相量模型

（a）时域伏安关系；（b）相量伏安关系；（c）相量图

## 2.5 阻抗与导纳

#### 1. 电阻

电阻`R`的阻抗值等于电阻值`R`，它是一个实数，而且与频率无关。电阻的导纳是其电阻值的倒数，也就是它的电导值`G`。

#### 2. 电容

电容量为`C`的电容元件的阻抗为

![](images/txt003_72.jpg "pagenumber_ebook=50,pagenumber_book=38")

`YC`为电容的导纳，其中`CB=Cω`，称为容纳，单位是西门子（`S`）。

#### 3. 电感

设电感元件的电感为`L`，它的阻抗为

![](images/txt003_73.jpg "pagenumber_ebook=50,pagenumber_book=38")

定义电感阻抗的虚部为感抗`XL`，即

![](images/txt003_74.jpg "pagenumber_ebook=50,pagenumber_book=38")

可以看出，感抗`XL`正比于频率`ω`，频率越低感抗越小，所以在直流电作用下电感相当于一根导线（短路）；反之，频率越高感抗越大，频率高至无穷大时，感抗变为无穷大，这时电感元件相当于断开。

电感的导纳为

![](images/txt003_75.jpg "pagenumber_ebook=50,pagenumber_book=38")

## 2.6 一阶电路的零输入响应

一阶电路根据所含动态元件不同，分为一阶`RC`电路和一阶`RL`电路。如果在换路之前，动态元件已经存储能量，换路后，电路中的动态元件在无激励的情况下通过放电而产生的响应称为零输入响应。响应对应微分方程的解。

### 2.6.1 RC电路的零输入响应

图2 - 14（a）所示的电路为一阶`RC`电路。开关闭合前，电路已处于稳态，电容的电压`uC(0-)=U0`，当`t=0`时，开关闭合，现在分析`t≥0`时电容电压`uC`和电流`iC`的变化规律。电容在`t≥0`时会通过`R`支路放电。在放电过程中，在电阻`R`上消耗能量，当`t→∞`时放电结束，能量全部消耗在电阻上。开关闭合后，根据`KVL`有：

![](images/txt003_76.jpg "pagenumber_ebook=51,pagenumber_book=39")

图2-14　一阶RC电路及其零输入响应曲线

（a）一阶RC电路；（b）零输入响应曲线

![](images/txt003_77.jpg "pagenumber_ebook=51,pagenumber_book=39")

这是一个以电容电压`uC`为变量的一阶线性齐次微分方程，其解的形式为

![](images/txt003_78.jpg "pagenumber_ebook=51,pagenumber_book=39")

将其代入式（2 - 44），得到微分方程的特征方程

![](images/txt003_79.jpg "pagenumber_ebook=51,pagenumber_book=39")

解得特征根为

![](images/txt003_80.jpg "pagenumber_ebook=51,pagenumber_book=39")

`λ`称为电路的自然频率或固有频率。由此可得微分方程的通解为

![](images/txt003_81.jpg "pagenumber_ebook=51,pagenumber_book=39")

利用初始值`uC(0+)=U0`确定系数`A`，即

![](images/txt003_82.jpg "pagenumber_ebook=51,pagenumber_book=39")

所以，`A=U0`，将其代回式（2 - 45）得`t≥0`时，电容电压的零输入响应为

![](images/txt003_83.jpg "pagenumber_ebook=51,pagenumber_book=39")

电路的电流为

![](images/txt003_84.jpg "pagenumber_ebook=51,pagenumber_book=39")

上两式中，`τ=RC`，具有时间量纲，称为时间常数，单位为秒（`s`）。`τ`反映了电路过度过程的进展速度。`τ`越小，其倒数越大，电容放电越快；反之，则电容放电越慢。

`RC`电路的零输入响应曲线如图2 - 14（b）所示。

### 2.6.2 RL电路的零输入响应

图2 - 15（a）所示为一阶`RL`电路。`t＜0`时，电路已处于稳态，`t=0`时，开关断开，现在分析`t≥0`时电感电压`u L (t)`和电感电流`i L(t)`的变化规律。

![](images/txt003_85.jpg "pagenumber_ebook=51,pagenumber_book=39")

`t=0`时，进行换路，此时`iL (0-)=iL(0+)=I 0`。

`t≥0+`时，电路变为单回路，电感通过电阻释放能量。最终，能量全部消耗在电阻上，电路将重新进入稳态。`RL`电路的零输入响应的求解过程如同`RC`电路的求解，这里直接给出结果，即其零输入响应为

![](images/txt003_86.jpg "pagenumber_ebook=52,pagenumber_book=40")

图2-15　一阶RL电路及其零输入响应曲线

（a）一阶RL电路；（b）零输入响应曲线

![](images/txt003_87.jpg "pagenumber_ebook=52,pagenumber_book=40")

图2 - 15（b）所示为`RL`电路的零输入响应曲线及电压的变化曲线。

零输入响应是在输入为零时，由非零初始状态产生的。它充分体现出在没有外来干预的“自由”状态下电路的表现，反映了电路的固有性质。它在理论上具有重要的意义。考虑到这一响应与初始状态和电路的特性有关。在求解时，首先必须掌握电容电压或电感电流的初始值，至于电路的特性，对一阶电路来说，则是通过时间常数`τ`来体现的。不论是`RC`电路还是`RL`电路，零输入响应都是随时间按指数规律衰减的，这是因为在没有外施电源的条件下，原有的储能总是要逐渐衰减到零的。在`RC`电路中，电容电压`uC`总是由初始值`uC (0)`按指数规律单调地衰减到零的，其时间常数`τ=RC`；在`RL`电路中，总是由初始值`iL (0)`按指数规律单调地衰减到零，其时间常数`τ= L/R`。掌握了`u C (t)`、`i L(t)`后，便可设法求得其他各个电压、电流。

初始状态既可认为是电路的激励，则从式（2 - 44）～式（2 - 47）不难看出：若初始状态增大`m`倍，则零输入响应也相应地增大`m`倍。这种初始状态和零输入响应的正比关系称为零输入响应比例性，亦即零输入响应是初始状态的线性函数，也简称零输入响应线性或比例性。

例2 - 4 如图2 - 16所示，换路前电路处于稳态，`t=0`时将开关打开，求换路后电感电流`iL (t)`和电容电压`u C(t)`。

解 由换路前的稳态电路得：

![](images/txt003_89.jpg "pagenumber_ebook=52,pagenumber_book=40")

![](images/txt003_90.jpg "pagenumber_ebook=53,pagenumber_book=41")

图2-16　例2-4图

根据换路定则，有：

![](images/txt003_91.jpg "pagenumber_ebook=53,pagenumber_book=41")

换路后，开关打开，电路分为左、右两个独立的一阶电路。左边回路是一阶`RL`电路的零输入响应，代入`i L(t)`零输入响应公式，得：

![](images/txt003_92.jpg "pagenumber_ebook=53,pagenumber_book=41")

右边回路是一阶`RC`电路的零输入响应，代入`u C(t)`零输入响应公式，得：

![](images/txt003_93.jpg "pagenumber_ebook=53,pagenumber_book=41")

### 2.6.3 阻抗的串联和并联

#### 1. 欧姆定律的复数形式

![](images/txt003_94.jpg "pagenumber_ebook=53,pagenumber_book=41")

`Z`称为复数阻抗，单位为Ω。

![](images/txt003_95.jpg "pagenumber_ebook=53,pagenumber_book=41")

`Y`称为复数导纳，单位为`S`。

#### 2. 阻抗的串联

多个阻抗首尾相接构成二端网络称为阻抗的串联，根据`KVL`的相量形式，有：

![](images/txt003_96.jpg "pagenumber_ebook=53,pagenumber_book=41")

所以，多个阻抗串联可等效为一个阻抗，阻抗值等于串联的所有阻抗值之和，即

![](images/txt003_97.jpg "pagenumber_ebook=53,pagenumber_book=41")

等效导纳为

阻抗`Zk`上分得的电压相量为
