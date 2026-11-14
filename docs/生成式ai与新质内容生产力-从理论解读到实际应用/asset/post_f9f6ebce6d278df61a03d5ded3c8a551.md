
数据。

第二个创新是预训练和知识蒸馏。前者使研究人员可以在特定任务的小规模数据集上进行微调，以适应该任务，后者旨在通过从大型复杂模型中转移知识来提高小型简单模型的性能。该工具最初是为了减小模型尺寸和计算复杂度而开发的，但随后人们发现它也可以提高模型的泛化性能。

第三个创新是转换器的使用。AlphaFold 使用了 Transformer Encoder 架构来处理蛋白质序列和结构信息。Transformer Encoder 可以对 MSA 表示进行编码，以捕获序列之间的关系。它能够有效地处理长序列，同时保留了序列中的关键特征，并将这些信息用于预测蛋白质的三维结构。这种结合了深度学习和生物信息学的方法在蛋白质结构预测领域取得了显著突破。

第四个创新是空间图的运用。空间图是一种基于图的表示方法，它会用节点和边的形式表示蛋白质的不同部分，并包含它们之间的关系和距离信息（见图 6-19）。通过空间图表示蛋白质结构信息，有助于模型更好地理解蛋白质的性质和结构，从而实现高度准确的结构预测。这对于生物学研究和药物发现等领域具有重要意义。

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_215_0.png)

图 6-19 蛋白质折叠

## 三、生成式 AI 与数据分析

### （一）科学研究中的数据分析

数据收集和分析是科学研究和发现的基础。人工智能越来越频繁地在多学科、多领域被用于集成大量数据集、细化测量、指导实验、探索与数据兼容的理论空间，并提供集成科学工作流的可操作和可靠的模型。尤其是在数据分析方面，信效度检验、相关关系测量、因果关系的判定等分析层面是各项研究中最重要的部分，它们与实验结果的验证直接相关，也间接影响到理论的构建。

李源等学者着重对因果关系的判断之意义进行了论述：“因果关系推断作为一项重要的研究课题，在许多领域中有极高的应用价值。一旦做到真正理解因果关系背后的逻辑，即可在计算机上进行模拟，进而创造出一个‘因果关系推断专家系统’。这个系统将可能为解释或发现未知的现象或规律，解决久而未解的科学问题，开发和设计新的实验，并不断地从环境中获取更多的因果知识，进而为社会和民众带来福祉。”实验数据信效度的检验则与可解释性密切相关，而相关关系正是因果关系推断的必要条件，因此数据分析的每一部分都具有重大意义。现在行业内已经有 IBM 等公司推出 SPSS、Amos、Stata 等多个数据分析软件供学者们使用。

数据分析的学科基础（即统计学），与相关软件的掌握程度与具体操作方法，成了学者们深耕精细研究的一大障碍，也是学者们想做出优质研究的必克难关。然而，很多学者并非统计学相关专业，他们对统计模型、软件应用的掌握程度自然不会非常高，大部分初学者在刚使用软件也难免边摸索边操作，况且软件的运转效率还常常取决于硬件的强度。这一系列的因素使人类研究者在数据分析的时候不仅容易产生错误，而且浪费了大量时间。

除此以外，数据分析的结果往往在实验成果里需要以图表的形式展现，目前相关软件并没有直接形成心仪图表的功能，因此研究人员需要在得出结果或标准化图表以后，对其进行相应的美化润色处理，进而通过可视化充分发挥实验数据的价值，这也是一种人力上的消耗。

### （二）大语言模型与智能数据建模

在最新的 ChatGPT-4V 测评报告中，ChatGPT 通过求解简易方程证明了自己的数学推理能力（见图 6-20）。当然这并不令人感到意外，因为计算机经过长期的发展，早已能通过各种方式实现这样的功能。然而，ChatGPT 有别于其他计算机程序的地方是，它是在一个通用的领域进行解答，且不需要进行任何程序代码的编写。如何认识用户所提供图片中的文字，如何寻找解答方法，如何调动自己的算力，这些工作都不用用户参与，全都可由人工智能自己完成，这便是大语言模型的独特与高效之处。

### Sec. 4.4 Scene Text, Table, Chart, and Document Reasoning

### Visual Math

Prompt: What is x in the equation?

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_217_0.png)

GPT-4V: Without any context or information about what the equation is representing, we can only assume that "x" is a variable or unknown value in the equation. In this equation, (x + 3) is being squared and is equal to 4. To solve for x, we can take the square root of both sides of the equation to get rid of the exponent on the left side. This would give us: √((x + 3)²) = √4 x + 3 = ±2 From here, we can solve for x by subtracting 3 from both sides. This would give us: x = -1 or x = 5 So, in this equation, x could be either -1 or 5.

Prompt: What is the value of d in the last equation?

Solve the following equations:
1) 8x + 11 = 4x + 14
2) 7d - 4 = 11d - 9

GPT-4V: 1) To solve the first equation, we need to isolate the x variable on one side of the equation and the constants on the other side. We can start by subtracting 4x from both sides to get rid of the x term on the right side of the equation. This gives us: 4x + 11 = 14 Next, we can subtract 11 from both sides to get rid of the constant term on the left side of the equation. This gives us: 4x = 3 Finally, we can divide both sides by 4 to isolate the x variable on the left side of the equation. This gives us: x = 3/4 2) To solve the second equation, we can start by subtracting 7d from both sides to get rid of the d term on the left side of the equation. This gives us: -4 = 4d - 9 Next, we can add 9 to both sides to get rid of the constant term on the right side of the equation. This gives us: 5 = 4d Finally, we can divide both sides by 4 to isolate the d variable on the right side of the equation. This gives us: d = 5/4 So, the value of d in the last equation is 5/4.

Prompt: What is the value of x in the image?

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_217_1.png)

GPT-4V: The value of x in the image can be found using the Pythagorean theorem, which states that in a right triangle, the square of the hypotenuse (the longest side, opposite the right angle) is equal to the sum of the squares of the other two sides. In this case, the hypotenuse is the side AC, and the other two sides are AB and BC. We know that AB is 4 and BC is 3, so we can plug these values into the equation: a² + b² = c² 3² + 4² = c² 9 + 16 = c² 25 = c² Therefore, c (the length of the hypotenuse) is 5. Since x is the length of the hypotenuse, x is equal to 5.

Figure 34: Results on visual math reasoning. GPT-4V is able to comprehend and solve visual math problems with a well-structured solution. Check Section 4.4 for detailed discussions.

图 6-20 GPT-4V 读图解决数学题示例

在数据分析领域也同样如此，ChatGPT 等大语言模型通过深度学习技术，已经系统地、高效地掌握了统计学知识，从而使它能根据用户提供的变量类型与样本数量等要素，对数据进行深入挖掘和分析，识别变量的因果关系，构建能够反映数据内在规律和特征的模型。这些模型可以用于预测、分类、关联等任务，甚至形成知识图谱，为决策提供有力支持。

此外，大语言模型是以高性能的 CPU 和 GPU 为算力基础，且拥有特定的程序语言，因此在数据分析上比较精准，它可以直接替代用户进行相应的分析操作。大语言模型甚至能在分析之后形成相应的图表，并给用户相应的解释。由于大语言模型的通用性，相应的视觉细节、数据细节甚至可以通过简单的提示工程优化。这极大程度地节约了用户的学习成本与操作成本。

大语言模型与数据建构在未来有可能形成互相促进的关系。大语言模型的算力提升，强化了数据建构的基础设施，而通过分析数据建构得到的结果，又可以进一步发现大语言模型的潜在缺陷，从而促使人工智能在相应层面进行更深入的学习，形成良性循环。

### （三）案例：ChatGPT 的回归分析应用

宾夕法尼亚大学沃顿商学院副教授伊桑·莫利克（Ethan Mollick）在社交平台上展示了他使用 ChatGPT Code Interpreter（ChatGPT 的代码解释器）实现回归分析的成果（见图 6-21）。他先上传了一个表格文件，并向 ChatGPT 提出了三个问题。

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_218_0.png)

图 6-21 ChatGPT Code Interpreter 回归分析示例（一）

- “你能进行可视化和描述性分析，帮助我理解这些数据吗？”
- “你能尝试回归并寻找模式吗？”
- “你能运行回归诊断吗？”

ChatGPT Code Interpreter 先是给予了肯定的答复，然后陈述它执行相应指令所需要完成的步骤。首先它需要识别表格文件中的变量，判断何为自变量，何为因变量。随后它抓取了表格中的各个变量，自行决定了做几组回归分析，并给出了散点图和回归分析曲线，从而让变量间的关系可视化。除此之外，ChatGPT 还能够主动识别数据可能有误的部分，并主动处理缺失值。图 6-22 为 ChatGPT 绘制的线性回归分析图。

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_219_0.png)

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_219_1.png)

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_219_2.png)

图 6-22 ChatGPT Code Interpreter 回归分析示例（二）

最后，ChatGPT 还将线性回归分析和相应可视化的结果以文字的形式分点呈现，清晰明了地展示了它选取的各组变量之间的联系如何，相应的指标又包含了什么样的具体含义（见图 6-23）。

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_220_0.png)

图 6-23 ChatGPT Code Interpreter 回归分析示例（三）

以上的案例仅仅是让大语言模型完成了简单的数理统计分析过程，其根本性地降低了分析难度，为更多的学者提供了深入研究的机会。未来，大语言模型可能还会在数据分析方面继续发展，比如在构建跨模态知识图谱、多元因果关系推断、动态知识图谱更新、向非专业人士直观展现因果关系等方面进行研究，实现新的突破。

## 四、生成式 AI 与理论构建

### （一）理论探索与构建

理论建构是科学研究的重要环节，是人类通过概念—判断—推理等思维类型，论题—论据—论证的逻辑推导过程来认识、把握世界的逻辑体系。从本质上讲，所有科学研究都是一个观察、合理化和验证的迭代过程。在观察阶段，我们观察自己感兴趣的自然或社会现象、事件或行为。在合理化阶段，我们试图通过逻辑连接我们观察到的不同拼图块，以此来理解观察到的现象、事件或行为，在某些情况下，这可能会导致理论的构建。最后，在验证阶段，我们使用科学方法测试我们的理论（见图 6-24）。

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_221_0.png)

图 6-24 理论探索与构建模型

如上文所述，假设通常是理论建构的第一步。假设的提出离不开前期资料的广泛搜集与筛选，从而归纳出一个逻辑自洽、解释力强、预测力强、可证伪性强的理论。生成式 AI 则利用其数据分析与挖掘的能力，辅助研究人员进行大规模的数据搜集，并从中提取相关有利于理论构建的信息。在理论构建的验证阶段，生成式 AI 的机器学习模型可以用于预测未来事件以及结果，有助于验证理论。

### （二）机器学习与算法归纳

阿尔约特（Alshater）认为 ChatGPT 可能是构建研究思路的有用工具。机器学习和算法归纳的结合，为理论构建提供了更加便利化、全面化、效率化的入口。机器学习和算法归纳之间存在紧密的关系，机器学习的本质即是通过算法进行归纳总结，从大量的数据中学习模式和规律，以便能够在未知数据上进行泛化。这种泛化能力是通过对已知数据的归纳分析和模型构建实现的。如人工智能系统可以在解释科学数据集和从科学文献中提取关系和知识方面发挥宝贵的作用，尤其是无监督大语言模型具有捕捉复杂科学概念的潜力。将机器学习与多案例分析相结合有助于富有成效地开展理论构建，这可以围绕着理论构建的步骤展开。

首先，理论构建需要明确研究问题和研究现象，以便开展具有意义和价值的研究。基于此，需对研究领域进行大量而繁复的文献综述，以便了解该领域此前的理论构建以及已有的研究成果。例如，Scite 即是一个利用人工智能辅助生成文献综述的工具，输入想要探索的问题，即可获得该领域的相关综述。对于先前理论的综述，可以更加深入了解研究主题，并借鉴研究方法，进一步发现研究中存在的相关问题。在文献综述的基础上，研究者提出一个初步的理论，理论中应包含假设、变量之间的联系，用于解释现象或者问题。机器学习可以帮助识别数据所蕴含的模式、关联和趋势，这对于理论建构至关重要。通过分析大规模数据集，机器学习算法可以自动发现潜在的关系，帮助研究者提出新的理论假设。

对于一些需要模型建构的理论而言，机器学习算法可以用来构建数据驱动的模型，这些模型可以帮助理论的构建。这些模型可能包括回归分析、分类模型、聚类分析等，它们可以用于描述和预测数据中的现象。生成式 AI 的模型验证和评估功能，能够通过将数据用于训练和验证模型，研究者可以确定模型的准确性和可解释性，从而支持或修正理论。不可否认的是，机器学习只是理论建构中的一个辅助工具，可以为研究者提供更多的信息和见解。理论建构仍需要人类专家的判断和创造力。

### （三）案例：YModel 辅助自动化数据挖掘和建模

YModel 作为自动化数据挖掘工具，能够辅助不熟悉数据科学知识的初学者和程序员开展工作，允许一键构建模型，显著提升工作效率（见图 6-25）。YModel 官网通过 7 点概括其功能特性：利用手头现有历史数据进行数据挖掘预测；易选择的专业编程语言；价格相对 SAS 更适合，可以为普通人提供统计学帮助；帮助优化、提升模型质量；数据预处理方法简单，操作易上手；容易理解的人工智能算法；降低数据噪声，平衡样本。

![](img/dbd366c6ca413ecc8cc1cbebab8f4f3f_222_0.png)

图 6-25 YModel 自动化数据挖掘工具示例

YModel 通过数据输入、自动数据预处理、智能建模、型号性能、模型输出等一系列流程帮助科学家建立高质量并稳定的模型。从理论构建的角度来讲，分析大规模数据集，通过机器学习算法发现潜在关系，是提出新的理论假设的前提。YModel 利用其功能特性，通过自动导入训练数据，并进行变量统计，进一步进行预处理与建模，对数据进行评分。在不断重复中构建模型，从而辅助理论的构建。

## 第四节 生成式 AI 的广泛应用是对学术生产力的升维改造

### 一、生成式 AI 对传统知识生产秩序的解构

生成式 AI 将解构传统的知识生产秩序。高奇琦认为大语言模型将打破人文社会科学的知识生产秩序。不止于此，大语言模型同时解构自然科学的知识生产秩序，实现生成式 AI 对知识生产全领域的重塑。传统的知识生产秩序由一整套子系统构成，其中包括教育系统、学术发表系统以及成果评价系统等。如果将知识生产视作一个复杂系统，生成式 AI 的出现为其注入了最活跃的变量，打破了传统系统平稳运行的原始状态，推动知识生产系统在不确定、不稳定中求变，获得涌现性的提升。

以 ChatGPT 技术为代表的通用大语言模型从介入文本生文本领域开始，如今 GPT-4 已能够理解图片的意义。Midjourney 主攻文本生图片模型，具备较强的文生图能力，已有部分设计师借助 Midjourney 生成的图片获得了艺术大奖。飞猪平台利用生成式 AI 参与广告图片生成，将 AI 广告投放至上海和杭州的地铁站，在微博引起热议。不止于此，生成式 AI 已经开始初步参与视频内容生成，2023 年年初，Netflix 发布了一个总时长约为 3 分半钟的动画短片《犬与少年》，这是 Netflix、小冰公司日本分部和 WIT STUDIO 共同创作的首个发行级别 AIGC 作品。未来，以 ChatGPT 技术为代表的生成式 AI 会通过多模态的内容覆盖知识生产领域，成为一个近乎无所不能的知识生产工具。

本节从教育系统、学术发表系统、成果评价系统三个角度探讨生成式 AI 对传统知识生产秩序的解构。首先，教育系统层面将推动高等教育平民化，学者大众化；其次，学术发表系统层面将提出生成式 AI 内容的版权争议，提出版权范式的转型迭代；最后，成果评价系统层面将推动生成式 AI 驱动下的自然评价体系建设。

### （一）教育系统：推动高等教育平民化，学者大众化

生成式 AI 会产生巨大的平权效应，赋予每个用户社会平均水准的话语表达与资源
