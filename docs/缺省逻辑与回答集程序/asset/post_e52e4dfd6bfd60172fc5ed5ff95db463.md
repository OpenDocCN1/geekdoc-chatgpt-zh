

- (1) 包含：$S, A\vdash A$;
- (2) 单调性：若 $S\vdash A$，则 $S, B\vdash A$;
- (3) 分离性(cut)：若 $S\vdash B$，且 $S, B\vdash A$，则 $S\vdash A$。

这里用 $S, A$ 作为 $S \cup \{A\}$ 的缩写，$S$ 是一个有穷集。

用类似的方法可以研究非单调逻辑的元理论性质，这最初是由 Dov Gabbay (多夫·加贝) 建议的，一些著名的结构是由 David Makinson (大卫·马金森) [31]、Kraus (克劳斯)、Lehmann (莱曼)、Magidor (马吉多尔) 和 Satoh (佐藤) 等分别得到。

## 7.4.1 结构性质

非单调逻辑的决定性特征是单调性不再成立，用 $S\vdash_{n} A$ 表示非单调推理：若 $S$ 中所有语句是真的，则正常情形 $A$ 将为真。在非单调逻辑 (NL) 中，一般情况下有 $S\vdash_{n} A$，但 $S, B\not\vdash_{n} A$。

为使 NL 尽可能接近经典的一阶逻辑，可以保留它的一些元理论原则。首先，使 NL 包含一阶逻辑，即若 $S\vdash A$，则 $S\vdash_{n} A$。其次，分离性作为缺省传递性的原则是合适的。最后，尽管放弃单调性原则，但可用一个弱化的形式代替，即谨慎的单调性：若 $S\vdash_{n} B$ 且 $S\vdash_{n} A$，则 $S, B\vdash_{n} A$。

因此，NL 一般至少应具有下述性质：

- (1) 超经典性：若 $S\vdash_{n} A$，则 $S\vdash A$;
- (2) 包含：$S, A\vdash_{n} A$;
- (3) 谨慎单调性：若 $S\vdash_{n} B$ 且 $S\vdash_{n} A$，则 $S, B\vdash_{n} A$;
- (4) 分离：若 $S\vdash_{n} B$ 且 $S, B\vdash_{n} A$，则 $S\vdash_{n} A$。

通常也将性质 (3) 和性质 (4) 并为累积性，即若 $S\vdash_{n} B$，则 $S, B\vdash_{n} A$ 当且仅当 $S\vdash_{n} A$

## 7.4.2 逻辑联结词

前述性质是结构性的，与语句的内部形式无关。那么，命题与一阶语句算子怎样与非单调导出关系相互作用呢？一般有下述规则 (类似于经典逻辑)：

- AND: 若 $S\vdash_{n} A$，且 $S\vdash_{n} B$，则 $S\vdash_{n} A \land B$;
- OR: 若 $S, B\vdash_{n} A$ 且 $S, C\vdash_{n} A$，则 $S, B \lor C\vdash_{n} A$;
- 合理性：若 $S\vdash_{n} A$ 且 $S\not\vdash_{n} \neg B$，则 $S, B\vdash_{n} A$。

注意：AND 可以从分离和超经典性导出。

可以推广这一分析到基于量词的规则。

## 参考文献

- [1] 陆钟万. 面向计算机科学的数理逻辑 [M]. 2 版. 北京: 科学出版社, 2002.
- [2] 王浩. 数理逻辑通俗讲话[M]. 北京: 科学出版社, 1981.
- [3] PAPADIMITRIOU C H. Computational complexity [M]. New Jersey: Addison Wesley, 1994.
- [4] LLOYD J W. Foundations of logic programming [M]. 2nd ed. Berlin: Springer-Verlag, 1987.
- [5] GELFOND M, LIFSCHITZ V. The stable model semantics for logic programming[C]// International Logic Programming Conference and Symposium, Seattle, Washington: MIT Press. 1988: 1070-1080.
- [6] GELFOND M, LIFSCHITZ V. Classical negation in logic programs and disjunctive databases[J]. New Generation Computing, 1991, 9(3): 365-385.
- [7] FERRARIS P. Answer sets for propositional theories[C]// International Conference on Logic Programming and Nonmonotonic Reasoning. Diamante, Italy: Springer-Verlag, 2005: 119-131.
- [8] BARAL C. Knowledge representation, reasoning and declarative problem solving[M]. New York: Cambridge University Press, 2003.
- [9] REITER R. A logic for default reasoning[J]. Artificial Intelligence, 1980, 13 (1-2): 81-132.
- [10] ZHANG M Y. On extension of general default theories[J]. Science in China Series A Mathematics 1993, 36 (10): 1273-1280.
- [11] ZHANG M Y. Some results on default logic[J]. Journal of Computer Science and Technology, 1994, 9 (3): 267-274.
- [12] ZHANG M Y. A New Research into default logic[J]. Information and Computation, 1996, 129 (2): 73-85.
- [13] MCDERMOTT D V, DOYLE J. Non-monotonic logic I[J]. Artificial Intelligence, 1980, 13(1-2): 41-72.
- [14] MCCARTHY J. Circumscription - a form of non-monotonic reasoning[J]. Computation and Intelligence, 1995, 10: 555-566.
- [15] SIMONS P, NIEMELÄ I, SOININEN T. Extending and implementing the stable model semantics[J]. Artificial Intelligence, 2002, 138(1-2): 181-234.
- [16] LEONE N, PFEIFER G, FABER W, et al. The DLV system for knowledge representation and reasoning[J]. ACM Transactions on Computational Logic, 2006, 7(3): 499-562.
- [17] GEBSER M, KAUFMANN B, SCHAUB T. Conflict-driven answer set solving: from theory to practice[J]. Artificial Intelligence, 2012, 187-188: 52-89.
- [18] WANG Y S, ZHANG M Y, YOU J H. Logic programs, compatibility and forward chaining construction[J]. Journal of Computer Science and Technology, 2009, 24(6): 1125-1137.
- [19] YANG B, ZHANG Y, ZHANG M Y, et al. Splitting computation of answer set program and its application on e-service[J]. International Journal of Computational Intelligence Systems, 2011, 4(5): 977-990.
- [20] LIN F Z, ZHAO Y T. ASSAT: computing answer sets of a logic program by SAT solvers[J]. Artificial Intelligence, 2004, 157 (1-2): 115-137.
- [21] ZHANG M Y, ZHANG Y, LIN F Z. A characterization of answer sets for logic programs[J]. Science in China (Series F: Information Sciences), 2007, 50 (1): 46-62.
- [22] CHEN Y, LIN F Z, WANG Y S, et al. First-order loop formulas for normal logic programs[C]// Tenth International Conference on Principles of Knowledge Representation and Reasoning. Lake District of the United Kingdom: AAAI Press. 2006: 298-307.
- [23] HUANG Y, WANG Y S, YOU J H, et al. Learning disjunctive logic programs from nondeterministic interpretation transitions[J]. New Generation Computing, 2021, 39(1): 273-301.
- [24] WANG Y S, ZHANG Y, ZHOU Y, et al. Knowledge forgetting in answer set programming[J]. Journal of Artificial Intelligence Research, 2014, 50: 31-70.
- [25] EITER T, FINK M, TOMPITS H, et al. On eliminating disjunctions in stable logic programming[C]// Ninth International Conference on Principles of Knowledge Representation and Reasoning. Whistler, British Columbia, Canada; AAAI Press, 2004: 447-458.
- [26] GONAALVES R, KNORR M, LEITE J. Forgetting in answer set programming – a Survey[J]. Theory and Practice of Logic Programming, 2021: 1-43.
- [27] LUKASZEWICZ W. Considerations on default logic: an alternative approach[J]. Computational Intelligence, 1988, 4: 1-16.
- [28] GOTTLOB G, ZHANG M Y. Cumulative default logic: finite characterization, algorithms, and complexity[J]. Artificial Intelligence, 1994, 69 (1-2): 329-345.
- [29] 张明文, 张颖. 缺省逻辑的累积性变种的扩张特征[J]. 计算机学报, 1998(2): 119-126.
- [30] ZHANG M Y, ZHANG Y. Characterizations and algorithms of extensions for CADL and QDL[J]. Journal of Computer Science and Technology, 1999(2): 140-145.
- [31] MAKINSON D. Bridges from Classical to nonmonotonic logic[M]. London: King's College, 2005.

## 缺省逻辑与回答集程序

