
```
}
.rating {
    margin-left: 20rpx;
    margin-top: 5rpx;
}
.area {
    margin-left: 5%;
    margin-top: 40rpx;
    border: solid 1px #f1f1f1;
    width: 90%;
    padding: 10rpx;
    font-size: 13px;
}
.public {
    margin-top: 80rpx;
    width: 80%;
}
```

上面代码中使用了一个评分组件，在 components 文件夹下新建一个名为 rating 的组件，在 rating.wxml 文件中编写如下代码；

```
<!--components/rating.wxml-->
<view class='com-rating'>
    <view class='rating-icon' wx:for='{{[1,2,3,4,5]}}' wx:key='*this' bindtap='_handleTap' data-num='{{item}}'>
        <view class='rating-on' style='width:{{rating >= (max/5)*item ? 1 : rating < (max/5)*(item-1) ? 0 : (rating*10)%(max/5*10)/(max/5*10)}}em'>
            <image src='../images/rating_on_icon.png' mode='widthFix' style='width:1em' />
        </view>
        <view class='rating-off' style='width:1em;'>
            <image src='../images/rating_off_icon.png' mode='widthFix' style='width:1em' />
        </view>
    </view>
</view>
```

配置 rating.wxss 文件中的样式表，具体如下：

```
/*components/rating.wxss*/
.com-rating {
    display: inline-block;
    letter-spacing: .3em;
    position: relative;
}
.com-rating .rating-icon,
.com-rating .rating-on,
.com-rating .rating-off {
    display: inline-block;
}
```

## 微信小程序开发实战

```
.com-rating .rating-icon:not(:last-child) {
    margin-right: .2em;
}
.com-rating .rating-on {
    color: black;
    position: absolute;
    overflow: hidden;
    padding: 0;
    margin: 0;
}
.com-rating .rating-off {
    color: #DBDBDB;
    padding: 0;
    margin: 0;
}
```

在 rating.js 文件中进行数据的初始化和触发方法的传递，具体如下：

```
//components/rating.js
Component({
    properties: {
        rating: {
            type: Number,
            value: 10
        },
        max: {
            type: Number,
            value: 5
        },
        disabled: {
            type: Boolean,
            value: false
        }
    },
    methods: {
        _handleTap: function (e) {
            if (this.data.disabled) return;
            const { max } = this.data;
            const { num } = e.currentTarget.dataset;
            this.setData({
                rating: max / 5 * num
            })
            this.triggerEvent('change', { value: max / 5 * num }, e);
        }
    }
})
```

到此，我们完成了发布书评页面的布局，运行代码，效果如图 12-9 所示。

![](img/b8569caa8cc98c6c14051d5a89496447_278_0.png)

图 12-9 书评发布页面

### 12.5.2 发布书评功能

关于发布书评，我们可以借助云开发技术实现，先在云数据库中添加一个 comment 集合，用来存放所有评论数据。

在 database.js 文件中添加两个工具方法，分别用于书评的添加和获取，具体如下：

```
addComment:function(isbn, rate, content, date){
return new Promise((res, rej)=>{
let user = wx.getStorageSync("userInfo");
if (!user.openId) {
network.login().then((data) => {
wx.setStorageSync("userInfo", data.result);
let db = wx.cloud.database();
let comments = db.collection("comment");
comments.add({
data: {
isbn: isbn,
content: content,
author: data.result.openId,
rate: rate,
createDate:date
},
fail: function (error) {
wx.showToast({
title: '发布评论失败',
});
rej(error);
},
success:()=>{
res();
}
})
}}).catch((error) => {
wx.showToast({
    title: '登录失败',
})
});
} else {
let db = wx.cloud.database();
let comments = db.collection("comment");
comments.add({
    data: {
        isbn: isbn,
        content: content,
        author: user.openId,
        rate: rate,
        createDate: date
    },
    fail: function (error) {
        wx.showToast({
            title: '发布评论失败',
        });
        rej(error);
    },
    success:function(){
        res();
    }
})
});
},

getComments:function(isbn){
return new Promise((res,rej)=>{
    let db = wx.cloud.database();
    let comments = db.collection("comment");
    comments.where({
        isbn:isbn
    }).get({
        success:function(comments){
            res(comments);
        },
        fail:function(){
            wx.showToast({
                title: '书评不翼而飞了!! ',
            })
            rej();
        }
    });
});
}
```

在发布书评时，数据库中主要记录了对应图书的 ISBN、发布者的 openId、书评内容、评分及发布时间。在 public.js 文件中编写如下代码：

```js
//pages/public/public.js
import db from '../../tools/database.js'
Page({
  data: {
    textValue:"",
    rate:0,
    isbn:""
  },
  onLoad: function (options) {
    this.data.isbn = options.isbn;
  },
  handleChange: function(event) {
    this.data.rate = event.detail.value;
  },
  bindTextAreaBlur:function(event){
    this.data.textValue = event.detail.value;
  },
  formatTime: function (date) {
    var date = util.getDate(date); //返回当前时间对象
    var year = date.getFullYear()
    var month = date.getMonth() + 1
    var day = date.getDate()
    return [year, month, day].join('-')
  },
  public:function() {
    var date = this.formatTime(new Date());
    db.addComment(this.data.isbn, this.data.rate, this.data.textValue, date).then(()=>{
      wx.showToast({
        title: '发布成功',
        complete: ()=>{
          wx.navigateBack({});
        }
      })
    }).catch(()=>{});
  }
})
```

### 12.5.3 在书籍详情页添加书评模块

发布书评成功后，我们可以在书籍详情页将关于此书籍的相关评论进行展示，在 book.wxml 文件底部添加如下代码：

## 微信小程序开发实战

```
<view class='comment' wx:for="{{comments}}">
    <view class='comment-row'>
        <view class='avatar'>读客</view>
        <text class='id'>{{item.author}}</text>
        <!-- <text class='date'>{{item.createDate}}</text> -->
    </view>
    <view>
        <text class='content'>{{item.content}}</text>
    </view>
    <view class='rating-view'>
        <rating disabled max="5" rating='{{item.rate}}'/>
    </view>
</view>
```

对应样式表如下：

```
.comment-row {
    display: flex;
    flex-direction: row;
    align-items: center;
}
.avatar {
    border-radius: 50%;
    width: 80rpx;
    height: 80rpx;
    background-color: #a1a1a1;
    color: white;
    padding: 10rpx;
    line-height: 80rpx;
    text-align: center;
    font-size: 13px;
    margin:40rpx;
}
.id {
    color: #444444;
    font-weight: 300;
    font-size: 14px;
    width: 35%;
    overflow: hidden;
    text-overflow: ellipsis;
}
.content {
    margin-left: 40rpx;
    font-size: 16px;
    font-weight: 100;
    margin-right: 40rpx;
}
.rating-view {
    display: flex;
    flex-direction: row-reverse;
    margin-right: 40rpx;
    margin-top: 20rpx;
    margin-bottom: 120rpx;
}
```

在 book.js 文件的 onLoad 方法中增加拉取评论的代码，具体如下：

```
onLoad: function (options) {
  let isbn = options.isbn;
  db.getBook(isbn).then((res)=>{
    this.setData({
      book: res[0],
      isbn:isbn
    });
    wx.setNavigationBarTitle({
      title: res[0].title,
    })
  }).catch((error)=>{
    wx.showToast({
      title: '获取不到图书信息',
    })
  });
  db.getComments(isbn).then((comments)=>{
    this.setData({
      comments:comments.data
    });
  }).catch(()=>{});
},
```

### 12.6 应用首页开发

读书社区应用程序的广场（首页）用来提供一个书评列表，用户可以在此浏览其他用户发布的读书感悟，也可以找到自己喜欢的图书进行收藏。

### 12.6.1 开发首页基础功能

前面已经编写过一个根据图书 ISBN 查询书评的数据库操作方法，下面提供一个查询所有书评的方法，具体如下：

```
getAllComments: function () {
  return new Promise((res, rej) => {
    let db = wx.cloud.database();
    let comments = db.collection("comment");
    comments.get({
      success: function (comments) {
```

## 微信小程序开发实战

```
res(comments);
},
fail: function () {
  wx.showToast({
    title: '书评不翼而飞!! ',
  })
  rej();
}
});
});
```

关于书评列表的页面设计，可以参考图书详情页的书评列表，再做一些简单的扩展，将图书基础信息展示出来，代码如下：

```
<!--index.wxml-->
<view>
  <view class='comment' wx:for="{{comments}}" bindtap='click' data-index="{{index}}">
    <view class='comment-row'>
      <view class='avatar'>读者</view>
      <text class='id'>{{item.author}}</text>
      <text class='date'>{{item.createDate}}</text>
    </view>
    <view>
      <text class='content'>{{item.content}}</text>
    </view>
    <view class='rating-view'>
      <rating disabled max="5" rating='{{item.rate}}'/>
    </view>
    <view class='book'>
      <view>
        <image mode='aspectFit' src='{{item.book.img}}'></image>
        <view class='book-title'>{{item.book.title}}</view>
        <view class='book-author'>{{item.book.author}}</view>
        <view class='desc'>{{item.book.gist}}</view>
      </view>
    </view>
    <float-button bindtap='addBook'>+</float-button>
  </view>
</view>
```

对应样式配置如下：

```
/**index.wxss**/
.comment-row {
  display: flex;
  flex-direction: row;
  align-items: center;
}
.avatar {
```
