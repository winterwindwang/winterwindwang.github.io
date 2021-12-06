---
layout: post
title: 【对抗样本（二十）】【ICCV2021】Meta-Attack Class-Agnostic and Model-Agnostic Physical Adversarial Attack
category: 文献阅读
keywords: meta attack, physical attack
tags: 物理攻击
---

关键词：class-agnostic, model-agnostic, physical adversarial attack。分阶段对抗攻击，第一阶段生成鲁棒的物理对抗样本；第二阶段，优化其迁移性。

一句话概述：**类别不可知**和**模型不可知**的物理对抗攻击方法，可以生成鲁棒的物理对抗样本，其泛化性也较好。

作者发现**VGG的ASR要比ResNet好，这意味着ResNet比VGG更加鲁棒**。

# 摘要

现有方法大都集中于修改**数字空间**中的对抗样本。然而由于很多无法控制的物理动态，**物理空间**中生成有效的对抗样本更具有挑战性。大多数当前的物理攻击方法旨在通过模拟所有可能的**物理动力学（physical dynamic）**来生成鲁棒的物理对抗样本。当攻击新图片或新模型时，需要花费**大量的时间来模拟物理动力**学并且需要为**每张图像进行迭代优化**。为解决这些问题，作者提出类别不可知和模型不可知物理对抗攻击模型（Meta Attack），其不仅有能力通过**模拟颜色和形状失真**来生成鲁棒物理对抗样本，也能通过利用新的数字和物理图像来攻击新方法和新模型。作者声称他们是首位利用少样本学习问题来构造物理攻击。在此，训练任务被定义成支撑集、查询集、和目标DNN模型。在少量样本的设置下，作者设计了一种新颖的类别不可知和模型不可知的meta学习算法来增强作者方法的泛化性。在两个benchmark数据集上、四种具有挑战实验设置的大量的实验结果表明，作者的方法比现有的方法具有更优异的鲁棒性和泛化性。

# 引言

作者的目的是构建物理攻击。作者指出当前的物理攻击的研究较少，并且他们的假设是不符合实际的。物理攻击更符合真实世界并且会对实际应用引发安全问题，例如自动驾驶或面部识别。

物理攻击由于（不同距离和视角，设备打印等特点）比数字攻击更难。物理攻击的多个步骤

1.  给定被攻击的图像和特定的目标DNN模型，攻击者在数字空间构造对抗样本。
2. 攻击者打印数字对抗样本（2D图片或3D物体）
3. 使用相机或者扫描仪拍摄打印的对抗物体。然后拍摄的图片（物理对抗图像）被喂入DNN模型。

上述的打印和拍摄过程被称为**digital-to-physical (D2P)**转换[23]。由于**打印设备**和**拍摄设备的特征**，D2P会引起**严重的颜色和形状失真**。这些特征使得物理对抗样本与数字对抗样本不一致。因此由于D2P转换，对抗图像可能会在物理世界变得无效。

![image-20211205155438190](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205155438190.png)

图1 显示了在生成对抗样本期间 模拟可能失真的基本思路。

但是这个过程中需要采集大量的物理图片，这个工作量是不能被接受的。并且物理世界**有大量的图片和不同的DNN模型**。现有的方法无法应用到这个复杂的情况下，由于需要大量的人工努力来实现**模拟物理动力学**和**大量的优化时间**。

为解决上述的问题，很直接的想法就是将构建**物理攻击问题**构建成**少样本学习问题**。作者希望通过使用**少量的物理样本来生成鲁棒的物理对抗样本**。为此，作者借助meta 学习算法来实现该目的。

作者希望Meta attack不仅有较好的鲁棒性，也有较好的泛化性。因此作者设计了类别不可知和模型不可知的元学习算法。

+ 作者通过模拟由D2P造成的颜色和形状失真，来生成鲁棒的物理对抗样本。使用CYCLEGAN来模拟打印机的图案颜色的失真。使用EOT算法来模拟拍摄造成的形状失真。
+ 类别不可知和模型不可知被设计使用少量收集的物理图像来增强跨攻击图像和跨模型的泛化能力。

# 方法

作者提出一种类别不可知和模型不可知的物理对抗攻击方法，Meta-Attack，不仅能够生成物理对抗样本，而且具有较好的鲁棒性。为实现该目标，作者设计了一个生成攻击模型与类别不可知和模型不可知的元学习（CMML）算法：

+ **生成攻击模型**旨在生成鲁棒的物理对抗样本，通过从D2P（digital to physical）转换模拟颜色和形状失真。具体地，作者利用**CycleGAN**来模拟由打印机引起的颜色图案**颜色失真**。此外，采用**EOT**算法来捕获其**形状失真**。然后CycleGAN和EOT的损失组合在一起来构建生成攻击模型。
+ **类别不可知**和**模型不可知**的**元学习(CMML)**被设计来增强生成攻击模型生成对抗样本在不同图像和DNN模型上的泛化性，作者利用少量收集的物理图像进行优化。与少样本学习不同，作者设计的方法包括支撑集、查询集和目标攻击模型。

在元学习阶段，通过最小化在多种攻击任务上地查询集的验证损失，生成攻击模型被迭代地进行更新以获得更好地模型初始化。

元学习训练一个base生成模型，然后用于其他任务的初始化。

作者提出的框架如下图所示

![image-20211205161223945](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205161223945.png)

### Meta Attack

#### 生成攻击模型

一个成功的和鲁棒的物理对抗样本应该满足以下三个约束：1）成功地数字攻击；2）对1:1的D2P转换鲁棒；3）对空域变换鲁棒。

1）构建成功的数字攻击

![image-20211205162504016](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205162504016.png)

其中Ladv是目标攻击的交叉熵损失。

2）对1比1的D2P变换的鲁棒性

G可以看作是从数字领域Xd到物理领域Xp的领域转换，其公式为

![image-20211205162756736](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205162756736.png)

D是判别器，作者将GAN的Loss简单地表示车间给你Lx-GAN，注意可以使用任意的GAN模型。

3）对空域比变换鲁棒

受EOT算法的启发，作者在生成对抗样本过程中引入集中空域变换来建模不同距离和视角。从而旨在提升基于一系列合成变换图像对抗样本的鲁棒性。从而，得到以下的优化问题

![image-20211205163101122](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205163101122.png)

T表示被选中的变换函数t。实际上，**T可以建模形状失真**，如random rotation, translation, resize, affine。

综上，总的优化目标函数如下

![image-20211205163242589](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205163242589.png)

优化问题变成了

![image-20211205163259819](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205163259819.png)

### 类别不可知和模型不可知的Meta学习

为使模型不可知的元学习适应于物理对抗攻击的场景，作者设计了一系列新的训练任务τ。作者将攻击任务τ包含支撑集Ds，查询集Dq，目标模型f，Ds和Dq均由数字图像和相应的物理图像构成。

正如图2中所示，作者的CMML算法由两个阶段构成：元生成攻击模型的元训练阶段以及对位置攻击任务的微调阶段。

在meta-training阶段（第一阶段），作者的CMML算法将$\{\tau\}^N_{i=1}=\{D^{\tau_i}_s, D^{\tau_i}_q,f_i\}^N_{i=1}\}$作为输入，并且通过最小化多任务攻击任务上查询集上的验证损失是得到meta生成攻击模型（i.e, Gmeta, Dmeta）

在微调阶段，给定新的攻击任务，meta生成攻击模型在少量样本上进行微调，这避免了大量数字和物理对抗样本的需要。

在微调过后，特定任务的生成攻击模型在查询集Dq上进行验证，Dq仅包含少量的数字图像。这样就能提高模型的泛化性和鲁棒性。

### 训练和推理

元学习的训练（参数的更新）

![image-20211205164633892](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205164633892.png)

其中α是学习率。查询集Dq用于验证更新参数的有效性，元学习的目标函数定义成

![image-20211205164843368](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205164843368.png)

生成器模型参数的优化

![image-20211205164903990](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205164903990.png)

在测试阶段，对于未见过的攻击任务，作者迭代地进行微调。

算法如下图所示

![image-20211205165230924](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205165230924.png)

# 实验

作者选择VGG16作为目标模型，其他实现细节参考论文。使用ASR作为衡量指标。测试集的规模为120

## 实验结果和分析

###  1、attacking a seen model on images of a seen class

![image-20211205165852015](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205165852015.png)

![image-20211205165902456](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205165902456.png)

作者的方法在物理实验下更加鲁棒

### 2、attacking an seen model on images of a unseen class

![image-20211205165933670](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205165933670.png)

作者的方法很容易在unseen攻击中获得较高的成功率。图4展示了空域变换对对卡样本有一些影响。

### 3、attacking an unseen model on images of a seen class

![image-20211205170048695](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205170048695.png)

展示了作者的方法具有较好的泛化特征以及生成对抗样本对空域变换具有较好的鲁棒性。

### 4、attacking an unseen model on images of an unseen class

![image-20211205170224701](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205170224701.png)

作者发现VGG的总体攻击成功率较ResNet的高，这意味着**ResNet比VGG更加鲁棒**。

### 消融实验

![image-20211205170341689](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211205170341689.png)

表示了经过预训练meta生成对抗攻击的微调的模型具有较好的性能。

# 总结

方法确实可以提升模型的鲁棒性，但是针对不同的新任务和新方法都需要重新微调。尽管需要的样本比较少，但是还是需要训练。这样看的话，迁移性也不太高。

此外，虽然是物理攻击，但实际上也只是希望消除打印和拍摄时变换的失真。并未真正的考虑过现实攻击，现实攻击使用的应该是扰动，而不是对抗样本。
