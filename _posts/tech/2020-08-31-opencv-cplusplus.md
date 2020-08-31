---

layout: post

title: OpenCV4.4, CMake, VS2017的环境部署

catagory: 技术

tags: React

keywords: React

---

## 基础环境

首先确保你已经下载了下面的软件/安装包：

1. OpenCV 4.4 下载完后，解压得到opencv文件夹，将其重命名成opencv-4.4，目录有两个文件夹`build`和`sources`
2. 安装Visual Studio2017
3. 安装CMake(推荐3.17.4)
4. 下载与OpenCV 4.4版本一致的opencv-contrib，将`opencv-contrib/module`目录下的`module`文件夹重名命成contrib并将其复制到`opencv-4.4/sources`

后续的操作尽量一致

## CMake编译OpenCV4.4

1. 打开CMake的GUI界面，并将`sources`和`build(推荐使用build目录)`两个目录配置到如图位置
   ![路径配置](https://winterwindwang.github.io/assets/img/2020-08-31-CMake_build_opencv.png)

2. 然后点击`configure`，第一次会让你选择编译的平台，如果是64位的机器的话，需要将`Optional platform for generator(if empty, generator uses:win32)`设置成`x64`
3. 完成后会进行编译，需要等一会。
4. 编译完成后，找到OPENCV_EXTRA_MODULES_PATH,并选择刚刚复制到`sources`文件中的`contrib`
5. 也会出现几个错误。

## 错误以及解决办法

#### 1. 下载错误

下载`ffmpeg`等库出错，出现这些错误没办法编译成功的

```
CMake Warning at cmake/OpenCVDownload.camke:202(message):
   FFMPEG: Download failed: 6;"Couldn't resolve host name"
   ......
```

![路径配置](https://winterwindwang.github.io/assets/img/2020-08-31-cannotdownload_ffmpeg.png)

#### 解决办法

首先去检查`build`目录下的`CMakeDownloadLog.txt`文件，位置如图

![CMakeDownload位置](https://winterwindwang.github.io/assets/img/2020-08-31-CMakeDownloadLogLocation.png)

打开后，找到如下图所示的地方，找到`https://raw.githubusercontent.com/opencv/opencv_3rdparty/1df9bf0c0c6c2cf225bd3d8e4cf5985198352454/ffmpeg/opencv_videoio_ffmpeg.dll`并复制到浏览器中下载，下载完成后，放到`D:/download/opencv/sources/.cache/ffmpeg/`，并将其更名成`854b3460c435d04277e1f1ecc06cb809-opencv_videoio_ffmpeg.dll`。

```
1.下载后得到文件：opencv_videoio_ffmpeg.dll（假设已经在目录D:/download/opencv/sources/.cache/ffmpeg/中了）
2.此时该目录下会有几个3个大小位0字节的带md5验证码的文件（如`854b3460c435d04277e1f1ecc06cb809-opencv_videoio_ffmpeg.dll`）
3.这时将带验证码的文件名复制下来，然后删掉，最后将opencv_videoio_ffmpeg.dll更改成854b3460c435d04277e1f1ecc06cb809-opencv_videoio_ffmpeg.dll
```



![问题解决](https://winterwindwang.github.io/assets/img/2020-08-31-solvemethod.png)

然后，依次完成其他文件的下载。这里需要提一下，有些文本文件是直接显示在网页上的。这时不能自己新建一个文件，如不能自己新建一个文本文件然后重名成`ffmpeg_version.cmake`。（因为编译会校验md5码，自己新建的话就会不一样）。正确的做法是：在显示的网页上右击选择另存为文件，然后在后续修改成`ffmpeg_version.cmake`文件

接着，按上面的方法，依次完成对`D:/download/opencv/sources/.cache/`文件夹下其他文件的修改

最后，完成后再次点击`configure`，不出意外会出现第二个错误

#### 2. CONFIGURATION IS NOT SUPPORTED错误

错误如图：

![configuration问题](https://winterwindwang.github.io/assets/img/2020-08-31-solvemethod.png)

#### 解决办法

如图，取消如图位置的勾就能编译成功了（问题好像其实并没有解决）

![configuration问题](https://winterwindwang.github.io/assets/img/2020-08-31-configuration_error_solve.png)

这时在次点击`configuration`应该就能编译成功了，最后点击`generate`就可以编译成功了

## VS2017编译

这时后再`build`文件下，会出现一个`OpenCV.sln`文件，打开文件进入VS2017界面，如见如图位置

![configuration问题](https://winterwindwang.github.io/assets/img/2020-08-31-vs2017opencv.png)

如果如图位置是`Debug`和`x64`的话，应该就没问题了，如果不是的话，重新装一遍吧。

最后在解决方案中找到`CMakeTargets`目录，并右击目录下的`ALL_BUILD`，然后选择生成。完成后，将`Debug`换成`Release`，再次生成。这时OpenCV编译完成



## 待续

完成examples,以及验证contrib能否起作用