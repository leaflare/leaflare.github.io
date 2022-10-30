+++
title = "github pages搭建个人博客"
date = 2022-01-08 20:08:34
slug = "202201082008"

[taxonomies]
tags = ["Git"]
categories = ["Git"]

+++

<!-- more -->

## 安装

### Scoop安装

Scoop是适用于 Windows 的命令行安装程序

PowerShell执行：

```
Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
```

或

```
iwr -useb get.scoop.sh | iex
```



### zola安装

```
$ scoop install zola
```

scoop报错解决：[How can I go back to original REPO https://github.com/lukesampson/scoop · Issue #4495 · ScoopInstaller/Scoop · GitHub](https://github.com/ScoopInstaller/Scoop/issues/4495)

## 本地配置

### CLI使用

#### 初始化

本地新建空文件夹

```cpp
$ git init
$ zola init
```

得到目录结构

```
├── config.toml
├── content
├── sass
├── static
├── templates
└── themes
```

#### config.toml:

TOML 格式的必需 Zola 配置文件。

#### content:

包含所有博客内容（主要是 .md 文件）。

content目录示例：

```
content/
  |- _index.md
  `- first/
       |- index.md
       `- image.png
```

md文件示例：

```
+++
title = "标题"
date = 2022-1-8 20:08:21
slug = "202201082008"
[taxonomies]
categories = ["分类"]
tags = ["标签"]
+++

描述

<!-- more -->

博客内容
```

#### sass：

包含要编译的 Sass 文件，非 Sass 文件将被忽略。复制编译文件时，将保留sass文件夹的目录结构;例如，sass/something/site.scss 上的文件将被编译为 public/something/site.css。

#### static：

包含任何类型的文件，主要是图片 / js 等静态资源。静态目录中的所有文件/目录将按原样复制到输出目录。如果静态文件很大，则可以通过在配置文件中设置 hard_link_static = true 来配置 Zola 硬链接。

static下的文件在其他地方以根开头的 URL 访问，比如 static/abc.ico 用 /abc.ico

#### templates：

包含用于呈现网站的所有 Tera 模板。

#### themes：

包含用于网站的主题。如果不打算使用主题，将此目录留空。

附：zola theme列表

<https://www.getzola.org/themes>

### 模板使用

cd 根目录

```
$ git submodule add https://github.com/getzola/after-dark.git themes/after-dark
```

### Github Actions配置

在根目录下创建.github\workflows，新建文件pages.yml

```
# On every push this script is executed
on: push
name: Build and deploy GH Pages
jobs:
  build:
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: checkout
        uses: actions/checkout@v2
      - name: build_and_deploy
        uses: shalzz/zola-deploy-action@v0.14.1
        env:
          # Target branch
          PAGES_BRANCH: gh-pages
          # Provide personal access token
          TOKEN: ${{ secrets.TOKEN }}
```

### build&serve

```
$ zola build
$ zola serve
```

浏览器打开http://127.0.0.1:1111即可看到初始页面



## GitHub Pages配置

推送github后会自动进行pages-build-deployment

生成gh-pages分支

##### 注意要在Settings-Pages中设置Source分支为gh-pages分支



# Bug记录

## 图片显示

图片在本地zola serve能显示，到GitHub显示不了，

但是设置例如`<img src=".\img\0.png" style="zoom:80%;"/>` 的图片尺寸后又可以正常显示了
