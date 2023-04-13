---
layout: post
title: Linux中使用多个anconda环境时，共享共享预训练模型
category: 技术
keywords: pretrained model, linux, torch
tags: technical details
---

>  前言：在linux系统上使用不同conda环境，但是不想重复下载torch预训练模型时，看这里！！！

# 步骤

在window上新建一个py文件并写下以下代码:

```python
from torchvision import models
model = models.renset50(pretrained=True) #
```

定位到resnet.py文件=>找到`model_ursl`，并且定位到使用它的位置`load_state_dict_from_url(model_urls[arch],progress=progress）`并且进一步定位=>'hub.py'文件，可以在line206左右看到一个判断是否有`_hub_dir`的判断，这就是问题的关键。torch会判断系统中是否有指定了`_hub_dir`，无的话则说明要下载或者进一步判断。将该`_hub_dir`改成你的预训练模型文件夹即可。

其中hub.py地址为

```
/home/ubuntu/anaconda3/lib/python3.9/site-packages/torch/hub.py
```



