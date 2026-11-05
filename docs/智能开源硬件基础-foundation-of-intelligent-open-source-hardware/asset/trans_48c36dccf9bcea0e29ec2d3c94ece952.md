

![](images/txt003_98.jpg "pagenumber_ebook=53,pagenumber_book=41")

![](images/txt003_99.jpg "pagenumber_ebook=54,pagenumber_book=42")

### 例2-5 求图2-17中支路的阻抗和导纳
图中已给出元件的阻抗。

![](images/txt003_100.jpg "pagenumber_ebook=54,pagenumber_book=42")

### 3. 阻抗的并联
多个阻抗并列地连接在相同的两个节点上的连接方式称为阻抗的并联，根据`KCL`的相量形式，有：

![](images/txt003_101.jpg "pagenumber_ebook=54,pagenumber_book=42")

图2-17　例2-5图

![](images/txt003_102.jpg "pagenumber_ebook=54,pagenumber_book=42")

所以多个阻抗并联可等效为一个阻抗，等效阻抗的导纳值等于并联的所有阻抗的导纳值之和，即

![](images/txt003_103.jpg "pagenumber_ebook=54,pagenumber_book=42")

等效阻抗为

![](images/txt003_104.jpg "pagenumber_ebook=54,pagenumber_book=42")

阻抗`Zk`上分得的电流相量

![](images/txt003_105.jpg "pagenumber_ebook=54,pagenumber_book=42")

### 例2-6 求图2-18中支路的阻抗和导纳
图中已给出元件的阻抗。

![](images/txt003_106.jpg "pagenumber_ebook=54,pagenumber_book=42")

图2-18　例2-6图

解 根据阻抗和导纳的定义式即可得出：

![](images/txt003_107.jpg "pagenumber_ebook=54,pagenumber_book=42")

## 2.7 用相量法分析电路的正弦稳态响应
将相量形式的欧姆定律和基尔霍夫定律应用于电路的相量模型，建立相量形式的电路方程求解，即可得到电路的正弦稳态响应，这一方法称为相量分析法。
和电阻电路的电路方程一样，相量形式的电路方程也是线性代数方程，只是方程式的系数一般是复数，因此分析电阻电路的各种公式、方法和定理乃至技巧都适用于正弦交流稳态电路的相量分析法。其分析步骤一般如下：
（1）画出和时域电路相对应的电路相量模型；
（2）建立相量形式的电路方程，求出相应的相量；
（3）将求得的相量变换成对应的正弦函数。

### 例2-7 试求图2-19所示电路中的`uC`，已知`C=1μF`，![](images/txt003_108.jpg "pagenumber_ebook=55,pagenumber_book=43")

![](images/txt003_109.jpg "pagenumber_ebook=55,pagenumber_book=43")

图2-19　例2-7图

解 用相量法求解。电流的相量形式为

![](images/txt003_110.jpg "pagenumber_ebook=55,pagenumber_book=43")

电容的容抗为

![](images/txt003_111.jpg "pagenumber_ebook=55,pagenumber_book=43")

那么电容电压的相量形式为

![](images/txt003_112.jpg "pagenumber_ebook=55,pagenumber_book=43")

将电压的相量形式转化为一般形式为

![](images/txt003_113.jpg "pagenumber_ebook=55,pagenumber_book=43")

# 第3章 一阶电路

## 3.1 电路的换路过程及换路定则
在电阻电路中，由于线性电阻的伏安特性关系是代数关系，因此，描述电阻电路的方程是一组代数方程。由代数方程描述的电路通常被称为静态电路。静态电路的响应仅由外加激励引起，当电阻电路从一种工作状态转到另一种工作状态时，电路中的响应也将立即从一种工作状态转到另一种工作状态。
实际上，许多实际电路模型中不仅包含电阻元件和电源元件，还包含电容元件和电感元件，这两种元件的电压与电流的约束关系为微分或积分关系，通常称这类元件为动态元件或储能元件。含有动态元件的电路称为动态电路，描述动态电路的方程是以电压或电流为变量的微分方程。
在动态电路中，通常将描述电路的微分方程的最高阶数定义为动态电路的阶数。一般地，只含有一个动态元件的电路的微分方程为一阶微分方程，所以只含有一个动态元件的电路称为一阶电路；含有n个独立的动态元件的电路的微分方程的最高阶次为n，这样的动态电路称为n阶电路。动态电路研究又称为瞬态电路分析，本章介绍瞬态电路分析的基础知识，以及只含有一个动态元件的电路，即一阶电路。
当动态电路的工作条件发生变化时，电路中原有的工作状态需要经过一个过程逐步达到另一个新的稳定工作状态，这个过程称为电路的过渡过程或暂态。在电路分析中，把电路结构（如开关）或元件参数（如电源信号）的改变，称为换路。换路意味着电路工作状态的改变。
动态电路中过渡过程的产生，其外因是换路，而内因是电路中电容或电感元件的存在。这两种元件不消耗能量，只存储能量并与外界交换能量。在一定的工作状态下，它们存储一定的能量，当发生换路时，电容或电感中的能量也要随之发生变化，能量的变化必须经过一定的时间才能完成，如果没有这样一个过渡过程，就意味着电容中存储的电场能量、电感中存储的磁场能量要发生跃变。要实现这一点，必须要求它们的能量的变化率（即功率）为无穷大，而这在实际电路中是不可能达到的。
设换路发生在`t=0`时刻，`t=0-`表示换路前的瞬间，这时还没有换路；用 `t=0+`表示换路后的瞬间，此时电路刚刚换路。这时有：
![](images/txt004_1.jpg "pagenumber_ebook=56,pagenumber_book=44")
![](images/txt004_2.jpg "pagenumber_ebook=57,pagenumber_book=45")
以上称为换路定则。`0-`和`0+`在数值上虽然都等于零，但对于动态电路来说，它们有本质的区别。

## 3.2 一阶电路的零状态响应
在电路的零状态下，即初始状态（动态元件初始储能）为零时，仅由外加激励的作用产生的响应为零状态响应。本节讨论在直流激励作用下的零状态响应。

### 3.2.1 RC电路的零状态响应
图3-1（a）所示为`RC`电路，开关闭合前即`t≤0-`时，电路已处于稳态，电容电压`uC(0-)=0`；当`t=0`时，开关闭合，电源将对电容进行充电；当`t=0+`时，`uC(0+)=uC(0-)=0 V`；`t≥0+`时，电容`C`相当于短路。下面分析`t≥0+`时的电容电压`uC`和电流`iC`的变化规律。
![](images/txt004_3.jpg "pagenumber_ebook=57,pagenumber_book=45")
图3-1　`RC`电路及响应曲线
（a）`RC`电路；（b）响应曲线
换路后，根据`KVL`得：
![](images/txt004_4.jpg "pagenumber_ebook=57,pagenumber_book=45")
这是一个以电容电压`uC`为变量的一阶线性非齐次微分方程，由高等数学可知，该微分方程的通解由两部分组成，即该微分方程的一个特解与该微分方程对应的齐次微分方程的通解，可写成
![](images/txt004_5.jpg "pagenumber_ebook=57,pagenumber_book=45")
为了叙述方便，称`Cu`为全解，称`uC′`为特解，称`Cu′`为通解。取电路到达稳态时的解作为特解是最简单的，即
![](images/txt004_6.jpg "pagenumber_ebook=57,pagenumber_book=45")
该微分方程所对应的齐次微分方程的通解为
![](images/txt004_7.jpg "pagenumber_ebook=58,pagenumber_book=46")
该微分方程的全解为`t`
![](images/txt004_8.jpg "pagenumber_ebook=58,pagenumber_book=46")
由电容的伏安关系求出电容电流为
![](images/txt004_9.jpg "pagenumber_ebook=58,pagenumber_book=46")
由此可以得出`RC`电路的响应曲线如图3-1（b）所示。零状态响应也是按指数规律变化，其变化速度仍与`τ`有关。

### 3.2.2 RL电路的零状态响应
图3-2（a）所示为`RL`电路。
![](images/txt004_10.jpg "pagenumber_ebook=58,pagenumber_book=46")
图3-2　`RL`电路及响应曲线
（a）`RL`电路；（b）响应曲线
开关闭合前，即`t≤0-`时，电感无初始储能，电感电流`iL(0-)=0`；当`t=0`时，开关闭合，电感电流随时间增加，最终电路将再次稳定下来，此时电感相当于短路，`iL(∞)=IS`，而`iL(0+)=iL(0-)=0`，所以`t=0+`时电感相当于开路。下面分析`t≥0+`时的电感电流`iL`和电压`uL`的变化规律。
换路后，根据并联电路的分流关系可得：
![](images/txt004_11.jpg "pagenumber_ebook=58,pagenumber_book=46")
![](images/txt004_12.jpg "pagenumber_ebook=59,pagenumber_book=47")
由以上可以看出，电感的电流和电压的变化也与时间常数有关。公式推导和一开始进行的物理分析的结果一致的，也就是最终电感的电流与外加的电流源的电流相等，即`iL(∞)=IS`。那么其零状态响应也可以表示为
![](images/txt004_13.jpg "pagenumber_ebook=59,pagenumber_book=47")
电感电压为
![](images/txt004_14.jpg "pagenumber_ebook=59,pagenumber_book=47")
`RL`电路的响应曲线如图3-2（b）所示。
![](images/txt004_15.jpg "pagenumber_ebook=59,pagenumber_book=47")
时间常数为
![](images/txt004_16.jpg "pagenumber_ebook=60,pagenumber_book=48")
图3-3　例3-1图
![](images/txt004_17.jpg "pagenumber_ebook=60,pagenumber_book=48")
因此电感电流为
![](images/txt004_18.jpg "pagenumber_ebook=60,pagenumber_book=48")
为了计算电压源发出的功率，就要计算其电流。设电流参考方向如图3-3（a）所示，由于
![](images/txt004_19.jpg "pagenumber_ebook=60,pagenumber_book=48")
电阻电流为
![](images/txt004_20.jpg "pagenumber_ebook=60,pagenumber_book=48")
电压源的电流为
![](images/txt004_21.jpg "pagenumber_ebook=60,pagenumber_book=48")
所以电压源发出的功率为
![](images/txt004_22.jpg "pagenumber_ebook=60,pagenumber_book=48")

## 3.3 一阶电路的全响应

### 3.3.1 线性动态电路的全响应
外加电源与储能元件上初始储能共同产生的电路响应，即零状态响应与零输入响应的叠加，称为全响应。本书只研究一阶电路的全响应。

#### 例3-2 如图3-4所示，已知换路前开关闭合，电路处于稳态，`t=0`时进行换路。分析该电路的全响应。
解 换路前电路处于稳态时，`L`相当于短路，在两个电源的共同作用下有：
![](images/txt004_23.jpg "pagenumber_ebook=61,pagenumber_book=49")
图3-4　一阶`RL`电路
![](images/txt004_24.jpg "pagenumber_ebook=61,pagenumber_book=49")
当`t0+`＞时，电压源已不起作用，只有电流源作用于电路，且电感元件的初始储能同时作用于电路。`t=∞`时，电路将再次稳定下来，`L`相当于短路，在电流源的作用下有：
![](images/txt004_25.jpg "pagenumber_ebook=61,pagenumber_book=49")
根据`KVL`列出方程：
![](images/txt004_26.jpg "pagenumber_ebook=61,pagenumber_book=49")
从上式可以看到，第一项与外加激励形式相同，称为强制分量，当`t→∞`时，该分量不随时间的变化而变化，又称为稳态分量。第二项是按指数规律变化且由电路的自身特性决定的，称为自由分量，当`t→∞`时，该分量将衰减为零，又称为暂态分量。所以，全响应按照电路响应形式又可以表示成
![](images/txt004_27.jpg "pagenumber_ebook=61,pagenumber_book=49")
式（3-10）也可以写成
![](images/txt004_28.jpg "pagenumber_ebook=61,pagenumber_book=49")
上式右边第一项是无外加激励且电感电流的初始值`I0`所产生的零输入响应，第二项是电感电流的初始值为零且有外加激励作用产生的零状态响应，由此说明了一阶电路的全响应是零输入响应和零状态响应的叠加，表示为
![](images/txt004_29.jpg "pagenumber_ebook=61,pagenumber_book=49")
根据式（3-10），设`I0＞IS`，电感电流的曲线如图3-5（a）所示。`t＞0`时，电感电流由初始值`I0`慢慢放电，最后稳定在`IS`。
若`I0＜IS`，如图3-5（b）所示，电感电流由初始值`I0`慢慢充电，最后稳定在`IS`，可以说从一个稳态达到另一个稳态。

### 3.3.2 三要素法
本节介绍的三要素法是一种求解一阶电路的简便方法，它可用于求解电路任一变量的零输入响应和直流作用下的零状态响应、全响应，不论它是状态变量还是非状态变量。
![](images/txt004_30.jpg "pagenumber_ebook=62,pagenumber_book=50")
图3-5　不同情况下电感电流的变化曲线
（a）`I0＞IS`；（b）`I0＜IS`
这一方法的背景是：① 一阶电路中的响应是按指数规律变化的，都有它的初始值和稳态值（平衡值），其变化过程唯一地由时间常数决定；② “提前”运用换路定则，不必等状态变量求得后再运用。
考察一下全响应解的特点，以一阶`RL`电路为例。当换路发生在`t = 0`时刻，那么电流的全响应为
![](images/txt004_31.jpg "pagenumber_ebook=62,pagenumber_book=50")
其中`I0`为电感电流的初始值`iL(0+)`；`IS`是`t→∞`时的电流值，`iL(∞)`是稳态值；`τ`为时间常数。
在`RC`或`RL`电路中，不管电路结构和参数如何，换路后，描述电路特性与激励关系的方程为一阶微分方程，其数学模型为
![](images/txt004_32.jpg "pagenumber_ebook=62,pagenumber_book=50")
则对于任意一阶微分方程，当它输入电源为稳定激励（直流）时，任意电压、电流响应的形式可表示为
![](images/txt004_33.jpg "pagenumber_ebook=62,pagenumber_book=50")
将此公式称为三要素公式。其中`y(t)`可以代表电路中的任一电压或电流；`y(0+)`表示该电压或电流的初始值；`y(∞)`表示该电压或电流的稳态值；`τ`表示电路的时间常数。只要知道`y(0+)`、`τ`、`y(∞)`即可，而它们均可不用列微分方程求得。

#### 例3-3 电路如图3-6所示，求`uC(t)`、`iC(t)`、`i1(t)`。
![](images/txt004_34.jpg "pagenumber_ebook=62,pagenumber_book=50")
图3-6　例3-3图
解 `t=0-`时，开关处于断开状态，电容`C`相当于开路。
（1）求初值。
![](images/txt004_35.jpg "pagenumber_ebook=63,pagenumber_book=51")
（2）求终值。`t→∞`时，电路将再次进入稳态。
![](images/txt004_36.jpg "pagenumber_ebook=63,pagenumber_book=51")
（3）求时间常数。把电容`C`开路，并将电压源短路，`R`为从电容向外看的等效电阻：
![](images/txt004_37.jpg "pagenumber_ebook=63,pagenumber_book=51")
代入三要素公式：
![](images/txt004_38.jpg "pagenumber_ebook=63,pagenumber_book=51")
求其他电量：
![](images/txt004_39.jpg "pagenumber_ebook=63,pagenumber_book=51")

# 第二篇 数字电路

# 第4章 数字逻辑基础

## 4.1 数制
数字电路所处理的各种数字信号都是以数码形式给出的。不同的数码既可以用来表示不同数量的大小，又可以用来表示不同的事物或事物的不同状态。用数码表示数量的大小时，一位数码往往不够用，因此经常需要用进位计数制的方法组成多位数码使用。多位数码中每一位的构成方法和从低位到高位的进位规则称为数制。数字电路中使用最多的数制是二进制，其次是在二进制的基础上构成的十六进制和十进制，有时也用到八进制。当两个数码分别表示两个数量时，可以进行数量的加、减、乘、除等运算。这一类运算称为算术运算。

### 4.1.1 几种常见数制
1. 十进制

