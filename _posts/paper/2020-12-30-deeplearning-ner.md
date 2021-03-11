---
layout: post

title: 深度神经网络实体词识别综述

category: 文献阅读

tags: Paper reading

keywords：deep learning, NER
---

## A Survey on Deep Learning for Named Entity Recognition  (2020, IEEE TKDE)
**摘要** Named entity recognition(NER)是一个任务，用于从预定义预定义语义类型（例如人、位置、组织、组织等）的文本中识别提及的刚性指示符。NER作为很多自然语言应用的基础，如问答、文本总结、机器翻译等。**早期纯人工在设计领域相关特征与规则**的NER系统取得了巨大的成功。最近，将通过非线性处理的连续实值表示与语义组合的深度学习引入NER系统，取得了SOTA的性能。在本篇文章中，作者全面回顾了基于深度学习的NER技术。通过首先回顾NER资源，包括NER标签文集与代表性的NER工具。然后，基于以下三个维度系统地对现在的工作进行分类：输入分布表示、内容编码、标签解码。接着，调查了在新的NER问题设置与应用中的深度学习技巧的大多数代表性工作。最后，提出了NER系统面临的挑战与指出了该领域的未来方向。

**Introduction** "Named Entity"(NE)一词首次出现在MUC-6（第六届信息理解大会）[10][]，并作为在文本中识别组织名、人名、地理位置名，目前也用于识别时间与百分比表达。从MUC-6发布以后，研究者对NER的研究兴趣逐渐增加，并发布了不同的数据集。尽管如此，Petasis等人[15][]将NE的定义限制在：“命名实体应该是一个专有名词，用于代表某些事物或某些人”。语料库中存在大量的专有名词可以证明这一限制。而Nadeai和Sekine等人[1][]认为“命名”一词将任务限制为仅一个或多个刚性指示符代表所指对象的实体。刚性指示符在文献[16][]中定义，包括专有名词以及如生物物种或物质的自然种类。尽管对NE定义存在差异，但研究者对可识别的NE类型达成了共识。NE的通常可以分成两种：通用NEs（人类或地点）与特定领域NEs（蛋白质，基因等）。该文主要调查了英语中的通用NEs。

NER中的技术可以分为以下四种：

+ Rule-based（基于规则）方法，不需要标准数据，仅依赖人工设计的规则
+ Unsupervised learning（无监督学习）方法，不需要人工标记训练集，依赖于无监督学习算法
+ Feature-based supervised learning（基于特征的监督学习）方法，依赖精心设计的特征工程，并使用监督学习算法
+ Deep learning based(基于深度学习)方法，以端到端的方式自动地从原始输入中发现用于分类或检测地表示。

**Background**

##### NER

一个命名实体是一个单词或短语，能从拥有一组具有类型属性的其他术语中清晰地识别出一术语[28][]。NER是在文本中定位或分类命名实体到预定义实体类别中的过程。

正式地，给定一个令牌序列$s=<w_1,w_2,...,w_3>$,NER输出一个元组集合$<I_s,I_e,t>$,其中每个都是一个在$s$中的命名实体。$I_s\in[1,N]$与$I_e\in[1,N]$分别是一个命名实体开始和终止索引;$t$是预定义类别集合中的实体种类。具体如下图所示

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_01.jpg)

NER是一个非常重要的预处理步骤，可以用于各种不同下游应用。

##### NER资源：数据集与工具

高质量标注是模型学习与评估的关键。下图列举了一些广泛使用的数据集。2005年以前的数据集主要由少量实体的标注新闻文章构成，适合粗粒度的NER任务。在那之后的任务就更加广泛了。标记的类别更加大（HYENA能够达到505个）。

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_02.jpg)

现有的NER工具如下图

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_04.jpg)

##### NER评估指标

NER系统一般通过比较系统输出与人类的标记进行评估。可以通过完全匹配或宽松匹配来量化比较。NER从本质上可以分成两个子任务：边界检测与类别识别。在“确切匹配评估”中，正确地识别样例需要同时正确地识别其边界和类型。更具体地，FP,FN,TP被用于计算Precision, Recall, F-score。

+ False Positive(FP): 实体是由NER系统返回，但没有出现真实实体（ground truth）集中。
+ False Negative(FN): 实体不是由NER系统返回，而是出现在真实实体中。
+ True Positive(TP): 实体由NER系统返回，同时出现在真实实体中。

Percision表示系统结果被正确识别的比例。Recall表示系统正确识别的总实体数比例。F-score衡量了Percision与Recall二者。
$$
Precision = \cfrac{TP}{(TP+FP)} ~~~~~~  Recall = \frac{TP}{TP+FN}~~~~ F-score=2*\frac{Precision x Recall}{Precision + Recall}
$$
此外，在多实体类型中macro-averaged F-score和micro-averaged F-score同样会被作为衡量性能

+ Maro-averaged F-score独立地在不同实体类型上计算F-score，最后进行平均
+ Mirco-averaged F-score在所有实体类型中所有独立地对TP,FP,FN进行求和，最后计算F-score

MUC-6定义了一个宽松匹配评价：如果为实体分配了正确的类型而不管其边界如何，只要正确的类型与基本真理边界重叠，就可以认为该类型正确； 无论实体的类型分配如何，正确的边界都将归功于此。最近的研究中复杂的评估方法使用的不广泛。

##### NER传统的方法

###### 基于规则的方法

生物医学领域，Hanisch等人[46][]提出了ProMiner, 利用预处理代名词字典来识别生物本文中的蛋白质提及与蛋白质基因。Quimbaya等人[47][]提出基于字典的NER方法，用于识别电子健康病历。实验结果表明能够达到很好的Recall，并保证对precision的影响也很小。基于规则的系统在词法很详尽的时候，可以工作的很好。但是由于特定领域规则与不完整字典，高精度与低召回的现象通常出现在这些系统中，并且使得这些系统不能够迁移到其他系统。

###### 无监督学习

典型的无监督学习就是聚类[1][]。基于聚类的NER系统从基于上下文相似的聚类组中提取命名实体。Collins等人[54][]观察到使用无标签数据减少了监督需求，并提出了两种无监督算法对命名实体进行分类。KNOWITALL等人[9][]等人利用一个预测命名集合作为输入，从一小套通用提取模式中引导其识别过程。zhang和Elhadad[44][]提出一个无监督方法以提取生物文本中的实体。不同于监督，他们建模重新排列了术语，文集统计（如逆文档频率与上下文向量）和浅层语法知识（如名词短语分块）。

###### 基于特征的监督学习

给定标注数据样本，仔细设计特征以表示每个训练样本。然后使用机器学习算法来识别未见数据中类似模式。在监督NER系统中，特征工程非常关键。特征向量表示是在文本上的抽象，即一个单词是由一个或多个布尔类型、数值类型或者名义值表示。基于这些特征，有很多机器学习算法如，隐藏马尔可夫模型[69][]，决策树[70][]，最大交叉熵模型[71][]，支持向量机[72][]，条件随机场[73][]。Bikel等人[74][],[75][]提出了首个HMM-based NER模型，为IdentiFinder，以识别或分类名称、数据、时间表达、或者数值量。此外，Szarvas等人[76][]开发了一个基于C4.5决策树和AdaBoostM1学习算法的多语言NER模型。主要的贡献在于提供了一个机会在不同特征子集上训练多数独立决策数分类器，然后使用多数投票的方式得到最后的决策。Borthwick等人[77][]通过应用最大熵理论，提出“最大熵命名实体”(MENE)。该方法能够利用额外的李赛范围知识源来进行决策标注。相似的最大熵方法有[78][]-[80][]。SVM在预测一个实体类别时，无法考虑“邻居”词。CRFs考虑了上下文。McCallum和Li[82][]等人提出了一个基于CRF特征感应的NER方法。实验结果表明，在CoNLL03的英语数据集上，能达到84.04%的F-score。Krishnam和Manning等人[67][]提出一个基于耦合CRF分类器的两阶段方法。第二个阶段的CRF利用了第一个CRF输出的潜在表示。CRF在NER中广泛的使用，如生成物文本[58,83][],推特[84,85][],化学文本[86][]。

##### 使用深度学习技术的NER

深度学习的关键优势在于其由向量表示和神经元处理驱动的表示学习与语义组合能力。这使机器可以获取原始数据，并自动发现分类或检测所需的潜在表示和处理。

作者将从以下几个方面介绍基于深度学习的NER技术

##### 词层级表示（word level representation）

一些研究[89-91][]应用词层级表示，通常是通过无监督的方式在大规模文本上预训练的模型，如CBOW，连续的skip-gram模型[92][]。最近的研究[88,93][]显示了预训练模型词嵌入的重要性。将其作为输入，预训练词嵌入能够固定或者在NER模型训练时进一步微调。

Yao等人[94][]提出了Bio-NER，使用skip-gram模型在PubMed数据集上训练。字典由600维向量的205924个词构成。Nguyen等人[89][]使用word2vec在增强的Gigaword词级数据集（BLOT新闻组数据）学习英语词表示。zhai等人[95][]设计了一个神经网络实现序列阶段，由两个过程组成：分割与标注。神经网络模型能够输入SENNA嵌入或者随机初始化嵌入。zheng等人[90][]使用一个单模型联合提取实体与关系。这个端到端模型使用了在NYT数据集上训练的word2vec词嵌入。Strubell等人[91][]等人提出了一个基于迭代空洞卷积神经网络的标注模式。他们模型中的**查找表（lookup table）**由使用skip-n-gram方法在SENNA文集上预训练的100维嵌入得到。在他们提出的用于提取实体及其关系的神经模型中，zhou等人[96][]利用谷歌提供的预训练的300维词嵌入向量。此外，Glove[97,98][]和fastText[99][]同样广泛的应用在NER任务中。

##### 字层级表示（character-level representation）

与仅考虑词层级表示作为基础输入的研究工作不同，一些同坐[100,101][]利用从端到端神经网络模型学到的字层级词表示。**字层级表示在提取具体子词层级信息（如前缀和后缀）时非常有用**。字层级表示的另一个优点是它能天然地处理词表外的情况。从而基于字的模型可以**为那些从未见过的词做出推断表示**与**共享词素层级规则信息**。

Ma等人[97][]利用CNN来提取词的字层面表示。随后字层级表示与词层级表示进行拼接后输入RNN模型。与此类似，li等人[98][]应用了一系列卷积层和highway层来为单词生成字层级表示；最后将最终的此嵌入输入一个双向递归网络。Yang等人[102][]提出了NER重排列神经模型，其中字符嵌入层的顶部使用了具有固定窗口大小的卷积层。最近，Peters等人[103][]提出ELMo词表示，其在使用字符卷积的双向语言模型的顶部进行计算。

对于基于RNN的模型。LSTM和GRU(Gated Recurrent Unit)是两种基本的单元选择。Kuru等人[100][]提出charNER，一个为独立NER的字符层级标注器。CharNER考虑了句子作为字符序列并且利用LSTM来提取字符层级表示。它为每个字符而不是词输出一个标注分布，然后词标注根据字符标记得到。最后由字符层级标注得到词层级标注。他们的结果表示考虑字符作为主要表示作为基础输入单元要优于以词作为输入效果好。Lample等人[19][]利用双向LSTM来提取词的字符层级表示。与[97][]类似，字符层级表示与从词查找表中的预训练词层级嵌入相拼接。Gridach[104][]研究了在识别医学命名实体任务中词嵌入与字符层级表示的影响。Rei等人[105][]使用门控机制组合字符层级表示与词嵌入，使用该方式，Rei的模型动态决定要从字符层级或词层级提取多少信息。Tran等人[101][]介绍了一种以堆叠的残差LSTM与可训练的偏差解码的神经NER模型，词特征提取自词嵌入和字符层级RNN。Yang等人[106][]开发了一个以联合训练的方式处理跨语言和多任务的模型。他们应用了一个深度双向GRU来从词的字符序列中学习信息形态表示。然后对每个词，拼接字符层级表示与词嵌入来推断除最终表示。最近使用递归神经网络的语言模型使得将语言建模成字符上得分布成为可行。Akbik等人[107][]使用字符层级神经语言模型在句子语境中字符串的字符生成情境化嵌入。**一条重要的性质是嵌入会由其周围的文本情景化，这意味着相同的词在不同的上下文中拥有不同的嵌入**。

##### 混合表示（Hybrid Representation）

除了词层级与字符层级表示外，一些研究同样将一些额外信息（如地名词典[18,108][]，词汇相似度[109][]，语言依赖[110][]与视觉特征[111][]）融合到最终的词表示，在输入到上下文编码器之前。换句话而言，基于DL表示与基于特征的方法以混合的方式组合。添加额外的信息可能有助于提高NER性能，以对这些系统造成泛化性的较差为代价。

将神经网络用于NER任务首先由[17][]提出，是一个在提出的词序列上基于临时卷积神经网络的结构。当与先验知识相结合时，结果系统性能优于仅使用词层级表示的baseline。在由Huang等人[18][]提出的BiLSTM-CRF中，使用了四种特征：拼写特征、上下文信息、词嵌入已经句地点信息。实验结果表明这些特征有助于提高模型的准确率。由Chiu与Nichols提出的BiLSTM-CNN模型结合双向LSTM与字符层级特征（字符类型的4维向量表示：大写、小写、标点，其他）。Wei等人[112][]提出一个基于CRF神经系统来识别和**标准化疾病名词**。这个系统应用了词嵌入以外的富特征(rich features)，包括词，POS标注，分块和词形状特征（如字典和形态学特征）。Strubell等人[91][]拼接了100维嵌入与5维词形状向量（如，所有大写、非大写、第一个字符大写或包含大写字符）。Lin等人[113][]拼接了字符层级表示、词层级表示与句法词表示（POS标注，依赖规则、词位置、头位置）来构建完整的词表示。Aguilar等人[114][]为NER任务提出了一种多任务方法，该方法利用了一个CNN来捕获正交特征和字符层级的词形状，如POS与词嵌入，模型结构是LSTM。Jansson和Liu等人[115][]提出利用深度学习在字符层级和词层级嵌入整合潜在迪利克雷定位（Latent Dirichlet Allocation，LDA）。Xu等人[116][]提出一个基于固定大小的通常忘记编码（FOFE）NER局部检测方法，FOFE探索了每个片段和其上下文的字符层级与词层级表示。Moon等人[118][]提出了多模型NER系统，对于含噪声用户数据，如推特等，字符嵌入和可视化特征与情态注意合并。Ghaddar和Langlais等人[109][]发现在神经NER系统中丢弃词汇是不公平的，他们提出一个可替换的词汇表示，该表示在线下表示中计算，**并且可以用于任何神经NER系统。**词汇表示维每个词进行计算得到120维向量，其中每个元素编码单词与实体类型的相似性。最近，Devlin等人[119][]提出一个新的语言表示模型，称为BERT，从transformers中产生双向编码器表示。BERT使用遮挡的语言模型来使预训练深度双向表示可行。对于给定的token，它输入向量由以下几个部分构成：汇总相对位置，分段与token嵌入。注意预训练模语言模型嵌入通常需要大规模数文本用于训练，并且本质上并入辅助嵌入（如，位置与分割嵌入）。因为该原因，作者将这些情境语言模型嵌入归入混合表示。

#### 上下文编码结构

接下来讨论广泛使用的上下文结构：卷积神经网络、循环/递归（recurrent/recursive）神经网络与深度transformer。

##### CNN

Collobert等人[17][]提出了句子方法网络，一个单词是否被标记需要考虑整个句子。在输入表示阶段后输入序列中的词都嵌入成N维向量，然后使用一个卷积神经层来生成每个单词周围的局部特征，卷积层的输出大小取决于句子中单词的个数。通过组合由卷积层提取的局部特征以构建全局特征。全局特征向量的维数是固定的，独立于句子的长度，未来应用子序列标准仿射层。提取全局特征时，有两个常用的方法：在每个句子中的位置上取最大或平均操作。最终这些固定大小的全局特征被输入标注解码器来计算网络输入的各个单词的标注。在Collobert的工作后，Yao等人[94][]提出Bio-NER来解决医学中的NER任务。Wu等人[120][]利用一个带一些全局隐藏节点的卷积层来生成全局特征表示。局部与全局特征然后输入标注的仿射网络来识别临床文本中的命名实体。

Zhou等人[96][]观察到在RNN中，相比于前面的单词，后面的单词更会影响最终句子表示。然而，重要的单词可能出现在一个句子中的任何位置。在他们提出的模型中，BLSTM-RE, BLSTM被用于捕获长期依赖并且获得输入句子的整个表示。然后使用CNN学习高层表示，再将其输入sigmoid分类器。最终，整个句子表示（由BLSTM生成）以及关系介绍（由分类器生成）再输入其他LSTM来预测实体。

传统上，一个以并行的方式使用LSTM处理长度为N的句子的时间复杂性为O(N)。Strubell等人提出ID-CNN，即**迭代空洞卷积神经网络，其在处理大规模上下文和结构化预测时计算效率更高**。实验结果显示，在获得相同的准确性情况下，ID-CNN的测试时间要比Bi-LSTM-CRF模型快14-20倍。

##### Recurrent Neural Networks

双线性RNN成为组合文本的深度上下文独立表示的事实标准[91,97][]。在Huang等人提出LSTM CRF方法后，出现了很多改进的方法[19,20,89,90,95-97,101,105,112,113][]应用BiLSTM做完基础结构来编码序列上下文信息。Yang等人[106][]在字符和单词层级上应用深度GRU来编码形态学和上下文信息。他们通过共享结构和参数进一步将模型拓展成跨语言与多任务联合训练。Gregoric等人[121][]在相同的输入中，应用多个独立的双向LSTM单元。他们的模型通过应用一个跨模型正则化项来促进LSTM单元的多样性。在跨多个小的LSTM模型上使用分布式计算，他们发现能够极大减少参数量。最近，一些研究[122,123][]设计了基于LSTM神经网络的嵌套命名实体识别网络。Katiyar和Cardie等人[122][]提出修改标准LSTM序列标注模型来解决嵌套命名实体识别。Ju等人[123]提出一个神经模型来识别嵌套实体，他们通过动态堆叠flat NER层，直到提取不到外部实体。每个flat NER层应用一个双向LSTM来捕获序列上下文。模型在当前flat NER层合并了LSTM层的输出来为检测到的实体构建新的表示，并将他们输入下一个flat NER层。

##### Recursive Neural Networks

递归神经网络是非线性自适应模型，该模型可以学习深度结构信息，通过以拓扑序的方式遍历给定的结构。命名实体与语言成分高度相似，如名词[98][]。然而，典型的序列标注方法仅考虑了句子的短语结构。为解决，Li等人[98][]提出NER分类每个节点中的选区结构。这个模型递归计算每个节点的隐藏状态向量，并且由这些隐藏向量对每个节点分类。自底向上方向计算这些子树节点的语义构成，自上而下的对应对象将包含子树的语言结构传播到该节点。给定每个节点的隐藏向量，网络会计算每个实体概率分布，在加上特殊的非实体类型。

##### 神经语言模型

语言模型是一族用于描述序列生成的模型。给定token序列，$$(t_1,t_2,...,t_N)$$,前向语言模型通过建模给定token$$t_k$$的序列历史$$(t_1,...,t_k-1)$$计算序列的概率[21][]，
$$
p(t_1,t_2,...,t_N) = \prod_{k=0}^Np(t_k|t_1,t_2,...,t_k-1)
$$
后向语言模型与前向语言模型类似，除了它以相反的顺序序列运行，给定未来的上下文以预测前面的序列：
$$
p(t_1,t_2,...,t_N) = \prod_{k=0}^Np(t_k|t+1,t+2,...,t_N)
$$
对于神经语言模型，token$$t_k$$的概率可以由RNN网络的输出计算得来。在每个位置$k$，得到两个上下文独立表示（前向与后向）然后将二者合并作为$t_k$作为语言模型最终的输入。这些语言增强模型知识在很多序列标注任务中[21,103,124-127][]。Rei等人[124][]以第二目标提出框架，学习预测数据集中每个单词周围的单词。在每个时间步，网络被优化去每个前面的token，当前的标记或者每个序列中下一个token。添加的语言建模目标鼓励系统学习更丰富的功能表示，然后将这些功能表示重新用于序列标记。Peters等人[21][]提出TagLM，语言增强模型序列标记器。在序列标注任务中，对于在输入序列中的每个token这个标注器考虑了预训练词嵌入和双向语言模型嵌入。LM-LSTM-CRF模型[125,126][]。语言模型与序列标注以多任务学习的方式共享相同的单词层。从字符层级嵌入、预训练单词嵌入和语言模型嵌入拼接成向量并且喂入单词层级LSTM。实验结果证明多任务学习是一种指导模型学习特定任务知识的有效方法。

Akbik等人[107][]提出了使用神经字符层级语言模型建模情境字符嵌入。他们利用前向-后向循环卷积网络的隐藏层状态来创建情境单词嵌入。这种模型的主要优点是字符层级语言模型独立序列号和固定字典。Peters等人[103][]提出ELMo表示，使用字符表示在两层双向语言模型的顶层计算而来。深度情境化单词表示的新类型有能力建模单词用例的复杂字符（e.g. 语法和语义）和跨语言环境的用法变化（多义性）。

##### Deep Transformer

Transformer利用堆叠的自注意和逐点，全连接层以构建基础块以编解码。不同任务[128-130][]中的实验结果表明Transformers质量卓越，同时所需的培训时间大大减少。基于transformer，Radford等人[131][]提出生成预训练Transformer（GPT）来阅读理解任务。GPT有两个阶段的训练过程。首先，在未标注数据上用Transformer使用语言建模目标来学习初始化参数。然后采取这些参数，使用监督目标用到目标任务，导致最小化改变预训练模型。不同与GPT，从Transformer双向解码器表示（BERT）提出通过联合条件在所有左右层上下文中预训练深度双线性Transformer[119][]。如图总结BERT[119][],GPT[131][],ELMo[103][]模型。Baevski等人[132][]提出基于双向Transformer的新颖的cloze驱动的预训练模式，以cloze样式目标训练，并预测所有给定所有左右上下文的中心单词。

![image](https://winterwindwang.github.io/assets/img/2020-12-30-dlner_reviews_03.jpg)

这些使用Transformer的预训练语言模型嵌入成为新的NER模式。首先，这些嵌入是情境化并且可以用于替换传统嵌入。一些研究[108][],[110][],[133-136][]通过衡量传统嵌入和语言模型嵌入合成取得了很好的性能。其次，这些语言模型嵌入可以使用额外的输出层进一步微调用于各种任务，包括NER和分块。特别是，Li等人[137,138][]分帧NER任务作为机器阅读理解（MRC）问题，可以使用微调的BERT模型来解决该任务。

##### 标记解码结构（Tag Decoder Architectures）

标注解码是NER模型最后阶段的阶段。它能将上下文独立的表示当作输入和生成标注序列。

###### 多层感知+Softmax

一系列NER模型[91][],[98][],[116][],[119][],[139][]使用早期的MLP+Softmax作为标注解码器。作为特定领域的NER任务，Tomori等人[140][]利用softmax作为标注解码来预测日本象棋游戏中的游戏状态。他们的模型需要将由文本与象棋格作为输入，预测21游戏中的命名实体。文字表示和游戏状态嵌入是同时输入softmax层，并使用BIO标注模式来预测命名实体。

###### 条件随机场

CRF在观测序列中是一个全局条件[73][]。很多基于深度学习的NER模型利用双向LSTM层作为标注解码器，如在CRF双线性LSTM层的顶部[18,90,103,141][]，和CNN的顶部[17,91,94][]。CRF是用于标注解码最常见的选择，[107][]使用CRF标注解码在CoNLL03和OntoNotes5.0上取得了SOTA性能。然而，CRFs可以充分利用片段层级信息，因为分段的内部性质无法全部以单词层级表示的编码。Zhuo等人[142][]提出门递归半马尔可夫CRF，这可以直接建模段而不是单词，并且可以通过门递归神经网络自动地提取段层级特征。最近，Ye和Ling等人[143][]提出混合半马尔可夫CRF来进行神经序列标注。该方法采取分段而不是单词作为基础单元来实现特征提取和转移建模。单词层级标签被用于派生分段得分。因此，这个方法可以利用单词和分段层级信息来进行计算分段得分。

###### RNN

很少地研究[88-90,96,144]探索RNN来解码标注。Shen等人[88][]认为RNN标注解码性能要由于CRF和在训练大规模实体类别时更快。

###### Pointer Networks 指针网络

指针网络应用RNN来学习以在各个位置输入序列位置相关地离散tokens输出序列地条件概率[145,146][]。通过使用softmx概率分布作为“指针”来表示变量长度字典。Zhang等人[95][]首次应用指针网络来生成序列标注。

##### 基于DL的NER总结

我们从以下三方面讨论优缺点：输入、编码器、解码器。首先，关于是否应该使用外部知识或如何将其集成到基于DL的NER模型尚未达成共识。一些研究[108,110,133,142][]显示，NER的性能可以通过外部信息提高。缺点如下：1）获取额外的知识是人工集成的。（如，地点词典）或计算代价（依赖）。2）集成外部知识对抗地影响端到端学习和损害基于DL系统地通用性。其次，Transformer编码器在大规模文集预训练模型上比LSTM更加有效。Transformeres在NER任务中失败的原因是没有预训练模型和训练数据集有限[147,148][]。另一方面，编码速度更快。其次，RNN和指针网络的缺点在于贪婪解码，这意味着当前步依赖前一步。当采取情境语言嵌入（如，BERT和ELMo）与softmax分类相比CRF不总是，CRF在不总会导致最好的性能。现在有很多现成的预训练网络。对于特定领域（医疗与社交媒体），使用带有特定域数据微调通用目的情境化语言模型通常是有效的方式。

Wu等人[120][]和Wang等人[149][]调查了中文临床数据中NER任务。Zhang和Yang等人[150][]提出句法结构的LSTM来实现中文NER任务，编码输入字符序列和潜在的单词以匹配此法。

##### 深度学习在NER中的应用

+ ##### 多任务学习 

  [161,17,106,160,124,90,96,114,162,163,164]。

  多任务学习主要假设是不同数据集共享相同的字符与词层级信息。然后使用多任务学习以更加有效的方式使用这些数据并且鼓励模型学习更加通用的表示。

+ 深度迁移学习

  迁移学习目的在利用在源域学习到知识的优点用到目标域[165][]。在NER任务中，传统方法通过驱算法[166-168][]。最近，少量方法[127,169-173,175][]提出来以实现低资源和跨领域NER。

+ 深度激活学习

  [181][]

+ 深度强化学习

  强化学习是机器学习的一个分支，灵感来自于行为心理学，其考虑了在某个环境中软件代理如何采取行动以使一些累积奖励最大[184,185][]。具体而言，强化学习问题可以定义成以下形式[186][]：环境被建模成一个人随机有限状态机，其输入是代理采取的行动，其输出是代理观测或奖励。其包含三个关键组件：（1）状态转移函数，（2）观测（输出）函数，（3）回报函数。最终的目的是通过尝试最大化累积回馈来学习一个好的状态更新函数与策略。Narasimhan等人[187][]将信息提取任务建模成一个马尔可夫决策过程（Markov decision process， MDP），即动态合并实体预测和提供灵活选择从自动生成替换集合中的下一个查询。过程由问题搜索查询，新源提取，提取值得对账，以及重复上述过程指导获得足够证据。代理为了学习一个好的政策，[188][]提出利用深度Q-network作为函数近似器，状态值函数（Q-function）是由深度神经网络近似。Yang等人[189][]利用远程监督生成的数据实现在新域数据中执行一个命名实体识别。样例选择器是基于强化学习并且从NE标注器中获得反馈，目的在于选择肯定句来减少标注噪声的影响。

+ 深度对抗学习

  深度对抗样本的目的在于使模型更加鲁棒，以攻击或减少其在干净样本上的测试错误率。于NER任务而言，对抗样本通常由两种方式生成。一些研究[192-194][]考虑源域的样本当作目标域的对抗样本，反之亦然。例如，Li等人[193][]和Cao等人[194][]合并来自其他域的对抗样本来鼓励学习跨领域NER的领域不变性特征。其他方法是通过向原始样本中添加扰动来生成对抗样本。例如，对偶对抗迁移网络（DATNet），由[195][]提出，目的在于解决低资源NER任务（数据源不足）。堆

+ 神经注意力机制

  注意力机制是基于在人类中发现的视觉松散的注意力机制。例如，人们通常高分辨率的集中注意力在特定图像的特定区域，以“低分辨率”感知周围区域。通过应用注意力机制，一个NER模型可以捕获输入中最具信息的元素。在NER任务中，有很多应用注意力机制的其他方式。Rei等人[105][]以端到端的方式应用注意力机制来动态地决定使用多少由字符和单词层级组件的信息。Zukov-Gregoric等人[197][]在NER任务中探索了自注意力机制，权重依赖于单独的序列（而不是两个序列之间的联系）。Xu等人[198][]提出基于自注意力的深度神经NER结构来利用文档层级全局信息。特别的，由以神经注意力机制的预训练双向语言模型得到的文档表示中来获取的文档层级信息。Zhang等人[199][]利用一个自适应联合注意力网络来处理推特中的NER。这个自适联合注意力网络是一个使用联合注意力过程的多模型网络。联合注意力包括视觉注意力和文本注意力以捕获不同模型之间的语义交互。

#### 挑战和未来方向

标签解码器选择的变化不如输入表示形式和上下文编码器的选择大。从Google Word2vec到最近的BERT模型、基于DL的NER受益于最近先进的语言模型预训练嵌入。无需复杂特征工程，我们有机会重新看到NER任务中未来的挑战和潜在的未来方向。

##### 挑战

**数据标注**：监督NER系统，包括基于DL的NER，需要大规模标注数据集。然而，标注数据集的时间消化大并且昂贵。这对于很多资源贫乏的语言模型和需要特定专家标注数据的特定领域而言是一个巨大的挑战。因为语言的模糊性，标注的质量和一致性是主要的需要考虑问题。例如，相同的命名实体可能被标注成不同的类型。例如，在句子”Baltimore defeated the Yankees“的”Baltimore“， 在数据集MUC-7中被标记为”位置“， 而在CoNLL03数据集中被标记为”组织“。”Empire State“和“Empire State Building”在CoBLL03和ACE数据集中被标记为“位置”，导致实体边界的混淆。由于数据标注的不一致性，在一个模型上训练的数据集在另一个数据集上表现得不好，尽管这两个数据集来自同一个域。

为使数据表中更加复杂，Katiyar和Cardie[122][]提出了嵌套实体很常见，在GENIA文集中17%的实体会被其他实体嵌入；在ACE文集中，30%的句子包括嵌入实体。需要开发通用的注释方案以适用于嵌套实体和细粒度实体，其中一个命名实体可以被分配多种类型。

**信息文本与未见实体**：体面的结果通常在正式文档数据集（如新闻文章）。然而，用户生成文本如WUT-17数据集，最佳的F-score的值些些微大于40%。由于数据量少和噪声，在信息文本中的NER（如，推特，评论和用户论坛）比正式文本中更具挑战。并且很多用户生成的文本是特定领域。在很多场景中，自电子商务和银行的NER系统需要以用户生成文本来处理。另外一个有趣的方面是评估NER系统的鲁棒性和有效性是识别在是在新兴讨论的背景下识别不寻常的，以前看不见的实体的能力。对于WUT-17数据集的这一研究方向存在一个共同的任务[200][]。（https://noisy-text.github.io/2017/emerging-rare-entities.html  ）

##### 未来研究方向

随着建模语言的进步和实际应用中的需求，我们期望NER可以受到更多研究者的关注。一方面，NER通常被视为下游应用的预处理组件。这意味着需要根据下游应用的需求定义特殊的NER，如命名实体类型和是否需要检测嵌套实体[201][]。

**细粒度NER和边界检测** 通用领域，很多现存的研究[19,97,109][]集中于粗粒度NER任务，我们期望在特殊领域有更多细粒度NER的研究来支持物理世界的各种应用。*细粒度NER的挑战在于NE类型的大量增加以及应用引入的允许一个实体有多个NE类型*。这需要重新审视常用NER方法中同时检测实体边界和类型，入使用BIES规范，并将O当作解码标注。值得考虑的是定义“命名实体边界检测”当作专用任务来检测NE边界，而忽略其NE类型。边界检测和NE类型分类的解耦，可以为共享不同域的边界检测提供一种常见和鲁棒的解决方案，并致力于NE类型分类的特定领域方法。正确实体边界在实体链接基础知识时可以有效消除错误传播。有很多研究[95,203][]考虑将实体边界检测视为NER中的中间步骤（子任务）。现在还没有在实体边界检测来达到更鲁棒的实体识别的研究。

**联合NER和实体链接** 实体连接（Entity linking, EL）[204][]同样被视为命名实体正则化和去模糊性，目的在于参考基础知识图谱，为文本中提及的实体指定唯一的标识，如在通用域的Wikipedia和生物域的UMLS(Unified Medical Language System)。目前很多研究在管道设置中分别地解决NER和EL任务。考虑通过成功链接实体携带的语义（通过在基础知识图谱中的联系）是非常丰富的[66,205][]。也就是说，链接实体有益于成功检测实体边界和正确分类实体类型。值得进一步探究联合NER和EL的任务，甚至实体边界检测，实体类型分类，实体链接，每个子任务都能从其他子任务的部分输出中受益，这能减少管道设置中不可避免的错误传播。

**在带有附属资源的信息文本中的基于DL的NER** 在信息文本或用户生成内容的基于DL的NER任务中，性能仍然较差。这呼吁在该领域进行更多的研究。特别地，我们发现NER的性能很大程度上受益于附属资源的可得性[206-208][]，如用户语言中的字典与命名定位。表3并没有提供很强的证据来支持在通用领域将地名词典用于额外特征可以提高NER的性能，我们考虑附属资源对于理解用户生成内容通常是必要的。问题在于在用户生成内容或特定领域文本中，对于NER任务如何获得匹配的附属资源，并且有效地将附属资源合并到基于DL的NER中。

**基于DL的NER的可伸缩性** 使神经NER模型变得可伸缩性仍然是一个挑战。当数据集的大小增长时，需要找到一个可以优化参数以指数增长的模型。因此，开发衡量模型复杂度以及可伸缩性的方法仍是一个可行的方向。另一方面，模型压缩和剪枝技术同样是是减少模型学习的空间和计算时间的一个选择。

**深度迁移学习** 很多实体应用重拍列现有的NER系统来识别命名实体。然而，由于语言字符的差异和标注的差异，在一个数据集上训练的模型在其他数据集上表现的不好。尽管存在一些将迁移学习应用在NER中的研究，这个问题并没有全部解决。未来的研究需要致力如何有效地进行迁移学习，可从以下几个方面着手：（a）更鲁棒的分类器，其在不同领域可以很好的工作。（b）探究NER中的zero-shot，one-shot，few-shot学习;（c）解决跨领域设置中的领域失配、标签失配问题。

**易用的基于DL的NER工具** 

+ Roder等人[210][]开发了GERBIL，为研究者、终端用户和开发者提供了易用的接口来获取benchmarking实体标注工具，目的在于确保实验的可复现性和可获得性。但是其没有提供最近的深度学习技术。
+ Ott[211][]提出FAIRSEQ,用于序列模型的一个快速、可扩展性工具包，特别是用于机器翻译和文字污名化。
+ Dernoncourt等人[212][]实现了一个框架，称NeuroNER，仅依赖循环神经网络。

一个易用的NER工具包可以指导开发者用一些标准模块实现：数据预处理，输入表示，上下文编码器，标准解码器，和有效策略方法。无论是否是专家都能从中获益。

**句式**

1. We do not claim this article to be exhaustive or representative of all NER works on all language. 我们不敢自称本文对所有语言中的NER都进行了详尽的调差或表示。



**词汇**

1. significant percentage of 大量的
2. natural kind term 自然种类
3. domain-specific 特定领域
4. off-the-shelf 现成的
5. enlighten 开导
6. coarse-grained 粗粒度
7. relaxed-match evalution 宽松匹配评估
8. syntactic-lexical   句法词汇
9. be cast to 被投射到  e.g. NER is cast to a multi-class classification
10. sentential context  句子语境
11.  gazetteers   地名词典
12. common priori knowledge   先验知识
13. capitalized 大写
14. produce 可以替换generate
15. nested named entity recognition 嵌套命名实体识别
16. a major merit of  。。。的主要优点
