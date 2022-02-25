---
layout: post
title: markdown中的技巧积累
category: 技术
keywords: markdown
tags: markdow, recording
---

# 图片插入技术

常见的图片插入时使用以下代码

```markdown
![image name](src url)
```

但是上述代码只能展示图片，无法对图片添加描述(caption)。为解决该问题，记录了以下方便的方法（方法有很多，但是使用`<center></center>`标签的方法会遇到图片小时，图片和caption都位于同一行的问题）。参考[这里][https://towardsdev.com/3-ways-to-add-a-caption-to-an-image-using-markdown-f2ca30562be6]

## figure标签法

```markdown
<figure>
<img src="https://images.unsplash.com/photo-1549740425-5e9ed4d8cd34?ixlib=rb-1.2.1&ixid=MXwxMjA3fDB8MHxjb2xsZWN0aW9uLXBhZ2V8MXwzOTU0NTB8fGVufDB8fHw%3D&w=1000&q=80" alt="Trulli" style="width:100%">
<figcaption align = "center"><b>Fig.1 - 4K Mountains Wallpaper</b></figcaption>
</figure>
```

效果如下：

<figure>
<img src="https://images.unsplash.com/photo-1549740425-5e9ed4d8cd34?ixlib=rb-1.2.1&ixid=MXwxMjA3fDB8MHxjb2xsZWN0aW9uLXBhZ2V8MXwzOTU0NTB8fGVufDB8fHw%3D&w=1000&q=80" alt="Trulli" style="width:100%">
<figcaption align = "center"><b>Fig.1 - 4K Mountains Wallpaper</b></figcaption>
</figure>

## Markdown表格法

如果由排列多张图的需求，表格就很合适了。代码如下

```markdown
# 效果图1
| ![space-1.jpg](https://blog-assets.thedyrt.com/uploads/2019/01/shutterstock_1033306540-1.jpg) |
|:--:|
| <b>Image Credits - Fig.2 - 4K Mountains Wallpaper</b>|

# 效果图2
| ![space-1.jpg](https://blog-assets.thedyrt.com/uploads/2019/01/shutterstock_1033306540-1.jpg) | ![space-1.jpg](https://blog-assets.thedyrt.com/uploads/2019/01/shutterstock_1033306540-1.jpg) |
|:--:|:--:|
| <b>Image Credits - Fig.2 - 4K Mountains Wallpaper</b>|
```

效果如下所示

| ![space-1.jpg](https://blog-assets.thedyrt.com/uploads/2019/01/shutterstock_1033306540-1.jpg) |
| :----------------------------------------------------------: |
|    <b>Image Credits - Fig.2 - 4K Mountains Wallpaper</b>     |

| ![space-1.jpg](https://blog-assets.thedyrt.com/uploads/2019/01/shutterstock_1033306540-1.jpg) | ![space-1.jpg](https://blog-assets.thedyrt.com/uploads/2019/01/shutterstock_1033306540-1.jpg) |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|    <b>Image Credits - Fig.2 - 4K Mountains Wallpaper</b>     |                                                              |