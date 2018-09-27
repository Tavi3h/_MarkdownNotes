[TOC]

# Spring Boot

## Spring Boot简介

Spring Boot是Spring家族中的一个全新的框架，它通用来简化Spring应用程序的创建和开发过程。在以往我们采用SSH/SSM框架进行开发的时候，搭建和整合三大框架需要做很多工作。例如，配置web.xml，配置Spring等等，并将它们整合在一起，而Spring Boot框架对此开发过程进行了颠覆，抛弃了繁琐的XML配置过程，采用大量的默认配置简化我们的开发。

### Spring Boot的特性

- 能够快速创建基于Spring的应用程序
- 能够直接使用Java main方法启动内嵌的Tomcat、Jetty服务器运行Spring Boot程序，不需要部署war文件
- 提供约定的starter POM来简化Maven配置
- 根据项目的Maven依赖配置，Spring Boot自动配置Spring、Spring MVC等
- 提供了程序的健康检查功能
- 基本可以完全不适用XML配置文件，采用注解配置

### Spring Boot的核心

- 自动配置：针对很多Spring应用程序和常见应用功能，Spring Boot能自动提供相关配置
- 起步依赖：告诉Spring Boot需要什么功能，它就能引入需要的依赖库
- Actuator：让我们能够深入运行中的Spring Boot应用程序，查看Spring Boot程序的内部信息
- CLI：Spring Boot的可选特性，主要针对Groovy语言使用

### Spring Boot开发环境

- Spring Boot 2.0.5
- Spring Framework 5.0.9
- JDK 8
- Maven 3.5.3
- Eclipse （with Spring Tool Suite，STS）

## 开发Spring Boot程序

### 创建一个Spring Boot项目

**使用STS插件创建**

步骤如下：

**1. 创建工程**

<center>
    ![创建SpringBoot工程-使用STS插件-01](images\创建SpringBoot工程-使用STS插件-01.PNG)
</center>

**2. 填写工程信息**

<center>
    ![创建SpringBoot工程-使用STS插件-02](images\创建SpringBoot工程-使用STS插件-02.PNG)
</center>

**3. 选择starter依赖**

<center>
    ![创建SpringBoot工程-使用STS插件-03](images\创建SpringBoot工程-使用STS插件-03.PNG)
</center>

**4. 确认Site Info**

<center>
    ![创建SpringBoot工程-使用STS插件-04](images\创建SpringBoot工程-使用STS插件-04.PNG)
</center>

**5. 完成创建**

<center>
    ![创建SpringBoot工程-使用STS插件-05](images\创建SpringBoot工程-使用STS插件-05.PNG)
</center>

STS自动为我们加入了Spring Boot的父级依赖和starter依赖：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.5.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

STS也为我们自动创建Spring Boot的入口`main()`方法：

```java
package pers.tavish.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

如上是使用STS创建Spring Boot工程的方法，如果我们选择使用Maven/Gradle自己搭建工程也是可以的，只是我们要自己完成添加相关依赖、目录和文件的工作。

### 定义一个Controller

```java
package pers.tavish.springboot.controllers;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
@RequestMapping("/boot")
public class HelloController {
    
    @RequestMapping("/hello")
    @ResponseBody
    public String hello() {
        return "Hello Spring Boot";
    }
}
```

最后我们运行之前的`main()`方法，就可以通过“http://localhost:8080/boot/hello ”访问这个Controller的`hello()`方法了。

### 程序解析

**父级依赖**

配置了Spring的父级依赖spring-boot-starter-parent之后，当前项目就是一个Spring Boot项目。

**starter依赖**

starter依赖是一个特殊的依赖，它用来提供相关的Maven默认依赖。

**默认依赖**

要查询Spring Boot提供了哪些默认依赖，可查看父级依赖和starter依赖的pom文件。

例如spring-boot-starter-web提供了如下依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter</artifactId>
        <version>2.0.5.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-json</artifactId>
        <version>2.0.5.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        <version>2.0.5.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.hibernate.validator</groupId>
        <artifactId>hibernate-validator</artifactId>
        <version>6.0.12.Final</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>5.0.9.RELEASE</version>
        <scope>compile</scope>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.0.9.RELEASE</version>
        <scope>compile</scope>
    </dependency>
</dependencies>
```

如果我们不想使用默认依赖的某个版本，我们可以通过pom.xml文件的属性配置进行覆盖，例如当前我们的Spring Framework版本为5.0.9，下述代码会将其改为5.0.0版本：

```xml
<properties>
    <spring.version>5.0.0.RELEASE</spring.version>
</properties>
```

**@SpringBootApplication注解**

这个注解是Spring Boot项目的核心注解，它可以开启Spring自动配置，并设置组件扫描：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
    // ...
}
```

注意其中的这个`@ComponentScan`注解，这个注解隐式地要求了我们的`Application`类和其它组件（Service、Controller、Repository...）所在包的关系。即为了使组件扫描可以扫描到我们定义的组件，`Application`类必须定义在组件所在包的上一级或同级，因为组件扫描默认扫描当前包即其子包。

**main()方法**

这个`main()`方法将启动SpringBoot程序，启动Spring容器和内嵌的Tomcat，这里内嵌的Tomcat版本为8.5。

**其他组件**

这里我们的组件只有Spring MVC的Controller。与单纯的Spring MVC相同，这个组件在Spring Boot中为我们提供MVC支持。

## Spring Boot的核心配置文件

Spring Boot可以使用两种格式的文件进行配置：

- .properties
- .yml

无论使用哪种格式，文件的名字必须为application。

### 使用properties进行配置

当STS为我们创建工程时，自动地在src/main/resources下创建了application.properties。

例如我们修改内嵌的Tomcat的端口号，以及配置一个应用上下文：

```text
server.port=8081
server.servlet.context-path=/01-springboot-web
```

经过修改，启动应用后，此时要访问`hello()`方法就需要访问“http://localhost:8081/01-springboot-web/boot/hello” 这个URL了。

### 使用yml进行配置

.yml/.yaml是一种YAML格式的配置文件。YAML是一种直观的能够被计算机识别的数据序列化格式，且容易被人类阅读。

YAML使用空格和换行等格式排版进行配置。示例如下：

```yaml
server:
    port: 9090
    servlet:
        context-path: /01-springboot-web
```

此时要访问`hello()`方法就需要访问“http://localhost:9090/01-springboot-web/boot/hello” 这个URL了。

当项目中同时存在application.propeties和application.yml时，由于application.propeties会在application.yml加载之后加载，所以application.propeties的配置会覆盖application.yml中的配置。

