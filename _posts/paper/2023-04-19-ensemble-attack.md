---
layout: post
title: A briefly survey on ensemble adversarial attack
category: 文献阅读
keyworks: model ensemble, adversarial attack and defense
tags: 对抗样本，对抗攻击
---

# 集成攻击方法调研

> A briefly survey on ensemble adversarial attack

对抗样本的迁移性是指针对模型A生成的对抗样本也能攻击模型B。可见对抗样本迁移性的定义可以视为在不同模型上均能攻击成功，提升对抗样本迁移性的目的是针对不同模型均有能攻击成功。由此可见，集成对抗攻击算法是提升对抗样本迁移性的一种很直接的方法。为了了解这方面的研究，故在此对现有基于集成攻击算法的方法做一个简单的回顾。注意，我们只关注与集成攻击算法相关的部分，忽略了研究种其他方面的贡献。

## 【ICLR2017】Delving into transferable adversarial examples and black-box attacks

+ 模型集成策略

$$\arg \min_{x^*}-\log((\sum_{i=1}^{K}\alpha_iJ_i(x^*))\cdot\textbf{1}_{y^*})+\lambda d(x,x^*)$$

其中，$J$表示模型的softmax输出；$\sum_{i=1}^{K}\alpha_i=1$。d是距离度量指标，表示为范数，无穷范数或2范数。

+ 实验设置

  + 优化器：Adam，100次迭代，学习率为8。用Adam来优化单个对抗样本。
  + 数据集：ImageNet validation

+ 实验结果

  + 目标攻击

  ![image-20230420153721457](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420153721457.png)

  + 非目标攻击

  ![image-20230420153725631](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420153725631.png)

+ 实验结论

  + 目标攻击：并非所有的目标对抗样本均会被其他模型识别成目标类别。其中对ResNet50模型的迁移性好于VGG16或GoogleNet。
  + 非目标攻击：性能普遍比目标攻击更好

+ 相关结论

  + The gradient directions of different source models are orthogonal to each other when conducting ensemble adversarial attacks, the gradient magnitude plays a vital role in the final search direction of adversarial examples.

## 【CVPR2018】Boosting Adversarial Attacks with Momentum

+ 模型集成策略

  + Ensemble in logits
  + Ensemble in predictions
  + Ensemble in loss

+ 实验设置

  + 扰动强度$$\epsilon$$：16/255
  + 数据集：NIPS2017

+ 实验结果

  ![image-20230420154006904](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420154006904.png)

+ 相关结论
  + Logit集成取得了最好的结果

## 【AAAI2020】Learning transferable adversarial examples via ghost networks

+ 动机
  + 在实际任务中，很难获得多样的模型，这限制了模型集成方法的性能。
  + Dropout提供了一种近视不同网络结构的有效方法，在模型训练过程中可以有效地的防止过拟合。
+ 模型集成策略
  + 针对特定模型，在每个网络层后都加上dropou层，通过控制不同的保留概率p，来生成多样参数的模型

![image-20230420155836621](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420155836621.png)

![image-20230420155844728](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420155844728.png)

+ 针对标准集成方法存在的计算量大的问题，提出了一种Longitudinal集成方法（串行集成），也就是一次只用一个模型。

![image-20230420160114143](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420160114143.png)

+ 模型多样化策略分析

  + dropout概率与模型准确率之间的对比结果

  ![image-20230420160147263](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420160147263.png)

  上图显示了不同模型对dropout的概率不一样，因此要对不同模型分别设置。

  + 不同模型之间像关系的对比

![image-20230420160232298](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420160232298.png)

采用JSD指标，对模型输出logit计算相关性

![image-20230420160647764](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420160647764.png)

+ 实验结果

![image-20230420160701306](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230420160701306.png)



## 【AAAI2020】A new ensemble adversarial attack powered by long-term gradient memories

+ 动机

  + 尽管有各种各样的神经网络，但是这些模型之间显示出相似的决策边界。因此，若对抗样本可欺骗几个源模型，那么它就能潜在地获得内在迁移性信息，从而获得较好的对抗迁移性。
  + 受限于现有有限的源模型，迁移性的提升有限。

+ Quotes

  + [MIFGSM] crafting an adversarial examples is analogous to training a model, and the transferability of the adversarial example is analogous to the generalizability of the model
  + [ICLR2017] Compared to the magnitude of the perturbation, the spatial structure of the adversarial perturbation, the spatial structure of the adversarial perturbation has stronger impact on the final fooling ability.

+ 模型集成思路：将提升对抗样本迁移性的任务看作是训练一个新的模型。具体来说，就是把模型视为batch，对抗样本在不同的batch都要有效

+ 模型集成策略：

  + SMBEA：将模型训练的pipeline引入对抗样本训练过程中。此外为了进一步提升迁移性，提出了一下三种特征集成策略

  ![image-20230423160725741](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423160725741.png)

  即从不同维度对特征进行融合

  + Intra-batch update rules
  + Inter-batch update rules： Adam优化器对历史信息进行利用

由于用于Image-to-Image任务，实验结果省略。

## 【Signal Processing: Image Communication】Revisiting ensemble adversarial attack

+ 动机

  + 以往利用集成模型方法只是简单的融合模型输出，未考虑模型梯度之间的差异，即大梯度模型会带来较大的预测偏差，从而陷入局部最优

  ![image-20230423161105610](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423161105610.png)

+ 集成策略

  + MABE：在集成以前对不同模型的梯度进行归一化，缩小不同模型梯度之间的差异

    ![image-20230423161754843](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423161754843.png)

  ![image-20230423161853146](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423161853146.png)

  + G2BASE：对模型进行分组，然后对每个组进行融合

    ![image-20230423161801354](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423161801354.png)

+ 实验结果

![image-20230423161940919](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423161940919.png)

## 【CVPR2022】Stochastic variance reduced ensemble adversarial attack for boosting the adversarial transferability

+ 动机

  + 基于集成的攻击是最有效、使用最广泛的黑盒攻击方法。
  + 由于模型结构之间的内在差异，模型的优化路径差异可能很大，这意味着模型之间的梯度方法可能存在很大的偏差。这样的偏差可能会导致集成攻击方法不那么准确。

  ![image-20230423162612880](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423162612880.png)

上图是给定图片在不同模型上梯度的cos相似度，值越靠近0，相似度越低（cos 90=0）

+ 集成思路：为此，作者提出了在集成过程中，对模型偏差进行调整的算法。具体算法如下图所示。

![image-20230423162722910](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423162722910.png)

+ 实验结果
  + 传统集成攻击方法与所提集成攻击方法
  + 

![image-20230423162738564](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423162738564.png)

+  黑盒攻击结果

![image-20230423162800160](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230423162800160.png)

