---
layout: default
title: 医学命名实体识别综述
category: 文献阅读
tags: paper reading
keywords: 医学实体命名识别、医学实体标准化、医学实体关系抽取
---

# 医学命名实体识别

### Unified Medical Language System term occurrences in clinical notes: a large-scale corpus analysis  （sci2区，2012）临床笔记中的统一医学语言系统术语出现：大规模语料库分析
#### **摘要**

**目标** 为表征在大规模临床语料库中的医学语言系统元术语项，以及展示哪种类型的术语特征更容易跨数据源泛化。

**设计** 基于拥有Mayo临床机构记录的5100万文档语料集的现存UMLS术语项，该研究计算了术语项的字符属性、源术语(source terminologies)、语义类型和句法范畴的统计量。映射了在2010i2b2/VA文本中的现存术语；从基于Mayo的统计量设计了8各滤波器，并且应用于i2b2/VA数据。

**结果** 对于语料库分析，Mayo语料库中可忽略的映射术语超过6个单词或55个字符。在UMLS中源术语项，《消费者健康词汇》和《系统化的医学术语项（临床术语项）》在Mayo临床记录中覆盖率最高，分别为106426和94788个唯一术语。在UMLS中的15个语义组，其中7个组覆盖了92.08%在Mayo数据中出现的术语。句法上，超过90%的匹配术语项是名词短语。对于跨机构分析，使用了在i2b2/VA中的5个滤波器，将实际词典减少到UMLS大小的19.13%，并且匹配术语项仅有2%的下降。

**结论** 提出的语料库统计对于构建UMLS词典具有启发性。内在特征与源术语项（良好的形式，长度以及语言）很容易泛化到跨临床机构，但是术语项频率应谨慎地进行匹配。不同机构的映射术语项的语法组可能不同，在生物文献邻域会有更大的差距。

##### 问题

尽管源自生物医学本体的词典覆盖面令人印象深刻，临床记录仍会包含很多变体。我们计划通过大规模分析词典变体，同义词和相关术语来生成准确的变体。

##### 数据和方法

数据源： 用于文集分析的数据源是由梅奥医疗机构从2001年1月1号至2010年12月31号的医疗记录，从梅奥公司数据信托提取（Enterprise Data Trust, EDT）。EDT存储了结构数据，无结构文本和CNI生成评分标注，这些数据完整完整地了反应了梅奥医院的服务邻域，排除微生物学、放射学、眼科和外科报告。此外，梅奥提供每个可能的记录类型：临床记录、医院总结、术后说明、手术说明、进度说明、第三创伤和转移票据。

**字符串匹配算法**：AhoCorasick算法的改进版本。该算法将一个字典与有限状态机（对每个字母字符状态间的失败匹配均有有效转移）作为输入。作者使用归一化后的单词作为字母表，但是储存了每个匹配的最初字符串和每个提取匹配的报告结果。

**句法类别**：过滤掉非名词短语构架在很多临床NLP系统中是常见的方法。

**结论**：术语频率在各个机构之间的变化很大，应谨慎对待各个领域或机构。

###### 词汇

1. instructive 有启发性的

2. synonyms 同义词

3. metathesaurus 元同义词库

4. Clinical Notes Indexing (CNI)

5. radiology  放射学

6. ophthamology  眼科

7. tertiary 第三

8. stark 强大
9. It should be clear that 有一点应该清楚的是
10. reminiscent  使人想起
11. Systematized Nomenclature of MedicinedClinical Terms(SNOMED-CT)   医学术语的系统化命名法
12. Medical Subject Headings  医学主题词

#### Tuning Support Vector Machines for Biomedical Named Entity Recognition （ACL Workshop，2002）调整支持向量机以进行生物医学命名实体识别

##### 摘要 

我探索了用SVM实现生物医学命名实体识别。为了使SVM可以在大规模语料库上训练--易处理的GENIA语料库，我们提出分割非实体类别到子类中，使用部分语音信息。此外， 我们探索了如单词缓冲和由无监督学习训练得到的HMM状态。在GENIA语料库上的实验表明，我们提出的类别分割技术允许使用GENIA语料库训练，同时也提高了准确率。提出的新特征同样对准确率的提升有帮助。最后比较了最大熵标注方法与作者提出的基于SVM识别系统。

+ 动态决策在医学命名实体中非常重要。这解释了为什么SVM的性能不如使用了动态决策的最大熵方法。
+ 去噪算法非常重要

#### BIOMEDICAL NAMED ENTITY RECOGNITION BASED ON SKIP-CHAIN CRFS (2012, Conference on Industrial Control and Electronics Engineering) 基于skip-chain条件随机场的生物医学命名实体识别

##### 摘要

生物医学命名实体识别（BioNER）是命名实体识别研究的子任务。尽管存在大量的命名实体识别系统，他们拓展到生物子领域后并不能获得好的性能。BioNER成为一个具有挑战性的任务。我们应用一个skip-chain条件随机场模型来解决生物医学信息。这些远距离的依赖关系非常强大，可以识别出一些经常出现的命名实体并且对他们进行分类，特别是蛋白质和细胞类型类别。当我们在GENIA语料库中测试时，我们的方法在其他方法上能够得到非常大的性能提升，表现在precision,recall 和 F-score分数分别为72.8%, 73.6%和73.2%。

##### 正文

医学命名实体的研究主要分成一下三个类别：基于规则、机器学习和结合人类知识机器学习的混合方法。

特征选择方法是：手工选择的方法。

This means that for skip-chain CRFs the performance varies among different classes. 这意味着对于基于skip-chain的CRF，性能在不同类别之间有所不同。

#### Extracting comprehensive clinical information for breast cancer using deep learning methods（SCI 3区）使用深度学习方法提取乳腺癌记录中完整的临床信息

##### 摘要

*对象*：乳腺癌是女性群体中最常见的恶性肿瘤。乳腺癌病人的诊断和治疗信息在多种类型临床领域中非常丰富，包括临床病理数据、基因型和表型信息、治疗信息和预后信息。然而，当前研究主要聚集在从一种特殊类型的临床领域提取信息。该研究定义了一个完整信息模型来表示病人的全程临床信息。进一步，通过微调BERT语言模型，深度学习方法被用于从临床乳腺癌文档中提取概念。

*材料和方法*：用于该项研究的临床语料库来自中国三甲医院，语料库由100个乳腺癌病人的encounter note、手术记录、病理记录、放射学记录、进度记录和收费记录组成。我们的系统包括两个组件：一个命名实体识别组件和关系识别组件。对每个组件，我们实现了基于微调BERT的深度学习方法，这优于在多NLP任务中的其他SOTA方法。一个临床语言模型首先在大规模未标注的中文临床文本语料库中预训练。对NER任务，使用预训练的BERT提取上下文嵌入，并将其作为BILSTM-CRF模型的输入特征模型并且使用标注乳腺癌记录进行fine-tuned。进一步地，我们提出了一种方法来微调BERT模型来提取关系提取。可以视为一个分类问题，其中输入句子中被提及的两个实体被其语义类型替换。

*结果*：最佳的系统可以达到在NER任务中F1得分93.53%，关系提取任务96.73%。额外评估显示了基于深度学习的方法，即微调BERT优于NER任务中传统BILSTM-CRF和CRF机器学习算法与关系提取中的attention-BiLSTM和SVM算法。

*结论*：在该项研究中，我们开发了一个机器学习方法，即微调BERT模型来提取乳腺癌概念和他们贡献。证明与传统机器学习算法相比，我们的方法性能更加优秀，并且有望将该方法推广到医疗领域中其他NER任务饿关系提取任务。

##### 正文

EHR二次使用的一个关键挑战是大规模有用的临床信息是无结构的临床文本。

**方法**：对于临床文本大于BERT的最大输入字（128），作者使用空格逗号等进行分割以编码所有文本来包含所有的文本（如果一段文本被分割成多段嵌入，如何整合/合并这些嵌入呢？）。使用BIO格式标注。

CRF在NER任务中的取得很好的性能，包括医疗信息提取[20-22]。常见的NER特征为：n-gram，prefix-suffix，orthographic ，离散单词嵌入。中文字符嵌入在3GB临床记录语料库中训练[23]。

##### 限制

数据集较少，只有100个病人的数据，没有覆盖其他更多的情况，导致有一些概念和关系的性能较低，更多的数据集将会提高方法的性能。10折交叉验证是因为当前数据集中特定实体类型的稀疏性。

##### 未来工作

1. 使用更多得数据集，并且分割成训练、验证和测试集。
2. 提取的信息被会被正则化到标准的术语项，如ICD10，实现更加实际的临床应用。
   + 整合图像数据以实现CT、MR图像和微观病理图像。对实际临床应用，从放射测试中合并图像分析和文本处理将会有更好的发现。
   + 由于所提取信息的准确性很高，因此将进行统计关联分析以回答有关病因，流行病学和预后因素的问题。如“哪些因素与乳腺癌有关？”，“哪些因素会影响乳腺癌的诊断？”

###### 词汇

1. prognosis 预后
2. encounter notes
3. operation records
4. pathology notes
5. radiology notes
6. progress notes 
7. discharge summaries   
8. disseminate 传播
9. predicting hospital readmissions 预测住院率
10. susceptibility  易感性
11. orthographic  正交法



# 医学命名实体标准化

#### Multitask learning for biomedical named entity recognition with cross-sharing structure（BMC， SCI3-4区）， 具有交叉共享结构的生物医学命名实体识别的多任务学习

##### 摘要

**背景** 生物医学命名实体识别（BioNER）是生物医学文献挖掘中的一项基础并且重要的任务，它会影响很多下游任务的的性能。大多数BioNER模型依赖特定领域特征或手工创建的规则，但是从大规模数据中提取特征需要大量时间和人力。为解决这个问题，神经网络模型别用于自动地学习特征。最近，多任务学习被成功地应用到生物医学挖掘的神经网络模型中。对于BioNER模型，使用多任务学习利用从多个数据集中的特征来提高模型的性能。

**结果**：在实验中，作者比较了作者提出模型与其他多任务模型，并且发现提出模型在基因、蛋白质、疾病类别的数据集上均优于其他方法。作者同样测试了不同数据集对的性能来寻找到最佳的数据集对。此外，作者探索并分析了使用子数据集的不同实体类型的影响。当数据集变小时，我们的模型仍然能产生好的结果。

**结论**：作者位BioNER提出了一个带跨分享结构的新颖的多任务模型来提升多任务模型的性能。在作者模型中的跨分享结构利用在训练阶段从数据集对中的特征。关于最佳数据集对的详细分析和实体类型之间的影响可以为多任务训练合适数据集对选择的指导。作者的实验在https://github.com/JogleLew/bioner-cross-sharing.  

现有神经网络的缺点是特征需要为每个数据集或每个实体类型设计特殊的特征，以获得较好的性能。作者认为通过共享主任务与附属任务之间的表示，多任务模型可以提高主任务的性能。尽管多任务模型可以优化主数据集的性能，使用训练数据集的组合可能可以提高判别性性能。

私有Bi-LSTM用于捕获任务独立特征。共享Bi-LSTM用于捕获数据集的通用特征，S是通用特征的表示。

**Limitations**： 很难预测在训练模型前一个数据集是否对其他数据集有帮助。其他限制模型的当前实现可能无法在所有数据集上产生很好的结果。

**未来的研究方向**：同时训练更多的数据集可以提升跨数据集信息和更好的性能；此外，调整作者提出的跨共享结构来提升在特定数据集的性能或组合新结构的多任务模型，比如BIioBERT。最终，作者的工作可能存在实体类型冲突问题，我们通过源数据集，使用实体类型统一进行识别，以达到性能提升。

##### 词汇

1. discrepancy 差异

#### A Knowledge-driven Generative Model for Multi-implication Chinese Medical Procedure Entity Normalization (EMNLP, 2020) 知识驱动的多含义中医程序实体规范化生成模型

##### 摘要

医学命名规范化，即将文本中提及的医学实体联系到知识库中的实体，在医学自然语言处理中是一项非常重要的研究。在本文中，作者主要关注中医程序实体规范化。然而，非规划化中文表达和组合程序给作者的问题带来了一些挑战。现有基于判别模型的策略很难满足规范化组合程序的要求。作者提出了一个序列生成框架来直接生成所有相应的中医实体。作者采取了两个策略：基于类别约束解码和基于类别模型提纯来避免不真实的结果。当提及包含多个程序概念时，该方法能够链接实体，并且综合实验表明，所提出的模型可以在现有基准之上取得显著的改进，特别是在多含义中医程序中。

##### 正文

在医学命名实体规范化中，判别模型被用于衡量原始提及与选择候选之间的语义相似度得分，并且将得分最高的作为规范化结果。这些方法被称作“Selecting and Re-ranking（SR）”。这些策略被证明在无含义命名规范化问题中非常有效，因为一个提及只需链接到给定知识库中的一个标准实体。但是这些策略却无法应用到多含义问题中，这是SR方法的缺陷。为解决多含义挑战，作者提出一个序列生成框架来直接生成所有相符合的标准实体。

相同标签下的实体通常分享通用信息。基于Li et al， 作者提出一个基于分类的提纯策略来使模型参数更好地适应输入提及的类别。为此，对于每个输入提及，作者首先采用通用模型对其规范化，从输出中获得类比信息。然后将原始测试集重新分配成一些基于类别的子测试数据集。最终，对于每个子测试数据集，从训练数据中找到相同类别中的句子对，并且用他们来fine-tune通用模型的参数。此外，对于每个提及，通过Beam搜索替代从给定知识库中选择候选策略，并由生成模型生成的一些标准候选实体。然后，使用一个预训练的判别模型来打分并且对所有候选实体重新排序。

##### 结论

在本文中，作者为中医程序规范化提出了一个带有基于分类约束解码与模型提纯机制的序列生成学习框架。提出的模型可实现端到端地为所有类型的输入提及生成所有相应标准实体，特别是“多含义”提及。使用“生成和重新排序”策略将提出的生成模型与判别相似重新排序方法进行集成，以进一步提高归一化性能。综合实验结果证明了提出模型远远超过了基准方法。此外，提出模型可应用于“无含义”与“多含义”中医程序提出的规范化。虽然考虑到特定领域的复杂性与训练数据的缺乏，这个挑战任务远未解决。在未来作者的计划是解决当资源受限时，如何提高医学实体规范化的性能。

##### 词汇

1. scarcity  缺乏

# 医学领域的实体关系抽取方法

#### Use of Lexico-Syntactic Patterns for the Evaluation of Taxonomic Relations  （2014）利用词典句法模式来分类关系的评估

##### 摘要

在本文中，作者提出了一种受限领域本体分类关系的评估方法。作者使用在与本体领域相联系语料库中发现的证据来决定分类关系的有效性。作者的方法将词典-句法模式应用到概念完全不同的类别关系分类的评估中，并且使用基于这些关系的包含（一个关系完全包含另外一个关系）的特殊技术。这两种技术的结合使两种受限制邻域本体类别关系的自动化评估成为可能。在线学习领域的单个本地性能可以达到70%，对于人工智能领域相关的本体，能达到88%的性能。

##### 结论

本体质量评估是一个非常具有挑战性的领域，需要借助计算语言社区来解决。在本文，作者提出了一种基于词典-句法模式和语料库参考的方法，语料库可以决定一个给定受限领域本体的类别关系的准确率。实验显示在评估过程中在高频出现时，存在高协议的模式，独立于本体领域。方法对指定AI本体的准确为88.29%，SCORM本体的准确率为70.42%。这些结果证明了作者系统的质量，经过两个专家评估，得到了可靠的80%的平均准确率。未来，考虑到存在大量的关系，作者计划评估系统的可靠性。此外，还想使用其他本体来进行评估。



##### 词汇

subsumption 包含，包容

#### Patterns Used to Identify Relations in Corpus Using Formal Concept Analysis（FCA） （2015）使用形式概念分析来识别语料库中关系的模式（与上一篇的作者是同一个人）

##### 摘要

在本文，作者提出一种用于自动识别受限领域本体关系。作者使用在与本体域相关联的语料库中的证据来决定本体关系的有效性。作者的方法应用形式概念分析，一种数据分析的方法，但是在本例中将用于在受限域语料库中的关系搜索。形式概念分析被用于评估两个本体的本体关系。在第一个本体中，类别关系的性能可以达到96%，非类别关系的的性能可以达到100%。在第二个本题中，类别关系与非类别关系的性能分别为92%与98%。

#### Computing Semantic Similarity of Concepts in Knowledge Graphs  （2016，TKDE，SCI二区，IEEE Transcation）计算知识图中概念的语义相似度

##### 摘要

本文提出了一种在知识图（如WordNet, DBpedia）中概念的语义相似度的方法。现有在语义相似度方法主要集中于概念间（如路径长度、深度）语义网络的结构，或者依赖概念的信息内容（Information Content, IC）。作者提出一种语义相似度方法，称为wpath，组合这两种方法，使用IC权衡概念之间的最短路径长度。传统的基于语料库的IC方法是根据概念文本语料库上的分布来计算的，这是准备包含标注概念的领域语料库所需要的，并且具有很高的计算成本。由于已经从文本语料库中提取了实例，并通过KG中的概念进行了注释，因此提出了基于图的IC，以基于在实例上的分布来计算IC。通过在著名的字相似度数据集上进行的实验，结果显示了wpath语义相似度方法较其他方法语义相似度方法具有更好的统计性能的提升。此外，在真实类别分类评估中，wpath方法在准确率和F得分上显示了最佳的性能。

##### 正文

语义相似性度量有两种方法：基于语料库与基于知识的方法。基于语料库语义相似度测量是基于根据依赖文本依赖大规模文本集中学习而来。如果两个单词的周围上下文相似，则它们将具有高度的分布相似度。仅在语料库中出现的单词才会被统计，而不是识别单词的特定意义以及检测单词间的语义关系。因为基于语料库的方法考虑了所有类型词典中单词间的关系，它们主要测量单词间语义相关性。另一方面，基于知识的语义相似度方法被用于测量概念语义网络间的相似度。

底层概念间的相似度比顶层度概念更具相似性。如果两个概念贡献一个更加特殊的概念，它意味着由于它们的LCS的IC更高，它们共享更多信息并且因此更加相似。

##### 结论

测量概念的语义相似度在很多应用中非常重要。在本文中，作者提出了将路径长度与IC结合起来的wpath语义相似度方法。基础的想法是使用概念间的路径长度来表达他们的差异，然后再用IC来考虑概念间的共性。实验结果显示wpath方法能够产生较其他语义相似度方法更高的统计性能提升。进一步地，提出了基于图的IC以基于概念在实例上的分布来计算IC。实验结果显示基于图的IC对res, lin 和 wpath方法非常有效，并且性能类似于传统的基于语料库的IC。此外，基于图的IC具有很多益处，因为它不需要语料库，并且允许在基于可获得的KG的在线计算。基于在简单方面类别分类任务的评估发现，提出wpath在准确率和F得分获得最佳性能。在未来工作中，本文主要讨论了语义相似度而不是通常语义相关性。因此，未来的另一项工作可能是基于知识的方法与基于语料库的方法之间的语义相关性。最终，作者将WordNet与DBpedia结合，进一步探索使用提出方法来测量KG中实体相似性与相关性。

#### Sematch: Semantic Entity Search from Knowledge Graph （2015）Sematch：从知识图上进行语义实体搜索

##### 摘要

随着知识图谱发表成链接公开数据集数量的增加，语义实体搜索需要发展新的应用。然而，结构查询语言如SPARQL的使用对于无技巧的人来说是个挑战，这些人需要掌握查询语言和获得链接数据知识库的潜在本体的知识。在本文，作者提出了Sematch框架用以在知识图谱中进行实体搜索，知识图谱组合自然语言处理查询处理，实体链接，实体类型链接，以及基于查询扩展的语义相似度。系统在数据集中被验证其合理性，并且开发好了原型，该原型可将自然语言查询转换成SPARQL。

##### 结论

本文，作者定义了SRTQ和为SRTQ定义了实体搜索任务。通过组合传统的NLP技术NER，WSD和LOD技术如实体链接提出回答SRTQ的框架。框架被设计用于扩展包括在NLP和LOD中更加先进的算法来解决STRQ。一个原型系统Sematch被实现并且在SRTQ验证数据集下完成验证，这些数据集都是采集自在语义搜索和SQ的几个LOD比赛中。验证结果显示Sematch系统在回答SRTQ中显示出好的性能并且提出的基于类型扩展算法的语义相似度可以提升实体搜索的recall率同时保持一定程度下的precision。此外，在查询图生成中的关系扩张可以提升搜索recall的性能但同时会使precision变得不可接受。结果，未来的一个工作可能开发更加先进的算法来保搜索的准确率同时扩展联系。进一步地在提纯基于类型扩展算法的语义相似度时进行更多研究，来优化执行时间和搜索性能。开发方法来联合WSD，NER和实体链接来消歧义同样是未来的工作方向之一。