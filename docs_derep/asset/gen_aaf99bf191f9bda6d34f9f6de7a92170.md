[PRE0]
## 深度解析：模型训练核心脚本 train.py

作为整个训练流程的“总指挥”，`train.py` 负责串联起从环境初始化、配置加载到模型构建的完整链路。下面我们通过代码实现，详细拆解其核心逻辑与关键步骤：

[PRE1]

workers, freeze = Path(opt.save_dir), opt.epochs, opt.batch_size, opt.weights,

opt.single_cls, opt.evolve, opt.data, opt.cfg, opt.resume, opt.noval,

opt.nosave, opt.workers, opt.freeze

#Directories

w = save_dir / 'weights'  #weights dir

(w.parent if evolve else w).mkdir(parents=True, exist_ok=True)  #make dir

last, best = w / 'last.pt', w / 'best.pt'

#Hyperparameters

if isinstance(hyp, str):

    with open(hyp, errors='ignore') as f:

        hyp = yaml.safe_load(f) #load hyps dict

LOGGER.info(colorstr('hyperparameters: ') + ', '.join(f'{k}={v}' for k, v in hyp.items()))

#Save run settings

if not evolve:

    with open(save_dir / 'hyp.yaml', 'w') as f:

        yaml.safe_dump(hyp, f, sort_keys=False)

    with open(save_dir / 'opt.yaml', 'w') as f:

        yaml.safe_dump(vars(opt), f, sort_keys=False)

#Loggers

data_dict = None

if RANK in [-1, 0]:

    loggers = Loggers(save_dir, weights, opt, hyp, LOGGER)  #loggers instance

    if loggers.wandb:

        data_dict = loggers.wandb.data_dict

        if resume:

            weights, epochs, hyp, batch_size = opt.weights, opt.epochs, opt.hyp, opt.batch_size

    #Register actions

    for k in methods(loggers):

        callbacks.register_action(k, callback=getattr(loggers, k))

#Config

plots = not evolve  #create plots

cuda = device.type != 'cpu'

init_seeds(1 + RANK)

with torch_distributed_zero_first(LOCAL_RANK):

    data_dict = data_dict or check_dataset(data)  #check if None

train_path, val_path = data_dict['train'], data_dict['val']

nc = 1 if single_cls else int(data_dict['nc'])  #number of classes

names = ['item'] if single_cls and len(data_dict['names']) != 1 else data_dict['names']  #class names

assert len(names) == nc, f'{len(names)} names found for nc={nc} dataset in {data_dict}'  #check

is_coco = isinstance(val_path, str) and val_path.endswith('coco/val2017.txt')  #COCO dataset

#Model

check_suffix(weights, '.pt')  #check weights

pretrained = weights.endswith('.pt')

if pretrained:

    with torch_distributed_zero_first(LOCAL_RANK):

        weights = attempt_download(weights)     #download if not found locally

        #load checkpoint to CPU to avoid CUDA memory leak

        #load checkpoint to CPU to avoid CUDA memory leak

        ckpt = torch.load(weights, map_location='cpu')

        model = Model(cfg or ckpt['model'].yaml, ch=3, nc=nc,

            anchors=hyp.get('anchors')).to(device)  #create

        exclude = ['anchor'] if (cfg or hyp.get('anchors')) and not resume else []  #exclude keys

        csd = ckpt['model'].float().state_dict()  #checkpoint state_dict as FP32

        csd = intersect_dicts(csd, model.state_dict(), exclude=exclude)  #intersect

        model.load_state_dict(csd, strict=False)  #load

        LOGGER.info(f'Transferred {len(csd)}/{len(model.state_dict())} items from {weights}')

else:

    model = Model(cfg, ch=3, nc=nc, anchors=hyp.get('anchors')).to(device)  #create

#Freeze

freeze = [f'model.{x}.' for x in (freeze if len(freeze) > 1 else range(freeze[0]))]  #layers to freeze

for k, v in model.named_parameters():

    v.requires_grad = True  #train all layers

    if any(x in k for x in freeze):

        LOGGER.info(f'freezing {k}')

        v.requires_grad = False

#Image size