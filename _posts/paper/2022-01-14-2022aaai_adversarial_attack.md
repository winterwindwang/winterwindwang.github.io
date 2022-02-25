---
layout: post
title: 【2022AAAI】对抗样本攻击
category: 文献阅读
keywords: adversarial examples
tags: 对抗样本攻击
---

# CMUA-Watermark: A Cross-Model Universal Adversarial Watermark for Combating Deepfakes
## 概览

![image-20220114142102693](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114142102693.png)

通过向图片中添加对抗扰动，使生成式模型失效。**攻击的是Deepfakes模型**，不是deepfake结果。可以应用于隐私保护，比如说用户将自己的照片发布到网络之前添加这种扰动，就可以避免被deepfake模型使用。

## 研究背景

deepfake防御分成以下两部分：

+ passive defenses：设计一个二分类检测器，判断图像是否来自deepfakes
+ active defense：在deepfake生成图片之前防御实施防御

### Active defense

针对Deepfakes，目前有研究提出**使用对抗水印来攻击deepfake模型**，从而导致**生成失真的输出**。但是当前的研究存在两个迁移性问题：

+ image level：需要为每一张图片生成对抗水印
+ model level：模型直接的迁移性不高

### 提出方案

+ image level：通用对抗扰动（图像层级融合）
+ model level：模型集成策略

## 方法

框架

![image-20220114143620403](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114143620403.png)

### 两个步骤

+ 第一步：**repeatedly conduct the cross-model universal attack** with **a small batch size** (for a faster search), evaluate the generated CMUA-Watermark, and then **use automatic step size tuning to select new attack step sizes** a1,.., am。
+ 第二步：we **use the found step sizes** a\*1,..., a*m m to **conduct the cross-model universal attack with a large batch size** (for enhancing the disrupting capability) and generate the final CMUA-Watermark.

算法

![image-20220114145340772](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114145340772.png)

针对单个模型生成通用对抗扰动的两阶段办法：

+ 第一阶段（搜索最优的step size）
  + 首先使用小的batch size重复执行跨模型通用攻击，
  + 然后验证生成的通用对抗扰动；
  + 接着自动搜索最优的攻击step size, a1,...,am（一个模型一个step size）
+ 第二阶段
  + 使用第一步搜索得到的的step size，使用更大的batch size来执行跨模型通用攻击，并且生成最终的通用扰动。

针对当前模型生成的对抗扰动被用于攻击下一个模型的初始化扰动。

### 攻击单个模型的单张图片

优化目标

![image-20220114145519971](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114145519971.png)

其中G（I）为输入图像I时，deepfake模型的输出。使用PGD算法更新对抗样本

![image-20220114145557017](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114145557017.png)

### 对抗扰动融合

#### image-level

图像级别的融合

![image-20220114145836456](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114145836456.png)

取batch中符号的均值，j是图片在batch中的下标

$G_{avg}$ to **concentrate more on the common attributes** of human faces rather than a specific face’s attributes.

使用PGD算法得到在$G_{avg}$优化下的$P_{avg}$

#### model-level 

模型级别的融合

![image-20220114150456801](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114150456801.png)

简单来说通用对抗扰动保留了针对历史模型的攻击信息

### 基于TPE的自动step size调优

we find that the attack step sizes for different models are also important for the transferability of the generated CMUA-Watermark.

针对基于梯度符号而言，有如下形式

![image-20220114151317816](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114151317816.png)

但是在实际计算中，梯度几乎不为0，所以符号的绝对值几乎为1。故基于符号的攻击有以下形式

![image-20220114151516108](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114151516108.png)

可见，α决定了更新率，所以影响很大。为此，对于通用攻击而言，有

![image-20220114151703797](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114151703797.png)

为此，引入TPE搜索超参a。**TPE is a hyper-parameter optimization method** based on Sequential Model-Based Optimization (SMBO), which **sequentially** constructs models to **approximate the performance of hyperparameters** based on **historical measurements**, and then subsequently chooses new hyperparameters to test based on this model.

令α为输入参数x，攻击的成功率作为 TPE 的相关质量评分 y。使用P(x|y)和P(y)建模P(y|x), p(x|y)由以下项给出

![image-20220114152433264](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114152433264.png)

where **y\* is determined by the historically best observation**, l**(x) is the density formed with the observations {x(i)}** such that the corresponding loss is lower than y*, and g(x) is the density formed with the remaining observations.

最终，在每次搜索迭代中，通过优化期望提升做为准则来搜索最佳的step sizes，由下式给出

![image-20220114153236154](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114153236154.png)



## 实验

**数据**：

+ CelebA：训练数据(128 images)
+ LFW:
+ Films 100: 实际场景

**目标网络**：

+ StarGAN
+ ACGAN
+ AttGAN
+ HiSD

参数：

+ small batch: 16
+ large batch size: 64

验证指标：

+ 由于修改的区域占图像的比重较少，因此直接计算干净图片与修改图片的欧式距离会受到非修改区域的影响。因此，为集中于图像中的修改部分，提出了以下公式计算出修改区域

![image-20220114154125242](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154125242.png)

从而，计算损失指标如下

![image-20220114154155273](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154155273.png)

如果大于0.05，就认为保护成功了，SR来衡量保护面部图像成功率。

+ FID：Frechet Inception 距离得分（Frechet Inception Distance score，FID）是计算真实图像和生成图像的特征向量之间距离的一种度量。FID 从原始图像的计算机视觉特征的统计方面的相似度来衡量两组图像的相似度，这种视觉特征是使用 Inception v3 图像分类模型计算的得到的。**分数越低代表两组图像越相似**，或者说二者的统计量越相似，FID 在最佳情况下的得分为 0.0，表示两组图像相同。

+ HyperFAS活动检测系统
  + TFHC：if the confidence score is greater than 0.99, we conclude that **the face is a true face with high confidence** 为真脸的比例
  + ACS： calculate **the average confidence score ACS for evaluation**.

## 实验结果

量化结果

![image-20220114154731272](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154731272.png)

对比结果

![image-20220114154814220](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154814220.png)

消融实验

![image-20220114154842098](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154842098.png)

图像质量

![image-20220114154936880](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154936880.png)

实际应用

![image-20220114154952695](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220114154952695.png)

总结感悟

+ 对抗水印作用于干净图像，对于隐私保护者而言，需要在将个人图片发布到公共信道前就添加该扰动，因此该方法有一定的局限性。
+ 对抗水印攻击的是现有的deepfake模型，如果攻击者考虑对抗噪声，那么该噪声就会失效。
+ 对于现有的一些神经网络应用，使用对抗攻击，尤其是通用对抗攻击是可以造车一些潜在威胁。