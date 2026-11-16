
## 依赖项管理

### 标准 Python 依赖项

这些是 PyPI 上可用的标准 Python 包。AI 平台通过 `pip install` 命令安装了标准依赖项。这类似于独立应用的依赖项解析。定义 AI 平台上的依赖项的标准方法是提及 `setup.py` 文件中的依赖项。`setup.py` 文件需要放置在应用的根目录中。在以下代码片段中可以看到 `setup.py` 文件的模板：

```python
from setuptools import find_packages
from setuptools import setup

REQUIRED_PACKAGES = ['comma separated list of required packages']
setup(
    name='trainer',
    version='0.1',
    install_requires=REQUIRED_PACKAGES,
    packages=find_packages(),
    include_package_data=True,
    description='Setup details and required packages for the training application'
)
```

### 用户定义和自定义依赖项

这些是应用在运行时所需的用户定义包。AI 平台可以使用 `pip install` 命令解决这些依赖性。定制依赖项包需要在运行时可供应用访问，因此，需要提供该包的标准 URI 作为脚本的参数。建议将包文件存储在可访问的 Cloud Storage 位置。使用 `gcloud` 命令时，依赖项可以放在本地计算机上，也可以放在 Cloud Storage 上。AI 平台按照它们在命令中出现的顺序对这些依赖项进行分级。需要将多个依赖项指定为以逗号分隔的列表。

# 为您的训练工作选择正确的计算选项

重要的是为训练工作选择正确的计算选项，以便最佳利用平台资源。这导致训练时间和成本最小化。我们需要为训练作业设置运行时属性。训练工作是 AI 平台上的标准对象。训练工作的结构如下（输入参数以粗体字体突出显示，[请在此链接中找到完整的配置](https://github.com/PacktPublishing/Hands-On-Artificial-Intelligence-on-Google-Cloud-Platform)）：

```json
{
  "jobId": "string", // Required: user defined identifier for the job
  "createTime": "string",          // Output Parameter: indicates when a job was created 
  "labels": {                    // Optional Input Parameter: recommended to be used for organizing 
    "string": "string",              // and troubleshooting the run-time jobs.   
    ...
  },
  "trainingInput": { // Required: specifies the input parameters for the training job.  
    "object": "TrainingInput"
  },
  "predictionInput": {           // Required: specifies the input parameters for the prediction job. 
    "object": "PredictionInput"
  }
}
```

具体来说，我们需要为运行时作业配置填充 `TrainingInput` 或 `PredictionInput` 资源。这些对于特定请求是互斥的，并且在运行时仅需要使用这些输入参数之一。让我们详细查看 `TrainingInput` 参数的以下 JSON 结构（[请在此链接中找到完整的配置](https://github.com/PacktPublishing/Hands-On-Artificial-Intelligence-on-Google-Cloud-Platform)）：

```json
{
  "scaleTier": "enum (ScaleTier)",    // Required: specifies machine types, count of replicas, workers, parameter servers
  "packageUris": [                 // Required: These are the Google Cloud Storage locations for
    "string"                         // the packages containing the training program along with
  ],                               //          additional dependencies
  "pythonModule": "string",          // Required: The python module to run after importing all the 
  "args": [                        // packages and resolving the dependencies 
    "string"
  ],
  "hyperparameters": {             // Optional: The set of hyper-parameters to be tuned. 
    "object": "HyperparameterSpec"
  },
  "region": "string",               // Required: The compute engine region on which the training job will run
}
```

我们将详细研究 `ScaleTier` 和 `HyperParameterSpec` 对象。在此之前，让我们了解在提交预测作业时使用的 `PredictionInput` 对象的 JSON 结构，如以下代码块所示：

```json
{
  "dataFormat": "enum (DataFormat)",        // Required: Format of the input data file (JSON, TEXT, etc.)    
  "outputDataFormat": "enum (DataFormat)",  // Optional: Format of the output data file (default-JSON)
  "inputPaths": [                         // Required: Cloud storage location of input data files
    "string"
  ],
  "maxWorkerCount": "string",               // Optional: Maximum number of workers (default-10)
  "region": "string",                       // Required: Google Compute Engine region
  "runtimeVersion": "string",               // Optional: AI Platform run-time version
  "batchSize": "string",                    // Optional: Number of records per batch (default-64)
  "signatureName": "string",                // Optional: Name of signature defined in the saved model
  "modelName": "string",                    
  "versionName": "string",
  "uri": "string",
  "outputPath": "string"
}
```

选择正确的参数后，可以极大地优化训练和预测表现。我们需要了解各种规模的层和超参数，以进一步优化表现和成本。根据训练数据的数量和算法的复杂性，选择合适的比例尺等级很重要。想法是仅使用适当数量的资源进行训练和预测。这有助于最小化训练单元的成本。根据需要，高级层在 CPU 内核数和 GPU 利用率方面具有额外的容量。但是，随着等级的提高，成本也会增加。GCP 上有各种可用的规模级别，如下所示：

*   `BASIC`：提供单个工作程序实例，适合于学习和实验。这也可以用于小尺寸的概念证明（`POC`）。
*   `STANDARD_1`：提供多个工作程序节点和仅几个参数服务器。
*   `PREMIUM_1`：为大量工作器提供许多参数服务器。
*   `BASIC_GPU`：为单个工作程序实例提供 GPU。
*   `CUSTOM`：此层允许设置主服务器类型，工作程序计数，参数服务器计数和参数服务器类型的自定义值。选择 `CUSTOM` 标度等级时，`TrainingInput` 中的这些参数成为必需参数。

除了比例等级之外，我们还需要仔细选择超参数值，以进一步优化训练效果。

# 为训练工作选择超参数

通过 `TrainingInput` 对象中的 `HyperparameterSpec` 对象控制超参数。以下代码块显示了 `HyperParameterSpec` 对象的结构：

```json
{
  "goal": "enum (GoalType)",            // Required: The type of goal used for tuning [MAXIMIZE/MINIMIZE]
  "params": [                         // Required: The set of parameters to be tuned
    {
      "object": "ParameterSpec"
    }
  ],
  "maxTrials": "number",               // Optional: Number of trials to be attempted (default-1)
  "maxParallelTrials": "number",       // Optional: Number of parallel trials. May reduce quality of optimization
  "maxFailedTrials": "number",         // Optional: Number of failed trials before the hyper parameter tuning job is failed
  "hyperparameterMetricTag": "string", // Optional: TensorFlow summary tag name for optimizing trials. 
  "resumePreviousJobId": "string",     
  "enableTrialEarlyStopping": "boolean",
  "algorithm": "enum (Algorithm)"      // Optional: Search algorithm used by the hyper parameter tuning job
}                                    //          ALGORITHM_UNSPECIFIED / GRID_SEARCH / RANDOM_SEARCH
```

虽然成本是选择计算资源时的基本考虑因素之一，但我们还需要了解，平台对训练资源的使用存在一些限制，这些限制由各种操作的配额设置。让我们简要地看一下 AI 平台施加的配额限制。

由于多租户云平台的固有性质，特定用户和项目所使用的资源需要受到配额的限制和控制，以防止错误地过度使用资源。AI 平台还根据服务请求强加了一些配额限制。分配给项目的任何用户帐户每分钟只能发起一定数量的单个 API 请求。该限制适用于特定的 API 或一组 API，如下所示：

*   作业创建请求：1 分钟内最多可以发出 60 个请求。
*   预测请求：1 分钟内最多可以提出 6,000 个请求。
*   以下用于资源管理的子 API 的请求总数：每分钟最多 300 个请求：
    *   `list/get`（`projects.jobs`，`projects.model`，`projects.models.versions`，`projects.operations`）
    *   `delete/create`（`projects.models`，`projects.models.versions`）
    *   `cancel`（`projects.jobs`，`projects.operations`）
    *   `setDefault`

除服务请求外，每个项目最多有 100 个模型，每个模型最多可以有 200 个版本。

在某些情况下，无法创建具有这些限制的 AI 应用的生产版本。GCP 允许通过管理控制台请求增加配额。此外，AI 平台还限制了虚拟机的并发使用次数，如下所示：

*   并发预测节点数：72
*   预测节点中运行的并发 vCPU 数量：450

用于训练的 GPU 的限制如下：

| **GPU 类型** | **并行 GCP（训练）** | **并行 GPU（预测）** |
| --- | --- | --- |
| `Tesla K80` | 30 | 30 |
| `Tesla P4` | 8 | 2 |
| `Tesla P100` | 30 | 30 |
| `Tesla V100` | 8 | 8 |
| `Tesla T4` | 6 | 6 |

我们需要从可用选项中仔细选择，以优化表现和成本。在下一节中，我们将研究如何在 GCP 上监视 TensorFlow 模型作业。

# 监控您的 TensorFlow 训练模型作业

模型训练工作所需的时间与训练数据量和训练模型的复杂度成比例。应用必须能够报告训练工作的状态。AI 平台提供了三种主要方法来监视训练工作。GCP 控制台提供了用于列出训练作业的用户界面。`gcloud` CLI 和自定义 Python 代码可用于获取训练作业的状态。

以下是用于监视训练作业的 `gcloud` 命令：

```bash
gcloud ai-platform jobs describe job_name
```

该命令返回当前正在运行的作业的状态，由 `job_name` 参数指示，如下所示：

```bash
gcloud ai-platform jobs list --limit=5
```

此命令返回项目平台上当前正在运行的所有作业的列表。

# 总结

在本章中，我们已经了解了如何借助 Cloud ML Engine（AI 平台）在 GCP 上利用无服务器机器学习。我们已经看到了访问 AI 平台的各种方法，以及构建 TensorFlow 应用以及打包和部署模型的方法。我们还研究了在 AI 平台上组织应用，通过使用正确级别的基础架构来优化表现，同时节省成本的最佳实践。最后，我们学习了如何使用命令行工具监视应用。

在下一章中，我们将使用 TensorFlow 模型构建预测应用，并在 GCP 上采用实用的方法进行 AI 应用开发。

# 九、构建预测应用

> 原文：[https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/handson-ai-gcp/09.md](https://gitee.com/apachecn/apachecn-dl-zh/blob/master/docs/handson-ai-gcp/09.md)

云中的预测是关于开发预测机器学习模型，部署机器学习模型的最终版本，以及使用该部署模型使用云原生服务或基础架构从新到达的数据中预测目标值。简而言之，云管理着基础架构和机器学习框架的高层抽象，可以轻松地用于训练或使用您的机器学习模型。

与其他任何云提供商一样，Google Cloud 还提供了使用其本机服务运行基于机器学习的模型预测的功能。在本章中，我们将研究使用 Google Cloud 服务执行预测所涉及的一些步骤，如下所示：

*   基于机器的智能预测概述
*   维护模型及其版本
*   深入研究已保存的模型
*   在 Google Cloud Platform（GCP）上部署模型
*   模型训练实例
*   使用服务端点执行预测

# 基于机器的智能预测概述

预测分析是大数据的推动力：组织收集大量的实时客户数据，并且预测分析使用此历史数据结合消费者的直觉来预测未来事件。预测分析使组织可以使用数据（历史数据和实时数据）从历史角度转换为客户的前瞻性角度。预测分析使公司能够变得前瞻性和前瞻性，预测基于数据的结果和行动，而不是假设。

规范性分析是建议采取行动以利用预测并提供决策选择的下一步骤，以从预测及其后果中受益。可以使用部署在云上的服务进行预测。这些服务可以作为易于使用的 API 公开，并且使分析人员可以轻松使用预测服务，而无需完全了解基础算法的细节。GCP 组件使您可以更轻松地构建、部署和利用预测服务，而所需的精力和成本却更少。

# 了解预测过程

下图描述了在 GCP 上使用预测所涉及的高级步骤（这些步骤将在以下段落和章节中详细说明）：
