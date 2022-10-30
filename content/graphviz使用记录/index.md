+++
title = "Graphviz使用记录"
date = 2022-02-05 15:16:18
slug = "202202051516"

[taxonomies]
tags = ["Visuization", "Python", "Graphviz" ]
categories = ["Python"]

+++

<!-- more -->

Graphviz 是一个开源的可视化图形工具，用于绘制结构化的图形网络，通过对输入脚本的解析，分析出其中的点、边及子图，然后根据属性进行绘制。

## 安装配置

官网[Graphviz](http://www.graphviz.org/)

下载[Download | Graphviz](https://graphviz.org/download/)

Graphviz是一个用 dot 语言编写的绘图脚本，在Windows中直接用pip安装会出现下面的报错

```
graphviz.backend.execute.ExecutableNotFound: failed to execute 'dot', make sure the Graphviz executables are on your systems' PATH
```

需要将graphviz中的bin路径添加到环境变量path中

控制台输入命令：`dot -version`检验是否配置成功

在Python中使用，需要

```
pip install graphviz
```

### Python 调用 Graphviz 工具

控制台输入

```
echo process1 = subprocess.Popen(command1,stdout=subprocess.PIPE,shell=True)
```

Python 安装路径下，找到文件 **subprocess.py**

如果是Anaconda，文件所在的地址是C:\Users\xxx\Anaconda3\Lib

找到 **Popen(object)** 类

![](.\img\popen.png)

找到这个类的 **init()**方法，把 640行的**shell=False** 修改为 **shell=True**。

![](.\img\init.png)

记得重启IDE！

### 测试代码

```python
from graphviz import Digraph
# 需要一个对象来作为生成 DOT 脚本的载体，通过这个语句实例化一个图形对象。
dot = Digraph('test')
# node() 方法可以在对象中生成一个结点
dot.node("1","start")
dot.node("2","end")
# edge() 方法可以添加一条边
dot.edge('1','2')
# 对象.view(filename="文件名", directory="输出路径")
dot.view()
print(dot)
```

运行生成test.gv.pdf文件，效果如下图：

<img src=".\img\res.png" style="zoom:100%;" />

print(dot)在控制台打印出

```
digraph test {
	1 [label="start"]
	2 [label="end"]
	1 -> 2
}
```

上面的代码可以直接放到 Graphviz 工具(GVEdit)里运行



详细参数介绍[Graphviz参数](https://www.cnblogs.com/Zzbj/p/11431015.html)



## 绘制Python类图

安装Pyreverse（在pylint模块）

```
pip install pylint
```

命令行检查

```
pyreverse -h
```

### 例子

`__init__`.py文件，（默认设置只能画这个文件名，暂时还没试出来不改文件名画图的方法

```python
class A():
    def __init__(self):
        pass


class B(A):
    def __init__(self):
        self.m_b = "Hello B"


class C(B):
    def __init__(self):
        self.m_c = 123


class D(B):
    def __init__(self):
        self.m_d = [12, 123]
```

目录结构：/code/example/\__init__.py

在code目录命令行

```
pyreverse example/
```

```
dot -Tpdf classes.dot -o classes.pdf
```

产生文件classes.dot和classes.pdf

<img src=".\img\0.png" style="zoom:80%;" />

<img src=".\img\1.png" style="zoom:60%;" />

