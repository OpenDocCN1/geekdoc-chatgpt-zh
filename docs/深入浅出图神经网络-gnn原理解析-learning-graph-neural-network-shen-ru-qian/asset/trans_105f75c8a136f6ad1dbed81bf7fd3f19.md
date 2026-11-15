
Another advantage of deep learning models is their ability to learn hierarchical representations of data, which is also a property that a good representation should possess. As introduced in Section 3.2, layers at different depths in a convolutional neural network can extract representations at different levels; lower-level convolutions mainly extract low-level features, while higher-level convolutions mainly extract abstract, task-relevant features. Deep learning models are stacks of layers, each of which is trainable. They transform input features into more abstract features. Transformations at lower layers yield basic features, which form the foundation for high-level abstract features. This is analogous to the relationship between characters, sentences, and articles: characters form words, words form sentences, and sentences form articles. Therefore, for low-level features, they are more general (`general feature`), while high-level features are specific to particular tasks. Transfer learning can be based on this property of deep learning. Transfer learning refers to transferring learned knowledge to new problems. A typical application of deep learning in this area is fine-tuning, i.e., using a model pre-trained on another dataset as a basis and further adjusting it on a new dataset. For example, many vision-related tasks use models pre-trained on `ImageNet`, freezing some convolutional layers without performing gradient updates on them because they extract general features; only the high-level structures are modified, such as changing the output layer dimension to adapt to classification problems with different numbers of categories, and then adjusting the model using new data.

The task of representation learning is usually to learn such a mapping: `f: X→R^d`, i.e., mapping the input to a dense low-dimensional vector space. In the next two sections, we will introduce two typical representation learning methods: one based on reconstruction loss, and the other based on contrastive loss.

## 4.2 Methods Based on Reconstruction Loss—Autoencoders

The advantage of deep learning lies in automatic feature learning. Convolutional neural networks use image labels for supervision to learn discriminative features for image classification; they combine representation learning with task learning and are a form of supervised learning. Autoencoders are also representation learning models, but they do not use label information for supervision; instead, they use the input data itself as the reference, making them an unsupervised learning model. They can be used for dimensionality reduction and feature extraction.

### 4.2.1 Autoencoders

Autoencoders are a typical method for representation learning based on deep learning models. Their idea is very simple: map the input to a certain feature space, and then map it back from this feature space to the input space for reconstruction. Structurally, they consist of an encoder and a decoder. The encoder extracts features from the input data, and the decoder reconstructs the input data based on the extracted features. After training is complete, the encoder is used for feature extraction. This encode-decode idea is reflected in many deep learning models.

The simplest autoencoder consists of 3 layers: 1 input layer, 1 hidden layer, and 1 output layer, as shown in Figure 4-3. The part from the input layer to the hidden layer output is called the encoder, and the part from the hidden layer output to the output layer is called the decoder. Given an input `x∈R^n`, assuming the transformation matrix from the input layer to the hidden layer is `Wenc∈R^(d×n)`, where `d` is the number of neurons in the hidden layer, the encoder is shown in Equation (4.1), and the encoded feature is `h∈R^d`. The decoder maps the encoded feature `h` back to the input space to obtain the reconstructed input `x̂`. Assuming the encoding matrix from the hidden layer to the output layer is `W[enc]∈R^(n×d)`, it is shown in Equation (4.2):

![](Images/070-i.jpg)
![](Images/070-2-i.jpg)

Autoencoders do not require additional label information for supervised learning; they are trained by continuously minimizing the reconstruction error between input and output. Based on the loss function (4.3), gradients are computed via backpropagation, and the gradient descent algorithm is used to continuously optimize parameters `W[enc]`, `W[dec]`, `b[enc]`, `b[dec]`.

![](Images/070-3-i.jpg)

The structure of an autoencoder is not limited to a fully connected network with only one hidden layer. Generally, the encoder and decoder can be more complex models, denoted by `f` and `g` respectively, and the loss function can be expressed as Equation (4.4), where `N` is the number of samples.

![](Images/070-4-i.jpg)
![](Images/4a3.jpg)

Figure 4-3 Schematic Diagram of Autoencoder Structure

We hope to obtain some useful features in the data by training the encoder. The most common method is to constrain the dimension of `h` to be smaller than the dimension of `x`, i.e., `d<n`. An encoder meeting this condition is called an undercomplete autoencoder. Under certain conditions, this autoencoder can achieve effects similar to Principal Component Analysis (PCA). Using non-linear encoders and decoders can yield more powerful results.

### 4.2.2 Regularized Autoencoders

If we relax the above constraint on the encoder dimension, allowing the encoder dimension to be greater than or equal to the input dimension, i.e., `d≥n`, such an encoder is called an overcomplete autoencoder. If no restrictions are placed on an overcomplete autoencoder, it may fail to learn any useful information from the data and simply copy the input to the output. The essential cause of this problem is not the change in dimensionality constraint, but rather that when we endow the encoder and decoder with overly powerful "capabilities," the autoencoder tends to directly copy the input to the output without extracting valuable features from the data. Therefore, we often impose some regularization constraints on the model. Below we introduce several common regularized autoencoders.

#### 1. Denoising Autoencoder

Unlike the vanilla autoencoder introduced above, the improvement of the denoising autoencoder[^([2])](../Text/chapter55.xhtml#dd-2) lies in adding some noise to the original input as the encoder's input. The decoder needs to reconstruct the original input `x` without noise. By imposing this constraint, the encoder is forced not to simply learn an identity transformation but to extract useful information from the noisy data to recover the original data.

The specific approach is to randomly set a portion of the input `x` values to 0, thus obtaining the noisy input `x[δ]` as the encoder's input. The decoder needs to reconstruct the noise-free data `x`; therefore, the loss function is:

![](Images/071-i.jpg)

#### 2. Sparse Autoencoder

Besides adding noise to the input, we can also make the model learn useful features by adding a regularization term to the loss function. Assuming the hidden layer uses the `Sigmoid()` activation function, we consider a neuron to be relatively "active" when its output is close to 1, and "inactive" when its output is close to 0. The sparse autoencoder constrains the model by limiting the activity of neurons, trying to keep most neurons in an inactive state.

We define the activity of a neuron as the average of its values across all samples, denoted by `ρ[i]`. We constrain `ρ[i]` to be close to a hyperparameter `ρ`, representing the desired activity level, usually a value close to 0. By penalizing neurons that deviate significantly from `ρ`, sparse encoded features can be obtained. Here we choose to use relative entropy (KL divergence) as the regularization term, as shown in Equation (4.6).

![](Images/072-i.jpg)
![](Images/072-2-i.jpg)
![](Images/072-3-i.jpg)

Relative entropy can quantitatively represent the difference between two probability distributions. The greater the difference between the two distributions, the larger the relative entropy value; when the two distributions are identical, the relative entropy value is 0. After adding the sparse penalty term, the loss function becomes:

![](Images/072-4-i.jpg)

Where `λ` is the weight balancing the reconstruction term and the sparse regularization term.

### 4.2.3 Variational Autoencoder

The variational autoencoder[^([3])](../Text/chapter55.xhtml#dd-3) can be used to generate new sample data. It differs significantly from traditional autoencoders. This section will introduce the principle of the variational autoencoder and compare it with traditional autoencoders.

#### 1. Principle of Variational Autoencoder

The essence of the variational autoencoder is a generative model. It assumes that the samples we obtain all follow a certain complex distribution `P(x)`, i.e., `x~P(X)`. The purpose of the generative model is to model `P(X)` so that we can sample from the distribution to obtain new sample data. For example, for images, each pixel can be regarded as a random variable; these pixels may be interdependent. The goal of the generative model is to model these dependencies to generate samples.

Generally, each sample may be controlled by certain factors. For instance, for handwritten digits, one needs to decide which digit to write, the size of the digit, the thickness of the strokes, etc. These factors are called latent variables. Assuming there are multiple latent variables, represented by vector `z`, with probability density function `p(z)`. Simultaneously, there exists a function `f(z; θ)` that can map the data `z` sampled from `p(z)` to sample data similar to `X`, i.e., the probability `p(X|z)` is higher. Introducing latent variables, the distribution `p(X)` is solved via Equation (4.8). There are two issues to consider here: how to determine the latent variable `z`, and how to compute the integral.

![](Images/073-i.jpg)

For the selection of latent variable `z`, the variational autoencoder assumes that each dimension of `z` has no explicit meaning but only requires `z` to be easy to sample; therefore, it assumes `z` follows a standard normal distribution `z~N(0, I)`. The choice of `p(x|z)` is also often a normal distribution, with its mean as `f(z; θ)` and variance as `σ²I`, where `σ` is a hyperparameter.

![](Images/073-2-i.jpg)

Why are the above assumptions reasonable? In fact, any `d`-dimensional complex distribution can be obtained by applying a complex transformation to a `d`-dimensional normal distribution. Therefore, given a function with sufficient expressive power, the latent variable `z` following a normal distribution can be mapped to the latent variable required by the model, and then these latent variables can be mapped to `x`.

However, for most `z`, they cannot generate samples similar to `x`, i.e., `p(x|z)` is usually close to 0, which is meaningless for estimating `p(X)`. We need to obtain those `z` that can generate samples resembling `x` with high probability. How to obtain these `z`? If we know the posterior distribution `p(z|x)` of `z`, we can infer them through `x`. The variational autoencoder introduces another distribution `q(z|x)` to approximate the posterior distribution `p(z|x)`. Now the question is: what is the relationship between `E[z~q(z|x)][p(x|z)]` and `p(x)`? Below we calculate the KL distance between `q(z|x)` and `p(z|x)`, as shown in Equation (4.10):

![](Images/073-3-i.jpg)

Then, using Bayes' theorem to expand, we can obtain Equation (4.11), and after slight rearrangement, we get Equation (4.12).

![](Images/073-4-i.jpg)

Equation (4.12) is the core of the entire variational autoencoder. The left side contains our optimization target `p(x)`. When the chosen `q(z|x)` has sufficient expressive power, it can approximately express `p(z|x)`, meaning `D_KL[q(z|x)||p(z|x)]` is a number approaching 0. The first term on the right is essentially a decoder, which samples the latent variable most likely to generate a similar sample based on input `x` and obtains the generated sample through the decoder. The second term on the right is a regularization term.

Previously we assumed `p(x|z)` is a normal distribution. Then for the first term on the right side of Equation (4.13), the calculation yields the first part of the loss function: the reconstruction loss. Note the negative sign here is because gradient descent is used for optimization, so the objective becomes minimizing `–p(x)`. `f(z; θ)` can be implemented using a neural network.

![](Images/074-i.jpg)

For the choice of `q(z|x)`, we also assume it follows a normal distribution, with its components mutually independent, as shown in Equation (4.15). Then the second part of the loss function, the regularization term, can be obtained.

![](Images/074-2-i.jpg)

Based on the above principles, the structure of the variational autoencoder can be derived, as shown in Figure 4-4.

![](Images/4a4.jpg)

Figure 4-4 Schematic Diagram of Variational Autoencoder Structure

There is still a problem: the latent variable `z` is obtained by sampling, and the sampling operation is non-differentiable, preventing backpropagation. We use a technique called reparameterization to solve this problem. Specifically, we first sample `ε` from the normal distribution `N(0, I)`, and the latent variable `z` is obtained by calculation: `z = ε * μ(x) + σ(x)`, thus solving the backpropagation problem.

#### 2. Comparison with Autoencoder

From the structure and loss function of the variational autoencoder, it looks quite similar to the traditional autoencoder, but essentially they are different. The autoencoder introduced earlier is an unsupervised representation learning method, while the variational autoencoder is essentially a generative model. Although traditional autoencoders have a decoder part, we cannot arbitrarily give a hidden layer feature and obtain a meaningful output through the decoder. The reason is that the latent space of a traditional autoencoder is not continuous; it consists of the encodings of individual sample points. For places without sample encodings, since the decoder has never seen such hidden layer features, it cannot give meaningful outputs when arbitrary hidden layer vectors are given. The variational autoencoder is different; its starting point is to model the sample distribution `p(x)`. Therefore, after training is complete, the decoder alone can be used to generate samples.

## 4.3 Methods Based on Contrastive Loss—Word2vec

In natural language processing, how to represent a word is very important. Before Word2vec appeared, commonly used methods, such as one-hot encoding, bag-of-words models, and co-occurrence matrices built based on word context, inevitably suffered from problems like excessively high dimensionality and sparsity. The Word2vec model embeds words into a vector space, using a low-dimensional vector to represent each word. Semantically related words are closer in distance, solving the problems of high dimensionality and data sparsity inherent in traditional methods. This section will introduce the motivation for word vectors and derive the Skip-gram model from the perspective of contrastive loss.

### Word Vector Model—Skip-gram

Word2vec[^([4])](../Text/chapter55.xhtml#dd-4) was proposed by Tomas Mikolov in 2013. Its core idea is to characterize a word using its context. From this idea, we can derive two different models. One is to predict the center word given its context, called CBow (Continuous Bag-of-Words); the other is to predict its context words given a center word, called Skip-gram. Below we take Skip-gram as an example to introduce how to model to obtain the word vectors we need.

Given a corpus, it can consist of multiple documents. For simplicity, assume the corpus can be represented as a sequence `C={w_1, w_2, ..., w_N}`, with corpus length `N`. The vocabulary is `V`, `w_i∈V`. The Skip-gram model uses the center word to predict its context words. Here, context words are defined as words within a certain window centered on the center word. Assume the window size is `2m+1`. Given a center word, we want to correctly predict the context words, i.e., we hope that given a certain center word, the probability of the output word being a context word is maximized.

Taking the sentence shown in Figure 4-5 as an example, choose `m=2`, and examine the center word "network" (网络). Its context is {"graph" (图), "neural" (神经), "has" (有), "very" (非常)}. We can construct such word pairs: [("graph", "network"), ("neural", "network"), ("has", "network"), ("very", "network")]. We call word pairs composed of a center word and its context words positive samples, denoted as `D`. Word pairs composed of a center word and its non-context words are negative samples, denoted as `D'`, e.g., ("network", "application").

![](Images/4a5.jpg)

Figure 4-5 Schematic Diagram of Positive and Negative Samples

To correctly predict context words based on the center word, we can maximize the probability of word pairs in positive samples appearing as context, while minimizing the probability of word pairs in negative samples appearing as context, thereby constructing the objective function. Specifically, labels are defined for positive and negative samples, as shown in Equation (4.16), where `w_c` represents the center word.

![](Images/076-2-i.jpg)

Our goal is to find parameters `θ` that maximize the conditional probabilities `p(y=1|(w_c, w))` and `p(y=0|(w_c, w))`, as shown in Equation (4.17).

![](Images/077-i.jpg)

This problem transforms into a binary classification problem: given any two words, determine whether they are context words. Therefore, logistic regression can be used to model this problem. Two matrices `U∈R^(|V|×d)` and `V∈R^(|V|×d)` are introduced; each row in them represents a word. After model training, they are the word vectors containing semantic expressions. `U` and `V` correspond to different representations of a word under two roles: center word and context word. For a word `w`, `U[w]` denotes its corresponding word vector. Then the probability in Equation (4.17) can be expressed as Equation (4.18), where `σ(x)` denotes the Sigmoid function:

![](Images/077-2-i.jpg)

Combining Equations (4.17) and (4.18) and taking the logarithm, we obtain Equation (4.19), which is the objective function of Skip-gram:

![](Images/077-3-i.jpg)

This equation increases the probability of positive samples on one hand and decreases the probability of negative samples on the other. We notice that increasing the probability of positive samples essentially increases `U[w_c]^T V[w]`, i.e., the dot product of the center word and context word, which is their similarity. In other words, minimizing Equation (4.19) actually makes the distance between the center word and context words smaller, and the distance between the center word and non-context words larger. This method serves as a supervisory signal to guide model learning. After convergence, the parameter matrices `U` and `V` are the word vectors we need; usually, we use `U` as the final word vector.

This approach of constructing positive and negative samples, maximizing the similarity between positive samples, and minimizing the similarity between negative samples is a common idea for constructing loss functions in representation learning. This type of loss is collectively called contrastive loss (`contrastive loss`)[^([5])](../Text/chapter55.xhtml#dd-5). It preserves the neighbor relationships of data and its neighbors in the input space within the feature space. In the above Skip-gram model, neighbors are defined as context words of a word. In other tasks, definitions may differ. For example, in face recognition, positive samples can be defined as face images of the same individual under different conditions, and negative samples as face images of different individuals. Optimization via contrastive loss learns discriminative features for face recognition.

### Negative Sampling

Usually, the number of negative samples is far greater than the number of positive samples. For example, for a center word, only a few words in the vocabulary can form positive samples with it. Therefore, when computing Equation (4.19), when the vocabulary size reaches millions, the latter term computing negative samples becomes a bottleneck. Negative sampling[^([6])](../Text/chapter55.xhtml#dd-6)[^([7])](../Text/chapter55.xhtml#dd-7)[^([8])](../Text/chapter55.xhtml#dd-8) technology uses sampling to reduce computational complexity. It provides a method to reduce computational complexity when we cannot compute all negative samples or the computational cost is too high. After using negative sampling technology, for the center word `w_c`, the loss function becomes Equation (4.20):

![](Images/078-i.jpg)

Where `NEF(w_c)` denotes the set of words sampled to form negative samples with `w_c`. Negative sampling reduces the computation of negative samples from `O(|V|)` to `O(n)`, where `n<<|V|`. During sampling, uniform sampling is not used; instead, weighted sampling based on word frequency is adopted. It can be proven that this sampling method optimizes not only the dot product of word vectors but also the mutual information of word vectors[^([9])](../Text/chapter55.xhtml#dd-9)[^([10])](../Text/chapter55.xhtml#dd-10). Therefore, word vectors obtained this way usually perform better.

### Word Vector Visualization

We visualize the learned word vectors by reducing their dimensionality to a 2D space. We can see that semantically related or similar words are closer in distance, while words with larger semantic differences are farther apart, as shown in Figure 4-6a. Additionally, we discover an interesting phenomenon: there exists a certain translational invariance between some word vectors, as shown in Figure 4-6b. They can perform approximate arithmetic operations, e.g., `Man - Woman ≈ King - Queen`. These phenomena all indicate that word vectors capture the semantic information of words.

![](Images/4a6.jpg)

Figure 4-6 Word Vector Visualization

## 4.4 References

[[1]](../Text/chapter48.xhtml#d1) Bengio Y, Courville A, Vincent P. Representation learning: A review and new perspectives[J]. IEEE transactions on pattern analysis and machine intelligence, 2013, 35(8): 1798-1828.

[[2]](../Text/chapter52.xhtml#d2) Vincent P, Larochelle H, Bengio Y, et al. Extracting and composing robust features with denoising autoencoders[C]//Proceedings of the 25th international conference on Machine learning. ACM, 2008: 1096-1103.

[[3]](../Text/chapter53.xhtml#d3) Kingma D P, Welling M. Auto-encoding variational bayes[J]. arXiv preprint arXiv:1312.6114, 2013.
