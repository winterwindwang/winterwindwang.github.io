---
layout: post
title: Mitsuba+AirSim 安装
category: 技术
keywords: 渲染器、仿真平台
tags: technical details
---

# Mitsuba安装

环境配置：

```
1. python 3.6
2. VS2019
3. Scons 3.0
4. Mitsuba 1(0.6)
```

以下是安装步骤

## Step 1 拉取Mitsuba

拉取 mitsuba分支，并选择mitsuba下scons-python3子分支

```
1、git clone https://github.com/mitsuba-renderer/mitsuba.git
2、git checkout scons-python3
```

## Step 2  安装Scons

不要直接使用`pip install scons`，因为这个命令会安装最新的scons，可能会发生编译错误，这里我参考issues中一位前辈的成功案例[这里](https://github.com/mitsuba-renderer/mitsuba/issues/150)，选择安装 scons 3.0版本

```
activate conda_env  #激活你的虚拟环境
pip install scons=3.0 -i https://pypi.tuna.tsinghua.edu.cn/simple   # 使用源可以加快安装速度
```

## Step 3 编译

编译时，为避免出现各种错误，使用`x64  native tools command prompt for vs 2019`命令行工具，该工具在windows系统左下角可以输入 x64等关键词找到。（**建议在安装scons这一步就可以使用上述命令行**）

打开 命令行工具后，定位到mistuba文件夹，然后激活conda虚拟环境。接着直接使用命令即可完成编译

```
scons
```

## Step 4 

编译完成后，会生成一个dist文件夹，里面有`mitsuba.exe`与`misgui.exe`，但是后者还运行不了，这时需要安装QT5，具体详情可以参考[这里](https://banbao991.github.io/2021/04/26/CG/mitsuba/mitsuba-0-6-installation/)

在查看issues时，发现有人把编译好的dist文件共享出来，下载下来后发现还能用，参考[这里](https://github.com/mitsuba-renderer/mitsuba/issues/52#issuecomment-357189129)，下载地址是[这里](https://drive.google.com/file/d/1Ggt1KzxaGT737qReroIwt9iDM3dFrD__/view)

## 参考资料

[1]:https://zhuanlan.zhihu.com/p/359008593
[2]:https://github.com/mitsuba-renderer/mitsuba/issues/52
[3]:https://banbao991.github.io/2021/04/26/CG/mitsuba/mitsuba-0-6-installation/

# Mitsuba 2的安装

参考：https://zhuanlan.zhihu.com/p/359008593

遇到最大的问题就是`git submodule update --init --recursive`，在开启代理的情况下，使用`git clone https:`拉取代码会出错。关闭代理就不会出现这个问题，但是容易超时。为此，我将mitsuba文件夹中`.gitmodules`的`https:`修改成`git@github.com`（即ssh形式），然后就可以拉取成功了。如果后续还会出现错误，可以尝试关闭代理，然后再执行命令`git submodule update --init --recursive`

# AirSim 安装

环境要求:

```
VS2019，SDK 安装默认与最新
Unreal Engine 4.27 
```

使用VS2019自带的`developr command prompt for vs 2019`进行编译

参考：airsim详细教程(1)：win10配置airsim仿真环境(2021.8.12更新) - 宁子安的文章 - 知乎 https://zhuanlan.zhihu.com/p/267321662

