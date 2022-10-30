+++
title = "coding通用记录"
date = 2019-06-10 17:21:06
slug = "201906101721"

[taxonomies]
tags = ["Cram" ]
categories = ["Cram"]

+++

<!-- more -->

# 操作符相关

## &&和&区别

&和&&都是逻辑运算符号，&&又叫短路运算符。区别如下：

```c++
int i=1;
if(i==2 & i++==2){}　//i==2为false,但i++==2还会继续比较，所以i++被执行
System.out.println(i);//i 打印出来为2
int j=1;
if(j==2 && j++==2){} //j==2为false,但j++==2不会继续比较，所以j++不被执行
System.out.println(j);//j打印出来为1
```

