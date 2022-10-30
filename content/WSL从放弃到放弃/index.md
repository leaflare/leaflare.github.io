+++
title = "WSL使用记录"
date = 2022-01-26 22:05:54
slug = "202201262205"

[taxonomies]
tags = ["Virtual Machine", "WSL" ]
categories = ["WSL"]

+++

<!-- more -->

WSL是适用于 Linux 的 Windows 子系统，可以直接在 Windows 上运行 GNU/Linux 环境。

## WSL安装

微软商店下载需要的Linux发行版

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

### 附常用Docker 命令：

- 通过输入以下命令列出 Docker CLI 中可用的命令：`docker`
- 使用以下命令列出特定命令的信息：`docker <COMMAND> --help`
- 使用以下命令列出计算机上的 docker 映像（此时仅为 hello-world 映像）：`docker image ls --all`
- 使用以下命令列出计算机上的容器：`docker container ls --all` 或 `docker ps -a`（如果没有 -a 显示全部标志，则仅显示正在运行的容器）
- 使用以下命令列出有关 Docker 安装的系统范围的信息，包括 WSL 2 上下文中你可使用的统计信息和资源（CPU & 内存）：`docker info`

## WSL设置

在宿主机上找到WSL：

WIN+R 或者文件资源管理器 输入`\\wsl$`

`\\wsl$\Ubuntu\etc\apt`sources.list换国内镜像

`~`目录下的文件对应`\\wsl$\Ubuntu\home\unix`目录

## VS Code

### 进入WSL工作环境

VS Code安装Remote - WSL插件

按**F1**或**【Ctrl】+【Shift】+【P】**，打开命令面板，输入关键词"WSL"，选择需要的Linux发行版

或者

点击VS Code左下角的打开远程窗口图标——>New WSL Window using Distro...

### 打开项目

打开 wsl 终端，进入到要打开的项目中，在项目中执行命令

```
code .
```

或者

在VS Code中进入WSL工作环境后，

打开命令面板，输入关键词"Remote-Containers"，选择在容器中打开文件夹

—>选择要容器化的项目文件夹

—>选择container configuration definition

此后系统将打开新的 VS Code 实例，并生成新映像，生成完成后会启动容器。 会出现新的 `.devcontainer` 文件夹，其中 `Dockerfile` 和 `devcontainer.json` 文件中包含容器配置信息。

### 在容器内运行和调试

打开“运行”菜单（Ctrl+Shift+D 或选择最左侧菜单栏上的选项卡）。 

然后选择“运行和调试”以选择调试配置，并选择最适合项目的配置

选择“运行”——>“启动调试”（或只按 F5 键）。 会在 VS Code 中打开终端，出现：“正在 http://127.0.0.1:8000/ 启动开发服务器。使用 CONTROL-C 退出服务器。”

按住 Control 键并选择显示的地址，可以在默认 Web 浏览器中打开，并查看在其容器中运行的项目。



## 命令行代理

使用`ipconfig`查询宿主机的ip

在WSL命令行配置，例如export ALL_PROXY=http:192.168.1.105:1081

```
export ALL_PROXY=http://宿主机ip:1081
```

