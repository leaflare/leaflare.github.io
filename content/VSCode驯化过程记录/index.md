+++
title = "VSCode驯化过程记录"
date = 2022-01-23 22:57:09
slug = "202201232257"

[taxonomies]
tags = ["IDE" ]
categories = ["IDE"]

+++

换电脑，获得一只野生的VSCode

<!-- more -->

## 自动保存

**【Ctrl】+【，】**，打开常用设置，第一行就是`File:Auto Save`

想知道有多少受害者（X

懒人选择`afterDelay`，上方搜索框直接搜`auto save delay`设置延迟时间



## C++/MinGW

确保环境变量配置正确：

```bash
g++ --version
gdb --version
```

打开VS Code，安装插件C/C++

然后**【Ctrl】+【Shift】+【P】**，打开命令面板，输入关键词"C/C++"

选中**编辑配置 (UI)**

设置**编译器路径**和**IntelliSense模式**

编写C程序选择`gcc.exe`，C++选择`g++.exe`

IntelliSense模式选择`gcc-x64`。（windows默认为windows-msvc-x64）

### 编译运行

第一次执行编译任务前，需要先进行配置

选择菜单栏【终端】→【配置任务…】

在弹出的待选项中按需求选择gcc或g++

之后会自动创建一个`tasks.json`文件，放在`.vscode`中

同目录下还有一个`launch.json`文件需要配置

#### tasks.json

```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe build active file",
            "command": "C:/msys64/mingw64/bin/g++.exe",
            "args": [
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": "build",
            "detail": "compiler: C:/msys64/mingw64/bin/g++.exe"
        }
}
```

- command和detail可以不用绝对路径

#### launch.json

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "g++.exe - Build and debug active file",
      "type": "cppdbg",
      "request": "launch",
      "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${fileDirname}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "C:\\msys64\\mingw64\\bin\\gdb.exe",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ],
      "preLaunchTask": "C/C++: g++.exe build active file"
    }
  ]
}
```

- miDebuggerPath改成本地绝对路径
- preLaunchTask和tasks.json的label对应

打开要编译的代码文件，选择菜单栏【终端】→【运行生成任务...】

或者 **【Ctrl】+【Shift】+【B】**

在弹出的待选项中选择之前生成的任务

运气好的话目录下已经有.exe文件了

好麻烦，还不如直接命令行 `g++ xxx.cpp`（X

但是有个坑：需要用`.\`指明它是当前目录下的可执行文件，因为VS Code的默认终端实际上是PowerShell。



## Java单文件

#### launch.json

```
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "java",
            "name": "Debug (Launch) - Current File",
            "request": "launch",
            "mainClass": "${file}"
        },
        {
            "type": "java",
            "name": "Debug (Launch)-Hello<java_single_file_9fc652ff>",
            "request": "launch",
            "mainClass": "HelloWorld"
        }
    ]
}
```

配置了两个 launch，一个是以当前打开的 Java 源文件作为主类调试，一个是以 HelloWorld.java 作为主类进行调试

部分插件不再支持Java8，需要手动插件降低版本

懒人（x 命令行编译：

【Ctrl】+【Shift】+【`】打开终端，cd到文件目录

javac xxx.java

java xxx

解决中文编码报错`错误: 编码GBK的不可映射字符`

方法1. 编译的时候使用带参数的编译指令：javac -encoding utf-8 xxx.java

方法2. 单文件改配置

在vscode左下角找到编码utf-8的地方，并点击utf-8

→选择save with encoding

→选择GBK

方法3. 全局改配置

【Ctrl】+【Shift】+【P】→ settings → encoding

### 踩坑

在vscode内运行java出现`javac : 无法将“javac”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。`

但是在cmd和powershell里检查java和javac都是正常的

事故原因：配置java环境变量时，配置了classpath，导致无法在当前目录下找到主类，而jdk6以后其实就不用再配置classpath了

解决办法：删除classpath，再重启vscode



## Python in WSL

命令行创建并激活虚拟环境

```
python3 -m venv .venv
source .venv/bin/activate
```

打开VSCode

```
code .
```

