[PRE0]

### 3）单次推理

AiCam 平台的单次推理功能，核心是响应应用层发起的一次性推理计算需求。流程上，应用层通过 Ajax 接口提交待处理图像及配置参数，算法端依据参数执行推理，最终回传带有可视化标注的结果图（如目标框、识别标签）以及结构化数据（坐标、关键点、类别、耗时、置信度等），供前端直接渲染展示。

以平台内置的 `file-exp` 示例为例，它对接了百度人脸识别能力，实现了人脸注册与比对两大核心场景。前端交互的 Ajax 接口规范详见下表：

**人脸注册与识别 前端 Ajax 接口参数详解**

| 参数字段 | 示例值 | 备注说明 |
| :--- | :--- | :--- |
| **url** | `"/file/baidu_face_recognition?camera_id=0"` | 请求接口地址 |
| **method** | `'POST'` | HTTP 请求方式 |
| **processData** | `false` | 禁止自动序列化数据，保持原始格式 |
| **contentType** | `false` | 不设置 Content-Type 头，由浏览器自动生成（含 boundary） |
| **dataType** | `'json'` | 预期服务端返回 JSON 格式响应 |
| **data** | `formData` 对象，包含两部分：<br>1. **文件流**：`formData.append('file_name', blob, 'image.png');` 接收图像/音视频/文本的 Blob 数据。<br>2. **配置参数**：`formData.append('param_data', JSON.stringify({...}));` 必须为 JSON 字符串。<br>   - **注册模式** (`type: 0`)：需传 `APP_ID`, `API_KEY`, `SECRET_KEY`, `userId`。<br>   - **比对模式** (`type: 1`)：需传 `APP_ID`, `API_KEY`, `SECRET_KEY`。 | 采用 `multipart/form-data` 格式，同时上传二进制文件与 JSON 参数 |
| **success** | 回调函数 `function(res){}`，解析返回体：<br>`{ code: 200, msg: null, origin_image: ..., result_image: ..., result_data: ... }`<br>- `code: 200` 代表业务成功。<br>- `origin_image` / `result_image`：原图与绘制结果图。 | 标准化响应结构，便于前端统一处理 |

配套的前端核心工具函数：将 Base64 编码转换为 Blob 二进制对象，以便构建 FormData 上传。

[PRE1]javascript

// Base64 转 Blob 工具函数
function dataURItoBlob(base64Data) {
    let byteString;
    const header = base64Data.split(',')[0];
    // 兼容处理：判断是否为 base64 编码头
    if (header.indexOf('base64') >= 0) {
        byteString = atob(base64Data.split(',')[1]);
    } else {
        byteString = unescape(base64Data.split(',')[1]);
    }
    // 提取 MIME 类型
    const mimeString = header.split(':')[1].split(';')[0];
    // 写入 Uint8Array
    const ia = new Uint8Array(byteString.length);
    for (let i = 0; i < byteString.length; i++) {
        ia[i] = byteString.charCodeAt(i);
    }
    return new Blob([ia], { type: mimeString });
}

[PRE2]

$ mkdir -p ~/aiedge-exp

[PRE3]

$ cd ~/aiedge-exp
$ unzip aicam.zip

[PRE4]

$ cd ~/aicam
$ chmod 755 start_aicam.sh
$ conda activate py36_tf114_torch15_cpu_cv345    # Ubuntu 20.04 下需切换至指定虚拟环境
$ ./start_aicam.sh

// 服务启动日志示例
* Serving Flask app "start_aicam" (lazy loading)
* Environment: production
WARNING: Do not use the development server in a production environment.
Use a production WSGI server instead.
* Debug mode: off
* Running on http://0.0.0.0:4000/ (Press CTRL+C to quit)

[PRE5]

// 摄像头视频流地址配置 (以人脸检测通道为例)
let linkData = 'http://192.168.100.200:4000/stream/face_detection?camera_id=0'