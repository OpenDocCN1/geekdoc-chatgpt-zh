memoryCheckMillis = currentMillis;

printMemoryInfo();

}

// 留出少量空闲，避免调用yield()
delay(1); // 采用1ms微延时替代yield

}

[PRE0]

// 底层环境初始化与基础配置

ESP.wdtDisable(); // 关闭默认看门狗
ESP.wdtEnable(WDTO_8S); // 重新使能8秒超时看门狗
Serial.begin(115200); // 启动115200波特率串口输出

[PRE1]

strip.begin(); // 初始化灯带驱动芯片
strip.show(); // 首次点亮设为全黑

[PRE2]

void setup_wifi() {

  Wi-Fi.mode(WIFI_STA); // 工作模式设为STA客户端
  Wi-Fi.setSleepMode(WIFI_NONE_SLEEP); // 关闭Wi-Fi休眠以保持在线
  Wi-Fi.setAutoReconnect(true); // 打开自动重连开关

}

[PRE3]

client.setServer(mqtt_server, 1883);    // 设置MQTT服务器地址端口
client.setCallback(callback);            // 注册消息回调处理

[PRE4]

uint32_t Wheel(byte WheelPos) {

    WheelPos = 255 - WheelPos;            // 将色相值倒置
    if (WheelPos < 85) {

        return strip.Color(255 - WheelPos * 3, 0, WheelPos * 3);

    }

    // 其余区间按公式计算RGB

}

[PRE5]

void updateRGBBreath( ) {

    breathBrightness += fadeAmount;        // 亮度按当前步长增减
    if (breathBrightness <= 0 || breathBrightness >= 180) {

        fadeAmount = -fadeAmount;          // 触顶触底时反向

    }

}

[PRE6]

unsigned long currentMillis = millis( );

if (currentMillis - previousEffectMillis >= effectInterval) {

    previousEffectMillis = currentMillis;

    // 触发一次灯效刷新

}

[PRE7]

void callback(char *topic, byte *message, unsigned int length) {

  feedWatchdog( );                     // 进入回调立刻喂狗
  String messageTemp;

  for (int i = 0; i < length; i++) {

    messageTemp += (char)message[i];  // 把字节流拼接成字符串

  }

}

[PRE8]

if (messageTemp == "1") {

  modeSwitch = 0;                     // 切换为普通点亮模式
  Open( );                            // 立刻执行开灯
} else if (messageTemp == "3") {

  modeSwitch = 1;                     // 切换为呼吸灯模式
  breathBrightness = 0;               // 将呼吸亮度清零

}

[PRE9]

void checkWi-FiConnection( ) {

  if (Wi-Fi.status( ) != WL_CONNECTED) {

    Wi-Fi.reconnect( );               // 调用快速重连
    int attempts = 0;

    while (Wi-Fi.status( ) != WL_CONNECTED && attempts < 10) {

      feedWatchdog( );                 // 重连期间不断喂狗
      delay(500);

      attempts++;

    }

  }

}

[PRE10]

if (!client.connected( ) && (currentMillis - mqttReconnectMillis >= reconnectInterval)) {

    mqttReconnectMillis = currentMillis;

    tryMqttConnect( );                // 发起MQTT重连尝试

}

[PRE11]

void updateRainbowDisplay( ) {

    for (uint8_t i = 0; i < strip.numPixels( ); i++) {

        strip.setPixelColor(i, Wheel((i + lampStep) & 255));

    }

    lampStep = (lampStep + 1) & 0xFF;  // 步进值取模255循环

}

[PRE12]

void updateRainbowCycle( ) {

    for (uint16_t i = 0; i < strip.numPixels( ); i++) {

        strip.setPixelColor(i, Wheel(((i * 256 / strip.numPixels( )) + rotationPosition) & 255));

    }

    rotationPosition = (rotationPosition + 1) % 256;

}

[PRE13]

void printStatusReport( ) {

    Serial.print("运行时长: ");

    Serial.print(millis( ) / 1000);    // 运行总秒数
    Serial.println(" 秒");

    Serial.print("Wi-Fi信号强度: ");

    Serial.print(WiFi.RSSI( ));        // 信号强度dBm
    Serial.println(" dBm");

}

[PRE14]

void printMemoryInfo( ) {

    Serial.print("可用堆内存: ");

    Serial.print(ESP.getFreeHeap( ));    // 剩余堆内存字节数
    Serial.println(" 字节");

}

[PRE15]

void feedWatchdog( ) {

    ESP.wdtFeed( );    // 喂狗以避免复位

}

[PRE16]

wdtTicker.attach(1.0, feedWatchdog); // 定时器每秒触发喂狗