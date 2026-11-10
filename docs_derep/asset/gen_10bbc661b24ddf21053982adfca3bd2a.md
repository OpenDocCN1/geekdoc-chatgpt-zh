当用户通过授权确认后，系统会调用 wx.chooseAddress 打开地址选择页，界面效果如图 9-4 所示。

[PRE0]

图 9-3 用户权限授权框

[PRE1]

图 9-4 用户地址选择页面

### 9.3.2 获取用户信息

wx.getUserInfo 用来申请读取用户资料，可传入一个配置对象进行参数设定，支持的配置项见表 9-8。

表 9-8 wx.getUserInfo 方法可配置的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| withCredentials | 是否带上登录信息 | 布尔值 |

| lang | 设置获取到的用户信息所使用的语言 | 字符串：en，英文；zh_CN，简体中文；zh_TW，繁体中文 |

| success | 设置成功后的回调函数 | 函数 |

| fail | 设置失败后的回调函数 | 函数 |

| complete | 设置接口调用完成的回调函数 | 函数 |

调用成功时回调会返回一个用户信息载体，其中包含的属性说明如表 9-9。

表 9-9 用户信息对象中的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| userInfo | 用户信息 | 对象 |

| rawData | 原始数据 | 字符串 |

| signature | 签名信息，用来校验 | 字符串 |

| encryptedData | 包含敏感信息在内的加密数据 | 字符串 |

| iv | 加密算法的初始向量 | 字符串 |

| cloudID | 敏感数据对应的云 ID | 字符串 |

UserInfo 本身封装了对外可见的基础信息，其公开字段如表 9-10。

表 9-10 用户信息对象中公开信息的相关属性

| 属性名 | 意义 | 值 |

|---|---|---|

| gender | 用户的性别 | 0：未知<br>1：男性<br>2：女性 |

| country | 用户所在国家 | 字符串 |

| province | 用户所在省份 | 字符串 |

| city | 用户所在城市 | 字符串 |

### 9.3.3 进行登录操作

小程序里登录流程被封装得比较简洁。先用 wx.checkSession 检测当前登录是否有效，该方法同样支持 success 与 fail 等回调。

若回调走 fail 分支，则说明未登录或登录已过期，这时需要调用 wx.login 发起登录，成功后会拿到 code，code 是临时的登录凭证，后端可用其换取 openid 和 session_key。

一套完整的登录通常由小程序前端、开发者服务器和微信服务端三方配合完成，步骤如下：

- 1. 小程序端调用 wx.login 获取临时凭证 code。
- 2. 前端把 code 传给开发者自有服务端。
- 3. 服务端结合 code 及密钥向微信接口换取 openid 等信息。
- 4. 服务端将 openid 与自有体系用户关联，记录登录状态，并把自有 token 返回给客户端。
- 5. 小程序端缓存 token，后续请求携带该 token 完成鉴权。

## 9.4 调用微信功能插件

微信除基础通信外，还内置了支付、卡券、发票、运动等生活类插件能力，小程序可以直接调用。

### 9.4.1 使用微信支付

移动支付已成日常，微信支付在小程序中的接入也很直接。用户发起购买后，后端需要先调用微信支付下单接口创建订单，再把生成的订单参数传给小程序的支付接口即可拉起支付，具体方法如下：

[PRE2]

上述接口需要传入配置对象，可配置字段见表 9-11。

表 9-11 requestPayment 方法可配置的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| timestamp | 时间戳 | 字符串 |

| nonceStr | 加密使用的随机字符串 | 字符串 |

| package | 订单预支付 ID，由服务端通过下单接口获取 | 字符串 |

| signType | 所使用的签名类型 | 可选值：MD5，HMAC-SHA256 |

| paySign | 签名 | 字符串 |

| success | 支付成功后的回调函数 | 函数 |

| fail | 支付失败后的回调函数 | 函数 |

| complete | 接口调用完成后的回调函数 | 函数 |

### 9.4.2 卡券与发票

微信卡包用于存放公众号的会员卡和票券，小程序已打通卡券能力，可在小程序内打开或添加卡券。注意，仅认证过的小程序才可以使用相关接口，需在小程序后台提交资质完成认证。

调用 wx.addCard 可往卡包添加卡券，参数对象可配置项如表 9-12。

表 9-12 wx.addCard 方法可配置的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| cardList | 需要添加的卡券列表 | 对象数组 |

| success | 添加成功后的回调函数 | 函数 |

| fail | 添加失败后的回调函数 | 函数 |

| complete | 接口调用完成后的回调函数 | 函数 |

cardList 需传入卡券对象，其可配置字段见表 9-13。

表 9-13 cardList 对象中可配置的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| cardId | 卡券的 ID | 字符串 |

| cardExt | 卡券的扩展参数 | 对象 |

对应的 wx.openCard 可用于查看卡包中的卡券，参数形式与 wx.addCard 保持一致。

发票方面，小程序也提供了相关接口，使用前需小程序绑定已认证的公众号并先获取用户授权。

wx.chooseInvoiceTitle 用于获取用户选择的发票抬头，参数对象可设置成功、失败和完成回调，成功回调会返回抬头信息。可配置属性见表 9-14。

表 9-14 wx.chooseInvoiceTitle 方法可配置的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| type | 抬头类型 | 字符串；0，单位；1，个人 |

| title | 抬头名称 | 字符串 |

| taxNumber | 抬头税号 | 字符串 |

| companyAddress | 单位地址 | 字符串 |

| telephone | 手机号码 | 字符串 |

| bankName | 银行名称 | 字符串 |

| bankAccount | 银行账号 | 字符串 |

| errMsg | 错误信息 | 字符串 |

wx.chooseInvoice 则可直接让用户挑选一张发票，返回结果中会包含加密的发票 ID，可据此查询报销信息。

### 9.4.3 获取用户运动数据

wx.getWeRunData 可读取用户的步数记录，该接口仅支持最近 30 天的数据，且调用前必须完成登录并取得授权。调用成功后回调会返回加密的运动数据，用 Session_key 解密后可得到明细列表，列表项结构见表 9-15。

表 9-15 用户微信运动数据对象中的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| timestamp | 时间戳 | 数值 |

| step | 运动步数 | 数值 |

## 9.5 常见的设备功能接口

小程序框架提供了大量与设备交互的能力，如网络状态、Wi-Fi 连接、屏幕亮度、电话与通讯录等。网页环境难以实现的这些功能，在小程序中可直接使用。

### 9.5.1 网络与 Wi-Fi

音频视频类应用对流量敏感，需要在移动网络下提醒用户。小程序可获取当前网络状态并监听变化，当非 Wi-Fi 网络时可在大流量下载前提示用户。

wx.getNetworkType 用于查询当前网络环境，传入常规的 success、fail、complete 配置对象，success 回调中会带上 networkType，其取值见表 9-16。

表 9-16 网络类型列举

| 值 | 意义 |

|---|---|

| wifi | Wi-Fi 网络 |

| 2g | 2G 网络 |

| 3g | 3G 网络 |

| 4g | 4G 网络 |

| unknown | 未知网络类型 |

| none | 无网络 |

wx.onNetworkStatusChange 可监听网络变化，参数为回调，当网络环境改变时触发：

[PRE3]

回调中会包含 isConnected 与 networkType，分别表示是否已联网及网络类型。

框架还提供了一组 Wi-Fi 操作接口，如表 9-17。

表 9-17 操作 Wi-Fi 的相关方法

| 方法名 | 意义 | 参数 |

|---|---|---|

| wx.getWifiList | 获取 Wi-Fi 列表 | 常规配置参数 { success:调用成功的回调 fail:调用失败的回调 complete:调用完成的回调 } |

| wx.onGetWifiList | 监听获取到 Wi-Fi 列表后的事件，调用 wx.getWifiList 成功后，会回调这个方法设置的监听函数 | 监听函数，参数如下： { wifiList: Wi-Fi 数据列表 } |

| wx.getConnectedWifi | 获取已经连接的 Wi-Fi 信息 | 常规配置对象，在 success 回调函数的参数中会封装 wifi 属性，其中为 Wi-Fi 的具体信息 |

| wx.onWifiConnected | 设置 Wi-Fi 连接成功后的回调函数 | 回调函数的参数中会封装 wifi 属性，其中为 Wi-Fi 的具体信息 |

| wx.connectWifi | 根据已知的 Wi-Fi 信息直接连接 Wi-Fi | { SSID: Wi-Fi 设置的 SSID 号 BSSID: Wi-Fi 设置的 BSSID 号 password: Wi-Fi 密码 success:成功回调 fail:失败回调 complete:接口调用完成回调 } |

| wx.startWifi | 初始化 Wi-Fi 模块 | 常规配置对象 |

| wx.stopWifi | 关闭 Wi-Fi 模块 | 常规配置对象 |

需要注意，以上方法仅可在真机调试，模拟器不支持。Wi-Fi 数据对象包含的属性见表 9-18。

表 9-18 Wi-Fi 数据对象中包含的属性信息

| 属性名 | 意义 | 值 |

|---|---|---|

| SSID | Wi-Fi 的 SSID 码 | 字符串 |

| BSSID | Wi-Fi 的 BSSID 码 | 字符串 |

| secure | Wi-Fi 是否安全 | 布尔值 |

| signalStrength | Wi-Fi 的信号强度 | 数值 |

### 9.5.2 电话与联系人

小程序可直接调用拨号功能，示例如下：

[PRE4]

调用后页面会弹出确认框，模拟器中会模拟拨打效果，如图 9-5 所示。wx.makePhoneCall 的配置对象中 phoneNumber 用于指定号码。

通讯录方面，小程序支持把联系人写入手机通讯录，对名片类应用很有用。调用 wx.addPhoneContact 添加联系人，示例代码如下：

[PRE5]

执行后会跳转到联系人编辑页，如图 9-6 所示。

[PRE6]

[PRE7]

wx.addPhoneContact 的对象参数可配置字段较多，具体见表 9-19。

## 表 9-19 wx.addPhoneContact 方法中可配置的属性

| 属性名 | 意义 | 值 |

|---|---|---|

| firstName | 名字 | 字符串 |

| photoFilePath | 联系人头像的本地文件路径 | 字符串 |

| nickName | 联系人昵称 | 字符串 |

| lastName | 姓氏 | 字符串 |

| middleName | 中间名 | 字符串 |

| remark | 备注信息 | 字符串 |

| mobilePhoneNumber | 手机号码 | 字符串 |

| weChatNumber | 微信号 | 字符串 |

| addressCountry | 国家 | 字符串 |

| addressState | 省份 | 字符串 |

| addressCity | 城市 | 字符串 |

| addressStreet | 街道 | 字符串 |

| addressPostalCode | 邮编 | 字符串 |

| organization | 公司 | 字符串 |

| title | 职位 | 字符串 |

| workFaxNumber | 工作传真 | 字符串 |

| workPhoneNumber | 工作电话 | 字符串 |

| hostNumber | 公司电话 | 字符串 |

| email | 电子邮件 | 字符串 |

| url | 网站 | 字符串 |

| workAddressCountry | 公司地址国家 | 字符串 |

| workAddressState | 公司地址省份 | 字符串 |

| workAddressCity | 公司地址城市 | 字符串 |

| workAddressStreet | 公司地址街道 | 字符串 |

| workAddressPostalCode | 公司地址邮编 | 字符串 |

| homeFaxNumber | 住宅传真 | 字符串 |

| homePhoneNumber | 住宅电话 | 字符串 |

| homeAddressCountry | 住宅地址国家 | 字符串 |

| homeAddressState | 住宅地址省份 | 字符串 |

| homeAddressCity | 住宅地址城市 | 字符串 |

| homeAddressStreet | 住宅地址街道 | 字符串 |

| homeAddressPostalCode | 住宅地址邮编 | 字符串 |

| success | 接口成功回调 | 函数 |

| fail | 接口失败回调 | 函数 |

| complete | 接口完成回调 | 函数 |

### 9.5.3 屏幕与电量

开发者常关注屏幕方向与亮度，亮度也会影响续航。小程序提供了相关屏幕接口，使用方式简单，汇总见表 9-20。

**表 9-20 与设备屏幕相关的方法**

| 方法名 | 意义 | 参数 |

|---|---|---|

| wx.setScreenBrightness | 设置屏幕的亮度 | { value:设置亮度为 0~1 success:设置成功回调 fail:设置失败回调 complete:设置完成回调 } |

| wx.getScreenBrightness | 获取当前屏幕的亮度 | 常规配置参数：在 success 成功回调的参数中会封装 value 属性表示亮度 |

| wx.setKeepScreenOn | 设置屏幕是否保持常亮，某些视频播放类应用通常会使用到这个方法,需要注意的是,只在当前小程序内有效 | { keepScreenOn:是否保持常亮,布尔值 success:设置成功回调 fail:设置失败回调 complete:设置完成回调 } |