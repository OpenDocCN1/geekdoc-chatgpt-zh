
#!/bin/bash
IMAGE=--image-family=tf-latest-cpu
INSTANCE_NAME=dlvm
GCP_LOGIN_NAME=<proxy@gmail.com> # CHANGE THIS
gcloud config set compute/zone us-central1-a # CHANGE THIS
echo "Launching $INSTANCE_NAME"
gcloud compute instances create ${INSTANCE_NAME} \
      --machine-type=n1-standard-2 \
      --scopes=https://www.googleapis.com/auth/cloud-platform,https://www.googleapis.com/auth/userinfo.email \
      ${IMAGE} \
      --image-project=deeplearning-platform-release \
      --boot-disk-device-name=${INSTANCE_NAME} \
      --metadata="proxy-user-mail=${GCP_LOGIN_NAME}"
echo "Looking for Jupyter URL on $INSTANCE_NAME"
while true; do
   proxy=$(gcloud compute instances describe ${INSTANCE_NAME} 2> /dev/null | grep dot-datalab-vm)
   if [ -z "$proxy" ]
   then
      echo -n "."
      sleep 1
   else
      echo "done!"
      echo "$proxy"
      break
   fi
done

这是该自动化脚本的伪代码：

*   定义虚拟机映像类型
*   提供 Google Cloud Platform 凭证
*   设置将在后台进行计算的计算区域
*   定义实例大小
*   遍历计算实例，并在实例上查找 Jupyter 笔记本 URL

在下一部分中，我们将探索 AI 笔记本，并学习如何将其用于特定任务。

# 使用 Google AI Platform 笔记本

您可以使用 AI Notebooks JupyterLab 平台设置本地笔记本，也可以从 Git 存储库克隆。在用户界面中，单击顶部菜单上的 Git 图标以克隆存储库，如以下屏幕截图所示：

![](Images/62bbd7188d3bea7d925c19f7fbe563d9.png)

如我们所见，Google Cloud Platform 允许我们选择各种 Python 版本，以及创建特定类型支持文件的方式：

![](Images/2b18dc79d0360c2b7ae5559effce17a7.png)

Jupyter 笔记本允许通过命令行界面从 Git 存储库提取代码。您可以使用左窗格中的文件浏览器查看此代码。这是一个方便的功能，使多个开发人员可以轻松无缝地进行协作。

# 自动执行 AI 笔记本

代码开发完成并可基于运行时配置参数运行后，即可使用名为 Papermill 的库来执行和评估这些笔记本。让我们来看看 Papermill 可以为我们做些什么：

*   该库允许您生成多个笔记本并并行运行。
*   Papermill 还可以帮助您收集和汇总笔记本集合中的指标。
*   它还支持从不同位置读取或写入信息。这意味着您可以将输出笔记本存储在另一个存储系统中，该系统具有更高的耐久性，并便于访问可靠的流水线。
*   撰写本文时，Papermill 刚新增了对 Google Cloud Storage 存储桶的支持。我们将在本章演示如何使用这一新功能。

下图显示了 Papermill 如何与 Google Cloud AI Notebooks 一起使用：

![](Images/07ab28044bed2a09597694134df7eee1.png)

Papermill 可让您改变笔记本工作流的范式。由于 Papermill 不会修改原始笔记本（这在笔记本环境中通常是缺失的），因此接收到的特性已被添加到我们的工作流定义中。我们的输入（一个 JSON 笔记本文档）和输入参数被视为不可变的执行文档，这些文档会生成不可变的输出文档。

该输出文档包含已执行的代码、输出和日志，以及可随时轻松重现的可复现模板。Papermill 从多个位置读写的能力是其另一大特点。为提供可靠的流水线，我们可将输出笔记本存储在高耐久且易访问的位置。请使用 Google Cloud 存储桶存储输出笔记本。这将输出笔记本作为独立文档隔离开来，从而为用户提供最大程度的支持。这使我们能轻松分析诸如连接服务或 Google Cloud 存储前缀等操作。

用户可以使用这些连接并调试问题，验证结果并生成新模板，而不会影响初始工作流程。此外，由于 Papermill 管理其运行时进程，您无需笔记本或其他基础设施即可针对笔记本内核执行代码。这消除了在托管笔记本服务的简化上下文中执行时的某些复杂性。请查看下图，以更深入地了解 Papermill 库：

![](Images/535a136febb531255d600e0b11935f9b.png)

以下代码是创建深度学习 VM 的常规方法。但是，请记住，必须选择包含要运行笔记本的关键依赖项的 VM。如果您的笔记本需要 PyTorch，反之亦然，请不要尝试使用 TensorFlow 镜像：

```
# Compute Engine Instance parameters
export IMAGE_FAMILY="tf-latest-cu100" 
export ZONE="us-central1-b"
export INSTANCE_NAME="notebook-executor"
export INSTANCE_TYPE="n1-standard-8"
# Notebook parameters
export INPUT_NOTEBOOK_PATH="gs://my-bucket/input.ipynb"
export OUTPUT_NOTEBOOK_PATH="gs://my-bucket/output.ipynb"
export PARAMETERS_FILE="params.yaml" # Optional
export PARAMETERS="-p batch_size 128 -p epochs 40" # Optional
export STARTUP_SCRIPT="Papermill ${INPUT_NOTEBOOK_PATH} ${OUTPUT_NOTEBOOK_PATH} -y ${PARAMETERS_FILE} ${PARAMETERS}"

gcloud compute instances create $INSTANCE_NAME \
        --zone=$ZONE \
        --image-family=$IMAGE_FAMILY \
        --image-project=deeplearning-platform-release \
        --maintenance-policy=TERMINATE \
        --accelerator='type=nvidia-tesla-t4,count=2' \
        --machine-type=$INSTANCE_TYPE \
        --boot-disk-size=100GB \
        --scopes=https://www.googleapis.com/auth/cloud-platform \
        --metadata="install-nvidia-driver=True,startup-script=${STARTUP_SCRIPT}"

gcloud --quiet compute instances delete $INSTANCE_NAME --zone $ZONE
```

前面的代码中使用的参数如下：

*   `INPUT_NOTEBOOK_PATH`：位于 Cloud Storage 存储桶中的输入笔记本；例如 `gs://my-bucket/input.ipynb`。
*   `OUTPUT_NOTEBOOK_PATH`：位于 Cloud Storage 存储桶中的输出笔记本；例如 `gs://my-bucket/output.ipynb`。
*   `PARAMETERS_FILE`：用户可提供一个 YAML 文件，其中包含笔记本参数值；例如 `gs://my-bucket/params.yaml`。
*   `PARAMETERS`：用户可通过 `-p` 键值对传递参数以执行笔记本；例如 `-p batch_size 128 -p epochs 40`。

以下是我们选择 `*cu-100` 镜像系列的 GPU 实例示例。根据特定要求和版本的可用性，可以使用其他配置。另外，我们需要提供输入和输出笔记本的路径。将所有这些参数传递给 `gcloud` 命令以创建计算实例：

```
IMAGE_FAMILY="tf-latest-cu100" # Or use any required DLVM image.
ZONE="us-central1-b"
INSTANCE_NAME="notebook-executor"
INSTANCE_TYPE="n1-standard-8"
INPUT_NOTEBOOK_PATH=$1
OUTPUT_NOTEBOOK_PATH=$2
GPU_TYPE=$3
GPU_COUNT=$4
STARTUP_SCRIPT="Papermill ${INPUT_NOTEBOOK_PATH} ${OUTPUT_NOTEBOOK_PATH}"
# Create DLVM
gcloud compute instances create $INSTANCE_NAME \
        --zone=$ZONE \
        --image-family=$IMAGE_FAMILY \
        --image-project=deeplearning-platform-release \
        --maintenance-policy=TERMINATE \
        --accelerator="type=nvidia-tesla-${GPU_TYPE},count=${GPU_COUNT}" \
        --machine-type=$INSTANCE_TYPE \
        --boot-disk-size=100GB \
        --scopes=https://www.googleapis.com/auth/cloud-platform \
        --metadata="install-nvidia-driver=True,startup-script=${STARTUP_SCRIPT}"
gcloud --quiet compute instances delete $INSTANCE_NAME --zone $ZONE
```

以下是 CPU 实例示例。这类似于创建 GPU 虚拟机。我们需要选择适当的映像系列，即 `*-cpu`，并提供类似的参数以使用 `gcloud` 命令启动实例：

```
IMAGE_FAMILY="tf-latest-cpu" # Or use any required DLVM image.
ZONE="us-central1-b"
INSTANCE_NAME="notebook-executor"
INSTANCE_TYPE="n1-standard-8"
INPUT_NOTEBOOK_PATH=$1
OUTPUT_NOTEBOOK_PATH=$2
STARTUP_SCRIPT="Papermill ${INPUT_NOTEBOOK_PATH} ${OUTPUT_NOTEBOOK_PATH}"
# Create DLVM
gcloud compute instances create $INSTANCE_NAME \
        --zone=$ZONE \
        --image-family=$IMAGE_FAMILY \
        --image-project=deeplearning-platform-release \
        --machine-type=$INSTANCE_TYPE \
        --boot-disk-size=100GB \
        --scopes=https://www.googleapis.com/auth/cloud-platform \
        --metadata="startup-script=${STARTUP_SCRIPT}"
gcloud --quiet compute instances delete $INSTANCE_NAME --zone $ZONE
```

启动脚本执行以下操作：

1.  使用 TensorFlow 深度学习 VM 和两个 NVIDIA Tesla T4 GPU 创建一个 Compute Engine 实例。
2.  安装 NVIDIA GPU 驱动。
3.  使用 Papermill 工具执行笔记本。
4.  将笔记本结果（所有单元格均已预计算）上传至 Cloud Storage 存储桶，本例中为 `gs://my-bucket/`。
5.  每个单元格执行后，Papermill 会发起一次保存。这可能会产生 429“请求过多”错误，但由库自行处理。
6.  终止 Compute Engine 实例。

如果要查看启动脚本的完整代码，[请查看以下链接](https://shorturl.at/tBJ12)。

希望您现在对 Papermill 库有了充分了解。您需要完成以下步骤，以落实在生产环境中使用 AI 笔记本的流程。我们已在此谨慎介绍这些步骤，但最好也在清单中列出它们：

1.  使用 Google AI 笔记本开发代码
2.  规划并自动化深度学习 VM 镜像配置
3.  规划并自动化 Jupyter 笔记本执行

# Keras 框架概述

Keras 是 Python 深度学习框架，可帮助我们构建和训练几乎任何类型的深度学习模型。Keras 旨在供科学家快速实验。这是一个在 TensorFlow 或 Theano 上运行的开源 Python 库。让我们看一下 Keras 及其功能：

*   它是模块化、快速且易于使用的。
*   它是由 Google 工程师 François Chollet 设计的。
*   Keras 不处理底层计算，而是使用名为后端的库来执行。因此，Keras 是一个高级 API 封装层，拥有低级 API，可在 TensorFlow、CNTK 或 Theano 之上运行。
*   Keras 的高级 API 处理模型创建、层定义及各种输入输出模型的设置。它允许相同代码在 CPU 或 GPU 上无缝运行。
*   Keras 具有主要特性：提供易用的 API 以快速构建深度学习模型原型。
*   它支持卷积（计算机视觉）网络、循环（序列）网络及其任意组合。
*   它支持各种任意网络架构，包括多输入/多输出、层共享、模型共享等。
*   Keras 本质上非常适合构建深度学习模型，从生成对抗网络到图灵机。
*   Keras 是一个模型库，为深度学习系统的发展提供了高级构建块。
*   它不处理张量操作和微分等底层任务，而是依赖经过优化的专用张量库，作为 Keras 的核心后端引擎。

Keras 不强制选择单一张量库，而是以模块化方式管理此问题，并将 Keras 与后端库关联。多个后端引擎可无缝接入 Keras。TensorFlow 后端、Theano 后端和 Microsoft **认知工具包**（**CNTK**）后端目前是当前的三种后端实现。Keras 将来将能够与更多的深度学习引擎一起使用。

Keras 专为 Python 设计，因此易于使用、模块化且易于扩展。该 API 是为人而不是机器而设计的，并且遵循减少认知负载的最佳实践。所有这些独立的模块都可以组合起来，以生成新的模型、神经层、成本函数、优化器、初始化方案、激活函数和正则化系统。有了新的类和函数，可以轻松添加新模型。模型定义在 Python 代码中，而非单独的配置文件中。

使用 Keras 的主要原因源于其设计理念：易于使用。Keras 的优势包括：广泛采用、支持广泛的生产部署、至少支持五种后端引擎（TensorFlow、CNTK、Theano、MXNet 和 PlaidML），以及对各种 GPU 和分布式训练的强大支持。易于教学、易于模型开发。此外，Google、Microsoft、Amazon、Apple、Nvidia、Uber 等都支持 Keras。

Keras 本身不像张量乘积和卷积那样在底层运行，而是依赖后端引擎。虽然 Keras 支持多个后端，但它使用 TensorFlow 作为主要（默认）后端，并由 Google 主力支持。如前所述，Keras API 在 TensorFlow 中打包为 `tf.keras`，现在是 TensorFlow 2.0 中的主要 TensorFlow API。以下是使用 Keras 框架结合 MNIST 数据集进行图像分类的基本示例：
