```
if ret==False:
    break
#创建图像处理对象
img_object=PersonDetection(c_dir+'/.../.../models/person_detection')
#调用图像处理函数对图像进行加工处理
result=img_object.inference(img,None)
frame = img_object.base64_to_image(result["result_image"])
#图像显示
cv.imshow('frame',frame)
key=cv.waitKey(1)
if key==ord('q'):
    break
cap.release()
cv.destroyAllWindows()
else :
from .persondet import PersonDet
```

# 5) 语音识别算法接口设计