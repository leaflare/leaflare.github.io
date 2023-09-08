+++
title = "VSC+WSL切换环境格式问题踩坑小记"
date = 2023-04-07 20:05:54
slug = "202304072005"

[taxonomies]
tags = ["Virtual Machine", "WSL" ]
categories = ["WSL"]

+++

<!-- more -->

## 前情提要

准备回顾一下以前的老项目，习惯性启动VSC-->git clone，想起来terminal没切环境，重新开一个WSL terminal，然后cd + ./gradlew build 出现报错`"/usr/bin/env: ‘sh\r’: No such file or directory"`

原因在于Windows和Linux换行符格式不同，在Windows下被打开过的脚本文件在Linux环境使用就很容易遇到这个问题。因为这种情况一般发生在第一次拉仓库的时候，过往一般都会直接删文件夹重开（划掉），不过这次准备尝试一下别的办法，毕竟总会遇到不能靠重开解决的时候hh

## 解决

dos2unix + 文件名

可以用于执行Windows和Linux换行符格式转换，使用起来非常简单

```
sudo apt-get install dos2unix
dos2unix gradlew
```

