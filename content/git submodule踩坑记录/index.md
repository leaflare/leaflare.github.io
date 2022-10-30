+++
title = "git submodule 踩坑记录"
date = 2022-01-08 12:41:34
slug = "202201081241"

[taxonomies]
tags = ["Git"]
categories = ["Git"]

+++

<!-- more -->

以https://github.com/iphysresearch/GWToolkit.git为例

### 添加子模块

添加一个远程仓库项目子模块到一个已有的主仓库项目中。

添加模块

```
$ git submodule add https://github.com/iphysresearch/GWToolkit.git GWToolkit
```

写错地址的补救方式

```
$ git rm --cached 仓库名
```

拉取出现空仓库

```
$ git submodule update --init --recursive
```

添加后，根目录下会出现 `.gitmodules` 文件，如果之前暴力删除了submodule，这里的 `.gitmodules` 文件也要删除。反过来，如果暴力删除了 `.gitmodules` 文件，删除或重新拉取submodule之前要先`git restore .gitmodules` 。

后文有完整的删除流程。



### 查看子模块

```
$ git submodule
```



### 更新子模块

```
$ git submodule update
```



### 删除子模块

step1. 删除子模块仓库本身

```
$ git rm --cached 仓库名
$ rm -rf 仓库名
```

step2. 删除 `.gitmodules` 文件中相关子模块的信息

形如：

```
[submodule "GWToolkit"]
        path = GWToolkit
        url = https://github.com/iphysresearch/GWToolkit.git
```

step3. 删除 `.git/config` 中相关子模块信息

形如：

```
[submodule "GWToolkit"]
        url = https://github.com/iphysresearch/GWToolkit.git
        active = true
```

step4. 删除 `.git` 文件夹中的相关子模块文件

```
$ rm -rf .git/modules/GWToolkit
```

