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



# 【ICCV2021】Adversarial Attack on Deep Cross-Modal Hamming Retrieval

关键词：跨模态汉明检索攻击，对抗攻击在新应用领域的攻击

一句话概述：。

## 摘要

最近，跨模态汉明空间检索（CMHR）的关注逐渐增加，主要受益于 深度学习强大的表示能力。另一方面，深度学习的脆弱性暴露了跨模态检索系统的安全隐患。然而，**尚未有研究探索跨模态汉明检索**。在本文，作者针对深度跨模态汉明检索提出了一种有效的对抗攻击算法，AACH，在**黑盒设置**下欺骗目标深度CMHR模型。具体地，给定一个目标模型，作者首先构建了其替代模型来**利用汉明空间内的跨模块关系**，作者仅需对目标模型**进行有限的查询**即可实现黑盒攻击。进一步地，为增强对抗攻击的效率，作者设计了一种**元组构建模块来利用跨模态正例和负例**。使用这种方式，将正例推向负例方向来学得扰动，以欺骗目标模型。在三个广泛使用的跨模态检索基准证明了AACH的优越性。作者发现AACH可以在少量的交互下成功攻击目标CMHR模型，其性能与SOTA相当。

![image-20211212225029402](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212225029402.png)



## 方法

作者提出的框架如下图所示

![image-20211212225053077](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212225053077.png)

一般地，CMHR可以作为标量学习问题。给定一个跨模态数据集O，其中有M个实例，oi={oiv,oit}，分布表示两种数据模态（图像-文本对）。深度跨模态检索的目的是学习两个函数F分别处理两种不同网络，*表示将原始模态实例投影到汉明空间，其中的θ\*表示待学习参数。原始的跨模态实例可以表示成二值编码B\*∈{-1,1}K, K表示码长。可以重构成如下式子

![image-20211212225748218](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212225748218.png)

训练好的F总能保持准确的相似语义结构。具体地，与负例相比，假设图像查询实例O与正例更接近，因此鼓励F满足以下不等式

![image-20211212225950386](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212225950386.png)

其中D表示两种码之间的汉明距离

![image-20211212230035807](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230035807.png)

为实现跨模型汉明攻击，需要翻转公式2将正例推向负例

![image-20211212230118715](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230118715.png)

对于图像扰动，有如下优化目标

![image-20211212230219999](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230219999.png)

对于文本扰动，有如下优化目标

![image-20211212230227570](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230227570.png)

才外，作者用图像查询文本作为例子来训练替代深度跨模态网络，因此元组损失如下

![image-20211212230343987](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230343987.png)

β是人为设定的常数项边界。考虑到二值表示，为降低二值表示和二值码之间的定量损失，设计了如下定量损失

![image-20211212230528385](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230528385.png)

因此，训练替代图像网络由以下损失得到

![image-20211212230625993](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230625993.png)

训练文本网络的元组损失为

![image-20211212230621745](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212230621745.png)



作者提出的算法总结为

![image-20211212225118307](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212225118307.png)



## 实验

数据集：

+ MIRFlickr-25K：包括25000张图像。实验中，与[12]相似，使用了20015张图像-文本对，24个常见的标签。
+ NUS-WIDE：由260000图像-文本对。在删除无数据和文本信息后，得到190421，共21个最常见的类别。
+ MS COCO: 包括120000图像，每张图像由5个语义相关的句子描述。

对于图像，其输入为224x224x3。对于前面两个数据集，使用维度为1380和1000维度的bag-of-words。

### 实验结果

![image-20211212231055854](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212231055854.png)

从上表可以看出，

+ 随着学习迭代的增加，攻击能力得到极大提升
+ 与在MIR和NUS上的两个检索任务结果相比，对抗图像查询比对抗文本查询更强。一种可能的原因是bag-of-words中存在大量的0和1.
+ 对于MS COCO，作者用基于BERT的文本嵌入表示文本来研究词层级的攻击。展示出更强的攻击的攻击成功率。

如下图所示，**因为作者将对抗性扰动初始化为0，可以看出随着迭代次数的增加畸变逐渐变大而MAP分数变低**。这证明了作者的方法知道如何欺骗目标模型

![image-20211212231659557](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212231659557.png)

+ 作者还研究了不同查询量下AACH的性能。结果如下表所示

![image-20211212231921834](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212231921834.png)

随着对抗样本的数量从200增长到500，MAP大部分都下降了。下图展示了使用500词迭代生成500个对抗样本用于测试。曲线下的面积越大，查询返回的语义就越相似

![image-20211212232131547](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212232131547.png)

+ 图片可视化

![image-20211212232147181](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212232147181.png)



# 【ICCV2021】Meta Gradient Adversarial Attack

关键词：元学习

一句话概述：通过最小化白盒模型和黑盒模型之间的梯度方向来提升模型的迁移性。

不怎么样，符号都没说清楚，而且对比白盒攻击是没意义的，正常的白盒攻击效果都能达到100，黑盒攻击的效果不显著

---

## 摘要

虽然大多数基于迁移性的黑盒攻击都取得了成功，由于白盒和黑盒方法之间的差异，对抗样本的迁移性仍然受限。**受到元学习的概念的启发**，本文提出了一种新颖的攻击方法**Meta Gradient Adversarial Attack(MGAA)**，一种**即插即用**并且能**与现有任何基于梯度的攻击方法相结合**以提升跨模型的迁移性。具体地，我们从**模型池中随机采样多个模型来组合不同任务**，并且在每个任务中模拟白盒和黑盒攻击。通过约束白盒攻击和黑盒攻击的梯度方向，在黑盒设置下的对抗样本的迁移性可以得到增强。在CIFAR-10和ImageNet上的大量实验显示了我们的结构优于现有的白盒和黑盒的SOTA方法。

**疑问：怎么攻击？白盒黑盒是如何设定的？**

## 引言

**攻击和防御是两个互补的方向，在对抗攻击上的研究可以最终促进模型的鲁棒性，从而使模型更加可靠。**

![image-20211212214858206](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212214858206.png)

**MGAA的主要思想是通过迭代地模拟白盒和黑盒攻击来提升对抗样本的迁移性**。具体地，如图1 所示，在每次迭代中，从模型池中随机采样模型来构造一个任务，该任务可以分为meta-train和meta-test。**Meta-train首先使用多个模型的集成来模拟白盒攻击**，以获得临时的对抗样本，该对抗样本随后被作为meta-test的基础，以通过模拟黑盒攻击场景得到扰动。最终**在meta-test过程中获得的扰动被添加到前一次迭代得到的对抗样本上**。注意，作者提出的MGAA不需要训练额外的模型。

作者将对抗样本攻击分成以下四类：

**白盒攻击**：

**基于得分黑盒攻击**：假设攻击者可获得给定图像在目标模型上的输出预测值。ZOO[3]，N ATTACK[25], P-RGR[5]

**基于决策黑盒攻击**：仅能获得给定输入图像在模型模型上的预测类别，该攻击比基于得分的黑盒攻击更难。

**基于迁移黑盒攻击**：作者认为基于迁移性的黑盒攻击无法获得与目标模型的任意信息，因此是最具挑战的攻击。现有的方法通过提升迁移性来提高攻击成功率。[8,50,9,54,27]

**元学习攻击（Meta-Attack）**：使用元学习框架来训练梯度估计器。在基于得分的黑盒攻击中，仅需要少量的查询来微调梯度估计器来完成对抗攻击。

不同于Meta Attack，作者提出的MGAA实际上非meta-learning学习框架，仅仅受其启发。具体地，作者不需要额外地训练要给模型，而是直接地通过迭代来生成对抗样本。

## 方法

正如图1 所示，从模型池内采样T个tasks。对于每个任务，在meta-train步骤，作者模仿白盒攻击，并在meta-test step中模拟黑盒攻击，缩小白盒和黑盒设置下的梯度方向来提提升模型的迁移性。

具体地，假定模型池中有N个模型M1,M2,...,MN，在每次迭代中，作者随机选择n+1个模型来组成一个任务，每个任物均有meta-train和meta-test两个过程构成。在每次迭代的开始（假设是从第i个迭代），作者令签一次迭代xi得到对抗样本作为输入，标记为xi,0。

#### Meta-train

n个模型用于模仿白盒攻击。当使用多个模型集成执行的对抗攻击，作者采用了如[8]的方法。具体地，为攻击n个模型的集成，作者的集成策略为

![image-20211212221854866](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212221854866.png)

其中lks(xi0)是模型Mks的logits输出，ws是每个集成模型的权重ws>=0, sum(ws)=1。然后使用交叉熵损失来计算误分类损失

![image-20211212222016283](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212222016283.png)

1y表示y的one-hot编码。与常见的基于梯度的方法一样，对抗样本与最大化损失函数的方向更新。

![image-20211212222109862](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212222109862.png)

其中α是step size。meta-train迭代K次，下标j表示meta-train的迭代次数。注意，作者的框架可以与任何基于梯度的方法相结合，作者使用BIM算法。

#### Meta-test

在使用多模型集成来模拟白盒攻击后，作者使用最后一个模型Mkn+1来模拟黑盒攻击，其基于meta-train过程得到的对抗样本xiK。因此，搜先计算了交叉熵损失

![image-20211212222404859](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212222404859.png)

其中lkn+1(xi,k)是模型Mkn+1的logit。然后我们基于Xi,K沿着meta-test步骤的最大化损失函数方向更新对抗样本

![image-20211212222520517](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212222520517.png)

β是meta-test的步长。

为提升对抗样本的迁移性，作者将meta-test得到的扰动添加到前一次迭代xi得到的对抗样本上来更新对抗样本

![image-20211212222630905](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212222630905.png)

算法过程如下图所示

![image-20211212222700079](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212222700079.png)

### 讨论

+ 与Meta-Attack的讨论
+ 与现有集成策略的讨论：差异并未说清楚，且并**没有说清楚权重是如何设置的**。

## 实验

所使用的白盒模型和黑盒模型

![image-20211212223041806](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212223041806.png)

+ meta-train中迭代次数K的影响



迭代次数K越大，生成的对抗样本就更具攻击性。但是K越大，迭代时间就越长，因此在实验中作者采用了K=5的设置。

+ 采样任务T的数量的影响

采样的任务数越大，得到的成功率就越高。然而，迭代时间也就越长。作者使用T=40。

+ 集成模型n的数量

集成模型个数越大，白盒和黑盒攻击的成功率就越高。但是当n大于5时，性能的提升并不明显。因此，作者将集成个数n设置为5。

![image-20211212223603497](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212223603497.png)

### 无目标攻击

![image-20211212223702651](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211212223702651.png)

# 【ICCV2021】Augmented Lagrangian Adversarial Attacks

关键词：算法设计

一句话概述：

## 摘要

对抗攻击算法主要是惩罚方法，该类方法在实际应用中很慢，或更加有效的自定义距离方法，这些是根据所考虑的距离的属性量身定制的。作者提出了一种基于**增强拉格朗日原理**的**白盒攻击算法**来生成**最小扰动量对抗样本**。作者引入了几种对算法性能由关键影响的修改。作者的攻击具有**惩罚方法的一般性**以及**距离自定义算法的计算效率**，用途广泛。作者在3个数据集与几个模型上与SOTA攻击进行比较，一致性获得了有竞争力地性能，与更低地计算复杂度。

## 引言

基于惩罚的对抗攻击算法：将约束的优化问题转换成无约束问题。最近有研究者引入了距离约束，如SSIM，LPIPS和CIED2000[37]。基于惩罚的方法收敛速度慢[18]。此外，寻找合适的惩罚权重也不是简单的任务[19]。尽管惩罚方法可以取得较好的性能，但是他们需要大量的线性搜索来找到最优的惩罚权重。这**限制了他们用于训练对抗鲁棒模型和评估鲁棒性的效率**。现在有大量的算法提出用于提升攻击算法的性能。

在常见的优化问题中，能量增强拉格朗日原理具有很好的优势[2,3]。此外，乘数估计趋向于拉格朗日乘数，避免了惩罚方法中经常遇到的病态问题 [2, 10]。 最后，增广拉格朗日方法避免了明确求解对偶问题的需要，这与基本拉格朗日对偶优化不同，后者可能难以处理/不稳定非凸问题。

作者认为增强拉格朗日方法尚未应用到对抗攻击中。作者发现该方法在对抗攻击中可以取得较好的结果。为此，作者介绍了**几种算法修改来设计一种自定义的增强拉格朗日算法以实现对抗样本生成**。作者的修改包括

+ 带有扰动更新和乘子的增强拉格朗日内层和外层迭代
+ 放松了对内层收敛规则的需要
+ 介绍了一种乘子指数移动均值
+ 在距离函数中采用了学习率

CIEDE2000颜色差异是一种评估两种颜色之间感知差异的指标。被广泛地应用与评估显示和打印材质颜色的差异。该指标旨在对齐人类的感知。该颜色指标定义在两种颜色像素空间内。一般而言，

+ 小于1的值意味着颜色差异几乎不可感知
+ 1到2的值通过颜色检查可以检测出
+ 2到10的值是一眼就能看出差异
+ 大于10的值意味着颜色是不同的

## 方法

算法1如下图所示

![image-20211213002230161](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213002230161.png)

算法2为

![image-20211213002248498](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213002248498.png)

具体看论文

## 实验

数据集

+ MNIST
+ CIFAR-10
+ ImageNet

以上数据集均使用两种不同的迭代次数100，和1000

指标：攻击成功率

MNIST攻击结果

![image-20211213002421681](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213002421681.png)

鲁棒性曲线

![image-20211213002439535](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213002439535.png)

![image-20211213002455435](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213002455435.png)

CIFAR-10攻击结果

![image-20211213002533353](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211213002533353.png)

# 【ICCV2021】TkML-AP: Adversarial Attacks to Top-k Multi-Label Learning

一句话概述：

## 摘要

topk多标签相学习，对于任意输入，均返回topk个预测的标签，在图像标注、文档分析或网页搜索中应用的非常广泛。然而，这些算法在经过详细设计的对抗扰动下的脆弱性还未的得到很好的研究。在本文，作者提出了一种构建对抗扰动的方法，该方法可以用于攻击topk多标签学习图像标注系统。作者的方法考虑了**topk排序关系**以及**基于新颖的损失函数**。在大规模基准数据集上的实验证明了方法的有效性。

![image-20211220154802645](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220154802645.png)

## 引言

### Top-K 多标准学习

一个成功的top-k多标注分类方法应该在真实标签和输入的预测标签上保持一致性。如果真实标签和预测标签的尺寸不同时，情况将会非常复杂。因此，作者提出了以下规则：k>=|Y|，Y属于Y'（预测）。当k小等于Y时，预测Y’属于Y。换句话说，根据k与真实标签之间的关系来谁是谁的子集。作者将top-k标签的一致性得分定义成

![image-20211220161747454](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220161747454.png)

Ic表示当条件c是true时，值为1；否则是0。

### Top-k 和 Average Top-k

平均top-k的定义成

![image-20211220161958945](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220161958945.png)

引理1

![image-20211220162020062](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220162020062.png)

作者方法的贡献在于：

+ 提出了首个对TkML问题的无目标和目标攻击算法。
+ 作者的方法基于不可微分排序操作的重新构造（构造了一种替代不可微分排序算法的）。按照独立预测得分的目标函数是凸的，因此很容易优化。
+ 在大规模基准数据集上的实验确认了在攻击SOTA的TkML算法上的有效性。

## 方法

### 无目标攻击

![image-20211220162047868](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220162047868.png)

其中z是扰动信号。E（）在之前已经定义。由于k<=m-1，作者重写公式5成

![image-20211220162153389](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220162153389.png)

放松规则：

![image-20211220162209644](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220162209644.png)

真个算法如算法1所示

![image-20211220162254166](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220162254166.png)

### 目标攻击

将目标攻击定义成

![image-20211220162325207](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220162325207.png)

目标攻击的算法如下图所示

![image-20211220172456858](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220172456858.png)

## 实验

+ 性能比较

![image-20211220174747956](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220174747956.png)

+ 可视化结果

![image-20211220174832068](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220174832068.png)

![image-20211220174838507](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220174838507.png)

![image-20211220174854346](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220174854346.png)

+ 通用无目标攻击

![image-20211220174944990](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220174944990.png)

+ 目标攻击结果

![image-20211220174927341](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211220174927341.png)