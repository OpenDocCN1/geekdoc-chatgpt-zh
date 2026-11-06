# 微信小程序开发实战

```
search: function () {
  //初始化云开发环境
  wx.cloud.init({
    env: 'test-3a7319'
  });
  //获取数据库索引
  let db = wx.cloud.database();
  //获取数据集
  let teachers = db.collection('teachers');
  teachers.doc("XOP1u0_Mv-8c574W").get({
    success:function(res) {
      console.log(res.data);
    },
    fail:function(error){
      console.log(error);
    }
  });
}
```

调用数据库集合的 doc 方法用来查询一条数据，其中需要传入数据的 id 值，即在数据库中自动生成的 “_id” 属性的值，之后调用 get 方法获取数据。get 方法中可以配置 success、fail 和 complete 回调。运行上面的代码，在控制台将输出查询到的教师对象，如图 10-7 所示。

![](bbox=[0.13, 0.51, 0.93, 0.67])

图 10-7 查询到的教师对象

上面的示例代码在已知数据 id 的情况下进行单条数据的查询，但在实际应用中，更多时候我们会批量拉取数据，而不是根据指定的 id 获取数据，这时我们可以使用 limit 方法，具体如下：

```
//获取集合中的数据总数
teachers.count({
  success:function(res){
    console.log(res.total);
    //批量查询
    teachers.limit(res.total).get({
      success:function(res){
```