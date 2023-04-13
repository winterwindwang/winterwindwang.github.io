---
layout: post
title: NeRF相关文献调研
category: 文献阅读
keyworks: Neural radiance field
tags: 对抗样本攻击
---

# NeRF基本理解

NeRF是一种从多视角图片合成三维模型的技术。该研究领域有长期的历史，过去的研究主要集中在通过数值方法重建模型。而NeRF通过训练一个神经网络（MLP）对多视角图片进行合成。

具体而言，NeRF的输入包括：

+ 不同视角的相机参数：空间坐标（x,y,z）以及视角方向（θ，Φ）
+ 对应的渲染图片

在训练时，需要将空间坐标系从相机坐标系转换到世界坐标系，然后对相机参数进行**位置编码**，并计算出射线ray。其次，对相应的图片进行网格化，并使用射线逐次穿过网格，形成训练batch。

【注意】NeRF的训练数据大部分由COLMAP这个软件生成。此外还包括：ShapeNet，DTU等

# 【2020ECCV】NeRF: Representing Scenes as Neural Radiance Fields for View Synthesis

>亮点：开山之作



# 【2019ICCV】Texture Fields: Learning Texture Representations in Function Space

> 亮点：使用神经网络来学习纹理渲染过程。



# 【2021CVPR】NeuTex: Neural Texture Mapping for Volumetric Neural Rendering

>亮点：提出了一种对体素神经渲染的神经纹理映射方法，其优势在于可以从输入图片中重建三维物体的网格与其对应的纹理。



# 【2021CVPR】GIRAFFE: Representing Scenes as Compositional Generative Neural Feature Fields

>亮点：2021年CVPR的best paper。可控的三维物体合成



# 【2022ECCV】NeuMesh: Learning Disentangled Neural Mesh-based Implicit Field for Geometry and Texture Editing

>亮点：对3D网格进行解耦，然后可分别对形状和纹理进行编辑。

