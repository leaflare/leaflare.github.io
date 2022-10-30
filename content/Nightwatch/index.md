+++
title = "Nightwatch使用笔记"
date = 2022-01-24 17:45:12
slug = "202201241745"

[taxonomies]
tags = ["Node.js", "Nightwatch"]
categories = ["Web"]

+++

一个自动化e2e测试框架

<!-- more -->

## 简要介绍

Nightwatch 是专门给网页使用的自动化测试框架，它使用 W3C WebDriver API（过去称为 Selenium WebDriver）来自动操作浏览器。 好处是可撰写并自动执行 End-to-End Testing 与 Node.js 的 Unit Testing，并简化设定 CI（Continuous Integration，持续整合）的过程。

WebDriver 是让开发者撰写程序来操作浏览器的远程控制界面，可作为跨浏览器自动化测试的工具。 Nightwatch 即是通过 WebDriver API 来控制浏览器，模拟人类的各种操作行为，例如：浏览特定网址、输入文字、点击按钮等。

### 运作原理

Nightwatch 将我们所撰写的测试程式码转成 HTTP Request 送到 WebDriver Server（也就是 Selenium Server），Selenium Server 再送到浏览器，接着 Selenium Server 回传 HTTP Response 回来给 Nightwatch。 Nightwatch 几乎必须至少送两次 Request 到 Selenium Server 才能完成一个完整的指令（Command）或断言（Assertion）。 第一个 Request 是依照 CSS Selector 或 Xpath 来定位网页元素，第二个指令是对这个网页元素执行指令或断言。

## 环境搭建

### 常规安装配置

官网：[Nightwatch.js | Node.js powered End-to-End testing framework (nightwatchjs.org)](https://nightwatchjs.org/)

```
npm install selenium-standalone --save-dev
npm install nightwatch --save-dev
```

#### 安装ChromeDriver

```
npm install chromedriver --save-dev
```

其他浏览器同理，Microsoft Edge似乎不能直接用npm安装，需要自己去官网下载

#### Chrome浏览器运行

```
npx nightwatch node_modules/nightwatch/examples/tests/ecosia.js --env chrome
```

#### 命令行启动 Nightwatch

1. 指定环境测试

```
npm test
```

在 package.json 设定的 npm test 会执行当前项目下的 Nightwatch Test Runner 并指定环境为 local 以进行测试。

2. 使用全局安装的 Nightwatch Test Runner 进行测试

```
nightwatch
```

示例：[cythilya/nightwatch101: 使用 Nightwatch 實現 End-to-End Testing ★ (github.com)](https://github.com/cythilya/nightwatch101)

### vue-cli安装Nightwatch

在 vue-cli 中选择使用 e2e tests，vue-cli 会自动安装 selenium-server 和 chromedriver 等必要工具

```
vue add @vue/e2e-nightwatch
```

运行 e2e 测试

```
vue-cli-service test:e2e
```

vue-cli 安装的 Nightwatch 会依赖一个 Selenium Server和一个 java 程序

Nightwatch 和浏览器需要通过 Selenium Server 来通信

### 更简洁的方法——直接驱动浏览器

Nightwatch@1.0 可以直接驱动浏览器, 无需 Selenium Server

```
npm install nightwatch@1.0.8
```

#### 简单示例

配置文件 nightwatch.conf.js

```javascript
module.exports = {

  "webdriver": {

    "server_path": require('chromedriver').path, // ChromeDriver 安装地址

    "start_process": true, // 需要启动 driver

    "port": 9515 // driver 启动的端口, 一般是 9515 或 4444

  },

  "test_settings": {

    "default": {

      "desiredCapabilities": {

        "browserName": "chrome" // 指定浏览器名

      }

    }

  }

}
```

测试脚本 e2e.test.js

```javascript
module.exports = {

  'Basic e2e Test' (browser) {

    browser

      .url('http://so.com') // 打开 so.com 网页

      .waitForElementVisible('body') // 确认能看到 body 元素

      .setValue('#input', 'Nightwatch') // 在搜索框输入 Nightwatch

      .click('#search-button') // 点击搜索

      .pause(1000) // 等待1秒钟

      .assert.containsText('#container', 'Nightwatch') // 查询结果包含 Nightwatch

      .end()

  }

}
```

运行

```
node_modules\.bin\nightwatch e2e.test.js
```

![](.\img\sample.png)

测试结果会以XML文件形式输出到tests_output目录下
