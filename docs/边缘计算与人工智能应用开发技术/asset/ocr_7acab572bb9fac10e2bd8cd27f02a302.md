```
subprocess.run(cmd, shell=True, check=True)
LOGGER.info(f'{prefix} export success, saved as {f} ({file_size(f):.1f} MB)')
return f
except Exception as e:
LOGGER.info(f"\n{prefix} export failure: {e}")
```

```
def export_tfjs(keras_model, im, file, prefix=colorstr('TensorFlow.js:')):
#YOLOv5 TensorFlow.js export
try:
check_requirements(('tensorflowjs',))
import re
import tensorflowjs as tfjs
LOGGER.info(f"\n{prefix} starting export with tensorflowjs {tfjs.__version__}...")
f = str(file).replace('.pt', '_web_model') #js dir
f_pb = file.with_suffix('.pb') #*.pb path
f_json = f + '/model.json' #*.json path
cmd = f'tensorflowjs_converter --input_format=tf_frozen_model ' \\
f"--output_node_names=\"Identity,Identity_1,Identity_2,Identity_3\" {f_pb} {f'}"
subprocess.run(cmd, shell=True)
json = open(f_json).read()
with open(f_json, 'w') as j: #sort JSON Identity_* in ascending order
subst = re.sub(
r'{"outputs": {"Identity.?.?": {"name": "Identity.?.?"}, '
r'"Identity.?.?": {"name": "Identity.?.?"}, '
r'"Identity.?.?": {"name": "Identity.?.?"}, '
r'"Identity.?.?": {"name": "Identity.?.?"}}}',
r'{"outputs": {"Identity": {"name": "Identity"}, '
r'"Identity_1": {"name": "Identity_1"}, '
r'"Identity_2": {"name": "Identity_2"}, '
r'"Identity_3": {"name": "Identity_3"}}}',
json)
j.write(subst)
LOGGER.info(f'{prefix} export success, saved as {f} ({file_size(f):.1f} MB)')
return f
except Exception as e:
LOGGER.info(f"\n{prefix} export failure: {e}")
```

```
@torch.no_grad()
def run(data=ROOT / 'data/coco128.yaml', #dataset.yaml path'
weights=ROOT / 'yolov5s.pt', #weights path'
imgsz=(640, 640), #image (height, width)
batch_size=1, #batch size
```