+++
title = "List<? extends Object>和List<? super Object>区别"
date = 2022-11-05 18:43:14
slug = "202211051843"

[taxonomies]
tags = [ "Java" ]
categories = ["Java"]

+++

<!-- more -->

## 关键字

搜了一下关键字的介绍是：

- ? 通配符类型
- <? extends T> 表示类型的上界，表示参数化类型的可能是T 或是 T的子类，extends 可用于返回类型限定，不能用于参数类型限定，带有extends子类型限定的通配符可以向泛型对象读取。
- <? super T> 表示类型下界（Java Core中叫超类型限定），表示参数化类型是此类型的超类型（或者叫父类型），直至Object，super 可用于参数类型限定，不能用于返回类型限定，带有super超类型限定的通配符可以向泛型对易用写入。

看完还是很迷茫，去搜了几个例子

## 示例

1. ### **List<? extends Object>：**

   - 列表元素的类型是`Object`或者`Object`的子类型。
   - 可以从中读取元素，但是由于类型的不确定性，不能往里面写入任何东西。因为无法确定列表中实际存储的是哪种类型，写操作可能导致类型不匹配的错误。也就是由于具体的类型是不确定的，因此可以使用 `get()` 方法进行读取操作，但是不能使用 `add()` 方法添加元素，除非是 `null` 值。

   例子：

   ```java
   List<? extends Object> list1 = new ArrayList<String>(); // 允许存储String或String的子类型 
   Object obj = list1.get(0); // 读取是安全的 
   list1.add("Hello"); // 编译错误，不允许写入具体的类型
   ```

2. ### **List<? super Object>：**

   - 列表元素的类型是`Object`或者`Object`的父类型。
   - 可以往里面写入`Object`类型或者`Object`的任何子类型，但是由于读取时无法确定实际存储的是什么类型，读取时只能将元素视为`Object`。可以使用 `add()` 方法添加 `Object` 或者 `Obeject` 的子类型的元素。然而，由于无法确定列表中实际存储的是哪种类型，因此在使用 `get()` 方法时得到的类型是不确定的，所以不能直接进行读取操作。

   例子：

   ```java
   List<? super Object> list2 = new ArrayList<Object>(); // 允许存储Object或Object的父类型
   list2.add("Hello"); // 允许添加String类型
   list2.add(42); // 允许添加Integer类型
   Object obj2 = list2.get(0); // 读取时只能将元素视为Object
   ```

### 完整示例

#### extends 示例

```scala
static class Food{}
static class Fruit extends Food{}
static class Apple extends Fruit{}
static class RedApple extends Apple{}

List<? extends Fruit> flist = new ArrayList<Apple>();
// complie error:
// flist.add(new Apple());
// flist.add(new Fruit());
// flist.add(new Object());
flist.add(null); // only work for null 
```

List<? extends Fruit> 表示 “具有任何从Fruit继承类型的列表”，编译器无法确定List所持有的类型，所以无法安全的向其中添加对象。可以添加null,因为null 可以表示任何类型。所以List 的add 方法不能添加任何有意义的元素，但是可以接受现有的子类型List<Apple> 赋值。

```csharp
Fruit fruit = flist.get(0);
Apple apple = (Apple)flist.get(0);
```

由于，其中放置是从Fruit中继承的类型，所以可以安全地取出Fruit类型。

```cpp
flist.contains(new Fruit());
flist.contains(new Apple());
```

在使用Collection中的contains 方法时，接受Object 参数类型，可以不涉及任何通配符，编译器也允许这么调用。

#### super 示例

```csharp
List<? super Fruit> flist = new ArrayList<Fruit>();
flist.add(new Fruit());
flist.add(new Apple());
flist.add(new RedApple());

// compile error:
List<? super Fruit> flist = new ArrayList<Apple>();
```

List<? super Fruit> 表示“具有任何Fruit超类型的列表”，列表的类型至少是一个 Fruit 类型，因此可以安全的向其中添加Fruit 及其子类型。由于List<? super Fruit>中的类型可能是任何Fruit 的超类型，无法赋值为Fruit的子类型Apple的List<Apple>。

```csharp
// compile error:
Fruit item = flist.get(0);
```

因为，List<? super Fruit>中的类型可能是任何Fruit 的超类型，所以编译器无法确定get返回的对象类型是Fruit,还是Fruit的父类Food 或 Object。

## 总结

1. PECS原则`List<? extends Object>`通常用于表示“只读”的情况（T{T类型或者T的子类型}类型不确定，写入的时候编译器不知道具体的类型所以会报编译器错误）{上届}，而`List<? super Object>`用于表示“可写”的情况（T{T父类型直至Object}，读出来的是Object）{下届}。
2. extends限定类型安全时返回值用， super限定类型安全时方法参数用。
3. 如果要从集合中读取类型T的数据，并且不能写入，可以使用 ? extends 通配符；(Producer Extends)
   如果要从集合中写入类型T的数据，并且不需要读取，可以使用 ? super 通配符；(Consumer Super)
   如果既要存又要取，那么就不要使用任何通配符。
4. 泛型是为了类型安全。
