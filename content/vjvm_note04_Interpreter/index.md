+++
title = "vjvm note 04： Instruction"
date = 2023-02-24 17:47:19
slug = "202302241747"

[taxonomies]
tags = ["vjvm"]
categories =  ["Java"]

+++

<!-- more -->

## Instruction

从实现上来说，所有的指令都是对操作数栈、局部变量表等数据结构里面的某处数据进行了修改。

每条指令的执行都分为解码与运行两个阶段。解码由指令的构造方法实现（如 `invokestatic`），而运行则由 `run` 方法实现。

除此之外，每条指令有一个 `toString` 方法，被调试器使用。

指令执行时， 首先会读取每条指令的第一个字节，然后查询`Decoder.decodeTable`进行解码。`Decoder.decodeTable`是一个从 opcode 到对应指令解码方法的映射。

解码方法使用了统一的接口：`Instruction (ProgramCounter, MethodInfo)`。第一个参数为指向当前指令的程序计数器，第二个是当前指令所在的类。

## invokestatic指令

实现了两个功能：

- 解析：

  invokestatic编码了一个 unsigned short 指向当前类常量池中一个 CONSTANT_Methodref_info 常量，首先加载该方法所在类，然后从加载的类中找到与 `Methodref` 相符的 `MethodInfo` 并保存至指令中。

- 运行：

  首先从当前方法的操作数栈顶弹出 `argc` 个 slots，即弹出方法调用的参数。然后通过 `Interpreter.invoke` 调用方法。 `MethodDescriptors.argc` 用于计算参数占用的 slots 数量。

  ```
  argc(descriptor /* 方法描述符，如 (I[JLjava/lang/String;)V */):
    argc = 0
    n = 0
    while 该方法有第 n 个参数:
      desc = 第 n 个参数的描述符，在以上例子中分别为 I、[J 与 Ljava/lang/String;
      size = desc 对应类型所占的槽位数。Long 和 Double 为 2，其它为 1
      argc += size
      n += 1
    return argc
  
  INVOKESTATIC(pc /* 当前 PC */, method /* 当前指令所在方法 */):
    cp = 当前类的常量池
    methodRef = cp[从 PC 读取下标]
    thisClass = method 所在的类
    jClass = 使用 thisClass 中的 classLoader 加载 methodRef 指向的类
    this.method = 使用 findMethod 在 jClass 中查找与 methodRef 匹配的方法
  
  run(thread /* 执行时的线程 */):
    stack = 线程当前栈帧的操作数栈
    argc = 使用 MethodDescriptors.argc 计算方法参数占用的 slots 数量
    interpreter = 解释器
    args = 从 stack 顶部弹出 argc 个 slots
    使用 interpreter.invoke 调用方法
  ```



## 代码

[ffangli/toyjvm (github.com)](https://github.com/ffangli/toyjvm)

```bash
$ tree src/main/java/vjvm/interpreter
```

