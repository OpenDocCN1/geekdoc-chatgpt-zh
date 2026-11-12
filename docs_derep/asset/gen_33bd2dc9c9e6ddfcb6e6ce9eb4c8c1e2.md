# 实战指南：基于百度OCR的智能仪表盘数字识别全流程解析

在工业自动化与物联网监控场景中，自动读取LED/LCD仪表盘读数是一个高频刚需。本文将拆解一套完整的「颜色定位+云端OCR+可视化回显」技术方案，涵盖Python后端核心逻辑与前端流式交互实现。

---

## 核心架构思路

整体流程可概括为三步走：
1.  **前置处理**：利用HSV色空间精准锁定显示屏蓝色字符区域，通过轮廓筛选剔除干扰，完成自动裁剪对齐。
2.  **云端推理**：将裁剪后的图像编码为Base64，调用百度数字识别API（高精度含位置版），获取带坐标的结构化结果。
3.  **结果渲染**：在原图上叠加红色边框与识别文本，编码返回前端；前端配合SSE（Server-Sent Events）实现准实时视频流更新。

---

## 后端核心模块深度剖析

### 1. 显示屏区域精准提取 (`cropGreenImg`)

代码并未简单使用RGB阈值，而是转换至**HSV空间**，定义蓝色阈值（`lower_blue: [80,89,218]` ~ `upper_blue: [96,255,255]`），配合**中值滤波**去噪，最后通过`findContours`提取轮廓。

*   **单轮廓**：直接包围盒裁剪。
*   **多轮廓**：采用**面积最大原则**锁定主显示屏，有效规避指示灯、反光等干扰块。
*   **关键点**：记录裁剪区域在原图中的偏移量（`self.x`, `self.y`），为后续坐标映射回原图埋下伏笔。

### 2. 百度OCR接口封装与参数调优 (`inference`)

*   **认证体系**：通过`AipOcr`注入`APP_ID`、`API_KEY`、`SECRET_KEY`，实现动态配置，便于多环境部署。
*   **关键参数**：`recognize_granularity='small'` —— 此项**必须开启**，才能拿到单字符级别的`location`，这是实现“逐字标注”的基础。
*   **异常兜底**：
    *   无显示屏 -> `Code 500`
    *   API报错/鉴权失败 -> `Code 500` 并回传原始报文
    *   无数字检出 -> `Code 404` 返回原图供排查

### 3. 可视化回写细节 (PIL + OpenCV 混合编程)

由于OpenCV中文绘制渲染差，代码巧妙切换至**PIL/Pillow**：
1.  `cv2.cvtColor` -> RGB -> `Image.fromarray`
2.  加载 `wqy-microhei.ttc` 矢量字体，保证中文/数字锐利无锯齿。
3.  **双层标注策略**：
    *   **词级框**：红色矩形框住整串数字 (`words_result` -> `location`)。
    *   **字级框+文本**：遍历 `chars` 列表，为每个数字单独画框、写字，坐标换算公式：`原图坐标 = 裁剪图坐标 + (self.x, self.y)`。
4.  最终 `RGB->BGR` 转回 OpenCV 格式，统一走 `imencode` -> Base64 流程。

---

## 前端流式交互实现 (SSE 方案)

相比传统轮询或WebSocket，代码采用**EventSource (SSE)** 单向流，极其轻量：

```javascript
// [PRE0]text
let {result_image} = JSON.parse(res.data)
$("#img_box>img").attr('src', 'data:image/jpeg;base64,${result_image}')

}, error: function(error){
  console.log(error);
  swal("调用接口失败",'','"error",{button: false,timer: 2000});
  
// 请求图像流资源
imgData.close()
imgData = new EventSource(linkData)

// 对图像流返回的数据处理
imgData.onmessage = function (res) {
  let {result_image} = JSON.parse(res.data)
  $("#img_box>img").attr('src', 'data:image/jpeg;base64,${result_image}')
}
```
*   **容错机制**：主请求失败时自动降级/重连建立 `EventSource` 连接。
*   **渲染直观**：直接解析返回的 `result_image` (Base64 JPEG) 赋值给 `<img>` 标签 `src`，浏览器原生解码，零依赖。

---

## 代码复用与部署提示

### 单元测试入口 (`if __name__ == '__main__':`)
提供了标准化调用范式：
1.  实例化 `BaiduMeterRecognition()`
2.  `cv.imread` 读图 -> `imencode` 压缩
3.  构造 `param_data` 字典注入密钥
4.  调用 `inference(img, param_data)` 获取结构化字典
5.  判断 `code == 200` 即成功，可直接落库或推流。

### 工程化建议
| 维度 | 建议 |
| :--- | :--- |
| **密钥管理** | 严禁硬编码，建议使用环境变量或配置中心 (Nacos/Apollo) 注入 `param_data` |
| **字体路径** | 打包镜像时确保 `wqy-microhei.ttc` 相对路径正确，或改用绝对路径 `/usr/share/fonts/...` |
| **并发性能** | `AipOcr` 客户端可单例复用；CPU密集型的图像预处理建议放入进程池 (Celery/Gunicorn workers) |
| **监控指标** | 重点埋点：裁剪失败率、百度API耗时/错误率、端到端延迟 |

---

## 总结

该方案以**轻量级传统视觉 (HSV+轮廓)** 解决「在哪看」，以**云端大模型 OCR** 解决「看什么」，再以**坐标逆映射+PIL绘制**解决「怎么展示」。前后端分离清晰，SSE流式传输体验丝滑，是中小型工业数字化改造项目的「开箱即用」最佳实践范本。

[PRE1]python
# 说明: 调用百度数字识别算法
########################################
from PIL import Image, ImageDraw, ImageFont
import numpy as np
import cv2 as cv
import os, sys, time
import json
import base64
from aip import AipOcr

class BaiduMeterRecognition(object):
    def __init__(self, font_path="font/wqy-microhei.ttc"):
        self.font_path = font_path
        # HSV空间下蓝色显示屏的阈值范围
        self.lower_blue = np.array([80, 89, 218])
        self.upper_blue = np.array([96, 255, 255])
        # 裁剪区域在原图中的偏移量，用于坐标映射
        self.x = 0
        self.y = 0

    def imencode(self, image_np):
        # 将图像编码为JPG字节流
        return cv.imencode('.jpg', image_np)[1]

    def image_to_base64(self, img):
        # 压缩质量60平衡体积与清晰度，转Base64字符串
        image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 60])[1]
        return base64.b64encode(image).decode()

    def base64_to_image(self, b64):
        # Base64字符串解码回OpenCV Mat
        img = base64.b64decode(b64.encode('utf-8'))
        img = np.asarray(bytearray(img), dtype="uint8")
        return cv.imdecode(img, cv.IMREAD_COLOR)

    @staticmethod
    def contours_area(cnt):
        # 计算轮廓外接矩形面积
        x, y, w, h = cv.boundingRect(cnt)
        return w * h

    def cropGreenImg(self, image):
        # 1. 色彩空间转换 & 阈值分割
        hsv_img = cv.cvtColor(image, cv.COLOR_BGR2HSV)
        mask = cv.inRange(hsv_img, self.lower_blue, self.upper_blue)
        # 2. 中值滤波去椒盐噪声
        mask = cv.medianBlur(mask, 7)
        # 3. 轮廓提取
        mask, contours, hierarchy = cv.findContours(mask, cv.RETR_EXTERNAL, cv.CHAIN_APPROX_NONE)
        
        if not contours:
            print("未检测到蓝色显示屏区域")
            return []
        
        # 4. 策略：单轮廓直取，多轮廓取最大面积
        target_cnt = max(contours, key=self.contours_area) if len(contours) > 1 else contours[0]
        x, y, w, h = cv.boundingRect(target_cnt)
        self.x, self.y = x, y
        return image[y:y+h, x:x+w]

    def inference(self, image, param_data):
        # 统一返回结构体
        return_result = {
            'code': 200, 'msg': None, 
            'origin_image': None, 'result_image': None, 'result_data': None
        }

        if param_data is None:
            return_result.update(code=500, msg="参数为空，无法调用百度接口")
            return return_result

        # 解码输入图像
        image = cv.imdecode(np.asarray(bytearray(image), dtype="uint8"), cv.IMREAD_COLOR)
        
        # 步骤一：定位裁剪显示屏
        cropImg = self.cropGreenImg(image)
        if len(cropImg) == 0:
            return_result.update(code=500, msg="未检测到显示屏区域")
            return return_result

        # 步骤二：编码并调用百度OCR
        img_bytes = self.imencode(cropImg)
        client = AipOcr(param_data["APP_ID"], param_data["API_KEY"], param_data["SECRET_KEY"])
        options = {'recognize_granularity': 'small'}  # 关键：开启单字符定位
        response = client.numbers(img_bytes, options)

        # 步骤三：异常处理
        if "error_msg" in response and response["error_msg"] != 'SUCCESS':
            return_result.update(code=500, msg="百度OCR接口报错", result_data=response)
            return return_result
        if response.get('words_result_num', 0) == 0:
            return_result.update(code=404, msg="未识别出数字", 
                                 origin_image=self.image_to_base64(image), result_data=response)
            return return_result

        # 步骤四：可视化绘制 (PIL)
        img_rgb = cv.cvtColor(image, cv.COLOR_BGR2RGB)
        pil_img = Image.fromarray(img_rgb)
        draw = ImageDraw.Draw(pil_img)
        font = ImageFont.truetype(self.font_path, 20, encoding="utf-8")

        for item in response['words_result']:
            loc = item['location']
            # 绘制词级包围框 (映射回原图坐标)
            draw.rectangle([
                loc["left"] + self.x, loc["top"] + self.y,
                loc["left"] + self.x + loc["width"], loc["top"] + self.y + loc["height"]
            ], outline='red', width=1)
            
            # 绘制字符级框与文本
            for char_info in item['chars']:
                cloc = char_info['location']
                cchar = char_info['char']
                draw.rectangle([
                    cloc["left"] + self.x, cloc["top"] + self.y,
                    cloc["left"] + self.x + cloc["width"], cloc["top"] + self.y + cloc["height"]
                ], outline='red', width=1)
                draw.text(
                    (cloc["left"] + self.x, cloc["top"] + self.y - 22),
                    cchar, fill='red', font=font
                )

        # 步骤五：结果打包
        result_cv = cv.cvtColor(np.array(pil_img), cv.COLOR_RGB2BGR)
        return_result.update(
            code=200, msg="识别成功",
            origin_image=self.image_to_base64(image),
            result_image=self.image_to_base64(result_cv),
            result_data=response
        )
        return return_result

# 单元测试
if __name__ == '__main__':
    detector = BaiduMeterRecognition()
    # 注意：测试时请替换为真实图片路径及有效AK/SK
    raw_img = cv.imread("/test.jpg")
    img_stream = detector.imencode(raw_img)
    params = {"APP_ID": "YOUR_ID", "API_KEY": "YOUR_AK", "SECRET_KEY": "YOUR_SK"}
    detector.font_path = "./../font/wqy-microhei.ttc"
    res = detector.inference(img_stream, params)
    print(f"识别状态: {res['code']} - {res['msg']}")