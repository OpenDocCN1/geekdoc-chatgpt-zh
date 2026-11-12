```
$ cd ~/aiedge-exp/object_detection_darknet
$ conda activate py36_tfl14_torch15_cpu_cv345      //Ubuntu 20.04 操作系统下需要切换环境
$ /darknet-cpu detector train cfg/yolov3-tiny-traffic.data cfg/yolov3-tiny-traffic.cfg yolov3-tiny.conv.15 15
GPU isn't used
OpenCV isn't used - data augmentation will be slow
yolov3-tiny-traffic
mini_batch = 16, batch = 16, time_steps = 1, train = 1
```

| layer | filters | size/strd(dil) | input | output |
|---|---|---|---|---|
| 0 conv | 16 | 3 x 3 / 1 | 416 x 416 x 3 3 -> | 416 x 416 x 16 0.150 BF |
| 1 max | | 2x 2 / 2 | 416 x 416 x 16 16 -> | 208 x 208 x 16 0.003 BF |
| 2 conv | 32 | 3 x 3 / 1 | 208 x 208 x 16 16 -> | 208 x 208 x 32 0.399 BF |
| 3 max | | 2x 2 / 2 | 208 x 208 x 32 32 -> | 104 x 104 x 32 0.001 BF |
| 4 conv | 64 | 3 x 3 / 1 | 104 x 104 x 32 32 -> | 104 x 104 x 64 0.399 BF |
| 5 max | | 2x 2 / 2 | 104 x 104 x 64 64 -> | 52 x 52 x 64 0.001 BF |
| 6 conv | 128 | 3 x 3 / 1 | 52 x 52 x 64 64 -> | 52 x 52 x 128 0.399 BF |
| 7 max | | 2x 2 / 2 | 52 x 52 x 128 128 -> | 26 x 26 x 128 0.000 BF |
| 8 conv | 256 | 3 x 3 / 1 | 26 x 26 x 128 128 -> | 26 x 26 x 256 0.399 BF |
| 9 max | | 2x 2 / 2 | 26 x 26 x 256 256 -> | 13 x 13 x 256 0.000 BF |
| 10 conv | 512 | 3 x 3 / 1 | 13 x 13 x 256 -> | 13 x 13 x 512 0.399 BF |