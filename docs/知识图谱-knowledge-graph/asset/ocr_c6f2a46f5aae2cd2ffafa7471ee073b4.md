这种三元组的形式组成的知识图谱结构，令 ConceptNet 完全适用于之前在三元组事实知识图谱上开发的推理方法，因此在 ConceptNet 中，常识的推理与正常的知识图谱推理没有什么不同。例如我们可以从以上的三元组中总结出：“兔子惧怕老虎，又因为老虎喜欢吃肉，因此老虎会吃兔子。”

另一个著名的常识库是 Cyc^[270]^，Cyc 是一个致力于将各个领域本体及常识知识进行集成，并在此基础上实现知识推理的人工智能项目。Cyc 目前包括超过 630000 个概念，38000 种关系的类型，超过 700 万条常识性的断言。Cyc 有着自己复杂的语法定义，包括对概念、谓词甚至逻辑的定义，Cyc 的表示方法被称为 CycL 语言，是一种以一阶逻辑为基础，伴有部分高阶逻辑的语言，类似于 Lisp 语言。Cyc 中概念包括个体、集合和函数，常以 “#$” 为开头，例如 #$MapleTree 和 #$massOfObject 等，而变量以 “?” 开头，如 ?OBJ 等。谓词也具有类似的表示，例如 #$isa、#$likesAsFriend、#$GovernmentOf 等。Cyc 表示常识的方法是用括号表示一条常识，并且谓词排在最前边，例如：

(#$isa #$EMRG #$Color)    EMR G 是一种颜色
(#$genls #$Tree-ThePlant #$Plant)    所有的树都是植物
(#$isa #$Obama #$USPresident)    奥巴马是美国总统

为了使用 Cyc 进行推理，Cyc 定义了自己的逻辑符号，包括 #$not, #$and, #$or 以及 #$implies。这样就可以形成一般的命题，如下面的例子：

```
(#$not
    (#$colorOfObject #$FredsBike #$RedColor)
)

(#$implies
    (#$and
    (#$isa ?OBJ ?SUBSET)
    (#$genls ?SUBSET ?SUPERSET))
    (#$isa ?OBJ ?SUPERSET)
)
```

前一个式子表示 #$FredsBike 的颜色不是红色，而后一个式子表示如果 ?OBJ 属于 ?SUBSET 并且 ?SUBSET 是 ?SUPERSET 的子集，则 ?OBJ 属于 ?SUPERSET。因此在 Cyc 这种精确的逻辑表示下，可以进行准确的逻辑推理。