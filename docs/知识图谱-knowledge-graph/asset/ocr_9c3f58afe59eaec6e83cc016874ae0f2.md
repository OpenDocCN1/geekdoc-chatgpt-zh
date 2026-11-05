（1）链接候选过滤（Blocking）：由于一个知识库中通常包含上百万个实体，在实际的实体链接任务中不可能计算一个指称项与所有实体之间进行链接的可能性。因此，实体链接需要首先根据规则或知识过滤掉大部分该指称项不可能指向的实体，仅保留少量链接实体候选。

（2）实体链接（Linking）：给定指称项及其链接候选，实体链接的第二个步骤是确定该实体指称项最终指向的目标实体。

目前，大部分实体链接研究的重点在于第二步，即如何根据实体指称项的上下文信息和知识库中实体的信息从链接候选中确定目标实体。

# 5.3.1 链接候选过滤方法

目前还缺少对链接候选过滤方法的系统化研究和量化分析，大部分工作都是基于实体指称项词典：通过在词典中记录一个指称项所有可能指向的目标实体来进行链接候选过滤。表 5.2 展示了一个实体指称项的词典示例。从中可以看出，基于实体指称项词典，我们可以通过查词典的方式方便地获取一个指称项所有可能指向的实体，如 AI 的目标实体候选为 Artificial Intelligence、Ai (singer) 等。

表 5.2 实体指称项字典实例

| 实体名 | 目标实体 |
|---|---|
| IBM | IBM
IBM Mainframe
IBM DB2
... |
| AI | Artificial Intelligence
Game Artificial Intelligence
Ai (singer)
Angel Investigations
Strong AI
Characters in The Halo Series
... |
| ... | ... |

传统实体链接方法通常使用 Wikipedia 等知识资源来构建指称项词典，包括 Wikipedia Entity Name，Wikipedia Redirection Page，Wikipedia Disambiguation Page，Wikipedia Anchor Dictionary 以及 Wikipedia 第一段里面的粗体字（也包括一些别名信息）。为了匹配模糊的或拼错的指称项，一些基于