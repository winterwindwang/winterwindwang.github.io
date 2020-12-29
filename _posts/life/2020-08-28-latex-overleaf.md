---
layout: post
titile: Overleaf模板中引用参考文献的方法在TeXstudio中无法显示的问题
category: 生活
tag: React
keywords: React

---

## 起因

最近准备做一份个人简历，之前有听说过使用LaTex做出来的简历很好看，所以就尝试找个简历来改改。[简历模板地址](https://www.overleaf.com/latex/templates/a-customised-curve-cv/mvmbhkwsnmwv#.Wh4y_S5ccQg),点击ViewSource就可以看见简历的LaTex源码了。其实可以在Overleaf直接编辑简历，然后导出pdf。我将整个文件都复制到本地以后，使用`textLive+TeXstudio`的LaTex编写环境。运行文件后，发现出现如图的参考文献没有显示的问题。



![未显示参考文献](https://winterwindwang.github.io/assets/img/2020-08-28-nonereference.png)



## 解决办法

首先，我检查了自己是否有复制错误，检查后发现，与Overleaf完全一样。

然后，我去Latex中找出与参考文献有关的代码，发现它使用`sttings.sty`格式文件配置了`bib`的使用方法，其中关键代码如下：

```
\RequirePackage[backend=biber,bibstyle=apa,sorting=ymdnt,uniquename=init,defernumbers=true]{biblatex}
```

使用的是`biblatex`包。在`cv-llt.tex`文件中使用`\addbibresource{ownbib.bib}`方式引入`.bib`文件，最后在`publications.tex`使用了`\notice{*}`方法输出所有参考文献，使用`\printbibliography`命令按期刊，会议等将参考文献输出在pdf中。

最后，在网上搜索无果后，查看TeXstudio日志有如下一行提示

```
Please (re)run Biber on the file:(biblatex) cv-llt(biblatex) and rerun LaTeX afterwards.
```

意思大概是，请先使用`Biber`运行`cv-llt.tex`然后重新运行`cv-llt.tex`。于是便在工具栏寻找`Biber`，最后在如图位置找到了Biber，位置在**工具->命令->Biber**

![Biber位置](https://winterwindwang.github.io/assets/img/2020-08-28-Biberlocation.png)

运行后，发现生成了`cv-llt.bbl`文件，心想这下应该是没问题了。再次运行`cv-llt.tex`文件，参考文献终于出来啦

![出现参考文献](https://winterwindwang.github.io/assets/img/2020-08-28-referenceon.png)

现在，可以着手修改成自己的个人简历啦。