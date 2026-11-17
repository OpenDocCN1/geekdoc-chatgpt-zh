

## 4.4 Unsupervised Learning and Clustering

Currently, a common scenario for unsupervised learning is clustering. Clustering is the process of dividing data into multiple categories based on the principle of data similarity. To evaluate the "similarity" between two different samples, the usual method is to calculate the "distance" between the two samples. Of course, there are many methods for calculating distance, and the choice of method directly affects the quality of the clustering results. Common distance calculation methods include Euclidean distance, Manhattan distance, Mahalanobis distance, etc.

### 1. Euclidean Distance

Euclidean distance is the most commonly used and easiest-to-understand distance measurement method, i.e., the distance between two points, as shown in Figure 4-30.

![](converter-master/example/165201/content_633682_0.jpg)

Figure 4-30

### 2. Manhattan Distance

Manhattan distance is also known as "city block distance". Just like calculating the distance between two points in various map applications, it needs to be calculated based on the direction of the roads, as shown in Figure 4-31.

![](converter-master/example/165201/content_633682_1.jpg)

Figure 4-31

### 3. Mahalanobis Distance

Mahalanobis distance represents the covariance distance of data, a scale-independent measurement method. Mahalanobis distance first standardizes the attributes of sample points, then calculates the distance between samples, as shown in Figure 4-32.

![](converter-master/example/165201/content_633682_2.jpg)

Figure 4-32

### 4. Other Distance Measurement Methods

In addition to the above methods, there are other distance measurement methods such as angle and cosine. In unsupervised learning, there are no known label values to train the model, but algorithms can still be used to find similarities between data, thereby grouping the data to form clusters.

Assume there is a dataset containing article information from various government websites nationwide, and similar articles need to be automatically grouped together. We first select a most commonly used clustering method, namely `K`-means clustering. The key to understanding `K`-means clustering is to remember that data consists of numerous data rows, each with different features. Assuming each feature is a numerical value, they can be plotted as coordinates.

First, plot features on a two-dimensional grid based on vector positive/negative tendency (0–6) and the number of typos (0–7), as shown in Figure 4-33.

![](converter-master/example/165201/content_633682_3.jpg)

Figure 4-33

Second, determine how many clusters need to be created. The number of clusters is generally called `K`. `K` points can be plotted at random positions; these points represent the centroids of different clusters. In this case, `K` is 3, so we need to create 3 clusters. Next, we determine which centroid each point in the grid is closest to, and assign these points to the 3 clusters accordingly, as shown in Figure 4-34.

![](converter-master/example/165201/content_633682_4.jpg)

Figure 4-34

Third, move the 3 predefined centroids to the true center positions of these 3 clusters, as shown in Figure 4-35.

![](converter-master/example/165201/content_633682_5.jpg)

Figure 4-35

Finally, reassign all points to clusters based on the nearest centroid. This process is then repeated continuously, eventually producing clusters with good separation effects.

In experiments, each cluster should be as independent as possible, far away from other clusters. To measure this, we can compare the average distance between cluster centroids, as well as the average distance from each point in a cluster to its cluster centroid, as shown in Figure 4-36. The greater the distance of each point from the centroid, the greater the degree of cluster separation. We can also compare the average distance between cluster centroids with the distance from the cluster centroid to the farthest point.

![](converter-master/example/165201/content_633682_6.jpg)

Figure 4-36

In addition, Principal Component Analysis (PCA) can be used to evaluate the results of clustering algorithms. Using this method, each point in a cluster can be decomposed into different directions. For example, the first two components decomposed by `PCA` can be plotted as an ellipse, as shown in Figure 4-37. The first principal component is plotted along the direction of maximum variance, i.e., the major axis of the ellipse.

![](converter-master/example/165201/content_633682_7.jpg)

Figure 4-37

The second principal component is plotted along the minor axis of the ellipse, as shown in Figure 4-38.

![](converter-master/example/165201/content_633682_8.jpg)

Figure 4-38

A cluster completely well-separated from the first cluster should also be elliptical, with its major axis perpendicular to the major axis of the first cluster's ellipse. If a cluster is basically separated from the first cluster but not completely, we would get an ellipse whose major axis is not completely perpendicular to the first ellipse's major axis.

```
EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==
```

## 4.5 Deep Learning

### 4.5.1 Neurological Basis of Deep Learning

Deep learning is a branch of machine learning, first proposed by Hinton et al. in 2006. Deep learning designs artificial neural networks by simulating the human brain's neural network and processes information by mimicking the transmission between brain neurons. Research by biologists and neuroanatomists has found that the human brain generally includes about 100 billion neurons (nerve cells), each connected to other neurons through up to 10,000 synapses, thereby forming a large-scale, complex human brain neural network capable of parallel processing, as shown in Figure 4-39.

![](converter-master/example/165201/content_633683_0.jpg)

Figure 4-39

The design of artificial neural networks references the human brain neural network. An artificial neural network is a parallel and distributed information processing network structure with a large number of connections. This network structure generally consists of multiple neurons; moreover, each neuron has a single output, each neuron can connect to multiple other neurons, each neuron's input has multiple connection pathways, and each connection pathway corresponds to a connection weight coefficient. Artificial neural networks are very similar to human brain neural networks but not identical. When analyzing artificial neural networks, four aspects must be considered: system structure, signal form, information storage, and information processing mechanism. A qualified artificial neural network must first have an associative memory function, able to associate through known information and store associated information. For example, seeing a bungalow lets one know it is a house; seeing a two-story villa also lets one know it is a house; seeing a partially obscured house, one can still recognize it as a house.

Through artificial neural networks, information classification can also be performed. Traditional classification methods are linear classification methods, but artificial neural networks can find segmentation regions that meet classification requirements from input samples, with samples in each region belonging to one category. Traditional classification methods are only suitable for solving recognition and classification problems where similar classes gather and dissimilar classes separate. However, in the objective world, the segmentation surfaces on many sample spaces are very complex and non-linear; similar samples may belong to different categories, while distant samples may belong to the same category. Artificial neural networks can well solve the problem of approximating non-linear surfaces. Therefore, artificial neural networks have better recognition and classification capabilities than traditional classification methods. Figure 4-40(a) shows traditional machine learning classification; Figure 4-40(b) shows artificial neural network classification.

![](converter-master/example/165201/content_633683_1.jpg)

Figure 4-40

Since artificial neural networks operate by simulating the human brain neural network, evaluating the overall performance of an artificial neural network requires assessment of three aspects: the information processing capability of the nodes themselves, the connections between nodes, and the connection strength between nodes. The node's own information processing capability examines whether the mathematical model is reasonable; the connections and connection strength between nodes examine whether the network topology is reasonable. Since the proposal of artificial neural networks to the present, there have been dozens of models. According to different classification methods, artificial neural networks can be divided into different categories, among which two common classification methods are classification by network connection topology and classification by information flow direction within the network.

Based on different network connection topologies, artificial neural networks can be divided into two major categories: layered structure artificial neural networks and interconnected mesh structure artificial neural networks. Layered structure artificial neural networks divide neurons into several layers by function, including the Input Layer, Hidden Layer, and Output Layer, which are sequentially connected, as shown in Figure 4-41.

![](converter-master/example/165201/content_633683_2.jpg)

Figure 4-41

In interconnected mesh structure artificial neural networks, there may be connection paths between any two nodes, forming a neural net, as shown in Figure 4-42.

![](converter-master/example/165201/content_633683_3.jpg)

Figure 4-42

Based on the information flow direction within the network, artificial neural networks can be divided into feedforward artificial neural networks and feedback artificial neural networks. The structural characteristics of feedforward artificial neural networks are exactly the same as the layered structure artificial neural network shown in Figure 4-41, named because the feedforward process executes in the direction of network information processing, with information passed layer by layer from the input layer backward. From the perspective of information processing capability, nodes in feedforward artificial neural networks can be divided into two types: one is input nodes, responsible only for introducing information from the outside world and passing it to the next layer; the other is nodes with processing capabilities, including various hidden layers and the output layer. In feedforward artificial neural networks, the output of the previous layer is the input of the next layer; information processing has a directional layer-by-layer transmission characteristic, and generally, there are no reverse feedback loops. Therefore, this type of artificial neural network is easily cascaded to establish multi-layer feedforward artificial neural networks.

Feedback artificial neural networks are a type of artificial neural network with feedback connections from output to input. In feedback artificial neural networks, input layer neurons can not only receive input information but also have information processing functions, as shown in Figure 4-43.

![](converter-master/example/165201/content_633683_4.jpg)

Figure 4-43

### 4.5.2 Characteristics of Artificial Neural Networks

Artificial neural networks possess self-learning and self-adaptive functions, good fault tolerance, non-linear mapping capabilities, large-scale parallel processing capabilities, and associative processing capabilities. The characteristics and advantages of artificial neural networks are mainly reflected in the following aspects.

**Self-learning and Self-adaptive Functions**: Artificial neural networks well simulate the human brain's acquired learning and training functions. If the final output is incorrect, the artificial neural network can adjust the weight of each input to produce a new result, generally implemented through training algorithms. Due to the complexity of the training process, the final output result can be obtained by repeatedly inputting data and adjusting weights. Therefore, artificial neural networks can find the intrinsic connections between input and output through learning and training to obtain the answer to the problem, rather than relying solely on relevant historical knowledge and rules.

**Fault Tolerance**: The brain has strong fault tolerance, and so do artificial neural networks. Artificial neural networks store results in the entire system, not in a specific storage unit. Even if a certain proportion of nodes do not participate in the operation, it will not have a significant impact on the overall system performance, and it can still process noisy or incomplete data, possessing good fault tolerance.

**Non-linear Mapping Capability**: Many machine learning algorithms assume that input values are linearly separable, i.e., a hyperplane can be represented by a function whose first derivative is constant. However, in the real world, problems are generally complex, mostly non-linear, and various factors involved influence each other. Therefore, artificial neural networks need to be able to simulate arbitrarily complex non-linear mapping capabilities by increasing the number of neurons in hidden layers.

**Parallel Processing Capability**: Artificial neural networks feature structural parallelism, parallel processing in processing sequence, and computational functions distributed across multiple processing units. Therefore, hardware-implemented artificial neural networks process at speeds far higher than traditional software, with good fault tolerance and fast overall processing capabilities, particularly suitable for real-time control and dynamic control.

**Associative Memory Function**: Artificial neural networks can accept and process analog, chaotic, fuzzy, and random information, and have advantages in natural language understanding, image pattern recognition, scene understanding, incomplete information processing, intelligent robot control, etc.

### 4.5.3 Typical Artificial Neural Networks

The development of artificial neural networks is closely linked to the development process of artificial intelligence technology. Along with the ups and downs of AI technology, artificial neural networks have also experienced periods of prosperity and troughs. Since being proposed in 1943, artificial neural networks have risen and fallen several times, developing in a wave-like pattern.

The earliest artificial neural network was the "Perceptron" proposed by Cornell University professor Frank Rosenblatt in 1957. This was the first artificial neural network precisely defined by an algorithm and possessing self-organizing learning ability, the ancestor of many subsequent new artificial neural network models.

Subsequently, the `Single Layer Perceptron` evolved into the `BP` (Back Propagation) artificial neural network and the `Hopfield` artificial neural network, and the `BP` artificial neural network further evolved into Recurrent Neural Networks. Since there are many types of artificial neural networks, the author briefly summarizes the evolution of several common ones, as shown in Figure 4-44.

![](converter-master/example/165201/content_633683_5.jpg)

Figure 4-44

#### 1. Single Layer Perceptron

The `Single Layer Perceptron` is the simplest type of artificial neural network. It contains only two layers: an input layer and an output layer, which are directly connected. The overall structure is shown in Figure 4-45.

![](converter-master/example/165201/content_633683_6.jpg)

Figure 4-45

After data is input through the input layer, it enters the output layer directly through calculation, see Formula (4-6):

![](converter-master/example/165201/content_633683_7.jpg)

(4-6)

First, an `N`-dimensional vector `i = [i[j], i[j+1], ..., i[n]]` is input, where each component corresponds to a weight `W[j]`. The weights are multiplied, then these products are summed to obtain the sum of products. If the sum of products is greater than the threshold (usually 0), the output is 1; if less than the threshold, the output is -1.

Therefore, the `Single Layer Perceptron` can classify linearly separable data. If `Single Layer Perceptrons` are stacked into a Multi-Layer Perceptron, it theoretically possesses the ability to handle non-linear classification problems, leading to the emergence of various multi-layer artificial neural networks. Multi-layer artificial neural networks can simulate arbitrarily complex functions by adjusting the number of inputs and increasing the number of hidden layers, to solve various complex problems.

#### 2. Hopfield Artificial Neural Network

The `Hopfield Artificial Neural Network` arranges all neurons in a row. Each layer has an input, and each input has an input value (`x[1]`, `x[2]`, `x[3]`, `x[4]`), as shown in Figure 4-46. Unlike the `Single Layer Perceptron`, the output result is sent back to `x[2]`, `x[3]` via a feedback loop, but not to `x[1]`; `x[1]` can treat the weight of feedback to itself as 0.

![](converter-master/example/165201/content_633683_8.jpg)

Figure 4-46

If every neuron in the `Hopfield Artificial Neural Network` is interconnected, it can also take the form of a loop, as shown in Figure 4-47.

![](converter-master/example/165201/content_633683_9.jpg)

Figure 4-47

Precisely because each output result can be passed to other neurons, the `Hopfield Artificial Neural Network` can be used to implement associative memory functions. Therefore, in a `Hopfield Artificial Neural Network`, even if some units in the network are removed, the entire network can still function normally. This is like seeing dinosaur fossil fragments in a natural history museum; even with just a scapula or two parietal bones, experts can basically reconstruct the dinosaur's skeleton from these simple bones.

Thus, the stable state of the `Hopfield Artificial Neural Network` represents a memory pattern, and the process of the initial state converging toward the stable state is the process of the `Hopfield Artificial Neural Network` searching for the memory pattern. The initial state can be regarded as partial information of the memory pattern, and the network evolution can be regarded as the process of recalling the whole information from partial information.

#### 3. Boltzmann Machine

A stochastic `Hopfield Artificial Neural Network` with hidden units is called a `Boltzmann Machine`. The principle of the `Boltzmann Machine` originates from statistical physics; it is a modeling method based on an energy function and can be used to construct a probability model for a set of binary data vectors. In addition, the `Boltzmann Machine` can describe high-order interactions between variables; the constructed models and learning algorithms have relatively complete physical explanations and rigorous mathematical statistical theoretical foundations, but their learning algorithms are very complex and belong to unsupervised learning algorithms. The `Boltzmann Machine` consists of a visible layer and multiple hidden layers, as shown in Figure 4-48. Nodes in the network are also divided into visible units and hidden units, using visible and hidden units to express the learning models of stochastic networks and stochastic environments, and using weights to express the correlations between units.

![](converter-master/example/165201/content_633683_10.jpg)

Figure 4-48

#### 4. BP Artificial Neural Network

The `BP` (Back Propagation) Artificial Neural Network is a multi-layer feedforward artificial neural network trained using the error backpropagation algorithm, and is one of the most widely used artificial neural networks currently. In practical applications of artificial neural networks, the vast majority of models adopt the `BP` artificial neural network and its variants. The `BP` artificial neural network is also the core of feedforward artificial neural networks, embodying the essence of artificial neural networks; therefore, when learning artificial neural networks, one must understand the `BP` artificial neural network.

As the earliest artificial neural network, the `Single Layer Perceptron` has advantages such as a clear model, simple structure, and small computation. However, as research deepened, it was found that it could only solve linearly separable problems, limiting its application. In the mid-1980s, experts introduced the Error Back Propagation Training algorithm, systematically solving the learning problem of hidden layers in multi-layer artificial neural networks and providing a complete mathematical derivation. The `BP` artificial neural network possesses arbitrary complex pattern classification capabilities and excellent multi-dimensional function mapping capabilities, solving problems the `Single Layer Perceptron` could not.

Structurally, the `BP` artificial neural network has an input layer, hidden layer(s), and an output layer, as shown in Figure 4-49. Essentially, the `BP` algorithm takes the square of the network error as the objective function and uses the gradient descent method to calculate the minimum value of the objective function. The main characteristic of the `BP` artificial neural network is that signals propagate forward, while errors propagate backward.

The operation process of the `BP` artificial neural network is mainly divided into two stages: the first stage is the forward propagation of signals, from the input layer through the hidden layer to the output layer; the second stage is the backward propagation of errors, from the output layer to the hidden layer, and finally to the input layer, sequentially adjusting the weights and biases from the hidden layer to the output layer, and the weights and biases from the input layer to the hidden layer.

![](converter-master/example/165201/content_633683_11.jpg)

Figure 4-49

The `BP` artificial neural network is relatively mature in theory, performance, and application. Its outstanding advantages are strong non-linear mapping capability, flexible network structure, and the number of hidden layers and neurons in each layer can be arbitrarily set according to specific situations. However, the `BP` artificial neural network also has the following defects.

(1) Slow learning speed; even for a simple problem, it generally requires hundreds or even thousands of learning iterations to converge.

(2) Prone to falling into local minima.

(3) Lack of theoretical guidance for selecting the number of network layers and neurons.

(4) Limited network generalization capability.

#### 5. Convolutional Neural Network

The `Convolutional Neural Network` (CNN) belongs to a type of feedforward artificial neural network. Currently, `CNN` has become a research hotspot in many scientific fields, especially in pattern classification. Because this network avoids complex pre-processing of images and can directly input raw images, it has gained wider application. The development of `CNN` was not achieved overnight; as early as 1980, the first implemented network of the newly proposed recognizer was the first implementation of `CNN`. Subsequently, more researchers improved the network. Among them, a representative achievement is the "Neocognitron" proposed by Alexander and Taylor (Note: Original text says Alexander and Taylor, historically Fukushima proposed Neocognitron, but translating source text faithfully), marking the maturity of the `Convolutional Neural Network`.

Generally, the basic structure of `CNN` includes two layers: the first layer is the feature extraction layer, where each neuron's input is locally connected to the previous layer and extracts features of that locality. Once the local features are extracted, the positional relationships between them and other features are also determined. The second layer is the feature mapping layer; each computational layer of `CNN` consists of multiple feature maps, each feature map is a plane, and the weights of all neurons on each plane are equal.

