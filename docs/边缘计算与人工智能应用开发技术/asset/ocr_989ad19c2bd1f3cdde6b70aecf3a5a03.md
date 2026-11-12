```
```python
def __init__(self, model_path="models/mask_detection"):
    #加载口罩检测模型
    self.mask_model = Yolov5_Rknn_Detection(model_path+'/yolov5s-mask.rknn',
                                     wh=(640, 640), masks=[[0, 1, 2], [3, 4, 5], [6, 7, 8]],
                                     anchors=[[10, 13], [16, 30], [33, 23], [30, 61], [62, 45], [59, 119],
                                     [116, 90], [156, 198], [373, 326]], names=('face','face_mask'))

#对图像进行 base64 编码
def image_to_base64(self, img):
    image = cv.imencode('.jpg', img, [cv.IMWRITE_JPEG_QUALITY, 100])[1]
    image_encode = base64.b64encode(image).decode()
    return image_encode

def base64_to_image(self, b64):
    img = base64.b64decode(b64.encode('utf-8'))
    img = np.asarray(bytearray(img), dtype="uint8")
    img = cv.imdecode(img, cv.IMREAD_COLOR)
    return img

def draw_pos(self, img, objs):
    img_rgb = cv.cvtColor(img, cv.COLOR_BGR2RGB)
    pilimg = Image.fromarray(img_rgb)
    #创建 ImageDraw 绘图类
    draw = ImageDraw.Draw(pilimg)
    #设置字体
    font_size = 20
    font_path = c_dir+'/.../.../font/wqy-microhei.ttc"
    font_hei = ImageFont.truetype(font_path, font_size, encoding="utf-8")
    #遍历对象
    for obj in objs:
        #判断检测对象是否为 mask
        if obj["name"] == 'face_mask':
            #获取对象图中坐标
            loc = obj["location"]
            #根据坐标对图像进行绘矩形框
            draw.rectangle(loc["left"], loc["top"], loc["left"]+loc["width"],
                        loc["top"]+loc["height"]), outline='green',width=2)
            #msg 表示在图中展示的文本内容
            msg = obj["name"]+": %.2f"%obj["score"]
            #将展示的文本内容显示在图像中
            draw.text((loc["left"], loc["top"]-font_size*1), msg, (0, 255, 0), font=font_hei)
    #对图像进行 RGB 至 BGR 的转换
    result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
    #返回结果图像
    return result

def inference(self, image, param_data):
    #code: 识别成功返回 200
    #msg: 相关提示信息
```
```