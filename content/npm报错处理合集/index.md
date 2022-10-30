+++
title = "npm报错处理合集"
date = 2021-12-15 12:55:12
slug = "202112151255"

[taxonomies]
tags = ["Npm", "Node.js"]
categories = ["Web"]

+++

<!-- more -->

## npm WARN deprecated core-js@2.6.11: core-js@<3 is no longer maintained and not recommended for usage

解决：

```powershell
npm install --save  -g core-js@^3
```

## A complete log of this run can be fund in: C\Users\用户\AppData\Roaming\npm-cache_logs

解决：

删除node_modules文件夹

```
npm cache clean --force
npm install
```

