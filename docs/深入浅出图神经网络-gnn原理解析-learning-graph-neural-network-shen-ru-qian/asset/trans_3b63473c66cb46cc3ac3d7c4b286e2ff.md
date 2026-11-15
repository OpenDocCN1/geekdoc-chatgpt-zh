
### Scene Graph

A scene graph is a method for describing image semantics. It treats objects in an image as nodes and the mutual relationships between objects as edges, forming a graph. A scene graph can simplify an image with complex relationships into a semantic graph with explicit relationships. Scene graphs have powerful application scenarios, such as image synthesis, image semantic retrieval, and visual reasoning. Figure 1-10(e) shows an example of synthesizing a semantic image from a scene graph. In this scene graph, five objects are described: two men, a child, a frisbee, a courtyard, and the relationships among them. It can be seen that the scene graph has strong semantic representation capabilities.

### Circuit Design Diagram

We can abstract electronic components, such as resonators, as nodes, and the wiring between components as edges, thereby representing circuit design as a form of graph data. In reference [^([\[1\]](#chapter8.xhtml#ch1_back))], circuit design is abstracted in this way, as shown in Figure 1-10(f). Then, based on Graph Neural Network technology, the electromagnetic characteristics of the circuit are simulated and fitted. Compared to rigorous electromagnetic formula simulation, this can greatly accelerate the design of high-frequency circuits within an acceptable error range.

The application scenarios of graph data go far beyond these. There are also computation graphs describing the computational processes of neural networks, sensor array networks, and the Internet of Things composed of various smart sensors. In fact, if one wants to find a most representative data description language corresponding to real-world data, graphs should be the most competitive candidate. In summary, graph data applications have a wide span and diverse scenarios; researching graph data has broad and significant practical significance.

[\[1\]](#chapter8.xhtml#ch1) Zhang G, He H, Katabi D. Circuit-GNN: Graph Neural Networks for Distributed Circuit Design[C]//International Conference on Machine Learning. 2019: 7364-7373.

## 1.4 Deep Learning on Graph Data

As an important data type, the demand for analysis and learning of graph data is increasingly prominent. Many Graph Learning theories focus on task learning related to graph data. Spectral Graph Theory[^(\[2\])](../Text/chapter10.xhtml#aa-2) is a theory combining graph theory and linear algebra. Spectral clustering algorithms[^(\[3\])](../Text/chapter10.xhtml#aa-3) developed based on this theory can be used to solve graph partitioning or node clustering problems. Statistical Relational Learning (SRL)[^(\[4\])](../Text/chapter10.xhtml#aa-4) is a machine learning theory combining relational representation and likelihood representation. Unlike traditional machine learning algorithms that assume data is Independent and Identically Distributed (i.i.d., meaning data objects are of the same class and independent/uncorrelated), SRL breaks these two assumptions about data, making it a better fit for learning on graph data. To better fit heterogeneous graph data in real-world scenarios, Heterogeneous Information Network (HIN) analysis[^(\[5\])](../Text/chapter10.xhtml#aa-5) was proposed to mine more comprehensive structural information and rich semantic information in heterogeneous graphs. Due to the tremendous achievements of deep learning in practical application fields in recent years, the concepts of representation learning and end-to-end learning have gained increasing attention. To learn vectorized representations containing sufficient information from complex graph data, a large number of Network Embedding[^(\[6\])](../Text/chapter10.xhtml#aa-6) methods have emerged. However, network embedding struggles to provide an end-to-end system combining representation learning and task learning. Based on this, end-to-end learning systems for graph data remain an important research topic.

Due to the complexity of the structure of graph data itself, it is not intuitive to directly define a set of differentiable computational frameworks supporting it. Data corresponding to graph data include images, speech, and text. These data are regularized structural data defined in Euclidean space. The tensor computation system based on these data is relatively natural and efficient. Figure 1-11 gives a comparison between graph data and several other common data types. Image data exhibits a regular 2D grid structure, which corresponds well with the operating mechanism of Convolutional Neural Networks. Text data is a regular sequence data, and this sequential structure corresponds to the operating mechanism of Recurrent Neural Networks.

![](Images/1a11.jpg)

Figure 1-11　Image, Speech, and Text Data Types[^(\[1\])](#chapter9.xhtml#ch1_back)

Inspired by the definition of graph signal convolution filtering in Graph Signal Processing[^(\[7\])](../Text/chapter10.xhtml#aa-7), a neural network theory based on graph convolution operations and continuously derived has been developed in recent years. This book collectively refers to this class of methods as Graph Neural Networks (GNN[^(\[8\])](../Text/chapter10.xhtml#aa-8)[^(\[9\])](../Text/chapter10.xhtml#aa-9)[^(\[10\])](../Text/chapter10.xhtml#aa-10)). Below we briefly describe its development history.

In 2005, Marco Gori et al. published a paper[^(\[11\])](../Text/chapter10.xhtml#aa-11), proposing the concept of Graph Neural Networks for the first time. Before this, methods for processing graph data converted graphs into a set of vectors during the data preprocessing stage. The biggest problem with this processing method is that structural information in the graph may be lost, and the results obtained would heavily depend on the preprocessing of the graph. The proposal of GNNs was to enable the learning process to be directly architected on graph data.

Subsequently, in two papers in 2009[^(\[12\])](../Text/chapter10.xhtml#aa-12)[^(\[13\])](../Text/chapter10.xhtml#aa-13), they further elaborated on Graph Neural Networks and proposed a supervised learning method to train GNNs. However, these early studies all used an iterative approach, propagating neighbor information through Recurrent Neural Networks until a stable fixed state was reached to learn node representations. This computation method consumes significant resources, and related research began to focus on how to improve this method to reduce computational cost.

Around 2012, Convolutional Neural Networks began to achieve remarkable results in the vision field, prompting people to consider how to apply convolution to Graph Neural Networks. In 2013, Bruna et al. first introduced convolution into Graph Neural Networks. In reference [\[14\]](../Text/chapter10.xhtml#aa-14), a Graph Convolutional Network model was developed based on the concept of spectral convolution operations, applying learnable convolution operations to graph data for the first time. Since then, many have proposed improvements and extensions to this spectral-based graph convolutional neural network model. However, spectral convolution methods require processing the entire graph simultaneously during computation and bear the high time complexity of matrix decomposition, making it difficult to scale the learning system to large-scale graph data learning tasks. Therefore, spatial-based graph convolution was proposed and gradually became popular.

In 2016, Kipf et al.[^(\[15\])](../Text/chapter10.xhtml#aa-15) simplified the definition of spectral graph convolution, enabling graph convolution operations to be performed in the spatial domain. This greatly improved the computational efficiency of graph convolution models. Simultaneously, benefiting from the efficiency of convolution filtering, graph convolution models achieved remarkable results on multiple graph data-related tasks.

In recent years, more variants of neural network models based on spatial graph convolution[^(\[16\])](../Text/chapter10.xhtml#aa-16)[^(\[17\])](../Text/chapter10.xhtml#aa-17)[^(\[18\])](../Text/chapter10.xhtml#aa-18) have been developed. We collectively refer to this class of methods as GNNs. The emergence of various GNN models has greatly enhanced the adaptability of learning systems to various types of graph data, laying a solid foundation for task learning on various graph data.

Since then, graph data and deep learning have had their first truly meaningful combination. The emergence of GNNs realized end-to-end learning for graph data, providing a highly competitive learning solution for tasks in numerous application scenarios of graph data.

Below, we give a classification of graph data-related tasks.

### 1. Node-Level Tasks

Node-level tasks mainly include classification and regression tasks. Although these tasks predict properties at the node level, the model should obviously not be built on individual nodes alone; the relationships between nodes also need to be considered. There are many node-level tasks, including the academically common classification of paper nodes in citation networks, and in industry, user tag classification and malicious account detection in online social networks.

### 2. Link-Level Tasks

Link-level tasks mainly include edge classification and prediction tasks. Edge classification refers to predicting a certain property of an edge; link prediction refers to predicting whether an edge will form between two given nodes. A common application scenario is in social networks, where users are modeled as nodes and follow relationships between users as edges. Link prediction is used to implement social user recommendations. Currently, link-level tasks are mainly concentrated in recommendation business.

### 3. Graph-Level Tasks

Graph-level tasks do not rely on the attributes of a specific node or edge, but start from the overall structure of the graph to achieve classification, representation, and generation tasks. Currently, graph-level tasks are mainly applied in the field of natural science research, such as classification of drug molecules, classification of enzymes, etc.

[\[1\]](#chapter9.xhtml#ch1) Image source: http://helper.ipam.ucla.edu/publications/dlt2018/dlt2018_14506.pdf.

## 1.5 References

[\[1\]](../Text/chapter8.xhtml#a1)　Zhang G, He H, Katabi D. Circuit-GNN: Graph Neural Networks for Distributed Circuit Design[C]//International Conference on Machine Learning. 2019: 7364-7373.

[\[2\]](../Text/chapter9.xhtml#a2)　F.R. Chung. Spectral Graph Theory. American Mathematical Society, 1997.

[\[3\]](../Text/chapter9.xhtml#a3)　Von Luxburg U. A tutorial on spectral clustering[J]. Statistics and computing, 2007, 17(4): 395-416.

[\[4\]](../Text/chapter9.xhtml#a4)　Koller D, Friedman N, Džeroski S, et al. Introduction to statistical relational learning[M]. MIT press, 2007.

[\[5\]](../Text/chapter9.xhtml#a5)　Shi C, Li Y, Zhang J, et al. A survey of heterogeneous information network analysis[J]. IEEE Transactions on Knowledge and Data Engineering, 2016, 29(1): 17-37.

[\[6\]](../Text/chapter9.xhtml#a6)　Cui P, Wang X, Pei J, et al. A survey on network embedding[J]. IEEE Transactions on Knowledge and Data Engineering, 2018, 31(5): 833-852.

[\[7\]](../Text/chapter9.xhtml#a7)　Shuman D I, Narang S K, Frossard P, et al. The emerging field of signal processing on graphs: Extending high-dimensional data analysis to networks and other irregular domains[J]. IEEE signal processing magazine, 2013, 30(3): 83-98.

[\[8\]](../Text/chapter9.xhtml#a8)　Zhou J, Cui G, Zhang Z, et al. Graph neural networks: A review of methods and applications[J]. arXiv preprint arXiv:1812.08434, 2018.

[\[9\]](../Text/chapter9.xhtml#a9)　Zhang Z, Cui P, Zhu W. Deep learning on graphs: A survey[J]. arXiv preprint arXiv:1812.04202, 2018.

[\[10\]](../Text/chapter9.xhtml#a10)　Wu Z, Pan S, Chen F, et al. A comprehensive survey on graph neural networks[J]. arXiv preprint arXiv:1901.00596, 2019.

[\[11\]](../Text/chapter9.xhtml#a11)　Gori M, Monfardini G, Scarselli F. A new model for learning in graph domains[C]//Proceedings. 2005 IEEE International Joint Conference on Neural Networks, 2005. IEEE, 2005, 2: 729-734.

[\[12\]](../Text/chapter9.xhtml#a12)　Micheli A. Neural network for graphs: A contextual constructive approach[J]. IEEE Transactions on Neural Networks, 2009, 20(3): 498-511.

[\[13\]](../Text/chapter9.xhtml#a13)　Scarselli F, Gori M, Tsoi A C, et al. The graph neural network model[J]. IEEE Transactions on Neural Networks, 2008, 20(1): 61-80.

[\[14\]](../Text/chapter9.xhtml#a14)　Bruna J, Zaremba W, Szlam A, et al. Spectral networks and locally connected networks on graphs[J]. arXiv preprint arXiv:1312.6203, 2013.

[\[15\]](../Text/chapter9.xhtml#a15)　Kipf T N, Welling M. Semi-supervised classification with graph convolutional networks[J]. arXiv preprint arXiv:1609.02907, 2016.

[\[16\]](../Text/chapter9.xhtml#a16)　Hamilton W, Ying Z, Leskovec J. Inductive representation learning on large graphs[C]//Advances in Neural Information Processing Systems. 2017: 1024-1034.

[\[17\]](../Text/chapter9.xhtml#a17)　Veličković P, Cucurull G, Casanova A, et al. Graph attention networks[J]. arXiv preprint arXiv:1710.10903, 2017.

[\[18\]](../Text/chapter9.xhtml#a18)　Gilmer J, Schoenholz S S, Riley P F, et al. Neural message passing for quantum chemistry[C]//Proceedings of the 34th International Conference on Machine Learning-Volume 70. JMLR.org, 2017: 1263-1272.

[\[19\]](../Text/chapter8.xhtml#a19)　Johnson J, Gupta A, Fei-Fei L. Image generation from scene graphs[C]//Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition. 2018: 1219-1228.

# Chapter 2 Neural Network Fundamentals

In recent years, with the improvement of algorithms, the explosion of data volume, and the enhancement of computing power, machine learning and deep learning have developed rapidly. This chapter starts from the basic concepts of machine learning, introducing what machine learning is and how to obtain machine learning models. Then it introduces the basic building units of deep learning models—the neuron model and the Multi-Layer Perceptron (MLP) model built upon it. We will also take the Multi-Layer Perceptron as an example to introduce the backpropagation algorithm and optimization methods in deep learning.

## 2.1 Basic Concepts of Machine Learning

### 2.1.1 Classification of Machine Learning

Machine learning is an interdisciplinary field involving probability theory, statistics, approximation theory, convex analysis, computational complexity theory, and many other disciplines. Machine learning theory mainly focuses on designing and analyzing algorithms that allow computers to automatically "learn". In layman's terms, machine learning enables computers to mine valuable information from data.

From different dimensions, machine learning can have different classifications. Below we briefly introduce several common classification methods.

Based on whether training data has labels, machine learning can be divided into **Supervised Learning**, **Semi-Supervised Learning**, and **Unsupervised Learning**.

**Supervised Learning**: Refers to the scenario where every sample in the training data has a label. Labels guide the model training to learn discriminative features, thereby enabling prediction on unknown samples. For example, in the ImageNet image classification competition, a model is trained using existing labels for each image, allowing the model to predict unknown images and obtain corresponding classification results.

**Unsupervised Learning**: Refers to the scenario where training data has no labels at all. Algorithms discover constraint relationships between data, such as associations, distance relationships, etc. A typical unsupervised algorithm is clustering, which groups samples with similar "distances" together based on certain metrics.

**Semi-Supervised Learning**: Refers to a learning method between supervised and unsupervised learning. Its training data contains both labeled and unlabeled data. Assuming labeled and unlabeled data are sampled from the same distribution, the unlabeled data contains some information related to the data distribution, which can serve as a supplement to labeled data. This situation is very common in reality. For example, in the internet industry, massive amounts of data are generated daily. Some of this data may carry labels, but more data is unlabeled. If manual labeling is relied upon for these unlabeled data, the cost is considerable, while semi-supervised learning can provide some solutions.

From the perspective of algorithm output form, it can be divided into classification problems and regression problems, both of which belong to the category of supervised learning.
