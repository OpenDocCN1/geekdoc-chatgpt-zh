```
matcher,object_detection.protos.faster_rcnn_box_coder_pb2,object_detection.builders.region_similarity_calculator_builder,object_detection.builders.image_resizer_builder,object_detection.core.matcher,object_detection.meta_architectures.faster_rcnn_meta_arch,object_detection.core.minibatch_sampler,object_detection.core.balanced_positive_negative_sampler,object_detection.protos.bipartite_matcher_pb2,object_detection.core.keypoint_ops,object_detection.protos.region_similarity_calculator_pb2,object_detection.builders.matcher_builder,object_detection.builders.losses_builder,object_detection.meta_architectures.rfcn_meta_arch,object_detection.core.box_predictor,object_detection.builders.box_coder_builder,object_detection.box_coders.square_box_coder,object_detection.protos.mean_stdev_box_coder_pb2,object_detection.utils.variables_helper,object_detection,object_detection.core.region_similarity_calculator,object_detection.builders.post_processing_builder ......
```

```
Ran 7 tests in 0.047s
```

```
OK
To exit: use 'exit','quit',or Ctrl-D.
An exception has occurred,use %tb to see the full traceback.
SystemExit: <sitecustomize.IPyTesProgram object at 0x0000002B770CBA048>
```

表明 Object Detection API 一切正常，可以使用了。

# 4. 将Object Detection API加入Python库默认搜索路径

为了不用每次都将文件复制到 Object Detection 文件夹外，可以将 Object Detection 加到 Python 引入库的默认搜索路径中，将 Object Detection 文件夹整个复制到 anaconda3 安装文件目录下的 lib\site-packages 下，如图 11-23 所示。

![](bbox=[0.23, 0.63, 0.75, 0.735])

图 11-23 Object Detection 安装

这样无论文件在哪里，只要搜索 import Object Detection xxx ，系统都会找到 Object Detection 了。

# 11.7.2 实例 87：调用 Object Detection API 进行实物检测

下面用一个例子来测试下 Object Detection API 中的检测效果。该例子改编于 Object Detection API 的自带程序，使用的图片也是 Object Detection API 中的图片。具体步骤如下。