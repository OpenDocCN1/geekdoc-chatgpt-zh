归纳 设对某个 \( j<m, r \in \Pi' \) 是用于从 \( \Pi' \) 构造 \( \Pi'^{j+1} \) 的规则，证明 \( r \in \Pi'^{j+1} \)。设若不然，则存在 \( \Pi' \) 中包含于 \( S \) 的极大正环 \( Z \)，使得 \( r \in R^+(Z, \Pi') \)。设 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p_k\}, Z' \) 是 \( \Pi'^m \) 中用于构造 \( \Pi'^{m+1} \) 的极大环，则 \( p_k \in Z \cup Z' \)。因为 \( \Pi'^m \subseteq \Pi' \)，故 \( Z \cup Z' \) 也是 \( \Pi' \) 中的正环。依 \( Z \) 的极大性，\( Z' \subseteq Z \)。因为 \( r \) 是构造 \( \Pi'^{j+1} \) 时从 \( \Pi' \) 中被删除的，所以 \( R^+(Z, \Pi') \) 中以 \( p_k \) 为头的所有规则也被删除。因此，\( R^+(Z', \Pi'^m) \) 中没有满足 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p_k\} \) 的规则 \( r \)，这与构造 \( \Pi'^{m+1} \) 时规则 \( r \) 的选择矛盾。

其次，归纳地证明：对每一 \( p \in S, \Pi'^m (0 \leqslant m \leqslant n) \) 中的规则 \( r \)，使得 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p\} \)，而 \( S \) 是 \( \Pi'^m \) 支承的。

基始 如前所证，因为 \( S \) 是 \( \text{Comp}(\Pi) \) 的模型，依 \( \Pi^0 \) 的定义可知，\( S \) 是 \( \Pi^0 \) 支承的。

归纳 设 \( S \) 是 \( \Pi'^m \) 支承的，\( \Pi'^m \) 中没有包含于 \( S \) 的正环，\( \Pi'^{m+1} = \Pi'^m \)，\( \Pi'^{m+1} \) 是 \( \Pi \) 支承的。设 \( \Pi'^m \) 中有包含于 \( S \) 的正环，因为 \( \Pi'^{m+1} \) 是从 \( \Pi'^m \) 中删除某些满足 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p_j\} \) 的规则 \( r \) 得到的，因此对任意不同于 \( p_j \) 的 \( p \in S \)，依归纳假设，\( \Pi'^m \) 中有一规则使得 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p\} \)。由 \( \Pi'^{m+1} \) 的构造易知，此规则也在 \( \Pi'^{m+1} \) 中。如前所证，用于从 \( \Pi'^m \) 构造 \( \Pi'^{m+1} \) 的满足 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p_j\} \) 的规则 \( r \) 在 \( \Pi'^{m+1} \) 中没有从 \( \Pi'^m \) 中被删除，故 \( r \in \Pi'^{m+1} \)。

至此，我们证明了，任一 \( m: 0 \leqslant m \leqslant n, S \in \text{SM}(\text{Head}(\Pi'^m)) \)。所以 \( S \in \text{SM}(\text{Head}(\Pi'^n)) \)，即 \( S \) 是 \( \Pi'^n \) 支承的。

最后，施归纳法证明：对 \( m: 0 \leqslant m \leqslant n, S \) 封闭于 \( \Pi'^m \)。

基始 如前所证，\( \Pi^0 = \text{GR}(S, \Pi) \) 且 \( \text{Head}(\Pi^0) = S \)。因此，\( S \) 封闭于 \( \Pi^0 \)。

归纳 对 \( m < n \)，设 \( S \) 封闭于 \( \Pi'^m \)。因为对任一从 \( \Pi'^m \) 中删除的满足 \( \mathbf{GAtom}_{\text{Head}(r)} \cap S = \{p_j\} \) 的规则 \( r \)，存在用于从 \( \Pi'^m \) 构造 \( \Pi'^{m+1} \) 的规则 \( r \)，使得 \( p \) 不同于 \( p_j \) 且 \( p \in \text{Head}(r) \)，如前所证，\( r \) 仍然在 \( \Pi'^m \) 中，故 \( \text{Head}(\Pi'^{m+1}) = \text{Head}(\Pi'^m) \)。依归纳假设和 \( \Pi'^{m+1} \subseteq \Pi'^m \)，因为 \( S \) 封闭于 \( \Pi'^m \)，所以，\( S \) 封闭于 \( \Pi'^{m+1} \)。

上述证明表明 \( S \) 封闭于 \( \Pi'^n \) 且 \( \Pi'^n \) 支承的。根据定理 5.2.5，\( S \) 是 \( \text{Comp}(\Pi'^n) \) 的模型。因为 \( \Pi'^n \) 是有穷的且没有正环，故它是紧凑的。由定理 5.2.6，\( S \) 是 \( \Pi'^n \) 的回答集。

依 \( \Pi^0 \) 和 \( \Pi'^m (m \geqslant 1) \) 的定义易知，对任意 \( r \in \text{GR}(S, \Pi'^n), \text{Pos}(r) \subseteq S \) 且 \( \text{Neg}(r) \cap S = \varnothing \)。因为 \( S \) 是 \( \text{Comp}(\Pi'^n) \) 的模型，根据定理 5.2.5, \( S \) 封闭于 \( \Pi'^n \)，故 \( S \in \text{SM}(\text{Head}(r)) \) 且 \( r \in \Pi'^n \)。这表明 \( \text{GR}(S, \Pi'^n) = \Pi'^n \)。因此，\( \Pi'^n = \text{GR}(S, \Pi'^n) \) 且 \( S \in \text{SM}(\text{Head}(\Pi'^n)) \)。因为 \( S \) 是 \( \Pi'^n \) 的回答集，根据定理 5.2.3（证明的“仅当”部分），\( \text{GR}(S, \Pi'^n) = (\text{GR}(S, \Pi'^n), S) \in \mathcal{A}(\text{GR}(S, \Pi'^n)) \)。因此，\( (\Pi'^n, S) \in \mathcal{A}(\Pi'^n) \)。

因为 \( S \) 是 \( \text{Comp}(\Pi'^n) \) 的模型，根据定理 5.2.5, \( S \) 是 \( \Pi \) 支承的且封闭于 \( \Pi \)。

综上所述，\( \Pi'^n \subseteq \Pi, \Pi'^n \) 是紧凑的，\( (\Pi'^n, S) \in \mathcal{A}(\Pi'^n) \) 且 \( S \in \text{SM}(\text{Head}(\Pi'^n)) \)。