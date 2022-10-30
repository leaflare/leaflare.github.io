+++
title = "Java版本问题处理合集"
date = 2021-11-12 16:13:14
slug = "202111121613"

[taxonomies]
tags = [ "Java" ]
categories = ["Java"]

+++

<!-- more -->

## 不一致报错

### A JNI error has occurred, please check your installation and try again

javac与java版本不同所致

检查

```
javac -version
java -version
```

在环境变量Path变量中将需要使用的路径上移，例如将`%JAVA_HOME%\bin`路径移至`C:\Program Files (x86)\Common Files\Oracle\Java\javapath`之上

这个时候在idea运行之前的项目还是会有报错，需要手动换SDK
