```
In [27]: tweets = api.search(q='from:nasa since:2018-09-01', count=3)

In [28]: print_tweets(tweets)
NASA: @WSYIW Our missions detect active burning fires, track the trans-
port of fire smoke, provide info for fire managemen. https://t.co/jx2iUoM-
lly

NASA: Scarring of the landscape is evident in the wake of the Mendocino
Complex fire, the largest #wildfire in California.. https://t.co/Mboo5GD9Om

NASA: RT @NASAglenn: To celebrate the #NASA60th anniversary, we’re explor-
ing our history. In this image, Research Pilot Bill Swan prepares for a_
```

# 搜索主题标签

推文通常包含以#开头的主题标签，以表示某些重要内容，如热门话题。下面的代码用于获取两条包含#collegefootball主题标签的推文：

```
In [29]: tweets = api.search(q='#collegefootball', count=2)

In [30]: print_tweets(tweets)
dmcreek: So much for #FAU giving #OU a game. #Oklahoma #FloridaAtlantic
#CollegeFootball #LWOS

theangrychef: It’s game day folks! And our BBQ game is strong. #bbq #at-
lanta #collegefootball #gameday @ Smoke Ring https://t.co/41kKhQCE7
```