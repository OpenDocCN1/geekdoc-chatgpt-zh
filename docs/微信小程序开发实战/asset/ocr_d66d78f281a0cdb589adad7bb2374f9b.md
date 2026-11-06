在上面的代码中，最外层 view 的 container 属性定义在 app.wxss 文件中，代码如下：

```
```
/**app.wxss**/
.container {
  background-color: #f1f1f1;
}
```
```

其他 class 属性都定义在 index.wxss 文件中，代码如下：

```
```
/*pages/index/index.wxss*/
.header {
  background-color: white;
  margin-top: 2rpx;
  padding: 30rpx;
}
.header text {
  color: black;
  font-size: 13px;
  font-weight: 200
}
swiper {
  height: 400rpx;
  margin-top: 20rpx;
}
.item {
  margin-top: 20rpx;
  background-color: white;
  height: 250rpx;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}
.item-title {
  font-size: 14px;
  font-weight: 300;
  margin-left: 30rpx;
  padding-top: 20rpx;
}
.item-subtitle {
  font-size: 12px;
  font-weight: 300;
  margin-left: 30rpx;
  padding-top: 10rpx;
  color: #a9a9a9
}
.item-time {
  font-size: 12px;
  font-weight: 300;
  margin-left: 30rpx;
  padding-top: 30rpx;
}
```
```