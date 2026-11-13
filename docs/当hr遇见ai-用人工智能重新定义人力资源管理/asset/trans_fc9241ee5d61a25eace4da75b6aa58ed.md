

## Section 1 Evolution and Transformation of Organizational Forms

![Figure 6-2 Different forms of networked organizations at different stages of development](Images/cutq_156_7373_l.jpg)

During the process of evolution and transformation, organizations exhibit the following characteristics.

1. **Flattening of organizational structure**. Driven by information technology, network technology, and digital technology, enterprises can transmit and share information on the same level without the need for top-down layer-by-layer communication or bottom-up step-by-step reporting. On the enterprise information network platform (data platform), the traditional vertical relationships among employees become crisscrossing, equal relationships. The information communication capability and management span of enterprise managers have grown multiples, even dozens of times, thereby significantly reducing the hierarchical levels of the organizational structure, making it flatter.

A flattened organizational structure changes the connection methods between superiors and subordinates, between departments, and between the organization and the outside world in the original bureaucratic structure. It possesses the advantages of high efficiency, flexibility, and rapid response. Its most distinctive feature is the coexistence of hierarchical organizations and agile project teams. People with different professional knowledge and experience are distributed in complex organizational forms, which can accelerate the circulation of knowledge and experience and improve overall organizational performance.

2. **Networking of organizational relationships**. With the further strengthening of the application of Human Resource Management Information Systems, Enterprise Office Systems, communication tools, `ERP`/`CRM`, `AI`/`BI`, and digital technologies, most repetitive and routine work in traditional functional management departments is completed by system software or robots. The tasks of functional departments are limited to formulating and modifying control procedures and handling special events. The work mode of functional department employees is no longer the traditional top-down command issuance but joint consultation and mutual collaboration. With the help of the internal enterprise network platform, vertical division of labor among employees continues to decrease, while horizontal division of labor and collaboration continue to strengthen. Enterprise organizational relationships centered on control (administrative orders) gradually transform into relatively equal, autonomous, innovative, and collaborative network relationships. The greatest benefit of networking organizational relationships is the reduction of delays between enterprise decision-making and action, accelerating employee response to market and competitive dynamic changes, making organizational capabilities more elastic and resilient, and reactions more agile.

3. **Downsizing of organizational scale**. In the mobile Internet era, the flexibility and innovativeness of small companies are significantly stronger than those of large companies. Downsizing of organizational scale does not refer to the shrinkage of output value or market, but to the reduction of personnel scale and organizational structure. Facing fierce market competition, many large companies are gradually making their operating entities smaller through spin-offs, divestitures, process reengineering, digital transformation, business outsourcing, or establishing strategic alliances, thereby achieving the goals of cost reduction, efficiency enhancement, improved organizational adaptability, and enhanced competitiveness.

As enterprise organizational management shifts from talent hierarchy systems to collaborative network teams, HR needs a new set of tools to understand team dynamics, and managers need to adopt new strategies to motivate, coordinate, and adjust team networks to share information and collaborate effectively.

In recent years, with the workforce becoming increasingly dispersed and forms of flexible employment increasingly rich, many people are employed outside organizational forms as individual instructors, consultants, freelance creators, and live streamers. These workers form new alliances and collaborations with each other, jointly empowering business development.

## [Section 2 Organization and Organizational Network Analysis](../Text/contents.xhtml#b_75)

Traditional HR research and application practices usually focus on individual employees, positions, and the mutual relationships between employees and positions. For example, enterprise performance prediction and automatic turnover prediction for employees are based on employees' own attributes. Focusing on employees' own attributes and related attributes is effective in many HR management application scenarios, but this method cannot provide more information and deeper insights at the organizational level. For example, according to traditional HR management methods, if HR wants to assess employee communication satisfaction, they would have employees fill out a survey questionnaire. The questionnaire includes communication-related questions. If employee feedback is positive, HR assumes internal organizational communication is smooth, and the investigation into employee communication status concludes successfully. But can this traditional research and analysis method reflect the full picture of the problem? For example, two employees have similar communication frequencies; one communicates with objects within the department, while the other communicates with both internal department members and members outside the department. These communication statuses represent different behavioral patterns at the organizational level. There are many similar questions, such as: What are the proportions of communication during working hours vs. off-hours? What is the average response time for different employees? In summary, traditional HR management methods can no longer support organizational-level, detailed data-based, quantifiable analysis.

Entering the 21st century, with the rise of social networks, experts conducted extensive research on social networks, promoting the gradual maturity of Social Network Analysis (`SNA`) technology. As early as 2005, Giuliani and Bell used `SNA` methods to study the laws of information transfer and knowledge sharing in the organizational network of the Chilean wine industry. Since then, a series of studies have applied `SNA` methods to analyze internal organizational communication, collaboration, and information transfer, the impact of inter-departmental communication on performance, and internal organizational decision-making patterns.

These research results reflect the advantages of `SNA` in HR organizational-level analysis: `SNA` provides a framework for analyzing and studying organizational status, behavior, and driving causes. The application of `SNA` in the field of HR management is becoming a new trend. Social network analysis with the organization as the research object is defined as Organizational Network Analysis (`ONA`). Deloitte defined `ONA` from the perspective of human capital: `ONA` is a structured method for analyzing and visualizing information flow, communication coordination, and decision formation within an organization.

Next, we help readers gain an intuitive understanding of the difference between `ONA` and traditional HR methods through a simplified case. Figure 6-3 is a traditional organizational structure, where each letter represents a company employee. The organizational chart describes the hierarchical management relationships within the company; with a higher degree of informatization, it would link employee personal information and position information. However, the traditional organizational chart is only a static and abstract description of the enterprise's organizational structure; it cannot reflect the actual communication and collaboration among employees. Important characteristics of actual communication and collaboration cannot be expressed through the traditional organizational chart. First, there is communication and collaboration within and across departments among employees, not just communication between employees and their direct leaders as depicted in Figure 6-3. Second, the state of communication and collaboration among employees is a dynamically changing process, which reflects important information about organizational effectiveness. For example, Figure 6-4 shows the actual communication and collaboration among enterprise employees during a specific period. Compared to the organizational chart in Figure 6-3, Figure 6-4 is a more complex network structure.

![Figure 6-3 Schematic diagram of traditional organizational structure](Images/cutq_159_17407_m.jpg)

![Figure 6-4 Actual more complex organizational network structure diagram](Images/cutq_160_28483_m.jpg)

### [1. Relevant Terms in Organizational Analysis](../Text/contents.xhtml#c_76)

Organizational forms can be represented using graph structures. In mathematics, a graph is the main object of study in graph theory. A graph consists of vertices and edges connecting vertices. Vertices are called `Vertex`, represented by `V`; edges are called `Edge`, represented by `E`. Therefore, mathematically a graph is represented as: `G=(V, E)`. However, in practical application scenarios, a graph is called a network, i.e., `Network`; correspondingly, the vertex `Vertex` is called a node, i.e., `Node`, and the edge is called a link, i.e., `Link`.

Connections between nodes are divided into directed connections and undirected connections. Directed connections include unidirectional directed connections and bidirectional directed connections. For example, a conversation between Employee A and Employee B can be represented as an undirected connection between A and B; Employee C sends an email to Employee D, but Employee D does not reply, which can be represented as a unidirectional directed connection from C to D; Employee E and Employee F have email correspondence, which can be represented as a bidirectional directed connection between E and F. Therefore, we can also regard an undirected connection as a symmetric bidirectional directed connection.

1. **`Degree`**

The `Degree` of a node is defined as the number of edges connected to that node. In a directed graph, the number of all edges pointing to a node is called the `In-degree` of that node; the number of all edges originating from that node pointing to other nodes is called the `Out-degree` of that node. The average degree of the network reflects the sparsity or density of the network, while the degree distribution can characterize the importance of different nodes. In a directed graph, the degree of a vertex is divided into `Out-degree` and `In-degree`. `Out-degree` refers to the number of connections from this vertex to other vertices; `In-degree` refers to the number of connections pointing to this vertex.

2. **`Network Density`**

`Network Density` can be used to characterize the density of connections between nodes. It is the ratio of the actual number of edges in the network to the upper limit of edges it can accommodate. For example, a network of 5 nodes can have at most 10 connections. Therefore, the density of a network with 5 nodes and 6 connections is `6/10=0.6`, as shown in Figure 6-5.

![Left: 5 nodes can have at most 10 possible connections; Right: Network with 6 actual connections](Images/cutq_161_35465_m.jpg)

Figure 6-5 Schematic diagram of network density

3. **`Clustering Coefficient`**

The `Clustering Coefficient` is mainly used to describe the extent to which nodes connected to the same node are also adjacent to each other in the network.

4. **`Closeness Centrality`**

`Closeness Centrality` refers to the average length of the shortest paths from a node in the graph to all other nodes.

5. **`Betweenness`**

`Betweenness` refers to the number of all shortest paths in the entire graph that pass through a specific node. It is usually used to evaluate the importance of a node.

6. **`Subgraph`**

Given graph `G'=(V', E')` and graph `G=(V, E)`, if the vertex set `V'` is a subset of `V`, and the edge set `E'` is a subset of `E`, then graph `G'` is a subgraph of graph `G`.

7. **`Structural Hole`**

A `Structural Hole` acts like a bridge in the graph, connecting nodes. If it is removed from the network, the network splits into multiple sub-networks.

Organizational Network Analysis can be implemented through the graph database `Neo4j` and the `Python` library `networkX`, and further achieve network visualization. Graph data learning theories, methods, and applications related to social network analysis are still developing rapidly, such as Graph Neural Networks combined with deep learning. Interested readers can consult relevant literature.

### [2. Applications of Organizational Network Analysis](../Text/contents.xhtml#c_77)

Traditional HR analysis tools can provide formal, explicit, and static information, such as organizational charts, communication survey questionnaires, etc.; while `ONA` can discover informal, implicit, and real-time dynamic information. As a social organization, an enterprise's organizational form and characteristics naturally conform to the definition of a social network. HR practitioners need to redefine employee value: Employee value should equal the sum of employee human capital and social capital. Human capital reflects the employee's individual ability, while social capital reflects the employee's connectivity in the organizational network. Employee human capital includes skills and abilities meeting job requirements. Employee social capital includes forming connections with other employees and exerting influence, as well as the ability to transmit information and knowledge in the organizational network. For a long time, traditional HR management methods have struggled to evaluate and quantify employee social value. With the maturity of `SNA` technology, HR can finally quantify employee social capital. The employee value calculation formula is shown in Figure 6-6.

![Figure 6-6 Redefining Employee Value](Images/cutq_163_56570_l.jpg)

`ONA` can help HR practitioners accomplish the following tasks:

- Visualize the informal network of the organization;
- Clarify the status of communication and collaboration within the organization;
- Understand the realization methods and key nodes of leadership.

The above tasks can help HR practitioners answer questions that traditional talent analysis struggles to answer: Which employees have stronger influence? Whose departure would seriously impact organizational communication and collaboration? Have new employees integrated into the team? How is leadership realized and evaluated by managers? etc.

By calculating the betweenness centrality of different nodes in the network, the influence of each employee in the organizational network can be quickly calculated, and key influential nodes in the network can be identified, as well as which nodes these key nodes can directly influence. The circled employees in Figure 6-7 are `Structural Holes` in the network, i.e., key nodes in the organization. Identifying key node employees allows the organization to drive business transformation and organizational change through these employees on one hand, and facilitates the formulation of succession plans on the other. In fact, non-management employees who play a leadership role should be included in succession plans. Simultaneously, the organization needs to control the risks brought to the entire network by the departure of key node employees.

![Figure 6-7 Key nodes in the organizational network](Images/cutq_164_1371_m.jpg)

## [Section 3 Introduction to Organizational Network Analysis Cases](../Text/contents.xhtml#b_78)

### [1. Baidu's Organizational Network Analysis](../Text/contents.xhtml#c_79)

Baidu's Talent Intelligence Center (`TIC`) scientifically assesses organizational stability by analyzing departmental vitality and talent structure, reveals the laws of talent flow between organizations, and provides intelligent support for organizational optimization and adjustment, efficient talent motivation, and reasonable talent flow. Taking the Talent Circle Radar as an example, the talent circle reflects that job hunting also requires "matching social status" (finding the right fit). For instance, `TIC` discovered through data mining that the programmers and media talents recruited by America Online (`AOL`) presented distinctly different hierarchical characteristics. `AOL`'s programmers mostly came from ordinary IT companies, with almost no employees from top-tier companies like Google or Facebook. `AOL`'s media talents came from relatively higher-end workplace circles, such as *The Wall Street Journal* and *Financial Times*. By building such a social career network and intelligently modeling millions of talent profiles and job postings, `TIC` can predict recruitment hotspots in specific industries and market circles, enabling HR to prepare and adjust for major trends.

### [2. Alibaba's Organizational Network Analysis](../Text/contents.xhtml#c_80)

Alibaba utilizes organizational network analysis methods, combined with survey data analysis results, to discover problems that conventional organizations cannot detect. For example: Who are the stars or marginal figures in an organization? Do people at this level have significant influence and can they drive the team? Do employees of different roles and levels play a bridging role in the organization? Does an employee's influence increase the longer they work at the company? The answers to these questions can all be discovered through data.

From an e-commerce perspective, the core of new retail is the reconstruction of people, goods, and fields. In fact, the HR field also has people, goods, and fields, as shown in Figure 6-8.

![Figure 6-8 Precision Operation — Matching and Recommendation of People, Goods, and Fields](Images/cutq_165_6594_m.jpg)

"People" refers to understanding talent portraits; "Goods" includes courses, services, resources, etc., provided by HR practitioners; "Fields" refers tos, customers are categorized into "Promoters," "Passives," and "Detractors." The final `NPS` score is obtained by subtracting the percentage of "Detractors" from the percentage of "Promoters."

```
NPS = (Number of Promoters / Total Sample Size) × 100% - (Number of Detractors / Total Sample Size) × 100%
```

- **Promoters** (Score 9-10): They are enthusiastically loyal people who will continue to buy the company's products or services and refer others.
- **Passives** (Score 7-8): Generally satisfied but not enthusiastic; they may consider competitors' products.
- **Detractors** (Score 0-6): Dissatisfied with the company or have no loyalty to the company.

The calculation logic of the `NPS` formula is that promoters will continue to buy the company's products or services, while detractors will damage the company's reputation.

`NPS` is the most popular customer loyalty analysis indicator, focusing on how customer loyalty affects enterprise growth. By closely tracking the Net Promoter Score, enterprises can make themselves better.

