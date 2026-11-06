pdf/1556521.pdf.

21. 温馨提示：此统计数据可能存在一些递项偏见，“求助现场”一般会在游戏初期使用，因为问题相对简单。尽管如此，认为团体的观点比个人的观点更准确，是有一定根据的。对此，请参阅詹姆斯·索罗维基《群体的智慧：如何做出最聪明的决策》(纽约：双日出版社，2004)，4页。

22. 网飞技术博客，https://medium.com/netflix-techblog/netflix-recommendations-beyond-the-5-stars-part-2-d9b96aa399f5.

23. Shih-ho Cheng, ‘Unboxing the random forest classifier: the threshold distributions’, Airbnb 工程与数据科学, https://medium.com/airbnb-engineering/unboxing-the-random-forest-classifier-the-threshold-distributions-22ea2bb58ea6.

24. Jon Kleinberg, Himabindu Lakkaraju, Jure Leskovec, Jens Ludwig and Sendhil Mullainathan, Human Decisions and Machine Predictions, 美国国家经济研究局研究报告服务，编号23180（Cambridge, MA: National Bureau of Economic Research, Feb. 2017）, http://www.nber.org/papers/w23180. 此项研究实际使用了“梯度增强版决策树”，与随机树相似。两者均合并了多种决策树的预测结果，最终形成决策，但梯度增强版的决策树是循序展开的，而随机决策树是平行展开的。为开展此项研究，数据集被一分为二。一半用于训练算法，另一半保留不用。算法就绪后，使用先前保留不用的另一半数据中的案例，来预测即将发生的事件（如果事先不分割数据，那么算法就会变成“查找表”）。