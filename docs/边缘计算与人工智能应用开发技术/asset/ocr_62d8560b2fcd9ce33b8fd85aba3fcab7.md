```
self._anchors = anchors
self.names = names
if isinstance(model, str):
    model = load_rknn_model(model)
self._rkenn = model
self.draw_box = True

def _predict(self, img_src, _img, gain, conf_thres=0.4, iou_thres=0.45):
    src_h, src_w = img_src.shape[:2]
    _img = cv2.cvtColor(_img, cv2.COLOR_BGR2RGB)
    t0 = time.time()
    #调用 NPU 进行推理
    pred_onnx = self._rkenn.inference(inputs=[_img])
    print("inference time:\t", time.time() - t0)
    #处理推理结果
    boxes, classes, scores = [], [], []
    for t in range(3):
        input0_data = sigmoid(pred_onnx[t][0])
        input0_data = np.transpose(input0_data, (1, 2, 0, 3))
        grid_h, grid_w, channel_n, predict_n = input0_data.shape
        anchors = [self._anchors[i] for i in self._masks[t]]
        box_confidence = input0_data[..., 4]
        box_confidence = np.expand_dims(box_confidence, axis=-1)
        box_class_probs = input0_data[..., 5:]
        box_xy = input0_data[..., :2]
        box_wh = input0_data[..., 2:4]
        col = np.tile(np.arange(0, grid_w), grid_h).reshape(-1, grid_w)
        row = np.tile(np.arange(0, grid_h).reshape(-1, 1), grid_w)
        col = col.reshape((grid_h, grid_w, 1, 1)).repeat(3, axis=-2)
        row = row.reshape((grid_h, grid_w, 1, 1)).repeat(3, axis=-2)
        grid = np.concatenate((col, row), axis=-1)
        box_xy = box_xy * 2 - 0.5 + grid
        box_wh = (box_wh * 2) ** 2 * anchors
        box_xy /= (grid_w, grid_h) #计算原尺寸的中心
        box_wh /= self.wh #计算原尺寸的宽高
        box_xy -= (box_wh / 2)
        box = np.concatenate((box_xy, box_wh), axis=-1)
        res = filter_boxes(box, box_confidence, box_class_probs, conf_thres)
        boxes.append(res[0])
        classes.append(res[1])
        scores.append(res[2])
    boxes, classes, scores = np.concatenate(boxes), np.concatenate(classes), np.concatenate(scores)
    nboxes, nclasses, nscores = [], [], []
    for c in set(classes):
        inds = np.where(classes == c)
        b = boxes[inds]
        c = classes[inds]
        s = scores[inds]
```