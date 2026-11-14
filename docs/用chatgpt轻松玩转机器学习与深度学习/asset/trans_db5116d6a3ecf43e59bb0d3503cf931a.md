
In summary, the difference between clustering algorithms and classification algorithms lies in that clustering algorithms have no predefined categories and generate categories based on sample similarity; whereas classification algorithms require predefined class labels and learn a classification model to classify unknown samples. Clustering algorithms can help us discover hidden structures in data, while classification algorithms predict and classify known structures.

The above response can be understood as: the role of clustering algorithms is similar to grouping a crowd of people by similarity, putting similar people in the same group and dissimilar people in different groups. In this way, people within each group are similar to each other, while people between different groups differ significantly. The role of classification algorithms is similar to labeling things, such as labeling apples and oranges with "apple" and "orange" tags respectively, for future identification.

So what are some common clustering algorithms? Let's see what `ChatGPT` says. Enter "Please list a few of the most commonly used clustering algorithms" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The following are a few commonly used clustering algorithms.

1. `K-Means` clustering algorithm: An iterative clustering algorithm based on distance measurement that partitions data into k clusters. It iterates using the sample mean as the center point and optimizes the clustering result by minimizing the sum of distances between samples within each cluster and the cluster center.

2. Hierarchical clustering algorithm: A clustering algorithm based on distance measurement that organizes data into a hierarchical cluster tree. This algorithm can be divided into two types: agglomerative clustering and divisive clustering, which build the cluster tree from different directions.

3. `DBSCAN` clustering algorithm: A density-based clustering algorithm that groups samples with higher density together. This algorithm can automatically discover clusters of arbitrary shapes and exclude interference from outliers.

4. `GMM` clustering algorithm: A probabilistic model-based clustering algorithm that regards samples as a mixture distribution composed of multiple Gaussian distributions, uses the `EM` algorithm to solve for the distribution parameters, and thus clusters the data.

Each of these algorithms has its own pros and cons and is suitable for different scenarios and data types. Choosing the appropriate clustering algorithm can effectively discover patterns and structures in data.

In the above response, `ChatGPT` listed a few of the most commonly used clustering algorithms. Next, we will conduct an in-depth study of some of these algorithms.

## 9.2 Let ChatGPT Guide Us in Mastering K-Means

From the previous section, we learned that the `K-Means` algorithm is an algorithm used to partition a dataset into k clusters, where k is the number of clusters we preset. The `K-Means` algorithm features simplicity, high efficiency, and ease of implementation, and has been widely used in practical applications. Let's first get to know this algorithm.

### 9.2.1 The Basic Idea of the K-Means Algorithm

Enter "The basic idea of the K-Means algorithm" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The basic idea of the K-Means algorithm is to partition the dataset into k clusters, where k is specified in advance. The algorithm adjusts the cluster centers iteratively until the distance between all sample points within a cluster and the cluster center is minimized.

Specifically, the K-Means algorithm contains the following steps.

1. Randomly select k data points as the centers of the k clusters.

2. For each data point, calculate its distance to the k cluster centers and assign it to the nearest cluster.

3. Update the center of each cluster, i.e., take the mean of all data points in the cluster as the new cluster center.

4. Repeat steps 2 and 3 until the distance between sample points and cluster centers within clusters no longer changes or the preset number of iterations is reached.

The goal of the K-Means algorithm is to minimize the Sum of Squared Errors (`SSE`) between sample points within clusters and their cluster centers.

In the above response, we can see that the main idea of K-Means is: the K-Means algorithm first randomly selects k data points as the centers of k clusters, then assigns each data point to the cluster whose center is nearest. Next, it recalculates the center of each cluster, updating it to the average of all data points in that cluster. This is also the origin of the name K-Means.

The `ChatGPT` response involves a term — Sum of Squared Errors (`SSE`). If readers are not familiar with this term, they can ask `ChatGPT` for an explanation. Enter "Explain Sum of Squared Errors in simple terms" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The Sum of Squared Errors (Sum of Squared Errors, `SSE`) is a metric used to measure the effectiveness of a clustering algorithm. It measures the sum of squared distances between each data point and the center of its assigned cluster; the smaller the distance, the smaller the `SSE` value, indicating better clustering performance.

For example, if we divide a group of data points into two clusters, where one cluster center is (3,4) and the other is (8,10), and the distances between each data point and its cluster center are 1, 2, 3, 4, 5, 6, 7, 8 respectively, then the `SSE` value for these two clusters is `` `12+22+32+42+52+62+72+82=204` ``. If we use another clustering method and the resulting clusters have a smaller `SSE` value compared to this result, it indicates that this new clustering method performs better.

Therefore, the `SSE` value is an important metric used to evaluate clustering performance in clustering algorithms.

From the above response, we can see that `ChatGPT` provided a layman's explanation of the Sum of Squared Errors: the `SSE` value can be used to evaluate clustering performance, and a smaller value indicates better performance.

### 9.2.2 Steps and Process of the K-Means Algorithm

After understanding the basic idea of the K-Means algorithm, we can continue to learn its specific implementation, which helps us intuitively grasp its working principle. Enter "Please demonstrate the steps and process of the K-Means algorithm with Python code, requiring visualization of the process" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The following is an example code implementing the steps and process of the K-Means algorithm using `Python` and visualizing the process. In this example, we will use the `KMeans` class from the `scikit-learn` library to implement the K-Means algorithm, and use the `scatter` function from the `Matplotlib` library to visualize the clustering results.

![](Images/9787301342640_Figure-0171-0208.jpg)![](Images/9787301342640_Figure-0172-0209.jpg)

Paste the code into `Jupyter Notebook` and run it to get the result shown in Figure 9-1.

In the code provided by `ChatGPT`, 50 two-dimensional random data points are first generated and stored in a two-dimensional `NumPy` array `X`. Next, the `KMeans` function from the `scikit-learn` library is called, specifying the number of clusters as 3 and the random seed as 0, to perform clustering on the data, with the result stored in the variable `kmeans`. The cluster index for each data point is obtained via `kmeans.labels_`, and the center of each cluster is obtained via `kmeans.cluster_centers_`. Finally, the `scatter` function from the `Matplotlib` library is used to visualize each data point according to its assigned cluster, while also displaying the center of each cluster in the figure, which are the points represented by the × marks in Figure 9-1.

![](Images/9787301342640_Figure-0172-0210.jpg)

Figure 9-1 Clustering using the K-Means Algorithm

### 9.2.3 Pros and Cons of the K-Means Algorithm

As a very classic clustering algorithm, K-Means has many advantages. Of course, dialectically speaking, it also has some shortcomings. Let's ask `ChatGPT` to explain. Enter "Pros and cons of the K-Means algorithm" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The K-Means algorithm is a simple and efficient clustering algorithm with the following advantages.

1. Simple algorithm: The K-Means algorithm is very easy to understand and implement, requiring only iterative optimization of clustering results, making it very suitable for handling large amounts of data.

2. Strong scalability: The computational cost and time complexity of the K-Means algorithm increase linearly with the size of the dataset and the number of features, so it can be easily scaled to large datasets.

3. Wide applicability: The K-Means algorithm can be applied to various data types, such as numerical, discrete, and binary types.

However, the K-Means algorithm also has some disadvantages.

1. Requires preset number of clusters: The K-Means algorithm requires the number of clusters to be determined in advance, but in practical applications, it is difficult to know the optimal number of clusters beforehand, so it must be chosen through experimentation and experience.

2. Sensitive to initial values: The clustering result of the K-Means algorithm largely depends on the selection of initial cluster centers; different initial centers yield different results, so multiple runs are needed to select the best result.

3. Sensitive to noise and outliers: The K-Means algorithm assumes that each cluster is convex and has similar size and density. Therefore, if noise or outliers exist in the data, these points may be incorrectly clustered into a cluster, leading to distorted clustering results.

Therefore, when using the K-Means algorithm, one needs to make trade-offs and adjustments based on the specific application scenario and data type to achieve better clustering results.

So in practical applications, how can we avoid the shortcomings of the K-Means algorithm? We can ask `ChatGPT`. Enter "How to avoid the shortcomings of the K-Means algorithm" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The K-Means algorithm has some shortcomings, such as sensitivity to initial values, requiring a preset number of clusters, and sensitivity to noise and outliers, but we can avoid these problems as much as possible through the following methods.

1. Choose appropriate initial values: Heuristic algorithms can be used or initial cluster centers can be randomly selected, then clustering is performed multiple times, selecting the result with the smallest `SSE`. For example, the `K-Means++` algorithm can effectively select appropriate initial cluster centers.

2. Choose the appropriate number of clusters: Clustering evaluation metrics such as the Silhouette Coefficient, Davies-Bouldin Index, and Calinski-Harabasz Index can be used to evaluate clustering performance under different numbers of clusters and select the optimal number.

3. Use hierarchical clustering algorithms: Hierarchical clustering algorithms can group data points into different hierarchical structures based on their similarity, avoiding the need to pre-determine the number of clusters, while also detecting and handling outliers and noise.

4. Use density-based clustering algorithms: Density-based clustering algorithms (such as `DBSCAN`) can automatically identify cluster shapes and sizes, and handle noise and outliers, making them more robust compared to K-Means.

5. Data preprocessing: Techniques such as data normalization, feature selection, and dimensionality reduction can be used to reduce data dimensionality and complexity, thereby improving clustering performance and reducing algorithm sensitivity.

In summary, by selecting appropriate initial values, number of clusters, algorithms, and data preprocessing methods, the shortcomings of the K-Means algorithm can be effectively avoided, improving clustering performance and robustness.

In the above response, `ChatGPT` gave several suggestions for avoiding the shortcomings of the K-Means algorithm. Interestingly, suggestions 3 and 4 are quite notable. Colloquially speaking, `ChatGPT` means "If you want to avoid K-Means' shortcomings, just don't use it." Next, let's learn about the clustering algorithms involved in these two suggestions.

## 9.3 Let ChatGPT Guide Us in Mastering Hierarchical Clustering

In the previous section, `ChatGPT` mentioned hierarchical clustering algorithms. Hierarchical clustering is a clustering method that groups data points into different hierarchical structures based on the similarity between them. This hierarchical structure can be represented by a tree diagram, also known as a "cluster tree" (dendrogram). In the cluster tree, each leaf node represents a single data point, while internal nodes represent clusters of data points.

Next, let's have `ChatGPT` detail the relevant knowledge of hierarchical clustering algorithms.

### 9.3.1 The Basic Idea of Hierarchical Clustering Algorithms

Enter "Please explain the basic idea of hierarchical clustering algorithms in detail" in the `ChatGPT` dialog box, and you can get the following response.

`ChatGPT` Hierarchical clustering algorithms are clustering methods based on similarity measures. Their basic idea is to measure the similarity between data points and group them into different hierarchical structures. This hierarchical structure can be represented by a tree diagram, also known as a "cluster tree". In the cluster tree, each leaf node represents a single data point, while internal nodes represent clusters of data points.

Hierarchical clustering algorithms have two approaches: top-down and bottom-up. The top-down algorithm starts with all data points as a single cluster and then recursively splits them into smaller clusters until each data point is its own cluster. The bottom-up algorithm starts with each data point as a single cluster and then gradually merges them into larger clusters until the entire dataset becomes one cluster.

In each cluster merging step, the similarity between each pair of clusters needs to be calculated, and they are combined into larger clusters. This similarity can be calculated by different methods, such as `Euclidean distance`, `Manhattan distance`, `Correlation coefficient`, etc.

Specifically, the basic idea of the bottom-up algorithm (also known as agglomerative hierarchical clustering) starts with each data point as a separate cluster, then gradually merges them into larger clusters. In each merge, the similarity between all pairs of clusters needs to be calculated, the two clusters with the highest similarity are selected for merging, until all data points are merged into one cluster. This process can be represented by a tree diagram, i.e., the cluster tree.

The top-down algorithm (also known as divisive hierarchical clustering) starts with all data points as a single cluster, then gradually splits them into smaller clusters. In each split, the similarity between all pairs of clusters needs to be calculated, the cluster with the lowest similarity is selected for splitting, until each cluster contains only one data point.

In the above response, `ChatGPT` introduced the basic idea of hierarchical clustering and listed two different hierarchical clustering algorithms: agglomerative hierarchical clustering and divisive hierarchical clustering. If we want to compare the differences between hierarchical clustering and K-Means, we can enter "Please explain the difference between hierarchical clustering and K-Means in simple terms" in the `ChatGPT` dialog box, and get the following response.

`ChatGPT` The difference between hierarchical clustering and K-Means lies in the clustering approach. K-Means is a partitioning clustering algorithm that divides the dataset into k clusters, with each data point belonging to one of them. This method requires specifying the number of clusters k first, then assigning data points to the nearest cluster center. The cluster center is calculated based on the mean of data points in each cluster, and the clustering process iteratively updates cluster centers and assigns data points until convergence.

Hierarchical clustering, on the other hand, is an agglomerative clustering algorithm that does not require pre-specifying the number of clusters k. The algorithm starts from each data point and gradually merges them into larger clusters until all data points are in a single cluster. Specifically, it treats each data point as a separate cluster, then gradually merges these clusters based on similarity until one large cluster is obtained. The merging process can be represented by a tree diagram, where each node represents a cluster, and the path from the root node to the leaf nodes represents the cluster merging process.

Therefore, K-Means requires specifying the number of clusters and can only yield flat clustering results, while hierarchical clustering does not require specifying the number of clusters and can yield hierarchical (tree-structured) clustering results. At the same time, hierarchical clustering allows choosing different linkage methods and distance measures as needed, making it more flexible. However, hierarchical clustering has higher computational complexity and is not suitable for processing large-scale data.

From the above response, we can see that there are significant differences between hierarchical clustering and K-Means. From a practical application perspective, hierarchical clustering does not require specifying the number of clusters like K-Means, making it more convenient.

Next, we will still use some examples to intuitively understand how hierarchical clustering works.

### 9.3.2 Principles and Implementation of Agglomerative Hierarchical Clustering

As mentioned earlier, there is a bottom-up algorithm in hierarchical clustering called agglomerative hierarchical clustering. To clearly and intuitively understand its principle, we can enter "Please visualize the principle of the agglomerative hierarchical clustering algorithm with Python" in the `ChatGPT` dialog box, and get the following response.

`ChatGPT` Here is a code example using `Python` to visualize the agglomerative hierarchical clustering algorithm, which uses the `SciPy` and `Matplotlib` libraries.

![](Images/9787301342640_Figure-0175-0211.jpg)

Paste the code into `Jupyter Notebook` and run it to get the result shown in Figure 9-2.

![](Images/9787301342640_Figure-0175-0212.jpg)

Figure 9-2 Principle of Agglomerative Hierarchical Clustering

In this example, we first generate a random dataset `X`, then use the `linkage()` function from the `SciPy` library to calculate the distance matrix `Z` between data points, and pass it as input to the `dendrogram()` function. In Figure 9-2, the `dendrogram()` function draws a tree diagram where each leaf node represents a data point, and the root node represents the merged cluster of all data points. Nodes that are closer in distance are closer on the tree, and the distance between nodes represents the distance of the merged clusters.

Through visualization, we can better understand the principle of the agglomerative hierarchical clustering algorithm. Of course, in actual work, we can easily apply agglomerative hierarchical clustering using the `scikit-learn` library. For the specific operation, we can ask `ChatGPT` like this — "Please demonstrate the agglomerative hierarchical clustering algorithm using the scikit-learn library, with visualization required", and get the following response.

`ChatGPT` The following is the code and visualization demonstrating the agglomerative hierarchical clustering algorithm using the `scikit-learn` library. We use the `AgglomerativeClustering` class from the `scikit-learn` library to implement the agglomerative hierarchical clustering algorithm, and use the `Matplotlib` library to visualize the clustering results.

![](Images/9787301342640_Figure-0176-0213.jpg)

Paste the code into `Jupyter Notebook` and run it to get the result shown in Figure 9-3.

![](Images/9787301342640_Figure-0176-0214.jpg)

Figure 9-3 Implementation of Agglomerative Hierarchical Clustering

In the code generated by `ChatGPT`, we first generate a two-dimensional dataset with 150 samples, then use the `AgglomerativeClustering` class to initialize the agglomerative hierarchical clustering algorithm, and set the number of clusters to 3. Then, we use the `fit_predict` method to cluster the data and visualize the clustering results; in Figure 9-3, dots of different colors represent different clusters.

### 9.3.3 Principles and Implementation of Divisive Hierarchical Clustering

Through previous learning, we already know that agglomerative hierarchical clustering is a bottom-up hierarchical clustering algorithm. Next, let's learn about a top-down hierarchical clustering algorithm — divisive hierarchical clustering. Again, we will use code for visualization to intuitively understand this algorithm's principle. Enter "Please visualize the principle of the divisive hierarchical clustering algorithm with Python" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` Divisive hierarchical clustering is a clustering algorithm different from agglomerative hierarchical clustering; it adopts a top-down divisive strategy, starting from the entire dataset and partitioning it into subsets until each subset contains only one data point.

Visualization of the divisive hierarchical clustering algorithm can be implemented using the `fcluster()` and `dendrogram()` functions from the `SciPy` library. The following is a `Python` code example.

![](Images/9787301342640_Figure-0177-0215.jpg)

Paste the code into `Jupyter Notebook` and run it to get the result shown in Figure 9-4.

![](Images/9787301342640_Figure-0178-0216.jpg)

Figure 9-4 Principle of Divisive Hierarchical Clustering Algorithm

In this example, we first generate a random dataset `X`, then use `SciPy`'s `linkage()` function to calculate the distance matrix `Z` between data points, and pass it as input to the `dendrogram()` function to draw the tree diagram. We also partition the dataset into different clusters by specifying a threshold `max_dist`, and color-code the cluster labels. Finally, we use the `Matplotlib` library to plot the data points and cluster division results. Through Figure 9-4, we can better understand the principle of the divisive hierarchical clustering algorithm.

Similarly, `scikit-learn` also has built-in tools for divisive hierarchical clustering. We can ask `ChatGPT` for example code. Enter "Please demonstrate the implementation of the divisive hierarchical clustering algorithm using scikit-learn" in the `ChatGPT` dialog box, and you will get the following response.

`ChatGPT` The following is an example code implementing the divisive hierarchical clustering algorithm using `scikit-learn`.

![](Images/9787301342640_Figure-0178-0217.jpg)![](Images/9787301342640_Figure-0179-0218.jpg)

Paste the code into `Jupyter Notebook` and run it to get the result shown in Figure 9-5.

![](Images/9787301342640_Figure-0179-0219.jpg)

Figure 9-5 Result of Divisive Hierarchical Clustering

In this example, we generated 50 two-dimensional random data points and used `scikit-learn`'s `AgglomerativeClustering` to implement the divisive hierarchical clustering algorithm. We set the `n_clusters` parameter to `None`, indicating that the algorithm should determine the final number of clusters based on a given distance threshold. Here, we set the distance threshold to 2 and chose `ward` as the linkage method, meaning the algorithm will try to minimize the variance within each cluster. Finally, we used the `labels_` attribute to obtain the cluster label for each data point and visualized the results.

### 9.3.4 Pros and Cons of Hierarchical Clustering Algorithms
