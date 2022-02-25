---
layout: post
title: Latex中图片的排版
category: 技术
keywords: latex, image layout
tags: writting, latex, image layout
---

# 使用minipage

```latex
\begin{figure*}	
	\centering
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width =1.1\linewidth]{png//rebview_on2go_waveform_real.png}
		%\centerline{\footnotesize Original}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width=1.1\linewidth]{png//rebview_on2go_waveform_adv.png}
		%\centerline{\footnotesize Original spectrogram}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width =1.1\linewidth]{png//rebview_on2go_specgram_real.png}
		%\centerline{\footnotesize Adversarial}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width=1.1\linewidth]{png//rebview_on2go_specgram_fake.png}
		%\centerline{\footnotesize Adversarial spectrogram}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width =1.1\linewidth]{png//rebview_on2go_waveform_res2.png}
		%\centerline{\footnotesize Residual}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width=1.1\linewidth]{png//rebview_on2go_waveform_res1.png}
		%\centerline{\footnotesize Enlarged residual}
	\end{minipage}
	
	\centering
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width =1.1\linewidth]{png//rebview_on2go_waveform_real.png}
		\centerline{\footnotesize (a) Original}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width=1.1\linewidth]{png//rebview_on2go_waveform_adv_alzantot.png}
		\centerline{\footnotesize (b) Adversarial}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width =1.1\linewidth]{png//rebview_on2go_specgram_real.png}
		\centerline{\footnotesize (c) Original}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width=1.1\linewidth]{png//rebview_on2go_specgram_fake_alzantot.png}
		\centerline{\footnotesize (d) Adversarial}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width =1.1\linewidth]{png//rebview_on2go_waveform_res2_alzantot.png}
		\centerline{\footnotesize (e) Residual}
	\end{minipage}
	\begin{minipage}{.16\linewidth}
		\centering
		\includegraphics[width=1.1\linewidth]{png//rebview_on2go_waveform_res1_alzantot.png}
		\centerline{\footnotesize (f) Enlarged residual}
	\end{minipage}
	
	\caption{Comparison result}
	\label{fig:waveform}
\end{figure*}
```

效果如下

![image-20220214105934695](https://gitee.com/freeneuro/PigBed/raw/master/img/image-20220214105934695.png)

下面给详细解释一下各个命令的用法

```latex
\begin{figure}[htbp] % 有htbp的话，会将图片限制在单栏内
\begin{figure*}	% 使用*号允许图片横跨双栏
	\centering  % 令图片居中
	\begin{minipage}{.25\linewidth}  % 一张图片一个minipage，调整图片的大小
		\centering
		\includegraphics[width=1\linewidth]{png//rebview_on2go_waveform_real.png}  % 宽度设置成1，如果设置成大于1图片间没有间隙，设置成1可使图片间有间隙
		\centerline{\footnotesize Original} % 单张图片的caption
	\end{minipage}
	% 必须要这个空格，表示换行
	\centering
	\begin{minipage}{.25\linewidth}
		\centering
		\includegraphics[width =1\linewidth]{png//rebview_on2go_waveform_real.png}
		\centerline{\footnotesize (a) Original}
	\end{minipage}
	\caption{Comparison result}
	\label{fig:waveform}
\end{figure*}
\end{figure}
```

参考资料

[1]:https://blog.csdn.net/a6822342/article/details/80533135
[2]:https://blog.csdn.net/a386115360/article/details/89358723