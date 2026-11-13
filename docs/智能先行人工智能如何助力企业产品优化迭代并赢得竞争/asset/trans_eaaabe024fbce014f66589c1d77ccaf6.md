

# Setting Different Threshold Levels

Set different threshold levels and collect the True Positive Rate (Hit Rate) and False Positive Rate (False Alarm Rate) for each threshold, as shown in Figure 7-8. This is a curve plotted on the *x* and *y* axes, where the *y* (vertical) axis represents the True Positive Rate and the *x* (horizontal) axis represents the False Positive Rate.

![](EPUB/images/image_068.jpg)

Figure 7-8 True Positive Rate vs. False Positive Rate at Different Decision Thresholds

## True Positive Rate Formula

![](EPUB/images/image_069.jpg)

## False Positive Rate Formula

![](EPUB/images/image_070.jpg)

## Area Under the ROC Curve (AUC)

Calculate the Area Under the ROC Curve (AUC) using integration; this is the area of the shaded portion (as shown in Figure 7-9). This area gives the overall accuracy of the model across all different thresholds. If the model is always correct, the AUC will be 1—the entire area; if always wrong, it will be 0.

![](EPUB/images/image_071.jpg)

Figure 7-9 Area Under the ROC Curve

## Precision and Recall

Precision and recall measure the efficiency with which a model retrieves and identifies relevant results. Precision is the number of relevant data points retrieved by the model divided by the total number of data points retrieved. It can also be said that precision is the probability that a randomly retrieved data point is relevant. Recall is the probability of how many relevant data points are accurately found in a random search. Measuring both is important because precision alone does not show how far the model is from capturing all relevant results, while recall can reach 100% simply by retrieving all data points.

### Precision Formula

![](EPUB/images/image_072.jpg)

### Recall Formula

![](EPUB/images/image_073.jpg)

## Loss

Loss is a measure of whether the model is correct or not when making a specific prediction. A perfect prediction has zero loss; therefore, it is a matter of degree. Measuring loss is useful in machine learning because not all outcomes are binary, and the process of improving a model is one of gradually reducing loss with each iteration. Measuring loss depends on whether one is looking at the entire dataset or a subset, whether a large number of outliers or a fairly uniform distribution is expected, and many other factors. Calculate the square of all losses to see if the loss regresses to the mean and if a change is needed. Plot loss against different parameter weights to see if there is a point where loss is minimized, and fix the weights at that point. To find this point, one must take the derivative of this curve to see if it is getting closer (descending curve, negative gradient) or farther away (ascending curve, positive gradient), to ultimately understand how parameter weights lead us to the minimum loss level. This is called Gradient Descent (as shown in Figure 7-10).

![](EPUB/images/image_074.jpg)

Figure 7-10 Gradient Descent

Working with model builders to better build models may require diving to this level, but understanding it usually only requires basic calculus knowledge.

## Human Acceptance

AI-first products adopt explicit ways for customers to provide feedback on machine decisions—implicitly accepting or rejecting the predictions used by the machine to make decisions. For example, a product might be determined from a photo to have caused dangerous skin damage; the dermatologist using the product can either accept or reject this judgment. This may be the highest single standard for measuring prediction accuracy, because the human user giving feedback may be applying their intuition, as well as all their real-world experience and domain knowledge.

## Usage Index

There is a fundamental principle we must not forget: the simplest way to judge whether customers are getting value from a product is to look at how much of the product they use. A product's usage index is product-specific; ideally, the usage index measures the core actions a customer takes to get value from the product, such as clicking a search result (not just performing a search) or purchasing a product (not just clicking a catalog). For AI-first products, the usage index must also measure actions that collect feedback data for the underlying model. Product managers can examine the product, log every point of customer feedback data—explicit or implicit, input or generated—and then measure product usage around these points to find ways to increase usage, thereby generating additional feedback data. Some users may generate more feedback data than others—they are the power source of the model, and the super-users of the product—while some may generate higher-quality feedback data—they are the super-teachers in the world of data learning effects. Figure out who they are, talk to them, and building more features for them may yield higher-quality feedback data.

# Machine Makes Money

Businesses must ultimately turn a profit and survive and grow without external funding sources. We have talked a lot about machine learning, but little about how to use ML models to generate profit. This section provides profitability metrics and the levers that can improve them.

The data learning loop brings profit and creates investment opportunities for AI-first vendors: better predictions lead to more automation, which lowers operating costs, meaning more funds can be invested in R&D (models and data), which produces better predictions, and so the cycle continues. Eventually, this business generates very high gross profits, covers all operating costs, and achieves profitability. Figure 7-11 shows the vendor's data learning loop.

![](EPUB/images/image_075.jpg)

Figure 7-11 Vendor's Data Learning Loop

We can also think about this from the perspective of the benefits gained by the customer: higher prediction accuracy enables higher automation, attracting more customers or encouraging existing customers to use the product more, generating more data and better predictions, and so on.

## Vertical vs. Horizontal

"Vertical" AI-first products face more profitability challenges than "Horizontal" products. Vertical products apply machine learning to business or industrial data to generate predictions valuable to their customers. Let me give you an example: a vertical product that collects machine data from a production line and uses this data to train a model to predict when the production line might fail, thereby helping customers avoid these failures. Another example collects data from a large volume of product images and uses this data to train a model to identify these products in a warehouse or store, helping customers avoid the high cost of stockouts. Horizontal products include tools provided to ML engineers and data scientists working in different verticals. Figure 7-12 shows the customer's data learning loop.

![](EPUB/images/image_076.jpg)

Figure 7-12 Customer's Data Learning Loop

The economics of these two types of businesses are distinctly different. Vertical businesses need to solve specific process problems for customers, while horizontal businesses need to solve general process problems. Vertical businesses need to vertically integrate around the customer problem, finding everything needed to solve the whole problem. A vertical business may need to integrate with specific data sources owned by the customer, build a workflow product that outputs predictions for them, and set up certain features for the company's specific users. Horizontal businesses need to generalize their solutions to apply to people working in different vertical industries. A horizontal business may involve integration with many different data sources, workflow products, and communication tools. Companies producing AI-first products often face fewer profitability challenges because the marginal cost of delivering these products is very low; therefore, the economics of such businesses often resemble those of traditional software companies.

For AI-first companies, this framework is not much different from that of traditional software companies. Vertical, traditional software companies need to build various software integrations, features, and processes for people in their industry. For example, `Veeva Systems` is a world-class vertical software company that integrates multiple products used by medical professionals, providing functions such as clinical data management and workflows, and offering customers more efficient regulatory services. Horizontal traditional software companies need to build software integrations, features, and processes for enterprises across industries. A first-class horizontal software company, `Salesforce`, has a complete cloud platform where different vendors can integrate to form an application marketplace; it provides industry-required functions and flexible Application Programming Interfaces (APIs) that allow customers to build their own workflows across applications.

## Profit

Here is the correspondence between Gross Profit, Operating Profit, and Net Profit:

**Revenue**

Less: Cost of Goods Sold (COGS)

Equals **Gross Profit**

Less: Operating Expenses

Equals **Operating Profit**

Less: Interest and Taxes

Equals **Net Profit**

## Unit Analysis

The first way to measure profitability is on a per-unit basis. Unit analysis is simple: select a unit department and analyze its profitability. Because business models differ, the relevant profit unit is different for every enterprise. For example, manufacturing something that reduces machine energy consumption might be billed based on the percentage of energy saved. Therefore, the unit of energy reduction would be each machine, measured based on energy usage per machine.

A business model may be based on a series of improvements and a multi-tiered pricing model; therefore, choosing the customer as the unit often makes sense. This means allocating all revenue and expenses associated with that customer to that unit. Allocating revenue is simple—it is simply the turnover per customer—but allocating costs usually involves tracking all sales, pre-sales engineering, data integration, product integration, training, customer-specific feature engineering, customer-specific model training, customer outcomes, customer service, and other costs for that unit. Based on the unit's revenue and expenditure, calculate its Gross Profit (see Table 7-3).

Table 7-3 Calculating Gross Profit (USD)

![](EPUB/images/image_077.jpg)

▼Continued

![](EPUB/images/image_078.jpg)

The goal of unit analysis is to increase the unit's gross profit. This means, first, that the product provided is profitable as measured by contribution margin, and then making the customer broadly profitable, accounting for the cost of services required to deliver the product.

Calculating the unit's net profit is much more complex because not all costs, from R&D to office space, can usually be allocated to a specific customer. We will cover this below.

## Input Cost Analysis

Every business uses inputs to obtain outputs. Companies that output predictions spend money on data as inputs. Analyzing the cost of data inputs is a prerequisite for generating profit.

Data is either new or old. New data comes from labeling; old data comes from pipelines.

### Data Labeling

Data labeling is done by experts and non-experts or machines. Experts label things that require specialized knowledge to identify, such as a specific bone in the body. Hiring experts with such specialized knowledge can be expensive; therefore, the unit cost of these labels is higher. Non-experts mainly label things that do not require specialized knowledge, such as chairs or boxes. Non-experts are more flexible in how they work and can be hired on various platforms, the most famous being Amazon Mechanical Turk. Machines can label without our help; the accuracy they can achieve depends on the technology used and the data needing labeling.

The first metric is labeling cost per unit of value. This is explicit when outsourcing labeling to a vendor—the vendor's bill is the cost. But when the customer hires labelers, or software engineers are involved in data labeling due to failures during training, the cost is higher. In the latter case, one must figure out the cost of employees preparing the data needed to train the ML model and allocate the labeling cost to the input cost of a specific value unit, such as a customer project.

The second metric is the difference between expert and non-expert labeling and how it changes over time. Assuming experts are more expensive than non-experts, and over time we must label the same amount of data, we must be clear on how to balance expert and non-expert labeling. The key metric is the agreement rate between experts and non-experts. That is, if non-experts perform largely at the same level as experts, then shift more work to non-experts and achieve the same labeling accuracy and model performance.

Using machines may be cheaper than hiring humans; therefore, it is worth exploring techniques like active learning and forms of semi-supervised learning, such as Generative Adversarial Networks (GANs).

Finally, if there is still a large amount of data to be labeled, one can explore using machines to generate synthetic data. Synthetic data generation systems can produce data and associated labels in a package.

### Data Pipelines

Obtaining old data from customers requires building pipelines from customer data to the ML system. Building these pipelines takes time, but this engineering time cost—or the cost of outsourcing it to a system integrator—can be amortized over the entire lifecycle of the customer relationship, because these pipelines (usually) are fixed once built. Attributing engineering time costs to a value unit, such as a customer project, helps determine if that customer is profitable.

Sometimes, it is not just a matter of building pipelines; there is much other work to do, such as manual data entry.

One way to reduce pipeline construction costs is to build automated integration software, train junior engineers to perform data integration, choose to work only with customers who have more common types of data storage (thereby obtaining low-cost, even free pipelines), or outsource to customers.

## R&D Cost Analysis

A tech company's R&D budget can be very significant. The cost of building software available to all potential customers is usually considered an R&D expense rather than Cost of Goods Sold (COGS); some large, established companies allocate 25% of capital to R&D. Classifying expenses as R&D rather than COGS affects Net Profit and Gross Profit respectively.

Expense classification related to software development is difficult and often controversial; it is hard to say which features and code tasks are necessary to retain customers or attract potential ones.

AI-first companies' expenses go beyond software feature development, e.g., manually tuning model features or data cleaning. If these features can be transferred to a global model that works across customers, or if incoming data improves the model's overall performance, this can arguably be classified as R&D.

So, how do AI-first companies manage R&D expenses? Break down R&D work to see what is effective. ML researchers and engineers work to improve the performance of models used by all customers, or customize a model to fit a specific customer. In the latter case, making more money is the primary issue. On one hand, ensure the customer is charged for custom feature development; on the other, strive to complete this development in a short enough time to earn 100% or more profit on the engineer's effective hourly rate, at least covering their "fully loaded" cost at the company. In the former case, making more money is a more difficult task. Figure out what works, and ensure R&D staff are actually developing features likely to improve overall model performance. This seems challenging but only requires applying the scientific method. That is, form a hypothesis that adding a feature or some data, etc., will produce what kind of results—improved accuracy, stability, etc. Then experiment and track diligently. For example, 98% of the time, when a machine tries to use supervised learning to identify a brand on a cereal box, one can first train the model on images of cereal box labels taken in low light or at special angles, then have the trained model identify real examples to see if accuracy improves across the board. Sometimes, domain experts have learned heuristics—using things collected from work—achieving quick experimental results by hard-coding in features, e.g.: "Whenever we see a bottle cap lifted 1mm, we know the next step of bottle production will be forcibly terminated, so the whole line will stop." If someone can repeatedly learn this in their career, they might not need a model to learn to make such a prediction.

# Chapter Conclusion: Flywheel First

If you want to do the right thing, you must be able to measure things correctly. I have seen companies trying to put AI first fail because their ultimate goal was not strengthening the core data learning effect. And when I saw the powerful force of the data learning loop, this feeling became even stronger. More generally, the management systems of AI-first companies are still in their infancy—the industry has not yet formulated suitable rules—so I work with companies to develop our own products, ensuring we can build sustainable enterprises. I have seen companies use the metrics discussed in this chapter to turn their companies into valuable enterprises.

If you view something as a dynamic system, you will not be so surprised when it inevitably changes. Once the flywheel gets the data learning effect spinning, they begin to cycle—and self-reinforce with every repetition. However, sometimes the flywheel is hard to get spinning, for many reasons, including the inherent risks of AI adoption, expectations for its performance being too high, too little data available to train it, and inability to get enough data to keep it up to date. You can use the frameworks and metrics in this chapter to choose the right problems to solve, and perhaps you can get the flywheel spinning.

# Chapter Key Points

◆ **The Data Learning Effect moves in a loop.** Learning is a cyclical process: observe, learn from it, act, and observe the effect. The Data Learning Effect is also a cyclical process: acquire data, feed it into the model, output predictions, and observe their accuracy.

◆ **The Moat is the wrong metaphor.** A moat is an accumulation of assets forming a barrier, but barriers can be breached, and those assets can be taken away. The Data Learning Effect accumulates not only data assets but also lessons learned.

◆ **The Loop keeps growing.** Data learning loops grow automatically; their shape and size constantly change because with every loop—every repetition of the learning process—they automatically accumulate experience.

◆ **Data Learning Loops are powerful because they endogenously generate assets, capabilities, and information.** With every loop, the intelligent system automatically creates data, programs, and accumulates experience.

◆ **There are good loops and bad loops.** Good loops have sufficient data input, increase entropy by letting people do lots of things with predictions, so they can produce effects in the real world that the model can observe. Engage customers with predictions to gather more information for the model and learn more.

◆ **Consider the return on solving the problem.** Product founders, product managers, and designers must carefully consider whether a system with probabilistic assumptions can meet customer needs, or, considering the cost of errors, whether a deterministic system would be better.

◆ **Recognize the target.** The Prediction Usability Threshold is the minimum performance level required to prove product adoption. Figure out what the customer expects, what they want to achieve, and use this information to make a prediction accurate enough to be useful to real-world customers.

◆ **Achieve sufficient scale.** Evaluate what data can be accessed, whether sufficient data scale can be reached, or discard models that require more data.

◆ **Not all data is useful to the loop.** Some data is used to build the model and then discarded; some data is used as system input; and some data is fed back into the system via feedback mechanisms. Of these three types, only the last is the basis for generating competitive advantage.

◆ **Watch for Drift.** New training data must be acquired at the same speed as the environment changes. Without this data, the model may start to drift from reality.

◆ **All aspects of performance must be considered when choosing a problem to solve.** Domains with low returns on data scale and high drift potential are less reliable. Companies that tackle problems using high returns on data scale and low drift potential are more reliable.

◆ **Model Measurement.** Accuracy, Binary Classification, Receiver Operating Characteristic (ROC), Precision, and Recall can indicate whether a model can deliver predictions and how it is becoming more accurate.

◆ **Track Unit Costs.** Identify the core unit delivered to the customer, and measure the revenue and expenditure of that unit to preliminarily assess the profit potential of an AI-first company. Then layer in labeling, data pipeline, data collection, and R&D costs to arrive at an accurate Gross Profit figure.

# Chapter 8: Advantage Aggregation

AI-first companies adopt many of the same strategic frameworks as other companies, but they also build Data Learning Effects to capture enough information about a process to automate it, ultimately lowering costs. This cost advantage gives managers of AI-first companies more options.

They can choose to keep prices the same: take the difference as profit; reinvest profits in the technology behind the Data Learning Effect to achieve more automation and further lower costs; reinvest profits in the business to achieve vertical integration, or reposition to acquire more data.

They can also choose to lower prices, using the gains from automation to achieve the following goals, thereby beating competitors in existing markets or gaining an advantageous position when entering new markets: Differential Pricing; Disruption; Clustering.

The Advantage Aggregation of AI-first companies is shown in Figure 8-1.

![](EPUB/images/image_079.jpg)

Figure 8-1 Advantage Aggregation of AI-First Companies

This chapter introduces the five strategies at the top of the diagram above: Vertical Integration, Positioning via Standardization, Data Aggregation, Disrupting Market Access, and Differential Pricing based on Data Contribution—generating more data for the model, growing the Data Learning Effect, and attracting more customers. Managers can either distribute these profits to shareholders or reinvest them in the business, technology, or strategy to further drive the development of the Data Learning Effect.

Additionally, this chapter discusses how the Data Learning Effect allows AI-first companies to gain an advantage in competition with incumbents. Acquiring data from a customer's existing database, converting it into new products, and integrating services around these products can wrest customers from existing software vendors. Wresting a critical mass of customers from existing software vendors, by setting industry standards for storing and moving data, makes the entire ecosystem revolve around them. The concepts in the diagram above—Disruption, Aggregation, Vertical Integration—have allowed new entrants to stand out from incumbents over the past few decades. Applying these concepts to the Data Learning Effect can build a series of long-term, sustainable, and powerful competitive advantages.

Before diving in, it must be remembered that the prerequisite for aggregating any of the following advantages is **putting AI first**. Strategy is about trade-offs to build competitive advantage. AI-first companies make trade-offs in capital, time, and resources to build the Data Learning Effect. Only when they have data, processing power, and Data Network Effects—the Data Learning Effect—can they aggregate other competitive advantages.

## Traditional Forms

