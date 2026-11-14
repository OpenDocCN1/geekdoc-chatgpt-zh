# # 处理回复

此时，我们已准备好处理西班牙语发言者的回复。

+   * 步骤6：显示
+     Press Enter then speak the Spanish answer
+     等待按下Enter键。当按下Enter键后，APP显示：
+     Recording 5 seconds of audio

西班牙语发言者说出回复并记录下语音。由于我们不会讲西班牙语，所以要使用Watson的文本转语音服务来预先录下Watson用西班牙语读出的回复 “El baño más cercano está en el restaurante”，然后用足够大的音量播放该音频，并通过计算机的麦克风进行录制。在ch13文件夹中提供了预先录制好的音频文件SpokenResponse.wav。如果使用此文件，需要在按Enter键后快速播放，因为APP仅记录5秒钟的音频。为确保音频能够快速加载和播放，可以在按Enter键开始录制前预先播放一次。五秒钟后，APP显示：

```
Recording complete
```

+   * 步骤7：与Watson的语音转文本服务进行交互，将西班牙语音频转录为文本并显示结果，如下：
+     Spanish response: el baño más cercano está en el restaurante

+   * 步骤8：使用Watson的语言翻译服务将西班牙语文本翻译成英语并显示结果，如下：
+     English response: The nearest bathroom is in the restaurant

+   * 步骤9：将英语文本传递给Watson的文本转语音服务，将文本转换为音频文件。

+   * 步骤10：播放生成的英语音频。

# ## 13.6.3 SimpleLanguageTranslator.py脚本代码分析

为了更好地解释SimpleLanguageTranslator.py脚本的源代码，可将其分为连续编号的小块。我们使用类似于第3章中使用的自上而下的方法进行讲解。最上面的内容是：