```
if(t[0] == 'A3'){    //火焔
    this.data603[2].value = t[1]
}
if(t[0] == 'A4'){    //燃气
    this.data603[3].value = t[1]
}
if(t[0] == 'A5'){    //光栅
    //触发光栅且上次为 0 时切换到人体识别检测
    if(this.data603[4].value == 0 && t[1] == 1){
        this.getVisitor()
    }
    //光栅为 0 时且上次为触发状态时切换到手势识别检测
    if(this.data603[4].value == 1 && t[1] == 0){
        this.gestureRecognition()
    }
    this.data603[4].value = t[1]
}
}
}
}
}
/************************************************************
* 名称： control()
* 功能： 根据单击的模块发送相应查询、控制命令
* 参数： type 表示对应的 MAC 节点， val 表示对应该模块协议参数
************************************************************/
control(type,val) {
    if(this.onlineBtn == '断开'){
        if(type == '601'){
            rtc.sendMessage(this.config.macList.mac_601,`{$val}=?`);    //发起数据查询
        }
        if(type == '602'){
            if(this.modelVal == '自动模式'){
                return swal("请切换为手动模式后进行操作！","","error",{button: false,timer: 2000});
            }
            if(val == 2){
                if(this.data602[val].value == '已关闭'){
                    //发送开关命令
                    rtc.sendMessage(this.config.macList.mac_602,`{OD1=16,D1=?}`);
                }
                if(this.data602[val].value == '一级灯'){
                    //发送开关命令
                    rtc.sendMessage(this.config.macList.mac_602,`{OD1=32,D1=?}`);
                }
            }
            if(this.data602[val].value == '二级灯'){

```