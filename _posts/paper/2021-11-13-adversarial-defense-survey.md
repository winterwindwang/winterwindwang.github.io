---
layout: post
title: 【文献调研】对抗样本防御调研
category: 文献阅读
keywords: adversarial examples, adversarial examples defense
tags: defense survey
---

## 【ICCV2021】Removing Adversarial Noise in Class Activation Feature Space[1][1]

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





## 参考文献

[1]:  Zhou, D., Wang, N., Peng, C., Gao, X., Wang, X., Yu, J., & Liu, T. (2021). Removing Adversarial Noise in Class Activation Feature Space. *arXiv preprint arXiv:2104.09197*.

