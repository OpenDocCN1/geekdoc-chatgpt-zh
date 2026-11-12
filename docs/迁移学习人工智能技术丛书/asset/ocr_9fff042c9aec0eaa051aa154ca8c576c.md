请注意，上述特征增强方法将假设分解为三个子假设，即 \( h=\langle h_c, h_s, h_t \rangle \)，这相当于学习两个特定域的假设 \( w_i=h_c+h_s \) 和 \( w_i=h_c+h_t \)。通过假设 \( w_i \) 和 \( w_i \) 对每个无标签目标域实例 \( x_i \) 达成一致，该方法可以自然地扩展到半监督学习：

\( w_i \cdot x_i \approx w_i \cdot x_i \Leftrightarrow \langle h_t, h_i \rangle \cdot \langle o, x_i \rangle - x_i) \approx 0 \quad (3.18) \)

通过这种方式，可以构建无标签数据的特征映射：

\( \Phi^*(x) = \langle 0, x, -x \rangle \)

在此之后，任何半监督学习分类器都可以应用于为源域有标签数据、目标域有标签数据和目标域无标签数据定义的特征映射。