---
layout: post
title: 【文献调研】新颖对抗攻击方法
category: 文献阅读
keywords: image-to-image translation, GAN, adversarial attack， nullifying attack
tags: 图像转换, 无效攻击, 黑盒攻击
---

# 【ICCV2021】Attack as the Best Defense Nullifying Image-to-image Translation GANs via Limit-aware Adversarial Attack

一句话介绍：作者提出了一种数据预处理方法，使用该方法可以使图像转换（图像风格变换）效果失效，如图像风格变换可以将一个人的头发换成其他颜色，使用作者方法处理后的图片在经过风格变换时保持不变。

---

## 摘要

由于图像到图像转换GAN的巨大成功，引发了很多应用上的道德问题，如deepfake, deepnude，给防止这些技术的滥用提出了巨大的挑战。在本文，作者通过设计一种新颖的对抗攻击模式来解决该问题，称为**Nullifying attack**（无效攻击），取消图像转换过程和提出一种响应过程，即黑盒场景下的**Limited-Aware self-Guiding Gradient Sliding Attack(LaS-GSA)**。换句话说，先通过使用提出的LaS-GSA处理图像，然后任何有害的修改都将变得无效。

+ 首先，作者介绍了**limit-aware RGF**和**gradient sliding mechanism**来估计遵守对抗性限制的梯度，如对抗样本的像素值约束。作者理论证明了方法有能力通过在方向和长度上的投影来避免错误的发生。
+ 然后，从受害模型中提取了一种有效的自监督先验，目标图像可以很好地利用该先验信息并且知道梯度估计过程。

大量地实验结果证明LaS-GSA需要很少地查询就能可以以很高的成功率使图像转换过程失效。

![image-20211123145113290](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211123145113290.png)
