---

layout: post

titile:DeepLearning读书笔记（一）

category: 技术，Deep Learning

tag: React

keyword: React

----

## Deep Learning读书笔记（一）

1. 平方L2范数在数学和计算上都比L2范数本身更方便。例如，平方L2范数对x中每个元素的导数只取决于对应的元素，而L2范数对每个元素的导数却和整个向量有关。
2. 当机器学习问题中零和非零元素之间的差异非常重要时，通常会使用L1范数。每当x中某个元素从0增加eplison，对应的L1范数也会增加eplison。
3. 如果v是矩阵A的特征向量，那么任何缩放后的向量sv（s in R, s not eq 0）也是A的特征向量。此外，sv和v有相同的特征值。
4. 虽然任意一个实对称矩阵A都有特征分解，但是特征分解可能并不唯一。特征分解唯一当且仅当所有的特征值都是唯一的。矩阵是奇异的当且仅当含有零特征值。
5. 所有特征值都是正数的矩阵被称为**正定（positive definite）**；所有特征值都是非负数的矩阵被称为**半正定（positive semidefinite）**。