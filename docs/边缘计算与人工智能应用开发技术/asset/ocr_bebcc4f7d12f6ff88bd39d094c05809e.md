```
```python
#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__ == '__main__':
    mode = sys.argv[1]
    c_dir = os.path.split(os.path.realpath(__file__))[0]
    #创建图像处理对象
    img_object = ImageFaceRecognition(c_dir)

    #读取测试图像
    image = cv.imread(c_dir+"/test.jpg")
    #将图像编码成数据流
    img = cv.imencode('.jpg', image, [cv.IMWRITE_JPEG_QUALITY, 60])[1]

    #设置参数
    addUser_data = {"type":"0", "reg_name":"lilianjie"}

    #调用接口进行人脸注册
    if mode == '0':
        result = img_object.inference(img, addUser_data)
        print(result)
```
```

```
```python
#调用接口进行人脸识别
elif mode == '1':
    result = img_object.inference(image, None)
    frame = img_object.base64_to_image(result["result_image"])
    print(result)

    #图像显示
    cv.imshow('frame',frame)
    while True:
        key=cv.waitKey(1)
        if key==ord('q'):
            break
    cv.destroyAllWindows()
else:
    print("参数错误！ ")
```
```

# 2. 2. 2 开发步骤与验证

# 2. 2. 2. 1 项目部署

# 1) 硬件部署