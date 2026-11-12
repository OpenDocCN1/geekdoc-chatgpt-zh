```
for k, m in model.named_modules():
    if isisntance(m, Conv):  #assign export-friendly activations
        if isisntance(m.act, nn.SiLU):
            m.act = SiLU()
    elif isisntance(m, Detect):
        m.inplace = inplace
        m.onnx_dynamic = dynamic
        if hasattr(m, 'forward_export'):
            m.forward = m.forward_export  #assign custom forward (optional)

for _ in range(2):
    y = model(im)  #dry runs
shape = tuple(y[0].shape)  #model output shape
LOGGER.info(f"\n{colorstr('PyTorch:')} starting from {file} with output shape {shape}
    ({file_size(file):.1f} MB)")

#Exports
f = [""] * 10  #exported filenames
warnings.filterwarnings(action='ignore', category=torch.jit.TracerWarning)  #suppress TracerWarning
if jit:
    f[0] = export_torchscript(model, im, file, optimize)
if engine:  #TensorRT required before ONNX
    f[1] = export_engine(model, im, file, train, half, simplify, workspace, verbose)
if onnx or xml:  #OpenVINO requires ONNX
    f[2] = export_onnx(model, im, file, opset, train, dynamic, simplify)
if xml:  #OpenVINO
    f[3] = export_openvino(model, im, file)
if coreml:
    _, f[4] = export_coreml(model, im, file)

#TensorFlow Exports
if any((saved_model, pb, tflite, edgetpu, tfjs)):
    if int8 or edgetpu:  #TFLite --int8 bug https://github.com/ultralytics/yolov5/issues/5707
        check_requirements(("flatbuffers==1.12.",))  #required before 'import tensorflow'
        assert not (tflite and tfjs), 'TFLite and TF.js models must be exported separately,
                    please pass only one type.'
    model, f[5] = export_saved_model(model, im, file, dynamic, tf_nms=nms or agnostic_nms or tfjs,
                    agnostic_nms=agnostic_nms or tfjs,
                    topk_per_class=topk_per_class, topk_all=topk_all,
                    conf_thres=conf_thres, iou_thres=iou_thres)  #keras model
if pb or tfjs:  #pb prerequisite to tfjs
    f[6] = export_pb(model, im, file)
if tflite or edgetpu:
    f[7] = export_tflite(model, im, file, int8=int8 or edgetpu, data=data, ncalib=100)
if edgetpu:
    f[8] = export_edgetpu(model, im, file)
if tfjs:
    f[9] = export_tfjs(model, im, file)
```