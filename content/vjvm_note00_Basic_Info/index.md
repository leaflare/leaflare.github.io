+++
title = "vjvm note 00： Basic Info"
date = 2022-02-11 20:47:19
slug = "202202112047"

[taxonomies]
tags = ["vjvm"]
categories =  ["Java"]

+++

<!-- more -->

toy jvm，本系列blog用于记录基本项目结构和重要的实现方法

## Main

定义了一个实现了Callable<Integer>接口的Main类。Main方法创建了一个Main类的新实例，并通过将命令行参数传递给CommandLine类的execute方法来执行。

Main类使用PicoCLI库的注释定义了几个字段和选项。userClassPath字段是一个选项，指定了搜索类文件的类路径。entryClass参数指定要运行的类，args参数指定传递给Java程序的参数。dump选项指定是否要转储classfile的内容。

`call`方法是该程序的主要逻辑入口。使用指定的userClassPath创建一个新的VMContext类的实例。如果转储（dump ）选项被启用，它使用VMContext类的userLoader方法加载指定的类，并使用转储方法转储其内容。否则，将使用VMContext类的run方法运行指定的类。

dump方法接收一个JClass对象作为输入，并使用Logger类转储其内容。该方法打印出类的名称、次要和主要版本、访问标志、本类、超类、常量池、接口、字段和方法。



## VMContext

VMContext负责为运行Java字节码设置上下文。

- bootstrapLoader：JClassLoader的一个实例，用于从Java Runtime Environment的bootstrap classpath加载类。
- userLoader：JClassLoader的一个实例，用于从用户指定的classpath中加载类。
- interpreter：JInterpreter的一个实例，用于解释Java字节码指令。
- threads：JThread实例的列表，代表JVM中的线程。
- heap：JHeap的一个实例，用于管理JVM中的内存。

构造函数需要一个userClassPath参数，该参数用于初始化userLoader。构造函数还初始化了bootstrapLoader和解释器，并设置了堆。

run()方法需要一个 entryClass 参数，它是包含要执行的主方法的类的名称。它创建一个新的JThread实例，将其添加到线程列表中，用userLoader加载 entryClass，并找到main方法。然后，它使用解释器调用主方法。

getSystemSearchPaths() 方法返回一个代表系统 classpath 的 ClassSearchPath 实例数组，返回 ModuleSearchPath 的实例。

## 代码

[ffangli/toyjvm (github.com)](https://github.com/ffangli/toyjvm)

