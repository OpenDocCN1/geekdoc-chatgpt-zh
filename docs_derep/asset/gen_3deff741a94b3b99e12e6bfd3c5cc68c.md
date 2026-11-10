|    011     |    150.3333     |

|    012     |    168.6000     |

|    013     |    176.2667     |

|    014     |    248.0667     |

|    015     |    281.6667     |

|    016     |    327.9333     |

|    017     |    363.5333     |

|    018     |    375.4000     |

|    019     |    387.0000     |

|    020     |    432.2000     |

|    021     |    454.6000     |

|    022     |    445.9333     |

|    023     |    463.7333     |

|    024     |    482.1333     |

|    025     |    496.2000     |

6. 最后，我们将形象化地表现出：

[PRE0]

到此为止，DGA 的训练阶段与可视化演示都已收尾。

# 运行机理

本篇秘籍对深度遗传算法做了优化。参照论文《深度神经进化：遗传算法是训练深度神经网络进行强化学习的强劲备选》，`MUTATION_POWER` 超参被设定为 `0.02`。由于优化路径不依赖梯度下降，因此整个过程无需调用 `PyTorch` 完成梯度计算，也就不需要环境构建的梯度支持。

随后我们初始化了一批智能体，并让它们按预设的 `generations` 代数循环演化，每一代都会记录全部个体的累积奖励。筛选环节挑出得分靠前的父代个体，把它们的索引传入 `child_agent` 函数来繁衍下一代群体。演化结束后会输出前五名个体的均值分数。

收尾展示沿用了“训练 DQN”秘籍中的 `play_agent` 函数，仅对模型输出的取值范围做了小幅适配。这里选用种群末尾的精英模型来呈现 CartPole 的实时表现，并逐代更新展示，调用的就是 `play_agent`。

## 拓展思路

深度遗传算法的多个超参都可以手工调节，用来观察表现差异，并把各代分数绘制成曲线做对比。

## 参考资料

DGA 的完整论述可查阅 [这里](https://arxiv.org/pdf/1712.06567.pdf) 以及 [这里](https://github.com/uber-research/deep-neuroevolution)。

## 八、在 PyTorch 中生产 AI 模型

> 原文：[`gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/pt-ai-fund/8.md`](https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/pt-ai-fund/8.md) 

本章聚焦如何把 `PyTorch` 的预测能力落地到真实场景。`PyTorch` 早已从研究原型走向工程可用，本章会介绍让模型走向生产的几项关键手段。所谓部署，就是把模型交付给终端用户或下游系统使用。为此通常要满足可通过 Web 访问、推理延迟可控、以及与其他深度学习框架互通等需求，方便开发团队在项目演进中选用合适的工具，加速从实验室到线上的迁移。

本章会覆盖以下实践：

*   通过 Flask 部署模型
*   生成 TorchScript
*   导出为 ONNX

## 技术要求

本章全部示例均在 Python 3.6 环境下，使用 `PyTorch` 1.3 版本完成。

## 使用 Flask 部署模型

本例演示如何用 Flask 微框架上线一个图像分类服务。挑选 Flask 的原因是它轻量、易上手，适合搭建 RESTful 微服务，且文档完善。我们选用基于 Densenet-161 预训练权重构建的图像分类器来完成本节任务。

### 准备工作

本秘籍需要先安装 Flask，可通过 `pip` 管理器执行安装：

[PRE1]

准备就绪后即可继续。

### 实施步骤

本案例拆分为多个文件，按如下顺序操作：

1. 新建文件 `image_classifier.py`。

2. 完成必要的导入：

[PRE2]

3. 读取存放类别名称的 `.json` 文件：

[PRE3]

4. 定义 `create_model` 函数：

[PRE4]

5. 定义 `image_transformer` 函数：

[PRE5]

6. 随后定义 `predict_image` 函数：

[PRE6]

7. 接着创建 `imageapp.py`。

8. 先导入依赖并引入 Flask：

[PRE7]

9. 实例化 Flask 应用并加载分类器模型：

[PRE8]

0. 配置路由：

[PRE9]

1. 编写路由对应的处理函数：

[PRE10]

2. 最后在运行 `imageapp.py` 时启动 Flask 服务：

[PRE11]

3. 使用如下命令启动服务：

[PRE12]

执行后 Flask 服务器会开始监听，可通过 `http://127.0.0.1:5000/` 访问并发送 `POST` 请求。

4. 用 Postman 等工具测试接口，示例请求体如下：

[PRE13]

查看接口返回：

[PRE14]

返回结果为：

[PRE15]

本秘籍展示了基于 Flask 的简易模型上线方式。

### 运行机理

本例借助 Flask Python 框架以 RESTful API 的形式提供推理服务。先在 `image_classifier.py` 中加载 `idx_class.json` 内的类别名。

该文件第一个函数负责载入在 ImageNet 上以 1,000 类训练好的 `densenet161` 预训练模型，并切到评估模式后返回模型。第二个函数把输入图片转成张量并做预处理，使用 `PIL` 的 `Image` 模块读取原始像素。第三个函数把图片转张量送入模型，输出图像中物体的名称。

切换到 `imageapp.py` 后，用 Flask 搭建 Web 服务。通过 `app = Flask(__name__)` 创建应用实例，并用 `create_model` 生成模型。随后注册 `/predict` 路由接收 `POST` 请求，并定义 `predicted` 函数作为回调。`request.files` 承载上传文件，校验 `image` 参数是否存在。图像数据随后传入前面定义的 `predict_image` 进行推理。`jsonify` 保证响应格式为 JSON，`app.run(debug=True)` 启动服务并处理请求。

### 拓展思路

本秘籍开启了 `debug=True` 调试模式，该模式不适合生产环境。原生的 Flask 服务器抗压能力有限，实际上线建议配合 `gunicorn` 与 `nginx` 部署。

### 参考资料

[Flask 官方文档](http://flask.palletsprojects.com/en/1.1.x/)

[gunicorn + Nginx 部署指南](https://www.digitalocean.com/community/tutorials/how-to-serve-flask-applications-with-gunicorn-and-nginx-on-ubuntu-18-04)

[PyTorch 官方 Flask REST API 替代实现](https://pytorch.org/tutorials/intermediate/flask_rest_api_tutorial.html)

## 创建一个 TorchScript

`TorchScript` 是对原生 `PyTorch` 模型的中间表示，可让模型在高性能运行时如 C++ 中执行。它把模型序列化并做优化，生成的代码可在不依赖 Python 的进程中加载。`TorchScript` 提供了捕获模型结构的工具，而 `PyTorch` 的动态特性依旧足以描述完整定义。创建方式有两种：跟踪与脚本编译，本秘籍将同时演示这两种方法。

### 实施步骤

按以下步骤生成 `TorchScript`：

1. 先写一个简单的网络结构：

[PRE16]

2. 基于该类实例化模型：

[PRE17]

3. 生成两组随机张量作为输入：

[PRE18]

4. 随后执行 `jit.trace`：

[PRE19]

5. 将张量喂入 `traced_cell`：

[PRE20]

6. 可通过如下代码查看计算图：

[PRE21]

>>traced_cell.graph

graph(%self : ClassType<MyCell>,

      %input : Float(3, 4),

      %h : Float(3, 4)):

  %1 : ClassType<Linear> = prim::GetAttrname="linear"

  %weight : Tensor = prim::GetAttrname="weight"

  %bias : Tensor = prim::GetAttrname="bias"

  %6 : Float(4, 4) = aten::t(%weight), scope: MyCell/Linear[linear] # /home/<user>/.local/lib/python3.6/site-packages/torch/nn/functional.py:1370:0