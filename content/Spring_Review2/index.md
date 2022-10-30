+++
title = "Spring 装配 Bean"
date = 2022-01-17 11:23:41
slug = "202201171123"

[taxonomies]
tags = ["Spring" ]
categories = ["Spring"]

+++

<!-- more -->

## Spring 装配 Bean

1. 装配机制
   1. 在XML中进行显式配置
   2. 在Java中进行显式配置
   3. 隐式的bean发现机制和自动装配
2. 原则：
   1. 显式配置越少越好，尽量使用自动配置
   2. 推荐使用类型安全并且比XML更加强大的JavaConfig

Spring 框架的核心是 Spring 容器。容器负责管理应用中组件的生命周期，它会创建这些组件并保证它们的依赖能够得到满足，这样组件才能完成预定的任务。

## XML配置

**Bean file**

```java
package com.dashboardapp.beanwire;
public class BeanWireXML {
	private String message;
	   public void setMessage(String message){
	      this.message  = message;
	   }
	   public void getMessage(){
	      System.out.println("Message is : " + message);
	   }
}
```

**XML configuration file**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.2.xsd">
    <bean id="beanwire" class="com.dashboardapp.beanwire.BeanWireXML">
            </bean>
</beans>
```

**Main Java file**

```java
package com.dashboardapp.beanwire;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.FileSystemXmlApplicationContext;
public class BeanWireXMLMain {
	public static void main(String[] args) {
		  ApplicationContext context = new FileSystemXmlApplicationContext(
                "WebContent/WEB-INF/appConfig.xml");	   
	      BeanWireXML beanwireobj = (BeanWireXML)context.getBean("beanwire");
	      beanwireobj.setMessage("Hello Bean. You are wired by XML config.....!");
	      beanwireobj.getMessage();
	   }
}
```



## Java Config配置

**Bean class**

```java
package com.dashboardapp.beanwire;

public class BeanWire {
	private String message;

	   public void setMessage(String message){
	      this.message  = message;
	   }

	   public void getMessage(){
	      System.out.println("Message is : " + message);
	   }

}
```

两个注释用于通知Spring容器将加载BeanWire Bean。

@Configuration注释通知 Spring 容器，此类是 Bean 定义的源。

@Bean注释通知spring框架，该方法将返回一个对象，该对象需要在Spring应用程序上下文中注册为Bean。

**具有注释的 Bean 配置类**

```java
package com.dashboardapp.beanwire;

import org.springframework.context.annotation.*;

@Configuration
public class BeanWireConfig {
	@Bean 
	   public BeanWire beanwire(){
	      return new BeanWire();
	   }

}
```

**Main class**

```java
package com.dashboardapp.beanwire;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.*;
public class BeanWireMain {
	public static void main(String[] args) {
	      ApplicationContext ctx = 
	      new AnnotationConfigApplicationContext(BeanWireConfig.class);	   
	      BeanWire beanwire = ctx.getBean(BeanWire.class);
	      beanwire.setMessage("Hello Bean. You are wired by annotation.....!");
	      beanwire.getMessage();
	   }
}
```



## 自动化装配 bean

Spring 从两个角度来实现自动化装配：

- 组件扫描（component scanning）：Spring 会自动发现应用上下文中所创建的 bean。
- 自动装配（autowiring）：Spring 自动满足 bean 之间的依赖。

### 为组件扫描的Bean命名

1. `@Componet("Bean_Name")`：可以为组件扫描的bean命名
2. `@Named("lonelyHeartsClub")`：Spring支持将@Named作为@Component 注解的替代方案，他们在绝大多数场景中，是可以互相替换的。

### 设置组件扫描的基础包

1. `@ComponentScan("soundsystem")`
2. `@ComponentScan(basePackages="soundsystem)"`
3. `@ComponentScan(basePackages={"soundsystem", "video"})`
4. `@ComponentScan(basePackageClasses={CDPlayer.class, DVDPlayer.class})`：建议这种方式，可以检查出是否有问题

### 为Bean添加实现自动装配

1. 使用`@Autowired`注解完成自动装配
   1. 可以修饰构造器。
   2. 可以修饰Setter方法。
   3. 可以修饰变量声明。
   4. 可选参数：`required=false`:如果没有bean也不会装配。
2. 使用`@Inject`注解：源自于Java依赖注入规范。
3. 在大多数场景下，这两个注解是可以替换的。



## 导入和混合配置

在自动装配时，spring并不在意要装配的 bean 来自哪里。自动装配的时候会考虑到 Spring 容器中所有的 bean，不管它是在 JavaConfig 或 XML 中声明的还是通过组件扫描获取到的。

### Import XML Configuration in Java Config

使用@ImportResource批注，并提供要导入的 XML 配置文件的位置。可以选择指定多个配置文件。@Import注释用于导入额外的Spring Java配置文件。

```java
package com.memorynotfound.spring.core;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.ImportResource;

@Configuration
@Import({ DbConfig.class })
@ImportResource("classpath:app-config.xml")
public class AppConfig {

}
```

下面是位于同一包中的第二个配置文件，并在主配置中被加载。

```java
package com.memorynotfound.spring.core;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.ImportResource;

@Configuration
public class DbConfig {

}
```

app-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">


</beans>
```

在标准的Java SE环境中，使用ConfigApplicationContext引导Spring，并提供Java Configuration类作为参数。

```java
ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
```

在 Web 服务器环境中启动 spring 时，应该在 web-INF 目录中的 web.xml 文件中添加上下文参数:

```xml
<context-param>
    <param-name>contextClass</param-name>
    <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
</context-param>
```

### Loading Java Config in XML

可以在Spring XML配置文件中加载Java配置文件。通过注册 <context：component-scan/> 元素并添加属性 base-package 并指定要自动扫描 Java 配置文件所在的包。第一个<import/>元素导入其他 xml 配置文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context
                           http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- loading additional xml config -->
    <import resource="classpath:db-config.xml"/>
    
    <!-- loading java config files in package -->
    <context:component-scan base-package="com.memorynotfound.spring.core.config" />

</beans>
```

 **db-config.xml** 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

这是在xml配置中加载的java配置文件，它位于com.memorynotfound.spring.core.config包中:

```java
package com.memorynotfound.spring.core.config;

import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

}
```

可以使用 ClassPathXmlApplicationContext 引导 spring 应用程序，并提供 Spring XML 配置文件的位置：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("app-config.xml");
```

在 Web 应用程序中使用 spring 时，可以通过添加以下上下文参数来注册 Spring XML 配置文件:

```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:app-config.xml</param-value>
</context-param>
```



## Resource

[How to wire Spring Beans by using XML and Java classes (eduonix.com)](https://blog.eduonix.com/java-programming-2/learn-how-to-wire-spring-beans-by-using-xml-and-java-classes/)

[Mixing XML and Java Configuration with Spring (memorynotfound.com)](https://memorynotfound.com/mixing-xml-java-config-spring/)
