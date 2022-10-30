+++
title = "maven报错处理合集"
date = 2020-12-09 12:55:12
slug = "202012091255"

[taxonomies]
tags = ["Maven"]
categories = ["Build Automation"]

+++

<!-- more -->

## 错误: 找不到或无法加载主类 .encoding=UTF-8

事故现场

```
java -Dfile.encoding=UTF-8 -jar xxx.jar
```

原因：

idea内置的terminal类似 PowerShell，而上述命令适用于cmd

解决方法：

在 PowerShell 中运行时需要加引号使用：

```powershell
java '-Dfile.encoding=utf-8' -jar xxx.jar
```

或者用cmd运行

