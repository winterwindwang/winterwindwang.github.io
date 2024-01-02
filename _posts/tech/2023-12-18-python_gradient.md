---
layout: post
title: Python中求梯度的奇怪现象
category: 技术
keywords: python, torch
tags: technical details
---

> 。

> 在使用pytorch循环求梯度时，发现以下现象

```python
for i in range(self.steps):
	adv_images.requires_grad = True

	# 下面这行代码会使得求梯度时，对象由上面一行转移到下面一行
    adv_images = self.input_diversity(adv_images)
	outputs = self.get_logits(adv_images)
    
	# Calculate loss
	if self.targeted:
	    cost = -loss(outputs, target_labels)
	else:
	    cost = loss(outputs, labels)

	# Update adversarial images
	grad = torch.autograd.grad(cost, adv_images,
	                           retain_graph=False, create_graph=False)[0]

	with torch.no_grad():
	    pred = torch.argmax(outputs.detach(), dim=1)
	    is_equal_list.append((pred == labels).cpu())

	grad = grad / torch.mean(torch.abs(grad), dim=(1,2,3), keepdim=True)
	grad = grad + momentum*self.decay
	momentum = grad

	adv_images = adv_images.detach() + self.alpha*grad.sign()
	delta = torch.clamp(adv_images - images, min=-self.eps, max=self.eps)
	adv_images = torch.clamp(images + delta, min=0, max=1).detach()
```

上面代码求出的梯度有误，猜测原因可能是`self.input_diversity`赋予的新变量`adv_images`会影响后续求梯度，grad是对进行变换后的样本求的，而不是针对最开始的样本求的。

```python
for i in range(self.steps):
	adv_images.requires_grad = True
	
    # solution 1 
	adv_images1 = self.input_diversity(adv_images)
	outputs = self.get_logits(adv_images1)
	# solution 2
	# outputs = self.get_logits(self.input_diversity(adv_images))

    # Calculate loss
	if self.targeted:
	    cost = -loss(outputs, target_labels)
	else:
	    cost = loss(outputs, labels)

	# Update adversarial images
	grad = torch.autograd.grad(cost, adv_images,
	                           retain_graph=False, create_graph=False)[0]

	with torch.no_grad():
	    pred = torch.argmax(outputs.detach(), dim=1)
	    is_equal_list.append((pred == labels).cpu())

	grad = grad / torch.mean(torch.abs(grad), dim=(1,2,3), keepdim=True)
	grad = grad + momentum*self.decay
	momentum = grad

	adv_images = adv_images.detach() + self.alpha*grad.sign()
	delta = torch.clamp(adv_images - images, min=-self.eps, max=self.eps)
	adv_images = torch.clamp(images + delta, min=0, max=1).detach()
```

