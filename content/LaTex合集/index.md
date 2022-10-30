+++
title = "LaTex使用总结"
date = 2022-01-29 15:36:14
slug = "202201291536"

[taxonomies]
tags = ["LaTex" ]
categories = ["LaTex"]

+++

<!-- more -->

# 学习笔记

[Overleaf速查表](https://blog.csdn.net/weixin_39876450/article/details/112287980)

## 章节和分级标题

```latex
\chapter{章节}
\section{一级标题}
\subsection{二级标题}
```

## 换行

#### 创建一个新的段落

只需要在代码中添加一个空白行就可以了，下一个段落会有段首缩进

```latex
\documentclass{article}
\begin{document}
第一自然段，有首行缩进

第二自然段，有首行缩进
\end{document}

```

#### 不缩进换行

```latex
\documentclass{article}
\usepackage[utf8]{inputenc}

\begin{document}
这是一个自然段，有缩进\\
这是\\（两个反斜杠）产生的新行，没有缩进\newline
这是\newline产生的新行，没有缩进\hfill \break
这是\hfill \break产生的新行，没有缩进
\end{document}
```

- `\\`（两个反斜杠）

- `\newline`
- `\hfill \break`

以上三种换行方法效果相同

#### 单段不首行缩进

```latex
\noindent{
	不需要首行缩进的段落内容
}
```

## 字号

从小到大

```
\tiny
\scriptsize
\footnotesize
\small
\normalsize
\large
\Large
\LARGE
\huge
\Huge
```

使用形式

```latex
\begin{tiny}
...
\end{tiny}
```



## 无序列表与有序列表

实心圆点无序列表

```latex
\begin{itemize}
    \item one
    \item two
    \item ...
\end{itemize}
```

使用其他符号进行排列，例如星号

```latex
\begin{itemize}
    \item[*] one
    \item[*] two
    \item[*] ...
\end{itemize}
```

数字顺序有序列表

```latex
\begin{enumerate}
    \item one
    \item two
    \item ...
\end{enumerate}
```

使用其他形式的编号需要先加载包，\usepackage{enumerate}

```latex
\begin{enumerate}[i)]
    \item one
    \item two
    \item ...
\end{enumerate}

\begin{enumerate}[1)]
    \item one
    \item two
    \item ...
\end{enumerate}
```

自定义编号形式

```latex
\begin{description}
    \item[Step1] one
    \item[Step2] two
    \item[Step3] ...
\end{description}
```



## 插入图片

### Overleaf 中导入图片

在文件夹中存放图片

```latex
\begin{figure}[htp]
    \centering
   	\includegraphics[width=0.8\textwidth,height=0.5\textwidth]{the.png}
    \caption{An image of a galaxy}
    \label{fig:galaxy}
\end{figure}

```

`\graphicspath{ {images/} }` 指令告诉 LaTeX  图片是被存储在 `Images/` 文件夹之中的，现在可以仅输入图片的文件名，不再需要输入它的路径了。

`\includegraphics[width=4cm]{InsertingImagesEx5}`需要注意的是，只需要输入图片的文件名，不包括它的后缀

可用于调整图片大小和缩放

## 插入表格

[TablesGenerator](https://www.tablesgenerator.com/latex_tables)

### 定义表格单列宽度

```
\begin{tabular}{p{2cm}p{3cm}p{2.5cm}p{2.5cm}p{2.5cm}p{2.5cm}}
```

`p{2cm}`就是代表第一列的宽度，后面依次是后面的宽度，会自动进行换行显示。

### 按页面宽度调整表格

`\setlength{\tabcolsep}{7mm}{XXXX}`

```latex
\begin{center}
\textbf{Table 2}~~Improved table.\\
\setlength{\tabcolsep}{7mm}{
\begin{tabular}{cccccc} \toprule
Models  &  $\hat c$  &  $\hat\alpha$  &  $\hat\beta_0$  &  $\hat\beta_1$  &  $\hat\beta_2$  \\ \hline
model  & 30.6302  & 0.4127  & 9.4257  & - & - \\
model  & 12.4089  & 0.5169  & 18.6986  & -6.6157  & - \\
model  & 14.8586  & 0.4991  & 19.5421  & -7.0717  & 0.2183  \\
model  & 3.06302  & 0.41266  & 0.11725  & - & - \\
model  & 1.24089  & 0.51691  & 0.83605  & -0.66157  & - \\
model  & 1.48586  & 0.49906  & 0.95609  & -0.70717  & 0.02183  \\
\bottomrule
\end{tabular}}
\end{center}
```



### 表名

```latex
\begin{table}
\begin{tabular}{|l|l|l|l|}
	Any & 112  & 123 & 132  \\
	Zhang                   & 324& 345 & 345  \\
	David                   & 123                     & 34  & 45   \\
	Araminta                & 133 & 33  & 35 \\ 
\end{tabular}
\caption{a table of scores}
\end{table}
```
`\caption{a table of scores}`设置表名，如果不需要自动编号可以使用`\caption*{a table of scores}`



### 三行线表

```latex
	\toprule   %第一行线
    %表示第一列占1.5cm 第二列占6cm 第三列占2cm 的距离 并且这几个字都是居中对齐
    \multicolumn{1}{m{1.5cm}}{\centering Symbol} & \multicolumn{1}{m{6cm}}{\centering Definition} & \multicolumn{1}{m{2cm}}{ Unit} \\
    \midrule   %第二行线
     $V$ & index & -- \\
     $X$ & The  & -- \\
     $Y$ & The & -- \\
     $Z$ & The  & -- \\
    \bottomrule   %第三行线
```

### 全线表

```latex
\begin{table}[H]
\setlength{\tabcolsep}{20mm}{
\begin{tabular}{|l|l|}
\hline
第一行 & 的内容     \\ 
\hline
第二行 & 的内容     \\ 
\hline
\end{tabular}}
\caption {表名}
\end{table}
```

## 插入代码

最简单的

```latex
\begin{verbatim}
...
\end{verbatim}
```

复杂一点的

```latex
\begin{lstlisting}
...
\end{lstlisting}

```

```latex
\usepackage{listings}
  \lstset{ %
    language=Octave,                % the language of the code
    basicstyle=\footnotesize,           % the size of the fonts that are used for the code
    numbers=left,                   % where to put the line-numbers
    numberstyle=\tiny\color{gray},  % the style that is used for the line-numbers
    stepnumber=2,                   % the step between two line-numbers. If it's 1, each line
                                    % will be numbered
    numbersep=5pt,                  % how far the line-numbers are from the code
    backgroundcolor=\color{white},      % choose the background color. You must add \usepackage{color}
    showspaces=false,               % show spaces adding particular underscores
    showstringspaces=false,         % underline spaces within strings
    showtabs=false,                 % show tabs within strings adding particular underscores
    frame=single,                   % adds a frame around the code
    rulecolor=\color{black},        % if not set, the frame-color may be changed on line-breaks within not-black text (e.g. commens (green here))
    tabsize=2,                      % sets default tabsize to 2 spaces
    captionpos=b,                   % sets the caption-position to bottom
    breaklines=true,                % sets automatic line breaking
    breakatwhitespace=false,        % sets if automatic breaks should only happen at whitespace
    title=\lstname,                 % show the filename of files included with \lstinputlisting;
                                    % also try caption instead of title
    keywordstyle=\color{blue},          % keyword style
    commentstyle=\color{dkgreen},       % comment style
    stringstyle=\color{mauve},         % string literal style
    escapeinside={\%*}{*)},            % if you want to add LaTeX within your code
    morekeywords={*,...}               % if you want to add more keywords to the set

```



## 引文

```
\bibliographystyle{样式}
1. plain，按字母的顺序排列，比较次序为作者、年度和标题
2. unsrt，样式同plain，只是按照引用的先后排序
3. alpha，用作者名首字母+年份后两位作标号，以字母顺序排序
4. abbrv，类似plain，将月份全拼改为缩写，更显紧凑
5. ieeetr，国际电气电子工程师协会期刊样式
6. acm，美国计算机学会期刊样式
7. siam，美国工业和应用数学学会期刊样式
```

```
\bibliography{xxx} %xxx.bib
```



# 问题解决

## LaTex不能显示中文生僻字

没错，就是我的名字

事故现场：

```latex
\documentclass[
    latin-font = win|mac|linux|gyre|none,
    cjk-font = win|mac|linux|fandol|founder|noto|source|none,
    ]{njuthesis}
```

解决：把字体设置里的其他系统删掉（X

```latex
\documentclass[
    latin-font = win,
    cjk-font = win,
    ]{njuthesis}
```

## 图片位置固定

为了避免图片位置被重排导致的图文分离，在`\begin{document}`前加上`\usepackage{float}`和`\usepackage{graphicx}` 

由此在画图表的时候产生了另一个问题：

`You have forgotten to include a float specifier, which tells LaTeX where to position your figure. To fix this, either insert a float specifier inside the square brackets (e.g. \begin{figure}[h]), or remove the square brackets (e.g. \begin{figure}). Find out more about float specifiers here.`

解决办法：

位置固定用 `\begin{figure}[H]`

位置不固定用 `\begin{figure}[htp]`

## 不引用但是显示参考文献

（不要问为什么不引用

```latex
\addcontentsline{toc}{section}{参考文献}
\nocite{1} %只加入到参考文献列表中，不在文中引用（显示[id]）
\nocite{*} %显示所有文献
\bibliographystyle{plain}
\begin{thebibliography}{}
\bibitem[1]{1} Book Title, Author
\end{thebibliography}

```

