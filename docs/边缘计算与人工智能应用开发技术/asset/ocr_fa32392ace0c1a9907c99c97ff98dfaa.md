```
}
}
}
}
}
********************************************************************************
* 名称: control()
* 功能: 根据单击的模块发送相应的查询或控制命令
* 参数: index 是对应数据列表的索引
********************************************************************************
control(index) {
if (this.ADAS[0].switch) return cocoMessage.error("智能模式下禁止操作!!");
if (this.onlineBtn == '断开') {
if (this.data602[index].value == '已关闭') {
//发送开关命令
rtc.sendMessage(this.config.macList.mac_602, `{OD1=${this.data602[index].val},D1=?}`);
console.log(this.config.macList.mac_602, `{OD1=${this.data602[index].val},D1=?}`);
} else {
//发送开关命令
rtc.sendMessage(this.config.macList.mac_602, `{CD1=${this.data602[index].val},D1=?}`);
console.log(this.config.macList.mac_602, `{CD1=${this.data602[index].val},D1=?}`);
}
} else {
swal("数据服务尚未连接!请检查网络或 IDKEY...", "", "error", { button: false, timer: 2000 });
}
},
```

# 5. 2. 1. 4 项目开发

+   1) 系统框架设计

见4. 1. 1. 3节。

+   2) 开发流程

本项目基于AiCam平台开发，开发流程如下：

+   (1) 项目配置见4. 1. 1. 3节。