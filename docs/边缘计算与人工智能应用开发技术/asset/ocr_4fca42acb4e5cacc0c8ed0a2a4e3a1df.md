```
//函数：ModelDetector.detect(image)
//参数：image 表示图像数据
//结果：JSON 格式的字符串
//code：200 表示执行成功， 301 表示执行失败
{
    "code" : 200,          //返回码
    "msg" : "SUCCESS",     //返回消息
    "result" : {           //返回结果
        "obj_list" : [     //返回内容
            {
                "location" : {   //目标坐标
                    "height" : 58,
                    "left" : 215,
                    "top" : 137,
                    "width" : 45
                },
                "mark" : [       //目标关键点
                    {
                        "x" : 227,
                        "y" : 160
                    },
                    {
                        "x" : 247,
                        "y" : 157
                    },
                    {
                        "x" : 239,
                        "y" : 169
                    },
                    {
                        "x" : 231,
                        "y" : 180
                    },
                    {
                        "x" : 249,
                        "y" : 177
```