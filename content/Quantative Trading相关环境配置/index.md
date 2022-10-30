+++
title = "Quantative Trading相关环境配置"
date = 2022-01-09 17:21:14
slug = "202201091721"

[taxonomies]
tags = ["Quant", "Python" ]
categories = ["Quant"]

+++

<!-- more -->

## Anaconda重装

如果conda已经没救了，尝试

```
$ conda clean --packages && conda clean --all && conda update --all
```

若conda还能正常执行安装命令，则跳过此步执行以下步骤



在 Anaconda Prompt，执行

```
$ conda install anaconda-clean
```

安装完成后，执行

```
$ anaconda-clean
```

不想一个一个敲y可以

```
$ anaconda-clean --yes
```

然后再执行常规卸载重装流程

需要注意anaconda版本和python版本的对应问题

<[Old package lists — Anaconda documentation](https://docs.anaconda.com/anaconda/packages/oldpkglists/)>



## tushare安装

conda执行

```
$ conda install -c waditu tushare
```

来源：<[Tushare :: Anaconda.org](https://anaconda.org/waditu/tushare)>

也可以用pip，但是多python版本环境用pip安装后使用anaconda环境时出现找不到包的情况，走conda可以省很多事，后面同理

如果有找不到的包可以去<https://anaconda.org/>看看



## Ta-Lib安装

```
$ conda install -c conda-forge ta-lib
```

Ta-Lib0.4.23部分API调用会报错，换0.4.17可以解决

但是ta-lib指定版本更新会报一堆错

简单粗暴的解决办法:

用[vnpy/vnpy: 基于Python的开源量化交易平台开发框架 (github.com)](https://github.com/vnpy/vnpy)

自带ta-lib0.4.17



## PyQt安装

```
$ conda install -c anaconda pyqt
```



### PyQt在PyCharm下的环境配置

以下配置默认anaconda环境

在Pycharm中，依次打开 File - Settings - Tools - External Tools，点击 ' + ' 

#### Qt Designer

配置如下

```cpp
Name: QtDisigner
Program : C:\Users\用户名\Anaconda3\Lib\site-packages\qt5_applications\Qt\bin\designer.exe
Working directory: $FileDir$
```

注意新版本的PyQt designer.exe的位置和以前不同

#### PyUIC

配置如下

```
Name: PyUIC
Program : C:\Users\用户名\Anaconda3\python.exe
Arguments: -m PyQt5.uic.pyuic $FileName$ -o $FileNameWithoutExtension$.py
Working directory: $FileDir$
```

#### PyRCC

配置如下

```
Name: PyRCC
Program : C:\Users\用户名\Anaconda3\Scripts\pyrcc5.exe
Arguments: $FileName$ -o $FileNameWithoutExtension$_rc.py
Working directory: $FileDir$
```

配置全部完成后，可以在PyCharm主界面，依次点击 Tools - External Tools 启动QtDisigner、PyUIC和PyRCC
