

# 6.5 Hybrid Feature Selection

## 6.5.1 Hybrid Selector Methods

The feature selection hybrid model combines filter methods and wrapper methods to perform dimensionality reduction on the feature space twice. The first dimensionality reduction uses a filter method, and the second uses a wrapper method. The filter employs an independence criterion; the method for evaluating feature subsets relies on the intrinsic properties of the data, such as observing the deviation between theoretical and actual values, the correlation between features and classes, and the distance differences between samples, etc. The wrapper employs a correlation criterion; it first determines a specific machine learning algorithm, then this algorithm classifies the sample set using a given feature subset, and the evaluation function measures the quality of the feature subset by classification accuracy. If the classification performance does not meet the target, the feature subset is reselected, requiring multiple calls to the learning algorithm, resulting in high time complexity; it is suitable for specific learning algorithms but may not be suitable for others. Common filter methods include Chi-square test, correlation, distance, and Information Gain Ratio, etc. Common wrapper methods include classifier accuracy, recall, and F1 measure, etc. The pros and cons and applicability of filters and wrappers are shown in Table 6.2:

Table 6.2 Pros and cons and applicability of two types of evaluation functions

![](images/txt006_88.jpg "pagenumber_ebook=181,pagenumber_book=172")

The most basic idea of the Chi-square test is to determine the correctness of a theory by observing the deviation between actual and theoretical values. The specific approach usually assumes first that the two variables are indeed independent, called the null hypothesis. The actual value is called the observed value, and the theoretical value is the value that should exist if the two are indeed independent. Observing the degree of deviation between the actual and theoretical values, if the deviation is small enough, the error can be considered a natural sampling error, caused by insufficient measurement precision or occurring by chance; the two are indeed independent, and the null hypothesis should be accepted. If the deviation is large enough to a certain extent, making such an error unlikely to be caused by chance or measurement imprecision, it can be considered that the two are actually correlated, and the null hypothesis should be rejected. The theoretical value is `E`, the actual value is `x`, and the formula for calculating the degree of deviation is:

![](images/txt006_89.jpg "pagenumber_ebook=181,pagenumber_book=172")

The above formula is the difference measurement formula used by the Chi-square test. Substituting the provided observed values `x1`, `x2`, …, `xn` of several samples into the formula yields the Chi-square value. Comparing this value with a preset threshold, if it is greater than the threshold, i.e., the deviation is large, the null hypothesis can be considered invalid. Conversely, the null hypothesis is considered valid.

The basic idea of correlation measurement is that a good feature subset should contain features with high relevance to the classification categories, while a poor feature subset contains features with low relevance to the classification categories, and moreover, the correlation between the two features should be low. The linear correlation coefficient can be used to measure the linear correlation between feature vectors:

![](images/txt006_90.jpg "pagenumber_ebook=182,pagenumber_book=173")

The basic idea of distance measurement is that the distance between samples within a class should be as small as possible, while the distance between samples of different classes should be as large as possible. Distance measurement is also called similarity measurement. Common distance measurements include Euclidean distance, Standardized Euclidean distance, and Mahalanobis distance, etc.

![](images/txt006_91.jpg "pagenumber_ebook=182,pagenumber_book=173")

Since filters are independent of specific classification algorithms, they have strong generalization ability across different classification algorithms and lower computational cost. Wrappers, however, apply specific classification algorithms for classification during the evaluation process; therefore, their generalization to other classification algorithms may be poorer, and the computational cost is higher.

When designing machine learning and classification algorithms, one can comprehensively employ independence criteria and correlation measurement methods according to the actual situation, which can better combine with the classification methods in the application scenario to find feature subsets that yield better classification performance.

## 6.5.2 Information Gain Ratio

In a good feature subset, the feature values belonging to the same class should be uniform (single), while those belonging to different classes should be dispersed. For a feature, how much information the dataset contains in two cases: with this feature and without this feature; calculating the difference between these two cases is the amount of information contained in this feature. The information amount containing this feature is called information entropy, and the information amount not containing this feature is called conditional entropy. Information entropy tends to favor features with more values; Information Gain Ratio can avoid the problem caused by too many feature values.

According to the entropy measurement principle, Information Gain Ratio uses `E(DS)` to represent the certainty of dataset `DS` being divided into several categories, e.g., positive and negative classes. The smaller the `E(DS)` value, the higher the certainty of judging a sample as a specific category:

![](images/txt006_92.jpg "pagenumber_ebook=183,pagenumber_book=174")

Where `pl` is the probability of category `l`. For example, `p1` represents the probability of category or label 1 in dataset `DS`, `p2` is the probability of category or label 2 in dataset `DS`, and so on. `|DS|` is the total number of samples in dataset `DS`, i.e., `m`. `DSl` is the data subset on category `l`, i.e., `|DSl|`. The dataset is divided according to attributes or features; `E(DS, fi)` represents the expected entropy generated by dividing dataset `DS` according to feature `fi`:

![](images/txt006_95.jpg "pagenumber_ebook=183,pagenumber_book=174")

Where `Values(fi)` are all values of feature `fi` on dataset `DS`, `DSvfi` is the data subset composed of samples where feature `fi` takes value `v`, and `|DSvfi|` is the number of samples in data subset `DSvfi`. `SplitInfo(DS, fi)` represents the uniformity of dividing dataset `DS` according to feature `fi`:

![](images/txt006_97.jpg "pagenumber_ebook=183,pagenumber_book=174")

Information Gain uses the entropy reduction `Gain(DS, fi)` before and after feature division of the dataset to represent the measure of information entropy decrease after dataset `DS` divides the data space according to feature `fi`:

![](images/txt006_98.jpg "pagenumber_ebook=183,pagenumber_book=174")

Information Gain Ratio uses the quotient `GainRatio(L, vi)` of the information gain before and after a certain feature divides the dataset divided by the uniformity of that feature to describe:

![](images/txt006_99.jpg "pagenumber_ebook=183,pagenumber_book=174")

## 6.5.3 Hybrid Feature Selection (HFS)

In practice, the original feature vectors constructed on the legal text vector space model have tens of thousands of dimensions. There are very many features or words used for legal text classification, including both legal terms and general words. Excessively high dimensions not only introduce noise but also generate redundant computational overhead. Therefore, it is necessary to reduce the data representation dimensionality of legal text classification.

Combining Information Gain Ratio and `PCA` methods, a hybrid feature selection model is constructed in three steps. Step 1: Data Preprocessing; Step 2: Establish Preliminary Feature Subset (PFS); Step 3: Construct Second Feature Subset (SFS). The Hybrid Feature Selection (HFS) model can be summarized as data preprocessing, feature selection, and feature extraction. The process is shown in Figure 6.11:

![](images/txt006_100.jpg "pagenumber_ebook=184,pagenumber_book=175")

Figure 6.11 Hybrid Feature Selection Model

### (1) Preprocessing

In the preprocessing stage, legal texts are segmented according to the vocabulary `Voc`, and the term frequency of words appearing in the legal texts is counted. Term Frequency `TF` emphasizes the number of occurrences of words in legal texts while ignoring their order. After preprocessing, the output is the term frequency text vector space model:

![](images/txt006_101.jpg "pagenumber_ebook=184,pagenumber_book=175")

The vocabulary includes many words and corresponding indices. Through the vocabulary, words can be represented as numbers. The vocabulary size is `n`, the corpus size is `m`, `tfij` is the statistical term frequency of the `j`-th word in the `i`-th text, and the legal text label `Y` is:

![](images/txt006_102.jpg "pagenumber_ebook=184,pagenumber_book=175")

Where `yi` is the supervisory information or one of categories 1, 2, …, `|L|` in the classification task.

### (2) Preliminary Feature Selection

In the preliminary feature subset stage, the Information Gain Ratio of each column or feature in the `TF` matrix is calculated according to Formula (6.67). The Information Gain Ratio of each feature word is expressed as:

![](images/txt006_103.jpg "pagenumber_ebook=184,pagenumber_book=175")

Where `rig1` is the Information Gain Ratio of the 1st word in the vocabulary on the corpus (i.e., the 1st column). A lower Information Gain Ratio reflects a lower classification effect. A threshold is set and feature words in the matrix below the threshold are marked, then the word is deleted from the vocabulary to obtain the updated vocabulary `Voc_new`, with a new vocabulary size of `p`. Based on the vocabulary `Voc_new`, following the `TF-IDF` idea, the text vector space model `TFIDF` is rebuilt:

![](images/txt006_104.jpg "pagenumber_ebook=185,pagenumber_book=176")

The value `tfij` of the `TF` matrix output by preprocessing is the term frequency of the `j`-th feature in the `i`-th text, lacking consideration of the occurrence of this feature in other texts. The value `tfidfij` of the `TF-IDF` matrix output by preliminary feature selection is proportional to the frequency of the feature word in the `i`-th text and inversely proportional to the number of texts in which the feature word appears. The dimension of the `TF` matrix is `n`, the dimension of the `TFIDF` matrix is `p`, and the dimension reduced by the first dimensionality reduction is `n-p`.

### (3) Refined Feature Selection (Second Feature Selection)

In the refined feature selection stage, the `TFIDF` matrix output by preliminary feature selection is mapped to a principal component matrix via the `PCA` method. `TFIDF` is an `m×p` matrix. Through `z` standardization operation, the `Z` matrix is obtained, making the mean and standard deviation meet requirements. The covariance matrix of `Z` is denoted as `CovZ`. There exists an `n`-order orthogonal matrix `V` such that:

![](images/txt006_105.jpg "pagenumber_ebook=185,pagenumber_book=176")

The right side of the above formula is the diagonal matrix `Σ` (often referred to as triangular matrix in the source text, but context implies diagonal/Sigma matrix in SVD/PCA), whose main diagonal elements `λ1…λn` correspond to eigenvectors constituting the orthogonal matrix `V`. The rank of the diagonal matrix `Σ` is `k`. According to Formula (6.50), the relationship between `S` and eigenvalues can be described as:

![](images/txt006_106.jpg "pagenumber_ebook=185,pagenumber_book=176")

Where:

![](images/txt006_107.jpg "pagenumber_ebook=185,pagenumber_book=176")

The identity matrix is denoted as `I`. There exists an `m`-order orthogonal matrix `U` such that:

![](images/txt006_108.jpg "pagenumber_ebook=185,pagenumber_book=176")

According to Formula (6.53), if the number of rows `m` of the `Z` matrix is greater than the number of columns `p`, the `Z` matrix can be projected onto the matrix `V`, i.e., `Z·V`. According to Formula (6.54), if the number of rows `m` of the `Z` matrix is less than the number of columns `p`, the `Z` matrix can be projected onto the matrix `U`, i.e., `ZT·U`.

The standardized `Z` matrix of the preliminary feature subset matrix `TFIDF` can be projected onto the column vector `V` or the row vector `U`, depending on the size relationship between the number of feature words `m` in the new vocabulary `Voc_new` and the number of dataset samples `p`. In other words, if the case of selecting partial principal components is not considered, the dimension reduced by the second dimensionality reduction is at least:

![](images/txt006_109.jpg "pagenumber_ebook=186,pagenumber_book=177")

Assuming dimensionality reduction is performed in the column vector direction, `m≤p`, `k≤p`. Since `σk+1`, `σk+2`…, `σm=0`, derived from Formula (6.54):

![](images/txt006_110.jpg "pagenumber_ebook=186,pagenumber_book=177")

The principal component matrix `PC` is the projection of the standardized matrix `Z` of the preliminary feature subset `TFIDF` onto the matrix `Vk` composed of eigenvectors corresponding to `k` eigenvalues from `λ1` to `λk`:

![](images/txt006_111.jpg "pagenumber_ebook=186,pagenumber_book=177")

In the refined feature selection stage, the output result is the `PC` matrix. Each column is called a principal component, with a total of `k` principal components, i.e., `pc1`, …, `pck`. Arranged by eigenvalues, the first principal component `pc1` has the maximum variance, the second principal component `pc2` the next, and so on.

The hybrid feature selection model composed of the preprocessing module, preliminary feature selection module, and refined feature selection module achieves two rounds of dimensionality reduction. The matrix `TF` output by the preprocessing module has dimension `n`. Inputting matrix `TF` into the preliminary feature selection module outputs matrix `TFIDF` with dimension `p`. Inputting matrix `TFIDF` into the refined feature selection module outputs matrix `PC` with dimension `k`. Alternatively, partial principal components are selected from the `[pc1, …, pck]` matrix, i.e., partial principal components starting from the first principal component, to form matrix `PCS`. Then, `PC` or `PCS` is input into the machine learning model for training.

Algorithm 6.3 (`HFS`) Given input: Text space matrix `X`=![](images/txt006_112.jpg "pagenumber_ebook=186,pagenumber_book=177") number of samples `m`, number of words `n`, number of features retained after first dimensionality reduction `first_n`, number of features retained after second dimensionality reduction `second_n`; Output: Feature selection subset `HFS`. The `HFS` algorithm includes the following processes:

![](images/txt006_113.jpg "pagenumber_ebook=186,pagenumber_book=177")

![](images/txt006_114.jpg "pagenumber_ebook=187,pagenumber_book=178")

![](images/txt006_115.jpg "pagenumber_ebook=188,pagenumber_book=179")

# Chapter 7 Unsupervised Topic Learning for Legal Texts

## 7.1 Feature Representation Based on Legal Terms

### 7.1.1 Chinese Legal Texts

According to relevant regulations, the Supreme People's Court established **China Judgements Online (CJO)** to uniformly publish effective judgment documents from people's courts at all levels. Through compiled legal vocabulary and query key codes, a legal terminology vocabulary list was established, named the `CJO_legal_terms` text file, containing 1,171 legal words including contract, this case, counterclaim, plaintiff, defendant, performance, agreement, liquidated damages, etc., with each word occupying one line, as shown in Figure 7.1:

![](images/txt007_1.jpg "pagenumber_ebook=189,pagenumber_book=180")

Figure 7.1 Legal Terminology Vocabulary List

The first parameter of Python's `codecs.open()` function specifies the path and filename to open, the second parameter specifies the open mode (default is read-only), the third parameter specifies the file encoding format (`utf-8` is a variable-length character encoding of `Unicode`), and the fourth parameter specifies the error handling method. The `readlines()` method reads the entire file at once, then reads it line by line into a list. The `strip()` method removes specified characters from the beginning and end of the string. The `split()` method splits the string according to the specified character. As shown in Figure 7.2:

![](images/txt007_2.jpg "pagenumber_ebook=190,pagenumber_book=181")

Figure 7.2 Opening Legal Terminology Vocabulary File

The `setdefault()` function of Python **Dictionary** takes the key to look up as the first parameter and the key value as the second parameter. If the key is not in the dictionary, it adds the key and specifies the key value. If the key is in the dictionary, it does not set the specified key value and returns the existing key value. The value of the key "Contract" in the legal terminology dictionary is 0, and the value of the key "Plaintiff" is 3, as shown in Figure 7.3:

![](images/txt007_3.jpg "pagenumber_ebook=190,pagenumber_book=181")

Figure 7.3 Key-Values of Legal Dictionary

The legal terminology dictionary can retrieve its key value by key. The key is the legal vocabulary, and the key value is the digital representation of the word, used in feature engineering to represent the corresponding word. `legal_terms_vocabulary` is the legal terminology dictionary, with a length of 1,171. Partial key-values in the dictionary are shown in Figure 7.4:

![](images/txt007_4.jpg "pagenumber_ebook=190,pagenumber_book=181")

Figure 7.4 Legal Terminology Dictionary

Python's `Jieba` is a third-party library supporting Chinese word segmentation, performing word segmentation with the aid of a Chinese lexicon and Chinese word association probabilities. The `jieba.analyse.extract_tags()` function returns the segmented words and statistical term frequencies. The first parameter `content` specifies the text to be segmented, the second parameter `topK` specifies the number of high-frequency words to return, the third parameter `withWeight` specifies the weight of each word, i.e., term frequency, and the fourth parameter `allowPOS` specifies the allowed parts of speech to extract, with values such as `ns` (place name), `n` (noun), `vn` (verb-noun/gerund), `v` (verb), etc. As shown in Figure 7.5:

![](images/txt007_5.jpg "pagenumber_ebook=191,pagenumber_book=182")

Figure 7.5 Extracting Legal Terms from Legal Texts Using Jieba

