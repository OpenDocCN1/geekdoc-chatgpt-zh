按'State'将sorted_df分组。DataFrame的groupby方法维护每个组中的原始行顺序。在一个给定的组中，推文最多的参议员排在第一位，因为代码段 [35] 根据推文数量以降序对参议员进行了排序：

```
In [36]: for index, (name, group) in enumerate(sorted_df.groupby('State')):
    ...:
    strings = [state_codes[name]] # used to assemble popup text
    ...:
    for s in group.itertuples():
    strings.append(
    f'{s.Name} ({s.Party}): Tweets: {s.Tweets}')
    ...:
    text = '<br>'.join(strings)
    marker = folium.Marker(
    (locations[index].latitude, locations[index].longitude),
    popup=text)
    ...:
    marker.add_to(usmap)
    ...
```

我们传递要枚举的分组DataFrame，以便获得每个组的索引，使用该索引在location列表中查找每个州的位置。每个组都有一个名称（已分组的州代码）和该组中的项目合集（该州的两名参议员）。循环操作如下：

+   - 在state_codes词典中查找完整的州名，然后将其存储在strings列表中，之后使用此列表来形成标记的弹出文本。
+   - 用嵌套循环遍历group合集中的项目，每个项目作为包含给定参议员的具名元组返回。我们为当前参议员创建一个格式化的字符串，其中包含此人的姓名、所属政党和推文数量，然后将其加到strings列表的尾部。
+   - 使用HTML对标记文本进行格式化。连接strings列表中的元素，使用HTML的<br>元素创建新行将每个元素与下一个元素分开。
+   - 创建标记。第一个参数是标记的位置（包括经度和纬度的元组）。关键字popup的参数指定了用户单击标记时要显示的文本。
+   - 将标记添加到地图。

# 显示地图

最后，将地图保存进HTML文件：

```
In [37]: usmap.save('SenatorsTweets.html')
```