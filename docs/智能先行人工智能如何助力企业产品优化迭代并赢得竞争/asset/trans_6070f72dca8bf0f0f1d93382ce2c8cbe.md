

# Outsourced and Crowdsourced Data

Outsourced or crowdsourced workers both create and label data. They can gather data in various ways: for example, making phone calls, searching online, manually copying information from websites, and other methods that can be reduced to standardized, discrete, and short tasks. They use interfaces provided by marketplaces to label data, interfaces that both add labels and track their work. Some marketplaces offer human-in-the-loop systems to label data. Workers can also process data effectively by deduplicating data lists, correcting typos in text lists, or discarding blurry images.

The cost of outsourcing is reduced specialization. Outsourcing businesses aim to serve a broad range of clients, so they have labels for all sorts of content, including characters in movies and cars driving on streets. These annotators have not been trained to identify specific objects, such as a particular car model or disturbances in a running machine. However, considering the tools they build for annotators and the prices they negotiate, outsourcing can be cheaper than insourcing. Companies tend to experiment with both insourcing and outsourcing, balancing between the two over time based on data labeling needs.

Outsourcing can take many forms. There are consulting firms, business process outsourcing (BPO) companies, and other firms engaged in custom labeling businesses. Additionally, crowdsourcing businesses are booming, operating in the form of small, single-fee tasks. The previously mentioned Mechanical Turk and Upwork are providers of such task-oriented businesses.

# Machine-Generated Data

Machines can generate data for models consistently, quickly, and cheaply, serving both as a supplement to human-generated data and as a standalone resource. Machine-generated data is shown in Figure 3-10.

![](EPUB/images/image_035.jpg)

Figure 3-10 Machine-Generated Data

## Simulation

Software engineers use simulations to verify the correctness of program functions, such as simulating all paths in a video game maze or the way electrons flow through a circuit board. Simulations can be used to test software. Programs contain logic rules written by humans, but we cannot always anticipate all the inputs a program might need. Therefore, we write simulation scripts to generate inputs, feed them into the program, and observe whether it works.

With the advent of the AI era, software engineers discovered another use for simulation. AI contains models written by humans or machines, with various functions. These functions can change the software's capabilities without further design work because they learn from inputs. That is the difference: simulation finds faults in traditional software, but in AI, it improves software performance.

Therefore, simulation is a key means of advancing AI, particularly reinforcement learning and other agent-based learning AI. These models typically need to try many different approaches, and because existing datasets are insufficient or unavailable, simulators become extremely useful. Model designers set a goal and an environment for these models; they can try many different methods in this environment—via a simulator—to achieve the goal, which contains the real-world constraints the model must ultimately face. If the model achieves the goal in the simulated environment, it can apply the learned knowledge to the production environment to make predictions, thereby making real decisions.

## Agent-Based Models

How do we generate data about complex environments? When `y` responds linearly to `x`, it is easy to model. However, when the number of functions or even variables is unknown, modeling the behavior of `y` becomes very difficult. Complex economic and biological systems have many variables and produce an impact with every action. Modeling the behavior of agents at the micro-level of each decision can help us understand what might happen elsewhere in the system at the macro level. Understanding breaking points, path dependencies, and other critical factors in the system allows for a better understanding of their potential consequences. Agent-Based Models (ABMs) can do this.

Given a set of incentives and environmental constraints, agent-based models model the behavior of agents with reference to the behavior of other agents. This field draws on knowledge from game theory, sociology, evolutionary theory, and theories of complexity and randomness. Creating an agent-based model requires figuring out how many agents are in the system, how they make decisions, how they learn from their own behavior, and how these behaviors affect the behavior of others in the system. Agents follow programmed rules. Sometimes, programming agents requires specific domain expertise—understanding the "rules of the game" or system principles. Programmers use adversarial learning and reinforcement learning techniques to create agent-based models. Conway's "Game of Life" and games solving the Prisoner's Dilemma are popular agent-based systems.

Financial and political institutions also frequently use agent-based models. For example, the Bank of England uses agent-based models to simulate the impact of policies on the real estate and credit markets. The impact of mergers and acquisitions on competition can be effectively modeled through simulation. Using agent-based models for macroeconomic forecasting is significant for banks, asset management firms, and traders and market makers at hedge funds. Governments also use these models to assess the potential impact of environmental policies.

Agent-based models differ from simulations. Agent-based model learning can be viewed as a superset of simulation: agent-based model environments are loosely defined, while simulation environments are strictly defined. We can also see the difference from another angle: agent-based models study behavior, while simulators test specific outcomes.

## Synthetic Data

Entirely new data can be synthesized by designing output rules for a set of data points. These rules need to be created in a prescribed manner—for example, all tables must have four legs—or learned from existing datasets, such as letting a model recognize that all chairs also have four legs. Synthetic data possesses the structure, concepts, types, and dependencies of existing datasets. The difference between synthesis and simulation is that simulation is used for verification, while synthesis is a creative process.

Synthetic data has many uses. Engineers wanting to test new product features on customer data often use real customer data. This can lead to erroneous operations or unauthorized viewing of live customer data. Salespeople wanting to demonstrate a functional product often cannot do so without the product's underlying data. Usually, they demonstrate the product only with accurate, clear customer data, which may expose customer information to competitors. Besides these two cases, any handling of live customer data can lead to errors, resulting in security vulnerabilities. The safest option is simply not to use customer data for anything.

Synthetic data is particularly useful when data labeling is too costly or too difficult. As described below, most example data used for demonstration relates to images and computer vision models, but the dimensions apply to other data types as well.

### Scalability

Labeling objects of a certain shape—for example, chairs—is feasible because the vast majority of people can recognize this basic object. However, considering all potential variations in shape, such as recliners, dining chairs, office chairs, etc., these objects differ slightly. Training a model to recognize chairs means recognizing all types of chairs; therefore, training data must contain all types of chairs. Acquiring images of all types of chairs and having people label them is expensive, even infeasible. Considering the different materials used to make chairs, slipcovers that might make chairs unrecognizable, etc., the situation is even more so. Synthetic data generators can use data on the basic components of chairs—legs, slipcovers, with or without backs—and then run all variations and combinations of these components to generate thousands of examples at very low cost.

### Flexibility

Labeling an object from one angle is feasible because most objects look the same from a certain angle, like a house viewed from the street. However, viewed from a different angle—for example, from above—they look slightly different. If there is a steady stream of data from a moving camera, such as one on a drone, then training a model to recognize objects from any angle is necessary. However, multi-angle observation of the same object greatly increases data acquisition costs. Synthetic data generators, by modeling a single object in 3D, can provide infinite perspectives, move around it, and generate a labeled data point at every step.

### Accessibility

Labeling objects is usually feasible because their photos are easy to obtain, like cars on the street. However, some objects are hard to find, like photos of tanks in active combat zones. Acquiring data to train such a model can be costly or nearly impossible. Synthetic data generators can acquire manual models of such objects and place them in various environments. Building such a generator may cost a lot, but that cost can be amortized across all labeled data points, because one generator can produce multiple examples of the same object. The tools used to build these generators are the same as those used by video game designers to build game environments.

### Feasibility

Labeling objects is feasible because they are as common as waves hitting the shore. However, some objects are rarely seen in the real world, like explosions caused by lightning. Without extensive historical records or long waits, acquiring data to train such a model is usually infeasible. Synthetic data generators can select a scene and produce it on demand.

### Cost

Synthetic data can be cheaper than human labeling because programs that generate large amounts of images can run on computers at low marginal cost. However, this is not always the case—for example, generating synthetic images requires 3D models, which must either be purchased or drawn by experienced computer graphics artists.

### Speed

Synthetic data can be generated faster than human labeling. Data labeling takes time; the process includes packaging data to be labeled, sending it to annotators, waiting for them to finish, checking, occasionally relabeling, sending it back, and retraining the model with incremental labels. Data synthesizers can generate data almost immediately and send it into the data pipeline.

Therefore, synthetic data is very useful for generating unstructured data—for example, images—but less useful for generating structured data—such as user information. Synthesizing images of multi-dimensional chairs will be relevant to many different customers because chairs are common objects in the world we share. However, synthesizing data representing a specific set of users is irrelevant to many other customers, because those users do not use the same profiles across customers. Synthesizing data for one use case only requires writing one program. Synthesizing data for multiple use cases, if these use cases share a common reality like the physical world, also only requires writing one program.

# Consumer-Generated Data

Given the right incentives, consumers will provide data. This differs from customers providing data in that customers pay for AI-first products, while consumers only get the output of that product. Consumer-generated data is shown in Figure 3-11.

![](EPUB/images/image_036.jpg)

Figure 3-11 Consumer-Generated Data

## Token-Based Incentives

Blockchain-based ownership tokens or crypto tokens can be used to incentivize people to submit data to a network. Since these concepts are relatively new, it is necessary to explain some terms.

**Blockchain:** A decentralized, distributed public ledger of transactions.

**Crypto Token:** An asset identifier stored on a blockchain.

**Data Network:** A set of data built by a group of originally unrelated entities rather than a single entity.

The idea is to distribute a crypto token to a group of people, who then submit data to, or sell it to, the data network and earn returns.

Blockchain offers two benefits in building data networks. First, blockchain can verify the provenance of data. Data submitted by each entity on the blockchain is assigned a unique identifier. When there are questions about when data was submitted, or even if it was submitted, the blockchain can be queried. When the blockchain has enough entities verifying transactions, the data provenance is absolutely certain. Second, crypto tokens are a secure, automatic way to pay data contributors. Payment calculation is a serious and transparent mathematical problem. Token owners are compensated based on their data contribution to the network, verified by all entities on the blockchain. Tokens are verified by a decentralized set of entities, meaning no single party can invalidate a token owner's payment request. Token management systems are measurable for two reasons: first, once registered, ownership does not need to be changed or managed; second, payment is automatically calculated based on a contribution formula, reducing the possibility of conflict between system operators and data contributors.

The challenge of running a data network on a blockchain is achieving standardization through verification—that is, automatically verifying data upon submission and then automatically compensating it. This can be done through standardized methods—only compensating data contributors with specific parameters (e.g., name and email address). When parameters are hard to determine, data that passes a software-based verification upon submission—passing a filter containing a specific value range—is considered the same type of data but in a different form.

## Consumer Applications

For today's tech industry leaders, consumer applications may be the most important unique data source. Facebook built a competitive advantage through data and further strengthened it with machine learning. These companies collect massive amounts of data from their users every day, then use this data to make predictions to improve consumer products or the business models around them.

Over the past decades, Google brought some highly innovative products to market, many of which were free to consumers. In formulating product plans, the company adopted a strategy of giving away products for free to collect data and gain market share. It then used this data to build systems predicting which ads people wanted to see, and sold these predictions in the form of reasonably priced, high-performing ads to brands. Today, the relationship between consumer-side data collection activities and business-side revenue-generating activities is obvious, but it was not so when Google was founded. At that time, most ads on the web were banner ads—essentially online billboards, not targeted at any specific visitor to a site. Google changed this model in several ways, primarily by making predictions based on what visitors searched for on the site and other auxiliary data—such as everyone's location. This data proved highly effective in increasing visitor interest in ads and Google's revenue from selling them.

Google also provided us with many other reliable data strategies. Google Translate is free, but Google does not sell the dictionaries developed when users translate. Google `TensorFlow`—a machine learning model management tool—is free, but Google does not sell the data people feed into the model. Google Search is free, but Google does not (directly) sell search data to marketers or other search companies. Google is a highly resilient enterprise because it has consistently adhered to a strategy of collecting valuable data to build intelligent products.

Facebook—one of the world's dominant social networks—brought us closer together without charging users any fees. As users shared their interests, hopes, and dreams with friends, the company's products accumulated massive amounts of data and continued to release products collecting more such data. Facebook uses this data to build systems predicting which ads people want to see, then sells these predictions in the form of reasonably priced, high-performing ads to brands. Facebook's ads are particularly effective because its visitors browse while logged in, meaning Facebook knows each person's browsing activity, allowing it to push ads to them more accurately.

Amazon changed the way various products are purchased, making it cheaper and easier than any other method. This tech giant collects data on customer needs based on what customers buy and how they browse, then uses this data to make better suggestions. Amazon has been doing this for a long time, so it can provide better recommendations than other shopping sites.

Collecting data from consumers for one reason and then selling that data to businesses for another is common and can form the foundational business of large companies. For example, 23andMe did this with genomics, BillGuard with purchase data, Credit Karma with credit scores, Onavo with app monitoring, and Dark Sky with location data. However, building a solid business this way can be very difficult. First, the "trick" to get users to submit data is not a long-term strategy; another company might use the same or other cheaper tricks to get the same data. Second, the trick might require paying the data pipeline, e.g., paying developers to install an `SDK` as a data pipeline. If these developers want to charge more, or consumers want to pay less, the profit from selling data might disappear. Finally, people cannot extract value from static data and earn regular income because, for consumers, the value of this data may disappear over time. Additionally, it is worth noting that the current leaders of the tech industry are not enterprise software companies. Companies are unwilling to provide data to each other. On one hand, large enterprise software companies usually operate in highly competitive environments, so they strictly limit what they share with other software vendors, because those vendors might sell software to their competitors. On the other hand, consumers have no such competitive concerns and are not as concerned about privacy.

## Sensor Networks

Aggregating data from the real world—not just from other software—requires considering a different set of factors. Creating sensor networks to collect data is one way to build datasets, but deploying such a network can be costly, because sensors can be simple counting tools or more complex things like airplane black boxes.

The most obvious and common sensor network is the global mobile phone network. Many of these phones have individual sensors that software applications can use to collect data. For example, the app Dark Sky obtains atmospheric pressure data from the phone's barometer sensor and aggregates it to provide weather predictions beyond the local area. Additionally, the company collects location data and sells it to third parties. Today, most data from sensors in phones is captured by companies, but there may still be opportunities to collect unique data from relatively new sensors or by using sensors in different ways.

Cheap and abundant commodity sensors will be the most obvious and accessible data sources in the future. Considering cost and availability, such sensors certainly will not instantly generate proprietary data. In this case, proprietary data will be generated by such sensor networks, with the competitive advantage coming from the proprietary way the sensors are deployed—for example, placing a device in every retail store in a specific region to count foot traffic.

Proprietary sensors are the least obvious and hardest-to-get data sources. People can manufacture sensors to measure anything, from biosignals and bodily functions to deep-sea phenomena like thermoclines. However, because sensors are complex, building and manufacturing them can be very difficult. Sensor design, manufacturing materials, components connecting sensors to computing devices, communication methods, production processes, and integration with existing sensors can all become sources of competitive advantage for enterprises through IP protection. Once a sensor is built, the next challenge is how to convince people to install them. Without corresponding benefits, people won't install a power-consuming, noisy device in their homes. The most effective incentive is to pay users, like Nielsen (the global market monitoring and data analytics company), paying people to install a box on their TV that tracks their viewing habits. Another very effective method is to provide an application that both processes data from the sensor and helps the customer. This model succeeds because it saves money for sensor users—for example, installing electricity sensors, collecting data, aggregating data through the sensor network, and then negotiating cost savings with the power company.

# Public Data

For AI-first companies, there are many public data resources available. These resources may be difficult to obtain and controversial, yet they are free. Figure 3-12 shows public data.

![](EPUB/images/image_037.jpg)

Figure 3-12 Public Data

## Web Scraping

There is a lot of information on the internet, much of which is structured and can therefore be used to train machine learning systems. This is how Google gained its data advantage; the company is arguably the world's largest web crawler today.

Volume ensures quality; the accumulation of large amounts of data can form a competitive advantage. Collecting data from the internet is often called scraping. Simply put, a crawler is a program that systematically finds web pages or other resources on the internet via `URLs`, removes unnecessary things like formatting, extracts important (or all) data, saves it in a standard document format (e.g., `JSON`), and passes it to its own database. For example, a crawler can log into the US financial market regulator `SEC` website, access company reports stored as `HTML` files, strip formatting, find annual revenue figures, put them in a `JSON` document, and store them in a database. Technically, scraping is very difficult because the form, location, and availability of web pages and other resources are constantly changing. Good crawlers need to be highly adaptive.

Scraping is a big business; many companies offer scraping services. Some companies launch custom crawler businesses to collect data points from specific resources. Some companies regularly extract fresh data from a specific set of web pages, using crawlers customized to adapt to page unavailability or frequent changes. Still others offer custom scraping systems that can extract any available data from web pages.

There is an alternative to scraping services: using `APIs` that can provide `DaaS`. This service scrapes effectively and then provides data through specific endpoints in the `API`. Companies providing `DaaS` run their own crawlers—perhaps along with other data collection methods—continuously providing the latest, ready-to-use, high-quality data. Other features may include inputting an email address into an `API`, which then provides the name, title, and company of the owner of that address. Companies providing this service can obtain this data in hundreds of different ways, such as scraping public resources and government websites.

