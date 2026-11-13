+   (1) 包含：$S, A\vdash A$;
+   (2) 单调性：若 $S\vdash A$，则 $S, B\vdash A$;
+   (3) 分离性(cut)：若 $S\vdash B$，且 $S, B\vdash A$，则 $S\vdash A$。

这里用 $S, A$ 作为 $S \cup \{A\}$ 的缩写，$S$ 是一个有穷集。

用类似的方法可以研究非单调逻辑的元理论性质，这最初是由 Dov Gabbay (多夫·加贝) 建议的，一些著名的结构是由 David Makinson (大卫·马金森) [31]、Kraus (克劳斯)、Lehmann (莱曼)、Magidok (马吉多克) 和 Satoh (佐藤) 等分别得到。

# 7.4.1 结构性质

非单调逻辑的决定性特征是单调性不再成立，用 $S\vdash_{n} A$ 表示非单调推理：若 $S$ 中所有语句是真的，则正常情形 $A$ 将为真。在非单调逻辑 (NL) 中，一般情况下有 $S\vdash_{n} A$，但 $S, B\not\vdash_{n} A$。

为使 NL 尽可能接近经典的一阶逻辑，可以保留它的一些元理论原则。首先，使 NL 包含一阶逻辑，即若 $S\vdash A$，则 $S\vdash_{n} A$。其次，分离性作为缺省传递性的原则是合适的。最后，尽管放弃单调性原则，但可用一个弱化的形式代替，即谨慎的单调性：若 $S\vdash_{n} B$ 且 $S\vdash_{n} A$，则 $S, B\vdash_{n} A$。

因此，NL 一般至少应具有下述性质：

+   (1) 超经典性：若 $S\vdash_{n} A$，则 $S\vdash A$;
+   (2) 包含：$S, A\vdash_{n} A$;
+   (3) 谨慎单调性：若 $S\vdash_{n} B$ 且 $S\vdash_{n} A$，则 $S, B\vdash_{n} A$;
+   (4) 分离：若 $S\vdash_{n} B$ 且 $S, B\vdash_{n} A$，则 $S\vdash_{n} A$。

通常也将性质 (3) 和性质 (4) 并为累积性，即若 $S\vdash_{n} B$，则 $S, B\vdash_{n} A$ 当且仅当 $S\vdash_{n} A$

# 7.4.2 逻辑联结词

前述性质是结构性的，与语句的内部形式无关。那么，命题与一阶语句算子怎样与非单调导出关系相互作用呢？一般有下述规则 (类似于经典逻辑)：

+   AND: 若 $S\vdash_{n} A$，且 $S\vdash_{n} B$，则 $S\vdash_{n} A \land B$;
+   OR: 若 $S, B\vdash_{n} A$ 且 $S, C\vdash_{n} A$，则 $S, B \lor C\vdash_{n} A$;
+   合理性：若 $S\vdash_{n} A$ 且 $S\not\vdash_{n} \neg B$，则 $S, B\vdash_{n} A$。

注意：AND 可以从分离和超经典性导出。

可以拓广这一分析到基于量词的规则。