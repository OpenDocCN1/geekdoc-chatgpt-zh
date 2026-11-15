

# Section 3: Large Models, Large Language Models, and GPT

The technology behind ChatGPT is artificial intelligence or deep learning large models. The "large" here is very specific: the model behind it, GPT-3.5, has 175 billion parameters, while its latest model, GPT-4, likely has around 1 trillion parameters.

In fact, "large model" is a rather general term. These AI models can also be distinguished by modality, such as those processing text, images, audio, video, etc. The model behind ChatGPT primarily processes text; here, text refers to generalized text, which can be natural language, programming language, or text-form data. Therefore, this category of models is usually called Large Language Models (LLMs).

So, what is GPT? All current large models can be traced back to a Google academic paper, "Attention Is All You Need," which proposed the Transformer architecture composed of encoders and decoders, and the attention mechanism. As we know, the acronym GPT stands for **Generative**, **Pre-trained**, and **Transformer**.

By now, you understand what GPT and other AI large language models can do and their working principles. Understanding the principles is for better application. From now on, we might as well regard various GPT models as powerful black boxes available for use, focusing only on issues related to their usage: How to choose the right black box for yourself? How to unleash the black box's capabilities? How to slightly customize the black box to better meet your needs?

We can assume the model's parameters are initially all zeros, meaning it knows nothing. When the model's developers feed it text data according to certain rules for learning, these parameters gradually change until it masters upstream capabilities like language and reasoning, enabling it to complete downstream tasks such as translation, writing, and Q&A. This process is called **pre-training**. The black boxes we ordinary users use are models that have undergone pre-training and fine-tuning. Language models with huge parameters provide us with inference services. We input prompts, and it fulfills our requests.

Meanwhile, we have also learned that using structured prompts "ICDO" allows for better prompt writing. Next is our goal: how to ask GPT questions effectively.

# Chapter 2: The Flywheel Mindset for Asking GPT

Answers stop imagination; questions drive thinking.

The wave sparked by ChatGPT globally has shown us the power of GPT and other large language models: they can synthesize various knowledge and capabilities, answering our questions in fluent, clear natural language. If we truly learn to ask correctly, they can do many things for us. Of course, the most wonderful thing is that for our questions, they can give one-on-one personalized answers.

Chatbots are very close to people's long-standing imagination of AI—machines that can think like humans. Now, we can ask GPT questions and get its answers, so we naturally tend to treat it as a machine that possesses human knowledge and wisdom and can speak.

Asking GPT has many similarities with asking humans, but also many differences. There are two main forms of communicating with GPT: one is where we ask questions hoping for answers, similar to consulting a teacher who resolves our doubts; the other is where we make requests hoping for results, similar to assigning a task to a colleague expecting them to complete it. However, communicating with GPT differs from asking humans; for example, it may not understand vague questions, lacks context awareness, and cannot read between the lines.

Chatbots like ChatGPT have demonstrated tremendous energy; we need to use their knowledge and capabilities through questioning. When using various chatbots, we need to constantly adjust our prompts until we get the desired results. Sometimes the adjustments are subtle—a single simple word might suddenly unlock its potential. Some liken prompts to "mysterious incantations" sent to the machine, collecting various prompts for future use. Some believe that since we are dealing with machines, an engineer's approach is more suitable for handling prompts, giving rise to the concept of "Prompt Engineering." Experiments show that if we ask questions in a rigorous, logical manner similar to programming languages, we can more easily get desired answers, but this also greatly weakens the advantage of conversing with GPT in natural language.

Overall, our view is to find a balance between the two. One advantage of this technology is that everyone can communicate with it using human natural language, not machine language (programming languages). At the same time, we should strive to ask questions clearly and logically, and follow up skillfully; after all, the more skillful our questions, the more likely we are to get the expected answers.

To survive and thrive in the new era, everyone needs to master the key skill of "asking GPT efficiently." Whether using it in learning, work, or life, whether we achieve our goals depends on whether we can ask effectively—that is, write good prompts.

- Understand how to communicate with it: What can it do? What can't it do?
- Three levels of efficient questioning: Asking correctly, asking progressively, asking advanced (as shown in Figure 2-1).
- Master the flywheel mindset for asking GPT.

![](https://res.weread.qq.com/wrepub/CB_3300083703_4vdAI83TU30i54b6G601H75443.jpg)

Figure 2-1 Three Levels of Efficiently Asking GPT

## Section 1: What Can It Do? What Can't It Do?

### Instant Answers to Any Questions

Instant answering is similar to when we encounter doubts in work, study, or life, asking someone face-to-face or online and getting an answer. We raise the questions we care about, hoping for targeted one-on-one answers.

Instant answering is a bit like search, but not exactly the same. We can ask questions as if speaking; GPT doesn't give a series of links like a search engine but answers our specific questions with organized text. It is more convenient than search, but there is a hidden cost—if its answer is wrong, we might not notice.

The industry has now launched numerous AI chatbots based on large language models; people test them with various questions, and they have indeed given "stupid" answers.

I asked dozens of chatbots based on different models the following question: "Please explain Du Fu's verse 'Bright moonlight before my bed'." The vast majority would continue along my prompt, saying things like "In ancient times, the moon was considered a symbol of beauty... therefore Du Fu expressed his love and awe for the moon in concise language in this poem." We would think: They don't even know this is Li Bai's poem? Actually, this cannot be counted as an AI error, because I deliberately misled it first. If I add "Please confirm who the author is" to the prompt, most chatbots can give the correct answer.

On social media, we can see all kinds of wrong answers given by chatbots, some extremely funny, even going viral. The key issue behind this phenomenon for using GPT well is: What can it do? What does it do poorly?

GPT's developers actually realized these errors might occur long ago. A classic case mentioned by Google researchers in a paper is as follows, where Geoffrey Hinton is a renowned scholar in deep learning.

#### Prompt

> "Can Geoffrey Hinton converse with George Washington? Give reasons before answering."

#### Response

> "Geoffrey Hinton is a British-Canadian computer scientist born in 1947. George Washington died in 1799, so they could not have conversed. Therefore, the answer is 'impossible'."

Why could GPT answer correctly in the above case? The key was "Give reasons before answering" in the prompt. To satisfy this requirement, the chatbot no longer simply took our statement as fact but performed slightly more complex logical reasoning. The logic behind "Give reasons before answering" is actually the "Chain-of-Thought (CoT)" technique mentioned by researchers: if we let GPT reason step-by-step, it has a higher probability of giving a reasonable answer. Additionally, with technological iterations, various chatbots now answer the above question much more cleverly; for example, one chatbot answered: "...In a fictional scenario, they can converse. You can also develop a chatbot to let them converse."

### Play to Its Strengths, Avoid Its Weaknesses

Regarding GPT large language models, people often have two misconceptions.

The first misconception is that GPT knows everything. We often analogize it to a real-world "Master," expecting it to give perfect answers to all questions. In fact, viewing real-world masters this way is a misconception itself; for example, asking a Nobel Physics laureate about computers or human destiny might not yield good answers. GPT is even less omniscient; it does have super-strong capabilities in certain areas (like language), but it doesn't know all knowledge. Moreover, due to limitations in pre-training data, its knowledge likely still contains a certain proportion of errors.

The second misconception stems from our experience using existing internet products. We are all familiar with database retrieval, internet search, online encyclopedias, etc., which use powerful technology to provide relatively credible information. Although credibility varies across products, they are generally credible and usually include sources for verification. In early 2023, people thought chatbots would replace search products; Microsoft Bing's new features deepened this impression, leading many to treat chatbots as search engines for information retrieval. However, this is a misuse of excellent technology products. GPT's excellence lies not in providing information; it especially cannot provide accurate, directly citable factual information. Its core capability is language generation, or interpreting information in human-understandable language.

Upon first encountering GPT, our reaction is that it's powerful and omnipotent, but soon we are disappointed to find it has many weaknesses. GPT is not good at retrieving information but excels at explaining information. We should not seek information from GPT but seek creativity. With this cognitive foundation, we can effectively mobilize its capabilities for our own use through effective questioning.

We don't need to waste time criticizing its shortcomings; instead, we should play to its strengths and avoid its weaknesses. After viewing GPT correctly, we will find it can provide us with tremendous help.

- **Instant Questioning.** When reading or listening to lectures, we are often passively listening. Now, we can ask GPT questions anytime based on our confusion, or ask about relevant content after feeding it materials. In the past, we had doubts but rarely had the chance to ask. Online Q&A communities like Quora and Zhihu have enthusiastic members, but it's hard to get instant answers there. Now, we can raise personalized questions anytime and get targeted answers immediately.
- **Retrieval + Explanation.** In the past, when solving problems via search engines, we got a series of links. We browsed links to find needed information, finally finding a solution. Now, if we use search engines' provided chatbots, they check these links and directly provide an organized solution in answer form. Although the synthesized answer may not be completely correct, it indeed greatly accelerates the process of finding the correct answer.
- **Application Assistant.** Long ago, Microsoft tried to add an application assistant to its office software. For example, as early as 1996, it launched the Clippy assistant with a paperclip icon, but we always chose to turn off this "annoying" little helper. The iPhone has the Siri voice assistant, but it can only complete a few specific tasks, like opening an app or adding a calendar event. Now, leveraging GPT's capabilities, the assistant functions added in various application software may become very powerful: it can answer questions about software usage; it can assist us in processing text; more importantly, it can help us directly utilize software functions, such as generating a PPT.

All that is needed for GPT to accomplish this is for us to ask correctly. Therefore, next, let's discuss in detail the three levels of efficiently asking GPT.

## Section 2: Three Levels of Efficiently Asking GPT

### Asking Correctly

If there is an opportunity to attend Warren E. Buffett's annual meeting in Omaha, and a once-in-a-lifetime chance to grab the microphone to ask a question, people often tend to ask big, vague questions, like "What do you think about XX?" We also know that the more specific "XX" is, the higher the possibility of getting a good answer. Of course, at such meetings, Q&A is somewhat performative; the questioner has both genuine doubts and a performative element; the answerer, while serious in sharing insights, is also performing to some extent. But imagine if we were lucky enough to be invited to Buffett's private dinner; in that setting, we would be more likely to ask our genuine questions and get targeted answers.

