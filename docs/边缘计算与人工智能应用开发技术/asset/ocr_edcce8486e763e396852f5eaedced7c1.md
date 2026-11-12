```
$ cd ~/aiedge-exp/traffic_detection_interface
$ cp build/*.so ./
$ conda activate py3_tfl14_torch15_cpu_cv345    //Ubuntu 20.04 操作系统下需要切换环境
$ python3 traffic_detection.py
{
    "code" : 200,
    "msg" : "SUCCESS",
    "result" : {
        "obj_list" : [
            {
                "location" : {
                    "height" : 93,
                    "left" : 425,
                    "top" : 158,
                    "width" : 107
                },
                "name" : "right",
                "score" : 0.90108931064605713
            },
            {
                "location" : {
                    "height" : 115,
                    "left" : 149,
                    "top" : 143,
                    "width" : 109
                },
                "name" : "left",
                "score" : 0.89343971014022827
            },
            {
                "location" : {
                    "height" : 116,
                    "left" : 273,
                    "top" : 145,
                    "width" : 124
                },
                "name" : "straight",
                "score" : 0.51184618473052979
            },
        ],
        "obj_num" : 3
    },
    "time" : 61.909912109375
}
```