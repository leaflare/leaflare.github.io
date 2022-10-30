+++
title = "paddle踩坑"
date = 2021-07-15 14:43:14
slug = "202107151443"

[taxonomies]
tags = [ "Python" ]
categories = ["Python"]

+++



<!-- more -->

这东西有CPU版本和GPU版本两种，GPU版本的完全依赖CUDA+CUDNN，直接pip install只会报一堆让人摸不着头脑的错（x

CPU版本直接`python -m pip install paddlepaddle==2.3.0 -i https://mirror.baidu.com/pypi/simple`

下面是GPU版本的踩坑记录

## CUDA+CUDNN安装

下载地址

https://developer.nvidia.com/cuda-toolkit-archive

https://developer.nvidia.com/rdp/cudnn-archive   （下这个还要注册nvda账户orz

目前(2022.4)paddle支持到10.2

命令提示符`nvcc -V`检查CUDA是否安装成功

CUDNN安装包解压后丢进`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\bin`

有冲突就覆盖（x

## paddle安装

CUDA10.2版本的镜像

```
python -m pip install paddlepaddle-gpu==2.3.0 -i https://mirror.baidu.com/pypi/simple
```

此时如果用paddle跑代码会出现报错：

```
RuntimeError: (PreconditionNotMet) The third-party dynamic library (cudnn64_7.dll) that Paddle depends on is not configured correctly. (error code is 126)
  Suggestions:
  1. Check if the third-party dynamic library (e.g. CUDA, CUDNN) is installed correctly and its version is matched with paddlepaddle you installed.
  2. Configure third-party dynamic library environment variables as follows:
  - Linux: set LD_LIBRARY_PATH by `export LD_LIBRARY_PATH=...`
  - Windows: set PATH by `set PATH=XXX; (at ..\paddle\phi\backends\dynload\dynamic_loader.cc:303)
  [operator < uniform_random > error]
```

缺少cudnn64_7.dll，需要手动下载丢进`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.2\bin`

下载地址：https://www.dll-files.com/search

我就是为了记录这个网址专门水了一篇（x
