+++
title = "C++STL笔记"
date = 2022-01-11 18:36:14
slug = "202201111836"

[taxonomies]
tags = ["C/C++" ]
categories = ["C/C++"]

+++

<!-- more -->

# 容器

## map迭代器

### iterator->first和iterator->second

分别指向键值和数值

```c++
map<string, string> m;
m["one"] = "sdfasf";
//迭代器类型需和容器保持一致
map<string, string>::iterator p = m.begin();
string a=p->first; // a值是 "one" 也可以写作p.first
string b=p->second; //b值是 "sdfasf",也可以写作p.second

```



## erase（）函数

函数原型：

（1）string& erase ( size_t pos = 0, size_t n = npos );

（2）iterator erase ( iterator position );

（3）iterator erase ( iterator first, iterator last );
对应用法：

（1）erase(pos,n); 删除从pos开始的n个字符，比如erase(0,1)就是删除第一个字符

（2）erase(position);删除position处的一个字符(position是个string类型的迭代器)

（3）erase(first,last);删除从first到last之间的字符（first和last都是迭代器）

注意事项：

- erase函数的返回值是一个指向被删除元素的下一个元素的迭代器。
- 注意使用完erase函数之后的迭代器是否会产生野指针。

示例：

```c++
#include<string>
#include<iostream>
using namespace std;
int main ()
{

string str ("This is an example phrase.");

string::iterator it;

//第（1）种方法

str.erase (10,8);

cout << str << endl;        // "This is an phrase."

//第（2）种方法

it=str.begin()+9;

str.erase (it);

cout << str << endl;        // "This is a phrase."

//第（3）种方法

str.erase (str.begin()+5, str.end()-7);

cout << str << endl;        // "This phrase."

return 0;

}
```



# 字符串

## string::npos

npos是一个常数，表示size_t的最大值（Maximum value for size_t）。

许多容器都提供这个东西，用来表示不存在的位置，类型一般是std::container_type::size_type。

用法：

1. npos可以表示string的结束位子，是string::type_size 类型的，也就是find（）返回的类型。find函数在找不到指定值得情况下会返回string::npos。举例如下（计算字符串中含有的不同字符的个数）：

```c++
#include <iostream>
#include <string>
using namespace std;
int main()
{
    string b;
    getline(cin,b);
    int count=0;
    for(int i=0;i<=127;i++)
        if(b.find(i)!=string::npos)
        count++;
    cout<<count;
}
```

2. string::npos作为string的成员函数的一个长度参数时，表示“直到字符串结束（until the end of the string）”。例如：

```c++

#include <iostream>
#include <limits>
#include <string>
using namespace std;
 
int main()
{
    string filename = "test.cpp";
    cout << "filename : " << filename << endl;
 
    size_t idx = filename.find('.');   //as a return value
    if(idx == string::npos)    
    {
        cout << "filename does not contain any period!" << endl;
    }
    else
    {
        string tmpname = filename;
        tmpname.replace(idx + 1, string::npos, "xxx"); //string::npos作为长度参数，表示直到字符串结束
        cout << "repalce: " << tmpname << endl;
    }
}

```

执行结果：

```
filename:test.cpp

replace: test.xxx
```

注意事项：

1. npos的类型

```
int idx = str.find("abc");
if (idx == string::npos)
  ...
```

上述代码中，idx的类型被定义为int，这是错误的，即使定义为 unsigned int 也是错的，它必须定义为 string::size_type。因为 string::size_type (由字符串配置器 allocator 定义) 描述的是 size，故需为无符号整数型别。因为缺省配置器以型别 size_t 作为 size_type，于是 -1 被转换为无符号整数型别，npos 也就成了该型别的最大无符号值。不过实际数值还是取决于型别 size_type 的实际定义。不幸的是这些最大值都不相同。事实上，(unsigned long)-1 和 (unsigned short)-1 不同(前提是两者型别大小不同)。因此，比较式 idx == string::npos 中，如果 idx 的值为-1，由于 idx 和字符串string::npos 型别不同，比较结果可能得到 false。
要想判断 find() 的结果是否为npos，最好的办法是直接比较：
if (str.find("abc") == string::npos) { ... }

2. string 类提供了 6 种查找函数,每种函数以不同形式的 find 命名。这些操作全都返回 string::size_type 类型的值，以下标形式标记查找匹配所发生的位置；或者返回一个名为 string::npos 的特殊值，说明查找没有匹配。string 类将 npos 定义为保证大于任何有效下标的值。
3. [string::npos 无符号数的陷阱-tang312-ChinaUnix博客](http://blog.chinaunix.net/uid-10014667-id-375027.html)

## 大小写转换

#### char

**toupper**
函数原型: `int toupper(int ch);`
函数返回: 与ch相应的大写字母

**tolower**
函数原型: `int tolower(int ch);`
函数返回: 返回ch所代表的字符的小写字母

```
string s="ABCDE";
s=tolower(s[0]);
```

#### char[]

**strupr**
函数原型：`char *strupr（char *a）`
函数功能：把字符串a中的串转换成大写
函数返回: 返回指向s参数的指针
所属文件：`#include <string.h>`

**strlwr**
函数原型: `char *strlwr（char *a）`
函数功能：将字符串a转换为小写形式
函数返回: 返回指向s参数的指针
所属文件：`#include <string.h>`

```
char s[]="ABCDE";
char *p=strlwr(s);//返回指向s参数的指针。
```

#### string

**transform和tolower及toupper进行结合**

所属文件：#include<algorithm>
transform(first1,last1,first2,result,binary_op);//first1是第一个容器的首迭代器，last1为第一个容器的末迭代器，first2为第二个容器的首迭代器，result为存放结果的容器，binary_op为要进行操作的二元函数对象或sturct、class

```c++
#include <iostream>
#include <algorithm>
using namespace std;
int main(){
    string str = "JFwxs";
    transform(str.begin(),str.end(),str.begin(),::toupper);
    cout<<str<<endl;//输出JFWXS
    transform(str.begin(),str.end(),str.begin(),::tolower);
    cout<<str<<endl;//输出jfwxs
}

```



# 踩坑记录

## 参数类型统一

### [Error] no matching function for call to 'min(int&, std::basic_string<char>::size_type)'

STL原型

```
template< class T > 
const T& min( const T& a, const T& b );
```

报错语句

```
min(n, str.size());
```

使用强制类型转换，改为

```
min(n, int(str.size()));
```

