对 im:message:send_to_all 这条消息权限的快照做了一次梳理，发现整体结构高度可预期，也藏着明显的规律。

同一权限键在不同记录里反复出现，主体部分都是成串的 read_write，从开头一路排到尾。不同的记录在末尾的收口不一样：有一类以 readonly 收尾，有一类以 write_only 收尾，还有一部分记录保持全程 read_write 的连续形态。数量上，短尾记录的主体大约有二十一个 read_write 节点，长尾记录的主体明显更长，read_write 的重复次数显著提升，整体呈现出批量复制、末位差异化的配置特征。

从分布看，readonly 与 write_only 像两种对照的例外标记，和周围大量 read_write 形成反差，说明这批权限在默认可读写的基础上，对少数节点做了只读或只写限制。整体上就是：同一个 im:message:send_to_all 前缀 + 连续的 read_write 序列 + 末端 readonly / write_only / 持续 read_write 三种收口，在多条记录中循环出现。