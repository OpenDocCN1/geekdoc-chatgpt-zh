

The Naive Bayes theorem has the following advantages: ① The `Naive Bayes theorem` algorithm is simple and easy to understand; ② The `Naive Bayes theorem` has high performance and fast calculation speed; ③ The `Naive Bayes theorem` excels at data prediction; ④ The `Naive Bayes theorem` requires a very small amount of training data.

It is precisely because the `Naive Bayes theorem` has the above advantages that it can often be seen in real-time prediction and multi-class prediction. Not only that, because text classification involves many variable types and the types are relatively independent of each other, the `Naive Bayes theorem` has also been widely used in text classification, such as spam identification, website content identification, news recommendation, etc.

### 4.1.4 Unsupervised Learning

Learning the distribution of data or the relationships between data through algorithms using data without any labels is called unsupervised learning. The goal of unsupervised learning is to use algorithms to explore certain structures and characteristics hidden in the data. These structures and characteristics cannot be directly understood by humans or achieved by human effort. Because there are no labels, it is also difficult to evaluate whether the results of unsupervised learning are correct. However, if data is grouped through unsupervised learning, people can gain insight into hidden information unforeseen in the raw data.

If the way students learn in school is called supervised learning, then children who don't go to school can also perform unsupervised learning through their own observation and understanding of the world, except what they learn is casual and arbitrary, and the final learning outcome is unpredictable. Artificial intelligence is the same. There was a news story in 2017: Two AI robots created by American scientists invented a language only they could understand through self-study, and these two robots had begun communicating in a language only they could understand, causing great panic among people.

Unsupervised learning has been validated in many application scenarios. If you open a garment factory to produce clothes, how do you determine clothing sizes S, M, L? If you possess the height data of all Chinese people, you can determine this through unsupervised learning. Precisely because unsupervised learning lacks human labeling, as of now, unsupervised learning still cannot reach the accuracy and effectiveness of supervised learning, so unsupervised learning has always been criticized by some scientists in the AI community. But in recent years, unsupervised learning has become hot again, and some scientists even claim that unsupervised learning is the future of artificial intelligence.

Currently, the principle of the popular `Generative Adversarial Network (GAN)` in unsupervised learning is to connect a generative network and a discriminative network. The generative network is responsible for generating data intended to try to deceive the discriminative network. Through the adversarial learning of the two networks, some surprising results have been obtained, such as AI technology that can generate photo-realistic images from text strings or hand-drawn sketches.

There are usually three evaluation metrics for AI: `Precision`, `Recall`, and `Accuracy`. In unsupervised learning, these three metrics are very important, and people usually use these three metrics to evaluate unsupervised learning.

**Precision**: For a given test dataset, the ratio of the number of samples correctly classified by the classifier to the total number of samples. Simply put, precision means "querying accurately". Like a policeman catching thieves: 10 people are caught, 9 of them are thieves, then the precision is 90%.

**Recall**: For a given test dataset, the number of all correctly classified samples correctly found by the classifier. Simply put, recall means "querying comprehensively". Still using the policeman catching thieves example: There are 100 people, 20 are thieves, 10 thieves are caught, then the recall is 50%.

**Accuracy**: Represents the proportion of correct identifications. That is, the probability of good people being identified as good people and bad people being identified as bad people. Continuing with the policeman catching thieves example: There are 100 people, 20 are thieves, 10 people are caught, but 1 of them is not a thief, then the accuracy is 90%.

`Precision`, `Recall`, and `Accuracy` are key metrics for measuring AI algorithms. The higher these three metrics, the better the adaptability of the algorithm.

### 4.1.5 Semi-Supervised Learning

Supervised learning is used when there is the capability to label data and perform classification and regression based on the labeled information; unsupervised learning is training performed when no label information is contained, usually used for clustering. Sometimes, the data at hand often only has a portion labeled rather than all labeled; then semi-supervised learning can be used to complete the training. The "National Government Website Monitoring Project" is a good example.

The National Government Website Monitoring Project monitored more than 90,000 government websites nationwide and nearly 25 billion pages of governments at all levels, as shown in Figure 4-12. The project's task is to monitor whether these pages have anomalies and which pages users are more interested in. For up to tens of billions of pages, it is impossible to label all pages; only a few tens of thousands of pages are labeled. If the unlabeled sample set is directly discarded, it is not only a pity but may also lead to data distortion due to insufficient training samples, thereby affecting the effect of machine learning. How to effectively utilize unlabeled data becomes key.

![](converter-master/example/165201/content_633678_13.jpg)

Figure 4-12

The simplest method is to label the unlabeled pages, but what comes with it is huge labor cost. If it takes 0.5 minutes for a person to label one page, to label 2.5 billion pages, one person working 365 days a year without rest would take over 2,300 years to complete.

The project team thus adopted the semi-supervised learning method to complete the labeling work. First, a learner is trained using the labeled sample dataset, then the unlabeled samples are predicted based on the learner. Next, the predicted result samples are classified, and samples with high uncertainty and low classification confidence are labeled a second time. Finally, the data is expanded into the training set, the learner is retrained, and the final result is generated, as shown in Figure 4-13.

![](converter-master/example/165201/content_633678_14.jpg)

Figure 4-13

Obviously, semi-supervised learning essentially still belongs to supervised learning, but it semi-automates the processing of training data samples. Compared with supervised learning, the learning cost is greatly reduced. For various algorithms of supervised learning and unsupervised learning, if the dataset conforms to the characteristics of semi-supervised learning, the semi-supervised learning method can be adopted.

### 4.1.6 Reinforcement Learning

Reinforcement learning is also known as reinforcement learning (translator note: the Chinese terms "强化学习", "再励学习", "评价学习" all map to Reinforcement Learning) or evaluative learning. If described with a standard definition, the goal of reinforcement learning is to learn an optimal `Policy`, which allows the `Agent` to take an `Action` based on the current `State` in a specific `Environment`, thereby obtaining the maximum return. Reinforcement learning is often confused with supervised learning and unsupervised learning.

Reinforcement learning regards learning as a trial-and-evaluation process, as shown in Figure 4-14. The agent selects an action to act on the environment; the environment changes state after accepting the action and simultaneously generates a reinforcement signal, which is fed back to the agent. The agent selects the next action based on the reinforcement signal and the current state of the environment. The principle of selection is to increase the probability of receiving positive reinforcement. The selected action not only affects the immediate reinforcement value but also affects the state of the environment at the next moment and the final reinforcement value.

![](converter-master/example/165201/content_633678_15.jpg)

Figure 4-14

Supervised learning is like a student taking an exam with a tutor standing next to them; every time the student finishes a question, the tutor immediately tells them whether they got it right or wrong. But the actual situation is complex; many practical problems have no standard answers, only which answer is more suitable. The process of reinforcement learning is like training an animal: every time the animal performs a behavior, the trainer needs to give timely feedback, and this good-or-bad feedback will influence its behavior next time. AI reinforcement learning is like this: through repeated reinforcement learning processes, the machine learns which behavior yields the best return. Precisely because reinforcement learning has the characteristic of real-time feedback, it is widely used in the field of robotics.

Both supervised learning and reinforcement learning obtain a mapping relationship from input data to output results through algorithms. Supervised learning gives the relationship between input and output, telling the algorithm what output corresponds to what input. Reinforcement learning gives feedback information, judging whether this behavior is good or bad. Of course, because reinforcement learning gives feedback information, the whole process has latency; therefore, sometimes it takes multiple processes before knowing whether the choice at a previous node was correct.

```
EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==
```

## 4.1 Machine Learning

Many experts classify machine learning as an interdisciplinary subject because machine learning involves multiple disciplines such as probability theory, statistics, and algorithm theory. Common definitions of machine learning include the following.

> "Machine learning is a science of artificial intelligence; the main research object of this field is artificial intelligence, especially how to improve the performance of specific algorithms in empirical learning."
>
> "Machine learning is the study of computer algorithms that can improve automatically through experience."
>
> "Machine learning uses data or past experience to optimize the performance criteria of computer programs."

Understanding machine learning does not require starting from these complex and obscure definitions; understanding from a simple and easy-to-grasp concept may be more effective. The author believes that "machine learning is a method of data analysis."

With the development of the Internet, the amount of data is growing larger, and how to monetize data has become the primary problem for enterprises. In fact, unprocessed raw data has little use value. Valuable data is obtained by using correct analysis techniques to gain richer data cognition, clearer scenario insights, and more accurate data portraits, thereby obtaining data that can assist decision-making; only then does the significance of data become prominent. The role of machine learning is to analyze massive data and mine its meaning and value.

As shown in Figure 4-1, data analysis can be divided into the following four categories.

![](converter-master/example/165201/content_633679_0.jpg)

Figure 4-1

**Current Status Description**: Describe the current state of the real world based on existing data.

**Anomaly Diagnosis**: Discover anomalies in existing data and understand the reasons for their occurrence.

**Future Prediction**: With time as the dimension, use historical data to view data change trends and predict the probability of a future event occurring.

**Behavioral Norms**: Consider how to change current or future data to meet future expectations.

What machine learning can do is also the above few things. Machine learning describes the real world and gains insight into it through learning from data, and predicts the evolution of the future world.

### 4.1.1 Logical Differences Between Traditional Software and Machine Learning

In the Internet era, the product manager's job is to analyze existing business and design a reasonable system processing logic based on the business process; development engineers implement the product based on the designed system processing logic. Therefore, the calculation process of traditional software from input to output is designed, fixed, and definite, as shown in Figure 4-2. The engineer's role is to design the process and let the data be processed according to the designed process.

![](converter-master/example/165201/content_633679_1.jpg)

Figure 4-2

Unlike Internet products and traditional software products, AI products automatically construct logical structures based on existing data. AI products repeatedly learn from data through machine learning algorithms; without telling the computer the business logic, AI algorithms can find the hidden patterns and meanings within.

AI products input training data (input data + result data) into AI algorithms to derive a model, then input new data into this model for calculation, thereby deriving results and achieving prediction and judgment, as shown in Figure 4-3. The model derived from data and algorithms replaces the software logic structure designed by product managers or requirement analysts.

![](converter-master/example/165201/content_633679_2.jpg)

Figure 4-3

The difference between machine learning and traditional methods is huge; machine learning subverts the original program thinking. The traditional program thinking is for humans to analyze data, find patterns, and obtain final results through "input data + pattern verification"; machine learning does not require humans to analyze and judge data but hands the work of analysis and judgment to the machine. After inputting training data, the machine finds the patterns hidden in the data according to the algorithm and applies these patterns in practice.

### 4.1.2 Learning Modes of Machine Learning

Depending on the training method, machine learning is divided into four major learning modes: supervised learning, unsupervised learning, semi-supervised learning, and reinforcement learning. The fundamental difference between these four learning modes lies in the difference in data content and training modes.

**Supervised Learning (also known as Supervised Learning)**: Training through labeled data to obtain a model, and processing unknown data through this model.

**Unsupervised Learning**: There are no training data samples in advance; instead, algorithms analyze and model the data to find the patterns within.

**Semi-Supervised Learning**: In the data used for semi-supervised learning training, only a part is labeled, while the majority is unlabeled.

**Reinforcement Learning**: Reinforcement learning also uses unlabeled data, but it can know through some method whether you are getting closer to or further from the correct answer.

Supervised learning is very easy to understand; it continuously trains through machine learning according to human-defined specifications to achieve the optimal solution under this specification. Why does unsupervised learning appear then? Will automated modeling without training go astray? For example, someone knows nothing about music, but by listening more, they can distinguish between classical, modern, jazz, and other genres in a short time. Similarly, we feed music from different eras, in large quantities, and of different types to the machine, and the machine can also find the characteristics of the music based on the use of high, mid, and low tones and rhythm in the audio, complete classification, without us telling the machine in advance what type of music it is.

Machine learning can solve many problems, but through induction and summary, they can be divided into classification, regression, clustering, dimensionality reduction, etc., as shown in Figure 4-4. There are many algorithms for solving these problems; common ones are supervised learning algorithms (such as `SVM`, `KNN`, Decision Trees, `Naive Bayes`, Logistic Regression, Linear Regression, Artificial Neural Networks, etc.) and unsupervised learning algorithms (such as Clustering, `EM Algorithm`, etc.).

![](converter-master/example/165201/content_633679_3.jpg)

Figure 4-4

(1) **Classification**: Given a sample feature, we want to predict its corresponding label value. If the attribute value is discrete, this is a classification problem.

(2) **Regression**: Given a sample feature, we want to predict its corresponding label value. If the attribute value is continuous, this is a regression problem.

(3) **Clustering**: Given a set of sample features, we do not need to predict their corresponding label values, but want to discover the spatial distribution of this set of samples, such as analyzing which samples are close and which are far apart; this is a clustering problem.

(4) **Dimensionality Reduction**: Given a set of sample features, if a low-dimensional subspace is needed to represent the original high-dimensional feature space, this is a dimensionality reduction problem.

The application areas and algorithms for various problems are shown in Table 4-1.

Table 4-1

![](converter-master/example/165201/content_633679_4.jpg)

As a product manager, one needs to have an in-depth understanding of the learning modes of machine learning, and also understand what each learning mode is mainly used for, so as to communicate effectively with engineers.

### 4.1.3 Supervised Learning

Supervised learning uses labeled historical data for training to derive a model, then inputs new data and derives a solution based on the model, as shown in Figure 4-5.

![](converter-master/example/165201/content_633679_5.jpg)

Figure 4-5

Supervised learning algorithms generate a model using a labeled dataset, then use this model together with new data to verify the model's accuracy, or apply the model to a production environment using real-time data.

Supervised learning requires a large amount of data as training data, and existing data needs to be divided into training data and test data. If the training data is too little, the model may be inaccurate; if too much data is put into training, it may affect verification due to too many noise points. Generally, a ratio of 6:4 to 8:2 for training data to test data is relatively appropriate.

Below, an example illustrates the process of supervised learning. For example, a user uploads a photo, and the system needs to identify whether this photo is of the user themselves. The photo uploaded by the user may be a full-body shot, a half-body shot, or even a close-up of just one eye.

Step 1: Data Generation and Classification. First, collect enough photos of the user—full body, half body, front, side—as long as it is the user's image, they all exist in one group, called the training set. The training set is used for training. Secondly, prepare another group of photos; this group contains some of the user's photos and some of other people's; this group is called the validation set. The validation set is used to test whether the trained algorithm model can recognize the user. The validation set serves as input to get some outputs; output 1 if the photo contains the user, 0 if not.

Step 2: Training. When training through an artificial neural network, each image in the training set becomes the input of the artificial neural network. After calculation by each layer of neurons in the artificial neural network, feature extraction is performed. When the calculation is complete, an output is obtained; observe whether the output is 1 or 0.

Step 3: Validation. After all data in the first group is used up, use the data in the second group to validate the accuracy of the trained model. This process involves hyperparameter optimization, selection of activation functions, etc.

Step 4: Encapsulation and Application. Once the data validation metrics reach the expected metrics, the model training is complete. At this point, the model can be encapsulated as an interface and integrated into the software. The software interacts with the user through the interface; when the user uploads a photo, the software automatically calls the interface to complete the calculation and returns the result to the software program interface.

AI product managers do not necessarily need to understand algorithm design, nor do they necessarily need to implement algorithms specifically, but they must understand the content and characteristics of algorithms, and the goals algorithms can achieve. Below, two supervised learning algorithms will be introduced: `SVM` and `Naive Bayes Classifier`.

#### SVM

`SVM` is also called Support Vector Machine, a very popular supervised learning model that can be used for classification or regression. `SVM` can effectively solve problems of small samples, non-linearity, and high-dimensional pattern recognition, so `SVM` is widely used. This book assumes there are different shapes in a two-dimensional space, as shown in Figure 4-6.

![](converter-master/example/165201/content_633679_6.jpg)

Figure 4-6

`C[1]` and `C[2]` are two categories of shapes in the planar space. `C[1]` is a cylinder, `C[2]` is a cube. The diagonal line in the middle with slope `W` is the classification function, which can completely separate the two samples. If a linear function can completely separate the samples, the data is generally called linearly separable; otherwise, it is called non-linearly separable. Observation reveals that the diagonal line in the figure can actually change its slope or move up and down, as shown in Figure 4-7. Any straight line with slope `W` between `WX - Y = -1` and `WX - Y = 1` can completely separate the samples, but the straight line equidistant from the sample points and with the maximum distance is the optimal solution, i.e., `WX - Y = 0`. The classification function is a point in one-dimensional space, a straight line in two-dimensional space, a plane in three-dimensional space, and of course there are higher-dimensional or infinite-dimensional classification functions, collectively called hyperplanes.

![](converter-master/example/165201/content_633679_7.jpg)

Figure 4-7

The purpose of `SVM` is to find a hyperplane that can divide the samples into two categories and maximize the classification interval; this is the basic theoretical model of `SVM`. Of course, if objects in the world are evenly and regularly distributed, results can be easily obtained, but the actual situation is not like that. The actual situation is often as shown in Figure 4-8, with cylinders and cubes irregularly scattered in this plane.

![](converter-master/example/165201/content_633679_8.jpg)

Figure 4-8

It is almost impossible to classify the shapes shown in Figure 4-8 using a simple function. Thus, scientists proposed a new concept "Kernel Function", which can map samples from the original space to a higher-dimensional space, and complete linear separation through the division of the images in the high-dimensional space.

Step 1: Upgrade the two-dimensional space to three-dimensional space, as shown in Figure 4-9.

![](converter-master/example/165201/content_633679_9.jpg)

Figure 4-9

Step 2: Project the two-dimensional sample data into the three-dimensional space, as shown in Figure 4-10.

![](converter-master/example/165201/content_633679_10.jpg)

Figure 4-10

Step 3: Perform cutting in the three-dimensional space, and map the cut hyperplane to a two-dimensional plane, as shown in Figure 4-11.

![](converter-master/example/165201/content_633679_11.jpg)

Figure 4-11

Thus, a new kernel function is completed. Because non-linear space is complex, and it is impossible to quickly know which kernel function is most suitable for converting non-linear space to linear space, `SVM` usually employs multiple kernel functions to identify the best option.

#### Naive Bayes Classifier

If your boss assigns you an analysis task with a medium amount of data, few variables, and hopes you can complete data prediction as soon as possible, then the `Naive Bayes Theorem` is a good choice. Let's first look at the expression of Bayes' Theorem (4-1):

![](converter-master/example/165201/content_633679_12.jpg)

(4-1)

Changing the expression form makes it easier to understand:

