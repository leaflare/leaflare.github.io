+++
title = "pip报错处理合集"
date = 2021-12-12 16:13:14
slug = "202112121613"

[taxonomies]
tags = [ "Python" ]
categories = ["Python"]

+++

<!-- more -->

## pip和pip3的区别

当一台电脑同时有多个版本的Python的时候，用pip3就可以自动区分用Python3来安装库，为了避免和Python2发生冲突的。如果电脑只安装了Python3，那么不管用pip还是pip3都一样的。
安装了python3之后，会有pip3
（1）使用pip install XXX ：
新安装的库会放在这个目录下面：python2.7/site-packages
（2）使用pip3 install XXX ：
新安装的库会放在这个目录下面：python3.7/site-packages
（3）如果使用python3执行程序，那么就不能importpython2.7/site-packages中的库。
