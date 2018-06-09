[TOC]

# ITcast---Struts2

>
使用版本Struts2.5.13

## 概述

Struts2是在WebWork2基础上发展而来的。和Struts1一样，Struts2也属于MVC框架。不过Struts2的编码风格和Struts1是几乎完全不一样的。

- 在软件设计上Struts2没有像Struts1那样跟Servlet API和Struts API有着紧密的耦合，Struts2的应用可以不依赖于Seervlet API和Struts API。这种设计属于无侵入式设计，而Struts1属于侵入式设计。
- Struts2提供了拦截器，利用拦截器可以进行AOP编程，实现如权限拦截等功能。
- Struts2提供了类型转换器，我们可以把特殊的请求参数转换成想要的类型。在Struts1中，如果我们要实现先沟通的功能，就必须向Struts1的底层实现BeanUtil注册类型转换器才行。
- Struts2提供支持多种表现层技术，如JSP、freeMarker、Velocity等。
- Struts2输入校验可以对指定方法进行校验。
- 提供了全局范围、包范围和Action范围的国际化资源文件管理实现。

在Struts1.x中，Struts框架是通过Servlet启动的。在Struts2中，Struts框架是通过Filter启动的。

    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

在Struts2.1.3以后FilterDispatcher已经标注为过时：

    <filter-class>org.apache.struts2.dispatcher.FilterDispatcher</filer-class>

在StrutsPrepareAndExecuteFilter的init()方法会读取类路径下默认的配置文件Struts.xml完成初始化操作。

Struts2读取到struts.xml的内容后，以JavaBean的形式存放在内存中，以后Struts2对用户的每次请求处理都将使用内存中的数据，而不是每次都读取struts.xml文件。 

**
struts.xml配置中的包介绍：
**

    <package name="" namespace="" extends="">
        <action name="" class="" method="">
            <result name=""></result>
        </action>
    </package>

Struts2框架中使用包来管理Action，包的作用和java中的类包笑死，它主要用于管理一组业务功能相关的action。在实际应用中，我们应该把一组业务功能相关的Action放在同一个包下。
配置包时必须指定name属性，该name属性可以取任意名，但必须唯一，它不对应java的类包，如果其他包要继承该包，必须通过该属性进行引用。包的namespace属性用于定义该包的命名空间，命名空间作为访问该包下Action路径的一部分。result定义了视图的路径。
通常每个包都应该继承struts-default包，因为Struts2很多核心功能都是拦截器来实现。struts-default定义了这些拦截器和Result类型。当包继承了struts-default时才能使用Struts2提供的核心功能。包还可以通过abstract="true"定义为抽象包，抽象包中不能包含action。

## Struts2的使用

>以下内容的源代码在itcast-Struts2/Struts2

### Action名称的搜索顺序

**
获得请求路径的URL，例如URL是：http://server/Struts2/path1/path2/path3/test.action
**

1. 首先寻找namespace为/path1/path2/path3的package，如果不存在这个package则执行步骤2。如果存在这个pacage，则在这个package中寻找名字为test的action。当在该package下找不到名为test的action，则到默认namespace的package里寻找名为test的action。如果还找不到则提示页面找不到该action。
2. 寻找namespace为/path1/path2的package，如果不存在这个package，则执行步骤3。如果存在这个package，则在这个package中寻找名为test的action。当在该package下找不到名为test的action，则到默认namespace的package里寻找名为test的action。如果还找不到则提示页面找不到该action。
3. 寻找namespace为/path1的package，如果不存在这个package，则执行步骤4。如果存在这个package，则在这个package中寻找名为test的action。当在该package下找不到名为test的action，则到默认namespace的package里寻找名为test的action。如果还找不到则提示页面找不到该action。
4. 寻找namespace为/的package，如果存在这个package，则在这个package中寻找名为test的action。当在该package下找不到名为test的action，则到默认namespace的package里寻找名为test的action。如果还找不到则提示页面找不到该action。

默认命名空间的package：没有namespace属性，或namespace属性为空字符串。

    <package name="tavish" namespace="" extends="struts-default">
        ...
    <package>

Struts2默认的处理后缀是.action。

### Struts2转发

**
只进行转发的action。
**

当访问addUI这个action时就会转发到result中配置的JSP。此时不配置class和method。

    <action name="addUI">
        <result>/WEB-INF/page/employeeAdd.jsp</result>
    </action>
Struts1写法：

    <action path="/control/employee/addUI" forward="/WEB-INF/page/employeeAdd.jsp">

**
Action配置的各项默认值：
**

    <package name="" namespace="" extends="">
        <action name="" class="" method="">
            <result name=""></result>
        </action>
    </package>

- 如果没有为action指定class，默认值为ActionSupport。
- 如果没有为action指定method，默认值为execute。
- 如果没有指定result的name属性，默认值为success。
- 如果没有指定result的type属性，默认值为dispatcher。

**
Struts的各种转发类型。
**

*重定向式的转发不能访问WEB-INF下的JSP。*

先看Struts1中的转发：

请求分派式转发：

    <action path="...">
        <forward name="...">/index.jsp</forward>
    </action>

浏览器重定向转发：

    <action path="...">
        <forward name="..." redirect="true">/index.jsp</forward>
    </action>


Struts2：提供了多种结果类型，常用的有：dispatcher（默认）、redirect、redirectAction、plainText。

**
在result中可以使用${属性名}表达式访问action中的属性，表达式里的属性名对应action中的属性：
**

    <result type="redirect">/view.jsp?id=${id}</result>

如果要传递参数是中文，则需要进行编码。否则出现乱码：xxx.jsp?paramname=???

**
重定向到action：
**
如果重定向的action在同一个命名空间下：

    <result type="redirectAction">helloworld</result>

如果重定向的action在别的命名空间下：

    <result type="redirectAction">
        <param name="actionName">helloworld</param>
        <param name="namespace">/test</param>
    </result>

**
plaintext显示原始文件内容，例如：当我们需要原样显示JSP文件的源码时，我们就可以使用此类型。
**

    <result name="source" type="plainText">
        <param name="location">/xxx.jsp</param>
        <param name="charSet">utf-8</param>
    </result>

**
global-results：配置并使用全局视图。
**

只有该包下的action才可以使用该视图。

例子：

    <pacakge ...>

        <global-results>
            <result name="message">/WEB-INF/page/message.jsp</result>
        </global-results>

        <action
            name="manage"
            class="bit.edu.tavish.action.HelloWorldAction"
            method="add">
        </action>

    </package>

HelloWorldAction.java， 使用add方法分派到message视图。

    // ...
    public String add() {
        return "message";
    }
    // ...

**
使用包继承定义一个整个应用都可使用的全局视图。
**
将全局视图定义在一个单独的包中，需要使用该视图的包则继承前者。

    <package name="base" extends="struts-default">
        <global-results>
            <result name="message">/WEB-INF/page/message.jsp</result>
        </global-results>
    </package>

    <package
        name="tavish"
        namespace="/bit/edu/tavish"
        extends="base">
        ...
    </package>

### 为Action的属性注入值

Struts2为Action中的属性提供了依赖注入功能，在Struts2的配置文件中，我们可以很方便地为Action中的属性注入值。注意：**必须提供属性的setter方法。**

例子：

Action:

    public class HelloWorldAction {
        private String savePath;

        public String getSavePath() {
            return savePath;
        }

        public void setSavePath(String savePath) {
            this.savePath = savePath;
        }

        public String execute() {
            return "success";
        }

        // ...
    }

Struts2配置文件：

    <package name="tavish" namespace="/bit/edu/tavish" extends="base">
        <action name="hello2" class="bit.edu.tavish.action.HelloWorldAction2" method="execute">
            <param name="savePath">/image</param>
            <result name="success">/WEB-INF/page/hello.jsp</result>
    </package>

上面通过param元素，为action的savePath属性注入“/image”。

### Struts2常量定义

#### 指定Struts2处理的请求后缀

Struts2默认使用的后缀是.action。默认后缀可以通过配置进行修改。

例子：配置Struts2只处理以.do为后缀的请求路径。

    <struts>
        <constant name="struts.action.extension" value="do"/>
    </struts>

*
修改前，访问name为hello的action可以使用hello或hello.action进行访问。
修改后必须以指定扩展名进行访问，不能再用不加扩展名的方式访问。
*

如果要指定多个请求后缀，则多个后缀之间以英文逗号隔开：

    <constant name="struts.action.extension" value="do,go"/>

#### 其他常量定义

常量可以在struts.xml或struts.properties中配置，建议在struts.xml中配置。
上述扩展名的定义也可以在struts.properties中进行。

    struts.action.extension=do

常量可以在下面多个配置文件中进行定义，所以我们需要先了解Struts2加载常量的顺序：

- struts-default.xml
- struts-plugin.xml
- struts-xml
- struts.propertie
- web.xml

如果在多个配置文件中配置了同一个常量，则后一个文件中的配置的常量值会覆盖前面文件中配置的常量值。

其他常用常量：

    <!--  指定默认编码集，作用于HttpServletRequest的setCharacterEncoding方法和freemaker、velocity的输出  -->
    <constant name="struts.i18n.encoding" value="utf-8"/>

    <!--  设置浏览器是否缓存静态内容，默认值是true，开发阶段最好置为false  -->
    <constant name="struts.serve.static.browserCache" value="false"/>

    <!--  当struts的配置文件修改后，系统是否自动重新加载该文件，默认值为false，开发阶段最后置为true  -->
    <constant name="struts.configuration.xml.reload" value="true"/>

    <!--  开发模式下使用，这样可以打印出更详细的错误信息  -->
    <constant name="struts.devMode" value="true"/>

    <!--  默认的视图主题  -->
    <constant name="struts.ui.theme" value="simple"/>

    <!--  与spring集成时，指定由spring负责action对象的创建  -->
    <constant name="struts.objectFactory" value="spring"/>

    <!--  该属性设置Struts2是否支持动态方法调用，默认值是false  -->
    <constant name="struts.enable.DynamicMethodInvocation" value="true"/>

    <!--  上传文件总大小限制  -->
    <constant name="struts.multipart.maxSize" value="1024000"/>

### Struts2处理流程

概览：

>
用户请求-----》StrutsPrepareAndExecuteFilter-----》Interceptor（Struts2内置的一些拦截器或用户自定义的拦截器）-----》Action（用户编写的Action类）-----》Result-----》JSP/HTML-----》响应。

StrutsPrepareAndExecuteFilter是Struts2框架的核心控制器，他负责拦截由url-pattern（通常是/*）指定的所有用户的请求。当用户请求到达后，该Filter会过滤用户的请求，默认情况下，如果用户请求的路径不带有后缀，或后缀以.action结尾，这时请求将被转入Struts2框架处理，否则Struts2框架将掠过该请求的处理。当请求转入Strust2框架后会先经过一系列的拦截器，然后再到Action。**与Struts1不同（Struts1的Action使用了单例模式，一个Action只会存在一个实例），Struts2对用户的每一次请求都会创建一个Action，所以Struts2中的Action是线程安全的。**

### 为应用指定多个配置文件

>
按照模块划分配置文件，再进行包含。

大部分应用里，随着应用规模的增加，系统中Action的数量也会大量增加，导致struts.xml配置文件变得非常臃肿。为了避免struts.xml文件过于庞大臃肿，提高struts.xml文件的可读性，我们可以将一个struts.xml配置文件分解成多个配置文件，然后在struts.xml文件中包含其他配置文件。在struts.xml通过include元素指定多个配置文件。

示例：通过这种方式，我们就可以将Struts2的Action按模块添加在多个配置文件中。

    <struts>
        <include file="struts-user.xml"/>
        <include file="struts-order.xml"/>
    </struts>

在实际开发中，struts.xml通常不会配置具体的action。

### 动态方法调用和使用通配符定义action

Struts1 做法：

1：自定义的 Action 必须继承 DispatchAction 而不能继承Action。

    public class UserAction extends DispatchAction
 
2: Action中不能有execute(......) 方法，否则将始终调用该方法：
 
3：struts-config配置文件中应该增加如下配置：
 
    <action ... parameter="method" ></action>
    
    <action path="/user"
       type="org.springframework.web.struts.DelegatingActionProxy" name="user"
       parameter="method" scope="request">
       <forward name="add_success" path="list.jsp"></forward>
       <forward name="list" path="list.jsp"></forward>
    </action>
 
4：通过 path.do?method=xxx的形式调用Action中的特定方法。

Struts2有两种做法：

**
1.动态方法调用：官方已不建议使用该方法。
**

如果Action种存在多个方法，我们可以使用“!+方法名”的方式指定调用方法：

示例：

HelloWorldAction3.java

    public class HelloWorldAction3 {
        private String msg;

        public String getMsg() {
            return msg;
        }

        public String addUI() {
            msg = "addUI method";
            return "success";
        }

        public String execute() {
            msg = "execute method";
            return "success";
        }
    }

struts.xml配置：需要进行两项配置。

    <struts>
        <constant name="struts.enable.DynamicMethodInvocation" value="true"/>
        <package namespace="/bit/edu/tavish" extends="base">
            <global-allowed-methods>regex:.*</global-allowed-methods>
            <action
                name="hello3"
                class="bit.edu.tavish.action.HelloWorldAction3">
                <result name="success">/WEB-INF/page/helloworld3.jsp</result>
            </action>
        </package>
    </struts>

假设访问上面action的URL路径为：/Struts2/bit/edu/tavish/hello3.action
要访问addUI()方法，我们可以这样调用：
    
    /bit/edu/tavish/hello3!addUI.action

由于上述action未配置method属性，所以默认访问hello3，会执行该action的execute方法。如果配置method为addUI，则默认访问hello3，会执行addUI方法。即：

- method未配置：
    + hello3.action：执行execute
    + hello3!execute.action：执行execute
    + hello!addUI.action：执行addUI
- method配置为execute：同上
- method配置为addUI：
    + hello3.action：执行addUI
    + hello3!execute.action：执行execute
    + hello3!addUI.action：执行addUI


**
使用通配符定义action：官方推荐使用的方式。
**

示例：

HelloWorldAction3.java不变。

struts.xml配置：

    <struts>
        <package namespace="/bit/edu/tavish" extends="base">
            <global-allowed-methods>regex:.*</global-allowed-methods>
            <action
                <!--  匹配以hello4开头的所有action名称  -->
                name="hello4*"
                class="bit.edu.tavish.action.HelloWorldAction4"
                method="{1}">
                <result name="success">/WEB-INF/page/helloworld4.jsp</result>
            </action>
        </package>
    </struts>

要访问上述action的addUI方法，可以这样访问：/bit/edu/tavish/hello4addUI.action

此时默认访问hello4.action将执行execute方法。

使用{}获得的字符串可以在多个地方使用，例如class属性或result元素的值。

### 接受请求参数

Struts1通过FormBean获得请求参数。

**
采用基本类型接受请求参数
**

在Action类中**定义与请求参数同名的属性**，Struts2便能自动接收请求参数并赋予给同名属性。

示例：

请求URL：/test/view.action?id=78

Action类：Struts2通过反射技术调用与请求参数同名的属性的setter方法来获取请求参数值

    public class ProductAction {
        private Integer id;
        public void setId(Integer id) {
            this.id = id;
        }
        public Integer getId() {
            return id;
        }
    }

**
采用复合类型接受请求参数
**
请求路径：/test/view.action?product.id=78

Action类：Struts2首先通过反射技术调用Product（一个JavaBean）的默认构造器并创建Product的一个实例，然后再通过反射技术调用该实例中与请求参数同名的属性的setter方法来获取请求参数值

    public class ProductAction {
        private Product product;
        public void setProduct(Product product) {
            this.product = product;
        }
        public Prouct getProduct() {
            return product;
        }
    }

**
两种获得请求参数的示例：
**

表单JSP：sendParam.jsp

    <body>
        采用基本类型接受请求参数
        <br>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/getparamaction_getParam1.do" method="post">
            name:
            <input type="text" name="name">
            id:
            <input type="text" name="id">
            <input type="submit" value="Send">
        </form>
        <br>

        采用复合类型接受请求参数
        <br>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/getparamaction_getParam2.do" method="post">
            name:
            <input type="text" name="person.name">
            id:
            <input type="text" name="person.id">
            <input type="submit" value="Send">
        </form>
    </body>

Action类：GetParamterAction.java

    public class GetParameterAction {
        private int id;
        private String name;
        private Person person;
        
        public Person getPerson() {
            return person;
        }
        public void setPerson(Person person) {
            this.person = person;
        }
        public int getId() {
            return id;
        }
        public void setId(int id) {
            this.id = id;
        }
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
        
        public String getParam1() {
            return "success1";
        }
        
        public String getParam2() {
            return "success2";
        }   
    }

bean类：Person.java

    package bit.edu.tavish.bean;

    public class Person {
        
        private String name;
        private int id;
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
        public int getId() {
            return id;
        }
        public void setId(int id) {
            this.id = id;
        }
    }


要转发的视图：

getParam1.jsp

    <body>
        id=${id}<br> 
        name=${name}<br>
    </body>

getParam2.jsp

    <body>
        id=${person["id"]}<br> 
        name=${person["name"]}<br>
    </body>

struts.xml相关配置：

    <!-- 接受请求参数 -->
    <action
        name="getparamaction_*"
        class="bit.edu.tavish.action.GetParameterAction"
        method="{1}">
        <result name="success1">/WEB-INF/page/getParam1.jsp</result>
        <result name="success2">/WEB-INF/page/getParam2.jsp</result>
    </action>


**
在开发中通常使用复合类型接受请求参数，避免在Action类中出现过多的成员变量及其setter和getter方法。
**

### Struts2.1.6接受中文请求参数乱码问题

Struts2.1.6版本中存在一个bug，即接受到中文请求参数为乱码（post提交）。原因是Struts2.1.6在获取并使用了请求参数后才调用HttpServletRequest的setCharacterEncoding()方法进行编码设置。这个bug在Struts2.1.8中已经解决了。如果使用的是Struts2.1.6版本，要解决这个问题，可以这样做：

新建一个Filter，把这个Filter放置在Struts2的Filter之前。然后在doFilter中添加以下代码：

    HttpServletRequest req = (HttpServletRequest) request;
    // 根据要用的编码进行替换
    req.setCharacterEncoding("utf-8");
    filterChain.doFilter(request, response);


### 自定义类型转换器

类型转换的目的：

例如我们定义Action中有如下属性：Date birthday。当我们使用请求参数2017-11-12去访问时，Struts2是可以完成对birthday的赋值的。此时如果输出birthday，会打印：Sun Nov 12 00:00:00 CST 2017。但是如果我们使用请求参数20171112去访问，就会报错，因为20171112不能被转换为Date类型。
此时我们就需要类型转换器。

*类型转换器可以双向转换。*

Struts2有两种类型转换器：

- 局部类型转换器：对Action的某个属性进行转换。
- 全局类型转换器：对所有Action都有用。

**
定义类型转换器：继承DefaultTypeConverter，覆盖convertValue(Map context, Object value, Class toType)方法。
**

**
注册为局部类型转换器：
**
在Action类所在包下放置ActionClassName-conversion.properties文件，ActionClassName是Action的类名，后面的-conversion.properties是固定写法。
properties文件中的内容为：属性名称=类型转换器的全类名

示例：

Action类：HelloWorldAction5.java

    package bit.edu.tavish.action;

    import java.util.Date;

    /*
     * 使用类型转换器DateTypeConverter
     */
    public class HelloWorldAction5 {
        
        private String test;
        
        private Date birthday;
        
        public void setTest(String test) {
            this.test = test;
        }
        
        public String getTest() {
            return test;
        }

        public Date getBirthday() {
            return birthday;
        }

        public void setBirthday(Date birthday) {
            this.birthday = birthday;
            System.out.println(birthday);
        }
        
        public String execute() {
            return "success";
        }
    }

类型转换器：DateTypeConverter.java

    package bit.edu.tavish.type.converter;

    import java.util.Date;
    import java.text.SimpleDateFormat;
    import java.util.Map;

    import com.opensymphony.xwork2.conversion.impl.DefaultTypeConverter;

    public class DateTypeConverter extends DefaultTypeConverter {

        /*
         * context：ognl表达式上下文。
         * value：我们传入的值，例如20171112.
         * toType：要转换成的类型，这里是Date类型。
         */
        @SuppressWarnings("rawtypes")
        @Override
        public Object convertValue(Map<String, Object> context, Object value, Class toType) {
            SimpleDateFormat dateFormat = new SimpleDateFormat("yyyyMMdd");
            try {
                if (toType == Date.class) {
                    // 当字符串向Date类型转换时
                    System.out.println("字符串向Date转换");
                    String[] params = (String[]) value;
                    return dateFormat.parse(params[0]);
                } else if (toType == String.class) {
                    // 当Date类型转换为字符串时
                    Date date = (Date) value;
                    System.out.println("Date类型向字符串转换");
                    return dateFormat.format(date);
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
            return null;
        }
    }

struts.xml：

    <!-- 类型转换 -->
    <action
        name="hello5"
        class="bit.edu.tavish.action.HelloWorldAction5"
        method="execute">
        <result name="success">/WEB-INF/page/birthday.jsp</result>  
    </action>

要转发的JSP：birthday.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>

    <!DOCTYPE html>

    <html>

    <head>

    <meta charset="utf-8">

    <title>TypeConverter Birthday</title>

    </head>

    <body>
        ${birthday}
        ${test}
    </body>

    </html>

访问URL：/bit/edu/tavish/hello5.do?test=tavish&birthday=20180125
JSP输出：Thu Jan 25 00:00:00 CST 2018 tavish

访问URL：/bit/edu/tavish/hello5.do?test=tavish&birthday=2018-01-25
JPS输出（日期有误）：Fri Dec 01 00:00:00 CST 2017 tavish

**
注册为全局类型转换器：
**
在WEB-INF/classes（工程中放在src文件夹下）下放置xwork-conversion.properties。
properties文件中的内容为：带转换的类型=类型转换器的全类名
例如：java.util.Date=bit.edu.tavish.type.converter.DateTypeConverter
>
使用了上述类型转换器后，**就不能用yyyy-mm-dd格式的参数了，只能用yyyymmdd格式。**虽然也可以访问，但得到的日期会出错。（有待考证）

### 访问或添加request/session/application属性

由于Struts2框架没有使用Servlet API，所以我们无法直接得到request、session和ServletContext对象并添加属性。**要通过ActionContext来进行属性添加。**

示例：

    package bit.edu.tavish.action;

    import com.opensymphony.xwork2.ActionContext;

    public class SetAttributeAction {
        public String execute() {
            ActionContext atx = ActionContext.getContext();
            atx.getApplication().put("app", "应用范围属性");
            atx.getSession().put("sess", "会话范围属性");
            atx.put("req", "请求范围属性");
            return "GetAttri";
        }
    }

**
如何获取HttpServletRequest、HttpServletResponse、HttpSession和ServletContext对象
**

方法一：通过ServletActionContext类直接获取

    HttpServletRequest request = ServletActionContext.getRequest();
    HttpServletResponse response = ServletActionContext.getResponse();
    HttpSession session = request.getSession();
    ServletContext servletContext = ServletActionContext.getServletContext();

方法二：实现指定接口，由Struts2框架运行时注入

    public class GetAction implements ServletRequestAware, ServletResponseAware, ServletContextAware {
        private HttpServletRequest request;
        private HttpServletResponse response;
        private ServletContext servletContext;

        public void setRequest(HttpServletRequest req) {
            request = req;
        }

        public void setResponse(HttpServletResponse res) {
            response = res;
        } 

        public void setServletContext(ServletContext ctx) {
            servletContext = ctx;
        }
    }

获取到相应对象后同样可以设置属性。

### 文件上传

#### 单文件上传

第一步：添加commons-fileupload-1.3.3.jar和commons-io-2.5.jar包。
第二步：设置表单格式，将form表的enctype设置为multipart/form-data。

    <form enctype="multipart/form-data" action="{pageContext.request.contextPath}/xxx.action" method="post">
        <input type="file" name="uploadImage">
        <input type="submit" value="upload">
    </form>
第三步：在Action中进行处理。

    public class UploadAction {
        private File uploadImage; // 上传的文件，变量名与表单中的name属性值匹配
        private String uploadImageContentType; // 该文件的文件类型，写法固定：文件变量名+ContentType
        private String uploadImageFileName; // 文件的文件名，写法固定：文件变量名+FileName

        // setter & getter

        public String upload() throws Exception {
            String realPath = ServletActionContext.getServletContext().getRealPath("/images");
            File file = new File(realPath);
            if (!file.exists()) {
                file.mkdirs();
            }
            FileUtils.copyFile(uploadImage, new File(file, uploadedImageFileName));
            return "uploadsuccess";
        }
    }

#### 多文件上传

第一步：不变

第二步：不变

第三步：将Action成员变量类型改为相应的数组或list类型，同时修改处理逻辑

    private File[] uploadImages;
    private String[] uploadImagesContentType;
    private String[] uploadImagesFileName;

    //...

    public String upload() throws Exception {
        ...
        // 添加循环，对文件进行处理。
    }

### 自定义拦截器

要自定义拦截器需要实现com.opensymphony.xwork2.interceptor.Interceptor接口。主要实现其intercept()方法。另外可以选择实现init()和destory()两个生命周期方法。
当在intercept()方法中调用invocation.invoke()方法时，被拦截的方法就会得到调用。
拦截器在使用前需要注册。

注册拦截器，示例：

    <package ...>   
        <!-- 自定义拦截器 -->
        <interceptors>
            <interceptor
                name="permission"
                class="bit.edu.tavish.interceptor.PermissionInterceptor">
            </interceptor>
        </interceptors>

        <action
            name="hello6_*"
            class="bit.edu.tavish.action.HelloWorldAction6"
            method="{1}">
            <!-- 使用拦截器 -->
            <interceptor-ref name="permission" />
            <result name="success">/WEB-INF/page/helloworld6.jsp</result>
        </action>
    </package>

*但是，使用这种方法后，这个action将丢失所有Struts2默认提供的拦截器，而只能使用我们定义的拦截器。*
为了解决这个问题我们需要使用**拦截器栈**。

    <!-- 自定义拦截器 -->
    <interceptors>
        <!-- 我们定义的拦截器 -->
        <interceptor
            name="permission"
            class="bit.edu.tavish.interceptor.PermissionInterceptor">
        </interceptor>
        <!-- 定义拦截器栈 -->
        <interceptor-stack name="permissionStack">
            <!-- 引入默认的拦截器，放在最前面 -->
            <interceptor-ref name="defaultStack"/>
            <!-- 引入我们定义的拦截器 -->
            <interceptor-ref name="permission"/>
        </interceptor-stack>
    </interceptors>

拦截器栈示例：在action中引入拦截器栈

    <!-- 自定义拦截器 -->
    <interceptors>
        <interceptor
            name="permission"
            class="bit.edu.tavish.interceptor.PermissionInterceptor">
        </interceptor>
        <interceptor-stack name="permissionStack">
            <interceptor-ref name="defaultStack"/>
            <interceptor-ref name="permission"/>
        </interceptor-stack>
    </interceptors>

    <action
        name="hello6_*"
        class="bit.edu.tavish.action.HelloWorldAction6"
        method="{1}">
        <!-- 使用拦截器栈中定义的拦截器 -->
        <interceptor-ref name="permissionStack"/>           
        <result name="success">/WEB-INF/page/helloworld6.jsp</result>
    </action>

示例，模拟以下功能：

如果用户没有登录，则不允许访问action中的方法，并且提示“你没有权限执行该操作”。

定义两个JSP，用于模拟用户登录和登出。

登录：login_simulation.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>

    <%
        request.getSession().setAttribute("user", "tavish");
    %>

    用户已经登录。

登出：logoff_simulation.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>

    <%
        request.getSession().removeAttribute("user");
    %>

    用户已经登出。

用于转发的JSP：

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>HelloWorldAction 6</title>
    </head>
    <body>
        ${message}
    </body>
    </html>

Action类：HelloWorldAction6.java

    package bit.edu.tavish.action;

    public class HelloWorldAction6 {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
        
        public String execute1() {
            message = "execute1";
            return "success";
        }
        
        public String execute2() {
            message = "execute2";
            return "success";
        }
    }

拦截器：PermissionInterceptor.java

    package bit.edu.tavish.interceptor;
    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionInvocation;
    import com.opensymphony.xwork2.interceptor.Interceptor;

    public class PermissionInterceptor implements Interceptor {

        private static final long serialVersionUID = 6674523998206422972L;

        @Override
        public void destroy() {
            // TODO Auto-generated method stub
        }
        @Override
        public void init() {
            // TODO Auto-generated method stub
        }
        @Override
        public String intercept(ActionInvocation invocation) throws Exception {
            Object obj = ActionContext.getContext().getSession().get("user");
            if (obj != null) {
                // 如果obj不为null，则代表session中有user属性，即用户已经登录
                return invocation.invoke();
            } else {
                ActionContext.getContext().put("message", "你没有权限执行该操作");
                return "success";
            }
        }
    }

struts.xml配置：

    <package
        name="other"
        namespace="/com/cn/tavish"
        extends="base">
        <!-- 自定义拦截器 -->
        <interceptors>
            <interceptor
                name="permission"
                class="bit.edu.tavish.interceptor.PermissionInterceptor">
            </interceptor>
            <interceptor-stack name="permissionStack">
                <interceptor-ref name="defaultStack"/>
                <interceptor-ref name="permission"/>
            </interceptor-stack>
        </interceptors>
        
        <global-allowed-methods>regex:.*</global-allowed-methods>

        <action
            name="hello6_*"
            class="bit.edu.tavish.action.HelloWorldAction6"
            method="{1}">
            <!-- 使用拦截器栈中定义的拦截器 -->
            <interceptor-ref name="permissionStack"/>           
            <result name="success">/WEB-INF/page/helloworld6.jsp</result>
        </action>
    </package>


测试：

如果不先访问login_simulation直接访问hello6的任意方法都会显示“你没有权限执行该操作”。
先访问login_simulation再访问hello6的方法会显示“execute1”或“execute2”。
若访问logoff_simulation再访问hello6的方法会显示“你没有权限执行该操作”。

**
如果希望包下的所有action都使用自定义的拦截器，可以通过在包中定义default-interceptor-ref进行指定。每个包只能指定一个默认拦截器。除此之外，一旦我们为该包中的某个action显示指定了某个拦截器，则默认拦截器不会起作用。解决方法是在引入这个拦截器前先引入默认拦截器。
**

示例：

    <package name="xxx" namespace="/aaa/bbb/ccc" extends="ddd">
        <!-- 定义两个拦截器及拦截器栈，拦截器栈由Struts2自带拦截器与拦截器foo组成 -->
        <interceptors>
            <interceptor name="xxxxx" class="bar.xxxxx"/>
            <interceptor name="foo" class="bar.foobar"/>
            <interceptor-stack name="foobar2000">
                <interceptor-ref name="defaultStack"/>
                <interceptor-ref name="foo"/>
            </interceptor-stack>
        </interceptors>

        <!-- 指定拦截器栈foobar2000为该package的默认拦截器 -->
        <default-interceptor-ref name="foobar2000"/>

        <action name="somthing" class="xxx.aaa.Anything">
            <interceptor-ref name="foobar2000"/>
            <!--
                如果显示指定了下一行的拦截器，而不显式指定上一行的默认拦截器，
                则此时默认拦截器对此action失效。要同时启用该包的默认拦截器，
                需要在前显式指定。
            -->
            <interceptor-ref name="xxxxx"/>
        </action>
    </package>

### 输入校验

>以下内容的源代码在itcast-Struts2/Struts2Validate

在Struts2中，我们可以实现对action的所有方法进行校验或者对action的指定方法进行校验。

两种实现方法：

- 手工编写代码实现
- 基于XML配置方式实现

#### 手工编码方式

**
手工编写代码实现对Action中所有方法输入校验：
**
继承ActionSupport，通过重写validate()方法实现。validate()方法会校验action中的所有方法。当某个数据校验失败时，我们应该调用addFieldError()方法往系统的fieldErrors添加校验失败信息（为了使用addFieldError()方法，action类需要继承ActionSupport），如果系统的fieldErrors包含失败信息，Struts2会将请求转发到名为input的result。在input视图中可以通过&lt;s:fielderror&gt;显示失败信息。

示例：

Action类：ValidateAction1

    package bit.edu.tavish;

    import java.util.regex.Pattern;

    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionSupport;
    /*
     * 对Action中的所有方法都进行输入校验
     */
    public class ValidateAction1 extends ActionSupport {
        private static final long serialVersionUID = 4320417535525659615L;
        
        private String username;
        private String phonenumber;
        public String getUsername() {
            return username;
        }
        public void setUsername(String username) {
            this.username = username;
        }
        public String getPhonenumber() {
            return phonenumber;
        }
        public void setPhonenumber(String phonenumber) {
            this.phonenumber = phonenumber;
        }
        
        public String update() {
            ActionContext.getContext().put("message", "更新成功");
            return "message";
        }
        
        public String save() {
            ActionContext.getContext().put("message", "保存成功");
            return "message";
        }
        
        @Override
        public void validate() {
            if (username == null || "".equals(username.trim())) {
                addFieldError("username", "用户名不能为空");
            }
            if (phonenumber == null || "".equals(phonenumber.trim())) {
                addFieldError("phonenumber", "手机号不能为空");
            } else {
                // 使用regex验证手机号格式
                if (!Pattern.compile("^1[3578]\\d{9}$").matcher(phonenumber).matches()) {
                    addFieldError("phonenumber", "手机号格式不正确");
                }
            }
        }
    }

提交表单和接受错误信息的JSP：validateInput.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s" %>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>输入校验</title>
    </head>
    <body>
        <!-- 
            校验要求：
            1. 用户名不能为空
            2. 手机号不能为空，且要符合手机号格式：1[3578]xxxxxxxxx
         -->
        <s:fielderror/>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/validAc1_save.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
        
    </body>
    </html>

校验通过后转发的JSP：

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>结果</title>
    </head>
    <body>
        ${message}
    </body>
    </html>

struts.xml：

    <struts>
        <constant
            name="struts.action.extension"
            value="do,action" />

        <package
            name="validate1"
            namespace="/bit/edu/tavish"
            extends="struts-default">
            <global-allowed-methods>regex:.*</global-allowed-methods>

            <!-- 使用手工编码的方式对Action中的所有方法进行输入校验 -->
            <action
                name="validAc1_*"
                class="bit.edu.tavish.ValidateAction1"
                method="{1}">
                <result name="message">/WEB-INF/page/message.jsp</result>
                <result name="input">/validateInput.jsp</result>
            </action>
        </package>
    </struts>

**
手工编写代码对action中的指定方法进行校验：
**
通过重写validateXxx()方法来实现，validateXxx()只会对action中方法名为xxx的方法进行校验。当某个数据校验失败时，我们应该调用addFieldError()方法往系统的fieldErrors添加校验失败信息（为了使用addFieldError()方法，action类需要继承ActionSupport），如果系统的fieldErrors包含失败信息，Struts2会将请求转发到名为input的result。在input视图中可以通过&lt;s:fielderror&gt;显示失败信息。
例如，对add()方法进行校验，则重写validateAdd()方法。

**
流程总结：
**

1. 类型转换器对请求参数执行类型转换，并把转换后的值赋给action中的属性。
2. **如果在执行类型转换的过程中出现异常，系统会将异常信息保存在ActionContext，conversionError拦截器将异常信息封装到fieldErrors里。不论类型转换是否出现异常，都会进入第三步。**
3. 系统通过反射技术调用action中的validateXxx()方法，然后再调用validate()。
4. 如果校验失败，则应向fieldErrors中添加错误信息。
5. 经过上面4步，如果fieldErrors中存在错误信息（即存放错误信息的集合size大于0），系统自动将请求转发至名为input的视图。如果没有任何错误信息，系统将执行请求指定的Action中的特定方法。

#### XML配置方式

**
基于XML配置方式实现对Action中所有方法输入校验：
**
使用基于XML配置方式实现输入校验时，Action也需要继承ActionSupport，并且提供校验文件。校验文件和action类放在同一个包下，文件的命名格式为：ActionClassName-validation.xml。
xml文件内容：

    <?xml version="1.0" encoding="utf-8"?>
    <!DOCTYPE validators PUBLIC "-//Apache Struts//XWork Validator 1.0.3//EN" "http://struts.apache.org/dtds/xwork-validator-1.0.3.dtd">
    <validators>
        <field name="username">
            <field-validator type="requiredstring">
                <param name="trim">true</param>
                <message>用户名不能为空</message>
            </field-validator>
        </field>
    </validators>

field元素指定action中要校验的属性，field-validator元素指定校验器，校验器requiredstring是由系统提供的，系统提供了能满足大部分验证需求的校验器，这些校验器的定义可以在xwork-2.x.jar中的com.opensymphony.xwork2.validator.validators下的default.xml中找到。
message元素为校验失败后的提示信息，如果需要国际化，可以为message指定key属性，key的值为资源文件中的key。

上述校验文件实现的功能：对action中字符串类型的username属性进行验证，首先要求调用trim()方法去掉两段的空格，然后判断用户名是否为空。

**
Struts2提供的校验器列表：
**
- required：必填校验器，要求field的值不能为null。
- requiredString：必填校验器，要求field的值不能为null，并且长度大于0.默认情况下会对字符串进行trim。
- stringlength：字符串长度校验器，要求field的值必须在指定范围内。minLength参数指定最小长度，maxLength指定最大长度，trim参数指定要先field之前是否去除字符串前后的空格。
- regex：正则表达式校验器，检查被检验的field是否匹配一个正则表达式。regexExpression参数指定正则表达式，caseSensitive参数指定进行正则表达式匹配时是否区分大小写，默认为true。
- int：整数校验器，要求field的整数值必须在指定范围内，min指定最小值，max指定最大值。
- double：双精度浮点数校验器，要求field的双精度浮点数必须在指定范围内，min指定最小值，max指定最大值。
- fieldexpression：字段OGNL表达式校验器，要求field满足一个ognl表达式，expression参数指定ognl表达式。该逻辑表达式基于ValueStack进行求值，返回true时校验通过。
- email：邮件地址校验器。要求如果field的值非空，则必需是合法的邮件地址。
- url：网址校验器。要求如果field的值非空，则必需是合法的 url 地址。
- date：日期校验器，要求field的日期值必须在指定范围内。min参数指定最小值，max参数指定最大值。
- conversion：转换校验器，指定在类型转换失败时，提示错误信息。
- visitor：用于校验action中的复合属性。它指定一个校验文件用于校验复合属性中的属性。
- expression：OGNL表达式校验器。要求field满足一个ognl表达式。expression参数指定ognl表达式。该逻辑表达式基于ValueStack进行求值。该校验器不可用在字段校验器风格的配置中！


示例：对Action中的所有方法进行校验。

Action类：XMLValidateAction1.java

    package bit.edu.tavish.xmlvalid;

    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionSupport;

    public class XMLValidateAction1 extends ActionSupport {
        private static final long serialVersionUID = 4320417535525659615L;
        
        private String username;
        private String phonenumber;
        public String getUsername() {
            return username;
        }
        public void setUsername(String username) {
            this.username = username;
        }
        public String getPhonenumber() {
            return phonenumber;
        }
        public void setPhonenumber(String phonenumber) {
            this.phonenumber = phonenumber;
        }
        
        public String update() {
            ActionContext.getContext().put("message", "XMLValidAc1_更新成功");
            return "message";
        }
        
        public String save() {
            ActionContext.getContext().put("message", "XMLValidAc1_保存成功");
            return "message";
        }
    }


校验器：XMLValidateAction1-validation.xml

    <?xml version="1.0" encoding="utf-8"?>
    <!DOCTYPE validators PUBLIC "-//Apache Struts//XWork Validator 1.0.3//EN" "http://struts.apache.org/dtds/xwork-validator-1.0.3.dtd">
    <validators>
        <!-- 校验username -->
        <field name="username">
            <field-validator type="requiredstring">
                <!-- 为校验器的trim属性注入值true，该值默认为true -->
                <param name="trim">true</param>
                <message>用户名不能为空！</message>
            </field-validator>
        </field>
        <!-- 校验phonenumber -->
        <field name="phonenumber">
            <field-validator type="requiredstring">
                <message>手机号不能为空！</message>
            </field-validator>
            <field-validator type="regex">
                <param name="regex"><![CDATA[^1[3578]\d{9}$]]></param>
                <message>手机号格式不正确！</message>
            </field-validator>
        </field>
    </validators>

提交表单的JSP：validateInput-xml.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>输入校验---基于XML</title>
    </head>
    <body>
        <!-- 
            校验要求：
            1. 用户名不能为空
            2. 手机号不能为空，且要符合手机号格式：1[3578]xxxxxxxxx
         -->
        <s:fielderror />
        <em>xmlvalidAc1--全部校验</em>
        <br>
        <strong>save方法</strong>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/xmlvalidAc1_save.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
        <br>
        <strong>update方法</strong>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/xmlvalidAc1_update.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
    </body>
    </html>

要转发的JSP：message.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>结果</title>
    </head>
    <body>
        ${message}
    </body>
    </html>

struts.xml

    <!-- 基于XML对Action中的所有方法进行校验 -->
    <action name="xmlvalidAc1_*"
            class="bit.edu.tavish.xmlvalid.XMLValidateAction1"
            method="{1}">
            <result name="message">/WEB-INF/page/message.jsp</result>
            <result name="input">/validateInput-xml.jsp</result>
    </action>

**
基于XML配置，对Action中的指定方法进行输入校验：
**
如果只需要对Action中的某个方法实施校验，那么校验文件的取名应为ActionClassName-ActionName-validation.xml，其中ActionName为struts.xml中action的名称。

例子：

struts.xml

    <action name="user_*" class="xx.xxx.xxx.ExampleAction" method={1}>
        <result name="success">/WEB-INF/page/success.jsp</result>
        <result name="input">/WEB-INF/page/example.jsp</result>
    </action>

ExampleAction:

    public String add() throws Exception() {
        ...
        return "success";
    }
    public String update() throws Exception() {
        ...
        return "success";
    }

要对add()方法实施验证，校验文件的取名为：ExampleAction-user_add-validation.xml
要对update()方法实施验证，校验文件的取名为：ExamplAction-user_update-validation.xml

示例：

Action类：XMLValidateAction2.java

    package bit.edu.tavish.xmlvalid;

    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionSupport;

    public class XMLValidateAction2 extends ActionSupport {
        private static final long serialVersionUID = 4320417535525659615L;
        
        private String username;
        private String phonenumber;
        public String getUsername() {
            return username;
        }
        public void setUsername(String username) {
            this.username = username;
        }
        public String getPhonenumber() {
            return phonenumber;
        }
        public void setPhonenumber(String phonenumber) {
            this.phonenumber = phonenumber;
        }
        
        public String update() {
            ActionContext.getContext().put("message", "XMLValidAc2_更新成功");
            return "message";
        }
        
        public String save() {
            ActionContext.getContext().put("message", "XMLValidAc2_保存成功");
            return "message";
        }
    }

struts.xml：

    <!-- 基于XML对Action中的部分方法（update）进行输入校验 -->
    <action name="xmlvalidAc2_*"
            class="bit.edu.tavish.xmlvalid.XMLValidateAction2"
            method="{1}">
            <result name="message">/WEB-INF/page/message.jsp</result>
            <result name="input">/validateInput-xml.jsp</result>
    </action>

校验器：XMLValidateAction2-xmlvalidAc2_update-validation.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE validators PUBLIC "-//Apache Struts//XWork Validator 1.0.3//EN" "http://struts.apache.org/dtds/xwork-validator-1.0.3.dtd">
    <validators>
        <!-- 校验username -->
        <field name="username">
            <field-validator type="requiredstring">
                <!-- 为校验器的trim属性注入值true，该值默认为true -->
                <param name="trim">true</param>
                <message>用户名不能为空！</message>
            </field-validator>
        </field>
        <!-- 校验phonenumber -->
        <field name="phonenumber">
            <field-validator type="requiredstring">
                <message>手机号不能为空！</message>
            </field-validator>
            <field-validator type="regex">
                <param name="regexExpression"><![CDATA[^1[3578]\d{9}$]]></param>
                <message>手机号格式不正确！</message>
            </field-validator>
        </field>
    </validators>

提交表单的JSP：validateInput-xml.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>输入校验---基于XML</title>
    </head>
    <body>
        <!-- 
            校验要求：
            1. 用户名不能为空
            2. 手机号不能为空，且要符合手机号格式：1[3578]xxxxxxxxx
         -->
        <s:fielderror />
        <em>xmlvalidAc1--全部校验</em>
        <br>
        <strong>save方法</strong>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/xmlvalidAc1_save.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
        <br>
        <strong>update方法</strong>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/xmlvalidAc1_update.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
        
        <p>=================================================================================</p>
        <em>xmlvalidAc2--部分校验--只对update方法提供校验</em>
        <br>
        <strong>save方法</strong>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/xmlvalidAc2_save.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
        <br>
        <strong>update方法</strong>
        <form action="${pageContext.request.contextPath}/bit/edu/tavish/xmlvalidAc2_update.action" method="post">
            用户名：
            <input type="text" name="username">
            <br>
            手机号：
            <input type="text" name="phonenumber">
            <br>
            <input type="submit" value="submit">
        </form>
        
    </body>
    </html>

要转发到的JSP：message.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>结果</title>
    </head>
    <body>
        ${message}
    </body>
    </html>

**
当为一个Action提供了多个校验文件：
**
当为某个action提供了ActionClass-validation.xml和ActionClassName-ActionName-validation.xml两种规则的校验文件时，系统按下面顺序寻找校验文件：

1. ActionClass-validation.xml
2. ActionClassName-ActionName-validation.xml

系统寻找到第一个校验文件时还会继续搜索后面的校验文件，当搜索到所有校验文件时，会把校验文件里的所有校验规则汇总，然后全部应用于action方法的校验。如果两个校验文件中指定的校验规则冲突，则只使用后面文件中的校验规则。

**
当Action继承了另一个Action：
**

父类Action的校验文件会先被搜索到。

假设UserAction继承BaseAction，当访问UserAction的时候，系统先搜索父类的校验文件：BaseAction-validation.xml，BaseAction-user-validation.xml；接着搜索子类的校验文件：UserAction-validation.xml，UserAction-user-validation.xml。应用于UserAction的校验规则为这4个文件的总和。

### 国际化

>以下内容的源代码在itcast-Struts2/Struts2i18n，并使用PropertiesEditor插件

资源文件的范围：全局范围/包范围/action范围

准备资源文件，资源文件的命名格式如下：

- baseName_language_contry.properties
- baseName_language.properties
- baseName.properties

其中baseName是资源文件的基本名，我们可以自定义，**但language和country必须是Java支持的语言和国家。**如：

中国大陆：baseName_zh_CN.properties
美国：baseName_en_US.properties

#### 全局范围国际化资源

配置并使用全局范围资源文件。

1. 先准备两个资源文件：
    - 第一个存放中文：bit_zh_CN.properties，内容为welcome=欢迎来到北京理工大学
    - 第二个存放英语：bit_en_US.properties，内容为welcome=welcome to BIT
2. 配置全局资源：在准备好资源文件后，我们可以在strust.xml中通过struts.custom.i18n.resources常量把资源文件定义为全局资源文件。&lt;constant name="struts.custom.i18n.resources" value="bit"/&gt;。其中bit为资源文件的基本名。
3. 访问国际化信息，可以使用struts2标签或action进行访问。
    - 在JSP中使用&lt;s:text name=""/&gt;标签输出国际化信息，name属性指定资源文件中的key。
    - 在Action类中，通过继承ActionSupport，使用getText()方法得到国际化信息，该方法的第一个参数用来指定资源文件中的key。
    - 在表单标签中，通过key属性指定资源文件中的key。&lt;s:textfield name="" key=""&gt;

具体示例：

资源文件：bit_zh_CN.properties

    welcome=欢迎来到北京理工大学
    user=用户名
资源文件：bit_en_US.properties

    welcome=welcome to BIT
    user=user
struts.xml：

    <struts>
        <constant
            name="struts.action.extension"
            value="do,action" />
        <constant name="struts.custom.i18n.resources" value="bit"/>

        <package
            name="i18n1"
            namespace="/i18n1"
            extends="struts-default">
            <global-allowed-methods>regex:.*</global-allowed-methods>

        </package>
    </struts>

使用JSP访问，JSP：i18n.jsp

    <body>
        <s:text name="welcome" />
    </body>

输出：欢迎来到北京理工大学（浏览器语言设置为zh-CN）/welcome to BIT（浏览器语言设置为en-US）

使用Action访问，Action类：I18nAction.java

    package bit.edu.tavish;

    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionSupport;

    public class I18nAction extends ActionSupport {

        private static final long serialVersionUID = -6442728281496518147L;
        
        @Override
        public String execute() {
            ActionContext.getContext().put("message", this.getText("welcome"));
            return "success";
        }
    }

struts.xml：

    <action name="i18nAc" class="bit.edu.tavish.I18nAction" method="execute">
        <result name="success">/WEB-INF/page/getI18n.jsp</result>
    </action>

要转发到的JSP：getI18n.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Insert title here</title>
    </head>
    <body>
        ${message}
    </body>
    </html>

在表达标签中使用：

提交表单的JSP：formI18n.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>在表单表中使用国际化信息</title>
    </head>
    <body>
        <s:textfield name="realname" key="user" />
    </body>
    </html>

访问这个JSP，输出：

    en-US环境：文本框前的文本为user。
    zh-CN环境：文本框前的文本为用户名。

#### 输出带有占位符的国际化信息

在资源文件中添加占位符：

bit_en_US.properties：

    welcome={0},welcome to BIT{1}
    user=user

bit_zh_CN.properties：

    welcome={0},欢迎来到北京理工大学{1}
    user=用户名

**
如果不对占位符指定参数，则会原样输出占位符。
**

在JSP页面输出带占位符的国际化信息：使用param，对占位符的内容进行指定。

    <s:text name="welcome">
        <s:param>param0</s:param>
        <s:param>param1</s"param>
    </s:text>

示例：

资源文件，bit_en_US.properties：

    welcome={0},welcome to BIT{1}
    user=user

JSP：i18n.jsp

    <body>
        <s:text name="welcome" >
            <s:param>liming</s:param>
            <s:param>study</s:param>
        </s:text>
    </body>

在en-US环境下访问这个JSP得到：

    liming,welcome to BITstudy 

使用Action访问带占位符的国际化信息：使用getText的重载版本，添加一个List/String[]作为参数。

    @Override
    public String execute() {
        ActionContext.getContext().put("message", this.getText("welcome", new String[] {"tavish", "study"}));
        return "success";
    }

访问这个Action，输出：

    tavish,welcome to BITstudy 

#### 包范围国际化资源

在一个大型引用中，这个应用有大量的内容需要实现国际化，如果我们把国际化都放置在全局资源属性文件中，显然会导致资源文件变的过于庞大、臃肿，不利于维护。这时我们可以针对不同模块，使用包范围来组织国际化文件。

方法：在java的包下放置package_language_country.properties资源文件，package为固定写法，处于**该包及子包**下的action都可以访问该资源。当查找指定key的消息时，系统会先从package资源文件查找，当找不到对应的key时，才会从常量struts.custom.i18n.resources指定的资源文件中查找。

示例：

包：bit.edu.tavish

包下的Action：I18nAction.java

    package bit.edu.tavish;

    import com.opensymphony.xwork2.ActionContext;
    import com.opensymphony.xwork2.ActionSupport;

    public class I18nAction extends ActionSupport {

        private static final long serialVersionUID = -6442728281496518147L;

        @Override
        public String execute() {
            // 有占位符时，才提供getText的第二个参数
            ActionContext.getContext().put("message", this.getText("welcome", new String[] {"tavish", "study"}));
            return "success";
        }
    }

包下的properties：

package_en_US.properties

    welcome={0},welcome to BIT{1}_package
    user=user

package_zh_CN.properties

    welcome=欢迎来到北京理工大学_包
    user=用户名

*同时该应用配置有上一节的全局国际化资源。*

访问这个Action：/localhost:8080/Struts2i18n/i18n1/i18nAc.action

en_US环境下输出：tavish,welcome to BITstudy_package 
zh_CN环境下输出：欢迎来到北京理工大学_包

#### Action范围国际化资源

我们也可以为某个action单独指定资源文件，方法如下：
在Action类所在路径，放置ActionClassName_language_country.properties资源文件，ActionClassName为Action类的简单名称。
当查找指定Key的消息时，系统会先从ActionClassName_language_country.properties资源文件查找，如果没有找到对应的key，然后沿着当前包往上查找基本名为package的资源文件，一直找到最顶层包。如果还没有找到对应的Key，最后会从常量struts.custom.i18n.resources指定的资源文件中寻找。

示例：

在I18nAction的类路径配置两个资源文件：

I18nAction_en_US.properties

    welcome={0},welcome to BIT{1}_Action
    user=user

I18nAction_zh_CN.properties

    welcome=欢迎来到北京理工大学_动作
    user=用户名

*同时包内还有上一节定义的包范围国际化资源，且该应用还配置有一个全局国际化资源。*

访问这个Action：/localhost:8080/Struts2i18n/i18n1/i18nAc.action

en_US环境下输出：tavish,welcome to BITstudy_Action  
zh_CN环境下输出：欢迎来到北京理工大学_动作

#### 不进行任何配置直接访问资源

使用Struts2的标签：&lt;s:i18n&gt;标签。
使用这个标签我们可以在类路径下直接从某个资源文件中获取国际化信息，而无需任何配置。

示例：

tavish为类路径（src）下资源文件的基本名。

    <s:18n name="tavish">
        <s:text name="welcome"/>
    </s:i18n>

如果要访问的资源文件在类路径的某个包（bit.edu）下，可以这样访问：

其中bit.edu为包名，tavish为资源文件基本名。

    <s:18n name="bit/edu/tavish">
        <s:text name="welcome">
            <s:param>Tabi</s:param>
        <s:text>
    </s:i18n>

示例：

使用标签的JSP：i18n_welcome.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>国际化</title>
    </head>
    <body>
        <s:i18n name="welcome">
            <s:text name="welcome" />
        </s:i18n>
        <br>
        ================================
        <br>
        <s:i18n name="bit/edu/tavish/welcome">
            <s:text name="welcome">
                <s:param>Tavi</s:param>
            </s:text>
        </s:i18n>
    </body>
    </html>

src路径下资源文件：

welcome_en_US.properties

    welcome=Welcome Amigo

welcome_zh_CN.properties

    welcome=欢迎，朋友

包bit.edu.tavish下资源文件：

welcome_en_US.properties

    welcome=Welcome Amigo{0}

welcome_zh_CN.properties

    welcome=欢迎，朋友{0}

访问i18n_welcome.jsp：

en_US环境下：

    Welcome Amigo 
    ================================ 
    Welcome AmigoTavi 

zh_CN环境下：

    欢迎，朋友 
    ================================ 
    欢迎，朋友Tavi


### OGNL表达式

>以下内容的源代码在itcast-Struts2/Struts2OGNL

#### 概述

OGNL（Object Graphic Navigation Language，对象图导航语言）是一个开源项目。Struts2框架使用OGNL作为默认的表达式语言。

相对于EL表达式，它提供了平时我们需要的一些功能。例如：

- 支持对象方法调用，如xxx.sayHello();
- 支持类静态方法调用和值访问，表达式格式为：@[类全名]@[方法名|值名]
    + @java.lng.String@format('foo %s', 'bar');
    + @bit.edu.tavish@APP_NAME;
- 操作集合对象。

OGNL有一个上下文（Context）概念，上下文是一个Map结构，它实现了java.utils.Map接口。在Struts2中上下文的实现为ActionContext。

结构示意图：

- OGNL Context（ActionContext，Map结构）：
    + ValueStack（值栈，根对象）
    + parameters
    + request
    + session
    + application
    + attr

当Struts2接受一个请求时，会迅速创建ActionContext，ValueStack，action。然后把action存放进ValueStack，所以action的实例变量可以被OGNL访问。

访问上下文（Context）中的对象需要使用#符号标注命名空间，如#application，#session。
另外OGNL会设定一个根对象（root对象），在Struts2中根对象是ValueStack。如果要访问ValueStack中对象的属性，则可以省略#命名空空间，直接访问该对象的属性即可。
在Struts2中，根对象ValueStack的实现类为OgnlValueStack，该对象不是我们想象的值存放单个值，而是存放一组对象。在OgnlValueStack类里有一个List类型的root变量，就是使用它存放一组对象。

- OGNL Context（ActionContext，Map结构）：
    + ValueStack （OgnlValueStack：root变量[action， OgnlUtil, ...]）
    + parameters
    + request
    + session
    + application
    + attr

在root变量中处于第一位的对象叫栈顶对象。通常我们在OGNL表达式里直接协商属性的名称即可访问root变量里对象的属性，搜索顺序是从栈顶对象开始寻找，如果栈顶对象不存在该属性，就会从第二个对象中寻找，依次类推，直到找到为止。

在Struts2中，OGNL表达式需要配合Struts2标签才可以使用。

例如：

root变量{new Person("12", "Tavish"), new Product("controller", "300"), ...}
value接受的为OGNL表达式。

    <s:property value="name"/>

输出：Tavish

由于ValueStack是Struts2中OGNL的根对象，如果用户需要访问值栈中的对象，在JSP页面可以直接通过下面的EL表达式访问ValueStack中对象的属性：

    ${foo} <!--  获得值栈中某个对象的foo属性  -->

如果访问其他Context对象，由于他们不是根对象，所以在访问时需要添加#前缀：

- application对象：用于访问ServletContext，例如#application.userName或者#application['userName']，相当于调用ServletContext的getAttribute("userName")。
- session对象：用于访问HttpSession。例子同上。
- request对象：用于访问HttpServletRequest。例子同上。
- parameters对象：用于访问HTTP的请求参数，例如#parameter.userName或者#parameter['userName']相当与调用request.getParameter('userName')。
- attr对象：用于按照page--request--session--application顺序访问其属性。

EL表达式可以直接访问ValueStack中对象的属性的原因：**Struts2对HttpServletRequest作了进一步封装。**

代码如下：

    public class StrutsRequestWrapper extends HttpServletRequestWrapper {
        public StrutsRequestWrapper(HttpServletRequest req) {
            super(req);
        }

        public Object getAttribute(String s) {
            ...
            ActionContext ctx = ActionContext.getContext();
            // 先使用父类的getAttribute方法从request范围获取属性值
            Object attribute = super.getAttribute(s);
            if (ctx != null) {
                if (attribute == null) {
                    // 如果从request范围中没有找到属性值，则从ValueStack中查找对象的属性值。
                    ...
                    ValueStack stack = ctx.getValueStack();
                    attribute = stack.findValue(s);
                    ...
                }
            }
            return attribute;
        }
    }


#### 使用OGNL表达式

**
显示属性：
**

index.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <%
        request.setAttribute("user", "tavish");
    %>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Insert title here</title>
    </head>
    <body>

        <s:property value="#request.user" />
        <br>
        <s:property value="#session.job" />
        <br>
        ${requestScope.user}
        <br>
        ${sessionScope.job}

    </body>
    </html>

输出：

    tavish 
    coder 
    tavish 
    coder

**
采用OGNL表达式创建List/Map集合对象：
**

如果需要一个集合元素的时候，可以使用OGNL中同集合相关的表达式。
使用如下代码直接生成一个List对象：

    <s:set var="list" value="{'tavish', 'xiaoming', 'lisi'}" />
    <s:iterator value="#list">
        <s:property/><br>
    </s:iterator>

set标签用于将某个值放入指定范围。
scope：指定变量被放置的范围，该属性可以接受application、session、request、page或action。如果没有设置该属性，则默认放置在OGNL Context中。
value：赋给变量的值。如果没有设置该属性，则将ValueStack栈顶的值赋给变量。

iterator标签在迭代集合时有个特点：会把当前迭代的对象放在值栈的栈顶。
property标签如果没有指定value属性，则默认输出值栈栈顶的值。

生成一个Map对象：

    <s:set var="foobar" value="#{'key1':'90', 'key2':'35', 'key3':'78'}" />
    <s:iterator value="#foobar">
        <s:property value="key"/>=<s:property value="value"/><br>
    </s:iterator>

当使用iterator标签迭代Map时，内部会执行以下代码，并把entry放在栈顶。

    for(Map.Entry<K, V> entry : maps.entrySet()) {
        ...
    }

**
采用OGNL表达式判断对象是否在集合中：
**

对于集合类型，OGNL表达式可以使用in和not in两个元素符号。其中in表达式用来判断某个元素是否在指定集合对象中；not in判断波u个元素是否不在指定的集合中。

in 表达式：

    <s:if test="'foo' in {'foo', 'bar'}">
        ...
    </s:if>
    <s:else>
        ...
    </s:else>

not in 表达式：

    <s:if test="'foo' not in {'foo', 'bar'}">
        ...
    </s:if>
    <s:else>
        ...
    </s:else>

**
ONGL表达式的投影功能：
**

除了in和not in之外，OGNL还允许使用某个规则获得集合对象的子集，常用的有以下3个相关操作符。

- ?：获得所有符合逻辑的元素
- ^：获得符合逻辑的第一个元素
- $：获得符合逻辑的最后一个元素

例如：

    <s:iterator value="books.{?#this.price > 35}">
        <s:property value="title"/> - $<s:property value="price"/> <br>
    </s:iterator>

在上述代码中，直接在集合后紧跟.{}运算符表明用于取出该集合的子集，{}内的表达式用于获取符合条件的元素。this指的是为了从大集合books筛选数据到小集合，需要对大集合books进行迭代，this代表当前迭代元素。上述代码最终用于表示获取集合中价格大于35的书的集合。

    public class BookAction {
        private List<Book> books;
        ...
        public String execute() {
            books = new LinkedList<>();
            books.add(new Book("A731345", "spring", 67));
            books.add(new Book("B123103", "ejb3.0", 15));
            ...
        }
    }

示例：

Bean类：Book.java

    package bit.edu.bean;

    public class Book {
        private String bookId;
        private String name;
        private int price;
        public String getBookId() {
            return bookId;
        }
        public void setBookId(String bookId) {
            this.bookId = bookId;
        }
        public String getName() {
            return name;
        }
        public void setName(String name) {
            this.name = name;
        }
        public int getPrice() {
            return price;
        }
        public void setPrice(int price) {
            this.price = price;
        }
        public Book(String bookId, String name, int price) {
            super();
            this.bookId = bookId;
            this.name = name;
            this.price = price;
        }
    }

Action类：BookListAction

    package bit.edu.tavish;

    import java.util.ArrayList;
    import java.util.List;

    import bit.edu.bean.Book;

    public class BookListAction {
        private List<Book> books;

        public List<Book> getBooks() {
            return books;
        }

        public void setBooks(List<Book> books) {
            this.books = books;
        }
        
        public String execute() {
            books = new ArrayList<>();
            books.add(new Book("A6548","J2EE",90));
            books.add(new Book("C1245","Spring",70));
            books.add(new Book("D2560","Ajax",54));
            books.add(new Book("B1432","jQuery",62));
            
            return "booklist";
        }
    }

struts.xml配置：

    <action
        name="booklist"
        class="bit.edu.tavish.BookListAction">
        <result name="booklist">/WEB-INF/page/booklist.jsp</result>
    </action>

要转发的JSP：booklist.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Book List</title>
    </head>
    <body>
        <!-- 只显示价格大于60的记录 -->
        <!-- 
            OGNL: books.{?#this.price > 60}
        -->

        <s:iterator value="books.{?#this.price > 60}">
        id:<s:property value="bookId" />,
        name:<s:property value="name" />,
        price:<s:property value="price" />
            <br>
        </s:iterator>
    </body>
    </html>

访问Action，输出：

    id:A6548, name:J2EE, price:90 
    id:C1245, name:Spring, price:70 
    id:B1432, name:jQuery, price:62 

*但通常我们不会在页面上对数据进行过滤，而是在查询时就对数据进行过滤。*

### Struts2标签

以下内容的源代码在itcast-Struts2/Struts2Tag

#### set标签

set标签用于将某个值放入指定范围。

    <s:set var="" value="" scope=""/>

- var：设定该变量的名字。
- value：赋给变量的值，如果没有设置该属性，则将ValueStack栈顶的值赋给变量。
- scope：指定变量被放置的范围，可以接受appliaction、session、request、page或action。如果没有设置该属性，则默认放置在OGNL Context中。

#### property标签

property标签用于输出指定值：

    <s:property value="" default="" escape="" id="" />

- value：可选属性，指定需要输出的属性值，如果没有指定该属性，则默认输出ValueStack栈顶的值。
- default：可选属性，如果要输出的属性值为null，则显示该属性指定的值。
- escape：可选属性，指定是否格式化HTML代码。
- id：可选属性，指定该元素的标识。

#### iterator标签

iterator标签用于对集合进行迭代，包括List、Set、Map和数组。迭代时会把迭代的元素放在值栈的栈顶，所以可以直接在标签内使用property标签进行输出。

    <s:iterator value="" status="">
        ...
    </s:iterator>

- value：可选属性，指定被迭代的集合，如果没有设置该属性，则使用ValueStack栈顶的集合。
- id：可选属性，指定集合里元素的id，该属性已被标注为过时。
- status：可选属性，该属性指定迭代时的IteratorStatus实例，该实例放在OGNL Context中。

IteratorStatus实例包含以下几个方法：

- int getCount()：返回当前迭代了几个元素。
- int getIndex()：返回当前迭代元素的索引。
- boolean isEven()：返回当前被迭代的元素的索引是否为偶数。
- boolean isOdd()：返回当前被迭代的元素的索引是否为奇数。
- boolean isFirst()：返回当前被迭代的元素是否是第一个元素。
- boolean isLast()：返回当前被迭代元素是否是最后一个元素。

示例：

    <s:set var="list" value="{'tavish', 'xiaoming', 'lisi'}" />
    <s:iterator value="#list" status="st">
        <font color=<s:if test="#st.odd">red</s:if><s:else>blue</s:else>>
        <s:property/>
        </font>
        <br>
    </s:iterator>

#### if/elseif/else标签

该标签用于进行逻辑判断。

    <s:if test="">
        ...
    </s:if>
    <s:elseif test="">
        ...
    </s:elseif>
    <s:else>
        ...
    </s:else>


test属性指定判断所用的逻辑。

示例：

    <s:set var="age" value="21" scope="request"/>
    <s:if test="#request.age==23">
        23
    </s:if>
    <s:elseif test="#request.age==21">
        21
    </s:elseif>
    <s:else>
        都不等
    </s:else>

#### url标签

该标签用来创建一个URL，并输出它作为文本格式。

- var：设定url的引用。
- value：指定链接的url。
- action：指定要链接的action，使用struts.xml文件中配置的名称。
- namespace：指定action所在的命名空间。

示例：

创建图片url：

    <img src="<s:url value="/images/man.jpg"/>" />

输出：（Struts2Example为根上下文名称）    

    <img src="/Struts2Example/images/man.jpg" />

创建一个“Google”文字，并将其链接到http://www.google.com。

    <a href="<s:url value="http://www.google.com" />" target="_blank">Google</a>

输出：

    <a href="http://www.google.com" target="_blank">Google</a> 


创建操作URL，以“id”为参数，并输出它的文本格式：

    <s:url action="urlTagAction.action" >
        <s:param name="id">123</s:param>
    </s:url>

输出：

    /Struts2Example/urlTagAction.action?id=123

创建操作URL，以“name”为参数，使用url引用，并输出它的文本格式：

    <s:url action="urlTagAction.action" var="urlTag" >
        <s:param name="name">Tavish</s:param>
    </s:url>
    <a href="<s:property value="#urlTag" />" >URL Tag Action (via property)</a>

输出：

    <a href="/Struts2Example/urlTagAction.action?name=Tavish" >URL Tag Action (via property)</a>

创建操作URL，以“age”为参数，使用%{urlTag}，并输出它的文本格式：

    <s:url action="urlTagAction.action" var="urlTag" >
        <s:param name="age">25</s:param>
    </s:url>
    <s:a href="%{urlTag}">URL Tag Action (via %)</s:a>

输出：

    <a href="/Struts2Example/urlTagAction.action?age=25">URL Tag Action (via %)</a>


当标签的属性值作为字符串类型处理时，“%”符号的用途是计算OGNL表达式的值。

    <s:set var="myUrl" value="'http://www.jd.com'"/>
    <s:url value="#myUrl"/><br>
    <s:url value="%{#myUrl}"/>

输出：

    #myUrl
    http://www.jd.com

#### checkboxlist标签

该标签用于生成复选框。

- name：指定生成复选框的name属性的值。
- list：指定生成复选框的集合。
- value：指定被选中的元素。
- listKey：指定复选框value属性的值。
- listValue：指定外部label标签的值。

如果集合是List：

    <s:checkboxlist name="list" list="{'Java','.Net','PHP','OCamel'}" value="{'Java','.Net'}"/>

上述代码会生成如下HTML代码：

    <tr>
        <td class="tdLabel"></td>
        <td class="tdInput">
            <input type="checkbox" name="list" value="Java" id="list-1" checked="checked" />
            <label for="list-1" class="checkboxLabel">Java</label>
            <input type="checkbox" name="list" value=".Net" id="list-2" checked="checked" />
            <label for="list-2" class="checkboxLabel">.Net</label>
            <input type="checkbox" name="list" value="PHP" id="list-3" />
            <label for="list-3" class="checkboxLabel">PHP</label>
            <input type="checkbox" name="list" value="OCamel" id="list-4" />
            <label for="list-4" class="checkboxLabel">OCamel</label>
            <input type="hidden" id="__multiselect_list" name="__multiselect_list" value="" />
        </td>
    </tr>

如果集合是Map：

    <s:checkboxlist name="map" list="#{1:'瑜伽用品',2:'户外用品',3:'球类',4:'自行车'}" listKey="key" listValue="value" value="{1,2,3}"/>

上述代码会生成如下HTML代码：

    <tr>
        <td class="tdLabel"></td>
        <td class="tdInput">
            <input type="checkbox" name="map" value="1" id="map-1" checked="checked" />
            <label for="map-1" class="checkboxLabel">瑜伽用品</label>
            <input type="checkbox" name="map" value="2" id="map-2" checked="checked" />
            <label for="map-2" class="checkboxLabel">户外用品</label>
            <input type="checkbox" name="map" value="3" id="map-3" checked="checked" />
            <label for="map-3" class="checkboxLabel">球类</label>
            <input type="checkbox" name="map" value="4" id="map-4" />
            <label for="map-4" class="checkboxLabel">自行车</label>
            <input type="hidden" id="__multiselect_map" name="__multiselect_map" value="" />
        </td>
    </tr>


如果集合里存放的是JavaBean：

    <%
        List<Person> list = new ArrayList<>();
        list.add(new Person(1, "zhangsan"));
        list.add(new Person(2, "lisi"));
        request.setAttribute("personlist", list);
    %>

    <s:checkboxlist name="beans" list="#request.personlist" listKey="personId" listValue="name" />
    <!-- personId和name为Person的属性 -->

生成如下html代码：

    <input type="checkbox" name="beans" value="1" id="beans-1" />
    <label for="beans-1" class="checkboxLabel">zhangsan</label>
    <input type="checkbox" name="beans" value="2" id="beans-2" />
    <label for="beans-2" class="checkboxLabel">lisi</label>
    <input type="hidden" id="__multiselect_beans" name="__multiselect_beans" value="" />


如果不需要Struts2为我们自动生成诸如tr、td这些代码，则需要配置以下常量：

    <constant name="struts.ui.theme" value="simple" />

#### radio标签

用于生成单选框。用法几乎和复选框标签相同。

    <s:radio name="list" list="{'Java','.Net','PHP','OCamel'}" value="{'.Net'}"/>
    <br>
    <s:radio name="map" list="#{1:'瑜伽用品',2:'户外用品',3:'球类',4:'自行车'}" listKey="key" listValue="value" value="{2}"/>

生成以下HTML代码：

    <input type="radio" name="list" id="listJava" value="Java" />
    <label for="listJava">Java</label>
    <input type="radio" name="list" id="list.Net" checked="checked" value=".Net" />
    <label for="list.Net">.Net</label>
    <input type="radio" name="list" id="listPHP" value="PHP" />
    <label for="listPHP">PHP</label>
    <input type="radio" name="list" id="listOCamel" value="OCamel" />
    <label for="listOCamel">OCamel</label>

    <br>
    <input type="radio" name="map" id="map1" value="1" />
    <label for="map1">瑜伽用品</label>
    <input type="radio" name="map" id="map2" checked="checked" value="2" />
    <label for="map2">户外用品</label>
    <input type="radio" name="map" id="map3" value="3" />
    <label for="map3">球类</label>
    <input type="radio" name="map" id="map4" value="4" />
    <label for="map4">自行车</label>

#### select标签

用于生成下拉选择框。

- name：指定select标签的name属性的值。
- list：指定select标签的option标签的属性值。
- value：指定初始选择的值。

使用list集合生成下拉选择框。

    <s:select name="list" list="{'Java','.Net','PHP','OCamel'}" value="{'OCamel'}" />

生成以下HTML代码：

    <select name="list" id="list">
        <option value="Java">Java</option>
        <option value=".Net">.Net</option>
        <option value="PHP">PHP</option>
        <option value="OCamel" selected="selected">OCamel</option>
    </select>

使用JavaBean构造下拉选择框：

    <s:select name="beans" list="#request.personlist" listKey="personId" listValue="name" value="{2}"/>

生成以下HTML代码：

    <select name="beans" id="beans">
        <option value="1">zhangsan</option>
        <option value="2" selected="selected">lisi</option>
    </select>

使用Map集合生成下拉选择框：

    <s:select name="map" list="#{1:'瑜伽用品',2:'户外用品',3:'球类',4:'自行车'}" listKey="key" listValue="value" value="{2}" />

生成以下HTML代码：

    <select name="map" id="map">
        <option value="1">瑜伽用品</option>
        <option value="2" selected="selected">户外用品</option>
        <option value="3">球类</option>
        <option value="4">自行车</option>
    </select>


#### token标签

该标签用于防止表单重复提交。

    <s:form action="helloworld_other" method="post" namespace="/test">
        <s:textfield name="person.name"/><s:token/><s:submit/>
    </s:form>

    <action name="helloworld_*" class="bit.edu.tavish.HelloWorldAction" method={1}>
        <interceptor-ref name="defaultStack"/>
        <interceptor-ref name="token"/>
        <result name="invalid.token">/WEB-INF/page/message.jsp</result>
        <result>/WEB-INF/page/result.jsp</result>
    </action>

以上配置加入了token拦截器和invaild.token结果。token拦截器在会话token与请求的token不一致时，返回invalid.token结果。

示例：

Action类：PersonAction.java

    package bit.edu.action;

    public class PersonAction {
        
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public String execute() {
            
            return "success";
        }
    }

表单JSP：form.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <%@ taglib uri="/struts-tags" prefix="s"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Insert title here</title>
    </head>
    <body>
        <s:form action="personaction" namespace="/bit/edu" method="post">
        姓名：<s:textfield name="name" />
            <s:token />
            <input type="submit" value="提交">
        </s:form>
    </body>
    </html>

struts.xml

    <package
        name="tavish"
        namespace="/bit/edu"
        extends="struts-default">
        <action
            name="personaction"
            class="bit.edu.action.PersonAction">
            <interceptor-ref name="defaultStack" />
            <interceptor-ref name="token" />
            <result name="invalid.token">/WEB-INF/page/invalid.jsp</result>
            <result>/WEB-INF/page/message.jsp</result>
        </action>
    </package>

invaid.jsp

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Token invalid</title>
    </head>
    <body>
        该表单不允许重复提交！
    </body>
    </html>

message.jsp

    <%@page import="java.util.Date"%>
    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Insert title here</title>
    </head>
    <body>
        name is : ${name}
        <br>
        <%=new Date() %>
    </body>
    </html>

访问form.jsp，输入姓名。
如果是第一次提交则经过Action处理转发到message.jsp，输出：提交的姓名+当前时间。
如果后退至form.jsp再进行提交或刷新action页面重新提交，则回转发到invaild.jsp，输出：该表单不允许重复提交！