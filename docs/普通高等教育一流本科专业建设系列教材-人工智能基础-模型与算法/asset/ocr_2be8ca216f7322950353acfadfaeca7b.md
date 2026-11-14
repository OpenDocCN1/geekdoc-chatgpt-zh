幂等律：

\( P \lor P \Leftrightarrow P \\
P \land P \Leftrightarrow P \)

交换律：

\( P \lor Q \Leftrightarrow Q \lor P \\
P \land Q \Leftrightarrow Q \land P \)

结合律：

\( (P \lor Q) \lor R \Leftrightarrow P \lor (Q \lor P) \\
(P \land Q) \land R \Leftrightarrow P \land (Q \land P) \)

吸收律：

\( P \lor (P \land Q) \Leftrightarrow P \\
P \land (P \lor Q) \Leftrightarrow P \)

De-Morgan 定律：

\( \neg(P \lor Q) \Leftrightarrow \neg P \land \neg Q \\
\neg(P \land Q) \Leftrightarrow \neg P \lor \neg Q \)

有关蕴涵的常用定理如下。

假言推理：

\( P \land (P \to Q) \Rightarrow Q \)

假言三段论：

\( (P \to Q) \land (Q \to R) \Rightarrow P \to R \)

化简式：

\( P \land Q \Rightarrow P \\
P \land Q \Rightarrow Q \)

附加式：

\( P \Rightarrow P \lor Q \\
Q \Rightarrow P \lor Q \)

其他：

\( P \to Q \Rightarrow \neg P \lor Q \\
\neg P \Rightarrow P \to Q \\
Q \Rightarrow P \to Q \)

■例 2.1 试用命题逻辑表示如下知识。

1) 若明天不是雨夹雪，则去学校。
2) 若明天不下雨并且不下雪，则去学校。
3) 若明天下雨或下雪，则不去学校。
4) 明天，我将雨雪无阻，一定要去学校。

解 设 \( P \) 表示明天下雨，\( Q \) 表示明天下雪，\( R \) 表示去学校。

1) 若明天不是雨夹雪，则去学校：
   \(\neg(P \land Q) \to R\)

2) 若明天不下雨并且不下雪，则去学校：
   \(\neg P \land \neg Q \to R\)