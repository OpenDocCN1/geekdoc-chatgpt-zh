NCNN框架是基于C/C++实现的，而AiCam平台的算法层采用Python语言，需要采用pybind11将NCNN框架模型的接口封装成so库，供Python算法调用。

AiCam平台对NCNN框架模型接口的调用与返回的数据做了标准化处理，返回的数据采用JSON格式，方便AiCam平台的算法层进行解析。NCNN框架模型接口函数如下（以交通标志识别模型为例）：

```
PYBIND11_MODULE(trafficdet, m) {
  py::class_<TrafficDet>(m, "TrafficDet")
      .def(py::init<>())
      .def("init", &TrafficDet::init)
      .def("detect", &TrafficDet::detect_yolov3);
}
```

模型接口的返回如下：