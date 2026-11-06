# 11.5 完善收藏功能与添加分享功能

新闻客户端应用的 3 个主要页面已介绍了 2 个，收藏页面其实也是一个新闻列表，只是此列表的数据并不是来源于接口，而是从云数据库中读取。

# 11.5.1 编写收藏页面

可以将 category_news 页面的布局代码与样式表代码直接复用到 collect 页面中，需要注意的是，云数据库中存储的数据字段和接口中的字段并不一致，因此在 collect.wxml 文件中也需要做简单的调整，具体如下：

```
<!--pages/collect/collect.wxml-->
<!--海报列表-->
<view class='item' wx:for="{{dataList}}" bindlongtap='longTap' bindtap='clickItem' data-index = "{{index}}">
  <view class='left'>
    <view class='item-title'>
      <text>{{item.title}}</text>
    </view>
    <view class='item-subtitle'>
      <text>{{item.source}}</text>
    </view>
    <view class='item-time'>
      <text>{{item.date}}</text>
    </view>
  </view>
  <view class='right'>
    <image class='item-image' src='{{item.imgUrl}}' mode='aspectFill'></image>
  </view>
</view>
```

上面的代码除了修改了一些数据字段的名称，还添加了一个长按手势，用来删除收藏的某条数据。

在 collect_namger.js 工具类中多提供了两种方法，分别用来获取收藏数据与删除收藏数据，具体如下：

```
getCollection:function() {
  return new Promise((res, rej) => {
    let db = wx.cloud.database();
    let news = db.collection("news");
    news.get({
      success: function (result) {
        res(result.data);
```