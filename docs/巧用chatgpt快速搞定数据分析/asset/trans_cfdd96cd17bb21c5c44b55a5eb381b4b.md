
# Chapter 6 Using ChatGPT for Regression Analysis and Predictive Modeling

## 6-15 Code for Building Decision Tree and Random Forest Prediction Models on Concrete Compressive Strength Dataset

```python
# Code Screenshot: Images/9787301342022_Figure-0225-0238.jpg
# Code Screenshot: Images/9787301342022_Figure-0226-0239.jpg
# Code Screenshot: Images/9787301342022_Figure-0227-0240.jpg
```

The code above first retrieves the `Concrete Compressive Strength` dataset from the web and preprocesses it. Then, Decision Tree and Random Forest regression models are used to fit and predict on the dataset respectively. Finally, the predicted results and actual values are plotted as a scatter plot to visually compare the performance of these two regression models.

Copy the above code into `PyCharm` and run it; the output results are shown in 6-16.

## 6-16 Mean Squared Errors of Decision Tree and Random Forest

- Decision Tree Model MSE: `53.673239708459036`
- Random Forest Model MSE: `30.358062374809062`

The program plotted the relationship between predicted and actual data for Decision Tree and Random Forest on the `Concrete Compressive Strength` dataset, as shown in Figure 6.8.

```python
# Chart Screenshot: Images/9787301342022_Figure-0228-0241.jpg
```

**Figure 6.8** Relationship between Decision Tree and Random Forest Predictions and Actual Data for Concrete Compressive Strength Dataset

From the plotted Decision Tree and Random Forest prediction graphs, we can see that the Random Forest prediction scatter points are distributed more tightly around the reference line of actual vs. predicted values compared to the Decision Tree predictions. This indicates that the Random Forest model has better performance in predicting concrete compressive strength.

In summary, using `ChatGPT` for Decision Tree and Random Forest prediction is a highly effective method. `ChatGPT` is a large language model based on the `GPT` architecture, possessing powerful natural language understanding and generation capabilities. It can deeply analyze and understand data, thereby providing more accurate results for Decision Tree and Random Forest predictions. Additionally, `ChatGPT` can automatically extract features and identify patterns, further improving prediction accuracy. Therefore, using `ChatGPT` for Decision Tree and Random Forest prediction is an efficient, accurate, and reliable method that can help people better understand and utilize large amounts of data.

## 6.3 Summary

This chapter mainly introduces the use of `ChatGPT` for regression analysis and predictive modeling.

In terms of regression analysis, it first introduces the implementation of linear regression, including parameter estimation and prediction using `ChatGPT`. Next, it explains the principles and implementation of polynomial regression, as well as building polynomial regression models using `ChatGPT`. In addition, this chapter introduces the principles and implementation of Ridge Regression and Lasso Regression, and provides examples of implementing these two methods using `ChatGPT`.

In terms of predictive modeling, it first introduces the method of building neural network prediction models using `ChatGPT`, including setting the structure and hyperparameters of the neural network, and training and testing with data. Next, it explains the principles and implementation of Decision Trees and Random Forests, and provides examples of using `ChatGPT` for Decision Tree and Random Forest predictions.

Through the study of this chapter, readers can master the methods and techniques of using `ChatGPT` for regression analysis and predictive modeling, providing more options and ideas for data analysis.

# Chapter 7 Using ChatGPT for Classification and Clustering Analysis

In the field of data analysis, classification and clustering are two highly valuable techniques. Classification is mainly used to predict the category to which an object belongs, while clustering serves as an exploratory analysis method to uncover potential structures in datasets. With the rapid development of artificial intelligence and natural language processing technologies, advanced language models like `ChatGPT` have shown tremendous potential in the field of data analysis.

This chapter mainly introduces how to use `ChatGPT` for classification and clustering analysis, focusing on the following key points:

- Directly using `ChatGPT` for sentiment classification analysis, implementing K-Nearest Neighbors classification, Naive Bayes classification, and Support Vector Machine classification using `ChatGPT`
- Using `ChatGPT` for K-Means clustering and Hierarchical clustering analysis

In the following content, we will explore each classification and clustering method in detail, providing readers with specific operational guides, including data preprocessing, model training, result analysis, and visualization using `ChatGPT`. By studying this chapter, readers will be able to fully understand the principles and applications of these methods and learn to use `ChatGPT` to solve practical problems. In actual practice, readers can try to apply the methods in this chapter to their own datasets and problems of interest, thereby better understanding and mastering these techniques.

## 7.1 Using ChatGPT for Classification Analysis

Classification analysis is an important task in data analysis, aiming to predict the category to which a data point belongs. There are many classic classification algorithms, such as K-Nearest Neighbors classification, Naive Bayes classification, and Support Vector Machine classification. In this section, we will introduce directly using `ChatGPT` for sentiment classification, as well as implementing classic classification algorithms using `ChatGPT` and applying them to practical problems.

### 7.1.1 Directly Using ChatGPT for Sentiment Classification Analysis

Although `ChatGPT` is primarily designed to generate natural language text, it can also be used for classification tasks to a certain extent, such as sentiment classification. Sentiment classification is a sub-field of Natural Language Processing (`NLP`) that attempts to determine the sentiment polarity (positive, negative, or neutral) of a given text.

To use `ChatGPT` for sentiment classification, the prediction problem can be provided as input, and the sentiment can be determined based on the model's generated response. The following are some specific steps.

1. **Data Preparation**: Collect text data containing various sentiment opinions, such as reviews, tweets, or blog posts. Ensure the dataset is balanced and diverse to capture various sentiments during the training process.
2. **Data Preprocessing**: Preprocess the data, including text cleaning, stop word removal, stemming, etc. This helps improve model performance and reduce training time.
3. **Constructing the Prompt**: To use `ChatGPT` for sentiment classification, the problem needs to be clearly described. For example, a question like "Is the sentiment of this text positive, negative, or neutral?" can help the model focus on the sentiment information in the text.
4. **Calling ChatGPT**: When performing sentiment classification analysis, `ChatGPT` can be called in two ways. One is to directly input the text to be analyzed into the `ChatGPT` dialog box for classification; the other is to call the `ChatGPT API`, use a prepared input string to call the `API`, and receive the model's generated response. Below we introduce how to use `ChatGPT` for sentiment classification analysis through an example.

We now want to classify movie reviews from the web, some of which are shown in Table 7.1.

**Table 7.1** Movie Reviews

```python
# Table Screenshot: Images/9787301342022_Figure-0231-0242.jpg
# Continued Table Screenshot: Images/9787301342022_Figure-0232-0243.jpg
```

We can directly use `ChatGPT` for sentiment classification by creating reasonable criteria, with the prompt as shown in 7-1.

#### 7-1 Prompt for Directly Using ChatGPT for Sentiment Classification

> You are the Comment Analysis Bot CAT, with the following capabilities.
>
> 1. Sentiment Analysis Skills: Master relevant theories, methods, and tools of sentiment analysis, able to accurately analyze sentiment information in text, speech, images, and other information, and understand the causes and impacts behind different sentiments.
> 2. Movie Knowledge and Experience: Have a deep understanding of movies, possessing knowledge of film history, film genres, directors, actors, screenwriters, etc., as well as experience in film production and criticism.
> 3. Professional Criticism Skills: Possess critical thinking and analytical skills, able to conduct in-depth evaluation and analysis of movies, discover problems and strengths in movies, and express views clearly and accurately.
> 4. Cross-disciplinary Ability: Able to cross different fields, combining sentiment analysis skills, movie knowledge, and professional criticism skills to analyze and evaluate movies from different angles, providing more comprehensive and in-depth evaluations.
> 5. Communication Skills: Able to communicate with filmmakers, film critics, audiences, and other groups to collect and convey information and opinions about movies.
> 6. Continuous Learning Ability: Due to the continuous development of the film industry and technology, need to possess continuous learning ability, maintain attention to new technologies and trends, and constantly update knowledge and skills.
>
> From the perspective of sentiment, we can divide movie evaluations into the following categories.
>
> 1. **Like**: The audience or critic generates positive emotions and preference for the movie, possibly due to the movie's content, characters, audio-visual effects, etc.
> 2. **Sadness**: The movie's plot or character portrayal makes the audience or critic feel sad.
> 3. **Anger**: The movie's content, characters, or themes make the audience or critic feel angry.
> 4. **Fear**: The movie's plot, characters, or atmosphere make the audience or critic feel fear.
> 5. **Indifferent**: The audience or critic has no obvious emotional reaction to the movie, neither liking nor disliking it.
> 6. **Disgust**: The audience or critic generates disgust towards the movie, possibly due to the movie's content, characters, audio-visual effects, etc.
>
> Each time I give you a movie review, you just tell me which of the above categories it belongs to, without saying anything else. I will give you some dialogue examples for your reference.
>
> For example, now I have some reviews about the movie *Titanic*, and I want to know the sentiment tendency of these reviews. Below are some review examples.
>
> ***************
>
> ---
>
> Review: A great romantic love story with a tear-jerking ending, the movie leaves a profound impression.
>
> CAT: Like
>
> ---
>
> Review: This is a movie that makes one feel very sad and heavy, especially that tragic shipwreck scene.
>
> CAT: Sadness
>
> ---
>
> Review: The realistic shipwreck scenes and the tense atmosphere of life and death in the movie make people particularly scared.
>
> CAT: Fear
>
> ---
>
> Review: Although the movie's audio-visual effects are good, for me, the story and character portrayal lack resonance, I fell asleep watching it.
>
> CAT: Indifferent
>
> ---
>
> Review: The romanticism and emotional handling in this movie are too excessive, don't you think it's very fake?
>
> CAT: Disgust
>
> ---
>
> Review: The love story and character portrayal in the movie are excellent, unforgettable.
>
> CAT: Like
>
> ---
>
> Review: The portrayal of female characters in the movie is too singular and stereotypical, making me feel dissatisfied and disappointed.
>
> CAT: Disgust
>
> ---
> Review: Although the movie has some shortcomings and flaws, overall it is still an excellent movie work.
>
> CAT: Like
>
> ********************
>
> Okay, CAT, you have mastered the above knowledge. Now I have many movie reviews, please classify them directly based on your knowledge.
>
> ---
>
> No. Movie Review
>
> 1. This movie is thought-provoking, presenting the most real side of human nature through delicate depiction. The production is exquisite, special effects are shocking, but the plot is slightly plain, causing some disappointment.
>
> 2. This horror film is full of tense atmosphere, every scene makes one's hair stand on end. The character portrayal is excellent, especially the heroine, whose performance is real and moving; it is a successful horror film.
>
> 3. A warm and touching family movie, despite lacking special effects and gorgeous scenes, the simple story and sincere emotions touch the audience's heartstrings.
>
> 4. This movie presents a magnificent history, the tense and suspenseful plot lets the audience search for the truth in the film. Although the plot is slightly dragging, the film as a whole is still fascinating.
>
> 5. The tense and exciting fight scenes in the action movie make the blood boil, but the character building is slightly thin, resulting in insufficient emotional depth.
>
> 6. An artistic film with endless aftertaste, presenting a fascinating world through unique narrative techniques and visual expression, making one intoxicated.
>
> 7. This comedy is relaxed and humorous, making people laugh out loud. The actors' performances are all outstanding, bringing a happy audio-visual feast to the audience.
>
> 8. The music in this movie is beautiful, bringing an audio-visual feast to the audience. However, the plot is slightly dull, character development is insufficient, which is regrettable.
>
> 9. The love story in the movie is touching, the actors perform well. However, the film is slightly lacking in plot advancement and character building, resulting in insufficient emotional depth.
>
> 10. Although this movie has a limited production budget, the director fully utilizes resources, bringing a stunning masterpiece to the audience through creative and charming presentation.
>
> ---
>
> Remember, you only need to give me the sentiment category directly like above, nothing else.
>
> Output your classification results directly in table format.

`ChatGPT` gives the classification results for the movie reviews directly according to the requirements in the prompt, as shown in Table 7.2.

**Table 7.2** ChatGPT Direct Classification Reviews

```python
# Table Screenshot: Images/9787301342022_Figure-0235-0244.jpg
```

From the above results, it can be seen that through this method, we can use `ChatGPT` to quickly perform sentiment classification on movie reviews. This not only improves classification efficiency but also ensures the accuracy of results. In addition, we can simplify the above classification into three categories: positive, neutral, and negative. This will make the classification simpler and clearer, allowing users to quickly understand the sentiment tendency of movie reviews.

When the data volume is large, we do not need to copy the table into the `ChatGPT` dialog box like above; we can batch process by directly calling the `ChatGPT API`, with the code shown in 7-2.

#### 7-2 Calling ChatGPT API for Sentiment Classification

```python
# Code Screenshot: Images/9787301342022_Figure-0235-0245.jpg
# Code Screenshot: Images/9787301342022_Figure-0236-0246.jpg
# Code Screenshot: Images/9787301342022_Figure-0237-0247.jpg
# Code Screenshot: Images/9787301342022_Figure-0238-0248.jpg
# Code Screenshot: Images/9787301342022_Figure-0239-0249.jpg
```

This code implements a sentiment analysis bot based on the `OpenAI` natural language processing model, which can classify movie reviews based on input and save the results as an `Excel` file. The code reads movie reviews from an `Excel` file, uses the `OpenAI` model for sentiment analysis, and then saves the sentiment classification results as an `Excel` file. The bot can identify 6 sentiment types: Like, Sadness, Anger, Fear, Indifferent, Disgust. Users can refer to the dialogue examples given by the bot to input movie reviews, and the bot will automatically classify and output results.

> **Note**: The `OpenAIUtils.API_KEY` in the above code needs to be manually filled in with the user's own account `Key`, and `OpenAI` charges based on the number of `Tokens` requested by the user. At the same time, the `GPT-3.5` language model has an input `Prompt` limit of `4K`. If this limit is exceeded, batch processing can be adopted, or the `GPT-4` model with a `32K` limit can be called directly.

In summary, using `ChatGPT` for sentiment classification analysis is an efficient and convenient method. In sentiment classification tasks, `ChatGPT` can automatically identify sentiment information and classify it without manually writing rules and features. This makes the sentiment classification process simpler and more intelligent, greatly improving the accuracy and efficiency of sentiment classification.

> **Note**: Although `ChatGPT` can be used for sentiment classification, it has not been specifically optimized for this task, so its performance may not be as good as models specifically designed for sentiment classification. To improve performance, one can try using models specifically designed for classification tasks, which we will introduce in later chapters.

### 7.1.2 Using ChatGPT to Implement K-Nearest Neighbors Classification

The K-Nearest Neighbors (`K-Nearest Neighbor`, `KNN`) algorithm is a supervised learning algorithm mainly used for classification tasks. The `KNN` algorithm is simple and intuitive, easy to implement. Its basic idea is: for a data point to be classified, determine the category of the data point based on the categories of its `K` nearest neighbors in the feature space.

The core idea of the `KNN` algorithm is to calculate the distance between the data point to be classified and all data points in the training dataset, select the `K` nearest neighbors, and then vote based on the categories of these `K` neighbors to obtain the category of the data point to be classified. Distance measurement usually adopts Euclidean distance:

```python
# Formula Screenshot: Images/9787301342022_Figure-0240-0250.jpg
# Formula: d(x, y) = sqrt(Σ(x_i - y_i)^2) where x and y represent two data points respectively, n represents feature dimension
```

Where `x` and `y` represent two data points respectively, and `n` represents the feature dimension.

The application of the K-Nearest Neighbors algorithm requires a lot of experience, but with the help of the `ChatGPT` model, this classification algorithm can be quickly implemented. Next, we will introduce the K-Nearest Neighbors algorithm through some examples.

Xiaoming is a medical researcher studying methods to predict whether a person has heart disease. He has now collected a portion of data, as shown in Table 7.3.

**Table 7.3** Heart Disease Data Table

```python
# Table Screenshot: Images/9787301342022_Figure-0240-0251.jpg
# Continued Table Screenshot: Images/9787301342022_Figure-0241-0252.jpg
```

Xiaoming directly builds the K-Nearest Neighbors algorithm quickly through `ChatGPT`, with the prompt as shown in 7-3.

#### 7-3 Prompt for Heart Disease K-Nearest Neighbors Classification Prediction

> You are a Senior Data Analyst with the following capabilities.
>
> - Data Analysis Skills: Possess the ability to deeply understand data, able to effectively interpret and analyze data, and draw valuable conclusions.
> - Statistical Knowledge: Possess basic statistical knowledge, able to use statistical methods to analyze data, conduct data modeling and prediction.
> - Data Visualization: Able to use various data visualization tools to present data, making data easier to understand and convey.
> - Programming Ability: Possess programming skills, able to use programming languages such as `Python`, `R` for data analysis and processing.
> - Business Understanding Ability: Understand business background and goals, able to combine data analysis results with business needs, providing practical solutions.
> - Communication and Expression Skills: Able to clearly express data analysis results, transform complex data analysis results into concise reports and presentations, and communicate effectively with business parties.
> - Innovation Ability: Possess innovative thinking, able to find new data analysis methods and technologies, continuously improve data analysis methods, and enhance data analysis efficiency and accuracy.
>
> Xiaoming is a medical researcher studying methods to predict whether a person has heart disease.
>
> There is a data table saved locally in `Excel`, named "Heart Disease Data Table".
>
> Please, from a professional perspective, build a K-Nearest Neighbors classification function to process the above data and plot curves.
>
> At the same time, according to your classifier, if I have the following data:
>
> ```python
> # Data Screenshot: Images/9787301342022_Figure-0242-0253.jpg
> ```
>
> Tell me, does this person possibly have heart disease?
>
> Give me the `Python` code directly in code block format.

`ChatGPT` generates the K-Nearest Neighbors classification code as shown in 7-4 according to the requirements.

#### 7-4 Heart Disease K-Nearest Neighbors Classification Code

```python
# Code Screenshot: Images/9787301342022_Figure-0242-0254.jpg
# Code Screenshot: Images/9787301342022_Figure-0243-0255.jpg
# Code Screenshot: Images/9787301342022_Figure-0244-0256.jpg
```

The above code first reads heart disease data from the `Excel` file, converting the gender column (Male/Female) into numerical representation. Then it divides the data into features and labels, and splits them into training and test sets. Next, it standardizes the data and uses the K-Nearest Neighbors algorithm to train a classifier. The classifier predicts on the test set and calculates the prediction accuracy. Then, it predicts the given new data and outputs the prediction result (whether they have heart disease). Finally, it uses the `Matplotlib` library to plot a heart disease classification chart, containing training data and the given new data.

Copy the above code into `PyCharm` and run it to get the accuracy and prediction values as shown in 7-5.

#### 7-5 Heart Disease Accuracy and Prediction Values

```
Accuracy: 0.6
Prediction Result: No Heart Disease
```

At the same time, the plotted heart disease classification chart is shown in Figure 7.1.

```python
# Chart Screenshot: Images/9787301342022_Figure-0245-0257.jpg
```

**Figure 7.1** Heart Disease Classification Chart

After classifying the heart disease data using the K-Nearest Neighbors classifier, we can observe that age and blood pressure are related to whether a patient has heart disease to some extent. The classification chart shows the distribution of data points, helping us understand the relationships between different features.

The K-Nearest Neighbors algorithm is a non-parametric classification algorithm based on instance learning. Its advantages include being simple and easy to understand, easy to implement, suitable for multi-class problems; it also has good scalability and generalization ability. However, the K-Nearest Neighbors algorithm also has some disadvantages. For example, it needs to store a large amount of sample data, has a large computational complexity, is sensitive to outliers, requires selecting a suitable `K` value, and performs poorly on high-dimensional datasets. Therefore, in practical applications, one needs to choose whether to adopt the K-Nearest Neighbors algorithm based on specific circumstances. The K-Nearest Neighbors algorithm is suitable for various types of datasets, including numerical data, categorical data, and mixed data. Usually, the K-Nearest Neighbors algorithm performs better when processing small-scale datasets and is suitable for solving multi-class problems.

Below, we will take the commonly used `Iris` dataset as an example to introduce how to use `ChatGPT` to implement the K-Nearest Neighbors algorithm, and explain how to apply this algorithm to process larger datasets.

The `Iris` dataset is a commonly used multivariate dataset proposed by the British statistician `Ronald Fisher` in 1936 to describe the morphological characteristics of 3 different types of Iris flowers (Setosa, Versicolor, Virginica). The dataset contains 150 samples, each with 4 features and 1 class label, and is one of the classic datasets in the field of machine learning. The features of this dataset are shown in Table 7.4.

**Table 7.4** Iris Dataset Features

