| auto-pause-if-open-native | 跳转到其他原生页面时是否自动静音 | 布尔值 |

| bindstatechange | 播放状态发生变化时触发的回调配置 | 函数 |

| bindfullscreenchange | 全屏模式切换时触发的回调配置 | 函数 |

| bindnetstatus | 网络状态发生变化时触发的回调配置 | 函数 |

调用 wx.createLivePlayerContext 能为当前页面实例化一个 LivePlayerContext 上下文，该上下文提供了对直播播放器的操作能力，相关接口汇总在表 5-18。

表 5-18 LivePlayerContext 可调用接口

| 方法名 | 参数 | 作用说明 |

|---|---|---|

| stop | { success:方法调用成功 fail:方法调用失败 complete:方法调用完成 } | 终止播放 |

| mute | { success:方法调用成功 fail:方法调用失败 complete:方法调用完成 } | 静音输出 |

| pause | { success:方法调用成功 fail:方法调用失败 complete:方法调用完成 } | 暂停播放 |

| resume | { success:方法调用成功 fail:方法调用失败 complete:方法调用完成 } | 恢复播放 |

| requestFullScreen | { success:方法调用成功 fail:方法调用失败 complete:方法调用完成 direction:设置全屏方向 } | 进入全屏状态 |

| exitFullScreen | { success:方法调用成功 fail:方法调用失败 complete:方法调用完成 } | 退出全屏状态 |

直播业务的服务端搭建较为复杂，若追求快速上线可采用第三方直播后台，例如腾讯云直播。可访问：

https://cloud.tencent.com/

使用微信账号登录后完成实名认证并开通云直播服务，进入管理控制台后通过辅助工具完成推流域名与播放域名的设置，如图 5-4 所示。

[PRE0]

图 5-4 进行直播服务域名配置

域名配置完成后，将生成的推流地址和播放地址填入小程序相应组件即可实现直播功能。

## 5.3 地图组件概览

很多应用场景都需要展示地图，小程序内置的 map 组件提供了丰富的定制能力，使用起来非常便捷。

### 5.3.1 map 组件的实际运用

map 组件可依据经纬度把对应区域渲染在页面上，并在其上叠加标记点、线段、多边形等元素。

在测试工程新建 map 页面，在 map.wxml 中写入如下结构：

[PRE1]

上述示例中 markers 用于放置地图标记，polyline 用于绘制线条。在 map.js 中声明对应数据，示例代码如下：

微信小程序开发实战

[PRE2]

map 组件常用配置见表 5-19。

表 5-19 map 组件的常用属性

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| longitude | 中心点经度 | 数值 |

| latitude | 中心点纬度 | 数值 |

| scale | 地图缩放等级，默认 16 | 数值，5～18 |

| markers | 地图标记点集合 | 数组 |

| polyline | 地图标注线段 | 数组 |

| polygons | 地图标注多边形 | 数组 |

| circles | 地图标注圆形 | 数组 |

| include-points | 自动缩放以使指定标记尽在可视区 | 数组 |

| show-location | 是否显示当前位置定位点 | 布尔值 |

| subkey | 个性化地图的授权 key | 字符串 |

| enable-3D | 是否渲染 3D 建筑 | 布尔值 |

| show-compass | 是否显示罗盘 | 布尔值 |

续表

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| enable-overlooking | 是否开启俯视模式 | 布尔值 |

| enable-zoom | 是否允许缩放操作 | 布尔值 |

| enable-scroll | 是否允许拖动操作 | 布尔值 |

| enable-rotate | 是否允许旋转操作 | 布尔值 |

| bindmarkertap | 标记点被点击时的回调，参数包含标记 id | 函数 |

| bindcallouttap | 气泡被点击时的回调，参数包含标记 id | 函数 |

| bindregionchange | 可视区域变化时的回调 | 函数 |

| bindtap | 地图被点击时的回调 | 函数 |

| bindupdated | 地图渲染完成时的回调 | 函数 |

注意：

map 组件属于原生组件，若需在其上叠加自定义视图，需借助 cover-view 组件进行封装。

### 5.3.2 在地图上添加标记点

markers 属性用于定义地图上的点位，点位在实际业务中应用广泛，如标注餐厅、目的地等。markers 需传入数组，数组元素为标记对象。可配置项见表 5-20。

表 5-20 标记点可配置项

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| id | 标记点的标识 | 数值 |

| latitude | 标记点纬度 | 数值 |

| longitude | 标记点经度 | 数值 |

| title | 标记点标题 | 字符串 |

| zIndex | 标记点层级 | 数值 |

| iconPath | 标记点图标路径 | 字符串 |

| rotate | 旋转角度 | 数值 |

| alpha | 透明度 | 数值 |

| width | 图标宽度 | 数值 |

| height | 图标高度 | 数值 |

| callout | 标记点气泡弹窗配置 | 对象 |

| label | 标记点旁标签配置 | 对象 |

| anchor | 锚点定位，{ x:0~1, y:0~1 } | 对象 |

标记对象的 callout 用于设置点击标记后弹出的气泡，相关配置见表 5-21。

表 5-21 标记点 callout 可配置项

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| content | 弹窗文本内容 | 字符串 |

| color | 文本颜色 | 颜色值 |

| fontSize | 文字大小 | 数值 |

| borderRadius | 边框圆角 | 数值 |

| borderWidth | 边框宽度 | 数值 |

| borderColor | 边框颜色 | 颜色值 |

| bgColor | 背景色 | 颜色值 |

| padding | 文本内边距 | 数值 |

| display | 显示时机 | BYCLICK 单击显示；ALWAYS 始终显示 |

| textAlign | 文本对齐方式 | left 左对齐；right 右对齐；center 居中 |

标记对象的 label 用于设置标记旁的文本标签，相关属性见表 5-22。

表 5-22 标记 label 属性

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| content | 标签文本 | 字符串 |

| color | 文本颜色 | 颜色值 |

| fontSize | 文字大小 | 数值 |

| anchorX | 相对标记的横坐标 | 数值 |

| anchorY | 相对标记的纵坐标 | 数值 |

| borderColor | 边框颜色 | 颜色值 |

| padding | 内边距 | 数值 |

| textAlign | 文本对齐方式 | left 左对齐；right 右对齐；center 居中 |

### 5.3.3 在地图上绘制线段

前文示例已用 polyline 绘制线段，polyline 需传入数组，数组中每个 line 对象描述一组线段。可配置属性见表 5-23。

表 5-23 line 对象属性

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| points | 线段顶点坐标数组，每个点包含 latitude 与 longitude | 数组 |

| color | 线段颜色 | 颜色值 |

| width | 线段宽度 | 数值 |

| dottedLine | 是否为虚线 | 布尔值 |

| arrowLine | 是否带箭头 | 布尔值 |

| arrowIconPath | 箭头图标路径，arrowLine 为 true 时生效 | 字符串 |

| borderColor | 边框颜色 | 颜色值 |

| borderWidth | 边框宽度 | 数值 |

### 5.3.4 在地图上绘制闭合多边形

map 组件支持添加多边形覆盖物，用于标注区域。polygons 属性需传入数组，数组元素为多边形对象，示例代码如下：

[PRE3]

多边形对象可配置项见表 5-24。

表 5-24 多边形对象属性

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| points | 构成多边形的坐标点数组 | 数组 |

| strokeWidth | 边线宽度 | 数值 |

| strokeColor | 边线颜色 | 颜色值 |

| fillColor | 填充颜色 | 颜色值 |

| zIndex | 图形层级 | 数值 |

### 5.3.5 在地图上绘制圆形

circles 属性用于添加圆形覆盖物，示例代码如下：

[PRE4]

圆形对象可配置项见表 5-25。

表 5-25 圆形对象属性

| 属性名 | 作用说明 | 取值 |

|---|---|---|

| latitude | 圆心纬度 | 数值 |

| longitude | 圆心经度 | 数值 |

| color | 线条颜色 | 颜色值 |

| fillColor | 填充颜色 | 颜色值 |

| radius | 半径，单位米 | 数值 |

| strokeWidth | 线条宽度 | 数值 |

### 5.3.6 MapContext 控制对象

通过 wx.createMapContext 可获取页面中 map 组件的上下文对象，若页面存在多个地图组件，需传入对应组件的 id。

MapContext 可用方法见表 5-26。

表 5-26 MapContext 可用方法

| 方法名 | 参数 | 作用说明 |

|---|---|---|

| getCenterLocation | { success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 获取当前地图中心经纬度 |

| getRegion | { success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 获取当前视野范围，返回东北角与西南角坐标 |

| getScale | { success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 获取当前缩放级别 |

| includePoints | { points:标记经纬度数组 padding:内边距设置 success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 缩放地图以展示所有指定标记 |

| moveToLocation | 无 | 将地图移动至当前位置 |

| translateMarker | { markerId:要移动的标记 id destination:目标经纬度 autoRotate:是否自动旋转 rotate:旋转角度 duration:动画时长 animationEnd:动画结束回调 success:设置接口调用成功的回调函数 fail:设置接口调用失败的回调函数 complete:设置接口调用完成的回调函数 } | 平移指定标记点 |

## 5.4 canvas 组件

小程序中可通过 canvas 组件实现自定义绘图。虽然组件库已十分丰富，但对于样式复杂的自定义绘制，canvas 提供了完善的绘图接口。

### 5.4.1 canvas 组件的使用方式

在测试工程创建 canvas 页面，在 canvas.wxml 中写入如下代码：

[PRE5]

代码创建了一个宽度自适应、高度 400px 的画布组件。借助 CanvasContext 上下文对象可在画布上绘制图形，示例代码如下：

[PRE6]

运行后页面会呈现一个宽高均为 100px 的矩形，如图 5-5 所示。