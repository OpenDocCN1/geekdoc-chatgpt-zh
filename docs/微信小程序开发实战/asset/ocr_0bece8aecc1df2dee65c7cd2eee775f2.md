# 5.3.6 MapContext 对象

通过调用 wx.createMapContext 方法可以获取当前页面中地图组件的上下文对象，由于在同一个界面中可能同时存在多个地图组件，所以 wx.createMapContext 方法需要传入 map 组件的 id 值作为参数。

MapContext 对象可调用的方法如表 5-26 所示。

表 5-26 MapContext 对象可调用的方法

| 方法名 | 参数 | 意义 |
|---|---|---|
| getCenterLocation | { success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 获取当前地图中心的经度和纬度，在成功的回调函数中将会经度和纬度信息传入 |
| getRegion | { success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 获取当前地图的视野范围，成功的回调函数中将会地图东北角与西南角的经度和纬度坐标传入 |
| getScale | { success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 获取地图的缩放级别 |
| includePoints | { points:标记经度和纬度坐标组 padding:设置缩放后地图与标记区域的上、右、下、左边距 success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 缩放地图展示地图上所有的标记点 |
| moveToLocation | 无 | 将地图移动到当前定位的位置 |
| translateMarker | { markerId:要移动的标记点 id destination:移动到的目标位置，经纬度对象 autoRotate:设置是否自动旋转，布尔值 rotate:设置旋转角度 duration:设置动画时长 animationEnd:设置动画结束后的回调函数 success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 对某个标记点进行平移 |