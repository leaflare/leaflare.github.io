+++
title = "C/C++_动态内存分配"
date = 2022-01-13 21:05:54
slug = "202201132105"

[taxonomies]
tags = ["C/C++" ]
categories = ["C/C++"]

+++

<!-- more -->

## 内存分配

### 静态分配

编译时分配或内存的静态分配：命名变量的内存由编译器分配。在编译时必须知道确切的大小和存储，对于数组声明，大小必须是恒定的。

### 动态分配

运行时分配或动态分配内存：在运行时分配内存，在程序运行中动态分配内存空间，内存段称为堆或自由存储。在这种情况下，编译器不必事先知道项目的确切空间或编号。在这种情况下，指针起着重要作用。

可以在程序运行时动态分配存储空间，但不能"动态"创建新的变量名称，因此，动态分配需要两个条件：

1. 在内存中创建动态空间

2. 将其地址存储在指针中（从而可以访问空间）

   

### 静态内存分配和动态内存分配的比较

C++ 程序中的内存分为两个部分：

- **栈：**在函数内部声明的所有变量都将占用栈内存。
- **堆：**这是程序中未使用的内存，在程序运行时可用于动态分配内存。

静态内存是由系统自动分配，由系统自动释放的； 静态内存是在栈分配的。

动态内存是由程序员手动分配,手动释放的；动态内存是在堆分配的。



## C动态内存分配

C语言使用了一个库解决方案实现动态内存分配，其函数malloc( )，calloc( )，realloc( )和free( )在<cstdlib>（在C中称为<stdlib.h>）中定义。这些函数可用于C++，用于分配和解除分配动态内存。

```c++
void *malloc(size_t size)
```

- **size** − 内存块的大小，以字节为单位。

分配请求的内存并返回指向该内存的指针。

```c++
void *calloc(size_t nitems, size_t size)
```

- **nitems** − 要分配的元素数。
- **size** − 元素的大小。

malloc 和 calloc 的不同之处在于，malloc 不会将内存设置为零，而 calloc 会将分配的内存设置为零。

```c++
void *realloc(void *ptr, size_t size)
```

- **ptr** − 指向先前使用malloc，calloc或realloc分配的内存块的指针，以进行重新分配。如果是 NULL，则分配一个新块，并且函数将返回指向该块的指针。
- **size** − 内存块的新大小，以字节为单位。如果为 0 并且 ptr 指向现有内存块，则 ptr 所指向的内存块将解除分配，并返回 NULL 指针。

```c++
void free(void *ptr)
```

- **ptr** − 指向先前分配了 malloc、calloc 或 realloc 以解除分配的内存块的指针。如果空指针作为参数传递，则不会发生任何操作。

示例：

```c++
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main () {
   char *str;

   /* Initial memory allocation */
   str = (char *) malloc(15);
   strcpy(str, "tutorialspoint");
   printf("String = %s,  Address = %u\n", str, str);

   /* Reallocating memory */
   str = (char *) realloc(str, 25);
   strcat(str, ".com");
   printf("String = %s,  Address = %u\n", str, str);

   /* Deallocate allocated memory */
   free(str);
   
   return(0);
}
```

输出：

```c++
String = tutorialspoint, Address = 355090448
String = tutorialspoint.com, Address = 355090448
```



## C++动态内存分配

### new 和 delete 运算符

```c++
new data-type;
```

例如：

```c++
int * p; // declares a pointer p
p = new int;  // dynamically allocate an int for loading the address in p
double * d;  // declares a pointer d
d = new double;  // dynamically allocate a double and loading the address in p
```

C 中的 malloc（） 函数在C++中仍然存在，但建议避免使用 malloc( )函数。

因为容易出错（X

因为new 不仅分配内存，它还构造对象 （√

当不再需要动态分配的变量时，可以使用 delete 运算符来释放内存空间。(对应free( ))

```c++
delete var_name;
```

简单示例：

```c++
#include <iostream>
using namespace std; 

int main()
{
    double* val = NULL;
    val = new double;
    *val = 38184.26;
    cout << "Value is : " << *val << endl;
    delete val;
}
```



### 数组动态内存分配

```c++
char* val  = NULL;      // Pointer initialized with NULL value
val = new char[40];     // Request memory for the variable
delete [] val;			// Delete the array pointed to by val
```



### 构造函数动态内存分配

```c++
#include <iostream>
using namespace std; 

class stud {
public:
    stud()
    {
        cout << "Constructor Used" << endl;
    }
    ~stud()
    {
        cout << "Destructor Used" << endl;
    }
};

int main()
{
    stud* S = new stud[6];
    delete[] S;
}
```

