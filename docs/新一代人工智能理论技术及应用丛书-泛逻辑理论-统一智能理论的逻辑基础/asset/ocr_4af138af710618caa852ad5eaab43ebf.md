本定理表明 Zadeh 算子是唯一的连续的非阿基米德型 T 范数。

# 定理 7.2.4(一级补余律) 如果 N(x, k) 是 N 范数，则弱 T 范数 T(x, N(x, k))≤k。

证明 由于

x≤k 时，N(x, k)≥k；x≥k 时，N(x, k)≤k

而 T(x, y)≤min(x, y)，所以 T(x, N(x, k))≤k。

# 定理 7.2.5 如果 x≤x', y≤y'，则弱 T 范数 T(x, y)≤T(x', y')。

证明 由条件 T2 知，T(x, y)≤T(x', y)≤T(x', y')，所以本定理成立。

# 推论 7.2.5 如果 x≤z, y≤z，则弱 T 范数 T(x, y)≤z。

# 定理 7.2.6 弱 T 范数 T(xz, y)≤min(T(x, y), T(z, y))。

证明 因为 xz≤min(x, z)，所以 T(xz, y)≤T(x, y)，且 T(xz, y)≤T(z, y)，即 T(xz, y)≤min(T(x, y), T(z, y))成立。

# 推论 7.2.6 弱 T 范数 T(x'n, y)≤T(x, y)，n≥1。

# 定理 7.2.7 如果 x+x'≤1，则弱 T 范数 T(x+x', y)≥max(T(x, y), T(x', y))。

证明 因为 x+x'≥max(x, x')，所以 T(x+x', y)≥T(x, y)，且 T(x+x', y)≥T(x', y)，即 T(x+x', y)≥max(T(x, y), T(x', y))成立。

上述弱 T 范数的性质全部适用于 T 范数。

# 推论 7.2.7 T 范数可以推广到多元运算。如果 T(x, y)是 T 范数，则 T(T(x, y), z)=T(x, T(y, z))=T(x, y, z)。

# 定理 7.2.8 T(x, y)=min(x, y)是最大的 T 范数。

证明 由定理 7.2.2 知，弱 T 范数 T(x, y)≤min(x, y)，即 T(x, y)=min(x, y)是最大的弱 T 范数。而 min(x, min(y, z))=min(min(x, y), z)=min(x, y, z)，满足 T 范数的结合律 T4。

所以 T(x, y)=min(x, y)是最大的 T 范数。

# 定理 7.2.9 T(x, y)=ite {min(x, y)|max(x, y)=1; 0}是最小的 T 范数。

证明 1)由于 T(0, y)=0, T(1, y)=y，满足 T 范数的边界条件 T1； 2) T(x, y)关于 x、y 单调增，满足 T 范数的单调性 T2； 3) T(x, T(y, z))=T(T(x, y), z)=T(x, y, z)，满足 T 范数的结合律 T4； 4) T(x, y)=T(y, x)，满足 T 范数的交换律 T5。

所以 T(x, y)是 T 范数。而任何 T 范数 T'(x, y)都满足 T'(x, y)≥0, T'(1, y)=y, T(x, y)=ite{min(x, y)|max(x, y)=1; 0}是其中的最小者，所以本定律成立。

# 7.2.2 S 范数的主要性质

# 定理 7.2.10(封闭性) 弱 S 范数 S(x, y)∈[0, 1]。

证明 由条件 S1 和 S2 知，0≤S(0, 0)≤S(x, y)≤S(1, 1)≤1，即 S(x, y)∈[0, 1]。