+++
title = "WSL C++ 23 Cookbook"
date = 2023-09-13 10:21:54
slug = "202309131021"

[taxonomies]
tags = ["Virtual Machine", "WSL" ]
categories = ["WSL"]

+++

<!-- more -->

WSL GCC/CMake Cookbook

## 指令速查

```
apt-cache policy gcc
```

很遗憾，目前没有（23.9.13）

GNU官网：[Index of /gnu/gcc](http://ftp.gnu.org/gnu/gcc/)

目前更新至gcc-13.2.0

## 安全可靠版

官网搜link

```
wget http://ftp.gnu.org/gnu/gcc/gcc-13.2.0/gcc-13.2.0.tar.gz
tar xf gcc-13.2.0.tar.gz
cd gcc-13.2.0/
./contrib/download_prerequisites
```

安装略慢，而且组件如果因为网络问题安装失败还要手动删除重新安装

```
mkdir build 
cd build
../configure -enable-checking=release -enable-languages=c, c++ -disable-multilib
sudo make install
```

太慢了可以开多线程 `make -j$(nproc)`

查看处理器核心数

```
nproc
```



## 懒人省事版

反正只是WSL这个玩具开发环境下写玩具项目而已（划掉

用别人的PPA（"Personal Package Archive"）

```
sudo add-apt-repository ppa:PPA_OWNER/PPA_NAME
sudo apt update
sudo apt install PACKAGE_NAME
```

找的是这个`ppa:ubuntu-toolchain-r/test`

之前的alternatives需要清一下

```
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt update
sudo apt install gcc-13 g++-13
ll /usr/bin/gcc-13
ll /usr/bin/g++-13
update-alternatives --display gcc
ll /etc/alternatives/g*
sudo update-alternatives --remove-all gcc
sudo update-alternatives --remove-all g++
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-13 10 --slave /usr/bin/g++ g++ /usr/bin/g++-13
g++ -v
gcc -v
```



## CMake更新最新版本

亲测当前（23.9.13）WSL可直接获取的最高版本cmake3.16.4不能支持CXX 23标准，所有还是只能手动升级

删掉旧版本的办法（不过似乎最好别删原生版本

```
sudo apt purge cmake
```

官网查link: [Download | CMake](https://cmake.org/download/)

```
wget https://github.com/Kitware/CMake/releases/download/v3.27.4/cmake-3.27.4.tar.gz
tar -xzvf cmake-3.27.4.tar.gz
cd cmake-3.27.4
sudo ./bootstrap
sudo make
sudo make install

cmake  --version 
```

