

## 5.6 Practical Process of AI Products

AI products go through multiple stages from planning to birth. Product planning requires determining the product scope, clarifying boundaries and expectations, and organizing data. Then, algorithm engineers design algorithms, and data engineers perform data preprocessing and annotation. After data processing is complete and the algorithm is finalized, model training and tuning are conducted. Subsequently, the processed data is used for model validation. Once validation passes, the final product evaluation takes place.

### 5.6.1 Data Annotation

The development of AI is closely related to the growth of data volume in the Internet era, but data in practical applications is full of noise. Precisely because data contains noise, data cleaning is often required before annotation. Cleaning yields data structures and effects that meet requirements, ensuring the algorithm can recognize them.

Taking image recognition as an example, to identify a cat in an image, traditional algorithms would store the cat's structure during recognition and then match it. However, if the cat is obscured and only a part is visible, or curls up and deforms, traditional algorithms fail to recognize it in these cases. But if we have many images of cats and annotate them, then summarize the cat's features through machine learning, the machine can recognize a cat when given a new image.

Generally, the ratio of training data to test data is 4:1 or 5:1. That is, when we have 10,000 cat images, we can use 8,000 for the training set and 2,000 for the test set. After training on 8,000 images, the remaining 2,000 are tested. If the recognition rate exceeds 95%, the model is considered relatively accurate.

Data annotation work generally involves two roles: annotators and reviewers. Annotators are responsible for annotating data, while reviewers audit the quality of annotated data. When annotating, annotators typically use methods such as tag annotation, bounding box annotation, segmentation (range) annotation, and keypoint annotation.

When using tag annotation, a tag library is usually established. Annotators select one or more tags from the library to annotate target data. As shown in Figure 5-16, tags such as gender, expression, race, glasses, age, and occlusion are used for annotation. For text tags, libraries for subject, predicate, object, attribute, adverbial, complement, noun, adjective, commendatory, derogatory, etc., can be established to determine text attributes. Tag annotation has a wide range of applications, suitable for scenarios such as text recognition, image recognition, speech recognition, video recognition, and semantic recognition.

![](converter-master/example/165201/content_633717_0.jpg)

Figure 5-16

Bounding box annotation generally applies to images. Annotators simply use boxes to annotate information in the training data. For example, in vehicle recognition, annotators box the vehicle images, as shown in Figure 5-17. Bounding box annotation is generally used in fields like face recognition and object recognition.

![](converter-master/example/165201/content_633717_1.jpg)

Figure 5-17

Segmentation (range) annotation is more precise than tag and bounding box annotation. It requires covering information in the image with irregularly shaped blocks, as shown in Figure 5-18. This type of annotation is generally used in road recognition, where vehicles rely on it for road judgment during autonomous driving.

![](converter-master/example/165201/content_633717_2.jpg)

Figure 5-18

Keypoint annotation requires refinement down to specific anchor point features, such as facial keypoints and skeletal keypoints. Keypoint annotation applies to various types of image monitoring, such as face detection in digital cameras, face information verification, medical image recognition, etc., as shown in Figure 5-19.

![](converter-master/example/165201/content_633717_3.jpg)

Figure 5-19

Since data annotation is repetitive work with low innovation requirements and tedious processes, considering costs, many companies hire interns or outsource to complete annotation work. When outsourcing, the author suggests establishing **three unifications** before submitting data: a unified tag system, unified tag rules, and unified data auditing, to ensure scientific rigor and executability in the annotation process.

A unified tag system means setting up a complete, reasonable, multi-level tag hierarchy, allowing users to annotate from multiple dimensions. Unified tag rules prevent annotation errors caused by human factors during annotation. Unified data auditing ensures a single standard determines the final usability of data; otherwise, situations may arise where identical data is annotated differently due to different auditing rules.

Due to the large volume of data to be annotated, auditors mostly use spot checks or cross-validation during the audit process. Spot checks generally randomly inspect about 10% of the total data volume to save manpower. Cross-validation is usually performed mutually between different annotators, with the validation quantity generally being 50%–80% of the annotation volume.

### 5.6.2 Model Training and Tuning

There are numerous algorithms in machine learning and deep learning, with varying structures. Different types of algorithms involve different parameters, so the model training and tuning processes vary by algorithm. Model training and tuning are generally completed independently by algorithm engineers; product managers or other personnel do not need to participate. However, product managers should make full preparations during this process.

During model training, algorithm engineers adjust the number of hidden layers, neurons, and activation functions based on model characteristics, thereby optimizing the learning curve. Product managers should promptly follow up and check the algorithm training process and tuning effects. If effects are poor, optimization can be assisted by adjusting data. Data adjustment mainly includes data supplementation and data optimization.

Product managers should analyze whether the model's recognition effect on data meets expectations. If not ideal, they should re-examine the training data. If there is a large amount of similar data and the model performance is mediocre, product managers should observe data details and remove similar data from the annotated data. Additionally, the spot check ratio of annotated data should be increased to over 50%, which will significantly improve recognition effects.

Besides reviewing already annotated data, supplementary data can be introduced to increase training data volume, using a portion of test data for training. Swapping test data with training data can also optimize recognition effects.

### 5.6.3 Model Validation

After model training is complete, the testing phase begins. During testing, test data is input into the model to observe its performance. The determination of test effects mainly relies on key indicators defined during the product design phase (indicators vary by product), such as `Accuracy`, `Recall`, `Precision`, `AUC Curve`, and `ROC Curve`.

Assume an algorithm model judges diabetes in a group. Patients are marked Positive, healthy individuals Negative. There are four judgment outcomes: True Positive (TP) — sick judged as sick; False Negative (FN) — sick judged as healthy; True Negative (TN) — healthy judged as healthy; False Positive (FP) — healthy judged as sick, as shown in Figure 5-20.

![](converter-master/example/165201/content_633717_4.jpg)

Figure 5-20

If the total sample is 1,000 people, with 200 diabetic and 800 healthy. After model calculation, 20 of the 200 diabetic are classified as healthy, and 10 of the 800 healthy are classified as diabetic. Then `TP=180`, `TN=790`, `FN=20`, `FP=10`.

`Accuracy` refers to the proportion of correct judgments, i.e., the ratio of correct data to total samples: `Accuracy = (TP+TN) / (TP+TN+FN+FP) × 100%`.

Accuracy is a crucial indicator; higher accuracy means the model judges correct information more accurately. In this case, Accuracy = `(180+790) / 1000 × 100% = 97%`, indicating the product is generally feasible.

`Recall` (Sensitivity) mainly judges whether the model captures all positive cases, i.e., the ratio of correctly judged positive samples to all actual positive samples: `Recall = TP / (TP+FN) × 100%`.

In this case, Recall = `180 / 200 × 100% = 90%`, meaning 90% of patients are detected.

`Precision` is the proportion of true positives among all predicted positives: `Precision = TP / (TP+FP) × 100%`.

In this case, Precision = `180 / (180+10) × 100% ≈ 95%`, representing the accuracy of all information judged as positive.

Different scenarios prioritize different indicators. Healthcare values Accuracy and Recall, believing higher detection probability of illness means better product effect. E-commerce intelligent recommendation focuses more on Precision; the higher the ratio of meaningful recommendations to total recommendations, the more helpful. Prioritizing one indicator doesn't mean ignoring others; product managers should find a balance among them.

Besides key indicators, error issues need attention during testing. Errors mainly include Training Error and Generalization Error. Training error is the error shown on training data; generalization error is the error shown on new test sets. Differences arise not only from algorithm selection and parameter tuning but also potentially from the dataset. If training and generalization errors differ significantly and cannot be improved via algorithm/parameter tuning, the dataset must be adjusted. Adjustment methods include:

1.  **Cross-Validation**

Cross-validation splits the dataset into multiple subsets of similar size and similar distribution. One subset is used as the test set, the rest as training sets. After multiple rounds of cross-training and validation, the final result is the average. The number of splits is crucial, called the `K` value, hence `K-fold Cross-Validation`. If K=10, it is 10-fold Cross-Validation.

2.  **Hold-out Method**

The hold-out method is simple: split data into two parts, one for training, one for testing. It must be repeated multiple times; single use leads to large differences between training and generalization errors. Both training and test data require stratified sampling to reduce error.

3.  **Bootstrapping**

If sample size is small, bootstrapping can be used to construct training sets via random sampling with replacement. Since bootstrapping changes the training set distribution, estimation bias exists. Therefore, with sufficient data, Cross-Validation and Hold-out are recommended.

### 5.6.4 Product Evaluation

After algorithm model testing and validation, a comprehensive evaluation based on product expectations is needed to confirm if the model meets launch requirements. Before launch, product managers and test managers must repeatedly verify model effects and have technicians adjust based on feedback. In the evaluation phase, product managers should assess all indicators. Besides measuring basic requirements via Accuracy, Recall, Precision, etc., they should evaluate other indicators specific to product characteristics. For example, developing a smart speaker requires evaluating Wake-up Rate, False Wake-up Rate, Power Metrics; developing a conversational robot requires evaluating Speech Synthesis technology. Below are key points for several actual products.

1.  **Speech Recognition (ASR)**

Speech recognition is a necessary function for smart robots, smart speakers, smart translation devices, etc., requiring human-computer interaction. Voice control must go through ASR (Automatic Speech Recognition), converting sound to text. ASR has many technical indicators; **Word Error Rate (WER)** is notable. Controlling WER ensures the recognized word sequence matches the standard sequence, requiring language revision. For example, in spoken communication, word order is often revised: "吃了饭了吗你？" (Ate rice? You?).

In communication, people automatically revise modal particles and understand inverted sentences. But machines need to revise recognized language, including word substitution, deletion, insertion, e.g., revising "吃了饭了吗你？" to "你吃饭了吗？" (Have you eaten?).

The total number of words substituted, deleted, or inserted by the machine, divided by the total words in the standard sequence, is the WER. Because of insertions, WER can exceed 100%. Excessive insertions usually cause semantic deviation. Determining WER requires distinguishing scenarios and types: different languages, numbers, genders, accents. Besides WER, there is **Sentence Error Rate (SER)**, the ratio of correctly recognized sentences to total sentences. SER is derived from WER, so SER is generally higher than WER; focusing on WER is more meaningful.

A key function in speech recognition product design is **when to wake the device and start recognition**. Two common wake-up methods: **Touch Wake-up** and **Voice Wake-up**.

Touch wake-up is usually near-field, used for handheld devices or recognition in noisy environments (e.g., voice input in WeChat requires holding a screen button).

If the device is far away, unreachable by hand, voice wake-up is used (e.g., smart speaker in living room). Touch wake-up here would ruin UX. **Wake words** are crucial for voice wake-up. They should be uncommon in daily life but easy to pronounce. English: 3–5 syllables; Chinese: 3–4 characters. Fewer than 3 syllables increases false wake-ups; more than 4 characters makes them hard to remember. E.g., Tmall Genie uses "天猫精灵" (Tmall Genie), Xiaomi AI Speaker uses "小爱同学" (Xiao Ai Tongxue). If Tmall Genie used just "天猫" (Tmall), false wake-up rate would skyrocket. Wake words activate the device; subsequent speech is recognized.

Key wake-up indicators: Wake-up Rate, False Wake-up Rate, Wake Word Length, Wake-up Response Time.

2.  **Speech Synthesis (TTS)**

Speech synthesis converts text to audio signals for playback, used for info delivery and HCI. E.g., Microsoft Xiaoice's voice uses TTS. Quality is mainly judged by **Naturalness**, assessed subjectively.

Familiar `GAN` (Generative Adversarial Networks) can analyze voice features to generate similar voices. Simulation quality can be evaluated by voice feature similarity.

3.  **Recommendation Systems**

Due to complex evaluation structures and subjective factors, recommendation system evaluation combines quantitative and subjective testing. Main indicators: User Satisfaction, Prediction Accuracy, Coverage, Diversity, Novelty, Serendipity, Timeliness, Anti-fraud Robustness.

Since the audience is users, **User Satisfaction** is key. It cannot be quantified directly, so analyzed via user behavior data or surveys. On e-commerce sites, purchasing via recommendations indicates satisfaction. Increased purchase/repurchase rates via recommendations measure satisfaction.

**Prediction Accuracy** measures the system's ability to predict user behavior, via rating prediction, Top-N recommendation Accuracy and Recall. Accuracy: proportion of recommended N items meeting user needs. Recall: proportion of relevant items appearing in recommendations.

**Coverage** is the proportion of recommendable items out of the total item pool. Not all items get exposure. A good system needs high satisfaction *and* high coverage.

User needs relate to context. Users don't only browse recommended items. To satisfy diversity, a good system should recommend items from different interest domains.

**Novelty** recommends completely unknown info, sparking curiosity. Users hope to discover new info/fields via systems; novelty aids retention.

**Serendipity** is a rising focus, boosting satisfaction. Unlike novelty, serendipity focuses on divergence from historical interests. If recommended info differs from history but attracts attention, serendipity is achieved.

For real-time content like news, systems must consider **Timeliness**, updating recommendations in real-time to match user behavior changes.

**Anti-fraud Robustness** is critical for sustainable operation. If algorithms aren't robust, they are vulnerable to behavior injection attacks; content producers game the rules to boost ranking. R&D should choose robust algorithms and high-cost user behaviors. E.g., prefer purchase data over browsing data for recommendations, as purchases cost more and are harder to fake. `EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==`

