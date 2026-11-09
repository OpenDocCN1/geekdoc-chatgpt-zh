
Qian，Z. 1997. “Breaking the Racial Barriers：Variations in Interracial Marriage between 1980 and 1990.” *Demography* 34（2），263-276.

Qian，Z. 1998. “Changes in Assortative Mating：The Impact of Age and Education，1970-1890.” *Demography* 35（3）.

Raymo，J.M. & Y.Xie. 2000. “Temporal and Regional Variation in the Strength of Educational Homogamy.” *American Sociological Review* 65（5）.

Rosenfeld，M.J. 2008. “Racial，Educational and Religious Endogamy in the United States：A Comparative Historical Perspective.” *Social Forces* 87（1）.

Schwartz，C.R. & R.D.Mare. 2005. “Trends in Educational Assortative Marriage from 1940 to 2003.” *Demography* 42（4）.

Smits，J.，Ultee，W.，& Lammers，J. 1998. “Educational Homogamy in 65 Countries：An Explanation of Differences in Openness Using Country-Level Explanatory Variables.” *American Sociological Review*，264-285.

Smits，J.，W.Ultee & J.Lammers. 1998. “Educational Homogamy in 65 Countries：An Explanation of Differences in Openness Using Country-Level Explanatory Variables.” *American Sociological Review* 63（2）.

Smits，J. 2003. “Social Closure among the Higher Educated：Trends in Educational Homogamy in 55 Countries.” *Social Science Research* 32（2）.

Song，L. 2009. “The Effect of the Cultural Revolution on Educational Homogamy in Urban China.” *Social Forces* 88（1），257-270.

Ultee，W.C.，& Luijkx，R. 1990. “Educational Heterogamy and Father-to-Son Occupational Mobility in 23 Industrial Nations：General Societal Openness or Compensatory Strategies of Reproduction？” *European Sociological Review* 6（2），125-149.

Whyte，M.K.，& Parish，W.L. 1984. *Urban Life in Contemporary China*. Chicago：The University of Chicago Press.

Xie，Y.，& Dong，H. 2021. “A New Methodological Framework for Studying Status Exchange in Marriage.” *American Journal of Sociology* 126（5），1179-1219.

Xie，Y.，Cheng，S.，& Zhou，X. 2015. “Assortative Mating without Assortative Preference.” *Proceedings of the National Academy of Sciences* 112（19），5974-5978.

Xie，Y. 1992. “The Log-Multiplicative Layer Effect Model for Comparing Mobility Tables.” *American Sociological Review*，380-395.

Xu，X.，J.Ji & Y.Y.Tung. 2000. “Social and Political Assortative Mating in Urban China.” *Journal of Family Issues* 21（1）.

Yu，J. & Y.Xie. 2015. “Changes in the Determinants of Marriage Entry in Post-Reform Urban China.” *Demography* 52（6）.

# Educational and Ethnic Matching in Marriage：A Quantitative Analysis and Text Generation Based on ChatGPT

*Maocan Guo and Yuyu Wu*

## Abstract
Previous studies on marital matching patterns in China have predominantly focused on educational matching，with limited attention given to the role of ethnicity in marriage. Using data from the “China Rural Social Survey”，this study employs log-linear models to deeply explore the effects of education and ethnicity on assortative mating. The findings reveal that，with the progression of China’s reform and opening-up，there has been a gradual increase in educational homogamy in marriages，especially in couples where both partners either have no education or have received education beyond high school. Furthermore，in couples with varying educational levels，the phenomenon of “downward marriage”，where the husband’s educational level exceeds that of the wife’s，is more pronounced. In terms of ethnic matching，educational homogamy in marriages has strengthened，irrespective of whether the marriages are within the same ethnicity or interethnic，especially within the Han ethnicity. Notably，to a certain extent，education seems to play a compensatory role against ethnic differences. Particularly in interethnic marriages where the husband is Han and the wife belongs to a minority ethnicity，the “identity compensation” role of education is evident，reflected in scenarios where the wife’s educational level significantly surpasses the husband’s，yet they still match. However，in combinations where the husband belongs to a minority ethnicity and the wife is Han，this compensatory effect is relatively weak. These findings underscore the intricate interplay between education and ethnicity in marital matching and emphasize the significance of education in fostering social integration，especially in a multi-ethnic context. On a deeper level，these insights offer a fresh perspective on understanding the shifts in modern Chinese society and the driving forces behind individual choices.

## Keywords
Marital Matching；Educational Homogamy；Ethnic Identity；`CRSS`

* * *

[\[1\]](#chapter004.html#w1) 本文系云南大学新一轮双一流建设重大项目“中国乡村社会大调查（CRSS）”系列成果之一。感谢参与云南大学社会学系“量化论文推进会”的师生的批评与建议。文责自负。

[\[2\]](#chapter004.html#w2) 郭茂灿，云南大学民族学与社会学学院副教授；吴宇雨，云南大学民族学与社会学学院硕士研究生。

## 《婚姻的教育和民族匹配：基于ChatGPT的量化分析及文本生成》技术报告

郭茂灿 吴宇雨[^(\[1\])](#chapter005.html#m1)

### 一 文献综述部分的文本生成

在我们进行量化研究论文的撰写过程中，对文献的回顾是至关重要的。最初，我们收集了逾100篇中英文文献作为研究基础。但在利用ChatGPT对这些文献进行尝试性的导入和写作后，发现其输出的初始文献综述的质量和深度都未能满足预期。我们推测，这可能是由于文献的数量和质量均未达到ChatGPT深度学习的标准。为了克服这一困难，我们进一步扩充了文献库，特别是增加了与婚姻匹配相关的文献，以丰富研究背景和内容。经过再次筛选，我们选定了210篇中英文文献进行分析，其中包括30篇中文文献和180篇英文文献。为了最大限度地利用ChatGPT-4的`Advanced Data Analysis`功能，我们选择了批量导入的方式。具体来说，我们将这210篇文献分为21组，每组10篇，并按组导入ChatGPT中。在此过程中，我们为每组文献设定了一系列命令，指导ChatGPT提取关键信息，如题目、作者、年份、摘要、研究内容、方法和结论等，并要求它根据这些信息生成一个文献总结的`Word`文档。这样，ChatGPT就可以对每篇文献进行深入的学习和分析，为我们提供一个全面而详细的文献解析报告。

在正式开始使用ChatGPT进行文献综述之前，我们认为必须确保其能够理解并遵循学术写作的规范。因此，我们输入了一个“`标准文风.docx`”文档，指导ChatGPT学习并遵循文献综述的格式和写作风格。这不仅能帮助ChatGPT明确文献综述的结构和引用格式，还能够使其保持写作的一致性和逻辑性。此外，为了确保ChatGPT能够准确地捕捉本次研究的核心要点，我们再次输入了一个“`文献综述范本.docx`”文档，明确要求ChatGPT根据该范本学习并理解本次研究文献综述的主要观点和论述逻辑。这确保了在撰写文献综述时，ChatGPT不仅能够遵循正确的格式和结构，还能够准确地反映研究的核心内容和观点。这一步骤对于后续的写作工作至关重要，因为只有当ChatGPT完全理解了文献综述的格式和结构时，它才能够为我们提供高质量、符合学术规范的写作输出内容。

尽管经过这些步骤，ChatGPT生成的文献综述已经有了明显的改善，但仍存在一些问题，如所引文献数量不足、核心主题不突出等。为了解决这些问题，我们进一步引入了人工干预。我们将文献根据我们自己的理解进行分类，制作了一个详细的“`文献回顾的维度.docx`”文档，明确了每个部分的主题和内容。然后，我们通过指令让ChatGPT逐一撰写每个部分，确保内容既有深度又有条理。为了提高文献综述的质量，我们还多次让其就生成的文本进行修改和完善，确保逻辑严密和文字通顺，且每个部分都能够准确地反映文献的核心观点和研究成果。在这一过程中，我们特别注重让ChatGPT对文献进行深度的评价和分析，使其不仅能描述每篇文献的主要内容和观点，还能指出其优点和不足，以及其与其他文献的关系。经过这一系列的优化，文献综述部分的最终生成文本基本达到了我们的预期。

### 二 引言部分的文本生成

引言部分的文本生成是一个难点。引言是学术论文的门面，它不仅为读者介绍了研究的背景和目的，还概述了研究的核心内容和使用的数据、方法。这部分的撰写尤为关键，因为它为整篇论文定了基调。撰写引言需要对研究主题有深入的理解，同时还要具备扎实的写作技巧。

在我们的研究过程中，当文献综述部分完成后，我们选择了一个与众不同的方法来撰写引言。考虑到`ChatGPT-4`的高级功能和它在文献综述部分的表现，我们决定利用它来协助我们撰写引言部分。我们将已经完成的文献综述上传到ChatGPT中，并明确指示它基于此部分的内容来完成引言。这一方法有其独特的优势。首先，ChatGPT能够迅速捕捉文献综述中的核心观点，并据此为引言部分提供一个清晰且有条理的结构。其次，得益于其深度训练的语言模型，它能够确保引言的语言既简洁明了，又具有学术性和权威性。

但这个过程并非一帆风顺。如果没有经过人工的干预和调整，ChatGPT并不能完全符合逻辑地进行文献综述，从而落脚在研究问题上。为此，在与ChatGPT的互动中，我们多次调整和优化了指令，以确保引言部分的内容、结构、格式、文风和字数都符合我们的要求。尤其是在描述研究的背景和目的时，我们特别注重生成文本在逻辑上的严密性，要求其在推导到研究问题时尽可能地进行平滑的转折。

为了进一步提高引言的质量，我们还多次要求其在保持逻辑严密、文字通顺的基础上，对生成文本进行修改和润色。这确保了引言部分的文字能准确地反映研究背景、研究问题和研究的必要性，从而为读者提供一个清晰而有深度的研究概览。

### 三 数据、变量和方法部分的文本生成

在研究的数据部分，我们首先给ChatGPT上传一份“`数据部分范本.docx`”文档，要求它阅读并理解其全部内容，然后学习其格式和文字风格。之后，我们又继续上传了一份关于“中国乡村社会大调查”的介绍，要求其综合数据介绍，根据之前上传的范本，完成数据部分的文本生成。

在变量部分，我们继续给它上传一份涵盖统计分析所涉及的变量的描述数据（`.dta`格式），要求其读取数据，并按照给出的`Stata`命令对变量之间的关系进行统计描述，最后输出统计描述表格。最终，它成功地结合所给数据生成了描述性的表格，并对表格中的统计结果进行了理解和总结。为了便于我们后续的查阅和编辑，所有的表格和对结果的解读都被整合并输出为一个可下载的`Word`文档。

在方法部分，同样，我们要求其根据我们给出的关于方法的简要表述以及“`数据部分范本.docx`”的格式生成文本。由于前期的训练，这部分的文本生成效果也比较好。

### 四 数据的定量分析与结果解读部分

在对数据进行定量分析时，为确保数据的准确性和可靠性，我们首先对数据进行了前期的人工处理。这一过程涉及对原始数据的清洗、整合和筛选，确保数据的完整性和连续性。在数据处理的过程中，我们尤其注重数据的隐私保护和脱敏处理。为此，我们对数据中的敏感信息进行了深度脱敏，确保数据在后续的处理和分析中不会泄露任何个人或机构的私密信息。经过这一系列的处理，我们得到了几个关键变量，这些变量是后续分析的核心，也是我们利用ChatGPT进行深度分析的基础。

当我们将数据和关键变量上传到ChatGPT后，我们期待能够得到准确的数据分析结果。然而，实际的情况并不尽如人意。首先是部分模型出现了完美分离问题，这是因为我们的数据样本量不够大。完美分离是统计学中的一个常见问题，它意味着某个变量完全预测了结果变量的一个类别。这会导致模型估计不准确，甚至无法估计。为了解决这一问题，我们尝试将数据样本量扩大，但这只解决了基础模型估计时遇到的完美分离问题。

之后，我们遇到了第二个问题：ChatGPT对模型的理解有误。虽然ChatGPT能够理解基础的对数线性模型，但对于更加专业的对数线性模型，如对数线性层效应模型和对数乘法层效应模型，它显然有些力不从心。表现之一即对这些专业模型估计出来的模型参数都是一样的。这对我们的研究造成了很大的困扰，因为这些模型是我们研究的核心。

为了解决这一问题，我们决定上传更多关于这些专业模型的资料，帮助ChatGPT进行深度学习。我们选择了一本权威的英文教材和3篇相关的英文论文。经过反复训练，ChatGPT终于正确理解了这些复杂的模型，并能够准确地应用它们进行数据分析。
