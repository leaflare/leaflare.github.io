+++
title = "python经验总结"
date = 2022-01-15 14:43:14
slug = "202201151443"

[taxonomies]
tags = [ "Python" ]
categories = ["Python"]

+++

<!-- more -->

# 查找安装地址

控制台

```
where python
```



# 调试/测试

## 程序计时

`time.perf_counter()`以秒为单位返回时间的浮点值

`time.perf_counter_ns()`返回时间以纳秒为单位

## 控制台打印屏蔽与显示

```python
import sys, os

# Disable
def blockPrint():
    sys.stdout = open(os.devnull, 'w')

# Restore
def enablePrint():
    sys.stdout = sys.__stdout__
```



# 报错处理

## Python版本问题

### ModuleNotFoundError: No module named 'cPickle'

python2有cPickle，但是python3没有cPickle

python2使用：

```python
import cPickle
```

python3使用：

```python
import pickle
```

### name 'reload' is not defined

- 在Python2.x中由于str和byte之间没有明显区别，经常要依赖于defaultencoding来做转换。 
- 在python3中有了明确的str和byte类型区别，从一种类型转换成另一种类型要显式指定*encoding。*

python2使用：

```python
import sys
reload(sys)
sys.setdefaultencoding(‘utf-8’)
```

python3使用：

```python
import importlib,sys
importlib.reload(sys)
```

### AttributeError: module 'sys' has no attribute 'setdefaultencoding'

报错原因同上，python3没有`sys.setdefaultencoding('utf-8')`，删除该行即可



## 多线程相关

### greenlet.error: cannot switch to a different thread

debug模式下会对线程的切换造成混乱,解决方案:

第一种: 禁用debug模式即可

第二种:在主函数中加入monkey patch

```python
if __name__ == '__main__':
    from gevent import monkey
    monkey.patch_all()
```



## PyQt5相关

### ERROR: Could not find a version that satisfies the requirement PIL (from versions: none)

ERROR: No matching distribution found for PIL

PIL，全称 Python Imaging Library，是 Python 平台一个功能非常强大而且简单易用的图像处理库。但是，由于 PIL 仅支持到Python 2.7，加上年久失修，于是一群志愿者在 PIL 的基础上创建了兼容 Python 3 的版本，名字叫 Pillow ，现在可以通过安装 Pillow 来使用 PIL。

```python
pip install pillow
```



### ImportError: cannot import name 'options' from 'pyecharts'

解决：

```
pip install pyecharts -U
```



## Numpy相关

### ValueError: numpy.ndarray size changed, may indicate binary incompatibility.

核心原因是 numpy 版本与当前一些库不匹配，把 numpy 更新到最新版本即可

```
pip install --upgrade numpy
```



## urllib相关

### AttributeError: module 'urllib' has no attribute 'request'

解决：

```
from urllib import request
```

### Unresolved reference 'urllib2'

解决：

在python3.3以后，用urllib.request代替urllib2

import语句使用

```
from urllib import request
```



## pandas相关

### SettingWithCopyWarning:  A value is trying to be set on a copy of a slice from a DataFrame

在向DataFrame中插入新行时，Pandas报出了这个警告。虽然不影响程序执行，但还是不想看到警告（X

事故现场：

```python
add = {'ts_code': stock_code, 'trade_date': now_date, 'close': now_price}
df.loc[len(df)] = add
```

解决方法：

直接对副本修改就会出现这个警告，需要在修改前应该先对副本复制一下

```python
add = {'ts_code': stock_code, 'trade_date': now_date, 'close': now_price}
df = df.copy()
df.loc[len(df)] = add
```

