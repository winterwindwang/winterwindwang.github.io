---
layout: post
title: 【ICCV2021对抗攻击文献调研】
category: 文献阅读
keywords: adversarial attack, ICCV2021
tags: 对抗攻击, ICCV2021
---

# 【ICCV2021】AdvDrop Adversarial Attack to DNNs by Dropping Information

关键词：DCT, frequency domain, dropped image information

一句话概述：通过drop out图像频率域中的一些噪声实现对抗攻击。（减性攻击），从而保证了图像质量，并且能够获得与PGD相当的攻击效果。

## 摘要

人类很容易识别有损信息的视觉目标：甚至丢失绝大多数细节，仅保留边缘，如卡通。然而，对于DNN的视角感受能力，识别抽象物体的能力（有损信息的视角目标）仍然是个挑战。在本工作，作者从对抗的角度研究了这个问题：面对很少的信息丢失，DNN的性能会受到影响吗？为此，作者提出了一种新颖的对抗攻击方法，AdvDrop，通过dropping现有的图像信息来修改对抗样本。以前，大多数对抗攻击将额外的干扰信息添加到干净图像上。与之前的工作相反，作者提出的工作以一种新的视角来探索DNN模型的对抗鲁棒性，通过丢弃不可感知的细节来修改对抗样本。作者通过大量的实验证明了AdvDrop的有效性，并且显示了这种对抗样本攻击很难被当前的防御系统防御。

![image-20211205193031873](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205193031873.png)

上图中作者的方法在局部区域丢弃了一些冗余的信息，但是不影响图像的视角效果也能使模型分类错误。

## 引言

IIyas等人[23]经验地证明对抗扰动是DNN的一种非鲁棒地特征。也就是说，对抗扰动是DNN的一种有意义的特征，但是没有含义并且对人类不可见。因此，**作者猜测是否可以使用一种相反的方式来生成对抗样本呢**？不同于在干净样本上的加性对抗扰动（或则非鲁棒特征），作者从干净图像中剔除某些不易于被人类发现的、但是对DNN分类关键的特征，从而导致DNN分类错误。

丢弃图像的信息可以是**空域（颜色量化）**或者**频域（JPEG压缩）**。作者考虑在频率域实现AdvDrop。在本文，作者考虑在频率域开发攻击方法。原则上，作案者可以drop图像的不同特征来生成对卡样本。早期研究作者聚焦在**频率域**，因为作者选择使用**图像细节**作为感兴趣的特征并且drop，这在频率域上可以很好的**量化**。该选择是优于人类对图像细节的敏感性较低。在本文，

![image-20211205204407777](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205204407777.png)

+ 首先使用AdvDrop将图像从空域转换到频域
+ 然后量化地减少一些变换后图像的频域信息。图2显示了AdvDrop的处理，由于PGD。

AdvDrop从移除细节信息（纹理）开始，保持结果图像与原图的不可区分性。当增加dropped 信息的数量，结果对抗图像最终会展现出“blank”，即纯色，不包含信息，对DNN识别特殊物体没有帮助。

![image-20211205204419284](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205204419284.png)

一些工作[21,39]提出在频域率生成对抗样本可以提高黑盒攻击的效率和迁移性。

## 方法

作者提出的AdvDrop方法由以下几个部分构成：

+ 对抗损失：作者提出的方法的目的是通过最小化Ladv优化量化表q。
+ 离散余弦变换DCT：DCT变换将输入图像从空域变换到频率域。
+ 逆离散余弦变换IDCT：IDCT将图像信息由频域变换回空域。
+ 量化：量化作为AdvDrop方法的核心处理过程，由量化表实现。作者采用了一种**可微分的量化操作**实现对量化表的优化。

总体流程图如下图所示

![image-20211205205357485](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205205357485.png)

最终的优化目标函数为

![image-20211205205506609](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205205506609.png)

作者将qinit设置成1（即初始值）。在优化的过程中逐渐增加量化表q的值。

### 对抗损失

![image-20211205205622087](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205205622087.png)

p是softmax输出的概率值。

### 变换

我们在这一部分介绍了 DCT 和 IDCT。 DCT 用作图像从空间域到频域的变换，它**根据在不同频率振荡的和余弦函数来表示数据点的有限序列**。 请注意，还有其他方法可以将图像从空间域转换为频域，例如离散傅立叶变换等。我们关注 DCT，因为它使建议的 AdvDrop 在选择要丢弃的信息和位置方面具有更大的灵活性。

在DCT前，作者首先将图像分割成NXN的块，N为8。**每个patch中像素之间的关系会随着patch的尺寸增大而减少**，导致较大失真的量化结果。对于每个block，像素的值被挑战至与0对称。DCT的数学定义为

![image-20211205210034507](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205210034507.png)

其中，u,vth是D(x)的元素。x[k,m]表示图像x在坐标(k,m)处的值。

### 量化

常见的完备量化-反量化过程可以定义成

![image-20211205210322510](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205210322510.png)

Δ表示间隔长度，它决定了 x 值的最近量化点，用作量化器。Δ越大，量化后量化的值得长度就越小。量化的值被约束在合理的范围内。

作者使用可量化的量化表q来量化变换到频率域的输入。量化表q的目的与Δ相同。**通过增大量化表的间隔来增加dropped的细节**。为准确调整量化表q，并且进一步提升提出攻击的成功率，作者采用梯度反传策略，利用目标模型的梯度将整个过程构造成一个优化问题。作者采用[19]提出的方法，将量化过程变成可微分

![image-20211205211041176](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211041176.png)

φ近似两个邻近量化值之间的变化、φ在任何地方都是连续可微分的，定义成

![image-20211205211139603](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211139603.png)

对于不同的φ和α，有

![image-20211205211204774](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211204774.png)

最终，量化表q的更新策略为

![image-20211205211222716](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211222716.png)

不同量化过程和更新量化表q的更新

![image-20211205211328264](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211328264.png)

## 实验验证

测试集：选择2000张正确分类的ImageNet来验证提出的模型。

指标：ASR，LPIPS

###  感知性研究

生成对抗样本的比较

![image-20211205211531342](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211531342.png)

LPIPS得分的比较

![image-20211205211553548](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211553548.png)

对抗攻击的比较

![image-20211205211607509](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211607509.png)

在不同对抗训练防御下的效果

![image-20211205211719385](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211719385.png)

CAM注意图的影响

![image-20211205211742699](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211742699.png)

drop不同频率域的信息的结果

![image-20211205211806723](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205211806723.png)