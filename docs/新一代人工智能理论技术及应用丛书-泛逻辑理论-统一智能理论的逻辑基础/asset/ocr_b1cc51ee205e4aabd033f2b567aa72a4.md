这时 1 量子比特位可同时表示 2 种状态；2 量子比特位同时表示 4 种状态；3 量子比特位同时表示 8 种状态；以此类推，n 量子比特位可同时表示 2^n 种状态。如表示 0 到 15 的所有 16 个整数需要 4×16 个比特位，而量子比特位只需要 4 位。因此表示相同信息时，量子比特位与传统比特位数量比为 n : (n×2^n) = 1 : 2^n，可见量子比特位较传统比特位对多状态信息表示的优势。一般情况下量子态是在 |0⟩ 和 |1⟩ 状态的线性组合基础上表示的，如式(15-1)所示：

\(|\mu\rangle = \alpha_0|0\rangle + \alpha_1|1\rangle\) (15-1)

其中，\(\alpha_0\) 和 \(\alpha_1\) 是任意复数，分别代表两种状态的概率幅，且 \(\alpha_0^2 + \alpha_1^2 = 1\)(塌缩概率)。式(15-1)给出了单量子态的表示方法，更为详尽的说明参见布洛赫球表示法，这里不做详述。式(15-1)由三个要素组成，\(|\mu\rangle\) 代表量子态或量子叠加态；\(\alpha_0\) 和 \(\alpha_1\) 分别代表 \(|0\rangle\) 和 \(|1\rangle\) 出现的概率幅；\(|0\rangle\) 和 \(|1\rangle\) 代表量子的极化态。那么多量子态叠加后的系统量子态表达也可从这三个方面确定。首先 2 量子态 \(|\mu_1\rangle\) 和 \(|\mu_2\rangle\) 表示为叠加态 \(|\mu_1 \mu_2\rangle\)，则 \(|\mu_1 \mu_2\rangle = \alpha_{00}|00\rangle + \alpha_{01}|01\rangle + \alpha_{10}|10\rangle + \alpha_{11}|11\rangle\)；\(|00\rangle\) 表示 2 量子态均极化为 0 的状态，\(|01\rangle\) 表示 \(|\mu_1\rangle\) 极化为 0 且 \(|\mu_2\rangle\) 极化为 1 的状态，\(|10\rangle\) 表示 \(|\mu_1\rangle\) 极化为 1 且 \(|\mu_2\rangle\) 极化为 2 的状态，\(|11\rangle\) 表示两量子态均极化为 1 的状态；\(\alpha_{00}\)、\(\alpha_{01}\)、\(\alpha_{10}\) 和 \(\alpha_{11}\) 分别为上述 4 个状态的概率幅，4 种状态的出现概率分别为 \(\alpha_{00}^2\)、\(\alpha_{01}^2\)、\(\alpha_{10}^2\) 和 \(\alpha_{11}^2\)，且满足 \(\alpha_{00}^2 + \alpha_{01}^2 + \alpha_{10}^2 + \alpha_{11}^2 = 1\)。

3 量子态 \(|\mu_1\rangle\)、\(|\mu_2\rangle\) 和 \(|\mu_3\rangle\) 的叠加表示为 \(|\mu_1 \mu_2 \mu_3\rangle\)，则 \(|\mu_1 \mu_2 \mu_3\rangle = \alpha_{000}|000\rangle + \alpha_{010}|010\rangle + \alpha_{100}|100\rangle + \alpha_{110}|110\rangle + \alpha_{001}|001\rangle + \alpha_{011}|011\rangle + \alpha_{101}|101\rangle + \alpha_{111}|111\rangle\)；同理 \(|000\rangle\) 表示 3 量子态均极化为 0 的状态，\(|010\rangle\) 表示 \(|\mu_1\rangle\) 极化为 0 且 \(|\mu_2\rangle\) 极化为 1 且 \(|\mu_3\rangle\) 极化为 0 的状态；以此类推，\(\alpha_{000}\)、\(\alpha_{010}\)、\(\alpha_{100}\)、\(\alpha_{110}\)、\(\alpha_{001}\)、\(\alpha_{011}\)、\(\alpha_{101}\) 和 \(\alpha_{111}\) 分别为上述 8 个状态的概率幅，8 种状态的出现概率分别为 \(\alpha_{000}^2\)、\(\alpha_{010}^2\)、\(\alpha_{100}^2\)、\(\alpha_{110}^2\)、\(\alpha_{001}^2\)、\(\alpha_{011}^2\)、\(\alpha_{101}^2\) 和 \(\alpha_{111}^2\)，且满足

\(\alpha_{000}^2 + \alpha_{010}^2 + \alpha_{100}^2 + \alpha_{110}^2 + \alpha_{001}^2 + \alpha_{011}^2 + \alpha_{101}^2 + \alpha_{111}^2 = 1\)

同理 4 状态 \(|\mu_1\rangle\)、\(|\mu_2\rangle\)、\(|\mu_3\rangle\) 和 \(|\mu_4\rangle\) 的叠加表示为 \(|\mu_1 \mu_2 \mu_3 \mu_4\rangle\)，则

\(|\mu_1 \mu_2 \mu_3 \mu_4\rangle = \alpha_{0000}|0000\rangle + \alpha_{0100}|0100\rangle + \alpha_{1000}|1000\rangle + \alpha_{1100}|1100\rangle\)
\(+ \alpha_{0010}|0010\rangle + \alpha_{0110}|0110\rangle + \alpha_{1010}|1010\rangle + \alpha_{1110}|1110\rangle\)
\(+ \alpha_{0001}|0001\rangle + \alpha_{0101}|0101\rangle + \alpha_{1001}|1001\rangle + \alpha_{1101}|1101\rangle\)
\(+ \alpha_{0011}|0011\rangle + \alpha_{0111}|0111\rangle + \alpha_{1011}|1011\rangle + \alpha_{1111}|1111\rangle\)

\(|0000\rangle\) 表示 4 量子态均极化为 0 的状态；\(|0100\rangle\) 表示 \(|\mu_1\rangle\) 极化为 0 且 \(|\mu_2\rangle\) 极化为 1 且 \(|\mu_3\rangle\) 极化为 0 且 \(|\mu_4\rangle\) 极化为 0 的状态；依次类推，\(\alpha_{0000} \sim \alpha_{1111}\) 分别为上述