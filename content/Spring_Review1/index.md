+++
title = "Spring Review"
date = 2021-12-16 21:53:16
slug = "202112162153"

[taxonomies]
tags = [ "Spring" ]
categories = ["Spring"]

+++

<!-- more -->

降低 Java 开发的复杂性，Spring 采取的关键策略

- 基于 POJO 的轻量级和最小侵入性编程；
- 通过依赖注入和面向接口实现松耦合；
- 基于切面和惯例进行声明式编程；
- 通过切面和模板减少样板式代码。



## POJO 和最小侵入性编程

**POJO**

Plain Ordinary Java Object简单的Java对象。

POJO的内在含义是指那些没有从任何类继承、也没有实现任何接口，更没有被其它框架侵入的java对象。POJO让开发者可专注于业务逻辑和脱离框架的单元测试。除此之外， 由于POJO并不须要继承框架的类或实现其接口，开发者能够极其灵活地搭建继承结构和建造应用。

**POJO与JavaBean的区别：**

所有 JavaBeans 都是 POJO，但并非所有 POJO 都是 JavaBeans。

[java - Difference between DTO, VO, POJO, JavaBeans? - Stack Overflow](https://stackoverflow.com/questions/1612334/difference-between-dto-vo-pojo-javabeans)

**非侵入编程模型**

在基于 Spring 构建的应用中，它的类通常没有任何痕迹表明你使用了 Spring。最坏的场景是，一个类或许会使用 Spring 注解，但它依旧是 POJO。Spring 的非侵入编程模型意味着这个类在 Spring 应用和非 Spring 应用中都可以发挥同样的作用。Spring通过DI来装配POJO。



## 依赖注入（DI）

依赖注入（DI）可以保证对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定，而对象无需自行创建或管理它们的依赖关系。依赖注入会将所依赖的关系自动交给目标对象，而不是让对象自己去获取依赖。

### 三种依赖注入基本类型

1. constructor 注入：依赖项通过类构造函数提供。
2. setter 注入：客户端公开注入器用于注入依赖项的 setter 方法。
3. interface 注入：依赖项提供一个注入器方法，该方法将依赖项注入到传递给它的任何客户端中。客户端必须实现一个接口，该接口公开接受依赖关系的 setter 方法。

### 依赖注入的职责

1. 创建对象
2. 了解哪些类需要这些对象
3. 为他们提供所有这些对象

### 使用 DI 的好处

1. 有助于单元测试。
2. 简化了样板代码，因为依赖项的初始化由注入器组件完成。
3. 扩展应用程序变得更加容易。
4. 有助于启用松散耦合，在应用程序编程中非常重要。

### 使用DI的缺点

1. 如果过度使用可能会导致管理问题和其他问题。
2. 许多编译时错误被推送到运行时。
3. 依赖注入框架是通过反射或动态编程实现的。这可能会妨碍 IDE 自动化的使用，例如"查找引用"、"显示调用层次结构"和安全重构。



## Spring依赖注入

 Spring依赖注入的三种方式：constructors, setters or fields

### Constructor-Based Dependency Injection

在基于构造函数的依赖关系注入的情况下，容器将调用一个构造函数，每个构造函数的参数表示我们要设置的依赖项。Spring 主要按类型解析每个参数，后跟属性名称和索引以消除歧义。

#### Java Config

```java
@Configuration
public class AppConfig {

    @Bean
    public Item item1() {
        return new ItemImpl1();
    }

    @Bean
    public Store store() {
        return new Store(item1());
    }
}
```

@Configuration注释指示该类是 Bean 定义的源。使用方法上的@Bean注释来定义 Bean。如果不指定自定义名称，则Bean名称将默认为方法名称。对于具有默认单例作用域的 Bean，Spring 首先检查该 Bean 的缓存实例是否已存在，如果不存在，则仅创建一个新实例。如果使用原型作用域，容器将为每个方法调用返回一个新的 Bean 实例。

#### XML configuration

```xml
<bean id="item1" class="org.baeldung.store.ItemImpl1" /> 
<bean id="store" class="org.baeldung.store.Store"> 
    <constructor-arg type="ItemImpl1" index="0" name="item" ref="item1" /> 
</bean>
```



### Setter-Based Dependency Injection

对于基于 setter 的 DI，容器在调用无参数构造函数或无参数静态工厂方法来实例化 Bean 后，将调用我们类的 setter 方法。

#### Java Config

```java
@Bean
public Store store() {
    Store store = new Store();
    store.setItem(item1());
    return store;
}
```

#### XML configuration

```xml
<bean id="store" class="org.baeldung.store.Store">
    <property name="item" ref="item1" />
</bean>
```



#### 依赖注入方式选择

可以为同一 Bean 组合基于构造函数和基于 setter 的注入类型。Spring 文档建议对强制依赖项使用基于构造函数的注入，对可选依赖项使用基于 setter 的注入。



### Field-Based Dependency Injection

#### Java Config

使用@Autowired注释标记依赖关系来注入依赖项

```java
public class Store {
    @Autowired
    private Item item; 
}
```

构造对象时，如果没有constructor或 setter 方法来注入 Item Bean，则容器将使用反射将 Item 注入到存储中。

此类型不推荐使用XML 配置。



### Autowiring Dependencies

自动依赖装配允许Spring容器通过检查已定义的bean来自动解析协作bean之间的依赖关系。

使用 XML 配置自动依赖装配 Bean 有四种模式：

- **no：** 默认值 - 这意味着没有自动连接用于bean，我们必须显式命名依赖项。
- **byName:**自动依赖装配是根据属性的名称完成的，因此Spring将查找与需要设置的属性同名的Bean。
- **byType:** 类似于 *byName* 自动依赖装配，仅基于属性的类型。这意味着Spring将寻找具有相同类型属性的Bean进行设置。如果有多个该类型的 Bean，则框架会引发异常。
- **constructor:** 自动依赖装配是基于构造函数参数完成的，这意味着Spring将查找与构造函数参数类型相同的bean。

#### 示例

装配item bean到store bean

byType 

```java
@Bean(autowire = Autowire.BY_TYPE)
public class Store {
    
    private Item item;

    public setItem(Item item){
        this.item = item;    
    }
}
```

或使用*@Autowired* 注解：

```java
public class Store {        
    @Autowired    
    private Item item; 
}
```

XML configuration:

```xml
<bean id="store" class="org.store.Store" autowire="byType"> </bean>
```



如果有多个相同类型的 Bean，我们可以使用@Qualifier注释按名称引用 Bean

byName

```java
public class Store {
    @Autowired
    @Qualifier("item1")
    private Item item;
}
```

XML configuration:

```xml
<bean id="item" class="org.baeldung.store.ItemImpl1" />

<bean id="store" class="org.baeldung.store.Store" autowire="byName">
</bean>
```

#### 可以通过显式定义constructor 参数或 setter 依赖项来覆盖自动装配。



### Lazy Initialized Beans

默认情况下，容器在初始化期间创建并配置所有单例 Bean。为了避免这种情况，我们可以在 Bean 配置上使用值为 true 的 lazy-init 属性：

```xml
<bean id="item1" class="org.baeldung.store.ItemImpl1" lazy-init="true" />
```

因此，item1 Bean 将仅在首次请求时初始化，而不是在启动时初始化。这样做的好处是初始化时间更快，但坏处是，在请求Bean之前，不会发现任何配置错误，这可能是在应用程序已经运行后的几个小时甚至几天。



## 控制反转IoC (Inversion of Control)

控制反转是软件工程中的一个原则，它将对象或程序部分的控制权转移到容器或框架中。通常在面向对象编程的上下文中使用它。与传统编程（我们的自定义代码调用库）相比，IoC 使框架能够控制程序流并调用我们的自定义代码。为了实现这一点，框架使用内置了其他行为的抽象。如果想添加自己的行为，需要扩展框架的类或插入自己的类。

### IoC的优点

1. 将任务的执行与其实现分离
2. 使在不同实现之间切换变得更加容易
3. 程序的更大模块化
4. 通过隔离组件或模拟其依赖关系，并允许组件通过协定进行通信，更轻松地测试程序

可以通过各种机制实现控制反转，例如：策略设计模式，服务定位器模式，工厂模式和依赖注入（DI）。

## Spring IoC Container

在Spring框架中，接口ApplicationContext表示IoC容器。Spring 容器负责实例化、配置和组装称为 Bean 的对象，以及管理其生命周期。

Spring 框架提供了 ApplicationContext 接口的几种实现：

ClassPathXmlApplicationContext 和 FileSystemXmlApplicationContext 用于独立应用程序，WebApplicationContext 用于 Web 应用程序。

为了组装 Bean，容器使用配置元数据，这些元数据可以是 XML 配置或注释的形式。

以下是手动实例化容器的一种方法：

```java
ApplicationContext context
  = new ClassPathXmlApplicationContext("applicationContext.xml");
```

要在上面的示例中设置 item 属性，可以使用元数据。然后，容器将读取此元数据，并在运行时使用它来组装 Bean。

## 面向切面编程AOP

DI 能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许把遍布应用各处的功能分离出来形成可重用的组件。

AOP 能够使服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。这些组件会具有更高的内聚性并且会更加关注自身的业务，完全不需要了解涉及系统服务所带来复杂性。AOP 能够确保 POJO 的简单性。

使用AOP有助于将核心应用程序逻辑与常见的可重复任务（输入验证、日志记录、错误处理等）分开。从主代码中剔除后者，并垂直定义它。例如：在运行时，可以使用 AOP 对易受 SQL 注入影响的应用程序进行热修补，或者将入侵检测和审核日志记录功能直接嵌入到应用程序中，而无需修改基础源代码。

AOP code：

```
function mainProgram()
{ 
   var x =  foo();
   doSomethingWith(x);
   return x;
}

aspect logging
{ 
    before (mainProgram is called):
    { 
       log.Write("entering mainProgram");
    }

    after (mainProgram is called):
    { 
       log.Write(  "exiting mainProgram with return value of "
                  + mainProgram.returnValue);
    }
 } 

aspect verification
{ 
    before (doSomethingWith is called):
    { 
       if (doSomethingWith.arguments[0] == null) 
       { 
          throw NullArgumentException();
       }

       if (!doSomethingWith.caller.isAuthenticated)
       { 
          throw Securityexception();
       }
    }
 }
```

aspect-weaver code：

```
function mainProgram()
{ 
   log.Write("entering mainProgram");

   var x = foo();   

   if (x == null) throw NullArgumentException();
   if (!mainProgramIsAuthenticated()) throw Securityexception();
   doSomethingWith(x);   

   log.Write("exiting mainProgram with return value of "+ x);
   return x;
} 
```

## 使用模板消除样式代码

1. JDBC、JMS、JNDI、REST等等都存在有大量的样式代码
2. 例如：JDBC的模板：jdbcTemplate

## Bean

1. 在基于Spring的应用中，应用对象生存于Spring容器(container)中，Spring容器负责创建对象并装配，配置它们并管理它们的整个生活周期，从new到finalize()
2. Spring容器不同类型
   1. bean工厂(org.springframework.beans.factory.BeanFactory)提供DI的支持
   2. 应用上下文(org.springframework.context.ApplicationContext)基于BeanFactory构建，并提供应用框架级别的服务。

## 应用上下文

常见的Spring上下文：区别在于如何装载配置

**AnnotationConfigApplicationContext：**从一个或多个基于 Java 的配置类中加载 Spring 应用上下文。

**AnnotationConfigWebApplicationContext：**从一个或多个基于 Java 的配置类中加载 Spring Web 应用上下文。

**ClassPathXmlApplicationContext：**从类路径下的一个或多个 XML 配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。

**FileSystemXmlapplicationcontext：**从文件系统下的一 个或多个 XML 配置文件中加载上下文定义。

**XmlWebApplicationContext：**从 Web 应用下的一个或多个 XML 配置文件中加载上下文定义。



上下文准备就绪后就可以调用上下文的getBean()从容器中获取Bean



## Bean的生命周期

![](./spring_bean_life_cycle.jpg)

1. Spring对bean进行**实例化**
2. Spring将值和bean的引用注入到bean对应的属性中
3. 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBeanName()方法
4. 如果bean实现了BeanFactoryAware接口，Spring将调用 setBeanFactory()方法，将BeanFactory容器实例传入
5. 如果bean实现了ApplicationContextAware接口，Spring 将调用setApplicationContext()方法，将bean所在的应用上下文的引用传入进来；
6. 如果bean实现了BeanPostProcessor接口，Spring 将调用它们的postProcessBefore-Initialization()方法；
7. 如果bean实现了InitializingBean接口，Spring 将调用它们的afterPropertiesSet()方法。类似地，如果bean使用 initmethod声明了初始化方法，该方法也会被调用；
8. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的postProcessAfter-Initialization()方法；
9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
10. 如果bean实现了DisposableBean接口，Spring 将调用它的destroy()接口方法：如果destroy-method声明了销毁方法，则该方法也会被调用。





## Book&Resource

<[Spring 实战（第 4 版） - Spring 实战(第四版) (gitbook.io)](https://potoyang.gitbook.io/spring-in-action-v4/)>

[Spring in Action, Fourth Edition (manning.com)](https://www.manning.com/books/spring-in-action-fourth-edition)

[A quick intro to Dependency Injection: what it is, and when to use it (freecodecamp.org)](https://www.freecodecamp.org/news/a-quick-intro-to-dependency-injection-what-it-is-and-when-to-use-it-7578c84fa88f/)

[Spring Dependency Injection with Example - GeeksforGeeks](https://www.geeksforgeeks.org/spring-dependency-injection-with-example/)

[Inversion of Control and Dependency Injection with Spring | Baeldung](https://www.baeldung.com/inversion-control-and-dependency-injection-in-spring)

[aop - What is aspect-oriented programming? - Stack Overflow](https://stackoverflow.com/questions/242177/what-is-aspect-oriented-programming)
