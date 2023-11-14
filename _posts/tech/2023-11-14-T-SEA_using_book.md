---
layout: post
title: T-SEA使用手册
category: 技术
keywords: torch
tags: technical details
---

> 本文旨在使用T-SEA训练对抗补丁，为数据集生成测试标签，验证补丁攻击性能。

T-SEA集成度较高，如果使用其代码，使用不同数据集，那么改的地方比较小。以下是训练和测试的需要修改的地方

# 1、训练

1. 如果不更改任何东西，直接使用T-SEA代码进行训练，那么首先`./config/advpatch`文件夹进行复制，并命名为`./config/newadvpatch`，然后在其目录下修改具体文件，如`v5.yaml`文件

```
DATA:
  CLASS_NAME_FILE: 'configs/namefiles/coco80.names'  # 这个为检测器所使用的类别标签-类别名对应文件
  AUGMENT: 1

  TRAIN:
    IMG_DIR: ../dataset/INRIAPerson/Train/pos   # 训练数据集合， 该目录下全是图片
    LAB_DIR: 

DETECTOR:
  NAME: ["YOLOV5"]  # 所使用的检测器，会根据这个名字从detlib/utils.py中选择所对应的detector作为攻击目标
  INPUT_SIZE: [640, 640]  #  [416, 416]  [640, 640]   # 这里为了方便，统一使用了640的设置
  BATCH_SIZE: 8
  CONF_THRESH: 0.5
  IOU_THRESH: 0.45
  PERTURB:
    GATE: null

ATTACKER:
  METHOD: "optim"			# T-SEA支持其他更新方式
  EPSILON: 255
  MAX_EPOCH: 1000
  ITER_STEP: 1
  STEP_LR: 0.03
  ATTACK_CLASS: '0'			# 攻击类别，攻击行人，所以这里设置的是0
  LOSS_FUNC: "obj-tv"       # 所设计的损失函数
  tv_eta: 2.5
  LR_SCHEDULER: 'plateau'

  PATCH:
    WIDTH: 300				# 补丁大小
    HEIGHT: 300
    SCALE: 0.2
    INIT: "gray"
    TRANSFORM: ['jitter', 'median_pool', 'rotate']
```

2. 更改完以后，将`train_optim.py`中对应的参数进行修改

```
    import argparse
    import warnings
    warnings.filterwarnings('ignore')

# 这里只给出需要更改的参数，cfg, save_patch
    parser = argparse.ArgumentParser()
    parser.add_argument('-p', '--patch', type=str, default=None)
    parser.add_argument('-cfg', '--cfg', type=str, default='advpatch/v5.yaml')
    parser.add_argument('-s', '--save_path', type=str, default='./results/exp_inria_dataset/optim')
    args = parser.parse_args()

    device = torch.device("cuda:0" if torch.cuda.is_available() else "cpu")
    args.cfg = './configs/' + args.cfg  
    save_patch_name = args.cfg.split('/')[-1].split('.')[0] if args.board_name is None else args.board_name
```

，并使用以下命令即可完成训练

```
nohup python -u train_optim.py > train_tsea_patch_on_inria_dataset_20231009.out 2>&1 &
```

或者可以使用其自带的bash代码

```
nohup bash -u scripts/train.sh > train_adv_patch_xxx.out 2>&1 &
```

## 2、测试

由于其测试集成度较高，改的地方比较多。

1. 首先是生成各个检测器对应的预测标签，具体是使用`utils/preprocesser/gen_det_labels.py`该文件，使用前需要注意几点，第一是生成标签的文件名是由`args.keep_scale`控制的，如果指定了该参数，则生成的文件名就会变成`yolov5-rescale-labels`，所以要特别注意。也可以直接将该参数设置成False，然后在`save_root`参数里面指定要保存的路径，如`dataset/INRIAPerson/FractalSet/label/test/yolov4-rescale-labels`

2. 其次对真实标签的文件名也有要求，必须是`ground-truth-labels`，因此如果真实标签的文件名不是该文件名，需要复制一份重新命名，`cp dataset/labels dataset/ground-truth-labels`

3. 在`evaluate.py`中，同样要设置`label_postfix`参数，因为该代码中会使用软连接，所以要特别注意文件名的设置。最后修改完后，使用其提供的bash代码进行测试

```
nohup bash -u scripts/train.sh > evaluate_adversarial_patch_xxxx.out 2>&1 &
```

