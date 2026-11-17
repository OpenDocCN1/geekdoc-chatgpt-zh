

### 4.1.4 Unsupervised Learning

If the way students learn in school is called supervised learning, then children who don't go to school can also engage in unsupervised learning through their own observation and understanding of the world. However, what they learn is spontaneous and arbitrary, and the final learning outcomes are unpredictable. The same applies to artificial intelligence. In 2017, there was a news story: two AI robots created by American scientists invented a language only they could understand through self-study, and these two robots had already begun communicating in a language only they could understand, which caused great panic among people.

Unsupervised learning has been validated in many application scenarios. If you open a garment factory to produce clothes, how do you determine clothing sizes S, M, and L? If you possess the height data of the entire Chinese population, you can determine this through unsupervised learning. Precisely because unsupervised learning lacks manual labeling, it has not yet reached the accuracy and effectiveness of supervised learning to date. Therefore, unsupervised learning has always been criticized by some scientists in the AI community. But in recent years, unsupervised learning has become hot again, with some scientists even claiming that unsupervised learning is the future of artificial intelligence.

Currently, the principle of the popular `Generative Adversarial Network (GAN)` in unsupervised learning is to connect a generative network and a discriminative network. The generative network is responsible for generating data intended to try to deceive the discriminative network. Through the adversarial learning of the two networks, some surprising results have been obtained, such as AI technology that can generate photo-realistic images from text strings or hand-drawn sketches.

There are usually three evaluation metrics for AI: `Precision`, `Recall`, and `Accuracy`. In unsupervised learning, these three metrics are very important, and people usually use these three metrics to evaluate unsupervised learning.

**Precision**: For a given test dataset, the ratio of the number of samples correctly classified by the classifier to the total number of samples. Simply put, precision means "querying accurately." Like a policeman catching thieves: if 10 people are caught, and 9 of them are thieves, then the precision is 90%.

**Recall**: For a given test dataset, the number of all correctly classified samples correctly found by the classifier. Simply put, recall means "querying comprehensively." Again, using the policeman catching thieves example: there are 100 people, 20 of whom are thieves; if 10 thieves are caught, the recall is 50%.

**Accuracy**: Represents the proportion of correct identifications. That is, the probability that good people are identified as good people and bad people are identified as bad people. Continuing with the policeman catching thieves example: there are 100 people, 20 of whom are thieves; 10 people are caught, but 1 of them is not a thief, then the accuracy is 90%.

Precision, Recall, and Accuracy are key indicators for measuring AI algorithms. The higher these three indicators, the better the adaptability of the algorithm.

### 4.1.5 Semi-Supervised Learning

Supervised learning is used when there is the capability to label data and perform classification and regression based on the labeled information; unsupervised learning is training without labeled information, usually used for clustering. Sometimes, the data at hand often has only a portion labeled rather than fully labeled; at this time, semi-supervised learning can be used to complete the training. The "National Government Website Monitoring Project" is a good example.

The National Government Website Monitoring Project monitored over 90,000 government websites nationwide and nearly 25 billion pages of various levels of government websites, as shown in Figure 4-12. The project's task is to monitor whether these web pages have anomalies and which pages users are more interested in. For up to tens of billions of pages, it is impossible to label all pages; only a few tens of thousands of pages are labeled. If the unlabeled sample set is directly discarded, it is not only a pity but will also lead to data distortion due to insufficient training samples, thereby affecting the effect of machine learning. How to effectively utilize unlabeled data becomes the key.

![](converter-master/example/165201/content_633677_13.jpg)

Figure 4-12

The simplest method is to label the unlabeled pages, but this comes with huge labor costs. If it takes a person 0.5 minutes to label a page, to label 2.5 billion pages, one person working 365 days a year without rest would take over 2,300 years to complete.

The project team thus adopted a semi-supervised learning method to complete the labeling work. First, a learner is trained using the labeled sample dataset; then, based on this learner, predictions are made on the unlabeled samples; next, the predicted result samples are classified, and samples with high uncertainty and low classification confidence are re-labeled twice; finally, the data is expanded into the training set, the learner is retrained, and the final result is generated, as shown in Figure 4-13.

![](converter-master/example/165201/content_633677_14.jpg)

Figure 4-13

Obviously, semi-supervised learning essentially still belongs to supervised learning, but it semi-automates the processing of training data samples. Compared with supervised learning, the learning cost is greatly reduced. For various algorithms of supervised learning and unsupervised learning, if the dataset conforms to the characteristics of semi-supervised learning, the semi-supervised learning method can be adopted.

### 4.1.6 Reinforcement Learning

Reinforcement learning is also known as reinforcement learning (note: the Chinese term "再励学习/评价学习" maps to the same English term) or evaluative learning. If described with a standard definition, the goal of reinforcement learning is to learn an optimal `Policy`, which allows the `Agent` to take an `Action` based on the current `State` in a specific `Environment` to maximize the return. Reinforcement learning is often confused with supervised learning and unsupervised learning.

Reinforcement learning regards learning as a trial-and-evaluation process, as shown in Figure 4-14. The agent selects an action to act on the environment; the environment changes state after accepting the action and simultaneously generates a reinforcement signal, which is fed back to the agent. The agent selects the next action based on the reinforcement signal and the current state of the environment. The principle of selection is to increase the probability of receiving positive reinforcement. The selected action not only affects the immediate reinforcement value but also affects the state of the environment at the next moment and the final reinforcement value.

![](converter-master/example/165201/content_633677_15.jpg)

Figure 4-14

Supervised learning is like a student taking an exam with a tutor standing next to them; every time the student finishes a question, the tutor immediately tells them whether they got it right or wrong. But the actual situation is complex; many practical problems have no standard answers, only which answer is more suitable. The process of reinforcement learning is like training an animal: every time the animal performs a behavior, the trainer needs to give timely feedback, and this good-or-bad feedback will influence its behavior next time. AI reinforcement learning is like this: through repeated reinforcement learning processes, the machine learns which behavior can obtain the best return. Precisely because reinforcement learning has the characteristic of real-time feedback, it is widely used in the field of robotics.

Both supervised learning and reinforcement learning obtain a relationship mapping from input data to output results through algorithms. Supervised learning gives the relationship between input and output, telling the algorithm what input corresponds to what output. Reinforcement learning gives feedback information, judging whether this behavior is good or bad. Of course, because reinforcement learning gives feedback information, the whole process has latency; therefore, sometimes it takes multiple processes before knowing whether the choice at a previous node was correct.

## 4.1 Machine Learning

Many experts classify machine learning as an interdisciplinary subject because machine learning involves multiple disciplines such as probability theory, statistics, and algorithm theory. Common definitions of machine learning include the following.

> "Machine learning is a science of artificial intelligence; the main research object of this field is artificial intelligence, especially how to improve the performance of specific algorithms in empirical learning."
>
> "Machine learning is the study of computer algorithms that can improve automatically through experience."
>
> "Machine learning uses data or past experience to optimize the performance criteria of computer programs."

It is not necessary to understand machine learning starting from these complex and obscure definitions; understanding from a simple and easy-to-grasp concept may be more effective with half the effort. The author believes that "machine learning is a method of data analysis."

With the development of the Internet, the amount of data is growing larger, and how to monetize data has become the primary issue for enterprises. In fact, unprocessed raw data has little use value. Valuable data is obtained by using correct analysis techniques to gain richer data cognition, clearer scenario insights, and more accurate data portraits, thereby obtaining data that can assist decision-making; only then does the significance of data become prominent. The role of machine learning is to analyze massive data and mine its meaning and value.

As shown in Figure 4-1, data analysis can be divided into the following four categories.

![](converter-master/example/165201/content_633678_0.jpg)

Figure 4-1

**Current Status Description**: Describe the current state of the real world based on existing data.

**Anomaly Diagnosis**: Discover anomalies in existing data and understand the reasons for their occurrence.

**Future Prediction**: With time as a dimension, use historical data to view data change trends and predict the probability of a future event occurring.

**Behavioral Norms**: Consider how to change current or future data to meet future expectations.

What machine learning can do is also the above-mentioned things. Machine learning describes the real world and gains insights into it through learning from data, and predicts the evolution of the future world.

### 4.1.1 Logical Differences Between Traditional Software and Machine Learning

In the Internet era, the product manager's job is to analyze existing business and design a reasonable system processing logic based on the business process; development engineers implement the product based on the designed system processing logic. Therefore, the computation process of traditional software from input to output is designed, fixed, and definite, as shown in Figure 4-2. The engineer's role is to design the process and let the data be processed according to the designed process.

![](converter-master/example/165201/content_633678_1.jpg)

Figure 4-2

Unlike Internet products and traditional software products, AI products automatically construct logical structures based on existing data. AI products repeatedly learn from data through machine learning algorithms; there is no need to tell the computer the business logic, and the AI algorithm can find the hidden rules and meanings within.

AI products input training data (input data + result data) into an AI algorithm to derive a model, then input new data into this model for computation, thereby obtaining results and achieving prediction and judgment, as shown in Figure 4-3. The model derived from data and algorithms replaces the software logic structure designed by product managers or requirement analysts.

![](converter-master/example/165201/content_633678_2.jpg)

Figure 4-3

The difference between machine learning and traditional methods is huge; machine learning subverts the original program thinking. The traditional program thinking is for humans to analyze data, find rules, and obtain final results through "input data + rule verification"; machine learning does not require humans to analyze and judge data but hands the work of analysis and judgment to the machine. After inputting training data, the machine finds the rules hidden in the data according to the algorithm and applies these rules in practice.

### 4.1.2 Learning Paradigms of Machine Learning

Depending on the training method, machine learning is divided into four major learning paradigms: supervised learning, unsupervised learning, semi-supervised learning, and reinforcement learning. The fundamental difference between these four learning paradigms lies in the difference in data content and training modes.

**Supervised Learning (also known as Supervised Learning)**: Training through labeled data to obtain a model, and processing unknown data through this model.

**Unsupervised Learning**: No training data samples in advance; instead, analyzing and modeling data through algorithms to find the rules within.

**Semi-Supervised Learning**: In semi-supervised learning training, only a portion of the data used is labeled, while the vast majority is unlabeled.

**Reinforcement Learning**: Reinforcement learning also uses unlabeled data, but it can know whether you are getting closer to or further from the correct answer through some method.

Supervised learning is very easy to understand; it follows human-defined specifications and continuously trains through machine learning to achieve the optimal solution under this specification. Why does unsupervised learning exist then? Will automated modeling without training go astray? For example, someone knows nothing about music, but by listening more, they can distinguish between classical, modern, jazz, and other genres in a short time. Similarly, we feed music from different eras, in large quantities, and of different types to the machine, and the machine can also find the characteristics of the music and complete classification based on the use of high, mid, and low tones and rhythm in the audio, without us telling the machine in advance what type of music it is.

Machine learning can solve many problems, but through induction and summary, they can be divided into `Classification`, `Regression`, `Clustering`, `Dimensionality Reduction`, etc., as shown in Figure 4-4. There are many algorithms for solving these problems; common ones are supervised learning algorithms (such as `SVM`, `KNN`, `Decision Tree`, `Naive Bayes`, `Logistic Regression`, `Linear Regression`, `Artificial Neural Network`, etc.) and unsupervised learning algorithms (such as `Clustering`, `EM Algorithm`, etc.).

![](converter-master/example/165201/content_633678_3.jpg)

Figure 4-4

1. **Classification**: Given a sample feature, we want to predict its corresponding label value. If the attribute value is discrete, this is a classification problem.
2. **Regression**: Given a sample feature, we want to predict its corresponding label value. If the attribute value is continuous, this is a regression problem.
3. **Clustering**: Given a set of sample features, we do not need to predict their corresponding label values but want to discover the distribution of this set of samples in space, such as analyzing which samples are close and which are far apart; this is a clustering problem.
4. **Dimensionality Reduction**: Given a set of sample features, if a low-dimensional subspace is needed to represent the original high-dimensional feature space, this is a dimensionality reduction problem.

The application areas and algorithms for various problems are shown in Table 4-1.

Table 4-1

![](converter-master/example/165201/content_633678_4.jpg)

As a product manager, one needs to have an in-depth understanding of the learning paradigms of machine learning and also understand what each learning paradigm is mainly used for, so as to communicate effectively with engineers.

### 4.1.3 Supervised Learning

Supervised learning uses labeled historical data for training to derive a model, then inputs new data and obtains a solution based on the model, as shown in Figure 4-5.

![](converter-master/example/165201/content_633678_5.jpg)

Figure 4-5

Supervised learning algorithms generate a model using a labeled dataset, then use this model together with new data to verify the model's accuracy, or use real-time data to apply the model to a production environment.

Supervised learning requires a large amount of data as training data, and existing data needs to be divided into training data and test data. If the training data is too small, the model may be inaccurate; if too much data is put into training, it may affect verification due to too many noise points. Generally, a ratio of 6:4 to 8:2 for training data to test data is relatively appropriate.

Below, the process of supervised learning is illustrated through a practical example. For instance, a user uploads a photo, and the system needs to identify whether this photo is of the user themselves. The photo uploaded by the user may be a full-body shot, a half-body shot, or even a close-up of just one eye.

**Step 1: Data Generation and Classification.** First, collect a sufficient number of photos of the user—full body, half body, front, side—as long as it is the user's image, they all exist in one group, called the training set. The training set is used for training. Secondly, prepare another group of photos; this group contains some photos of the user and some of others; this group is called the validation set. The validation set is used to test whether the trained algorithm model can recognize the user. The validation set serves as input to obtain some outputs: output 1 if the photo contains the user, 0 if not.

**Step 2: Training.** When training through an `Artificial Neural Network`, each image in the training set becomes the input of the `Artificial Neural Network`. After computation by each layer of neurons in the `Artificial Neural Network` for feature extraction, an output is obtained upon completion; observe whether the output is 1 or 0.

**Step 3: Validation.** After all data in the first group is used up, use the data in the second group to validate the accuracy of the trained model. This process involves hyperparameter optimization, selection of activation functions, etc.

**Step 4: Encapsulation and Application.** Once the data validation indicators reach the expected indicators, the model training is complete. At this point, the model can be encapsulated as an interface and integrated into the software. The software interacts with the user through the interface; when the user uploads a photo, the software automatically calls the interface to complete the calculation and returns the result to the software program interface.

AI product managers do not necessarily need to understand algorithm design or specifically implement algorithms, but they must understand the content and characteristics of algorithms and the goals algorithms can achieve. Below, two supervised learning algorithms will be introduced: `SVM` and `Naive Bayes Classifier`.

#### SVM

`SVM` stands for Support Vector Machine, a very popular supervised learning model used for classification or regression. `SVM` can effectively solve problems of small samples, non-linearity, and high-dimensional pattern recognition, so its application is very wide. This book assumes there are different shapes in a two-dimensional space, as shown in Figure 4-6.

![](converter-master/example/165201/content_633678_6.jpg)

Figure 4-6

`C[1]` and `C[2]` are two categories of shapes in the planar space. `C[1]` is a cylinder, `C[2]` is a cube. The oblique line in the middle with a slope of `W` is the classification function, which can completely separate the two samples. If a linear function can completely separate the samples, the data is generally called linearly separable; otherwise, it is called non-linearly separable. Upon observation, one will find that the oblique line in the figure can actually change its slope or move up and down, as shown in Figure 4-7. Any straight line with a slope of `W` between `WX - Y = -1` and `WX - Y = 1` can completely separate the samples, but the straight line that is equidistant from the sample points and has the maximum distance is the optimal solution, i.e., `WX - Y = 0`. The classification function is a point in one-dimensional space, a straight line in two-dimensional space, a plane in three-dimensional space, and of course, there are higher-dimensional or infinite-dimensional classification functions, collectively called hyperplanes.

![](converter-master/example/165201/content_633678_7.jpg)

Figure 4-7

The purpose of `SVM` is to find a hyperplane that can divide the samples into two categories and maximize the classification margin; this is the basic theoretical model of `SVM`. Of course, if objects in the world are evenly dispersed and regular, it is easy to get results, but the actual situation is not like that; the actual situation is often as shown in Figure 4-8, where cylinders and cubes are irregularly scattered in this plane.

![](converter-master/example/165201/content_633678_8.jpg)

Figure 4-8

It is almost impossible to classify the shapes shown in Figure 4-8 using a simple function. Thus, scientists proposed a new concept "Kernel Function," which can map samples from the original space to a higher-dimensional space, and through the division of the images in the high-dimensional space, finally complete the linear division.

**Step 1**: Upgrade the two-dimensional space to a three-dimensional space, as shown in Figure 4-9.

![](converter-master/example/165201/content_633678_9.jpg)

Figure 4-9

**Step 2**: Project the two-dimensional sample data into the three-dimensional space, as shown in Figure 4-10.

![](converter-master/example/165201/content_633678_10.jpg)

Figure 4-10

**Step 3**: Perform cutting in the three-dimensional space, and map the cut hyperplane to a two-dimensional plane, as shown in Figure 4-11.

![](converter-master/example/165201/content_633678_11.jpg)

Figure 4-11

Thus, a new kernel function is completed. Because non-linear space is more complex, and it is impossible to quickly know which kernel function is most suitable for converting non-linear space into linear space, `SVM` usually employs multiple kernel functions to identify the best option.

#### Naive Bayes Classifier

If your boss assigns you an analysis task with a medium amount of data, few variables, and hopes you can complete data prediction as soon as possible, then the `Naive Bayes Theorem` is a good choice. Let's first look at the expression of Bayes' Theorem (4-1):

![](converter-master/example/165201/content_633678_12.jpg)

(4-1)

Changing the expression form makes it easier to understand:

`P(Class|Feature) = P(Feature|Class) * P(Class) / P(Feature)`

If we want to distinguish between product managers and programmers by gender and weight, we are seeking `P(Programmer|Gender, Weight)` and `P(Product Manager|Gender, Weight)`. According to Bayes' Theorem:

`P(Occupation|Gender, Weight) = P(Gender|Occupation) * P(Occupation) / P(Weight)`

Besides Bayes' Theorem, there is the `Naive Bayes Theorem`. The `Naive Bayes Theorem` is an algorithm derived from the basic assumption on the basis of Bayes' Theorem. The `Naive Bayes Theorem` assumes that all data attributes are independent and do not affect each other. It is precisely through this assumption that many complex problems can be simplified, and the `Naive Bayes Theorem` is also very suitable for predicting unknown datasets and making data predictions. For example, according to the `Naive Bayes Theorem`, we can know that gender and weight are unrelated, i.e.,

`P(Weight|Occupation, Gender) = P(Weight|Occupation)`

`P(Gender, Weight|Occupation) = P(Gender|Occupation) × P(Weight|Occupation)`

```
EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==
```

