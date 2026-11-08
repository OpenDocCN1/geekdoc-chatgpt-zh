parser.add_argument("--total_num_steps", type=int, default=100000, help="训练总轮次步数")

    parser.add_argument("--gradient_accumulation_steps", type=int, default=1, help="梯度累积的步频")

    parser.add_argument("--per_device_train_batch_size", type=int, default=16, help="单卡训练批次大小")

    parser.add_argument("--max_length", type=int, default=1024, help="输入序列最大长度")

    parser.add_argument('--gradient_checkpointing', action='store_true', help='打开梯度检查点以降低显存占用，默认关闭')

    parser.add_argument("--log_steps", type=int, default=10, help="日志输出的步长间隔")

    parser.add_argument("--save_steps", type=int, default=-1, help="模型保存的步长间隔")

    parser.add_argument('--ds_offload_cpu', action='store_true', help='是否启用CPU卸载')

    parser.add_argument('--ds_zero_stage', type=int, default=2, help='DeepSpeed ZeRO 分布式优化阶段')

    parser.add_argument('--ds_steps_per_print', type=int, default=100, help='DeepSpeed日志打印间隔')

    parser.add_argument("--local_rank", type=int, default=-1, help="多进程多卡时的本地进程编号")

    parser.add_argument("--global_rank", type=int, default=-1, help="多进程多卡时的全局进程编号")

    parser = deepspeed.add_config_arguments(parser)

    args = parser.parse_args()

    return args

def get_deepspeed_config(args):

    ds_config = {

        "train_micro_batch_size_per_gpu": args.per_device_train_batch_size,

        'gradient_accumulation_steps': args.gradient_accumulation_steps,

        "steps_per_print": args.ds_steps_per_print,

        "zero_optimization": {

            "stage": args.ds_zero_stage,

        },

        "scheduler": {

            "type": "WarmupDecayLR",

            "params": {

                "total_num_steps": args.total_num_steps,

                "warmup_min_lr": 0,

                "warmup_max_lr": args.learning_rate,

                "warmup_num_steps": args.num_warmup_steps

            }

        },

        "optimizer": {

            "type": "AdamW",

            "params": {

                "lr": args.learning_rate,

                "weight_decay": args.weight_decay,

            }

        },

        "fp16": {

            "enabled": True,

        },

        "gradient_clipping": 1.0,

        "prescale_gradients": False,

        "wall_clock_breakdown": False,

    }

    return ds_config

[PRE0]python

import torch

import json

from dataclasses import dataclass

from datasets import load_from_disk

from main import log_dist

class JsonlDatasetPT(torch.utils.data.Dataset):

    """

        JSONL格式预训练数据加载器

    """

    def __init__(self,

                 data_path,

                 tokenizer,

                 max_length,

                 ):

        self.dataset = []

        with open(data_path, 'r', encoding='utf-8') as f:

            for line in f:

                text = json.loads(line)['text']

                inputs = tokenizer.encode_plus(

                    text,

                    add_special_tokens=True,

                    max_length=max_length,

                    padding='max_length',

                    return_tensors='pt',

                    truncation=True

                )

                input_ids = inputs['input_ids'].squeeze()

                self.dataset.append({

                    'input_ids':input_ids,

                })

        log_dist(f'Loaded {len(self.dataset)} examples from {data_path}')

    def __len__(self):

        return len(self.dataset)

    def __getitem__(self, idx):

        return self.dataset[idx]

def get_pt_dataset(args):

    """

        读取已词表化并保存到磁盘的预训练数据集

    """

    train_dataset = load_from_disk(args.data_path)

    train_dataset = train_dataset.shuffle(seed=42)

    return train_dataset

class JsonDatasetSFT(torch.utils.data.Dataset):

    """

        JSON格式指令微调数据加载器

    """

    def __init__(self,

                 data_path,

                 tokenizer,

                 max_length,

                 ):

        super().__init__()

        self.dataset = []

        with open(data_path, 'r') as file:

            for line in file:

                sample = json.loads(line)

                sentence = sample['instruction'] + sample['response']

                tokenized = tokenizer(

                    sentence,

                    max_length=max_length,

                    padding="max_length",

                    truncation=True,

                    return_tensors="pt")

                tokenized["input_ids"] = tokenized["input_ids"].squeeze(0)

                tokenized["attention_mask"] = tokenized["attention_mask"].squeeze(0)

                self.dataset.append(tokenized)

        log_dist(f'Loaded {len(self.dataset)} examples from {data_path}')

    def __len__(self):

        length = len(self.dataset)

        return length

    def __getitem__(self, idx):

        return {

            "input_ids": self.dataset[idx]["input_ids"],

            "labels": self.dataset[idx]["input_ids"],

            "attention_mask": self.dataset[idx]["attention_mask"]

        }

@dataclass
class DataCollatorForPT(object):

    """

        预训练用数据拼接器，生成输入与标签

    """

    pad_token_id: int = 0

    ignore_index: int = -100

    max_length: int = -1

    def __call__(self, instances: list) -> dict:

        if self.max_length > 0:
            input_ids = torch.stack([instance['input_ids'][:self.max_length] for
                instance in instances], dim=0)
        else:
            input_ids = torch.stack([instance['input_ids'] for
                instance in instances], dim=0)

        labels = input_ids.clone()

        labels[labels == self.pad_token_id] = self.ignore_index

        return dict(
            input_ids=input_ids,
            labels=labels,
        )

@dataclass
class DataCollatorForSFT(object):

    """

        指令微调用数据拼接器，生成输入、标签与注意力掩码

    """

    pad_token_id: int = 0

    def __call__(self, features):

        len_ids = [len(feature["input_ids"]) for feature in features]

        longest = max(len_ids)

        input_ids_list = []

        labels_list = []

        for ids_l, feature in sorted(zip(len_ids,features),key=lambda x: -x[0]):

            ids = feature["input_ids"]

            labels = feature["labels"]

            labels = labels[:len(ids)]

            ids += [self.pad_token_id] * (longest - ids_l)

            labels += [-100] * (longest - ids_l)

            input_ids_list.append(torch.LongTensor(ids))

            labels_list.append(torch.LongTensor(labels))

        input_ids = torch.stack(input_ids_list)

        labels   = torch.stack(labels_list)

        return {
            "input_ids": input_ids,
            "labels": labels,