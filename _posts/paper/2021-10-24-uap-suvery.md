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

| 方法                                           |                     |          | 方法                 | $\xi$                    | 评估方式                                | 代码                                                         |
| ---------------------------------------------- | ------------------- | -------- | -------------------- | ------------------------ | --------------------------------------- | ------------------------------------------------------------ |
| [UAP][1]【CVPR2017】                           | 数据依赖            |          | 边界最小距离（优化） | lp范数10<br />l2范数2000 | fooling rate                            | [官方](https://github.com/LTS4/universal)<br />[torch版本1](https://github.com/ferjad/Universal_Adversarial_Perturbation_pytorch)<br />[torch版本2]() |
| [FFF][2]【BMVC2017】                           | 数据无关            |          |                      |                          |                                         | [官方](https://github.com/val-iisc/fast-feature-fool)        |
| [AAA][3]【ECCV2018】                           | 数据无关            |          | 生成模型             | 10                       |                                         | [官方](https://github.com/val-iisc/aaa)                      |
| [NAG][4]【CVPR2018】                           | 数据相关            |          | 生成模型             |                          |                                         | [官方](https://github.com/val-iisc/nag)                      |
| [GAP][5]【CVPR2018】                           | 数据无关<br />/依赖 |          | 生成模型             | lp范数-10                | fooling rate                            | [官方](https://github.com/OmidPoursaeed/Generative_Adversarial_Perturbations) |
| [GD-UAP][6]（会议论<br />文扩展）【TPAMI2018】 | 数据无关<br />/依赖 | 任务无关 | 神经网络中间层       |                          |                                         | [官方](https://github.com/val-iisc/GD-UAP)<br />[torch版本](https://github.com/psandovalsegura/pytorch-gd-uap) |
| [PD-UAP][7]【ICCV2019】                        |                     |          |                      |                          |                                         | [官方](https://github.com/LynnHongLiu/PDUA)                  |
| [SV-UAP][8]【CVPR2018】                        |                     |          |                      |                          |                                         | [torch版本1](https://github.com/slayff/art_of_vectors_pytorch)<br />[torch版本2](https://github.com/AndrewAtanov/nla-project/blob/master/method.py) |
| [UPGD-UAP](10)【ICIP2020】                     | 数据依赖            |          | 投影梯度优化         |                          |                                         |                                                              |
| [SGD-UAP][9]【ICIP2021】                       | 数据依赖            |          | 梯度优化             |                          | fooling rate<br />targeted fooling rate | [官方](https://github.com/kenny-co/sgd-uap-torch)            |

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

## 【ICIP2021】Universal adversarial robustness of texture and shape-biased models



## 【TPAMI2018】Generalizable data-free objective for crafting universal adversarial perturbations

关键词：通用对抗扰动，非数据依赖，中间层特征

---

摘要：



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





