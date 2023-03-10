+++
title = "vjvm note 01： ClassLoader"
date = 2023-02-13 20:47:19
slug = "202302132047"

[taxonomies]
tags = ["vjvm"]
categories =  ["Java"]

+++

<!-- more -->

## ClassLoader

作为Java运行时环境的一部分，Java ClassLoader动态地将Java类加载到Java虚拟机中。通过在Java中使用类加载器，它不需要Java运行时系统了解文件和文件系统的情况。Java类在应用程序需要时被加载，而不是一次性加载到内存中。Java中的ClassLoader是由JRE调用的。这些ClassLoaders动态地将类加载到内存中。

在java中，有以下3种默认的类加载器:

- **Bootstrap ClassLoader:** Loads class from JRE/lib/rt.jar
- **Extension ClassLoader:** Loads class from JRE/lib/ext
- **Application ClassLoader:** Loads class from CLASSPATH environment variable



## 双亲委托加载机制（Parent-First）

ClassLoader委托层次模型的运作顺序是:

Application ClassLoader -> Extension ClassLoader -> Bootstrap ClassLoader

Java 默认使用了名为 parent-first 的策略：每个 loader（除 Bootstrap Loader 外）均有一个**亲代加载器 （parent）**，在搜索 class 时首先委托亲代进行搜索，找不到时才搜索自己的加载路径。 于是，各个 ClassLoader 之间就形成了如下的委托关系：

<img src=".\img\loader-hierarchy.png" style="zoom:80%;" />

```
if 需要加载的 class 已被保存在 definedClass 中：
    返回已加载的 class
else if parent 不为 null：
    使用 parent 加载 class

if parent 未找到相应的 class：
    for searchPaths 中的每一项：
        尝试使用它来加载类，并调用 JClass 构造函数构造类

if 所有的 searchPath 都没有找到需要加载的类：
    返回 null
```

在查找 class 文件时，一个 loader 可能会搜索以下两种路径：

1. 搜索单个目录

   在指定 `/foo` 为加载路径时，如果加载 `bar.A` 类，你应该查找 `/foo/bar/A.class` 文件。

2. 单个 Jar 文件

   Jar 文件事实上是一个 zip 压缩包，将多个 class 文件打包在一起。在从 `/foo/bar.jar` 中加载 `baz.B` 时，你应该读取该文件并搜索其中的 `baz/B.class`路径。JDK 中提供了 `JarFile` 类来读取 jar 文件。（可以使用 `jar -tf <jarfile>` 查看 Jar 文件的内容）

## 代码

[ffangli/toyjvm (github.com)](https://github.com/ffangli/toyjvm)

```
$ tree src/main/java/vjvm/classloader
src/main/java/vjvm/classloader
├── searchpath
│   └── ClassSearchPath.java
        DirSearchPath.java
        JarSearchPath.java
        ModuleSearchPath.java
└──JClassLoader.java
```

