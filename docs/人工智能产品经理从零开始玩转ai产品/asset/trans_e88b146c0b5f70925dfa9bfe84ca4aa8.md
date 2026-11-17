

## 5.5 Technical Analysis

### 5.5.1 AI Product Classification

AI products have two development directions: "more human-like" and "more rational." Currently, AI products that can be commercially deployed are all developing in the direction of "more rational" and "more logical." Rational, logical AI products have more applicable scenarios than "human-like" AI products and are more suitable for engineering R&D and productization.

When humans interact with the external environment, they perceive the outside world through various sensory organs, make judgments via the brain, and then produce action feedback through bodily effector organs. As shown in Figure 5-2, like humans, AI products also possess components such as `sensors` and `actuators`, enabling them to perceive the external environment and provide feedback and operations. The module through which an AI product interacts with the external environment is called an `Intelligent Agent`.

![](converter-master/example/165201/content_633713_0.jpg)

Figure 5-2

Humans use organs such as eyes, ears, mouth, nose, and tongue to perceive the world, and interact with the external environment through hands, feet, vocal cords, etc. AI products interact with the external environment through `Intelligent Agents`. The `Intelligent Agent` perceives external information via `sensors` and interacts with the external environment via `actuators`.

An AI robot's `Intelligent Agent` can be an infrared rangefinder, camera, thermometer, hygrometer, etc., while the `Intelligent Agent`'s `actuators` can be various motors, robotic arms, etc.

An AI software product's `Intelligent Agent` can be a camera, keyboard, microphone, etc., while the `Intelligent Agent`'s `actuators` can be software functions, displays, etc.

The `Intelligent Agent` of an AI product has the following characteristics: ① Can adapt to new problem-solving rules; ② Needs to maintain online status and real-time performance; ③ Capable of self-analysis upon behavioral errors and successes; ④ Can learn and improve through interaction with the external environment; ⑤ Can rapidly learn from massive amounts of data; ⑥ Possesses memory-based storage and retrieval capabilities; ⑦ Possesses characteristics representing short-term and long-term memory.

For example, the basic function of a robotic vacuum cleaner is to detect garbage in an enclosed space and clean it. The information a robotic vacuum needs to process includes planar spatial position, cleanliness, and other information.

First, a robotic vacuum must be able to perceive the area size and position of the room's planar space and initialize the house map. During initialization of the house's planar spatial position, the robotic vacuum can calculate the house map by traversing house paths. After map initialization, the robotic vacuum begins daily cleaning. When the robotic vacuum starts the cleaning program and enters a certain position, it needs to judge the cleanliness of the current position and make an operation based on cleanliness: clean or move. If the robotic vacuum judges that cleanliness is below a certain threshold, it drives the cleaner to clean; if cleanliness is above a certain threshold, it drives the axle rollers to change direction and enter the next area, and confirms whether to move left or right based on the house planar map.

The description of the cleaning operation and execution method in the above example is relatively simple, but we can still imagine the working mode of the AI product's `Intelligent Agent`, i.e., how it makes specific execution operations through judgment of the external environment. However, the specific execution process is related to the design pattern of the `Intelligent Agent`. Theoretically, `Intelligent Agents` are divided into simple reflex `Intelligent Agents`, model-based `Intelligent Agents`, goal-based `Intelligent Agents`, utility-based `Intelligent Agents`, and hybrid `Intelligent Agents`.

The structure of a simple reflex `Intelligent Agent` is relatively simple. It can directly generate a response to external environmental stimuli, understand what the real world is like through `sensors`, trigger actions based on established conditions, return action decisions to `actuators`, which then complete the operations, as shown in Figure 5-3.

![](converter-master/example/165201/content_633713_1.jpg)

Figure 5-3

The triggering process of a simple reflex `Intelligent Agent` is like the human knee-jerk reflex. When the knee joint is semi-flexed and the lower leg hangs freely, a light, quick tap on the patellar tendon causes the quadriceps femoris to contract, making the lower leg kick forward rapidly, as shown in Figure 5-4. The knee-jerk reflex involves only two neurons: an afferent neuron and an efferent neuron. The receptor for the knee-jerk reflex is the muscle spindle, which has contractile force. When the patellar tendon is tapped, it causes contraction of the quadriceps muscle fibers; then the afferent neuron transmits the information to the nerve center, which in turn transmits the stretch information back to the quadriceps via the efferent neuron, causing the lower leg to extend forward.

The structure of a model-based `Intelligent Agent` contains a series of model rules. These model rules mainly include: what the real world is like; how the world evolves; what the world will become if an action is taken; what the goal is and what action to take for that goal. The model-based `Intelligent Agent` forms "condition-action rules" through the above content, then forms a cognition of the current state of the world through `sensors`, and combines the world's development logic, the `Intelligent Agent`'s influence methods, how to influence the real world, and other conditions to make action decisions, finally operating on the external environment through `actuators`, as shown in Figure 5-5.

![](converter-master/example/165201/content_633713_2.jpg)

Figure 5-4

![](converter-master/example/165201/content_633713_3.jpg)

Figure 5-5

### 5.5.2 Technical Implementation Path Selection

During product planning, product managers not only need a profound understanding of the value range of AI products and complete the planning and design of requirements, scenarios, and markets, but also need to understand the technical implementation paths of AI products. Of course, the technical implementation paths for AI products are diverse. There is no specific and accurate standard for whether to use traditional algorithms, machine learning algorithms, or artificial neural networks. However, as a product manager, one should have a basic understanding of traditional algorithms, machine learning algorithms, and artificial neural networks to better achieve product goals.

Artificial neural networks can conveniently implement image style transfer; similarly, traditional algorithms can also implement some image style transfers. For example, if we want to convert an image's style to a blur style, we can use traditional algorithms to adjust the pixel values of the image, randomly selecting a pixel value `X` within a pixel region to replace other pixel values. The larger `X` is, the blurrier the image; the smaller `X` is, the clearer the image. The comparative effect is shown in Figure 5-9.

![](converter-master/example/165201/content_633712_7.jpg)

Figure 5-9

Besides converting image styles by changing pixel values, we can also complete image style conversion by adjusting the image's grayscale. The colors of objects displayed on TVs and monitors all use the three primary colors of light: Red (`Red`), Green (`Green`), Blue (`Blue`), i.e., `RGB` three colors. If different proportions of the three primary colors of light are superimposed, new colors can be formed. Therefore, traditional algorithms generally only need to adjust the grayscale of the image to form different types of image styles, such as negative film style, fresh style, warm tone style, high exposure style, etc.

Currently, there are many algorithms for changing grayscale in traditional algorithms. The basic principles are as follows:

(1) Obtain the `RGB` values of each pixel.
(2) Use a grayscale algorithm to calculate a grayscale value.
(3) Replace the image's original `RGB` values with this grayscale value.

Of course, adopting different grayscale algorithms will produce different style types. If `Gray=(Red+Green+Blue)/X` is used to obtain a high-exposure style image, the resulting comparative effect is shown in Figure 5-10.

![](converter-master/example/165201/content_633712_8.jpg)

Figure 5-10

If `Math.max(Red, Green, Blue)` is used, the resulting effect is shown in Figure 5-11.

![](converter-master/example/165201/content_633712_9.jpg)

Figure 5-11

It was mentioned earlier that a blur effect can be achieved by adjusting the pixel values of an image. In addition, a mosaic effect can also be achieved by adjusting pixel values. Randomly selecting a pixel value within a pixel region to replace other pixel values, using the following code, can achieve a `10px×10px` image, achieving a mosaic effect. The comparative effect is shown in Figure 5-12.

![](converter-master/example/165201/content_633712_10.jpg)![](converter-master/example/165201/content_633712_11.jpg)

Figure 5-12

Additionally, we can randomly select a pixel value from the surrounding area of the current pixel value to replace the current pixel value, achieving a frosted glass-like effect. The comparative effect is shown in Figure 5-13.

![](converter-master/example/165201/content_633712_12.jpg)

Figure 5-13

Traditional algorithms can implement image style transfer, and artificial neural networks can do so as well, except the implementation method of artificial neural networks differs from traditional algorithms. For example, the `VGG 16` artificial neural network can easily convert images according to certain feature styles. The `VGG 16` artificial neural network can extract features from the style image, superimpose these features on the original image, and obtain the final generated image according to the features of the style image. The processing logic of the `VGG 16` artificial neural network is shown in Figure 5-14.

![](converter-master/example/165201/content_633712_13.jpg)

Figure 5-14

The author used a children's photo as the original image and a sketch self-portrait by Albrecht Dürer as the style image. After inputting both into a pre-trained `VGG 16` artificial neural network, the resulting image was based on the content of the original image but possessed a sketch style, as shown in Figure 5-15.

![](converter-master/example/165201/content_633712_14.jpg)

Figure 5-15

From the above examples, it can be seen that many projects can be implemented either by traditional algorithms or by artificial neural networks. So, for a specific function, how should we choose?

Generally speaking, when dealing with a problem of low computational intensity, or a relatively simple problem with a closed business line, clear input/output data features, a clear and describable business processing logic, using appropriate traditional algorithms or machine learning algorithms can achieve the goal. However, it should be noted that these two types of algorithms have insufficient fault tolerance, and much data is easily affected by the objective environment, causing image distortion, blurring, deformation, and high data noise. These issues all affect the final results, at which point the advantages of artificial neural networks become prominent. Therefore, when dealing with problems requiring higher flexibility and adaptability, such as image feature processing, semantic processing, speculation, and inference, one can consider choosing artificial neural networks for implementation. The characteristics of traditional algorithms, machine learning algorithms, and artificial neural networks are shown in Table 5-4.

Table 5-4

![](converter-master/example/165201/content_633712_15.jpg)

Artificial neural networks have certain advantages when dealing with some relatively complex problems or open, systemic problems. The internal logical structure of such problems generally cannot be described by explicit rules, and there are fault tolerance requirements, such as object recognition and semantic analysis.

Traditional algorithms or machine learning algorithms can classify and judge based on object features, but if objects undergo deformation, occlusion, etc., traditional algorithms or machine learning algorithms appear incapable, while artificial neural networks are often the best choice.

Therefore, for tasks where internal regularities are unclear, logical structures cannot be described by a set of rules or equations, especially tasks with fault tolerance requirements, such as graphic detection and recognition, feature extraction, prediction, and inference, artificial neural networks are often the most suitable processing means.

For a given practical problem, if it is determined to solve the problem through an artificial neural network, one also needs to understand the applicable characteristics of artificial neural networks: ① The system involves a large amount of data, and this data depends on multiple related parameters; ② Within the scope of the system, a large amount of historical data and samples have been accumulated or collected; ③ The data in the data set is imperfect and contains errors, but this data can describe definite samples.

If the task conforms to the above characteristics, it is more appropriate to implement it through an artificial neural network. Similarly, as a product manager, one also needs to understand which situations are not suitable for artificial neural networks and are more suitable for traditional algorithms with precise computation and precise data management. For example, scenarios requiring high precision in numbers, such as financial information, fixed asset information, bank data, precise control; scenarios requiring deduction, inference, and logic, such as taxation, actuarial science; scenarios requiring statistics, computation, analysis, and reporting, such as sales data analysis, enterprise resource management.

Through a comprehensive understanding of the applicable and inapplicable scenarios of artificial neural networks, it can be known that artificial neural networks are mainly applied in scenarios where traditional algorithms are difficult to implement, such as when data is available but has diversity, high noise, and errors. However, for a project, if the network training time is sufficient, using artificial neural networks merely for the sake of using this technology may lead to poor target product implementation results or failure to implement.

### 5.5.3 Neural Network Design

Generally speaking, applications suitable for artificial neural networks mostly involve graphic recognition or statistical transformation, such as character recognition, object detection, information prediction, process monitoring, signal processing, robot control, etc. This book will focus on explaining how to specifically design artificial neural networks after determining to use them.

Artificial neural networks used in the product implementation process are generally designed by the company's internal algorithm engineers or senior algorithm engineers. Since different artificial neural networks have different characteristics, product managers must communicate fully with algorithm engineers regarding the preconditions for designing artificial neural networks during the design process to facilitate their design.

Communication between product managers and algorithm engineers mainly involves the following aspects:

(1) Resource Constraints and Allocation: The company's strategic planning for the product, the product's functional system, expected boundaries, development cycle, financial investment status, human resource allocation, available equipment, and organizational composition, etc.
(2) Data Resources: Including data volume, data types, data accuracy, and data costs, etc.
(3) Target Requirements: The product's accuracy requirements, performance requirements, consistency requirements, and fault tolerance rate, etc.
(4) Time Requirements: Planned completion time, training time limits, and operation time limits, etc.

After the product manager communicates the preconditions with the algorithm engineer, the algorithm engineer can carry out work such as understanding data and designing the network. When the algorithm engineer designs the artificial neural network, the product manager needs to understand and evaluate the network model's parallelism, adaptability, flexibility, and speed. The network model's parallelism, adaptability, flexibility, and speed will affect the success or failure of the product.

The process of designing an artificial neural network is very complex, specifically involving the selection of network size, training process, input layer design, hidden layer design, output layer design, and many other issues. Generally speaking, non-statistical artificial neural network models should have fewer than 1,000 nodes, and statistical artificial neural network models should have fewer than 300 nodes to achieve higher efficiency.

During the training process of the artificial neural network, the product manager needs to pay attention to its training methods and training time, and make suggestions for this process. Generally, the training process includes supervised learning, unsupervised learning, semi-supervised learning, etc. Supervised learning requires all data used to have correctness result judgments, so supervised learning has high data quality requirements and requires annotation of all data. Unsupervised learning only requires input data, without needing output data result judgments. Semi-supervised learning is where only a portion of the data has result judgments.

Network node design needs to determine node types, network size, number of layers, connection requirements between nodes, and determine learning algorithms and parameters. When designing network nodes, the first step is to determine the node type, based on input type, combination method, and transfer function. Input types are divided into binary and continuous numerical input. Combination methods mainly involve weighted input and combination. Transfer functions mainly include different types of functions, such as linear functions, sign functions, step functions, etc.

The role of the input layer is to acquire data and play a buffering role for the entire artificial neural network. If the data source contains too much invalid data, it will make the training process uncontrollable, so invalid data should be deleted. After deleting invalid data, the preprocessing method for the data should be determined to ensure the data is more meaningful. After data cleaning is complete, the data can be added to the input layer.

Network hierarchy design needs to determine the quantity, size, and connection relationships of different layers within the network. Artificial neural networks have pre-set input and output layers. If the network hierarchy is greater than two layers, there are one or more hidden layers between the input and output layers. Hidden layers can extract data features from the input end, so increasing hidden layers can effectively increase the processing capability of the artificial neural network. Of course, hidden layers cannot be increased arbitrarily, because increasing hidden layers will greatly increase the number of training iterations and training time, complicating the training process. Therefore, many algorithm engineers initially set only one hidden layer and then increase based on training effects.

Some algorithm engineers, to improve the processing capability of the artificial neural network, will add groupings within a hidden layer and process information according to grouped neurons. Specific details such as the number of hidden layer nodes, number of layers, and connections involve a large amount of technical detail and will not be elaborated in this book.

Speed evaluation of the artificial neural network is also very important in the entire design stage. Usually, the running speed of an artificial neural network is measured by "how many connections processed per second," i.e., `connections/second`. The faster the running speed, the more reasonable the design of the artificial neural network. Besides training speed, one should also consider whether the artificial neural network has been fully trained. If training is complete but some nodes are found untrained, there is a problem with the network design, requiring adjustment. During verification, a set of verification data never used by the network can be prepared. If the network performs associative recall on the verification data, and the average error or standard deviation is within an acceptable range, it meets the standard.

Usually, there are four types of output results in artificial neural networks: classification, images, numbers, and optimal solutions. Classification converts data into a finite number of categories for output. Scenarios suitable for classification output include process control, image classification, signal processing, prediction, etc. If the output is an image, it generally requires image processing, image transformation, data conversion, data pattern recognition, image recognition, etc. If the output is a number with specific meaning, such as distance, amount, length, and level, these numbers are generally used in financial prediction, process control. Outputting optimal solutions generally targets `TSP problems` or task optimal processing problems, often used in problem solving and gaming.

After the artificial neural network design and training are complete, the network needs to be verified. During verification, not only the network's accuracy but also the noise caused by the network must be considered. During data selection, 70%~80% of the data in the training data can be selected for training, and the remaining 20%~30% for verification. If the output results of the remaining 20%~30% of data achieve an accuracy rate of over 95%, it proves the network is relatively effective. If the verification effect is poor, it is recommended to reduce the nodes in the hidden layer, improve the network's reasoning ability, or provide more complete training data for retraining.

### 5.5.4 Product Technical Implementation

Developing AI products requires enterprises not only to have strong financial and technical strength but also to be equipped with all-round talents during the R&D process, such as algorithm engineers to design artificial neural networks, development engineers to adjust and optimize algorithms, and data engineers to conduct data training. After the enterprise invests huge costs, whether the product can gain market recognition is entirely unknown. Precisely because of this, some large enterprises have launched enterprise open platforms. These platforms directly provide algorithms needed in specific scenarios to users, who only need to call their interfaces to complete the work.

Currently, AI R&D personnel are in short supply, mostly distributed in startups and tech giants worldwide, while top experts in the AI field are concentrated in major giant companies and research institutes.

It can be said that most traditional or small enterprises find it difficult to build professional AI R&D teams. Even if they can build a relatively professional AI R&D team, reaching the R&D level achievable by major giants or professional large AI enterprises is very difficult. Therefore, many small enterprises choose to use platforms. Small enterprises using large enterprise open platforms can quickly conduct experiments and verify product feasibility, and save costs, especially in idle server costs and labor costs. Not only that, utilizing large enterprise open platforms can also shorten the R&D cycle, and after successful verification, development work can be carried out directly through the large enterprise open platform.

Of course, the author does not recommend over-reliance on these enterprise open platforms: first, over-reliance leads to a lack of technical accumulation by users, limiting their own technical development; second, enterprise open platforms themselves have many uncertainties, and once restrictions or blockades occur, it will directly lead to product failure. Although using enterprise open platforms carries risks, the author also recommends that startups or enterprises willing to transform to AI leverage enterprise open platforms to complete development work in the initial stage.

Although enterprise open platforms have many shortcomings, they cannot obscure the benefits they bring. Currently, many startups and enterprises in traditional industries have chosen enterprise open platforms. Therefore, for AI product managers, understanding open platforms has obviously become a necessary skill. AI product managers learning to use enterprise open platforms is not only to cater to the enterprise but, more importantly, AI product managers can quickly verify the feasibility of their designed products through using enterprise open platforms. `EXr6287u6NNVz+GYLY+7SC6AgGofrpiId/XnwIEoGMEPq3TPzkM5RyXBRbuLcLFjwnCuEHVXaQsGpygWHLFy9g==`

