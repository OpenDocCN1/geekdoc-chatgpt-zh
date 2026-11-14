
2. Please use graphical programming software, load the speech synthesis module, write the corresponding program, and see if it can read out the information you input.

# Chapter 5 Artificial Intelligence Ethics

![](images/txt005_1.jpg "pagenumber_ebook=116,pagenumber_book=105")

## Introduction

In the era of artificial intelligence, technologies such as image recognition, speech recognition, autonomous driving, and intelligent robots have entered people's lives. While bringing convenience, they have also given rise to issues such as personal privacy leaks, ambiguous value judgments, and missing accountability. AI faces ethical risks at the data, algorithm, and application levels. We need to strengthen risk assessment and prevention for AI applications, and comprehensively employ technical innovation, ethical standards, legal systems, and other means to achieve harmonious coexistence between humans and machines.

![](images/txt005_2.jpg "pagenumber_ebook=116,pagenumber_book=105")

## 5.1 Data and Privacy

### 5.1.1 Convenience and Challenges in the Data Era

The more "intelligent" artificial intelligence becomes, the more personal data it needs to acquire. Today we frequently use mobile phones to shop on Taobao, post photos on WeChat Moments, and watch short videos on Douyin; simultaneously, we are constantly producing data. Personal communication information, location information, and various biometric data are being continuously collected and stored in cyberspace.

As users of different applications, our every action is recordable, analyzable, and traceable. Should this personal information be saved? Where should it be saved? Is it secure during data processing and transmission? How should it be managed and used after collection? These are all issues facing the digital society. For example, incidents like the PRISM scandal and the Facebook user data leak have occurred.

China's *Civil Code* categorizes personal information into "private information" and "non-private information." Among them, private information belongs to both personal information and privacy. Examples include personal health information, ID card numbers, personal biometric information, bank account numbers, etc. However, a natural person's name, gender, etc., do not belong to private information. For instance, during a company job interview, one needs to provide such personal information; names are originally used in social interactions (Figure 5-1).

In the intelligent era, data has become a new factor of production. Personal non-sensitive information needs to be shared and utilized reasonably, but personal privacy data should be strictly protected and supervised, and must not be used by certain merchants or individuals for profit.

![](images/txt005_3.jpg "pagenumber_ebook=117,pagenumber_book=106")

Figure 5-1　General Information vs. Sensitive Information

### 5.1.2 Privacy Data Leakage and Hazards

#### 1. AI in Mobile Phones

Artificial intelligence can be used in various aspects such as photo beautification, automatic video subtitling, OCR text recognition, and language translation. While bringing convenience, it may also be stealing your privacy (Figure 5-2).

Before using an App, everyone needs to open the phone's "Privacy" settings to "agree" to authorize permissions for contacts, location, microphone, etc. (Figure 5-3). Behind most of these software applications, there exist problems such as unauthorized collection of personal information, excessive permission requests, and even "sharing user information with third parties without authorization."

When we browse a certain product, shopping websites immediately recommend related products. Behind this phenomenon is a recommendation algorithm carefully designed by programmers. It acquires users' names, birthdays, addresses, consumption habits, product preferences, and even parents' annual salaries, analyzes and organizes this personal information, assigns unique tags to each user based on personal data, creates precise user "portraits," and completes personalized shopping recommendations (Figure 5-4).

![](images/txt005_4.jpg "pagenumber_ebook=118,pagenumber_book=107")

Figure 5-2　App Privacy Theft

![](images/txt005_5.jpg "pagenumber_ebook=118,pagenumber_book=107")

Figure 5-3　Mobile Phone "Privacy" Authorization

![](images/txt005_6.jpg "pagenumber_ebook=118,pagenumber_book=107")

Figure 5-4　Shopping Website User Information

![](images/txt005_7.jpg "pagenumber_ebook=119,pagenumber_book=108")

Figure 5-5　User Tags

![](images/txt005_8.jpg "pagenumber_ebook=119,pagenumber_book=108")

Figure 5-6　Hazards of Information Leakage

![](images/txt005_9.jpg "pagenumber_ebook=119,pagenumber_book=108")

Figure 5-7　AI Face Swapping and Voice Changing

When users browse many pages about food and health, they are tagged as "foodie," "weight loss," etc. (Figure 5-5), and relevant advertising content is recommended. These contents not only satisfy users' needs of being "understood" in form but also take the opportunity to seek profit.

Personal privacy information leakage brings serious consequences. Many people are often bombarded with harassing calls, spam text messages, and spam emails. Phones may be "wiretapped" by third parties, cameras may be automatically turned on by criminals for "peeping," and users may even face risks of online fraud and crime (Figure 5-6).

#### 2. AI Voice Interaction

Currently popular smart speakers are "monitoring" your every move at home, and the privacy rights of natural persons may be being harmed. From a working principle perspective, data collection by smart speakers is unavoidable. The reason smart speakers are "smart" is that they possess autonomous learning functions. In the process of dialogue, communication, and interaction with users, they become "intelligent" by learning massive amounts of data information.

During the wake-up process of a smart speaker, as long as the microphone is turned on, it may be "eavesdropping" on the user's speech continuously. It can collect personal information such as the user's voice and location, and upload the voice information of the conversation to the cloud server provided by the merchant for storage.

#### 3. AI Face Swapping and Voice Changing

AI's talent lies in being recognizable, imitable, and recreatable (Figure 5-7). AI face swapping can help film actors perfectly portray characters with large age spans.

##### Knowledge Expansion

###### Information Security of Facial Recognition

The information security of facial recognition contains two levels.

**First level: Learn to protect personal facial information.**

Media once reported that merchants openly sold "facial data" on online marketplaces. Behind these hundreds of thousands of data records were over 2,000 faces, including celebrities, ordinary citizens, and even some minors. This data included location information with faces and 68 facial feature points. The interviewees were not only unaware that their "facial data" was being sold, but even didn't know when their facial data was collected. This news sends chills down one's spine: what if my facial information is "stolen"? A stolen password can be reset, but a face cannot be changed! Therefore, for us, we can do the following to prevent leakage of personal privacy including biometric information.

- Do not download applications of unknown purpose at will; download from official or large-scale app stores.
- Do not authorize application permission requests at will; agree to necessary permissions, reject unnecessary ones.
- Do not use application facial photo functions at will, especially when facial photos are required and ID information upload is requested (e.g., ID card, passport, driver's license, etc.).
- Do not use various head-swapping, face-swapping, and facial expression pack creation functions at will.
- Do not agree to take photos with strangers at will; photos may leak the facial data of those in them.
- Do not upload your own facial photos and videos on social networking sites at will.

**Second level: Major enterprises, when using facial recognition for identity verification, have realized the security risks brought by facial information leakage and have begun to add the key step of facial liveness detection in facial recognition.**

Liveness detection usually adopts the following measures:

- Record a short video on the spot, read random characters to ensure the video is not pre-recorded, upload the video for liveness analysis to improve resistance.
- According to voice prompts, complete preset actions such as blinking, opening mouth, shaking head, turning head left/right, nodding up/down, capture multiple images for liveness judgment.
- Based on the principle of structured light imaging, construct depth images through infrared light spots reflected by the face surface to determine whether the target is a living body, effectively defending against attacks from photos, videos, etc.

Through the dual approach of the above two levels, combined with necessary legislative guarantees for citizens' personal privacy including facial data, facial data can certainly be better protected from theft and abuse, safeguarding citizens' relevant rights and interests.

AI voice changing technology can be used in film dubbing, providing voices for characters of different ages and identities. AI only needs a few seconds of audio samples to imitate or clone another person's voice. Your voice can be changed, and thus can be forged.

The biggest problem with AI face-swapping and voice-changing Apps lies in the large-scale collection of personal biometric information samples. Once biometric information samples are lost or leaked, more serious consequences will ensue. Biometric information such as faces and voiceprints generally cannot be altered; once leaked and obtained by criminals, they will bring forgery and fraud.

### 5.1.3 Rules Data Ethics Should Follow

#### 1. Hierarchical Data and Sound Legal Systems

AI development should respect and protect personal privacy. At the national legal level, specific issues such as the subject and scope of data sets must be clarified, returning the right to know and the right to choose to the users themselves. Strengthen protection of personal sensitive data, implement hierarchical classification management for private information that can be shared, promote the circulation and sharing of various social data information, and enable public data to effectively empower various industries.

#### 2. Qualified Institutions Strengthen Supervision According to Law

For enterprises, users are the weaker party. The use and supervision of personal data should be conducted routinely and in accordance with the law by qualified third-party institutions. Spot checks should be conducted on the security and compliance of enterprises in the collection, processing, storage, circulation, and use of user data information. If issues involving leakage of personal private information security are involved, public interest litigation should be initiated.

#### Thinking & Exploration

1. What kind of "data rights" do we need in the AI era?
2. How to protect the security of our "personal privacy data"?

## 5.2 Algorithms and Bias

### 5.2.1 Machine Learning Makes AI Smarter

Machine learning is automatic programming, a type of "learning algorithm." Computers can learn from past human experience, i.e., "data." The "learning algorithm" is similar to the human "brain"; after training and learning, it can make machines "understand you" better (Figure 5-8).

![](images/txt005_10.jpg "pagenumber_ebook=122,pagenumber_book=111")

Figure 5-8　Machine Learning

We already live in a world of algorithms. Algorithmic decision-making can assist humans in recommendation shopping, financial investment, medical diagnosis, personnel recruitment, fugitive capture, autonomous driving, and other tasks. "Algorithms" are increasingly dominating human social affairs.

Machine learning requires a large number of correct samples for training, and the "big data sets" that "algorithms" rely on are all extracted from society. They often carry inherent social inequalities and discriminatory information. Engineers often unconsciously infiltrate their personal values when formulating "algorithm rules," leading to the problem of "algorithmic bias."

### 5.2.2 Ethical Issues Brought by Algorithmic Bias

#### 1. Computational Data Carries Bias

Algorithmic models are "opinions expressed in mathematical or code form." If the computed output results are biased, there must be a reason. Data sets are the soil that breeds "bias." If the data sets used for machine learning are themselves biased or lack representativeness, algorithmic decisions will also be unfair (Figure 5-9).

#### 2. Algorithms May Hide Discrimination

"Algorithmic neutrality" is an ideal. Behind algorithms exist the designers' positions and decisions. Engineers are the formulators of "algorithm rules." The selection of features (data labels), the choice of models, and the embedding of value judgments all determine the final prediction results.

The farce of "big data price discrimination" (killing the familiar) has become commonplace in modern society. Online platforms profile users based on previously collected personal identity data, shopping data, behavioral data, etc. Using algorithmic analysis to identify "high consumption" and "low consumption" groups, they implement differentiated pricing strategies of "one price per person," selling the same product at different prices to different consumers (Figure 5-10). The recommendation algorithms designed by programmers should uphold the traditional business ethics of "integrity first, no deception," but merchants, for "commercial interests," change the "algorithm rule" design, infringing on user interests.

![](images/txt005_11.jpg "pagenumber_ebook=123,pagenumber_book=112")

Figure 5-9　Schematic Diagram of Algorithmic Model

![](images/txt005_12.jpg "pagenumber_ebook=123,pagenumber_book=112")

Figure 5-10　Big Data Price Discrimination

#### 3. Algorithmic Decision-Making Dilemmas

There is a classic philosophical thought experiment, the "Driver's Dilemma" (Figure 5-11). The experiment goes like this: a car is driving down a slope, and suddenly 1 person appears ahead, while there are 4 people in the car. At this critical moment, which way should the car turn?

![](images/txt005_13.jpg "pagenumber_ebook=124,pagenumber_book=113")

Figure 5-11　Algorithmic Decision-Making Dilemma

Humans themselves have not figured out the "algorithm" to solve this problem, so how should the machine choose? Throwing this unsolvable problem to the "algorithm" and hoping it can give the most suitable answer is obviously impossible. AI algorithmic decision-making is a "black box"; we cannot understand the decision-making process, only accept the decision results. On issues involving the "decision-making power" of life, are we ready to hand over the "decision-making power" to artificial intelligence?

### 5.2.3 Rules Algorithmic Ethics Should Follow

#### 1. Reasonably Regulate Fair Data Sets

Data bias is the soil for "algorithmic bias." To solve this, we must start with data, e.g., combining "big data" and "small data" for comprehensive analysis to ensure the decision-making accuracy of algorithms in deducing causal relationships. Both humans and AI make mistakes; correction mechanisms are essential. Autonomous test data sets can be used to identify inherent discrimination and bias, and resampling can eliminate data bias in AI systems.

#### 2. Improve Algorithmic Transparency

Every AI product launched by an enterprise should undergo AI moral and ethical review and supervision. Programmers must embed human social ethics, legal morals, etc., into AI systems. AI must respect the diversity of human culture and not harm human dignity. The algorithms must possess publicity, transparency, and interpretability. Once an accident occurs, ensure the AI decision-making process is traceable, and all decision data can be submitted to courts and professional third-party evaluation agencies for assessment and review.

#### Thinking & Exploration

Algorithms also have biases, so do we still need to trust them?

## 5.3 Safety and Responsibility

### 5.3.1 Development Trends of Artificial Intelligence

With the development of AI technology, various types of robots have appeared, such as patrol robots, sweeping robots, aerial flying robots, underwater robots, surgical robots, rehabilitation robots, consulting service robots, exoskeleton robots, and bionic robots (Figure 3-5, Figures 5-12—5-14). Mechanical devices have gradually mastered the abilities to see, hear, speak, think, and move, becoming increasingly "intelligent." We call these robots controlled by AI programs "AI robots."

![](images/txt005_14.jpg "pagenumber_ebook=125,pagenumber_book=114")

Figure 5-12　Haircut Robot

![](images/txt005_15.jpg "pagenumber_ebook=125,pagenumber_book=114")

Figure 5-13　Humanoid Robot

![](images/txt005_16.jpg "pagenumber_ebook=125,pagenumber_book=114")

Figure 5-14　Patrol Robot

Currently, we are still in the stage of weak AI development. AI robots can only solve specific problems in a certain field. For example, the Go robot AlphaGo can only do one thing specifically; it fails in other aspects. Experts predict that around 2040, the "strong AI stage" will arrive, when AI robots will possess "general intelligence equivalent to human level"; around 2060, the "super AI stage" will also come, which we can understand as a "superintelligent brain" millions of times smarter than the strongest "human brain" arriving in this world. This "tipping point" may arrive early, or may never appear.

Will AI robots replace humans in future society? Do AI robots count as a person? How should machines and humans integrate and coexist? AI technology can greatly improve work efficiency and create huge economic benefits, but also brings severe challenges to human society.

### 5.3.2 Social Problems Brought by Artificial Intelligence

#### 1. Unemployment Issues

Human wisdom's advantage lies in creativity—inventing, creating, and using "tools" to improve social production efficiency and drive continuous social progress. "Artificial Intelligence" is another "tool" created by humans, used to replace human "mental labor."

Reviewing history, every technological development and innovation improves social production efficiency and brings huge economic benefits; it creates many new jobs, but inevitably causes some people to lose their original jobs.

In future society, unmanned supermarkets, unmanned containers, and unmanned restaurants will become increasingly popular. More and more jobs will be replaced by "AI robots." According to surveys, the jobs most likely to be replaced by AI within 15 years are telemarketers, truck drivers, financial advisors, security guards, translators, and other repetitive positions.

Currently, jobs difficult for AI robots to replace are complex creative professions, such as scientists, designers, negotiation experts, entrepreneurs, oral surgeons, teachers, aircraft mechanics, etc.

AI's advantages lie in memory and logical thinking capabilities far exceeding humans, and it is much more efficient in massive information processing and classification. For example, the Go robot AlphaGo already possesses powerful autonomous learning capabilities. Therefore, humans need to keep learning to make themselves stronger, otherwise they will be eliminated for failing to adapt to social development needs.

#### 2. Legal Issues

In the AI era, "algorithms" also have the opportunity to become artists. AI can learn from data of past successful cases and finally output its own works (Figure 5-15), such as writing poems, painting, composing music, etc. But this also brings a series of problems: who owns the copyright of AI works? Is the author the robot or the programmer? This brings new problems and challenges to the currently implemented intellectual property and copyright systems.

![](images/txt005_17.jpg "pagenumber_ebook=127,pagenumber_book=116")

Figure 5-15　AI Works

#### 3. Safety Issues

The inevitable result of technological progress is the replacement of human "physical labor," and even part of "mental labor," by robots. The issue of how humans and robots can coexist safely in the AI era (Figure 5-16) must be given sufficient attention. According to news reports, tragedies of robots causing accidents leading to worker deaths have occurred in Germany, Japan, and China.

#### 4. Digital Divide

The "digital divide" faced by the elderly is manifested in their relatively weaker ability to apply information technology and discern the authenticity of online information. In the AI era, issues such as how to achieve facial recognition to obtain epidemic prevention green codes without smartphones have attracted attention. This requires society and families to give more humanistic care and patient help.

In 1940, famous sci-fi writer Isaac Asimov proposed the "Three Laws of Robotics."

> **First**, a robot may not injure a human being;
>
> **Second**, a robot must obey orders given by human beings, except where such orders would conflict with the First Law;
>
> **Third**, a robot must protect its own existence as long as such protection does not conflict with the First or Second Law.

![](images/txt005_18.jpg "pagenumber_ebook=128,pagenumber_book=117")

Figure 5-16　Coexistence of AI and Humans

### 5.3.3 Society of Human-Machine Integration and Development

#### 1. Safety First, Serving Humans as the Foundation

Robots must ensure safety and reliability for humans during use, always adhere to the principle of "serving humans," bring welfare and convenience to humans, and never deceive, harm, or even destroy humans. Robots must learn to respect human rights and abide by basic guidelines and values of human ethical society. By 2050, the world will enter an aging society; the future will be a society of human-machine collaborative integration. At that time, a large number of care robots will certainly be put into nursing homes, which can greatly reduce the workload of human caregivers.

#### 2. Adhere to Accountability for Robot Decision Accidents

If a robot replaces human decision-making and produces bad results, who is responsible—the machine or the human? For example, if a fully autonomous vehicle makes a wrong decision leading to a car accident, we cannot let the machine or AI system be the scapegoat for humans. Machine intelligence is bestowed by humans; humans must be responsible for the consequences of decision-making errors.

#### Thinking & Exploration

1. Who do you think should own the patent for products invented by AI? Do you support so-called AI human rights?
2. Will the development of AI technology pose a threat to humans? Why?

## Postscript

Looking at the present, it is not difficult to find that AI has been increasingly integrated into our lives: medical robots in hospitals, news pushed to us daily may be automatically generated by machines, cars equipped with autonomous driving technology are already on the road, instant voice translation makes language barriers abroad no longer a worry, drones and unmanned vehicles make express delivery faster, phones can unlock via facial recognition, smart speakers bring human-machine interaction...

In the future, with the deep integration of AI with 5G networks, the Internet of Things, big data, cloud computing, and other technologies, various intelligent devices will interconnect people with people, people with things, and things with things, forming an intelligent interconnected ecosystem. Various data will flow and communicate in real-time in this ecosystem, and AI, relying on increasingly mature algorithms and continuously improving computing power, will become the core of this ecosystem.

