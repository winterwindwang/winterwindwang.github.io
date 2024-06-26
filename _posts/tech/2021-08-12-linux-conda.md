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
# 注 在imagenet validation中，执行该命令是50000(即文件个数为50000张图片)
```

查看当前目录下**所有文件的大小**

```
ls | wc -c
# 注 在imagenet validation中，执行该命令是1450000
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

## 3 使用nohup在服务器后台玉运行代码

 一开始在网上找到了下面这个版本的代码

```
nohup python -u run.py > log 2>&1 &
```

但是运行这个代码很快就遇到问题了，使用上述代码会出现 系统会认为`>log 2>&1`是`run.py`中的参数，就会报错。所以采用了如下的形式，该问题就消失了

```
nohup python ./run.py >> ./train.log 2>&1 &
```

运行以后吗，可以通过以下代码查看后台在运行的python命令

```
ps -ef | grep python
```

关闭nohup后台运行的进程

```
kill -9 pid
e.g.
kill -9 29589
```

## 4 Python中Linux和Window的差异

1. os.listdir()

```
在window中得到的是有序的文件名，如
listfile = ['a.txt', 'b.txt', 'd.txt', 'z.txt']
但是在Linux系统中得到的是无序的文件名，如
listfile = ['d.txt', 'z.txt', 'b.txt', 'a.txt']
为了减少不同系统照成的差异，建议使用
listfile = sorted(listfile) 
对list进行排序
```

## 5 服务器NFS挂载

```
通过nfs挂载磁盘
command：
apt-get update
apt-get install -y nfs-common
mkdir -p <新建的路径>
sudo mount -t nfs 192.168.2.1:<nfs路径> <本地路径>
例如：进入服务器的情况下运行如下命令
sudo mount -t nfs 192.168.2.1:/mnt/share1/ /mnt/share1/
```

## 6 GIT命令

git常见的命令就不提了。有时候会出现你使用了`git add.`和`git commit -m "update"`以后发现添加了不想要的东西，这时候就需要撤回。使用以下命令即可实现撤回

```bash
git reset --soft HEAD^
```

其中`HEAD^`的意思是上一个版本，也可以写成`HEAD~1`

如果进行了两次commit，都想撤回，可以使用`HEAD~2`

`--soft`：不删除工作空间改动代码，撤销commit, **不撤销git add .**

`--mixed`：不删除工作空间改动代码，撤销commit，并且撤销`git add .`操作。该参数是默认参数，即`git reset --mixed HEAD^`和`git reset HEAD^`是等效的

`--hard`：删除工作空间改动代码，撤销commit， 撤销`git add .`。注意完成这个操作后，就恢复到了上一次的commit状态。
