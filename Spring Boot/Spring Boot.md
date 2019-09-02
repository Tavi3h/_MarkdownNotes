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

.yml/.yaml是一种YAML格式的配置文件。YAML是一种直观的能够被计算机识别的数据序列化格式，且容易被人类阅读。这个格式也是Spring推荐我们使用的格式。

YAML使用空格和换行等格式排版进行配置。示例如下：

```yaml
server:
  port: 9090
  servlet:
    context-path: /01-springboot-web
```

此时要访问`hello()`方法就需要访问“http://localhost:9090/01-springboot-web/boot/hello” 这个URL了。

当项目中同时存在application.propeties和application.yml时，由于application.propeties会在application.yml加载之后加载，所以application.propeties的配置会覆盖application.yml中的配置。

### 多环境配置文件

在实际开发中，我们可能会对应用程序进行不同的配置，例如有开发环境、生产环境。这时我们就需要定义不同的配置文件。

例如，我们有如下开发环境的配置文件和生产环境的配置文件：

- application-dev.yml
- application-product.yml

其中application-dev是开发环境下应用的配置，配置如下：

```yaml
server:
  port: 8081
  servlet:
    context-path: /01-springboot-web-dev 
```

application-prod是生产环境下应用的配置，配置如下：

```yaml
server:
  port: 9090
  servlet:
    context-path: /01-springboot-web-prod
```

现在，根据不同的场景，我们需要在主配置文件application.yml中启用配置，例如启用开发环境的配置：

```yaml
spring:
  profiles:
    active:
    - dev
```

则此时我们的应用会运行在8081端口上，且应用上下文为“/01-springboot-web-dev”。

**如果我们的主配置文件的配置与我们所激活的配置文件的配置存在冲突，那么主配置文件的配置无效。**

### 自定义配置的读取

我们可以在配置文件中自定义一些配置，并通过程序进行读取。例如，我们在主配置文件中定义如下配置：

```yaml
application: 
  name: SpringBoot-Project
  author: Tavish
```

要在程序中读取我们自定义的配置，可以使用两种方式。

#### 使用@Value注解

例如：

```java
@Controller
public class AppInfoController {

    @Value("${application.name}")
    private String name;

    @Value("${application.author}")
    private String author;

    @ResponseBody
    @RequestMapping("/appInfo")
    public String getInfo() {
        return "AppInfo [name=" + name + ", author=" + author + "]";
    }
}
```

这里使用了`@Value`注解，这个注解可以读取properties和YAML文件中的配置，然后对成员变量进行注入。虽然我们的配置文件是使用YAML格式的，但读取时采用“foo.bar”的形式进行读取。

#### 使用@ConfigurationProperties注解

这个注解用于将整个文件映射成一个对象，例如：

```java
@Component
@ConfigurationProperties("application")
public class ConfigInfo {
    
    private String name;
    private String author;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAuthor() {
        return author;
    }

    public void setAuthor(String author) {
        this.author = author;
    }
}
```

此时注解`@ConfigurationProperties`会加载属性文件中前缀为“application”的属性，并自动装配到两个对应的成员变量中。这里提供了Setter/Getter方法，我们会用Getter方法在其它位置使用装配进来的属性。

那么，Setter方法有什么作用？Spring Boot使用这些Setter方法来为成员变量注入属性值，如果这里我们不提供Setter方法，那么启动应用时会报错，例如：

```text
***************************
APPLICATION FAILED TO START
***************************

Description:

Failed to bind properties under 'application' to pers.tavish.springboot.config.ConfigInfo:

    Property: application.name
    Value: SpringBoot-Project
    Origin: class path resource [application.yml]:9:9
    Reason: No setter found for property: name

Action:

Update your application's configuration
```

接下来我们就可以在其它类中使用我们的配置类了，例如修改前述的`AppInfoController`，使其使用`ConfigInfo`来获取属性：

```java
@Controller
public class AppInfoController {
    
    @Autowired
    private ConfigInfo info;

    @Value("${application.name}")
    private String name;

    @Value("${application.author}")
    private String author;
    
    @ResponseBody
    @RequestMapping("/appInfo")
    public String getInfo() {
        return "AppInfo [name=" + info.getName() + ", author=" + info.getAuthor() + "] by @Value" + "<br>" + "AppInfo [name=" + name + ", author=" + author + "] by @ConfigurationProperties";
    }
}
```

因为我们在配置`ConfigInfo`时使用了注解`@Component`注解，所以组件扫描器会扫描到这个类并将其注入进来。

启动程序访问“http://localhost:8081/01-springboot-web-dev/appInfo” 会输出如下信息：

```text
AppInfo [name=SpringBoot-Project, author=Tavish] by @ConfigurationProperties
AppInfo [name=SpringBoot-Project, author=Tavish] by @Value
```

## Spring Boot集成

### Spring Boot中的Spring MVC

在Spring Boot中使用Spring MVC与传统的Spring MVC项目没有什么不同。如果说确实有不同的话，那就是Spring Boot为我们提供了许多的默认配置。例如上述程序启动时会输出以下信息：

```text
2018-09-28 20:36:12.153  INFO 1768 --- [           main] pers.tavish.springboot.Application       : The following profiles are active: dev
2018-09-28 20:36:12.187  INFO 1768 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@7334aada: startup date [Fri Sep 28 20:36:12 CST 2018]; root of context hierarchy
2018-09-28 20:36:13.211  INFO 1768 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 8081 (http)
2018-09-28 20:36:13.229  INFO 1768 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2018-09-28 20:36:13.229  INFO 1768 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.34
2018-09-28 20:36:13.235  INFO 1768 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [C:\Program Files\Java\jre1.8.0_161\bin;C:\WINDOWS\Sun\Java\bin;C:\WINDOWS\system32;C:\WINDOWS;C:/Program Files/Java/jre1.8.0_161/bin/server;C:/Program Files/Java/jre1.8.0_161/bin;C:/Program Files/Java/jre1.8.0_161/lib/amd64;C:\ProgramData\Oracle\Java\javapath;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Program Files\Java\jdk1.8.0_152\bin;C:\Program Files\Java\jdk1.8.0_152\jre\bin;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\Program Files\MySQL\MySQL Server 5.7\bin;C:\WINDOWS\System32\OpenSSH\;D:\Program Files\Git\cmd;D:\Program Files\apache-maven-3.5.3\bin;D:\Program Files\gradle-4.9\bin;C:\Program Files\MongoDB\Server\4.0\bin;D:\Program Files\neo4j-community-3.4.7\bin;D:\Program Files\Gaussian\G03W;C:\Program Files (x86)\NVIDIA Corporation\PhysX\Common;D:\Program Files\spring-1.5.16.RELEASE\bin;C:\Users\Tavish\AppData\Local\Microsoft\WindowsApps;;C:\WINDOWS\system32;;.]
2018-09-28 20:36:13.343  INFO 1768 --- [ost-startStop-1] o.a.c.c.C.[.[.[/01-springboot-web-dev]   : Initializing Spring embedded WebApplicationContext
2018-09-28 20:36:13.344  INFO 1768 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1159 ms
2018-09-28 20:36:13.413  INFO 1768 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
2018-09-28 20:36:13.416  INFO 1768 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2018-09-28 20:36:13.416  INFO 1768 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2018-09-28 20:36:13.416  INFO 1768 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2018-09-28 20:36:13.416  INFO 1768 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2018-09-28 20:36:13.502  INFO 1768 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-09-28 20:36:13.610  INFO 1768 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@7334aada: startup date [Fri Sep 28 20:36:12 CST 2018]; root of context hierarchy
2018-09-28 20:36:13.660  INFO 1768 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/appInfo]}" onto public java.lang.String pers.tavish.springboot.controllers.AppInfoController.getInfo()
2018-09-28 20:36:13.661  INFO 1768 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/boot/hello]}" onto public java.lang.String pers.tavish.springboot.controllers.HelloController.hello()
2018-09-28 20:36:13.663  INFO 1768 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2018-09-28 20:36:13.664  INFO 1768 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2018-09-28 20:36:13.679  INFO 1768 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-09-28 20:36:13.679  INFO 1768 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-09-28 20:36:13.766  INFO 1768 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-09-28 20:36:13.802  INFO 1768 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8081 (http) with context path '/01-springboot-web-dev'
2018-09-28 20:36:13.804  INFO 1768 --- [           main] pers.tavish.springboot.Application       : Started Application in 1.916 seconds (JVM running for 2.28)
2018-09-28 20:36:16.587  INFO 1768 --- [nio-8081-exec-1] o.a.c.c.C.[.[.[/01-springboot-web-dev]   : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-09-28 20:36:16.587  INFO 1768 --- [nio-8081-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization started
2018-09-28 20:36:16.606  INFO 1768 --- [nio-8081-exec-1] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization completed in 19 ms
```

可以看到Spring Boot为我们自动进行了如下配置：

```text
- Servlet dispatcherServlet mapped to [/]
- Mapping filter: 'characterEncodingFilter' to: [/*]
- Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
- Mapping filter: 'httpPutFormContentFilter' to: [/*]
- Mapping filter: 'requestContextFilter' to: [/*]
- Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
- Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
- Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
- Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
- Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
- Registering beans for JMX exposure on startup
```

### Spring Boot使用JSP

在Spring Boot中使用jsp，需要引入如下几个依赖：

```xml
    <!-- 内嵌Tomcat对JSP的解析包 -->
    <dependency>
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-jasper</artifactId>
    </dependency>
    
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <scope>provided</scope>
    </dependency>
    
    <!-- JSP-API -->
    <dependency>
        <groupId>javax.servlet.jsp</groupId>
        <artifactId>javax.servlet.jsp-api</artifactId>
        <version>2.3.3</version>
        <scope>provided</scope>
    </dependency>
```

如果要使用JSTL，我们需要JSTL的依赖：

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
```

这里servlet-api和jstl没有指定版本是因为我们继承的父工程中定义了版本：

```xml
<servlet-api.version>3.1.0</servlet-api.version>
<jstl.version>1.2</jstl.version>
```

所以如果我们确实要使用这个版本的servlet-api和jstl，那么就无需再指定版本。

接下来我们需要指定我们JSP视图的前缀与后缀：

```yaml
spring: 
  mvc:
    view:
      prefix: /jsp/
      suffix: .jsp
```

配置完毕后，在src/main下创建一个webapp目录，在该目录下创建jsp目录，此目录用于存放JSP文件。

index.jsp如下：

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Index JSP</title>
</head>
<body>
    ${msg}
</body>
</html>
```

Controller如下：

```java
package pers.tavish.springboot.controllers;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class JspController {
    
    @GetMapping("/boot/index")
    public String index(Model model) {
        model.addAttribute("msg", "JSP!");
        return "index";
    }
}
```

程序启动后，访问对应端口，对应应用上下文下的“/boot/index”就会跳转到index.jsp并显示“JSP！”。

### Spring Boot集成MyBatis

这里数据库使用MySQL。

Spring Boot集成MyBatis需要添加如下依赖：

```xml
<!-- mybatis-starter依赖 -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>1.3.2</version>
</dependency>

<!-- MySQL驱动 -->
<!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

以下在整合时不使用任何的XML文件，MyBatis的相关配置写在application.yml中：

配置数据库连接四要素：

```yaml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&useSSL=true
    username: root
    password: mysql
```

这里无需配置数据源，Spring Boot 2.0.x版本使用的默认数据源是HikariCP，这个数据源具有非常好的性能。

这里没有对Mybatis进行显式配置，如果需要配置，那么所有配置在“mybatis :”，例如：

```yaml
mybatis: 
  config-location: classpath:mybatis.xml
  mapperLocations: classpath:mapper/*.xml
  typeAliasesPackage: pers.tavish.springboot.domain
```

`Student`类如下：

```java
package pers.tavish.springboot.domain;

public class Student {
    private Integer id;
    private String name;
    private int age;
    private double score;

    public Student() {
        super();
    }

    public Student(String name, int age, double score) {
        super();
        this.name = name;
        this.age = age;
        this.score = score;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getScore() {
        return score;
    }

    public void setScore(double score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student [id=" + id + ", name=" + name + ", age=" + age + ", score=" + score + "]";
    }
}
```

`StudentMapper`接口：

这里使用全注解形式来代替mapper.xml。

```java
package pers.tavish.springboot.mappers;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import pers.tavish.springboot.domain.Student;

public interface StudentMapper {
    @Insert("insert into student(name,age,score) values(#{name},#{age},#{score})")
    void insertStudent(Student student);
    
    @Select("select * from student")
    List<Student> selectAllStudents();
}
```

创建student表：

```sql
CREATE TABLE `student` (
  `id` int(5) NOT NULL AUTO_INCREMENT,
  `name` varchar(20) DEFAULT NULL,
  `age` int(3) DEFAULT NULL,
  `score` double DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8
```

定义测试类：

```java
package pers.tavish.springboot;

import java.util.List;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

import pers.tavish.springboot.domain.Student;
import pers.tavish.springboot.mappers.StudentMapper;

@MapperScan("pers.tavish.springboot.mappers")
@RunWith(SpringRunner.class)
@SpringBootTest
public class ApplicationTests {
    
    @Autowired
    private StudentMapper studentMapper;
    
    @Test
    public void testInsertStudent() {
        Student student1 = new Student("Tavish", 23, 98.0);
        Student student2 = new Student("Taco", 22, 92.5);
        Student student3 = new Student("Triela", 24, 99.3);
        studentMapper.insertStudent(student1);
        studentMapper.insertStudent(student2);
        studentMapper.insertStudent(student3);
    }
    
    @Test
    public void testSelectAllStudents() {
        List<Student> list = studentMapper.selectAllStudents();
        for (Student student : list) {
            System.out.println(student);
        }
    }
}
```

测试通过，查询方法输出：

```text
Student [id=1, name=Tavish, age=23, score=98.0]
Student [id=2, name=Taco, age=22, score=92.5]
Student [id=3, name=Triela, age=24, score=99.3]
```

### Spring Boot配置事务管理

Spring Boot使用事务特别简单，只需要两步就可以完成：

1. 使用`@EnableTransactionManagement`开启事务支持
2. 在Service方法上添加注解`@Transactional`并进行配置

（注意，事实上我们无需使用`@EnableTransactionManagement`，当我们的类路径下存在Spring-tx时，或者我们使用了spring-boot-starter-jdbc或者spring-boot-starter-data-jpa，则Spring Boot会为我们自动开启事务管理器）

接下来我们把上一节的工程改为web工程。

Service：

```java
package pers.tavish.springboot.services;

import java.util.List;

import pers.tavish.springboot.domain.Student;

public interface StudentService {
    List<Student> getAllStudents();

    void saveStudent(Student student);
}
```

实现类，使用`@Transactional`注解，并使用其默认配置，同时`saveStudent()`方法在保存了学生后会抛出一个运行时异常：

```java
package pers.tavish.springboot.services;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import pers.tavish.springboot.domain.Student;
import pers.tavish.springboot.mappers.StudentMapper;

@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentMapper mapper;
    
    @Override
    @Transactional
    public List<Student> getAllStudents() {
        return mapper.selectAllStudents();
    }

    @Override
    @Transactional
    public void saveStudent(Student student) {
        mapper.insertStudent(student);
        throw new RuntimeException("Just Kidding...");
    }

}
```

Controller：

```java
package pers.tavish.springboot.controllers;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import pers.tavish.springboot.services.StudentService;

@RestController
public class StudentController {

    @Autowired
    private StudentService service;

    @RequestMapping("/boot/queryAll")
    public Object queryAllStudents() {
        return service.getAllStudents();
    }

    @RequestMapping("/boot/randomInsert")
    public void randomInsert() {
        Student student = new Student("Error", 50, 50.0d);
        service.saveStudent(student);
    }
}
```

Mapper，这里使用`@Mapper`注解：

```java
package pers.tavish.springboot.mappers;

import java.util.List;

import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

import pers.tavish.springboot.domain.Student;

@Mapper
public interface StudentMapper {
    @Insert("insert into student(name,age,score) values(#{name},#{age},#{score})")
    void insertStudent(Student student);
    
    @Select("select * from student")
    List<Student> selectAllStudents();
}
```

程序入口，使用`@EnableTransactionManagement`开启事务管理器：

```java
package pers.tavish.springboot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

application.yml：

```yaml
server:
  port: 9090
  servlet:
    context-path: /04-springboot-tx

spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&useSSL=true
    username: root
    password: mysql
```

启动应用，访问“http://localhost:9090/04-springboot-tx/boot/queryAll” ，显示：

```json
[{"id":1,"name":"Tavish","age":23,"score":98.0},{"id":2,"name":"Taco","age":22,"score":92.5},{"id":3,"name":"Triela","age":24,"score":99.3}]
```

接着访问“http://localhost:9090/04-springboot-tx/boot/randomInsert” ，根据我们的配置，此时Controller会构造一个`Student`并调用Service对这个学生进行持久化。但是由于我们开启了事务，并且`StudentServiceImpl`在调用Mapper对学生持久化后抛出了一个运行时异常，所以事务发生回滚，这个“Error”学生也就没有被存入数据库。如果我们没有抛出异常，或者方法没有使用`@Transactional`注解，亦或者没有开启事务管理器（我们没有显示开启，Spring Boot同时没有为我们自动启用事务管理器），则不会回滚。

事实上想要发生异常时使事务进行回滚有两个方法：

1. 程序发生运行时异常
2. 程序抛出了受查异常，同时在注解上进行了声明：`@Transactional(rollbackFor = FoobarException.class)`

注意，异常如果被`try-catch`处理了，则不会发生事务回滚，毕竟经过`try-catch`后我们认为程序已经从异常中恢复了。

### Spring Boot实现RESTful API

什么是RESTful？

>
REST（英文：Representational State Transfer，简称REST）描述了一个架构样式的网络系统，比如 web 应用程序。它首次出现在 2000 年 Roy Fielding 的博士论文中，Roy Fielding是 HTTP 规范的主要编写者之一。在目前主流的三种Web服务交互方案中，REST相比于SOAP（Simple Object Access protocol，简单对象访问协议）以及XML-RPC更加简单明了，无论是对URL的处理还是对Payload的编码，REST都倾向于用更加简单轻量的方法设计和实现。值得注意的是REST并没有一个明确的标准，而更像是一种设计的风格。

如果一个架构符合REST原则，就称它为RESTful架构。

REST的四个原则：

1. 使用Http动词：GET、POST、PUT、DELETE
2. 无状态连接，服务器端不应保存过多上下文状态，即每个请求都是独立的
3. 为每个资源设置URI
4. 通过XML JSON进行数据传递

在RESTful架构中，一切都被认为是资源，每个资源由URI标识。

实现RESTful风格主要通过注解`@PathVariable`实现。

在Service接口中添加方法：

```java
public interface StudentService {
    
    Student getStudentById(int id);
    
    List<Student> getAllStudents();

    void saveStudent(Student student);
}
```

实现类：

```java
@Service
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentMapper mapper;
    
    @Override
    @Transactional
    public Student getStudentById(int id) {
        return mapper.selectStudentById(id);
    }

    // ...

}
```

Mapper：

```java
@Mapper
public interface StudentMapper {
    @Insert("insert into student(name,age,score) values(#{name},#{age},#{score})")
    void insertStudent(Student student);
    
    @Select("select * from student")
    List<Student> selectAllStudents();
    
    @Select("select * from student where id = #{id}")
    Student selectStudentById(int id);
}
```

Controller：

```java
@RestController
@RequestMapping("/boot")
public class StudentController {

    @Autowired
    private StudentService service;

    @RequestMapping("/queryAll")
    public Object queryAllStudents() {
        return service.getAllStudents();
    }

    @RequestMapping("/randomInsert")
    public void randomInsert() {
        Student student = new Student("Error", 50, 50.0d);
        service.saveStudent(student);
    }
    
    @GetMapping("/student/{id}")
    public Object queryStudentById(@PathVariable int id) {
        return service.getStudentById(id);
    }
}
```

application.yml：

```yaml
server:
  port: 9090
  servlet:
    context-path: /04-springboot-restful

spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&useSSL=true
    username: root
    password: mysql
```

启动应用，访问“http://localhost:9090/04-springboot-restful/boot/student/1” 则在浏览器中显示：

```json
{"id":1,"name":"Tavish","age":23,"score":98.0}
```

上述Controller中我们使用了注解`@GetMapping`，在RESTful风格的架构中，我们通常使用Http动词来表达动作含义。对应的注解如下：

- @GetMapping：用于查询资源，限制method为GET
- @PostMapping：用于创建资源，限制method为POST
- @PutMapping：用于更新资源，通常更新整个资源，限制method为PUT
- @PatchMapping：用于更新资源，通常更新局部资源，限制method为PATCH
- @DeleteMapping：用于删除资源，限制method为DELETE

### Spring Boot热部署插件

在实际开发中，我们修改某些代码逻辑功能或页面都需要重启应用，这无形中降低了开发效率。热部署是指当我们修改代码后，服务器能自动重启并加载新的内容。

Spring Boot的热部署通过添加一个插件实现：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-devtools -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
```

添加这个插件之后，我们的应用就具备热部署功能。此时修改代码后不必重新启动应用了。

### Spring Boot集成Redis

Spring Boot集成Redis需要使用其对应的starter依赖：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-redis -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

接下来对redis进行配置：

```yaml
spring: 
  redis: 
    host: localhost
    port: 6379
    password: myRedis
```

在引入依赖并配置redis后，Spring Boot会为我们自动装配`RedisTemplate`，我们只要在需要的地方使用就可以了。这里我们为`getAllStudents()`方法添加redis缓存：

```java
@Service
public class StudentServiceImpl implements StudentService {

    // ...

    @Autowired
    private RedisTemplate<Object, Object> redisTemplate;

    @SuppressWarnings("unchecked")
    @Override
    @Transactional
    public List<Student> getAllStudents() {

        RedisSerializer<String> serializer = new StringRedisSerializer();
        redisTemplate.setKeySerializer(serializer);

        List<Student> studentList = (List<Student>) redisTemplate.opsForValue().get("allStudents");

        if (studentList == null) {
            synchronized (this) {
                studentList = (List<Student>) redisTemplate.opsForValue().get("allStudents");
                if (studentList == null) {
                    studentList = mapper.selectAllStudents();
                    redisTemplate.opsForValue().set("allStudents", studentList);
                }
            }
        }

        return studentList;
    }

    // ...
}
```

最后由于redis要求我们的domain是可以序列化的，所以我们的`Student`类需要实现`Serializable`接口：

```java
public class Student implements Serializable {
    
    private static final long serialVersionUID = 997446597268576734L;
    
    private Integer id;
    private String name;
    private int age;
    private double score;

    public Student() {
        super();
    }

    public Student(String name, int age, double score) {
        super();
        this.name = name;
        this.age = age;
        this.score = score;
    }

    // ...
}
```

### Spring Boot集成Dubbo

要在Spring Boot中集成Dubbo需要加入以下starter依赖：

```xml
<dependency>
    <groupId>com.alibaba.spring.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>
```

Dubbo官方推荐我们将项目分为三个部分：

- 服务接口
- 服务提供者
- 服务消费者

**定义服务接口项目**

服务接口项目是一个单纯的Maven项目。

`StduentService`：

```java
package pers.tavish.springboot.service;

import pers.tavish.springboot.domain.Student;

public interface StudentService {

    public String sayHi(String name);
    
    public Student getStudentById(int id);
}
```

`Student`类：

```java
package pers.tavish.springboot.domain;

public class Student {
    private Integer id;
    private String name;
    private int age;
    private double score;

    public Student() {
        super();
    }

    public Student(String name, int age, double score) {
        super();
        this.name = name;
        this.age = age;
        this.score = score;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getScore() {
        return score;
    }

    public void setScore(double score) {
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student [id=" + id + ", name=" + name + ", age=" + age + ", score=" + score + "]";
    }
}
```

我们的`Student`类要实现`Serializable`接口以使其可以序列化。

编写完毕后，使用Maven install将这个Maven工程打成一个Jar包，以供服务提供者工程和服务消费者工程使用。

**定义服务提供者项目**

服务提供者项目中需要使用dubbo-spring-boot-starter依赖：

```xml
<dependency>
    <groupId>com.alibaba.spring.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>
```

加入依赖后，我们需要在application.yml中配置dubbo：

```yaml
# dubbo 配置
spring:
  dubbo:
    application:
      name: dubbo-provider
    registry:
      address: zookeeper://localhost:2181
```

由于使用了zookeeper作为注册中心，所以需要再加入zookeeper的依赖：

```xml
<!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.10</version>
</dependency>
```

最后我们还需要添加服务接口的依赖：

```xml
<dependency>
    <groupId>pers.tavish.springboot</groupId>
    <artifactId>07-springboot-dubbo-interface</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

接下来编写`StudentService`的实现类：

```java
package pers.tavish.springboot.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

import com.alibaba.dubbo.config.annotation.Service;

import pers.tavish.springboot.domain.Student;
import pers.tavish.springboot.mappers.StudentMapper;
import pers.tavish.springboot.service.StudentService;

@Component
@Service(version = "1.0.0", timeout = 10000, interfaceClass = StudentService.class) // 这里使用的是dubbo的注解
public class StudentServiceImpl implements StudentService {

    @Autowired
    private StudentMapper mapper;

    @Override
    public String sayHi(String name) {
        return "Hi, Spring Boot " + name;
    }

    @Override
    public Student getStudentById(int id) {
        return mapper.selectStudentById(id);
    }
}
```

这里使用的`@Service`注解是dubbo的注解，不是Spring的注解，这个注解表明这个类是dubbo的服务。然后我们使用`@Component`注解表明这个类是Spring的一个bean。

然后我们需要在provider项目的主程序上添加注解`@EnableDubboConfiguration`以开启我们对dubbo的配置：

```java
@SpringBootApplication
@EnableDubboConfiguration 
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

现在，在启动zookeeper之后我们就可以运行主函数启动服务提供者程序了。

**定义服务消费者项目**

与服务提供者项目相同，我们要加入Spring Boot与dubbo集成的starter依赖、zookeeper的依赖以及服务接口的依赖：

```xml
<dependency>
    <groupId>com.alibaba.spring.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>2.0.0</version>
</dependency>

<!-- https://mvnrepository.com/artifact/com.101tec/zkclient -->
<dependency>
    <groupId>com.101tec</groupId>
    <artifactId>zkclient</artifactId>
    <version>0.10</version>
</dependency>

<dependency>
    <groupId>pers.tavish.springboot</groupId>
    <artifactId>07-springboot-dubbo-interface</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

加入依赖后，进行配置：

```yaml
# dubbo 配置
spring:
  dubbo:
    application:
      name: dubbo-consumer
    registry:
      address: zookeeper://localhost:2181
```

下面我们编写一个Controller来调用远程的dubbo服务：

```java
@RestController
public class StudentController {

    @Reference(version = "1.0.0")
    private StudentService service;

    @RequestMapping("/boot/sayHi/{name}")
    public Object sayHi(@PathVariable String name) {
        return service.sayHi(name);
    }

    @RequestMapping("/boot/student/{id}")
    public Object queryStudentById(@PathVariable int id) {
        return service.getStudentById(id);
    }
}
```

这里我们使用了注解`@Reference`，它表示要引用dubbo远程服务。

最后，我们同样要在`Application`类中开启dubbo配置：

```java
@SpringBootApplication
@EnableDubboConfiguration
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

需要注意的是，由于事实上我们的服务提供者和服务消费者都运行在localhost上，所以我们在配置端口时要指定不同的端口。

服务提供者启动后，启动我们的服务消费者，访问“http://localhost:9091/07-springboot-dubbo-consumer/boot/student/1” 则会显示:

```json
{"id":1,"name":"Tavish","age":23,"score":98.0}
```

### Spring Boot使用拦截器

这里按照Spring MVC的方式编写拦截器：

```java
public class MyInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
        System.out.println("进入自定义拦截器...");
        return true;
    }

}
```

这个拦截器只会再后台输出一句话，然后放行。

接下来注册这个拦截器：

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        String[] addPathPatterns = { "/boot/**" };
        
        String[] excludePathPatterns = { "/boot/hello" };

        registry.addInterceptor(new MyInterceptor()).addPathPatterns(addPathPatterns)
                .excludePathPatterns(excludePathPatterns);
    }

}
```

这里定义了要拦截和不拦截的URL，我们设定会拦截所有以“/boot”开头的请求但是对“/boot/Hello”不进行拦截。

同时这个类使用了`@Configuration`注解使得Spring Boot可以加载这个配置。

最后编写Controller：

```java
@RestController
@RequestMapping("/boot")
public class MyController {

    @RequestMapping("/hi")
    public String sayHi() {
        return "Hi!";
    }

    @RequestMapping("/hello")
    public String sayHello() {
        return "Hello!";
    }
}
```

按照我们的配置`sayHi()`方法会被拦截，`sayHello()`方法会被拦截。

### Spring Boot中使用Servlet和Filter

**使用Servlet**

在Spring Boot中使用Servlet与通常的Web项目使用Servlet没有太大的区别。

我们首先要添加依赖：

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <scope>provided</scope>
</dependency>
```

编写Servlet：

```java
package pers.tavish.springboot.servlet;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/boot/hello")
public class HelloServlet extends HttpServlet {

    private static final long serialVersionUID = 3940993967576797496L;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setCharacterEncoding("utf-8");
        PrintWriter writer = resp.getWriter();
        writer.print("<h1>Hello Spring Boot!</h1>");
        writer.flush();
        writer.close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
    
}
```

最后开启Servlet扫描：

```java
@ServletComponentScan
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

我们也可以使用配置类的方式来注册Servlet，例如如下的`HiServlet`：

```java
public class HiServlet extends HttpServlet {

    private static final long serialVersionUID = 3940993967576797496L;

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter writer = resp.getWriter();
        writer.print("<h1>Hi Spring Boot!</h1>");
        writer.flush();
        writer.close();
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
    
}
```

这里我们不使用注解`@WebServlet`，而是在配置类中注册它：

```java
package pers.tavish.springboot.config;

import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import pers.tavish.springboot.servlet.HiServlet;

@Configuration
public class WebConfig {
    @Bean
    public ServletRegistrationBean<HiServlet> servletRegistrationBean() {
        ServletRegistrationBean<HiServlet> registration = new ServletRegistrationBean<>(new HiServlet(), "/boot/hi");
        return registration;
    }
}
```

上述方式实际上是Spring JavaConfig的通用模式，即使用`@Configuration`和`@Bean`来代替注解和XML来配置Spring bean。

**Spring Boot中使用Filter**

使用Filter的方式与使用Servlet的方式基本相同。这里我们定义两个Filter，一个通过注解注册、一个通过配置类注册。

Filter0：

```java
package pers.tavish.springboot.filter;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;

@WebFilter("/boot/hello")
public class Filter0_HelloFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("Filter0_HelloFilter works...");
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {
        
    }
}
```

Filter1：

```java
package pers.tavish.springboot.filter;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;

public class Filter1_HiFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        System.out.println("Filter1_HiFilter works...");
        chain.doFilter(request, response);
    }

    @Override
    public void destroy() {
        
    }

}
```

配置类增加方法：

```java
@Bean
public FilterRegistrationBean<Filter1_HiFilter> filterRegistrationBean() {
    FilterRegistrationBean<Filter1_HiFilter> registration = new FilterRegistrationBean<>(new Filter1_HiFilter());
    registration.addUrlPatterns("/boot/hi");
    return registration;
}
```

`Application`类上的注解`@ServletComponentScan`也会扫描使用`@WebFilter`注解的类，并将其注册为Filter。

### Spring Boot项目配置字符编码

之前字符编码问题主要由配置`org.springframework.web.filter.CharacterEncodingFilter`来解决，解决方式如下：

```xml
<!-- 注册字符集过滤器 -->
<filter>
    <filter-name>characterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <!-- 指定字符集 -->
    <init-param>
        <param-name>encoding</param-name>
        <param-value>utf-8</param-value>
    </init-param>
    <!-- 指定强制使用指定的字符集 -->
    <init-param> 
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>

<filter-mapping>
    <filter-name>characterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

如果不使用XML我们也可以在Web项目中使用JavaConfig的方式进行配置：

```java
@Bean
public FilterRegistrationBean<CharacterEncodingFilter> characterEncodingFilter() {
    FilterRegistrationBean<CharacterEncodingFilter> registration = new FilterRegistrationBean<CharacterEncodingFilter>();
    CharacterEncodingFilter filter = new CharacterEncodingFilter("utf-8", true);
    registration.setFilter(filter);
    registration.addUrlPatterns("/*");
    return registration;
}
```

上述JavaConfig的方式在Spring Boot中也可以使用，但是Spring Boot为我们提供了一种全新的方式来配置，就是在application.yml中配置：

```yaml
spring: 
  http:
    encoding:
      enabled: true
      charset: UTF-8
      force: true
```

如果我们已经使用JavaConfig的方式进行配置，那么要在application.yml中将`spring.http.encoding.enable`设置为`false`（默认为`true`），这样才能使其生效。

（测试发现使用无论使用哪种配置在使用Servlet时都会出现乱码，需要使用`resp.setContentType("text/html;charset=utf-8");`）

### Spring Boot与非Web应用程序

前面我们的程序大多都是Web项目（除了MyBatis集成的测试），我们也可以使用Spring Boot来开发非Web应用程序，即纯Java程序。

由于不是Web项目，所以在创建工程的时候就无需使用web-starter依赖了。

运行非Web项目主要有两种方式：

**方式一：直接从Spring上下文中获取bean来使用**

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        
        ConfigurableApplicationContext context = SpringApplication.run(Application.class, args);
        
        StudentMapper mapper = context.getBean(StudentMapper.class);
        
        for (Student student : mapper.selectAllStudents()) {
            System.out.println(student);
        }
    }
}
```

这种方式与传统Spring项目获取bean的方式相同。

**方式二：主类实现`CommandLineRunner`接口**

```java
@SpringBootApplication
public class Application implements CommandLineRunner {

    @Autowired
    private StudentMapper mapper;
    
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        for (Student student : mapper.selectAllStudents()) {
            System.out.println(student);
        }
    }
}
```

这种方式下，`main()`方法同样用于启动Spring Boot，在Spring容器启动后就会调用`run()`方法。由于我们无法在`run()`方法中获取Spring应用上下文，所以可以使用自动注入的方式来获取bean。

### Spring Boot打成war包并部署

首先第一步我们需要将pom.xml中的`<package>`元素由jar改为war：

```xml
<groupId>pers.tavish.springboot</groupId>
<artifactId>11-springboot-war</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>war</packaging>
```

接下来我们的`Application`类需要继承`SpringBootServletInitializer`并重写方法：

```java
@SpringBootApplication
public class Application extends SpringBootServletInitializer {
    
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        return builder.sources(Application.class);
    }
}
```

由于我们最终打包后要部署到一个外部的Tomcat中，所以这里无需再指定端口和应用上下文了（应用上下文自动由`<artifactId>`和`<version>`决定）。这里最终我们的war为“11-springboot-war-0.0.1-SNAPSHOT.war”。

然后，执行Maven install，这样我们的程序就会被Maven打为war包，并存入本地仓库中。

最后我们只要在Tomcat中部署这个war包就可以了。现在我们的应用根路径为“http://localhost:8080/11-springboot-war-0.0.1-SNAPSHOT/” 。

### Spring Boot打成jar包并运行

首先第一步我们需要将pom.xml中的`<package>`元素由jar改为war：

```xml
<groupId>pers.tavish.springboot</groupId>
<artifactId>11-springboot-jar</artifactId>
<version>0.0.1-SNAPSHOT</version>
<packaging>jar</packaging>
```

然后我们的`Application`类无需再继承`SpringBootServletInitializer`：

```java
@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

}
```

接下来配置我们的应用上下文和端口，因为我们的应用打为jar包后是运行在内嵌的Tomcat上的：

```yaml
server:
  port: 9090
  servlet:
    context-path: /11-springboot-jar
```

然后，执行Maven install，这样我们的程序就会被Maven打为jar包，并存入本地仓库中。这里最终生成“11-springboot-jar-0.0.1-SNAPSHOT.jar”。

最后我们要使用命令`java -jar`来运行，cmd中cd到我们jar包所在目录，执行：

```text
java -jar 11-springboot-jar-0.0.1-SNAPSHOT.jar
```

控制台输出：

```text
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::        (v2.0.5.RELEASE)

2018-10-01 21:15:12.349  INFO 18324 --- [           main] pers.tavish.springboot.Application       : Starting Application v0.0.1-SNAPSHOT on DESKTOP-A8341UT with PID 18324 (D:\Program Files\apache-maven-3.5.3\repository\pers\tavish\springboot\11-springboot-jar\0.0.1-SNAPSHOT\11-springboot-jar-0.0.1-SNAPSHOT.jar started by Tavish in D:\Program Files\apache-maven-3.5.3\repository\pers\tavish\springboot\11-springboot-jar\0.0.1-SNAPSHOT)
2018-10-01 21:15:12.353  INFO 18324 --- [           main] pers.tavish.springboot.Application       : No active profile set, falling back to default profiles: default
2018-10-01 21:15:12.404  INFO 18324 --- [           main] ConfigServletWebServerApplicationContext : Refreshing org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@1c2c22f3: startup date [Mon Oct 01 21:15:12 CST 2018]; root of context hierarchy
2018-10-01 21:15:13.748  INFO 18324 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat initialized with port(s): 9090 (http)
2018-10-01 21:15:13.772  INFO 18324 --- [           main] o.apache.catalina.core.StandardService   : Starting service [Tomcat]
2018-10-01 21:15:13.772  INFO 18324 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/8.5.34
2018-10-01 21:15:13.780  INFO 18324 --- [ost-startStop-1] o.a.catalina.core.AprLifecycleListener   : The APR based Apache Tomcat Native library which allows optimal performance in production environments was not found on the java.library.path: [C:\ProgramData\Oracle\Java\javapath;C:\WINDOWS\Sun\Java\bin;C:\WINDOWS\system32;C:\WINDOWS;C:\ProgramData\Oracle\Java\javapath;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Program Files\Java\jdk1.8.0_152\bin;C:\Program Files\Java\jdk1.8.0_152\jre\bin;C:\WINDOWS\system32;C:\WINDOWS;C:\WINDOWS\System32\Wbem;C:\WINDOWS\System32\WindowsPowerShell\v1.0\;C:\Program Files\MySQL\MySQL Server 5.7\bin;C:\WINDOWS\System32\OpenSSH\;D:\Program Files\Git\cmd;D:\Program Files\apache-maven-3.5.3\bin;D:\Program Files\gradle-4.9\bin;C:\Program Files\MongoDB\Server\4.0\bin;D:\Program Files\neo4j-community-3.4.7\bin;D:\Program Files\Gaussian\G03W;C:\Program Files (x86)\NVIDIA Corporation\PhysX\Common;D:\Program Files\spring-2.0.5.RELEASE\bin;C:\Users\Tavish\AppData\Local\Microsoft\WindowsApps;;.]
2018-10-01 21:15:13.989  INFO 18324 --- [ost-startStop-1] o.a.c.c.C.[.[.[/11-springboot-jar]       : Initializing Spring embedded WebApplicationContext
2018-10-01 21:15:13.990  INFO 18324 --- [ost-startStop-1] o.s.web.context.ContextLoader            : Root WebApplicationContext: initialization completed in 1595 ms
2018-10-01 21:15:14.063  INFO 18324 --- [ost-startStop-1] o.s.b.w.servlet.ServletRegistrationBean  : Servlet dispatcherServlet mapped to [/]
2018-10-01 21:15:14.067  INFO 18324 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'characterEncodingFilter' to: [/*]
2018-10-01 21:15:14.068  INFO 18324 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'hiddenHttpMethodFilter' to: [/*]
2018-10-01 21:15:14.068  INFO 18324 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'httpPutFormContentFilter' to: [/*]
2018-10-01 21:15:14.068  INFO 18324 --- [ost-startStop-1] o.s.b.w.servlet.FilterRegistrationBean   : Mapping filter: 'requestContextFilter' to: [/*]
2018-10-01 21:15:14.319  INFO 18324 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**/favicon.ico] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-01 21:15:14.546  INFO 18324 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@1c2c22f3: startup date [Mon Oct 01 21:15:12 CST 2018]; root of context hierarchy
2018-10-01 21:15:14.606  INFO 18324 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/boot/queryAll]}" onto public java.lang.Object pers.tavish.springboot.controllers.StudentController.queryAllStudents()
2018-10-01 21:15:14.607  INFO 18324 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/boot/randomInsert]}" onto public void pers.tavish.springboot.controllers.StudentController.randomInsert()
2018-10-01 21:15:14.609  INFO 18324 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public org.springframework.http.ResponseEntity<java.util.Map<java.lang.String, java.lang.Object>> org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.error(javax.servlet.http.HttpServletRequest)
2018-10-01 21:15:14.609  INFO 18324 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error],produces=[text/html]}" onto public org.springframework.web.servlet.ModelAndView org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController.errorHtml(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2018-10-01 21:15:14.631  INFO 18324 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-01 21:15:14.631  INFO 18324 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-01 21:15:14.829  INFO 18324 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Registering beans for JMX exposure on startup
2018-10-01 21:15:14.831  INFO 18324 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Bean with name 'dataSource' has been autodetected for JMX exposure
2018-10-01 21:15:14.834  INFO 18324 --- [           main] o.s.j.e.a.AnnotationMBeanExporter        : Located MBean 'dataSource': registering with JMX server as MBean [com.zaxxer.hikari:name=dataSource,type=HikariDataSource]
2018-10-01 21:15:14.863  INFO 18324 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 9090 (http) with context path '/11-springboot-jar'
2018-10-01 21:15:14.866  INFO 18324 --- [           main] pers.tavish.springboot.Application       : Started Application in 2.813 seconds (JVM running for 3.212)
2018-10-01 21:16:42.101  INFO 18324 --- [nio-9090-exec-5] o.a.c.c.C.[.[.[/11-springboot-jar]       : Initializing Spring FrameworkServlet 'dispatcherServlet'
2018-10-01 21:16:42.102  INFO 18324 --- [nio-9090-exec-5] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization started
2018-10-01 21:16:42.126  INFO 18324 --- [nio-9090-exec-5] o.s.web.servlet.DispatcherServlet        : FrameworkServlet 'dispatcherServlet': initialization completed in 23 ms
2018-10-01 21:16:55.327  INFO 18324 --- [nio-9090-exec-3] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2018-10-01 21:16:55.497  INFO 18324 --- [nio-9090-exec-3] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
```

项目运行成功，现在我们的应用根路径为“http://localhost:9090/11-springboot-jar/boot/queryAll” 。

### Spring Boot集成Thymeleaf

Thymeleaf是一个流行的模板引擎，改模板引擎采用Java语言开发。Thymeleaf是基于HTML的，以HTML为载体。

Thymeleaf模板既能用在web环境下，也能用于非web环境下。在非web环境下他能直接显示模板上的静态数据，在web环境下，他能像JSP一样从后台接收数据。

Spring Boot集成了Thymeleaf模板技术，并且Spring Boot官方也推荐使用Thymeleaf来替代JSP。

集成的第一步我们需要使用thymeleaf的starter依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

接下来在application.yml中对Thymeleaf进行配置：

```yaml
spring: 
  thymeleaf:
    servlet:
      content-type: text/html # 设置content-type
    enable-spring-el-compiler: true # 启用SpEL表达式编译器
    encoding: UTF-8 # 字符集设置为UTF-8
    cache: false # 关闭模板缓存，开发时关闭
    mode: LEGACYHTML5 # 使用非严格模式的HTML
```

这里使用`LEGACYHTML5`模式（事实上我们很少使用严格的HTML模式），需要引入如下依赖，否则报错：

```xml
<!-- https://mvnrepository.com/artifact/net.sourceforge.nekohtml/nekohtml -->
<dependency>
    <groupId>net.sourceforge.nekohtml</groupId>
    <artifactId>nekohtml</artifactId>
</dependency>

<!-- https://mvnrepository.com/artifact/org.unbescape/unbescape -->
<dependency>
    <groupId>org.unbescape</groupId>
    <artifactId>unbescape</artifactId>
    <version>1.1.6.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.attoparser/attoparser -->
<dependency>
    <groupId>org.attoparser</groupId>
    <artifactId>attoparser</artifactId>
    <version>2.0.5.RELEASE</version>
</dependency>
```

编写Controller：

```java
@Controller
@RequestMapping("/boot")
public class ThymeleafController {

    @RequestMapping("/salute")
    public String salute(Model model) {
        model.addAttribute("msg", "Spring Boot: Hello, Thymeleaf");
        return "hello";
    }
}
```

编写HTML页面，Thymeleaf默认模板页面要存在于classpath:src/main/resources/templates下：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Spring Boot集成Thymeleaf</title>
</head>
<body>
    <p th:text="${msg}">
        
    </p>
</body>
</html>
```

要在HTML中使用Thymeleaf，我们需要设置它的命名空间`<html xmlns:th="http://www.thymeleaf.org">`。

#### Thymeleaf简明语法

##### Thymeleaf标准表达式

**变量表达式**

变量表达式：`${}`，用于获取容器上下文环境中的变量，功能与JSTL中的`${}`相同。

例如Controller中代码：

```java
@RequestMapping("/studentInfo/{id}")
public String studentInfo(Model model, @PathVariable int id) {
    model.addAttribute("student", mapper.selectStudentById(id));
    return "studentInfo";
}
```

HTML中就可以这样获取：

```html
<body>
    <table border="1">
        <tr>
            <th>ID</th>
            <th>NAME</th>
            <th>AGE</th>
            <th>SCORE</th>
        </tr>
        <tr> 
            <td th:text="${student.id}"></td>
            <td th:text="${student.name}"></td>
            <td th:text="${student.age}"></td>
            <td th:text="${student.score}"></td>
        </tr>
    </table>
</body>
```

其中`th:text`是Thymeleaf的属性，用于文本显示。

**选择表达式**

选择表达式：`*{}`，使用`th:object`绑定对象。

控制器代码不变，HTML代码如下：

```html
<div th:object="${student}">
    <p>ID: <span th:text="*{id}"></span></p>
    <p>NAME: <span th:text="*{name}"></span></p>
    <p>AGE: <span th:text="*{age}"></span></p>
    <p>SCORE: <span th:text="*{score}"></span></p>
</div>
```

这里首先使用`th:object`绑定后台传来的`Student`对象，然后使用`*`来代表这个对象，后面大括号中的值是该对象的属性。

选择表达式在执行时是在选择的对象上秋节，而变量表达式是在上下文的`Model`中求解。

上述代码事实上等价于：

```html
<div>
    <p>ID: <span th:text="${student.id}"></span></p>
    <p>NAME: <span th:text="*${student.name}"></span></p>
    <p>AGE: <span th:text="*${student.age}"></span></p>
    <p>SCORE: <span th:text="*${student.score}"></span></p>
</div>
```

变量表达式与选择表达式可以混合使用。

我们也可以不使用`th:object`进行对象的选择，而直接使用`*{}`来获取数据：

```html
<div>
    <p>ID: <span th:text="*{student.id}"></span></p>
    <p>NAME: <span th:text="*{student.name}"></span></p>
    <p>AGE: <span th:text="*{student.age}"></span></p>
    <p>SCORE: <span th:text="*{student.score}"></span></p>
</div>
```

**URL表达式**

URL表达式：`@{}`，可以用于`<script>`、`<link>`、`<a>`、`<form>`等元素中需要使用URL的地方。通常在URL包含动态变量时使用。在使用时需要使用属性`th:href`。

根据写法，URL表达式分为三种：

- 绝对URL：使用协议开头
- 相对于当前页面的URL：不以“/”开头
- 相对于项目上下文的URL：以“/”开头，应用上下文会自动添加

示例如下，当前URL为“http://localhost:9090/12-springboot-thymeleaf/boot/studentInfo/1” ：

```html
<!-- 绝对URL -->
<a th:href="@{'http://localhost:9090/12-springboot-thymeleaf/boot/studentInfo/' + ${student.id}}">查看ID为<span th:text="${student.id}"></span>的学生信息</a>
<!-- 相对于当前页面的URL -->
<a th:href="@{'' + ${student.id}}">查看ID为<span th:text="${student.id}"></span>的学生信息</a>
<!-- 相对于应用上下文的URL -->
<a th:href="@{'/boot/studentInfo/' + ${student.id}}">查看ID为<span th:text="${student.id}"></span>的学生信息</a>
```

这里静态的部分由“''”包括。

##### Thymeleaf常用属性

常用属性如下：

- th:action
- th:each
- th:href
- th:id
- th:if
- th:unless
- th:switch/th:case
- th:object
- th:text
- th:value
- th:attr
- ...

**th:action**

在form表单中使用，替换原有的`action`属性，通常搭配URL表达式使用。

**th:each**

遍历集合（List、Set、Map、数组）使用，例如：

```html
<ul th:each="student, iterStat:${students}">
    <li th:text="${student.id}"></li>
    <li th:text="${student.name}"></li>
    <li th:text="${student.age}"></li>
    <li th:text="${student.score}"></li>
</ul>
```

`th:each="student, iterStat:${students}"`表示从后台传来一个名为“students”的集合（这里是`List`），用“iterStat”来代表，而“student”是集合中的一个元素。这类似于Java中的`for-each`循环写法。

这里“student”和“iterStat”可以随便写。

循环体信息，即这里的“iterStat”有如下属性：

- index：当前迭代对象的index，从0开始计算
- count：当前迭代对象的个数，从1开始计算
- size：元素的总量
- current：当前迭代变量
- even/odd：布尔值，当前循环是否是偶数/奇数，从0开始计算
- first：布尔值，当前迭代是否是第一个
- last：布尔值，当前迭代是否是最后一个

**th:id**

类似于html标签中的`id`属性，例如：

```html
<span th:id="${student.id}">foo.bar</span>
```

**th:if**

用于条件判断，例如：

```html
<div th:object="${student}" th:if="*{id} eq 1">
    <span>如果id是1才输出本行</span>
</div>
```

**th:unless**

这个属性与`th:if`正好相反，例如：

```html
<div th:object="${student}" th:unless="*{id} eq 1">
    <span>如果id不是1才输出本行</span>
</div>
```

**th:switch/th:case**

switch语句，case用于判断，例如：

```html
<div th:switch="${student.id}">
    <span th:case="1">id为1</span>
    <span th:case="2">id为2</span>
    <span th:case="*">id即不为1也不为2</span>    
</div>
```

一旦某个case判断为`true`，那么就不再向下判断了。`*`表示默认的case，当前面的case均不为`true`时，执行默认的case。

**th:src**

用于外部资源引入，替代`<script>`、`<img>`等的`src`属性，常与URL表达式联用。

例如：

```html
<script th:src="@{/js/jquery-3.3.1.js}"></script>
```

**th:value**

与html标签中的`value`属性对应，能对某元素的`value`属性进行赋值，例如：

```html
<input type="text" id="studentId" name="studentId" th:value="${student.id}" />
```

**th:attr**

该属性也是用于给HTML中某元素的某属性赋值，例如：

```html
<input type="text" id="studentId" name="studentId" th:attr="value=${student.id}" />
```

**th:onclick**

定义点击事件，例如：

```html
<button th:onclick="'clickMe()'">ClickMe!</button>
<script type="text/javascript">
    function clickMe() {
        window.alert("Click!");
    }
</script>
```

**th:style**

用于定义样式，例如：

```html
<span th:style="'display:none;'">你看不到我</span>
```

**th:name**

与HTML的`name`属性类似，例如：

```html
<input type="text" th:name="${student.name}" />
```

**th:method**

用于form表单，与HTML的`method`属性类似，例如：

```html
<form th:action="@{/foo/bar}" th:method="POST">
</form>
```

**th:inline**

该属性使用内联表达式`[[]]`获取变量数据，例如：

```html
<span th:inline="text">Hello, [[${student.name}]]</span>
```

它相当于：

```html
<span>Hello, <span th:text="${student.name}"></span></span>
```

该属性还可以用于内联脚本，使得js脚本可以直接获取后台传来的变量：

```html
<script th:inline="javascript" type="text/javascript">
    var studentName = [[${student.name}]];
    alert(studentName);
</script>
```

##### Thymeleaf字面量

**文本字面量**

用给单引号`''`包围的字符串为文本字面量，例如：

```html
<a th:href="@{'/boot/studentInfo/' + ${student.id}}">查看ID为<span th:text="${student.id}"></span>的学生信息</a>
```

文本字面量的拼接有两种方式。

方式一：

```html
<span th:text="'当前是第' + ${currentPage} + '页，共' + ${pageCount} + '页'"></span>
```

方式二：

```html
<span th:text="|当前是第${currentPage}页，共${pageCount}页|"></span>
```


**数字字面量**

例如：

```html
<p>今年是<span th:text="2017"></span>年</p>
```

数字字面量可以直接进行数学运算：

```html
<p>20年后是<span th:text="2017 + 20"></span>年</p>
```

**布尔字面量**

`true`和`false`，例如：

```html
<p th:if="${ifFlag == true}">foobar</p>
```

**null字面量**

判断对象是否为空，例如：

```html
<p th:if="${studentList != null}">studentList不为空</p>
```

##### Thymeleaf三元运算符

例如：

```html
<span th:text="${gender eq 1} ? 'MALE' : 'FEMALE'"></span>
```

##### Thymeleaf运算和关系判断

- 算术运算：+、-、\*、/、%
- 关系比较：>（gt）、<（lt）、>=（ge）、<=（le）
- 相等判断：==（eq）、!=（ne）

##### Thymeleaf表达式基本对象

Thymeleaf模板引擎提供了一组内置的对象，这些内置的对象可以直接在模板中使用，这些对象由“#”号开头。

**#request**

获取应用上下文路径：

```text
${#request.getContextPath()}
```

获取request中的属性：

```text
${#request.getAttribute("attr")}
```

**#session**

获取session中的属性：

```text
${#session.getAttribute("attr")}
```

获取session的id：

```
${#session.id}
```

##### Thymeleaf表达式功能对象

Thymeleaf模板引擎提供的一组功能性内置对象，可以在模板中直接使用这些对象提供的功能方法，内置功能对象以“#”开头。

- \#dates
- \#calendars
- \#numbers
- \#objects
- \#bools
- \#arrays
- \#lists
- \#sets
- \#maps
- \#aggregates

### Spring Boot Actuator

在生产环境中，需要实时或顶起监控服务的可用性，Spring Boot的Actuator提供了很多监控所需的接口。Actuator可以对应用系统进行配置查看、健康检查等。

在使用时我们首先要加入依赖：

```xml
<!-- https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-actuator -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

然后进行配置：

```yaml
management:
  server:
    port: 9091 # 监控端口
    servlet:
      context-path: /13-springboot-actuator # 配置上下文
  endpoints:
    web:
      exposure:
        include: "*" # 开启所有web入口端点
```

完整配置：

```yaml
server:
  port: 9090

management:
  server:
    port: 9091 # 监控端口
    servlet:
      context-path: /13-springboot-actuator # 配置上下文
  endpoints:
    web:
      exposure:
        include: "*" # 开启所有web入口端点

spring:
  datasource: # 数据库连接四要素
    driver-class-name: com.mysql.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test?useUnicode=true&characterEncoding=UTF-8&useSSL=true
    username: root
    password: mysql
```

这里我们的应用运行在9090端口上，没有应用上下文；监控服务运行在9091端口上，上下文为“/13-springboot-actuator”。

程序启动后会出现如下信息：

```text
2018-10-03 20:10:58.098  INFO 17772 --- [           main] o.s.b.a.e.web.EndpointLinksResolver      : Exposing 14 endpoint(s) beneath base path '/actuator'
2018-10-03 20:10:58.108  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/auditevents],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.109  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/beans],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.109  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/health],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.109  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/conditions],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.109  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/configprops],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.109  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/env],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.109  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/env/{toMatch}],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.110  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/info],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.110  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/loggers],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.110  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/loggers/{name}],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.111  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/loggers/{name}],methods=[POST],consumes=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.111  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/heapdump],methods=[GET],produces=[application/octet-stream]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.111  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/threaddump],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.112  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/metrics],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.112  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/metrics/{requiredMetricName}],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.112  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/scheduledtasks],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.112  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/httptrace],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.113  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator/mappings],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto public java.lang.Object org.springframework.boot.actuate.endpoint.web.servlet.AbstractWebMvcEndpointHandlerMapping$OperationHandler.handle(javax.servlet.http.HttpServletRequest,java.util.Map<java.lang.String, java.lang.String>)
2018-10-03 20:10:58.113  INFO 17772 --- [           main] s.b.a.e.w.s.WebMvcEndpointHandlerMapping : Mapped "{[/actuator],methods=[GET],produces=[application/vnd.spring-boot.actuator.v2+json || application/json]}" onto protected java.util.Map<java.lang.String, java.util.Map<java.lang.String, org.springframework.boot.actuate.endpoint.web.Link>> org.springframework.boot.actuate.endpoint.web.servlet.WebMvcEndpointHandlerMapping.links(javax.servlet.http.HttpServletRequest,javax.servlet.http.HttpServletResponse)
2018-10-03 20:10:58.132  INFO 17772 --- [           main] s.w.s.m.m.a.RequestMappingHandlerMapping : Mapped "{[/error]}" onto public java.util.Map<java.lang.String, java.lang.Object> org.springframework.boot.actuate.autoconfigure.web.servlet.ManagementErrorEndpoint.invoke(org.springframework.web.context.request.ServletWebRequest)
2018-10-03 20:10:58.137  INFO 17772 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-03 20:10:58.137  INFO 17772 --- [           main] o.s.w.s.handler.SimpleUrlHandlerMapping  : Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
2018-10-03 20:10:58.148  INFO 17772 --- [           main] s.w.s.m.m.a.RequestMappingHandlerAdapter : Looking for @ControllerAdvice: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@29f85fe1: startup date [Wed Oct 03 20:10:57 CST 2018]; parent: org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext@6ca8564a
2018-10-03 20:10:58.192  INFO 17772 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 9091 (http) with context path '/13-springboot-actuator'
2018-10-03 20:10:58.205  INFO 17772 --- [           main] o.s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 9090 (http) with context path ''
```

此时访问“http://localhost:9091/13-springboot-actuator/actuator/health” 会出现以下信息：

```text
{"status":"UP"}
```

