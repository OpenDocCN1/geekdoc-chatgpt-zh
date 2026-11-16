
# 语音识别配置参数

| 参数 | 类型 | 说明 |
|------|------|------|
| `encoding` | 枚举 | `MULAW`/`AMR`（自适应多速率窄带编解码器）/`AMR_WB`（宽带）/`OGG_OPUS`/`SPEEX_WITH_HEADER_BYTE` |
| `sampleRateHertz` | `number` | 该字段定义音频数据的采样率。数据点的范围为 8,000 至 48,000 Hz。对于 `WAV` 和 `FLAC` 音频格式，此字段是可选的。 |
| `audioChannelCount` | `number` | 该字段指示输入音频数据中的多个通道。 |
| `enableSeparateRecognitionPerChannel` | `boolean` | 如果 `audioChannelCount` 大于 1，则需要将该参数显式设置为 `true`。 |
| `languageCode` | `string` | 这是指示对话语言的必填字段。例如，`en-US` 用于表示美国英语。 |
| `maxAlternatives` | `number` | 这是一个可选参数，指示响应中返回的替代识别消息的最大数量。根据训练的模型和语音上下文，服务器返回的值可能少于设置的替代值。参数值范围为 0 到 30。值 0 或 1 将最多返回一个识别，如果该字段不是请求的一部分，则最多返回一个识别。 |
| `profanityFilter` | `boolean` | 这是一个可选属性，如果设置为 `true`，则会过滤出亵渎或淫秽的语言表达式。 |
| `speechContexts[]` | 对象（`SpeechContext`） | 这是一个可选的但重要的属性，可为识别模型提供提示以实现更准确的转录。通过在此对象中发送语音上下文，可以消除由于语音上相似的单词而引起的潜在错误，从而可以基于语音上下文进行更准确的识别。 |
| `enableWordTimeOffsets` | `boolean` | 这是个可选的选项。如果设置为 `true`，则转录语音中的每个单词都将在音频信号中标记其开始和结束时间。默认情况下，此参数的值为 `false`。 |
| `enableAutomaticPunctuation` | `boolean` | 此可选字段仅影响所选的语言，当前可作为实验字段使用。启用后，转录将包括标点文本。默认情况下，该参数设置为 `false`。 |
| `metadata` | `object` | 这是一个可选字段，提供有关音频信号的元数据。该字段是 `RecognitionMetadata` 类型，包含以下子字段：`interactionType`/`industryNaicsCodeOfAudio`/`microphoneDistance`/`originalMediaType`/`recordingDeviceType`/`recordingDeviceName`/`originalMimeType`/`obfuscatedId`/`audioTopic`。元数据为模型提供了其他提示，这些提示用于更准确和上下文相关的转录。 |
| `model` | `string` | 这是一个可选字段，用于选择适当的机器学习模型以执行语音到文本的识别。选择合适的识别模型可以大大提高转录的准确率。如果未设置模型，则根据 `RecognitionConfig` 中的参数选择运行时模型。以下是 GCP 上当前可用模型的列表： |
|  |  | `command_and_search`：最适合语音命令或语音搜索的模型 |
|  |  | `phone_call`：最适合电话对话转录的模型 |
|  |  | `video`：最适合从原始视频数据中提取音频信号的模型 |
|  |  | `default`：未指定或无法使用特定对话模型时的模型 |
| `useEnhanced` | `boolean` | 这是一个可选参数，为使用增强型模型而设置为 `true`。增强型模型比常规模型更准确，成本也更高。 |

## 嵌入式音频内容

通过在请求的 `audio` 字段中发送 `content` 参数，可以将音频内容嵌入到请求主体中。嵌入式内容可以成为 gRPC 或 REST 请求的一部分。对于 gRPC 请求，音频必须与协议缓冲区版本 3 语言规范兼容，并作为二进制数据嵌入。在 REST 请求中发送嵌入式音频时，需要对音频进行 JSON 序列化和 Base64 编码。这是在请求正文中发送 Base64 编码的音频内容的示例：

```json
{
    "config": {
      "encoding": "WAV",
      "sampleRateHertz": 18000,
      "languageCode": "en-US"
    },
    "audio": {
      "content": "XtxhQwJJJCIQABQLLTUJABtAA+gA8AB+W8FZndQvQAyjv..."
    }
}
```

这是 Base64 编码的音频数据的 Python 代码：

```python
import base64

def encode_audio(audio):
    audio_content = audio.read()
    return base64.b64encode(audio_content)
```

## URI 引用音频内容

或者，可以通过使用 URI 指向内容来在请求中发送对音频内容的引用。指向的音频应为原始二进制格式，而不是 Base64 编码格式。URI 指向 Google Cloud Storage 上音频文件的请求示例如下：

```json
{
      "config": {
        "encoding": "WAV",
        "sampleRateHertz": 18000,
        "languageCode": "en-US"
      },
      "audio": {
        "uri": "gs://bucket-name/path_to_audio_file"
      }
}
```

URI 指向的音频文件应该可以通过互联网公开访问和/或可以通过 GCP 上的服务帐户访问。

## 同步请求响应

同步请求提供的时间响应与音频内容的长度成正比。以以下格式接收响应：

```json
{
  "name": "8214202757943088943",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeMetadata",
    "progressPercent": 100,
    "startTime": "2019-09-22T08:16:32.013650Z",
    "lastUpdateTime": "2019-09-22T08:16:55.278630Z"
  },
  "done": true,
  "response": {
    "@type": "type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeResponse",
    "results": [
      {
        "alternatives": [
          {
            "transcript": "One Two Three Four",
            "confidence": 0.97186122,
            "words": [
              {
                "startTime": "1.300s",
                "endTime": "1.400s",
                "word": "One"
              },
              {
                "startTime": "1.400s",
                "endTime": "1.600s",
                "word": "two"
              },
              {
                "startTime": "1.600s",
                "endTime": "1.600s",
                "word": "three"
              },
              {
                "startTime": "1.600s",
                "endTime": "1.900s",
                "word": "four"
              }
            ]
          }
        ]
      },
      {
        "alternatives": [
          {
            "transcript": "one too thee four",
            "confidence": 0.9041967
          }
        ]
      }
    ]
  }
}
```

响应包含两个不同的部分：

### 标头信息

这是有关语音到文本转录过程的标头级信息。标头包含以下字段：

*   `name`：这是语音转文字 API 分配的唯一名称。
*   `metadata`：包括以下内容：
    *   `@type`：此字段表示 GCP 定义的响应类型。该字段指向对象定义 URI。
    *   `progressPercent`：此字段表示转录完成的百分比。
    *   `startTime`：这是语音到文本翻译的开始时间。
    *   `lastUpdateTime`：这是 API 上次更新状态的时间。

### 响应正文

包含以下字段：

*   `@type`：此字段表示由 GCP 定义的响应主体的类型。该字段指向对象定义 URI（`type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeResponse`）。
*   `results`：这是一个收集对象，它是基于音频输入的连续部分的语音到文本转换单元的顺序列表。
*   `alternatives`：每个单独的顺序结果均包含一个或多个具有不同置信度的替代转录。备选方案按置信度降序排列。通常，第一种选择是最好的，可以由应用使用。响应中的替代数目由 `maxAlternatives` 请求参数控制。
*   `transcript`：每个备选方案均显示音频部分的笔录。
*   `confidence`：这是一个数字字段，指示模型与副本的可信度。
*   `words`：在每个替代成绩单中，都有多个单词。（可选）这些单词显示在时间轴上（基于 `enableWordTimeOffsets` 请求参数的值）：
    *   `startTime`：表示输出单词的音频信号内的开始时间
    *   `endTime`：表示输出单词的音频信号内的结束时间
    *   `word`：实际转录

对于同步转录，该请求将发送到以下端点：

```
POST https://speech.googleapis.com/v1/speech:recognize
```

这是一条简单的 HTTP `POST` 消息，以 JSON 格式返回语音识别响应。

## 异步请求

异步或非阻塞请求在请求主体和标头方面相似。但是，当请求旨在异步时，API 方法调用是不同的。调用 `LongRunningRecognize` 方法。响应不会立即返回。`LongRunningRecognize` 的请求将在调用 API 时立即返回脉冲检查消息，而不是包含转录的响应。响应格式如下：

```json
{
  "name": "operation_name",
  "metadata": {
    "@type": "type.googleapis.com/google.cloud.speech.v1.LongRunningRecognizeMetadata",
    "progressPercent": 37,
    "startTime": "2019-09-03T13:24:39.579144Z",
    "lastUpdateTime": "2019-09-03T13:24:39.826903Z"
  }
}
```

该过程将继续转录请求中发送的音频信号。转录完成后，将以 `progressPercent` 为 `100` 发送响应。此时，响应的格式与同步响应的格式相同。

## 流式请求

根据应用的要求，可能需要具有双向流传输功能以实时连续记录音频信号。语音转文字 API 提供了一种在双向流中进行实时转录的方法。发送者应用可以将连续的音频信号流发送到 API，并从服务中接收离散的以及完整的转录形式。即时结果以转录格式表示当前音频，最终响应获得整个转录（类似于同步和异步响应）。

就 API 而言，流请求将作为端点发送到 `StreamingRecognize` 方法。由于该 API 是连续的流式 API，因此会将多个请求发送到具有不同音频窗口的 API。但是，第一条消息必须包含流请求的配置。该配置由 `StreamingRecognitionConfig` 对象定义，该对象向 API 提供提示以处理特定的流音频信号。`StreamingRecognitionConfig` 对象定义如下：

*   `config`：这是我们在本章前面讨论的 `RecognitionConfig` 对象。
*   `single_utterance`：这是一个可选的 `boolean` 标志。当将其设置为 `false` 时，尽管语音中有很长时间的停顿，流识别 API 仍会继续转录输入信号。该流将保持打开状态，直到被调用进程显式关闭为止，或者直到经过了某个时间阈值为止。在这种情况下，API 可能返回多个 `StreamingRecognitionResult` 对象。如果将此标志设置为 `true`，则模型会检测到音频信号中的暂停，并且 API 返回 `END_OF_SIGNAL_UTTERANCE` 事件并完成识别过程。在这种情况下，API 将仅返回一次出现的 `StreamingRecognitionResult`。
*   `interim_results`：这是一个可选标志。如果设置为 `true`，则中间结果由 API 返回；如果设置为 `false`，则 API 仅在转录完成后才返回结果。

API 以 `StreamingRecognizeResponse` 消息对象的形式返回响应。这是流式语音转文本服务 API 返回的唯一对象。响应对象包含以下重要字段：

*   `speechEventType`：这表示基础模型检测到的音频对话中的暂停。API 识别两种事件类型。`SPEECH_EVENT_UNSPECIFIED` 表示未指定任何事件，`END_OF_SINGLE_UTTERANCE` 表示模型已检测到音频信号中的暂停，并且 API 预计流中不会再有其他音频数据。仅当 `single_utterance` 请求参数设置为 `true` 时，才发送此事件。
*   `results`：这是主要包装对象，其中包含转录结果作为集合：
    *   `alternatives`：类似于同步和异步转录请求，此集合提供了具有不同置信度的各种转录选择。
    *   `isFinal`：当模型录制整个音频信号时，此标志设置为 `true`。
    *   `stability`：在流语音识别的上下文中，语音的重叠部分会在移动的时间窗口上被转录。这意味着音频信号内的特定位置可以在后续帧内被转录一次以上。语音到文本模型会生成一个稳定性分数，该分数指示转录发生变化的可能性。分数为 0 表示不稳定的转录将最终改变，分数为 1 表示原始转录将没有任何变化。

# 使用 AutoML 自然语言 API 的情感分析

情感分析是构建智能平台的关键实用功能之一，该智能平台可根据最终用户对服务或主题的感知来推荐业务操作。考虑到数据的上下文和性质的变化，该过程非常复杂。通常，在大多数情况下，数据是非结构化或半结构化的，这使得难以实现较高的准确率。作为一般过程，情感分析可以分为以下步骤：

![](Images/606493f300be5d3bb260920860f57b42.png)

*   **目标设置**：需要在用例的上下文中明确定义情感分析的目标。例如，当要分析购买者的情感时，我们需要将其围绕产品及其功能集中。
*   **文本处理**：基于上下文目标和内容来源，下一步是执行文本处理，这涉及消除噪音和不需要的单词，并组织语和情感内容。
*   **解析内容**：在此阶段，将根据逻辑和有意义的内涵对内容进行解析和分组。根据极性和语义对内容进行细分。
*   **文本细化**：在此阶段需要识别停用词和同义词。
*   **分析和评分**：在此阶段，将根据训练数据和文本的语义对经过解析和精炼的内容进行评分。评分用于训练模型以对新内容进行实际情感分析。

AutoML 自然语言情感分析创建了一个抽象层，使应用开发过程摆脱了情感分析的核心复杂性。一旦以适当的评分精心准备了训练数据，该平台就会通过简单的 Web 界面和 API 来进行模型的训练、评估和部署。在本节中，我们将了解使用 AutoML 在 GCP 上执行情感分析的过程。

该过程中最重要且最耗时的步骤是使用 GCP AutoML 创建用于自然语言情感分析的训练数据。类似于任何其他有监督的学习模型训练过程，我们需要提供示例数据，该数据以整数比例标记文本内容。通常，情感分数从 0 开始，可以根据需要进行细化。整数标签越高，情感越积极。可以基于预期的粒度定义最大情感分数。作为一般准则，情感分数必须从 0 开始，并且在训练数据中不得有任何差距，以进行准确的模型训练。建议在训练数据中使用偶数个样本，每个单独的分数。这将避免模型欠拟合。
