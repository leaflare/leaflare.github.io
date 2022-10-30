+++
title = "C++记录"
date = 2019-01-10 17:21:06
slug = "201901101721"

[taxonomies]
tags = ["Cram" ]
categories = ["C/C++"]

+++

<!-- more -->

## vector相关

### vector传参

c++中常用的vector容器作为参数时，有三种传参方式，分别如下（为说明问题，用二维vector）：

- function1(std::vector<std::vector<int> > vec)，传值
- function2(std::vector<std::vector<int> >& vec)，传引用
- function3(std::vector<std::vector<int> >* vec)，传指针

三种方式的效果分别为：

- 会发生拷贝构造
- 不会发生拷贝构造
- 不会发生拷贝构造

验证程序

```c++
#include <iostream>
#include <vector>

using namespace std;

void function1(std::vector<std::vector<int> > vec)
{
    cout<<"-----------------------------------------"<<endl;
    //打印vec的地址
    cout<<"function1.&vec:"<<&vec<<endl;
    //打印vec[i]的地址（即第一层vector的地址）
    cout<<"function1.&vec[i]:"<<endl;
    for(int i=0;i<2;i++)
        cout<<&vec[i]<<endl;
    //打印vec的各元素地址
    cout<<"function1.&vec[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<&vec[i][j]<<" ";
        cout<<endl;
    }
    cout<<"---------------------------"<<endl;
    //打印vec的各元素值
    cout<<"function1.vec[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<vec[i][j]<<" ";
        cout<<endl;
    }
}
void function2(std::vector<std::vector<int> >& vec)
{
    cout<<"-----------------------------------------"<<endl;
    //打印vec的地址
    cout<<"function2.&vec:"<<&vec<<endl;
    //打印vec[i]的地址（即第一层vector的地址）
    cout<<"function2.&vec[i]:"<<endl;
    for(int i=0;i<2;i++)
        cout<<&vec[i]<<endl;
    //打印vec的各元素地址
    cout<<"function2.&vec[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<&vec[i][j]<<" ";
        cout<<endl;
    }
    cout<<"---------------------------"<<endl;
    //打印vec的各元素值
    cout<<"function2.vec[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<vec[i][j]<<" ";
        cout<<endl;
    }

}
void function3(std::vector<std::vector<int> > *vec)
{
    cout<<"-----------------------------------------"<<endl;
    //打印vec的地址
    cout<<"function3.&vec:"<<vec<<endl;
    //打印vec[i]的地址（即第一层vector的地址）
    cout<<"function3.&vec[i]:"<<endl;
    for(int i=0;i<2;i++)
        cout<<&(*vec)[i]<<endl;
    //打印vec的各元素地址
    cout<<"function3.&vec[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<&(*vec)[i][j]<<" ";
        cout<<endl;
    }
    cout<<"---------------------------"<<endl;
    //打印vec的各元素值
    cout<<"function3.vec[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<(*vec)[i][j]<<" ";
        cout<<endl;
    }
}

int main()
{
    //创建2*3的vector容器v,初始值均初始化为0 1 2 1 2 3
    std::vector<std::vector<int> > v(2,std::vector<int>(3,0));
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            v[i][j]=i+j;
    }

    //打印v的地址
    cout<<"&v:"<<&v<<endl;
    //打印v[i]的地址（即第一层vector的地址）
    cout<<"&v[i]:"<<endl;
    for(int i=0;i<2;i++)
        cout<<&v[i]<<endl;
    //打印v的各元素地址
    cout<<"&v[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<&v[i][j]<<" ";
        cout<<endl;
    }

    cout<<"---------------------------"<<endl;
    //打印v的各元素值
    cout<<"v[i][j]:"<<endl;
    for(int i=0;i<2;i++)
    {
        for(int j=0;j<3;j++)
            cout<<v[i][j]<<" ";
        cout<<endl;
    }

    function1(v);
    function2(v);
    function3(&v);

    return 0;
}
```



### vector初始化

#### 一维数组

vector < int > v(n,m);//n个m

或

vector < int > v;此时size=0

v.resize(n, m) ;

或

vector < int > v(v0); //使用另外一个数组来初始化v，这里的v0也必须是vector

或

vector < int > v(*p, *q); //使用另外一个数组的指针来初始化v，这里即可以使用vector的指针，也可以使用普通数组的指针。

例如：

```c++
	int a[3] = { 1,2,3 };
	vector<int> v = {1,2,3,4};
	
	vector<int> v2(a, a+2);
	for (int i = 0; i < v2.size(); i++)
		cout << v2[i] << " ";          //输出为1 2
	cout << endl;
	
	vector<int> v3(v.begin()+1, v.end() - 1);
	for (int i = 0; i < v3.size(); i++)
		cout << v3[i] << " ";   		//输出为2 3

```

#### 二维数组

vector < vector < int > > v;//和一维数组一样，这里的v的size是0。
可以先v.resize(n)来初始化一个长度为n的二维数组，然后使用一个for循环

```c++
		for (int i = 0; i < v.size(); i++)
			v[i].resize(n);

```

这样v的大小就是n*n。
也可以v.resize(n, v0),使用n个一维数组来初始化一个二维数组。
这里v0可以直接使用vector(n,m)来表示，比如

```c++
vector<vector<int>> v(3, vector<int>(4,1));

```

3*4大小的二维数组，值全为1。

vector < vector < int > > v(n, v0);//这个和resize的用法一样。

使用指针初始化：

和一维数组类似，可以使用vector指针

```c++
		vector<int> v0 = { 1,2,3,4 };
		vector<vector<int>> v1(4, v0);
		vector<vector<int>> v(v1.begin()+1, v1.end()-1); //此时的v是 {{1,2,3,4},{1,2,3,4}}

```

### vector清空

```c++
#include <iostream>
#include <vector>

using namespace std;

//STL vector的几种清空容器（删除）办法

void test()
{
    vector<int> vecnum;
    vecnum.push_back(1);
    vecnum.push_back(2);
    vecnum.push_back(3);
    vecnum.push_back(4);
    vecnum.push_back(5);
    vecnum.push_back(6);
    vecnum.push_back(7);

    //打印vector的大小和容量
    cout << "old: size = " << vecnum.size() << " ; capacity = " << vecnum.capacity() << endl;

    //1. clear()，清空元素，但不回收空间

    //vecnum.clear();

    //2. 使用 erase循环删除，结果同上

    //vector<int>::iterator it;
    //for (it = vecnum.begin(); it != vecnum.end(); )
    //{
    //    //注意使用erase()方法时，迭代不要随意的++
    //    it = vecnum.erase(it);
    //}

    //结论：erase在每次操作时，迭代器指针会整体前移1，就是每次都会移动全部数据，所以vector不适合做频繁删除的容器

    //3. 最简单的使用swap,清除元素并回收内存
    //3.1 写法一

    //vector<int>().swap(vecnum);

    //3.2 写法二
    vecnum.swap(vector<int>());

    //打印vector的大小和容量
    cout << "last: size = " << vecnum.size() << " ; capacity = " << vecnum.capacity() << endl;

}

int main()
{
    test();
    getchar();
    return 0;
}
```

## 数组相关

### 数组初始化

1. 数组初始化有memset与fill两个函数，前者较快。
2. memset(数组名,初始值,初始字节长度)
3. memset需要引入string库  memset按照字节赋值，所以一般用于初始化为-1,或0；它们补码中每个字节都一样
4. 函数内部定义数组容量不要大于10^6,否则会报错。因为函数内部申请的局部变量来自系统栈，允许申请的容量较小。在外部申请的全局变量来自静态存储区，允许申请的空间较大。

```c
//memset 初始化是将一个一个字节填充的
//数在内存中是以补码形式存放，正数的原码，反码，补码都一样。负数的补码为原码取反+1。 
int a[2];
memset(a,0,sizeof(a));
//0的补码为：00000000
//此时,这一片内存存储结果为(4字节a[0]+4字节a[1])：00000000|00000000|00000000|00000000|00000000|00000000|00000000|00000000 补码形式
//所以a[0] = a[1] = 0(补码：00000000|00000000|00000000|00000000)
memset(a,1,sizeof(a));
//1的补码为：00000001
//此时,这一片内存存储结果为(4字节a[0]+4字节a[1])：00000001|00000001|00000001|00000001|00000001|00000001|00000001|00000001 补码形式
//所以此时a[0]=a[1]=16843009(补码:00000001|00000001|00000001|00000001)
memset(a,1,sizeof(a));
//1的补码为：11111111
//此时,这一片内存存储结果为(4字节a[0]+4字节a[1])：11111111|11111111|11111111|11111111|11111111|11111111|11111111|11111111 补码形式
//a[0] = a[1] = -1(补码：11111111|11111111|11111111|11111111)
```



## 数值相关

#### 初始化极大值

```c++
int num1 = 1e9;
int num1 = 1e10;
输出的结果为：
num1 = 1 000 000 000；
num2 = 1 410 065 408；
```

为什么num2 不等于 10000000000 ？
C/C++中int类型是32位的，范围是-2147483648 到 2147483647。
int占用4个字节，也就是32位，除了第一位代表符号，剩下的31位可用。
十进制的 1e10时，转换为二进制：
10 0101 0100 0000 1011 1110 0100 0000 0000前两位出现数据溢出问题。
而0101 0100 0000 1011 1110 0100 0000 0000 转换成十进制就是 1 410 065 408

#### 正无穷与负无穷

```c++
    int a=0x3f3f3f3f;
    int b=0xc0c0c0c0;
    输出：
    a=1061109567
    b=-1061109568

```



## 输入输出处理相关

### EOF条件判断：while (scanf("%d%d",&n,&m)!=EOF) 功能等效于 while (~scanf("%d%d",&n,&m))

EOF的值为0，测试：

```c
#include <bits/stdc++.h>
using namespace std;
int main() {
    printf("%d",EOF);   //output -1
    return 0;
}
```

● 函数 scanf() 的返回值是输入值的个数，如果没有输入值就是返回-1；

● 符号 ~ 表示按位取反操作，-1按位取反操作的结果是0。

### 结果输出记得换行符



## C/C++等效语句

### while(scanf("%d",&a)!=EOF) 功能等效于 while(!(cin>>a).eof())

C语句

```
while(scanf("%d",&a)!=EOF)
```

C++语句

```
while(!(cin>>a).eof())
```

