```
del kpt, csd
```

```
#DP mode
if cuda and RANK == -1 and torch.cuda.device_count() > 1:
    LOGGER.warning('WARNING: DP not recommended, use torch.
    distributed.run for best DDP Multi-GPU results.\n'
    'See Multi-GPU Tutorial at https://github.com/ultralytics/yolov5/issues/475
    to get started.')
model = torch.nn.DataParallel(model)
```

```
#SyncBatchNorm
if opt.sync_bn and cuda and RANK != -1:
    model = torch.nn.SyncBatchNorm.convert_sync_batchnorm(model).to(device)
    LOGGER.info('Using SyncBatchNorm()')
```

```
#Trainloader
train_loader, dataset = create_dataloader(train_path, imgsz, batch_size //WORLD_SIZE,
    gs, single_cls, hyp=hyp, augment=True, cache=None if opt.cache ==
    'val' else opt.cache, rect=opt.rect, rank=LOCAL_RANK, workers=workers,
    image_weights=opt.image_weights, quad=opt.quad,
    prefix=colorstr('train: '), shuffle=True)
mlc = int(np.concatenate(dataset.labels, 0)[:, 0].max()) #max label class
nb = len(train_loader) #number of batches
assert mlc < nc, f'Label class {mlc} exceeds nc={nc} in {data}. Possible class labels are 0-{nc - 1}'
```

```
#Process 0
if RANK in [-1, 0]:
    val_loader = create_dataloader(val_path, imgsz, batch_size //WORLD_SIZE * 2, gs, single_cls,
        hyp=hyp, cache=None if noval else opt.cache,
        rect=True, rank=-1, workers=workers * 2, pad=0.5,
        prefix=colorstr('val: '))[0]
```

```
if not resume:
    labels = np.concatenate(dataset.labels, 0)
    #c = torch.tensor(labels[:, 0]) #classes
    #cf = torch.bincount(c.long(), minlength=nc) + 1. #frequency
    #model.initialize_biases(cf.to(device))
    if plots:
        plot_labels(labels, names, save_dir)
```

```
#Anchors
if not opt.noautoanchor:
    check_anchors(dataset, model=model, thr=hyp['anchor_t'], imgsz=imgsz)
    model.half().float() #pre-reduce anchor precision
```

```
callbacks.run('on_pretrain_routine_end')

#DDP mode
```