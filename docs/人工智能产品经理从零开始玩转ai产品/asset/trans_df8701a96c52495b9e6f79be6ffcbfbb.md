

## 4.4 Common Deep Neural Network Architectures

### 4.4.1 Convolutional Artificial Neural Networks (CNN)

They are called Convolutional Artificial Neural Networks because these networks utilize convolution operations. A convolution operation is a computational method, similar to arithmetic operations such as addition, subtraction, multiplication, division, square root, and squaring. Through convolution operations, `CNN` can extract basic features of images; artificial neural networks based on convolution operations continuously enhance the network's recognition capability. In 2012, the `ImageNet` challenge champion `Alex Net` achieved image recognition with an accuracy exceeding 84% using 5 `convolutional layers` and 3 `fully connected layers`, as shown in Figure 4-50. By 2018, the accuracy of image recognition using convolutional artificial neural networks had exceeded 97%.

![](converter-master/example/165201/content_633683_12.jpg)

Figure 4-50

`CNN` generally includes the following parts: `Input Layer`, `Convolutional Layer`, `Pooling Layer`, `Fully Connected Layer`, and `Output Layer`.

The `Convolutional Layer` in `CNN` applies convolution operations to the original image or features from the previous layer. To obtain specific features from different layers, `CNN` often employs different `convolution kernels` for convolution operations. A single `convolution kernel` yields a 3rd-order tensor with 1 channel; multiple `convolution kernels` yield multiple 3rd-order tensors. Combining the vectors of these 3rd-order tensors produces a feature map, which is the final output of the `Convolutional Layer`.

During convolution operations, the `convolution kernel` slides to compute image features. However, if the image or feature map is large, the computational pressure becomes significant. To reduce computational load, a `Pooling Layer` is typically introduced to lower the resolution of the feature map.

The pooling process includes: ① Separating the feature map by channels to obtain several matrices; ② Dividing each matrix into equal-sized squares; ③ Taking a value from each block (maximum or average value) to obtain a new matrix; ④ Stacking the new matrices in the original order to form a 3rd-order tensor.

The resulting 3rd-order tensor is the `Pooling Layer`. If the maximum value is taken, it is a Max `Pooling Layer`; if the average value is taken, it is an Average `Pooling Layer`. The resolution of the feature map after the `Pooling Layer` is greatly reduced, thereby reducing computational load. Figure 4-51 shows the input features, and Figure 4-52 shows the output features.

![](converter-master/example/165201/content_633683_13.jpg)

Figure 4-51

![](converter-master/example/165201/content_633683_14.jpg)

Figure 4-52

Through the `Fully Connected Layer`, the feature map can be transformed into a feature vector. In the `Fully Connected Layer`, several vectors of the same dimension perform inner product operations with the input vector, and all results are concatenated into a single output vector. If `X` is the input, and `K` parameter vectors `W` of the same dimension perform inner products with `X`, plus a scalar `b`, the output vector is `Y = XW + b`, and the `K` scalar results form the output.

The results obtained through the `Fully Connected Layer` cannot directly determine the image category. To obtain classification probabilities for the image, a Normalized Exponential Layer (`Softmax Layer`) is needed to compress the multi-dimensional vector into another `K`-dimensional real vector where each element ranges from 0 to 1 and the sum of all elements is 1, yielding the final result.

### 4.4.2 Recurrent Artificial Neural Networks (RNN)

Recurrent Artificial Neural Networks (`RNN`) are a collective term for two types of artificial neural networks: one is the Recurrent Neural Network (time-recurrent); the other is the Recursive Neural Network (structure-recurrent). The time-recurrent neural network forms a directed graph via connections between neurons, while the structure-recurrent neural network recursively constructs more complex deep networks using similar neural network structures. Because `RNN` possesses a feedback dynamical system, as shown in Figure 4-53, it exhibits stronger dynamic behavior and computational capability than feedforward artificial neural networks. Therefore, recurrent artificial neural networks have become one of the important research objects for international neural network experts.

![](converter-master/example/165201/content_633683_15.jpg)

Figure 4-53

The need for `RNN` arises because some artificial neural networks can only process a single input independently, and the previous input has absolutely no relation to the next. However, certain tasks require the neural network to better process sequential information—i.e., the previous input is related to the next. For example, when understanding a sentence, understanding each word in isolation is insufficient; we need to process the entire sequence of connected words. When processing video, we cannot analyze each frame in isolation but must analyze the entire sequence of connected frames. `RNN` can utilize its internal memory to process input sequences of arbitrary length, making it easier to handle unsegmented handwriting recognition, speech recognition, etc.

### 4.4.3 Generative Adversarial Networks (GAN)

Generative Adversarial Networks (`GAN`) are currently one of the hottest artificial neural networks. In 2018, *MIT Technology Review* listed `GAN` among the top ten breakthrough technologies. The main inspiration for `GAN` comes from the concept of zero-sum games in game theory. `GAN` consists of a Generative Network `G` (Generator Network) and a Discriminative Network `D` (Discriminator Network), which work by mutually competing against each other. The generative network generates data, while the discriminative network judges whether the data generated by the generative network is correct. Through continuous competition, the performance of both networks improves. The basic logic of `GAN` is shown in Figure 4-54.

![](converter-master/example/165201/content_633683_16.jpg)

Figure 4-54

The emergence of `GAN` is highly significant. Previously, all artificial neural networks required humans to prepare data before training. After `GAN`, humans no longer need to prepare data; machines can automatically complete data preparation. At the 2018 top-tier AI conference `IJCAI`, top-tier machine learning conferences `ICML` and `NIPS`, and the renowned deep learning conference `ICLR`, there were extensive traces of `GAN` research.

Taking image generation as an example, the `GAN` generative network receives a random noise `z` (random number) and generates an image from this noise. The discriminative network judges whether the generated image is "real." Its input parameter is `x`, where `x` represents an image, and the output `D(x)` represents the probability that `x` is a real image. An output of 1 means 100% real, while 0 means impossible to be real. During training, the generative network aims to generate real images to fool the discriminative network, while the discriminative network aims to distinguish fake images generated by the generative network from real ones. Thus, the *generative* network and *discriminative* network form a dynamic "game process," with the final equilibrium point being the Nash equilibrium, as shown in Figure 4-55.

![](converter-master/example/165201/content_633683_17.jpg)

Figure 4-55

### 4.4.4 Capsule Artificial Neural Networks (CapsNet)

Capsule Artificial Neural Networks (`CapsNet`) are also currently one of the hottest artificial neural networks. Proposed by Hinton in October 2017, Hinton named them Capsules. Like `CNN`, Capsule Artificial Neural Networks were initially used to solve visual problems. The reason for proposing Capsule Networks is that Hinton discovered `CNN` has some intractable problems in image recognition. `CNN` obtains feature information of image components through convolution operations, but `CNN` has weak ability to judge the associative relationships between components. For example, a person has eyes, a nose, and a mouth; if these components exist, `CNN` judges it as a face. But if the nose is on the forehead and eyes are next to the mouth, `CNN` might still judge it as a face. In reality, a face should not only have two eyes, a mouth, and a nose; the associative relationships between components are also crucial for image recognition. Additionally, `CNN` struggles when images are translated or rotated. Figure 4-56 shows several letter 'A's; humans easily recognize them all as 'A', but `CNN` considers them different letters.

![](converter-master/example/165201/content_633683_18.jpg)

Figure 4-56

Hinton believes the biggest difference between humans and `CNN` is that humans recognize images by abstracting data in a tree-like structure, while `CNN` abstracts data in a hierarchical structure. `CNN` pursues invariance and stability, while Capsule Networks pursue variance and difference. Capsule Artificial Neural Networks can form information abstractions of the same image from different viewpoints, rather than simply abstracting image information from a fixed viewpoint.

Precisely because Capsule Artificial Neural Networks can flip, translate, and combine features of images, they greatly reduce data annotation work, allowing the Capsule Network to perform learning and abstraction on its own.

Similar to the deep networks possessed by `CNN`, Capsule Artificial Neural Networks also have a hierarchical structure, as shown in Figure 4-57. Capsules at different levels process different information; lower-level capsules train detailed features in the image, while higher-level capsules train the mutual relationships between detailed features and synthesize the pose intensities given by each capsule to ultimately reconstruct the image.

![](converter-master/example/165201/content_633683_19.jpg)

Figure 4-57

Capsule Artificial Neural Networks are a disruptive network architecture with tremendous influence in recent years because they transform input scalars into a set of vectors. Currently, Capsule Artificial Neural Networks have achieved state-of-the-art accuracy on the `MNIST` dataset. Because capsules in Capsule Networks can realize the association of feature information position and rotation, Capsule Networks can surpass traditional `CNN` results trained on large data volumes using less training data.

## 4.5 Deep Learning

### 4.5.1 Neurological Basis of Deep Learning

Deep learning is a branch of machine learning, first proposed by Hinton et al. in 2006. Deep learning designs artificial neural networks by simulating the human brain's neural network and processes information by mimicking the transmission between brain neurons. Biological and neuroanatomical researchers have found that the human brain generally contains about 100 billion neurons (nerve cells), each connected to up to 10,000 other neurons via synapses, forming a massive, complex brain neural network capable of parallel processing, as shown in Figure 4-39.

![](converter-master/example/165201/content_633684_0.jpg)

Figure 4-39

The design of artificial neural networks references the human brain neural network. An artificial neural network is a parallel and distributed information processing network structure with a large number of connections. This network structure generally consists of multiple neurons; each neuron has a single output, can connect to multiple other neurons, has multiple input connection pathways, and each pathway corresponds to a connection weight coefficient. Artificial neural networks are very similar to brain neural networks but not identical. Analyzing artificial neural networks requires evaluation from four aspects: system structure, signal form, information storage, and information processing mechanism. A qualified artificial neural network must first have associative memory function, able to associate through known information and store associated information. For example, seeing a bungalow identifies it as a house; seeing a two-story villa also identifies it as a house; seeing a partially obscured house can still be recognized as a house.

Artificial neural networks can also perform information classification. Traditional classification methods are linear classification, but artificial neural networks can find division regions meeting classification requirements from input samples, with samples in each region belonging to one class. Traditional methods only suit recognition and classification problems where similar classes cluster and dissimilar classes separate. However, in the objective world, division surfaces in many sample spaces are highly complex and non-linear; similar samples may belong to different classes, while distant samples may belong to the same class. Artificial neural networks can well solve the problem of approximating non-linear surfaces. Therefore, artificial neural networks have better recognition and classification capabilities than traditional methods. Figure 4-40(a) shows traditional machine learning classification; Figure 4-40(b) shows artificial neural network classification.

![](converter-master/example/165201/content_633684_1.jpg)

Figure 4-40

Since artificial neural networks operate by simulating the human brain neural network, evaluating the overall performance of an artificial neural network requires assessment of three aspects: the information processing capability of the nodes themselves, the connections between nodes, and the connection strength between nodes. The node's own information processing capability examines whether the mathematical model is reasonable; the connections and connection strength examine whether the network topology is reasonable. Since the proposal of artificial neural networks, there have been dozens of models. According to different classification methods, artificial neural networks can be divided into different categories, among which two common methods are classification by network connection topology and classification by information flow direction within the network.

Based on different network connection topologies, artificial neural networks can be divided into two major categories: Layered Structure Artificial Neural Networks and Interconnected Mesh Structure Artificial Neural Networks. Layered Structure Artificial Neural Networks group neurons by function into several layers, including `Input Layer`, `Hidden Layer(s)`, and `Output Layer`, which are sequentially connected, as shown in Figure 4-41.

![](converter-master/example/165201/content_633684_2.jpg)

Figure 4-41

In Interconnected Mesh Structure Artificial Neural Networks, there may be connection paths between any two nodes, forming a neural net, as shown in Figure 4-42.

![](converter-master/example/165201/content_633684_3.jpg)

Figure 4-42

Based on the direction of information flow within the network, artificial neural networks can be divided into Feedforward Artificial Neural Networks and Feedback Artificial Neural Networks. The structural characteristics of Feedforward Artificial Neural Networks are identical to the Layered Structure Artificial Neural Network shown in Figure 4-41, named because the feedforward process executes in the direction of network information processing, with information passing layer by layer from the `Input Layer` backward. From an information processing capability perspective, nodes in Feedforward Artificial Neural Networks can be divided into two types: input nodes, responsible only for introducing information from the outside world and passing it to the next layer; and processing-capable nodes, including various `Hidden Layers` and the `Output Layer`. In Feedforward Artificial Neural Networks, the output of the previous layer is the input of the next layer; information processing has a directional layer-by-layer propagation, and reverse feedback loops generally do not exist. Therefore, this type of artificial neural network is easily cascaded to establish multi-layer feedforward artificial neural networks.

Feedback Artificial Neural Networks are a type of artificial neural network with feedback connections from output to input. In Feedback Artificial Neural Networks, `Input Layer` neurons can not only receive input information but also possess information processing functions, as shown in Figure 4-43.

![](converter-master/example/165201/content_633684_4.jpg)

Figure 4-43

### 4.5.2 Characteristics of Artificial Neural Networks

Artificial neural networks possess self-learning and self-adaptive functions, good fault tolerance, non-linear mapping capabilities, large-scale parallel processing capabilities, and associative processing capabilities. The characteristics and advantages of artificial neural networks are mainly reflected in the following aspects.

**Self-learning and Self-adaptive Functions**: Artificial neural networks well simulate the human brain's acquired learning and training functions. If the final output is incorrect, the artificial neural network can adjust the weight of each input to produce a new result, generally implemented through training algorithms. Due to the complexity of the training process, the final output result can be obtained by repeatedly inputting data and adjusting weights. Thus, artificial neural networks can find the intrinsic connections between input and output through learning and training to solve problems, rather than relying solely on relevant historical knowledge and rules.

**Fault Tolerance**: The brain has strong fault tolerance, and so do artificial neural networks. Artificial neural networks store results in the entire system, not in a single storage unit. Even if a certain proportion of nodes do not participate in computation, it will not significantly impact the overall system performance, and they can still handle noisy or incomplete data, exhibiting good fault tolerance.

**Non-linear Mapping Capability**: Many machine learning algorithms assume input values are linearly separable, i.e., the hyperplane can be represented by a function with a constant first derivative. However, in the real world, problems are generally complex, mostly non-linear, and various factors influence each other. Therefore, artificial neural networks need to simulate arbitrary complex non-linear mapping capabilities by increasing the number of neurons in the `Hidden Layer(s)`.

**Parallel Processing Capability**: Artificial neural networks feature structural parallelism, parallel processing in processing sequence, and computational functions distributed across multiple processing units. Therefore, hardware-implemented artificial neural networks process at speeds far higher than traditional software, with good fault tolerance and fast overall processing capabilities, particularly suitable for real-time and dynamic control.

**Associative Memory Capability**: Artificial neural networks can accept and process analog, chaotic, fuzzy, and random information, and have advantages in natural language understanding, image pattern recognition, scene understanding, incomplete information processing, intelligent robot control, etc.

### 4.5.3 Typical Artificial Neural Networks

The development of artificial neural networks is closely linked to the development of AI technology. Along with the ups and downs of AI technology, artificial neural networks have also experienced periods of prosperity and troughs. Since being proposed in 1943, artificial neural networks have risen and fallen several times, developing in a wave-like pattern.

The earliest artificial neural network was the "Perceptron" proposed by Cornell University Professor Frank Rosenblatt in 1957. This was the first artificial neural network precisely defined by an algorithm and possessing self-organizing learning ability, serving as the ancestor of many subsequent new artificial neural network models.

Subsequently, the Single-Layer Perceptron evolved into `BP` (Backpropagation) Artificial Neural Networks and Hopfield Artificial Neural Networks, and `BP` Artificial Neural Networks further evolved into Recurrent Artificial Neural Networks. Since there are many types of artificial neural networks, the author briefly summarizes the evolution of several common ones, as shown in Figure 4-44.

![](converter-master/example/165201/content_633684_5.jpg)

Figure 4-44

#### 4.5.3.1 Single-Layer Perceptron

The Single-Layer Perceptron is the simplest artificial neural network. It contains only two layers: the `Input Layer` and the `Output Layer`, which are directly connected. The overall structure is shown in Figure 4-45.

![](converter-master/example/165201/content_633684_6.jpg)

Figure 4-45

Data enters through the `Input Layer` and goes directly to the `Output Layer` after computation, see Formula (4-6):

![](converter-master/example/165201/content_633684_7.jpg)

(4-6)

First, an `N`-dimensional vector `i = [i[j], i[j+1], ..., i[n]]` is input, where each component corresponds to a weight `W[j]`. The weights are multiplied, then these products are summed to obtain the sum of products. If the sum of products is greater than a threshold (usually 0), the input terminal takes 1; if less than the threshold, it takes -1.

Therefore, the Single-Layer Perceptron can classify linearly separable data. If Single-Layer Perceptrons are stacked into a Multi-Layer Perceptron, it theoretically gains the ability to handle non-linear classification problems, leading to the emergence of various multi-layer artificial neural networks. Multi-layer artificial neural networks can simulate arbitrarily complex functions by adjusting the number of inputs and increasing the number of `Hidden Layers` to solve various complex problems.

#### 4.5.3.2 Hopfield Artificial Neural Network

The Hopfield Artificial Neural Network arranges all neurons in a row. Each layer has an input, and each input has an input value (`x[1]`, `x[2]`, `x[3]`, `x[4]`), as shown in Figure 4-46. Unlike the Single-Layer Perceptron, the output *result* is fed back via a loop to `x[2]`, `x[3]`, but not to `x[1]`; `x[1]` can treat the weight of feedback to itself as 0.

![](converter-master/example/165201/content_633684_8.jpg)

Figure 4-46

If every neuron in the Hopfield Artificial Neural Network is interconnected, it can also form a ring structure, as shown in Figure 4-47.

![](converter-master/example/165201/content_633684_9.jpg)

Figure 4-47

Precisely because each output result can be transmitted to other neurons, the Hopfield Artificial Neural Network can implement associative memory functions. Therefore, in a Hopfield Artificial Neural Network, even if some units in the network are removed, the entire network can still function normally. This is like seeing dinosaur fossil fragments in a natural history museum; even with just a scapula or two parietal bones, experts can basically reconstruct the dinosaur's skeleton from these simple bones.

Thus, using the stable state of the Hopfield Artificial Neural Network to represent a memory pattern, the process of the initial state converging toward the stable state is the process of the Hopfield Artificial Neural Network searching for the memory pattern. The initial state can be regarded as partial information of the memory pattern, and the network evolution can be regarded as the process of recalling the whole information from partial information.

