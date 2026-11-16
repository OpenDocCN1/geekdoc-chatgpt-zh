
# 四、使用 Cloud AutoML

> 原文：[https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/handson-ai-gcp/04.md](https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/handson-ai-gcp/04.md)

在上一章中，我们了解了机器学习中最流行、最方便的算法之一。在本章中，我们将了解 **Google 云平台**（**GCP**）如何使通过 AutoML 服务轻松使用各种机器学习模型成为可能。AutoML 使开发人员和分析人员在数据工程和机器学习编程方面经验有限的情况下，可以轻松构建机器学习模型。我们将对 AutoML 采取一种实际的方法，并学习如何利用它来构建和部署一些实际用例的模型，例如文档和图像分类、语音到文本转换以及情感分析。

本章将涵盖以下主题：

*   Cloud AutoML 概述
*   使用 AutoML 自然语言的文档分类
*   使用 AutoML Vision API 的图像分类
*   使用语音到文本 API 执行语音到文本转换
*   使用 AutoML 自然语言 API 的情感分析

## Cloud AutoML 概述

有监督的机器学习模型遵循一致的生命周期。监督学习取决于历史数据。基于历史数据，对模型进行训练。模型训练只是建立一个假设函数，该函数能够根据输入或自变量预测输出或因变量。例如，在零售商店的销售预测模型的情况下，历史销售数据用于训练。数据可以横向分布在影响销售的众多因素中。训练数据被随机分为训练数据集和评估数据集。通常，训练数据和评估数据之间分别有 80-20 的比例。

基于所选算法对模型进行训练，然后将其用于基于评估数据集评估准确率。调整训练参数可提高模型的准确率和表现。一旦模型在各种评估样本中都能很好地运行，就可以在实际环境中进行部署和使用了。在此阶段，模型遇到了全新的数据集，而在训练过程中可能看不到。在这里，将假设函数的泛化用于预测。下图描述了通用的监督学习过程：

![](Images/c6351af443956a0d94e11854332b7d17.png)

传统上，在非云环境中，此过程需要由数据科学家完全管理。GCP Cloud AutoML 是一个完全托管的环境，负责所有操作流程、基础架构和模型管理。GCP Cloud AutoML 为与**自然语言处理**（**NLP**）和**计算机视觉**（**CV**）相关的模型提供了接口。在本章中，我们将通过利用 NLP 和 CV 接口的示例用例深入探讨 GCP Cloud AutoML。Cloud AutoML 的优点如下：

*   **易于使用**：机器学习经验有限的开发人员可以轻松地使用 AutoML 平台来训练自定义机器学习模型。可以根据特定的业务用例快速定制模型。底层的复杂性由 GCP 管理，最终用户在训练模型和运行各种实验时无需担心计算能力或存储需求。
*   **高性能**：通过使用 GCP AutoML 引擎进行训练、评估和部署模型，用户可以访问 Google 的最新计算基础架构和分布式计算平台，从而始终如一地提供可靠的结果。根据资源需求与数据量和模型复杂性成比例，对集群进行动态自动缩放。
*   **速度和敏捷性**：上图所示的监督学习过程通过 AutoML 提供的简单直观的用户界面进行全面管理。GUI 使您可以轻松快速地训练、评估、调整和部署模型。由于可以进行各种实验的速度和敏捷性，可以快速尝试和测试不同的模型。可以调整参数并可以快速验证结果。这使得模型开发和部署过程极为灵活。

## AutoML 的工作原理

通过在机器学习模型的训练、评估和部署上创建高层抽象，AutoML 简化了有监督的学习过程。下图显示了 AutoML 的工作原理：

![](Images/94fa0b741b026027c910953e598a9ead.png)

将 AutoML 与传统的有监督的学习管道进行比较，显而易见：

*   AutoML 简化了训练、评估和部署 ML 模型的过程。
*   此外，AutoML 支持通过 RESTful API 与模型进行交互。
*   与 REST API 集成后，可以轻松利用来自各种应用和端点的机器学习模型。
*   AutoML 用户界面通常用于实验和快速测试假设。

但是，REST API 用于训练、评估和利用机器学习模型。这是 AutoML 支持的通用和代表性 API 端点的快速概述。

## AutoML API 概述

RESTful API 被基于 GCP 的 AI 应用用来调用 AutoML 服务。用户界面通常用于概念验证，但是企业应用需要一组丰富的 API 库才能与诸如 AutoML 之类的组件进行交互。在本节中，我们将介绍一组代表性的 API 和这些 RESTful API 中的重要字段。

让我们看一下与 `v1beta1.projects.locations.models` 中的模型进行交互的 API 端点。

### REST 源 – 指向模型位置

在下表中，我们列出了通过模型位置 API 进行的一些基本方法调用。调用应用需要将适当的参数传递给 API 端点，以创建和删除模型并将其用于预测：

| **方法** | **端点** | **说明** | **响应** |
| --- | --- | --- | --- |
| `create` | `POST /v1beta1/{parent}/models` | 创建一个模型 | 完成后在响应字段中返回模型 |
| `delete` | `DELETE /v1beta1/{name}` | 删除模型 | 完成后，在响应字段中返回 `google.protobuf.Empty`，在元数据字段中返回 `deleteDetails` |
| `get` | `GET /v1beta1/{name}` | 获取模型 | 如果成功，则响应主体包含模型的实例 |
| `predict` | `POST /v1beta1/{name}/predict` | 预测 | 执行预测 |

让我们看一下可用于调用 API 的实际有效负载。这是机器学习模型的 JSON 表示形式。以下代码段描述了关键字段和相应的数据类型：

```json
{
  "name": "string",
  "displayName": "string",
  "datasetId": "string",
  "createTime": "string",
  "updateTime": "string",
  "deploymentState": "enum(DeploymentState)",

  // Union field model_metadata can be only one of the following:
  "imageClassificationModelMetadata": {
    "object": "ImageClassificationModelMetadata"
  },
  "textClassificationModelMetadata": {
    "object": "TextClassificationModelMetadata"
  },
  "translationModelMetadata": {
    "object": "TranslationModelMetadata"
  }
  // End of list of possible types for union field model_metadata.
}
```

应用运行时需要提供通用存根，以利用响应内容并利用调用应用中的响应。

让我们看一下前面代码中的字段描述：

*   `name`：这是一个仅输出的字段，代表模型的资源名称。该字段的输出格式为 `projects/{project_id}/locations/{locationId}/models/{modelId}`。
*   `displayName`：此字段是 GCP Web 界面中显示的模型的名称。名称可以由用户选择。管理此字段名称的规则如下：
    *   长度最多为 32 个字符。
    *   它可以包含 ASCII 拉丁字母 `A-Z` 和 `a-z`：
        *   下划线（`_`）
        *   ASCII 数字 `0-9`
    *   第一个字符必须是字母。
*   `datasetId`：此字段指向用于创建模型的数据集的资源 ID。数据集必须属于相同的祖先项目和位置。
*   `createTime`/`updateTime`：这是创建/更新模型时的日期时间戳。GCP 使用具有纳秒精度的 RFC3339 UTC 格式，例如 `2019-03-05T15:01:23.045123456Z`。
*   `deploymentState`：这是模型的当前状态。GCP 平台上模型的可能状态如下：
    *   `DEPLOYMENT_STATE_UNSPECIFIED`：不应使用。
    *   `DEPLOYED`：已部署模型。
    *   `UNDEPLOYED`：未部署模型。

`imageClassificationModelMetadata`、`textClassificationModelMetadata` 和 `translationModelMetadata` 分别用于图像、文本和翻译模型。我们将在本章的后续部分中对此进行解释。

这是用于模型评估的 REST 源 API：`v1beta1.projects.locations.models.modelEvaluations`。

### REST 源 – 用于评估模型

这是获取和列出模型评估的方法调用：

| **方法** | **端点** | **说明** |
| --- | --- | --- |
| `get` | `GET /v1beta1/{name}` | 获得模型评估 |
| `list` | `GET /v1beta1/{parent}/modelEvaluations` | 列出模型评估 |

可以使用简单的 API 列出模型评估参数，并且可以将其用于调用应用中的迭代模型改进。

以下 API 使应用能够在运行时分析模型操作。在某些情况下，由于数据量和模型复杂性，模型训练和评估需要更长的时间。这些操作调用帮助应用向最终用户报告模型训练和评估的状态。

### REST 源 – 操作 API

下表列出了 `v1beta1.projects.locations.operations` API：

| **方法** | **端点** | **说明** |
| --- | --- | --- |
| `cancel` | `POST /v1beta1/{name}` | 在长时间运行的操作上启动异步取消 |
| `delete` | `DELETE /v1beta1/{name}` | 删除长时间运行的操作 |
| `get` | `GET /v1beta1/{name}` | 获取长时间运行的操作的最新状态 |
| `list` | `GET /v1beta1/{name}/operations` | 列出与请求中的指定过滤器匹配的操作 |
| `wait` | `POST /v1beta1/{name}/wait` | 等待指定的长时间运行的操作，直到完成或最多达到指定的超时，并返回最新状态 |

请注意，对于操作 API 的请求是异步的，并且对于调用应用是非阻塞的。这些对于调用应用以报告模型训练和评估的进度很有用。

## 使用 AutoML 自然语言的文档分类

文档分类是一个非常重要的用例，主要用于分析文档或大量文档（例如法律文档）的内容。有时，手动分类需要付出很多努力，而且可能不可行。GCP 提供了易于使用的自然语言界面，可以对其进行自定义训练，以基于 AutoML 进行文档分类。让我们了解一个包含 20 个新闻组的公共可用数据集的分类过程。该数据集可从[这里](http://qwone.com/~jason/20Newsgroups/)下载。这是大约 20,000 个新闻组文档的集合，平均分布在 20 个不同的新闻组中。这些新闻组对应于不同的主题。目标是根据训练数据训练模型，评估模型，最后将其用于文档分类。

### 用于文档分类的传统的机器学习方法

文档分类过程遵循一组标准步骤。在训练分类模型之前，需要对文档内容进行分类，以确保分类的准确率。下图显示了以传统方式使用机器学习进行文档分类的整个过程：

![](Images/ce8b153865ceb0be41b903c99ec60598.png)

与该图所示的方法不同，AutoML 简化了文档分类的流程。

### 使用 AutoML 的文档分类

在本节中，我们将研究 GCP 中的 AutoML 接口，以使用 AutoML 进行文档分类。

#### 导航到 AutoML 自然语言界面

使用您的 GCP 凭据登录[这里](https://console.cloud.google.com)。在导航菜单上，转到**人工智能**部分，然后单击**自然语言**子菜单：

![](Images/5442109b1119a01b50a57d3d61f67993.png)

AutoML 自然语言使上传数据集和训练模型变得容易。在我们的示例中，数据集是新闻组文档的集合。

#### 创建数据集

要创建新数据集，请单击标题栏中的“新建数据集”按钮：

![](Images/d0cdf785f6d996746c2882373aa7b962.png)

为数据集提供唯一的名称（在这种情况下为 `newsgroups`）。GCP 提供以下用于上传数据集的选项：

*   从计算机上载 CSV 文件：CSV 文件应包含 Google Cloud Storage 路径列表和相应的标签，并用逗号分隔。
*   从计算机上载文本项：该界面允许选择多个文本文件或包含多个文件的 ZIP 存档。
*   在云存储上选择 CSV：可以从 Cloud Storage 中选择包含路径和标签的带标签的 CSV 文件。

创建数据集后，可以从 Google Cloud Storage URI 和本地驱动器导入其他项目。

#### 标记训练数据

可以通过在上传到数据集的 CSV 文件中包含标签并使用 AutoML Natural Language UI 来为文本文档添加标签来标记训练数据：

![](Images/2f2fd242f2dc9ded360b626646e20026.png)

创建标记的训练数据集时，请考虑以下提示：

*   创建具有可变长度文档以及各种写作风格和作者的数据集。较高的变化水平可提高模型准确率。
*   这些文档需要对人类读者分类。AutoML 界面取决于预先标记的训练数据，因此模型的准确率取决于手动标记过程的准确率。
*   GCP 建议每个标签有 1,000 个训练文件。每个标签的最小文档数为 10。每个标签的训练样本数量越多，内容的变化也越大，模型的准确率就越高。
*   对于无法归入预定义标签之一的文档，请使用 `None_of_the_above` 或等效标签。这将提高模型的准确率，而不是贴错标签或保留空白标签。

#### 训练模型

创建数据集并对其进行完全标记后，即可对模型进行训练。单击 TRAIN NEW MODEL 按钮，通过提供模型名称来启动异步训练过程。模型名称最多可以包含 32 个字符，并且可以包含字母、数字和下划线。模型训练所需的时间取决于训练数据的数量和变化。训练模型可能需要几分钟到几个小时。一旦模型训练完成，就会向已注册的电子邮件 ID 发送电子邮件通知。

![](Images/c1137cc3fc2dedfb7536490ffaff1b7b.png)

如下面的屏幕快照所示，模型经过训练后，将显示模型属性（例如，模型创建时的日期时间和训练数据的定量性质）以及质量参数（例如**精度**和**召回**）：

#### 评估模型

与传统的机器学习管道不同，在传统的机器学习管道中，我们需要在评估数据集上评估模型，而 GCP 在内部使用测试集中的项目评估模型。在此阶段检查模型的质量和准确率。AutoML 提供了两个级别的聚合质量指标，这些指标指示模型在所有特征和标签以及每个类别标签上的总体训练水平。GCP 报告了以下指标：

*   **AuPRC**（精度和召回曲线下的**面积**）：这表示平均精度。典型值在 0.5 到 1.0 之间。值越高表示模型越准确。
*   **精度和召回曲线**：AutoML 提供了一种交互式方式来设置标签的阈值。较低的阈值会增加召回率，但会降低精度：

![](Images/79537a35f4b894a74705dcf209169caa.png)

*   **混淆矩阵**：这可以可视化模型的准确率，以预测输出类别。它代表评估阶段训练数据中输出标签的百分比：

![](Images/e21b8df59005cb41b14b7958c54bc5e9.png)

这些易于使用的 AutoML 度量标准可用于评估模型的可靠性，从而预测实际数据集上的输出类别。如果混乱程度很高，而准确率和召回率得分很低，则该模型可能需要其他训练数据。

除了用于模型评估的 Web 界面之外，GCP 还提供了一个可编程的 API 接口，用于使用命令行、Python、Java 和 Node.js 进行评估。

#### 命令行

使用以下命令通过将 `model-name` 替换为实际模型名称来获取 JSON 格式的模型评估参数：
