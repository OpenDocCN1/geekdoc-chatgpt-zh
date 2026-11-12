
目和覆盖面积进行加权综合。

- 13.2 编程实践，略。

- 13.3 较早地搜到更好（差）的子结点会直接最大（最小）化当前节点的估值，也就能更早地满足Alpha-Beta剪枝的触发条件。由于我们按照深度优先的顺序进行搜索，一旦在比较浅的层数剪掉了一个节点，最后总的搜索数目将会有指数级的减少。

- 13.4 首先要明确，置换表的不精确来自于哈希函数可能的冲突。假如我们对冲突置之不理，就可能使得某些局面从置换表中索引到错误的估值，进而可能影响搜索算法的剪枝效率。因此，需要在处理冲突和搜索效率间寻找平衡。通常情况下，这个平衡会允许置换表有一定的不精确性。

- 13.5 蒙特卡罗树搜索算法所扩展的搜索树是极不平衡的，模拟胜率较高的结点被扩展得更深。这是置信上界选择的结果，在文中已有详细说明。

- 13.6 编程实践，略。

- 15.1 随着由γ变小，智能体逐渐变得“贪心短视”，更注重于短期利益；反之当增大时，它会趋于深谋远虑，更多考虑未来的期望收益。

- 16.1 探索就是鼓励智能体去尝试不熟悉的动作。因此我们可以记录所有动作被采取的次数，综合考虑待选动作的Q值和被采取次数，其中待选动作的Q值越高或是被采取的次数占比越低，越容易被智能体选择，这叫做置信上界（Upper Confidence Bound）方法。另外，还可以将所有的Q值函数初始化为一个很大的数值，这样智能体起初会因为过高的初始Q值而遍历尝试所有的动作，随后尝试过的动作的价值会被更新为合理值，这叫做乐观初始值方法。

- 16.2 以游戏为例，游戏的规则是智能体的环境模型，比如说会下围棋的AlphaGo就是基于模型的强化学习的典型实例。

## 附录二 参考文献

- [1] LeCun, Yann, Corinna Cortes, and C. J. Burges. “MNIST handwritten digit database.” AT&T Labs [Online]. Available: http://yann.lecun.com/exdb/mnist 2 (2010): 18.

- [2] Deng, Jia, et al. “Imagenet: A large–scale hierarchical image database.” 2009 IEEE conference on computer vision and pattern recognition. IEEE, 2009.

- [3] Krizhevsky, Alex, Ilya Sutskever, and Geoffrey E. Hinton. “Imagenet classification with deep convolutional neural networks.” Advances in neural information processing systems. 2012.

- [4] Simonyan, Karen, and Andrew Zisserman. “Very deep convolutional networks for large–scale image recognition.” arXiv preprint arXiv:1409.1556 (2014).

- [5] Taigman, Yaniv, et al. “Deepface: Closing the gap to human–level performance in face verification.” Proceedings of the IEEE conference on computer vision and pattern recognition. 2014.

- [6] Sun, Yi, Xiaogang Wang, and Xiaoou Tang. “Deep learning face representation from predicting 10,000 classes.” Proceedings of the IEEE conference on computer vision and pattern recognition. 2014.

- [7] Silver, David, et al. “Mastering the game of Go with deep neural networks and tree search.” Nature 529.7587 (2016): 484.

- [8] Gatys, Leon A, Alexander S. Ecker, and Matthias Bethge. “A neural algorithm of artistic style.” arXiv preprint arXiv:1508.06576 (2015).

- [9] Zeiler, Matthew D, and Rob Fergus. “Visualizing and understanding convolutional networks.” European conference on computer vision. Springer, Cham, 2014.

- [10] Zhu, Jun-Yan, et al. “Unpaired image–to–image translation using cycle–consistent adversarial networks.” Proceedings of the IEEE international conference on computer vision. 2017.

- [11] He, Kaiming, et al. “Deep residual learning for image recognition.” Proceedings of the IEEE conference on computer vision and pattern recognition. 2016.

- [12] Sutskever, Ilya, Oriol Vinyals, and Quoc V. Le. “Sequence to sequence learning with neural networks.” Advances in neural information processing systems. 2014.

- [13] Hochreiter, Sepp, and Jürgen Schmidhuber. “Long short–term memory.” Neural computation 9.8 (1997): 1735–1780.

- [14] Mikolov, Tomas, et al. “Distributed representations of words and phrases and their compositionality.” Advances in neural information processing systems. 2013.

- [15] Schwalbe, Ulrich, and Paul Walker. “Zermelo and the early history of game theory.” Games and economic behavior 34.1 (2001): 123–137.

- [16] Zhang, Huichu, et al. “CityFlow: A Multi-Agent Reinforcement Learning Environment for Large Scale City Traffic Scenario.” The World Wide Web Conference. ACM, 2019.

- [17] Zaidi, Ali A., Bart Kulcsar, and Henk Wymeersch. “Back-pressure traffic signal control with fixed and adaptive routing for urban vehicular networks.” IEEE Transactions on Intelligent Transportation Systems 17.8 (2016): 2134–2143.

- [18] Liang, Xiaoyuan, et al. “Deep reinforcement learning for traffic light control in vehicular networks.” arXiv preprint arXiv:1803.11115 (2018).

- [19] Mnih, Volodymyr, et al. “Human-level control through deep reinforcement learning.” Nature 518.7540 (2015): 529.

- [20] Wei, Hua, et al. “Intellilight: A reinforcement learning approach for intelligent traffic light control.” Proceedings of the 24th ACM SIGKDD International Conference on Knowledge Discovery & Data Mining. ACM, 2018.

联系电话：18183299177

联系邮箱：ugcn@qq.com

联系网址：www.jiaokey.com

![](img/9b291315612c9c478ad54afa29381fce_192_0.png)

微信：
