
### 9.4.6 SmartEye系统

构图是影响照片质量的一大因素，如今已经有很多构图推荐算法来帮助用户构图。构图同时也是一个主观性十分强的任务，由于审美的不同，不同的人对构图有着不同的喜好，现有的算法均没有考虑用户偏好的问题。为了解决这个问题，SmartEye系统设计了一个用户构图偏好学习算法来帮助构图推荐算法学习用户的偏好。具体来说，用户拿着手机单击拍照按钮，系统会基于构图推荐算法给出多种构图推荐。用户可以选择一张最喜欢的保存，从这一自然的“拍照-保存”过程中，系统可以逐渐学习用户的构图偏好。具体实现过程为，系统引入一个偏好学习算法来调整构图推荐模型的输出，用户每保存一次喜欢的照片，偏好学习模型都会提取该照片与构图相关的特征，并将该照片作为正样本，其他照片作为负样本来更新基于逻辑回归的用户偏好模型，每一次保存照片之后，模型都会快速进行更新，更新后的新模型用来支持下一次的拍照，这样随着用户使用得越多，系统就会推荐给用户越来越满足自己喜好的构图。

SmartEye系统的交互式学习示意图如图9.10所示。针对同一个场景，SmartEye系统会给出几种不同的构图推荐供用户选择，用户每选择一次，系统就会从中学习到一些知识，从而快速更新系统背后的构图偏好模型，随着用户的使用，系统会逐渐学习到用户的构图偏好。

![img](Images/txt009_10.jpg)

图9.10　SmartEye系统的交互式学习示意图

用户输入：用户对当前推荐构图的态度(喜欢/不喜欢)。

系统反馈：在下一次推荐过程中展示学习用户偏好之后的推荐结果。

交互模式：用户输入-系统反馈循环。

值得一提的是，SmartEye系统提供了隐式交互的方式，通过将打标签融入拍照应用场景中的必需步骤(从多个构图候选中保存自己最满意的一张)，用户可能并没有意识到自己在参与一个机器学习算法的更新和训练，减轻了用户的负担。

### 9.4.7　ReGroup\[26\]:交互式地为用户推荐相关的群组成员

ReGroup系统是一个辅助用户创建群组的工具。一般来说，用户想要拉一个讨论组或者群组，被邀请进群的人都会有一些共同特点，比如在同一家公司或者有一些共同好友等。ReGroup系统可以在用户向群组中拉人的过程中，不断学习群组内成员的特征相似之处，并以此推荐最可能的候选群成员。

举一个例子，Sally想给华盛顿大学的一些朋友做一个小规模的线下报告，并决定用ReGroup系统来创建一个关于这个报告的私人群组。首先，Sally想到一个她认识的朋友会对这个报告感兴趣，然后她通过名字进行搜索(通过一个搜索框，见图9.11)。她通过单击屏幕上的添加按钮把这个朋友添加到了群组。ReGroup系统从这个例子中进行学习，尝试帮助Sally找到其他朋友来加到群组中。ReGroup系统重新组织朋友列表，以便将更可能拉入到群组中的朋友排序到顶部显示。Sally现在看到了其他几个对这场报告感兴趣的朋友，并将他们同时也添加到她的群里。通过这些新加入群组的用户，Regroup系统了解了更多关于正在创建的群组信息，并重新整理Sally的朋友列表，以帮助她找到更多的需要添加到群里的朋友。

![img](Images/txt009_11.jpg)

图9.11　ReGroup系统使用交互式机器学习帮助用户在在线社交网络中创建自定义的群组

当ReGroup系统了解到Sally正在创建的群组时，它还显示了可以用作过滤器的相关特征。例如，考虑到目前已经选中的好友，Regroup系统认为Sally可能希望往群里拉进来住在华盛顿州的其他人，这些人往往与她有几个共同的好友，并且在华盛顿大学工作。虽然并非每个Sally想拉进群组的好友都在华盛顿大学工作(例如有些是学生)，但她假定这些好友都可能住在华盛顿。她单击了“当前状态：华盛顿”过滤器后，ReGroup系统将不住在华盛顿的人从推荐列表中删除。这有助于减少Sally在选择好友时必须考虑的人数，更方便Sally创建一个群聊。

Sally继续以这种方式与Regroup系统进行交互。当然她也可以自己创建一些条件过滤器，直到把所有想拉进来的好友都拉进群组。

用户输入：已拉入群里的用户、选用/弃用的约束条件。

系统反馈：剩下的好友中可能被拉进群的概率，以及从已加入群组的好友中学到的约束条件。

交互模式：用户输入-系统反馈循环，混合主导交互。

该系统不但可以根据用户输入学习到一些知识，并且会将学到的知识反馈给用户，让用户从中进行选择，丰富了交互的形式，实现模型更有效的更新和迭代。

### 9.4.8　小结

在这一节我们回顾了几个交互式机器学习的典型实例，重点讨论了这些系统中关于丰富用户和机器交互的界面设计。从上述实例中可以看出，从用户到机器的信息流已经远远超越了传统机器学习里面的数据标注，还包括交互式地设定各特征的重要性、指定具体实例之间的相似关系、调整决策边界、调整集成分类器中各子模块的比重等。与此对应的是，系统的反馈也不仅仅局限于对未标记数据的预测的可视化呈现，同时也包括对用户具体的操作或输入所带来影响的反馈、对算法内部参数的呈现以及对用户数据包含的冲突提醒等。从交互模式的角度，最常见的是用户标记-系统反馈的循环，但引入主动学习机制进而构建混合主动界面来帮助用户更快速地找到更有价值的数据是该方向一个发展趋势。

## 9.5　讨论与展望

交互式机器学习是一种能够让终端用户深入参与机器学习系统构建的技术，在大数据的时代具有巨大的潜力。同时，该方向的研究工作仅在近些年刚刚开始展开，还有很多研究机会和开放问题值得探索。本节讨论关于交互式机器学习中的交互设计的开放性的挑战和机遇。

从本章所讨论的各个研究实例可以看出，交互式机器学习已经被很多领域应用来解决各自不同的问题，比如信息检索中的搜索、推荐系统中的过滤等。然而，不同的领域通常会用不同的术语来命名交互式机器学习系统(比如相关反馈、演示编程、调试机器学习)。术语的不同阻碍了人们对于这一共同问题的统一认识，并极有可能导致重复的工作。因此，一个重要的研究机会就是如何从具体的研究工作中剥离出与领域无关的工作流程和系统模块，进而采用共同的描述语言统一认识，加速这一领域的研究和发展。

除了建立通用的语言描述，归纳和总结交互式机器学习界面设计的原则和指导方针也是一项重要的研究机会。它可以指导我们设计未来的交互式机器学习系统，正如那些设计传统界面和交互的原则一样。比如，Schneiderman提出的黄金法则\[25\]指出用户应该是系统的控制者，系统在每次交互的时候都应给用户提供详细的反馈。然而，并不是所有的原则都可以适用于这个新领域。比如，研究表明，如果用户可以准确地了解系统的工作机制并能够预测系统的下一步工作，交互就会更高效。然而大多数机器学习系统都无法做到这一点，因为让普通用户了解系统背后的学习算法是非常困难的。因此，如何调整我们现有的界面设计原则使其能够应用在交互式机器学习系统的设计中是一个很重要的研究方向。

另外一个重要的问题是关于制定合理评估交互式机器学习系统的标准。由于交互式机器学习中同时涉及人机交互和机器学习算法，且两者往往紧密交织，因此传统的界面设计评估方法和算法评估方法都很难应用在这个领域。比如，当一个系统工作效果不理想时，很难确定原因是交互设计不好、算法不好或交互和算法的组合不好。因此，制定合理的评估标准是一项亟待解决的工作。

交互式机器学习中的交互周期相对于传统的机器学习具有更快速、更集中、较小增量的特点。这些特点更加凸显了交互式机器学习中人和机器间高效交互的重要性。本章通过案例研究的形式，在介绍交互式机器学习领域发展概况的同时，着重围绕用户输入、系统反馈以及交互模式三大问题，讨论如何丰富该类系统中的交互设计，实现人与机器有效沟通理解并实现高效学习。除了展现交互式机器学习领域研究工作的重要性和未来的发展潜力，本章的最后也指出了该领域目前存在的挑战和机遇，包括建立统一描述语言、归纳界面交互设计原则以及制定合理评估标准等。我们相信，了解并试图解决这些挑战会促进该领域的长远快速发展。

## 参考文献

\[1\] FAILS J A, OLSEN J D R. Interactive machine learning \[C\]//Proceedings of the 8th international conference on Intelligent user interfaces,2003: 39-45.

\[2\] AMERSHI S, CAKMAK M, KNOX W B,et al. Power to the people: The role of humans in interactive machine learning\[J\].AI Magazine, 2014,35(4):105-120.

\[3\] LESH N, RICH C,SIDNER C L.Using plan recognition in human-computer collaboration\[J\]. In UM99 User Modeling . Vienna:Springer, 1999: 23-32.

\[4\] ADOMAVICIUS G,TUZHILIN A.Toward the next generation of recommender systems: A survey of the state-of-the-art and possible extensions\[J\].IEEE Transactions on Knowledge & Data Engineering, 2005 (6): 734-749.

\[5\] KONSTAN J A,RIEDL J. Recommender systems: from algorithms to user experience\[J\]. User modeling and user-adapted interaction, 2012,22(1-2):101-123.

\[6\] STAMOU S,NTOULAS A. Search personalization through query and page topical analysis\[J\].User Modeling and User-Adapted Interaction, 2009,19(1-2): 5-33.

\[7\] SHEN X, TAN B, ZHAI C. Implicit user modeling for personalized search \[C\]//Proceedings of the 14th ACM international conference on Information and knowledge management,2005:824-831.

\[8\] RUVINI J D. Adapting to the user’s internet search strategy \[C\]//International Conference on User Modeling,2003:55-64.

\[9\] HARMAN D. Relevance feedback and other query modification techniques \[M\]//Information retrieval:data structure and algorithms.Englewood Cliff:Prentice-Hall，1992.

\[10\] GAUCH S, SPERETTA M, CHANDRAMOULI A,et al. User profiles for personalized information access\[J\]. The adaptive web,2007: 54-89.

\[11\] WHITE R W.Implicit feedback for interactive information retrieval\[D\]. Scotland，UK：University of Glasgow，2004.

\[12\] SEGAL R B, KEPHART J O. MailCat: An Intelligent Assistant for Organizing E-Mail\[J\]. AAAI/IAAI,1999:925-926.

\[13\] ABOWD G, ATKESON C G, HONG J,et al. Cyberguide: A mobile context-aware tour guide\[J\]. Wireless networks, 1997,3(5):421-433.

\[14\] ![img](Images/txt009_12.jpg) M A, GONZALEZ V M, RODR![img](Images/txt009_13.jpg)GUEZ M,et al. Supporting context-aware collabo-ration in a hospital: an ethnographic informed design \[C\]//International Conference on Collaboration and Technology,2003: 330-344.

\[15\] DEY A K, HAMID R, BECKMANN C,et al. Acappella: programming by demonstration of context-aware applications \[C\]//Proceedings of the SIGCHI conference on Human factors in computing systems,2004: 33-40.

\[16\] FOGARTY J,HUDSON S E. Toolkit support for developing and deploying sensor-based statistical models of human situations \[C\]//Proceedings of the SIGCHI Conference on Human Factors in Computing Systems,2007:135-144.

\[17\] FOGARTY J, TAN D, KAPOOR A,et al. Cueflik: interactive concept learning in image search \[C\]//Proceedings of the sigchi conference on human factors in computing systems,2008:29-38.

\[18\] AMERSHI S, FOGARTY J, KAPOOR A,et al. Overview based example selection in end user interactive concept learning \[C\]//Proceedings of the 22nd annual ACM symposium on User interface software and technology,2009:247-256.

\[19\] AMERSHI S, FOGARTY J, KAPOOR A,et al. Examining multiple potential models in end-user interactive concept learning \[C\]//Proceedings of the SIGCHI Conference on Human Factors in Computing Systems,2010:1357-1360.

\[20\] KAPOOR A, LEE B, TAN D,et al. Interactive optimization for steering machine classification\[C\]//Proceedings of the SIGCHI Conference on Human Factors in Computing Systems, 2010:1343-1352.

\[21\] FREUND Y,SCHAPIRE R E. A decision-theoretic generalization of on-line learning and an application to boosting \[C\]//Journal of computer and system sciences, 1997,55(1):119-139.

\[22\] TALBOT J, LEE B, KAPOOR A,et al. EnsembleMatrix: interactive visualization to support machine learning with multiple classifiers \[C\]//Proceedings of the SIGCHI Conference on Human Factors in Computing Systems, 2009:1283-1292.

\[23\] FAN X, LIU Y, CAO N,et al. Mindminer: A mixed-initiative interface for interactive distance metric learning \[C\]//IFIP Conference on Human-Computer Interaction,2015:611-628.

\[24\] SHNEIDERMAN B,PLAISANT C.Designing the user interface: strategies for effective human-computer interaction\[M\]. Pearson Education India，2010.

\[25\] MA S, WEI Z, TIAN F,et al. SmartEye: Assisting Instant Photo Taking via Integrating User Preference with Deep View Proposal Network \[C\]//Proceedings of the 2019 CHI Conference on Human Factors in Computing Systems, 2019:471.
