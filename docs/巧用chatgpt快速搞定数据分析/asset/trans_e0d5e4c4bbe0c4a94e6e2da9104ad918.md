
# Chapter 5 Data Visualization with ChatGPT

## 4.3 Summary

In this chapter, we delved into feature engineering and feature dimensionality reduction using `ChatGPT`. Feature engineering is a crucial step in machine learning that directly impacts model performance. We learned how to use `ChatGPT` for feature selection and creating derived features, and how to apply them to real-world problems. These techniques help us extract the most useful information from massive datasets while reducing redundant features and noise.

Additionally, we introduced feature dimensionality reduction using `ChatGPT`. In practical problems, we often face high-dimensional data, which can lead to overfitting and increased computational overhead. By implementing Principal Component Analysis (`PCA`) and Linear Discriminant Analysis (`LDA`) using `ChatGPT`, we can transform high-dimensional data into low-dimensional data, thereby better understanding the data and improving model performance.

In summary, using `ChatGPT` for feature engineering and dimensionality reduction is a vital step in machine learning, helping us improve model performance and better understand data. In practice, we need to continuously experiment and optimize to obtain the optimal feature set and dimensionality reduction scheme, thereby better solving practical problems.

---

### 4.2.2 Implementing Linear Discriminant Analysis with ChatGPT

Linear Discriminant Analysis (`LDA`) is a supervised learning algorithm primarily used for dimensionality reduction, feature extraction, and classification tasks. The core idea of `LDA` is to map original high-dimensional data to a low-dimensional space through linear transformation, while maximizing the distance between different classes and minimizing the distance within the same class. Unlike `PCA`, `LDA` considers class information, making it suitable for classification tasks.

`LDA` involves the following steps.

1.  Calculate the mean vector for each class.
2.  Calculate the Within-Class Scatter Matrix.
3.  Calculate the Between-Class Scatter Matrix.
4.  Calculate the eigenvalues and eigenvectors of the product of the inverse of the within-class scatter matrix and the between-class scatter matrix.
5.  Select the eigenvectors corresponding to the top `k` largest eigenvalues to form a `p×k` matrix `W`.
6.  Project the original data onto the new space constructed by these `k` eigenvectors.

Below is the mathematical representation of `LDA`.

Assume the original dataset `X` consists of `n` samples, where the `i`-th sample `xi` belongs to a class, and each sample has `p` features.

1.  Calculate the mean vector `mi` for each class, averaging all samples `xi` belonging to class `i`:
    ![](Images/9787301342022_Figure-0137-0127.jpg)
2.  Calculate the global mean vector `m`, averaging all samples `xi`:
    ![](Images/9787301342022_Figure-0137-0128.jpg)
3.  Calculate the within-class scatter matrix `Sw` and between-class scatter matrix `Sb`:
    ![](Images/9787301342022_Figure-0137-0129.jpg)
4.  Calculate the eigenvalues `λ` and eigenvectors `V` of the inverse matrix of `Sw`:
    ![](Images/9787301342022_Figure-0137-0130.jpg)
5.  Select the eigenvectors corresponding to the top `k` largest eigenvalues to form the new matrix `W`:
    ![](Images/9787301342022_Figure-0137-0131.jpg)
6.  Project the original dataset `X` onto the new `k`-dimensional space:
    ![](Images/9787301342022_Figure-0137-0132.jpg)

Here, `X_reduced` is an `n×k` matrix representing the dimensionality reduction result of the original dataset `X`.

`LDA` is a supervised dimensionality reduction method. Unlike `PCA`, its purpose is to enable the reduced data to better distinguish different classes. The following is a simple example.

`Sara` is a biologist studying 3 different species of Iris flowers (Setosa, Versicolor, Virginica). She collected data on these irises, including features such as petal length and petal width. She hopes to use `LDA` to reduce data dimensionality and classify these 3 iris species. Partial iris data is shown in Table 4.6.

**Table 4.6 Initial Iris Data**

![](Images/9787301342022_Figure-0138-0134.jpg)

> **Note**: The Iris dataset is a classic machine learning dataset containing 4 features (sepal length, sepal width, petal length, petal width) for 3 different iris species (Setosa, Versicolor, Virginica). The dataset contains 150 samples total, with 50 samples per species. This dataset is commonly used for training and evaluating classification problems and is one of the classic introductory cases in machine learning.

`Sara` leveraged `ChatGPT`'s powerful algorithmic capabilities to perform Linear Discriminant Analysis directly on the above data, with the prompt shown in 4-17.

#### 4-17 Iris LDA Prompt

> You act as a data analysis expert with the following skills.
>
> - Data processing and cleaning capabilities: Able to use `SQL`, `Python`, `R` language, or other tools for data cleaning, preprocessing, and transformation.
> - Data analysis capabilities: Able to apply statistical and data modeling techniques to analyze data, understand relationships between data, identify patterns and trends, and extract useful information.
> - Data visualization capabilities: Able to use visualization tools (such as `Tableau`, `Power BI`, etc.) to present data as easy-to-understand and analyzable charts and visualizations.
> - Business understanding and problem-solving capabilities: Possess industry and business domain knowledge, able to understand business needs, solve practical problems, and provide data-driven suggestions and solutions.
> - Machine learning and AI skills: Able to apply machine learning and AI technologies for data prediction and classification, and develop intelligent applications.
> - Communication and expression capabilities: Able to present complex data analysis results and conclusions in a simple, understandable way to non-technical personnel, and communicate and collaborate effectively with team members and business personnel.
> - Continuous learning and innovation capabilities: Able to keep up with technology and industry developments, learn new tools and technologies, and continuously innovate and improve data analysis methods and techniques.
>
> Now, I need to perform `LDA` on the iris data for better separation.
>
> Partial data is as follows. (Unit: cm)
>
> ![](Images/9787301342022_Figure-0139-0135.jpg)
>
> Please implement Linear Discriminant Analysis (`LDA`) on the above data from a professional perspective, with the output format as follows.
>
> 【LDA Operation Steps】
>
> 【LDA Processed Data】
>
> Output the processed data in a table format.

`ChatGPT` implemented Linear Discriminant Analysis on the sample data as required, shown in 4-18.

#### 4-18 ChatGPT Linear Discriminant Analysis Output

**【LDA Operation Steps】**

- Import necessary `Python` libraries: `Numpy`, `Pandas`, `LDA` and `train_test_split()` function from `Sklearn`.
- Load iris data and split it into training and testing data.
- Perform `LDA` dimensionality reduction, reducing original data to 2 dimensions while specifying classification labels.
- Convert the reduced data to `DataFrame` format for easy output.

The data processed by `ChatGPT`'s `LDA` is shown in Table 4.7.

**Table 4.7 LDA Processed Data Table**

![](Images/9787301342022_Figure-0140-0136.jpg)

Here, `LDA1` and `LDA2` are the 2 principal components obtained after `LDA`.

Meanwhile, we can continue to ask `ChatGPT` to perform Linear Discriminant Analysis on the complete iris dataset, with the prompt shown in 4-19.

#### 4-19 Complete Processing Prompt

> Please perform `LDA` on the complete iris dataset from a professional perspective, and give me the complete `Python` code directly.
>
> Requirement: After performing Linear Discriminant Analysis on the data, plot a feature scatter plot.
>
> The two charts are displayed in a one-row, two-column format; the left one is the original data, the right one is the `LDA` feature data.

`ChatGPT` provided the code as shown in 4-20.

#### 4-20 Iris Data Processing Code

![](Images/9787301342022_Figure-0141-0137.jpg)![](Images/9787301342022_Figure-0142-0138.jpg)

Copy the above code into `PyCharm` and run it to obtain the new and old feature plots, as shown in Figure 4.4.

![](Images/9787301342022_Figure-0142-0139.jpg)

**Figure 4.4 Feature Plots Before and After LDA Processing**

As can be seen from the figure, after Linear Discriminant Analysis, the boundaries between data features are more distinct.

In summary, implementing Linear Discriminant Analysis (`LDA`) with `ChatGPT` can find the optimal classification decision boundaries in high-dimensional datasets, thereby classifying new data more accurately. `LDA` is a supervised learning algorithm typically used for multi-class classification problems, with strong interpretability and stability. By combining `ChatGPT` with `LDA`, we can leverage `ChatGPT`'s powerful language generation and reasoning capabilities to process large-scale high-dimensional data and extract useful features, thereby improving classification accuracy and efficiency.

Dimensionality reduction transforms high-dimensional data into low-dimensional data while preserving data information. Besides `PCA` and `LDA`, there are many other common dimensionality reduction methods in data analysis.

1.  **t-Distributed Stochastic Neighbor Embedding (`t-SNE`)**: Suitable for visualizing high-dimensional data, mapping high-dimensional data to 2D or 3D space.
2.  **Independent Component Analysis (`ICA`)**: Achieves dimensionality reduction by finding mutually independent components in the data, extracting potential signal sources.
3.  **Autoencoder**: Learns the latent structure and feature representation of data by compressing high-dimensional data into a low-dimensional space and then reconstructing it.
4.  **Factor Analysis (`FA`)**: Achieves dimensionality reduction by finding latent factors in the data, reducing redundant information, and improving data interpretability and visualization effects.
5.  **Non-negative Matrix Factorization (`NMF`)**: Suitable for datasets with non-negative features, achieving dimensionality reduction by finding sparse representations of data.
6.  **Kernel Principal Component Analysis (`KPCA`)**: Achieves dimensionality reduction by mapping high-dimensional data to a kernel space in a low-dimensional space, improving classification accuracy and generalization ability.
7.  **Manifold Learning**: Utilizes the geometric structure of data to achieve dimensionality reduction, better reflecting the intrinsic relationships within the data.

The methods for implementing these algorithms using `ChatGPT` are similar to the `PCA` and `LDA` introduced earlier. Specifically, one can use the code generated by `ChatGPT` to implement these algorithms, or perform data preprocessing and feature engineering directly in `ChatGPT`. `ChatGPT`'s powerful language generation and reasoning capabilities can process large-scale high-dimensional data more efficiently and extract useful information from it. If readers are interested in these algorithms, they can try using `ChatGPT` to explore more dimensionality reduction techniques.

---

## 5.1 Creating Basic Charts with ChatGPT

Basic charts are the foundation of data visualization; they can intuitively display data distribution, relationships, and trends. By creating basic charts with `ChatGPT`, readers can quickly transform data into easy-to-understand graphics, providing strong support for data analysis. This section introduces how to use `ChatGPT` to create basic charts, including line charts, trend charts, bar charts, column charts, pie charts, donut charts, scatter plots, and bubble charts. These chart types have a wide range of application scenarios, such as showing changes in time series data, comparing data across different categories, describing proportional relationships in data, etc. By mastering the drawing methods of these basic charts, readers will be able to better utilize `ChatGPT` for data visualization.

### 5.1.1 Drawing Line Charts and Trend Charts with ChatGPT

A line chart is a common data visualization method suitable for showing the changing trends of time series data. By connecting data points, a line chart can clearly present data fluctuations and trends. A trend chart smooths the line chart to show the overall trend of the data. Drawing line charts and trend charts with `ChatGPT` can quickly reveal data change patterns, providing strong support for data analysis. Below are two examples.

#### Example 1: Drawing a Line Chart

`Xiao Ming` is a sales data analyst at a fresh food supermarket. He is responsible for analyzing the supermarket's sales data and providing decision support. Table 5.1 is the sales data table for the supermarket from January to April 2023 organized by `Xiao Ming`, including 3 indicators: `Sales Revenue`, `Sales Volume`, and `Profit`.

**Table 5.1 Supermarket Sales Table**

![](Images/9787301342022_Figure-0146-0140.jpg)

**Continued Table**

![](Images/9787301342022_Figure-0147-0141.jpg)

From the above data table, we can see the sales situation of the fresh food supermarket from January to April 2023, and `Sales Revenue`, `Sales Volume`, and `Profit` are all gradually increasing, but the specific trends need to be analyzed through a line chart. Now, `Xiao Ming` uses `ChatGPT` to draw a line chart to better present the supermarket's sales situation from January to April 2023, with the prompt shown in 5-1.

#### 5-1 Supermarket Sales Line Chart Prompt

> You are a Senior Data Analyst with the following capabilities.
>
> - Data analysis skills: Possess the ability to deeply understand data, effectively interpret and analyze data, and draw valuable conclusions.
> - Statistical knowledge: Possess basic statistical knowledge, able to use statistical methods to analyze data, conduct data modeling and prediction.
> - Data visualization: Able to use various data visualization tools to present data, making it easier to understand and communicate.
> - Programming ability: Possess programming skills, able to use programming languages such as `Python`, `R` for data analysis and processing.
> - Business understanding ability: Understand business background and goals, able to combine data analysis results with business needs to provide practical solutions.
> - Communication and expression ability: Able to clearly express data analysis results, transform complex data analysis results into concise reports and presentations, and communicate effectively with the business side.
> - Innovation ability: Possess innovative thinking, able to find new data analysis methods and technologies, continuously improve data analysis methods, and improve data analysis efficiency and accuracy.
>
> I am a sales data analyst at a fresh food supermarket, responsible for analyzing the supermarket's sales data and providing decision support. Now, the sales data for the past few months has been collected, and I need to analyze this data to draw valuable conclusions and provide decision support for the supermarket's operations.
>
> The data has been saved by me in an `Excel` file named "`Supermarket Sales Data.xlsx`", which contains the supermarket's sales data for the past few months.
>
> Please analyze this data from a professional perspective, draw a line chart, and present the analysis results.
>
> Provide the `Python` code directly in a code block format.

`ChatGPT` generated the line chart code as shown in 5-2.

#### 5-2 ChatGPT Generated Line Chart Code

![](Images/9787301342022_Figure-0148-0142.jpg)![](Images/9787301342022_Figure-0149-0143.jpg)

The above code reads an `Excel` file of supermarket sales data, analyzes the `Sales Revenue` data, and draws a line chart of the sales revenue trend. In the line chart, the date corresponding to the maximum `Sales Revenue` is highlighted to show the peak growth of `Sales Revenue`.

Copy the above code into `PyCharm` and run it to obtain the final statistical data as shown in 5-3.

#### 5-3 Supermarket Sales Statistical Data

```
Total Sales Revenue: 3,290,000
Total Sales Volume: 267,200
Total Profit: 632,000
```

At the same time, the line chart drawn by the program is shown in Figure 5.1.

