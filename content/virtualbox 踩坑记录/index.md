+++
title = "Virtualbox 踩坑记录"
date = 2021-10-20 12:41:34
slug = "202110201241"

[taxonomies]
tags = ["Virtual Machine"]
categories = ["Virtual Machine"]

+++

<!-- more -->

Oracle VM Virtualbox 

## 安装

官网：[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)

Oracle VM VirtualBox Extension Pack也可在同页面下载

## 踩坑

### 挂载扩展包

Extension Pack可在`Tools`下导入，但用于实现虚拟机与宿主机共享剪切板的扩展包需要单独下载

以版本号7.0.12为例：[Index of http://download.virtualbox.org/virtualbox/7.0.12](http://download.virtualbox.org/virtualbox/7.0.12/)

要下载的是VBoxGuestAdditions_7.0.12.iso    

在工具栏`Devices`->`Optional Drives`导入遇到了挂载失败的问题

```
Unable to insert the virtual optical disk 
```

解决办法：

关闭虚拟机，`Settings->Storage->Controller: IDE`下添加

然后启动虚拟机，执行：

```
sudo mkdir /mnt/cdrom

sudo mount /dev/cdrom /mnt/cdrom

cd /mnt/cdrom

./VBoxLinuxAdditions.run
```

重启虚拟机

### 启动黑屏

安装扩展后启动虚拟机出现黑屏

解决办法：

调大显存：`Settings->Display->Video Memory`，直接拉满
