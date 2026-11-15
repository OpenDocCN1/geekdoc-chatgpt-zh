
### Advanced Questioning Techniques: Combining with Other Tools

When we ask GPT questions, the goal is to have it generate unique text that can be utilized by other software, tools, or APIs.

We can have GPT generate unique text that other tools can recognize and adopt; furthermore, we can take this a step further. We can leverage GPT's language and logical reasoning capabilities to string together the entire process of completing a task, at which point it becomes a central hub that listens to our instructions and calls other tools. In other words, it means using a large language model to connect various tools, giving it a goal, and letting it optimize and make decisions on its own. `Auto-GPT` is a typical example; it utilizes the capabilities of the `OpenAI GPT-4` language model while connecting various tools to automatically accomplish set goals.

There is another advanced questioning technique that everyone can use immediately: multi-round processing of a single query. Unlike the "decompose tasks, ask step-by-step" technique in intermediate questioning, here we not only split the task into several steps but also perform multi-round complex operations—some done by GPT, some by us—ultimately completing a complex task through human-GPT collaboration. This is not a machine's "solo act," but a pas de deux between human and machine.

### Advanced Questioning Techniques: Multi-Round Interaction for Complex Tasks

Engaging in multi-round interaction with GPT, where some steps are completed by GPT and others by humans, with human-machine collaboration jointly completing a complex task.

Many commercial AI products now follow this logic, performing multi-round processing behind the scenes for us. For clarity, let's first illustrate with a simple example of editing an article. For instance, we have an article excerpt and ask GPT to help optimize the phrasing to make it easier to understand. Unlike the usual approach, we can proceed as follows (this may require some tool assistance but can also be attempted via multi-round dialogue).

After sending the article to GPT, we can make the following requests step by step:

1.  First, evaluate whether the phrasing is easy to understand and provide three possible directions for revision.
2.  Second, for each suggestion, provide two revised versions: one minimizing changes to the original wording; the other allowing major revisions.
3.  Then, request that the revised versions be stylistically optimized, for example, by emulating a particular writer's style.
4.  Finally, check the grammar, recommend synonyms for repeated words, and give the article a final polish.

In the process of using GPT to edit the article above, we didn't ask GPT to answer just one question, but many. If the tool we use provides such an interface, we might also need to adjust GPT's calling parameters. For example, if we want to see if the revised version differs significantly from the original, we can change the temperature parameter in the calling parameters from the usual `0.7` to `1.0`, and we will see its expression become more diverse. Even though the same prompt is input, the results are very different each time.

Figure 2-2 shows a more complex hypothetical case: we attempt to use GPT as the core of an AI travel consultant. First, a client asks the AI travel consultant to design a travel plan; GPT starts a dialogue with the client, inquiring about their needs in detail. Second, GPT analyzes the client's needs and decides which tools to use. GPT generates text suitable for each tool, such as commands to call a database or an API. In Figure 2-2, we assume GPT will call an internal database to query proprietary plans, perform a web-wide search for the latest scenic spot info, call an external knowledge base (e.g., querying travel notes on Xiaohongshu), and call ticketing platform APIs to retrieve flight and hotel booking info. Then, after collecting all materials and information, GPT generates a draft of the required travel plan and submits it to the client for feedback. Finally, upon user confirmation, GPT generates the final travel plan and completes bookings for flights, hotels, and guide services.

![](https://res.weread.qq.com/wrepub/CB_3300083703_4vdAI83fs30b54b6G601H06ffc.jpg)

Figure 2-2 AI Travel Consultant: Using GPT to Design Travel Routes for Clients

In this comprehensive usage example, we employ at least two advanced questioning techniques: combining with other tools and completing complex tasks through multi-round interaction. We can see that, on one hand, GPT plays a core role in multiple processes when completing such comprehensive tasks; on the other hand, the professional user of GPT is also crucial—it is the professional user who strings everything together, ultimately completing the task together with the machine.

### Customizing Your Own Tools Through Advanced Questioning

The ability to customize one's own tools is the standard that distinguishes experts from general users.

Everyone's phone and computer differ from others', containing their own chosen software, shortcut settings, and personal preferences. When using GPT, we can similarly make many settings, even customizing our own unique usage methods. When we combine GPT with other tools, customize them according to our needs, and integrate them into our workflow, they unleash tremendous power.

## Section 3: The Flywheel Mindset for Questioning GPT

We ask questions; AI gives answers. How can we use questioning to get the maximum help from AI? Here, we propose a, evaluate, refine the question, ask again, until a satisfactory answer is obtained. But before starting to ask directly, we notice there is a preliminary step—setting the role.

In fact, when we don't set any role, GPT uses a default setting; for example, the `ChatGPT` chatbot is set as a helpful assistant. If we access via the programming interface, we can explicitly see this default setting. Generally, we can assume that various GPT models have undergone alignment with human values during service provision; they are set as "`HHH Assistants`," the three "H"s standing for `Helpful`, `Honest`, and `Harmless`.

Below are relevant prompt fragments for `ChatGPT`'s role setting (originally in English), obtained by asking it directly.

```
ChatGPT旨在通过提供的内容，生成类似人类语言的文本。它经过训练，利用各种数据，包括书籍、文章和网站，形成对人类语言的广泛理解。

作为一个AI语言模型，它可以回答问题、参与讨论、提供解释并协助完成各种任务。但需要注意的是，它是人工智能，没有个人经历或情感。
```

We can also see that `ChatGPT` has some settings regarding its own limitations and how we should use it.

```
尽管我努力提供准确的最新信息，但是请记住我的知识都是2021年9月之前的。因此，我可能不了解该日期之后发生的最新事件或进展。

在使用ChatGPT时，提出具体而明确的问题非常重要，这可以确保获得最佳回答。模糊或含糊不清的查询可能导致不准确或不完整的答案。

在必要时，请务必对我提供的信息进行批判性评估，并与可靠的来源进行交叉参考。尽管我致力于提供帮助，但无法保证所生成信息的准确性或可靠性。
```

In the prompt, we can also set GPT's role according to our needs to enable it to answer better. Usually, we set the role to define its knowledge and capability scope and its way of answering. In early 2023, most materials teaching how to use `ChatGPT` were largely compilations of role-setting prompts. Here, we use an English teacher as an example to illustrate how to set a role.

```
你是一个以英语为母语、又懂中文的英语老师。
```

We can explicitly set its task so we don't have to repeat the setting every conversation.

```
你是一个以英语为母语、又懂中文的英语老师。当我输入一段英文时，请帮我修改为接近英语母语的表达方式。当我输入中文时，请帮我用英语重说一次，注意不是翻译。
```

We can also set more specific rules, such as what level of English vocabulary to use, whether to use a business or academic writing style, etc.

```
……请选用易懂的英文单词，避免使用复杂的句式，使用场景是工作中的交流。
```

Role setting is the foundation of questioning GPT. We can also understand it this way: role setting uses these words to specify GPT's step, it's GPT's job, and we can rest. Not so. To get maximum benefit from GPT, we need to pay special attention to this step.

When we ask, our prompt shouldn't just contain the question; it should also include a key element: requirements for the generated result, also called output prompting. We will detail this in Chapter 3. Sometimes, output requirements are implicit in the question, e.g., "What is the Feynman Technique?" "Please rewrite this text into a 140-character Weibo post." More often, we explicitly state output requirements, e.g., "Write code for the above task in `Python`." "Give 10 options, numbered 1.2.3." If we want GPT's answer to be usable by other software, we also need format requirements, e.g., "Present in a `Markdown` table, and wrap the table in triple backticks (` ``` `)." Usually, if the answer format isn't what we want, we immediately modify the output prompt in the question to see how the answer changes.

### Evaluating

We ask; GPT answers. Is the answer good? Can we use it? Evaluating the answer is our responsibility. When using GPT, we easily fall into "two ends of the spectrum." One end: glancing at results, feeling amazed: "Wow, it really writes poems." "It really does math." "It really codes." The other end: denying it: "Huh? How did it get this math wrong?" "Why does the code use a non-existent interface?"

GPT's answers are usually not perfect; we must remember this when using GPT. This may be due to two reasons. First, our question isn't accurate enough, or our questioning method is flawed. An interesting example: GPT sometimes gives correct but overly brief math answers, sometimes completely wrong. But if we add one sentence to the prompt, it might work wonders: "Let's think step by step." Research analyzes that this sentence can increase GPT's math accuracy by 61%.

The second reason is GPT's inherent limitations. We often see amazement at GPT's abilities, but we must not ignore that it's far from perfect: its answers are often too general; English-to-Chinese translations may completely miss the meaning; its info doesn't withstand strict verification; it may even fabricate non-existent info.

Whether to accept GPT's answer is the questioner's responsibility. We must ask ourselves: Am I satisfied with this answer? If not, why? If re-asking, how should I modify the question? An interesting trick: we can have GPT evaluate itself. You can follow up with:

-   "Are you completely sure of this answer?" If wrong, it will immediately realize and give a corrected answer.
-   "Please evaluate this answer yourself, give a score and reasons on a `0~10` scale." Its self-evaluation helps us analyze and judge its answer.

Of course, we can also provide supplementary info and then ask it to self-evaluate and correct:

-   (Previously asked to do a primary school math problem) Your calculation just now didn't include the number of teachers.
-   (Previously asked to analyze a business problem) Supplementary info is as follows: ... Are you now sure of your previous analysis?

GPT's answers aren't extremely right or wrong; more often, just some aspects don't meet our requirements. This leads us to the next step in the loop—refine the question, ask again.

### Refining the Question

Asking GPT, we rarely get the perfect desired result in one go. In most cases, we refine the question and ask again. Generally, we can refine from three aspects. In the following examples, we assume the questioner is a parent tutoring a primary schooler, wanting GPT to answer a math problem like the "Seven Bridges of Königsberg".

-   If the question itself has errors/omissions, add more specific info. E.g., because it's hard to describe the Königsberg bridges graph in text, leading to wrong answers. If we misspoke, correct and re-ask.
-   If the answer format is unexpected, add output requirements: "Please solve in a way a 6th grader can understand." "Please list steps numbered `1.2.3.4.`"
-   If the solution has deviations, correct the specific point. E.g., in an experiment, we found GPT miscalculated whether the number of connections at a node in the Königsberg problem was even or odd; pointing out the error and asking for recalculation sufficed.

Refining and re-asking here can be a completely new question or a follow-up to the previous answer. For simplicity, this book mostly uses a Q&A format in examples. Describing the Königsberg bridges graph in text is difficult. When writing this section, I tried to have GPT describe the Königsberg problem in text to see if it could describe the math problem clearly. Initially, it always gave answers about history, Euler's story, etc., not what I wanted. This was because my initial prompt was "Give a textual description of the Königsberg problem." Later I added: "Name the islands and land areas, then list which pairs have bridges connecting them." But the answer was still hit or miss. Adding this sentence made it work every time: "Please ensure your description is clear enough for a reader to draw the graph based on it."

-   What goal to achieve with it?
-   How to ask or make requests?
-   How to evaluate answers? How to adjust subsequently?

In short, starting from role setting, our Q&A with GPT is a loop: ask, get answer, evaluate, refine. After multiple loops, we usually get the ideal answer we want. Finally, we judge, accept an answer from GPT, and end the questioning process.

![](https://res.weread.qq.com/wrepub/CB_3300083703_4vdAI83k030e54b6G601H0ccd7.jpg)

Figure 2-4 Q&A Loop with GPT

In interacting with GPT, we need to focus on 3 key points: What goal to achieve? How to ask/request? How to evaluate and adjust? The subtlety is: don't obsess over whether your question is good; we can repeatedly modify our question until we get a satisfactory answer. Also, after getting GPT's answer, we usually still need further adjustments before practical application.

### Getting Good Answers Through Iteration

We rarely get a good answer in one go; usually it takes repeated questioning. When asking a person, we might stop after a few questions. But when asking a machine, we should keep asking until we get the expected answer. The key mindset for questioning GPT is the iterative loop: "Ask—Answer—Evaluate—Modify," to approach the best answer. We can see this loop is similar to Lean Startup's "Learn—Build—Measure" iterative loop.

### Issues to Note When Using GPT

1.  It doesn't know the latest information.
