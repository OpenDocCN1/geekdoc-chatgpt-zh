
从目前对比的结果来看，四个工具对情境化的处理都存在较大问题，如例1中对`beliefs`这一单词的翻译，其中三个工具都将其翻译为了“信仰”，只有NewTranx将其译为“信念”。但是结合语境可知，作者想要探讨的是大数据基于平台经验所得出的关于个人经验的画像，来模拟这一人物所表现出来的特征和观点，因此将其翻译为“信仰”是极为不准确的，因为这里并没有提到有关宗教的内容，整篇文章也未涉及“信仰”。NewTranx将其翻译为“信念”要比“信仰”略有改进，但我们认为其在情境化方面仍然存在进步的空间。

所有的语言都有一词多义，或者多个词语均可描述同一现象的情况，所以在翻译过程中就需要结合具体的语境进行综合考量。情境化的翻译不仅可以使句子更加准确，还可以更加贴近原文的含义。中文往往用一个字就可以表达极为丰富的含义，这些字连在一起又成了对某种状态的精准描述，这些用法在被翻译为英文的过程中不仅需要对字词原本的含义进行解释，还需要对其组合之后的衍生意义进行说明，在表达精准的基础之上还要兼顾连贯性，避免译文过于生硬。以《从脱贫攻坚到乡村振兴：迈向“家国一体”的国家与农民关系》中的一段话为例，来判断不同翻译平台的表现将有助于我们测试其翻译的连贯性。

例2：产业发展中家庭经营主体地位的维持、对内生动力不足以及病残鳏寡户的帮扶方式显示了“家”成为国家与农民之“对接”关系的两个不同侧面。

DeepL：The maintenance of the status of the family as the main businessman in industrial development，the way of helping households with insufficient endogenous motivation and sick and disabled widows and widowers show two different aspects of the relationship between the state and peasants，namely，the economic and ethical dimensions of the family.

NewTranx：The maintenance of the status of the main body of family management in the development of industry，the insufficiency of endogenous motive force and the way of helping the sick，disabled，widowed and widowed households show two different aspects of the relationship between the state and the peasants.

ChatGPT：Maintenance of the household as the primary economic entity in industrial development，addressing intrinsic motivation deficiencies，and support mechanisms for the vulnerable and disadvantaged all showcase the two different facets of the “home” becoming the interface between the state and farmers.

文心一言：The maintenance of family management’s dominant position in industrial development，assistance methods for households lacking endogenous motivation or with disabled，widowed，or orphaned members show two different aspects of the “docking”relationship between “family” and the state and farmers.

这里对“病残鳏寡户”等的翻译都存在一定问题，一方面是不够简洁，使得句子变得过于冗长，另一方面是用词不够精准。如NewTranx的翻译直接忽略了孤儿群体，如果要进行省略，则可以将其全部概括为“groups without self-care ability”，但是一部分省略，一部分详细翻译容易导致信息缺失；再如ChatGPT则直接将“病残鳏寡户”这样颇具中国特色的表述简化为了“the vulnerable and disadvantaged”。

四个工具在参考文献部分均无法提供较为理想的中文翻译。DeepL相比于其他工具会保留更多英文元素不进行翻译。文心一言在这方面存在明显问题，不仅大量内容无法翻译，输入进去的参考文献有一半在输出的时候无故消失。NewTranx则是对能翻译的部分都进行了翻译，但是准确率不是非常高，尤其是人名部分。对于致谢部分的翻译，DeepL在翻译人名时，除了会给出音译的中文名，还会加上括号，在里面写上其原文；但是NewTranx则会直接给出中文名，且名字的翻译也存在很大问题，准确率较低。其他工具则均表现欠佳，无法对翻译质量进行更多评判。

## （四）整体翻译质量

从把英文文献翻译成中文的内容质量上看，ChatGPT虽然整体速度上慢了一些，但两代ChatGPT的翻译质量均远远高于文心一言，而ChatGPT 4.0的译文连贯性又明显强于ChatGPT 3.5。因为4.0版本是基于整篇文档内容进行翻译，而3.5版本是分段落输入进行翻译，所以这可能会影响整体翻译质量。而文心一言则会罔顾使用者的命令，在明确输入“请把以上内容翻译成中文”的情况下，文心一言仍然自动提炼内容，不顾文本翻译的目的，对文章进行概括化的描述，且其中文翻译也很难体现出原文的论文风格。DeepL在翻译的过程中会尽量按照英文的断句进行翻译，而NewTranx则会将逗号隔开的部分连起来翻译，使其译文更符合中文的表达方式。而从译文流畅性角度来评判，文心一言的译文每一段读起来会比其他三个工具的译文更通顺，较少感受到机器翻译的生硬感。

而从把中文文献翻译成英文的内容质量上看，文心一言虽然速度较慢，操作繁琐，但相较于DeepL、NewTranx和ChatGPT而言对于很多中文内容的理解更加深入，在翻译成英文后也更能表现出中国文化特色，翻译出的内容更还原中文语境，整体翻译质量略高一筹。

## 三 译文排版效果

由于英译中之后篇幅会明显增加，所以不同工具均出现了不同程度的排版问题，而在这方面DeepL在翻译PDF的时候表现得尤为不理想，经常会出现找不到分页处的段落下一部分，侧面证明DeepL在翻译PDF时仍然还停留在文字翻译的阶段，尚未对排版等方面做出较为合理的处理，见图1。

![](image/b0000001000000200000000000010501.jpg)

图1 DeepL翻译PDF文件

而DeepL在翻译Word版本的文献时，这一情况得到了极大的改善，甚至在字体等方面都尽量还原了原版的格式。

英文文献原版效果：“…large language models rely on ‘shadows’ of human experiences described in cultural products.”

DeepL译文效果：“……大型语言模型依赖于文化产品中描述的人类经验的‘影子’。”

NewTranx在这一方面也存在一些问题，因为NewTranx对PDF格式识别不准确，将每一个字母都识别成了一个单词，所以最后还是选择转成了Word版本进行测试，但是最后翻译的Word版本也存在一定的问题。比如原文中有一个句子是单独加粗呈现的，但是在翻译后的版本中，这一句子直接被省略了。

英文文献原版效果：“…large language models rely on ‘shadows’ of human experiences described in cultural products.”

NewTranx译文效果：“……建立在较小受控数据集上的实验室生长的自然语言处理模型将提供更强的内部有效性。”

而在中译英的情况下，无论是DeepL还是NewTranx对于译文的排版皆不尽如人意。经DeepL翻译后的文献排版上出现了数页空白以及页码错乱的情况，此外，DeepL翻译时还对很多单词进行了随机加粗处理，这些都是中文原文中所没有的操作。而在NewTranx对于Word版本的中文文献进行翻译时，除了译文是以一段中文一段英文的方式呈现以外，大部分内容还超出了界面显示范围，呈现的排版效果十分混乱。由此可见，DeepL和NewTranx虽然方便快捷，但是格式上依旧需要花费不少时间进行相应的调整才能够得到一篇相对完整合格的译文。

不同于DeepL和NewTranx，ChatGPT和文心一言两个工具由于需要通过使用者输入原文进行翻译或是不断输入“继续”指令进行翻译结果的输出，所以最终排版效果取决于使用者如何排版，在增加使用者工作压力的同时此项指标脱离平台自身属性，无法进行比较，因此在这里不再进行探讨。

## 四 总结与反思

表2 各工具翻译时长对比

![](image/b0000001000000200000000000010601.jpg)

综上所述，虽然五大翻译平台在翻译不同句子时各有各的长处和短处，但总体而言可将它们的翻译水平进行粗略的排序，即文心一言＜ChatGPT3.5＜ChatGPT4.0＜NewTranx＜DeepL。

总体来看，无论是通用型AI平台还是专业的翻译工具，目前都难以做到完全代替人工翻译，但是翻译工具的方便快捷还是可以为译文准确度要求不那么高的文档提供快捷的解决方案，有助于使用者在短时间内快速对文章内容有一个大致的了解。但如果是专业知识的学习，还是需要直接阅读原文。目前AI平台和专业的翻译工具都未能完全做到忠于原文进行翻译，且都无法完全克服不同格式对翻译的影响。目前的翻译尚且无法做到对全文的核心观点进行准确的“理解”，也就难以在翻译的过程中呈现文章思想的精髓。最后，所有工具在处理参考文献的时候都会出现明显问题，尤其是在人名、地名和出版社等的缩写方面。就测试的结果来看，目前只有DeepL对Word版本的翻译较为贴近原文。综上，目前使用AI平台和专业的翻译软件/平台时，最理想的模式是人和工具的使用相结合，唯有此才能做到在更快理解文章大意的基础之上对文章进行更深入和更准确的解读。

需要各位读者注意的是，本研究在翻译比较上还是非常粗陋的，存在以下不足。首先，不同工具背后的语料库存在规模大小、专精方向上的差异。考虑到全世界绝大多数的论文均为英文的理工科论文，本研究仅仅采用两篇社科文章进行比较很难具有代表性和典型性。其次，不同工具在后期优化水平上存在较大差距，比如不同格式的文档在转化中体现的就是不同公司后期优化的能力，而这个黑箱式的过程是无法考察的。再次，本研究受到经费限制，除了ChatGPT之外，未能检测DeepL和NewTranx的付费版。而后两者付费版拥有更多功能、得到更庞大语料库支持、优先得到更强大算力和带宽，本研究对于经费充足的科研团队来说缺少参考价值。此外，这些工具都在不断地更新、快速地迭代，据说每几个月就会更新一次，本研究的比较仅能代表四个工具在2023年国庆前后的水平，很可能不同工具在几个月后的表现会和今天明显不同。最后，在研究过程中了解到，AI平台在对文本进行翻译的过程中会受到“食物”，即开发者在开发过程中为AI进行示范的范本的影响，不同学科领域、不同水平的范本都会对AI翻译的准确性造成影响，所以未来AI发展中如何获得有效无污染的范本也成了一个挑战。此外，由于AI强大的数据抓取能力，其在获取范本过程中面临的版权合法性的问题也亟待解决。

# A Comparative Study of the Translation Proficiency of AI-Based Translation Tools

*Qian Ma and Xiaoci Cui*

**Abstract：**As the internationalization of Chinese academic research increases，the demand for translation software in academic writing，reading，and communication becomes more pressing. In recent years，the explosive development of Generative Artificial Intelligence（GAI）has also raised high expectations among scholars for its role in translation scenarios. In order to better leverage AI-based translation tools in academic production，this study selected four tools：ChatGPT，Wenyi Yiyuan，DeepL，and Newtranx. By comparing their translations of the same article，we evaluated their performance in terms of speed，accuracy，compatibility，and coherence. Our research found significant differences among these tools in terms of usage and translation speed. While they produce different final translations，they also share some common issues. Based on in-depth usage of these four translation tools，this study provides a relatively systematic operational framework for educators and scholars seeking to utilize such tools to assist in academic production. It also offers guidance on avoiding common pitfalls in their use and makes recommendations for the future technological development of these platforms.

**Keywords：**Artificial Intelligence；Translation Tools；Man-Machine Interaction；Comparative Study

* * *

[\[1\]](#chapter010.html#w1) 马倩，云南大学民族学与社会学学院硕士研究生；崔筱慈，云南大学民族学与社会学学院硕士研究生。

## ChatGPT 4.0生成学术论文全文的使用方法和评测

朱悦[^(\[1\])](#chapter011.html#m1)

**摘要：**本文为笔者使用ChatGPT-4进行学术写作并完成全篇论文的使用手册。在写作开始前，笔者借鉴了目前相对成熟的提示方式，用英文与ChatGPT就写作主旨和全文结构进行了详细的要求。之后笔者再对ChatGPT进行逻辑上和思考上的要求，使其逐章节完成英文论文写作。在完成后，笔者要求ChatGPT检验文献来源真实性，并对写作规范做进一步要求。最后，笔者要求ChatGPT将英文翻译成中文，完成全文的写作。基于笔者多次使用经验，笔者对现阶段人工智能的学术写作能力进行评估，并对其未来发展进行了预测。

**关键词：**ChatGPT AI写作 人工智能与伦理

### 一 生成方式

本文通过笔者与2023年8月3日版本的ChatGPT-4对话生成。具体而言，笔者首先概述本文写作主旨和全文结构，并提示ChatGPT-4可以在有充分理由的前提下适当偏离文章主旨。同时在本文生成过程中，笔者也借鉴了目前相对成熟的提示实践，要求ChatGPT-4注重逻辑、逐步思考，并要求只能引用可检索的文献，最后按照文章结构逐节生成。在这一过程中，笔者的干预主要体现在两个方面：一是在ChatGPT-4引用文献后，会要求其再检查一次文献来源是否真实可靠；二是如果ChatGPT-4在文体上偏离学术论文的写作方式，会要求其以学术写作的方式重新生成。最后，笔者将生成的各个英文章节片段缀连成原稿，通过同样基于人工智能的DeepL Pro翻译软件将其译为汉语，再由笔者手动修改参考文献格式。

### 二 生成质量

整体而言，这当然不可能是一篇质量上乘的论文。我们可以看到本文分析深度较浅，对于理论的理解和引用更像是囫囵吞枣的产物，许多段落谋篇和行文充满了“人工智能味”。此外，本文少数参考文献依然存在“弄虚作假”的问题。尽管如此，本文的质量也并没有想象中的那么差，部分段落的论述颇有见地，甚至让人有所收获。大部分的参考文献也都符合文章的需要。如果满分100分，笔者认为这篇论文可以得到30分，且至少有五个理由让我们相信人工智能生成的论文未来可以达到60分甚至更高水平。

第一，笔者使用的只是非常“大路货”的ChatGPT-4，没有使用功能更强的ChatGPT Enterprise版，也没有使用传言正在开发的ChatGPT-5，而后两者生成文章的水平显然会比这篇论文要高。第二，由于网页端个人用户能够使用的算力有限，所以ChatGPT-4的生成能力没有得到完全发挥。第三，为了尽可能展示ChatGPT-4本身的能力，笔者没有使用联网状态下的各类插件，这一点极大限制了文献综述等章节的生成质量。第四，笔者使用的提示词也非常粗糙。尽管笔者使用了一些常见技巧，但既没有使用拼接等能够显著加强长文本生成能力的提示构造方法，也没有引入“越狱”等潜在上限更高的提示技巧。第五，本文没有在通用型人工智能的ChatGPT-4上附加针对学术论文生成所需要的相关微调，而微调能够显著使得在特定类别任务上的表现更佳。综上，笔者相信，如果有足够的算力、数据、经验等资源，未来生成达到60分甚至更高水平的论文是有可能的。

### 三 具体操作示例
