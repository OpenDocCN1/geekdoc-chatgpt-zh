# 12.5 开发书评相关模块

作为读书社区，其核心是提供一个供读书爱好者分享和交流的平台。每个用户都可以为某本书进行打分和编写评论。有了书评系统，后面就可以扩展出更丰富的功能。

# 12.5.1 发布评论页面开发

图书的详情页提供了两个功能按钮，其中一个按钮用来收藏当前图书，另一个按钮则是为当前图书编写书评。

先在 pages 文件夹下新建一个命名为 public 的文件夹，在其中新建命名为 public 的页面。在 public.wxml 文件中编写如下布局代码：

```
<!--pages/public/public.wxml-->
<view>
  <view class='header'>
    <text>点滴的分享，崭藏的收获</text>
  </view>
  <view class='rating-view'>
    <text>给个评分吧~</text>
    <view class='rating'><rating max="5" rating='0' bindchange='handleChange' class="rating" /></view>
  </view>
  <textarea class='area' adjust-position='{{true}}' bindinput='bindTextAreaBlur' maxlength='300' placeholder='写点什么吧~'></textarea>
  <button class='public' bindtap='public'>发布书评</button>
</view>
```

在 public.wxss 文件中编写样式表，具体如下：

```
/*pages/public/public.wxss*/
.header {
  font-size: 20px;
  font-weight:300;
  margin-left: 50rpx;
  margin-top: 20rpx;
}
.rating-view {
  display: flex;
  flex-direction: row;
  margin-top: 60rpx;
  margin-left: 50rpx;
  align-items: center;
  color: #alalal;
```