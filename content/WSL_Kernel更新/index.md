+++
title = "WSL Linux kernel 手动更新"
date = 2023-09-14 10:21:54
slug = "202309141021"

[taxonomies]
tags = ["Virtual Machine", "WSL" ]
categories = ["WSL"]

+++

体验WSL环境下手动编译安装Linux kernel，实践&踩坑记录

<!-- more -->

目前（23.9.13）最新的WSL只提供到5.15.90，更高版本的Linux kernel 需要手动更新。



wsl内查看当前Linux kernel版本

```
uname -rs
```

## 镜像备份和恢复

预防翻车，先对wsl进行备份：

cmd查看现有wsl

```
wsl -l -v
```

导出镜像

```
wsl --export wsl_name path\xxx.tar
```

恢复同理

```
wsl --import wsl_name path\xxx.tar
```

但是导入的时候Ubuntu版本必须相同，例如只能在安装了WSL Ubuntu-20.04的主机上导入Ubuntu-20.04版本的镜像

## 获取更新

```
sudo apt install git bc build-essential flex bison libssl-dev libelf-dev dwarves
```

## 方法一

从 [WSL2 git](https://link.zhihu.com/?target=https%3A//github.com/microsoft/WSL2-Linux-Kernel/releases) 下载微软改好的内核，但是仍需要编译安装

虽然版本选择上很受限，但优点是不太容易遇到不适配或者编译失败的问题。

```
wget https://github.com/microsoft/WSL2-Linux-Kernel/archive/refs/tags/linux-msft-wsl-6.1.21.2.tar.gz
tar -xf linux-msft-wsl-6.1.21.2.tar.gz
```

解压后

```
cd WSL2-Linux-Kernel-linux-msft-wsl-6.1.21.2
```

```
make KCONFIG_CONFIG=Microsoft/config-wsl
```

检查目录.\arch\x86\boot下是否有bzImage文件

当前根目录下（目录WSL2-Linux-Kernel-linux-msft-wsl-6.1.21.2）继续执行

```
powershell.exe /C 'Copy-Item .\arch\x86\boot\bzImage $env:USERPROFILE'
powershell.exe /C 'Write-Output [wsl2]`nkernel=$env:USERPROFILE\bzImage | % {$_.replace("\","\\")} | Out-File $env:USERPROFILE\.wslconfig -encoding ASCII'
```

执行顺利的话在`$env:USERPROFILE`会多出两个文件`bzImage`和`.wslconfig`, `bzImage`就是前面编译的产物, `.wslconfig`是wsl的配置文件，最大进程数等配置也可以在这里设置。该配置直接作用于当前WSL。

然后

```
exit
wsl --shutdown
wsl
```

重启后确认当前内核版本

```
uname -rs
Linux 6.1.21.2-microsoft-standard-WSL2
```



## 方法二（容易踩坑）

从 [kernel.org](https://link.zhihu.com/?target=http%3A//kernel.org/) 下载最新的内核，配合微软的配置，编译安装任意指定版本，但是部分版本可能会遇到未知的坑



在官网找到想要的版本[kernel/git/stable/linux.git - Linux kernel stable tree](https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git)

```
wget https://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git/snapshot/linux-6.5.2.tar.gz
tar -xf v6.5.2.tar.gz
```

WSL GitHub：[microsoft/WSL2-Linux-Kernel: The source for the Linux kernel used in Windows Subsystem for Linux 2 (WSL2) (github.com)](https://github.com/microsoft/WSL2-Linux-Kernel)，在仓库的branch中找到对应的支持版

```
wget https://github.com/microsoft/WSL2-Linux-Kernel/blob/linux-msft-wsl-6.1.y/Microsoft/config-wsl
```

可以看到`Saving to: ‘config-wsl’`，对应替换下文的`config-wsl`

```
cp config-wsl linux-6.5.2/arch/x86/configs/wsl_defconfig
```

```
cd linux-6.5.2
make KCONFIG_CONFIG=arch/x86/configs/wsl_defconfig -j$(nproc)
```

一路回车，反正有备份（划掉

后面的步骤同上

编译完不要退出去，检查目录linux-6.5.2/arch/x86/boot下是否有bzImage文件，在`linux-6.5.2`目录下执行

```
powershell.exe /C 'Copy-Item .\arch\x86\boot\bzImage $env:USERPROFILE'
powershell.exe /C 'Write-Output [wsl2]`nkernel=$env:USERPROFILE\bzImage | % {$_.replace("\","\\")} | Out-File $env:USERPROFILE\.wslconfig -encoding ASCII'
```

执行顺利的话在`$env:USERPROFILE`会多出两个文件`bzImage`和`.wslconfig`, `bzImage`就是前面编译的产物, `.wslconfig`是wsl的配置文件，最大进程数等配置也可以在这里设置。

然后

```
exit
wsl --shutdown
wsl
```

重启后确认当前内核版本

```
uname -rs
```



补充：wsl本身的默认路径

```
C:\Users\USER_NAME\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu20.04LTS_79rhkp1fndgsc\LocalState
```

重装的时候可以检查一下是否卸载干净了
