---
layout: post 
title: 博士研究计划参考文献阅读（武大）
category: 文献阅读
tags: paper reading
keywords: 医学实体命名识别、医学实体标准化、医学实体关系抽取
---

#### Artificial intelligence as the next step towards precision pathology （SCI 2区，2020）人工智能是精密病理学的下一步

##### 摘要

病理学是癌症护理的基石。随着个性化癌症治疗需要准确生物标志物评估，对癌症的组织病理学诊断中的准确率的需要正在增加。数字图像分析的出现有望改善组织形态学评估的数量和准确性。最近，机器学习，特别是深度学习促进了计算病理学的快速发展。将深度学习整合到日常护理将是下一个十年的护理领域的基石，组织病理学是是这场变革的中心。在病理学中，潜在高价值机器学习应用的例子包括基于日常诊断特征的模型评估和新特征提取和识别的能力，该新特征有望为疾病提供一个新的视角。最近的突破结果证明了在病理学中，机器学习应用方法极大提高了淋巴结点中癌症专辑检测效果，乳腺癌中ki67评分，腺癌中Gleason分级和黑色素瘤的肿瘤浸润淋巴细胞（TIL）评分。进一步地，基于深度学习模型已经被证明能够预测标准HE切片的肺、腺、胃和小分子癌症的小分子标志物的状态。此外，在很多疾病中如肺癌、小分子和胃癌，基于HE切片的预后（生存结果）深度神经网络模型已经证明了有效性。在本篇综述中，作者旨在提出和总结在数字图像分析和诊断病理学人工之恶能的最近进展。

##### 正文

可以在不损害生物标志物表达图像分析的情况下，对大型数字化的完整载玻片进行主要压缩和缩放[69]。作者分别介绍了深度学校在各种常见癌症诊断中的最新研究进展。

##### 限制和未来方向

尽管在很多任务中AI目前的记过显示出其能与人类专家性能相匹配的令人信服的结果，AI仍然存在限制和一些挑战。构成深度学习算法临床应用障碍的主要问题之一是解释和理解复杂AI模型如何做出决策有关的挑战，有时也被称为“黑盒”问题。可解释性AI盒可解释性机器学习方法是目前非常活跃的研究领域，提供深度学习模型不同层度的可解释性解决方案真在出现，可解释性问题在将来可能会减轻，至少部分是这样。建立可解释性机器学习方法对于临床实践而言非常关键，这将会解决一些医学社区常见的批评。另一方面，一旦AI满足所有临床使用，医学参与者同样需要接受一些这种限制。最近的研究表明，当仅用少量数据集进行训练时，目前的AI模型在独立数据集测试阶段将会出现20%的性能下降。

训练数据集的表示误差，如疾病的形态学差异等。关于技术差异的挑战可以通过标准化或严格控制处理，预处理图像数据来减少技术差异的影响，或者通过使模型对技术差异更加鲁棒来解决。在大量且多样数据集中训练或者fine-tuning深度学习模型有望减少泛化误差。

任何新测试的临床实践的应用都将由规则指导。对于新的AI工具，在CE-IVD认证下非常重要，它可以避免与特定实验室机器学习方法的潜在不可复现的风险。定义AI模型必须达到的最低性能水平，以使病理学家们能够接受使用它们是一个尚未解决的问题。（ defining the minimal level of performance that AI models would have to achieve for pathologists to accept using them is an issue that has not been addressed yet.）

##### 结论

随着个性化癌症治疗需求的增加，我们面临对更加准确生物标志物评估盒更加定量的组织病理学诊断来治疗并且提供高治理决策的迫切需求。病理学家需要配备新的方法论和工具，以提供所需的诊断敏感性和特异性，现在看来，人工智能是迈向精确病理学的下一步。

##### 词汇

1. cancer care 癌症护理
2. histopathologic  组织病理学
3. histomorphological 组织形态学
4. holds promise to 有望， be excepted to 
5. tumour-infiltrating lymphocyte (TIL) 肿瘤浸润淋巴细胞
6. gastric   胃的
7. prognostic  预后的
8. anatomic pathology  解剖病理学
9. stromal tissues 基质组织
10. be bound to 必然、必定
11. reproducibility 再现性、重复能力
12. controversy 争论、辩论
13. proliferation 增殖，分芽繁殖
14. active contour models 主动轮廓模型
15. high-through-put 高通量
16. assay 化验，分析试验，试样，试料
17. equivocal 模棱两可的，意义不明的，模糊的
18. inter-observer 观察员之间
19. intra-observer 观察者内部
20. profile 侧面，轮廓，外形，剖面
21. technical variability 技术差异
22. stratify 分层

#### ImageNet Large Scale Visual Recognition Challenge. (2015) ImageNet大规模视觉识别挑战赛（是介绍ImageNet数据集以及其中相应技术）

##### 摘要

ImageNet大规模视觉识别挑战赛是对数百种物体类别和数百万张图像进行物体类别分类和检测的基准。从2010年至今，每年都要进行一次挑战，吸引了50多个机构的参与。

本文描述了该基准数据集的创建以及由此带来的对象识别方面的进步。讨论了收集大规模真实标注数据的挑战，指出了在类别物体识别中的关键突破，提供了当前大规模数据分类和物体检测领域详细分析，并且用人类标准比较了最先进计算机视觉的识别准确率。最后以挑战赛的5年中获得的经验教训作为结束语，并提出未来的方向和改进建议。

#### An End-to-End Deep Learning Histochemical Scoring System for Breast Cancer TMA  (2018， Transaction on medical imaging, TMI, SCI 1区，医学顶刊) 一个乳腺癌TMA的端到端深度学习组织化学评分系统

##### 摘要

用于对乳腺癌的不同分子类别进行分层的方法之一是诺丁汉预后指数plus， 该指数使用与乳腺癌相关的生物标志物对组织微阵列上制备的肿癌组织进行染色。为了决定肿瘤的分子类别，病理学家们不得不通过显微镜对核活性生物标志物进行手工标记，并且使用一个半定量评估方法对每个TMA核心指定一个组织化学得分（H-Score)。手工标记正染色核是一项耗时，不精确，主观过程，这将会导致观察者间与观察者内差异。在本文，作者提出了一个端到端的深度学习系统，它可以直接自动地预测H-Score。系统模仿了病理学家决策过程，并且使用一个全卷积网络（FCN）来提取肿瘤核区域；一个多列卷积神经网络接收前两个FCN的输出和染色强度描述图像作为输入，作为一个高层决策制定机制来直接输出输入TMA图像的H-Score。就目前而言，该系统是一个使用TMA图像作为输入，并且直接输出临床得分的端到端系统。实验结果证明了使用作者提出的模型能以很高的、与病理学家评分具显著的统计相关性来预测H-Score，提出算法和病理学家之间H-Score的差异与病理学家之间的主观差异相当。

##### 正文

乳腺癌是一组具有不同基因型和表型特征的异质性肿瘤。最近基因表达谱的研究表明乳腺癌可以分成不同的分子肿瘤组。个性化BC管理通常使用鲁棒常用的技术，如针对肿瘤分子谱的免疫组织化学（IHC，免疫组化）。为了分类正染色像素和它们的强度，如颜色反卷积的方法在RGB图像中运用数学转换，这些方法被广泛地应用于从负染色中分离出正染色。

对于病理学家和CAD系统而言，传统评估方法至少有3个未解决的问题。首先，正染色强度需要分类成四个类别：未染色、弱、中性和强。然而，分类DAB染色强度尚未有标准定量准则。第一，两个病理学家通常将相同的染色强度分类成两个不同的类别或者将两个不同的强度分类成相同的强度。此外，人类视觉系统可能更多地关注强染色区域，但是这些强染色区域通常围绕着多种染色强度，这将会影响评估结果。第二，在评估中，细胞/核实例计数是一个非常重要的参数。尽管如此，人类和计算机仍然无法很好处理重叠细胞计数的困难。此外，不同核类型的外表的多样性，异质染色和复杂组织结构使得独立分割细胞/核成为一个非常有挑战的问题。第三，肿瘤核和正常核之间外表尺寸的差异将会影响肿瘤核评估的定量判断。在本文，作者想回答这样一个问题：开发一个像有经验的病理学家给出H-Score一样，直接对一个数字病理学图像给出高层评估的CAD模型是否可能？作者的创新在于模仿病理学家做出H-Score的过程，但是却不需要统计几种核的数量。

##### 方法

提出的H-Score预测框架包括3个阶段：1）普通核和肿瘤区域分割；2）染色强度描述；3）SINI和SITI的构建，并且通过区域注意力多列卷积神经网络（Region Attention Multi-column Convolutional Neural Network, RAM-CNN）来预测最终的H-Score。框架结构的基本原理是：因为核的数量，肿瘤核的数量和肿瘤核的染色强度个数是预测H-Score的有用信息。因此，作者提出包含这些信息的两个区域掩码。与对染色强度分类人工分界设置不同，作者保持了染色强度的连续描述。仅将有益于预测H-Score的信息提供给深度CNN来预测输入图像的H-Score。这与很多文献中直接将整个图像丢入CNN，而不管区域是否有用的工作不同。

对于正染色像素，为了保留正核的形态学，$I_{la}(m,n)$与原始图像的亮度组件相同；对于负染色像素，在强染色像素（黑蓝颜色）赋予更高的$I_{la}(m,n)$值，赋予更弱染色像素（更亮颜色）更低的值。为了清晰地分离正负像素，往负染色像素中添加了255的偏置，正负像素值将会很清晰地被$I_{la}(m,n)$分离。当$I_{la}(m,n)\leq 255$，它是正染色像素。

使用两个FCN来提取核数量和肿瘤核数量，一个FCN用于分割包括所有核的前景，另一个用于分割仅有肿瘤核的区域。为了分割肿瘤区域，作者对肿瘤TMA图像使用了手工设计的像素层级标签来训练FCN；迁移学习策略被用于普通核检测，训练数据从三个不同的数据集中获取。

##### 实验

由于医学数据集是有限的，因此分别使用了标签和图像增强技术来进行数据增强。其中，为解决模棱两可的H-Score问题，引入了Distributed Label Augmentation(分布式标签增强技术，DLA)，即一个实例可能有多个标签。UNET在肿瘤分割中取得了最好的成绩，Res18-UNET在普通核分割中取得最好性能。

在提出的H-Score数据集中，核掩模图像显示使用迁移学习在混合数据集上训练的深度卷积网络可以成功地检测核。肿瘤分割网络能够从正常组织中识别肿瘤区域。值得一提的是，两个检测网络使用了不同的真实掩膜，在Warwick colon癌症图像中所有的核使用了统一的圆形掩膜，肿瘤区域则使用像素级别的标注。因此，由两个网络对相同核生成的最终预测图是不同的。此外，发现随着DAB染色强度的增强，掩膜的扩张变得明显。一个可能的解释是强同质染色使得核纹理核边缘特征变得很难提取。

###### 讨论

不同生物标志物的性能不同，其原因可能是标志物的组织染色不同。尽管这些差异不大，训练一个网络来对不同生物标志物分类将是有益的，并且值得未来研究。

图像之类越好（TMAs中组织的染色清晰，细胞结构清晰没有严重的重叠），算法的性能就越好。反之，图像之类越差，结果就越差，也也容易被提出的方法误打分。

TMA核中包含大量的失去焦点的区域，这些可能在强染色组织更常发生。模糊区域直接影响核分割的性能，和检测准确性。它们也阻碍了最终的回归网络提取拓扑和形态信息。

组织折叠会在薄的组织切片自身折叠时发生，并且在制备载玻片的过程中尤其是在TMA载玻片中很容易发生。 组织折叠会导致幻灯片扫描过程中无法聚焦。 此外，浅色图像中的组织折叠在外观上与深色图像中的肿瘤区域相似[44]。 因此，颜色反卷积的分割精度将在组织折叠区域中受到很大影响。

同质化和重叠同样会影响自动评分的性能。染色同质化会导致在单个核中染色强度差异，并且核重叠会增加其难度。

上述三大挑战将会影响方法的预测，目前很多方法都或多或少存在一俩个这些特点。因此，未来的工作要克服这些组织来获得更高的预测性能。使用带有先验知识的多种模型的图像预处理是一个方向。

##### 词汇

1. histochemical 组织化学
2. microarray 微阵列
3. nuclei activity 核活性
4. semi-quantitative   半定量
5. positively stained nuclei 正染色核
6. on par with  与。。。相当
7. enotype 基因型 
8. phenotype 表型
9. Gene Expression Profiling  （GEP）基因表达谱
10. antigens 抗原
11. Clinical decision making   临床决策
12. available treatment options   可用的治疗方案
13. Diaminobenzidine (DAB)  二氨基联苯胺（DAB）
14. Human Epidermal Growth Factor Receptor  2 (HER2) 人表皮生长因子受体
15. rationale 基本原理
16. one possible reason is that

#### Sparse Autoencoder for Unsupervised Nucleus Detection and Representation in Histopathology Images  （2019， Pattern Recognition，SCI1区）稀疏编码器，用于组织病理学图像中核检测核表示

##### 摘要

作者提出了一个稀疏卷积编码器（CAE）来对组织病理学组织图像进行核检测和特征提取。CAE在组织图像中的图像块中检测核和并将核编码成稀疏特征图，该特征图对核的位置核外观进行编码。主要贡献在于通过利用组织病理学图像块特征，提出了一个无监督检测网络。CAE中的预训练核检测核特征提取模块被微调到以端到端方式的监督学习。作者在四个数据集上进行评估并且取得了SOTA的结果。能够以5%的代价取得全监督标注相当的性能。

##### 正文

人们普遍认为，了解和治愈疾病需要对多种生物学规模的疾病机制进行系统的检查，并且需要整合来自多种数据模式的信息。组织样本被长期用于检查在不同子细胞层级和修改组织形态学的疾病自身的表现。很多SOTA核分析方法都是半监督方法，通过预训练一个自编码器为无监督表示学习并且从预训练的自编码器中构建一个CNN。

##### 方法

方法修改了传统的CAE不仅对特征图中的外观，还对空域信息进行编码。首先学习分割图像块中的背景（细胞质）和背景（核）。使用图像块的原因是组织图像非常大，内存无法加载。在组织图像分析中将组织图像分成patch并处理是常见的操作。CAE使用少量编码神经元将输入图像编码成低分辨率特征图的集合。受到能力的限制，特征图仅能编码大规模颜色和纹理变化。因此，特征图编码图像背景。

使用滑动窗口方法来训练和测试。CNN输出特征图是输入大小的1/4。使用双线性插值将输出图调整到输入图像大小。最终使用高斯滤波，非极大值抑制和阈值来获得最终的检测原子核位置。当位于真实原子核周围6个像素内，原子核才算检测正确。如果在真实位置6个像素内有多个位置被检测到，仅将最近的检测位置作为正确位置，其他检测被当作False Positives。

玻片染色的几种方式：Hemeatoxylin and Eosin(H&E), immunohistochemistry (IHC),  DAPI 和影像。DAPI染色组织样本中组织图片显示了最高的背景和原子核的对比度。

##### 结论

作者提出了一种横向稀疏CAE模型，利用核的视觉特征来同时进行无监督核检测和特征提取。使用CAE初始化监督的CNN，可以以端到端的方式进行核检测，特征提取和分类/分割训练步骤。实验验证显示该方法执行的较其他方法好，并且横向约束在提升性能的过程中扮演着一个非常重要的角色。此外，方法可以用其他方法需要的训练数据5%取得与其相当的结果。也调查了使用从DAPI染色图像中生成的弱标签数据集进行训练，实验结果显示可以达到较好的结果。在数字病理图像中生成真实数据是一项费力的过程。这会限制深度学习方法的应用。横向稀疏CAE和弱标签数据解决了数字病理学中的这个问题，并且促进深度学习更有效的应用。在未来的工作中，使用邻域知识来正则编码层，使用如N-CUT技术和更好检测不同形状和纹理的核。对于核实例层级的监督学习分割，将测试深度分水岭方法。为加快测试速度，研究如SqueezeNet网络来减少网络的大小。输入图像与重建背景之间的高频残差是包含核的前景。作者以“横向稀疏”的方式设计网络学习前景特征图：在大多数特征图位置，跨所有特征图的神经元被激活（输出0）。因为未被激活的神经元对后续编码层没有影响，仅使用前景编码特征图中的非0相应来重建前景。这意味着仅当在不同位置捕获到被检测的核的编码神经元被激活，图像重建错误才会最小化。

为了表示参考的核位置，介绍了一种特殊的二值特征图：核检测图。通过二值化激活函数来使这个图变得稀疏。优化以后，当且仅当在核位置中检测到核，在核检测图中的神经元才会输出1。

一旦背景完成编码核重建，重建背景与输入图像之间的残差就是前景。前景由原子核组成，原子核的大小大致相同，并且通常散步在整个图像。前景编码特征图编码关于原子核的一切，包括位置和外观。前景特征图可以视为一个矩阵，每个条目都是一个向量（神经元响应集合）并编码图像块（原子核的感受域）。如果在图像块中心有原子核，那么向量将编码原子核。否则向量全为0。因为仅使用少量的非0向量编码原子核，所以前景特征图是稀疏的。

##### 词汇

1. simultaneous 同时
2. deep watershed 深度分水岭方法
3. cytoplasm  细胞质
4. It is widely accepted that 人们普遍认为
5. manifest 表现
6. epithelial  上皮
7. entry 矩阵中的元素

#### Structured crowdsourcing enables convolutional segmentation of histology images  （2019， SCI 3区）结构化的众包可对组织学图像进行卷积分割

##### 摘要

**动机**：在语义图像分割任务中，深度学习算法证明了其突出的性能，构建准确的模型需要大规模标注数据集。由于需要仔细地描述组织结构所需要地努力和经验，以及与共享和标记整个切片图像相关的困难，因此病理学图像的标记是一项挑战。

**结果**：作者招募了25个参与者，包括有经验的高级病理学家与医学学生，根据数字切片档案勾画出151个乳腺癌切片中组织区域。参与者之间的不和谐得到了系统的评估，揭露了肿瘤和基质的较低的不一致性，对于主观定义的或稀有的组织类别，较高的不一致程度。由高级病理学家提供的反馈实现了2万多个带注释的组织区域的生成和管理。使用这些带注释图像训练的全卷积网络能达到很高的准确率（mean AUC=0.945)，并且标注数据的规模显著提高了图像分类的准确率。

##### 正文

病理学图像中组织区域的准确分割是一项具有挑战的问题，在计算机病理学应用中非常重要。生成有意义数量的标注需要很多专家的参与，甚至有经验病理学家也显示出他们之间的不一致性。用于查看和注释全幻灯片组织学图像，协作查看和数据管理的界面也是吸引病理学家扩大准确的地面真相产量的关键要素。在生命科学中，基于游戏化或小额支付的基于人群的方法成功地扩展了生物注释。最近的一项系统综述发现，病理学文献中几乎所有众包文章都侧重于疟疾诊断和免疫组化生物标记物相对简单的评分。选择ROI代表每个切片中的主导区域类别和纹理。

##### 讨论

卷积网络在分析病理学图像中的成功提高了生成标注数据策略的兴趣。ALs在很多应用中显示出很强大的诊断性能，需要用大规模标注来开发和验证这些模型。在标注研究中，必须由很多参与者，有效组织广泛有经验水平的参与者的能力是一个促进标准过程的方法。病理学的准确分割标注需要非常专业的知识，本文提供了一个例子，即非专家可以训练用于有效地执行这些耗时地工作。但是无法期望非专家可以识别稀有模式或准确标注困难的例子，在勾画组织边界的大部分工作都不属于这个类别。通过在需要的时候利用专业知识，如标记稀有或困难的例子，并且回归或纠正非专业的标注。应当指出的是，许多不一致性是由SP在校正过程中添加的非优势类引起的。

尽管我们的研究提出了有关注释组织学图像的重要发现，但仍有许多研究问题尚未解决。作者的研究依靠医学学生和应届毕业生，基本原理是熟悉病理学或普通生成物一次减少可能错误的打分。未来的研究可能研究这个假设是否正确，以及是否有可能让缺乏此项培训的更多参与者参与进来，以进一步扩大注释工作的规模。作者的研究没有评估参与者内的不和谐性，这个病理学中总所周知非常重要的一个问题。

##### 词汇

1. crowdsourcing  众包
2. delineate  勾画
3. in the context of  在。。。背景下
4. to a lesser extent  在较小的范围下
5. granular  粒状
6. asymptotic  渐进趋势

#### Learning Where to See: A Novel Attention Model for Automated Immunohistochemical Scoring（2019， TMI， SCI1区）学习在哪里看：自动化免疫组织化学评分的新型注意模型

##### 摘要

估算在入侵乳腺癌中HER2的过度放大被认为是很重要的预测和诊断标记。作者提出一种新颖的基于深度强化学习（DRL）模型，将HER2的免疫组织化学（IHC）评分作为一个序列学习任务。对于从一个从多分辨率大尺寸全切片图像（WSI）中给定图像图块采样，该模型学习通过遵循参数化策略来顺序地识别一些诊断相关的关注区域（ROI）。通过循环和餐厨卷积网络处理选择的ROI，学习为不同HER2评分和预测下一个位置的判别性特征，而不需要处理所有给定图像图块地亚图像块来预测HER2得分，模仿组织病理学家不需要以最高放大倍数分析切片的每个部分的过程。提出模型合并了一个特定任务正则化项和抑制返回机制来防止模型访问之前注意的位置。作者在两个IHC数据集上进行了评估：一个HER2评分挑战比赛的公开可用的数据集和其他由Glo1标记染色的胃肠胰腺神经内分泌肿瘤切片组成的WSI图像数据集。证明了提出模型优于其他基于深度卷积网络的SOTA方法。就作者所知，这是第一项使用DRL来进行IHC评分并且潜在地促进DRL在计算病理学领域地广泛使用，可以减少大规模多巨大像素病理学图像分析的计算负担。

##### 正文

在具有侵袭性肿瘤区域的乳腺癌（BC）病例中观察到HER2基因的过度扩增。与HER2-（阴性）病例相比，BC HER2 +（阳性）病例的癌细胞会发生肿瘤转化，导致肿瘤细胞不受控制的生长。 推荐将所有浸润性乳腺癌病例用于HER2检测[1]，近20-30％的病例HER2蛋白过表达[2]，这与预后不良，生存率较低和复发率高相关。最近的研究报道了HER2的状态是抗HER2和激素治疗的预测因素，也是将浸润性肿瘤与死亡率和无复发生存时间相关的预后因素。因此，准确定量HER2过表达对于确保HER2 +患者接受适当的抗HER2治疗至关重要。

IHC染色是HER2生物标志物表达最常见的方法。在常规临床实践中，专业病理学家在显微镜下目视检查IHC染色的BC组织学切片，以定量HER2的表达，并报告0到3+之间的得分。得分为0或1+的样品在不到10％的肿瘤细胞中没有膜染色或膜染色很弱，被视为HER2-。 在超过10％的肿瘤细胞中观察到强烈的膜染色并被视为HER2 +的情况，得分为3+。 观察到染色不均匀的临界病例被分类为模棱两可，得分为2+。据报道，超过20%的HER2结果可能都不正确。

对于手头的任务，通常在整个WSI基本上提供IHC分数的真实标签，并且没有提供关于做出最终HER2得分的组织切片的ROIs的详细标注。在现有的自动标注方法中，最简单的方法是手工或随机从WSI中提取ROIs块来训练一个监督模型来预测所需的HER得分。这些方法不可避免地对模型预测由偏差并且块的不连续选择也可能会遭受视觉环境的损失。其他潜在的缺陷是计算效率较低，因为这些方法需要处理给定图像中所有的区域，这些区域中的一些组织区域可能跟预测正确的HER2得分的诊断不相关。

关于上述提到的挑战，作者提出这样的问题：能够训练一个忽略不相干信息，学习“看哪里”的模型？文中提出的模型通过学习ROI位置相互作用序列中的参数化策略，从给定图像的低分辨率（2.5倍）粗略表示中识别出一些与诊断相关的位置。该模型从相关位置顺序采样40x和20x的多分辨率ROI，以学习针对不同HER2分数（0至3+）的判别特征。该模型的核心组件是残差卷积神经网络（ResNet）和递归神经网络（RNN）。 ResNet在此模型中的作用是学习判别功能，而RNN依次分析提供的功能以预测结果和下一个位置。 由于GT信息仅提供给WSI级别，而没有ROI位置的先验知识，因此我们使用策略梯度来训练模型。 我们的模型旨在探索空间上不同的位置，并从视觉上有区别的区域中学习特征。 在认知心理学中，这种现象称为回返抑制（IoR）[8]，它阻止了以前参加的区域再次参加。 我们的模型结合了IoR的概念，以鼓励模型参与非重叠的诊断相关区域。为减少错误得将3+得分设置为0/1+，提出了一个特定于任务的正则化项，它会惩罚此类预测。

最常见的自动IHC评分方法包括一个预处理步骤来识别潜在组织区域来训练潜在的模型。然后，手动地从选择地组织区域中采样一小块，通过随机或使用滑动窗口方法。**识别潜在组织区域通常有三种方法：手工选择，基于半自动化或阈值的方法**。必须探索如何训练深度学习模型以忽略给定图像中的不必要信息，而仅关注最终有助于预测正确结果的区域。另一个有趣的扩展是将注意模型与策略梯度相结合，使下划线模型能够学习基于空间依赖性的参数化策略。

模型最重要的任务预测下一个$l_{t+1}$的位置，根据由CNN提供的$v^{\downarrow16}$以及由CNN和LSTM处理ROI图像的隐藏层表示$f_h(\theta_h)$。通过计算$v^{\downarrow16}$和$f_h(\theta_h)$之间的Hadamard乘积来获得组合特征向量。最终定位模型$f_l(\theta_l)$，线性转换组合特征来预测下一个位置$l_{t+1}(x,y)$的归一化坐标。

网络结构如图所示

![image][https://winterwindwang.github.io/assets/img/2021-03-11-where2see.png]

##### 结论

本文提出了一种深度强化学习的方法来自动对IHC染色的乳腺癌的HER2切片评分。不同于处理给定输入图像所有区域的全监督模型，提出模型将IHC评分视为一个序列选择任务，并且可以有效地定位诊断相关的区域（where to see）。提出模型可以潜在地解决其他那一得到准确像素级别标注地病理学图像分析问题。相似的方法也许也可以辅助病理学家对在H&E和IHC染色病理学图像中潜在ROI的自动定位和分类。在公开可用的HER2评分挑战数据集上评估。详细的比较验证支撑了提出的模型。这项研究可能会开始在计算病理学领域将顺序学习和策略梯度进行更可解释的整合。

##### 词汇

1. estimating 估算
2. human epidermal growth factor receptor 2（HER2） 人类表皮生长因子受体2
3. over-amplification  过度放大
4. inhibition 抑制，禁忌，禁令
5. gastroenteropancreatic   胃肠胰
6. neuroendocrine  神经内分泌
7. poor prognosis  预后不良
8. underlying model   基础模型
