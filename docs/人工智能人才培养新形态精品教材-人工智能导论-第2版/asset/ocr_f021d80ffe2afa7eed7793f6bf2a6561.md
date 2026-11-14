x \rightarrow y

又如，“自然数都是大于零的整数” 可表示为

(\forall x)[N(x) \rightarrow GZ(x) \land l(x)]

其中，N(x) 表示 “x 是自然数”，l(x) 表示 “x 是整数”，GZ(x) 表示 “x 是大于零的数”。

# 4. 一阶谓词逻辑表示

下面介绍一个简单的一阶谓词逻辑表示的例子。

# 【例 7.1】机器人搬弄积木块问题。

设在一个房间里，有一个机器人 ROBOT、一个壁橱 ALCove、一个积木块 BOX、两个桌子 A 和 B。机器人可把积木块 BOX 从一种状态变换成另一种状态。

+   定义以下常量。
+   机器人：ROBOT。
+   壁橱：ALCOVE。
+   积木块：BOX。
+   桌子：A。
+   桌子：B。

+   定义以下谓词。
+   TABLE(x): x 是桌子。
+   EMPTYHANDED(x): x 双手是空的。
+   AT(x,y): x 在 Y 的旁边。
+   HOLDS(y, ω): Y 拿着 ω。
+   ON(ω,x): ω 在 x 上。

# 初始状态用谓词公式表示：

AT(ROBOT, ALCove) \land EMPTYHANDED(ROBOT) \land ON(BOX, A) \land TABLE(A) \land TABLE(B)

# 目标状态用谓词公式表示：

AT(ROBOT, ALCove) \land EMPTYHANDED(ROBOT) \land ON(BOX, B) \land TABLE(A) \land TABLE(B)

问题是依机器人可进行的操作，实现一个由初始状态到目标状态的机器人操作过程。

机器人的每个操作的结果所引起的状态变化，可用对原状态的增添表和删除表来表示。例如，机器人由初始状态把 BOX 从 A 桌上移到 B 桌上，然后拐回到壁橱，这时同初始状态相比有如下变化。

+   增添表：
+   ON(BOX,B)
+   删除表：
+   ON(BOX,A)

又如，机器人由初始状态走近 A 桌，然后拿起 BOX。这时同初始状态相比有如下变化。

+   增添表：
+   AT(ROBOT,A) \land HOLDS(ROBOT,BOX)
+   删除表：
+   AT(ROBOT,ALCOVE) \land EMPTYHANDED(ROBOT) \land ON(BOX,A)

进一步说，机器人的每个操作还必须有先决条件。如机器人拿起 A 桌上的 BOX 这个操作的先决条件是

ON(BOX,A) \land AT(ROBOT,A) \land EMPTYHANDED(ROBOT)

从初始状态出发，实现机器人的每个操作都会验证先决条件，并建立相应的增添表和删除表，从而逐步达到目标状态。