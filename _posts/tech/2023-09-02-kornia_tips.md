---
layout: post
title: Kornia数据增强技术版本差异问题以及解决方案
category: 技术
keywords: python
tags: technical details
---

# Kornia AffineTransformer

提示：kornia与torch版本对比：https://kornia.readthedocs.io/en/v0.6.7/augmentation.html

kornia < v0.6.10版本,示例代码：

```python
import torch
from kornia.augmentation import RandomAffine
from kornia.geometry import warp_affine
import copy

transform_mat = {
        "translate": (.3, .3),
        'degrees': 45.,
        "scale": (0.9, 1.1)
    }
aff_transformer = RandomAffine(**transform_mat, return_transform=True)

input1 = torch.rand((1,3,3,3))
input2 = copy.deepcopy(input1)
>> input2
tensor([[[[0.2903, 0.5883, 0.6943],
          [0.6972, 0.5964, 0.2566],
          [0.7513, 0.8165, 0.0481]],

         [[0.7780, 0.7027, 0.2204],
          [0.1610, 0.4645, 0.6134],
          [0.3792, 0.9236, 0.2911]],

         [[0.4979, 0.8827, 0.9596],
          [0.6056, 0.3297, 0.0025],
          [0.7898, 0.9005, 0.2488]]]])

input1, transformer_matrix = aff_transformer(input1)
input2 = warp_affine(input2, transformer_matrix[:,:2,:], dsize=(3,3))

>> input1
tensor([[[[0.2903, 0.5883, 0.6943],
          [0.6972, 0.5964, 0.2566],
          [0.7513, 0.8165, 0.0481]],

         [[0.7780, 0.7027, 0.2204],
          [0.1610, 0.4645, 0.6134],
          [0.3792, 0.9236, 0.2911]],

         [[0.4979, 0.8827, 0.9596],
          [0.6056, 0.3297, 0.0025],
          [0.7898, 0.9005, 0.2488]]]])

>> input2
tensor([[[[0.2903, 0.5883, 0.6943],
          [0.6972, 0.5964, 0.2566],
          [0.7513, 0.8165, 0.0481]],

         [[0.7780, 0.7027, 0.2204],
          [0.1610, 0.4645, 0.6134],
          [0.3792, 0.9236, 0.2911]],

         [[0.4979, 0.8827, 0.9596],
          [0.6056, 0.3297, 0.0025],
          [0.7898, 0.9005, 0.2488]]]])
```

AffineTransformer函数中有return_transform参数，但是在新版(>=v0.6.10)中却没有。如果强行指定该参数会报如下错误：

```
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: __init__() got an unexpected keyword argument 'return_transform'
```

查阅相关api与源码后，发现可以使用以下方法进行替代

```python
input1 = torch.rand((1,3,3,3))
input2 = copy.deepcopy(input1)
>> input1
tensor([[[[0.4527, 0.7195, 0.3826],
          [0.5125, 0.9755, 0.7425],
          [0.0060, 0.6773, 0.5196]],

         [[0.5307, 0.0591, 0.8750],
          [0.5675, 0.3772, 0.9713],
          [0.1934, 0.2576, 0.1242]],

         [[0.5124, 0.0553, 0.7935],
          [0.1680, 0.5821, 0.1888],
          [0.7296, 0.2973, 0.9806]]]])

input1 = aff_transformer(input1)
# 第一次用完后，立刻用aff_transformer._params得到上一次变换的矩阵
input2 = aff_transformer(input2, params=aff_transformer._params)

>> input1
tensor([[[[0.4527, 0.7195, 0.3826],
          [0.5125, 0.9755, 0.7425],
          [0.0060, 0.6773, 0.5196]],

         [[0.5307, 0.0591, 0.8750],
          [0.5675, 0.3772, 0.9713],
          [0.1934, 0.2576, 0.1242]],

         [[0.5124, 0.0553, 0.7935],
          [0.1680, 0.5821, 0.1888],
          [0.7296, 0.2973, 0.9806]]]])
>> input2
tensor([[[[0.4527, 0.7195, 0.3826],
          [0.5125, 0.9755, 0.7425],
          [0.0060, 0.6773, 0.5196]],

         [[0.5307, 0.0591, 0.8750],
          [0.5675, 0.3772, 0.9713],
          [0.1934, 0.2576, 0.1242]],

         [[0.5124, 0.0553, 0.7935],
          [0.1680, 0.5821, 0.1888],
          [0.7296, 0.2973, 0.9806]]]])
```

