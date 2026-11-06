```
},
//扩展生命周期方法
ready() {
    this.log();
},
//对组件的方法进行扩展
methods: {
    log:function(){
        console.log(this.properties.name,this.data.version,"准备完成",Date());
    }
}
});
module.exports = behavior;
```

上面的代码中有十分详尽的注释，这个行为方法的作用是对组件的外部数据和内部数据进行扩展，并且实现了组件准备完成的生命周期函数 ready()。

在需要使用打印功能的组件中混入上面的 behaviors 对象，示例代码如下：

```
//components/custom-button/custom-button.js
var count = 1;
const log = require("../../behaviors/timelogbehavior.js");
Component({
    properties: {
        buttonTitle:{
            type:String,
            value:"按钮",
            observer:function(){
                console.log("按钮标题修改了");
            }
        }
    },
    behaviors:[
        log
    ],
    /**
     * 组件的初始数据
     */
    data: {
        buttonColor:'green'
    },
    ready:function(){
        console.log(count++, "ready");
    },
})
```

在使用组件的地方可以对组件的外部数据进行设置，具体如下：

```
<custom-button button-title="联系客服" data-name="自定义属性" data-location="上海" name="客服按钮"></custom-button>
```