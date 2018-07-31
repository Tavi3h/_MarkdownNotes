[TOC]

# SpringMVC

## 第一章 SpringMVC概述

### 1.1 SpringMVC简介

SpringMVC也被称为Spring web mvc，属于表现层的框架。SpringMVC是Spring框架的一部分，是在Spring 3.0后发布的。

![SpringFramework](images\SpringFramework.png)
![SpringFramework_2](images\SpringFramework_2.png)

### 1.2 第一个SpringMVC程序

项目primary，完成功能：用户提交一个请求，服务端在接收到这个请求后，给出一条欢迎信息，在响应页面中显示该信息。

#### 1.2.1 所需Jar包

SpringMVC需要的基本Jar包是在原Spring的Jar包基础上，增加两个Jar包：

- Spring-context-support：包含支持UI模板，邮件服务，缓存Cache等方面的类。
- Spring-webmvc：对SpringMVC的实现。

#### 1.2.2 创建工程并编写相应的类

*以下内容在工程SpringMVC-01-primary中。*

创建类MyController，实现接口org.springframework.web.servlet.mvc.Controller。

```java
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

public class MyController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        // TODO Auto-generated method stub
        return null;
    }

}
```

完善代码，设置要显示的信息：

```java
public class MyController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("message", "Hello SpringMVC World!");
        mv.setViewName("/WEB-INF/jsp/welcome.jsp");
        return mv;
    }
}
```

#### 1.2.3 定义目标页面

在WEB-INF/jsp下创建welcome.jsp：

```html
<%@ page language="java" contentType="text/html; charset=utf-8"
    pageEncoding="utf-8"%>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort()
            + path + "/";
%>
<!DOCTYPE html>
<html>
<head>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Welcome Page</title>
</head>
<body>

${message}

</body>
</html>
```

#### 1.2.4 在配置文件中注册处理器类

springmvc.xml：

在注册处理器时需要注意处理器的id属性值为一个请求URI，表示当客户端提交该请求时，会访问class属性指定的这个处理器。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd">
        
    <!-- 注册处理器 -->
    <bean id="/my.do" class="pers.tavish.handlers.MyController" />    
        
</beans>
```

该配置文件放置于classpath下。

#### 1.2.5 在web.xml中配置SpringMVC的中央调度器

```xml
<!-- 注册SpringMVC中央调度器 -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

配置好上述信息后，如果我们访问my.do，即http://localhost:8080/SpringMVC-01-primary/my.do，会发生报错：

```text
java.io.FileNotFoundException: Could not open ServletContext resource [/WEB-INF/springmvc-servlet.xml]
```

容器试图加载WEB-INF下的springmvc-servlet.xml（该文件名中的“springmvc”来自web.xml中servlet-name）但是没有找到该文件。

**所以，我们需要将springmvc.xml修改为springmvc-servlet.xml并将其放置于WEB-INF下。**

再次访问http://localhost:8080/SpringMVC-01-primary/my.do，没有报错，页面显示Hello SpringMVC World!

#### 1.2.6 指定springmvc配置文件的位置及文件名

通常情况下我们不会使用例如springmvc-servlet.xml这样的文件名，并且也不会把配置文件置于WEB-INF下。

根据`DispatcherServlet`的父类`FrameworkServlet`中的相关描述：

>
 * Passes a "contextConfigLocation" servlet init-param to the context instance,
 * parsing it into potentially multiple file paths which can be separated by any
 * number of commas and spaces, like "test-servlet.xml, myServlet.xml".
 * If not explicitly specified, the context implementation is supposed to build a
 * default location from the namespace of the servlet.

所以我们为Servlet添加初始化参数：

```xml
<!-- 注册SpringMVC中央调度器 -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 指定配置文件的位置及文件名 -->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

此时再将配置文件的名字改为springmvc.xml并置于src目录下即可。

#### 1.2.7 在Tomcat启动时初始化中央调度器

由于Servlet默认是当收到第一次请求时才进行初始化，所以对于第一次的访问会略显缓慢。这里通过配置load-on-startup标签使得中央调度器在web容器初始化时就得到创建。

```xml
<!-- 注册SpringMVC中央调度器 -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 指定配置文件的位置及文件名 -->
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <!-- 在Tomcat启动时就直接初始化当前Servlet -->
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

#### 1.2.8 注册视图解析器

*以下内容在工程SpringMVC-02-primary中*。

由于`mv.setViewName("/WEB-INF/jsp/welcome.jsp");`中参数的写法很繁琐，所以我们注册视图解析器，分离视图的前缀和后缀：

```xml
<!-- 注册视图解析器 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp"/>
</bean>
```

此时，Controller中的代码可简化为：

```java
public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
    ModelAndView mv = new ModelAndView();
    mv.addObject("message", "Hello SpringMVC World!");
    mv.setViewName("welcome");
    return mv;
}
```

此时访问my.do，同样可以转发到welcome.jsp。

*（实际上这种方式并不好用）*

### 1.3 SpringMVC执行流程

![SpringMVC执行流程](images\SpringMVC执行流程.PNG)

#### 1.3.1 执行流程简单描述

1. 浏览器提交请求到中央调度器。
2. 中央调度器将请求转给处理器映射器。
3. 处理器映射器根据请求，找到处理该请求的处理器，并将其封装为处理器执行链后返回给中央调度器。
4. 中央调度器根据处理器执行链中的处理器，找到能够执行该处理器的处理器适配器。
5. 处理器适配器调用执行处理器。
6. 处理器将处理结果及要跳转的视图封装到一个对象ModelAndView中，并将其返回给处理器适配器。
7. 处理器适配器直接将结果返回给中央调度器。
8. 中央调度器调用视图解析器，将ModelAndView中的视图名称封装为视图对象。
9. 视图解析器将封装了的视图对象返回给中央调度器。
10. 中央调度器调用视图对象，让其自己进行渲染，即进行数据填充，形成响应对象。
11. 中央调度器将响应返回给浏览器。

#### 1.3.2 各组件简要说明

1. **DispatcherServlet**：中央调度器，也称为前端控制器，在MVC架构模式中充当控制器C，DispatcherServlet是整个流程的控制中心，由它调用注入处理器映射器、处理器适配器、视图解析器等其他组件处理用户请求。中央调度器的存在降低了组件之间的耦合度。
2. **HandlerMapping**：处理器映射器，负责根据用户请求找到响应的将要执行的Handler，即处理器。即用于完成将用户请求映射为要处理该请求的处理器，并将处理器封装为处理器执行链传给中央调度器。
3. **HandlerAdapter**：处理器适配器，通过HandlerAdapter对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。中央调度器会根据不同的处理器自动为处理器选择适配器，以执行处理器。
4. **Handler**：处理器，也称为后端控制器，在中央调度器的控制线Handler调用Service层对具体的用户请求进行处理。由于Handler涉及到具体的用户业务请求，所以一般情况下需要程序员根据业务需求自己开发Handler。
5. **ViewResolver**：视图解析器，负责将处理结果生成View视图，ViewResolver首先将视图名解析为物理视图名，即具体的页面地址，再生成View视图对象。最后将处理结果通过页面形式展示给用户。

#### 1.3.3 DispatcherServlet的默认配置

在简单了解了各个组件后我们会发现一个问题：处理器映射器、处理器适配器等这些组件不用配置吗？

如果我们没有对这些组件进行配置，那么中央调度器会使用默认的配置，这个默认的配置在DispatcherServlet.properties文件中：

```text
org.springframework.web.servlet.LocaleResolver=org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver

org.springframework.web.servlet.ThemeResolver=org.springframework.web.servlet.theme.FixedThemeResolver

org.springframework.web.servlet.HandlerMapping=org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping,\
    org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping

org.springframework.web.servlet.HandlerAdapter=org.springframework.web.servlet.mvc.HttpRequestHandlerAdapter,\
    org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter,\
    org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter

org.springframework.web.servlet.HandlerExceptionResolver=org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerExceptionResolver,\
    org.springframework.web.servlet.mvc.annotation.ResponseStatusExceptionResolver,\
    org.springframework.web.servlet.mvc.support.DefaultHandlerExceptionResolver

org.springframework.web.servlet.RequestToViewNameTranslator=org.springframework.web.servlet.view.DefaultRequestToViewNameTranslator

org.springframework.web.servlet.ViewResolver=org.springframework.web.servlet.view.InternalResourceViewResolver

org.springframework.web.servlet.FlashMapManager=org.springframework.web.servlet.support.SessionFlashMapManager
```

由上可知，这些组件的默认配置如下：

组件名称|默认值
-----|-----
处理器映射器|BeanNameUrlHandlerMapping<br>DefaultAnnotationHandlerMapping
处理器适配器|HttpRequestHandlerAdapter<br>SimpleControllerHandlerAdapter<br>AnnotationMethodHandlerAdapter
视图解析器|InternalResourceViewResolver

### 1.4 &lt;url-pattern/&gt;

#### 1.4.1 配置详解

##### 1.4.1.1 建议写为*.do(action/...)等形式

在没有特殊要求的情况下。SpringMVC的中央调度器的&lt;url-pattern/&gt;常使用后缀匹配方式。

##### 1.4.1.2 不能写为/*

这里的&lt;url-pattern/&gt;不能写为/*，因为DispatcherServlet会将向**动态页面**的跳转请求，即向JSP页面的跳转请求也当作是一个普通的Controller请求。中央调度器会调用处理器映射器为其查找响应的处理器，当然，这是无法找到的。所以在这种情况下，所有的JSP页面跳转均会发生404错误。

##### 1.4.1.3 最好也不要写为/

因为中央调度器会将向**静态资源**的获取请求，例如.css、.js、.png等资源的获取请求当作是一个普通的Controller请求。中央调度器会调用处理器映射器为其查找响应的处理器。当然，这也是无法找到的。所以在这种情况下，所有的静态资源获取均会发生404错误。

在本例中，如果使用/，那么可以访问index.jsp，也可以访问my.do，但此时如果要获取一个png图片，则会失败。

示例，*以下内容在工程SpringMVC-03-urlpattern中。*

将&lt;url-pattern/&gt;修改为/：

```xml
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:springmvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

在index.jsp页面添加一个图片请求：

```html
<body>

Index Page.
<br>

<img alt="Picture" src="images/character.jpg"/>

</body>
```

此时访问index.jsp，不会加载出图片。

**解决方法：**

当&lt;url-pattern/&gt;为/时，可以通过一些配置使得静态资源可以被正常请求。

**方法（1）：使用Tomcat中名为default的Servlet**

*以下内容在工程SpringMVC-04-urlpattern-default中。*

由于我们这里要访问的静态资源为.jpg格式的图片，所以在web.xml中添加以下内容：

```xml
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.jpg</url-pattern>
</servlet-mapping>
```

此时，访问index.jsp就可以正常加载出.jpg格式的图片了。

那么这个servlet-mapping到底对应的是哪个Servlet呢？

这里实际上我们调用的是Tomcat自身的Servlet，这个Servlet的定义位于Tomcat根目录下conf/web.xml中，具体定义如下：

```xml
<servlet>
    <servlet-name>default</servlet-name>
    <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
    <init-param>
        <param-name>debug</param-name>
        <param-value>0</param-value>
    </init-param>
    <init-param>
        <param-name>listings</param-name>
        <param-value>false</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
```

根据注释，该Servlet处理一切没有被映射到指定Servlet的静态资源请求。

>
The default servlet for all web applications, that serves static resources. It processes all requests that are not mapped to other servlets with servlet mappings (defined either here or in your own web.xml file).

**方法（2）：使用&lt;mvc:default-servlet-handler/&gt;**

*以下内容在工程SpringMVC-05-urlpattern-default-servlet-handler中。*

这里我们要使用mvc标签，所以需要添加mvc的约束：

```xml
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

</beans>
```

在添加了mvc约束后，添加default-servlet-handler：

```xml
<mvc:default-servlet-handler/>
```

此时，&lt;url-pattern/&gt;为/也可以正常访问静态资源了。

这里&lt;mvc:default-servlet-handler/&gt;会将对静态资源的访问请求添加到SimpleUrlHandlerMapping的urlMap中，key就是请求的URL，儿value则为默认Servlet请求处理器DefaultServletHttpRequestHandler对象。而该处理器调用的正是Tomcat的DefaultServlet来处理静态资源的访问请求。

**方法（3）使用&lt;mvc:resources/&gt;标签**

*以下内容在工程SpringMVC-06-urlpattern-resources中。*

在Spring3.0.4版本后，Spring中定义了专门用于处理静态资源访问请求的处理器ResourceHttpRequestHandler，并添加了&lt;mvc:resources/&gt;标签专门用于解决静态资源无法访问的问题。

由于我们的静态资源在images文件夹下，所以添加以下代码：

```xml
<mvc:resources location="/images/" mapping="/images/**" />
```

- location：表示静态资源所在目录，这里的目录可以是WEB-INF目录及其子目录。
- mapping：表示对该资源的请求。**注意后面是两个星号**。

该配置会把对该静态资源的访问请求添加到SimpleUrlHandlerMapping的urlMap中，key就是真正与mapping的URL匹配的URL，而value则为静态资源处理器对象ResourceHttpRequestHandler。

### 1.5 绝对路径与相对路径

#### 1.5.1 问题演示

*以下内容在工程SpringMVC-07-accessPath中。*

index.jsp：

```html
<body>

Index Page.

<br>

<a href="hello.do">跳转到welcome页面。</a>

</body>
```

springmvc.xml：

```xml
<!-- 注册处理器 -->
<bean id="/hello.do" class="pers.tavish.handlers.MyController" />   
```

此时点击index页面上的超链接将正常跳转到welcome.jsp。

但此时，若在index.jsp的超链接中加上斜杠（`href="/hello.do"`），则会发生404错误。

#### 1.5.2 访问路径与资源名称

通常的URL资源访问路径由两部分组成：访问路径与资源名称。

如：`http://localhost:8080/SpringMVC-07-accessPath/hello.do`

- 访问路径：`http://localhost:8080/SpringMVC-07-accessPath/`
- 资源名称：hello.do

又如：`<a href="foo/bar/show.do">xxx</a>`

- 访问路径：foo/bar/
- 资源名称：show.do

根据“访问路径是否可以独立完成资源准确定位”的判别标准，可以将访问路径分为绝对路径与相对路径。

#### 1.5.3 绝对路径

绝对路径，是指根据给出的访问路径可以准确定位到资源的路径。对于Web应用的绝对路径，指的是**带访问协议的路径**。例如：`http://localhost:8080/SpringMVC-07-accessPath/index.jsp`

#### 1.5.4 相对路径

相对路径，是指仅根据访问路径无法准确定位资源的路径。**相对路径必须要结合其参照路径才可组成可以准确定位资源的绝对路径。**参照路径的不同，所形成的可以准确定位资源的绝对路径也是不同的。在进行资源访问时，必须要将相对路径转换为绝对路径才可完成资源的准确定位。

在Web应用中，相对路径有两种写法：

- 以斜杠开头的相对路径。
- 不以斜杠开头的相对路径。

**根据相对路径是否以斜杠开头，且出现的文件所处位置的不同，其默认的参照路径是不同的。**

##### 1.5.4.1 以斜杠开头的相对路径

以斜杠开头的相对路径，根据路径所在文件所处的位置不同，分为两种：**前台路径**和**后台路径**。

**（1）前台路径**

前台路径是指由浏览器解析执行的代码中所包含的路径。例如html、css、js中的路径以及jsp中静态部分的路径。

例如`<img src=""/>`、`<a href=""></a>`、`<form action=""></form>`等，像css中的`background:img("")`、js中的`window.location.href=""`等都属于前台路径。

**前台路径的参照路径是Web服务器的根路径**，如`http://localhost:8080/`。

**（2）后台路径**

后台路径是指由服务器解析执行的代码及文件中所包含的路径，例如java代码中的路径、jsp文件动态部分（java代码块）中的路径、xml文件（被java加载入内存，并由java代码解析）中的路径等。

**后台路径的参照路径是Web应用的根路径**，如`http://localhost:8080/SpringMVC-07-accessPath/`。

**（3）后台路径特例**

对于后台路径的参照路径有一个特例：**当代码中使用`response.sendRedirect()`方法进行重定向时，其参照路径不是Web应用的根路径，而是Web服务器的根路径。**

例如，执行`reponse.sendRedirect("/show.jsp");`将会发生404错误，因为其参照路径是Tomcat的根，而非当前项目的根，所以若要使用重定向：

- 要么写无斜杠的相对路径`reponse.sendRedirect("show.jsp");`
- 要么在路径上添加项目名称：`reponse.sendRedirect(request.getContextPath() + "/show.jsp");`

##### 1.5.4.2 不以斜杠开头的相对路径

不以斜杠开头的相对路径，无论是前台路径，还是后台路径，**其参照路径都是当前资源的访问路径，而不是当前资源的保存路径**。

#### 1.5.5 相关现象解析

**（1）为什么页面超链接的href属性值加上斜杠会报错**

A、原因分析

index.jsp中超链接的路径是前台路径，加上斜杠后其参照路径是当前Web服务器的根，则绝对路径变为：`http://localhost:8080/hello.do`，所以会报出404错误。

B、解决办法

我们当然可以使用不加斜杠的写法，但如果想加上斜杠，则可以手工为href属性值的相对路径上添加项目名称：
`<a href="/SpringMVC-07-accessPath/hello.do">xxx</a>`。
但是这样做不好的是，若在项目发布时项目名称发生了改变，则必须将每一个请求路径进行改变。这里我们可以使用EL表达式动态获取到项目名称：
`<a href="${pageContext.request.contextPath}/hello.do">xxx</a>`。这里EL表达式`${pageContext.request.contextPath}`的值为`/SpringMVC-07-accessPath`。

**（2）跳转回index页面后地址栏多出一个test**

*以下内容在工程SpringMVC-08-accessPath-2中。*

A、问题重现

a、修改springmvc.xml

为了构建发生这种异常的场景，在注册处理器时，为该bean的id属性添加一个/test，表示模块信息。

```xml
<!-- 注册处理器 -->
<bean id="/test/hello.do" class="pers.tavish.handlers.MyController" />   
```

b、修改index页面

修改index页面，让index页面的超链接href属性采用无斜杠的路径。

```html
<body>
Index Page.
<br>
<a href="test/hello.do">跳转到index页面。</a>
</body>
```

c、修改处理器

修改MyController中ModelAndView中指定要跳转的页面为/index.jsp，表示重新返回index页面。

```java
@Override
public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
    return new ModelAndView("/index.jsp");
}
```

发布这个项目后，访问index页面，此时地址栏地址为：`http://localhost:8080/SpringMVC-07-accessPath-2/`（默认访问index.jsp），点击超链接，经由controller回到index页面，此时地址栏地址却变为：`http://localhost:8080/SpringMVC-07-accessPath-2/test/hello.do`，在此基础上再次点击超链接，发生404错误，此时地址栏地址为：`http://localhost:8080/SpringMVC-07-accessPath-2/test/test/hello.do`。

B、原因分析

为什么回报错呢？因为这里index页面的超链接为无斜杠的相对路径，所以其参照路径为当前的访问路径。在第一次访问index页面时，当前的访问路径是`http://localhost:8080/SpringMVC-07-accessPath-2/`，所以点击超链接，此时的绝对路径即为：`http://localhost:8080/SpringMVC-07-accessPath-2/test/hello.do`；经由controller跳转回index页面后，此时的访问路径发生了变化，为`http://localhost:8080/SpringMVC-07-accessPath-2/test/`，所以在此基础上再次点击超链接，这时的绝对路径为`http://localhost:8080/SpringMVC-07-accessPath-2/test/test/hello.do`，而这个资源是没有定义的，所以发生404错误。

C、解决办法

a、使用斜杠开头的请求路径

由于使用带斜杠的请求路径会造成其参照路径为Web容器的根的问题，所以还要加上项目名称。这里采用EL表达式的方式动态增加项目名称。

```html
<body>
Index Page.
<br>
<a href="${pageContext.request.contextPath}/test/hello.do">跳转到index页面。</a>
</body>
```

b、不使用斜杠开头的请求路径

若不使用斜杠开头的请求路径，则需要使用jsp的base标签。该标签会在当前页面的资源请求路径前自动加上该标签所代表的路径，将相对路径变为带访问协议的绝对路径。

该方式实际上是修改了不带斜杠的相对路径的参照路径，将由原来的当前访问路径作为参照路径改为以basePath为参照路径。

```html
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort()
            + path + "/";
%>
<!DOCTYPE html>
<html>
<head>
<base href="<%=basePath%>">
<meta charset="utf-8">
```

使用上述两种解决方案都不会再发生404错误。

## 第二章 SpringMVC配置式开发

所谓配置式开发是指，“处理器类是程序员手工定义的、实现了特定接口的类，然后再在SpringMVC配置文件中对该类进行显式的、明确的注册”的开发方式。

### 2.1 处理器映射器 HandlerMapping

HandlerMapping接口负责根据request请求找到对应的Handler处理器及Interceptor拦截器，并将它们封装在HandlerExecutionChain对象中，返回给中央调度器。

常用的实现类有两个：

- BeanNameUrlHandlerMapping
- SimpleUrlHandlerMapping

#### 2.1.1 BeanNameUrlHandlerMapping

BeanNameUrlHandlerMapping处理器映射器，会根据请求的Url与Spring容器中定义的处理器bean的name属性值进行匹配，从而在Spring容器中找到处理器bean示例。

打开该类的源码，从处理器映射器的方法中可以看出，**对于处理器的Bean的名称，必须以“/”开头，否则无法加入到urls数组中。**而urls数组中的url则是中央调度器用于判定**该url所对应的类是否作为处理器交给处理器适配器进行适配**的依据。

```java
/**
 * Checks name and aliases of the given bean for URLs, starting with "/".
 */
@Override
protected String[] determineUrlsForHandler(String beanName) {
    List<String> urls = new ArrayList<String>();
    if (beanName.startsWith("/")) {
        urls.add(beanName);
    }
    String[] aliases = getApplicationContext().getAliases(beanName);
    for (String alias : aliases) {
        if (alias.startsWith("/")) {
            urls.add(alias);
        }
    }
    return StringUtils.toStringArray(urls);
}

}
```

由于BeanNameUrlHandlerMapping是默认使用的HandlerMapping实现类，所以不用注册。

#### 2.1.2 SimpleUrlHandlerMapping

使用BeanNameUrlHandlerMapping映射器有两点明显的不足：

- 处理器Bean的id为一个url请求路径，而不是Bean的名称，有些不伦不类。
- 处理器Bean的定义与请求url绑定在了一起，若出现多个url请求同一个处理器的情况，就需要在Spring容器中配置多个该处理器的bean标签，这将导致容器会创建多个该处理器类实例。

例如，MyController可以处理两个请求：

```xml
<bean id="/hello.do" class="pers.tavish.handlers.MyController" />  
<bean id="/hello2.do" class="pers.tavish.handlers.MyController" />    
```

SimpleUrlHandlerMapping处理器映射器，不仅可以将url与处理器的定义分离，还可以对url进行统一映射管理。**默认的HandlerMapping实现类不包括该类。**

SimpleUrlHandlerMapping处理器映射器，会根据请求url与Spring容器中定义的处理器映射器子标签的key属性进行匹配。匹配上后，再将该key的value值与处理器bean的id值进行匹配，从而在Spring容器中找到处理器bean。

*以下内容在工程SpringMVC-09-simpleUrlHandlerMapping中。*

注册SimpleUrlHandlerMapping处理器映射器，并修改处理器的id：

第一种写法：

```xml
<!-- 注册处理器映射器 -->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="mappings">
        <props>
            <prop key="/hello.do">myController</prop>
        </props>
    </property>
</bean>

<!-- 注册处理器 -->
<bean id="myController" class="pers.tavish.handlers.MyController" /> 
```

第二种写法：

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="urlMap">
        <map>
            <entry key="/hello.do" value-ref="myController" />
        </map>
    </property>
</bean>

<!-- 注册处理器 -->
<bean id="myController" class="pers.tavish.handlers.MyController" /> 
```

不需要为SimpleUrlHandlerMapping配置id属性。

此时假设有多个请求路径对应我们的myController，则可以配置多个prop标签或entry标签：

```xml
<props>
    <prop key="/hello.do">myController</prop>
    <prop key="/hello2.do">myController</prop>
    <prop key="/hello3.do">myController</prop>
</props>
```

或

```xml
<map>
    <entry key="/hello.do" value-ref="myController" />
    <entry key="/hello2.do" value-ref="myController" />
    <entry key="/hello3.do" value-ref="myController" />
</map>
```

### 2.2 处理器适配器 HandlerAdapter

处理器适配器使用适配器模式。适配器模式使得**原本由于接口不兼容而不能一起工作的那些类可以在一起工作。**所以处理器适配器所起到的作用是**将多种处理器（实现了不同接口的处理器），通过处理器适配器的适配，使它们可以进行统一标准的工作，对请求进行统一方式的处理。**

前面的工程中只所以要将Handler定义为Controller接口的实现类，就是因为这里使用的处理器适配器是SimpleControllerHandlerAdapter。这个适配器会将Handler强转为Controller。

HandlerAdapter接口会根据处理器所实现接口的不同，对处理器进行适配，适配后即可对处理器进行执行。通过扩展处理器适配器，可以执行多种类型的处理器。

在得到请求时，中央调度器在`doDispatch()`方法中会遍历处理器适配器，并使用其`support()`方法，判断Handler是否与某个接口具有is-a关系。

中央调度器判断Handler是否与某个接口具有is-a关系：

```java
protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
    for (HandlerAdapter ha : this.handlerAdapters) {
        if (logger.isTraceEnabled()) {
            logger.trace("Testing handler adapter [" + ha + "]");
        }
        if (ha.supports(handler)) {
            return ha;
        }
    }
    throw new ServletException("No adapter for handler [" + handler +
            "]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
}
```

中央调度器得到ha后，会执行以下代码来执行处理器适配器的`handle()`方法：

```java
// Actually invoke the handler.
mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
```

常用的HandlerAdapter接口实现类有两种：

- SimpleControllerHandlerAdapter
- HttpReuqestHandlerAdapter

#### 2.2.1 SimpleControllerHandlerAdapter

**所有实现了Controller接口的处理器Bean，均是通过此适配器进行适配并执行的。**

SimpleControllerHandlerAdapter的源码如下：

```java
public class SimpleControllerHandlerAdapter implements HandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof Controller);
    }

    @Override
    public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {

        return ((Controller) handler).handleRequest(request, response);
    }

    @Override
    public long getLastModified(HttpServletRequest request, Object handler) {
        if (handler instanceof LastModified) {
            return ((LastModified) handler).getLastModified(request);
        }
        return -1L;
    }

}
```

从上述代码可知，当中央调度器在执行`ha.handle()`方法时，这里就会强转后执行我们实现的`handlerRequest()`方法。

该方法用于处理用户提交的请求。通过调用Service层代码，实现对用户请求的计算响应，并将最终计算所得数据及要响应的页面，封装为一个ModelAndView对象返回给中央调度器。

#### 2.2.2 HttpRequestHandlerAdapter

**所有实现了HttpRequestHandler接口的处理器Bean，均通过此适配器进行适配、执行。**

HttpRequestHandler接口只有一个方法：

```java
void handleRequest(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException;
```

**该方法没有返回值，不能像ModelAndView一样将数据及目标视图封装为一个对象。但可以将数据直接放入到request、session等的域属性中，并由request或response完成到目标页面的跳转。**

HttpRequestHandlerAdapter的源码：

```java
public class HttpRequestHandlerAdapter implements HandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof HttpRequestHandler);
    }

    @Override
    public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {

        ((HttpRequestHandler) handler).handleRequest(request, response);
        return null;
    }

    @Override
    public long getLastModified(HttpServletRequest request, Object handler) {
        if (handler instanceof LastModified) {
            return ((LastModified) handler).getLastModified(request);
        }
        return -1L;
    }

}
```

从上述代码可知，当中央调度器在执行`ha.handle()`方法时，这里就会强转后执行我们实现的`handlerRequest()`方法。

注意该适配器与SimpleControllerHandlerAdapter的`handle()`方法的不同，该适配器执行`handleRequest()`方法，但是返回null。

*以下内容在工程SpringMVC-10-httpRequestHandlerAdapter中。*

定义处理器，实现HttpRequestHandler接口：

```java
public class MyController implements HttpRequestHandler {

    @Override
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setAttribute("message", "Hello SpringMVC World!");
        request.getRequestDispatcher("/WEB-INF/jsp/welcome.jsp").forward(request, response);
    }

}
```

当然，此时无需再配置前缀、后缀了，即再springmvc.xml中无需声明视图解析器的Bean了。

注册处理器Bean：

```xml
<bean id="/hello.do" class="pers.tavish.handlers.MyController" />    
```

jsp页面：

```html
<body>

${message}

</body>
```

发布后访问hello.do，显示Hello SpringMVC World!

### 2.3 处理器

处理器除了可以实现Controller/HttpRequestHandler接口外，还可以继承自一些其它的类来完成一些特殊的功能。

#### 2.3.1 继承AbstractController类

该抽象类继承了WebContentGenerator并实现了Controller接口：

```java
public abstract class AbstractController extends WebContentGenerator implements Controller {
    // ...
}
```

**继承该类的类，需要实现抽象方法`handleRequestInternal()`**：

```java
@Override
protected ModelAndView handleRequestInternal(HttpServletRequest request, HttpServletResponse response) throws Exception {
    
    return null;
}
```

这个方法就是我们需要编写的方法。

那么这个方法是在什么时候被调用执行的呢？

AbstractController中定义的`handleRequest()`方法负责调用它：

```java
@Override
public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response)
        throws Exception {

    if (HttpMethod.OPTIONS.matches(request.getMethod())) {
        response.setHeader("Allow", getAllowHeader());
        return null;
    }

    // Delegate to WebContentGenerator for checking and preparing.
    checkRequest(request);
    prepareResponse(response);

    // Execute handleRequestInternal in synchronized block if required.
    if (this.synchronizeOnSession) {
        HttpSession session = request.getSession(false);
        if (session != null) {
            Object mutex = WebUtils.getSessionMutex(session);
            synchronized (mutex) {
                return handleRequestInternal(request, response);
            }
        }
    }

    return handleRequestInternal(request, response);
}
```

**限定请求提交方式：** 

WebContentGenerator类具有supportedMethods属性，可以设置支持的HTTP数据提交方式。默认支持GET、POST。

```java

/** Set of supported HTTP methods */
private Set<String> supportedMethods;

// ...

/**
 * Set the HTTP methods that this content generator should support.
 * <p>Default is GET, HEAD and POST for simple form controller types;
 * unrestricted for general controllers and interceptors.
 */
public final void setSupportedMethods(String... methods) {
    if (!ObjectUtils.isEmpty(methods)) {
        this.supportedMethods = new LinkedHashSet<String>(Arrays.asList(methods));
    }
    else {
        this.supportedMethods = null;
    }
    initAllowHeader();
}
```

若处理器继承自AbstractController类，那么处理器就可以通过属性supportedMethods来限制HTTP请求提交方式了。例如，指定只支持POST的HTTP请求提交方式：

```xml
<bean id="/hello.do" class="pers.tavish.handlers.MyController">
    <property name="supportedMethods" value="POST" />
</bean>    
```

此时意味着该请求只能通过表单或AJAX请求方式进行提交，而不能通过地址栏、超链接、HTML标签中的src方式进行提交，因为这些提交方式都是GET请求。如果进行提交，则会发生请求方法不允许的405错误。

客户端浏览器常用的请求方式及提交方式：

请求方式|提交方式
-----|-----
表单请求 | 默认GET，可以指定POST
AJAX请求 | 默认GET，可以指定POST
地址栏请求 | GET请求
超链接请求 | GET请求
src资源路径请求 | GET请求

*以下内容在工程SpringMVC-11-abstractController中。*

定义处理器：

```java
public class MyController extends AbstractController {

    @Override
    protected ModelAndView handleRequestInternal(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("message", "Hello SpringMVC World!");
        mv.setViewName("/WEB-INF/jsp/welcome.jsp");
        return mv;
    }
}
```

注册处理器并限定只能使用POST方法：（如果不限定则GET、POST都支持）

```xml
<!-- 注册处理器 -->
<bean id="/hello.do" class="pers.tavish.handlers.MyController" >
    <property name="supportedMethods" value="POST" /> 
</bean>  
```

此时发布程序，使用`http://localhost:8080/SpringMVC-11-abstractController/hello.do`进行请求就会报出405错误。

#### 2.3.1 继承MultiActionController类

MultiActionController类继承自AbstractController并实现了LastModified接口，所以继承MultiActionController的子类也可以设置HTTP请求提交方式。

```java
public class MultiActionController extends AbstractController implements LastModified {
    // ...
}
```

除此之外，继承自该类的处理器中可以定义任意多个处理方法。这些方法的方法名随意，访问修饰符为public，返回值为ModelAndView，包含参数HttpServletRequest个HttpServletResponse，并抛出Exception异常。

**注意：MultiActionController类已经在4.3版本中被标注为@Deprecated：**

>
@deprecated as of 4.3, in favor of annotation-driven handler methods

*以下内容在工程SpringMVC-12-multiActionController中。*

修改处理器类：

```java
public class MyController extends MultiActionController {

    public ModelAndView doFirst(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("message", "执行doFirst()方法");
        mv.setViewName("/WEB-INF/jsp/welcome.jsp");
        return mv;
    }
    
    public ModelAndView doSecond(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mv = new ModelAndView();
        mv.addObject("message", "执行doSecond()方法");
        mv.setViewName("/WEB-INF/jsp/welcome.jsp");
        return mv;
    }
}
```

修改springmvc.xml：

这里需要配置处理器映射器SimpleUrlHandlerMapping，并且其中路径的写法要求必须以/xxx/\*的方式定义映射路径，其中\*为通配符，在访问时使用要访问的方法名代替。**这里的/xxx主要用于对访问路径进行限定。**

```xml
<!-- 注册处理器映射器 -->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="urlMap">
        <map>
            <entry key="/my/*.do" value-ref="myController" />
        </map>
    </property>
</bean>

<!-- 注册处理器 -->
<bean id="myController" class="pers.tavish.handlers.MyController" />    
```

程序发布后，分别访问`http://localhost:8080/SpringMVC-12-multiActionController/my/doFirst.do`和`http://localhost:8080/SpringMVC-12-multiActionController/my/doSecond.do`，服务器会执行对应的方法并在页面上显示相关信息。

只所以通过在请求URL中写上方法名就可以访问到指定方法，是因为在MultiActionController类中有一个专门处理方法名称的解析器MethodNameResolver。该解析器作为一个属性出现，具有get和set方法。MethodNameResolver是一个接口，不同的解析器实现类，其对方法名在URL中的写法要求也是不同的。

**注意MethodNameResolver接口及其实现类也已被标记为@deprecated：**

>
@deprecated as of 4.3, in favor of annotation-driven handler methods

##### 2.3.1.1 InternalPathMethodNameResolver 方法名解析器（默认）

MultiAnctionController类具有一个默认的MethodNameResolver解析器：

```java
/** Delegate that knows how to determine method names from incoming requests */
private MethodNameResolver methodNameResolver = new InternalPathMethodNameResolver();
```

当未显示配置方法名解析器时，该解析器将作为默认方法名解析器对方法名进行解析。

该方法名解析器要求**方法名以URL中资源名称的身份出现**，即方法作为一种可以被请求的资源出现。要求写法为：**/xxx/方法名**。

##### 2.3.1.2 PropertiesMethodNameResolver 方法名解析器

该方法名解析器中的**方法名是作为URL资源名称中的一部分出现的**，即方法名并非单多作为一种资源名称。例如请求时写为/xxx_doFirst，则会访问xxx所映射的处理器的doFirst()方法。

*以下内容在工程SpringMVC-13-multiActionController-2中。*

保持处理器类不变，直接修改springmvc.xml：

```xml
<!-- 注册处理器映射器 -->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="urlMap">
        <map>
            <entry key="/my_*.do" value-ref="myController" />
        </map>
    </property>
</bean>

<!-- 配置方法名解析器 -->
<bean id="propertiesMethodNameResolver" class="org.springframework.web.servlet.mvc.multiaction.PropertiesMethodNameResolver">
    <property name="mappings">
        <props>
            <prop key="/my_First.do">doFirst</prop>
            <prop key="/my_Second.do">doSecond</prop>
        </props>
    </property>
</bean>

<!-- 注册处理器 -->
<bean id="myController" class="pers.tavish.handlers.MyController">
    <!-- 将配置好的方法名解析器注入给处理器 -->
    <property name="methodNameResolver" ref="propertiesMethodNameResolver" />
</bean>
```

（下划线不是必须的，我们要定义的是映射关系。甚至可以定义/my/First.do与doFirst对应。）

此时可以通过`http://localhost:8080/SpringMVC-13-multiActionController-2/my_First.do`访问`doFirst()`方法，通过`http://localhost:8080/SpringMVC-13-multiActionController-2/my_Second.do`访问doSecond()方法。

##### 2.3.1.3 ParameterMethodNameResolver 方法名解析器

该方法名解析中的**方法名作为请求参数的值出现。**例如请求时可以写为/foo?bar=doFirst，则会访问xxx所映射的处理器的doFirst()方法。其中bar为该请求所携带的参数名，而doFirst则作为其参数值出现。

*以下内容在工程SpringMVC-14-multiActionController-3中。*

直接修改springmvc.xml：

```xml
<!-- 注册处理器映射器 -->
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="urlMap">
        <map>
            <entry key="/my.do" value-ref="myController" />
        </map>
    </property>
</bean>

<!-- 配置方法名解析器 -->
<bean id="parameterMethodNameResolver" class="org.springframework.web.servlet.mvc.multiaction.ParameterMethodNameResolver">
    <property name="paramName" value="method" />
</bean>

<!-- 注册处理器 -->
<bean id="myController" class="pers.tavish.handlers.MyController">
    <!-- 将配置好的方法名解析器注入给处理器 -->
    <property name="methodNameResolver" ref="parameterMethodNameResolver" />
</bean>
```

上述配置中，paramName属性的值即为请求所携带的方法名。

经过上述配置，现在可以通过请求`http://localhost:8080/SpringMVC-14-multiActionController-3/my.do?method=doFirst`访问doFirst()方法，`http://localhost:8080/SpringMVC-14-multiActionController-3/my.do?method=doSecond`访问doSecond()方法。

注意到ParameterMehodNameResolver类的源码中定义默认的参数名：

```java
@Deprecated
public class ParameterMethodNameResolver implements MethodNameResolver {

    /**
     * Default name for the parameter whose value identifies the method to invoke:
     * "action".
     */
    public static final String DEFAULT_PARAM_NAME = "action";

    // ...
}
```

也就是说，当我们配置方法名解析器时，如果不设置paramName属性，那么我们将以类似/my.do?action=doFirst的形式来对相应的方法进行访问。

