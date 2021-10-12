---
layout: post
title: 使用Python自动下载论文
category: 技术
keyword: 爬虫，论文自动下载，python
---

> 批量下载某个研究领域的相关论文，尤其是会议论文，一篇一篇下载太慢，故写此代码自动下载所有论文。

会议论文列表有很多种形式，比如说：

+ 第一种：有人总结了某个会议（如ICCV）中所有关于某个研究领域的论文，并给出了相关的文献的链接，此时得到这些链接就可以下载这些论文。
+ 第二种：直接从会议接受列表中，按照关键词查找某个研究领域的论文，然后通过相应的链接下载论文。

由此可见，在最后一步是相同的，不同的是论文列表集合来源。本篇博客暂时考虑了第一种情况

## 环境要求

下述python包直接装就行。

```
python 3.6 
BeautifulSoup # 解析html网页
requests_html # 爬取网页，试过requests库，感觉比较麻烦
urllib.request # 下载pdf
```

## 第一种情况：他人给出了关于某个研究领域的论文列表

这种情况常出现在他人通过某种方式，将某个研究领域的论文都整合到一起，方便日后查看。这时候，我们可以利用这个列表提供的链接下载论文。

例如，本文从[该](https://zhuanlan.zhihu.com/p/419951814?utm_source=com.alibaba.android.rimet&utm_medium=social&utm_oi=1112035085750071296)知乎链接中，自动下载该博主整理的与对抗样本有关的论文。

具体代码如下：

```python
from bs4 import BeautifulSoup as bf
from requests_html import HTMLSession
import os
import urllib.request

# 博客链接
url = "https://zhuanlan.zhihu.com/p/419951814?utm_source=com.alibaba.android.rimet&utm_medium=social&utm_oi=1112035085750071296"
```

使用`request_html`库爬取网页

```python
r = session.get(url)
absolute_links = r.html.absolute_links  # 得到网页中所有的超链接地址
```

接着遍历超链接地址，去掉无关地址

```python
for i, item in enumerate(absolute_links):
    # 如果是论文，超链接地址中必定含有以下字符（这部分有点不自动）
	if "content/ICCV2021" in item:
		get_paper_pdf_from_url(args, item)
		print(f"{i} completed!!")
```

函数`get_paper_pdf_from_url`的定义为

```python
def get_paper_pdf_from_url(args, url):
	'''此处args.conference为https://openaccess.thecvf.com'''
    # 这里也有点不自动，主要是知乎链接跳转需要确认，为了方便直接使用了以下的方式
    url = url.replace("//link.zhihu.com/?target=https%3A//", "//")
    sub_r = session.get(url)  # 爬取论文页面网页
    resp = bf(sub_r.html.html, "html.parser")
    ahref_list = resp.find_all('a')  
    pdf_url = [i for i in ahref_list if "pdf" in i.text][0].attrs['href'] # 找到论文下载的链接
    pdf_url = args.conference + pdf_url  # 组成论文下载的链接
    download_file(pdf_url, args.save_dir)
```

函数`download_file`的定义为

```python
def download_file(download_url, save_dir):
    response = urllib.request.urlopen(download_url)
    filename = os.path.basename(download_url)
    filepath = os.path.join(save_dir, filename)
    # 这里做判断主要是因为网络不好，下载会中断，所以要重复运行代码，为了避免重复下载。这里应该有更好的解决办法
    if os.path.exists(filepath) is False:
        file = open(filepath, 'wb')
        file.write(response.read())
        file.close()
```

另外，还新增了一个下载`bibtex`的方法

```python
def get_bib(url):
    url = url.replace("//link.zhihu.com/?target=https%3A//", "//")
    sub_r = session.get(url)
    resp = bf(sub_r.html.html, "html.parser")
    bib_text = resp.find(name='div', attrs={"class":'bibref pre-white-space'}).text
    return bib_text
```

(未完待续....)