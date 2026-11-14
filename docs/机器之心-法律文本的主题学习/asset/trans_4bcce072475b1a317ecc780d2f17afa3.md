

## 7.1 Legal Text Feature Extraction

### 7.1.1 Chinese Legal Texts

The `doc_word_cut` function is defined to extract legal terms from legal texts. This function is called for each legal text in the corpus to obtain word frequency statistics of legal terms and save them. The `open()` function creates a file object that can access disk files. The first parameter specifies the filename to access; the second parameter `mode` specifies the access mode: `w` for write mode, `r` for read mode; the third parameter `encoding` specifies the encoding format. As shown in Figure 7.6:

![](images/txt007_6.jpg "pagenumber_ebook=191,pagenumber_book=182")

Figure 7.6　Word segmentation and saving for the corpus

Word frequency statistics are performed on the corpus based on legal terms, as shown in Figure 7.7. In the first legal text, "Company" (公司) appears both in the legal term dictionary and in the legal text, with a frequency of 208. Under the same conditions, "Loss" (损失) appears 47 times, "Contract" (合同) 35 times, "Compensation" (赔偿) 25 times, and "Breach of contract" (违约) and "Liquidated damages" (违约金) total 22 times. It can thus be inferred that the subject of this legal text is likely a contract breach compensation case related to a company.

![](images/txt007_7.jpg "pagenumber_ebook=192,pagenumber_book=183")

Figure 7.7　Obtaining legal term frequency statistics

### 7.1.2 English Legal Texts

The Australasian Legal Information Institute (AustLII) provides public access to Federal Court of Australia (FCA) judgment data. To effectively, quickly, and systematically retrieve corresponding legal practices, the legal corpus is divided into 9 National Practice Areas (NPAs), including Commercial and Corporations, Employment and Industrial Relations, Administrative and Constitutional Law and Human Rights, Admiralty, Native Title, Criminal, Intellectual Property, Taxation, and Other. Based on legal practice areas and legal query points, an English legal terminology vocabulary is established, named the text file `FCA_legal_terms`, containing 1,259 legal words such as `bankrupt`, `Act`, `evidence`, `statement`, `assessment`, `costs`, `applicants`, `taxation`, etc., with each word occupying one line, as shown in Figure 7.8:

![](images/txt007_8.jpg "pagenumber_ebook=192,pagenumber_book=183")

Figure 7.8　English Legal Terminology Vocabulary

Use Python's `codecs.open()` function to open the English legal terminology vocabulary file. Read the file line by line and add legal terms to an array, as shown in Figure 7.9:

![](images/txt007_9.jpg "pagenumber_ebook=193,pagenumber_book=184")

Figure 7.9　Opening the English legal terminology vocabulary file

Add the English legal terminology array elements to the dictionary `legal_terms_vocabulary`, as shown in Figure 7.10. In the English legal terminology dictionary, the value for the key "bankrupt" is 0, and the value for the key "Act" is 1.

![](images/txt007_10.jpg "pagenumber_ebook=193,pagenumber_book=184")

Figure 7.10　Key-value pairs in the English legal dictionary

Use the `len` function to get the length of the English legal terminology dictionary `legal_terms_vocabulary`; the return value is 1,259. Some key-value pairs in the dictionary are shown in Figure 7.11.

![](images/txt007_11.jpg "pagenumber_ebook=193,pagenumber_book=184")

Figure 7.11　English Legal Terminology Dictionary

Define the `doc_word_cut` function, with the formal parameter being the legal text filename. Read the text content, filter newline characters, and use the `jieba.analyse.extract_tags` method to extract English legal terms and their frequencies from the text. As shown in Figure 7.12:

![](images/txt007_12.jpg "pagenumber_ebook=194,pagenumber_book=185")

Figure 7.12　Extracting legal terms from English legal texts

Create a file object via the `open()` function, then loop through calling the `doc_word_cut` function to process the FCA corpus. The return result of each call is saved as a line in the file. As shown in Figure 7.13:

![](images/txt007_13.jpg "pagenumber_ebook=194,pagenumber_book=185")

Figure 7.13　Word segmentation and saving for the FCA corpus

The extracted legal terms and their frequencies from the FCA corpus are shown in Figure 7.14. In the first legal text, "benefit" appears 32 times, "evidence" 24 times, "application" 18 times, and "bankrupt" and "bankruptcy" total 50 times. It can thus be inferred that the subject of this legal text is likely related to corporate bankruptcy proceedings and appeals.

![](images/txt007_14.jpg "pagenumber_ebook=195,pagenumber_book=186")

Figure 7.14　FCA Legal Term Frequency Statistics

### 7.1.3 Chinese Text Vector Space Model Based on Legal Terms

For legal text processing, feature spaces based on legal terms and feature spaces based on general words are established separately. The aforementioned file for the Chinese legal text corpus CJO, after word frequency statistics based on legal terms, is `CJO_based_legalterms_feature_representation.txt` (see Figure 7.13). A vector space model is established with the aid of Python's third-party libraries: regular expression `re` and sparse matrix `scipy.sparse`. As shown in Figure 7.15:

![](images/txt007_15.jpg "pagenumber_ebook=195,pagenumber_book=186")

Figure 7.15　Importing re and scipy.sparse

The vector space model is generally a sparse matrix. The function `csr_matrix()` stores the original matrix through three arrays. The first parameter `data` contains all non-zero values in the sparse matrix; the second parameter `indices` contains the column indices of all non-zero values; the third parameter `indptr` contains the index in `data` where the non-zero values of each row start. For example, the non-zero values of the first row are `data[indptr[0]:indptr[1]]`, and their column indices are `indices[0:(indptr[1]-indptr[0])]`. Similarly, the non-zero values of the second row are `data[indptr[1]:indptr[2]]`, and their column indices are `indices[(indptr[1]-indptr[0]):(indptr[2]-indptr[1])]`. As shown in Figure 7.16:

![](images/txt007_16.jpg "pagenumber_ebook=196,pagenumber_book=187")

Figure 7.16　CJO Feature Vector Space Based on Legal Terms

`vocabulary` is dictionary-type data. `setdefault` assigns an index `index` to the read legal term, i.e., the order in which the legal term enters the dictionary. In the feature space, the index represents the legal term. If the legal term already exists in the dictionary, no index is added; only the existing index of the legal term is returned. `vsmtf` is the vector space model, with a feature count of 1,154. Through the dictionary and a loop, the feature space can be converted into a legal term text representation. As shown in Figure 7.17:

![](images/txt007_17.jpg "pagenumber_ebook=196,pagenumber_book=187")

Figure 7.17　Converting CJO Legal Term Feature Space to Text

### 7.1.4 English Text Vector Space Model Based on Legal Terms

The aforementioned file for the English legal text corpus FCA, after word frequency statistics based on legal terms, is `FCA_based_legalterms_feature_representation.txt` (see Figure 7.13). Establish the FCA legal term vector space model, as shown in Figure 7.18:

![](images/txt007_18.jpg "pagenumber_ebook=197,pagenumber_book=188")

Figure 7.18　FCA Feature Vector Space Based on Legal Terms

The FCA legal term vector space has a feature count of 1,086. `vocabulary` is a dictionary containing 1,086 tuples of legal terms and indices. `vocabulary.items()` returns all tuples; `items[0]` is the key name, and `items[1]` is the key value. Convert the feature vector space into an English legal term text representation. As shown in Figure 7.19:

![](images/txt007_19.jpg "pagenumber_ebook=197,pagenumber_book=188")

Figure 7.19　Converting FCA Legal Term Feature Space to Text

## 7.2 Feature Representation Based on General Words

### 7.2.1 Chinese Legal Texts

Python's `jieba.analyse.set_stop_words()` function sets stop words. Legal terms can be set as stop words so that when extracting feature words from legal texts, legal terms are excluded, and the extraction results contain only general word features. As shown in Figure 7.20:

![](images/txt007_20.jpg "pagenumber_ebook=198,pagenumber_book=189")

Figure 7.20　Setting CJO Legal Terms as Stop Words

Loop through calling the `doc_word_cut()` function to read legal texts in the corpus. After excluding legal terms, store the read general word frequencies in the disk file pointed to by `fp`. As shown in Figures 7.21 and 7.22:

![](images/txt007_21.jpg "pagenumber_ebook=198,pagenumber_book=189")

Figure 7.21　Extracting General Words from CJO Corpus

![](images/txt007_22.jpg "pagenumber_ebook=199,pagenumber_book=190")

Figure 7.22　Converting CJO General Word Feature Vectors to Text

### 7.2.2 English Legal Texts

With the aid of the `jieba.analyse.set_stop_words()` function, set English legal terms as stop words, and extract features from the FCA corpus. The results contain only general word features. As shown in Figure 7.23:

![](images/txt007_23.jpg "pagenumber_ebook=199,pagenumber_book=190")

Figure 7.23　Setting FCA Legal Terms as Stop Words

The `doc_word_cut()` function performs word segmentation on the input legal text. Loop through reading each legal text in the FCA corpus. After setting FCA legal terms as stop words, the extracted feature words are general words, which are stored in the disk file pointed to by `fp`. As shown in Figures 7.24 and 7.25:

![](images/txt007_24.jpg "pagenumber_ebook=200,pagenumber_book=191")

Figure 7.24　Extracting General Words from FCA Corpus

![](images/txt007_25.jpg "pagenumber_ebook=200,pagenumber_book=191")

Figure 7.25　Converting FCA General Word Feature Vectors to Text

### 7.2.3 Chinese Text Vector Space Model Based on General Words

The aforementioned file for the Chinese legal text corpus CJO, after word frequency statistics based on general words, is `CJO_based_commonwords_feature_representation.txt` (see Figure 7.24). The function `readlines()` reads all lines; each line represents the general word frequency statistics for one legal text. The delimiter between a word and its frequency is a colon; `wordfreq` is a word-frequency item. The delimiter between two word-frequency items is a tab character. The function `re.findall("(\\w+):", wordfreq)` extracts the word from the word-frequency item, and `re.findall(":(\\d+)", wordfreq)` extracts the frequency. As shown in Figure 7.26:

![](images/txt007_26.jpg "pagenumber_ebook=201,pagenumber_book=192")

Figure 7.26　CJO Feature Vector Space Based on General Words

The feature count of the Chinese legal text general word vector space model is 41,623. It can be seen that the dimension of the general word feature space far exceeds that of the legal term feature space. Through the `vocabulary` dictionary and a for loop, the feature space can be converted into a general word text representation. As shown in Figure 7.27:

![](images/txt007_27.jpg "pagenumber_ebook=201,pagenumber_book=192")

Figure 7.27　Converting CJO General Word Feature Vector Space to Text

### 7.2.4 English Text Vector Space Model Based on General Words

The aforementioned file for the English legal text corpus FCA, after word frequency statistics based on general words, is `FCA_based_commonwords_feature_representation.txt` (see Figure 7.24). Establish the FCA general word vector space model, as shown in Figure 7.28:

![](images/txt007_28.jpg "pagenumber_ebook=202,pagenumber_book=193")

Figure 7.28　FCA Feature Vector Space Based on General Words

The FCA general word vector space has a feature count of 43,663. `vocabulary` is a dictionary containing 43,663 tuples of general words and indices. Convert the feature vector space into an English general word text representation. As shown in Figure 7.29:

![](images/txt007_29.jpg "pagenumber_ebook=202,pagenumber_book=193")

Figure 7.29　Converting FCA General Word Feature Space to Text

## 7.3 LDA Topic Model

### 7.3.1 Probabilistic Generation of Legal Texts

The multivariate parameters of the multinomial distribution conform to the form of the Dirichlet distribution, and the posterior and prior distributions have the same distribution form, i.e., conjugate distributions. When generating a word in a legal text, a topic of a certain aspect is first selected with a certain probability, and then a word is selected from that topic with a certain probability. The continuous repetition of the above process generates a legal text.

Based on the Bayesian probability assumptions from text to topic and topic to word, a three-layer topic model generates a legal text. The generation probability of each word statistically follows a parameter distribution. According to the conjugate posterior distribution, parameter estimation is adjusted and iterated step by step. A latent topic distribution obeying the multinomial distribution is generated based on all words in a legal text. The topic model is a bag-of-words model, meaning texts with the same words but different word orders have the same topic distribution. Legal terms carry more information than general words. If these words are not specially treated and are only extracted by statistics and probability, the topic distribution will ignore the legal semantic background. Legal terms should be given a higher weight and status in legal texts.

### 7.3.2 LDA Principles

LDA is a probabilistic model that generates words by repeatedly sampling from latent topics. Its generative process is described in Figure 7.30. In the figure, `w_m,i` is the word generated at the i-th word position in the m-th document; `α` is the Dirichlet hyperparameter for the document-to-topic distribution; the total number of documents is `M`; `θ_m` is the topic multinomial distribution parameter for the independent experiment of the m-th document. The latent topic number at the i-th word position of the m-th document is `z_m,i`. The number of topics is `K`; `φ_k` is the parameter for the independent repeated experiment of the multinomial distribution where the latent topic at the i-th word position is k; the Dirichlet hyperparameter for topic-to-word is `β`. The arrows shown in the figure represent conditional probabilities obeying certain probability distributions. Latent variables include `θ_m`, `z_m,i`, and `φ_k`. Box `M` indicates M independent repeated experiments, each producing a legal document. Box `N_m` indicates that the m-th document requires `N_m` independent repeated experiments, each producing a word.

![](images/txt007_30.jpg "pagenumber_ebook=203,pagenumber_book=194")

Figure 7.30　LDA Model

Use ![](images/txt007_31.jpg "pagenumber_ebook=203,pagenumber_book=194") to represent the topic distribution of the m-th document on topic numbers 1 to K. The topic distributions of all documents, from the 1st to the M-th, constitute the ![](images/txt007_32.jpg "pagenumber_ebook=203,pagenumber_book=194") matrix, of size M×K. Use ![](images/txt007_33.jpg "pagenumber_ebook=203,pagenumber_book=194") to represent the word distribution of topic number k on various words. The word distributions of all topics, including those numbered 1 to K, constitute the ![](images/txt007_34.jpg "pagenumber_ebook=203,pagenumber_book=194") matrix, of size K×V, where V is the vocabulary size. ![](images/txt007_35.jpg "pagenumber_ebook=204,pagenumber_book=195") and ![](images/txt007_36.jpg "pagenumber_ebook=204,pagenumber_book=195") respectively follow Dirichlet distributions with parameters ![](images/txt007_37.jpg "pagenumber_ebook=204,pagenumber_book=195") and ![](images/txt007_38.jpg "pagenumber_ebook=204,pagenumber_book=195").

The LDA topic model connects documents and words through latent topics. According to the Bayesian posterior probability, the probability of generating all words in the entire document can be calculated through the marginal probability density and Bayes' formula:

![](images/txt007_39.jpg "pagenumber_ebook=204,pagenumber_book=195")

The above formula involves the Bayesian law of total probability, making parameter estimation difficult. Gibbs sampling is used to approximately draw a sequence of samples from a multivariate probability distribution when direct sampling is difficult, in order to solve the target. In the K-dimensional topics, each dimension takes turns using the full conditional probability, iterating cyclically until convergence:

![](images/txt007_40.jpg "pagenumber_ebook=204,pagenumber_book=195")

The expression ¬i refers to the count after removing the word at the i-th word position in the m-th document. ![](images/txt007_41.jpg "pagenumber_ebook=204,pagenumber_book=195") refers to the count of word t at the i-th word position under topic number k. ![](images/txt007_42.jpg "pagenumber_ebook=204,pagenumber_book=195") is the statistical count after removing word wi, i.e., ![](images/txt007_43.jpg "pagenumber_ebook=204,pagenumber_book=195") minus 1. The Dirichlet distribution hyperparameter from topic to word is denoted as β. Although each word originally corresponds to a different component, the same value is used for simplification. ![](images/txt007_44.jpg "pagenumber_ebook=204,pagenumber_book=195") represents the count of all words belonging to topic k after removing the word wi at the i-th word position. Obviously, the probability of the word wi at the i-th word position under topic number k is the front part of formula (7.2), i.e., ![](images/txt007_45.jpg "pagenumber_ebook=204,pagenumber_book=195")

![](images/txt007_46.jpg "pagenumber_ebook=204,pagenumber_book=195")

