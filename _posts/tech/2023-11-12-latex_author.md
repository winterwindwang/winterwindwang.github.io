---
layout: post
title: latex模板使用authblk包存在的问题
category: 技术
keywords: latex
tags: technical details
---

# 使用某些latex模板的authblk包遇到的问题

某些模板使用authblk包时，虽然运行不会出错，但是一直很会卡在那。查完资料后，模板和authblk存在冲突。因此找到了[这里](https://github.com/MCG-NKU/CVPR_Template/issues/13)的资料，并摘录在下面

```python
\usepackage{silence}
\makeatletter
\robustify\@latex@warning@no@line
\makeatother
\usepackage{authblk}

\author[1]{Author name1}
\author[2,3]{Author name2}
\author[2,3]{Author name3}
\author[2,3]{Author name4}
\affil[1]{Affilation 1}
\affil[2]{Affilation 2}
\affil[3]{Affilation 3}
```

