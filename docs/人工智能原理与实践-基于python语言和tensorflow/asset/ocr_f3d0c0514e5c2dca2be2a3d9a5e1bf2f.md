在第 2 层结构选择 256 个滤波器，滤波器大小为 5×5，卷积步长为 1。池化也选择跟之前一样的参数，代码如下。

```
layers {
    name: "conv2"
    type: CONVOLUTION
    bottom: "norm1"
    top: "conv2"
    blobs_lr: 1
    blobs_lr: 2
    weight_decay: 1
    weight_decay: 0
    convolution_param {
        num_output: 256
        pad: 2
        kernel_size: 5
        weight_filler {
            type: "gaussian"
            std: 0.01
        }
        bias_filler {
            type: "constant"
            value: 1
        }
    }
}
layers {
    name: "relu2"
    type: RELU
    bottom: "conv2"
    top: "conv2"
}
layers {
    name: "pool2"
    type: POOLING
    bottom: "conv2"
    top: "pool2"
    pooling_param {
        pool: MAX
        kernel_size: 3
        stride: 2
    }
}
layers {
    name: "norm2"
    type: LRN
    bottom: "pool2"
    top: "norm2"
    lrn_param {
        local_size: 5
        alpha: 0.0001
        beta: 0.75
    }
}
```

(3) 第 3 层：滤波器个数选择 384，卷积核大小为 3×3，代码如下。