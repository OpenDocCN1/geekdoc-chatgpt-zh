```
```javascript
/****************************************************************************************
 * 初始化变量
****************************************************************************************/
var myZCloudID = "12345678";               //智云账号
var myZCloudKey = "12345678";              //智云密钥
var SensorA = "01:12:4B:00:E3:7D:D6:64";   //Sensor-A 节点 MAC 地址
var channel = '${SensorA}_A2';             //传感器数据通道

var rtc = new WSNRConnect(myZCloudID, myZCloudKey);         //创建数据连接服务对象
var myHisData = new WSNHistory(myZCloudID, myZCloudKey);    //创建历史数据服务对象
var LightIntensity;

/****************************************************************************************
 * 与云服务连接，监听和解析实时数据并显示
****************************************************************************************/
$(function(){
    rtc.setServerAddr("api.zhiyun360.com:28080");    //设置服务器地址

    rtc.connect();

    rtc.onConnect = function() {    //连接成功回调函数
        rtc.sendMessage(SensorA, {"A2=?"});    //查询光照度的初始值
        $("#ConnectState").text("数据服务连接成功！");
    };

    rtc.onConnectLost = function() {    //数据服务掉线回调函数
        $("#ConnectState").text("数据服务掉线！");
```
```