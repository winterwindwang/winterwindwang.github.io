---
layout: post
title: Python项目中配置Docker
category: 技术
keywords: python, docker
tags: python, docker
---

博客简介:

+ 在Docker中拉取ubuntu镜像，然后实例化一个容器，然后在容器中安装fenics包

+ 将一个Python打包成docker镜像，然后上传到docker images中

# Docker

我对Docker的理解是，不依赖于任何系统（可跨平台使用）的项目可执行环境。具体地来说就是，由于不同设备存在的差异可能会导致A电脑上能运行的项目，到B电脑上就不能运行了。为了解决这个问题，就必须提供第三方平台，将A电脑上的项目部署到独立的第三方平台上（Docker），然后B电脑想用的时候，直接从第三方平台上拉取这个项目，就可以运行了。

# 一、下载和安装

安装过程中会提示要先安装`wsl2`，按要求去微软的官网下载并安装就行了。

## 配置Docker加速

我用的是Docker v1.22.4版本，参考[这篇](https://yeasy.gitbook.io/docker_practice/install/mirror)博客，将加速源配置到Docker Engine中，配置完成后，点击`Apply & Restart`保存后Docker就会重启并应用配置的镜像地址了。

```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com",
    "https://itljlss8.mirror.aliyuncs.com"
  ]
}
```

Docker Engine的位置如下图所示

![image-20211208205516946](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211208205516946.png)

## 前提概要

两种生成`requirements.txt`的方法

### 第一种

```python
pip freeze > requirements.txt
```

这种方法会导出整个虚拟环境下的包

### 第二种

```python
安装包：pip install pipreqs
在项目目录下使用命令：pipreqs .
```

但是使用`pipreqs .`命令时会报错误

```python
Traceback (most recent call last):
  File "e:\software\anaconda3\envs\ml\lib\runpy.py", line 193, in _run_module_as_main
    "__main__", mod_spec)
  File "e:\software\anaconda3\envs\ml\lib\runpy.py", line 85, in _run_code      
    exec(code, run_globals)
  File "E:\software\Anaconda3\envs\ml\Scripts\pipreqs.exe\__main__.py", line 7, 
in <module>
  File "e:\software\anaconda3\envs\ml\lib\site-packages\pipreqs\pipreqs.py", line 488, in main
    init(args)
  File "e:\software\anaconda3\envs\ml\lib\site-packages\pipreqs\pipreqs.py", line 418, in init
    follow_links=follow_links)
  File "e:\software\anaconda3\envs\ml\lib\site-packages\pipreqs\pipreqs.py", line 115, in get_all_imports
    contents = f.read()
UnicodeDecodeError: 'gbk' codec can't decode byte 0x81 in position 248: illegal multibyte sequence
```

这时，使用以下的命令，即可解决问题

```python
pipreqs ./ --encoding=utf8
```

# 二、Docker使用--example

+ 拉取python包

  ```python
  docker pull python:3.7.4
  ```

+ 查看镜像

  ```python
  docker images
  ```

+ 运行容器

  容器就是应用所在的系统了，这就是能够运行一个python应用程序的完整环境。在这个容器中利用pip安装依赖包，放入开发好的python程序，然后将这个容器导出为image，就可以共享使用了。

  1. 运行容器

     -it：-i和-t的结合，感觉就是如下图，直接进入容器的命令行模式。

     –name：自定义容器名称，不用的话会自动分配一个名称。

     -v： 将本地文件夹`D:python/text_similar`与容器文件夹/root/text_similar共享。

     python:3.7.4：要运行的镜像名+TAG

     bash：进入容器命令行。

     ```python
     docker run -it --name pytest -v D:python/text_similar:/root/text_similar python:3.7.4 bash
     ```

     执行上述命令后，就进入了容器的环境（linux系统），所有的操作均在容器中进行。但是需要注意的是，如果使用`rm -rf`删除共享文件夹里面的内容，那么也会删除本地相关的文件夹。

  2. 在容器下安装python包

     ```python
     pip install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
     ```

  3. 查看容器中的文件`text-simliar`:

     ```python
     cd /root/
     ls
     ```

# 三、制作Docker 镜像

代码基于老版的第八次作业。

目的是编写dockerfile文件，然后使用docker打包并上传到docker hub中。

## 1、Dockerfile文件的编写

```python
# 这一步是指定基础的python环境，要与你项目所用python相同
FROM python:3.6.13

# 讲当前目录下所有的文件添加到 镜像中的/app下
COPY . /app

# 将/app设置为工作目录，这样以下的操作均会在该目录下执行
WORKDIR /app

# 首先更新pip
RUN pip install --no-cache-dir --upgrade pip
# 然后安装requirements.txt
RUN pip install --no-cache-dir -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# 安装完成后，打包
RUN python setup.py install

CMD [ "python" "simplelayout"]
```

这里需要注意几点：

+ 第一是为防止命令`RUN pip install --no-cache-dir -r requirements.txt`安装不成功，requirements.txt中一定要指定版本，注意版本号是`x.x.x`格式，后面不能出现`x.x.x.postsdasd`这样的后缀，否则会装不上包
+ 第二是python版本最好最好与你项目所用的版本号一致，因为不同的python版本需要的包不一样，如果用不同的python版本安装requirement.txt可能会安装不上。

## 2、Docker build

在根目录下，执行以下命令以生成镜像

```python
docker build -t simplelayout:v1 .
```

不要忘记后面那个点  `.`， 安装完成后，使用`docker images`查看：

![image-20211208202543407](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211208202543407.png)

## 3、Docker Run

最后实例化一个容器，并查看是否包含simplelayout

![image-20211208202907018](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211208202907018.png)

从上图可以看出，容器被实例化，并且simplelayout也在容器中。

# 4、上传镜像至网上

1. 注册Docker账号
2. 在命令行中登录

```python
docker login -u 用户名 -p 密码
```

3. docker hub网站创建镜像信息（这一步省略也不影响下面的步骤）
4. 本地tag关联

```python
docker tag <existing-image> <hub-user>/<repo-name>[:<tag>]
# 具体命令
docker tag simplelayout:v1 用户名/simplelayout:v1
```

5. push到docker官网

```python
docker push <hub-user>/<repo-name>:<tag>
# 具体命令
docker push 用户名/simplelayout:v1
```

下面是仔细的流程

![image-20211208205223912](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211208205223912.png)

在docker官网上也可以看到

![image-20211208205301269](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211208205301269.png)

# 四、在Docker的ubuntu中安装文件

目的：在docker中拉取ubuntu镜像，然后实例化一个ubuntu容器，并在容器中安装fenics

以下是具体步骤：

1. 拉取ubuntu镜像

```dockerfile
docker pull ubuntu:latest 
```

2. 实例化容器

```dockerfile
docker run -t ubuntu:latest bash
```

参数说明：

+ -it：-i和-t的结合，感觉就是如下图，直接进入容器的命令行模式。
+ –name：自定义容器名称，不用的话会自动分配一个名称。
+ -v： 将本地文件夹`D:/text_similar`与容器文件夹/root/text_similar共享。可以不设置
+ ubuntu:latest：要运行的镜像名+TAG
+ bash：进入容器命令行。
+ 可选参数dit：如果从这个stdin中退出，会导致容器的停止

注意，如果在使用以下命令，那么容器运行完后将会被删除

```python
docker run -t rm -f ubuntu:latest bash
```

3. 生成系统文件

1. It is because there is no package cache in the image, you need to run

```python
apt-get update
```



2. before installing packages, and if your command is in a Dockerfile, you'll then need:

```python
apt-get -y install curl
```

3. To suppress the standard output from a command use -qq. E.g.(使用-qq后，不会有输出)

```python
apt-get -qq -y install curl
```

4. 完事后，使用命令安装fenics

```python
apt-get --y install fenics
```

安装成功

![image-20211206194519979](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211206194519979.png)

5. 退出容器

```pyhon
exit
```

6. 容器打包成镜像，需要用到容器ID(即ubuntu的 CONTAINER ID)

```python
docker commit -a "eric" -m "ubuntu fenics images" 17bab7644e7b fenics_freeneuro:v1
```

参数说明：

+ -a :提交的镜像作者；
+ -c :使用Dockerfile指令来创建镜像；
+ -m :提交时的说明文字；
+ -p :在commit时，将容器暂停。

得到输出

![image-20211206203948029](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211206203948029.png)

注意红色框，第一个红色框是我们操作的容器，里面安装了fenics。因此将其打包成镜像，这时候查看镜像有

![image-20211206204207159](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20211206204207159.png)

7. 容器/镜像删除

+ 容器删除


```python
docker rm [CONTAINER ID]
```

+ 镜像删除（必须先删除运行该镜像的容器）

```python
docker rmi IMAGE-ID
```

8. 如何在运行现有的容器(即`docker ps -a`查询出的容器)

```python
docker attach [CONTAINER ID] # 注意container ID可以是容器ID的前4位
```

更高级的用法参考[这里](https://blog.csdn.net/skh2015java/article/details/80229930)，或者参考以另外一个命令

```powershell
# 运行一个容器
$ docker run -dit ubuntu
69d137adef7a8a689cbcb059e94da5489d3cddd240ff675c640c8d96e84fe1f6

# 查看现有容器
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
69d137adef7a        ubuntu:latest       "/bin/bash"         18 seconds ago      Up 17 seconds                           zealous_swirles
# 进入容器
$ docker exec -it 69d1 bash
root@69d137adef7a:/#
```



## 一些命令

+ docker ps -a 查看存在的容器
+ docker images 查看存在的镜像
+ docker stop 停止正在运行的容器
+ docker pull ubuntu:latest 拉取容器
+ docker attach container_id

# 参考资料

1. https://zhuanlan.zhihu.com/p/137895577

2. https://blog.csdn.net/gf19960103/article/details/109489632

3. https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.htm

4. https://scipy.github.io/devdocs/building/linux.html

5. https://zhuanlan.zhihu.com/p/137895577