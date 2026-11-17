

## 5.6 Practical Process of AI Products

AI products go through multiple stages from planning to launch. Product planning requires defining the product scope, clarifying boundaries and expectations, and organizing data. Then, algorithm engineers design algorithms, and data engineers perform data preprocessing and annotation. Once data processing is complete and the algorithm is finalized, model training and tuning are conducted. Subsequently, the processed data is used for model validation. Upon successful validation, a final product evaluation is performed.

### 5.6.1 Data Annotation

The development of AI is closely related to the growth of data volume in the Internet era, but data in practical applications is full of noise. Precisely because data contains noise, data cleaning is often required before annotation. Cleaning yields data structures and results that meet requirements, ensuring the algorithm can recognize them.

Take image recognition as an example. To identify a cat in an image, traditional algorithms would store the cat's structure and then match it. However, if the cat is partially obscured or curled up and deformed, traditional algorithms fail to recognize it. But if we have many cat images, annotate them, and use machine learning to summarize the cat's features, the machine can then recognize a cat in a new image.

Generally, the ratio of training data to test data is 4:1 or 5:1. For instance, with 10,000 cat images, 8,000 can be used as the training set and 2,000 as the test set. After training on the 8,000 images, the remaining 2,000 are tested. If the recognition rate exceeds 95%, the model is considered relatively accurate.

Data annotation work generally involves two roles: annotators and reviewers. Annotators are responsible for labeling data, while reviewers check the quality of the annotated data. During annotation, annotators typically use methods such as tag annotation, bounding box annotation, segmentation (range) annotation, and keypoint annotation.

1. **Tag Annotation**  
   When using tag annotation, a tag library is usually defined. Annotators select one or more tags from the library to label the target data. As shown in Figure 5-16, tags such as gender, expression, ethnicity, glasses, age, and occlusion are used. For text tags, libraries for subject, predicate, object, attribute, adverbial, complement, noun, adjective, commendatory, derogatory, etc., can be established to determine text attributes. Tag annotation has a wide range of applications, suitable for text recognition, image recognition, speech recognition, video recognition, semantic recognition, and other scenarios.

   ![](converter-master/example/165201/content_633718_0.jpg)  
   Figure 5-16

2. **Bounding Box Annotation**  
   Bounding box annotation generally applies to images. Annotators label information in the training data with boxes. For example, in vehicle recognition, annotators simply box the vehicle image, as shown in Figure 5-17. This method is commonly used in face recognition, object recognition, and similar fields.

   ![](converter-master/example/165201/content_633718_1.jpg)  
   Figure 5-17

3. **Segmentation (Range) Annotation**  
   Segmentation annotation is more precise than tag or bounding box annotation. It requires covering information in the image with irregularly shaped blocks (segments), as shown in Figure 5-18. This type of annotation is generally used in road detection, where vehicles rely on it for road judgment during autonomous driving.

   ![](converter-master/example/165201/content_633718_2.jpg)  
   Figure 5-18

4. **Keypoint Annotation**  
   Keypoint annotation requires refining down to specific anchor point features, such as facial keypoints or skeletal keypoints. It applies to various types of image monitoring, such as face detection in digital cameras, face verification, medical image recognition, etc., as shown in Figure 5-19.

   ![](converter-master/example/165201/content_633718_3.jpg)  
   Figure 5-19

Since data annotation is repetitive, requires low innovation, and is tedious, many companies hire interns or outsource to save costs. When outsourcing, the author suggests establishing **three unifications** before submitting data: a unified tag system, unified tag rules, and unified data review, to ensure scientific rigor and executability in the annotation process.

- **Unified Tag System**: Defining a comprehensive, reasonable, multi-level tag hierarchy, allowing users to annotate from multiple dimensions.
- **Unified Tag Rules**: Preventing annotation errors caused by human factors during the labeling process.
- **Unified Data Review**: Ensuring a single standard determines the final usability of data, preventing situations where identical data receives different annotations due to differing review standards.

Due to the large volume of data to be annotated, reviewers mostly use **spot checks** or **cross-validation** during the review process. Spot checks typically randomly inspect about 10% of the total data volume to save manpower. Cross-validation is usually performed mutually between different annotators, with the validation quantity generally being 50%–80% of the annotation volume.

### 5.6.2 Model Training and Tuning

There are numerous machine learning and deep learning algorithms with varying structures. Different algorithm types involve different parameters, so the model training and tuning processes vary. Training and tuning are generally completed independently by algorithm engineers; product managers or other personnel do not need to participate directly. However, product managers should make thorough preparations during this process.

During training, algorithm engineers adjust the number of hidden layers, neurons, and activation functions based on model characteristics to optimize the learning curve. Product managers should promptly track and inspect the training process and tuning effects. If results are poor, they can assist optimization by adjusting data. Data adjustment mainly includes data supplementation and data optimization.

Product managers should analyze whether the model's recognition performance on data meets expectations. If not, they should re-examine the training data. If there is a large amount of similar data and model performance is mediocre, product managers should observe data details and remove similar data from the annotated set. Additionally, the spot-check ratio for annotated data should be increased to over 50%, which significantly improves recognition accuracy.

Besides re-checking annotated data, supplementary data can be introduced to increase training volume, using a portion of test data for training. Swapping test data with training data can also optimize recognition effects.

### 5.6.3 Model Validation

After model training, the testing phase begins. Test data is fed into the model to observe its performance. The determination of test effects relies mainly on key indicators defined during the product design phase (indicators vary by product), such as `Accuracy`, `Recall`, `Precision`, `AUC Curve`, and `ROC Curve`.

Assume an algorithm model judges diabetes in a population. Patients are labeled Positive, non-patients Negative. There are four judgment outcomes: True Positive (TP), False Negative (FN), True Negative (TN), False Positive (FP), as shown in Figure 5-20.

- Judging a diabetic patient as diabetic: correct data judged as correct, `TP`.
- Judging a diabetic patient as non-diabetic: correct data judged as incorrect, `FN`.
- Judging a non-diabetic as non-diabetic: incorrect data judged as incorrect, `TN`.
- Judging a non-diabetic as diabetic: incorrect data judged as correct, `FP`, as shown in Figure 5-20.

![](converter-master/example/165201/content_633718_4.jpg)  
Figure 5-20

If the total sample is 1,000 people, with 200 diabetics and 800 non-diabetics. After model calculation, 20 of the 200 diabetics are classified as non-diabetic, and 10 of the 800 non-diabetics are classified as diabetic. Then `TP=180`, `TN=790`, `FN=20`, `FP=10`.

- **Accuracy**: The proportion of correct judgments, i.e., correct data vs. total samples. `Accuracy = (TP + TN) / (TP + TN + FN + FP) × 100%`.  
  Accuracy is a crucial indicator; higher accuracy means better judgment of correct information. In this case: `(180 + 790) / 1,000 × 100% = 97%`, indicating overall product feasibility.

- **Recall** (Sensitivity): Measures how completely the model identifies positives. `Recall = TP / (TP + FN) × 100%`.  
  Here: `180 / 200 × 100% = 90%`, meaning 90% of actual patients are detected.

- **Precision**: The proportion of true positives among all predicted positives. `Precision = TP / (TP + FP) × 100%`.  
  Here: `180 / (180 + 10) × 100% ≈ 95%`, indicating the accuracy of all positive predictions.

Different scenarios prioritize different indicators. Healthcare values `Accuracy` and `Recall` (higher detection probability is better). E-commerce recommendation focuses on `Precision` (higher ratio of relevant recommendations). Prioritizing one doesn't mean ignoring others; product managers must find a balance.

Besides key indicators, **error** issues need attention during testing. Two main types: `Training Error` and `Generalization Error`. Training error is the error on training data; generalization error is the error on new test sets. Discrepancies arise from algorithm choice, parameter tuning, and the dataset itself. If the gap is large and cannot be fixed by algorithm/tuning, the dataset must be adjusted. Methods include:

1. **Cross-Validation**  
   Split the dataset into multiple similar-sized subsets with similar distributions. Use one as the test set, the rest for training. Repeat multiple times; the final result is the average. The number of splits is `K` (K-fold Cross-Validation). If K=10, it is 10-fold Cross-Validation.

2. **Holdout Method**  
   Split data into two parts: one for training, one for testing. Must be repeated multiple times; single use leads to large error gaps. Both training and test data require **stratified sampling** to reduce error.

3. **Bootstrapping**  
   If sample size is small, use random sampling with replacement to build the training set. Since this changes the training set distribution, estimation bias exists. With sufficient data, Cross-Validation and Holdout are preferred.

### 5.6.4 Product Evaluation

After validation, a comprehensive evaluation against product expectations is needed to confirm if the model meets launch requirements. Before launch, product managers and test managers repeatedly verify model effects, and technicians adjust based on feedback. In the evaluation phase, product managers assess all indicators. Beyond `Accuracy`, `Recall`, `Precision` for basic requirements, they must evaluate other indicators specific to product characteristics. For example, smart speakers need `Wake-up Rate`, `False Wake-up Rate`, `Power Metrics`; chatbots need `Speech Synthesis Technology` evaluation. Below are key points for actual products.

#### 1. Speech Recognition (ASR)

Speech recognition is essential for interactive devices like smart robots, speakers, and translators. Voice control requires ASR (converting sound to text). ASR has many technical indicators; **Word Error Rate (WER)** is critical. Controlling WER aligns the recognized word sequence with the standard sequence, requiring language revision. In spoken communication, word order is often revised, e.g., "吃了饭了吗你？" (Ate rice? You?).

Humans automatically revise particles and understand inverted sentences. Machines need to revise recognized language via substitution, deletion, insertion, e.g., revising "吃了饭了吗你？" to "你吃饭了吗？" (Have you eaten?).

Total edits (substitutions, deletions, insertions) divided by total words in the standard sequence equals WER. Due to insertions, WER can exceed 100%. Excessive insertions usually cause semantic deviation. WER determination requires scenario/type distinction: language, digits, gender, accents. Besides WER, there is **Sentence Error Rate (SER)**: ratio of correctly recognized sentences to total sentences. SER is derived from WER, so SER is usually higher; focusing on WER is more meaningful.

A key function in ASR product design is **when to wake the device**. Two common methods: **Touch Wake-up** and **Voice Wake-up**.

- **Touch Wake-up**: Close-range, for handheld devices or noisy environments (e.g., holding a button in WeChat to record voice).
- **Voice Wake-up**: For far-field devices where touch is inconvenient (e.g., smart speakers in living rooms). Touch would ruin UX. **Wake Word** is crucial. Usually uncommon but pronounceable words: 3–5 phonemes in English, 3–4 characters in Chinese. <3 phonemes increases false wake-ups; >4 characters hurts memorability. E.g., Tmall Genie: "天猫精灵" (Tmall Jingling); Xiaomi AI Speaker: "小爱同学" (Xiao Ai Tongxue). If Tmall Genie used just "天猫", false wake-ups would soar. The wake word activates the device; subsequent speech is recognized.

Key metrics for wake-up: `Wake-up Rate`, `False Wake-up Rate`, `Wake Word Length`, `Wake-up Response Time`.

#### 2. Speech Synthesis (TTS)

Speech synthesis converts text to audio signals for playback, used for info delivery and interaction. E.g., Microsoft Xiaoice's voice uses TTS. Quality is judged mainly by **naturalness**, assessed subjectively.

Familiar `GAN` (Generative Adversarial Networks) can analyze voice features to generate similar voices. Simulation quality can be evaluated by voice feature similarity.

#### 3. Recommendation Systems

Due to complex validation structures and subjective factors, recommendation system evaluation combines **quantitative** and **subjective** testing. Key metrics: `User Satisfaction`, `Prediction Accuracy`, `Coverage`, `Diversity`, `Novelty`, `Serendipity`, `Timeliness`, `Anti-Fraud Robustness`.

- **User Satisfaction**: Critical but not directly quantifiable. Analyzed via user behavior data or surveys. In e-commerce, purchases via recommendations indicate satisfaction. Increased purchase/repurchase rates measure satisfaction.

- **Prediction Accuracy**: Measures ability to predict user behavior. Evaluated via rating prediction, `Top-N` recommendation `Precision` and `Recall`. Precision: proportion of recommended N items meeting user needs. Recall: proportion of relevant items appearing in recommendations.

- **Coverage**: Proportion of recommendable items out of total item pool. Not all items get exposure. A good system needs high satisfaction *and* high coverage.

- **Diversity**: User needs relate to context. Users don't browse only algorithmic recommendations. Good systems provide items from different interest areas.

- **Novelty**: Recommending completely unknown info to spark curiosity. Users want new info/knowledge domains. Novelty is key for retention.

- **Serendipity**: A rising star, boosting satisfaction. Differs from novelty; focuses on *unexpected relevance*—items differing from historical interests but still attracting attention.

- **Timeliness**: For news/info with high timeliness, systems must update recommendations in real-time to match changing user behavior.

- **Anti-Fraud Robustness**: Critical for sustainable operation. Weak algorithms invite behavior injection attacks; content producers game the rules for exposure. Systems need robust algorithms and high-cost user behaviors (e.g., purchase data over browse data, as purchases are costly to fake).

