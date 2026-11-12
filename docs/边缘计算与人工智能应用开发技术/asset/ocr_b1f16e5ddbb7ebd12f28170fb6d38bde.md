```
```python
draw.text((loc["left"], loc["top"]]+count), '行为:+"闭眼，视角未朝前
可能在疲劳驾驶",fill= 'red', font=font_hei)
count = count + 20
#判断双手是否离开方向盘
elif key == "both_hands_leaving_wheel":
    draw.text((loc["left"], loc["top"]]+count), '行为:+"双手离开方向盘违规",
    fill= 'red', font=font_hei)
    count = count + 20
#判断是否系安全带
elif key == "not_buckling_up":
    draw.text((loc["left"], loc["top"]]+count), '行为:+"未系安全带",
    fill= 'red', font=font_hei)
    count = count + 20
#判断是否在打电话
elif key == "cellphone":
    draw.text((loc["left"], loc["top"]]+count), '行为:+"使用手机,
    玩手机",fill= 'red', font=font_hei)
    count = count + 20
#判断是否在抽烟
elif key == "smoke":
    draw.text((loc["left"], loc["top"]]+count), '行为:+"吸烟",
    fill= 'red', font=font_hei)
    count = count + 20
#输出图像
result = cv.cvtColor(np.array(pilimg), cv.COLOR_RGB2BGR)
return_result["code"] = 200
return_result["msg"] = "多主体监测成功！ "
return_result["origin_image"] = self.image_to_base64(image)
return_result["result_image"] = self.image_to_base64(result)
return_result["result_data"] = response
else:
    return_result["code"] = 500
    return_result["msg"] = "百度接口调用失败！ "
    return_result["result_data"] = response
#实时视频接口：@app.route('/stream/<action>')
#image：摄像头实时传递过来的图像
#param_data：必须为 None
else:
    return_result["result_image"] = self.image_to_base64(image)
return return_result
#单元测试，如果处理类中引用了文件，则在单元测试中要修改文件路径
if __name__=='__main__':
    #创建图像处理对象
    img_object = BaiduDrivingBehaviorAnalysis()
```
```