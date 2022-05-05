---
layout: post
title: FCA技术细节--face文件的生成
category: 技术
keywords: fca, 3D max
tags: technical details
---

# [FCA](https://www.aaai.org/AAAI22Papers/AAAI-8153.WangD.pdf)技术细节：face文件的生成

 

工具：3ds MAX建模软件（本人使用的是2020版）

3D模型：audi_et_te.obj模型

本文将结合操作图片介绍FCA中face文件的生成。以DAS论文中提供的audi_et_te.obj模型为例，生成faces索引文件，即控制3D模型哪些表面区域可以涂装对抗性迷彩。



## First step:

用3ds MAX软件打开3D模型，得到界面如下：

![3dmodel_loaded](https://gitee.com/freeneuro/PigBed/raw/master/img/3dmodel_loaded.jpg)



## Second Step

将模型选中为可编辑状态，并根据下图所示步骤选择3D模型的face。

![3dmodel_edit](https://gitee.com/freeneuro/PigBed/raw/master/img/3dmodel_edit.jpg)

## Third Step:

在完成可涂装迷彩区域的选择后，在状态栏：Scripting=>New script中打开代码运行器，如下图所示。

![3dmodel_script](https://gitee.com/freeneuro/PigBed/raw/master/img/3dmodel_script.jpg)

代码如下：

```3dmax
(
out_name = "D:/DataSource/DAS/faces.txt"
out_file = createfile out_name

fSel = $.selectedFaces	
for face in (fSel) do (
	format "%\n" face.index to:out_file
	)
					
close out_file
)
```

最终在代码编辑状态栏中，按照如下步骤运行代码，即可在指定位置得到faces文件

```
Tools=>Excute All
```



最后感谢[DAS论文](https://openaccess.thecvf.com/content/CVPR2021/papers/Wang_Dual_Attention_Suppression_Attack_Generate_Adversarial_Camouflage_in_Physical_World_CVPR_2021_paper.pdf)作者提供上述代码
