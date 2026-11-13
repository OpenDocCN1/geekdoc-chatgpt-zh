所提出的 ABEM 算法将角度信息作为训练和预测的输入特征，是一种直接对 AEP 进行建模的方法。一个具有 \( W_{\text{tra}} \) 个单元的训练数据集可以表示为

```
\( D_{\text{tra}} = \{(\boldsymbol{u}, \boldsymbol{y}_{\text{tra}, w}) | w = 1, \cdots, W_{\text{tra}} \} \) (5.9)
```

式中，输入数据的维度为 \( W_{\text{tra}} \times X_{\text{tra}} \)，可表示为

```
\( \boldsymbol{u}_w = [\boldsymbol{l}_w \ f_{\text{obj}, w} \ u_{\text{obj}, w} \ v_{\text{obj}, w}]^{\text{T}} \) (5.10)
```

输出数据为 \( \boldsymbol{y}_{\text{tra}, w} = [\text{Mag}\{g_{\text{AEP}}\} \ w \ \text{Pha}\{g_{\text{AEP}}\} \ w]\)；\( f_{\text{obj}, w} \)、\( u_{\text{obj}, w} \) 及 \( v_{\text{obj}, w} \) 分别代表所关注的频率及角度点；训练数据的列数为 \( X_{\text{tra}} = KM + 5 \)，而行数为 \( W_{\text{tra}} = W_{\text{ABE}} W_f W_u W_v \)，其中 \( W_{\text{ABE}} \)、\( W_f \)、\( W_u \) 和 \( W_v \) 分别代表训练集中的 ABE 的数量、频率点和角度点的数量。如之前所述，当 \( W_{\text{ABE}} \) 随着设计过程增长时，\( W_{\text{tra}} \) 亦会随之增长；更重要的是，处理二维面阵的设计问题时，所需要处理的角度点数会急剧增长。考虑一个典型的二维面阵设计问题，假设算法需要 5 次迭代以达到设计目标，每次迭代需要处理 20 个阵元的全波仿真数据，考虑 1 个设计频点和上半球面内的间隔为 \( 1^\circ \) 的角度点，训练数据集将最终会增长至 \( W_{\text{tra}} = 3240000 \) 维，从而很难在需要快速迭代的工程问题中进行学习及预测。

文献 [90] 提出了一种针对固定阵元位置的线阵综合问题的 AEP 展开的方法。在 HKDT 方法中，将上述方法扩展至二维面阵的应用中，并创造性地将其拟合得到的特征值用来代表不同输入参数下的 ABE 的特征，相较于前述的通过角度特征描述的 ABEM，取得相当大的计算复杂度的削减。在 HKDT 的平面虚拟子阵拟合方法中，将给定 ABE 的 AEP 拟合为一个等间距的平面虚拟子阵的辐射：

```
\( g_n(u, v) \approx g_{\text{ave}}(u, v) \sum_{q=-\frac{Q-1}{2}}^{\frac{Q-1}{2}} \sum_{p=-\frac{P-1}{2}}^{\frac{P-1}{2}} c_{n,p,q} e^{\mathrm{j}\beta(gdu + pdv)} \) (5.11)
```

式中，\( c_{n,p,q} \) 代表该平面虚拟子阵在第 \( p \) 行第 \( q \) 列处的虚拟阵元 \( n \) 的激励；\( d \) 代表该平面虚拟子阵的阵元间距；\( g_{\text{ave}}(u, v) \) 是实际阵列中阵元 AEP 的平均值。平面虚拟子阵的激励 \( c_{n,p,q} \) 可表示为

```
\( \min_{\boldsymbol{c}_n} ||\boldsymbol{G} \boldsymbol{E}_n \boldsymbol{c}_n - \boldsymbol{g}_n||_2^2 \) (5.12)
```

式中，

```
\( \boldsymbol{G} = \begin{bmatrix} g_{\text{ave}}(u_1, v_1) & & O \\ & g_{\text{ave}}(u_1, v_2) & \\ & & \ddots \\ O & & g_{\text{ave}}(u_k, v_s) \end{bmatrix} \) (5.13)
```