+++
title = "Docker 实践记录"
date = 2022-10-06 14:47:19
slug = "202210061447"

[taxonomies]
tags = ["Docker"]
categories =  ["Container"]

+++

<!-- more -->

## Docker配置WSL Integration

启动Docker Desktop

docker settings——>General——>勾选`Use the WSL 2 based engine` 

docker settings——>Resources——>WSL Integration——>选择要启用的WSL 2 版本

如果已经安装配置过WSL却出现`You don’t have any WSL 2 distro. Please convert a WSL 1 distro to WSL 2`，解决方法如下：

使用命令检查WSL模式：

```
wsl.exe -l -v
```

将已存在的Linux发行版转到v2：

```
wsl.exe --set-version ubuntu 2
```

(Ubuntu是要启用的发行版本)

将 WSL 2 设置为默认版本：

```
wsl --set-default-version 2
```

### 常用速查：

- 列出计算机上的 docker 镜像：`docker image ls --all`
- 列出计算机上的容器：`docker container ls --all` 或 `docker ps -a`（如果没有 -a 显示全部标志，则仅显示正在运行的容器）
- 列出Docker 安装的系统信息，包括 WSL 2 上下文中你可使用的统计信息和资源（CPU & 内存）：`docker info`
- 运行镜像：`docker run --rm -it XXX`   --rm是指运行完了之后自动把容器删除，便于管理（X
- 删除镜像：`docker rmi XXX`
- 创建镜像：`docker build -t XXX .`  -t ：镜像的标签，注意最后的`‘.'`代表路径当前目录下，可替换其他path
- 将指定镜像保存成 tar 归档文件：`docker save -o XXX.tar XXX`      -o ：输出到的文件。
- 两组导入导出，不能混用：save 和 load，export 和 import。因为 import 和 export 导入导出的是一个容器的快照, 不是镜像本身。快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积更大。docker save 保存的是镜像（image），docker export 保存的是容器（container），docker load 用来载入镜像包，docker import 用来载入容器包，但两者都会恢复为镜像

