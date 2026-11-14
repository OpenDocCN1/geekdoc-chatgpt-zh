\( R_1 = (A_1 \text{ AND } B_1) \rightarrow C_1 = \overline{R}_{A_4 \times B_1}^T \wedge C_1 = \begin{bmatrix} 1.0 \\ 0.6 \\ 0.2 \\ 0.5 \\ 0.2 \\ 0 \\ 0 \\ 0 \end{bmatrix} \wedge [1.0 \quad 0.4 \quad 0.0] = \begin{bmatrix} 1.0 & 0.4 & 0 \\ 0.6 & 0.4 & 0 \\ 0.2 & 0.2 & 0 \\ 0.5 & 0.4 & 0 \\ 0.5 & 0.4 & 0 \\ 0.2 & 0.2 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \end{bmatrix} \)

同理可得

\( R_1 = \begin{bmatrix} 0 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0 & 0 \\ 0 & 0.2 & 0.2 \\ 0 & 0.4 & 0.5 \\ 0 & 0.4 & 0.5 \\ 0 & 0.2 & 0.2 \\ 0 & 0.4 & 0.6 \\ 0 & 0.4 & 1.0 \end{bmatrix} \)

+   2) 计算模糊熵关系 R:

\( R = R_1 \cup R_2 = \begin{bmatrix} 1.0 & 0.4 & 0 \\ 0.6 & 0.4 & 0 \\ 0.2 & 0.2 & 0 \\ 0.5 & 0.4 & 0.2 \\ 0.5 & 0.4 & 0.5 \\ 0.2 & 0.4 & 0.5 \\ 0 & 0.2 & 0.2 \\ 0 & 0.4 & 0.6 \\ 0 & 0.4 & 1.0 \end{bmatrix} \)

+   3) 计算输入量的模糊集合:

\( A' \text{ AND } B' = A' \times B' = A'^T \wedge B' = \begin{bmatrix} 0.5 \\ 1.0 \\ 0.5 \end{bmatrix} \wedge [0.6 \quad 1.0 \quad 0.6] = \begin{bmatrix} 0.5 & 0.5 & 0.5 \\ 0.6 & 1.0 & 0.6 \\ 0.5 & 0.5 & 0.5 \end{bmatrix} \),
\( \overline{R}_{A' \times B'} = [0.5 \quad 0.5 \quad 0.5 \quad 0.6 \quad 1.0 \quad 0.6 \quad 0.5 \quad 0.5 \quad 0.5] \),
\( C' = (A' \text{ AND } B') \circ R = \overline{R}_{A' \times B'} \circ R \)