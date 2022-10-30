+++
title = "git报错处理合集"
date = 2022-01-07 17:45:12
slug = "202201071745"

[taxonomies]
tags = ["Git"]
categories = ["Git"]

+++

<!-- more -->

大部分都是网络问题报错

又是饱受gfw折磨的一天 テ_デ

记录一下（曾经可能）有效的方法



# 网络问题报错

## 解决github连不上

#### 修改hosts

在 C:\Windows\System32\drivers\etc 文件夹下找到hosts文件

Run  as Administrator打开

添加：

```
140.82.113.3  www.github.com
```

查询IP地址使用：

<https://ipaddress.com>



#### 使用镜像

<https://github.com.cnpmjs.org>



#### 去Gitee碰运气

<https://gitee.com>



#### 科学上网

能解决浏览器访问的问题

但命令行还是会间歇性失效orz

命令行设置代理

```bash
set http_proxy=http://127.0.0.1:1081
set https_proxy=http://127.0.0.1:1081
```



## bash代理

```bash
export ALL_PROXY=http://127.0.0.1:1081
export ALL_PROXY=socks5://127.0.0.1:1081
```



## Failed to connect to github.com port 443:connection timed out

### 比较可能有用的解决方法

#### 重新设置代理

```bash
$ git config --global http.proxy http://127.0.0.1:1080

$ git config --global https.proxy http://127.0.0.1:1080
```



#### 取消全局代理

```bash
$ git config --global --unset http.proxy
 
$ git config --global --unset https.proxy
```

体感上这个有用的概率更高一点



#### 其他曾经有效的解决方法

##### 包括但不限于：

把梯子关了

重开一个git bash

关机重启

换个时间再试一次



## OpenSSL SSL_read: Connection was reset, errno 10054

### OpenSSL SSL_read: Connection was reset, errno 10054 fatal: expected flush after ref listing

```bash
$ git config --global http.sslVerify "false"
```



# git clone失败

## fatal: early EOF fatal: index-pack failed

1. 关闭压缩：

```bash
git config --global core.compression 0
```

2. 截断一部分测试能否成功拉取：

```bash
git clone --depth 1 <repo_URI>
```

3. 若成功，则继续拉取剩下的部分

```bash
git fetch --unshallow
```

或

```bash
git fetch --depth=2147483647
```

4. git pull

```bash
git pull --all
```

# 换行符

## warning: LF will be replaced by CRLF

简单粗暴

```bash
git config --global core.autocrlf false
```

下三大主流操作系统的换行符：

Uinx/Linux 采用换行符LF 表示下一行（LF：LineFeed，换行）；

Dos 和Windows 采用CRLF （回车+换行）表示下一行（CRLF：CarriageReturn LineFeed，回车换行）；

Mac OS采用回车CR表示下一行（CR：CarriageReturn，回车）。

通过 `git config [--global] core.autocrlf true | false | input` 命令来设置Git 对待换行符的方式

- true


​		Git会可以在你add（提交）代码时自动地把换行结束符CRLF转换成 LF，而在checkout （签出）代码时把LF转换成CRLF。如果是在Windows系统上，把它设置成true，这样git代码时，LF会被转换成CRLF

- false


​		换行符不做任何改变，文本文件保持其原来的样子。

- input


​		add 时Git会把CRLF转换为LF，而check时仍旧为LF，所以Windows 操作系统不建议设置此值。

可以通过 `git config core.autocrlf` 命令来显示当前Git 中对待换行符的方式

# 个人访问令牌

github21年8月13后不再支持用户名密码的方式验证

不小心用了`git config --global --unset credential.helper` 

现在每个本地仓库都要重新验证一次orz

```
git remote set-url origin https://[githubtoken]@github.com/[username]/[repositoryname].git
```

