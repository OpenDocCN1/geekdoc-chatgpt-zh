

### 7.3.3 Text Similarity

The latter part of Equation (7.2), ![](images/txt007_47.jpg "pagenumber_ebook=204,pagenumber_book=195"), represents the word count for topic number `k` in the `m`-th text. ![](images/txt007_48.jpg "pagenumber_ebook=204,pagenumber_book=195") denotes the statistics after removing the word `wi`, i.e., ![](images/txt007_49.jpg "pagenumber_ebook=204,pagenumber_book=195") minus 1. The Dirichlet distribution hyperparameter from text to topic is denoted as `α`, and the same value is adopted for simplification. ![](images/txt007_50.jpg "pagenumber_ebook=204,pagenumber_book=195") represents the word count of the `m`-th text after removing the word `wi` at the `i`-th position. Obviously, the probability of topic number `k` under the `m`-th text is the latter part of Equation (7.2), i.e., ![](images/txt007_51.jpg "pagenumber_ebook=204,pagenumber_book=195")

![](images/txt007_52.jpg "pagenumber_ebook=204,pagenumber_book=195")

Following Equations (7.3) and (7.4) for Gibbs sampling, the latent topic label for the word `wi` at the `i`-th position of the `m`-th text is denoted as `zm,i`. Probability parameter estimation is performed based on the posterior distribution and pseudo-counts:

![](images/txt007_53.jpg "pagenumber_ebook=205,pagenumber_book=196")

![](images/txt007_54.jpg "pagenumber_ebook=205,pagenumber_book=196") is the word count under topic number `k` in the `m`-th text. The total number of words in the `m`-th text is denoted as `Nm`. According to Equation (7.5), the text-to-topic distribution matrix ![](images/txt007_55.jpg "pagenumber_ebook=205,pagenumber_book=196") can be generated.

![](images/txt007_56.jpg "pagenumber_ebook=205,pagenumber_book=196")

![](images/txt007_57.jpg "pagenumber_ebook=205,pagenumber_book=196") is the count of word `w` under topic number `k`. The total number of words under topic number `k` is denoted as `Nk`. According to Equation (7.6), the topic-to-word distribution matrix ![](images/txt007_58.jpg "pagenumber_ebook=205,pagenumber_book=196") can be generated.

To measure the similarity between two texts in the Vector Space Model, Euclidean distance, Manhattan distance (absolute value distance), and cosine similarity can be used. Based on the difference in topic distributions between two texts, the cosine similarity between two legal texts can be calculated:

![](images/txt007_59.jpg "pagenumber_ebook=205,pagenumber_book=196")

`docsim(di, dj)` is the cosine similarity between legal text `di` and text `dj`. Assuming the 0-th text is the new text to be matched, the cosine similarity with each of the remaining legal texts is calculated, and the legal text `dmax` with the maximum cosine similarity value is returned:

![](images/txt007_60.jpg "pagenumber_ebook=205,pagenumber_book=196")

The legal text with the maximum cosine similarity value is the most similar text. In the `θ` matrix, the `dmax`-th row is found to obtain the topic numbers in the topic distribution of that legal text.

## 7.4 LDA Topic Analysis of Chinese Legal Texts

### 7.4.1 Chinese Legal Terminology LDA Model

The corpus data comes from China Judgments Online (CJO), referred to as the `CJO` dataset. A total of 1,103 court judgments were selected. Word segmentation was performed using the `Python` Natural Language Toolkit (NLTK), yielding 42,777 words. There are 1,154 specified legal terms (see Appendix A), and the remaining 41,623 words are general words.

The legal terminology feature vector matrix of the `CJO` corpus is `vsmtf_legalterms_CJO`, with 1,103 rows and 1,154 columns. The `LDA` module is imported from `slda.topic_models`, and the `LDA` parameters are initialized. `K` is the number of topics. China's main legal fields generally number around 10, with sub-fields numbering in the dozens. Based on experience, `K` is set to 15. `alpha` is the parameter representing a legal text as a topic multinomial distribution, set to 1. `beta` is the parameter representing a topic as a word multinomial distribution, set to 0.01. `V` is the vocabulary size, i.e., the number of columns in the legal terminology feature vector matrix. As shown in Figure 7.31:

![](images/txt007_61.jpg "pagenumber_ebook=206,pagenumber_book=197")

Figure 7.31　Initializing CJO Legal Terminology LDA Model

The number of iterations `n_iter` is 100. The `lda.fit` training function uses Gibbs sampling and the pseudo-count method to assign latent topics to the generation of each word in the `vsmtf_legalterms_CJO` legal terminology feature vector space. On a system with a 2.8 GHz CPU and 8 GB memory, the training time overhead is approximately 5 seconds. As shown in Figure 7.32:

![](images/txt007_62.jpg "pagenumber_ebook=206,pagenumber_book=197")

Figure 7.32　CJO Legal Terminology LDA Model Training Process

After `lda.fit(vsmtf_legalterms_CJO)` training is complete, the returned `lda.theta` matrix represents the distribution of each legal text over 15 topics, with the number of rows equal to the number of legal texts and the number of columns equal to the number of topics (15). `lda.theta[0]` is the topic distribution of the first legal text; the cumulative values across topics sum to 1. The values in the topic distribution can be understood as the proportion of each topic in that legal text. The topic with the largest proportion is topic number 5, which is actually the 6th topic because topic numbering starts from 0. As shown in Figure 7.33:

![](images/txt007_63.jpg "pagenumber_ebook=207,pagenumber_book=198")

Figure 7.33　Text-to-Topic Distribution of CJO Legal Terminology LDA Model

After `lda.fit` completes training, another return value, the `lda.phi` matrix, represents the distribution of each topic over the 1,154 words, with the number of rows equal to the number of topics and the number of columns equal to the number of legal terms (1,154). `lda.phi[0]` is the word distribution on the first topic; the cumulative values across words sum to 1. The values in the word distribution can be understood as the proportion of each word in that topic. The word with the largest proportion is legal term number 321; by traversing the dictionary, this legal term is "defendant" (被告人). As shown in Figure 7.34:

![](images/txt007_64.jpg "pagenumber_ebook=207,pagenumber_book=198")

Figure 7.34　Topic-to-Word Distribution of CJO Legal Terminology LDA Model

By looping through each topic-to-word distribution, i.e., each row of the `lda.phi` matrix, sorting by value in descending order, the top 20 words for each topic are found. As shown in Figure 7.35:

![](images/txt007_65.jpg "pagenumber_ebook=208,pagenumber_book=199")

Figure 7.35　Top 20 Words for Topics 0–4 of CJO Legal Terminology LDA Model

It can be seen that topic 0 is roughly related to public prosecution cases and criminal law; topic 1 is roughly related to lease agreements and ownership; topic 5 is roughly related to companies and agency contracts; topic 6 is roughly related to insurance and traffic liability; topic 7 is roughly related to international trade and agency; topic 8 is roughly related to contracts and breach of contract; etc.

![](images/txt007_66.jpg "pagenumber_ebook=208,pagenumber_book=199")

Figure 7.36　Top 20 Words for Topics 5–14 of CJO Legal Terminology LDA Model

Taking the first legal text as a reference, the original feature vector dimension is 1,154. By representing it as a topic distribution, i.e., `lda.theta[0]`, the dimension is reduced to 15. In addition, by sorting, the content of the first legal text regarding topic numbers 5, 8, and 7, etc., can be output. As shown in Figure 7.37:

![](images/txt007_67.jpg "pagenumber_ebook=209,pagenumber_book=200")

Figure 7.37　Topic Representation of Legal Texts in CJO Legal Terminology LDA Model

Based on the topic representation of legal texts, text similarity is no longer calculated in the original feature space but uses topic representation to calculate cosine similarity. Taking the first legal text as a reference, the cosine similarity with the 1st to 1,103rd legal texts is calculated respectively, yielding 1,103 values. Sorted in descending order, the indices of the top 10 values are selected. These indices are the row numbers of the Vector Space Model matrix and also the legal text numbers. As shown in Figure 7.38, it can be seen that index 0 refers to the first legal text being most similar to itself, so the first returned index needs to be excluded. Therefore, the legal text most similar to the first legal text is number 936, i.e., the 937th text.

![](images/txt007_68.jpg "pagenumber_ebook=209,pagenumber_book=200")

Figure 7.38　Cosine Similarity Calculation of CJO Legal Terminology LDA Model

### 7.4.2 Chinese General Words LDA Model

The general words feature vector matrix of the `CJO` corpus is `vsmtf_commonwords_CJO`, with 1,103 rows and 41,623 columns. As shown in Figure 7.39:

![](images/txt007_69.jpg "pagenumber_ebook=210,pagenumber_book=201")

Figure 7.39　CJO General Words Feature Vector Matrix

After importing the `LDA` module, the `LDA` parameters are prepared for initialization. Based on experience, `K` is set to 100, `alpha` to 1, `beta` to 0.01, `V` to 41,623, and the number of iterations `n_iter` to 100. The `lda.fit` training time overhead is approximately 1 minute 29 seconds, as shown in Figure 7.40:

![](images/txt007_70.jpg "pagenumber_ebook=210,pagenumber_book=201")

Figure 7.40　CJO General Words LDA Model Training Process

After `lda.fit(vsmtf_commonwords_CJO)` training is complete, the returned `lda.theta` matrix represents the distribution of each legal text over 100 topics, with 1,103 rows (number of legal texts) and 100 columns (number of topics). `lda.theta[0]` is the distribution of the first legal text over the 100 topics; the topic with the largest proportion is topic number 9. As shown in Figure 7.41:

![](images/txt007_71.jpg "pagenumber_ebook=210,pagenumber_book=201")

Figure 7.41　Text-to-Topic Distribution of CJO General Words LDA Model

The `lda.phi` matrix represents the distribution of each topic over the 41,623 words, with 100 rows (number of topics) and 41,623 columns (number of general words). `lda.phi[0]` is the word distribution on the first topic; the word with the largest proportion is general word number 2,257. By traversing the dictionary, this word is "property investment" (置业). As shown in Figure 7.42:

![](images/txt007_72.jpg "pagenumber_ebook=211,pagenumber_book=202")

Figure 7.42　Topic-to-Word Distribution of CJO General Words LDA Model

By looping through each row of the `lda.phi` matrix, sorting by value in descending order, the top 20 words for each topic are found. As shown in Figure 7.43:

![](images/txt007_73.jpg "pagenumber_ebook=211,pagenumber_book=202")

Figure 7.43　Top 20 Words for Topics 0–2 of CJO General Words LDA Model

Taking the first legal text as a reference, the original feature vector dimension is 41,623. By representing it as a topic distribution, i.e., `lda.theta[0]`, the dimension is reduced to 100. By sorting in descending order, the ranking of the first legal text's topic proportions is output, with the top three topic numbers being 9, 13, and 71. As shown in Figure 7.44:

![](images/txt007_74.jpg "pagenumber_ebook=212,pagenumber_book=203")

Figure 7.44　Topic Representation of Legal Texts in CJO General Words LDA Model

Taking the first legal text as a reference, the cosine similarity with the 1st to 1,103rd legal texts is calculated respectively, yielding 1,103 values. Sorted in descending order, the indices of the top 10 values with the highest cosine similarity are selected. As shown in Figure 7.45, excluding itself, the text most similar to the first legal text is number 631.

![](images/txt007_75.jpg "pagenumber_ebook=212,pagenumber_book=203")

Figure 7.45　Cosine Similarity Calculation of Legal Terminology LDA Model

## 7.5 LDA Topic Analysis of English Legal Texts

### 7.5.1 English Legal Terminology LDA Model

The corpus data comes from the Federal Court of Australia (FCA), referred to as the `FCA` dataset. A total of 3,886 court judgments were selected. Word segmentation was performed using the `Python` Natural Language Toolkit, yielding 44,749 words. There are 1,086 specified legal terms (see Appendix A), and the remaining 43,663 words are general words.

The legal terminology feature vector matrix of the `FCA` corpus is `vsmtf_legalterms_FCA`, with 3,886 rows and 1,086 columns. As shown in Figure 7.46:

![](images/txt007_76.jpg "pagenumber_ebook=213,pagenumber_book=204")

Figure 7.46　Initializing CJO Legal Terminology LDA Model

After importing the `LDA` module, the `LDA` parameters are prepared for initialization. Based on experience with Australian equity law and statute law, `K` is set to 10, `alpha` to 1, `beta` to 0.01, `V` to 1,086, and the number of iterations `n_iter` to 100. The `lda.fit` training time overhead is approximately 33 seconds, as shown in Figure 7.47:

![](images/txt007_77.jpg "pagenumber_ebook=213,pagenumber_book=204")

Figure 7.47　FCA Legal Terminology LDA Model Training Process

After `lda.fit(vsmtf_legalterms_FCA)` training is complete, the returned `lda.theta` matrix represents the distribution of each legal text over 10 topics, with 3,886 rows (number of legal texts) and 10 columns (number of topics). `lda.theta[0]` is the topic distribution of the first legal text; the topic with the largest proportion is topic number 2. As shown in Figure 7.48:

![](images/txt007_78.jpg "pagenumber_ebook=213,pagenumber_book=204")

Figure 7.48　Text-to-Topic Distribution of FCA Legal Terminology LDA Model

After `lda.fit` completes training, another return value, the `lda.phi` matrix, represents the distribution of each topic over the words, with 10 rows (number of topics) and 1,086 columns (number of legal terms). `lda.phi[0]` is the word distribution on the first topic; the word with the largest proportion is legal term number 0. By traversing the dictionary, this legal term is "sentence". As shown in Figure 7.49:

![](images/txt007_79.jpg "pagenumber_ebook=214,pagenumber_book=205")

Figure 7.49　Topic-to-Word Distribution of FCA Legal Terminology LDA Model

By looping through each topic-to-word distribution, i.e., each row of the `lda.phi` matrix, sorting by value in descending order, the top 20 words for each topic are found. As shown in Figures 7.50 and 7.51:

![](images/txt007_80.jpg "pagenumber_ebook=214,pagenumber_book=205")

Figure 7.50　Top 20 Words for Topics 0–3 of FCA Legal Terminology LDA Model

![](images/txt007_81.jpg "pagenumber_ebook=215,pagenumber_book=206")

Figure 7.51　Top 20 Words for Topics 4–9 of FCA Legal Terminology LDA Model

Taking the first legal text as a reference, the original feature vector dimension is 1,086. By representing it as a topic distribution, i.e., `lda.theta[0]`, the dimension is reduced to 10. By sorting in descending order, the ranking of the first legal text's topic proportions is output, with the top three topic numbers being 2, 7, and 8. As shown in Figure 7.52:

![](images/txt007_82.jpg "pagenumber_ebook=215,pagenumber_book=206")

Figure 7.52　Topic Representation of Legal Texts in FCA Legal Terminology LDA Model

Taking the first legal text as a reference, the cosine similarity with the 1st to 3,886th legal texts is calculated respectively, yielding 3,886 values. Sorted in descending order, the indices of the top 10 values with the highest cosine similarity are selected. As shown in Figure 7.53, excluding itself, the text most similar to the first legal text is number 1,917.

![](images/txt007_83.jpg "pagenumber_ebook=215,pagenumber_book=206")

Figure 7.53　Cosine Similarity Calculation of FCA Legal Terminology LDA Model

### 7.5.2 English General Words LDA Model

The general words feature vector matrix of the `FCA` corpus is `vsmtf_commonwords_FCA`, with 3,886 rows and 43,663 columns. As shown in Figure 7.54:

![](images/txt007_84.jpg "pagenumber_ebook=216,pagenumber_book=207")

Figure 7.54　FCA General Words Feature Vector Matrix

After importing the `LDA` module, the `LDA` parameters are prepared for initialization. Based on experience, `K` is set to 250, `alpha` to 1, `beta` to 0.01, `V` to 43,663, and the number of iterations `n_iter` to 100. The `lda.fit` training time overhead is approximately 26 minutes 58 seconds, as shown in Figure 7.55.

![](images/txt007_85.jpg "pagenumber_ebook=216,pagenumber_book=207")

Figure 7.55　FCA General Words LDA Model Training Process

After `lda.fit(vsmtf_commonwords_FCA)` training is complete, the returned `lda.theta` matrix represents the distribution of each legal text over 250 topics, with 3,886 rows (number of legal texts) and 250 columns (number of topics). `lda.theta[0]` is the distribution of the first legal text over the 250 topics; the topic with the largest proportion is topic number 235. As shown in Figure 7.56:

![](images/txt007_86.jpg "pagenumber_ebook=217,pagenumber_book=208")

Figure 7.56　Text-to-Topic Distribution of FCA General Words LDA Model

The `lda.phi` matrix represents the distribution of each topic over the 43,663 words, with 250 rows (number of topics) and 43,663 columns (number of general words). `lda.phi[0]` is the word distribution on the first topic; the word with the largest proportion is general word number 0. By traversing the dictionary, this word is "id". As shown in Figure 7.57:

![](images/txt007_87.jpg "pagenumber_ebook=217,pagenumber_book=208")

Figure 7.57　Topic-to-Word Distribution of CJO General Words LDA Model

By looping through each row of the `lda.phi` matrix, sorting by value in descending order, the top 20 words for each topic are found. As shown in Figure 7.58:

