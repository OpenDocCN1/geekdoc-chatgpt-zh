

# Discussion on AI Development and Anti-Epidemic Applications

## Integration of China's Consumer Internet and Enterprise Management

China possesses the most developed consumer internet, which has also accumulated excellent data. Based on this, the development of AI applications is also leading globally, giving rise to many world-class AI giants, all of which grew from the consumer (C-end) side. However, China's enterprise management technology had not fully developed in the pre-internet era. In Europe and the US, progress in this field began in the mid-20th century and has continued to develop ever since. Therefore, for AI, it is necessary not only to provide excellent technology but also to integrate advanced enterprise management concepts into products—combining advanced management methods, good ideas, and AI technology—and deliver the decades of management experience accumulated by other countries to clients through products. For our tech enterprises, this is a major opportunity and challenge.

**Yao Zhiqiang**: AI must ultimately help every individual enhance their capabilities and improve everyone's efficiency. Empowered by AI, everyone will have stronger senses, richer knowledge and experience, thereby enhancing individual capabilities and efficiency, and achieving fair resource allocation in the process. This is the era of human-machine collaboration we hope to see.

## AI Development in the Context of China-US Tech Competition

**Xia Huaxia**: Decoupling or the process of decoupling between China and the US is a short-term pain for China, but a long-term benefit. It will force us to concentrate our efforts on addressing weaknesses, including chips and many other basic components.

**Wu Minghui**: The impact on the software level is currently very small, but I believe the impact on computing power will be significant, including future edge computing. At the same time, I have seen many emerging domestic tech companies rapidly closing this gap. Many underlying areas of AI relate to materials, but even more to mathematics, where China still holds advantages.

From a software development perspective, whether consumer internet or industrial internet companies, many have benefited from open source, and open source itself is not easily affected by international politics. Many outstanding engineers and scientists internationally are co-creating products; this field still has a very good culture of borderless, collaborative win-win. So I feel AI itself will not have so many barriers in the future; collaboration remains the mainstream.

**Chen Haibo**: The current situation is severe, but the sun always shines after the storm. China has its own innovation capabilities; our spirit of self-reliance will be stimulated, and most enterprises have their own contingency plans. For example, we established a Chip Research Institute in 2018 to develop our own `FPGA` chips for industrial intelligence, and we are full of confidence in the prospects.

**Zhou Jian**: In the short term, we have some concerns in the chip field, but in the long run, I believe we will find our own path. UBTECH Robotics started from hardware and began developing servo actuators in 2008. At that time, we searched all over China for small servo actuators but couldn't find any. Finally, we found three suppliers from Japan, South Korea, and Switzerland, with exorbitant prices—a tiny digital motor cost nearly $100. A humanoid robot with twenty or thirty joints would be very costly; a small robot might sell for tens of thousands of yuan. So we started developing servo actuators in 2008, and to this day, I can proudly say that globally, especially for small servo actuators under tens of kilograms, our mass production capability is very leading. Back then we couldn't buy servo actuators, and even if we could, they were expensive; now our servo actuators, whether `PID` algorithms, reducers, or motors, are all self-made. Through more than ten years of persistent effort, we have at least achieved a breakthrough in this field. Sharpening the sword day after day in every sub-field for ten years, I believe chips will eventually break through too; Chinese people are not only hardworking but also very smart.

**Yao Zhiqiang**: The essence of China-US competition is still efficiency competition. The US tries to slow down China's development speed through various means, while we should, under the support of national policies, accelerate R&D speed and application speed, especially all AI enterprises should develop in a cluster, allowing more application scenarios to accelerate landing, and thereby driving basic research work.

**Zheng Qingsheng**: Even under the current macro circumstances, venture capital funds still flow freely globally. China, like the US, remains the most prosperous hotbed for entrepreneurship in the world. Over the past two years, investment in China's hard tech fields, including AI, has been very active.

The potential for domestic substitution on the supply side is also huge. Therefore, while we worry about the unfavorable macro situation, we should also recognize that crisis brings opportunity. China has a vast number of entrepreneurs. In those fields where new enterprises previously seemed difficult to grow, due to new macro opportunities and the emergence of new technologies, a large number of startups have now appeared, which will certainly create many remarkable enterprises.

## Expectations for the Next 5 Years

**Zheng Qingsheng**: I hope that in 5 years, driven by new infrastructure, governments at all levels, all startups, and VC institutions, China's industrial internet (including AI) field can develop to the world-leading status that the consumer internet enjoys today. China will not only be a digital giant but also an intelligent powerhouse.

**Yao Zhiqiang**: I hope that in 5 years, all industries can achieve better generalization of resources, so that not only experts but also ordinary practitioners can accomplish many highly valuable things, helping all industries maximize efficiency.

**Zhou Jian**: Current AI and intelligent robots are still in a relatively early stage, with a considerable distance to general intelligence. Precisely because the industry is not yet mature, the government needs to provide technical environments and scenarios to allow AI and intelligent robots to be better applied. I hope that in 5 years, in Shanghai or elsewhere, we can see the smart elderly care center we built, running the model with 100 beds, and then promoting it to all regions globally facing aging population problems. I believe we can do it.

**Chen Haibo**: AI serves people's livelihood. I hope that in 5 years, Shanghai can become a city outstanding in both AI basic research and application globally, built into a world-renowned AI highland.

**Wu Minghui**: I believe that in 5 years, we will see AI achieve perception, cognition, and even action systems in more and more scenarios, with more complex applications based on AI analysis and decision-making realized, surpassing human capabilities.

![](images/txt022_8.jpg "pagenumber_ebook=192,pagenumber_book=176")

**Xia Huaxia**: From a 5-year time dimension, technologically AI will benefit from impacts in several areas, including "AI+5G", "AI+IoT", "AI+Robotics". The combination of these fields with AI will greatly change many scenarios in life. I hope that in 5 years, drones and driverless vehicles can provide faster and more convenient delivery services in many cities, not just first-tier cities.

## Special Topic: Anti-Epidemic

### Application of Machine Learning Technology for Infectious Risk Prediction on Smartphones

**Yoshua Bengio**

Turing Award Winner, Professor at University of Montreal, Scientific Director of Mila - Quebec AI Institute

![](images/txt024_1.jpg "pagenumber_ebook=194,pagenumber_book=178")

As a professor at the University of Montreal, he won the Turing Award—the highest honor in computer science—for his outstanding contributions to deep learning technology. It is precisely deep learning technology that has sparked the renaissance of AI, gradually making autonomous driving, instant voice translation, and facial recognition possible.

![](images/txt024_2.jpg "pagenumber_ebook=195,pagenumber_book=179")

An important fact about COVID-19 infection is that the main transmission route is close-contact infection. Another important fact is that infected individuals are already contagious shortly before and after the onset of COVID-19 symptoms. The possibility of an individual being infected with the novel coronavirus can be estimated based on whether they have been in contact with a person who tested positive for the virus.

Digital contact tracing refers to sending COVID-19 related information via mobile phones, such as positive test reports of contacts. Compared to digital contact tracing, the limitation of manual contact tracing lies in the need to consume a large amount of time.

The current standard method for digital contact tracing only transmits binary information about whether a person's virus test result is positive. However, the infectious period of COVID-19 begins several days before a positive test result comes out; standard digital contact tracing has a certain delay. If we can shorten this delay, we can effectively reduce the spread of the virus.

In fact, judging an individual's infection risk level is not a simple binary problem of positive/non-positive. COVID-19 has multiple symptoms with varying severity. In addition, previous health status, age, gender, risk level of contacted groups, and time of contact also affect the epidemic progression. Therefore, it is necessary to integrate this information for more complete analysis and judgment.

Analyzing large amounts of information and making judgments is not easy. The best solution is to use machine learning methods to analyze massive fragmented information to determine whether an individual has been infected or what stage of infection they are in. If this can be achieved, those with high infection risk levels need to self-isolate and send risk information to previously contacted people; those with not particularly high risk need to be cautious but do not need to undergo mandatory isolation.

![](images/txt024_3.jpg "pagenumber_ebook=196,pagenumber_book=180")

Digital contact tracing Apps based on machine learning can expand the scope of tracking and alerting. However, it should be noted that digital contact tracing does not completely replace manual contact tracing, because manual tracing can discover interpersonal relationships that phones cannot capture; therefore, the two tracing methods are complementary.

The information accumulated in digital contact tracing tools is also very valuable for public health management. For example, public health authorities can prioritize nucleic acid testing based on digital contact tracing analysis results—people with higher risk or contact history need priority testing. Additionally, digital contact tracing can provide much important information, such as predicting whether a large outbreak may occur before large crowds rush to hospitals for virus testing, based on changes in epidemic risk in certain areas.

If we already know the global network graph of interpersonal contacts, we can use machine learning methods to iteratively obtain the infection risk of each individual in the network. But the problem is, we do not know the global network graph of interpersonal contacts. Another issue to consider is that the computational and bandwidth costs of this iteration may be high.

If the machine learning predictor is simple enough, another approach is to use `federated learning`, i.e., without needing a central training server, everyone trains on their own phone. But a problem with this method is that each phone has to perform multiple iterative calculations, and a large amount of parameter communication exchange is also required between the central server and phone clients.

Our experiment adopts a hybrid method—both transmitting data between phones to calculate risk, and exchanging data and information between phones and servers, thereby better training the risk predictor. The information passed between phones is only updates about personal infection risk; each person can send and receive 4 messages a day. In addition, users who agree to share data can send their data to the server. Using de-identification methods, the sender's phone number, name, and other information are desensitized; the server only obtains the user's symptoms, feelings, and this person's virus test results.

In our experiment, the risk predictor receives the following information inputs: existing diseases, age, gender, behavior-related information (e.g., whether wearing a mask outdoors, in enclosed spaces, or at workplaces; whether there are glass partitions at work; whether taken public transport; how many people live at home), virus test report results, symptoms in the past 14 days, how many people contacted, contact duration, and distance.

![](images/txt024_4.jpg "pagenumber_ebook=198,pagenumber_book=182")

Depending on the individual's situation, the length of data received by the risk predictor varies, meaning the input data length is variable. This means many standard machine learning algorithms, such as the `EM Algorithm`, can no longer handle this data. In our experiment, we used the `Transformer` to solve this problem.

There is still an important question now: what is the prediction target? Or in practical application, what kind of information should one person send to another? For example, if Alice met Bob 5 days ago, and Alice now finds she is infected, what information should she send to Bob? The most useful and relevant information is to assess how infectious Alice was when she contacted Bob 5 days ago. Therefore, the prediction target is how strong the infectivity of the infected person was on each of the past 14 days.

We do not know the true value of infectivity; we can only use test results as proxy information. But even test results have false positive and false negative errors. Fortunately, there are some methods based on latent variables in machine learning to handle this problem.

In the experiment, we built a generative model to capture individual-level information, including how people move, how they get infected through contact, and their condition. We calibrated the model based on known medical information to keep it consistent with known data on population mobility and medical care. Through experiments, we found that compared to standard digital tracing methods (only transmitting positive/negative test results), using the AI digital contact tracing model can significantly reduce the number of cases.

![](images/txt024_5.jpg "pagenumber_ebook=199,pagenumber_book=183")

By using machine learning-based digital tracing technology, we can fully leverage the advantages of both digital and manual tracing, and expand the scope of manual tracing. At the same time, we can achieve the goal without compromising privacy or human rights and dignity.

### AI Healthcare at a Critical Juncture

**Peter Lee**

Corporate Vice President, Microsoft; Head of Microsoft Research; ACM Fellow; Member of the National Academy of Medicine

![](images/txt025_1.jpg "pagenumber_ebook=201,pagenumber_book=185")

Corporate Vice President of Microsoft, leading Microsoft Research and technology incubation; fully responsible for Microsoft's eight global research labs and new technology incubation empowering Microsoft products and business groups, including the rapidly developing healthcare and life sciences business. Possesses rich management experience spanning the entire process from basic research to commercial impact. Before joining Microsoft in 2010, served as Division Director at the US Defense Advanced Research Projects Agency (DARPA), leading R&D of practical technologies such as advanced machine learning, crowdsourcing, and big data analytics. Formerly Chair of the Computer Science Department at Carnegie Mellon University. Board member of the Allen Institute for AI and the Kaiser Permanente School of Medicine. ACM Fellow, served on multiple national research institutions. Served on the US President's Commission on Enhancing National Cybersecurity.

The sudden outbreak of COVID-19 this year has made healthcare and public health the focus of the world, significantly increasing investment in all aspects of this field. Besides life sciences and medical research, technologies such as AI and big data have also played a major role in epidemic tracking and monitoring, refined control, and telemedicine.

The combination of AI and healthcare has always been a focus for Microsoft. How to fully utilize emerging technologies like AI to achieve balanced development of technology, humanity, nature, and society requires joint efforts from all sectors of society. The current stage is a critical moment for the technology and healthcare industries; the future direction will depend on every choice made today.

#### Infinite Possibilities of AI Healthcare

When a doctor draws your blood, they get about 1 million T cells, which are samples of the body's natural immune system. The specificity of each T cell is determined by the genetic mechanism of the extracellular receptor (or T cell receptor), so there is currently a lot of research attempting to massively decode the information conveyed by these genes. If this can be achieved, it can map the T cell atlas in the human body, diagnosing infectious diseases, cancers, autoimmune diseases, etc., occurring in people. This will also become the basis for a universal blood-based diagnostic method.

![](images/txt025_2.jpg "pagenumber_ebook=202,pagenumber_book=186")

**T Cells**

Currently, Microsoft is collaborating with a Seattle biotech company, Adaptive Biotechnologies, in this area. Its proprietary immune sequencing technology can rapidly read about 1 million T cells in a blood sample. The vast amount of information covered can be used to understand what infectious diseases, cancers, or other immune diseases the human immune system is responding to. The key is whether this information can be decoded. This is precisely where AI and machine learning play a role. Microsoft has been using machine learning technology to process these outputs, attempting to convert the T cell receptor language into antigen language, and further using machine learning to probe these immune engines to find out what diseases the body is fighting.

![](images/txt025_3.jpg "pagenumber_ebook=203,pagenumber_book=187")

**Building the Human Adaptive Immune System**

The ultimate dream of the collaboration is to use a simple, universal method to detect any infectious disease based on blood tests, and to perform early diagnosis of any cancer, determining what immune diseases a patient might have. As a researcher and a fan of sci-fi shows like *Star Trek*, this research reminds me of the "Tricorder". The current era is indeed remarkable; medical science development will bring infinite possibilities, and today's computing technology and capabilities allow data to help people have better health.

New concepts are triggering infinite possibilities, while the application of AI and other technologies in precision medicine is only a small part of future healthcare.

Besides future-oriented research, AI's optimization of the current diagnosis and treatment process is also very important. Doctor burnout is very common in many countries; doctors usually focus more on computer screens than on patients. Medical diagnostic records have now been digitized, but the problem is that the heavy burden brought by digitization has fallen entirely on doctors and nurses. Multiple studies show that clinicians currently spend over 40% of their working time on data entry; the burden of creating clinical documentation has become one of the main causes of doctor burnout. Even in many countries, doctors are among the professions with the highest suicide rates.

Microsoft, together with partner Nuance, developed a product called `Dragon Ambient eXperience`. It can observe and listen to the conversation between doctor and patient, then automatically construct the vast majority of the clinical record. In early deployments, doctors reported high satisfaction, and patients also gave good trust to the machine that listens to the conversation and assists the doctor.

![](images/txt025_4.jpg "pagenumber_ebook=204,pagenumber_book=188")

**Hospital Consultation**

#### Fighting the Novel Coronavirus with AI

The outbreak of COVID-19 has seriously affected people worldwide. How to respond to the disease and the social problems and global economic contraction it triggered poses a challenge to everyone. The good news is that organizations worldwide have mobilized to jointly respond to this crisis. The spirit of cooperation, sharing, and collaboration is inspiring and unprecedented in history. At Microsoft, everyone is also committed to working with partners to jointly combat the COVID-19 pandemic.

First, Microsoft deeply collaborates with medical institutions to help them face the surge in patients, reorganize operational processes, and provide sufficient infection prevention and control capabilities.

In Seattle, the Providence Health System treated the first local COVID-19 patient in the US, so they had to deal with many thorny issues very early on. One of Microsoft's collaboration projects with them was to use the AI chatbot technology `Microsoft Health Bot` to develop a bot named `Grace`. It can interact with patients at home and intelligently provide advice, letting them continue resting at home, or transferring patients to doctors or nurses for real-time health consultation to further verify the situation.

Subsequently, Microsoft also collaborated with the Centers for Disease Control and Prevention (CDC) to launch this bot service for users nationwide on the CDC official website. Since then, over 1,900 websites globally have deployed this medical bot. So far, 39 million people have used the bot to assess their symptoms. Most medical institutions using this medical bot service reported that visits or inquiries related to COVID-19 in their emergency departments, call centers, and telemedicine services decreased by at least 30%.

![](images/txt025_5.jpg "pagenumber_ebook=206,pagenumber_book=190")

**Microsoft Healthcare Bot**

At the same time, the biggest challenge for medical institutions in the early stage of the epidemic was admission capacity and its utilization. Hospitals needed sufficient beds, ICUs, ventilators, and personal protective equipment. But how can multiple systems in a hospital collaborate, coordinate overall, and better optimize processing capacity on demand?

On the `Power Platform`, Microsoft rapidly deployed the Hospital Emergency Response Platform. It allows hospitals to easily track COVID-19 patient admission capacity and utilization, and share it across the entire health system. This platform can also automatically report relevant information to the government for public health reporting aggregation, and then use `Azure AI` data analysis to predict future demand. Now hundreds of hospitals in the US and around the world are using this easy-to-deploy solution.

Second, telemedicine is also a good auxiliary method, and `Microsoft Teams` is becoming an AI assistant for doctor-patient conversations.

Microsoft is committed to simplifying the creation process of new telemedicine services while meeting all requirements of medical institutions and patients for privacy, security, and compliance.

![](images/txt025_6.jpg "pagenumber_ebook=207,pagenumber_book=191")

**Hospital Emergency Response Platform**

Today, `Microsoft Teams` meeting systems for medical use handle millions of telemedicine visits every day. Any hospital or clinic can easily use Teams to create new telephone consultation services. At the same time, more and more AI technologies are being integrated into Teams for automatic transcription, intelligent search, image processing, voice interaction, etc. `Microsoft Teams` is becoming an AI assistant for doctor-patient conversations.

Third, AI technology can not only be used to provide medical services; advancing medical research and disease treatment is also fertile ground for AI.

