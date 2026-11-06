# 微信小程序开发实战

上述代码封装了一个简单的分页查询数据的接口，并处理是否有下一页的数据逻辑，将结果返回客户端，在小程序端创建一个测试页面，在页面的数据集合中定义相关属性，具体如下：

```
```javascript
data: {
  page:0,
  pageSize:1,
  dataArray:new Array(),
},
```
```

在 onLoad 方法中进行云开发接口的初始化，具体如下：

```
```javascript
onLoad: function (options) {
  wx.cloud.init({
    env:"test-3a7319"
  });
},
```
```

编写一个获取教师列表的方法，具体如下：

```
```javascript
getTeachers:function() {
  wx.cloud.callFunction({
    name:"getTeachers",
    data:{
      page:this.data.page,
      pageSize:this.data.pageSize
    },
    success:(res) => {
      if (res.error){
        console.log("接口请求出错：" + res.error);
        return
      }
      //如果还有数据，则继续请求
      if (res.result.hasMore) {
        //继续请求
        this.data.page = this.data.page+1;
        this.data.dataArray = this.data.dataArray.concat(res.result.data);
        this.getTeachers();
      } else {
        //请求完所有教师数据
        this.data.dataArray = this.data.dataArray.concat(res.result.data);
        console.log(this.data.dataArray);
      }
    },
    fail:function(error) {
      console.log(error);
    }
  });
}
```
```