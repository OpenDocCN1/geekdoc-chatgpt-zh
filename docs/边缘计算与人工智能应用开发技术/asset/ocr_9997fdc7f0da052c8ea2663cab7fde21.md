```
running:
  model_type: onnx
  export: True
  inference: True
  eval_perf: False

parameters:
  onnx:
    model: './yolov5s-mask.onnx'          #设置输入的 ONNX 框架模型路径
  rknn:
    path: './yolov5s-mask.rknn'          #设置输出的 RKNN 框架模型路径

config:
  mean_values: [0, 0, 0] #123.675 116.28 103.53 58.395 #0 0 0 255
  std_values: [255, 255, 255]
  quant_img_RGB2BGR: True #2 1 0 '#0 1 2' 2 1 0 '
  target_platform: 'rk3588'              #设置 RKNN 设备为 RK3588
  quantized_dtype:'asymmetric_quantized-8'#asymmetric_quantized-u8,dynamic_fixed_point-8,
                                          dynamic_fixed_point-16
  optimization_level: 1

build:
  do_quantization: True
  dataset: './single_dataset.txt'

export_rknn:
  export_path: './yolov5s-mask.rknn'

init_runtime:
  target: null
  device_id: null
  perf_debug: False
  eval_mem: False
  async_mode: False

img: &img
  path: './mask.jpg'                     #设置测试图像的路径

inference:
  inputs: *img
  data_type: 'uint8'
  data_format: 'nhwc' #nchw', 'nhwc'
  inputs_pass_through: None

eval_perf:
  inputs: *img
  data_type: 'uint8'
  data_format: 'nhwc'
  is_print: True
```