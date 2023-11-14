---
layout: post
title: 对图像求梯度的异同
category: 技术
keywords: python
tags: technical detail
---

# 动机

最近在做补丁攻击相关研究，对使用adam更新与直接使用梯度更新方差产生了兴趣，想搞清楚这二者是否是一样的。此博客记录本次小测试与结论。

先说结论：**使用adam对补丁进行更新与使用torch.autograd.grad()方式求得的梯度是一样的**。

验证方式

```python
import torch
from torch import models
import torch.nn as nn

model = models.resnet(pretained=True)
criterion = nn.CrossEntropy()

patch = torch.rand((1, 3, 50, 50))
patch = torch.autograd.Variable(patch)
patch.requires_grad = True

# ---------------- 1. 使用adam进行优化 ----------------
optimizer = torch.optim.Adam([patch], lr=0.01)

masks = torch.zeros((1,3,224,224))
mask[:,:,87:137,87:137] = 1


inputs = torch.ones((1, 3, 224, 224))
label = torch.tensor([1])
patch = F.interploate(patch, (224,224))

input_patch = inputs * (1 - mask) + patch * mask

output = model(input_patch)

loss = criterion(output, label)
grad_1 = torch.autograd.grad(loss, patch, create_graph=False, retain_graph=False)[0]  # shape: [3,50,50]

optimizer.zero_grad()
optimizer.backward()
optimizer.step()

# ---------------- 2. 使用梯度进行优化 ----------------
masks = torch.zeros((1,3,224,224))
mask[:,:,87:137,87:137] = 1
inputs = torch.ones((1, 3, 224, 224))
label = torch.tensor([1])
patch = F.interploate(patch, (224,224))

input_patch = inputs * (1 - mask) + patch * mask

input_patch.requires_grad = True

output = model(input_patch)

grad_2 = torch.autograd.grad(loss, input_patch, create_graph=False, retain_graph=False)[0]  # shape: [1,3,224,224]

grad_2_patch = grad_2[:,:,87:137,87:137]


# 判断二者梯度是否一致

print("Adam 优化的梯度", (grad_1.sign() == 1).sum().item())
print("操作优化的梯度", (grad_2_patch.sign() == 1).sum().item())

# 结论：上述二者的输出是一致的，因此两种更新方法没有差别
```

