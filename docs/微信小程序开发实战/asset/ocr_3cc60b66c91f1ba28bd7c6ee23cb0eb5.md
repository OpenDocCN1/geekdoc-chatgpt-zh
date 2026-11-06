| 属性名 | 意义 | 值 |
|---|---|---|
| fontSizeSetting | 微信中用户设置的字体大小 | 字符串 |
| SDKVersion | 基础库的版本 | 字符串 |
| benchmarkLevel | 设备的性能等级 | 数值 |
| albumAuthorized | 用户是否允许微信使用相册， iOS 有效 | 布尔值 |
| cameraAuthorized | 用户是否允许微信使用相机 | 布尔值 |
| locationAuthorized | 用户是否允许微信使用定位 | 布尔值 |
| microphoneAuthorized | 用户是否允许微信使用麦克风 | 布尔值 |
| notificationAuthorized | 用户是否允许微信开启通知 | 布尔值 |
| bluetoothEnabled | 蓝牙是否可用 | 布尔值 |
| locationEnabled | 地理位置是否可用 | 布尔值 |
| wifiEnabled | Wi-Fi 网络是否可用 | 布尔值 |

与 wx.getSystemInfo 方法对应，wx.getSystemInfoSync 方法用来同步获取系统信息，调用这个方法后会直接返回一个 Object 对象，该对象中封装的属性与表 9-2 列举的一致。

# 9.1.2 小程序更新机制

小程序的启动分为两种方式，分别是冷启动与热启动。当用户打开过小程序，并且在一定时间内再次打开就会进行热启动，即小程序不会重新启动，而是从后台切换到前台。如果用户是首次打开小程序或小程序被微信关闭后用户再次打开，则会进行冷启动。

如果小程序版本更新，对于热启动则不会进行新版本的加载，对于冷启动，微信会异步下载新版本但是不启动新版本，而是直接启动本地的旧版本小程序，当小程序下次冷启动时才会启动新的版本。我们也可以通过更新管理相关接口强制用户使用新版本。

首先，调用 wx.getUpdateManager 方法可以获取全局的更新管理器 UpdateManager 对象，该对象中封装的方法用来进行更新操作，如表 9-3 所示。

表 9-3 UpdateManager 对象可调用的方法

| 方法名 | 意义 | 参数 |
|---|---|---|
| applyUpdate | 强制小程序重启并使用新版本 | 无 |
| onCheckForUpdate | 检查更新 | 函数，其中将会将是否有版本更新信息返回，结果中的 hasUpdate 字段表示是否有新版本 |
| onUpdateFailed | 设置小程序更新失败后的回调函数 | 函数 |
| onUpdateReady | 设置小程序更新完成后的回调函数，可以在此回调中调用 applyUpdate 方法进行强制更新 | 函数 |