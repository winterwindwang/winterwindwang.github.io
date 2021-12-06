---
layout: post
title: 【文献调研】通用对抗样本攻击
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
- 数据独立：quickly craft generalizable UAP[AAA]. GD-UAP， Cosine-UAP

无数据UAP和数据依赖UAP。但是有些UAP宣称是无数据UAP，但是这些方法却需要代理数据来训练。

采用无穷范数（10/255）的文献：UAP, GAP, GD-UAP, CosUAP

------

| 方法                                           |                      |          | 方法                 | $\xi$                    | 评估方式                                | 代码                                                         |
| ---------------------------------------------- | -------------------- | -------- | -------------------- | ------------------------ | --------------------------------------- | ------------------------------------------------------------ |
| [UAP][1]【CVPR2017】                           | 数据依赖             |          | 边界最小距离（优化） | lp范数10<br />l2范数2000 | fooling rate                            | [官方](https://github.com/LTS4/universal)<br />[torch版本1](https://github.com/ferjad/Universal_Adversarial_Perturbation_pytorch)<br />[torch版本2]() |
| [FFF][2]【BMVC2017】                           | 数据无关             |          |                      |                          |                                         | [官方](https://github.com/val-iisc/fast-feature-fool)        |
| [AAA][3]【ECCV2018】                           | 数据无关             |          | 生成模型             | 10                       |                                         | [官方](https://github.com/val-iisc/aaa)                      |
| [NAG][4]【CVPR2018】                           | 数据相关             |          | 生成模型             |                          |                                         | [官方](https://github.com/val-iisc/nag)                      |
| [GAP][5]【CVPR2018】                           | 数据无关<br />/依赖  |          | 生成模型             | lp范数-10                | fooling rate                            | [官方](https://github.com/OmidPoursaeed/Generative_Adversarial_Perturbations) |
| [GD-UAP][6]（会议论<br />文扩展）【TPAMI2018】 | 数据无关<br />/依赖  | 任务无关 | 神经网络中间层       |                          | fooling rate                            | [官方](https://github.com/val-iisc/GD-UAP)<br />[torch版本](https://github.com/psandovalsegura/pytorch-gd-uap) |
| [SV-UAP][8]【CVPR2018】                        | 数据依赖（少量数据） |          |                      |                          |                                         | [torch版本1](https://github.com/slayff/art_of_vectors_pytorch)<br />[torch版本2](https://github.com/AndrewAtanov/nla-project/blob/master/method.py) |
| [PD-UAP][7]【ICCV2019】                        | 数据独立             |          |                      | 10                       | fooling rate                            | [官方](https://github.com/LynnHongLiu/PDUA)                  |
| [F-UAP][12]【CVPR2020】                        | 数据独立             |          |                      | 10                       | fooling rate                            | [官方][https://github.com/phibenz/uap_virtual_data.pytorch]  |
| [UPGD-UAP](10)【ICIP2020】                     | 数据依赖             |          | 投影梯度优化         | 10                       |                                         |                                                              |
| [SGD-UAP][9]【ICIP2021】                       | 数据依赖             |          | 梯度优化             | 10                       | fooling rate<br />targeted fooling rate | [官方](https://github.com/kenny-co/sgd-uap-torch)            |
| [CosUAP][11]【ICCV2021】                       | 数据独立             | 任务无关 | 梯度优化             | lp，10                   | fooling rate                            | [官方][https://github.com/ChaoningZhang/Cosine-UAP]代码尚未公布 |

注释：

+ [这个版本](https://github.com/BardOfCodes/universal_pytorch)提供了UAP，FFF，DeepFool三种方法的实现
+ $\xi$一般是指无穷范数约束下的值



---

## 【CVPR2017】Universal adversarial perturbations

经过预训练的模型后仍然容易受到通用对抗扰动的攻击

### 摘要

给定一个训练好的神经网络，作者表示存在一种通用扰动（图像认知）并且很小的扰动向量，可以使自然图像以很高的概率被误分类。作者提出了一个系统性的算法来计算UAP，并且显示了SOTA神经网络很容易受到这种UAP的影响，但是却又对人眼不可感知。作者进一步分析了这些通用扰动，表示这些扰动可以很好地跨神经网络。UAP的存在揭露了分类器的高维决策边界中重要的几何关联。它进一步概述了潜在的安全漏洞，因为输入空间中存在单一方向，攻击者可能会利用这些方向来破坏大多数自然图像上的分类器。

![image-20211114140326086](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114140326086.png)

#### 贡献

+  第一个研究UAP的工作
+ 提出了一个算法找到UAP。算法在一系列训练数据集上搜索扰动，并且通过聚集原始扰动向量来持续地将数据点发送到分类器的决策边界之外。
+ UAP不仅可以在跨不同图像，在不同网络之间也有很好的泛化性。
+ 通过检查决策边界间不同部分的几何关系分析了深度神经网络的对UAP的脆弱性

### 方法

提出的优化框架

![image-20211114140751505](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114140751505.png)

其中μ是图像的分布，k表示分类器函数，其输出是每个图像x的预测标签k(x)。本文的目的是找到扰动向量v，使得分类器在来自μ的绝大部分图像上都做出错误的预测。为找到UAP，需要满足以下两个约束

![image-20211114141030958](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114141030958.png)

其中ξ控制了扰动向量的大小，δ是扰动在数据分布μ的图像中期望达到的fooling rate。

优化方向如下

![image-20211114141150459](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114141150459.png)

为了满足||v||p<=ξ，UAP的更新要进一步地投影到lp范数的以0为原点，ξ为半径空间内球内。投影可以表示成

![image-20211114141329527](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114141329527.png)

算法的整体框架如下图所示

![image-20211114141418401](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114141418401.png)

### 深度网络的UAP

在ImageNet验证集上验证UAP对不同网络结构的影响，结果图下表所示

![image-20211114141829367](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114141829367.png)

针对不同扰动生成的UAP效果图

![image-20211114142033415](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114142033415.png)

![image-20211114142119662](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114142119662.png)

下面展示了UAP效果与数据规模的关系，整体看，所使用的图像越多，UAP的效果就越好

![image-20211114142216475](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114142216475.png)

**UAP还会导致神经网络做出主导性标签**，比如网络将添加UAP的对抗样本都识别为某个类。

![image-20211114142537825](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114142537825.png)

### 对UAP脆弱性的解释

与不同方法的对比

![image-20211114142800454](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114142800454.png)

可以看出UAP可以使用最少的扰动量，达到较高的fooling rate

为计算分类器决策边界的不同区域之间的关联，作者定义了如下的归一化向量矩阵

![image-20211114142944122](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114142944122.png)

在验证集数据点附近的n个邻居的决策边界距离。对于线性分类器而言，决策边界是超平面，N的秩是1，因为所有正常向量都是线性的。

![image-20211114143225182](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114143225182.png)

---

## 【BMVC2017】 Fast feature fool: A data independent approach to universal adversarial perturbations

### 摘要

SOTA的CNN被显示了会被这个图像认知的扰动欺骗，称为UAP。这些扰动可以泛化到在相同数据集上训练的模型上。然而，这些算法需要训练数据，这些数据是用于训练CNN的，并且通过复杂的优化计算对抗扰动。这些方法的攻击性能直接与可得到的训练数成比例相关。这使得他们不适合用于实际攻击场景中，因为攻击者获得训练数据是不符合实际的。在本文，作者首次提出了一种**新颖的数据独立方法来生成图像认知的扰动**来攻击用于目标识别的CNN。作者进一步证明了这些扰动可以**跨相同或不同数据上训练的网络结构**。在缺乏数据的而情况下，作者的方法通过欺骗多个网络层学到特征来有效地生成通用对抗扰动，从而导致CNN误分类。实验证明了由**无需任何数据生成的通用扰动的效果**的**有效性**和**很好的迁移性**。

### 1 引言

UAP需要大量的目标模型的训练数据来优化，并且通过解决一个复杂的数据依赖优化问题来设计单个扰动，将该扰动可以使大部分图像使分类器的分类发生错误。使用训练数据的子集，它以改变预测标签为目的，迭代地更新通用扰动。**他们方法（UAP）的缺点是他们的优化过程需要一定最小数量的训练数据才能收敛**。此外，**他们的fooling rate直接与所使用的训练数据集规模成比例，数据规模越大，效果越好**。

+ 作者表示现在还没有方法可以在不需要任何数据的情况下生成对抗样本的研究。作者的方法不需要关于目标数据分布的任何知识，只需要目标网络。
+ 使CNN的单个网络层学到的特征哑火来生成公正的激活可以导致最终样本的误分类。
+ 作者表示与数据依赖的UAP相比，数据独立的扰动展现出更强的跨网络（在相同数据集上训练的）迁移性。

### 2 方法Fast Feature Fool

在不需要任何与目标CNN有关的数据，作者找到的扰动δ可以在每一层产生最大的虚假激活。为了得到这个扰动，首先从随机噪声δ开始，并使用如下的损失进行优化

![image-20211114131659114](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114131659114.png)

其中$\bar{l}_i(\delta)$是当δ作为输入到CNN时，**在第i层的输出张量的均值激活(mean activation)。其中激活是在非线性（通常是ReLU）后的值，所以$\bar{l}$是非负的。K是CNN中的最大扰动激活的总行数**。作者**考虑CNN中除全连接层外的所有卷积层**。这是因为卷积层通常被当作可以学习合适的特征来提取信息，然后全连接层作为一个分类器的角色。并且**作者也发现在卷积层上优化已经足够**。因此，将优化限制在特征提取层，ξ是扰动强度的最大幅度，设置为10。

上述的目标函数计算了多个网络层的均值激活值的乘积，以同时最大化所有层的扰动。作者发现**product可以比其他组合单词激活值的方式（如sum）生成更强的扰动**。这是可以理解的，因为product是更强的约束，来迫使所有层的激活值增加以减少损失。为了避免极端值（约等于0）的影响，在**乘积上使用了对数**。注意，函数函数是开区间，所以没有可以到达的极值。仅使用损失进行约束。

**步骤:** 作者以**一个训练好的网络和一个随机扰动图像δ开始优化**。然后使用上述的优化策略更新δ来达到给定网络上所有卷积层的更高的激活值。在每个更新步，将扰动裁剪符合不可感知约束的范围内，当损失饱和或者欺骗率不再上升时，视为收敛。（全程只优化扰动？扰动作为网络的输入，并始终优化该扰动？）

### 实验

生成的扰动如下图所示（扰动初始化为[-10,10]内的均匀随机分布）

![image-20211114133932579](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114133932579.png)

下图是在ImageNet验证集上的对抗样本

![image-20211114134040989](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114134040989.png)

下表是跨网络间的迁移性

![image-20211114134124580](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114134124580.png)

此外，作者还进行了以下的实验

+ 跨数据的迁移性

+ 以更小（浅层）网络上生成的扰动初始更深网络扰动的优化：如使用针对VGGF网络生成的扰动，来初始化攻击VGG19的扰动。

+ 与数据依赖方法的比较（UAP）

  + 可以得到目标数据

  ![image-20211114134759239](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114134759239.png)

  可以看出UAP的性能与数据规模呈正相关

  + 不能使用目标数据

  ![image-20211114134951415](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114134951415.png)

  可以看到在不能使用与目标网络训练数据相同的数据训练时，UAP的性能远差于作者的方法。

+ 收敛时间

![image-20211114135055883](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114135055883.png)

+ 实现细节：作者**最小化公式3得到的值，因此损失在负方向无下届**。使用Adam优化器，监视了损失来查看是否饱和，并且在1000张图片上验证效果。输入δ限制在[-10,10]范围内，饱和非常快。因此，**为了避免后续的优化被忽略，将扰动缩放到[-5,5]并继续优化，实验结果证明每300次缩放一次的效果较好**。

---

## 【TPAMI2018】Generalizable data-free objective for crafting universal adversarial perturbations（FFF的期刊版本）

关键词：通用对抗扰动，非数据依赖，中间层特征

无数据先验的UAP对VGG16和ResNet152的fooling rate分别为45.47和29.78。

输入均值和动态范围先验先验的UAP对VGG16和ResNet152的fooling rate分别为63.08和37.3。

无数据先验的UAP对VGG16和ResNet152的fooling rate分别为77.77和66.68。

**没有做不同方式生成对抗样本（有无数据先验，只做了有数据先验的实验）的迁移性攻击的实验**？

**无数据对抗样本攻击呈现出模型差异，resnet152对这种攻击的鲁棒性更好，反之VGG系列的鲁棒性较差**。

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

### 方法

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

目标分类任务（**无数据训练**得到的对抗样本的攻击效果）

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



## 【ECCV2018】Ask, acquire, and attack: Data-free uap generation using class impressions

关键词：利用白盒模型生成训练样本，利用这些训练样本来生成UAP。（本质上还是基于数据的UAP）

**UAP对VGG16和ResNet152的fooling rate分别为65.64和60.72。迁移性为（在所有网络上进行攻击的均值分别是：29.64，53.73）**

---

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

#### 思考

他们是通过模型来采样类别印象样本，使得每个噪声图像在分类成某个特定的类别时具有很高的置信度。那么是否可以探究模型难以分类的这些样本，找到这些样本特征空间的特点（有点像北航的checkout）。

---

## 【CVPR2018】 Nag: Network for adversary generation

### 摘要

现有方法提出的优化方法大都使用不可感知的约束来解决一个fooling 目标函数，以构建对抗样本。然而，对于一个给定的分类器，他们通常需要每次生成一个扰动，这是来自多种对抗性扰动的单个实例。同样的，为了**构建鲁棒模型，探索对抗样本的流形非常关键**。在本文，作者首次提出，使用**一种生成方法来建模型对抗扰动的分布**。生成模型的灵感来自于GAN，但是使用fooling和多样性目标函数进行训练。训练的生成网络尝试捕获在给定分类器上对抗扰动的分布，并且大量的不同种类的扰动。实验整理了生成的扰动：1）达到SOTA的性能；2）展示出多样性；3）显示出很好的跨模型迁移性。作者认为这个工作可以认为是在对端过程中对抗扰动复杂流形的重要一步。

### 1 引言

作者指出尽管UAP和FFF可以使不同数据序列或初始化进行优化，他们的目标函数均以学习到非常接近的扰动结束（**扰动缺乏多样性**）。实际上，这些方法仅能证明UAP存在于给定分类中。这限制了**揭露这些扰动的潜在分布的信息，反过来关于目标分类器本身**。因此，更加迫切的相关任务是建模对抗扰动的分布。这可以帮助我们更好地理解模型对对抗扰动的脆弱性。进一步地，建模分布将提供对抗样本迁移性的探究并且有助于防止黑盒攻击。它同样地有助于生成大规模扰动，以便在对抗训练中学习更加鲁棒地模型。

### 2 提出方法

方法的总体框架如下图所示

![image-20211114151930692](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114151930692.png)

#### fooling 目标函数

![image-20211114152051429](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114152051429.png)

其中q’c表示干净类别c的预测概率。添加扰动后，该值会减少，并被分类成其他类别。使用该损失来确保生成的扰动具有攻击性

#### diversity 目标函数

仅使用上述的目标函数可能会导致生成有限的扰动。为了建模扰动的分布，需要探索扰动的空间并生成多样性的扰动。在一个生成的扰动的mini-batch中，该目标函数通过分开目标分类器投影的特征嵌入来鼓励他们之间彼此不同。具体地，在给定一对生成扰动δn和δ‘n，目标函数增加他们之间在分类器第i层的特征之间的距离

![image-20211114152749897](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114152749897.png)

其中n’是[1,B]中的随机索引，并且n'≠n，B是批大小。fi是CNN的第i层输出，d表示特征之间的距离（欧拉距离）。

所以最终的优化目标为

![image-20211114152943191](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114152943191.png)

#### 结构和实现细节

对于给定的目标分类器，生成器G将潜在空间Z映射到扰动分布。其结构是5层的转置卷积层。最后一层是tanh激活函数，并由ξ进行缩放。ξ被设置成10，扰动的范围是[-10,10]。网络来自[26]，在**ILSVRC-2014数据集上训练**。**针对不同的CNN结构，生成器保持不变，分别学习不同的扰动分布**。训练时，从均匀分布[-1,1]间采样数据，然后通过生成器转移到图像的大小。并构建了对抗对，并且还打乱了扰动的原始分布，构建了新的对抗对。在训练时，使用了留1000个随机图像作为验证集，在该数据集上取得最佳结果就停止训练。最大的epochs是100，所有的目标CNN都在60-70轮的时候饱和。

### 实验

实验数据：从ILSVRC2014训练取10000个训练样本（每类10个），然后在相应的50000测试集上验证。**潜在空间d为10**。

#### Fooling rate

![image-20211114155407424](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114155407424.png)

白盒攻击的fooling rate100个扰动上计算的平均值，黑盒是10个。

#### 生成的不同的扰动，以及对比

![image-20211114155557873](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114155557873.png)

![image-20211114155605827](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114155605827.png)

![image-20211114155626110](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114155626110.png)



## 【CVPR2018】Generative Adversarial Perturbations

关键词：显示出可以用于分割网络中。但是需要专门为分割网络设计通用扰动。

---

### 摘要

作者提出可训练深度网络来将图像转换成对抗扰动。作者的方法可以生成目标、非目标攻击的图像不可知和图像依赖的扰动。作者也证明了相似的结果可以在分类和语义分割中获得令人印象深刻的攻击结果，消除了为每个任务人工设计攻击方法的需要。在更具挑战性的高分辨率图像上的广泛实验证明了方法的有效性，证明可以使用少量扰动达到较高的成功率。此外 ，在推理阶段远快于现有的迭代方法。

### 引言

贡献：

+ 提出了一个统一的框架来生成通用和数据依赖的扰动，同时对分类和语义分割有用，考虑使用无穷范数和2范数的目标和无目标攻击。
+ 利用通用生成模型提升了UAP的性能
+ **首个提出有效的目标通用攻击**。这更具具有挑战性，因为这要求单个扰动加入到其他图像上可以使分类其分类成某个特定的类别。
+ 在推理阶段，比**基于迭代和基于优化的方法要快**。

### 方法

提出的框架结构如下图所示

![image-20211114164044287](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114164044287.png)

作者发现下述的损失函数可以在实验中获得较好的结果（H为交叉熵损失函数）

![image-20211114164139584](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114164139584.png)

同样的，正如[26,27]中提到的，还考虑了最不像的ku(x)=argmin k(x)，损失函数可以定义成

![image-20211114164253166](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114164253166.png)

作者发现对于Inception模型，基于logits损失[7,8]可以生成更优的结果。

对于目标扰动，则使用one-hot格式的目标的交叉熵损失

![image-20211114164423819](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114164423819.png)

#### 欺骗多个网络

网络结果如下图所示

![image-20211114164853056](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114164853056.png)

同时欺骗多个目标网络，即集成攻击

### 实验

攻击的是在ImageNet上的预训练网络。特征之间衡量使用的是欧拉距离。所有结果均在50000张ImageNet验证集上进行。

![image-20211114165839576](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114165839576.png)

![image-20211114170016191](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114170016191.png)

#### 以下是在分割上的实验结果

风格数据集采用的是Cityspace，它包含2975个训练样本和500个验证样本。与[34]一样，使用双线性和最近邻插值方法分别对图像和标签进行下采样。通用扰动是在FCN分割网络上训练得到的。

![image-20211114170035066](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114170035066.png)

---

## 【CVPR2018】Art of singular vectors and universal adversarial perturbations

关键词：只需要64个样本，就能达到60%的fooling rate

---

### 摘要

在本文，足总和提出了一种新的算法来构建UAP。作者的方法是基于计算网络隐藏层的雅可比矩阵(p,q)的奇异向量来实现的。结果UAP呈现出很有趣的图案，并且仅需要使用64张图像就可以实现在ImageNet验证集上达到超过60%的fooling rate。作者还调查了雅可比矩阵奇异值最值和fooling rate之间的关系，显示出构造的UAP可以泛化到其他网络。

### 1 引言

作者提出了一种新算法来构建通用扰动，该方法基于解决简单的优化问题，即找到DD的特征图上的雅可比矩阵的奇异值向量(p,q)。作者的idea是基于一下的观察，即对抗噪声的范数通常很小，由DNN计算得到的非线性特征图中扰动可以合理地近似成雅可比矩阵。矩阵A的(p,q)奇异向量定义成以下的优化问题

![image-20211117145625127](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117145625127.png)

贡献：

+ 提出一种生成UAP的算法，使用通用能量方法来计算特征图的雅可比矩阵的(p,q)奇异值向量
+ 我们的方法有能力从数据集中的少量样本中产生相对较好的对抗样本
+ 作者研究了最大奇异值(p,q)和fooling rate之间的关系。表明奇异值可以作为衡量给定神经网络鲁棒性的定量方法。

### 2 方法

#### 通用能量方法

假定对于一些线性特征图A，以及A和A的转置的马特维茨(matvec)函数。给定参数r>1，定义以下的函数

![image-20211117150600762](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117150600762.png)

向量之间是element-wise。当r>1时，我们可以找到1/r + 1/r` =1。然后给定一些初始条件x，使用下面的算法

![image-20211117150730449](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117150730449.png)

马特维茨函数的有效是实现。具体算法见

![image-20211117151047803](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117151047803.png)

假定拥有自然图像数据X，以及固定的训练用于分类的神经网络。

+ 首先，从X中选择固定的随机batch Xb，并且指定特定的DNN隐藏层。
+ 然后使用算法3计算矩阵的马特维茨函数（由公式9定义）。
+ 最终运行算法2来得到扰动，并且有必要的话对其进行调整。

### 3 实验

实验设置

在50000张ILSVRC2012 验证集数据集上进行测试

设置为：p=∞，q=10

在不同网络层上生成UAP的效果

![image-20211117151541619](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117151541619.png)

不同神经网络的奇异值对比

![image-20211117151945901](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117151945901.png)

奇异值收敛情况

![image-20211117152015263](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117152015263.png)

不同网络层的奇异值以及fooling rate

![image-20211117152051605](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117152051605.png)

UAP的迁移性

![image-20211117152110696](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117152110696.png)

batch size 对UAP性能的影响（训练UAP使用的数据量对攻击性能的影响），总趋势是使用的数据越多，fooling rate就越高

![image-20211117152151047](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117152151047.png)

相关结论：

+ 使用VGG19的block2_pool1层得到的扰动是最有效的，与其有趣的边状形状有关系。
+ 网络层越接近DNN输入，生成的UAP的效果就越好。
+ 增大batch size并不能带来大的fooling rate增益，使用batch size大小仅有16张图片也有可能构造超过56%fooling rate的UAP。（疑问：随机选择的64张图片，无法覆盖1000个类，那么使用的这64张图片的类别与攻击成功的类别之间存在什么关系吗？）

---

## 【ICCV2019】Universal Adversarial Perturbation via Prior Driven Uncertainty Approximation

关键词：模型不确定性，蒙特卡洛采样方法，激活神经元增加模型不确定性，纹理偏置增加统计不确定性，基于动量增强的随机梯度下降算法，Laplacian金字塔频率模型。

---

### 摘要

与传统的监督UAP需要训练数据的知识，数据独立的无监督UAP更加使用。现有的无监督方法未能利用模型的不确定性来生成鲁棒的扰动。在本文，作者提出了一种无监督UAP方法，称为Prior Driven UAP, **PD-UA**，通过利用模型的不确定性来生成鲁棒的UAP。具体地，使用蒙特卡洛采样方法来激活更多神经元来增加模型不确定性以生成更好的对抗扰动。此外，纹理偏置先验揭露了统计不确定性。UAP使用基于boosted动量优化器的随机梯度下降生成，Laplacian金字塔频域模型最终被用于维持模型的统计不确定性。大量的实验证明了作者的方法在ImageNet验证集上取得了很好的攻击性能。

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

![image-20211129002307224](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129002307224.png)

UAP通过增加模型输出的预测不确定性来欺骗CNN。

**方法的输入是什么？UAP是如何优化的？**

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

## 【CVPR2020】Understanding Adversarial Examples from the Mutual Influence of Images and Perturbations

关键词

---

### 摘要



---

## 【ICIP2020】Universal adversarial attack via enhanced projected gradient descent

关键词：

---

### 摘要

在本文作者提出一种新颖的方法来计算更加有效的UAP，通过增强目标分类器的投影梯度方向。通过最大化目标网络的原始损失函数，作者使用反向传播更新了对抗样本，并且通过在扰动的图像上持续的积累的小的扰动来更新对抗样本。作者攻击了不同的ImageNet网络，并且与SOTA通用对抗攻击方法进行比较。实验结果显示了，与SOTA方法相比，作者提出的对抗攻击方法可以取得更高的fooling rate，并且在跨模型验证时展示出更好的结果。



### 方法

作者提出的框架如下图所示

![image-20211117162143912](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117162143912.png)

作者提出的算法框架如下图所示：

![image-20211117162210799](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117162210799.png)

对于每个图片，作者仅更新加上扰动而没有改变图像预测结果的图片。



### 实验

实验设置：

作者在ILSVRC2021 ImageNet训练集上随机采样10000张图片作为训练集，选择50000张ImageNet验证集作为验证集。

扰动结果图

![image-20211117162328690](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117162328690.png)

以下是与其他UAP方法的对比结果图

![image-20211117162514261](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117162514261.png)

以下是UAP迁移性能结果表。

![image-20211117162725680](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117162725680.png)



---

## 【ICIP2021】Universal adversarial robustness of texture and shape-biased models

关键词：UAP会改变模型特征对对抗扰动的脆弱性。

---

### 摘要

神经网络中形状偏置的曾加显示出其对常见破坏和噪声的鲁棒性。本文分析了纹理和形状偏置模型的对于UAP的对抗鲁棒性。作者使用UAP验证了不同程度的基于形状的DNN模型的鲁棒性。作者发现形状偏置模型并不能极大地提升对抗鲁棒性，作者显示纹理和形状偏置的模型可以提升通用对抗鲁棒性，并且保持较强的攻击性能。

### 1 引言

文献[5]宣称偏向形状的模型可以提升他们的鲁棒性，并且通过实验证明z和谐形状偏置模型可以提高模型对常见破坏的鲁棒性。

从机器学习的角度，UAP揭露了模型对全局特征比较敏感[10,11]。这些全局特征值得研究，因为他们可以揭露模型用于分类的潜在特征。

在本文，作者验证了纹理和形状偏置模型对通用攻击的鲁棒性，提出目标UAP方法来可视化和 分析他们大多数脆弱特征，并且最终证明了**这些模型的集成投票**可以在保证干净模型的的性能时，提升防御通用攻击时最糟糕的性能。我们对 **UAP 的分析揭示了增加的形状偏差在多大程度上提高了模型对普遍攻击的对抗性鲁棒性**。

贡献：

+ 我们表明，形状偏向模型不会减轻普遍攻击，但它们会改变模型易受攻击的特征集。
+ 我们在基于纹理和形状偏置的模型上验证了目标UAP，并且可视化UAP，来显示结果扰动的的差异。
+ 最终，作者提出了纹理和形状偏置的集成模型来维持更强的性能，并且提高对通用攻击的鲁棒性

### 2 方法

下图是针对不同神经网络生成的UAP图

![image-20211117155739553](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117155739553.png)

作者使用SGD算法来生成UAP。**SGD是PGD算法的一种变体，但是在整个批上进行优化，而不是在单个图像上进行优化**。使用SGD是因为其在最佳通用逃避率上优于其他方法[20,21]，并且在收敛上也更有保证。SGD优化目标函数$\sum_iL*(x_i+\delta)$，其中L是网络的训练损失，Xbatch是输入的batch，δ是扰动的几何。δ沿着$-\sum_iL*(x_i+\delta)$的方向在batch中进行优化，结果对抗样本的值被裁减到[0, 255]。在研究中，扰动约束采用无穷范数约束。

### 实验

实验设置：考虑使用4个resnet50模型。在每个模型上使用不同的范数在50000张图象ImaegNet验证集上生成和优化UAP。

不同ε的值的实验结果如下

![image-20211117160629047](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117160629047.png)

跨模型之间的迁移性

![image-20211117160726697](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211117160726697.png)

---

## 【ICCV2021】Data-Free Universal Adversarial Perturbation and Black-Box Attack

关键词：无目标UAP可以使网络将对抗样本都分类成某个主导类别。

---

### 摘要

UAP，单个扰动来欺骗大部分图像，被广泛地任务是更具实际的攻击，因此UAP可以先生成然后直接用于攻击阶段。一个有趣的现象是**无目标UAP会造成大部分图像都被误分类成某个 领域类别**。该现象在之前的工作中被报道，但是**缺乏合理的解释**，这也是本文工作尝试提供一个替代解释。对于更加实际的通用攻击，对**无目标UAP的研究集中在消除对原始训练样本的依赖，从移除对样本标签的需要到限制样本大小**。对于更严格的无数据无目标UAP，作者提出利用人工拼图（artificial Jigsaw）图像作为训练样本，证明了具有竞争力的性能。作者进一步研究了利用UAP实现无数据黑盒攻击的可能性，这被认为是最具挑战性的威胁模型。作者证明了存在一种无需优化的重复图案，可以成功地攻击深度模型。

### 1 引言

作者的工作通过对显性(dominant)标签现象提供另一种解释来重新审视 UAP，即**无目标UAP导致模型误分类大部分图像到主导标签（即同一个标签，类似于目标攻击）**。与[25,60]一样，作者通过感知主导标签观测到无目标UAP有相似的现象。**UAP导致所有对抗样本上的平均logit值与单独使用UAP作为输入的logit值成比例**。该结果**进一步通过分层和逐步模型响应分析进行写作，表明无目标UAP对模型响应有主导作用，并且随着训练的进行，主导影响和fooling rate之间存在正相关**。这个观察结果激发作者去进一步调查简单但是有效的技术来实现无数据约束下（黑盒、白盒）的更加实际对抗攻击。

**贡献**：

+ 作者审视了在由无目标UAP导致的主导标签背后的机制。特别地，作者显示了现有地解释[38]假设主导标签占据了大部分数据空间无法证明一些基于合理性假设的观察现象。作者通过观测提供了一种替代性解释，**无目标UAP对模型对对抗样本的响应有主导作用**。尽管如此，无目标UAP仍然无法使所有图像误分类，作者发现**一些样本对于探索的非目标 UAP 来说系统性更强，并且它们往往具有重复的语义内容**。
+ 提出了一种无数据的目标UAP。具体地，采用无监督余弦相似度算来优化无目标UAP，并且通过常见的数据增强技术减少了样本大小。对于更严格的无数据UAP，作者提出采用变频人工拼图作为训练样本。作者表示设人工图像可以模拟自然图像的属性。
+ 进一步研究了UAP用于更加实际的无数据黑盒攻击。作者发现无需优化的简单重复图案也有一定的攻击效果。并且调查了目标检测和语义分割任务上的应用。

### 2 相关工作

#### 基础攻击方法

#### 通用对抗方法

通用对抗攻击方法的描述

#### 对抗脆弱性的解释

UAP脆弱性的研究比较少。[38]认为UAP和随机扰动的fooling rate的巨大差距表明决策边界的几何具有冗余性。[39]中的作者进一步从几何角度分析了UAP的存在。具体地，UAP的存在归咎于正性曲线决策边界。通过研究UAP，[25]发现预测能力和被研究的深度分类器对抗脆弱性纠错在一起，这表明了准确率的损失会带来鲁棒性的增益。聚焦目标攻击，[60]展示了目标攻击有预设目标类别的主导特征。作者集中研究**为什么无目标UAP会导致模型将大部分对抗样本图像分类成主导类别**。尽管以前也有人研究，但是他们并没有给出合理的解释。

### 3 背景和算法对比

算法讨论部分详细分析了不同的UAP算法。大部分方法采用交叉熵来执行通用对抗训练。然而，在实际样本不可得的时候，该方法不可用。为此，作者提出使用子监督的方式优化UAP，设计的新损失来最小化余弦相似度（CosSim）：

![image-20211113224919938](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113224919938.png)

其中k()表示DNN输出的logit向量，在**argmax前一层特征**，预测类别由$\hat{k}$表示。直觉上，使用该损失，扰动v可以被优化，因为k(x)和k(x+v)彼此相距很远，从而导致对x的预测标签发生了变化。作为控制研究，也计算了k(v)和k(x+v)的余弦相似度。为了使其可以从公式2中的损失可微分，我们将其称为 CosSim-max，根据经验发现，与等式中的性能相比，它的性能较差。图2中对比了二者效果。假设训练数据是全部可得的，作者使用基于训练数据的Cosine-UAP与其他SOTA UAP的比较，这些方法均使用了真实标签，对比结果如表1所示。GAP需要训练一个额外网络，因此他们需要更多的资源。而UAP需要数小时来构建一个UAP，Cosine-UAP则只需要1分钟，1000次迭代就能优化一个对抗扰动。由于其效率，本文剩余部分在限制无标签和无训练样本下，均使用CosSim来优化UAP。算法图如下

![image-20211113230327045](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113230327045.png)

### 无目标UAP的有趣的现象

若无其他说明，本文均使用VGG16作为受害模型来进行分析。

#### UAP导致绝大多数图像被分类成主导标签

[38]的解释假设误分类预测类别的频率与相应类别的区域有关。因此，做出了以下假设：a）输入空间区域是解释的唯一原因。

b）某个新类别的比重与其相应输入空间区域成比例。

ImageNet有1000个类，假设在图像空间中每个样本具有相同的区域，那么某个样本被随机选择分类成某个类别的概率是0.1%。作者发现在VGG16上，在UAP的攻击下（平均fooling rate是97.4%），90.1%的样本被分类成主导类别“brain coral”。给定上述假设，90.1%意味着在图像空间，相比于剩余的999个类别，类别"brain coral"占据了绝大部分。如果是这样的话，**大多数随机扰动也会导致误分类的样本到该主导类别**。在某个随机噪声下，作者观测到42.7%的fooling rate并且，50个样本中仅有0.1%的样本被分类成“brain coral”，这表明在图像空间中“brain coral”不是必要的标签。

**作者经验地发现一个决定因素主导类别是训练样本的类别**。作者仅**使用单个类别的样本基于Cosine-UAP优化UAP**。与使用所有类相比，将训练样本限制为单个类只会导致性能差距很小。不同训练的结果UAP导致相同的主导标签。作者通过改变用于训练的图像类别重复进行实验，作者发现了一种新的主导标签。这种现象进一步**表明了大部分空间区域不是主导标签的现象的主要原因**。否则，主导标签不应该随着选择的单个类别的改变而改变。

**一种替代解释**： “在图像空间中占据大区域”的假设并不能解释为什么 UAP 导致许多样本具有某个显性标签。这个现象很难通过在UAP影响下图像行为进行解释，如k(x)和k(x+v)，但是**k(v)和k(x+v)可以直观地解释这个现象**。

![image-20211113230527376](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113230527376.png)

作者在ImageNet验证集上验证了UAP，并且报告了k(v)的有序logits（图1中）和有序的k(k+v)（图1右）。可以发现这二者的分布几乎完全匹配，显示了图像x的扰动主导的logit分布。尽管幅度要小得多，但无目标的 UAP 掩盖了图像对 DNN 响应的贡献。结果特征向量ki(.)层是输入在模型的第i层触发的相应。作者计算ki(x)和ki(x+v)的余弦相似性，表示cosi(x, x+v)以及cosi(v, x+v)。我们通过使用余弦相似性采取广泛研究来研究和衡量相似性。余弦相似性的值范围为0-1，0（1）表示少的（大的）扰动对联合触发响应。

作者在图2中可视化了final logits层的简单性和结果。作者发现cosi(v,x+v)增加了训练优化过程。此外，最终fooling rate和cosi(v, x+v)的正向关系，与cosi(x, x+v)呈负向关系。

![image-20211114000144355](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114000144355.png)

### 使用有限或无数据创造UAP

##### 使用有限训练样本

用**GoogleNet作为目标模型**，数据样本大小为500，获得了30%的fooling rate。作者观察到使用仅有的数据无法达到使用全部数据的攻击性能。作者认识到主要原因是攻击成功率的减少是因为对有限数据的过拟合。一个方法是进行数据增强（随机旋转（5个角度）、随机 裁剪、随机水平或垂直翻转或）。表4显示了结果。

![image-20211114001502408](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114001502408.png)

##### 严格的无数据目标UAP

**人工图像的动机**：使用代理数据来生成目标UAP，在无目标设置中，代理数据集仍需要原始训练数据集。使用人工图像的一个问题是，训练样本与人工图像之间存在领域差异，会导致性能的快速下降。为了解决这个性能下降，人工图像需要两个属性来类似训练样本：

(a) 局部平滑，适合类似自然图像

(b) 混合频率图案来提升多样性

实验结果如图3和图4所示（图像扰动）

![image-20211114003216846](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114003216846.png)

对比实验结果如下表所示

![image-20211114003355139](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211114003355139.png)

**自适应输入尺寸**：实际上，一个在固定尺寸上训练好的CNN在推断时可以适用于不同的输入尺寸。以前的UAP没有考虑自适应的尺寸，因此fooling rate可能会因为输入尺寸的变化而受到影响。为了减少这个影响，**作者在训练的时候随机调整UAP的尺寸到[112, 336]，实验表明，这种操作增强了攻击能力，同时也会提升跨模型的迁移性**。如表7和表8.

使用FT或者SVD移除高频内容有益于促进性能。

##### 缺陷

+ 分割实验没有给出使用的数据集
+ 还是使用了数据，无论是使用了代理数据集

----

## 参考文献：

[1]: Seyed-Mohsen Moosavi- Dezfooli, Alhussein Fawzi, Omar Fawzi, and Pascal Frossard. Universal adversarial perturbations. In CVPR, 2017.

[2]: Konda Reddy Mopuri, Utsav Garg, and R. Venkatesh Babu. Fast feature fool: A data independent approach to universal adversarial perturbations. In BMVC, 2017.

[3]: Konda Reddy Mopuri, Phani Krishna Uppala, and R Venkatesh Babu. Ask, acquire, and attack: Data-free uap generation using class impressions. In ECCV, 2018.

[4]: Konda Reddy Mopuri, Utkarsh Ojha, Utsav Garg, and R. Venkatesh Babu. Nag: Network for adversary generation. In CVPR, 2018.

[5]: Omid Poursaeed, Isay Katsman, Bicheng Gao, and Serge Belongie. Generative adversarial perturbations. In CVPR, 2018.

[6]: Konda Reddy Mopuri, Aditya Ganeshan, and Venkatesh Babu Radhakrishnan. Generalizable data-free objective for crafting universal adversarial perturbations. TPAMI, 2018.

[7]: Hong Liu, Rongrong Ji, Jie Li, Baochang Zhang, Yue Gao, Yongjian Wu, and Feiyue Huang. Universal adversarial perturbation via prior driven uncertainty approximation. In ICCV, 2019.

[8]: Valentin Khrulkov and Ivan Oseledets. Art of singular vectors and universal adversarial perturbations. In CVPR, 2018.

[9]:  Co, Kenneth T., et al. "Universal adversarial robustness of texture and shape-biased models." *2021 IEEE International Conference on Image Processing (ICIP)*. IEEE, 2021.

[10]:Deng Y, Karam L J. Universal adversarial attack via enhanced projected gradient descent[C]//2020 IEEE International Conference on Image Processing (ICIP). IEEE, 2020: 1241-1245.

[11]: Zhang, C., Benz, P., Karjauv, A., & Kweon, I. S. (2021). Data-Free Universal Adversarial Perturbation and Black-Box Attack. In Proceedings of the IEEE/CVF International Conference on Computer Vision (pp. 7868-7877).

[12]: Zhang, Chaoning, et al. "Understanding adversarial examples from the mutual influence of images and perturbations." *Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition*. 2020.











[12]: 
