# 初始化视觉检测模块

```python
# 实例化行人检测引擎
detector = PersonDetection(model_path=c_dir + '/.../models/person_detection')

# 执行推理流程
output = detector.inference(input_frame, None)

# 解码返回的Base64图像数据
rendered_frame = detector.base64_to_image(output["result_image"])

# 窗口渲染与交互
cv.imshow('Detection Preview', rendered_frame)
if cv.waitKey(1) & 0xFF == ord('q'):
    break

# 资源释放
cap.release()
cv.destroyAllWindows()
```

[PRE0]bash
```bash
$ mkdir -p ~/aiedge-exp
```

[PRE1]bash
```bash
$ unzip edge_grating.zip
$ unzip aicam.zip -d edge_grating
```

[PRE2]javascript
```javascript
// 云端认证与边缘节点配置
const config = {
  user: {
    id: '12345678',       // 智云平台账号
    key: '12345678',       // 对应密钥
    addr: 'ws://api.zhiyun360.com:28080',  // 云端WebSocket入口
    edge_addr: 'http://192.168.100.200:4001'  // 边缘网关地址
  },
  // 设备唯一标识映射表
  macList: {
    mac_603: '01:12:4B:00:E5:24:1F:F1'  // Sensor-C 型安防探头
  }
};
```

[PRE3]bash
```bash
$ cd ~/aiedge-exp/edge_grating
$ chmod +x start_aicam.sh
$ conda activate py36_tf114_torch15_cpu_cv345  # Ubuntu 20.04 专用运行环境
$ ./start_aicam.sh
```

[PRE4]javascript
```javascript
// 建立云端长连接通道
function establishLink() {
  const rtc = new WSNRTCConnect(config.user.id, config.user.key);
  rtc.setServerAddr(config.user.addr);
  rtc.connect();

  // 连接就绪处理
  rtc.onConnect = () => {
    isOnline = true;
    setTimeout(() => {
      if (isOnline) {
        cocoMessage.success('云端通道已就绪，正在拉取设备状态...');
        // 主动轮询传感器数据
        rtc.sendMessage(config.macList.mac_602, config.sensor.mac_602.query);
      }
    }, 200);
  };

  // 掉线重连提示
  rtc.onConnectLost = () => {
    isOnline = false;
    cocoMessage.error('云端连接中断，请排查网络或核验 ID/Key');
  };

  // 下行报文解析器
  rtc.onmessageArrive = (mac, payload) => {
    if (!isValidJsonWrapper(payload)) return;

    const kvPairs = stripBraces(payload).split(',');
    kvPairs.forEach(pair => {
      const [key, val] = pair.split('=');
      if (key === undefined || val === undefined) return;

      // 针对 mac_602 执行器的指令分发
      if (mac === config.macList.mac_602) {
        console.log('执行器反馈:', key, val);
        if (key === 'DI') {  // 数字量输入/开关量
          const isActive = (parseInt(val) & 8) !== 0;
          $('#icon').attr('src', isActive ? './img/icon-on.gif' : './img/icon-off.png');
        }
      }
    });
  };
}

// 辅助：校验首尾大括号
function isValidJsonWrapper(str) {
  return str.startsWith('{') && str.endsWith('}');
}
// 辅助：去首尾大括号
function stripBraces(str) {
  return str.slice(1, -1);
}
```

[PRE5]
```
视频流访问模板：
http://<edge_gateway_ip>:4001/stream/<算法标识>?camera_id=<摄像头索引>
示例：http://192.168.100.200:4001/stream/handpose_detection?camera_id=0
```

[PRE6]javascript
```javascript
// 订阅手势识别算法的图像流
const streamUrl = '/stream/handpose_detection?camera_id=0';
const evtSource = new EventSource(streamUrl);

evtSource.onmessage = (e) => {
  const packet = JSON.parse(e.data);
  
  // 实时渲染推理后的画面
  $('#img_box > img').attr('src', `data:image/jpeg;base64,${packet.result_image}`);

  // 节流处理交互数据
  if (interactionThrottle) {
    interactionThrottle = false;
    const { result_data } = packet;
    // 后续业务逻辑...
  }
};
```