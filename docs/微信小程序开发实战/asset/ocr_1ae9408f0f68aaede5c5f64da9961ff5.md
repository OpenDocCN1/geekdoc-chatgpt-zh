# 微信小程序开发实战

```
requestUserAddress: function () {
    wx.authorize({
        scope: 'scope.address',
        success: function () {
            wx.chooseAddress({
                success: function (res) {
                    console.log(res);
                }
            })
        }
    })
}
```

运行代码，第一次请求使用用户的地址权限时会弹出授权弹窗，如图 9-3 所示，如果用户进行了选择，则之后不会再弹出授权框。

如果用户允许授权，则调用 wx.chooseAddress 方法进入地址选择页面，如图 9-4 所示。

![](bbox=[0.215, 0.39, 0.415, 0.62])

图 9-3 用户权限授权框

![](bbox=[0.655, 0.39, 0.855, 0.62])

图 9-4 用户地址选择页面

# 9.3.2 获取用户信息

调用 wx.getUserInfo 方法可以获取用户信息，该方法可以设置一个对象参数，对象中可配置的属性如表 9-8 所示。

表 9-8 wx.getUserInfo 方法可配置的属性

| 属性名 | 意义 | 值 |
|---|---|---|
| withCredentials | 是否带上登录信息 | 布尔值 |
| lang | 设置获取到的用户信息所使用的语言 | 字符串：en，英文；zh_CN，简体中文；zh_TW，繁体中文 |
| success | 设置成功后的回调函数 | 函数 |
| fail | 设置失败后的回调函数 | 函数 |
| complete | 设置接口调用完成的回调函数 | 函数 |