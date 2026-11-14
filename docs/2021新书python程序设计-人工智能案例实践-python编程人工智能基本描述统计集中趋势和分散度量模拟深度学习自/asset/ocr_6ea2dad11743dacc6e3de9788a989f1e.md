第70~85行代码可以用更密集的语句来替换

```
return result['results'][0]['alternatives'][0]['transcript']
```

但我们倾向于使用多个独立的简洁的语句。

# translate函数

要访问Watson语言翻译服务， translate函数（第89~111行）会 首先创建一个LanguageTranslatorV3类的对象language_translator， 将服务版本（’2018-05-31’）、之前设置的API密钥和服务的URL作为 参数传递。第93和94行代码使用LanguageTranslatorV3的translate方 法来调用语言翻译器服务，传递两个关键字参数，如下：

+   - text是要被转换为其他语言的字符串。
+   - model_id是语言翻译服务理解原始文本并将其翻译为适当语言所使用的预定义模型。本例采用的模型是IBM的预定义翻译模型之一 —‘en-es’（英语到西班牙语）或‘es-en’（西班牙语到英语）。

```
89 def translate(text_to_translate, model):
90 """Use Watson Language Translator to translate English to Spanish
91 (en-es) or Spanish to English (es-en) as specified by model."""
92 # create Watson Translator client
93 language_translator = LanguageTranslatorV3(version='2018-05-31',
94 iam_apikey=keys.translate_key)
95
96 # perform the translation
97 translated_text = Language_translator.translate(
98 text=text_to_translate, model_id=model).get_result()
99
100 # Get 'translations' list. If method translate's text argument has
101 # multiple strings, the list will have multiple entries. We passed
102 # one string, so the list contains only one element.
103 translations_list = translated_text['translations']
104
105 # get translations_list's only element
106 first_translation = translations_list[0]
107
108 # get 'translation' key's value, which is the translated text
109 translation = first_translation['translation']
110
111 return translation # return the translated string
112
```

translate方法会返回一个DetailedResponse类对象。getResult方法 会返回一个JSON文档，其结构如下图所示。