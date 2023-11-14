---
layout: post
title: PyTorch模型微调
category: 技术
keywords: torch
tags: technical details
---

> 本文旨在新数据上微调目标检测器，记录了微调过程中遇到的不同问题以及解决方案。

# Yolov5微调

## 数据准备

U版Yolov5的标签要求是：

```
def convert(size, box):
    # 该函数将xmin, ymin, w, h转为x,y,w,h中心点坐标和宽高
    # Convert flir box (xmin, ymin, w, h) to YOLO xywh box, (x,y) is the center
    # 将坐标归一化
    dw, dh = 1. / (size[0]), 1. / (size[1])
    # 将左上角坐标和宽高转化为归一化后的检测框：中心点坐标以及宽高
    return (box[0] + box[2] / 2) * dw, (box[1] + box[3] / 2) * dh, box[2] * dw, box[3] * dh
    # return (x, y, w, h)
```

待生成数据后，在data目录下新建一个`dataset.yaml`

```
# Train/val/test sets as 1) dir: path/to/imgs, 2) file: path/to/imgs.txt, or 3) list: [path/to/imgs1, path/to/imgs2, ..]
path: /mnt/jfs/wangdonghua/dataset/flir/
train: # train images (relative to 'path')  16551 images
  - images_thermal_train/images
val: # val images (relative to 'path')  4952 images
  - images_thermal_val/images
test: # test images (optional)

# Classes
nc: 80   # number of classes
#names: ["Person", "Bike", "Car", "Motorcycle", "Bus", "Train", "Truck",
#        "Traffic light", "Fire Hydrant", "Street Sign", "Dog", "Deer", "Skateboard", "Stroller", "Scooter", "Other Vehicle"]

names: [ 'person', 'bike', 'car', 'motor', 'airplane', 'bus', 'train', 'truck', 'boat',
         'light', 'hydrant', 'sign', 'parking meter', 'bench', 'bird', 'cat', 'dog',
         'deer', 'sheep', 'cow', 'elephant', 'bear', 'zebra', 'giraffe', 'backpack',
         'umbrella', 'handbag', 'tie', 'suitcase', 'frisbee', 'skis', 'snowboard',
        'sports ball', 'kite', 'baseball bat', 'baseball glove', 'skateboard',
         'surfboard', 'tennis racket', 'bottle', 'wine glass', 'cup', 'fork', 'knife',
         'spoon', 'bowl', 'banana', 'apple', 'sandwich', 'orange', 'broccoli', 'carrot',
         'hot dog', 'pizza', 'donut', 'cake', 'chair', 'couch', 'potted plant', 'bed', 'dining table',
         'toilet', 'tv', 'laptop', 'mouse', 'remote', 'keyboard', 'cell phone', 'microwave',
         'oven', 'toaster', 'sink', 'stroller', 'rider', 'scooter', 'vase', 'scissors', 'face',
         'other vehicle', 'license plate' ] # class names
```



## 模型训练（微调）

使用以下命令训练:

```
nohup python -u train_flir.py --data FLIR.yaml --weights yolov5s.pt --img 640 > train_FLIR_detector_yolov5s_20231113.out 2>&1 &
```



## 模型加载

本blog的模型加载均基于T-SEA库

遇到问题

1. Q：因torch版本问题导致模型权重不一致的问题

A：使用修改的的Yolo5_U模型进行替换

```
原代码：
self.detector = Model(model_config).to(self.device)
self.detector.load_state_dict(torch.load(model_weights, map_location=self.device)['model'].float().state_dict())
修改后：
ckpt = torch.load(model_weights, map_location=self.device)
self.detector = Model_U(ckpt['model'].yaml).to(self.device)
self.detector.load_state_dict(ckpt['model'].float().state_dict())
```





# torchvision中Faster RCNN的微调

repo地址：https://github.com/pytorch/vision/tree/main/references/detection



## 数据准备

Faster RCNN的标签要求是未经归一化的`[xmin,ymin,xmax,ymax]`

```
对于yolo生成的标签，可以使用方式进行转换
def read_label(lab_path, size):
"""
label_path为coco数据集的标签，其形式为
0 x_center, y_center, width, height
均为乘了1/w, 1/h后的归一化值
"""
    labels = []
    bboxes = []
    w, h = size
    with open(lab_path, 'r') as fr:
        for line in fr.readlines():
            info = line.split(" ")
            cls = coco91_names[coco80_names[eval(info[0])]]
            labels.append(cls)
            xc, yc, dw, dh = [eval(item) for item in info[1:]]
            xmin, ymin = xc - dw / 2, yc - dh / 2
            xmax, ymax = xc + dw / 2, yc + dh / 2
            # dw, dh = dw * w, dh * h
            bboxes.append([xmin * w, ymin * h, xmax * w, ymax * h])
    # 注意需要将考虑Faster RCNN中不允许存在超过图像范围内的标签，否则会报错。因此需要进行以下操作
    labels = np.array(labels)
    bboxes = np.array(bboxes)   
	keep = (bboxes[:, 3] > bboxes[:, 1]) & (bboxes[:, 2] > bboxes[:, 0])
    bboxes = bboxes[keep]
    labels = labels[keep]
    return labels, bboxes
```

然后需要将train.py文件中的get_dataset.py进行修改

```
def get_dataset(name, image_set, transform, data_path):
    paths = {
        "flir": (data_path, 91),
        "visdrone": (data_path, 80),
    }
    # TODO 可以使用于不同数据集，目前只适用于红外数据集
    type = {
        "train": "images_thermal_train",
        "val": "images_thermal_val",
    }
    p, num_classes = paths[name]
    ds = FLIRDataset(data_root=os.path.join(p, type[image_set], "images"),
                             label_root=os.path.join(p, type[image_set], "labels"),
                             transforms=transform)
    return ds, num_classes
```



## 模型训练

#### 训练时，调整完学习率和迭代次数后，只需使用以下命令就可训练

```
nohup python -u train.py --data-path /mnt/jfs/wangdonghua/dataset/flir/ > train_flir_faster_rcnn_resnet_20231113.out 2>&1 &
```

训练注意注意的事项：

1. 学习率设置在0.001以上时会出现梯度爆炸，需要将学习率调到1e-4



## 模型加载（TODO）

