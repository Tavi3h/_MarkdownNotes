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

创建类MyController，实现接口`org.springframework.web.servlet.mvc.Controller`。

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

该配置文件放置于`classpath`下。

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

由于Servlet默认是当收到第一次请求时才进行初始化，所以对于第一次的访问会略显缓慢。这里通过配置`load-on-startup`标签使得中央调度器在web容器初始化时就得到创建。

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
6. 处理器将处理结果及要跳转的视图封装到一个对象`ModelAndView`中，并将其返回给处理器适配器。
7. 处理器适配器直接将结果返回给中央调度器。
8. 中央调度器调用视图解析器，将`ModelAndView`中的视图名称封装为视图对象。
9. 视图解析器将封装了的视图对象返回给中央调度器。
10. 中央调度器调用视图对象，让其自己进行渲染，即进行数据填充，形成响应对象。
11. 中央调度器将响应返回给浏览器。

#### 1.3.2 各组件简要说明

1. **DispatcherServlet**：中央调度器，也称为前端控制器，在MVC架构模式中充当控制器C，`DispatcherServlet`是整个流程的控制中心，由它调用注入处理器映射器、处理器适配器、视图解析器等其他组件处理用户请求。中央调度器的存在降低了组件之间的耦合度。
2. **HandlerMapping**：处理器映射器，负责根据用户请求找到响应的将要执行的`Handler`，即处理器。即用于完成将用户请求映射为要处理该请求的处理器，并将处理器封装为处理器执行链传给中央调度器。
3. **HandlerAdapter**：处理器适配器，通过`HandlerAdapter`对处理器进行执行，这是适配器模式的应用，通过扩展适配器可以对更多类型的处理器进行执行。中央调度器会根据不同的处理器自动为处理器选择适配器，以执行处理器。
4. **Handler**：处理器，也称为后端控制器，在中央调度器的控制下`Handler`调用Service层对具体的用户请求进行处理。由于Handler涉及到具体的用户业务请求，所以一般情况下需要程序员根据业务需求自己开发Handler。
5. **ViewResolver**：视图解析器，负责将处理结果生成View视图，`ViewResolver`首先将视图名解析为物理视图名，即具体的页面地址，再生成`View`视图对象。最后将处理结果通过页面形式展示给用户。

#### 1.3.3 DispatcherServlet的默认配置

在简单了解了各个组件后我们会发现一个问题：处理器映射器、处理器适配器等这些组件不用配置吗？

如果我们没有对这些组件进行配置，那么中央调度器会使用默认的配置，这个默认的配置在`DispatcherServlet.properties`文件中：

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

在没有特殊要求的情况下。SpringMVC的中央调度器的`&lt;url-pattern/&gt;`常使用后缀匹配方式。

##### 1.4.1.2 不能写为/*

这里的`&lt;url-pattern/&gt;`不能写为/*，因为`DispatcherServlet`会将向**动态页面**的跳转请求，即向JSP页面的跳转请求也当作是一个普通的Controller请求。中央调度器会调用处理器映射器为其查找响应的处理器，当然，这是无法找到的。所以在这种情况下，所有的JSP页面跳转均会发生404错误。

##### 1.4.1.3 最好也不要写为/

因为中央调度器会将向**静态资源**的获取请求，例如.css、.js、.png等资源的获取请求当作是一个普通的Controller请求。中央调度器会调用处理器映射器为其查找响应的处理器。当然，这也是无法找到的。所以在这种情况下，所有的静态资源获取均会发生404错误。

在本例中，如果使用/，那么可以访问index.jsp，也可以访问my.do，但此时如果要获取一个png图片，则会失败。

示例，*以下内容在工程SpringMVC-03-urlpattern中。*

将`&lt;url-pattern/&gt`;修改为/：

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

当`&lt;url-pattern/&gt;`为/时，可以通过一些配置使得静态资源可以被正常请求。

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

那么这个`servlet-mapping`到底对应的是哪个Servlet呢？

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

此时，`&lt;url-pattern/&gt;`为/也可以正常访问静态资源了。

这里`&lt;mvc:default-servlet-handler/&gt;`会将对静态资源的访问请求添加到`SimpleUrlHandlerMapping`的urlMap中，key就是请求的URL，而value则为默认Servlet请求处理器`DefaultServletHttpRequestHandler`对象。而该处理器调用的正是Tomcat的`DefaultServlet`来处理静态资源的访问请求。

**方法（3）使用&lt;mvc:resources/&gt;标签**

*以下内容在工程SpringMVC-06-urlpattern-resources中。*

在Spring3.0.4版本后，Spring中定义了专门用于处理静态资源访问请求的处理器`ResourceHttpRequestHandler`，并添加了`&lt;mvc:resources/&gt;`标签专门用于解决静态资源无法访问的问题。

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

修改index页面，让index页面的超链接`href`属性采用无斜杠的路径。

```html
<body>
Index Page.
<br>
<a href="test/hello.do">跳转到index页面。</a>
</body>
```

c、修改处理器

修改MyController中`ModelAndView`中指定要跳转的页面为/index.jsp，表示重新返回index页面。

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

`HandlerMapping`接口负责根据`request`请求找到对应的`Handler`处理器及`Interceptor`拦截器，并将它们封装在`HandlerExecutionChain`对象中，返回给中央调度器。

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

由于`BeanNameUrlHandlerMapping`是默认使用的`HandlerMapping`实现类，所以不用注册。

#### 2.1.2 SimpleUrlHandlerMapping

使用`BeanNameUrlHandlerMapping`映射器有两点明显的不足：

- 处理器bean的id为一个url请求路径，而不是bean的名称，有些不伦不类。
- 处理器bean的定义与请求url绑定在了一起，若出现多个url请求同一个处理器的情况，就需要在Spring容器中配置多个该处理器的`bean`标签，这将导致容器会创建多个该处理器类实例。

例如，MyController可以处理两个请求：

```xml
<bean id="/hello.do" class="pers.tavish.handlers.MyController" />  
<bean id="/hello2.do" class="pers.tavish.handlers.MyController" />    
```

`SimpleUrlHandlerMapping`处理器映射器，不仅可以将url与处理器的定义分离，还可以对url进行统一映射管理。**默认的HandlerMapping实现类不包括该类。**

`SimpleUrlHandlerMapping`处理器映射器，会根据请求url与Spring容器中定义的处理器映射器子标签的key属性进行匹配。匹配上后，再将该key的value值与处理器bean的id值进行匹配，从而在Spring容器中找到处理器bean。

*以下内容在工程SpringMVC-09-simpleUrlHandlerMapping中。*

注册`SimpleUrlHandlerMapping`处理器映射器，并修改处理器的id：

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

前面的工程中只所以要将`Handler`定义为`Controller`接口的实现类，就是因为这里使用的处理器适配器是`SimpleControllerHandlerAdapter`。这个适配器会将`Handler`强转为`Controller`。

`HandlerAdapter`接口会根据处理器所实现接口的不同，对处理器进行适配，适配后即可对处理器进行执行。通过扩展处理器适配器，可以执行多种类型的处理器。

在得到请求时，中央调度器在`doDispatch()`方法中会遍历处理器适配器，并使用其`support()`方法，判断Handler是否与某个接口具有is-a关系。

中央调度器判断`Handler`是否与某个接口具有is-a关系：

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

常用的`HandlerAdapter`接口实现类有两种：

- SimpleControllerHandlerAdapter
- HttpReuqestHandlerAdapter

#### 2.2.1 SimpleControllerHandlerAdapter

**所有实现了`Controller`接口的处理器bean，均是通过此适配器进行适配并执行的。**

`SimpleControllerHandlerAdapter`的源码如下：

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

该方法用于处理用户提交的请求。通过调用Service层代码，实现对用户请求的计算响应，并将最终计算所得数据及要响应的页面，封装为一个`ModelAndView`对象返回给中央调度器。

#### 2.2.2 HttpRequestHandlerAdapter

**所有实现了`HttpRequestHandler`接口的处理器Bean，均通过此适配器进行适配、执行。**

`HttpRequestHandler`接口只有一个方法：

```java
void handleRequest(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException;
```

**该方法没有返回值，不能像`ModelAndView`一样将数据及目标视图封装为一个对象。但可以将数据直接放入到`request`、`session`等的域属性中，并由`request`或`response`完成到目标页面的跳转。**

`HttpRequestHandlerAdapter`的源码：

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

注意该适配器与`SimpleControllerHandlerAdapter`的`handle()`方法的不同，该适配器执行`handleRequest()`方法，但是返回`null`。

*以下内容在工程SpringMVC-10-httpRequestHandlerAdapter中。*

定义处理器，实现`HttpRequestHandler`接口：

```java
public class MyController implements HttpRequestHandler {

    @Override
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.setAttribute("message", "Hello SpringMVC World!");
        request.getRequestDispatcher("/WEB-INF/jsp/welcome.jsp").forward(request, response);
    }

}
```

当然，此时无需再配置前缀、后缀了，即在springmvc.xml中无需声明视图解析器的bean了。

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

处理器除了可以实现`Controller`/`HttpRequestHandler`接口外，还可以继承自一些其它的类来完成一些特殊的功能。

#### 2.3.1 继承AbstractController类

该抽象类继承了`WebContentGenerator`并实现了`Controller`接口：

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

`AbstractController`中定义的`handleRequest()`方法负责调用它：

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

`WebContentGenerator`类具有`supportedMethods`属性，可以设置支持的HTTP数据提交方式。默认支持GET、POST。

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

若处理器继承自`AbstractController`类，那么处理器就可以通过属性`supportedMethods`来限制HTTP请求提交方式了。例如，指定只支持POST的HTTP请求提交方式：

```xml
<bean id="/hello.do" class="pers.tavish.handlers.MyController">
    <property name="supportedMethods" value="POST" />
</bean>    
```

此时意味着该请求只能通过表单或Ajax请求方式进行提交，而不能通过地址栏、超链接、HTML标签中的src方式进行提交，因为这些提交方式都是GET请求。如果进行提交，则会发生请求方法不允许的405错误。

客户端浏览器常用的请求方式及提交方式：

请求方式|提交方式
-----|-----
表单请求 | 默认GET，可以指定POST
Ajax请求 | 默认GET，可以指定POST
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

`MultiActionController`类继承自`AbstractController`并实现了`LastModified`接口，所以继承`MultiActionController`的子类也可以设置HTTP请求提交方式。

```java
public class MultiActionController extends AbstractController implements LastModified {
    // ...
}
```

除此之外，继承自该类的处理器中可以定义任意多个处理方法。这些方法的方法名随意，访问修饰符为public，返回值为`ModelAndView`，包含参数`HttpServletRequest`和`HttpServletResponse`，并抛出`Exception`异常。

**注意：`MultiActionController`类已经在4.3版本中被标注为`@Deprecated`：**

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

这里需要配置处理器映射器`SimpleUrlHandlerMapping`，并且其中路径的写法要求必须以/xxx/\*的方式定义映射路径，其中\*为通配符，在访问时使用要访问的方法名代替。**这里的/xxx主要用于对访问路径进行限定。**

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

只所以通过在请求URL中写上方法名就可以访问到指定方法，是因为在`MultiActionController`类中有一个专门处理方法名称的解析器`MethodNameResolver`。该解析器作为一个属性出现，具有get和set方法。`MethodNameResolver`是一个接口，不同的解析器实现类，其对方法名在URL中的写法要求也是不同的。

**注意`MethodNameResolver`接口及其实现类也已被标记为`@deprecated`：**

>
@deprecated as of 4.3, in favor of annotation-driven handler methods

##### 2.3.1.1 InternalPathMethodNameResolver 方法名解析器（默认）

`MultiAnctionController`类具有一个默认的`MethodNameResolver`解析器：

```java
/** Delegate that knows how to determine method names from incoming requests */
private MethodNameResolver methodNameResolver = new InternalPathMethodNameResolver();
```

当未显示配置方法名解析器时，该解析器将作为默认方法名解析器对方法名进行解析。

该方法名解析器要求**方法名以URL中资源名称的身份出现**，即方法作为一种可以被请求的资源出现。要求写法为：**/xxx/方法名**。

##### 2.3.1.2 PropertiesMethodNameResolver 方法名解析器

该方法名解析器中的**方法名是作为URL资源名称中的一部分出现的**，即方法名并非单多作为一种资源名称。例如请求时写为/xxx_doFirst，则会访问xxx所映射的处理器的`doFirst()`方法。

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

此时可以通过`http://localhost:8080/SpringMVC-13-multiActionController-2/my_First.do`访问`doFirst()`方法，通过`http://localhost:8080/SpringMVC-13-multiActionController-2/my_Second.do`访问`doSecond()`方法。

##### 2.3.1.3 ParameterMethodNameResolver 方法名解析器

该方法名解析中的**方法名作为请求参数的值出现。**例如请求时可以写为`/foo?bar=doFirst`，则会访问xxx所映射的处理器的`doFirst()`方法。其中bar为该请求所携带的参数名，而doFirst则作为其参数值出现。

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

经过上述配置，现在可以通过请求`http://localhost:8080/SpringMVC-14-multiActionController-3/my.do?method=doFirst`访问`doFirst()`方法，`http://localhost:8080/SpringMVC-14-multiActionController-3/my.do?method=doSecond`访问`doSecond()`方法。

注意到`ParameterMehodNameResolver`类的源码中定义默认的参数名：

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

也就是说，当我们配置方法名解析器时，如果不设置`paramName`属性，那么我们将以类似`/my.do?action=doFirst`的形式来对相应的方法进行访问。

### 2.4 ModelAndView

`ModelAndView`，即模型与视图。我们可以通过`addObject()`方法向模型中添加数据，通过`setViewName()`方法向模型添加视图名称。

#### 2.4.1 模型

**模型的本质是一个HashMap。**

对源码进行跟踪：

`addObject()`方法：

```java
/**
 * Add an attribute to the model.
 * @param attributeName name of the object to add to the model
 * @param attributeValue object to add to the model (never {@code null})
 * @see ModelMap#addAttribute(String, Object)
 * @see #getModelMap()
 */
public ModelAndView addObject(String attributeName, Object attributeValue) {
    getModelMap().addAttribute(attributeName, attributeValue);
    return this;
}
```

`getModelMap()`方法：

```java
/**
 * Return the underlying {@code ModelMap} instance (never {@code null}).
 */
public ModelMap getModelMap() {
    if (this.model == null) {
        this.model = new ModelMap();
    }
    return this.model;
}
```

`ModelMap`类：该类是继承`LinkedHashMap`的。

```java
@SuppressWarnings("serial")
public class ModelMap extends LinkedHashMap<String, Object> {
    // ...
}
```

`ModelMap`的`addAttribute()`方法：

```java
/**
 * Add the supplied attribute under the supplied name.
 * @param attributeName the name of the model attribute (never {@code null})
 * @param attributeValue the model attribute value (can be {@code null})
 */
public ModelMap addAttribute(String attributeName, Object attributeValue) {
    Assert.notNull(attributeName, "Model attribute name must not be null");
    put(attributeName, attributeValue);
    return this;
}
```

由以上代码可知，通过`addObject()`方法向`ModelAndView`添加数据就是向`ModelMap`对象中添加数据，也就是向一个`HashMap`对象中添加数据。

#### 2.4.2 视图

通过`setViewName()`指定视图名称。这里的视图名称将会对应一个视图对象，这个试图对象将会被封装在`ModelAndView`中，传给视图解析器来解析，最终转换为相应的页面。需要注意的是，这里的`View`对象本质仅仅是一个`String`而已，在后续的步骤中还会继续对这个`View`对象进行进一步的封装。

`setViewName()`方法：

```java
/**
 * Set a view name for this ModelAndView, to be resolved by the
 * DispatcherServlet via a ViewResolver. Will override any
 * pre-existing view name or View.
 */
public void setViewName(String viewName) {
    this.view = viewName;
}
```

`setView()`方法：View是一个接口。

```java
/**
 * Set a View object for this ModelAndView. Will override any
 * pre-existing view name or View.
 */
public void setView(View view) {
    this.view = view;
}
```

上述这两个方法均会覆盖已有的`viewName`或`view`。

当然，若处理器方法返回的`ModelAndView`中并没有数据需要携带，则可以直接通过`ModelAndView`的带参构造器使用视图名称或视图对象构造`ModelAndView`：

```java
/**
 * Convenient constructor when there is no model data to expose.
 * Can also be used in conjunction with {@code addObject}.
 * @param viewName name of the View to render, to be resolved
 * by the DispatcherServlet's ViewResolver
 * @see #addObject
 */
public ModelAndView(String viewName) {
    this.view = viewName;
}

/**
 * Convenient constructor when there is no model data to expose.
 * Can also be used in conjunction with {@code addObject}.
 * @param view View object to render
 * @see #addObject
 */
public ModelAndView(View view) {
    this.view = view;
}
```

### 2.5 视图解析器 ViewResolver

视图解析器`ViewResolver`接口负责将处理结果生成`View`视图，常用的实现类有4个。

#### 2.5.1 InternalResourceViewResolver（默认）

*该视图解析器是中央调度器默认的视图解析器*。

该试图解析器用于完成对当前Web应用内部资源的封装与跳转。而对于内部资源的查找规则是，将`ModelAndView`中指定的视图名称与为视图解析器配置的前缀和后主相结合的方式，拼接为一个Web应用内部资源路径，其规则为：prefix + viewName + suffix。

假如我们要转发至`/WEB-INF/jsp/welcome.jsp`页面则可以进行如下配置：

```java
modelAndView.setViewName("welcome");
```

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp"/>
</bean>
```

当然，如果不配置前缀和后缀，那么在`setViewName()`方法中写明资源的全路径即可：

```java
modelAndView.setViewName("/WEB-INF/jsp/welcome.jsp");
```

该试图解析器会把处理器方法返回的模型属性都存放到对应的request中，然后将请求转发到目标URL。

#### 2.5.2 BeanNameViewResolver

`InternalResourceViewResolver`存在两个问题，使其使用很不灵活：

- 只可以完成将内部资源封装后的跳转，无法转向外部资源，如外部网页。
- 对于内部资源的定义，也只能定义一种格式的资源：存放于统一目录的同一文件类型的资源文件。

`BeanNameViewResolver`视图解析器，将资源封装为“Spring容器中注册的Bean实例”，`ModelAndView`通过设置视图名称为该bean的`id`属性值来完成对该资源的访问。所以在springmvc.xml中可以定义多个`View`视图bean，让处理器中`ModelAndView`通过对这些bean的`id`的引用来完成向`View`中封装资源的跳转。

*以下内容在工程SpringMVC-15-beanNameViewResolver中。*

修改springmvc.xml：

对于配置文件，需要做两处修改：

- 配置视图解析器的类型为`BeanNameViewResolver`
- 注册多个视图对象Bean
    + **RedirectView**：定义外部资源视图
    + **JstlView**：定义内部资源视图

```xml
<!-- 注册视图解析器 -->
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver" />    

<!-- 定义内部资源View对象 -->
<bean id="internalResource" class="org.springframework.web.servlet.view.JstlView">
    <property name="url" value="/WEB-INF/jsp/welcome.jsp" />
</bean>

<!-- 定义外部资源View对象 -->
<bean id="taobao" class="org.springframework.web.servlet.view.RedirectView">
    <property name="url" value="http://www.taobao.com" />
</bean>

<!-- 定义外部资源View对象 -->
<bean id="jd" class="org.springframework.web.servlet.view.RedirectView">
    <property name="url" value="http://www.jd.com" />
</bean>

<!-- 注册处理器 -->
<bean id="/my.do" class="pers.tavish.handlers.MyController" />    
```

修改处理器类：

由于只进行跳转，而不向`ModelAndView`中存入数据，所以可以简化代码：

```java
public class MyController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        // return new ModelAndView("taobao");
        // return new ModelAndView("jd");
        return new ModelAndView("internalResource");
    }

}
```

当我们返回的是`new ModelAndView("taobao")`或`return new ModelAndView("jd")`时，使用`my.do`进行访问则会相应的跳转到淘宝或京东。而当返回的是`return new ModelAndView("internalResource")`时则会跳转到welcome.jsp页面。

**注意：实际测试时发现如果想要跳转到welcome.jsp页面，需要导入两个关于`jstl`的jar包：**

- taglibs-standard-impl-1.2.5.jar
- taglibs-standard-spec-1.2.5.jar。

**否则会报出500错误：org.springframework.web.util.NestedServletException: Handler processing failed; nested exception is java.lang.NoClassDefFoundError: javax/servlet/jsp/jstl/core/Config。**

#### 2.5.3 XmlViewResolver 

当需要定义的View视图对象很多时，就会使springmvc.xml文件变得很臃肿。我们可以将这些`View`视图对象专门抽取出来，单独定义为一个xml文件，此时就需要使用`XmlViewResolver`了。

*以下内容在工程SpringMVC-16-xmlViewResolver中。*

将原springmvc.xml中`View`对象的注册bean全部复制到一个单独的xml文件中，当然这个xml文件需要Spring配置文件中的bean约束。

在src下创建myViews.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 定义内部资源View对象 -->
    <bean id="internalResource" class="org.springframework.web.servlet.view.JstlView">
        <property name="url" value="/WEB-INF/jsp/welcome.jsp" />
    </bean>
    
    <!-- 定义外部资源View对象 -->
    <bean id="taobao" class="org.springframework.web.servlet.view.RedirectView">
        <property name="url" value="http://www.taobao.com" />
    </bean>
    
    <!-- 定义外部资源View对象 -->
    <bean id="jd" class="org.springframework.web.servlet.view.RedirectView">
        <property name="url" value="http://www.jd.com" />
    </bean>
</beans>
```

修改spirngmvc.xml，将视图解析器配置为`XmlViewResolver`，并配置xml文件的位置及文件名：

```xml
<!-- 注册视图解析器 -->
<bean class="org.springframework.web.servlet.view.XmlViewResolver">  
      <property name="location" value="classpath:myViews.xml" />
</bean>

<!-- 注册处理器 -->
<bean id="/my.do" class="pers.tavish.handlers.MyController" /> 
```

如果不配置location属性，则默认的文件位置及名字为：/WEB-INF/views.xml。

#### 2.5.4 ResourceBundleViewResolver

对于View视图对象的注册，除了使用xml文件外也可以在properties文件中进行注册。只不过此时的视图解析器需要更换为`ResourceBundleViewResolver`。

该属性文件需要定义在类路径即src下，同时对于属性文件的写法要求如下：

```text
资源名称.(class)=封装资源的View全限定性类名
资源名称.url=资源路径
```

*以下内容在工程SpringMVC-17-resourceBundleViewResolver中。*

在src下创建myViews.properties：

```text
# 定义一个内部资源
myInternalView.(class)=org.springframework.web.servlet.view.JstlView
myInternalView.url=/WEB-INF/jsp/welcome.jsp
# 定义一个外部资源
taobao.(class)=org.springframework.web.servlet.view.RedirectView
taobao.url=http://www.taobao.com
# 定义一个外部资源
jd.(class)=org.springframework.web.servlet.view.RedirectView
jd.url=http://www.jd.com
```

修改springmvc.xml：

```xml
<!-- 注册视图解析器 -->
<bean class="org.springframework.web.servlet.view.ResourceBundleViewResolver">  
      <property name="basename" value="myViews" />
</bean>

<!-- 注册处理器 -->
<bean id="/my.do" class="pers.tavish.handlers.MyController" />    
```

如果不配置`basename`属性，则默认的文件名为views.properties。

该视图解析器还可以配置属性`basenames`，用以接收不同的属性文件。

#### 2.5.5 视图解析器的优先级

有时我们会应用一些视图解析器策略来解析视图名称，即同时存在多个视图解析器。那么这些视图解析器的优先级是怎样的呢？

视图解析器有一个order属性（`InternalResourceViewResolver`没有该属性），专门用于设置多个视图解析器的优先级（其余三个视图解析器的默认`order`均为`Integer.MAX_VALUE`）。**数字越小，优先级越高；数字相同，先注册的优先级高。**

通常，我们不为`InternalResourceViewResolver`指定优先级，即让其优先级是最低的。

*以下内容在工程SpringMVC-18-viewResolverOrder中。*

定义myViews.properties：

```text
# 定义一个外部资源
e-commerce.(class)=org.springframework.web.servlet.view.RedirectView
e-commerce.url=http://www.jd.com
```

定义myViews.xml：

```xml
<!-- 定义外部资源View对象 -->
<bean id="e-commerce" class="org.springframework.web.servlet.view.RedirectView">
    <property name="url" value="http://www.taobao.com" />
</bean>
```

定义处理器：

```java
@Override
public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
    return new ModelAndView("e-commerce");
}
```

修改springmvc.xml：通常在定义了多个视图解析器并制定了优先级时，我们会显式的注册`InternalResourceViewResolver`。

```xml
<!-- 注册视图解析器1，优先级第二高 -->
<bean class="org.springframework.web.servlet.view.ResourceBundleViewResolver">  
      <property name="basename" value="myViews" />
      <property name="order" value="3" />
</bean>

<!-- 注册视图解析器2，优先级最高 -->
<bean class="org.springframework.web.servlet.view.XmlViewResolver">  
      <property name="location" value="classpath:myViews.xml" />
      <property name="order" value="1" />
</bean>

<!-- 注册视图解析器3，优先级最低 -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" />

<!-- 注册处理器 -->
<bean id="/my.do" class="pers.tavish.handlers.MyController" />
```

此时由于`XmlViewResolver`优先级最高，所以访问`my.do`会跳转到淘宝。

## 第三章 SpringMVC注解式开发

**注解式开发是重点。**

SpringMVC注解式开发是指，处理器是基于注解的类的开发。对于每一个定义的处理器，无需在配置文件中逐个注册，只需在代码中通过对类与方法的注解，便可完成注册。即注解替换的是配置文件中对于处理器的注册部分。

### 3.1 第一个注解式开发程序

*以下内容在工程SpringMVC-19-primary-annotation中。*

#### 3.1.1 注册组件扫描器

这里的组件即处理器，需要指定处理器所在基本包：

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
        
    <!-- 注册组件扫描器 -->
    <context:component-scan base-package="pers.tavish.handlers.*" />   
        
</beans>
```

#### 3.1.2 定义处理器 

修改处理器类，加入注解，此时处理器类无需再实现`Controller`接口，方法名此时也不用必须为`handleRequest()`，当然，也无需抛出异常：

```java
@Controller // 表示当前类是一个处理器类
public class MyController {

    @RequestMapping("/hello.do") // 设置请求路径
    public ModelAndView salute(HttpServletRequest request, HttpServletResponse response) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("message", "Hello SpringMVC World!");
        mv.setViewName("/WEB-INF/jsp/welcome.jsp");
        return mv;
    }
}
```

我们可以设置多个请求路径，在`@RequestMapping`的value属性中可以写上一个数组：

```java
@Controller // 表示当前类是一个处理器类
public class MyController {

    @RequestMapping({"/hello.do", "/world.do"}) // 设置请求路径
    public ModelAndView salute(HttpServletRequest request, HttpServletResponse response) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("message", "Hello SpringMVC World!");
        mv.setViewName("/WEB-INF/jsp/welcome.jsp");
        return mv;
    }
}
```

### 3.2 请求映射规则的定义

通过注解`@RequestMapping`可以定义处理器对于请求的映射规则。该注解可以在方法上，也可以在类上，但意义不同。

#### 3.2.1 对请求URL命名空间的定义

`@RequestMapping`的`value`属性用于定义所匹配请求的URL。但对于注解在方法与类上，其value属性所指定的URL意义是不同的。

一个`@Controller`注解的类中可以定义多个处理器方法。当然，不同的处理器方法所匹配的URL是不同的。这些不同的URL被指定在注解于方法之上的`@RequestMapping`的`value`属性中。但若这些请求具有相同的部分，则可以被抽取导注解在类上的`@RequestMapping`的`value`属性中。此时的这个相同的URL部分被称为命名空间。

*以下内容在工程SpringMVC-20-requestMapping-value-namespace中。*

修改处理器：

这里的`/foo/bar`就是命名空间。

```java
@Controller  
@RequestMapping("/foo/bar")
public class MyController {

    @RequestMapping("/some.do")  
    public ModelAndView doSome(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/some.jsp");
    }
    
    @RequestMapping("/other.do")  
    public ModelAndView doOther(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/other.jsp");
    }
}
```

添加some.jsp，other.jsp：

```html
<body>

Some Page.

</body>
```

```html
<body>

Other Page.

</body>
```

现在`http://localhost:8080/SpringMVC-20-requestMapping-value-namespace/foo/bar/some.do`将访问`doSome()`方法，`http://localhost:8080/SpringMVC-20-requestMapping-value-namespace/foo/bar/other.do`将访问`doOther()`方法。

当我们定义命名空间时，就可以不在方法的访问路径前加/了。例如，当定义了命名空间`@RequestMapping("/foo/bar")`，此时将访问路径定义为`@RequestMapping("/some.do")`或`@RequestMapping("some.do")`都可以。这里不再适用1.5节中的相对路径的相关解释。因为如果不以/开头，命名空间会自动补充/，如果有，则正常访问。但我们推荐使用带/的方式。

```java
@Controller
@RequestMapping("/foo/bar")
public class MyController {

    @RequestMapping({ "/some.do", "some2.do" })
    public ModelAndView doSome(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/some.jsp");
    }

    @RequestMapping("/other.do")
    public ModelAndView doOther(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/other.jsp");
    }
}
```

#### 3.2.2 请求URL中通配符的应用

在处理器方法所映射的URL中，可以使用通配符。

##### 3.2.2.1 资源名称中使用通配符

*以下内容在工程SpringMVC-21-requestMapping-value-wildcard中。*

在请求的资源名称中使用通配符，表示请求资源名称中只要包含**指定的字符**即可完成匹配。

例如，`/some*.do`表示只要请求的资源名称以some开头即可。

修改处理器：

```java
@Controller
@RequestMapping("/foo/bar")
public class MyController {

    @RequestMapping("/some*.do")
    public ModelAndView doSome(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/some.jsp");
    }

    @RequestMapping("/*other.do")
    public ModelAndView doOther(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/other.jsp");
    }
}
```

##### 3.2.2.2 资源路径中使用通配符

*以下内容在工程SpringMVC-21-requestMapping-value-wildcard-2中。*

在资源路径中使用通配符有两种用法：

- 路径级数的精确匹配：`/xxx/*/show.do`表示在show.do的资源名称前面只能有两级路径，第一级必须为/xxx，而第二级随意。
- 路径级数的可变匹配：`/xxx/**/show.do`表示在show.do的资源名称前面必须以/xxx开头，而是否存在其它级路径，包含几级以及各级的名称均随意。

```java
@Controller
@RequestMapping("/foo/bar")
public class MyController {

    @RequestMapping("/*/some.do")
    public ModelAndView doSome(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/some.jsp");
    }

    @RequestMapping("/**/other.do")
    public ModelAndView doOther(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/other.jsp");
    }
}
```

#### 3.2.3 对请求提交方式的限定

`@RequestMapping`中有一个属性`method`，用于对被注解方法所处理请求的提交方式进行限定。

method属性的取值为`RequestMap`枚举常量，常用的为`RequestMethod.GET`与`RequestMethod.POST`，分别为提交方式的匹配规则为GET和POST提交。

如果不指定该属性，则对请求的提交方式无要求。

*以下内容在工程SpringMVC-23-requestMapping-method中。*

修改处理器：

```java
@Controller
@RequestMapping("/foo/bar")
public class MyController {

    @RequestMapping(value = "/some.do", method = RequestMethod.GET)
    public ModelAndView doSome(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/some.jsp");
    }

    @RequestMapping(value = "/other.do", method = RequestMethod.POST)
    public ModelAndView doOther(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/other.jsp");
    }
}
```

修改index页面：

```html
<body>

    <!-- 正常访问 -->
    <a href="foo/bar/some.do">跳转到some页面，GET方法</a>
    <br>
    <!-- 405 – Method Not Allowed -->
    <a href="foo/bar/other.do">跳转到other页面，GET方法</a>
    <br>

    <!-- 405 – Method Not Allowed -->
    <form action="foo/bar/some.do" method="POST">
        <input type="submit" value="跳转到some页面，POST方法">
    </form>
    <!-- 正常访问 -->
    <form action="foo/bar/other.do" method="POST">
        <input type="submit" value="跳转到other页面，POST方法">
    </form>
    <br>

</body>
```

#### 3.2.4 对请求中携带参数的定义

@RequestMapping中params属性定义了请求中必须携带的参数的要求。

示例：

- **@RequestMapping(value="/xxx.do", params={"name", "age"})**：要求请求中必须携带请求参数name与age。
- **@RequestMapping(value="/xxx.do", params={"!name", "age"})**：要求请求中必须携带请求参数age，但必须不能携带请求参数name。
- **@RequestMapping(value="/xxx.do", params={"name=foo", "age=bar"})**：要求请求中必须携带请求参数name，且其值必须为foo；必须携带请求参数age，且其值必须为bar。
- **@RequestMapping(value="/xxx.do", params="name!=foo")**：要求请求中必须携带请求参数name，且其值必须不能为foo。

*以下内容在工程SpringMVC-24-requestMapping-params中。*

修改处理器：

```java
@Controller
@RequestMapping("/foo/bar")
public class MyController {

    @RequestMapping(value = "/some.do", params = { "name", "age=20" })
    public ModelAndView doSome(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/some.jsp");
    }

    @RequestMapping(value = "/other.do", params = { "!name", "age!=20" })
    public ModelAndView doOther(HttpServletRequest request, HttpServletResponse response) {
        return new ModelAndView("/WEB-INF/jsp/other.jsp");
    }
}
```

做了上述修改后，如果在访问时参数使用不正确会发生404错误。

### 3.3 对请求中携带参数的定义

处理器方法常用以下五类参数：

- HttpServletRequest
- HttpServletResponse
- HttpSession
- 用于承载数据的Model
- 请求中所携带的参数

这些参数会在系统调用时由系统自动赋值，即我们可在方法内直接使用。

下面说明处理器是如何接收请求中携带的请求参数的。

#### 3.3.1 逐个参数接收

逐个参数接收只要保证请求参数名与该请求处理方法的参数名相同即可。

*以下内容在工程SpringMVC-25-receiveParameters-property中。*

修改处理器：

```java
@Controller  
@RequestMapping("/test")  
public class MyController {

    @RequestMapping("/register.do")  
    public ModelAndView register(String name, int age) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("myname", name);
        mv.addObject("myage", age);
        mv.setViewName("/WEB-INF/jsp/show.jsp");
        return mv;
    }
}
```

修改index页面：

```html
<body>
    <form action="test/register.do" method="POST">
        <label>
            姓名：
            <input type="text" name="name" />
        </label>
        <br>
        <label>
            年龄：
            <input type="text" name="age" />
        </label>
        <br>
        <input type="submit" value="注册" />
    </form>
</body>
```

设置show.jsp页面：

```html
<body>
    name=${myname}
    <br>
    age=${myage}
    <br>
</body>
```

这里表单提交的age值会进行自动类型转换。

#### 3.3.2 请求参数中文乱码问题

对于上一节的请求参数，如果含有中文，则会出现中文乱码问题。

Spring对于请求参数中的中文乱码问题给出了专门的字符集过滤器：org.springframework.web.filter.CharacterEncodingFilter，这个类位于spring-web-4.3.9.RELEASE.jar中。

在web.xml中注册字符集过滤器即可解决Spring的请求参数的中文乱码问题。**最好将该过滤器注册在其它过滤器之前，因为过滤器的执行是按照其注册顺序执行的。**

*直接在工程SpringMVC-25-receiveParameters-property上修改。*

注册字符集过滤器：

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

源码分析：

`CharacterEncodingFilter`类具有以下属性：

- **encoding**：String类型，指定字符集
- **forceEncoding**：boolean类型，指定是否对请求和响应强制使用指定的字符集。
    + **true**：请求和响应的字符集将被这里指定的字符集覆盖。
    + **false**：如果代码中已对请求和响应的字符集进行了设置，则不进行覆盖；如果没有设置，则使用这里指定的字符集。
- **forceRequestEncoding**：boolean类型，指定是否对请求强制使用指定的字符集。true/false的意义与与forceEncoding相同。
- **forceResponseEncoding**：boolean类型，指定是否对响应强制使用指定的字符集。true/false的意义与与forceEncoding相同。

对`forceEncoding`进行配置相当于同时配置`forceRequestEncoding`和`forceResponseEncoding`。

```java
/**
 * Set whether the configured {@link #setEncoding encoding} of this filter
 * is supposed to override existing request and response encodings.
 * <p>Default is "false", i.e. do not modify the encoding if
 * {@link javax.servlet.http.HttpServletRequest#getCharacterEncoding()}
 * returns a non-null value. Switch this to "true" to enforce the specified
 * encoding in any case, applying it as default response encoding as well.
 * <p>This is the equivalent to setting both {@link #setForceRequestEncoding(boolean)}
 * and {@link #setForceResponseEncoding(boolean)}.
 * @see #setForceRequestEncoding(boolean)
 * @see #setForceResponseEncoding(boolean)
 */
public void setForceEncoding(boolean forceEncoding) {
    this.forceRequestEncoding = forceEncoding;
    this.forceResponseEncoding = forceEncoding;
}
```

`doFilterInternal()`方法：

```java
@Override
protected void doFilterInternal(
        HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
        throws ServletException, IOException {

    String encoding = getEncoding();
    // 如果没有配置encoding，则不对请求/响应进行字符集的设置。
    if (encoding != null) {

        // 根据配置情况对请求和响应的字符集进行设置。
        if (isForceRequestEncoding() || request.getCharacterEncoding() == null) {
            request.setCharacterEncoding(encoding);
        }
        if (isForceResponseEncoding()) {
            response.setCharacterEncoding(encoding);
        }
    }
    filterChain.doFilter(request, response);
}
```

#### 3.3.3 校正请求参数名@RequestParam

所谓校正请求参数名，是指若请求URL所携带的参数名称与处理方法中指定参数名不相同时，则需在处理方法参数前添加一个注解`@RequestParams`，指定请求URL所携带参数的名称。该注解加在方法参数上。

`@RequestParams`具有四个属性：

- **value**：指定请求参数的名称。
- **name**：value的别名。
- **required**：指定该注解所修饰的参数是否是必须的，boolean类型。
    + true：则表示请求中所携带的参数中必须包含当前参数。
    + false：有没有均可以。
- **defaultValue**：指定当前参数的默认值。若请求URL中没有给出当前参数，则当前方法参数将取该默认值。即使required为true，且URL中没有给出当前参数，该处理器方法参数会自动取该默认值，而不会报错。

*以下内容在工程SpringMVC-26-receiveParameters-property-2中。*

修改index页面，将参数名修改为rname和rage：

```html
<body>
    <form action="test/register.do" method="POST">
        <label>
            姓名：
            <input type="text" name="rname" />
        </label>
        <br>
        <label>
            年龄：
            <input type="text" name="rage" />
        </label>
        <br>
        <input type="submit" value="注册" />
    </form>
</body>
```

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/register.do")
    public ModelAndView register(@RequestParam("rname") String name, @RequestParam("rage") int age) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("myname", name);
        mv.addObject("myage", age);
        mv.setViewName("/WEB-INF/jsp/show.jsp");
        return mv;
    }
}
```

此时提交表单依然可以正常跳转到show.jsp并显示我们提交的参数。

经过上述设置后，我们可以将rname校正为name，rage校正为age。但是此时如果提交的参数就是name、age，则会报错。例如使用参数`?rname=test&age=23`进行访问会发生400错误，信息为Required String parameter 'rage' is not present。

另外，此时如果要使用注解`@RequestMapping`的params参数对请求参数进行限定，则只能使用`@RequestParam`中定义的参数。

例如我们进行如下限定：

```java
@RequestMapping(value = "/register.do", params= {"name"} )
public ModelAndView register(@RequestParam("rname") String name, @RequestParam("rage") String age) {
    // ...
}
```

此时`@RequestMapping`要求我们必须传递名为name的参数，但是`@RequestParam`要求我们传递名为rname的参数。此时无论使用name还是rname都会报错：

- 404：?rname=test&rage=23
- 400：?name=test&rage=23

将`params= {"name"}`改为`params= {"rname"}`，再使用`?rname=test&rage=23`参数就可以正常访问了。此时意义为，请求必须提交一个名为rname的参数，然后rname和rage会被校正为name和age。

总而言之，这两个注解在使用时其意义不能冲突。

#### 3.3.4 参数整体接收

将处理器方法的参数定义为一个对象，只要保证请求参数名与这个对象的属性同名即可。

*以下内容在工程SpringMVC-27-receiveParameters-object中。*

创建实体类：

```java
public class Student {
    private String name;
    private int age;

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

    @Override
    public String toString() {
        return "Student [name=" + name + ", age=" + age + "]";
    }
}
```

修改处理器，直接接收Student对象，并将该对象存入ModelAndView。

```java
@Controller  
@RequestMapping("/test")  
public class MyController {

    @RequestMapping("/register.do")  
    public ModelAndView register(Student student) {
        ModelAndView mv = new ModelAndView();
        mv.addObject("student", student);
        mv.setViewName("/WEB-INF/jsp/show.jsp");
        return mv;
    }
}
```

确保表单参数名与Student类的属性名相同：

```html
<body>
    <form action="test/register.do" method="POST">
        <label>
            姓名：
            <input type="text" name="name" />
        </label>
        <br>
        <label>
            年龄：
            <input type="text" name="age" />
        </label>
        <br>
        <input type="submit" value="注册" />
    </form>
</body>
```

show页面直接使用EL表达式输出这个Student对象：

```html
<body>
    student=${student}
</body>
```

**想要这样接收参数，必须保证表单参数名与实体类的属性名相同。**

#### 3.3.5 域属性参数的接收

所谓域属性，即对象属性。当请求参数中的数据为某类对象域属性的属性值时，要求请求参数名为“域属性名.属性”。

*以下内容在工程SpringMVC-28-receiveParameters-objectProperty中。*

定义School类：

```java
public class School {
    private String name;
    private String address;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }

    @Override
    public String toString() {
        return "School [name=" + name + ", address=" + address + "]";
    }

}
```

修改Student类，加入属性school：

```java
public class Student {
    private String name;
    private int age;
    private School school;

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

    public School getSchool() {
        return school;
    }

    public void setSchool(School school) {
        this.school = school;
    }

    @Override
    public String toString() {
        return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
    }
}
```

修改index页面：

```html
<body>
    <!-- 表单参数名与实体类属性名必须相同 -->
    <form action="test/register.do" method="POST">
        <label>
            姓名：
            <input type="text" name="name" />
        </label>
        <br>
        <label>
            年龄：
            <input type="text" name="age" />
        </label>
        <br>
        <!-- 表单参数名与实体类域属性的属性值必须相同 -->
        <label>
            学校：
            <input type="text" name="school.name" />
        </label>
        <br>
        <label>
            地址：
            <input type="text" name="school.address" />
        </label>
        <br>
        <input type="submit" value="注册" />
    </form>
</body>
```

#### 3.3.6 路径变量@PathVariable

对于处理器方法中所接收的请求参数，可以来自于请求中所携带的参数，也可以来自于请求的URL中所携带的变量，即路径变量，此时需要使用`@PathVariable`。

`@PathVariable`在不指定参数的情况下，默认其参数名，即路径变量名与用于接收其值的参数名相同。若不同，则也可以通过`@PathVariable`的参数指出路径变量名称。

*以下内容在工程SpringMVC-29-receiveParameters-pathVariable中。*

修改index页面，不再需要index页面中的表单了：

```html
<body>
    index page
</body>
```

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/{pname}/{age}/register.do")
    public ModelAndView register(
        @PathVariable("pname") String name, 
        @PathVariable int age) {
        
        ModelAndView mv = new ModelAndView();
        mv.addObject("myname", name);
        mv.addObject("myage", age);
        mv.setViewName("/WEB-INF/jsp/show.jsp");
        return mv;
    }
}
```

修改show页面：

```html
<body>
    name = ${myname}<br>
    age = ${myage}<br>
</body>
```

通过地址栏访问：`http://localhost:8080/SpringMVC-29-receiveParameters-pathVariable/test/zs/24/register.do`会成功跳转并显示信息。

### 3.4 处理器方法的返回值

使用`@Controller`注解的处理器的处理器方法，有4种常用的返回值类型：

- ModelAndView
- String
- void
- 自定义对象

根据不同情况使用不同的返回值。


#### 3.4.1 返回ModelAndView

若处理器方法处理完后需要跳转到其它资源，且又要在跳转的资源间传递数据，此时处理器方法返回`ModelAndView`比较好。当然，若要返回`ModelAndView`，则处理器方法种需要定义`ModelAndView`对象。

在使用时，若该处理器方法只是进行跳转而不传递数据，或者只是传递数据而并不向任何资源跳转（如对页面Ajax异步响应），此时若返回`ModelAndView`则将总是有一部分多余，即此时返回`ModelAndView`将不合适。

#### 3.4.2 返回String

处理器方法返回的字符串可以指定逻辑视图名，通过视图解析器将其转换为物理视图地址。

##### 3.4.2.1 返回内部资源逻辑视图名

若要跳转的资源为内部资源，则视图解析器可以使用`InternalResourceViewResolver`。此时处理器方法返回的字符串就是跳转页面的文件名去掉扩展名后的部分。这个字符串与视图解析器种的prefix、suffix相结合，即可形成要访问的URL。

*以下内容在工程SpringMVC-30-returnString-internal中。*

注册视图解析器：

```xml
<!-- 注册InternalResourceViewResolver -->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/" />
    <property name="suffix" value=".jsp" />
</bean>
```

修改处理器，这里我们不使用`ModelAndView`存储数据，而使用ServletAPI：

```java
@Controller  
@RequestMapping("/test")  
public class MyController {

    @RequestMapping("/register.do")  
    public String register(HttpServletRequest request, Student student) {
        System.out.println(student);
        request.setAttribute("student", student);
        return "show";
    }
}
```

定义index页面表单：

```html
<body>
    <!-- 表单参数名与实体类属性名必须相同 -->
    <form action="test/register.do" method="POST">
        <label>
            姓名：
            <input type="text" name="name" />
        </label>
        <br>
        <label>
            年龄：
            <input type="text" name="age" />
        </label>
        <br>
        <input type="submit" value="注册" />
    </form>
</body>
```

当然，如果我们不配置视图解析器的前缀和后缀，可以直接返回物理视图地址：

```java
@Controller  
@RequestMapping("/test")  
public class MyController {

    @RequestMapping("/register.do")  
    public String register(HttpServletRequest request, Student student) {
        System.out.println(student);
        request.setAttribute("student", student);
        return "/WEB-INF/jsp/show.jsp";
    }
}
```

##### 3.4.2.2 返回View对象名

若要跳转的资源为外部资源，则视图解析器可以使用`BeanNameViewResolver`，然后再配置文件中定义外部资源视图`View`对象。此时处理器方法返回的字符串就是要跳转资源视图`View`名称。当然这些视图`View`对象也可以是内部资源视图`View`对象。

*以下内容在工程SpringMVC-31-returnString-external中。*

注册视图解析器并定义资源：

```xml
<!-- 注册视图解析器 -->
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver" />    

<!-- 定义内部资源View对象 -->
<bean id="internalResource" class="org.springframework.web.servlet.view.JstlView">
    <property name="url" value="/WEB-INF/jsp/welcome.jsp" />
</bean>

<!-- 定义外部资源View对象 -->
<bean id="taobao" class="org.springframework.web.servlet.view.RedirectView">
    <property name="url" value="http://www.taobao.com" />
</bean>

<!-- 定义外部资源View对象 -->
<bean id="jd" class="org.springframework.web.servlet.view.RedirectView">
    <property name="url" value="http://www.jd.com" />
</bean>
```

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/goto.do")
    public String doSome() {
        return "jd";
    }

}
```

#### 3.4.3 返回void

对于处理器方法返回void的应用场景主要有两种：

- 通过ServletAPI传递数据并完成跳转
- Ajax响应

##### 3.4.3.1 通过ServletAPI传递数据并完成跳转

通过在处理器方法的参数中放入ServletAPI参数，来完成资源跳转时索要传递的数据及跳转。
可在方法中放入`HttpServletRequest`或`HttpSession`，使方法中可以之将数据放入到`request`、`session`的域中，也可通过`request.getServletContext()`获取到`ServletContext`，从而将数据放入到`application`的域中。
可在方法参数中放入`HttpServletRequest`与`HttpServletResponse`，使方法可以完成请求转发与重定向。

*以下内容在工程SpringMVC-32-returnVoid-servletAPI中。*

修改处理器，使用`request`、`response`进行请求转发：

```java
@Controller  
@RequestMapping("/test")  
public class MyController {

    @RequestMapping("/register.do")  
    public void register(HttpServletRequest request, HttpServletResponse response, Student student) {
        request.setAttribute("student", student);
        try {
            request.getRequestDispatcher("/WEB-INF/jsp/show.jsp").forward(request, response);
        } catch (ServletException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

删除配置文件中的视图解析器：

```xml
<!-- 注册组件扫描器 -->
<context:component-scan base-package="pers.tavish.handlers" />
```

##### 3.4.3.2 Ajax响应

若处理器对请求处理后，无需跳转到其他任何资源，此时可以让处理器返回void。例如对于Ajax的异步请求响应。

*以下内容在工程SpringMVC-33-returnVoid-Ajax中*。

导入Jar包：

由于本工程中服务端向浏览器传回的是JSON数据，所以需要一个工具类将字符串包转为JSON格式，这里使用JSON-lib。

根据JSON-lib的官方说明，要使用json-lib-2.4-jdk15这个包需要至少需要以下Jar包：

- commons-lang 2.5
- commons-beanutils 1.8.0
- commons-collections 3.2.1
- commons-logging 1.1.1
- ezmorph 1.0.6

引入jQuery库：

由于本工程要使用jQuery的ajax()方法提交Ajax请求，所以需要引入jQuery的库。在WebConten下新建一个Folder，命名为js，将jQuery库文件放入其中，这里使用jquery-3.3.1。

定义index页面：

index页面由两部分组成：

- **&lt;button/&gt;**：用于提交Ajax请求。
- **&lt;script/&gt;**：用于处理Ajax请求。

```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort()
            + path + "/";
%>
<!DOCTYPE html>
<html>
<head>
<script type="text/javascript" src=js/jquery-3.3.1.js charset="utf-8"></script>
<script type="text/javascript">
    $(function() {
        $("button").click(function() {
            $.ajax({
                url : "test/myajax.do",
                data : {
                    name : "Tavish",
                    age : 23
                },
                success : function(data) {
                    var myJson = eval("(" + data + ")");
                    alert(myJson.pname + " " + myJson.page);
                }
            });
        });
    });
</script>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Insert title here</title>
</head>
<body>
    <button>提交Ajax请求</button>
</body>
</html>
```

修改处理器：

处理器对于Ajax请求所提交的参数，可以使用逐个接收的方式，也可以使用对象的方式整体接收。只要保证Ajax请求参数与接收的对象类型属性同名。

```java
@Controller
@RequestMapping("/test")
public class MyController {

    // 以对象方式接收参数
    @RequestMapping("/myajax.do")
    public void doAjax(HttpServletResponse response, Student student) throws IOException {

        // 将数据存放到map中，这里将name变为pname，age变为page
        Map<String, Object> map = new HashMap<>();
        map.put("pname", student.getName());
        map.put("page", student.getAge());

        // 将map转为JSON
        JSONObject myJson = JSONObject.fromObject(map);
        String jsonString = myJson.toString();

        // 将数据发回客户端
        PrintWriter out = response.getWriter();
        out.print(jsonString);
        out.close();
    }
}
```

要逐个接收只需将参数定义为`String name, int age`并将其直接放入map即可。

删除视图页面：

由于是服务端直接向浏览器发回数据，所以也就无需视图页面了，所以删除jsp目录下的show.jsp。

此时访问index.jsp页面，单击“提交Ajax请求”按钮，浏览器就会直接弹出警告框“Tavish 23”。

#### 3.4.3 返回Object

处理器方法也可以返回`Object`对象。但返回的这个`Object`对象不是作为逻辑视图出现的，而是作为直接在页面显示的数据出现的。

**环境搭建**

由于返回`Object`数据通常都是将数据转化为JSON对象后传递给浏览器页面的。这里使用Jackson完成将数据转化为JSON对象这个过程。

使用Jar包如下：*不再需要JSON-lib的相关Jar包。但注意不要将commons-logging也删了*

- jackson-annotations-2.9.6
- jackson-core-2.9.6
- jackson-databind-2.9.6

将Object数据转化为JSON数据需要由Http消息转换器`HttpMessageConverter`完成。而转换器的开启需要进行配置，这里需要配置`&lt;mvc:annotation-driven /&gt;`。

当Spring容器进行初始化过程中，在`&lt;mvc:annotation-driven /&gt;`处创建注解驱动时，默认创建了7个`HttpMessageConverter`对象。也就是说，这里进行配置的目的就是为了让容器为我们创建`HttpMessageConverter`对象。

##### 3.4.3.1 返回数值型对象

*以下内容在工程SpringMVC-34-returnObject-number中。*

修改index页面，这里不需要向服务器传输数据：

```html
<head>
<script type="text/javascript" src=js/jquery-3.3.1.js charset="utf-8"></script>
<script type="text/javascript">
    $(function() {
        $("button").click(function() {
            $.ajax({
                url : "test/myajax.do",
                success : function(data) {
                    alert(data);
                }
            });
        });
    });
</script>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Insert title here</title>
</head>
<body>
    <button>提交Ajax请求</button>
</body>
```

修改处理器：

在处理器方法上需要添加`@ResponseBody`，将数据放入到响应体中。

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/myajax.do")
    @ResponseBody
    public Object doAjax()  {
        return 123.456d;
    }
}
```

修改配置文件：

注册mvc的注解驱动。

```xml
<!-- 注册组件扫描器 -->
<context:component-scan base-package="pers.tavish.handlers" />

<!-- 注册mvc注解驱动 -->
<mvc:annotation-driven />
```

##### 3.4.3.2 返回字符串对象

若要返回非中文字符串，则将上一节返回数值型对象的返回值直接改为字符串即可。但若返回的字符串中有中文字符，则接收放页面将会出现乱码（即使我们配置了字符集过滤器，并强制使用utf-8，也会乱码）。此时需要使用`@RequestMapping`的`produces`属性指定字符集。

该属性的意义是指，被访问时要返回什么样的结果（设置MIME）。

*以下内容在工程SpringMVC-35-returnObject-String中。*

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping(value = "/myajax.do", produces = "text/html;charset=utf-8")
    @ResponseBody 
    public Object doAjax() {
        return "测试";
    }
}
```

##### 3.4.3.3 返回自定义类型对象

返回自定义类型对象时，不能以对象的形式直接返回给客户端浏览器，而是将对象转换为JSON格式的数据再进行发送。由于转换器底层使用Jackson的转换方式将对象转为JSON数据，所以这里我们就需要真正导入Jackson的Jar包了。

*以下内容在工程SpringMVC-36-returnObject-custom中。*

定义Student类：

```java
public class Student {
    private String name;
    private int age;

    public Student(String name, int age) {
        this.name = name;
        this.age = age;
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

    @Override
    public String toString() {
        return "Student [name=" + name + ", age=" + age + "]";
    }
}
```

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/myajax.do")
    @ResponseBody // 将返回的数据放回到相应体中
    public Object doAjax() {
        return new Student("张三", 23);
    }
}
```

修改index页面：

```html
<head>
<script type="text/javascript" src=js/jquery-3.3.1.js charset="utf-8"></script>
<script type="text/javascript">
    $(function() {
        $("button").click(function() {
            $.ajax({
                url : "test/myajax.do",
                success : function(data) {
                    alert(data.name + " " + data.age);
                }
            });
        });
    });
</script>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Insert title here</title>
</head>
<body>
    <button>提交Ajax请求</button>
</body>
```

##### 3.4.3.4 返回Map集合

*以下内容在工程SpringMVC-37-returnObject-map中。*

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/myajax.do")
    @ResponseBody // 将返回的数据放回到相应体中
    public Object doAjax() {
        Map<String, Student> map = new HashMap<>();
        map.put("student1", new Student("张三", 23));
        map.put("student2", new Student("李四", 24));
        return map;
    }
}
```

修改index页面：

```html
<head>
<script type="text/javascript" src=js/jquery-3.3.1.js charset="utf-8"></script>
<script type="text/javascript">
    $(function() {
        $("button").click(function() {
            $.ajax({
                url : "test/myajax.do",
                success : function(data) {
                    alert(data.student1.name + " " + data.student1.age);
                    alert(data.student2.name + " " + data.student2.age);
                }
            });
        });
    });
</script>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Insert title here</title>
</head>
<body>
    <button>提交Ajax请求</button>
</body>
```

##### 3.4.3.5 返回List集合

*以下内容在工程SpringMVC-38-returnObject-list中。*

修改处理器：

```java
@Controller
@RequestMapping("/test")
public class MyController {

    @RequestMapping("/myajax.do")
    @ResponseBody // 将返回的数据放回到相应体中
    public Object doAjax() {
        List<Student> list = new ArrayList<>();
        list.add(new Student("张三", 23));
        list.add(new Student("李四", 24));
        list.add(new Student("王五", 25));
        return list;
    }
}
```

修改index页面：

```html
<head>
<script type="text/javascript" src=js/jquery-3.3.1.js charset="utf-8"></script>
<script type="text/javascript">
    $(function() {
        $("button").click(function() {
            $.ajax({
                url : "test/myajax.do",
                success : function(data) {
                    $(data).each(function(index) {
                        alert(data[index].name + " " + data[index].age);
                    });
                }
            });
        });
    });
</script>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Insert title here</title>
</head>
<body>
    <button>提交Ajax请求</button>
</body>
```