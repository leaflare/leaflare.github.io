+++
title = "C++_编译报错处理合集"
date = 2022-01-11 17:36:14
slug = "202201111736"

[taxonomies]
tags = ["C/C++" ]
categories = ["C/C++"]

+++

<!-- more -->

## 编译报错

### error: 'system' was not declared in this scope

使用`system("pause");`缺少头文件

```
#include <cstdlib> 
```

### error: 'malloc' was not declared in this scope

使用

```
#include <malloc.h>
```

也可以直接用

```
#include <stdlib.h>
```

函数原型

```
void *malloc(size_t size) 
```

size： 内存块的大小，以字节为单位。

该函数返回一个指针 ，指向已分配大小的内存。如果请求失败，则返回 NULL。

补充：

重新分配内存

```
void *realloc(void *ptr, size_t size)
```

### error: 'memset' was not declared in this scope

补充头文件

```
#include <cstring>
```

