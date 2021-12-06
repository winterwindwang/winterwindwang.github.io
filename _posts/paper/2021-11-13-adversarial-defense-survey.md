---
layout: post
title: 【文献调研】对抗样本防御调研
category: 文献阅读
keywords: adversarial examples, adversarial examples defense
tags: defense survey
---

# 对抗样本防御

对抗样本防御可以分为：

+ 预防
  + 对抗训练提升模型的鲁棒性
    + 对抗训练会引发一个问题，即**提升模型鲁棒性**的同时会**降低模型在干净样本上的准确性**。现在有大量的工作研究同时提高二者的性能
  + 设计一种预处理方法，消除对抗噪声
+ 处理
  + 分类
  + 认证



# 【ICCV2021】Removing Adversarial Noise in Class Activation Feature Space[1][1]

[code][https://github.com/dwDavidxd/CAFD]

---

### 摘要

基于预处理的防御方法可以通过处理输入来大量移除对抗噪声。然而，DNN很容易受到**错误放大效应**，特别是在不断发展的攻击面前。为了解决该问题，在本文，作者提出通过实现一种**在类别激活特征空间的自监督对抗训练机制**移除对抗噪声。特别地，作者首先通过**最大限度地破坏自然样本的类别激活特征**来**构建对抗样本**。然后，训练一个去噪模型来最小化对抗样本和自然样本在类别激活空间的距离。实验结果证明了与现有SOTA方法相比，该方法可以极大地地**增强对抗鲁棒性**，特别是对抗未知地对抗攻击和自适应攻击。

### 引言

有研究表明，基于预处理防御方法会**受到错误放大效应地影响**，即小地残差对抗噪声在目标网络的中间层被放大并且导致错误预测[23]。基于预处理的防御方法**对未见过对抗攻击效果有限**。在网络的high-level层中的权重特征往往会因为小的扰动而导致预测错误。这激发作者设计一种集中在权重特征上（类别激活特征）的防御方法，来解决这个错误放大的效应。为此，作者首先 提出了一种**基于类别激活特征的攻击(CAFA)**，然后，**训练了一个去噪网络，基于类别激活的去噪网络（CAFD）**来移除对抗样本中的噪声。最后引入了**一个判别器来通过提存纹理细节来确保恢复的图像与自然图像更接近**。

方法[49]通过从对抗噪声中解耦攻击不变的特征，来移除对抗噪声。

### 方法

本文方法是一种**基于预处理的防御方法**，它可以**消除错误放大现象并提供鲁棒性预测**。

#### CAFA

![image-20211113212601763](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113212601763.png)

其中$\Phi_x$和$\Phi_\hat{x}$是正常样本和对抗样本x‘的类激活特征，其中**$\Phi_x$表示对于某个类别，各个通道中的权重特征，即权重和特征的乘积，$\phi_x^{k}=f^k_x \cdot w^k_x$**。δ(.)表示L2范数距离指标。

![image-20211113212927159](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113212927159.png)

图3表明了fooling rate和特征距离显示出一种相同的趋势。这意味着破坏类别激活特征可以直接影响攻击效果。因此，最大化公式2可以构造强大的对抗样本和开发更有效的防御模型。

+ 给定自然样本x，首先将对抗样本x’初始化为x。
+ 然后，前向输入到预训练模型P中，得到相应的类激活特征。
+ 然后计算Δ的特征距离和它的梯度
+ 最终使用公式4和5计算梯度来优化对抗样本。

![image-20211113213213209](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113213213209.png)

### 去除对抗噪声（CAFD）

通过设计一个类激活特征去噪器C来移除对抗噪声。设计了**混合损失函数**来训练去噪器，由**类激活特征损失**和**对抗损失**组成。

+ 类激活特征损失定义如下

![image-20211113213422678](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113213422678.png)

其中$\Phi_C(\hat{x})$表示去噪后的对抗样本的类激活特征。由于**Φ是深度特征和类权重的点积，可以通过联合正常样本和对抗样本之间深度特征和类权重的距离**。即

![image-20211113214003330](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113214003330.png)

+ 对抗损失定义如下

![image-20211113214110434](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113214110434.png)

其中σ表示sigmoid函数，τ表示均值函数。最终的优化目标是

![image-20211113214156153](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113214156153.png)

算法流程如下图

![image-20211113214221103](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113214221103.png)

方法框架如下图

![image-20211113214239851](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113214239851.png)

给定训练样本X，首先从X中采样样本x，并使用CAFA为其构造相应的对抗样本。然后，将对抗样本输入去噪器C中并且计算损失Lcaf。接着，通过D前向传播C(x‘)，并且计算LD和Ladv。最终使用梯度不走更新C和D来最小化Lc和LD。重复上述步骤直至C和D收敛。

### 实验

数据集：SVHN和CIFAR-10。

去噪网络结构（C）参考的是:DUNET[23]

判别器网络（D）参考的是：VGG

下表是量化对比结果

![image-20211113214851341](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211113214851341.png)

# 【ICCV2021】Defending against Universal Adversarial Patches by Clipping Feature Norms

动机：图像中通用对抗补丁的区域，其深层特征的范数通常高于其他区域。

方法：

---

## 摘要

基于**通用对抗补丁**物理世界对抗攻击已经被证明有能力误导CNN，暴露出基于CNN的真实世界分类系统的脆弱性。在本文，作者**经验性地**揭露以及**数学上解释**了通用对抗补丁通常会导致常见**CNN中的深度特征出现更大地范数**。受该思想的启发，作者提出了一种但是有效的防御方法，即使用一种新特征范数裁剪（Feature norm clipping， FNC）层（是一种可微分的模块），可以灵活的插入不同的CNN中来自适应地抑制large norm深度特征向量地生成。FNC的引入不需要训练参数，仅需要少量的计算资源。然而，在多个数据集上的实验验证了它能有效地提升不同CNN模型对于白盒通用补丁攻击的鲁棒性，同时维持较高的干净样本识别准确率。

作者提出的框架图

![image-20211123151826124](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211123151826124.png)

## 引言

尽管通用对抗攻击对真实世界应用的威胁是明显的，但是在防御通用对抗补丁攻击的研究仍然较少。以前的一些防御方法，包括数字水印（DW）[5]，局部梯度平滑(LGS)[13]，均基于经验线索的补丁检测。缺乏理论发现，他们的防御方法在面对白盒自适应攻击时性能会大打折扣，白盒攻击中防御策略是对攻击者透明的。基于对抗训练需要额外的计算和认证的对抗防御方法需要定义阈值（在使用不同网络结构时，阈值可能不一样）。

在本文，作者**基于数学分析通用对抗补丁深层特征表示**，提出了一种有效地抵抗通用对抗补丁攻击的防御方法。**通常而言，通用对抗补丁仅占据图像的少量区域。然而，不管将其它放置在何处以及放在什么图像上，均能使CNN预测错误的类别**。实验观察揭露了**当对抗补丁图像被输入到CNN中，空域上放置对抗样本的区域的特征向量的范数大于其他区域特征向量**。考虑到被广泛采用的**平均池化策略，大的特征向量范数将对池化结果的方向有主导性作用**，无论其他的特征向量如何。这种现象仅存在在当对抗扰动所在区域（如对抗补丁）。这可以通过以下事实来解释：实际 CNN 的有效感受野 (ERF) 呈指数中心并且明显小于理论感受野[11]。因此，对抗补丁在特征图上的影响是空域集中的。这意味着大多数特征向量将不会被影响，不可避免地导致有对抗补丁区域的特征向量的范数较大。

贡献：

+ 分析了通用对抗补丁在深度特征表示上地影响，并且数学地解释了在补丁区域大范数特征向量的存在。
+ 提出简单有效的对抗样本防御方法，使用FNC层约束大范数特征向量的生成以弱化对抗补丁的影响。
+ 在不同网络和数据集上，取得了对抗准确率的提升。方法优于SOTA方法。

通用对抗补丁位于不同位置时的特征范数图

![image-20211123161103651](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211123161103651.png)

位于右下角时

![image-20211123161125130](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211123161125130.png)

方法涉及太多公式，若有需要看原文。

## 实验

通用对抗补丁方法：

通用对抗补丁方法：**Adversarial Patch**[2]，**LaVAN**[8]，ImageNet上的目标攻击是`toaster`，CIFAR10上的目标攻击是`dog`。消融实验中，目标攻击方法具有相似的结果。

对比实验结果如下图所示：

![image-20211123162805362](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211123162805362.png)

# 【ICCV2021】Adversarial Attacks are Reversible with Natural Supervision

关键词: 寻找**加性扰动来修复输入**。图片预处理消除对抗攻击性。

一句话概述：作者发现对抗样本不仅能使CNN分类出错，而且还会破坏图像的内在结构。为此，作者提出通过**约束干净样本和对抗样本的对比嵌入距离（constrastive embeddings）**使模型学习到图像的内在结构，从而提升模型的鲁棒性。具体操作上，作者设计了一个两层的的生成器，使用对抗样本和干净样本作为训练数据，使用对比损失函数进行训练。训练完后，作者直接使用该生成器G生成repair噪声。

---

## 摘要

作者发现**图像的内在结构可以使很多对抗攻击无效(reversal)**。攻击向量不仅使图像分类器出错，同时**破坏了图像的附带结构（incidental structure）**。作者证明了**修改对抗样本来恢复图像的自然结构可以使很多攻击无效**，提供了一种新的防御方法。实验证明了在CIFAR-10, CIFAR-100, SVHN和ImageNet数据集上的取得了优于SOTA方法的鲁棒性。作者表示，在攻击者知道该防御机制的情况下，作者的防御方法仍然有效。这是因为作者提出的方法部署在推理阶段，该方法还能很好地与预训练模型和其他防御方法相结合。**作者认为深度神经网络容易受到对抗样本的影响的部分原因是它们的表示没有强制学习图像的自然结构**。

## 引言

作者表示基于对抗训练的防御方法在测试阶段对新的攻击方法的防御效果有限。为此，作者提出了一种翻转攻击过程的方法，该方法可以在测试阶段自适应各种攻击。正如**攻击者寻找加性对抗扰动来破坏输入**，作者的方法寻找**加性扰动来修复输入**。下图是作者方法的构想

![image-20211127165216170](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127165216170.png)

作者的方法的关键思路在于，**利用图像的自然和内在结构来逆转对抗攻击**。作者提出的框架的关键优势在于**从视觉表示的角度来设计防御策略**。

## 方法

方法框架的结构图如下

![image-20211127165924923](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127165924923.png)

具体部署环境中，测试条件下由于真实标签y是不可得的，因此采用自监督的目标函数实现，因为他不依赖于真实标签y。因为对抗攻击旨在破坏分类器，同样**会破坏自监督表示，作者就是利用该信号来实现逆转**。为此，作者通过优化以下目标函数创建了修改(repair)加性噪声。

![image-20211127170136771](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127170136771.png)

其中，xa是生成的对抗样本，r是repair 加性噪声。对抗样本加上repair噪声r后，应该没目标网络识别正确。

反向攻击的一个优点是，由于它们不依赖于离线对抗训练，防御将推广到看不见的对抗性攻击。 此外，我们的防御能够在不重新训练的情况下强化现有模型。

### 如何构建并优化repair噪声？

对比目标创建了最大化正例对之间一致性的特征，同时最小化负例对之间的一致性。当接收到潜在的对抗样本x时，作者通过采样不同的增强来创建正例以构建多个正例。以相同的方式构建负例。在验证期间，这些正负例可以通过最小化以下的目标函数来创建负例

![image-20211127171129979](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127171129979.png)

z是对比特征。yij(s)=1表示样本i和j都是来自x，否则就是来自不同的域。cos表示余弦相似度，τ表示标量超参数。

![image-20211127172149418](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127172149418.png)

上图显示了在分类目标函数上的对抗攻击同样会影响对比目标函数，尽管攻击者并没有想这么做。当出现对抗攻击时，Ls(Xa)就会大于干净图像的Ls(x)。算法1总结了作者算法

![image-20211127172349090](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127172349090.png)

为了估计对比特征z，作者使用模型F中logit前面一层的特征，并将其输入一个两个网络层的网络G中。线下，作者采用与[10,11]一样的方法优化对比模型G。采用了以下两种策略训练模型G。

+ 随机裁剪然后缩放回原尺寸
+ 随机颜色失真，包括jittering和随机灰度

作者发现，移除从数据增强中移除高斯模糊可以提高性能，因为其会导致过模糊。

最后，作者使用训练好的G直接修改图像。

省略理论分析结果

## 实验设置

数据集：CIFAR-10, CIFAR-100, SVHN, ImageNet。

对比的防御方法：

+ TRADES
+ Roust Overfit (RO)
+ Bag of Tricks(BagT)
+ Semi-supervised Learning（Semi-SL）
+ MART
+ Adversarial Weight Perturbation (AWP)
+ Fast is Better than free (FBF)

攻击方法

+ FGSM
+ BIM
+ PGD
+ C&W
+ AUTO Attack
+ Defense Aware Attack

实验设置

+ Backbone结构：Pre-ResNet18, ResNet50, WideResNet
+ self-supervised Learning Branch：两层全连接层的网络

### 实验结果

对比实验结果

![image-20211127175150133](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127175150133.png)

收敛速度与鲁棒性指标的对比

![image-20211127175337345](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127175337345.png)

消融实验

![image-20211127175645899](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127175645899.png)

---

# 【ICCV2021】Robustness and Generalization via Generative Adversarial Training

关键词：借助**生成模型生成尽可能真实的对抗样本**。

一句话：首先利用StyleGAN获得的潜在变量z，然后将其输入8层MLP网络并得到特征表示w，然后使用仿射变换对w进行处理作为**风格特征**，与经过缩放操作B后的**噪声特征**作为**合成网络**的输入，然后构建多样化的对抗样本。

---

## 摘要

当前的防御方法仅对参与训练的对抗攻击样本有效，对其他新的攻击方法的可能无效。此外，这些方法通常会降低模型在干净样本上的性能，并且无法泛化到新的对抗样本上。在本文，作者提出了**生成对抗训练**，即同时**提升模型在测试集上和OOD样本上的泛化性**，以及**对未见过对抗样本的鲁棒性**。与修改low-level预定义的图像不同，作者用一解耦潜在空间的生成模型生成了low-, mid-, high-level的修改。通过这些，使用这些对抗样本可以使模型经受住各种攻击。作者表示不仅是提升模型在干净样本和OOD数据上的准确率，还能使其对未见过对抗样本更加鲁棒。作者验证了分类、分割和目标检测上的效果。

## 引言

附加扰动使图像的高级语义方面完好无损。 因此，针对这些示例训练的模型不能提供对高级输入变化的鲁棒性。为了使模型对所有现实的变化输入更具鲁棒性。尽管考虑其他几种攻击类型的集合，但是模型仍容易受到未知攻击的影响。为提高对未知攻击的鲁棒性，作者提出利用对抗训练来抵抗一系列对输入的low-, high-level的变换。作者利用具有解耦潜在表示的生成模型来系统地合成多样化和真实样本，可以不需要利用自然图像的流形。

作者通过修改不同分辨率下的潜在变量来构建粗粒度和细粒度的对抗扰动。预训练的生成模型用以约束对抗样本的搜索空间来保证生成对抗样本的真实性，从而揭露了目标模型在自然图像空间的脆弱性。作者的贡献如下：

+ 提出生成对抗训练来同时提高模型在干净样本和未知样本上的泛化性以及在未知对抗攻击上的鲁棒性。作者的方法基于细粒度的无限制对抗样本上训练，攻击者控制图像修改的比例，以生成多样化的数据集，流形上的例子。
+ 作者评估GAT面对不同对抗攻击的效果，recolor，空域转换，感知以及加性噪声。
+ 作者将方法拓展到语义分割和目标检测任务中，并提出针对分割和检测生成无限制对抗样本。**使用这些样本训练可以提高模型在干净样本上的准确率和提高对未知攻击的鲁棒性**。

## 方法

现在对抗攻击方法主要是集中修改图像的low-level，因此，经过对抗训练的模型仍然容易受到攻击。

#### 分类

风格化变量由风格变量表示，随机细节由噪声变量捕获。作者提出提出以控制的方式来修改图片，提供一种生成细粒度无限制攻击的新方法。

作者表示映射函数f和合成网络g表示style-gan。如下图所示

![image-20211127221755004](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127221755004.png)

映射函数是一个由8层MLP，其输入是潜在编码z，并且生成中间潜在特征w=f(z)。这个向量然后通过学习的仿射变换 A 专门用于样式变量 y，它控制合成网络 g 的每个卷积层之后的自适应实例归一化操作。噪声输入是单信号通道图像，由无关联的高斯噪声构成，然后未入到合成网络的各层。学习到的预特征缩放因子B用于生成噪声变量n，然后添加到卷积层的输出。合成网络的输入是风格y以及噪声n，生成图像x=g(y,n)。作者将生成的图像送入预训练的分类器F。作者的目的是寻找细小的修改x，从而修改F的预测结果。作者使用初始化对抗风格和噪声变量y0=y,n0=n，并且迭代更新以以欺骗分类器。对于无目标攻击，采用以下策略来更新风格和噪声变量。

![image-20211127224011924](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127224011924.png)

对于目标攻击，优化策略如下

![image-20211127224123322](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127224123322.png)

对于语义分割和目标检测的框架如下

![image-20211127224400527](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127224400527.png)

作者通过选择模型的特定层来控制扰动的修改粒度。修改顶层网络层，与粗粒度空间分辨率相关，得到high-level改变。换句话说，修改lower layer，就是修改细粒度。

## 实验

无目标攻击效果图

![image-20211127224925202](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127224925202.png)

对比结果

![image-20211127224955171](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211127224955171.png)

作者表示使用作者生成的对抗样本，可以提高模型在干净样本上的准确率。

# 【ICCV2021】Learnable Boundary Guided Adversarial Training

关键词：引导学习

一句话：use the model logits from one clean model to guide learning of another
one robust model。约束对抗样本在鲁棒模型上logits的输出与干净模型在干净样本上的logits输出，以保证干净样本在鲁棒模型上的准确率。

## 摘要

以往的对抗训练方法都是以牺牲模型在自然图像上的准确率作为代价来提升模型的鲁棒性。作者**使用干净模型的logits来指导其他鲁棒模型的学习**，考虑训练好的干净模型上的logits可以表示最具去区分性的自然数据特征，如泛化分类边界。作者的解决方案是约束鲁棒模型的训练，即保证对抗样本输入到鲁棒模型中的logits与正常样本输入到干净模型中的logits尽可能类似。这将使得鲁棒模型继承干净模型的分类边界。此外，作者还发现这种边界指导不仅能保持干净样本上的准确率，而且还有助于提升模型的鲁棒性。最终，在CIFAR-10，CIFAR-10、TinyImageNet上的结果表明方法的有效性。其中在CIFAR-10上，在不需要额外真实或合成数据的情况下，性能优于auto-attack。

## 引言

现有的对抗防御方法：

+ 防御蒸馏
+ 特征压缩
+ 基于随机方法
+ 对抗训练

传统的对抗训练会降低鲁棒模型在干净样本上的准确率，如下图所示

![image-20211128000909234](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128000909234.png)

作者的灵感基于下图

![image-20211128000945759](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128000945759.png)

作者的动机是利用干净模型Mnatural来提升Mrobust在干净样本上的准确率。

## 方法

为此，作者构建了如下的损失函数

![image-20211128001312729](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128001312729.png)

其中，σ是softmax激活函数，CE是交叉熵损失。作者使用MSE损失作为L的损失，β为1。

提出的算法如下

![image-20211128002725583](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128002725583.png)

### 模型的灵活性

作者为对抗训练提供了一种新的模式。这与现有的攻击方法不冲突。如与其他方法结合有

+ 与Adversarial Logit Paring

![image-20211128003023745](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128003023745.png)

+ 与TRADES

![image-20211128003017638](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128003017638.png)

## 实验

消融实验

+ Natural Classifier Boundary Inheritance

![image-20211128003356548](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128003356548.png)

ACCn是自然图像的准确率，ACCr是鲁棒模型的准确率

+ Feature Visualization（可视化方法是什么？）

  随机在CIFAR-100中选择5个或20个类。图中的是class indexes。 每个采样的类别，作者收集了干净图像和相应对抗样本的logits特征（如何画成图呢？）。

![image-20211128003430192](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128003430192.png)

+ Separate Batch Normalization

![image-20211128003928735](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128003928735.png)

+ Effectiveness of Our Method

![image-20211128003947317](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128003947317.png)

+ 方法之间的对比

![image-20211128004019114](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128004019114.png)

![image-20211128004051332](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211128004051332.png)

不足之处：没有指明鲁棒模型和干净模型训练的模型。

---

# 【ICCV2021】Can Shape Structure Features Improve Model Robustness under Diverse Adversarial Settings?

关键词：**提升模型鲁棒性**的同时维持模型**在干净样本上的准确率**。具体来说，利用边缘图片来提升模型的鲁棒性；但是仅用该方法不仅仍对自适应攻击脆弱，而且还会降低干净样本的准确率。对于第一个问题，作者提出了一种鲁棒的边缘检测算法。对于第二个问题，作者还提出，使用GAN边缘图像进行重建，然后也用来训练模型，这样既提高了模型的鲁棒性，也在一定程度上维持了模型在干净样本上的鲁棒性。

一句话概述：着重解决模型的鲁棒性，但不可避免会造成干净样本精度的下降。为此，作者基于边缘结构特征设计了两个网络：NetRob，用以提升模型对对抗样本的鲁棒性，但是仅使用该网络会降低干净样本的精度；为此，作者还提出使用GANRob来填充边缘以重建图像提升模型在干净样本上的准确率。**注意：作者并没有使用对抗样本进行训练，而是从数据和网络训练的角度提升模型的鲁棒性**

## 摘要

受到**人类视觉系统**在识别物体时更多注意**全局结构（如形状）**，而**CNN**偏向于使用**图像中的局部特征**的启发，在本文作者旨在分析**“边缘特征”**是否可以**提升这些场景下识别的鲁棒性**，如果可以，能提升多大程度？为挥发这些问题并且系统地评估全局结构特征，作者聚焦于形状特征并且提出了两个基于边缘特征的方法，EdgeNetRob以及EdgeGANRob，逼迫CNN更多地依赖于边缘特征。具体地，上述两个网络首先显示地从使用**边缘检测算法提取给定图像的形状结构特征**开始。然后，NetRob直接在边缘特征上训练下游学习任务，GANRob则利用一个GAN模型来填充边缘信息以重建图像。为了减少边缘检测算法对扰动的敏感性，作者发现NetRob有助于促进网络以牺牲一定的干净模型准确率的条件下在不同攻击下的鲁棒性，另一方面，GANRob可以用于提升模型在干净样本上的准确性。这表明，给定这些边缘特征，如何利用他们来确保模型的鲁棒性，同样也依赖于数据属性。在不同设置下边缘结特征上的系统研究将为未来的鲁棒特征探索和优化提供一种新思路。

## 引言

Baker[2]和Geirhos[16]显示人类在识别时主要依赖于全局目标形状而不是局部图案（如纹理），但是CNN 更多地聚焦于后者。

作者指出：边缘特征有两个优点：1）有效地建模形状；2）有很多算法可以方便地提取图像的边缘。

作者提出方法的结构图

![image-20211129093619380](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129093619380.png)

正如上图所示，EdgeNetRob是一个简单但有效的方法，他通过边缘检测算法来提取结构边缘信息，用以训练分类器。结果CNN在预测时只依赖于形状信息，而不是纹理或颜色，从而消除了纹理偏置。与基于对抗训练的方法相比，EdgeNetRob更加通用和有效，因为其不需要在训练的时候生成对抗样本。然而，NetRob的一个潜在问题就是会降低CNN在干净样本上的准确率。为此，作者提出GANRob填充边缘结构特征图以重建图像的纹理或颜色。

为提取边缘信息，作者首先使用了Canny和RCF（基于网络）边缘提取算法。但是作者表示简单地使用这二者容易受到对抗攻击的影响。为此，作者提出了一种改进的鲁棒边缘检测算法，Robust Canny。

## 方法

### 鲁棒边缘检测算法

+ 噪声降低：高斯滤波平滑输入图像
+ 使用sobel算子计算平滑图像中每个像素的梯度强度和方向
+ 噪声masking：设置阈值α来减少对抗扰动中的噪声
+ 非极大值抑制：沿梯度方向未达到最大值的梯度幅值将被抑制
+ 双阈值：在NMS后，对梯度幅值应用下届和上界阈值（θl,θh），像素被映射成3个level：强、弱、无边缘像素
+ 通过滞后进行边缘跟踪：对强像素搜索以检测边缘像素，或连接弱像素。

如下图所示，噪声对计算图像梯度后的原始Canny算法有较大影响

![image-20211129095406338](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129095406338.png)

### EdgeGANRob

作者采用pix2pix作为GAN模型的基本框架[26,56]，并使用如下的目标函数进行优化

![image-20211129095556693](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129095556693.png)

其中，Ladv和LFM表示对抗损失以及特征匹配损失的梯度。

然后，作者希望分类器能在GAN生成的RGB图像上有较高的准确率。最后通过以下的目标函数来训练两个网络

![image-20211129095805562](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129095805562.png)

注意作者首先训练了GAN，然后在GAN训练的比较好的时候才开始联合训练这两个网络。

个人思考：需要对比使用常规数据训练的网络在不同攻击下的效果，然后指出使用作者方法的效果。

作者在验证了他们的方法在以下几种攻击/变换下的鲁棒性

+ 对抗攻击
+ 常见破坏
+ 后门攻击

## 实验

数据集：

+ 清晰不含歧义的数据集：Fashion MNIST, CelebA
+ 不那么清晰，含有歧义的数据集：CIFAR-10, Tiny-ImageNet， ImageNet

### 不同边缘检测算法的结果

![image-20211129101526721](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129101526721.png)

与使用原数据集的对比结果

![image-20211129101710993](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129101710993.png)

对抗攻击下的结果

![image-20211129101938867](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129101938867.png)

与常见的破坏操作的对比

![image-20211129102309792](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129102309792.png)

**从实验结果看，相比于对抗训练，作者的方法优势不是很大。并且提出的分类模型也没说是什么模型，是自己设计的还是说使用的是常见的模型。**

# 【ICCV2021】Reliably fast adversarial training via latent adversarial perturbation

关键词：潜在特征梯度来实现单步对抗训练。

一句话概述：作者表示利用潜在表示的梯度作为对抗扰动，可以实现l1范数的正则化，从而提升模型的鲁棒性。本文的焦点是trade-off对抗鲁棒性与计算代价（对抗训练的速度）。感觉是向网络的中间层注入了噪声，从而来提升模型训练的鲁棒性？

---

## 摘要

多步对抗训练被广泛地作为一种有效的防御强大对抗攻击的方法，与标准的对抗训练相比，其计算非常昂贵。一些单步对抗训练方法被提出来消除上述的代价。然而，在优化设置上，他们的性能不够足够可靠。为了克服这种限制，作者跳出了现有的基于输入空间的对抗训练范式，并且提出了但不潜在对抗训练方法（SLAT），即**利用潜在表示的梯度作为对抗扰动**。与现有的单步对抗训练方法相比，作者证明特征梯度的l1范数通过采用潜在扰动实现**隐式地正则化**，从而恢复局部线性并确保可靠的性能。因为潜在扰动基于潜在特征的梯度，该梯度可以通过输入梯度计算的过程得到，提出**方法的代价与快速梯度符号相同**。实验结果证明，提出的方法尽管结构简单，仍优于现有的SOTA加速对抗训练方法。

## 引言

作者指出FreeAT与PGD AT（Fast AT）存在与FGSM AT相同的灾难性过拟合问题，即在一个epoch的训练中模型突然会失去其鲁棒性，Andriushchenko[2]表示这可能是由于神经网络的局部线性性，Kim[3]发现在丢失其鲁棒性后，若使用简单的多步学习率会导致训练最终崩溃。

这些在AT中的问题激发作者探索一种新颖的方法来提升单步AT的可靠性，一种时间高效的训练。

作者表示其贡献在于:

+ 作者通过采用潜在对抗扰动，提出了的**神经网络的局部线性可以正则化**，而无需要求训练时间。作者表示不同于梯度对齐正则化[2]，从而h实现训练时间比FGSM少三倍。特别地，作者证明了特征梯度的l1范数是隐式正则化，通过引入潜在对抗扰动，这有拉近了神经网络的损失函数与一阶近似的的距离。因为潜在对抗扰动被跨多个潜在层采用，可以期望增效的正则化影响。
+ 其次，作者证明SLAT由于SOTA的加速对抗训练方法，且性能与PGD AT相当。

与其他方法如可证明的防御[37,42,6]，标签平滑[24]，mixup[43]，雅可比正则化[13]相比，基于PGD的对抗训练被认为是最有效的对抗防御方法，然而由于生成对抗样本的复杂性大大降低了基于PGD的AT方法的可用性。尽管有方法尝试解决这个问题，但是大多都以来输入扰动，作者则**探究了使用潜在对抗扰动实现有效AT的可能性**。

尽管有一些很精彩的工作[39,36]激发了将潜在特征在的对抗鲁棒性中的重要性，仅仅有少量的工作直接利用潜在对抗扰动来实现AT。Sankaranarayanan[23]使用从最小批中计算而来的潜在特征的梯度来进士内层最大化的结果。尽管该方法相对于基于FGSM的AT在鲁棒性上有一定的提升，但是他们也没法直接证明来自最小批中的梯度的对抗扰动是最优的。[27]建议使用潜在对抗训练来fine-tuning对抗训练的模型，其性能与多步PGD AT的相似。与此相反，作者的方法集中降低AT的计算带代价，通过潜在对抗扰动。

**潜在对抗扰动是什么？如何得到？如何用以提升模型的鲁棒性？**

## 方法

### 符号定义

使用$f_l ( \cdot )$表示第l层，$h_l ( \cdot )$表示给定样本x的潜在表示向量。有$h_o ( \cdot )=x$。更准确的有

$h_l(x)=f_l(f_{l-1}(...(f_1(x))))=f_l(h_{l-1}(x))$，其中$f_l(h_{l-1}(x))= \phi(W_l(h_{l-1}(x)+b_l)) $，给定第l层的权重矩阵Wl，偏置向量bl，以及激活函数Φ。给定x，对于第k层的对抗扰动标记为$\delta_k(x)$，其中$k \in K$，其中K表示注入对抗扰动的层索引的子集合，第0层表示输入层，对抗扰动为$\delta={\delta_k(x)}_{\forall k \in K}$。

为检查潜在对抗扰动的边缘化效应，作者定义了在L-1层的累积扰动为$\hat{\delta}_{\ell-1}(x)$，其源自δ的前向传播。实际上，每个潜在对抗对抗扰动都被应用了level-wise。因此，有$h_{l+1}=f_l(h_l(x)+\delta_l(x)),\forall l\in K$。

优化参数集合θ*和对抗扰动δ\*可通过以下方式解决

![image-20211129123623451](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129123623451.png)

在给定高级非线性函数f(.)时，以封闭的形式获得累积扰动是困难的，因此作者通过合理的假设扰动足够小来近似它。近似基于[4]，即检查高斯噪声注入（GNI）到多个潜在层的效果。在L-1的扰动积累可以表示成（详细的证明在附录）

![image-20211129130330692](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129130330692.png)

损失函数的线性近似为

![image-20211129130405407](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129130405407.png)

可以得到潜在对抗扰动的隐式正则化

![image-20211129130444209](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129130444209.png)

为了更好的线性近似，线性近似错误可以表示成

![image-20211129130536820](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129130536820.png)

其中ε是足够小的任意扰动。Rk(x)包括二阶导数项。通过低秩Hessian矩阵的进士，二阶扰动项的上界为

![image-20211129130810058](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129130810058.png)

作者进一步促进了特征梯度正则化与对抗损失最小化之间的密切合作，由潜在对抗扰动δk(x) 引起损失项中细微的变化

![image-20211129131206628](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129131206628.png)

提出的算法如下

![image-20211129131832701](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129131832701.png)



## 实验

#### 在Toy数据集上的实验

作者首先在简单的二分类问题上验证了方法的有效性。从2D高斯分布生成样本(x,y)。从下图可以看出，潜在对抗扰动有助于筛除非鲁棒特征

![image-20211129131734466](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129131734466.png)

### 鲁棒性的比较

![image-20211129131900332](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129131900332.png)

### 消融实验

标准训练以及鲁棒训练的准确率比较

![image-20211129131917813](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129131917813.png)

CIFAR-10中使用不同网络层嵌入对抗扰动的影响。

![image-20211129131927300](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211129131927300.png)

# 【ICML2019-TRADES】Theoretically Principled Trade-off between Robustness and Accuracy





# 【ICML2021】Towards Defending against Adversarial Examples via Attack-Invariant Features

关键词：预处理对抗样本防御方法，神经网络消除对抗噪声

一句话介绍：设计了一种自编码器来消除对抗噪声。具体地，作者设计了一种对抗攻击分类器，来约束编码器学习消除对抗噪声的影响，此外使用了归一化器约束了中间特征（自编码器的bottleneck）。然后将消除完对抗噪声的图像输入预训练的模型，将其损失用以约束对抗噪声。

---

## 摘要

因为对抗攻击在不断的发展，在这些已经出现的对抗样本上训练的模型可能无法泛化到未见过的对抗样本上。为了解决该问题，在本文，作者提出了通过学习**跨攻击方法泛化性不变特征**来**移除**对抗噪声，它能保持语义分类信息。具体地，作者引入**对抗特征学习机制**从对抗噪声种**解耦不变特征**。一个**规范化项**被用于攻击不变特征编码空间来解决在seen和unseen攻击之间的偏差问题。实验证明，与现有的SOTA方法相比，作者的方法可以**提供更好的保护**，特别是在**unseen类别的攻击**和**自适应攻击**上。

## 引言

作者的工作灵感来自于**认知科学**。具体地，认知科学认为我们能识别人脸，即使人脸显示差异或者未见过的表情，这是因为我们的大脑擅长提取不变的脸部特征。相似的，人类不容分辨自然样本与对抗样本的原因是我们关注不变特征，这些不变特征表示语义分类信息，忽略对抗噪声。

在本文，作者提出利用攻击不变特征（attack-invariant feature, AIF）一种**对抗噪声移除网络**（ARN）来恢复自然数据，该方法有能力防御unseen对抗攻击类别。具体地，作者设计了一个基于自编码器的框架，该框架将对抗噪声分成：学习AIF以及从AIF中恢复自然样本。特别地，作者以对抗特征学习地方式引入一对编码器和判别器来从对抗噪声中解耦AIF。判别器地作用是从编码的AIF空间中区分指定地攻击信息（如攻击类型标签），编码器旨在学习一种使判别器无法区分的特征。通过迭代地优化，特定攻击信息将被移除，从而保留跨攻击方法不变地特征。

作者发现不同类型的攻击，其扰动存在也不同，如下图所示

![image-20211130000937276](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130000937276.png)

PGD和AA是一类攻击，即均在像素层级寻找扰动；而STA是空域变换对抗攻击，其余前二者不同。**如果不解决这种差异，那么学习到的AIF很可能对unseen的对抗攻击无效**。为解决该问题，作者在**AIF的编码空间中引入一个规范化项，以将每种攻击类型的特征分布与多元高斯分布相匹配**。通过这种设计，学到的AIF有望更好地繁华到广泛地unseen攻击类型中。

为**从AIF中恢复原始自然严样本，通过在像素空间最小化合成样本与自然样本的距离来训练解码器。通过联合优化编码器和解码器，ARN对unseen类型的攻击就能有更好的保护效果**。

贡献：

+ 对抗样本通常共享不变特征，即使他们是由unseen攻击类型的构造。作者通过探究攻击不变特征AIF，并提出ARN来有效地移除对抗噪声。
+ 为解决在训练过程中可用对抗样本的偏差问题，作者在编码的AIF空间设计了规范项来增强其对未知攻击类型的泛化性。
+ 实验显示作者提出的方法在基于像素约束或者空间约束的攻击获得较好的结果。特别是在unseen攻击中，与现有SOTA方法相比，攻击成功率大大降低。

**unseen攻击是如何定义的？**

## 方法

作者提出的框架如下图所示

![image-20211130002811692](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130002811692.png)

首先同通过编码网络E从输入的图像（对抗样本？）中学习AIF特征。

然后通过解码器G从AIF特征中恢复自然样本。

编码器E是由探索攻击判别器（分类不同攻击方法）DA以及规范化项训练，解码器G是由最小化合成样本和自然样本之间的差距训练的，该差距由像素相似度以及图像判别器D构成。

### 损失函数

编码器的损失函数

![image-20211130003647839](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130003647839.png)

其中K表示攻击类型的总数，σ表示softmax层。其中每种攻击都由one-hot编码表示。

与此相反，E的目的是移除特定攻击信息，使得学到的编码特征对DA是不可区分的。也就是说作为DA的对手，E的目的是混淆DA的判断，将DA的预测推向攻击混淆标签$Y_\varsigma^p=[y_{\varsigma_1}^p,y_{\varsigma_2}^p,...,y_{\varsigma_N}^p]^\top$,其中$y_{\varsigma_n}^p=[1/K,1/K,...,1/K]^\top$是K维的常量向量。

+ 攻击判别损失项

![image-20211130005034301](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005034301.png)

+ 规范化项

受现有方法的启发，作者将规范化项引入AIF的编码空间来降低其不想要的风险。具体地，通过香农散度（JSD）将每种类型攻击的特征分布$P_k(E(\hat X_k))$都与一个多元高斯先验分布相匹配。为此，规范化项的目标函数可以表示成

![image-20211130005446761](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005446761.png)

+ 像素相似度指标

作者使用像素层级的MSE来约束解码器，即其损失函数为

![image-20211130005539443](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005539443.png)

为2范数损失。

+ 图像判别器的损失，其目的是判断图像是干净图像还是恢复的合成图像

![image-20211130005702143](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005702143.png)

从而，解码器G的对抗目标函数为

![image-20211130005742661](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005742661.png)

### 实现细节

优化编码器使用以下损失组合

![image-20211130005814421](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005814421.png)

优化解码器使用以下损失组合

![image-20211130005817974](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005817974.png)

算法可以表示成

![image-20211130005832781](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005832781.png)

## 实验

实验设置细节参考论文

+ 防御像素约束的对抗攻击

![image-20211130005951198](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130005951198.png)

图3显示了作者的方法可以有效地消除对抗噪声

![image-20211130010039002](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130010039002.png)

+ 防御空域约束攻击

![image-20211130010112866](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130010112866.png)

+ leaked 防御（即防御方法被攻击者知道）

![image-20211130010156844](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130010156844.png)

![image-20211130010204896](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130010204896.png)

+ 跨模型防御结果（unseen攻击）

![image-20211130010328164](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130010328164.png)

+ 各损失项的作用

![image-20211130010353733](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130010353733.png)

+ unseen攻击数量的讨论

作者在训练的时候使用了两种seen类型的攻击，并且声明这并不是固定的。理想的seen攻击类型与unseen类型攻击的多样性有关。实际上，很多unseen攻击都很相似（CWN或CWT），使用一个最强的seen攻击足够了。如果unseen攻击差异很大，那么就需要使用更多类型的攻击，从而学习到更具与攻击方法无关的对抗攻击。

# 【ICMLW2021】Improved Adversarial Robustness via Uncertainty Targeted Attacks

关键词：不确定性、更**鲁棒的对抗训练**（同时**不降低干净数据集上的结果**）、**不确定性**目标攻击

一句话概述：首先利用通过最大化模型的不确定性估计来获得不确定性目标攻击（UTA），然后用该样本来训练模型，从而在保证模型准确率的同时，也提高了其对抗鲁棒性。

## 摘要

在现实时间应用中部署的深度学习模型通常会引起安全和可靠性问题，由于他们对小的输入扰动非常敏感。然而对抗训练方法旨在训练更加鲁棒的模型，这些技术通常会导致干净样本上的测试准确率下降，包括使用最广泛的PGD方法。此外，快速对抗训练方法通常会对训练中的特定对抗扰动过拟合。在本文，我们提出不确定性目标攻击（UTA），通过**最大化模型估计不确定性来得到扰动**。作者在MNIST和CIFAR10上训练该扰动---使用图像和潜在空间实现，不会极大地损害与PGD对抗训练模型在干净测试集上的准确率，不会出现灾难性的过拟合问题，并且对PGD攻击鲁棒。

## 引言

目前大多数工作集中在研究如何通过对抗训练提升模型的鲁棒性，以及在提升模型鲁棒性的同时不会影响模型的错误率。一个单独的工作线旨在通过将不确定性估计与每个预测相关联来增加模型的可解释性。深度学习中主要考虑两种主要不确定性类型：

+ aleatoric（偶然性）：描述观测中存在的噪声。前者无法降低
+ epistemic（认知性）：**源自模型的不确定性。后者会由于训练数据的不足造成，它能通过加大训练数据来减轻**。

在本文，作者考虑“不确定性目标攻击”，灵感来自于：1）标准的AT寻找扰动来最大化损失，将对抗样本推向决策边界。2）不确定性最大化扰动将将对抗样本推向决策边界或未被访问过的数据空间，依赖于其邻近的值。更精确地是，我们研究如果找到的扰动可以最大化模型的不确定性，那么就能泛化性和鲁棒性就能达到一个平衡，较好地解决灾难性地过拟合问题。

相关工作：AT&最大化交叉熵。Stutz等人显示了对抗样本留下了数据流行和流形上对抗训练可以促进合成数据集上的泛化性。现在有工作提出利用VAE-GAN的潜在空间的扰动。类似的，一些方法遍历潜在空间以找到被错误分类的数据样本（ACGAN）

## 方法

### 不确定性估计

作者使用MC Dropout来采样M个模型，集成预测定义成预测的均值

![image-20211130213456807](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130213456807.png)

最后使用**输出分布的熵**来量化给定样本的不确定性估计

![image-20211130213731377](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130213731377.png)

### 不确定性目标攻击

不同于标准基于损失的攻击，作者提出不确定性指导的数据或潜在空间的损失。具体地，通过最小化一下的不确定性估计得到UTA

![image-20211130215107044](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130215107044.png)

当扰动应用在输入空间，其中E就是相同的。上述的公式应用在潜在空间UTA扰动上，为避免符号滥用，编码器部分E：x->z；分类部分Cw：z->y。

与AT类似，UTA的内层最大化--对图像和潜在空间扰动，可以使用PGD或者FGSM。

对于PGD，作者发现扰动e的大小有一定影响：

+ small: PGD不会提高广泛分离的相反类样本的鲁棒性;
+ large：PGD 可以通过在边界的另一侧移动样本来扰动样本，从而导致样本标签错误。

因此，需要恰当的选择扰动范围e以trade-off泛化性和鲁棒性。另一方面，作者发现UTA对于扰动范围e的选择不那么敏感。正如以下图1和图2

![image-20211130220158595](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130220158595.png)

## 实验

实现：作者在每一个卷积层后都加上一个Dropout来稳定FGSM，作者使用概率p=0.2的MC Dropout相同的设置。图3描述了结果。

![image-20211130220551755](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211130220551755.png)

# 【arXiv 2019】Label Smoothing and Logit Squeezing: A Replacement for Adversarial Training?

关键词：对抗鲁棒性

一句话概述：利用soft label和高斯噪声数据增强就能使网络的训练超过现有对抗训练方法。

注意：审稿人对该文章的效果提出了质疑

机构：马里兰大学

## 摘要

对抗训练是防御对抗攻击最强的防御方法，但是其需要在优化过程中为每个batch生成对抗样本。在训练期间生成这些对抗样本是非常耗时的。在本文，作者探究了提升模型鲁棒性的机制，并且显示这些机制可以有效地提高模型鲁棒性，这些机制是标签平滑、logit squeezeing。值得注意的是使用高斯噪声注入和简单正则化方法，可以取得更强地鲁棒性模型，甚至能超过对抗训练，注意作者的方法不使用对抗样本

## 方法 

标签平滑

![image-20211201164312833](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211201164312833.png)

其中$\alpha \in [0,1]$，Nc是 类别总数。当α=0时，就是未使用标签平滑的one-hot标签，当α=1时，会导致模棱两可的决策，即所有类别的概率都一样

logit squeezing

![image-20211201164524257](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211201164524257.png)

第二项为：Frobenius norm，其中z(Xk)表示类别k的logit。



## 参考文献

[1]:  Zhou, D., Wang, N., Peng, C., Gao, X., Wang, X., Yu, J., & Liu, T. (2021). Removing Adversarial Noise in Class Activation Feature Space. *arXiv preprint arXiv:2104.09197*.

[2]: Yu, Cheng, et al. "Defending Against Universal Adversarial Patches by Clipping Feature Norms." *Proceedings of the IEEE/CVF International Conference on Computer Vision*. 2021.

