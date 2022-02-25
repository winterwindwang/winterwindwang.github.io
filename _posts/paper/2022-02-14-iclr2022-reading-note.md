---
1layout: post
title: ICLR2022对抗样本论文阅读笔记
category: 文献阅读
keywords: adversarial example, adversarial attack, transferability
tags: 对抗样本攻击，对抗样本的可转移性
---

## Transferable Adversarial Attack based on Integrated Gradients

一句话概述：

审稿地址：https://openreview.net/forum?id=DesNW4-5ai9

----

## Abstract

当前，**优化标准目标函数**，利用注意力图以及**平滑决策面**是三种用于构建对抗样本的常用方法。通过紧密集成者三种方法，作者在本文提出了一种新的简单的算法：Transferable attack based on Integrated Gradients(TAIG)方法，该方法可以为黑盒攻击找到高转移性的对抗样本。不同于以前的使用多个计算项或者与其他方法组合，TAIG将上述的三种方法集成到一项之中。本文研究了两种TAIG的版本在straight-line path和random piecewise linear path计算了他们的积分渐变。这两种版本均提供了很强的转移性并且可以与以前的方法一起工作。实验结果证明了TAIG优于当下的SOTA方法。

## Introduction

研究者发现一些对抗样本会共同存在于不同结构和参数的模型中[Papernot]。通过利用该属性，攻击者可以通过代理模型来生成对抗样本以攻击其他模型[liu]。这些共同存在的对抗样本有很多方面的优点，包括验证模型的鲁棒性，开发防御机制，以及理解深度学习。白盒攻击对于模型的验证以及开发鲁棒模型非常重要[Goodfellow]。

（第二段的写作逻辑）对抗样本通常被分为两种威胁模型：白盒攻击和黑盒攻击

+ 白盒攻击-->定义-->一般做法（citing）-->白盒攻击作用
+ 黑盒攻击-->定义-->分类：基于查询的方法，基于迁移的方法
  + 基于查询的方法-->一般做法-->缺点（大量查询，容易被防御）
  + 基于迁移的方法-->一般做法，阐明优点

然后说一些研究尝试结合二者来减少查询次数。最后说明本文研究重心是基于迁移的方法，因为其优点是practicality和its use in the combined methods。

第三段开始从产生对抗样本不同方法的角度讨论。**优化标准目标函数**，利用注意力图以及**平滑决策面**是三种用于构建对抗样本的常用方法。通常而言，基于不同方法的组合方法一起可以生成更强大的黑盒攻击。沿着这条线，作者在本文提出了一种新的简单的算法TAIG来生成高转移性对抗样本。本文与以前方法的根本区别在于TAIG使用单项来同时执行所有这三项方法。TAIG的两种版本TAIG-S,TAIG-R分别表示使用原始积分渐变，即直接路径计算的方式以及TAIG-R在随机对线性路径上计算积分渐变；同时TAIG可以与其他方法结合使用来进一步提升它的迁移性。【根据梯度集成的方式不同，分成了两种版本，那么梯度是怎么集成的呢？】

## 2 Related works

**Optimization approach**：主要使用代理模型的梯度来优化标准的目标函数，如最小化训练损失或最小化得分或干净图像的logits输出。著名的方法有PGD, FGSM, BIM, MIFGSM, C&W。他们通常被称为基于梯度的攻击。这些方法为白盒攻击设计，但通常被用于黑盒攻击中的back-edn组件。

**Attention modification approach**：假设不同深度网络在相同的图像上具有相似的特征。因而，通过修改干净图像中特征生成的对抗样本预期更具迁移性。如以下几种方法均使用注意力图来识别潜在的常见特征来攻击

+ JSMA：使用雅可比矩阵来计算它的注意力图，但是它的目标函数是不清晰的。

+ Attack on attention（AoA）：使用SGLRP[Iwana,2019]来计算注意力图

+ Attention-guided transfer attack(ATA)：使用目标函数相对于神经元输出的梯度来计算注意力图。

AoA和ATA通过最大化对抗样本和干净样本之间的注意力图来生成对抗样本。除了注意力项，AOA和ATA同样包含典型的攻击损失，如目标函数中的logit输出，并且使用超参数来衡量二者。

以下几种方法采用直接最大化干净样本与对抗样本之间的特征图的距离来优化对抗样本：Adversarial Perturbation(TAP), Activation attack（AA）, Intermediate level attack(ILA)。TAP和AA分别使用多层和单层特征来优化对抗样本。ILA通过在特定隐藏层增加扰动来获得更高的黑盒攻击的迁移性来微调对抗样本。

**Smoothing approach**：目的在于避免对代理模型的决策表面过拟合。基于平滑的方法可以分成两类。

+ 一类是使用来自决策表面的多个点的梯度。如Diverse inputs iterative method（DI），scale invariance attack（SI），translation-invariant attack（TI），smooth gradient attack（SG）， admix attack（admix），variance tuning（VT）。这些方法的大多数都通过计算增强图像的平均梯度来是实现平滑。
+ 另外一类修改梯度计算来估计一个平滑表面的梯度。如Skip gradient methods（SGM），Linear back-propagation attack（LinBP）。前者是特定为具有跳跃连接的模型设计的，如resnet。它迫使模型更多的使用跳跃连接而不是以非线性函数路由。后者采用了相似的方法，它正常计算前向损失，但在反向传播时跳跃了一些非线性激活层。通过逐渐缩小代理模型上非线性路径，从而使梯度在更加平滑的表面计算。

## 3 TAIG

### 3.1 符号和积分渐进

令$F:R^N \rightarrow R^K$表示分类网络将输入x映射成k个元素的向量，k个元素表示在输出的logits层中的第k个输出节点。令$f_k : R^N \rightarrow R$表示网络将x映射成第k个类别的值，如$F(x)=[f_1(x),...,f_k(x),..,f_K(x)]^T$，其中T表示转置操作。为了简化表示省略了下表k，k表示K中的任意类别或者类别标签是已知的。**x**和**x'**表示干净图像和对抗样本，而xi和xi'表示他们的第i个像素。**x**的类别标签为y。粗体的**x**表示图像，矩阵或向量，非粗体的符号表示标量。

**积分渐变[Sundararajan, 2017]是一种将深度网络的预测归因于其输入特征的方法**。有其计算的属性表示每个像素对网络输出的重要性，并且可以当作是注意力和显著值。积分渐变基于两个公理：敏感性(sensitivity)和实现不变性(implementation invariance)，并且满足其他两种公理：线性性（linearity）和完备性（completeness）。为讨论本文的TAIG，完备性是必要的。因此，作者将对积分渐变和完备性公理进行简单介绍。积分渐变是从参考图像r到输入图像x的渐变的线性积分。输入x中的第i个像素的积分渐变定义成

![image-20220215133340173](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215133340173.png)

其中ri表示参考图像r的第i个像素。在本文，参考图像r是一种黑色图片。

**完备性公理** 是指f(x)和f(r)的差异等价于$IG_i(f,x,r)$的和，即

![image-20220215133604643](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215133604643.png)

为了简化上述符号，$IG_i(x)$和$IG_i(f,x)$用于表示$IG_i(f,x,r)$，以及IG(x)$和$IG(f, x)$用于表示$[IG_1(f,x,r)...IG_N(f,x,r)]^T$，当f和r确定时。其他公理的详情和积分渐进的属性见[Sundararajan, 2017]。

### 3.2 TAIG-S和TAIG-R

TAIG-S/R均用于无目标攻击。S版本基于原始的积分渐进，直接在直线路径上执行渐进。攻击公式可以定义成

![image-20220215134127867](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215134127867.png)

其中积分渐进是由x的标签计算而来的，α控制了步骤。

第二个版本TAIG-R。令P是随机分段线性路径，x0,...,xE表示E+1个分段点，包括起始点x0和端点xE。从xe到xe+1的线性分段定义成$x_e+\eta \times (x_{e+1} - x_e)$，其中η在[0,1]。当计算线段的积分渐进时，xe用作参考图像，相应的积分渐进可以由公式1计算得到。整个路径的积分渐进可以定义成

![image-20220215134532794](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215134532794.png)

从随机分段线性路径计算得到的积分渐进被称为随机路径积分渐进（RIG）。注意TIG仍然满足完备性公理

![image-20220215134635047](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215134635047.png)

需要强调的是从其他路径计算得到的积分渐进仍然满足完备性公理。在本文，随机路径中端点xe由以下公式生成

![image-20220215134801528](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215134801528.png)

其中$e \in (0,1,...,E)$，v表示[-τ，τ]间的均价分布得到的随机向量。从而，TAIG-R的攻击等式为

![image-20220215134918358](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215134918358.png)

与PGD和BIM，TAIG可以迭代的形式进行。在TAIG中使用符号函数是因为x’和x是由无穷范数计算的。

### 3.3从三个角度看TAIG

直接从公式3和公式7中，优化，注意力以及平滑方法的集成并不明显。本部分首先从优化的角度解释TAIG，然后从注意力和平滑的角度解释。进队TAIG-S讨论，R的讨论相似。

使用完备性公理，f的最小化可以写成

![image-20220215135256302](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215135256302.png)

因为f（r）独立于x，所以可以被省略。同时对等式8的两边求导，得到ΔIGi(x)等于Δf(x)，这与在PGD和FGSM中使用白盒攻击的梯度一样。对于有ReLU的网络，它可以证明

![image-20220215135443079](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215135443079.png)

其中h>0。根据完备性公理，如果对抗样本x‘，其$IG_i(\bar x_{tar})=0$，那么有$f(\bar x_{tar})-f(r)=0$，其中r是黑色图像。换句话说，**对抗样本的网络的输出与黑色图像的输出是一样的**，这意味着对抗样本有很高的概率被误分类。在等式12中，等式右边表示分子两项的斜率。$IG_i(x)$和$IG_i(x-h\Delta x)$可以被当作当前x和目标对抗样本的第i个元素的积分渐进。为最小化公式8，作者寻找一个对抗样本，使得其积分渐进是0。因此，目标积分渐进设置成0，从而它不影响网络的输出。将$IG_i(x-h\Delta x)$设置成0，有

![image-20220215140237499](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215140237499.png)

假定h是正数，那么TAIG-S使用IG的符号，可以得到以下结论：（1）sign(IG(x))可以用于近似ReLU网络的符号sign(Δf)；（2）这个近似的质量取决于公式12，意味着sign(IG(x))和sign(Δf)不是非常接近。为保持Δf的符号用于最小化，使用了公式12的负向差而不是正向差。如果在公式12中使用了正向差，公式3中的项-α×sign(IG(fy,x))将会变成 +α×sign(IG(fy,x))，TAIG-S就会最大化f。更多详情见附录。

![image-20220215144229030](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215144229030.png)

图2展示了$||sign(IG(f,x))-sign(\Delta f)||_1$。平均二者的相似的元素有68%，这意味着IG弱近似于f。对于TAIG-R版本而言，相似元素仅有58%。因为弱近似，所以TAIG-S用于白盒攻击。作者认为TAIG能提升在黑盒攻击的对抗迁移性可以从注意力和平滑性二者讨论。

![image-20220215140849679](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215140849679.png)

从注意力角度，积分渐进识别出了深度学习用于预测的关键特征，所以分配了更高的积分渐进值给它们。如果网络被训练在相同的分类任务上，它们更倾向于使用相似的关键特征。在公式12，负向性的目标积分渐进被设置成0。**通过其积分渐进修改输入图像，关键特征也被修改了，从而迁移性得到了增强。**【解释很牵强？】为证明这些论点，图1展示了从不同网络中原始图像的积分渐进，图3显示了在TAIG-S/R前后的积分渐进。这些图表面：1）在相同图片上，不同模型具有相似的积分渐进；2）TAIG-S/R修改了积分渐进。

为了避免对代理模型决策面过拟合，基于平滑的修正是常用的办法。公式1展示了如果参考图像r是黑色图像，TAIG-S使用大量的增强。因为ri=0，xi大等于0，并且TAIG-S仅使用符号函数，公式1中(xi-ri)可以被忽略。因此公式3中的符号sign(IG(fy,x))取决于积分其中积分。这个离散版本显示，TAIG-S使用强度增强图像的梯度总和来执行攻击。类似的，从公式1，4和6中，TAIG-R使用噪声和密度增强来执行攻击。

![image-20220215144304253](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215144304253.png)

## 实验

# 攻击效果

![image-20220215144424156](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215144424156.png)

上表可以看出当扰动范围在10/255迁移性确实有很大幅度的提高。但是随着扰动值的变大，虽然迁移性会增加， 但是图像的质量也会相对的变差。

### 攻击防御模型的效果

![image-20220215145757320](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215145757320.png)

从效果看上，迁移性很高了，平均攻击迁移性提升幅度很大。

### 与现有方法的组合

![image-20220215145854313](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220215145854313.png)

结合现有的方法，也能大幅度提升其性能。

虽然其分析了很多积分渐进的可视化图，可说明TAIG方法可以改变关键点信息，但是并未给出攻击的对抗样本结果图。