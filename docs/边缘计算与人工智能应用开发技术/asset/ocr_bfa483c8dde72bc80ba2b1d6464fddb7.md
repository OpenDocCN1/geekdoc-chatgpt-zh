```
["TorchScript", 'torchscript', '.torchscript'],
["ONNX", 'onnx', '.onnx'],
["OpenVINO", 'openvino', '_openvino_model'],
["TensorRT", 'engine', '.engine'],
["CoreML", 'coreml', '.mlmodel'],
["TensorFlow SavedModel", 'saved_model', '_saved_model'],
["TensorFlow GraphDef", 'pb', '.pb'],
["TensorFlow Lite", 'tflite', '.tflite'],
["TensorFlow Edge TPU", 'edgetpu', '_edgetpu.tflite'],
["TensorFlow.js", 'tfjs', '_web_model']]
return pd.DataFrame(x, columns=['Format', 'Argument', 'Suffix'])
```

```
def export_torchscript(model, im, file, optimize, prefix=colorstr('TorchScript:')):
    #YOLOv5 TorchScript model export
    try:
        LOGGER.info(f'\n{prefix} starting export with torch {torch.__version__}...')
        f = file.with_suffix('.torchscript')

        ts = torch.jit.trace(model, im, strict=False)
        d = {"shape": im.shape, "stride": int(max(model.stride)), "names": model.names}
        extra_files = {'config.txt': json.dumps(d)} # torch.C.ExtraFilesMap()
        if optimize: # https://pytorch.org/tutorials/recipes/mobile_interpreter.html
            optimize_for_mobile(ts)._save_for_lite_interpreter(str(f), _extra_files=extra_files)
        else:
            ts.save(str(f), _extra_files=extra_files)

        LOGGER.info(f'{prefix} export success, saved as {f} ({file_size(f):.1f} MB)')
        return f
    except Exception as e:
        LOGGER.info(f'{prefix} export failure: {e}')
```

```
def export_onnx(model, im, file, opset, train, dynamic, simplify, prefix=colorstr('ONNX:')):
    #YOLOv5 ONNX export
    try:
        check_requirements(('onnx',))
        import onnx

        LOGGER.info(f'\n{prefix} starting export with onnx {onnx.__version__}...')
        f = file.with_suffix('.onnx')

        torch.onnx.export(model, im, f, verbose=False, opset_version=opset,
                        training=torch.onnx.TrainingMode.TRAINING if train
                        else torch.onnx.TrainingMode.EVAL,
                        do_constant_folding=not train,
                        input_names=['images'], output_names=['output'],
                        dynamic_axes={'images': {0: 'batch', 2: 'height', 3: 'width'}, #shape(1,3,640,640)
                                        'output': {0: 'batch', 1: 'anchors'} #shape(1,25200,85)
                                        } if dynamic else None)
```