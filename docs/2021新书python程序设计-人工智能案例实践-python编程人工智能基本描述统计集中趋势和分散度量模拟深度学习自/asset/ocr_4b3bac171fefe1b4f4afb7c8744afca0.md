```
In [22]: for t in df.itertuples():
    ...:     text = ': '.join([t.screen_name, t.text])
    ...:     popup = folium.Popup(text, parse_html=True)
    ...:     marker = folium.Marker((t.latitude, t.longitude),
    ...:                         popup=popup)
    ...:     marker.add_to(usmap)
    ...: 
```

首先，创建一个字符串（文本），其包含用冒号分隔的用户的screen_name（屏幕名称）和推文text（文本），该字符串是单击地图上的相应标记时所显示的内容。第二条语句创建一个folium Popup对象来显示该文本。第三条语句使用元组创建一个folium Marker对象来指定Marker的经度和纬度，popup关键字参数将Popup对象与Marker对象相关联。最后一条语句调用Marker的add_to方法来指定将显示该Marker的Map。

# 保存地图

最后一步是调用Map的save方法将地图保存在HTML文件中，通过双击该HTML文件可以在浏览器中打开它：

```
In [23]: usmap.save('tweet_map.html')
```

所得地图如下所示。注意，读者在运行程序时，地图上显示的Marker会有所不同。

![](bbox=[0.205, 0.61, 0.798, 0.84])

地图数据 ©OpenStreetMap contributors.