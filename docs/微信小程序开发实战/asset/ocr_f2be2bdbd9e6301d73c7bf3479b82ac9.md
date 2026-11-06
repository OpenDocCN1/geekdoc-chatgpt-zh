# 表 8-7 操作标签栏的相关方法

| 方法名 | 意义 | 参数 |
|---|---|---|
| wx.showTabBar | 显示标签栏 | { animation:设置是否需要动画，布尔值 success:接口调用成功的回调 fail:接口调用失败的回调 complete:接口调用完成后的回调 } |
| wx.hideTabBar | 隐藏标签栏 | { animation:设置是否需要动画，布尔值 success:接口调用成功的回调 fail:接口调用失败的回调 complete:接口调用完成后的回调 } |
| wx.showTabBarRedDot | 设置在标签上显示红点 | { index:要显示红点的标签下标 success:接口调用成功的回调 fail:接口调用失败的回调 complete:接口调用完成后的回调 } |
| wx.hideTabBarRedDot | 设置在标签上隐藏红点 | { index:要隐藏红点的标签下标 success:接口调用成功的回调 fail:接口调用失败的回调 complete:接口调用完成后的回调 } |
| wx.setTabBarBadge | 为某个标签设置徽章信息 | { text:设置徽章显示的文本 index:要显示徽章的标签下标 success:接口调用成功的回调 fail:接口调用失败的回调 complete:接口调用完成后的回调 } |
| wx.removeTabBarBadge | 移出某个标签的徽章信息 | { index:要移除徽章的标签下标 success:接口调用成功的回调 fail:接口调用失败的回调 complete:接口调用完成后的回调 } |