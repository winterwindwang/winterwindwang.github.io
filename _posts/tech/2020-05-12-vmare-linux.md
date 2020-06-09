---
layout: post
title: Vmare ubuntu 学习 - 开发环境和相关技术设置
category: 技术
tags: React
keywords: React
---

## 1. 安装ubuntu

### 如何使window10（宿主机）可以连接上vm中的ubuntu， 如ip地址为：192.168.111.131
问题：虚拟机能够通过宿主机的ip地址ping通宿主机，宿主机无法ping通虚拟机
解决方法：
1、将虚拟机设置设置为“NAT模式”
2、在虚拟机内使用以下命令查看虚拟机的ip（网段）
```
ifconfig -a
```
3、在win10系统中，在网络设置中对VMNet8的ip4进行如下设置：
```
IP地址：192.168.111.1
子网掩码：255.255.255.0
```
最后使用ubuntu的ip地址（192.168.111.131）登录ubuntu。

## 2. vim使用
如下的目录文件
```
.
├── checkpoint
├── model
├── main.py
├── data
└── test.py
```
使用tab键自动匹配当前目录中所有符合条件的文件/文件夹, 如以下命令可自动匹配main.py文件
```
cd ma + tab key
```

## 3.安装Nvidia Cuda cudnn
cuda, cudnn之前首先得安装Nvidia显卡驱动，而ubuntu中，如果出现以下情况则会导致驱动安装失败。
Ubuntu kernel is built by generic 7.5 but current generic version is 4.85 

可能在重启电脑后，使用```nvidia-smi```命令显示无法与系统交互时，解决方法如下：
1、执行命令```sudo apt install dkms```  
2、执行命令```sudo dkms install -m nvidia -v NVIDIA-version``` 其中Nvidia-version是已安装的NVIDIA版本号
DKMS 是一个便于构建和安装内核模块的框架。DKMS 允许 Clear Linux OS 提供钩子，为新版本内核自动重新构建模块。
因此它可以将NVIDIA与Ubuntu进行连接，保证nvidia的使用

安装好的CUDA以及CUDNN无效的解决方案：
1、如果你的conda环境等配置是~/.bashrc中的，那么你就的激活exec bash, 并且激活source ~/.bashrc
2、如果你当前的环境是zsh, 你使用source ~/.zshrc后，你将会遇到你conda中所有的python都变成了2.7版本，且你的conda虚拟环境都仅有带python2.7的基础环境，
 你之前安装的环境都不见了