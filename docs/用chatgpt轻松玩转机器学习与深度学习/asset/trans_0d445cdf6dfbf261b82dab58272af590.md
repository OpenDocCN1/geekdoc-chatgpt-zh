

# Chapter 10 Core Algorithms and Limitations of Neural Networks

## 10.2 Visual Comparison of Activation Functions

In the previous response, `ChatGPT` listed more activation functions and provided their computational formulas. Among them, the `Sigmoid` function is probably no longer unfamiliar to everyone; the logistic regression algorithm uses the `Sigmoid` function to process the input results of the linear regression model.

To intuitively compare the differences among several activation functions, we can ask `ChatGPT` to display them visually. Enter "Please use `Python` to visualize the differences of several activation functions" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> Sure, we can use the `Matplotlib` library in `Python` to visualize the differences of several activation functions. Below is a sample code showing the graphs of the `Sigmoid`, `ReLU`, `LeakyReLU`, and `Tanh` functions.

![](Images/9787301342640_Figure-0195-0237.jpg)![](Images/9787301342640_Figure-0196-0238.jpg)

We paste this code into `Jupyter Notebook` and run it to get the result shown in Figure 10-4.

Figure 10-4 shows the graphs of the `Sigmoid`, `ReLU`, `Leaky ReLU`, and `Tanh` functions respectively. We can see that different activation functions have different characteristics. For example, the output value of the `Sigmoid` function is between 0 and 1; the derivative of the `ReLU` function is constantly 1 in the positive region; the `Leaky ReLU` function has a certain derivative in the negative region; and the output value of the `Tanh` function is between -1 and 1.

![](Images/9787301342640_Figure-0196-0239.jpg)

Figure 10-4 Comparison of Different Activation Functions

## 10.3 Propagation Algorithms in Neural Networks

In neural networks, there are two important algorithms: forward propagation and backpropagation. Forward propagation refers to the process of passing input signals through each layer of the neural network sequentially starting from the input layer, finally calculating the output result. In each layer, the input signal is first subjected to a weighted sum operation with the weights of that layer, then the weighted sum is fed into the activation function of that layer for non-linear transformation to obtain the output of that layer. This output then serves as the input to the next layer, until the final output layer produces the output result of the entire neural network.

Backpropagation, on the other hand, refers to the process of calculating the error contribution of each parameter in reverse order using the chain rule based on the error between the neural network's output and the true labels, and then updating each parameter using gradient descent. Specifically, the error of the output layer is calculated first, then the error of each layer is calculated in reverse order, and the gradient of the parameters of each layer is obtained based on the error and the activation function of each layer. Finally, according to gradient descent, each parameter is iteratively updated so that the neural network's prediction results continuously approach the true labels.

Forward propagation and backpropagation are the core algorithms of neural network training, and their optimization efficiency and accuracy are crucial for the training and application of neural networks. Below we will learn about these two algorithms.

### 10.3.1 Forward Propagation in Neural Networks

We ask `ChatGPT` to explain the forward propagation algorithm in simple terms. Enter "Please explain the forward propagation algorithm in neural networks in simple terms" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> In a neural network, the forward propagation algorithm (`Forward Propagation`) refers to the process of passing input data from the input layer to the output layer through a series of mathematical calculations.
>
> The specific steps of the forward propagation algorithm are as follows.
>
> 1. Feed the input data into the input layer of the network, with each input node receiving one input data.
> 2. The input data connects to adjacent hidden layer or output layer nodes through a series of connection weights (`Weights`) and bias terms (`Bias`).
> 3. Each hidden layer or output layer node transforms its weighted sum (`Weighted Sum`) through an activation function (`Activation Function`) to produce an output value.
> 4. These output values become the input of the next layer. As the input data propagates layer by layer, it eventually reaches the output layer, where the output layer nodes produce the final result.
>
> The purpose of the entire propagation algorithm is to convert input data into an output result. During the training process of the neural network, we adjust the network parameters based on the actual results so that the network's output approaches the true results as closely as possible.

To understand the working principle of the forward propagation algorithm more intuitively, we ask `ChatGPT` to demonstrate it visually. Enter "Please use `Python` to visually demonstrate the forward propagation algorithm" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> The following is a visual example of a simple forward propagation algorithm implemented in `Python`.

![](Images/9787301342640_Figure-0197-0240.jpg)![](Images/9787301342640_Figure-0198-0241.jpg)![](Images/9787301342640_Figure-0199-0242.jpg)

Paste the above code into `Jupyter Notebook` and run it to get the result shown in Figure 10-5.

In Figure 10-5, we can see that the forward propagation algorithm first computes the data received by the input layer (`Input`), then propagates it to the hidden layer (`Hidden Layer`), and then propagates it to the output layer (`Output`) through the hidden layer. This process goes from front to back, which is the origin of the name "forward propagation algorithm".

![](Images/9787301342640_Figure-0199-0243.jpg)

Figure 10-5 Demonstration of the Forward Propagation Algorithm Principle

### 10.3.2 Backpropagation in Neural Networks

After understanding the forward propagation algorithm, let's study the backpropagation algorithm in neural networks. Enter "Please explain the backpropagation algorithm in neural networks in simple terms" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> In a neural network, the backpropagation algorithm is a common method for training neural network models. This algorithm updates model parameters by backpropagating error signals to minimize the model's prediction error on training data.
>
> Specifically, the backpropagation algorithm first uses the forward propagation algorithm to calculate the output value and loss function for each sample. Then, by calculating the gradient of the loss function with respect to each parameter, it computes the error signal, i.e., the gap between the network output and the actual label, to determine the update direction and magnitude for each parameter.
>
> These gradient values propagate backward along the network. The error signal of each layer is backpropagated to the previous layer until it reaches the input layer. During backpropagation, each neuron calculates the gradient of the input signals it receives and passes it forward to the previous layer's neurons to facilitate the calculation of the previous layer's gradients. This process continues until the input layer is reached.
>
> Finally, based on these gradient values, we can use optimization algorithms (such as Stochastic Gradient Descent `SGD`) to update the model parameters to minimize the loss function.
>
> Therefore, the backpropagation algorithm is a key step in neural networks. It allows us to automatically compute the gradients of model parameters, thereby enabling automatic optimization of the model.

If we briefly summarize `ChatGPT`'s response, it can be understood as: during the model training process, we propagate the loss of each layer backward to the previous layer, and then the model can gradually adjust its parameters based on these losses and eventually reach an optimal state. Of course, we can also ask `ChatGPT` for a visual demonstration. Enter "Please use `Matplotlib` to visually demonstrate the backpropagation algorithm" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> The backpropagation algorithm is a relatively abstract concept and is difficult to visualize intuitively. However, we can demonstrate the effect of the backpropagation algorithm by visualizing the training process of the neural network. Below is a simple example using `Matplotlib` to visualize the training process of a neural network with a single hidden layer.

![](Images/9787301342640_Figure-0200-0244.jpg)![](Images/9787301342640_Figure-0201-0245.jpg)

Paste the code into `Jupyter Notebook` and run it to get the result shown in Figure 10-6.

![](Images/9787301342640_Figure-0201-0246.jpg)

Figure 10-6 Optimization of the Model by Backpropagation Algorithm

The code generated by `ChatGPT` trains a neural network with a single hidden layer and uses `Matplotlib` to visualize the loss function during the training process. It can be seen that as training proceeds, the loss function continuously decreases. This indicates that the backpropagation algorithm is gradually optimizing the model, enabling it to fit the training data better.

## 10.4 Limitations and Future Development of Neural Networks

In this section, we mainly discuss the limitations of neural networks and their future development directions. First, we enter "Please introduce the limitations of neural networks in detail" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> Although neural networks have been widely used in many fields, they still have some limitations.
>
> 1. **High data requirements**: Neural networks require a large amount of training data to learn patterns and features. If the data quality is low or the quantity is insufficient, the model's performance will be affected.
> 2. **High computational cost**: Neural networks require a large amount of computational resources for training and inference, especially in large-scale deep networks where the computational cost is even higher.
> 3. **Prone to overfitting**: Neural networks have strong fitting capabilities, but if the model is too complex or the training data is too small, overfitting is likely to occur, i.e., the model performs well on training data but poorly on new data.
> 4. **Poor interpretability**: Due to the complex structure and numerous weight parameters of neural networks, it is difficult to explain the specific meaning of each weight. This limits the application of neural networks in certain fields, such as healthcare and finance.
> 5. **May not be suitable for specific problems**: The design of neural networks is usually general-purpose, applicable to different types of problems, but in certain specific domains or problems, they may not be the best choice. For example, for sequence data problems, recurrent neural networks may be more suitable.
>
> In summary, although neural networks have powerful modeling capabilities, they also have some limitations that need to be carefully considered in practical applications.

From the above response, we can see that even the powerful neural network algorithm has many limitations, such as high computational cost and poor interpretability.

Next, we can ask `ChatGPT` to analyze the future development of neural network algorithms. Enter "Please analyze the future development trends and possible improvement directions of neural networks in detail" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> Neural networks are an important component in the field of artificial intelligence. Their future development trends and improvement directions include the following aspects.
>
> 1. **Deeper neural networks**: Deep learning technology has been widely used in recent years, and more and more problems can be solved by using deeper neural networks. Therefore, a major future trend of neural networks will be to continuously increase the depth of the network.
> 2. **Enhanced structure, sparsity, and dynamics**: The structural characteristics of neural networks are crucial to their performance. Future neural networks will pay more attention to improvements in structure, sparsity, and dynamics to achieve better learning effects.
> 3. **Research on new activation functions**: The choice of activation function has a significant impact on the performance of neural networks. Currently commonly used activation functions include `Sigmoid`, `Tanh`, and `ReLU`, etc. Future research will focus on new activation functions to improve the performance of neural networks.
> 4. **Multi-modal and multi-task learning**: Future neural networks will not only be single input-output mappings but will possess multiple input and output forms. At the same time, they will be able to complete multiple tasks or learn multiple knowledge domains to meet broader application needs.
> 5. **More efficient neural networks**: Current neural networks have problems such as slow training speed and high computational resource occupancy. As neural network models continue to grow, future neural networks will pay more attention to improving training efficiency and inference efficiency.
> 6. **Research on new optimization algorithms**: Currently commonly used neural network optimization algorithms include `SGD`, `Adam`, etc. Future research will focus on new optimization algorithms to improve the learning effect and performance of neural networks.
>
> In summary, the future development trends and improvement directions of neural networks are diverse. There are still many problems to be solved in this field, but with the continuous progress of technology, artificial intelligence will achieve greater development results.

In the above response, `ChatGPT` gave some possible improvement directions for neural networks. Of course, we can further ask questions about specific issues. For example, enter "How to solve the problem of high computational resource occupancy of neural networks" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> The problem of high computational resource occupancy of neural networks is mainly due to the overly large and complex neural network models, which consume a large amount of computational resources during training and inference. The following are some methods to solve this problem.
>
> 1. **Model compression**: Model compression is a way to reduce computational resource occupancy by reducing the number of parameters in the neural network. Techniques such as pruning, quantization, and distillation can be used to compress the model.
> 2. **Optimize the computational graph**: The computational graph is the basic unit of neural network computation. Optimizing the computational graph can reduce the use of computational resources. For example, reducing the number of computations by sharing layers or subgraphs.
> 3. **Use hardware accelerators**: Dedicated hardware accelerators, such as `GPU`, `TPU`, etc., can effectively improve the running speed and efficiency of neural networks, thereby reducing computational resource occupancy.
> 4. **Data augmentation and batch training**: Data augmentation and batch training can effectively reduce the size of the dataset, thereby reducing computational resource occupancy.
> 5. **Transfer learning**: Transfer learning accelerates the training of new models by leveraging the knowledge of existing models, thereby reducing computational resource occupancy.
>
> In summary, the above methods can effectively solve the problem of high computational resource occupancy of neural networks, but the specific application depends on the specific scenario and requirements.

From the above response, we can see that for the problem of excessively high computational resource occupancy, `ChatGPT` gave some possible solutions. Readers can also ask `ChatGPT` about optimization issues of neural networks they are interested in to obtain more information.

## 10.5 Exercises

In this chapter, we learned neural network-related concepts with `ChatGPT`, including its origin and development history; then we studied the structure of neural networks and different activation functions; the forward propagation and backpropagation algorithms in neural networks are also very important concepts, which are also discussed in this chapter; finally, we asked `ChatGPT` to summarize the limitations of neural networks and outlook their development directions. Below are exercises prepared by `ChatGPT` for everyone, hoping to help consolidate the knowledge of this chapter.

1. Explain what a neural network is and introduce its origin.
2. Please list some application scenarios of neural networks.
3. What are the basic components of a neural network?
4. Explain what a neuron is and briefly introduce its working principle.
5. What is the forward propagation algorithm? How is it used to train neural networks?
6. What is the backpropagation algorithm? How is it used to train neural networks?
7. What are the common activation functions in neural networks? What are their differences?
8. What are the limitations of neural networks? How will they be improved in the future?

# Chapter 11 Mastering Keras with ChatGPT

### Chapter Guide

In Chapter 10, we studied some theoretical knowledge of neural networks together. Many readers may feel unsatisfied and want to train neural network models themselves. This chapter will meet everyone's needs, letting `ChatGPT` guide you to learn how to use `Keras` to train simple neural network models and understand how to set relevant parameters.

### Key Knowledge Points

Through the study of this chapter, you will master the following knowledge:

- What `Keras` is and its advantages.
- Adding layers to a `Sequential` model.
- Setting the model's optimizer, loss function, and evaluation metrics.
- Hyperparameter tuning.

## 11.1 Let ChatGPT Introduce Keras

`Keras` is a high-level `API` for building neural networks. It can conveniently create deep learning models and supports multiple backends, such as `TensorFlow`, `Theano`, etc. Using `Keras`, you can quickly build various types of neural networks, such as fully connected neural networks, convolutional neural networks, recurrent neural networks, etc. `Keras` also provides many commonly used layers and activation functions, as well as tool functions required for training and evaluating models. At the same time, `Keras` also supports model saving and loading, facilitating model reuse and deployment.

In this section, we will learn about `Keras` in detail and learn how to install and configure it.

### 11.1.1 Keras Introduction

First, let `ChatGPT` introduce what `Keras` is. Enter "Please introduce `Keras` in detail" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> `Keras` is a high-level neural network `API`. It is a deep learning framework based on `Python`. It provides a convenient and efficient way to build various types of neural networks, including Convolutional Neural Networks (`CNNs`), Recurrent Neural Networks (`RNNs`), and Hybrid Neural Networks. The design goal of `Keras` is to improve developer efficiency while ensuring code is easy to read and understand.
>
> The following are some main features of `Keras`.
>
> 1. **Simple and easy to use**: The design philosophy of the `Keras API` is simplicity, intuitiveness, and ease of use. Developers can implement various types of neural networks with a small amount of code.
> 2. **Supports multiple backends**: `Keras` supports multiple deep learning backends, including `TensorFlow`, `CNTK`, and `Theano`. This allows developers to choose which backend to use according to their preferences and needs.
> 3. **Modular design**: The modular design of `Keras` allows developers to easily combine different layers (`Layers`) to build their own models. At the same time, `Keras` also provides a series of commonly used layers, such as convolutional layers, pooling layers, fully connected layers, etc.
> 4. **Automatic differentiation**: `Keras` provides automatic differentiation functionality, allowing developers to quickly define their own loss functions and perform backpropagation.
> 5. **Extensibility**: `Keras` is a highly extensible framework. Developers can extend `Keras` functionality by writing their own layers, loss functions, optimizers, etc.
> 6. **High performance**: The underlying implementation of `Keras` adopts various optimization strategies, such as parallel computing and `GPU` acceleration, which can significantly improve training speed.
>
> In summary, `Keras` is a powerful, easy-to-use, and highly extensible deep learning framework, very suitable for developers to quickly build various types of neural networks.

In the above response, `ChatGPT` briefly described the functions of `Keras` and listed some of its features. From the perspective of practical application scenarios, `Keras` can indeed reduce the amount of code for training neural network models and improve our work efficiency.

If we want to know the origin of `Keras`, we can enter "Background of `Keras` birth" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> `Keras` was originally developed by `François Chollet` in 2015. He is a researcher at `Google Brain`. At that time, deep learning was developing rapidly, but building deep neural networks required a large amount of code and mathematical knowledge, which was very difficult for many developers.
>
> Therefore, `François Chollet` dedicated himself to developing an easy-to-use, highly extensible deep learning framework. His goal was to bring deep learning technology to more developers and accelerate the development of deep learning technology.
>
> Against this background, `Keras` was born. `Keras` provides a high-level neural network `API`, allowing developers to quickly build various types of neural networks. Its design philosophy is simplicity, intuitiveness, and ease of use, while also possessing high extensibility and flexibility.
>
> With the gradual maturity and wide application of `Keras`, it has become one of the most popular frameworks in the field of deep learning and has received extensive support and contributions. At the same time, `Keras` has also provided many people with the opportunity to enter the field of deep learning, promoting the popularization and development of deep learning technology.

From the above response, we can see that the initial intention of the `Keras` developer was to let everyone quickly build various types of neural networks. If readers are just starting to get in touch with the construction and training of neural network models, starting with `Keras` is a relatively easy choice.

### 11.1.2 What are the Advantages of Keras

Besides being easy to get started, what other advantages does `Keras` have? Here we let `ChatGPT` give us a summary. Enter "Why use `Keras`" in the `ChatGPT` dialog box, and the following response will be obtained.

> **ChatGPT**  
> There are many benefits to using `Keras`. The following are a few of them.
>
> 1. **Easy to use**: The `Keras API` is designed to be simple and intuitive. Using `Keras` allows you to easily build various types of neural network models. `Keras` also provides a large amount of sample code and documentation, making it convenient for developers to learn and use.
> 2. **Highly extensible**: `Keras` can easily add custom layers, loss functions, optimizers, etc., and integrate other deep learning libraries, allowing developers to build more flexible and powerful deep learning models.
> 3. **Cross-platform support**: `Keras` supports multiple deep learning backends, including `TensorFlow`, `CNTK`, and `Theano`, etc., and can run and deploy on different platforms.
> 4. **Visualization tools**: `Keras` provides visualization tools, such as `TensorBoard`, which can help developers better understand and debug models, improving model accuracy and efficiency.
> 5. **Community support**: `Keras` has a huge community support, including developers, researchers, and enthusiasts. They continuously share code, documentation, tutorials, and solutions, making the use of `Keras` more convenient and faster.
>
> In summary, using `Keras` can help developers quickly build various types of neural network models, while also possessing high extensibility and cross-platform support, making the application of deep learning more extensive and convenient.

