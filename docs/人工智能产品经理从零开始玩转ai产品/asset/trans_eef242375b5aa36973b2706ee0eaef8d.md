

## 5.6 Practical Process of AI Products

AI products go through multiple stages from planning to launch. Product planning requires determining the product scope, clarifying boundaries and expectations, and organizing data. Then, algorithm engineers design algorithms, and data engineers perform data preprocessing and labeling. Once data processing is complete and the algorithm is finalized, model training and tuning are conducted. Subsequently, the processed data is used for model validation. Upon successful validation, a final product evaluation is performed.

### 5.6.1 Data Labeling

The development of AI is closely related to the growth of data volume in the Internet era, but data in practical applications is full of noise. Precisely because data contains noise, data cleaning is often required before labeling to obtain data structures and effects that meet requirements, ensuring the algorithm can recognize them.

Taking image recognition as an example, to identify a cat in an image, traditional algorithms would store the cat's structure during recognition and then match it. However, if the cat is obscured and only a part is visible, or curls up and deforms, traditional algorithms fail to recognize it in these cases. But if we have many cat images, label them, and then use machine learning to summarize the cat's features, the machine can then recognize a cat when given a new image.

Generally, the ratio of training data to test data is 4:1 or 5:1. That is, if we have 10,000 cat images, we can use 8,000 for the training set and 2,000 for the test set. After training on the 8,000 images, the remaining 2,000 images are tested. If the recognition rate exceeds 95%, the model is considered relatively accurate.

Data labeling work generally involves two roles: labeling and review. Annotators are responsible for labeling data, while reviewers are responsible for reviewing the quality of labeled data. When annotators label data, they typically use methods such as tag labeling, bounding box labeling, segmentation labeling, and keypoint labeling.

When using tag labeling, a tag library is usually established. Annotators select one or more tags from the library to label the target data. As shown in Figure 5-16, tags such as gender, expression, ethnicity, glasses, age, and occlusion are used for labeling. For text tags, libraries for subject, predicate, object, attribute, adverbial, complement, noun, adjective, commendatory, derogatory, etc., can be established to determine text attributes. Tag labeling has a wide range of applications and is suitable for multiple scenarios such as text recognition, image recognition, speech recognition, video recognition, and semantic recognition.

![](`converter-master/example/165201/content_633715_0.jpg`)

Figure 5-16

Bounding box labeling is generally applicable to images. Annotators simply use boxes to label information in the training data. For example, in vehicle recognition, annotators box the vehicle images, as shown in Figure 5-17. Bounding box labeling is generally used in fields such as face recognition and object recognition.

![](`converter-master/example/165201/content_633715_1.jpg`)

Figure 5-17

Segmentation labeling is more precise than tag labeling and bounding box labeling. It requires covering information in the image with irregularly shaped blocks, as shown in Figure 5-18. This type of labeling is generally used in road recognition, where vehicles rely on it for road judgment during autonomous driving.

![](`converter-master/example/165201/content_633715_2.jpg`)

Figure 5-18

Keypoint labeling requires refinement down to specific anchor point features, such as facial keypoints and skeletal keypoints. Keypoint labeling is applicable to various types of image monitoring, such as face detection in digital cameras, face verification, medical image recognition, etc., as shown in Figure 5-19.

![](`converter-master/example/165201/content_633715_3.jpg`)

Figure 5-19

Since data labeling is repetitive work with low innovation requirements and tedious processes, considering costs, many companies hire interns or outsource to complete data labeling work. When outsourcing labeling work, the author suggests establishing **three unifications** before submitting data: a unified tag system, unified labeling rules, and unified data review, to ensure the scientific nature and executability of the data labeling process.

A **unified tag system** means setting up a complete, reasonable, and multi-level tag hierarchy, allowing users to label from multiple dimensions. **Unified labeling rules** are designed to prevent labeling errors caused by human factors during the labeling process. **Unified data review** ensures that the final data usability is determined by a single standard; otherwise, situations may arise where identical data is labeled differently due to different review rules.

Due to the large volume of data to be labeled, reviewers mostly use spot checking or cross-checking during the review process. Spot checks generally randomly inspect about 10% of the total data volume to save manpower. Cross-checking is generally performed mutually between different annotators, with the check volume typically being 50%–80% of the labeling volume.

### 5.6.2 Model Training and Tuning

There are numerous algorithms in machine learning and deep learning, with varying structures. Different types of algorithms involve different parameters, so the model training and tuning processes vary for different algorithms. Model training and tuning are generally completed independently by algorithm engineers; product managers or other personnel do not need to participate. However, product managers should make full preparations during the model training and tuning process.

During model training, algorithm engineers adjust the number of hidden layers, the number of neurons, and activation functions based on model characteristics, thereby optimizing the learning curve. Product managers should promptly follow up and check the algorithm training process and tuning effects. If the effect is not ideal, they can assist optimization by adjusting data. Data adjustment mainly includes data supplementation and data optimization.

Product managers should analyze whether the model's recognition effect on data meets expectations. If the effect is not ideal, the training data should be re-examined. If there is a large amount of similar data and the model performance is mediocre, product managers should observe data details and remove similar data from the labeled data. Additionally, the spot-check ratio of labeled data should be increased to over 50%, which will significantly improve the recognition effect.

In addition to reviewing already labeled data, supplementary data can be introduced to increase the training data volume, using a portion of test data for training. Swapping test data with training data can also optimize the recognition effect.

### 5.6.3 Model Validation

After model training is completed, the testing phase begins. During testing, test data is input into the model to observe the performance of the test data in that model. The determination of test effects is mainly based on key indicators determined during the product design phase (different products have different indicator designs), such as **Accuracy**, **Recall**, **Precision**, `AUC` curve, and `ROC` curve.

Assume an algorithm model is used to determine the diabetes situation in a group of people. Those with the disease are recorded as Positive, and those without are recorded as Negative. There are the following judgment results for the population: judging a diabetic person as diabetic; judging a diabetic person as non-diabetic; judging a non-diabetic person as non-diabetic; judging a non-diabetic person as diabetic.

Judging a diabetic patient as diabetic means judging correct data as correct data, recorded as `TP`; judging a diabetic patient as non-diabetic means judging correct data as incorrect data, recorded as `FN`; judging a non-diabetic patient as non-diabetic means judging incorrect data as incorrect data, recorded as `TN`; judging a non-diabetic patient as diabetic means judging incorrect data as correct data, recorded as `FP`, as shown in Figure 5-20.

![](`converter-master/example/165201/content_633715_4.jpg`)

Figure 5-20

If the total sample size is 1,000 people, with 200 having diabetes and 800 not. After model calculation, among the 200 diabetic people, 20 are classified as non-diabetic; among the 800 non-diabetic people, 10 are classified as diabetic. Then `TP=180`, `TN=790`, `FN=20`, `FP=10`.

**Accuracy** refers to the proportion of correct judgments, i.e., the ratio of correctly judged data to all samples: `Accuracy = (TP+TN) / (TP+TN+FN+FP) × 100%`.

Accuracy is a very important indicator. The higher the accuracy, the higher the model's accuracy in judging correct information. The accuracy in this case is `(180+790) / 1000 × 100% = 97%`. Accuracy indicates that the product is generally feasible.

**Recall** (also known as Sensitivity) mainly judges whether the model identifies all relevant cases, i.e., the ratio of correctly judged positive samples to all actual positive samples: `Recall = TP / (TP+FN) × 100%`.

In the above case, the recall is `180/200 × 100% = 90%`, meaning 90% of the sick people can be detected.

**Precision** is the proportion of true positive samples among all samples judged as positive, i.e., the ratio of correct positive samples to all samples judged as positive: `Precision = TP / (TP+FP) × 100%`.

The precision in this case is `180 / (180+10) × 100% ≈ 95%`. Precision represents the accuracy of all information judged as positive.

Different scenarios have different requirements for different indicators. The medical industry values Accuracy and Recall more, believing that the higher the probability of detecting a disease, the better the product effect. For e-commerce intelligent recommendation, Precision is more critical; the higher the proportion of meaningful recommended information to the user out of all recommended information, the more helpful it is. Of course, focusing on one indicator does not mean completely ignoring others; product managers should find a balance among different indicators.

During model testing, besides focusing on key indicators, attention must also be paid to error issues. Errors mainly fall into two types: **Training Error** and **Generalization Error**. Training error refers to the error value exhibited on the training data, while generalization error refers to the error value exhibited on a new test set. The difference between training error and generalization error is related not only to algorithm selection and parameter tuning but also potentially to the dataset. If a large discrepancy between training error and generalization error occurs and cannot be improved by algorithm selection and parameter tuning, the dataset must be adjusted. Methods for adjusting the dataset include the following.

1. **Cross-Validation Method**

The cross-validation method splits the dataset into multiple subsets of similar size and tries to make the distribution of each subset similar. Then, one subset is taken as the test set, and the rest as the training set. After multiple rounds of cross-training and validation, the final returned result is their average. The number of splits is crucial, generally called the `K` value; hence, it is also called `K`-Fold Cross-Validation. If `K` is 10, it is called 10-Fold Cross-Validation.

2. **Hold-out Method**

The hold-out method is easier to understand: the data is divided into two parts, one for training and one for testing. When using the hold-out method, it must be repeated multiple times; otherwise, a single use results in a large difference between training error and generalization error. Additionally, both training and test data require stratified sampling, which can reduce error.

3. **Bootstrapping Method**

If the data sample size is small, the bootstrapping method can be used to construct the training dataset through random sampling with replacement. Since bootstrapping changes the distribution of the training set, there will be estimation bias. Therefore, when the dataset has sufficient data, it is recommended to use cross-validation and hold-out methods.

### 5.6.4 Product Evaluation

After the algorithm model passes testing and validation, a comprehensive evaluation must be conducted based on product expectations to confirm whether the model meets launch requirements. Therefore, before the model goes live, product managers and test managers need to repeatedly verify the model effect and have technical personnel provide feedback and adjustments based on the verification. In the product evaluation phase, product managers should evaluate various indicators. Besides measuring whether the product meets basic indicator requirements through Accuracy, Recall, Precision, etc., they should also evaluate other indicators targeting product characteristics. For example, developing a smart speaker requires considering wake-up rate, false wake-up rate, and power indicators; developing a conversational robot requires considering speech synthesis technology, etc. Below are brief introductions to key points of several actual products.

1. **Speech Recognition (ASR)**

Speech recognition is a necessary function for devices requiring human-computer interaction, such as intelligent robots, smart speakers, and smart translation devices. Controlling products via voice must go through speech recognition (`ASR`), the process of converting sound into text. There are many `ASR` technical indicators, among which **Word Error Rate (WER)** needs attention. Controlling WER aims to make the recognized word sequence consistent with the standard word sequence, a process requiring language revision. For instance, in spoken communication, we often modify word order, e.g., "吃了饭了吗你？" (Ate rice already? You?).

In communication, people automatically revise modal particles and auxiliary words, and using inverted sentences does not affect understanding. But for machines, the recognized language needs revision. The revision process includes word substitution, deletion, and insertion, such as revising "吃了饭了吗你？" to "你吃饭了吗？" (Have you eaten?).

The total number of words substituted, deleted, or inserted by the machine, divided by the total number of words in the standard word sequence, is the Word Error Rate. Because of insertions, WER can exceed 100%. If too many words are inserted, the sentence meaning generally deviates. When determining WER specifically, scenarios and types need to be distinguished, such as different languages, numbers, genders, and regional accents. Besides WER, there is **Sentence Error Rate (SER)**, the ratio of correctly recognized sentences to total sentences. SER is derived based on WER, so SER is generally higher than WER; focusing on WER is more meaningful than focusing on SER.

When designing speech recognition products, there is a key function: when to wake up the device and start recognition. There are generally two common wake-up methods: **Contact-based Wake-up** and **Voice Wake-up**.

Contact-based wake-up generally belongs to close-range wake-up scenarios, mainly used for handheld devices or recognition devices in noisy environments, such as voice recognition Apps in mobile phones. When we use WeChat to send voice messages, we need to press and hold the screen button to wake up the voice recognition device.

If the device to be woken is far away and cannot be touched directly, requiring movement to reach it, voice wake-up is generally used, such as smart speakers in living rooms. If users have to touch to wake up every time, the user experience would be poor, and usability low. When using voice wake-up, the **Wake Word** is crucial. Wake words generally use words uncommon in daily life but easy to pronounce. English usually selects 3–5 phonemes, Chinese usually 3–4 characters. If less than 3 phonemes, the false wake-up rate increases; if more than 4 characters, the wake word is hard to remember. For example, Tmall Genie's wake word is "天猫精灵" (Tmall Genie), and Xiaomi AI Speaker's is "小爱同学" (Xiao Ai Classmate). If Tmall Genie's wake word were just "天猫" (Tmall), the false wake-up rate would increase significantly. The wake word activates the device, and subsequent speech after activation will be recognized.

Key indicators for the wake-up function include Wake-up Rate, False Wake-up Rate, Wake Word Length, and Wake-up Response Time.

2. **Speech Synthesis (TTS)**

Speech synthesis technology refers to the technology of converting text into voice signals for playback, mainly used for information delivery and human-computer interaction. For example, Microsoft Xiaoice's voice is completed through Speech Synthesis (`TTS`) technology. The quality of speech synthesis is mainly evaluated by the **Naturalness** of the synthesized speech, which is primarily judged subjectively.

The familiar `GAN` can analyze human voice characteristics and generate voices similar to the original. The judgment of such simulated voices can be evaluated through the similarity of voice characteristics.

3. **Recommendation System**

Due to the complex evaluation and verification structure of recommendation systems and many subjective judgment factors, evaluation requires a combination of quantitative evaluation and subjective evaluation. Recommendation system evaluation mainly includes User Satisfaction, Prediction Accuracy, Coverage, Diversity, Novelty, Serendipity, Real-time Performance, and Anti-Fraud Robustness.

Since the audience of the recommendation system is users, **User Satisfaction** is an important indicator for evaluating the system. Of course, user satisfaction cannot be calculated quantitatively, so it is generally analyzed through user behavior data or surveys. On e-commerce sites, if a user purchases a product through the recommendation system, it indicates satisfaction to some extent. The ability of the recommendation system to increase user purchase rate, and the repurchase rate, can measure user satisfaction.

**Prediction Accuracy** measures the recommendation system's ability to predict user behavior, mainly through rating prediction and `Top X` recommendation calculation of Accuracy and Recall. Accuracy refers to how many of the `X` items recommended by the system meet user needs, i.e., the proportion of recommendation quantity. Recall refers to the proportion of correct items that appear in the recommendation results.

**Coverage** refers to the proportion of items the recommendation system can recommend out of the total item set. Not all products have the opportunity to be recommended. A good recommendation system must not only have high user satisfaction but also high coverage.

Of course, user needs are related to their specific surrounding environment. Users do not only browse products recommended by algorithms. Therefore, to meet the diversity of user needs, a good recommendation system should be able to provide products from different interest areas, achieving recommendation diversity.

**Novelty** is also a focus of recommendation systems, i.e., recommending information completely unknown to the user before, fully arousing the user's curiosity. Users hope to obtain completely different information or enter new knowledge fields through the system; therefore, novelty is one of the important indicators for user retention.

**Serendipity** is a new hotspot in recommendation systems, which can improve user satisfaction. Serendipity differs from novelty; it focuses more on aspects different from historical interest points. If the recommended information differs from historical interests but still attracts the user's attention, serendipity is achieved.

For news, information, and other content with high real-time requirements, the recommendation system should fully consider the real-time nature of the content and be able to update recommendation data in real-time to meet users' new behavior changes.

**Anti-Fraud Robustness** is the most critical content for the sustainable operation of the recommendation system. If the recommendation system algorithm is not robust enough, it is prone to behavior injection attacks. Information producers will transform according to recommendation rules to increase the number of times or order of being recommended. Therefore, in actual R&D, the recommendation system should choose algorithms with high robustness and try to use high-cost user behaviors. For example, when having both user purchase behavior data and browsing behavior data, purchase behavior data should be preferred for reference recommendation because purchase behavior has a high cost and is not easily faked.

`EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==`

