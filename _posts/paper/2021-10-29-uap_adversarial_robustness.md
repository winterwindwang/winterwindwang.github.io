---
layout: post
title: 【文献调研】通用对抗攻击的对抗防御方法
category: 文献阅读
keywords: 通用对抗样本攻击，对抗防御
tags: 通用对抗样本防御
---

## 通用对抗样本攻击防御方法

| 方法                                 |      |
| ------------------------------------ | ---- |
| [UAT][1]【AAAI2020】                 |      |
| [雅可比正则防御方法][2]【ICANN2021】 |      |
| [3][]【ICCV2019】                    |      |



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

## 【ICCV2019】Defending against universal perturbations with shared adversarial training

关键词： 通用对抗扰动，对抗训练，对抗训练的拓展，研究了通用对抗扰动的鲁棒性和未扰动数据上的性能，Pareto front

---

作者显示了对抗训练在预防通用对抗扰动时更有效。并且研究了通用对抗扰动的鲁棒性和未扰动数据上的性能的平衡，并提出了一种对抗训练（**shared adversarial training，共享对抗训练**）的拓展方法优雅地解决了这个trade-off。在图像分类和语义分割上地结果显示了欺骗经过对抗训练强化的模型变得清晰可辨并显示目标场景的模式。

![image-20211031160655800](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211031160655800.png)

从上图可以看出，在作者提出的共享对抗训练策略训练的鲁棒模型上生成通用对抗扰动时，扰动的效果很差。

### 引言

现有关于通用对抗样本攻击防御的工作：

对**通用对抗样本的分析**：

+ Moosavi-Dezfooli[31]等人连接通用对抗样本鲁棒性和决策边界的几何性，证明了微小对抗扰动的存在，如果决策边界系统地正弯曲，则存在小的普遍扰动。
+ Jetleyp[18]等人上者的基础上提供了分类器易受到普遍扰动影响的方向与对未扰动数据正确预测很重要的方向一致。他们认为预测能力和对抗性脆弱性密切相关

**通用对抗样本防御**：

+ **现有方法以及问题**：
  + 对某个模型的通用扰动定义一个分布（近似最优），可以是预先计算和随机采样[29]，学习一个生成模型[16]，或者从训练时不同epoch保存的模型上收集一系列的通用对抗扰动，扰动fine-tune模型参数，但是这种方法在鲁棒性的提升是有限的。这很可能是因为模型在优化过程中对通用扰动的固定分布过拟合。然而，在每个mini-batch重新计算通用对抗扰动是不现实的。
  + 另一种防御方法是通过向模型添加额外的组件：如训练一个判别输入是否是对抗扰动的模型。然而这种方法使整个模型变大，需要更高的代价。
+ **解决方法**：在本文，作者提出了一种通过计算每个最小批的**共享扰动**，并将它们运用到对抗训练中。（**注：共享扰动是如何实现的？与普通的对抗训练方式有何区别？**）

在分割任务中，Arnab[2]发现**残差连接和多尺度处理会增加结构的鲁棒性**，然而**密集条件随机场的平均场推理仅屏蔽梯度，但不会增加自身的鲁棒性**。与这些方法相比，作者聚焦在修改训练过程（**思考：训练代价会不会提高？**）来提高 鲁棒性。这两种方法在未来可以组合。

### 方法

#### 符号和对抗攻击

首先定义了风险，即$risk ~ p(\theta)$，考虑以下三种风险[47]:

![image-20211031164603343](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211031164603343.png)

![image-20211031164610964](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211031164610964.png)

其中$\xi$表示通用对抗扰动。S表示扰动采样空间。注意，对抗和通用风险不一致，$\xi(x)$依赖从数据分布D中特定的x，而后者需要在泛化到整个数据分布上。

为生产对抗样本，采用了PGD，因为它可以在计算效率和攻击效果上有一个tradeoff。通用对抗样本上形式类型。

#### 共享对抗训练

对抗训练在通用风险上优化了一个宽松的上界，并且激发了共享对抗训练，其目的最大化抵抗通用扰动的鲁棒性。其目标函数可以定义成以下形式

![image-20211031165747292](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211031165747292.png)

其中σ平衡了鲁棒性和干净样本的性能。具体细节看section4.1.
根据上述推理，可知最好在对抗训练中使用 ρuni 的上限，它比 ρadv 更严格，但比 ρuni 更便宜。为此，作者提出**heap adversary， $f_{heap}$**（PGD的替换形式）。

**具体做法**：作者将包含d个样本的最小批分割成大小为s 的d/s个堆（最小批的子集）。与使用$f_{adv}$在d个数据点上计算一个扰动不同，作者使用$f_{heap}$计算d/s个共享扰动。于是，通过repeat每个共享扰动s次就将这些扰动被广播到所有d个数据中。s 越小，对相应堆的共享扰动就越“过拟合”。整个过程如下图所示

![image-20211031170813222](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211031170813222.png)

使用交叉熵损失会存在，在一些数据点上具有过高的误分类置信度，和在一些数据点上正确分类。（与错误分类更多数据点但置信度较低相比，这会产生更高的成本）。为了解决该问题，作者采用了clip版本的交叉熵算![image-20211031171409354](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211031171409354.png)

k=-log0.2，对应于不鼓励对手将正确类别的置信度降低到 0.2 以下。相同的做法可以在[42]中看到。



## 参考文献

[1]: Shafahi, Ali, et al. "Universal adversarial training." *Proceedings of the AAAI Conference on Artificial Intelligence*. Vol. 34. No. 04. 2020.

[2]:CoK.T.,RegoD.M.,LupuE.C(2021) Jacobian Regularization for Mitigating Universal Adversarial Perturbations.In:FarkašI.,MasulliP.,OtteS.,WermterS (eds)ArtificialNeuralNetworksandMachineLearning ICANN2021.ICANN2021.LectureNotesinComputerScience,vol12894.Springer,Cham.https://doi.org/10.1007/978-3-030-86380-7_17

[3]: Mummadi, Chaithanya Kumar, Thomas Brox, and Jan Hendrik Metzen. "Defending against universal perturbations with shared adversarial training." *Proceedings of the IEEE/CVF International Conference on Computer Vision*. 2019.
