该数据在开源数据库许可下可用，见 www.openstreetmap.org/copyright。

# 12.15.2 tweetutilities.py 中的实用函数

这里介绍在前一节的 IPython 会话中使用的实用函数 get_tweet_content 和 get_geo_codes，这两个实用函数都定义在了 ch12 示例文件夹的 tweetutilities.py 脚本文件中。为方便叙述，对于每一个函数，代码的行号都从1开始。

# get_tweet_content实用函数

get_tweet_content函数通过参数 tweet 接收一个 Status 对象并创建一个字典，该字典中包含推文的 screen_name（第4行）、text（第7～10行）和 location（第12和13行，仅当参数 location 为 True 时才包含）。对于推文的文本，先尝试访问 extended_tweet 的 full_text 属性，如果其不可用，再访问 text 属性。

```
def get_tweet_content(tweet, location=False):
    """Return dictionary with data from tweet (a Status object)."""
    fields = {}
    fields['screen_name'] = tweet.user.screen_name
    # get the tweet's text
    try:
        fields['text'] = tweet.extended_tweet.full_text
    except:
        fields['text'] = tweet.text
    if location:
        fields['location'] = tweet.user.location
    return fields
```

# get_geocodes实用函数

get_geocodes函数通过参数 tweet_list 接收包含推文和地理位置的字典列表。如果推文的地理编码成功，则该函数会将经度和纬度添加到 tweet_list 的 tweet 字典中。此代码需要使用 geopy 模块的 OpenMapQuest 类，这里通过下面代码将其导入文件 tweetutilities.py 中：