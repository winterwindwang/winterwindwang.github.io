---
layout: post
title: 【对抗样本（二十一）】【ICCV2021】Towards Understanding the Generative Capability of Adversarially Robust Classifiers
category: 文献阅读
keywords: adversarial attack; adversarial defense; generative capability of an adversarial robust model
tags: 对抗样本，对抗防御，利用鲁棒模型生成样本，能量函数
---



关键词：

一句话概述：

# 摘要

最近，一些工作发现了一个很有趣的现象，对抗鲁棒性分类器可以生成质量与生成模型相当的图片。作者从能量的角度调查了这个问题并提供了一种新颖的解释。作者用能量函数重构了对抗样本生成，对抗训练、和图像生成。作者发现**对抗性训练**有助于在**真实数据周围获得平坦且低能量的能量函数**，这是生成能力的关键。基于作者的新发现，进一步提出了更好的对抗训练模型，联合能量对抗训练（JEAT），它可以生成高质量图像并在各种攻击下取得sota的鲁棒性。由JEAT生成图像的Inception score为8.80，高于原始鲁棒分类器。特别地，作者发现JEAT的鲁棒性优于其他混合模型。

# 引言

一些研究发现了一个有趣的现象，即生成图像可以与GAN相当。分类任务中对抗鲁棒模型的生成能力非常有趣和令人惊讶。在本文，作者旨在**从能量角度**来理解**对抗训练分类器的生成能力**并且**进一步提升生成图像的质量**。对于基于能量的模型，它首先从随机噪声中生成低能量样本，然后通过更新模型的参数来提升生成样本的能量。使用这种方式，EBM可以获得好的能量函数，在真实数据附近能量很低。从而EBM可以利用Langevin Dynamics的好的能量函数生成好的图像。

作者发现对抗性训练的分类器也能获得好的能量函数，即在真实数据附近获得平坦且低能量的能量函数。对于分类器，作者**在输出的logit上定义了能量函数**。作者给出的解释如下图所示

![image-20211205184403505](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205184403505.png)

作者还发现向训练数据中注入噪声可以生成高能量的样本，但在扰动方向可能不如对抗攻击有效。因此，在训练过程中需要更大的噪声来生成高能量的样本。

贡献：

+ 从能量角度提出对鲁棒分类器的图像生成能力的新颖解释
+ 作者发现在训练过程中向正常分类器注入噪声后的生成能力，并从能量的角度进行了解释
+ 提出了从能量的角度的训练算法JEAT，并提升了图像的生成能力。

# 方法

### 鲁棒模型的图像生成

Madry等人证明了仅使用鲁棒分类器足以解决如生成的各种图像合成任务。对于给定的标签y，图像生成最小化损失标签y的损失L由以下公式决定：

![image-20211205184921458](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205184921458.png)

从样本x0出发（采样自均值为μ，方差为Σ的正态分布），通过最小化损失可以得到好的图像。使用一些如选择更多样的分布来初始化有助于进一步提升生成图像的质量。

### 基于能量的模型

对于x的任意概率密度可以表示成

![image-20211205185218366](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205185218366.png)

基于能量模型的优化通过最小化来最大似然学习

![image-20211205185356478](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205185356478.png)

对于EBM而言，pθ是不可得的。因此EBM的学习分成两个阶段

+ 使用Langevin动态与能量下降的方法一致从pθ中近视生成样本
+ 使用SGD优化模型参数来增加这些样本的能量，并且降低真实样本的能力。
+ ![image-20211205185628780](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205185628780.png)

![image-20211205185240230](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205185240230.png)

图2a，对于正常分类器而言，中心的能量函数非常尖锐，真实数据的能量很高。

对于鲁棒性分类器，靠近中心的能量函数更加平滑和低，如图2b所示。

作者提出的算法图下图所示

![image-20211205185936841](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205185936841.png)

# 实验

作者使用IS和FID来衡量生成图像的质量

![image-20211205190026597](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205190026597.png)

+ 作者比较了不同对抗训练方法生成的图像的质量

![image-20211205190052681](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205190052681.png)

+ 在CIFAR-10上针对不同混合模型构造对抗样本的鲁棒性

![image-20211205190127716](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205190127716.png)

# 结论

作者提出了一种新颖的攻击和防御方法，但是我觉得这个方向显然不具备继续研究的可能性。方法比较新颖，但是为什么要用鲁棒模型来生成图片呢？而且这个图片还是在原图的基础上生成的。

实际上使用作者的方法生成的样本质量并不好。