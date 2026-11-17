

### 4.1.1 Bayes' Theorem and Naive Bayes Theorem

`P(Class|Feature) = P(Feature|Class) * P(Class) / P(Feature)`

If we want to distinguish between programmers and product managers based on gender and weight, we are essentially seeking `P(Programmer|Gender, Weight)` and `P(Product Manager|Gender, Weight)`. According to Bayes' Theorem:

`P(Occupation|Gender, Weight) = P(Gender|Occupation) * P(Occupation) / P(Weight)`

Besides Bayes' Theorem, there is the Naive Bayes Theorem. The Naive Bayes Theorem is an algorithm derived from Bayes' Theorem with a fundamental assumption. It assumes that all data attributes are independent of each other and do not affect one another. It is precisely through this assumption that many complex problems can be simplified, and the Naive Bayes Theorem is also very suitable for predicting unknown datasets and performing data prediction. For example, according to the Naive Bayes Theorem, we can know that gender and weight are unrelated, i.e.,

`P(Weight|Occupation, Gender) = P(Weight|Occupation)`

`P(Gender, Weight|Occupation) = P(Gender|Occupation) × P(Weight|Occupation)`

The Naive Bayes Theorem has the following advantages:

1.  The Naive Bayes algorithm is simple and easy to understand;
2.  Naive Bayes has high performance and fast computation speed;
3.  Naive Bayes excels at data prediction;
4.  Naive Bayes requires a very small amount of training data.

It is precisely because of these advantages that Naive Bayes is frequently seen in real-time prediction and multi-class prediction. Not only that, because text classification involves many variable types that are relatively independent of each other, Naive Bayes has also been widely used in text classification, such as spam detection, website content recognition, news recommendation, etc.

### 4.1.4 Unsupervised Learning

Learning the distribution of data or the relationships between data through algorithms using data without any labels is called unsupervised learning. The goal of unsupervised learning is to use algorithms to explore hidden structures and characteristics within the data. These structures and characteristics are impossible for humans to understand directly or achieve through human effort. Because there are no labels, it is also very difficult to evaluate whether the results of unsupervised learning are correct. However, if data is grouped through unsupervised learning, people can discover hidden information in the original data that was unforeseeable.

If the way students learn in school is called supervised learning, then children who don't go to school can also perform unsupervised learning through their own observation and understanding of the world, except that what they learn is arbitrary and spontaneous, the final learning outcome is unpredictable, and the same applies to artificial intelligence. There was a news story in 2017: two AI robots created by American scientists invented a language only the two of them could understand through self-learning, and these two robots had already begun communicating in a language only they could understand, which caused great panic among people.

Unsupervised learning has been validated in many application scenarios. If you run a garment factory producing clothes, how do you determine clothing sizes S, M, L? If you possess the height data of all Chinese people, you can determine this through unsupervised learning. Precisely because unsupervised learning lacks human labeling, as of now, unsupervised learning still cannot reach the accuracy and effectiveness of supervised learning, so unsupervised learning has always been criticized by some scientists in the AI community. But in recent years, unsupervised learning has become hot again, and some scientists even claim that unsupervised learning is the future of artificial intelligence.

Currently, the popular "Generative Adversarial Network (GAN)" in unsupervised learning works by connecting a generator network and a discriminator network. The generator network is responsible for generating data intended to try to deceive the discriminator network. Through the adversarial learning of the two networks, some surprising results have been obtained, such as AI technology that can generate photo-realistic images from text strings or hand-drawn sketches.

There are usually three evaluation metrics for AI: **Precision**, **Recall**, and **Accuracy**. In unsupervised learning, these three metrics are very important, and people usually use these three metrics to evaluate unsupervised learning.

**Precision**: For a given test dataset, the ratio of the number of samples correctly classified by the classifier to the total number of samples. Simply put, precision means "querying accurately". Like a policeman catching thieves: if 10 people are caught, and 9 of them are thieves, then the precision is 90%.

**Recall**: For a given test dataset, the number of all correctly classified samples correctly found by the classifier. Simply put, recall means "querying comprehensively". Again, take the policeman catching thieves: there are 100 people, 20 of whom are thieves; if 10 thieves are caught, the recall is 50%.

**Accuracy**: Represents the proportion of correct identifications. That is, the probability of good guys being identified as good guys and bad guys being identified as bad guys. Continuing with the policeman catching thieves example: there are 100 people, 20 of whom are thieves; 10 people are caught, but 1 of them is not a thief, then the accuracy is 90%.

Precision, Recall, and Accuracy are key metrics for measuring AI algorithms; the higher these three metrics, the better the adaptability of the algorithm.

### 4.1.5 Semi-supervised Learning

Supervised learning is used when there is the capability to label data and perform classification and regression based on the labeling information; unsupervised learning is training performed when no labeling information is contained, usually used for clustering. Sometimes, the data at hand often has only a portion labeled rather than fully labeled; in this case, semi-supervised learning can be used to complete the training. The "National Government Website Monitoring Project" is a good example.

The National Government Website Monitoring Project monitored over 90,000 government websites nationwide and nearly 25 billion pages at all levels of government websites, as shown in Figure 4-12. The project's task is to monitor whether these pages have anomalies and which pages users are more interested in. For up to tens of billions of pages, labeling all pages is impossible; only a few tens of thousands of pages are labeled. If the unlabeled sample set is simply discarded, it is not only a pity but also leads to data distortion due to insufficient training samples, thereby affecting the effectiveness of machine learning. How to effectively utilize unlabeled data becomes the key.

![](converter-master/example/165201/content_633679_13.jpg)

Figure 4-12

The simplest method is to label the unlabeled pages, but this comes with huge labor costs. If it takes 0.5 minutes for a person to label one page, to label 2.5 billion pages, one person working 365 days a year without rest would take over 2,300 years to complete.

The project team thus adopted semi-supervised learning methods to complete the labeling work. First, a learner is trained using the labeled sample dataset; then, based on this learner, predictions are made on the unlabeled samples; next, the predicted result samples are classified, and samples with high uncertainty and low classification confidence are re-labeled; finally, the data is expanded into the training set, the learner is retrained, and the final result is generated, as shown in Figure 4-13.

![](converter-master/example/165201/content_633679_14.jpg)

Figure 4-13

Obviously, semi-supervised learning essentially still belongs to supervised learning, but it performs semi-automated processing on the training data samples. Compared with supervised learning, the learning cost is greatly reduced. For various algorithms of supervised learning and unsupervised learning, if the dataset conforms to the characteristics of semi-supervised learning, semi-supervised learning methods can be adopted.

### 4.1.6 Reinforcement Learning

Reinforcement Learning is also known as Reinforcement Learning (alternate translation) or Evaluative Learning. If described with a standard definition, the goal of reinforcement learning is to learn an optimal **Policy**, which allows the **Agent** to take an **Action** based on the current **State** in a specific **Environment**, thereby obtaining the maximum reward. Reinforcement learning is often confused with supervised learning and unsupervised learning.

Reinforcement learning views learning as a trial-and-evaluation process, as shown in Figure 4-14. The agent selects an action to act on the environment; the environment accepts the action, its state changes, and simultaneously generates a reinforcement signal, which is fed back to the agent. The agent selects the next action based on the reinforcement signal and the current state of the environment. The principle of selection is to increase the probability of receiving positive reinforcement. The selected action not only affects the immediate reinforcement value but also affects the environment's state at the next moment and the final reinforcement value.

![](converter-master/example/165201/content_633679_15.jpg)

Figure 4-14

Supervised learning is like a student taking an exam with an instructor standing next to them; every time the student finishes a question, the instructor immediately tells them whether it was right or wrong. But the actual situation is complex; many real problems have no standard answers, only which answer is more suitable. The process of reinforcement learning is like training an animal: every time the animal performs a behavior, the trainer needs to give timely feedback, and this good-or-bad feedback will influence its behavior next time. AI reinforcement learning is exactly this: through repeated reinforcement learning processes, the machine learns which behavior yields the best reward. Precisely because reinforcement learning has the characteristic of real-time feedback, it is widely used in the field of robotics.

Both supervised learning and reinforcement learning obtain a mapping relationship from input data to output results through algorithms. Supervised learning gives the relationship between input and output, telling the algorithm what output corresponds to what input. Reinforcement learning gives feedback information, judging whether this behavior is good or bad. Of course, because reinforcement learning gives feedback information, the whole process has latency; therefore, sometimes it takes multiple steps before knowing whether a choice at a previous node was correct.

## 4.2 Machine Learning — Regression

In Section 4.1, it was mentioned that regression is one of the key problems machine learning can solve. Regression can classify samples; below we learn about classification-related content through a practical example.

Suppose we can collect data from gym users via smart bracelets, understanding users' age, height, weight, what equipment they use for exercise, calories burned, etc. Can we predict the calories a new gym-goer will burn when performing a certain exercise through machine learning? The answer is yes; we can achieve this using supervised learning for regression.

For example, there is a gym-goer named Xiaoli, aged 27, female, weight 60 kg, height 165 cm. After she continuously lifts a 30 kg barbell for 25 minutes, her measured skin temperature is 37°C, heart rate is 134 bpm, and calories burned are 231 kcal, as shown in Figure 4-15.

![](converter-master/example/165201/content_633680_0.jpg)

Figure 4-15

Knowing Xiaoli's exercise feature values and calories burned, we can learn a function through an algorithm that uses Xiaoli's exercise feature values to calculate 231 kcal. Xiaoli's exercise data can be represented by Formula (4-2):

![](converter-master/example/165201/content_633680_1.jpg)

(4-2)

Of course, a single person's sample cannot yield a function applicable to everyone. So what we need to do is collect the same type of data from many different participants, using part for training and another part for validation, as shown in Figure 4-16.

![](converter-master/example/165201/content_633680_2.jpg)

Figure 4-16

After the training data volume increases, the numbers corresponding to each person can be represented by `X[1] ~ X[7]`, and the final calories burned `Y` can be represented by Formula (4-3):

![](converter-master/example/165201/content_633680_3.jpg)

(4-3)

If these `X` and `Y` are represented in a 2D table, it is as shown in Figure 4-17.

![](converter-master/example/165201/content_633680_4.jpg)

Figure 4-17

Through training on these discrete points, their linear pattern can be found, as shown in Figure 4-18.

![](converter-master/example/165201/content_633680_5.jpg)

Figure 4-18

Now, we can put the trained data into this model for validation. Since the data has bias, when the trained data is put into the model, deviations will appear as shown in Figure 4-19, i.e., residuals.

![](converter-master/example/165201/content_633680_6.jpg)

Figure 4-19

Through residuals, we can know some error levels in the model. Similarly, we have methods to handle errors in the model, such as Root Mean Squared Error (RMSE), Mean Absolute Error (MAE), etc. Of course, depending on the prediction target, the significance of absolute values may differ greatly. For this section's case, if the result has an error of 5 kcal, it indicates a very reasonable model. But if the case is not measuring calories burned, but measuring how long it takes to burn calories, then if the result shows a deviation of 5 hours, the model's effect is unacceptable. Therefore, sometimes we need to use relative error to measure the model to represent a more general error level. Relative error is generally a relative value between 0 and 1. Combining Relative Absolute Error and Relative Squared Error, the closer the error value is to 0, the better the model effect.

## 4.3 Machine Learning — Classification

Through machine learning classification, we can predict which level, category, or attribute a certain thing belongs to. The simplest classification method is binary classification, used to predict whether a thing belongs to one of two categories. Binary classification is often used to determine the truth or falsehood of something. Usually, a binary classifier is a function that predicts whether the `Y` value is 0 or 1 through different `X` values.

In practical applications, the binary classifier's function does not directly calculate the result as 1 or 0, but calculates a value between 0 and 1, and we use a threshold or critical value to decide whether the final result should be recorded as 0 or 1.

For example, based on some patient information collected by a hospital: patient's age, whether pregnant, glucose intake, BMI (Body Mass Index), etc., to determine who has diabetes, as shown in Figure 4-20.

![](converter-master/example/165201/content_633681_0.jpg)

Figure 4-20

We can establish a function `f(x)` applicable to these patients. These indicators of the patients are all fed into the `f(x)` function. The result for diabetic patients is recorded as 1, and for non-diabetic patients as 0, see Formula (4-4).

![](converter-master/example/165201/content_633681_1.jpg)

(4-4)

Since the model does not directly yield 0 or 1, it generally gives a value between 0 and 1 as the result. So when we use the model to predict values, we need to set a threshold. Whether the final result is 1 or 0 depends on which side of the threshold line it falls on, as shown in Figure 4-21. Set the threshold to 0.5; if the result exceeds this threshold, it is 1; if below, it is 0.

![](converter-master/example/165201/content_633681_2.jpg)

Figure 4-21

In general, classification problems are completed through supervised learning, so we reserve some known labels to validate the model's performance, as shown in Figure 4-22. After filling in the known data, it is found that a threshold of 0.5 is the best choice.

![](converter-master/example/165201/content_633681_3.jpg)

Figure 4-22

If the model predicts 1 and the actual label is also 1, this data is considered a **True Positive (TP)**; similarly, if the model predicts 0 and the actual label is also 0, this data is considered a **True Negative (TN)**. If the model predicts 1 but the actual label is 0, this is a **False Positive (FP)**; if the model predicts 0 but the actual label is 1, this is a **False Negative (FN)**. The threshold selection shown in Figure 4-23 determines the classification of predicted values.

![](converter-master/example/165201/content_633681_4.jpg)

Figure 4-23

In some cases, predicted values may be very close to the threshold but still get the wrong classification. We can control the classification of predicted values by moving the threshold, as shown in Figure 4-24.

![](converter-master/example/165201/content_633681_5.jpg)

Figure 4-24

When building the diabetes model, we would rather get more False Positive results than False Negative results, because through False Positive results we can confirm more patients at risk of diabetes. Of course, the number of True Positives, False Positives, True Negatives, and False Negatives produced by the model are all important indicators for evaluating model effectiveness. We usually put these four results in a confusion matrix for display, which provides a basis for judging classifier performance, as shown in Figure 4-25.

![](converter-master/example/165201/content_633681_6.jpg)

Figure 4-25

The simplest metric is **Accuracy**, which is the value obtained by dividing the number of correctly classified results by the total number of results. In this example, there are 5 True Positives and 4 True Negatives, plus 2 False Positives, and no False Negatives, see Formula (4-5).

![](converter-master/example/165201/content_633681_7.jpg)

(4-5)

A total of 11 predicted values, from which we got 9 correct predictions, so Accuracy is 0.82 (or 82%). This may look like a very good result, but surprisingly, Accuracy is actually not that useful in measuring model performance.

Assume only 3% of people have diabetes. Then we can create a model that always predicts 0. The model's Accuracy would be as high as 97%, but this is completely useless for identifying potential diabetes patients. A more useful metric is the proportion of True Positives among all results predicted as Positive. This metric is called **Precision**.

In this case, there are 5 True Positives and 2 False Positives. So our Precision is 5/7, approx 0.71, meaning 71% of those testing Positive in the experiment have diabetes, and 29% are false alarms. In some cases, we may need a metric to judge the model's sensitivity to True Positives, which is **Recall**. It is calculated by dividing the number of True Positive results by the sum of True Positives and False Negatives.

In other words, among all actual Positive results, there are 5 True Positives and no False Negatives (missed cases). Therefore, our Recall is 5/5, which is 1 or 100%. So in this case, we correctly identified all patients with diabetes. Recall is sometimes also called True Positive Rate. Similarly, for False Positives, we have a corresponding metric.

In this case, we have 2 False Positives and 4 True Negatives. So our False Positive Rate is 2/6, approx 0.33. The classification we got is based on a threshold around 0.3. For such a threshold, we can derive corresponding False Positives and True Positives. If we move the threshold to 0.5, the True Positive Rate is 4/5, i.e., 0.8; the False Positive Rate is 1/6, i.e., 0.17, as shown in Figure 4-26.

![](converter-master/example/165201/content_633681_8.jpg)

Figure 4-26

Assume we further move the threshold to 0.7. We can get a True Positive Rate of 2/5, i.e., 0.4, and a False Positive Rate of 0/6, i.e., 0, as shown in Figure 4-27.

![](converter-master/example/165201/content_633681_9.jpg)

Figure 4-27

If every possible threshold is plotted on a chart, we will eventually see a curve as shown in Figure 4-28, called the Receiver Operating Characteristic Curve or `ROC` curve. The area under the `ROC` curve in Figure 4-28 is called `AUC` (Area Under the Curve), which is an indicator for judging model prediction performance. Generally, the larger the `AUC` area, the closer the curve is to the top-left corner, the better the model performance.

![](converter-master/example/165201/content_633681_10.jpg)

Figure 4-28

A perfect classifier would have an `ROC` curve going straight up along the leftmost side, then straight right along the top, with an `AUC` value of 1. We can always compare the `ROC` curve with a diagonal line. The diagonal represents how the model would perform if you just guessed randomly with a 50% probability. In this case, the model's `AUC` value is 0.9, so the model's performance is absolutely better than random guessing, as shown in Figure 4-29.

![](converter-master/example/165201/content_633681_11.jpg)

Figure 4-29

## 4.4 Machine Learning — Clustering

