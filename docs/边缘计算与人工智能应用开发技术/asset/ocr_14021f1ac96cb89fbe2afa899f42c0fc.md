```
```python
#读取测试图像
img = cv.imread("./test.jpg")
#将图像编码成数据流
img = img_object.imencode(img)

#设置参数
param_data = {"APP_ID":"123456", "API_KEY":"123456", "SECRET_KEY":"123456"}
img_object.font_path = "./../font/wqy-microhei.ttc"

#调用接口处理图像并返回结果
result = img_object.inference(img, param_data)
if result["code"] == 200:
    frame = img_object.base64_to_image(result["result_image"])
    print(result["result_data"])

#图像显示
cv.imshow('frame',frame)
while True:
    key=cv.waitKey(1)
    if key==ord('q'):
        break
cv.destroyAllWindows()
else:
    print("识别失败！")
```
```

# 5）百度语音识别算法接口设计