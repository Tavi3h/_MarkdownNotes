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

**3. 选择Starter依赖**

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


STS自动为我们加入了Spring Boot的父级依赖和Starter依赖：

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

这个`main()`方法将启动SpringBoot程序，启动Spring容器和内嵌的Tomcat，这里内嵌的Tomcat版本为8.5。

如上是使用STS创建Spring Boot工程的方法，如果我们选择使用Maven/Gradle自己搭建工程也是可以的，只是我们要自己完成添加相关依赖、目录和文件的工作。

**定义一个Controller**

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

