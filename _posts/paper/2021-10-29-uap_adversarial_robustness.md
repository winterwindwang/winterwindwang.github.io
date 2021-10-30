---
layout: post
title: 通用对抗攻击的对抗防御方法--survey
category: 文献阅读
keywords: 通用对抗样本攻击，对抗防御
tags: 通用对抗样本防御
---

## 通用对抗样本攻击防御方法

| 方法                                 |      |
| ------------------------------------ | ---- |
| [UAT][1]【AAAI2020】                 |      |
| [雅可比正则防御方法][2]【ICANN2021】 |      |



## 【AAAI2020】Universal Adversarial Training

#### 摘要

我们研究了通用对抗扰动的生成效率，和强化网络低于这些攻击的有效方法。我们提出了简单的基于优化通用攻击的方法，将ImageNet上不同网络结构的准确率降低了20%，但是比标准方法快了13倍。为了防御这些扰动，我们提出**通用对抗训练**，将鲁棒分类器构建任务建模成一个双方最小最大化博弈，生成的鲁棒性模型的代价仅是自然训练的2倍。我们还提出了一个同时随机梯度方法，不需要额外的计算，这允许我们在ImageNet上进行通用攻击训练。

#### 现有对抗训练的缺陷

在训练时对每个batch生成对抗样本，然后注入到训练样本中的对抗训练方式可以增加模型对微小扰动的鲁棒性。但是对于像通用对抗扰动的大的扰动却无效。昂贵的传统对抗训练使得其无法用于复杂和大规模数据集上。为此，作者将对抗训练问题构建成一个最小最大化优化，其中在网络参数上进行最小化，在通用对抗扰动上最大化。使用没有内层循环的交替的随机梯度方法，使其比更加强大的per-instance对抗训练更加有效。

Parolat 2018认为由于从头开始训练通用对抗扰动需要内层优化，因此是在通用对抗扰动上的对抗训练是不可行的。Shafahi等人首次介绍了与标注训练一样的对抗训练策略创建一系列“自由”对抗训练策略。

#### 通用对抗扰动生成方法

传统的UAP方法不一定能找到通用对抗扰动。而使用如下的损失函数也存在潜在的缺陷。交叉熵损失从上面看是无界的，在单个图像上评估时可以任意大。最糟糕的情况，仅仅一张图片中的扰动也能通过迫使平均损失趋于无穷来最大化公式2

![image-20211030004739417](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211030004739417.png)

为了迫使优化器找到可以欺骗多个样本的扰动，我们提出了**交叉熵的裁剪版本**

![image-20211030010111668](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211030010111668.png)

我们将损失函数限制在 β 处，以防止任何单个图像主导（2）中的目标，并为我们提供更好的错误分类精度替代指标。然后使用随机梯度方法对公示2进行优化。

#### 实验结果

**对抗攻击**

![image-20211030105650373](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211030105650373.png)

可以看到与DeepFool方法相比，我们设计的对抗扰动可以使模型的准确率下降的更多，而且效率也更高。

**对抗防御**

![image-20211030105559353](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211030105559353.png)

可以看到，使用不同的通用对抗扰动重新对网络进行对抗训练的后分布对通用扰动和自然样本的分类准确率。可以看到经过我们方法重新对抗训练后的模型的准确率可以维持在一个较高的准确率。

## 【ICANN2021】Jacobian Regularization for Mitigating Universal Adversarial Perturbations

### 摘要

我们**根据数据相关的雅可比矩阵推导出UAP有效性的上限**。我们经验地验证了**雅可比正则**在保持干净样本性能的同时，模型对UAP攻击的鲁棒性提升了4倍。我们的理论分析构建了输入对之间共享对抗性扰动的强度制定度量。我们将该指标用于基准数据集，显示了它与实际观测到的鲁棒性高度相关。这表明可以在不牺牲干净样本准确率的情况下，可靠地减轻现实和实用的普遍攻击，这表明机器学习系统的鲁棒性很有希望。

### 简要介绍

作者首先将对抗样本的形式进行泰勒展开

$f(x+\delta)=f(x)+J_f(x)\delta+O(\delta^2)$

其中$J_f(x)$表示$f$在x处的输入和输出。当x足够小时，上述式子可以表示成

$f(x+\delta)\approx f(x)+J_f(x)\delta$

对于任意的q范数，有

$||f(x+\delta) - f(x) ||_q\approx ||J_f(x)\delta||_q$

然后作者通过奇异值分解证明了叠加起来的雅可比矩阵的上界。最后，作者通过计算两个不同输出的雅可比值得余弦相似度，该比率使我们能够衡量两个输入共享对抗性扰动的强度。

![image-20211029220614488](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211029220614488.png)

雅可比正则化可以表示成

![image-20211029220644211](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211029220644211.png)

## 参考文献

[1]: 

[2]: CoK.T.,RegoD.M.,LupuE.C.(2021)JacobianRegularizationforMitigatingUniversalAdversarialPerturbations.In:FarkašI.,MasulliP.,OtteS.,WermterS.(eds)ArtificialNeuralNetworksandMachineLearning–ICANN2021.ICANN2021.LectureNotesinComputerScience,vol12894.Springer,Cham.https://doi.org/10.1007/978-3-030-86380-7_17