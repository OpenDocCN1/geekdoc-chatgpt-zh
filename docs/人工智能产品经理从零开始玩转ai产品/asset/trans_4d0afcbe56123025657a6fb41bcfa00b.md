
## Reinforcement Learning

Reinforcement learning regards learning as a trial-and-evaluation process, as shown in Figure 4-14. The agent selects an action to act on the environment; the environment changes state after accepting the action and simultaneously generates a reinforcement signal, which is fed back to the agent. The agent then selects the next action based on the reinforcement signal and the current state of the environment, with the principle of increasing the probability of receiving positive reinforcement. The selected action affects not only the immediate reinforcement value but also the environment's state at the next moment and the final reinforcement value.

![](converter-master/example/165201/content_633673_15.jpg)

Figure 4-14

Supervised learning is like a student taking an exam with a tutor standing beside them; whenever the student finishes a question, the tutor immediately tells them whether it was right or wrong. However, real situations are complex; many practical problems have no standard answers, only answers that are more suitable. The reinforcement learning process is like training an animal: each time the animal performs a behavior, the trainer must give timely feedback, and this good-or-bad feedback influences its behavior next time. AI reinforcement learning works similarly: through repeated reinforcement learning processes, the machine learns which behavior yields the best reward. Precisely because reinforcement learning possesses the characteristic of real-time feedback, it is widely used in the field of robotics.

Both supervised learning and reinforcement learning obtain a relational mapping from input data to output results through algorithms. Supervised learning gives the relationship between input and output, telling the algorithm what output corresponds to what input. Reinforcement learning provides feedback information, judging whether an action is good or bad. Of course, because reinforcement learning provides feedback information, the entire process has latency; therefore, sometimes it takes multiple steps before knowing whether a choice at a previous node was correct. EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==

## 4.1 Machine Learning

Many experts classify machine learning as an interdisciplinary subject because it involves probability theory, statistics, algorithm theory, and other disciplines. Common definitions of machine learning include the following:

> "Machine learning is a science of artificial intelligence; the main research object of this field is artificial intelligence, particularly how to improve the performance of specific algorithms in empirical learning."
>
> "Machine learning is the study of computer algorithms that can improve automatically through experience."
>
> "Machine learning uses data or past experience to optimize the performance criteria of computer programs."

Understanding machine learning does not require starting from these complex and obscure definitions; understanding it from a simple and easy-to-grasp concept may be more effective. The author believes that **"machine learning is a method of data analysis."**

With the development of the Internet, data volume is growing larger, and how to monetize data has become the primary issue for enterprises. In fact, unprocessed raw data has little use value. Valuable data uses correct analysis techniques to obtain richer data cognition, clearer scenario insights, and more accurate data portraits, thereby obtaining data that can assist decision-making; only then does the meaning of data become prominent. The role of machine learning is to analyze massive data and mine its meaning and value.

As shown in Figure 4-1, data analysis can be divided into the following four categories.

![](converter-master/example/165201/content_633674_0.jpg)

Figure 4-1

- **Current Status Description**: Describe the current state of the real world based on existing data.
- **Anomaly Diagnosis**: Discover anomalies in existing data and understand the causes of these anomalies.
- **Future Prediction**: Use the time dimension to view data change trends using historical data and predict the probability of a future event occurring.
- **Behavioral Prescription**: Consider how to change current or future data to meet future expectations.

Machine learning can do the aforementioned things as well. Machine learning describes the real world and gains insights into it through learning from data, and predicts the evolution of the future world.

### 4.1.1 Logic Differences Between Traditional Software and Machine Learning

In the Internet era, the product manager's job is to analyze existing business and design reasonable system processing logic based on the business process; development engineers implement the product based on the designed system processing logic. Therefore, the computation process of traditional software from input to output is designed, fixed, and definite, as shown in Figure 4-2. The engineer's role is to design the process and let data be processed according to the designed process.

![](converter-master/example/165201/content_633674_1.jpg)

Figure 4-2

Unlike Internet products and traditional software products, AI products automatically construct logical structures based on existing data. AI products repeatedly learn from data through machine learning algorithms; without telling the computer the business logic, AI algorithms can find the hidden patterns and meanings within.

AI products input training data (input data + result data) into AI algorithms to derive a model, then input new data into this model for computation, thereby obtaining results and achieving prediction and judgment, as shown in Figure 4-3. The model derived from data and algorithms replaces the software logic structure designed by product managers or requirements analysts.

![](converter-master/example/165201/content_633674_2.jpg)

Figure 4-3

The difference between machine learning and traditional methods is huge; machine learning subverts the original programming mindset. The traditional programming mindset is for humans to analyze data, find patterns, and obtain final results through "input data + pattern verification"; machine learning does not require humans to analyze and judge data but hands the work of analysis and judgment to the machine. After inputting training data, the machine finds the patterns hidden in the data according to the algorithm and applies these patterns in practice.

### 4.1.2 Learning Paradigms of Machine Learning

Depending on the training method, machine learning is divided into four major learning paradigms: supervised learning, unsupervised learning, semi-supervised learning, and reinforcement learning. The fundamental difference between these four paradigms lies in the difference in data content and training modes.

- **Supervised Learning** (also called supervised learning): Training is performed using labeled data to obtain a model, which is then used to process unknown data.
- **Unsupervised Learning**: There are no training data samples beforehand; instead, algorithms analyze and model the data to find patterns within.
- **Semi-Supervised Learning**: In semi-supervised learning training, only a portion of the data used is labeled, while the majority is unlabeled.
- **Reinforcement Learning**: Reinforcement learning also uses unlabeled data, but it can know through some method whether it is getting closer to or further from the correct answer.

Supervised learning is very easy to understand; it continuously trains via machine learning according to human-defined specifications to achieve the optimal solution under this specification. Why does unsupervised learning exist then? Will automated modeling without training go astray? For example, someone knows nothing about music, but by listening more, they can distinguish between classical, modern, jazz, and other genres in a short time. Similarly, if we feed music from different eras, in large quantities, and of different types to a machine, the machine can also find the characteristics of the music based on the use of high, mid, and low tones and rhythm in the audio, and complete classification, without us telling the machine the music type in advance.

Machine learning can solve many problems, but through induction and summary, they can be divided into classification, regression, clustering, dimensionality reduction, etc., as shown in Figure 4-4. There are many algorithms for solving these problems; common ones are supervised learning algorithms (such as `SVM`, `KNN`, Decision Trees, Naive Bayes, Logistic Regression, Linear Regression, Artificial Neural Networks, etc.) and unsupervised learning algorithms (such as Clustering, `EM算法`, etc.).

![](converter-master/example/165201/content_633674_3.jpg)

Figure 4-4

1. **Classification**: Given a sample feature, we want to predict its corresponding label value; if the attribute value is discrete, this is a classification problem.
2. **Regression**: Given a sample feature, we want to predict its corresponding label value; if the attribute value is continuous, this is a regression problem.
3. **Clustering**: Given a set of sample features, we do not need to predict their corresponding label values but want to discover the spatial distribution of this set of samples, such as analyzing which samples are close and which are far apart; this is a clustering problem.
4. **Dimensionality Reduction**: Given a set of sample features, if a low-dimensional subspace is needed to represent the original high-dimensional feature space, this is a dimensionality reduction problem.

The application areas and algorithms for various problems are shown in Table 4-1.

Table 4-1

![](converter-master/example/165201/content_633674_4.jpg)

As a product manager, one needs to have an in-depth understanding of machine learning paradigms and understand what each paradigm is mainly used for, so as to communicate effectively with engineers.

### 4.1.3 Supervised Learning

Supervised learning uses labeled historical data for training to derive a model, then inputs new data and obtains a solution based on the model, as shown in Figure 4-5.

![](converter-master/example/165201/content_633674_5.jpg)

Figure 4-5

Supervised learning algorithms generate a model using a labeled dataset, then use this model together with new data to verify the model's accuracy, or apply the model to a production environment using real-time data.

Supervised learning requires a large amount of data as training data, and existing data needs to be divided into training data and test data. If training data is too little, the model may be inaccurate; if too much data is put into training, noise may affect verification. Generally, a ratio of 6:4 to 8:2 for training data to test data is relatively appropriate.

Below, an example illustrates the supervised learning process. For instance, a user uploads a photo, and the system needs to identify whether the photo is of the user themselves. The photo uploaded by the user may be a full-body shot, a half-body shot, or even a close-up of just one eye.

**Step 1: Data Generation and Classification.** First, collect enough photos of the user—full body, half body, front, side—as long as it is the user's image, they all exist in one group, called the **training set**. The training set is used for training. Secondly, prepare another group of photos, some of the user and some of others; this group is called the **validation set**. The validation set is used to test whether the trained algorithm model can recognize the user. The validation set serves as input to obtain outputs: output 1 if the user is in the photo, 0 if not.

**Step 2: Training.** When training via an artificial neural network, each image in the training set becomes an input to the network. After computation by each layer of neurons in the network for feature extraction, an output is obtained upon completion; observe whether the output is 1 or 0.

**Step 3: Validation.** After all data in the first group is used up, use the data in the second group to verify the accuracy of the trained model. This process involves hyperparameter optimization, selection of activation functions, etc.

**Step 4: Packaging and Application.** Once the data validation metrics reach the expected targets, the model training is complete. At this point, the model can be packaged as an interface and integrated into the software. The software interacts with users through the interface; when a user uploads a photo, the software automatically calls the interface to complete the computation and returns the result to the software program interface.

AI product managers do not necessarily need to understand algorithm design or implement algorithms specifically, but they must understand the content, characteristics, and achievable goals of algorithms. Below, two supervised learning algorithms will be introduced: `SVM` and the Naive Bayes Classifier.

#### 1. SVM

`SVM`, also known as Support Vector Machine, is a very popular supervised learning model used for classification or regression. `SVM` can effectively solve problems of small samples, non-linearity, and high-dimensional pattern recognition, so its application is very wide. This book assumes there are different shapes in a two-dimensional space, as shown in Figure 4-6.

![](converter-master/example/165201/content_633674_6.jpg)

Figure 4-6

`C[1]` and `C[2]` are two shape categories in the planar space; `C[1]` is a cylinder, `C[2]` is a cube. The oblique line in the middle with slope `W` is the classification function, which can completely separate the two samples. If a linear function can completely separate the samples, the data is generally called linearly separable; otherwise, it is called non-linearly separable. Observation reveals that the oblique line in the figure can actually change its slope or move up and down, as shown in Figure 4-7. Any straight line with slope `W` between `WX - Y = -1` and `WX - Y = 1` can completely separate the samples, but the straight line equidistant from the sample points and with the maximum distance is the optimal solution, i.e., `WX - Y = 0`. The classification function is a point in one-dimensional space, a straight line in two-dimensional space, a plane in three-dimensional space, and of course there are higher-dimensional or infinite-dimensional classification functions, collectively called hyperplanes.

![](converter-master/example/165201/content_633674_7.jpg)

Figure 4-7

The purpose of `SVM` is to find a hyperplane that can divide the samples into two classes and maximize the classification margin; this is the basic theoretical model of `SVM`. Of course, if objects in the world were evenly distributed and regular, results would be easy to obtain, but the actual situation is not like that; it is often as shown in Figure 4-8, with cylinders and cubes irregularly scattered on this plane.

![](converter-master/example/165201/content_633674_8.jpg)

Figure 4-8

It is almost impossible to classify the shapes shown in Figure 4-8 using a simple function. Thus, scientists proposed a new concept: "Kernel Function," which maps samples from the original space to a higher-dimensional space, and through the division of the images in the high-dimensional space, ultimately achieves linear division.

**Step 1:** Upgrade the two-dimensional space to three-dimensional space, as shown in Figure 4-9.

![](converter-master/example/165201/content_633674_9.jpg)

Figure 4-9

**Step 2:** Project the two-dimensional sample data into the three-dimensional space, as shown in Figure 4-10.

![](converter-master/example/165201/content_633674_10.jpg)

Figure 4-10

**Step 3:** Perform slicing in the three-dimensional space and map the sliced hyperplane to a two-dimensional plane, as shown in Figure 4-11.

![](converter-master/example/165201/content_633674_11.jpg)

Figure 4-11

Thus, a new kernel function is completed. Because non-linear space is complex and it is impossible to quickly know which kernel function is best suited to convert non-linear space to linear space, `SVM` usually employs multiple kernel functions to identify the best option.

#### 2. Naive Bayes Classifier

If your boss assigns you an analysis task with a medium amount of data, few variables, and hopes you can complete data prediction quickly, the Naive Bayes Theorem is a good choice. Let's first look at the expression of Bayes' Theorem (4-1):

![](converter-master/example/165201/content_633674_12.jpg)

(4-1)

Changing the expression form makes it easier to understand:

`P(类别|特征) = P(特征|类别) * P(类别) / P(特征)`

If we want to distinguish between product managers and programmers by gender and weight, we are seeking `P(程序员|性别，体重)` and `P(产品经理|性别，体重)`. According to Bayes' Theorem:

`P(职业|性别，体重) = P(性别|职业) * P(职业) / P(体重)`

Besides Bayes' Theorem, there is the Naive Bayes Theorem. The Naive Bayes Theorem is an algorithm derived from Bayes' Theorem by making a basic assumption. The Naive Bayes Theorem assumes that all data attributes are independent and do not affect each other. It is precisely through this assumption that many complex problems can be simplified, and the Naive Bayes Theorem is also very suitable for predicting unknown datasets and making data predictions. For example, according to the Naive Bayes Theorem, we can know that gender and weight are unrelated, i.e.,

`P(体重|职业，性别) = P(体重|职业)`

`P(性别，体重|职业) = P(性别|职业) × P(体重|职业)`

The Naive Bayes Theorem has the following advantages:
1. The Naive Bayes Theorem algorithm is simple and easy to understand;
2. The Naive Bayes Theorem has high performance and fast calculation speed;
3. The Naive Bayes Theorem excels at data prediction;
4. The Naive Bayes Theorem requires very little training data.

Precisely because the Naive Bayes Theorem has the above advantages, it can often be seen in real-time prediction and multi-class prediction. Not only that, because text classification has many variable types and the types are relatively independent, the Naive Bayes Theorem is also widely used in text classification, such as spam email identification, website content identification, news recommendation, etc.

### 4.1.4 Unsupervised Learning

Learning the distribution of data or the relationships between data through algorithms using data without any labels is called unsupervised learning. The goal of unsupervised learning is to use algorithms to explore hidden structures and characteristics in the data. These structures and characteristics are impossible for humans to understand directly or achieve through manpower. Because there are no labels, it is also difficult to evaluate whether the results of unsupervised learning are correct. However, if data is grouped through unsupervised learning, people can learn hidden information unforeseeable in the raw data.

If the way students learn in school is called supervised learning, then children who don't go to school can also perform unsupervised learning through their own observation and understanding of the world, except what they learn is casual and random, and the final learning outcome is unpredictable; AI is the same. In 2017, there was a news story: two AI robots created by American scientists invented a language only they could understand through self-study, and these two robots had begun communicating in a language only they could understand, causing great panic.

Unsupervised learning has been validated in many application scenarios. If you run a garment factory producing clothes, how do you determine S, M, L sizes? If you possess the height values of all Chinese people, you can determine them through unsupervised learning. Precisely because unsupervised learning has no human labeling, as of now, unsupervised learning still cannot reach the accuracy and effectiveness of supervised learning, so unsupervised learning has always been criticized by some scientists in the AI community. But in recent years, unsupervised learning has become hot again; some scientists even claim that unsupervised learning is the future of AI.

Currently, the more popular "Generative Adversarial Networks (`GAN`)" in unsupervised learning work by connecting a generative network and a discriminative network. The generative network is responsible for generating data intended to deceive the discriminative network. Through the adversarial learning of the two networks, some surprising results have been obtained, such as AI technology that can generate photo-realistic images from text strings or hand-drawn sketches.

AI evaluation metrics usually have three types: Precision, Recall, and Accuracy. In unsupervised learning, these three metrics are very important; people usually use these three metrics to evaluate unsupervised learning.

- **Precision**: For a given test dataset, the ratio of the number of samples correctly classified by the classifier to the total number of samples. Simply put, precision means "accurate retrieval." Like a policeman catching thieves: if 10 people are caught and 9 are thieves, the precision is 90%.
- **Recall**: For a given test dataset, the number of all correctly classified samples correctly found by the classifier. Simply put, recall means "comprehensive retrieval." Still using the policeman catching thieves example: there are 100 people, 20 are thieves, 10 thieves are caught, then recall is 50%.
- **Accuracy**: Represents the proportion of correct identifications. That is, the probability of good people being identified as good and bad people as bad. Continuing the policeman example: 100 people, 20 are thieves, 10 caught, but 1 is not a thief, then accuracy is 90%.

Precision, Recall, and Accuracy are key metrics for measuring AI algorithms; the higher these three metrics, the better the algorithm's adaptability.

### 4.1.5 Semi-Supervised Learning

Supervised learning is used when there is the capability to label data and perform classification and regression targeting the label information; unsupervised learning is used for training when label information is not included, usually for clustering. Sometimes, the data at hand often has only a portion labeled rather than all labeled; then semi-supervised learning can be used to complete the training. The "National Government Website Monitoring Project" is a good example.

The National Government Network Monitoring Project monitored over 90,000 government websites nationwide and nearly 2.5 billion pages of governments at all levels, as shown in Figure 4-12. The project's task is to monitor whether these pages have anomalies and which pages users are more interested in. For up to billions of pages, labeling all pages is impossible; only tens of thousands of pages are labeled. If the unlabeled sample set is directly discarded, it is not only a pity but may also lead to data distortion due to insufficient training samples, thereby affecting the effect of machine learning. How to effectively utilize unlabeled data becomes the key.

![](converter-master/example/165201/content_633674_13.jpg)

Figure 4-12

The simplest method is to label the unlabeled pages, but this comes with huge labor costs. If it takes 0.5 minutes for a person to label one page, to label 2.5 billion pages, one person working 365 days a year without rest would take over 2,300 years to complete.

