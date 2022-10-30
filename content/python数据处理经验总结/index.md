+++
title = "python数据处理经验总结"
date = 2021-11-15 14:43:14
slug = "202111151443"

[taxonomies]
tags = [ "Python" ]
categories = ["Python"]

+++

<!-- more -->

## 数据类型处理

### 查看数据类型

#### type()和isinstance()

区别：

type() 不会认为子类是一种父类类型，不考虑继承关系。

isinstance() 会认为子类是一种父类类型，考虑继承关系。

### bytes

#### bytes和str

```
>>> type(b'xxxxx')
<class 'bytes'>
 
>>> type('xxxxx')
<class 'str'>
```

bytes是byte的序列，而str是unicode的序列。

互相转化：

```
bytes = str.encode()
str = bytes.decode()
str = bytes.decode("gb2312") #使用 gb2312 的方式
```

#### bytearray

bytearray是可变的。

**bytearray()** 方法返回一个新字节数组。这个数组里的元素是可变的，并且每个元素的值范围: 0 <= x < 256。

```
>>>bytearray()
bytearray(b'')
>>> bytearray([1,2,3])
bytearray(b'\x01\x02\x03')
>>> bytearray('runoob', 'utf-8')
bytearray(b'runoob')
>>>
```

