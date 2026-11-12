```
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
```