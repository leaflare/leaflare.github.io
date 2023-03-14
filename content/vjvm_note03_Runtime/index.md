+++
title = "vjvm note 03： Runtime"
date = 2023-02-21 02:47:19
slug = "202302210247"

[taxonomies]
tags = ["vjvm"]
categories =  ["Java"]

+++

<!-- more -->

## Slots与OperandStack 

Slots代表规范中定义的JVM插槽数组。它支持获取和放置原始数据类型，包括地址。使用对象数组Object[]实现。

在局部变量表和操作数栈中，每个位置都是 4 字节，且 `long` 与 `double` 占用连续两个位置。

规定每个 `int` 所占空间为一个槽位（slot），`long` 与 `double` 占用两个槽位， 且使用第一个的下标访问。Slots 的下标从 0 开始。在初始化时，能够确定最多使用的 slots 数量 `slotSize`，它可能来自 `Code` 属性中的 `max_stack` 或 `max_locals`。 对于每一种基本数据类型，都提供了对应的 getter 与 setter 方法（无 get 与 set 前缀），其中 `byte`、`char` 与 `short` 类型应转换成 `int` 存储。Slots 的大小可通过 `size` 方法获得。在进行函数调用时，参数首先从操作数栈的栈顶弹出，然后被复制进被调用方法栈帧的局部变量表中。通过 `copyTo` 与`popSlots` 支持这种操作。

JVM 在操作数栈和局部变量表中并不会保存 char、byte、short 和 bool 类型的值，而是把它们转换成 int。在使用具体类型的 get 与 set 方法时，每个位置 set 的类型必须和 get 的类型相同。

`OperandStack` 负责维护Slots，只需额外维护栈顶的位置，在压栈或出栈时自动更新。

## Code 与 ProgramCounter

为了执行字节码，需要首先将它保存在对应的方法中。Class 文件的字节码在方法的 `Code` 属性里，见*src/main/java/vjvm/runtime/classdata/attribute/Code.java*，在`MethodInfo` 中调用它解析 `Code` 属性。

在 VJVM 中，每个栈帧都有自己独立的 PC，指向所执行方法的字节码，而整个线程使用最顶部栈帧的 PC。`ProgramCounter` 使用一个 `ByteBuffer` 来包装原始字节码，并提供了 `byte_`、`ubyte` 等方法读取数据同时向前移动。这样，在我们读取完一条指令并解码后，PC 自然就指向了下一条指令。

## JThread 与 JFrame

框架代码中使用 `JThread` 代表一个线程，`JFrame` 代表一个线程的单个栈帧。

`VMContext` 中使用 `ArrayList` 保存 `JThread`。

## Instruction 与 JInterpreter

VJVM 运行一个 Java 程序的流程：运行程序的命令通过 `vm.Run` 类实现，其中会首先初始化运行时环境，然后调用 `VMContext.run` 执行程序。在`VMContext` 中，会首先加载用户指定的主类并寻找其中的 `main` 方法。在找到之后将它交给解释器 `JInterpreter` 执行（在这里额外传入了一个 `Slots`，代表 `main` 函数的参数）。

解释器首先利用方法和参数构造一个栈帧，在 `JFrame` 构造方法中将参数压栈，然后进入程序主循环 `run` 方法解释执行指令。

在 VJVM 中，把执行一条指令分为解码与执行两部分。每个指令通过公共的 `Instruction.run` 接口执行，而解码则由 `Decoder.decode` 完成。

使用了一个巨大的表来解码指令。`Decoder.decodeTable` 有 256 个表项，其中的每一项对应指令第一个字节 `opcode`。解码时，首先读取 `opcode`，然后查表调用相应函数来解码指令。

## 方法调用

通过 `JInterpreter.invoke` 进行调用。该方法接受三个参数：需调用的方法、当前线程与参数。它会创建一个新的栈帧，将参数复制到新栈帧的局部变量表中，并将栈帧压到当前线程的栈顶。在完成准备之后，解释器会进入 `run` 执行该方法的代码。虚拟机启动时，会在 `VMContext.run` 中调用 `main` 函数。

框架代码使用 native 方法实现了输入输出功能。在 `testdata` 中的 `IOUtil` 类，提供了打印数据到标准输出和从标准输入读取数据的方法。相应地，解释器中对于这几个方法作了特殊处理：当其中一个方法被调用时，不会解释执行 Java 代码（几个方法也根本没有 `Code` 属性），而是直接在 VM 中实现了输入和输出的功能。native 方法的执行通过 `runNativeMethod` 实现。

事实上，标准的 JVM 中输入输出也是通过类似方式实现的。JVM 本身只能进行计算，并不能读取或写入数据。为了完成 IO 任务，JVM 将 native 方法绑定到对应的 C 函数上，并通过 C 函数与操作系统交互。

## 代码

[ffangli/toyjvm (github.com)](https://github.com/ffangli/toyjvm)

```bash
$ tree src/main/java/vjvm/runtime/frame
```

