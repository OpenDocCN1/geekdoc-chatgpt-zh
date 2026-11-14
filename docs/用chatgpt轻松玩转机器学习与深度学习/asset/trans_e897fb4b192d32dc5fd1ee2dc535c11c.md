

### 2.4.3 Model Training and Validation

After completing the above work, we can officially start model training. The code for training and validating the model is as follows.

![](Images/9787301342640_Figure-0044-0044.jpg)![](Images/9787301342640_Figure-0045-0045.jpg)

In this code, we first created a logistic regression model, then let the model fit the data in the training set. After training, we used the `score()` method to validate the model's accuracy on the test set and printed the result. Running the code yields the following result.

![](Images/9787301342640_Figure-0045-0046.jpg)

Judging from the program's returned results, our trained model achieved **100% accuracy** on the test set. That is to say, through learning from the training set, the model can classify every single sample in the test set into the correct category. If you obtained the same result, congratulations! You have achieved a **"success right out of the gate"** in the field of machine learning!

> **Warm Tip**
>
> For beginners, the code above also involves some knowledge that hasn't been explained yet (such as what logistic regression is). This chapter only aims to introduce the overall workflow of a small machine learning project; specific knowledge will be explained in detail in later chapters.

## 2.5 Exercises

Through the study of this chapter, I believe many readers are **eager to try** and want to practice hands-on. Below are some exercises arranged for you.

1.  With the help of ChatGPT, find and download a dataset you like.
2.  Load and read your downloaded dataset in Jupyter Notebook.
3.  Check the format of your dataset: how many rows and columns does it have?
4.  Check whether there are duplicate values or missing values in your dataset.
5.  Ask ChatGPT to clarify the function of the parameter `hue` in Seaborn's `pairplot`.
6.  With the help of ChatGPT, select a feature from your dataset and plot a histogram.
7.  View the statistical information of each feature in your dataset.
8.  Perform a t-test on the sample data in your dataset.

# Chapter 3 Let ChatGPT Lead Us to Master Linear Models

## Chapter Guide

In Chapter 2, ChatGPT helped us find some small datasets suitable for learning and conducted interesting experiments in Jupyter Notebook. Through these experiments, we learned the general steps and some simple implementation methods of Exploratory Data Analysis (EDA). With the help of ChatGPT, we used a simple linear model to complete a small classification task and achieved **100% accuracy**. In this chapter, we will delve into linear models in machine learning.

## Key Points

Through the study of this chapter, you will master the following knowledge:

- Basic concepts of linear models.
- Principles of linear regression.
- Principles and implementation of the gradient descent algorithm.
- Linear models for classification tasks.
- Concepts of regularization and linear models using regularization.

## 3.1 Let ChatGPT Tell Us What a Linear Model Is

To understand what a linear model is, we can type "What is a linear model" into the ChatGPT dialog box and get the following reply.

> **ChatGPT**
>
> A linear model is a statistical model used to establish a linear relationship between input variables and output variables. The basic form of this model is a linear equation where the output variable is a linear combination of input variables plus a constant term. Linear models can be used for regression and classification problems and are widely applied in statistics, machine learning, and data science.
>
> Common linear models include the following.
>
> 1.  Linear Regression Model: Used to predict the value of continuous output variables.
> 2.  Logistic Regression Model: Used to predict the value of output variables in binary classification problems.
> 3.  Polynomial Regression Model: A regression model used to fit higher-order polynomial functions.
> 4.  Ridge Regression Model: A regression model used to handle multicollinearity problems.
> 5.  Lasso Regression Model: A regression model used for feature selection and sparsity.
> 6.  Linear Discriminant Analysis Model: A model used for multi-class classification.
>
> Linear models have many advantages, such as strong interpretability and high computational efficiency, but they are usually not suitable for non-linear problems.

From a beginner's perspective, ChatGPT's reply might seem a bit abstract. Next, we will learn common linear models through some practical examples.

### 3.1.1 Understanding Linear Regression with a Simple Example

To better understand the principle of linear regression, we can ask ChatGPT to provide an example code. Type "Python example code for linear model" into the ChatGPT dialog box, and you will see the following reply from ChatGPT.

> **ChatGPT**
>
> Below is an example code implementing a linear regression model using Python.

![](Images/9787301342640_Figure-0047-0047.jpg)![](Images/9787301342640_Figure-0048-0048.jpg)

This code first uses the `NumPy` library to prepare a set of input and output variables, then uses the `LinearRegression` class from the `scikit-learn` library to create a linear regression model object, and uses the `fit()` method to fit the data. Next, it uses the `predict()` method to predict a new input variable and outputs the corresponding output variable. Finally, it uses the `Matplotlib` library to plot the regression line and a scatter plot of the original data points.

Next, we can create a new Notebook file in Jupyter Notebook, paste the code generated by ChatGPT into a cell, and try running it.

Below, we analyze step by step what each section of code does.

![](Images/9787301342640_Figure-0048-0049.jpg)

The code above imports the `NumPy` library for scientific computing, the `Matplotlib` library for plotting, and the linear regression model `LinearRegression` from the widely used `scikit-learn` library in machine learning. The next section of code uses the `NumPy` library to generate demonstration data.

![](Images/9787301342640_Figure-0048-0050.jpg)

The code above uses the `array` function of the `NumPy` library to generate two arrays. The array serving as features is named `x`, and the array serving as target values is named `y`. The `reshape()` method is used to change the shape of the array. `(-1,1)` means converting the array into a shape with an arbitrary number of rows but only 1 column.

With the data ready, we can try using the linear regression model to fit the data, which is the function of the following code.

![](Images/9787301342640_Figure-0048-0051.jpg)

Running the above code, the model completes the fitting in an instant. Next, we can call the model to make predictions on new samples. For example, if the `x` value of a new sample is 6, the model can predict its `y` value. The code is as follows.

![](Images/9787301342640_Figure-0048-0052.jpg)

Running this code yields the following result.

![](Images/9787301342640_Figure-0049-0053.jpg)

From the code's running result, we can see that the model successfully predicted the `y` value of the new sample. When the `x` value of the new sample is 6, the model predicts its `y` value to be 9.3. The code provided by ChatGPT also visually demonstrates the principle of the model with a graph.

![](Images/9787301342640_Figure-0049-0054.jpg)

Here, we added a line of code to control the figure size. At the same time, we use the `scatter()` function of the `Matplotlib` library to draw a scatter plot and the `plot()` function to draw a line plot. Running the code produces the result shown in Figure 3-1.

In Figure 3-1, the dots are the sample data generated using the `NumPy` library. For example, the dot in the lower left corner is the first sample we generated, with an `x` value of 1 and a `y` value of 2, and so on. The straight line in the figure is the linear regression model fitted according to the generated samples. This line also explains the origin of the name "Linear Regression" very well.

![](Images/9787301342640_Figure-0049-0055.jpg)

Figure 3-1 Visualizing the Linear Regression Model

### 3.1.2 Brief Introduction to the Principle of Linear Regression

After intuitively understanding the linear regression model, we can also ask ChatGPT to introduce the principle of linear regression. Type "Briefly introduce the principle of linear regression, no Markdown format" into the ChatGPT dialog box. Here we emphasize asking ChatGPT not to return Markdown-formatted text to avoid strange symbols in the reply. Sending our request yields the following reply.

> **ChatGPT**
>
> Linear regression is a common machine learning algorithm used to establish a linear relationship model between an input variable and an output variable. Its basic principle is to find the optimal linear function to fit the known input-output relationship of the data. Specifically, linear regression finds the optimal coefficient values by minimizing the squared error between predicted values and actual values, thereby establishing a model that can describe the linear relationship between input and output variables. This model can be used to predict the output variable corresponding to new input variables.
>
> The linear regression model can be expressed as
>
> ```
> Y = b0 + b1 * X1 + b2 * X2 + … + bn * Xn + ε
> ```
>
> Where `Y` is the output variable, `X1, X2,…, Xn` are input variables, `b0, b1, b2,…, bn` are coefficients, and `ε` is the error term. The model finds the optimal coefficient values to minimize the squared error between predicted and actual values. Linear regression can use optimization algorithms such as gradient descent to find the optimal coefficient values, thereby obtaining a model usable for predicting new output variables.

Combining ChatGPT's reply, we can better understand the example above. In this example, our model has only one input variable `x`, so the formula can be expressed as

```
y = b0 + b1 * x + ε
```

You may have noticed that this is actually the equation of a straight line. During the fitting process, the model aims to find the best coefficient `b1` (corresponding to the slope of the line) and the error term `b0` (corresponding to the intercept of the line). If we want to check the slope and intercept of this line, we can ask ChatGPT for an example. Type "Python code to view linear regression parameters" into the ChatGPT dialog box to get the following reply.

> **ChatGPT**
>
> The following is the code to view the parameters of the linear regression model in Python's scikit-learn library.

![](Images/9787301342640_Figure-0050-0057.jpg)

In the code above, we first import the `LinearRegression` class from the `scikit-learn` library; then create a linear regression model object named `model`; next, train the model with some sample data `X` and target data `y`; finally, use the `coef_` and `intercept_` attributes to view the model's coefficients and intercept. Among them, the `coef_` attribute stores the model's coefficient values, and the `intercept_` attribute stores the model's intercept.

From the code given by ChatGPT, we can see that to view the parameters of a linear regression model, we just need to use the model's `coef_` and `intercept_` attributes. We have already completed the model fitting in the previous example, so we only need to use the following two lines of code.

![](Images/9787301342640_Figure-0050-0058.jpg)

Paste these two lines of code into Jupyter Notebook and run them to get the following result.

![](Images/9787301342640_Figure-0051-0059.jpg)

From the code above, we can see that the equation of our trained linear regression model can be roughly expressed as `y = 1.5x + 0.3`. Therefore, when we give an `x` value of 6, the model can calculate the `y` value as `1.5 * 6 + 0.3 = 9.3`. Of course, if we have multiple input variables, the model is no longer a straight line but a hyperplane.

### 3.1.3 What is Gradient Descent

Reading here, some readers might ask: How does the model determine the optimal slope and intercept? According to ChatGPT's previous reply, we know that linear regression can use optimization algorithms like gradient descent to find the optimal coefficient values. So, what does gradient descent mean? We'll hand this question to ChatGPT as well. Type "What is gradient descent" into the ChatGPT dialog box to get the following reply.

> **ChatGPT**
>
> Gradient Descent is an optimization algorithm used to minimize the value of a function, which is usually called the Loss Function. In machine learning and deep learning, gradient descent is widely used in model training. By continuously adjusting model parameters, the value of the loss function gradually decreases, eventually yielding a better model.
>
> The basic idea of gradient descent is to calculate the partial derivatives (gradients) of the loss function with respect to the model parameters, and update the model parameters in the opposite direction of the gradient, so that the value of the loss function continuously decreases. This process can be expressed mathematically as
>
> ```
> θ = θ - α * ∇J(θ)
> ```
>
> Where `θ` represents model parameters, `J(θ)` represents the loss function, `∇J(θ)` represents the gradient of the loss function with respect to `θ`, and `α` represents the learning rate, i.e., the step size for each parameter update.
>
> There are two main implementations of gradient descent: Batch Gradient Descent and Stochastic Gradient Descent. Batch gradient descent uses the gradients of all training samples each time it updates model parameters, so the computation is large, but the update direction is more accurate, guaranteeing convergence to the global optimum. Stochastic gradient descent uses only the gradient of one training sample each time it updates model parameters, so the computation is small, but the update direction is less accurate and may converge to a local optimum.
>
> Besides batch and stochastic gradient descent, there is a compromise method called Mini-batch Gradient Descent. It uses the gradients of a small batch of training samples each time it updates model parameters, which ensures a more accurate update direction than stochastic gradient descent while reducing computation. It is currently one of the most commonly used gradient descent algorithms.

Next, we can also ask ChatGPT to help us better understand the concept of gradient descent with example code. Type "Demonstrate gradient descent in linear regression with Python code" into the ChatGPT dialog box to get the following reply.

> **ChatGPT**
>
> Below is a Python code demonstration of linear regression using the gradient descent algorithm.

![](Images/9787301342640_Figure-0051-0061.jpg)![](Images/9787301342640_Figure-0052-0062.jpg)

This code generates some random linear data, then uses the gradient descent algorithm to train a linear regression model, and plots the data scatter plot and the fitted line. In the code, we first define the loss function and gradient function, then initialize model parameters, set the learning rate and number of iterations, and finally execute the gradient descent algorithm loop. In each loop, we calculate the loss function and gradient, and use the learning rate and gradient to update model parameters. Finally, we obtain the optimal model parameters and use them to plot the fitted line.

Below, we paste the code into Jupyter Notebook and run it in sections. First is the data construction part.

![](Images/9787301342640_Figure-0053-0063.jpg)

In this code, the `linspace()` function of the `NumPy` library is first used to generate an arithmetic progression ranging from 0 to 10 with a quantity of 100, serving as the input variable of the samples. Then, random numbers following a normal distribution are added as noise to the output variable `y`.

Next is the code defining the loss function and gradient function.

![](Images/9787301342640_Figure-0053-0064.jpg)

The code above first multiplies the input variable `x` with the parameter matrix `theta` to obtain the predicted value `y_pred` of the output variable `y`. Then, it calculates the mean of the squared differences between all predicted values `y_pred` and true values `y` to obtain the value of the loss function. Then, it performs matrix multiplication of the difference between predicted values `y_pred` and true values `y` with the input variable `x`, and divides by the number of samples to obtain the value of the gradient function.

Next, we can use the loss function and gradient function to execute the gradient descent algorithm.

![](Images/9787301342640_Figure-0053-0065.jpg)

The code above uses the `zero()` function of the `NumPy` library to specify the initial parameter `theta` as 0, then sets the number of iterations to 1000. Each iteration updates the parameters based on the loss function and learning rate, and the program prints the model's loss function and latest parameters every 100 iterations. Running the code yields the following result.

![](Images/9787301342640_Figure-0054-0066.jpg)

From the code's running results, we can see that the model's initial loss function was about 69.2; after 100 iterations, the loss function dropped to about 1.1, and the parameters became `[0.55028868 2.0848709]`; thereafter, the loss function continued to drop, and after 900 iterations, the loss function dropped to about 0.9, while the parameters had updated to `[1.25079686 1.97954236]`.

Finally, ChatGPT also visually demonstrates the final model.

![](Images/9787301342640_Figure-0054-0067.jpg)

Running the code yields the result shown in Figure 3-2.

This time, we did not use the built-in linear regression model in the `scikit-learn` library, but instead defined the loss function and gradient function ourselves, using the gradient descent algorithm to fit a linear regression model. The dots in Figure 3-2 are our constructed sample data, and the straight line is the linear regression model that fits the data best, found by the gradient descent algorithm after several iterations. This process explains very well how the linear regression model finds the optimal parameter combination.

![](Images/9787301342640_Figure-0054-0068.jpg)

Figure 3-2 Linear Regression Model Obtained Using Gradient Descent Algorithm

> **Warm Tip**
>
> The code above is mainly to help you understand the process of the linear regression model finding optimal parameters through the gradient descent algorithm. If beginners feel strained reading the code, don't be nervous; this part of the code is not required to be mastered.

## 3.2 Linear Models Can Also Be Used for Classification

Through the study of Section 3.1, we have already understood the basic knowledge of the linear regression model. The example in Section 3.1 is a very simple regression task where the model inputs continuous numerical values. However, the capabilities of linear models are not limited to this. ChatGPT introduced two linear models that can be used for classification tasks—Logistic Regression and Linear Discriminant Analysis. Below, we take Logistic Regression as an example to study how linear models are used for classification tasks.

### 3.2.1 Brief Introduction to Logistic Regression

To learn the relevant knowledge of Logistic Regression, we can ask ChatGPT to introduce some basic concepts first. Type "Briefly introduce Logistic Regression" into the dialog box to get the following reply.

> **ChatGPT**
>
> Logistic Regression is a widely used statistical analysis method, typically used for binary classification problems, i.e., dividing data into two categories (such as yes/no, true/false, etc.). Logistic Regression is based on the linear regression model, but its output is a probability value between 0 and 1, representing the likelihood of belonging to a certain category. Logistic Regression achieves this by mapping the output of the linear function to the probability range.
>
> The main idea of Logistic Regression is to multiply the input features by a set of weights and map the result to between 0 and 1 through an activation function called the `Sigmoid` function. During the training process, we optimize these weights to maximize the model's likelihood of the observed data. Specifically, we minimize the Cross-Entropy Loss Function, which measures the difference between the probability output by the model and the actual labels.

