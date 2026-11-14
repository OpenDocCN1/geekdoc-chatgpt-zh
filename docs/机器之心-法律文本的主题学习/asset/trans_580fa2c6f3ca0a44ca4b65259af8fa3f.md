
# 6.4 Wrapper Feature Selection

## 6.4.1 Heuristic Wrapper Model Based on Feature Extraction

From a certain perspective, the feature selection process is essentially a process of searching for feature subsets from the feature space set. In legal text processing, many features exhibit various correlations; for instance, the words "contract" (合同) and "breach" (违约) have a strong correlation. Legal text data is characterized by high dimensionality. If a complete search is conducted from the feature space constituted by all words, both spatial and temporal complexity will be extremely large, rendering it of little practical significance. When solving such problems, it is often difficult to achieve the global optimal solution; in this case, one can settle for the next best thing and seek a sub-optimal solution. A greedy algorithm starts from an initial solution and, according to an optimization criterion, seeks local optimal solutions step by step. Each step considers only the sub-problem, achieving a global sub-optimal solution through local optimal solutions. Based on the greedy idea, heuristic methods start from an empty feature subset, selecting a feature that optimizes the feature evaluation function each time and adding it to the subset, until a certain evaluation function reaches a threshold. The evaluation function is a measure describing the quality of the selected feature subset; the choice of evaluation function varies according to different evaluation objectives. For example, some evaluation functions are designed based on whether the dataset distribution is balanced, while others evaluate based on the classification performance of machine learning models. Therefore, one must comprehensively consider the intrinsic characteristics of the dataset and application scenarios such as the learning model. If the intrinsic structural characteristics of the dataset are used, utilizing its intrinsic feature evaluation function, and feature selection is treated as a data preprocessing stage independent of the learning model and algorithm, this is called a **Filter Model**. The evaluation functions used in the previous sections, which are independent of classification results, belong to the filter model. If the classification results of a machine learning model are used to verify whether the dataset selection is appropriate, the classification result evaluation depends on the learning algorithm and dataset selection. By fixing the learning algorithm, the quality of the feature subset can be assessed through classification results; this is called a **Wrapper Model**.

The wrapper model adopts the **correlation principle**, and its processing flow is shown in Figure 6.8. The wrapper model selects a feature subset according to the feature selection method, then inputs this subset into the learning algorithm to compute the classification performance of the learning algorithm, using the quality of the classification performance as the criterion for evaluating the quality of the feature subset. The feature subset that satisfies the target classification accuracy is taken as the final feature subset. Obviously, the classification accuracy of the same dataset differs across different classification algorithms. Therefore, feature subset selection using the wrapper model is dependent on the learning algorithm. During the feature space search, whether to continue generating the next feature subset requires consideration of a stopping criterion. For different evaluation functions, the stopping criteria also differ. The stopping condition can be set as a threshold; if the value of the evaluation function reaches this threshold, no new feature subsets are generated. For example, the stopping condition for a filter can be the feature combination with the maximum average spacing selected according to the sample average coverage principle. The stopping condition for a wrapper can be the feature subset that returns the highest accuracy on the validation set. The dataset can be divided into a training set, a validation set, and a test set: first, the learning algorithm parameters are trained using the feature subset of the training set, and the classification performance of the learning algorithm is calculated on the validation set. Then, the feature subset is selected based on the best classification performance on the validation set. Finally, test data is used to verify the effectiveness of the feature subset selection. The division of training, validation, and test sets must avoid data coupling and overlap. The aforementioned filter and wrapper methods can be combined; for example, using a heuristic feature search method under a combination of accuracy, recall, and F1-measure.

![Figure 6.8 Wrapper Principle](images/txt006_55.jpg "pagenumber_ebook=173,pagenumber_book=164")

Feature extraction methods map the high-dimensional original space to a low-dimensional feature subset space. Principal Component Analysis (PCA) is a coordinate transformation method that can remove redundant features. During the specific feature transformation process, smaller eigenvalues are discarded, thereby achieving the goals of denoising, removing correlation, and feature reduction. Wavelet is also a method of feature space transformation; compared to the Fourier Transform, the Wavelet Transform can better adapt to drastic changes.

## 6.4.2 Wrapper Model Based on Principal Component Analysis

Through matrix transformation, the original feature space is projected onto the principal component space. Although dimensionality reduction is achieved while preserving original information, the principal component transformation alters the data information structure of the original feature space. Therefore, not all principal components contribute positively to the classification results, necessitating principal component selection.

Since the eigenvalue of each principal component already contains a measure of the intrinsic structural aspects of the dataset, the evaluation function for principal component selection should focus on the correlation criterion. The sample set is classified using the principal component subset selected by the classifier, and the classification accuracy serves as the basis for evaluating the quality of the principal component subset. The search for the principal component subset stops when a certain classification accuracy is reached. Classification accuracy can include accuracy, recall, F1-measure, etc. The Principal Components Subset Selection (PCSS) wrapper model based on performance evaluation is shown in Figure 6.9:

![Figure 6.9 Principal Component Selection Wrapper Model](images/txt006_56.jpg "pagenumber_ebook=174,pagenumber_book=165")

High-dimensional legal text requires spatial dimensionality reduction without loss of original information, which calls for a feature extraction method. In statistical problems, to study issues objectively, various influencing factors must be considered comprehensively; these numerous factors constitute high-dimensional vectors. These high-dimensional vectors reflect certain information of the research data to varying degrees. When two spatial vectors have a certain correlation, it can be considered that these two vectors contain a certain amount of overlapping information. Through orthogonal transformation, Principal Component Analysis (PCA) converts high-dimensional vectors with correlation into a set of linearly uncorrelated low-dimensional vectors. PCA is a feature extraction method; although it is a dimensionality reduction method, every feature in the original feature space has a projection on the new dimensions in the new space (called principal components). Therefore, unlike general feature selection methods, PCA maximally preserves the information of the original feature space. In other words, PCA projects vectors that are difficult to distinguish in the dimensions of the original feature space onto another orthogonal space through an orthogonal transformation; the original data exhibits larger differences on the dimensions of the transformed orthogonal space, making them easier to distinguish.

Through an orthogonal transformation, PCA transforms the original high-dimensional space vectors with correlated components into new low-dimensional space vectors with uncorrelated components. Algebraically, this manifests as transforming the covariance matrix of the original random vector into a diagonal matrix. Let `xi` represent the feature vector of the `i`-th text in the original vector space model; the original feature vector has `n` dimensions, and `xi` is expressed as:

![Formula](images/txt006_57.jpg "pagenumber_ebook=175,pagenumber_book=166")

In the formula, `xij` is the value of feature word `j` in the dictionary within sample `i`. The standardization transformation of the dataset includes calculating the mean and variance:

![Formula](images/txt006_58.jpg "pagenumber_ebook=175,pagenumber_book=166")

Where `m` is the number of samples in the dataset, `μj` is the mean of the values on the `j`-th dimension of the original vector space model with high dimensionality, and `σj²` is the variance on that dimension. The standardization operation is performed:

![Formula](images/txt006_61.jpg "pagenumber_ebook=175,pagenumber_book=166")

PCA seeks a vector or direction in the original vector space model such that the variance produced by projecting all samples onto this vector direction is maximized. Assuming the unit vector of the projection direction that yields the maximum variance is `v`, the objective function can be expressed as:

![Formula](images/txt006_62.jpg "pagenumber_ebook=175,pagenumber_book=166")

Simultaneously, the following constraint condition must be satisfied:

![Formula](images/txt006_63.jpg "pagenumber_ebook=175,pagenumber_book=166")

In the formula, `vT` is the transpose of `v`. The Lagrangian multiplier function is constructed from the objective function and the constraint condition:

![Formula](images/txt006_64.jpg "pagenumber_ebook=175,pagenumber_book=166")

Let:

![Formula](images/txt006_65.jpg "pagenumber_ebook=175,pagenumber_book=166")

When the Lagrangian multiplier function reaches its maximum value, the partial derivative must be zero:

![Formula](images/txt006_66.jpg "pagenumber_ebook=175,pagenumber_book=166")

From equations (6.42) and (6.43), it can be seen that the projection direction yielding the maximum variance is related to the eigenvectors of the covariance matrix of the standardized dataset. The covariance matrix `Cov` is an `n×n` square matrix. Assuming the rank of the covariance matrix is `k`:

![Formula](images/txt006_67.jpg "pagenumber_ebook=176,pagenumber_book=167")

The number of eigenvectors of the covariance matrix is `n`, and the number of eigenvectors with non-zero eigenvalues is `k`, and:

![Formula](images/txt006_68.jpg "pagenumber_ebook=176,pagenumber_book=167")

Thus, eigenvalue `λ1` corresponds to eigenvector `v1`, eigenvalue `λ2` corresponds to eigenvector `v2`, and so on. The variance of the dataset projected onto eigenvector `v1` is the largest, followed by `v2`, gradually decreasing. From the perspective of classification tasks, intra-class spacing is small while inter-class spacing is large. Therefore, the maximum variance projected onto eigenvector `v1` is beneficial for classification, indicating that the principal component transformation loses the least information in classification tasks.

From the perspective of rotation and scaling, a matrix transformation is a rotation, scaling along the coordinate axes, and then rotating back. Based on the relationship between the eigenvalues and eigenvectors of the covariance matrix, the matrix representation is as follows:

![Formula](images/txt006_69.jpg "pagenumber_ebook=176,pagenumber_book=167")

`Σ` is a diagonal matrix composed of eigenvalues `λi`. Assuming `U` is an `m×m` orthogonal matrix and `S` is an `m×k` matrix, it can be made so that:

![Formula](images/txt006_71.jpg "pagenumber_ebook=176,pagenumber_book=167")

Where:

![Formula](images/txt006_72.jpg "pagenumber_ebook=176,pagenumber_book=167")

Additionally:

![Formula](images/txt006_73.jpg "pagenumber_ebook=176,pagenumber_book=167")

Substituting equation (6.50) into equation (6.48) yields:

![Formula](images/txt006_74.jpg "pagenumber_ebook=176,pagenumber_book=167")

According to equation (6.47), it is known that:

![Formula](images/txt006_75.jpg "pagenumber_ebook=176,pagenumber_book=167")

If the number of samples `m` is greater than the number of features `n`, the following formula is used for projection:

![Formula](images/txt006_76.jpg "pagenumber_ebook=176,pagenumber_book=167")

If the number of samples `m` is less than the number of features `n`, the following formula is used for projection:

![Formula](images/txt006_77.jpg "pagenumber_ebook=176,pagenumber_book=167")

Text feature selection is generally related to words. Words in general or narrative texts can be adjusted opportunely according to expressive needs, offering more flexibility. Legal texts, however, are relatively standardized written languages reflecting the will of the ruling class, possessing strong authority and solemnity. For example, colloquial words like "good guy" or "bad guy" (好人或坏人) do not appear in legal texts. To express rigorous and precise meanings, legal texts contain a large number of legal terms, i.e., legal vocabulary. Legal terms are used to express specific legal phenomena and content, such as "contract" (合同), "apparent agency" (表见代理), "invalid" (无效), "rescission" (撤销), "evidence" (证据), "appeal" (上诉), etc. These legal terms have specific scopes of application and must not be arbitrarily interpreted or replaced by other vocabulary. Legal vocabulary also includes some words originating from general vocabulary but closely related to legal activities, used to regulate legal acts and reflect legal characteristics, such as "prohibited" (禁止), "shall" (应当), and "may" (可以).

The legal text feature extraction process is shown in Figure 6.10. Stage ① is the data preparation stage, providing the `FCA` or `CJO` dataset, and providing a legal terminology dictionary according to the content in Appendix A; the daily vocabulary dictionary consists of general words excluding legal terms and stop words. Stage ② performs word segmentation on the legal texts in the corpus, disregarding the order of words in the text, and establishes the corresponding bag-of-words set for the text. Stage ③ establishes the legal terminology `VSM` (Vector Space Model) and the daily vocabulary `VSM` model according to the legal terminology dictionary and the daily vocabulary dictionary, respectively. Stage ④ performs principal component transformation according to the number of principal components corresponding to the legal topic, obtaining the principal components relative to the legal topic. Stage ⑤ transforms the daily vocabulary feature set into a daily vocabulary principal component set through principal component analysis. Stage ⑥ divides the daily vocabulary principal component set into training, validation, and test sets, retaining the training and validation sets. Stage ⑦ inputs the training set into the classifier for training to obtain classifier model parameters. Stage ⑧ evaluates the performance of the trained classifier using the validation set to judge whether the principal component selection is appropriate; if the target is not met, it returns to Stage ⑥ to re-select principal components until the performance on the validation set meets the target. Stage ⑨ merges the daily vocabulary principal component subset and the legal terminology principal component subset to obtain the extraction result of the legal text corpus mapped in space.

![Figure 6.10 PCA Legal Text Feature Extraction](images/txt006_78.jpg "pagenumber_ebook=178,pagenumber_book=169")

According to equation (6.46), the cumulative contribution rate of the first few principal components is calculated to ensure that the cumulative contribution rate reaches a high level, i.e., the amount of information retained after high-dimensional feature dimensionality reduction must be maintained at a high level. Define the Accumulative Contribution Rate (ACR) of information variance:

![Formula](images/txt006_79.jpg "pagenumber_ebook=178,pagenumber_book=169")

The number of extracted principal components must, on one hand, ensure the information in the original vector space that is conducive to classification, and on the other hand, consider the influence of noise. When the cumulative contribution rate reaches a certain level, the classification intervals in the original vector space are maximally preserved, but it is also possible that noise misleading to classification has been introduced. On the principal component training set, according to the PCA feature extraction method, the original high-dimensional feature space is mapped to a `k`-dimensional principal component space:

![Formula](images/txt006_80.jpg "pagenumber_ebook=179,pagenumber_book=170")

Therefore, principal component selection generally uses an evaluation function to evaluate the `n`-dimensional principal components individually or as a whole, retaining principal components that enhance classification and removing those that interfere with classification. The result is a subset of the `n`-dimensional principal component set, essentially a reduction in the number of principal components.

Usually, the interpretation of principal components is somewhat vague to a greater or lesser extent, not as clear and exact as the meaning of the original vectors. Using an effectiveness evaluation function to solve for the optimal principal component combination, generally speaking, traditional methods for seeking optimal or approximate optimal solutions mainly include the enumeration method and heuristic search algorithms. The values of principal component combinations are discrete; all combinations of principal components can be enumerated to find the optimal solution for the principal component combination scenario that achieves the best classification performance. The optimal solution cannot fall outside all principal component combination scenarios.

![Formula](images/txt006_81.jpg "pagenumber_ebook=179,pagenumber_book=170")
