+++
title = "vjvm note 02： Parser"
date = 2023-02-15 22:47:19
slug = "202302152247"

[taxonomies]
tags = ["vjvm"]
categories =  ["Java"]

+++

<!-- more -->

## class 文件结构

```c
ClassFile {
  u4              magic;
  u2              minor_version;
  u2              major_version;
  u2              constant_pool_count;
  cp_info         constant_pool[constant_pool_count - 1]; // 常量池
  u2              access_flags;
  u2              this_class;
  u2              super_class;
  u2              interfaces_count;
  u2              interfaces[interfaces_count]; // 类实现的接口
  u2              fields_count;
  field_info      fields[fields_count]; // 类的成员变量
  u2              methods_count;
  method_info     methods[methods_count]; // 类的方法
  u2              attributes_count;
  attribute_info  attributes[attributes_count];
};
```

每一行的前一列是类型，后一列是名称。例如，`u4 magic` 代表 `magic` 为 32 位无符号整形。在一个 class 文件中，最重要的四个结构是常量池（Constant Pool）、 成员变量（Field）、方法（Method）和属性（Attribute）。

- 常量池（Constant Pool）这里的 `info` 成员的长度是可变的。

```c
cp_info {
  u1  tag; // 常量类型
  u1  info[]; // 常量数据
};
```

比如，对于 `CONSTANT_Class_info`：

```
struct CONSTANT_Class_info {
  u1  tag;
  u2  name_index;
};
```

这里的 `info` 就被替换成了两个字节的索引，指向常量池中代表这个类名称的项。

- 成员变量（Field） 其中的 `name_index` 和 `descriptor_index` 都指向常量池的内容。

```c
field_info {
 u2 access_flags;
 u2 name_index;
 u2 descriptor_index;
 u2 attributes_count;
 attribute_info attributes[attributes_count];
}
```

- 方法（Method）

```c
method_info {
 u2 access_flags;
 u2 name_index;
 u2 descriptor_index;
 u2 attributes_count;
 attribute_info attributes[attributes_count];
}
```

- 属性（Attribute）属性在类、成员变量和方法的结构中出现，一个属性的通用结构如下：

```
attribute_info {
 u2 attribute_name_index;
 u4 attribute_length;
 u1 info[attribute_length];
}
```

属性中保存了许多内容，如 `static final` 变量的值、方法的代码、异常处理所需的信息等。

## 惰性求值和及早求值

惰性求值（Lazy Evaluation）在需要时才进行计算

及早求值  (Eager Evaluation) 在构造时就把全部值计算出来

## 代码

[ffangli/toyjvm (github.com)](https://github.com/ffangli/toyjvm)

```bash
$ tree src/main/java/vjvm/classfiledefs
$ tree src/main/java/vjvm/runtime/classdata
```

## 参考文献

[The Java® Virtual Machine Specification (oracle.com)](https://docs.oracle.com/javase/specs/jvms/se8/jvms8.pdf)
