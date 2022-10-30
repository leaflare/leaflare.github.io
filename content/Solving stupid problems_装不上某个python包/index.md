+++
title = "Solving stupid problems——装不上某个python包"
date = 2020-09-13 14:43:14
slug = "202009131443"

[taxonomies]
tags = [ "Python" , "Solving stupid problems"]
categories = ["Python"]

+++

记录一次用pip安装python包失败的解决过程

<!-- more -->

## 事故现场

![](.\img\1.png)

pip装不上，去[stable-baselines3 · PyPI](https://pypi.org/project/stable-baselines3/#files)上下的whl也装不上

## 问题解决

python 中的egg文件；类似于Java 中的jar 包，把一系列的python源码文件、元数据文件、其他资源文件 zip 压缩，重新命名为.egg 文件，目的是作为一个整体进行发布。

关于egg-info是什么：

[Chapter 38 - The Python egg — Python 101 1.0 documentation (pythonlibrary.org)](https://www.python101.pythonlibrary.org/chapter38_eggs.html)



Built Distribution 和 Source Distribution

- 不同操作系统安装 Python 包的要求不完全相同，Built Distribution 是按照某类操作系统（例如 Linux）安装要求所打成的包，这样安装的时候只需要将包解压，文件放到适当的位置即可，效率比较高。但一个 Built Distribution 只适用于一类操作系统，例如 Linux 上的 Built Distribution 包无法在 Windows 上安装。而 Source Distribution 包便解决了这个问题，它是对源代码进行打包，将源代码打包成利于打成 Built Distribution 包的形式。这样，我们便可以通过 Source Distribution 包生成适用于特定类型操作系统的 Built Distribution 包，再进行安装。

xxx.whl是Built Distribution 

xxx.tar.gz是 Source Distribution

xxx.whl扔进`C:\Users\username\Anaconda3\Scripts`用pip安装，如上图所示

xxx.tar.gz解压后包含xxx和xxx.egg-info两个文件夹，需要一起扔进`C:\Users\usern\Anaconda3\Lib\site-packages`

直接把解压后的包扔进去或者没有egg-info都不能被python环境识别，在这里踩了坑orz

类似egg-info这种伴生关系的还有dist-info
