---
layout: post
title: linux服务器上使用anaconda等碰到的一些问题问题
category: 技术
keyword: conda, source, etc.
---

此贴用于记录使用linux服务器时，遇到的一些问题和相应的解决方案。

### 1 安装完Anaconda后，出现“conda: command not found”

如果在安装anaconda时，已经选择将conda的path添加到`~/.bashrc`时（推荐），使用如下命令即可解决该问题

```
source conda
```

*注*：

+ 当重新连接服务器时，也会出现该问题，使用上述的命令即可解决。
+ 此外，当我使用activate base(base为conda的基础环境)时，命令行最前面没有出现(base)，此时使用命令`source activate`就可以激活`base`环境，如果想激活其他虚拟环境，使用命令`conda acitvate your_env_name`即可

### 2 查看Linux文件下文件数量

查看当前目录下有**多少个文件及文件夹**

```
ls | wc -w 
```

查看当前目录下有**多少个文件**

```
ls | wc -c
```

查看当前文件夹下有**多少个文件**，**多少个子目录**

```
ls -l | wc -l
```

查看当前文件夹中的文件数量

```
/bin/ls -l | grep ^- | wc -l 
```

原文：[原文地址][http://www.chenxm.cc/article/556.html]

查看当前文件夹下指定后缀名的文件个数

```
find . -name '*.jpg' | wc -l
```

