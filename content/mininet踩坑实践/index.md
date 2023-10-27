+++
title = "Mininet报错排查记录"
date = 2023-10-27 16:13:14
slug = "202310271613"

[taxonomies]
tags = [ "Web" ]
categories = ["Mininet"]

+++

<!-- more -->

## 踩坑&报错解决

### 拓扑保存和.mn文件打开报错

环境：Ubuntu 22.04.3 LTS / mininet 2.3.1b4 

事故现场：在GUI绘制拓扑并保存为.mn文件时，出现报错：

```
TypeError: a bytes-like object is required, not 'str'
```

问题解决：

mininet的GitHub issues中，有人建议使用Python 2启动mininet, 但使用Python 2保存后仍然无法重新打开保存的文件。

顺着Python2可以保存而Python3不能的思路，在GitHub issues中搜索Python版本相关问题，在Issue[#1059](https://github.com/mininet/mininet/pull/1059) 中发现

```
dict.items() returns a list in python2 and a dict_items in python3. dict_items can't be added with +, which caused failures when using python3. This update changes this to dict.update() which works in both python2 and python3.

When files are opened with f = open(filename, "wb"), the following calls to f.write expects bytes while miniedit provides regular strings. This works fine in python2, as python2 strings are bytes. This changed in python3, which caused miniedit to fail when saving files. This update changes the call to open() to use text-mode, which makes it possible to write strings regardless of python version.
```

注意到对`f.write`的调用需要字节，而`miniedit`提供了常规字符串，以及上文提到的miniedit文件，推测问题可能来自于miniedit文件中的相关部分。

查阅源码*mininet/examples/miniedit.py*  搜索*unicode*

发现第1423行出现 globals.get('unicode', str)

```python
def convertJsonUnicode(self, text):
	"Some part of Mininet don't like Unicode"
	unicode = globals.get( 'unicode', str )
	if isinstance(text, dict):
		return {self.convertJsonUnicode(key): self.convertJsonUnicode(value) for key, value in text.items()}
	if isinstance(text, list):
		return [self.convertJsonUnicode(element) for element in text]
	if isinstance(text, unicode):  # pylint: disable=undefined-variable
		return text.encode('utf-8')
	return text
```

将其改为globals().get('str', bytes)：

```python
def convertJsonUnicode(self, text):
	"Some part of Mininet don't like Unicode"
	str_type = globals().get('str', bytes)
	if isinstance(text, dict):
		return {self.convertJsonUnicode(key): self.convertJsonUnicode(value) for key, value in text.items()}
	if isinstance(text, list):
		return [self.convertJsonUnicode(element) for element in text]
	if isinstance(text, str_type):
		return text.encode('utf-8')
	return text
```

问题解决!



## 常用命令

Python3启动mininet:

```
sudo python3 mininet/examples/miniedit.py
```

list all active Open vSwitches

```
sudo ovs-vsctl list-br
```

delete existing flows

```
sudo ovs-ofctl del-flows switch_name
```

e.g. “sudo ovs-ofctl del-flows s5"

add flows

```
sudo ovs-ofctl add-flow switch_name ip,nw_dst=dst_ip,action=output:port_name
sudo ovs-ofctl add-flow switch_name in_port=port_name,actions=output:port_name
sudo ovs-ofctl add-flow switch_name arp,action=normal
```

e.g. "sudo ovs-ofctl add-flow s1 ip,nw_dst=10.0.0.1,action=output:1"

action=normal是必须的

show flows

```
sudo ovs-ofctl dump-flows switch_name
```

