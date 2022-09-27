---
layout: post
title: ubuntu20.04安装nvidia-driver，问题以及解决方案
category: 技术
keywords: ubuntu, cuda, nvidia driver
tags: technical details
---

>  前言：没有直接去装驱动，而是通过装cuda的方式，顺便把nvidia驱动装上。具体流程如下

# 系统配置

1. Ubuntu 20.04
2. 显卡 RTX3090ti

#  下载所需文件

为了装高版本的英伟达驱动，我选择装最新版的cuda，即cuda 11.7。但是现在很多包并不适配这么高的cuda版本（尤其是torch）。为此，权衡以后我选择安装两次cuda，第一次是为装最新的驱动(cuda11.7中自带的Nvidia driver 515)，然后安装cuda11.3作为日常使用。

下载完成后，在终端中定位安装包所在地址，然后使用以下命令安装

```
sudo sh cuda.11.7xxxx.sh
```

接下来是可能会遇到的问题

## 1 没有安装gcc， g++等

具体错误忘记了，反正按错误去搜，就是需要安装一个gcc的库

## 2  Nouveau驱动问题

对一个新装机刚装系统的用户而言，如果是桌面版的ubuntu，一般会使用Nouveau驱动。但是使用该驱动以后，就没法装nvidia-smi驱动，因此首先要使用以下步骤禁用该驱动，然后再装英伟达驱动。

步骤1：打开黑名单

```
sudo vim /etc/modprobe.d/blacklist-nouveau.conf
```

步骤2：添加两行语句：

```
blacklist nouveau
options nouveau modeset=0
```

步骤3：更新initramfs

```
sudo update-initramfs -u
```

步骤4：重启（！！！！非常关键，请参考以下几篇博客，否则会遇到开机后无图形界面显示的问题！！！！），本教程针对直接使用步骤四重启，解决黑屏问题。

[配置ssh，运行远程连接（可使用命令行操作）](https://blog.csdn.net/zhangjian2928/article/details/102948974)

[方法合集](https://blog.csdn.net/qq_43158129/article/details/109177618)

```
reboot
```

重启以后，你就会发现根本没有图形化界面显示。因此，你需要

步骤1：关闭计算机（长按开机键强制关闭，或者按开机键旁边的小键关闭（我的不管用））

步骤2：开启机器，并按Esc键（可以多按几次），直到出现

![在Ubuntu上使用恢复模式及Recovery Mode各选项的使用方法](https://ywnz.com/uploads/allimg/19/1-1Z510160511521.JPG)

然后，选择Advance options for ubuntu，之后会看到

![在Ubuntu上使用恢复模式及Recovery Mode各选项的使用方法](https://ywnz.com/uploads/allimg/19/1-1Z510160523543.JPG)

选择Recovery模式。

步骤3：进入Recovery模式以后，有以下几个选择

![在Ubuntu上使用恢复模式及Recovery Mode各选项的使用方法](https://ywnz.com/uploads/allimg/19/1-1Z510160630T8.JPG)

步骤4：选择root模式。选择root模式后，界面上面会出现终端，可以安装nvidia驱动

步骤5：查看是否已经关闭Nouveau。如果没有输出，则说明已经关闭。

```
lsmod | grep nouveau
```

步骤6：找到cuda所在目录，并安装nvidia驱动。安装完成后，使用nvidia-smi确认是否已经安装成功，如果有输出，则说明已经安装成功，并可继续后续操作；否则我也不知道怎么办~~！没遇到过。安装结束以后重启。即可正常进入图形界面。

# 3  重新进入系统后，英伟达驱动已经完成安装

现在需要做两件事

## 3.1 关闭系统的自动更新

大致有两种方法：第一种是在桌面中打开software&update中，把相关项设置为never

另外一种是直接在终端中配置命令

具体细节省略。

## 3.2 配置cuda路径

```
>> vim ~/.bashrc
export PATH=/usr/local/cuda-11.3/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.3/lib64:$LD_LIBRARY_PATH
>> source ~/.bashrc
```

## 另：安装多个cuda，用哪个链接哪个

```
To uninstall the CUDA Toolkit, run cuda-uninstaller in /usr/local/cuda-11.6/bin
To uninstall the NVIDIA Driver, run nvidia-uninstall

# 多cuda版本
cuda-8.0 # 笔者之前安装的cuda-8.0
cuda-9.0 # 刚刚安装的cuda-9.0
cuda # cuda-8.0 的软连接
#在切换cuda版本时
rm -rf /usr/local/cuda#删除之前创建的软链接
sudo ln -s /usr/local/cuda-8.0/ /usr/local/cuda/
nvcc --version #查看当前 cuda 版本

nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2016 NVIDIA Corporation
Built on Mon_Jan_23_12:24:11_CST_2017
Cuda compilation tools, release 8.0, V8.0.62

#cuda8.0 切换到 cuda9.0 
rm -rf /usr/local/cuda
sudo ln -s /usr/local/cuda-9.0/ /usr/local/cuda/
nvcc --version
```

