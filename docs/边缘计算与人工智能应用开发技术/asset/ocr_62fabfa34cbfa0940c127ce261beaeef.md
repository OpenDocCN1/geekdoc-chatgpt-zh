# 设备发送开关命令

```
********************************************************************************/
gestureRecognition(){
imgData && imgData.close()
let resultThrottle = true                 //将节流结果识别间隔时间设置为 500 ms
let gestureThrottle = true                //将节流阀门开启间隔时间设置为 8 s
let count = {}                            //累计手势识别次数
//请求视频资源
imgData = new EventSource(this.config.user.edge_addrr + this.linkData[1])
console.log(this.config.user.edge_addrr + this.linkData[1]);
//对视频资源返回的数据进行处理
imgData.onmessage = res => {
let {result_image} = JSON.parse(res.data)
this.homeVideoSrc = `data:image/jpeg;base64,${result_image}`
let {result_data} = JSON.parse(res.data)
//对识别到的手势进行判断（设置间隔不得小于 500 ms 一次）
if(result_data && resultThrottle){
resultThrottle = false
if (result_data.obj_num > 0 && result_data.obj_list[0].score > 0.70 && gestureThrottle) {
console.log(result_data);
//设置变量并赋值识别到的手势
let gesture = result_data.obj_list[0].name
//为识别到的每个手势设置为对象属性名，初始值为 1。当某个手势的识别
//次数累计达到 4 次时，向相应的家居设备发送开关命令，并清除计数
if(count[gesture]){
count[gesture] += 1
if(count[gesture] === 4 && gesture !== 'undefined'){
count = {}
gestureThrottle = false
let index
if(gesture === 'one') index = 1
if(gesture === 'two') index = 2
if(gesture === 'three') index = 3
if(gesture === 'four') index = 4
if(gesture === 'five') index = 5
console.log(gesture,index);
if(index){
if(this.data602[index-1].value === '已关闭'){
rtc.sendMessage(this.config.macList.mac_602,`OD1=${this.data602[index-1].val},D1=?`); //发送开关命令
swal(`识别到手势 ${index},打开${this.data602[index-1].name}! `, "",
"success",{button: false,timer: 2000});
}else{
rtc.sendMessage(this.config.macList.mac_602,`CD1=${this.data602[index-1].val},D1=?`); //发送开关命令
swal(`识别到手势 ${index},关闭${this.data602[index-1].name}! ; ", ",
```