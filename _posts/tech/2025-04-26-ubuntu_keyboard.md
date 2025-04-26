---
layout: post
title: ubuntu键盘被重定位
category: 技术
keywords: python, torch, yolo
tags: technical details
---

# 问题起因

系统：ubuntu 20.04 desktop

问题描述：不知道从什么时候开始，按键盘的波浪号键（代码是系统里面称为 TLDE）时，输出的却是数字4（我把这个问题称为按键错误定位），对于ubuntu终端深度拥护来说非常不友好。几个月前尝试解决该问题，无果。最近再次需要使用波浪号，于是便下定决心一定要解决这个问题。

注：可以使用F6输出波浪号。

# 解决方案

1. 首先，怀疑是不是不小心按倒键盘，导致键位的映射发生了变化。于是想到通过修改ubuntu系统的键盘映射来解决这个问题，于是问大模型“如何修改ubuntu系统键盘按键隐射”，并根据流程进行修改。大致流程如下：

   ```terminal
   1). 查看当前键盘布局： setxkbmap -print
   
   注意，在输出的内容中，xkb_symbols属性后的pc+us...是待修改文件，需要提前做好备份。
   
   2) 使用编辑器打开/usr/share/X11/xkb/symbols/us: sudo gedit /usr/share/X11/xkb/symbols/pc
   在打开的文件中，查找步骤1)中xkb_symbols属性所指内容，查看其是否与name[Group1]匹配。如果匹配就查看KEY <TLDE>是否映射正确。默认的KEY <TLDE>（波浪号）和KEY <AE04>（数字4）如下：
    key <TLDE> { [         grave,       asciitilde, dead_grave, dead_tilde ] };
    key <AE04> { [	   4,     dollar,      currency,         sterling ] };
   
   参考：https://blog.csdn.net/weixin_40407199/article/details/79234552
   ```

    修改完保存后，仍然没有效果。

2. 接着，怀疑是不是因为键盘本身的缘故，以前使用的键盘键位多一些，现在使用的键盘键位少一些。于是便有了以下流程：

   ```terminal
   sudo dpkg-reconfigure keyboard-configuration
   
   输入命令后，便有窗口弹出，但是我没看懂怎么修改作罢。可参考：https://zhuanlan.zhihu.com/p/670524395
   ```

3. 最后，怀疑是不是因为安装中文输入法导致键位映射出错。于是便删除settings->Region & Language-> Input Source中，关于中文输入法相关部分。但还是没用！！后来想着干脆只留English(US)死马当活马医，没想到！！！它竟然正常了，波浪号输出正常！

