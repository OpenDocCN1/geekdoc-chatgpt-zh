| 深度学习框架 | 发布时间 | 发布者 | 支持语言 | 主要特点 | 官方网址 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| PyTorch | 2017 年 | Facebook | C/Lua/Python | 易用、灵活、部署简单、支持分布式训练、强大的生态系统、支持移动端 | https://pytorch.org/ |
| Theano | 2016 年 | 蒙特利尔大学 | Python | 自动微分、GPU 加速、易于编写 | https://pypi.org/project/Theano/ |
| 飞桨 | 2016 年 | 百度 | Python | 同时提供动态图和静态图、兼顾灵活性和效率、精选应用效果最佳算法模型 | https://www.paddlepaddle.org.cn/ |
| 昇思 | 2019 年 | 华为 | Python | 可满足终端、边缘计算、云等全场景 AI 计算需求 | https://www.mindspore.cn |

本章主要介绍常用的 TensorFlow、PyTorch 和飞桨三个深度学习框架。

# 8.2 TensorFlow

# 8.2.1 TensorFlow 简介

2015 年 11 月 6 日，Google 公司宣布推出全新的深度学习开源框架 TensorFlow[1]。TensorFlow 最初由 Google Brain 团队开发，基于 Google 开发的深度学习基础架构 DistBelief 构建。TensorFlow 的中文官方网站：https://tensorflow.google.cn/。Github 主页：https://github.com/tensorflow/tensorflow。

TensorFlow 提供了丰富的工具和组件库，用于构建和训练各种深度学习模型。TensorFlow 核心组件是计算图（Computation Graph），计算图由一系列的操作（Operation）和张量（Tensor）构成。操作定义了计算图中的节点，而张量表示流经这些节点的多维数据。用户可以使用 TensorFlow 的 API 创建计算图，并将其传递给 TensorFlow 的执行引擎，以便在计算设备上执行。TensorFlow 支持各种类型的深度学习任务，包括分类、回归与生成等。TensorFlow 提供了非常丰富的预定义模型和算法，也支持开发者进行模型的自定义。TensorFlow 的灵活性和可扩展性使其成为研究人员和工程师在大规模深度学习项目中的首选深度学习框架之一。

TensorFlow 还有许多其他的功能和特性，如用于数据输入、预处理、数据增强、数据可视化、模型评估和优化的工具。此外，TensorFlow 还支持分布式计算，可以在多个设备上并行执行任务。TensorFlow 的优势之一是其广泛的社区支持和丰富的文档资源。许多深度学习研究人员和开发者使用 TensorFlow，并在社区分享他们的经验、代码和模型，这使得学习和使用 TensorFlow 更加容易和高效。

# 8.2.2 TensorFlow 的主要功能

TensorFlow 是一个以计算图形式描述数值计算的编程框架。计算图是用“节点”（Node）