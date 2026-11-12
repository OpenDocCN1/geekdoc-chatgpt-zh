```
$ cd ~/aiedge-exp/object_detection_darknet
$ conda activate py36_tf114_torch15_cpu_cv345 //Ubuntu 20.04 操作系统下需要切换环境
$ ./darknet-gpu detector train cfg/yolov3-tiny-traffic.data cfg/yolov3-tiny-traffic.cfg yolov3-tiny.conv.15 15
```

CUDA-version: 11030 (11040), cuDNN: 8.2.1, GPU count: 1
OpenCV is not used - data augmentation will be slow
yolov3-tiny-traffic

0 : compute_capability = 860, cudnn_half = 0, GPU: NVIDIA GeForce RTX 3080
net.optimized_memory = 0
mini_batch = 16, batch = 16, time_steps = 1, train = 1

| layer | filters | size/strid(dil) | input | output |
| :--- | :--- | :--- | :--- | :--- |
| 0 Create CUDA-stream - 0 | | | | |
| Create cudnn-handle 0 | | | | |
| conv | 16 | 3 x 3/ 1 | 416 x 416 x 3 -> | 416 x 416 x 16 0.150 BF |
| 1 max | | 2x 2/ 2 | 416 x 416 x 16 -> | 208 x 208 x 16 0.003 BF |
| 2 conv | 32 | 3 x 3/ 1 | 208 x 208 x 16 -> | 208 x 208 x 32 0.399 BF |
| 3 max | | 2x 2/ 2 | 208 x 208 x 32 -> | 104 x 104 x 32 0.001 BF |
| 4 conv | 64 | 3 x 3/ 1 | 104 x 104 x 32 -> | 104 x 104 x 64 0.399 BF |
| 5 max | | 2x 2/ 2 | 104 x 104 x 64 -> | 52 x 52 x 64 0.001 BF |
| 6 conv | 128 | 3 x 3/ 1 | 52 x 52 x 64 -> | 52 x 52 x 128 0.399 BF |
| 7 max | | 2x 2/ 2 | 52 x 52 x 128 -> | 26 x 26 x 128 0.000 BF |
| 8 conv | 256 | 3 x 3/ 1 | 26 x 26 x 128 -> | 26 x 26 x 256 0.399 BF |
| 9 max | | 2x 2/ 2 | 26 x 26 x 256 -> | 13 x 13 x 256 0.000 BF |
| 10 conv | 512 | 3 x 3/ 1 | 13 x 13 x 256 -> | 13 x 13 x 512 0.399 BF |
| 11 max | | 2x 2/ 1 | 13 x 13 x 512 -> | 13 x 13 x 512 0.000 BF |
| 12 conv | 1024 | 3 x 3/ 1 | 13 x 13 x 512 -> | 13 x 13 x1024 1.595 BF |
| 13 conv | 256 | 1 x 1/ 1 | 13 x 13 x1024 -> | 13 x 13 x 256 0.089 BF |
| 14 conv | 512 | 3 x 3/ 1 | 13 x 13 x 256 -> | 13 x 13 x 512 0.399 BF |
| 15 conv | 30 | 1 x 1/ 1 | 13 x 13 x 512 -> | 13 x 13 x 30 0.005 BF |
| 16 yolo | | | | |
| [yolo] params: iou loss: mse (2), iou_norm: 0.75, obj_norm: 1.00, cls_norm: 1.00, delta_norm: 1.00, scale_x_y: 1.00 | | | | |
| 17 route | 13 | | -> | 13 x 13 x 256 |
| 18 conv | 128 | 1 x 1/ 1 | 13 x 13 x 256 -> | 13 x 13 x 128 0.011 BF |
| 19 upsample | | 2x | 13 x 13 x 128 -> | 26 x 26 x 128 |
| 20 route | 19 8 | | -> | 26 x 26 x 384 |
| 21 conv | 256 | 3 x 3/ 1 | 26 x 26 x 384 -> | 26 x 26 x 256 1.196 BF |
| 22 conv | 30 | 1 x 1/ 1 | 26 x 26 x 256 -> | 26 x 26 x 30 0.010 BF |