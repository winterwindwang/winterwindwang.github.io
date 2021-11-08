---
layout: post
title: 通用对抗样本攻击--调研
category: 文献阅读
keywords: uap， adversarial attack, adversarial example
tags:  universal adversarial perturbation
---



# 通用对抗样本

衡量指标：

- fooling rate:  表示加入扰动后，预测标签发生了变化。[uap,nag]
  - Fooling rate (FR) presents the ratio of images whose predict label become incorrect by adding the UAP. [NAG]
  - fooling rate, which is the proportion of inputs that are misclassified by the
    model when the UAP is applied.[SGD-UAP]
  - the fooling ratio, that is the proportion of images that change labels when perturbed by our universal perturbation.[UAP]
- attack succes rate: 在加入扰动后，原来分类正确的样本被错误分类
  - It is the percentage of data samples (x) for which the target CNN predicts a different label upon adding the UAP (v).【AAA】更像是fooling rate 的定义，在论文表示写的是FR？

数据是否有关：

- 数据依赖：effective in fooling[AAA].
- 数据独立：quickly craft generalizable UAP[AAA].

------

| 方法                                           |                      |          | 方法                 | $\xi$                    | 评估方式                                | 代码                                                         |
| ---------------------------------------------- | -------------------- | -------- | -------------------- | ------------------------ | --------------------------------------- | ------------------------------------------------------------ |
| [UAP][1]【CVPR2017】                           | 数据依赖             |          | 边界最小距离（优化） | lp范数10<br />l2范数2000 | fooling rate                            | [官方](https://github.com/LTS4/universal)<br />[torch版本1](https://github.com/ferjad/Universal_Adversarial_Perturbation_pytorch)<br />[torch版本2]() |
| [FFF][2]【BMVC2017】                           | 数据无关             |          |                      |                          |                                         | [官方](https://github.com/val-iisc/fast-feature-fool)        |
| [AAA][3]【ECCV2018】                           | 数据无关             |          | 生成模型             | 10                       |                                         | [官方](https://github.com/val-iisc/aaa)                      |
| [NAG][4]【CVPR2018】                           | 数据相关             |          | 生成模型             |                          |                                         | [官方](https://github.com/val-iisc/nag)                      |
| [GAP][5]【CVPR2018】                           | 数据无关<br />/依赖  |          | 生成模型             | lp范数-10                | fooling rate                            | [官方](https://github.com/OmidPoursaeed/Generative_Adversarial_Perturbations) |
| [GD-UAP][6]（会议论<br />文扩展）【TPAMI2018】 | 数据无关<br />/依赖  | 任务无关 | 神经网络中间层       |                          | fooling rate                            | [官方](https://github.com/val-iisc/GD-UAP)<br />[torch版本](https://github.com/psandovalsegura/pytorch-gd-uap) |
| [PD-UAP][7]【ICCV2019】                        | 数据独立             |          |                      | 10                       | fooling rate                            | [官方](https://github.com/LynnHongLiu/PDUA)                  |
| [SV-UAP][8]【CVPR2018】                        | 数据依赖（少量数据） |          |                      |                          |                                         | [torch版本1](https://github.com/slayff/art_of_vectors_pytorch)<br />[torch版本2](https://github.com/AndrewAtanov/nla-project/blob/master/method.py) |
| [UPGD-UAP](10)【ICIP2020】                     | 数据依赖             |          | 投影梯度优化         |                          |                                         |                                                              |
| [SGD-UAP][9]【ICIP2021】                       | 数据依赖             |          | 梯度优化             |                          | fooling rate<br />targeted fooling rate | [官方](https://github.com/kenny-co/sgd-uap-torch)            |

注释：

+ [这个版本](https://github.com/BardOfCodes/universal_pytorch)提供了UAP，FFF，DeepFool三种方法的实现
+ $\xi$一般是指无穷范数约束下的值



---

## 【CVPR2017】Universal adversarial perturbations

经过预训练的模型后仍然容易受到通用对抗扰动的攻击

## 【BMVC2017】 Fast feature fool: A data independent
approach to universal adversarial perturbations



## 【ECCV2018】Ask, acquire, and attack: Data-free uap generation using class impressions

由于数据依赖UAP可以获得更高的FR，但是在数据独立的情况下如何得到数据呢？作者给出的答案是利用模型本身来采样类别印象样本，从而得到训练数据（称为**代理数据**）。然后训练一个前向网络，更快的构建UAP。所以作者生成UAP也分成两个步骤：获取训练样本和训练生成模型。

#### 疑问

1. 如何从已知训练好的模型中重建类别印象图片？

采样优化，从潜在数据分布中采样而来的表示。在为每一类别采样得到类别印象图片后，训练一个生成模型。

视觉可视化：（1）每个神经元对输入图案的反应；（2）对神经网络有直观的感受，可以缓解神经网络的黑盒性质。

+ 优化随机输入来最大化网络结构中被选择神经元的激活函数。这生成了在输入空间中，给定神经元的可视化。**也就是指定输出类别，输入随机噪声，通过优化这些噪声来使得这些噪声被分类为指定的类别**。
+ Deep Dream: 与选择神经元来激活不同，任意自然图像作为输入传入网络，并且网络增强了检测到的激活。这种方式找到了输入中轻微的图案，并且放大了他们。

[26,20]认为通过减少其他类的 pre-softmax logits，直接在 softmax 上优化会导致类概率增加。从而不会增加想要类别的pre-softmax的值，所以会导致糟糕的可视化。因此作者选择优化pre-softmax logits。

### 方法

作者提出方法的总体结构如下：

![image-20211026172552908](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211026172552908.png)

#### 步骤一

作者的目的是选择类别c，优化图像印象$CI_c$，使得$CI_c$具有很高的置信度分类成类别c。具体地，考虑了pre-softmax层(也就是使用softmax的前一层)来最大化模型的置信度

![image-20211026171713405](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211026171713405.png)

为了使生成鲁棒的$CI_c$，使用了如下的数据增强技术

- [-5,5]之间的随机旋转
- {0.95, 0.975, 1.0, 1.025}随机选择的缩放因子
- RGB jittering
- 随机裁剪
- [-10,10]之间的随即均匀噪声。

优化停止策略：从[0.55,0.99]中随机采样置信度值。当采样的$CI_c$置信度超过阈值时，停止优化。

采样数量：1000个类别，每个类别10张图片。

![image-20211026172405249](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211026172405249.png)

#### 步骤二：craft the data-free perturbations

**fooling loss**

输入z：[-1,1]之间的均匀分布，10维（同样尝试了不同维度，10维最有效）。

在潜在数据分类上训练生成器来欺骗目标分类器。目的是让模型在干净样本和对抗样本上的预测不一样。由于softmax的非线性，在其他标签上的预测置信度会增加，并最终导致样本标签的翻转。损失函数设计如下

![image-20211026173153377](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211026173153377.png)

c是干净样本标签，$f_c^{s/m}$是预测成类别c的概率（softmax output）。

**diversity loss**

为避免生成器生成单个很强的UAP（这里比较奇怪，UAP不就是要很强吗？），为生成多样性的UAP，最大化每个最小批内不同对的嵌入距离，实现方式如下：

![image-20211026173532811](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211026173532811.png)

l指的是第l层，K是最小批大小32，d是距离指标（欧拉或余弦距离），计算**一对（这一对怎么取？一个batch大于2，是不是两辆之间都的计算？==>每次只选择一对，然后计算在最小批上的所有结果的sum）**对抗样本之间的提取的特征（**提取目标CNN的softmax层输出作为embedding，余弦距离**）。将f(x+vi)和f(x+vj)推开会使得UAP更加多样化。

### 实验



#### 思考

他们是通过模型来采样类别印象样本，使得每个噪声图像在分类成某个特定的类别时具有很高的置信度。那么是否可以探究模型难以分类的这些样本，找到这些样本特征空间的特点（有点像北航的checkout）。



## 【CVPR2018】Generative Adversarial Perturbations



显示出可以用于分割网络中。但是需要专门为分割网络设计通用扰动。



---

## 【TPAMI2018】Generalizable data-free objective for crafting universal adversarial perturbations

关键词：通用对抗扰动，非数据依赖，中间层特征

---

### 摘要

现在证明也存在输入不可知的扰动，称为通用对抗扰动，可以改变目标网络在绝大多数数据样本上的推断。然而，现有的方法修改通用对抗扰动存在几个问题：1）任务相关；2）需要从训练数据分布采样数据。3）需要进行复杂的优化。此外，由于数据依赖，构建的扰动的攻击能力与可得到的训练数据有关。在本文中，我们提出了一种新颖的、泛化性好并且数据无关的构造通用对抗扰动方法。独立于基本任务，我们的通过修改多层提取到的特征来实现欺骗率。因此，提出目标函数可以泛化到修改图像无关的扰动，并且可以应用到多计算机视觉任务，如目标检测，语义分割和深度估计。在黑盒攻击场景下，我们展示了在欺骗目标模型上，我们的优化目标优于数据依赖的目标。进一步地，通过利用探索与数据分布的简单先验，我们的目标显著地提高了扰动的攻击能力。我们方法达到的成功率重申了当前深度学习模型正处于危险中，因为我们的方法在不需要训练数据来构建对抗扰动的前提下，可以很好地泛化到其他任务上。

### 引言

当前UAP攻击存在的缺陷[8,11,15]

+ 数据依赖：[8]提出的目标函数生成UAP需要最少的训练样本才能实现收敛，并且构造的是图像认知的扰动。此外，扰动的效果与训练数据集有关。相似的，对于语义分割弄下构建的目标同样需要数据 。因此，当前方法在**缺少数据的情况下无法构造对抗样本**。
+ 弱黑盒性能：目标网络的信息通常是不被攻击者所知的，研究黑盒攻击具有实际意义。同样地，黑盒攻击揭露了真实模型的可疑性，而白盒攻击提供了攻击性能的上界。然而，[8]的UAP的黑盒攻击比他们的白盒攻击弱，而且他们也没有在黑盒场景下测试。黑盒攻击通常的含义是[13]。尽管在语义分割案例中，[11]的工作进行了目标攻击，他们也观察到**通用扰动无法很好的泛化到其他模型上**。
+ 任务相关：当前构建UAP的目标是任务相关的。目标通常旨在适应手头的基本任务，因为**攻击的概念因任务而异**。特别的，对于回归任务如深度估计和人群统计而言，当前方法构建的对抗样本效果很有限。

为了解决上述缺点，并且更好的分析模型的能力，我们提出了一种新颖的数据无关的方法来构建UAP，称为GD-UAP。我们的目标是修改图像不可知的扰动，在需知道目标模型的任何知识（数据分布，数据类别，数据样本）的情况下攻击模型。因为我们不想使用任何数据样本，与减少预测类别标签的置信度或改变预测标签的目标函数方法不同[4,7,8,11]，我们提出了一个目标函数来学习扰动，我们提出了一个学习扰动的目标，这些扰动可以掺假模型提取的特征。我们提出的目标函数尝试**在多网络层中激活神经元来破坏提取的特征**。在推理期间，增加的扰动使神经元激活失火，以污染表征并最终导致错误预测。

**贡献（FFF的改进版本）**：

1. 提出了新颖的数据无关目标函数来修改图像不可知的扰动。
2. 证明了我们的目标函数可以泛化到多视觉任务，通过对涵盖分类和回归的三个不同视觉任务的精心设计的扰动进行广泛评估。
3. 进一步地，我们展示了除了数据无关目标函数外，提出的方法可以利用关于目标函数训练数据分布的最小先验来构造更强的扰动。
4. 我们对我们提出的方法进行了全面的分析，包括：1）与数据依赖方法的全方面比较；2）在不同防御机制下，评估UAP的能力。

方法框架如下：

![image-20211108160718367](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108160718367.png)

## 方法

构建对抗样本的常见方法是

![image-20211108162854577](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108162854577.png)

但由于我们提出的方法是不需要任何数据样本的。因此，不能使用上述目标函数。相反，与典型的翻转标签的目标函数不同，我们尝试**破坏多层网络层的特征提取过程**。也就是说，我们**构建一个对抗扰动，它会导致每一层激发额外的激活，并因此误导随后的网络层。污染的积累效应最终导致CNN误分类**。

实际上，扰动只会在特殊的网络层激活信息，并提取有效信息。因此，在不使用任何数据的情况下，我们找到图像不可知的扰动，该扰动可以最大化给定CNN的每一层的伪造激活。为了构造这样的扰动，我们以随机扰动开始，并使用以下目标函数进行优化

![image-20211108163550515](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108163550515.png)

$l_i(\delta)$是当δ输入到网络f中后，在第i层的输出张量（在非线性层以后）的激活值。K是神经网络f 的层数，ξ是对δ的限制。提出的目标函数在**所有独立层计算激活值的乘积**。作者观察到乘积比其他形式的聚集（如,sum）可以生成更强大的δ。注意，目标函数是开区间的，因为不存在需要收敛的最优值。我们期望，在不可感知的约束下，尽可能生成攻击能力更强的δ。

#### 实现细节

目标网络f的参数是固定的，δ是随机扰动。我们在**所有卷积层**优化我们的目标函数。经验证明这比在所有层优化一样有效。具体而言，对于有残差连接的网络，我们在**残差连接的最后一层以及独立的卷积层上进行优化**。实验结果表明，使用上述方式生成的扰动与在所有中间层优化得到的扰动的效果一样好，但是上述方式更有效。

**注意，仅优化扰动，并且在优化过程中不使用任何数据。使用从公式3计算得到的损失的梯度来优化δ，迭代直到δ在验证数据集上达到饱和**。为了验证优化的δ的攻击性能，构造了一个无关数据集D，从验证集中随机挑选1000张图片作为验证集。目标模型在ImageNet上进行训练。从VOC2012中随机选择测试样本。相似的对于在VOC上训练的模型，在ImageNet上选择测试样本。在KITTI上训练的深度估计模型，从Place-205上选择测试样本。

#### 利用数据先验

尽管我们的方法是图像不可知的方法，但是我们也可以利用数据分布为先验。两种数据分布先验模式：

+ 输入的均值和动态范围

将范数约束的δ作为输入，并且最大化结果的激活函数。因此，在优化过程中目标CNN的输入就有一个动态范围[-ξ，ξ]（ξ=10）。然而，由于推理阶段，输入范围是[0,255]。为了解决这二者的差异，使得学习变容易，我们**提供了关于数据的有用信息**，使得扰动可以更好地探索扰动空间。因此，我们修改了目标函数来使得δ与数据的动态范围联系起来。具体而言，我们通过从均值为μ，方差为σ的高斯分布采样伪数据，该数据99.9%都位于[0,255]中。因此，目标函数变成了

![image-20211108170712796](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108170712796.png)

最后生成了输入图像两倍的单个高斯样本。然后，从该高斯样本中随机裁剪，并使用简单的数据增强技术进行增强，如随机裁剪，模糊，旋转。

+ 目标数据样本

直接使用目标数据分布优化目标函数。我们仍然使用数据无关的优化目标，而不是减少目标网络的置信度。作者发现使用作者提出的目标函数结合训练数据训练后的UAP效果更好。因此，目标函数可以设置成

![image-20211108171912557](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108171912557.png)

#### 相对比会议论文的提升点

我们观察到，提议的目标迅速累积超过强加的最大范数约束 (ξ)。 由于**每次迭代后都进行裁剪，到达约束后的更新是徒劳的**。 为了解决这种饱和，重新缩放到其动态范围的一半（即 [-5; 5]）。 重新缩放操作不仅可以提高梯度的利用率，还可以保留优化过程中学习到的模式，直到迭代为止。在[9]中，re-scale的操作每300次迭代为一个间隔。尽管该操作有助于学习更好的δ，**在没有验证δ的范围前盲目的re-sacleing时，操作是无效的。这在学习过程的后期特别有害，此时扰动可能不会在 300 次迭代中重新饱和**。

为此，我们提出了基于像素值饱和率（达到极值10）的δ的自适应re-scale策略。在每个迭代中，我们都计算δ中像素达到最大范数约束的比例。随着学习的进行，更多的像素达到了最大范数限制，并且由于裁剪，最终在ξ处达到饱和。因此，p 的增加率随着饱和而降低。我们计算训练期间每次迭代后δ的像素饱和率，表示为S。对于连续迭代，如果概率p增加的不显著（小于预设的阈值θ），我们将重新缩放到动态范围的一半。我们观察到这种自适应重新缩放始终会导致更好的学习。

#### 算法总结

![image-20211108173237880](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108173237880.png)

#### 泛化Fooling rate （GFR）

不同的任务，fooling的含义不一样。因此，提供了一个可解释性指标来衡量fooling，GFR，使该指标独立于任务，依赖于用于评估模型性能的指标。

令M为衡量任意任务的模型性能的指标，M的范围是[0,R]。该指标的输入是y，和y’，y‘是预测输出，y是gt，因此模型的性能可以衡量为M（y，y’）。令y‘’为模型对扰动的预测值。然后，指标M的GFR可以定义成

![image-20211108184959864](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108184959864.png)

该指标有以下几个优点：

+ GFR是图像识别中fooling rate的自然拓展，fooling rate的可以写成GFR(Top1) = 1-Top1（y'', y'），Top1是Top1准确率指标
+ Fooling rate应该是由扰动引起的模型输出的变化。独立于gt，仅依赖y‘’，y'，GFR衡量输出的变化。然而，一个对对抗性攻击非常健壮的表现不佳的模型将显示非常糟糕的 GFR 值，突出其健壮性。
+ GFR 衡量扰动的性能，其衡量指标是对模型造成的损害。 这是一个重要的特性，因为深度估计等任务有多种性能度量，其中一些扰动可能只对某些度量造成伤害，而其他度量不受影响。

#### 实验

目标分类任务

![image-20211108191440089](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108191440089.png)

语义分割

![image-20211108191548415](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108191548415.png)

深度估计

![image-20211108191633987](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108191633987.png)

### 分析和讨论

#### 与其他方法的比较

+ 与其他数据无关的方法相比

![image-20211108191809891](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108191809891.png)

+  数据无关与数据有关的方法对比

![image-20211108191906296](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211108191906296.png)

这些实验显示了，当目标分布不可得时，数据依赖目标函数是无效的。额外的，因为数据依赖目标依赖公开数据，得到的扰动就会依赖公开训练数据的大小。从上图可以看出，我们的UAP不受训练数据的影响，而UAP严重依赖于训练数据。

## 【CVPR2018】Art of singular vectors and universal adversarial perturbations



## 【ICCV2019】Universal Adversarial Perturbation via Prior Driven Uncertainty Approximation

关键词：模型不确定性，蒙特卡洛采样方法，激活神经元增加模型不确定性，纹理偏置增加统计不确定性，基于动量增强的随机梯度下降算法，Laplacian金字塔频率模型。

---

### 摘要

与传统的监督UAP需要训练数据的知识，数据独立的无监督UAP更加使用。现有的无监督方法未能利用模型的不确定性来生成鲁棒的扰动。在本文，作者提出了一种无监督UAP方法，称为Prior Driven UAP, PD-UA，通过利用模型的不确定性来生成鲁棒的UAP。具体地，使用蒙特卡洛采样方法来激活更多神经元来增加模型不确定性以生成更好的对抗扰动。此外，纹理偏置先验揭露了统计不确定性。UAP使用基于boosted动量优化器的随机梯度下降生成，Laplacian金字塔频域模型最终被用于维持模型的统计不确定性。大量的实验证明了作者的方法在ImageNet验证集上取得了很好的攻击性能。

###  引言

两种UAP方法：

+ 数据依赖：数据依赖方法生成UAP采用了以下的公式[21]，即模型结构和训练数据必须提取知道。因此**数据依赖方法的性能对训练样本的数量很敏感[15,24]**。

![image-20211102225758175](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211102225758175.png)

+ 数据独立：数据独立UAP更加灵活[23,24]，仅需要模型结构和参数，不需要使用的训练数据。通过**最大化**随随机高斯初始化输入的**卷积层神经元的激活值**，它可以通过直接攻击给定CNN模型的稳定性来优化UAP。（**疑问：稳定性和不确定性是如何定义的？其有什么含义？**）

本质上，UAP利用CNN模型的不确定性来干扰其在输入观察下的输出可靠性。因此，UAP的重要问题变成如何评估模型的不确定性，这激励作者从一种新的角度来研究数据独立的UAP。相似的工作有[7,18,29]，他们表示通过CNN模型中的dropout操作能够得到模型的不确定性估计。

现有的两种主要的不确定类型[4]：

+ Epistemic uncertainty（认识性不确定性）： 模型参数的不确定性与训练数据相关。直观上，在作者看来，CNN的不确定性可以由每一层卷积层激活的神经元个数反应。在模型输出期间，激活的可信神经元越多，实现的不确定性就越大，从而产生更好的 UAP。为此，在本文的UAP中引入了虚拟模型不确定性，目的在于**对每个卷积层使用蒙特卡洛Dropout激活更多的神经元以增加模型的不确定性**。
+ Aleatoric uncertainty（ 偶然的不确定性）：**数据独立但是任务依赖的不确定性**，是一种统计不确定性可以表示成一个量，这个量在不同输入上保持稳定，但是不同任务上表现不同。对于一个分类任务而言[10]，统计证明了ImageNet上的预训练模型包含很强的纹理偏差，这激励作者**使用纹理图像来揭露偶然不确定性从而更好的欺骗CNN**。具体地，**引入纹理偏差作为先验信息**，其在扰动训练时约束在模型的偶然不确定性上。因为纹理图片包括了低频信息[33]，因此**引入了Laplacian金字塔频域模型来以更快的收敛效率提升攻击性能**。

贡献：

1. 为了近似Epistemic uncertainty，作者提出了一个虚拟模型不确定性，其能使PD-UA主要最大化对应于扰动输入的神经元激活，这能增加不确定性和提升攻击攻击性能。
2. 为了近似Aleatoric uncertainty，作者首先引入了纹理偏置来初始化UAP，这在公共基准上取得了重大的性能提升。从而得到了两个基层而重要的结论：（1）好的初始化扰动在攻击更深层CNN上对生成的UAP的质量有重要影响。（2）纹理样的扰动可以不需要任何训练直接欺骗CNN。
3. 进一步提出了一个拉普拉斯金字塔频率模型来提升低频部分的梯度，其输出被有效地用于通过带有动量的 SGD 更新扰动。
4. 在ImageNet上的6个模型上，与SOTA的数据独立UAP进行比较。在fooling rate上有巨大的提升。

### 方法

UAP通过增加模型输出的预测不确定性来欺骗CNN。

+ **首先**，当越来越多的神经元被激活时，CNN的认知不确定性会变大，更有助于生成鲁棒的UAP。为此，**向神经元的输入上引入二项分布来近似每一层的不确定性（a Bernoulli distribution is introduced
  over the output of the neurons to approximate the uncertainty per layer）**。该过程由MC dropout实现。
+ **其次**，为了更好的近似偶然不确定性，其统计地反应了内在的噪声分布，作者引入纹理偏置作为信息先验，进一步增加模型的不确定性。模型的不确定性在更深层次的网络结构上提升了攻击性能。
+ **最终**，作者将二者结合在一个网络中，并且由SGD直接优化。
+ **此外**，Laplacian金字塔频域模型被用来正则化梯度以促进收敛。

整个框架如下图所示：

![image-20211103001705251](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103001705251.png)

相应的算法如图所示

![image-20211103001741429](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103001741429.png)

其中公式8如下图所示，其中Ue(δ)表示整个CNN网络的不确定性，La(δ)表示**风格损失**（是特征向量的Gramm矩阵），鼓励扰动重现纹理偏置图像。![image-20211103001814263](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103001814263.png)

公式10-12如下图所示，为动量增强更新策略

![image-20211103001928362](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103001928362.png)

#### 分析

最近的研究表明[32,34]，**当每层所有的神经元都被激活，具有相似概念的神经元就被会反复激活，这导致了信息冗余**。

### 实验

实验设置：

+ 数据集：数据和模型
+ 评估指标：Fooling rate
+ 比较方法：UA和PP都是作者方法的简化版本分别为
  + UA没有偶尔不确定性（没有纹理先验）
  + PP仅使用纹理先验作为扰动
+ 实现细节：

**结果**

![image-20211103002837723](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103002837723.png)

从表1可以看出作者方法的性能都超过了其他方法。

![image-20211103002936086](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103002936086.png)

![image-20211103003009466](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103003009466.png)

![image-20211103003025928](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103003025928.png)

上图是使用纹理偏置初始化的扰动结果图

![image-20211103003036499](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211103003036499.png)

以上是直接使用不同的纹理先验作为通用扰动的攻击效果

**消融实验**

+ 收敛速度
+ 优化中不同LPFM(拉普拉斯金字塔频率模型)参数的影响

---

## 【ICIP2020】Universal adversarial attack via enhanced projected gradient descent



## 【ICIP2021】Universal adversarial robustness of texture and shape-biased models



----

## 参考文献：

[1]: Seyed-Mohsen Moosavi- Dezfooli, Alhussein Fawzi, Omar Fawzi, and Pascal
Frossard. Universal adversarial perturbations. In CVPR, 2017.

[2]: Konda Reddy Mopuri, Utsav Garg, and R. Venkatesh Babu. Fast feature fool: A data independent approach to universal adversarial perturbations. In BMVC, 2017.

[3]: Konda Reddy Mopuri, Phani Krishna Uppala, and R Venkatesh Babu. Ask, acquire, and attack: Data-free uap generation using class impressions. In ECCV, 2018.

[4]: Konda Reddy Mopuri, Utkarsh Ojha, Utsav Garg, and R. Venkatesh Babu. Nag: Network for adversary generation. In CVPR, 2018.

[5]: Omid Poursaeed, Isay Katsman, Bicheng Gao, and Serge Belongie. Generative adversarial perturbations. In CVPR, 2018.

[6]: Konda Reddy Mopuri, Aditya Ganeshan, and Venkatesh Babu Radhakrishnan. Generalizable data-free objective for crafting universal adversarial perturbations. TPAMI, 2018.

[7]: Hong Liu, Rongrong Ji, Jie Li, Baochang Zhang, Yue Gao, Yongjian Wu, and Feiyue Huang. Universal adversarial perturbation via prior driven uncertainty approximation. In ICCV, 2019.

[8]: Valentin Khrulkov and Ivan Oseledets. Art of singular vectors and universal adversarial perturbations. In CVPR, 2018.

[9]:  Co, Kenneth T., et al. "Universal adversarial robustness of texture and shape-biased models." *2021 IEEE International Conference on Image Processing (ICIP)*. IEEE, 2021.

[10]:Deng Y, Karam L J. Universal adversarial attack via enhanced projected gradient descent[C]//2020 IEEE International Conference on Image Processing (ICIP). IEEE, 2020: 1241-1245.





