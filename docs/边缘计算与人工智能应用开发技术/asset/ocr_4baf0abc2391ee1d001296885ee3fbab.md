```
```python
import argparse
import json
import os
import platform
import subprocess
import sys
import time
import warnings
from pathlib import Path

import pandas as pd
import torch
import torch.nn as nn
from torch.utils.mobile_optimizer import optimize_for_mobile

FILE = Path(__file__).resolve()
ROOT = FILE.parents[0]  # YOLOv5 root directory
if str(ROOT) not in sys.path:
    sys.path.append(str(ROOT))  # add ROOT to PATH
ROOT = Path(os.path.relpath(ROOT, Path.cwd()))  # relative

from models.common import Conv
from models.experimental import attempt_load
from models.yolo import Detect
from utils.activations import SiLU
from utils.datasets import LoadImages
from utils.general import (LOGGER, check_dataset, check_img_size, check_requirements,
                           check_version, colorstr, file_size, print_args, url2file)
from utils.torch_utils import select_device

def export_formats():
    # YOLOv5 export formats
    x = [['PyTorch', '-', '.pt'],
```
```