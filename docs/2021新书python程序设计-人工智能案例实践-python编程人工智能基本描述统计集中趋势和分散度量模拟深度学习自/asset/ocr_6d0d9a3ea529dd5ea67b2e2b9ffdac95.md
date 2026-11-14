+   - SpeechToTextV1类用来将音频文件传递给Watson的语音转文本服务，并接收包含文本转录内容的JSON文档；
+   - LanguageTranslatorV3类用来将文本传递给Watson的语言翻译服务，并接收包含翻译文本内容的JSON文档；
+   - TextToSpeechV1类用来将文本传递给Watson的文本转语音服务，并接收以指定的语言读出文本的音频。

```
1 # SimpleLanguageTranslator.py
2 """Use IBM Watson Speech to Text, Language Translator and Text to Speech
3 APIs to enable English and Spanish speakers to communicate."""
4 from watson_developer_cloud import SpeechToTextV1
5 from watson_developer_cloud import LanguageTranslatorV3
6 from watson_developer_cloud import TextToSpeechV1
```

# 导入的其他模块

下面代码中的第7行导入包含Watson凭据的keys.py文件，第8~11行导入支持APP音频处理功能的模块，包括：

+   - pyaudio模块用来录制麦克风的音频；
+   - pydub和pydub.playback模块用来加载和播放音频文件；
+   - Python标准库的wave模块用来保存WAV（波形音频文件格式）文件。WAV是一种流行的音频文件格式，由Microsoft和IBM合作开发。这款APP使用wave模块将录制的音频保存为.wav文件，并将其发送给Watson的语音转文本服务进行转录。

```
7 import keys # contains your API keys for accessing Watson services
8 import pyaudio # used to record from mic
9 import pydub # used to load a WAV file
10 import pydub.playback # used to play a WAV file
11 import wave # used to save a WAV file
12
```

# 主程序： run_translator函数

run_translator函数（第13~54行）是这款APP的主程序部分，它调用了多个在脚本后面定义的函数。为了方便讨论，我们将run_translator函数分解为10个步骤。在步骤1（第15~17行）中，用英语提示用户按Enter键，然后说出问题。函数record_audio录制5秒钟的音频并将其存储在english.wav文件中：