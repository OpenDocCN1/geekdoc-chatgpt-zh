# 2. 消息模型

Agent 间的消息交互在 Agent 类模型中已经有所体现，因为黑板模型承担了知识共享的角色，Agent 间的消息交互变得比较简单。本章采用 KQML 语言定义了 Agent 之间的消息模型，定义了 inform 和 trans 两个扩展消息原语。

inform: 通知消息，当检测器中心更新检测器后，通知各人侵监视器从黑板上获取新的检测器；

trans: 传送消息，负责从信息收集器到人侵监视器的抗原传送，驱动抗原识别。

以下定义各类通信的消息格式。

+   1) 检测器中心到人侵监视器的通知消息

```
{inform
:sender CenterX
:receiver MonitorY
:language KQML
:ontology Detector
:content (new)
}
```

其中，sender 参数为发送通知的检测器中心名称，即 DetectorCenter. name；receiver 参数为接收通知的人侵监视器名称，即 IntrusionMonitor. name；ontology 参数为更新的检测器；content 参数为更新的类型，其中，new 表示新增了检测器，delete 表示删除，modify 表示修改，replace 表示全部替换。

+   2) 信息收集器到人侵监视器的传送消息

```
{trans
:sender CollectorX
:receiver MonitorY
:language KQML
:ontology RFIDAntigen
:content (…) 
}
```

其中，sender 参数为发送特征日志的信息收集器名称；receiver 参数为接收特征日志的人侵监视器名称；ontology 参数为 RFID 抗原；content 参数为 RFID 抗原的属性和属性值，用 attr= value 的形式表示。

在传输协议的选择上，为了保证 Agent 的自治特性，可选用非连接的 UDP 协议，通信方式采用简单可靠的点对点方式。