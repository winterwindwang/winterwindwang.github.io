---
layout: post
title: neural_rendered的不成功安装记录
category: 技术
tags: neural renderer, build, ninja
keywords: 神经渲染器安装，环境编译
---

> 导语：复现DAS论文时，需要使用神经渲染器，在我的环境下安装时遇到一些问题，先将不成功的解决方案记录下来。

### 1 系统环境

+ Python 3.6
+ VS2017
+ GTX3070
+ CUDA11.0  

### 2 遇到的问题以及解决办法

1. 第一个问题，报了一个无法找到`cl.exe`文件的错误，解决这个错误只需要使用x64 Native 

Tool command这个工具，这个问题就消失了

2. 第二个问题，报了一个无法加载`load_texture.obj`的问题

   > 解决方案：使用python setup.py install 命令运行，等到报错的时候就去`build/temp.win-amd64-3.6/Release`下寻找build.ninja。
   >
   > 首先，下载ninja并将其编译，然后C盘下新建一个文件夹Local并在其下新建Ninja文件夹，把编译好的ninja.exe放到里面，再把路径名配置到环境变量里面。回过头看nerual renderer文件夹下有个build文件夹，里面有个cuda文件夹，里面有个build.ninja文件，如果报算力不匹配(如sm_86不匹配，就将其改成sm_80)，改完后然后在命令行里面使用ninja -f build.ninja编译.obj文件。后续的无.obj报错的解决方案类似。