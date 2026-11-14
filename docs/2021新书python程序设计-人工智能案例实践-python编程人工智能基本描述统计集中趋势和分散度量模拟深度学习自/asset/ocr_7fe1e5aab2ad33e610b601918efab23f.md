Google的另外两篇论文也为Hadoop的发展做出了贡献，即  
“MapReduce: Simplified Data Processing on Large Clusters” 和  
“Bigtable: A Distributed Storage System for Structured  
Data”，后者是Apache HBase（一个NoSQL的键-值，以及面向列的数  
据库）的基础。

# HDFS、 MapReduce和YARN

Hadoop的关键组件如下：

+   - HDFS（Hadoop分布式文件系统）：用于在整个集群中存储大量数据。
+   - MapReudece：用于执行处理数据的任务。

前面介绍了基本的函数式编程以及过滤器/映射/归约。Hadoop MapReduce与其在概念上相似，只是Hadoop MapReduce大规模地并行执 行。执行MapReduce任务有两个步骤：映射和归约。在映射步骤（可 能还包括过滤）中处理整个集群中的原始数据，并将其映射为键-值对 的元组。在归约步骤中将这些元组合起来，生成MapReduce任务的结 果。关键是如何执行MapReduce任务的步骤。Hadoop将数据分为若干 批，其分布在集群中的各个节点上—从几个节点到Yahoo具有40,000个 节点以及超过100,000个处理内核的集群。Hadoop还将MapReduce任务 的代码分发到集群中的节点上，每个节点仅处理存储在该节点上的那 批数据。在归约步骤中合并所有节点的结果以产生最终的结果。为了 对此进行调度，Hadoop使用YARN（Yet Another Resource Negotiator，另一个资源协调者）来管理集群中的所有资源，并调度 执行任务。

# Hadoop生态系统

从HDFS和MapReduce开始，再到YARN，Hadoop如今已成长为一个大 型生态系统，其中包括Spark（在16.6节和16.7节中讨论）和其他 Apache项目：