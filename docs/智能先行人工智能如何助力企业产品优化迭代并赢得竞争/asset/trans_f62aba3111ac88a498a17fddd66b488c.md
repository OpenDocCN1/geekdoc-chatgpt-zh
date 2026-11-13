
In the second decade of the 21st century, as billions of devices connected to the internet, the volume of data in the world increased dramatically. Acquiring data seemed paramount, yet funding for data acquisition was severely limited. Data itself is a valuable asset class because it drives data learning effects. Compared to other strategic priorities, AI-first companies allocate more money, time, and effort to acquiring data. But how do you acquire unique data? Where does a data strategy begin? This chapter introduces a data assessment methodology for rapidly scoping data acquisition projects, where to find data, and how to create data from scratch (as shown in Figure 3-1).

![Figure 3-1 Acquiring Data](EPUB/images/image_024.jpg)
Figure 3-1 Acquiring Data `EPUB/images/image_024.jpg`

## Assessment

Before investing in data acquisition, a data assessment framework must be established (as shown in Figure 3-2).

![Figure 3-2 Data Assessment Framework](EPUB/images/image_025.jpg)
Figure 3-2 Data Assessment Framework `EPUB/images/image_025.jpg`

Based on the difficulty for others to acquire the data, there are five methods to assess the value of data (as opposed to its ultimate utility to the acquirer): Accessibility, Usability, Cost, Time, and Substitutability.

### Accessibility

Data that is hard for you to obtain is likely hard for others to obtain as well. For example, to get the data, one might need to go to a specific location, such as a local council office, manually collect paper documents, photocopy them, process them with Optical Character Recognition (OCR) software, and finally convert the images from the copier into machine-readable text.

It is important to assess whether data will remain difficult to obtain in the future based on contracts or policies associated with the dataset. This often involves restrictions by the data owner on external access. For instance, government agencies and private vendors often make data publicly available for free for a period before starting to charge access fees or stopping data distribution altogether.

### Usability

The time required to extract data from a specific system can be a barrier for other systems. Some systems only allow data retrieval at very slow speeds, perhaps because the cost of running such a system is high, requiring data products to be tiered according to different rate limits. One example is financial market data; market data providers only allow access to stock price quotes at specific, relatively long intervals (typically tens of microseconds). If clients want quotes at shorter intervals, they must pay more. Paying more to get information more frequently can give clients a competitive advantage when using that data to make critical decisions. For example, a difference of a few microseconds might allow an AI-first trading system to outperform competitors and execute profitable trades.

### Cost

Fees charged by data providers are an obvious barrier to data acquisition. Sometimes the fee is explicitly set by the provider in dollars, but sometimes the charging model is less transparent—it might be a revenue-sharing agreement with the data provider, or a requirement for the accessor to purchase expensive software needed to access the data. For example, a terminal from Bloomberg, the New York-based financial data and media company, rents for thousands of dollars a month, yet it is essentially an ordinary computer assembled from commodity parts. However, it provides access to high-quality, real-time financial market data. Data acquisition costs can sometimes be paid in non-monetary forms, such as submitting some of one's own internal data before accessing the provider's data. Since not all data has equal value for everyone, it is difficult for companies to assess the exact cost of contributing data. However, whether the contribution requirement creates a barrier for competitors can be assessed based on the minimum volume, format, and row-level completeness of data required. The stricter the requirements, the harder it is for competitors to contribute data, and thus the harder it is to access the provider's data.

### Time

The time required to collect data is a key factor in determining whether you will lead competitors in data collection. Some data accumulates at a certain rate: for example, weather data is inextricably linked to the Earth's orbit around the sun, and employment data is determined by the speed at which relevant government departments collect and release it. Therefore, to get enough of such data, one simply needs to collect it over a long period.

### Substitutability

The substitutability or interchangeability of data affects whether others can use that data to achieve the same goal. Substitutable data can be replaced by different data (potentially cheaper) without negatively impacting the quality of decisions based on that data. For example, you can use text from any news website to train a model to understand the general meaning of sentence structures.

There are five additional methods to assess the value of data based on its ultimate utility (as opposed to the difficulty of acquisition by others): Durability, Accuracy, Dimensionality, Breadth, and Self-Reinforcement. These are less clear-cut because they depend on the intended use of the data.

### Durability

The speed at which data becomes obsolete determines its relevance. Old data may no longer represent reality and can therefore lead to models generating invalid predictions. For example, stock prices change almost instantaneously; therefore, the price from one second ago is not necessarily correlated with the price from one microsecond ago (depending on the features the AI-first trading model uses to predict prices). Other types of data may have very long shelf lives: map data, including the height of Mount Everest, will not change (significantly) and will remain relevant for many years. Then there are cases in between, such as consumer preference data obtained from surveys. Sometimes preferences are durable, like clothing sizes; sometimes they are fleeting, like fashion trends that last only a season.

Durability can be extended through updates. The speed at which a data provider or source updates data affects its durability. Sometimes data providers tier pricing based on update rates, charging more for fresher data. Highly durable data is usually less valuable because it requires continuous updating, which increases the cost of computation and acquiring more data.

### Accuracy

The accuracy of data determines the reliability of decisions. Typically, determining accuracy requires manual verification of data: for example, obtaining a sample of product specification data, such as power voltage—just a few lines—and verifying it against data manually collected from the manufacturer. Sometimes, to ensure data accuracy, providers offer data accuracy guarantees. Finally, third parties can verify the data accuracy of a range of providers by industry category or specific requirements.

### Dimensionality

The number of dimensions in data determines its relevance to a decision. Dimensions are attributes of an entity. This is typically reflected in the number of columns in a data table. For example, demographic data can include age, gender, and income.

Dimensionality is a particularly powerful factor in determining value when the intended use is training a machine learning model, because every dimension influences the model as it attempts to learn shapes and patterns in the data.

### Breadth

The breadth of data determines the degree to which it represents reality. Breadth is the number of entity data points or scatter distributions, typically reflected in the number of rows in a data table. Breadth means more examples of the same type, and more variation in attributes of entity cases and edge cases. Sometimes greater breadth can be achieved by joining datasets from different sources or providers, provided they share the same attributes. Usually, greater breadth can also be achieved by combining different datasets through aligning attributes and filling in any missing data.

### Self-Reinforcement

Over time, self-reinforcing data becomes more valuable. Self-reinforcement is reflected in the changing attributes of the same entity over time, but measurable in the same way. For example, an employee's performance feedback is a value at one point in time and another value at a later point, but the latter value reinforces the value of the first. This is because if two data points are the same or trend in the same direction, they enhance each other's value.

### The Volume Question

Acquiring large volumes of data has always been a concern, and many often sacrifice better data for more data. Which approach is better depends entirely on the type of decision being made and which models—whether using human or machine intelligence—might assist in making those decisions. For example, training a model to recognize something in an image might require a few hundred labeled images; if unlabeled, it might require hundreds of thousands. Because different image recognition systems require different numbers of examples and labels. In natural language understanding, at the time of writing this book, state-of-the-art models used 499 billion tokens, whereas just over a year prior, they used only 10 billion tokens. However, a model identifying specific clauses in legal contracts could be trained on just 100 `Word documents`. In the months after you read this book, these numbers will change due to advances in AI. Returns on data scale are rarely linear; they depend on the decision type and the model used for the decision. Data volume and value are shown in Figure 3-3.

![Figure 3-3 Data Volume and Value](EPUB/images/image_026.jpg)
Figure 3-3 Data Volume and Value `EPUB/images/image_026.jpg`

## Customer-Generated Data

For AI-first companies, the most important source of data is likely the customers they serve. After all, these companies build products to predict business or industrial outcomes for customers; therefore, it makes sense for predictive models to be based on these customers' data. Customer-generated data is shown in Figure 3-4.

![Figure 3-4 Customer-Generated Data](EPUB/images/image_027.jpg)
Figure 3-4 Customer-Generated Data `EPUB/images/image_027.jpg`

### Contractual Rights with Customers

This section covers strategies for negotiating data rights and offers tactical advice on how to draft contracts around data to build a competitive moat. This is an opportunity to establish strong defenses for both incumbents and emerging imitators, outside of traditional IP strategy.

### The Clean-Slate Advantage

At the dawn of the "cloud era" in the early 2000s, the vast majority of large enterprises were unwilling to entrust their data to external parties. For many, this was the first time they were asked to store data off-premises. To allay these concerns, cloud providers typically explicitly waived all rights to the customer data they managed. Many of these agreements are still in effect today, hindering cloud-era companies' attempts to build intelligent systems. They face two challenges: renegotiating data rights with existing customers, and a frantic scramble to acquire data.

However, today's AI-first companies have access to customers who are more willing to let third parties manage their data, enabling a different conversation about data. It is generally believed that AI-first companies lack the brand recognition and standing of large cloud providers, making them less competitive. Therefore, they are better suited to resolving data rights through negotiation.

### Negotiation

Negotiating data rights with customers is a chicken-and-egg problem. The ability to get data depends on the ability to demonstrate good results, but AI-first products need data to produce results. AI-first companies solve this by finding alternative data sources through the following methods:

- Targeting small-to-medium business (SMB) customers, who are more open about data rights or data they perceive as relatively low value, and may explicitly trade data rights for lower prices;
- Offering a free tier of the product that freely captures data but has limited functionality;
- Selling auxiliary products at cost to acquire data.

Many of these external data sources can train models to reach a prediction usability threshold for enterprise customers.

## Structure

Here is a very common story: A startup approaches a large enterprise, demoing an incredible new AI-first product that claims to automate tedious work. The enterprise agrees to a pilot and intends to buy. Unfortunately, the contract gets stuck in Legal and Compliance: the startup is not allowed to access their data, for fear of mishandling or it falling into competitors' hands. The startup is dead in the water.

This AI-first company made it clear from the start of negotiations that their primary interest was learning from the data and data exhaust—such as user engagement and interaction data, metadata, and data flow information—thereby sidestepping potential compliance concerns. Below, we outline ways to structure contracts to obtain customer data usage rights while achieving the following goals:

- Improve the model over time by adding new ML capabilities—making the product more useful to the customer;
- Adapt the model to the customer's changing circumstances;
- Prevent competitors from achieving similar levels of efficiency;
- Own a valuable asset.

Achieving these goals requires understanding the following concepts, which will ultimately be incorporated into the contract.

#### Models

These are models used to make specific predictions for a specific customer. A model can be something built by fine-tuning specific functions based on customer needs, or something built for all customers and then adapted to a customer's data, such that given the same data, the model's output differs by customer. Essentially, a model (in this case) is not useful for all customers.

#### Global Multi-Tenant Models

These models predict things that all customers find common. They are trained on data collected from all customers and contain features designed for all customers. Essentially, a global model is useful for all customers.

#### Data

This refers to data from the customer. This data is unprocessed and proprietary to the customer.

#### Anonymized and Aggregated Data

This is the data mentioned above, but processed in a way that prevents it from being traced back to a specific customer. Data is anonymized by `Hashing`, removing data points that can be directly linked to a specific individual, pseudonymizing, randomizing, or perturbing. Anonymized data can be aggregated across customers to train general models for all customers. This aggregation process takes a specific type of data point common across all customers and puts all data points of that type into a single table: for example, the number of times a product with a `UPC` identifier from a barcode scanner was scanned by all customers on a specific date.

#### Personally Identifiable Information

Some data is classified as `PII`, and therefore carries certain legal obligations for its use in the US and other countries. This data may need to be handled in a specific compliant manner. Some customers do not allow access to this data because they might mishandle it.

#### Storage

Data can be stored on public cloud or private servers. Customers may require different types of data and models to be stored in different locations. This may appear in the contract.

Having reviewed the concepts above, let's look at some contract clauses to avoid:

- Customers owning the global multi-tenant models;
- Restrictions on using anonymized aggregated data across customers to train these global models;
- Liability for managing `PII`;
- Requirements to store data in separate locations, thereby breaking the data architecture.

### Customer Data Consortia

A customer data consortium is where one company (the vendor) organizes a group of companies (customers) to share data with each other. Data consortia typically create a unique data asset for both the vendor and the users. Here is why customer data consortia build good data assets using a common framework.

- Small companies often lack sufficient data to train and run intelligent systems. This means they are more prone to problems like overfitting, where the model thinks a specific data point represents the feature it is trying to predict, but in fact, that data point is an outlier. Having a larger volume of data allows customers to effectively average over multiple data points when training their systems.
- Members can see different variants of the same category of data from other members. For example, product images taken under different lighting conditions are used to train a computer vision model to recognize the product in different environments.
- Members can cross-validate data points with each other. For example, they have a user with the same (non-PII) data in a medium—say, email—but no other data, such as preference for liquid vs. powder detergent. They can then correct these data points by unifying the email address and then filling in the "detergent preference" field for that user.
- Some companies collect data continuously—for example, from a device or `POS terminal`. Others collect data periodically—for example, through quarterly surveys or on-demand data purchases. Data from some members of the consortium can help other, slower-moving members adapt to current conditions.

Building a data consortium is both as simple and as difficult as getting every customer to agree to share data with every other customer. This is achieved by granting customers the right to use data in specific ways via the contract, such as using data to train ML models. Data can be anonymized and aggregated at different granularities so customers don't share all their data; how much is shared depends on what the customer hopes to contribute and gain by joining the consortium. Building a customer data consortium faces some challenges.

- **Marketing**. Organizing consortium members creates a powerful force to compete with industry giants. However, this must first find a way to ignite members' enthusiasm to participate. Potential members will hesitate to share data, just like any company. Therefore, selecting initial members requires considering factors like relative competitive advantage, size, and maturity. The right messaging can bring these companies together.
- **Contracts**. The contracts signed between the vendor and each company joining the consortium must be identical; no special deals where some customers can access certain data while others cannot.
- **Anonymization**. This receives significant attention in customer data consortia because these customers may ultimately compete with each other, often directly.

Imagine what a data consortium trying to compete with Amazon would look like. The vast majority of shopping journeys start with search, and most product searches happen on Amazon. The site's search function and its suggestions help consumers quickly find what they want to buy. This is not only because thousands of engineers have worked on product search and recommendations since 2003 to produce an excellent product, but also because Amazon has vast amounts of search data that consumers want to see. Engineers use this data to train high-precision ML models, which power suggestions in the search bar, rank results, and recommend products on different pages. These features help customers get a high-quality search experience while providing relevant suggestions. Amazon's excellence deters competitors. Other e-commerce retailers are unlikely to have Amazon's engineering capability or data resources, and thus unlikely to provide the same high-quality experience. You could form a consortium to change the game, letting retailers use a highly engineered platform for product search, powered by aggregated data from all customers. The retailer consortium contributes data and trains the ML system on it, producing more accurate suggestions, results, and recommendations than any single retailer, allowing any member of the consortium to compete with Amazon. Collective intelligence shines again.

### Customer Workflow Applications

Workflow applications are a common alternative for acquiring data from customers, particularly effective for smaller customers who are often less sensitive about sharing data.

A workflow application is software that captures a series of actions a person performs in the real world and puts them into software. For example, taking a task list from a construction site project manager—written on a piece of paper—and putting it into a mobile app so they can track tasks, assign them, and generate reports. Workflow applications are ubiquitous. In fact, we are still in the era of building such workflow applications for many industries—a huge opportunity for software developers willing to learn the specific work patterns of a given industry.

Workflow products collect data. Every piece of data entered into a workflow application goes into a database. Take the auto insurance claims process as an example. First, an adjuster visits a body shop, takes a few photos of the damaged vehicle, and writes a report. This report goes to the insurance company's claims adjuster, who decides whether to repair or replace the vehicle and how much to pay. Sometimes the customer disagrees with the assessment, so the whole process repeats. Currently, these steps rely on pens, paper, clipboards, faxes, and cameras. A better solution might be a standalone app. It can take photos, provide text fields for the report, and automatically send the report to the claims adjuster for processing. If necessary, disputed claims can be sent back to the adjuster for reassessment. Data on what damage warrants repair vs. replacement, repair costs, and vehicle valuation is the most valuable but also the hardest to collect.

Business process data flows in and out of workflow applications every moment of every day. Companies can use this data to build an intelligent system—meaning the system not only records work but automates it by predicting the next step, even auto-filling parts of forms. Continuing the example above, imagine an app where you input vehicle specs, determine damage severity from photo files, and it calculates the total cost. Some of the tedious work originally done by the adjuster can now be automated, reducing claim cycles, and even auditing or replacing the claims adjuster's work. The result of collecting data and then building an intelligent system is a smarter workflow product.

Through further integration with banks and credit card providers, San Francisco software company `Expensify` began offering a better way to manage expenses. The product pulls transactions from these services and puts them into expense reports. The experience of dragging expenses into a report is far better than logging them one by one. `Expensify`'s second step was building an AI-based product to auto-categorize expenses. The system was trained on all data from the core workflow application, enabling customers to link transactions to categories, tags, and reports.

This ability to take real-world workflows, build software around them, and then add more data and functionality is exactly why I believe all business software will eventually become intelligent.

### Cross-Customer Application Integrators
