
# Machine Learning Model Evaluation and Selection

## 1. Generalization Error and Model Selection

If a classifier fits the training set data very well, the classification error rate or empirical error can reach or approach zero, and the model's hypothesis covers very specific features in the training samples. In other words, the learning model has learned all information from the training samples, possessing relatively strong learning capability. Obviously, the samples in the test set are different from those in the training set; they are identical or have small differences in certain features, while different or having large differences in others. Therefore, a learner with good training error does not necessarily have good generalization error, and training error cannot be used to evaluate the merits of a learning algorithm. Different learning algorithms have different learning effects on the same dataset; even the same learning algorithm with different parameter settings can produce drastically different learning effects. The goal of practical tasks is to minimize generalization error through model evaluation and model selection. Model selection refers to choosing one learning algorithm from many, and one parameter configuration from many parameter configurations.

The classifier learns a classification hypothesis or model from the training set. Evaluation of the model requires judging its generalization ability on the test set, measuring the model's applicability by calculating classification accuracy on the test set. Training set samples follow an independent and identically distributed (i.i.d.) sample space; test set samples must be new samples different from the training set, and test set samples must also follow an i.i.d. sample space. For example, assuming student scores follow a normal distribution, the score samples of the training set class need to be sampled from a normal distribution with a specific mean and variance, and the score samples of the test set class must also be sampled from a normal distribution with the same mean and variance. The training process can only use training set data; test samples cannot be used in the training process. For instance, the samples in the test set of case learning in Table 2.2 must not appear in the training set of case learning in Table 2.1. Otherwise, test samples used in training will lower the test error, causing the model's generalization ability to be overestimated.

## 2. Dataset Partitioning Methods

### 2.1 Hold-out Method

Generally, the training set and test set are not obtained by two separate samplings, but are partitioned from a single sampled dataset. From a sample space, a dataset `D={(d1，y1)，(d2，y2)，…，(dm，ym)，…，(dM，yM)}` containing `M` samples is collected, from which a portion is selected as the training set `P`, and the remainder as the test set `T`.

If the training set `P` and test set `T` are mutually exclusive sets and together constitute the entire dataset `D`, this partitioning is called the hold-out method. In the aforementioned sample space `|D|=1000`, `|D|` denotes the number of elements in the set; training set size `|P|=800`, test set size `|T|=200`. By definition, `P∪T=D`, `P∩T=Ø`, `|P|+|T|=|D|`. The model is trained using `P`, then tested on `T`, discovering 40 misclassified samples, including actual positive samples judged as negative, and actual negative samples output as positive. According to the error rate calculation, the classification error rate is `(40/200)×100%=20%`, and classification accuracy is `((200-40)/200)×100%=80%`.

### 2.2 Stratified Sampling

The data distribution in the test set must be consistent with that in the training set; otherwise, data skew will lead to more test error. An extreme example is that the training set contains no positive samples; even the best classifier cannot learn what features correspond to positive output from such a training set, and the classifier will not produce positive output on the test set. Even if not extreme, if the proportions of each category in the training set and test set are imbalanced, it will also cause significant bias in results. For example, dataset `D` has 500 positive samples and 500 negative samples; training set `P` has 490 positive and 310 negative; test set `T` has 10 positive and 190 negative. The ratio of positive to negative in `D` is 1:1; in `P` it is 49:31; in `T` it is 1:19. The classifier learns more positive features from `P`, and the classification function or hypothesis tends toward positive; samples on `T` have a higher probability of being classified as positive, yet `T` actually has only 1 positive output (relative to the ratio context, likely meaning very few). This distribution difference will inevitably cause large test error. The reasonable approach is stratified sampling: during dataset partitioning into training and test sets, preserve the category proportions unchanged. According to stratified sampling, the category ratio in `D` is 1:1; `P` has 400 positive and 400 negative; `T` has 100 positive and 100 negative. Even with stratified sampling, there are many ways to partition the dataset. For example, sorting positive samples by `feature_1`, putting the first 400 into set `P`, the last 100 into set `T`. Or vice versa, first 100 into `T`, last 400 into `P`. One can also sort by `feature_2`, `feature_3`, and `feature_n`; such sorting combinations can number in the thousands.

### 2.3 Repeated Random Sub-sampling

Different partitions of the dataset have a large impact on the model's generalization error, often producing large fluctuations. Therefore, the usual practice is to perform many random partitions, then train and test on each pair of training and test sets to obtain evaluation results, and finally take their average as the model's approximate generalization ability. For example, randomly shuffle all positive samples in dataset `D`; the first partition takes samples at positions 1–400 as positive samples in the training set, the remainder as positive samples in the test set; selection of negative samples is the same as for positive. The second partition differs from the first only in the positions taken: samples 2–401 as positive in training, remainder as positive in test. And so on, the 100th partition takes samples 100–500 as positive in training, remainder as positive in test. Each partition slides the previous partition's positions back by a step; if step is 1, 100 partitions are obtained. Compared to a single partition evaluation, using the average of 100 results evaluates the model more stably and reliably.

### 2.4 Cross-Validation

In fact, the expected model should use as much of dataset `D` as possible for training, so it can learn from more data. However, the hold-out method reserves a portion as test set; too small a training set `P` makes the trained model deviate from the expected model. Too large a `P` makes test set `T` too small, making evaluation unreliable. For example, a test set with only two samples yielding one classification error gives 50% accuracy, but a test set with 100 samples yielding one error gives 99% accuracy. Therefore, there is no perfect solution for the training/test split ratio; the usual practice is to rely on experience.

#### 2.4.1 k-Fold Cross-Validation

To improve the stability and authenticity of model evaluation, 5-fold cross-validation is often used to partition the dataset. First, through stratified sampling, dataset `D` is evenly partitioned into 5 subsets, i.e., `D=D1∪D2∪D3∪D4∪D5`, `|D1|=|D2|=|D3|=|D4|=|D5|`, as shown in Figure 2.14. Any two subsets are mutually exclusive, i.e., `D1∩D2=Ø`, `D2∩D3=Ø`, `D3∩D4=Ø`, `D4∩D5=Ø`, `D1∩D3=Ø`, etc.

![](images/txt002_18.jpg "pagenumber_ebook=67,pagenumber_book=58")

Figure 2.14 Partitioned into 5 mutually exclusive subsets

Then, each time one subset is selected as the test set, the rest as the training set. The first experiment selects `D5` as test, `D1`–`D4` as training. The second selects `D4` as test, `D1`–`D3` and `D5` as training. And so on until the fifth selects `D1` as test, `D2`–`D5` as training, as shown in Figure 2.15:

![](images/txt002_19.jpg "pagenumber_ebook=68,pagenumber_book=59")

Figure 2.15 5-Fold Cross-Validation

The average of the 5 evaluation results is returned as the model's performance evaluation; this return value depends on the 5-subset partitioning method. The positive sample set `DP` in `D` is randomly partitioned into 5 equal parts, `DP=DP1∪DP2∪DP3∪DP4∪DP5`, `|DP1|=|DP2|=|DP3|=|DP4|=|DP5|`. The negative sample set `DN` is similarly partitioned into 5 equal parts, `DN=DN1∪DN2∪DN3∪DN4∪DN5`, `|DN1|=|DN2|=|DN3|=|DN4|=|DN5|`. From `DP`, a random `DPi` is selected and removed from `DP`. Then from `DN`, a random `DNj` is selected and removed from `DN`. Finally, `DPi` and `DNj` together form subset `Dt` for 5-fold cross-validation. The above process is called one partition; to reduce bias from partitioning differences, multiple partitions are repeated, each using 5-fold cross-validation, as shown in Figure 2.16:

![](images/txt002_20.jpg "pagenumber_ebook=69,pagenumber_book=60")

Figure 2.16 10 Times 5-Fold Cross-Validation

The flowchart shows 10 times 5-fold cross-validation; the average of 10 results has certain stability, thus avoiding bias introduced by different data partitions to some extent. 5-fold cross-validation can be extended to 10-fold cross-validation; the more general cross-validation is called k-fold cross-validation. The larger `k`, the closer the training set is to the dataset, and the closer the trained model is to the expected model; simultaneously, the number of subset partitioning methods increases, and runtime overhead becomes relatively larger.

## 3. Model Selection and Parameter Tuning

### 3.1 Learning Algorithms and Hyperparameters

Machine learning tasks involve two aspects: selecting a learning algorithm and tuning the algorithm's parameters. Common learning algorithms include `Linear Models`, `Support Vector Machines`, `Decision Trees`, `Neural Networks`, `Naive Bayes Classifiers`, `Principal Component Analysis`, and `Topic Models`, etc. Each learning algorithm has corresponding parameter settings and adjustments, called parameter tuning or hyperparameter tuning. The combination space of parameter configurations is very large; the goal of tuning is to find the parameters that train the best-performing model. Although one could try training a model for every parameter configuration, the time cost is too high. Even if parameter values are discrete, as the number of parameters increases, training all configuration models becomes impossible, let alone many parameters being continuous. In fact, the empirical approach is to set a range and step size for parameters. For example, the `LDA` topic model has a prior hyperparameter `alpha` related to the number of topics, defaulting to 1, and a prior hyperparameter `beta` related to vocabulary size, defaulting to 0.01. For `alpha`, set range `[0, 10]`, step 1, candidate values `{1, 2, 3, 4, 5, 6, 7, 8, 9, 10}` (10 values). For `beta`, set range `[0, 0.1]`, step 0.01, candidate values `{0.01, 0.02, 0.03, 0.04, 0.05, 0.06, 0.07, 0.08, 0.09, 0.1}` (10 values). Without considering learning rate, just tuning `alpha` and `beta` in small ranges requires evaluating 10×10=100 models. `Support Vector Machines` tune penalty coefficient and kernel function coefficient; the number of hyperparameters exceeds 3; each configuration corresponds to a model, requiring training and evaluation on training and test sets, making the tuning and learning workload very large. Additionally, if the tuning step is too large, the model may miss the optimal performance parameters.

### 3.2 Validation Set and Tuning Process

Parameter tuning requires a portion of data to verify the performance of each parameter configuration model. Therefore, dataset `D` must be partitioned into three parts: training set `P` (Practicing), validation set `V` (Validating), and test set `T` (Testing). For example, the parameter `n_components` (number of principal components) in `Principal Component Analysis` has candidate values `|N_comps|={1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 15, 20, 25, 30, 35, 40, 45, 50, 60, 70, 80, 90, 100, …}`. Each parameter setting `n_components_i` corresponds to a model `model_i`, where `n_components_i` is the `i`-th element of set `N_comps`, `i` ranges from 1 to `|N_comps|`, `|N_comps|` being the number of elements in `N_comps`. Model `model_i` learns on training set `P`, then evaluates its performance `performance_i` on validation set `V`. Compare performance of `|N_comps|` models, return the maximum `performance_MAX=max(performance_i)`. Based on the returned element index `MAX`, the best parameter configuration is `n_components_MAX`, corresponding model `model_MAX`. A new training set `P'` is composed of training set `P` and validation set `V`; model `model_MAX` is retrained on `P', expecting to train on a dataset with maximum sample size to approach the true model. To tune parameters, a portion of the original training set is used to evaluate the selected parameters, called the validation set. Thus, the training set trains the model, the validation set performance tunes parameters, and the original test set evaluates the model's generalization ability in a way approximating and simulating the actual user environment.

## 4. Regression Task Performance Metrics

The applicability of a learning algorithm in application and the performance metrics of a learner are both related to the learning model's generalization ability. How to evaluate a learner's performance requires not only certain measurement methods but also corresponding objective evaluation criteria. Different evaluation criteria often yield vastly different results; therefore, it is difficult to simply judge the superiority of different models' generalization ability by a single metric. In regression tasks, Mean Squared Error, Root Mean Squared Error, and Mean Absolute Error are commonly used to measure learner performance. Sample `xi`'s predicted output is `h(xi)`, actual true value is `yi`, validation set has `m` samples.

### 4.1 Mean Squared Error (MSE)

Mean Squared Error, called `MSE` (Mean Squared Error), is a criterion measuring the deviation between a learning model's predicted values and actual true values. It first sums the squared differences between predicted and true values for each sample, then takes the average:

![](images/txt002_21.jpg "pagenumber_ebook=71,pagenumber_book=62")

The MSE on the validation set has the same form as the loss function in regression learning tasks. On the training set, the loss function is minimized via stochastic gradient descent to learn the model's hypothesis function or mapping function `h(xi)`. Since MSE is a squared relationship of model output, it is not convenient for intuitively describing model effects, especially for horizontal comparison of model output data magnitudes. For example, last year a region's household disposable annual income was 100,000 yuan; a regression model predicts next year's income. If evaluating with MSE, the error value could reach tens of millions, obviously not matching intuitive understanding.

### 4.2 Root Mean Squared Error (RMSE)

To make the model evaluation magnitude consistent with actual observation values, Root Mean Squared Error can be used. RMSE (Root Mean Squared Error) first sums the squared differences between predicted and true values for each sample, then takes the square root of the average:

![](images/txt002_22.jpg "pagenumber_ebook=71,pagenumber_book=62")

RMSE is smaller than MSE, but outliers in the dataset will have a larger impact on RMSE. In the above example, if the household disposable income dataset contains outliers far from the mean, the `RMSE` value could reach hundreds of thousands or even millions.

### 4.3 Mean Absolute Error (MAE)

To further lower the evaluation value to be on the same level as actual observations, Mean Absolute Error can be used. MAE (Mean Absolute Error) first sums the absolute differences between predicted and true values for each sample, then takes the average:

![](images/txt002_23.jpg "pagenumber_ebook=72,pagenumber_book=63")

The metric unit for predicting a region's household disposable annual income is yuan, model output averages around 100,000; the metric unit for predicting developing country adolescent height is centimeters, model output averages around 150. Therefore, when comparing the merits of two models with different data metric units, one cannot directly compare using MSE, RMSE, or MAE. Whether MAE, MSE, or RMSE, model evaluation is an absolute value under a scale unit; closer to 0 is better.

### 4.4 Coefficient of Determination (R²)

The coefficient of determination is a relative coefficient measuring learning model performance, called `R2` (R-square), related to Residual Sum of Squares and Total Sum of Squares. Residual Sum of Squares, called `RSS` (Residual Sum of Squares), is the sum of squared differences between predicted and true values for each sample. Total Sum of Squares, called `TSS` (Total Sum of Squares), is the sum of squared differences between true values and the mean for each sample. `R2` is calculated by subtracting the ratio of `RSS` to `TSS` from 1:

![](images/txt002_24.jpg "pagenumber_ebook=72,pagenumber_book=63")

`R2` values typically range in `[0, 1]`. When the learning model fits the data very well, `RSS` tends to 0, `R2` gradually approaches 1. When the learning model explains the data very poorly, using a value close to the mean instead of predicted values, `RSS/TSS` tends to 1, `R2` gradually approaches 0.

## 5. Classification Task Performance Metrics

### 5.1 Limitations of Accuracy and Error Rate

In binary classification or multi-class classification, although error rate or accuracy can depict learning model performance, they are too broad. After all, error rate refers to the proportion of all misclassified samples, including actual positive predicted as negative, and actual negative predicted as positive. Accuracy refers to the proportion of all correctly classified samples, including actual positive predicted as positive, and actual negative predicted as negative. For example, among cases predicted as winning, what proportion are actually won; or among actually won cases, what proportion are predicted as won. If only error rate or accuracy describes predictive performance, it clearly cannot satisfy application needs. Assume 1000 case samples contain 500 wins and 500 losses. Model A predicts 400 of 500 wins as wins, and 0 of 500 losses as wins. Model B predicts 450 of 500 wins as wins, and 100 of 500 losses as wins. Model A has 400 true positives and 500 true negatives, accuracy 90%, error rate 10%. Model B has 450 true positives and 400 true negatives, accuracy 85%, error rate 15%. If considering error rate or accuracy, Model A seems more favored. In fact, it is not so; if the user focuses on *all* won cases, Model B has an advantage. Under the criminal justice ideal of "better to let a guilty person go free than to convict an innocent one," Model B achieves fairness in legal performance with fewer won cases misclassified.

### 5.2 Precision and Recall

In book literature retrieval and information retrieval, retrieval by keywords returns results containing relevant information the user cares about and irrelevant information they don't. Precision describes how many of the returned results are relevant information the user cares about; Recall describes how much of all the relevant information the user cares about was hit or retrieved.

### 5.3 Confusion Matrix

Generally, a confusion matrix describes the correspondence between a learning model's predicted categories and actual categories. `TP` (True Positive): number of samples predicted as positive that are actually positive, or actually positive predicted as positive. `TN` (True Negative): number of samples predicted as negative that are actually negative, or actually negative predicted as negative. `FP` (False Positive): number of samples predicted as positive that are actually negative, or actually negative predicted as positive. `FN` (False Negative): number of samples predicted as negative that are actually positive, or actually positive predicted as negative. Total samples equals TP + TN + FP + FN. The confusion matrix for binary classification results is shown in Table 2.4.

Table 2.4 Binary Classification Confusion Matrix

![](images/txt002_25.jpg "pagenumber_ebook=73,pagenumber_book=64")

### 5.4 Precision and Recall Calculation

When retrieving information, the information the user cares about is marked as positive. Precision is defined as the accuracy of the positive class (`Precision`), i.e., the percentage of samples predicted as positive that are actually positive, indicating the probability that a sample judged as positive is actually positive. Recall is defined as the recall rate of the positive class (`Recall`), i.e., the proportion of correctly identified positive samples to the total actual positive samples, indicating the probability that a positive sample can be correctly identified:

![](images/txt002_26.jpg "pagenumber_ebook=74,pagenumber_book=65")

