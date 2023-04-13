---
layout: post
title: CarlaUE4: 添加obj格式静态汽车，导入自定义纹理，更改场景中的纹理
category: 技术
keywords: pretrained model, linux, torch
tags: technical details
---

>  前言：一直纠结DTA中对汽车进行纹理替换是如何实现，最近学习CarlaUE4知识，并从其展示页面得到灵感，遂作记录。
>  注意，本博客只关注如何在CarlaUE4中导入新模型，并改变其外观；不对其法线贴图以及其他属性进行更改。

# 1 将obj模型文件转换为fbx模型文件

不清楚是否可以将Obj文件直接导入UE4，[这篇博客](https://continuebreak.com/articles/how-rig-vehicle-blender-28-ue4/)介绍了模型导入UE4前需要注意的一些地方。主要涉及调整模型的大小，同时如果模型姿势不对，也需要对模型进行旋转。

【注意】如果要使得模型会旋转，

# 2 将模型导入CarlaUE4，并为其设置纹理

### 2.1 带UV纹理材质球制作

首先打开CarlaUE4，并定位到`D:\carla\Unreal\CarlaUE4\Content\Carla\Static\Vehicles\4Wheeled`，新建文件夹来保存要导入的模型

![image-20230209231610193](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209231610193.png)

进入该文件，导入准备好的fbx文件。

导入后会生成该模型的相关文件，主要是包括：模型材质，模型，UV纹理图

![image-20230209231800819](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209231800819.png)

显然，这三者决定了模型在场景中的外观。在介绍下一步之前，首先将该模型拉到场景中，然后将其坐标设置成世界坐标系的原点，如果模型姿态不对，还要对其进行调整。可以在右侧的属性界面进行调整

![image-20230209232007811](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209232007811.png)

接着依葫芦画瓢，复制一个材质球将其命名为Mat_Test，然后导入一张自定义的UV纹理并命名为Tex_Test，最后双击材质球就进入编辑页面

<img src="https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209232414230.png" alt="image-20230209232414230" style="zoom:50%;" />

这时候，新建的材质上是没有纹理的，这时候需要鼠标左击右边的界面中的”基础颜色“（注意！！！如果这个界面没出现，则可以通过滚动鼠标来调整大小，并用鼠标右键调整位置以显示在界面中间），并向外拉出一条线，这时候会出现如下界面

<img src="https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209232701371.png" alt="image-20230209232701371" style="zoom:50%;" />

这时候键入”texture sample“并选就会出现以下界面

<img src="https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209232830197.png" alt="image-20230209232830197" style="zoom:50%;" />

这时候点击ERROR!上面的黑框，在左侧的界面中就会出现如下界面

<img src="https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209233034695.png" alt="image-20230209233034695" style="zoom:50%;" />

这时候，可以看到纹理一栏是None，这时候点击无，并在弹出的搜索框中，键入”Tex_Test“并确定，就可以为该材质球贴上自定义纹理。如下图所示

![image-20230209233214992](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209233214992.png)

这时候关闭该界面。

### 2.2 替换模型材质球，改变物体外观

将新建的材质球拖到下图红框处，即可实现对模型纹理外观的替换。

![image-20230209233513277](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20230209233513277.png)



# 3 遇到问题

在UE4中编译通不过，这时候需要检查将asset文件解压到`D:\carla\Unreal\CarlaUE4\Content\Carla`时是否正确，[这篇博客](https://blog.csdn.net/qq_44410849/article/details/119965114)介绍了解决办法



1、官方教程：向UE4中添加汽车https://carla.readthedocs.io/en/latest/tuto_A_add_vehicle/
