# 微信小程序开发实战

```
}};
    this.data.currentPage += 1;
  } else {
    //上拉加载更多的逻辑
    this.setData({
      dataList: this.data.dataList.concat(res.newslist),
    });
  }
}).catch((error) => {
  wx.stopPullDownRefresh();
  wx.hideLoading();
  console.log(error);
});
},
/**
 * 页面相关事件处理函数——监听用户下拉动作
 */
onPullDownRefresh: function () {
  this.data.currentPage = 1;
  this.getWxNews();
},
/**
 * 页面上拉触底事件的处理函数
 */
onReachBottom: function () {
  this.getWxNews();
}
}
```

上面的代码有详细的注释，这里就不再赘述。完成了上面的代码，新闻客户端小程序主页的页面与逻辑就基本开发完成，运行代码，效果如图 11-4 所示。

![](bbox=[0.42, 0.64, 0.64, 0.89])

图 11-4 新闻客户端主页