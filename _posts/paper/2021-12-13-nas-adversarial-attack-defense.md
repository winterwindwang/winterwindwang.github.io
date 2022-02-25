---
layout: post
title: 【文献调研】NAS对抗攻击和防御方法
category: 文献阅读
tags: NAS, 对抗攻击, 对抗防御
keywords: adversarial attack/defense,  nas
---

# 【ICCV2021】AdvRush: Searching for Adversarially Robust Neural Architectures

关键词：

一句话概述：

---

## 摘要

深度神经网络以他们令人印象深刻的性能持续地令世界敬畏。然而，他们的预测容易受到对抗样本的破坏。当前**提升模型对对抗样本的鲁棒性**的工作主要几种在开发鲁棒训练方法，通过**以更鲁棒的方向更新网络的权重**。在本文，作者绕过权重参数的训练，考虑设计一种**具有内在鲁棒性**的**对抗鲁棒性网络结构**。作者提出AdvRush，一种对抗鲁棒性神经网络结构搜索算法，基于找到独立于训练方法，神**经网络的内在鲁棒性可以被表示成其输入损失景观的平滑性**。通过一个正则化算子，支持具有平滑输入损失景观的候选结构，AdvRush可以成功地发现对抗鲁棒结构。伴随着AdvRush全面理论动机，我们进行了大量的实验来证明AdvRush在不同benchmark数据集上的效率。特别是，在CIFAR10上，在对抗训练后，AdvRush取得了在FGSM下55.91%的鲁棒准确率，在7-step PGD对抗训练后在AutoAttack上取得了50.04%的鲁棒准确率。

## 引言

作者指出尚有一个重要的问题等待探索：能否利用具有高度内在鲁棒性的结构来提升模型的对抗鲁棒性？如果可以，能否可以自动地搜索这种鲁棒的神经网络结构。

作者应用神经网络搜索来搜索鲁棒的神经网络结构。现有的NAS算法的主要目标是提升标准准确率，因此，他们在搜索过程中并未考虑搜索结构的鲁棒性。结果，他们并未保证搜索得到的模型结构的鲁棒性，因为没有免费的午餐理论，对抗性鲁棒性阻止神经网络在没有额外努力的情况下获得足够的鲁棒性。此外，标准准确率和对抗鲁棒性之间的trade-off表明最大化标准准确率和对抗鲁棒性不可同时兼得，因此需要设计特别NAS来得到对抗鲁棒性的模型。

贡献：

+ 作者提出了AdvRush，以及一种新颖的NAS算法来探索鲁棒的神经网络结构。由于AdvRush并不需要独立对抗训练候选结构，它的搜索过程很高效。
+ 通过系统地验证证明了AdvRush在一系列对抗攻击下的有效性。进一步地，我们验证了AdvRush在不同基准数据集下的迁移性。
+ 为AdvRush提供了理论证明，并且用视觉分析补充了发现的结构。此外，**作者还发现了提升模型对对抗扰动鲁棒性的关键**。

## 方法

搜索对抗鲁棒性神经网络结构的问题可以重新建模成搜索具有平滑输入景观的神经网络结构。对于为每个网络权重wt计算网络fA(wt)的曲率是不可行的，因此，作者选择在如下标准训练下验证候选结构

![image-20211213224241795](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213224241795.png)

其中Hstd是fA(Wstd)的Lstd在xstd处的海森矩阵，λ表示**Hstd的最大特征值**。

因此，在搜索阶段，AdvRush使用λ惩罚了候选结构。支持具有更加平滑损失景观的网络结构。

### AdvRush框架

作者提出advRush的双层优化问题表示成

![image-20211213225112693](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213225112693.png)

Dtrain是训练数据，Dval是验证数据。

Lλ的近似

![image-20211213225258103](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213225258103.png)

其中$z \sim N(0,I_d)$，h控制损失景观的平滑性。但是由于需要计算大量的梯度，所以计算效率很低，因此作者沿着最高的曲率方向最小化输入损失的景观。

![image-20211213225625554](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213225625554.png)

使用该近似的Lλ，AdvRush的双层优化问题可以表示成

![image-20211213225707260](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213225707260.png)

xval是Dval中的干净数据集。γ是正则化的强度。

由于随机初始化超网的损失景观是无用的信息，因此，作者不使用Lλ对α和w使用warm up策略。完成warm-up 结束后，作者引入Lλ。

## 实验

数据集：CIFAR-10

基准可微分搜索算法：DARTS

### 实验结果

#### 白盒攻击

![image-20211213230222770](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213230222770.png)

白盒攻击上的结果显示AdvRush可以达到很高的标准和鲁棒准去率。

### 黑盒攻击

![image-20211213230309081](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213230309081.png)

AdvRush对基于迁移的黑盒攻击算法最有效。（难道这不是因为基于迁移攻击的黑盒攻击本身就很弱吗？）

### 数据集之间的迁移性

![image-20211213230421373](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213230421373.png)

该实验结果隐式地证明了在简单地数据集上，如SVHN，鲁棒性结构足以取得较高的鲁棒性，甚至不需要对抗训练方法。

### 损失景观可视化

![image-20211213230635140](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213230635140.png)