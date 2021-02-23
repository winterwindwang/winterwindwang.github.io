---
layout: post
title: 读论文，记笔记
category: 生活
tags: Essay
keywords: 生活,2020
description: 
---


## 论文英语句子笔记
### Article: Cooling-Shrinking Attack Blinding the Tracker with Imperceptible Noises
1. <font color=#008000>Although several works have focused on attacking</font> image classifiers and object detectors,
<font color=#008000>an effective and efficient method for attacking</font> single object trackers of any
target **in a model-free way** remains lacking.

2. Research on adversarial attack is beneficial to understand deep neural networks and design robust models.

3. The latter method is faster than the former method because only one-time forward propagation is needed for each attack after training. 

4. In the recent year, adversarial attack has become a popular topic and has extended from image classification to more challenging tasks,
such as object detection [35, 31] and semantic segmentation [35].

5. The latter type is generally faster than the former type because the operation for transforming an image to an adversarial one does not need to be
repeated.

6.  AdvGAN [34] is the first work to generate adversarial examples with GAN, which can run efficiently during
the inference phase.

7. UEA achieves comparative performance but is much faster than DAG, because only one-time forward propagation is needed during the inference phase
after training.(基于GAN的对抗样本生成技术优于其他方法的解释)

8. Inheriting similar ideas （继承自该思想） 与 Inspired by 。。。一致

9. 将自己方法时，首先用一句话指出我们的工作，或提出的方法；然后再指出提出该方法的目标；最后提出为了实现该目标，做了的工作。
In this work, we propose an adversarial perturbationgenerator for deceiving the SiamRPN++ tracker. The goal of our method is to make the target invisible to trackers,thereby leading to tracking drift. To accomplish this goal,
we train the generator with a carefully designed and novel cooling-shrinking loss. Considering that SiamRPN-based
trackers [16, 40, 15, 39, 30] locate the target in a local search region based on the template given in the initial frame, we
design two versions of perturbation-generators to attack the search regions and the template respectively.

### Threat of Adversarial Attacks on Deep Learning in Computer Vision: A Survey
1. Whereas deep learning **performs** a wide variety of Computer Vision tasks with **remarkable** accuracies

2.  Notice that the perturbations are hardly perceptible for human vision system, however their effects on the deep learning models are catastrophic.

3. The remaining article also follows the same definitions of the terms. 表述某个符号或者定义在下文不发生变化

4. laboratory setting, in the digital world.

### Matching Networks for One Shot Learning

1. 规则的单词：criterion, regime, strategy
2. more precisely, more specifically
3. improve .... on dataset xx% to xx% and from xx% to xx% dataset compared to competing approaches.
4. The novelty of our work is twoflod:
5. we draw inspiration from models such as sequence to sequence with attention.
6. Unless otherwise specified, training is always on, and test in .. 除非另有说明，
7. as reported in their paper 引用他人文章数据用语
8. An obvious drawback of our model is the fact that, 表述方法的缺陷

### One-shot Learning with Memory-Augmented Neural Networks

1. adequately incorporate 充分纳入  
2. catastrophic interference 灾难性推断
3. obviate the downside of 消除...的不利因素
4. The network is tasked to.../The goal of the network is ...网络的任务是/网络的目标是
5. akin to 类似于
6. As such 因此
7. prior to... 先于
8. in accordance with 依据（后面接公式等）

## New High-speed Frame Detection Method: Side Trace Algorithm (STA) for i-appli on Cellular Phones to Detect Watermarks  
1. the response delay caused by the transmission time presented a problem for the user interface. 由传输时间引起的相应延迟给用户交互引出了一个难题。
   句式：名词（现象）+ 原因状语 + 谓语 + 宾语 （个人己见）
2. The drawback to this method is that  这个方法的缺点是
3. the former , 二者中的前者
4. A feature point search must be able to *withstand* the effects of this noise 特征点搜索必须能够经受得了这种噪声。
5. quadrilateral 四边形
6. bisect 二等分
7. traversal 遍历
8. 

## Screen-Shoting Resilient Watermarking

1. traversal 遍历
2. The reason can be analyzed as follows. 原因解释如下
3. intensity 强度
4. As explained previously , 正如之前的解释
5. The second requirement is motivated by the observation that .... 第二个要求是由以下观察得出：
6. Similar conclusion can be drawn as that for Dataset8. 可以得出与数据集8相似的实验结论
7. It is observed that,   可以观察到

## The Unreasonable Effectiveness of Deep Features as a Perceptial Metric

1. *The ability to* compare data items *is perhaps the most fundamental* operation underlying all of computing.

   句意：比较数据项的能力也许能所有计算的基础的最基本操作

2. the goal is for the compressed image to be indistinguishable from the original by a human observer， irrespective of the fact that their pixel representations might be very different.
   句意：压缩图像的目标是尽可能让一个人类观察者感知不到与原始图像的差别，尽管实际上它们像素表示上有一些差别。

3. A well-know example is that blurring causes large perceptual but small $\ell_2$ change.
   句意：一个显而易见的例子是感知上造成较大模糊的情况下，二范数损失的该变量却很小。

4. Our results are consistent with the hypothesis that perceptual similarity is not a special function all of its own, but rather a *consequence* of visual representations tuned to be predictive about important structure in the world.
   句意：我们的结果与假设一致：感知相似性并不是其本身的特殊功能，而是视觉表示的结果，这些视觉表示被调整为可预测现实世界的重要结果。

5. We create a set of "traditional" distortions consisting of common operations performed on the input pathches, listed in Table 1. **In general**, we use photometric distortions, random noise, blurring, spatial shifts and corruptions, and compression artifacts. 
   句意：我们创建了一个”传统“失真集合，包括在输入patches上常用的一些操作。一般而言，我们使用。。。。
6. In addition, we collect judgements on a large number of 64 x 64 patches rather than a small number of images. **There are three reasons for this**. First,...
   句意：此外，我们在64x64尺寸的patches集合中收集判断，而不是小数据集。原因是。。。
7. facto standard   事实标志

## Text-image Watermarking based on Integer Wavelet Transform(IWT) and Discrete Cosine Transform(DCT)

1. Integer Wavelet Transform(IWT) is much faster beacuse it deals only with integers. The image can be reconstructed without any loss using IWT and can be stored without rounding off the error [1]([1])
   句意：IWT更快，因为它仅处理整数。使用IWT重建图像而不会造成任何损失，并且可以存储图像出现错误？
2. In this section, watermarking methods which performed on text-image will be discussed and compared. Text-image watermarking methods in the spatial domain are presented first, then watermarking methods which are performed in the transform domain.
   句意：在本节，将会讨论数字水印用于文本图片的方法。首先介绍空域方法，然后介绍变换域方法。

## Image Demoireing with Learnable Bandpass Filters

1. Through an ablation study, we demonstrate the effectiveness of the different components of Net
   句意：通过对比实研究，我们证明了网络的不同组件的有效性。
2. **In this section, we make a breif introduction of **several CNN-based methods in related tasks, where deep learning has made significant impact.
   句意：在本节，我们简短地介绍了基于CNN方法在相关任务中的工作，这些工作中深度学习方法取得了重大的进展。
3. L1 loss is more effective than L2 loss for image restoration tasks.
   句意：在图像重建任务中，L1损失的比L2损失更加有效
4. Please note: the validation dataset is completely independent and not used in training.
   句意：请注意：验证集完全独立并且没有在训练中使用
   *这个可以视情况使用，比如说实验结果都在什么数据集上的*
5. To verify the effectiveness of each component in our model, we conduct extensive ablation study, including evaluation of MTRB vs. GTMB and LTMB, learnable bandpass filter, and loss function.
   这段是指出消融实验都包括哪些内容，可以在消融实验标题下的第一段使用。
6. + The result is mainly caused by two reasons. First, .... ;(用.)Second,...
     句意：解释某项结果的原因的句式。
   + There are two reasons for this observation. First, .... ;(用.)Second,...
     句意：这个观察的原因有两个。
7. The CNN has **certain** denoising and local smoothing capabilities.
   句意：CNN**有一定的**去噪和局部平滑能力。
8. We denote the networks constructed without LP and DDT as MBCNN-nLP and MBCNN-nDDT, respectively.
   用法：在对网络进行修改的对比实验设置时，可以使用该句法。
   句意：我们将不带LP或DDT的网络结构分别称为。。。。
9. We tested  the performance of these three models on validation set of *LCDMoire*.
   句意：我们在验证机上测试了三个模型的性能。

## 

1. whether the model assigns the perturbed input to some class different from the class label of original input. 
2. Target misclassification for which the perturbed input has to be classified in a given target class.

## VisCode embedding information in visualization images using encoder-decoder network

1. disseminating 传播
2. salient feature 显著性特征
3. The resolution of the output coded image is the same as that of the input carrier image. 
   句意：输出编码图的分辨率与输入载体图一致
4. We refer the reader to the ISO/IEC 18004 specification for more technical details of QR codes.
   句意：
   在推荐让读者了解某件事的时候可以使用

## Drug repurposing: progress, challenges and recommendations 

1. drug repurposing  药物再利用。 drug repositioning   药物重定位
2. attrition  损耗
3. substantial  充实的，坚实，实惠，殷实，物质的
4. escalating  逐步升级，升高
5. in generic terms, 一般而言
6. underlying/latent features 潜在特征

## Adversarial Learning for Invertible Steganography

1. pristine 原始的
2. to name but a few 这仅仅是列举的一小部分
3. Experimental result validated the effectiveness of the proposed method and *showed a significant performance boost*. 实验结果验证了提出方法的有效性和显示了巨大的性能提升。
4. revisits 回顾
5. UNet 模型的介绍：It is characterised by it U-shape, which explains the origins of the name, and is comprised of a pair of encoder and decoder with skip connections between mirrored layers. The samples flow from top to bottom through a series of convolutions and downsampling, and then go back through a succession of convolutions and upscaling to full resolution. The skip connections allow multi-resolution feature maps from previous layers to be retained and concatenated with feature maps at later layers.
6. l1 norm may be preferable to l2 norm for the case of high dimensional data [2]([2]). l1 norm is capable of capture low-frequency structure, if the model solely rests on the l1 norm, the outcome would trend to be blurry since this loss function is minimised by averaging all plausible outputs, thus incentivising a blur when uncertainty encountered in complex areas.
7. 本文使用GAN完成可逆隐写的任务。具体地说，作者将图像转化成8个位图，根据正则奇数映射（Regular-singular, RS）的原理，将7个无失真的位平面输入到生成器里面，输出一个目标位平面（利用未修改的位平面来预测或合成修改的位平面）。该目标位平面被用于可逆隐写时嵌入和提取的参考位图。最后对生成器的合成图进行了后处理（量化成0-1数据）。**疑问在于**：1、神经网络无法做到完全不失真，这是否符合可逆隐写的规则？2、如果生成器只是用来生成目标参考位图，那么是如何实现隐写和信息提取的？在文中提到一句，“Blocks for carrying message bits was fixed to the size of 2 x 2 pixels”。 所以最终的信息位是否是合成图按2x2大小像素块计算得到的信息？所以最终的逻辑是：输入7个不变的位平面得到可改变的目标位平面，得到目标位平面后呢？与原来7个不变的位平面叠加重建图片？如何实现可逆呢？

+ The forward pass computes a weighted sum of their inputs from the previous layer and pass the result through a non-linear function.
+ The key advantage of deep learning is the capability of representation learning and the semantic composition empowered by both the vector representation and neural processing.
+ Typically, the generative network learns to map from a latent space to a particular data distribution of interest, while the discriminative network discriminates between candidates generated by the generator and instance from the real-world data distribution.

## Functional adversarial attack (NeurIPS 2019)

1. cause the algorithm to **report(替换make)** an **erroneous（替换wrong）** outpout, e.g. the **incorrect（替换wrong）** label for classifier.
2. a change in the environment that is **not noticeable to a human** may cause **unexpected**, unwanted, or dangerous behavior.
3. enforces/guide 逼迫/指导

## Unsupervised Domain Adaptation using Feature-Whitening and Consensus Loss  (CVPR 2019)

领域自适应存在的两个问题：a) 源域和目标域不匹配； b) 无标注目标数据假设，超参数在UDA中很难调优。

UDA方法可以分成以下几类：

+ 根据一二阶统计量建模领域分布。如一些工作致力于通过最大化均方误差来最小化领域偏移和按照阶数统计量描述分布。作者的研究考虑使用相关性对其范例来使用二阶统计量。很多研究直接嵌入利用BN的深层网络域对齐来处理域转移问题。
+ 第二类方法包括学习领域不变深度表示。如梯度反转层（dann）学习判别性的领域知识表示。也有鼓励网络学习对领域偏移有益的鲁棒性特征。
+ 第三类包括生成对抗网络。主要思想是直接将图像从目标域变换到源域。但是GAN在复杂图像上的效果有限。

熵最小化是一种在半监督领域种常见的策略。它利用未标注样本的高置信度作为伪标注。后来在很多UDA中都能看到。

其他UDA中的范式，称为一致性逼迫范式，然后在相同目标输入的两个扰动版本的预测之间强加一些一致性。通过设计一些损失函数来实现这样的一致性。

###### 方法

常见的技术是在网络中使用基于BN的层来减少领域偏移，比如通过特征标准化将源域和目标域分布投影到一个参考域中。作者使用whitening实现特征标准化。使用`Min-Entropy Consensus(MEC)`损失，网络的输入要由两个编程三个。

特征whitening完全去批样本的相关性，因此潜在地提升损失函数的landsacpe的平滑度。在无标签数据集上时，损失函数的平滑重要性高于类似熵的损失。

Accessibility 可访问性

# On Adaptive Attacks to Adversarial Example Defenses (CVPR 2020)（对抗样本防御的自适应攻击）

##### 词汇

1. de facto  实际上
2. bootstrap 自展

## 医学名词

1. Unified Medical Language System, UMLS 一体化医学语言系统，是医学领域的专家系统，定义了医学实体的本体和概念、多种关联关系、规划库等，其目的是克服医学领域的描述多元化问题。

2. 知识图谱可以用来更好的查询复杂的关联信息，从语义层面理解用户意图，改进搜索质量。

3. 作为知识抽取的第一步，识别文本中的医学实体，其目的在于通过识别关键概念后能够进一步提取关系和其他信息，并将识别的概念以标准化的形式表示出来。医学领域的实体抽取是从医学数据源中提取出特定类型的命名实体，主要有疾病、药物、检查、化学式、基因、蛋白质等。

4. 大多数情况词典难以应对医学中实体抽取中的多样性和广泛性。因此，基于医学词典及规则方法仅可作为一个初步的基线解决方案，因其简单且无需监督的特点，在一些限定场景使用。

5. 传统的浅层机器学习方法在实体抽取中能够取得较好且稳定的性能，但性能很大程度上依赖于特征工程的人工选择结果，由于人工选择的局限性，系统的泛化能力受到限制。在此基础上，医学领域的实体抽取采用了一些基于神经网络方法的自动抽取特征的方式。整体而言，传统机器学习方法能够在小规模数据集上取得可靠的效果，且有完善的理论证明，但在大规模语料上的效果会略逊于具有更强学习能力的深度神经网络方法。标准的循环神经网络模型单个节点输出为当前节点的局部最优解（虽然考虑了上下文的影响），因而容易忽略前后类标的整体性，结合 CRF 后，输出计算全局的最优解，能够进一步提升性能。

6. 在医学领域，知识特征对深度学习方法的性能依然具有较大的影响，完全端到端的深度学习方法通常难以取得能够和采用知识特征的浅层机器学习、深度学习方法相当的性能。

7. 医学知识图谱中知识来源的多样性导致了知识重复、知识质量良莠不齐、知识间关联不够明确等问题。医学实体在不同的数据源中存在严重的多元指代问题。实体标准化是判断多源异构数据中的实体是否指向真实世界同一对象的过程。*实体标准化（Entity Normalization），在通用医学知识图谱领域也常被作为实体链接（Entity Linking）或者实体对齐（Entity Alignment），是医学知识抽取中非常重要的一步，若不进行实体标准化，则抽取的实体依旧是描述性的文字，而非知识性的概念。*现有的实体标准化算法有**单实体标准化**与**基于结构的实体标准化**两类，各自在不同场景下应用。

   1. 单实体标准化考虑实体形式和属性之间的相似性，包括基于规则的实体标准化方法和基于机器学习的实体标准化方法。单实体标准化不依赖于大规模知识图谱，多用于知识抽取阶段的实体标准化，适用于医学领域知识图谱完备程度较低的情况。目前研究的方法大多都基于词法和人工规则或一些浅层语义来寻求相似性，对实体本身的深度语义层面的建模较少。
   2. 基于结构的实体标准化则是依赖于知识图谱的图结构信息，核心思想是知识图谱中其他相关节点和关系能够对医学实体标准化提供帮助，例如两个实体被多个相同实体以相同关系指向，则二者为同一实体的概率较高。在单实体标准化的基础上再计算实体相似度时加入了实体间的相互关系，分为局部结构实体标准化与全局结构实体标准化。前者典型算法是使用向量空间模型和余弦相似度计算实体相似性，通常具有较低的精确度，但召回率较高且效率较快。后者通过不同匹配决策之间的相互影响来调整实体间的相似度，又分为基于相似性传播和基于概率模型的实体标准化方法。基于结构的标准化方法由于需要两个实体均处于在知识库中且具有关联的其他节点，其应用在于已构建成型的大规模知识图谱中的实体去重或异源知识库的合并，在知识抽取阶段由于缺乏冗余的大规模知识数据则难以适用。由于其能够利用知识图谱的结构信息来计算语义相似性[57,58]以提高实体标准化效果，通常性能较单实体标准化较好。

8. 实体消岐（Entity Disambiguation）为医学实体标准化的核心问题。对于通用领域，通过一系列有监督以及半监督的方法，抽取文本或者知识图谱的结构特征来进行实体标准化是主流的研究方法[60]。对于医学领域而言，医学知识库由于可靠性和权威性的要求，主要由专家设计制定，不同知识库结构差异较大相对难以利用知识图谱结构，因此单实体标准化方法相对应用更多[61]。随着知识库规模扩大和实体数量的增加，知识库中的实体标准化越来越受到重视，如何准确高效地实体标准化是知识抽取方法研究重点之一。

9. 医学实体关系抽取可以分为两个层级，第一个层级是医学实体层级关系抽取，这个层级按照本体的的观点通常是上位本体和下位本体或实体的概念关系，例如“糖尿病”与“I 型糖尿病”；第二个层级则是非层次关系抽取，例如“三多一少”与“糖尿病”是一种症状和疾病之间的联系，又如蛋白质之间、化学药品之间的相互作用关系。

   1. 对于同类型实体层级关系抽取而言，关系类型通常为两种，一种是上下位关系、一种是部分和整体的关系。对于同类型实体层级关系抽取，在医学领域通常均采用标准的知识库和词典，通过开放访问接口针对数据格式进行抽取分层结构，构建知识图谱。由于大量医学专家本体概念词表的存在，对于自动抽取而言技术难度较低，通常不是关系抽取研究的重点内容，而集中在实体标准化对齐表示这一方面。
   2. 对于不同类型医学实体间的语义关系识别，通常可以依照数据来源划分为两类。一是百科、知识库等结构化数据，这种通过已有的结构化知识转换的抽取构建方法，清晰的定义了具体的实体和关系，本文称为自上而下的抽取构建方法；二是医学文献、电子病例、健康档案、专业说明等半结构化或无结构化数据，由于这些半结构化和无结构化数据中蕴含的专业知识，通过自然语言描述，实体和关系都不是显见的，对此本文称为自下而上的抽取构建。

   通常预定义好要在实体间抽取的关系类型，再将抽取任务转换为分类问题来处理。如何预定义实体关系目前尚未有统一的标准，这取决于医学知识图谱构建过程中模式图的设置、实体识别情况、语料来源、构建目的及应用场景等。在 I2B2 2010 评测数据集中，将电子病历中的实体关系分成了疾病症状定义为问题，因此具有三类关系：问题与问题、问题与治疗、问题与检查。在样本数较少时，人工规则方法效果更明显，而面向大规模样本时则机器学习方法效果更好。句法分析特别是最短依存路径[69,76]被证明能够提升深度学习关系抽取模型的效果

10. 对于医学领域而言，医学信息化的一个核心是医学知识信息化，也就是医学知识库乃至知识图谱的应用与构建。对于医学知识图谱的自动构建方法[106]而言，主要可分为四个部分，知识抽取方法、知识表示方法、知识推理方法以及知识展示方法。

    1. 构建医学知识图谱首要的问题是如何抽取知识，知识抽取是指如何从结构化、非结构化医学数据中抽取实体和关系知识、并且对应到知识图谱中的相应实体，防止产生冗余和歧义。
    2. 医学知识的表示是应对医学知识图谱的网络结构导致的数据稀疏性问题，若推理步骤多、网络规模较大，通常复杂网络会导致推理效率过低，此时可将网络结构转换成稠密低维的张量表示
    3. 医学知识推理是从已有医学知识图谱中找到更多的知识，减少人工参与，推理出缺失的事实，完成问题求解
    4. 医学知识图谱需要知识加工来量化评估抽取知识质量、表示损失的信息量、推理的可信度和准确度，对于知识构建而言，医学诊疗对于数据的可信度的要求通常更为严格

    医学知识抽取和通用领域抽取类似，首先需要从语料中获取实体的描述，再判断实体描述在知识库或知识图谱中对应的实体，最后将各个实体之间的关系进行汇总。主要是从非结构化数据中人工或自动地提取实体、关系和属性。

11. 实体抽取和关系抽取任务通常采用查准率（Precision, P）、查全率(Recall, R）以及 F 值 (F-score, F) 来进行评估性能，而常见的用来评价实体标准化的指标是正确率 (Accurary, Acc)。查准率表示正确抽取的正例数据占实际标注数据的比例，查全率表示分类正确的正例数据占全部正例的比例。查全率和查准率二者相互制约，在模型性能不变的情况下，查全率的提高通常会导致查准率的下降。对于正确率而言，则为正确标准化的实体个数占据整个实际标注数据的比例，也就是损失函数是 0-1 损失时测试数据集上的准确率。对于多分类问题，则采用宏平均值和微平均值对各个指标进行统计，其中微平均值将各个类别的各个实例均等看待，若与标注结果一致则认为是正例，否则认为是负例，然后通过上述公式计算；宏平均值则是对各个类别分别计算查准率、查全率、F 值，然后取平均值。

12. 医学领域实体标准化。将无序的信息组织成知识，文本描述的对象通常面临对应到相应的知识条目的标准化问题。主要挑战包含有三个方面：歧义性问题、多样性问题、知识缺失问题。在大多数医学领域，特别是临床文本关注的疾病、症状、药物、治疗这些实体标准化中，多样性的问题远较歧义性问题更为突出。至于知识缺失问题，该问题的重要性取决于数据集中对应的知识库的完备性而各有不同。

#### 第2章

​	相较于通用自然语言处理领域的各类型命名实体抽取（如人名、地名等，通常为连续的词或词组），医学（特别是临床医疗）所关注的通常而言不仅包括直接描述疾病、症状、药物、治疗的短语实体，还关注表示形式更加多元化的复杂实体。与通常以短语形式描述的命名实体不同，复杂实体还常以非连续、短句、含有数值或描述性指标的描述形态记录在医疗健康档案中。*通用领域的命名实体抽取通常采用基于结构学习的序列标注方法，将实体抽取问题视作对文本中的字词进行分类，在实践中对短语实体抽取效果明显，但对于非连续、短句或者指标型等更复杂的实体抽取识别力有不及。*（通用领域与医学领域实体识别的差异）。

​	除此之外，由于医学知识紧密的结构体系，根据应用情况不同中可能确定一个实体多个不同层次的类型和分类，例如“Propranolol”(普萘洛尔) 是一个药物实体，而具体又属于“受体阻断药”这一药物分类，因此需要具有对实体进行层次分类的能力。临床诊疗还关注于事实实体出现的先后顺序，如诊断前是否用药、手术时间等，需要对实体出现的时间进行判断。

​	**传统机器学习方法**，具有较好的数学可解释性，且在小空间搜索的优势明显，可以得到全局最优解，但其依赖于特征的选择和优化方法；**深度学习模型**则多缺乏收敛性证明，但由于其通常不依赖于过多的外部特征，在具有大规模训练样本的情况下，通常能取得相比前两类方法更好的性能，但进行训练时的时间复杂度较高。

​	概率图模型是一类用图来表达变量相关关系的概率模型。以图作为表示工具，最常见的使用一个节点表示一个或一组随机变量，节点之间的边表示变量间的概率相关关系，即“变量关系图”。**条件随机场（CRF）**作为一种无向概率图模型，在自然语言处理领域，由于文本的线性表示特性，通常采用链式无向图结构来计算给定观察值来估计输出状态的条件概率。通常通过梯度下降算法算法计算该条件概率以学习模型参数 λ, t, µ, s。对于预测过程则采用动态规划方法中的维特比算法预测序列。**结构化支持向量机（SSVM）**，是在多分类支持向量机上的一种改进，其在函数间隔的约束条件中引入损失函数。**深度循环神经网络（RNN）**，其输入为时序化状态序列，输出为对应时刻的预测值，其对每个时刻的每个状态序列采用相同的参数和操作进行运算，每一个操作都依赖于之前时刻的计算结果。因此其能够涵盖上下文的信息，适用于序列标注问题。将一篇文档中的每一个字词看做一个时间点状态，对应的类标作为输出，整个文档则作为一个时间序列，则对于每个时刻 t，其输入为 xt，输出为 yt,。相比于传统前馈神经网络模型，其输入和输出之间的关联性更强，每一个操作都依赖于之前的计算结果，对于文本序列而言，其逐字逐句的处理方式也更符合人类本身对语言的处理方法。在具体应用中，通常采用双向循环神经网络（Bidirectional RNN）、长短记忆神经元（Long-short term memory，LSTM）网络和门限循环神经元（Gated RecurrentUnit，GRU）网络等能够更好的处理较长序列依赖问题的改进算法。

基于结构学习的序列标注技术广泛应用于实体抽取应用上，对于短语层面的抽取效果在通用领域和医疗领域均经过多年的检验，但其难以直接应用于需要进行逻辑推理或者长段文本的抽取情况。而对于医疗实体抽取而言，虽然实体主要以短语的形式进行描述，但依旧有大量类型的实体难以通过短语记录表达。

## Reference

[1] R. Harshitha, S.S., Vidya, Robust and High Limit Watermarking using DWT-IWT. International Journal of Advanced Scientific Research and Management, 2 (4), pp.18-21, 2017.  

[2]C. C. Aggarwal, A. Hinneburg, and D. A. Keim, “On the surprising behavior of distance metrics in high dimensional space,” in Proceedings of International Conference on Database Theory (ICDT), London, UK, 2001, pp. 420–434.  