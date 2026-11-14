# 6）模型 测试

训练结束后，一般需要进行模型测试，验证实际检测效果。OpenCV 提供了一个 cascade 分类器测试工具及源代码，在 vs2015 中打开，就可以运行，观察训练过的模型的实际运用效果。

调用 face_classifier 对象的 detectMultiScale 方法时，如果参数使用不当，会出现误识别人脸的情况，如图 9.4 所示。

![](bbox=[0.2, 0.33, 0.78, 0.58])

对 face_classifier. detectMultiScale (gray, faces, 1, 2, 3 , 1 ,Size ( 50 ,50 ) ) 中 的几个数据参数进行调节，可以准确地检测到人脸。

![](bbox=[0.2, 0.645, 0.78, 0.915])