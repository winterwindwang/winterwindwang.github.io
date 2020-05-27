---
layout: post
title: Vmare ubuntu 学习 - 开发环境和相关技术设置
category: 技术
tags: React
keywords: React
---

## 1. 安装ubuntu

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
