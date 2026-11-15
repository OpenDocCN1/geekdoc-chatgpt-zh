

# Convolutional Neural Networks

After convolution, a bias is typically added to each feature map, as shown in Equation (3.7):

![](Images/045-2-i.jpg)

This is the complete process of convolution in deep learning. To summarize, for an input `H∈R^(H×W×C)`, performing a convolution operation with a kernel `R^(k×k×C×C')` and adding a bias yields an output `H∈R^(H'×W'×C')`. The parameters introduced in this process are the kernel and the bias, with a total parameter count of: `k²×C×C'+C'`.

In single-channel convolution, we discussed how kernel size, padding, and stride affect the output dimensions of the convolution. Assuming the input dimension is `H×W×C`, the kernel size is `k`, the padding value is `p`, the stride is `s`, and the output feature map is `H'×W'×C'`, the relationship between them can be given by Equation (3.8), and the calculation for `W'` is the same:

![](Images/046-i.jpg)

## 3.1.3 Pooling

The primary purpose of the pooling operation is dimensionality reduction to reduce computational cost, and to provide some translation invariance[^1] in the early stages of training. The two commonly used pooling operations are average pooling and max pooling.

The pooling operation uses a fixed-size sliding window that moves across the input; each time, the elements within the window are aggregated into a single value as the output. Depending on the aggregation method, it can be divided into average pooling and max pooling, as shown in Figure 3-6.

The window size `k` (assuming the window length and width are equal) and the sliding stride `s` both affect the final output. Typically, `k=2×2` is chosen, with the stride equal to the window size, `s=2`. For multi-channel inputs, pooling is performed channel by channel, so it does not change the number of input channels. For an input `X∈R^(H×W×C)`, after the above pooling operation, the output is `R^(H/2×W/2×C)`, with its height and width halved. Generally, for sliding windows and strides of arbitrary sizes, Equation (3.8) can be used for calculation.

![](Images/3a6.jpg)

**Figure 3-6** Schematic diagram of average pooling and max pooling process

## 3.2 Convolutional Neural Networks

A Convolutional Neural Network (CNN) is obtained by stacking convolutional layers and pooling layers. This section will explain the structure of a CNN using a concrete example, then introduce the characteristics of CNNs and how to understand them.

### 3.2.1 Structure of Convolutional Neural Networks

We use AlexNet[^2] as an example to introduce the structure of a CNN. AlexNet was designed by deep learning pioneer Hinton and his student Alex Krizhevsky. It won the ImageNet competition in 2012, reducing the error rate by more than 10% compared to the runner-up, causing a sensation and ushering in the explosive phase of deep learning. AlexNet can be considered the progenitor of deep convolutional neural networks; compared to previous CNNs, its most significant features are its deeper architecture and larger parameter scale.

AlexNet consists of 5 convolutional layers, 2 pooling layers, and 3 fully connected layers. Limited by the hardware conditions at the time, a single GPU could not run a model of this scale, so the authors split the model and ran it on two separate GPUs. For ease of explanation, we have merged the structure, as shown in Figure 3-7. Let's look at the specifics of each layer.

- **Input Layer**: An image of size `224×224×3`.
- **Layer 1**: Convolutional layer, kernel size `11×11`, outputting 96 feature maps. The input has 3 channels, so the kernel dimension is `R^(11×11×3×96)`. The sliding stride `stride=4`, padding `padding=2`. The output feature map size can be calculated using Equation (3.8), as shown in Equation (3.9). Therefore, the output feature map is `H^(1)∈R^(55×55×96)`. A ReLU activation function is used after the convolution.

![](Images/047-i.jpg)

- **Layer 2**: Pooling layer, using max pooling. The pooling window size is `3×3`, stride is 2. Using a calculation method similar to convolution, the output size is calculated as `(55–3)/2+1=27`. Therefore, the pooling layer output is `H^(2)∈R^(27×27×96)`.
- **Layer 3**: Convolutional layer, kernel size `5×5`, outputting 256 feature maps. The kernel dimension is `R^(5×5×96×256)`, stride `stride=1`, padding `padding=2`. The output feature map is `H^(3)∈R^(27×27×256)`. Again, a ReLU activation function is used after the convolution.
- **Layer 4**: Max pooling, window size `3×3`, stride 2. Output is `H^(4)∈R^(13×13×256)`.
- **Layers 5 to 7**: All are convolutional layers with kernel size `3×3`. The output feature maps are 384, 384, and 256 respectively. Stride `stride=1`, padding `padding=1`. Therefore, Layer 7 outputs `H^(7)∈R^(13×13×256)`. As with the previous structure, a ReLU activation function is used after each convolution.
- **Layer 8**: Pooling layer, window size `3×3`, stride 2. Output is `H^(8)∈R^(6×6×256)`.
- **Last Three Layers**: Fully connected layers. The convolutional output `H^(8)∈R^(6×6×256)` is flattened to obtain the fully connected layer input dimension `R^(9216)`. The three fully connected layers have 4096, 4096, and 1000 neurons respectively. A 1000-dimensional output is finally obtained for image classification.

![](Images/3a7.jpg)

**Figure 3-7** AlexNet architecture diagram[^3]

Besides designing this structure, AlexNet proposed two important improvements: first, using ReLU as the activation function to solve the vanishing gradient problem, which was introduced in Chapter 2; second, using the Dropout mechanism to prevent overfitting. Dropout works by randomly zeroing out parts of the feature map during training, equivalent to discarding some information, forcing the model to make correct inferences based on the remaining features, thereby learning more robust and discriminative features.

The structure of a CNN can generally be divided into two parts: one part is the backbone network formed by alternately stacking convolutional and pooling layers, which is mainly used to extract rich features from the input; the other part is the fully connected layers, which flatten the feature maps obtained from convolution, discarding the spatial information of the feature maps. Its main role is to aggregate global information and map it to the output space—for example, AlexNet uses it for classification. General convolutional structures are also composed of these two parts. More improved structures will be introduced in Section 3.4.

[^3]: The image was generated via https://github.com/HarisIqbal88/PlotNeuralNet.

### 3.2.2 Characteristics of Convolutional Neural Networks

CNNs possess the following three characteristics: local connectivity, weight sharing, and hierarchical representation.

#### (1) Local Connectivity

Since images usually exhibit local correlation, convolution computation is performed only on the region corresponding to the kernel size each time. That is, the input and output are locally connected. If a Multi-Layer Perceptron (MLP) were used to process images, a simple approach would be to transform the multi-dimensional input image into a vector as the MLP input. For an image of size `224×224×3`, flattening it into a vector would require 150,528 input neurons. If the first hidden layer has 32 neurons, it would introduce 4.8 million parameters. Such a large parameter count brings two problems: first, high computational complexity; second, risk of overfitting. If a `3×3` kernel is used with 32 output channels, the introduced parameter count is below 1,000, far fewer than the parameters required by an MLP.

The convolution operation is very similar to certain concepts in biology. In the nervous system, neurons typically respond only to a portion of stimulus signals. For example, when the retina receives light stimulation and transmits signals to the visual cortex, usually only a portion of the visual cortex neurons respond to this signal. This mechanism is called local receptive field. The same applies to convolution: using two consecutive `3×3` convolutional layers, the output is only related to a `5×5` input region, as shown in Figure 3-8. This region is called the receptive field, which refers to the size of the input region relevant to an output on the feature map. In deep CNNs, the effective receptive field is usually smaller than this theoretical region.

![](Images/3a8.jpg)

**Figure 3-8** Schematic diagram of receptive field

#### (2) Weight Sharing

The second characteristic of convolution is weight sharing. In an image, different regions use the same kernel parameters. On one hand, this reduces the parameter count; on the other hand, it brings translation invariance. Translation invariance means that regardless of how the input is translated, the same output can always be obtained. For example, for two identical left and right eyes, using the same kernel to convolve the corresponding eye regions yields the same output, which is brought about by the weight sharing mechanism. Additionally, pooling also brings some translation invariance. For instance, max pooling aggregates information in the receptive field using the maximum value; when the input changes within the receptive field, the output of the pooling layer remains unchanged.

#### (3) Hierarchical Representation

The third characteristic of a convolutional network is its ability to learn hierarchical representations. A convolutional network is formed by stacking convolutional layers, where each layer transforms the previous one. The extracted features progress from low-level to high-level, becoming increasingly abstract. As shown in Figure 3-9, low-level convolutions generally extract simple features such as colors, edges, and corners. They have relatively small receptive fields and correspond to local features. Mid-level convolutions yield more abstract features, such as texture structures. High-level convolutions yield even more abstract features related to image semantics and specific objects contained. Because they have larger receptive fields, they represent more global features. High-level features are built upon low-level features. Generally, low-level features are more generic, while high-level features are more strongly associated with specific tasks.

![](Images/3a9.jpg)

**Figure 3-9** Schematic diagram of hierarchical representation[^4]

## 3.3 Special Forms of Convolution

Besides the standard convolution method introduced in Section 3.1, some special convolutions have been proposed for various tasks. This section introduces several commonly used alternative convolution forms.

### 3.3.1 1×1 Convolution

As the name suggests, the kernel size of a 1×1 convolution is 1. It was proposed in a paper[^5] and adopted by subsequent models. The process of 1×1 convolution is shown in Figure 3-10, which is essentially no different from the convolution operation introduced earlier.

Readers may wonder: is 1×1 convolution useful? Typically, 1×1 convolution serves the following functions: first, for information aggregation while increasing non-linearity. A 1×1 convolution can be viewed as a linear weighting of information across all channels (i.e., information aggregation). Meanwhile, a non-linear activation can be used after the convolution, which can increase the model's expressive power to a certain extent. Second, for transforming the number of channels, which can increase or decrease the number of output feature map channels.

![](Images/3a10.jpg)

**Figure 3-10** Schematic diagram of 1×1 convolution

### 3.3.2 Transposed Convolution

Transposed convolution[^6] is an indispensable module in semantic segmentation tasks. Semantic segmentation refers to classifying an image at the pixel level; pixels belonging to the same category usually correspond to a specific object, as shown in Figure 3-11, where pixels belonging to a person need to be classified into the same category, and pixels belonging to the deer need to be classified into the same category.

![](Images/3a11.jpg)

**Figure 3-11** Semantic segmentation[^7]

CNN-based semantic segmentation models usually consist of two parts: an encoder, which maps the original input image to a low-dimensional feature space. It needs to encode both the target's feature information and positional information to correctly predict the category of each pixel location. The other part is the decoder, which maps the encoded low-dimensional features back to the pixel space to determine the specific category of each pixel. The standard convolution introduced earlier cannot be used for the decoder part because the previous convolution operations do not increase the height and width dimensions of the feature map. Transposed convolution, on the contrary, can increase the height and width dimensions, gradually mapping the encoded low-dimensional features back to the pixel space.

As shown in Figure 3-12(a), assuming the input is `2×2` and the kernel size is `3×3`, transposed convolution is equivalent to padding the input with two layers of zeros around the border, then performing a standard convolution, resulting in a `4×4` output. Similar to convolution, transposed convolution can also set `padding` and `stride`, but their meanings differ slightly from those in standard convolution. For a transposed convolution with stride `s` and kernel size `k`, the calculation defaults to padding the input surroundings with `k–1` layers of zeros. The set `padding` affects the final padding size, specifically given by `k–1–p`. For stride `s`, it inserts `s–1` zeros between convolution elements, as shown in Figure 3-12(b). With an input of `2×2`, `p=0`, stride `s=2`, the output after convolution is `5×5`.

![](Images/3a12.jpg)

**Figure 3-12** Transposed convolution examples

The relationship between input dimensions, kernel size, padding, stride, and output dimensions in transposed convolution can be determined by Equation (3.10):

![](Images/054-i.jpg)

[^7]: Image source https://ccvl.jhu.edu/datasets/.

### 3.3.3 Dilated Convolution

Dilated convolution[^8] "expands" the kernel by inserting gaps between kernel elements. A hyperparameter controls the degree of expansion, called the dilation rate `r`, which refers to inserting `r–1` zeros between kernel elements. Therefore, for an original kernel of size `k`, the actual kernel size becomes `k+(k–1)(r–1)` when using dilated convolution. Because the kernel expands, the receptive field also expands. Thus, dilated convolution is an effective way to rapidly expand the receptive field without increasing the parameter count.

![](Images/3a13.jpg)

**Figure 3-13** Dilated convolution kernels

For a `k=3` kernel, when the dilation rate `r` is 2 and 3 respectively, the actual kernel sizes are 5 and 7, as shown in Figure 3-13. For an output region of size `3×3`, the receptive field sizes corresponding to the three kernels above are:

- When `r=1`, the receptive field is 3.
- When `r=2`, the receptive field is 7.
- When `r=3`, the receptive field is 11.

When stacking multiple layers composed of dilated convolutions, the receptive field expands rapidly to capture more local information.

### 3.3.4 Grouped Convolution

Grouped convolution first appeared in AlexNet. At that time, the convolution operations for training AlexNet could not all be processed on a single GPU, so the authors split the feature maps across multiple GPUs for processing and then fused the results from multiple GPUs. This is essentially grouped convolution. We illustrate this below with an example.

As shown in Figure 3-14, the input is divided into `g` groups along the depth dimension, each group consisting of `C₁/g` channels. Similarly, the output channels are split into `g` groups, each having `C₂/g` output channels. The input groups are paired with the output groups, and convolution is computed separately for each pair. Thus, for each group of convolution, the required kernel dimension is `R^(k×k×C₁/g×C₂/g)`, meaning the kernel depth per group changes from the original `C₁` to `C₁/g`, and the number of kernels becomes `C₂/g` instead of the original `C₂`. Each group's kernels convolve with its corresponding input group; after obtaining the outputs, they are concatenated, and the final output channels remain `C₂`. In other words, we can compute `g` identical convolution processes in parallel. The parameter count used becomes `k²×C₁×C₂/g`, i.e., the parameter count is reduced by `g` times compared to standard convolution.

![](Images/3a14.jpg)

**Figure 3-14** Grouped convolution processing flow

### 3.3.5 Depthwise Separable Convolution

Depthwise separable convolution consists of two parts[^9]: one part is depthwise convolution along the channel dimension; the other part is 1×1 convolution. Depthwise convolution is a special case of grouped convolution when `g=C₁=C₂`. It is equivalent to assigning a separate kernel to each input channel for convolution. Since this convolution only utilizes information from a single input channel (i.e., only spatial position information) and does not use inter-channel information, a 1×1 convolution is typically used afterward to increase inter-channel information.

Compared to standard convolution, depthwise separable convolution not only reduces the parameter count but also lowers computational cost and improves operational efficiency. Therefore, this type of convolution is often used in convolutional structure designs where speed is a requirement, and it has been applied in models such as MobileNet[^10] and ShuffleNet[^11].

## 3.4 Application of Convolutional Networks in Image Classification

In just a few years, deep learning has achieved remarkable results in the field of computer vision. Various image classification models have been proposed one after another, from AlexNet to VGG, from GoogleNet v1 to v4, and later to ResNet, DenseNet, etc., constantly evolving and refreshing records. In this section, we introduce several typical image classification frameworks.

### 3.4.1 VGG

VGG[^12] was proposed by the Visual Geometry Group at the University of Oxford in 2014, achieving second place in the ImageNet 2014 classification task and first place in the localization task.

VGG made some improvements based on AlexNet, mainly reflected in the adoption of small `3×3` convolutional kernels with a convolution stride of 1. Two such convolutional layers yield a receptive field of 5, but with fewer parameters compared to directly using a single `5×5` convolutional layer. Additionally, it removed the LRN (Local Response Normalization) layer, as the authors found in experiments that LRN's effect was not significant. Another structural feature of VGG is the repeated use of simple convolutional blocks stacked to form the model. Its basic convolutional block consists of two or more convolutional layers followed by a pooling layer.

VGG has two commonly used models: VGG16 and VGG19. The difference between VGG16 and VGG19 lies in the number of convolutional layers in some basic blocks. The structure of VGG16 is shown in Figure 3-15. Due to its concise structure and good performance, VGG is still often used as a backbone network for other tasks.

![](Images/3a15.jpg)

**Figure 3-15** VGG16 network architecture

### 3.4.2 Inception Series

The best result in the ImageNet 2014 classification task was achieved by GoogleNet, proposed by Google. The core structure of this network is the Inception block. Unlike the basic blocks constructed by VGG, the Inception block uses a multi-branch structure with kernels of different scales. The Inception structure has undergone several optimizations, resulting in several different versions.

#### 1. Inception V1

Inception V1[^13] first used a parallel structure. As shown in Figure 3-16, each Inception block uses multiple kernels of different sizes. This is completely different from the traditional stacked network structure where each layer uses only one kernel size. The multiple different kernels in an Inception block can extract different types of features. Simultaneously, each kernel has a different receptive field, allowing the extraction of multi-scale features, which are finally concatenated.

If multiple kernel sizes are simply introduced, it would bring a large number of parameters and consume significant computational resources. To reduce computational cost, an improvement is to introduce `1×1` convolutions before the `3×3` and `5×5` convolutions to reduce the number of input channels. Additionally, a `1×1` convolution is used for dimensionality reduction after the pooling layer. The improved Inception block is shown in Figure 3-17. Let's compare the parameter count changes before and after the improvement. Ignoring bias terms, with input/output channel numbers as shown in Figure 3-17, the parameters required before improvement are shown in Equation (3.11), and after improvement in Equation (3.12). It can be seen that the parameters before improvement are 2.3 times those after improvement. Moreover, before improvement, the pooling layer's output channel count equals the input channel count; after concatenation with other convolution results, the output channel count would increase dramatically.

![](Images/058-i.jpg)
![](Images/3a16.jpg)

**Figure 3-16** Initial Inception module

GoogleNet stacks Inception blocks. Additionally, to alleviate the vanishing gradient problem, auxiliary classifiers were added in the middle of the network.

#### 2. Inception V2

Inception V2[^14] mainly improves the kernel design of Inception V1 by decomposing large-sized convolutions to reduce parameter count and computational complexity.

![](Images/3a17.jpg)

**Figure 3-17** Improved Inception V1 module

[^1]: [^(\[1\])](../Text/chapter45.xhtml#cc-1)
[^2]: [^(\[2\])](../Text/chapter45.xhtml#cc-2)
[^4]: [^(\[3\])](../Text/chapter45.xhtml#cc-3)
[^5]: [^(\[4\])](../Text/chapter45.xhtml#cc-4)
[^6]: [^(\[5\])](../Text/chapter45.xhtml#cc-5)
[^8]: [^(\[6\])](../Text/chapter45.xhtml#cc-6)
[^9]: [^(\[7\])](../Text/chapter45.xhtml#cc-7)
[^10]: [^(\[8\])](../Text/chapter45.xhtml#cc-8)
[^11]: [^(\[9\])](../Text/chapter45.xhtml#cc-9)
[^12]: [^(\[10\])](../Text/chapter45.xhtml#cc-10)
[^13]: [^(\[11\])](../Text/chapter45.xhtml#cc-11)
[^14]: [^(\[12\])](../Text/chapter45.xhtml#cc-12)

