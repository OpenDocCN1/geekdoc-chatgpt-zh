如上述代码所示，调用 wx.cloud.callFunction()函数可以进行云函数的调用，其中，name 属性用来指定要调用的云函数名称、在 success 的回调中会将云函数的返回数据包装进行返回。

其实，云函数的作用和传统应用开发中后端接口的作用类似，在实际开发中，我们可以将敏感的数据操作和复杂的数据逻辑都编写为云函数，为客户端提供支持。这样不仅可以增强数据的安全性，还可以在不重新发布小程序版本的情况下修改云函数中的逻辑。

# 10.4.2 进行参数传递

在客户端调用云函数时，也可以传递参数给云函数，将其封装在 data 属性中即可，示例代码如下：

```
callCloudFunc: function() {
  wx.cloud.callFunction({
    name:"hello",
    data:{
      name:"小李",
      age:24
    },
    success:function(res){
      console.log(res);
    },
    fail:function(error) {
      console.log(error);
    }
  });
}
```

修改 hello 云函数，具体如下：

```
exports.main = async (event, context) => {
  console.log("参数",event);
  const wxContext = cloud.getWXContext()
  return {
    data:"HelloWorld",
    openid: wxContext.OPENID,
    appid: wxContext.APPID,
    unionid: wxContext.UNIONID,
  }
}
```

其中，event 参数中会封装客户端调用云函数时传递的参数，同时会将调用者的 openid 等信息传递进来。上面的示例代码只是将 event 参数进行了打印，并没有做任何逻辑，因此在客户端调用云函数的过程中看不出参数的传递是否成功，我们可以在云函数后台查看云函数调用日志，日志中会将打印信息进行记录。