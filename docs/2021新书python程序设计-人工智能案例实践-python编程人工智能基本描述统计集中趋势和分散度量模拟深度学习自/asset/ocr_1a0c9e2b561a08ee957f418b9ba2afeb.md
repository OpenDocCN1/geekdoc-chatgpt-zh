文，其 'latitude' 和 'longitude' 的值为 NaN。通过调用 DataFrame 的 dropna 方法，可以删除所有包含 NaN 无效数据的行：

```
In [17]: import pandas as pd
In [18]: df = pd.DataFrame(tweets)
In [19]: df = df.dropna()
```

# 使用 Folium 创建地图

现在，创建一个 folium Map 对象，其将被用于绘制推文位置：

```
In [20]: import folium
In [21]: usmap = folium.Map(location=[39.8283, -98.5795],
........ tiles="Stamen Terrain",
........ zoom_start=5, detect_retina=True)
.......
```

location 关键字参数指定了一个序列，该序列包含地图中心点的经度和纬度坐标，这里给出的值是美国大陆的地理中心（见 http://bit.ly/CenterOfTheUS）。实际绘制的一些推文位置可能在美国之外的地方，当打开地图时并不会马上看到这些位置。此时，可以使用地图左上角的 +和- 按钮进行放大和缩小，也可以使用鼠标拖动地图来平移地图，以查看任何位置。

zoom_start 关键字参数指定了地图的初始缩放级别，较低的值对应较大的显示范围，而较高的值则对应较小的显示范围。本例中将 zoom_start 设置为 5，表示显示整个美国大陆。detect_retina 关键字参数使 folium 能够检测高分辨率屏幕，即当在高分辨率屏幕上显示地图时，folium 会从 OpenStreetMap.org 请求更高分辨率的地图并相应地更改缩放级别。

# 为推文位置创建弹出标记

下面遍历 DataFrame 并将包含每条推文文本的 folium Popup 对象添加到 Map 中。本例使用 itertuples 方法根据 DataFrame 的每一行数据创建一个元组，每个元组都包含一条推文数据的所有属性：