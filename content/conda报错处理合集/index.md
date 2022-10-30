+++
title = "conda报错处理合集"
date = 2022-01-10 21:36:14
slug = "202201102136"

[taxonomies]
tags = ["Python" ]
categories = ["Python"]

+++

<!-- more -->

## conda更新报错

### CondaValueError: Malformed version string '~': invalid character(s).

尝试

```
$ conda upgrade -n base -c defaults --override-channels conda
```



## conda安装包报错

### PackagesNotFoundError: The following packages are not available from current channels: ruamel_yaml_conda

pip和conda部分安装包名不完全一致

pip下 **ruamel_yaml_conda** 等价于conda下 **ruamel.yaml**

使用：

```
$ conda install -c conda-forge ruamel.yaml
```

### RemoveError: 'requests' is a dependency of conda and cannot be removed from conda's operating environment

执行：

```
$ conda update --force conda
```



## conda编码报错

### Fatal Python error: init_sys_streams: can't initialize sys standard streams LookupError: unknown encoding: 65001

执行：

```
$ set pythonioencoding=utf-8
```

查看：

```
$ echo %PYTHONIOENCODING%
```

