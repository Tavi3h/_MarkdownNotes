[TOC]

# Maven 教程

**什么是Maven？**
***
Maven是一个项目构建工具。开发人员使用Maven可以自动完成项目的基础工具建设。

在多个开发团队环境时，Maven可以按标准在非常短的时间里完成配置工作。由于大部分项目的设置都很简单，并且可重复使用，Maven让开发人员的工作更轻松，同时创建报表，检查，构建和测试自动化设置。

概括地说，Maven简化和标准化项目建设过程。处理编译，分配，文档，团队协作和其他任务的无缝连接。 Maven增加可重用性并负责建立相关的任务。

Maven项目的结构和内容在一个xml文件中声明，pom.xml项目对象模型（POM），这是整个Maven系统的基本单元。

**Maven的两大核心**

- 依赖管理：使用仓库对jar包进行管理。
- 项目构建：项目在编码完成后对项目进行编译、测试、打包、部署等一系列操作都使用Maven命令。

## Maven安装配置

由于Maven本身是纯Java开发的，**所以安装Maven前需要确保已安装JDK，且系统环境变量中已配置"JAVA_HOME"**。

访问[Maven官方网站](http://maven.apache.org/){:target="_blank"}，选择版本3.5.3。

下载好后解压到要安装Maven的文件夹。

**添加环境变量：**

添加M2_HOME和MAVEN_HOME环境变量到系统环境变量，并将其指向你的Maven文件夹。

![System_Env](images\System_Env.PNG)

Maven只要求添加M2_HOME变量，但某些项目仍然引用MAVEN_HOME，因此为了兼容，把MAVEN_HOME也添加进去。

配置PATH变量，将apache-maven-3.5.3\bin目录添加到PATH变量中，以便于我们在任何目录中运行Maven命令。

配置完毕后，在cmd中执行`mvn -version`或`mvn -v`：

    C:\Users\Tavish>mvn -v
    Apache Maven 3.5.3 (3383c37e1f9e9b3bc3df5050c29c8aff9f295297; 2018-02-25T03:49:05+08:00)
    Maven home: D:\Program Files\apache-maven-3.5.3\bin\..
    Java version: 1.8.0_152, vendor: Oracle Corporation
    Java home: C:\Program Files\Java\jdk1.8.0_152\jre
    Default locale: zh_CN, platform encoding: GBK
    OS name: "windows 10", version: "10.0", arch: "amd64", family: "windows"

出现上述信息，代表Maven已经安装成功。

**设置JDK信息：**

Maven默认使用的JDK还是1.5版本，这里修改为1.8。

修改conf\settings.xml，在profiles标签内增加以下内容：

```xml
<profile>    
    <id>jdk-1.8</id>    
    <activation>    
        <activeByDefault>true</activeByDefault>    
        <jdk>1.8</jdk>    
    </activation>    
    <properties>    
        <maven.compiler.source>1.8</maven.compiler.source>    
        <maven.compiler.target>1.8</maven.compiler.target>    
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>    
</profile>
```

上述设置是全局设置，我们也可以对某一个项目指定JDK的版本，修改该项目的pom.xml，对编译插件进行修改：

```xml
<plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.7.0</version>
        <configuration>
            <source>1.7</source>
            <target>1.7</target>
        </configuration>
    </plugin>
</plugins>
```

上述代码指定了编译时使用1.7版本的JDK。

**Maven启用代理：**

如果你的公司正在建立一个防火墙，并使用HTTP代理服务器来阻止用户直接连接到互联网。如果您使用代理，Maven将无法下载任何依赖。为了使它工作，你必须声明在Maven的配置文件中设置代理服务器。

打开apache-maven-3.5.3\conf\settings.xml，找到proxies标签：

```xml
<!-- proxies
| This is a list of proxies which can be used on this machine to connect to the network.
| Unless otherwise specified (by system property or command-line switch), the first proxy
| specification in this list marked as active will be used.
|-->
<proxies>
    <!-- proxy
     | Specification for one proxy, to be used in connecting to the network.
     |
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
    -->
</proxies>
```

取消标签proxy上的注释，填写代理服务器的信息：

```xml
<proxies>
    <proxy>
      <id>optional</id>
      <active>true</active>
      <protocol>http</protocol>
      <username>proxyuser</username>
      <password>proxypass</password>
      <host>proxy.host.net</host>
      <port>80</port>
      <nonProxyHosts>local.net|some.host.com</nonProxyHosts>
    </proxy>
</proxies>
```

填写完毕后保存即可。

## Maven资源库

### Maven本地资源库

Maven的本地资源库是用来存储所有项目的依赖关系(插件jar和其他文件，这些文件被Maven下载)到本地文件夹。很简单，当你建立一个Maven项目，所有相关文件将被存储在你的Maven本地仓库。

默认情况下，Maven的本地资源库默认为.m2目录文件夹：C:\Users\\{your-username}\\.m2

我们可以更改本地库的位置，同样在settings.xml中进行修改：

```xml
<!-- localRepository
| The path to the local repository maven will use to store artifacts.
|
| Default: ${user.home}/.m2/repository
<localRepository>/path/to/local/repo</localRepository>
-->
```

取消注释，配置标签localRepository：

```xml
<!-- localRepository
| The path to the local repository maven will use to store artifacts.
|
| Default: ${user.home}/.m2/repository
<localRepository>/path/to/local/repo</localRepository>
-->
<localRepository>D:\Program Files\apache-maven-3.5.3\repository</localRepository>
```

### Maven中央存储库

当我们建立一个Maven项目时，Maven会检查pom.xml，以确定哪些依赖需要下载。首先Maven将从本地资源库查找依赖资源，如果没有找到，Maven会默认从[中央资源库](http://repo1.maven.org/maven2/){:target="_blank"}查找下载。Maven中央存储库是由Maven社区提供的资源库。它包含了大量的常用程序库。

要浏览中央存储仓库的内容可以从[The Search Engine for The Central Repository](http://search.maven.org/){:target="_blank"}进行搜索。

常用的Maven中央库镜像：

（如果需要使用镜像，同样在settings.xml中进行配置）

阿里云：

```xml
<mirror>
    <id>alimaven</id>
    <name>aliyun maven</name>   
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>        
</mirror>
```

Jboss：

```xml
<mirror>  
    <id>jboss-public-repository-group</id>  
    <mirrorOf>central</mirrorOf>  
    <name>JBoss Public Repository Group</name>  
    <url>http://repository.jboss.org/nexus/content/groups/public</url>  
</mirror>
```

Spring.io：

```xml
<mirror>
    <id>springio</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>https://repo.spring.io/libs-snapshot/</url>
</mirror>
```

repo2：

```xml
<mirror>  
    <id>repo2</id>  
    <mirrorOf>central</mirrorOf>  
    <name>Human Readable Name for this Mirror.</name>  
    <url>http://repo2.maven.org/maven2/</url>  
</mirror>
```

### Maven远程存储库

根据Apache Maven的说明：

>
Downloading in Maven is triggered by a project declaring a dependency that is not present in the local repository (or for a SNAPSHOT, when the remote repository contains one that is newer). By default, Maven will download from the central repository.

当我们在项目中声明了一个依赖但是不存在于本地库时，默认情况下，Maven会自动从中心库进行下载。

但是，如果我们声明的库依赖既不存在于本地存储库中也不存在于Maven中心库时，这个下载过程就将停止并输出错误信息到Maven控制台。

通常情况下，如果本地资源库没有相应的依赖，Maven可以在中央存储库中找到缺失的依赖。但是，有些依赖在中央存储库也找不到，为了防止这种情况，Maven提供远程仓库概念，这是开发商的自定义库包含所需的库文件或其他项目jar文件。

例如我们要使用org.jvnet.localizer：

```xml
<dependency>
    <groupId>org.jvnet.localizer</groupId>
    <artifactId>localizer</artifactId>
    <version>1.8</version>
</dependency>
```

当我们建立这个Maven项目时，Maven会因为找不到依赖而输出错误信息。

要告诉Maven来获得Java.net的依赖需要我们声明远程仓库：

```xml
<repositories>
    <repository>
        <id>java.net</id>
        <url>https://maven.java.net/content/repositories/public/</url>
    </repository>
</repositories>
```

如果配置了远程存储库，那么Maven的依赖库查询顺序变为：

- 在本地资源库中搜索，如果没有找到，进入下一步。
- 在中央存储库中搜索，如果没有找到，进入下一步。
- 在远程存储库中搜索，如果没有找到，提示错误信息。

在中央存储库和远程存储库中找到的依赖会下载到本地资源库以供将来使用。

## Maven的依赖管理

### Maven的依赖机制

假设现在我们要用log4j作为项目的日志，我们要怎么做？

**传统方式：**

- 访问log4j的官网。
- 下载log4j的jar包。
- 复制jar到项目的lib目录。
- 将jar包添加到classpath。
- 手动维护log4j的依赖及版本。

**Maven方式**

- 首先我们需要知道log4j的Maven坐标，例如：
```xml
<groupId>log4j</groupId>
<artifactId>log4j</artifactId>
<version>1.2.14</version> 
```
- 根据上述的坐标，编写pom.xml文件：
```xml
<dependencies>
    <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.14</version>
    </dependency>
</dependencies>
```
- 当Maven编译或构建pom.xml文件被解析，Maven会在本地资源库、中央存储库以及远程存储库（如果定义了）中查找log4j的1.2.14版本的jar包，找到后加入本地资源库。
- 所有这一切由Maven管理维护。

### 添加jar到Maven本地资源库

例如，我们要添加dom4j到本地资源库，假设它不在Maven的中央存储库中。

首先下载dom4j-2.1.0.jar，将它放在任意位置，例如放在桌面上一个名为temp的文件夹内。

查找这个jar的groupId、artifactId和version，这里分别为：org.dom4j、dom4j和2.1.0。

在cmd下发出命令：

    C:\Users\Tavish>mvn install:install-file -Dfile=C:\Users\Tavish\Desktop\temp\dom4j-2.1.0.jar -DgroupId=org.dom4j -DartifactId=dom4j -Dversion=2.1.0 -Dpackaging=jar

    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] --- maven-install-plugin:2.4:install-file (default-cli) @ standalone-pom ---
    [INFO] Installing C:\Users\Tavish\Desktop\temp\dom4j-2.1.0.jar to D:\Program Files\apache-maven-3.5.3\repository\org\dom4j\dom4j\2.1.0\dom4j-2.1.0.jar
    [INFO] Installing C:\Users\Tavish\AppData\Local\Temp\mvninstall105033884616644052.pom to D:\Program Files\apache-maven-3.5.3\repository\org\dom4j\dom4j\2.1.0\dom4j-2.1.0.pom
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 0.413 s
    [INFO] Finished at: 2018-06-10T14:12:31+08:00
    [INFO] ------------------------------------------------------------------------

现在dom4j已经被复制到了Maven的本地存储库中。

当我们在pom.xml中声明dom4j的坐标时：

```xml
<dependency>
    <groupId>org.dom4j</groupId>
    <artifactId>dom4j</artifactId>
    <version>2.1.0</version>
</dependency>
```

Maven就能在本地存储库中找到2.1.0版的dom4j了。

### 指定外部依赖

**（不建议使用）**

如果我们的依赖在中央存储库和远程存储库中都不存在，我们也可以使用外部依赖。

例如我们有一个名为jdapjdk.jar的包在项目目录的lib文件夹下，那么在pom.xml中可以这样添加外部依赖：

```xml
<dependency>
    <groupId>ldapjdk</groupId>
    <artifactId>ldapjdk</artifactId>
    <scope>system</scope>
    <version>1.0</version>
    <systemPath>${basedir}\lib\ldapjdk.jar</systemPath>
</dependency>
```

### 依赖范围

总的来说，依赖范围如下表所示：

依赖范围|对于编译classpath有效|对于测试classpath有效|对于运行classpath有效|例子
-----|-----|-----|-----|-----
compile | Y | Y | Y | spring-core
test | - | Y | - | junit
provided | Y | Y | - | servlet-api
runtime | - | Y | Y | JDBC驱动
system | Y | Y | - | 本地的Maven仓库之外的类库

**“-”代表在该场景下不使用该jar包。**

当我们添加依赖时，默认的范围是compile。

对于servlet-api来说，如果我们将其范围设置为compile，就意味着当我们将项目打包成war时，servlet-api会被打包到WEB-INF\lib目录下。但是由于tomcat自身的lib就具有servlet-api这个jar包，所以这个时候会发生冲突。所以对于servlet-api这类jar包，我们应该设置其范围为provided。

## Maven项目构建

### Maven项目标准目录结构

以一个Maven构建的JavaWeb项目为例，目录结构如下：

- Maven-helloworld：项目名称
    + .setting
    + src：项目的源码
        * main：项目主程序运行需要的代码
            - java：主程序java代码
            - resource：配置文件，例如spring、hibernate的配置文件
            - webapp：相当于web项目中的WebContent，存放jsp、js、css等页面素材
        * test：项目单元测试用的代码
            - java：单元测试用java代码
            - resource：配置文件（通常不使用）
    + target：非Maven项目标准结构内容，src中的源码经编译，存放在这里
    + .classpath
    + .project
    + pom.xml：Maven项目核心配置文件

JavaWeb项目

### 使用Maven创建项目

根据项目的不同，通常情况下我们只需要在以下两个模板中选择一个并创建项目即可：

- maven-archetype-webapp （Java Web Project (WAR)）
- maven-archetype-quickstart （Java Project (JAR)）

例如在myCode下使用maven-archetype-quickstart模板创建一个Java项目：

    E:\myCode>mvn archetype:generate -DgroupId=pers.tavish -DartifactId=MavenJavaProject -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

创建一个JavaWeb项目：

    E:\myCode>mvn archetype:generate -DgroupId=pers.tavish -DartifactId=MavenJavaWebProject -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false

Maven总计提供了2000+的模板，我们可以手动进行选择：

    mvn archetype:generate

选择674号Spring+Hibernate的模板（ ml.rugal.archetype:springmvc-spring-hibernate (A pretty useful JavaEE application archetype based on springmvc spring and hibernate)）：

    Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): 1291: 674

然后选择版本：

    Choose ml.rugal.archetype:springmvc-spring-hibernate version:
    1: 0.1
    2: 0.2
    3: 0.3
    4: 0.4
    5: 0.5
    6: 0.6
    7: 0.7
    8: 1.0
    Choose a number: 8: 8

填写groupId、artifactId、version和package：

    Define value for property 'groupId': pers.tavish
    Define value for property 'artifactId': MavenSSHibernate
    Define value for property 'version' 1.0-SNAPSHOT: : 0.1
    Define value for property 'package' pers.tavish: : pers.tavish

确定：

    Confirm properties configuration:
    groupId: pers.tavish
    artifactId: MavenSSHibernate
    version: 0.1
    package: pers.tavish
     Y: : Y

构建成功：

    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: springmvc-spring-hibernate:1.0
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: pers.tavish
    [INFO] Parameter: artifactId, Value: MavenSSHibernate
    [INFO] Parameter: version, Value: 0.1
    [INFO] Parameter: package, Value: pers.tavish
    [INFO] Parameter: packageInPathFormat, Value: pers/tavish
    [INFO] Parameter: package, Value: pers.tavish
    [INFO] Parameter: version, Value: 0.1
    [INFO] Parameter: groupId, Value: pers.tavish
    [INFO] Parameter: artifactId, Value: MavenSSHibernate
    [INFO] Project created from Archetype in dir: E:\myCode\MavenSSHibernate
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 02:31 min
    [INFO] Finished at: 2018-06-11T10:36:21+08:00
    [INFO] ------------------------------------------------------------------------

### Maven与Eclipse

Eclipse是自带Maven插件的，但需要我们进行配置。

首先配置Eclipse使用的Maven版本，这里不使用内置的Maven，使用我们自己安装的Maven：

![eclipse_maven_installations](images\eclipse_maven_installations.PNG)

在使用Eclipse对Maven项目开发之前，需要设置Eclipse，在Preferences-Maven-User Settings中设置User Settings为Maven\conf下的settings.xml：

![eclipse_maven_user_setting](images\eclipse_maven_user_setting.PNG)

#### 将Maven项目导入Eclipse

可以通过命令`mvn eclipse:eclipse`将Maven项目变为可被Eclipse导入的项目：

将MavenTest项目导入Eclipse：

    mvn eclipse:eclipse

    [INFO] Using Eclipse Workspace: null
    [INFO] Adding default classpath container: org.eclipse.jdt.launching.JRE_CONTAINER
    [INFO] Not writing settings - defaults suffice
    [INFO] Wrote Eclipse project for "MavenTest" to E:\myCode\MavenTest.
    [INFO]
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 02:57 min
    [INFO] Finished at: 2018-06-11T10:45:59+08:00
    [INFO] ------------------------------------------------------------------------

之后使用eclipse导入（Existing Projects into Workspace）这个项目就可以了。

另一种方法，不使用命令，直接导入（Existing Maven Projects）项目。

#### 使用Eclipse创建Maven项目

我们同样可以直接使用Eclipse创建Maven项目。

在New Project中选择Maven Project，根据向导设置相关Maven属性即可。

#### 使用Eclipse将Java项目转为Maven项目

右键项目，在configure菜单中选择convert to Maven Project，根据向导设置相关Maven属性即可。

#### 示例：Eclipse下Maven整合Servlet

创建Maven工程：

![create_maven_project](images\create_maven_project.PNG)

跳过原型选择：

![skip_archetype_selection](images\skip_archetype_selection.PNG)

填写项目信息，父工程为空：

![configure_project1](images\configure_project1.PNG)

工程刚刚创建完毕会报错，web.xml缺失：

![webxml_missing](images\webxml_missing.PNG)

生成一个web.xml即可。

创建一个Servlet：

![create_servlet](images\create_servlet.PNG)

创建Servlet之后会发生报错，这是因为缺少Servlet的相关依赖。通常情况下我们可以通过添加Apache Tomcat library来解决Servlet的相关jar包的导入问题，但由于现在我们使用的是Maven，所以我们通过Maven来添加Servlet依赖：

![add_servlet_dependency](images\add_servlet_dependency.PNG)

添加后，报错解决：

![after_add_servlet_dependency](images\after_add_servlet_dependency.PNG)

这个jar包在pom.xml中对应的代码为：

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.1.0</version>
    <scope>provided</scope>
</dependency>
```

同理，我们添加一个jsp-api：

```xml
<dependency>
    <groupId>org.apache.tomcat</groupId>
    <artifactId>jsp-api</artifactId>
    <version>6.0.29</version>
    <scope>provided</scope>
</dependency>
```

修改Servlet的代码，添加Servlet映射（*使用注解“@WebServlet("/HelloMaven")”会访问不到，可能是bug*），进行测试：

```java
public class HelloMaven extends HttpServlet {
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        
        response.setContentType("text/html;charset=utf-8");
        
        response.getWriter().write("Hello Maven!");
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

```xml
<servlet>
    <servlet-name>HelloMaven</servlet-name>
    <servlet-class>pers.tavish.servlet.HelloMaven</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>HelloMaven</servlet-name>
    <url-pattern>/HelloMaven</url-pattern>
</servlet-mapping>
```

选择Run as -- Maven build...，填写命令，Run：

![run_as_maven](images\run_as_maven.PNG)

访问 http://localhost:8080/maven_servlet/HelloMaven ，屏幕显示“Hello Maven!”。

如果我们在源码上加入断点并以Debug启动进行Debug，那么我们需要在Maven build...界面的source中添加项目源码：

![debug_as_add_source](images\debug_as_add_source.PNG)

#### 示例：Eclipse下Maven整合Struts2

创建Maven工程：

![create_maven_project](images\create_maven_project.PNG)

跳过原型选择：

![skip_archetype_selection](images\skip_archetype_selection.PNG)

填写项目信息，父工程为空：

![configure_project](images\configure_project2.PNG)

工程刚刚创建完毕会报错，web.xml缺失：

![webxml_missing](images\webxml_missing.PNG)

生成一个web.xml即可。由于我们要使用Struts2，所以配置Struts2的核心过滤器：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
    id="WebApp_ID"
    version="3.1">
    <display-name>maven_struts2</display-name>
    <!-- 通过filter启动Struts2 -->
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

整合Struts2，添加Struts2的依赖（servlet-api和jsp-api的添加略）：

![add_dependency](images\add_dependency.PNG)

添加了Struts2核心包后，其核心包的依赖包会被自动添加：

![maven_dependencies](images\maven_dependencies.PNG)

创建Struts2配置文件struts.xml：

Struts2的配置文件应被放在main\resources下：

![struts.xml](images\struts.xml.PNG)

创建Action类，Action类应该被放在main\java下：

![CreateAction](images\CreateAction.PNG)

配置struts.xml：

```xml
<struts>
    <package
        name="demo"
        namespace="/"
        extends="struts-default">
        <global-allowed-methods>regex:.*</global-allowed-methods>
        <action
            name="customerAction_*"
            class="pers.tavish.action.CustomerAction"
            method="{1}">
            <result name="success">/index.jsp</result>
        </action>
    </package>
</struts>
```

配置success视图：

![success](images\success.PNG)

在tomcat上运行项目，项目右键Run as选择Maven build...，Goals命令填写：tomcat:run。填写完毕后点击Run运行项目。

成功的话，根据上述配置，访问 http://localhost:8080/maven_struts2/customerAction_execute 会在页面上显示Hello World，同时控制台输出Call CustomerAction.execute()。

#### 示例：Eclipse下Maven整合SSH

创建Maven项目：maven_ssh。

**依赖信息：**

```xml
<!-- 版本属性信息 -->
<properties>
    <spring.version>4.3.9.RELEASE</spring.version>
    <struts.version>2.5.13</struts.version>
    <hibernate.version>5.0.1.Final</hibernate.version>
</properties>
```

![maven_ssh_dependencies](images\maven_ssh_dependencies.PNG)

**需要配置的配置文件：**

- web.xml
- applicationContext.xml
- db.propertites
- struts.xml
- hibernate.cfg.xml
- xxx.hbm.xml

**搭建Struts2的环境：**

1. 在src\main\resources下创建struts.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
    "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>

</struts>
```
2. 在web.xml中配置Struts2的核心过滤器
```xml
<!-- 配置Struts2核心过滤器 -->
<filter>
    <filter-name>struts2</filter-name>
    <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>struts2</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

**搭建Spring的环境：**

1. 在src\main\resources下创建applicationContext.xml
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
</beans>
```
2. 在web.xml中配置Spring的监听器
```xml
<!-- 配置Spring监听器 -->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<!-- 通过上下文参数指定Spring配置文件的路径 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>
```

**搭建Hibernate环境：**

1. 在src\main\resources下创建hibernate.cfg.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- 指定方言 -->
        <property name="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</property>

        <!-- 配置自动建表 -->
        <property name="hibernate.hbm2ddl.auto">update</property>

        <!-- 显示SQL -->
        <property name="hibernate.show_sql">true</property>

        <!-- 格式化SQL -->
        <property name="hibernate.format_sql">true</property>
    </session-factory>
</hibernate-configuration>
```

**整合Struts2与Spring：**

整合的关键：将Action对象的创建由Struts2交给Spring。

1. 创建Action类
```java
public class CustomerAction {

}
```
2. 将Action对象配置到Spring的配置文件中
```xml
<bean
    id="customerAction"
    class="pers.tavish.action.CustomerAction"
    scope="prototype">
</bean>
```
3. 在Struts2的配置文件中配置相应的Action
```xml
<package name="demo" namespace="/" extends="struts-default">
    <global-allowed-methods>regex:.*</global-allowed-methods>
    <action name="customerAction_*" class="customerAction" method="{1}">
        
    </action>
</package>
```

**整合Spring与Hibernate：**

整合的关键：

- dataSource的配置交给Spring
- SessionFactory的创建交给Spring
- 事务的管理交给Spring

1. 在src\main\resources下创建保存数据库链接四要素的属性文件db.properties
```text
c3p0.driverClass=com.mysql.jdbc.Driver
c3p0.jdbcUrl=jdbc:mysql://localhost:3306/maven
c3p0.user=root
c3p0.password=mysql
```
2. 在Spring的配置文件中配置数据源
```xml
<!-- 加载属性文件 -->
<context:property-placeholder location="classpath:db.properties"/>

<!-- 配置数据源 -->
<bean
    id="c3p0DataSource"
    class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <!-- 配置数据库链接四要素 -->
    <property name="driverClass" value="${c3p0.driverClass}" />
    <property name="jdbcUrl" value="${c3p0.jdbcUrl}" />
    <property name="user" value="${c3p0.user}" />
    <property name="password" value="${c3p0.password}" />
</bean>
```
3. 配置SessionFactory
```xml
<!-- 配置SessionFactory -->
<bean
    id="sessionFactory"
    class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
    <!-- 注入数据源 -->
    <property
        name="dataSource"
        ref="c3p0DataSource" />
    <!-- 设置Hibernate配置文件的位置 -->
    <property
        name="configLocations"
        value="classpath:hibernate.cfg.xml" />
</bean>
```
4. 配置事务管理器
```xml
<!-- 配置SessionFactory -->
<bean id="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
    <!-- 注入数据源 -->
    <property name="dataSource" ref="c3p0DataSource" />
    <!-- 设置Hibernate配置文件的位置 -->
    <property name="configLocations" value="classpath:hibernate.cfg.xml" />
</bean>

<!-- 配置事务管理器 -->
<bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
    <property name="sessionFactory" ref="sessionFactory" />
</bean>

<!-- 配置通知：具体增强逻辑 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <!-- 匹配业务类中方法名称 -->
        <tx:method name="save*" isolation="DEFAULT" propagation="REQUIRED" />
        <tx:method name="update*" isolation="DEFAULT" propagation="REQUIRED" />
        <tx:method name="delete*" isolation="DEFAULT" propagation="REQUIRED" />
        <tx:method name="find*" isolation="DEFAULT" propagation="REQUIRED" read-only="true" />
        <tx:method name="*" />
    </tx:attributes>
</tx:advice>

<!-- 配置AOP -->
<aop:config>
    <!-- 配置切点：具体哪些方法要增强（真正被增强的方法） -->
    <aop:pointcut expression="execution(* pers.tavish.service.*.*(..))" id="cut" />
    <!-- 配置切面：将增强的逻辑作用到切点（通知+切点） -->
    <aop:advisor advice-ref="txAdvice" pointcut-ref="cut" />
</aop:config>
```

**对该项目进行完善并测试：**

需求：在地址栏发送action请求，经由action-service-dao完成客户查询。

请求路径：http://localhost:8080/maven_ssh/customerAction_findOne.action?custId=1

查询数据库中custId为1的信息。

1. 创建实体类及映射文件
```java
// 实体类
package pers.tavish.domain;

public class Customer {
    
    private String custId;
    private String custName;
    private String address;

    public String getCustId() {
        return custId;
    }

    public void setCustId(String custId) {
        this.custId = custId;
    }

    public String getCustName() {
        return custName;
    }

    public void setCustName(String custName) {
        this.custName = custName;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```
```xml
<!-- 映射文件 -->
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="pers.tavish.domain">
    <class name="pers.tavish.domain.Customer" table="customer">
        <id name="id" type="java.lang.String">
            <column name="id" length="32" />
            <generator class="uuid" />
        </id>
        <property name="custName" type="java.lang.String" />
        <property name="address" type="java.lang.String" />
    </class>
</hibernate-mapping>
```
2. 在Hibernate的主配置文件中注册映射文件
```xml
<!-- 注册映射文件 -->
<mapping resource="pers/tavish/domain/Customer.hbm.xml" />
```
3. 完成action、service以及dao的注入
```xml
<!-- 完成注入 -->
<!-- 配置Dao对象 -->
<bean id="customerDao" class="pers.tavish.dao.impl.CustomerDaoImpl">
    <!-- 注入SessionFactory -->
    <property name="sessionFactory" ref="sessionFactory" />
</bean>

<!-- 配置Service -->
<bean id="customerService" class="pers.tavish.service.impl.CustomerServiceImpl">
    <!-- 注入Dao -->
    <property name="customerDao" ref="customerDao" />
</bean>

<!-- 配置Action对象 -->
<bean id="customerAction" class="pers.tavish.action.CustomerAction" scope="prototype">
    <!-- 注入Service -->
    <property name="customerService" ref="customerService" />
</bean>
```
4. 编写Action类、配置struts.xml拦截请求并配置结果视图
```java
public class CustomerAction {
    
    private CustomerService customerService;
    
    private String custId;

    public void setCustomerService(CustomerService customerService) {
        this.customerService = customerService;
    }
    
    public void setCustId(String custId) {
        this.custId = custId;
    }

    // 根据主键进行查询
    public String findOne() {
        Customer customer = customerService.findOne(custId);
        ActionContext.getContext().getValueStack().push(customer);
        return "success";
    }
}
```
```xml
<struts>
    <package name="demo" namespace="/" extends="struts-default">
        <global-allowed-methods>regex:.*</global-allowed-methods>
        <action name="customerAction_*" class="customerAction" method="{1}">
            <result name="success">/success.jsp</result>
        </action>
    </package>
</struts>
```
```html
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort()
            + path + "/";
%>
<!DOCTYPE html>
<%@ taglib uri="/struts-tags" prefix="s" %>
<html>
<head>
<base href="<%=basePath%>">
<meta charset="utf-8">
<title>Insert title here</title>
</head>
<body>

    <s:property value="custName"/>

</body>
</html>
```
5. 编写Service、Dao及其实现类
```java
// Service接口
public interface CustomerService {
    Customer findOne(String custId);
}

// Service实现类
public class CustomerServiceImpl implements CustomerService {
    
    private CustomerDao customerDao;

    public void setCustomerDao(CustomerDao customerDao) {
        this.customerDao = customerDao;
    }

    @Override
    public Customer findOne(String custId) {
        return customerDao.findOne(custId);
    }
}

// Dao接口
public interface CustomerDao {
    Customer findOne(String custId);
}

// Dao实现类
public class CustomerDaoImpl extends HibernateDaoSupport implements CustomerDao {
    @Override
    public Customer findOne(String custId) {
        return this.getHibernateTemplate().get(Customer.class, custId);
    }
}
```

部署项目，访问 http://localhost:8080/maven_ssh/customerAction_findOne.action?custId=1 ，页面上显示“Tavish”。（数据库中信息为1，Tavish，China）

后台输出sql语句：

```text
Hibernate: 
    select
        customer0_.id as id1_0_0_,
        customer0_.custName as custName2_0_0_,
        customer0_.address as address3_0_0_ 
    from
        customer customer0_ 
    where
        customer0_.id=?
```

### 常用的Maven命令

- **clean**：删除项目根目录下的.class文件，通常会直接删掉根目录下的target文件夹
```
E:\myCode\Maven\EclipseMavenJavaProject>mvn clean
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< pers.tavish:EclipseMavenJavaProject >-----------------
[INFO] Building EclipseMavenJavaProject 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-clean-plugin:2.5:clean (default-clean) @ EclipseMavenJavaProject ---
[INFO] Deleting E:\myCode\Maven\EclipseMavenJavaProject\target
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.190 s
[INFO] Finished at: 2018-06-11T14:52:51+08:00
[INFO] ------------------------------------------------------------------------
```
- **compile**：编译项目中的.java文件，生成.class文件。
```
E:\myCode\Maven\EclipseMavenJavaProject>mvn compile
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< pers.tavish:EclipseMavenJavaProject >-----------------
[INFO] Building EclipseMavenJavaProject 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ EclipseMavenJavaProject ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory E:\myCode\Maven\EclipseMavenJavaProject\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ EclipseMavenJavaProject ---
[INFO] Changes detected - recompiling the module!
[INFO] Compiling 1 source file to E:\myCode\Maven\EclipseMavenJavaProject\target\classes
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.092 s
[INFO] Finished at: 2018-06-11T14:56:31+08:00
[INFO] ------------------------------------------------------------------------
```
- **test**：执行项目根目录中src\test\java下的单元测试。单元测试类的类名必须为XxxTest.java。
```
E:\myCode\Maven\EclipseMavenJavaProject>mvn test
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< pers.tavish:EclipseMavenJavaProject >-----------------
[INFO] Building EclipseMavenJavaProject 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ EclipseMavenJavaProject ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory E:\myCode\Maven\EclipseMavenJavaProject\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ EclipseMavenJavaProject ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ EclipseMavenJavaProject ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory E:\myCode\Maven\EclipseMavenJavaProject\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ EclipseMavenJavaProject ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ EclipseMavenJavaProject ---
[INFO] Surefire report directory: E:\myCode\Maven\EclipseMavenJavaProject\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running pers.tavish.AppTest
Hello World!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.043 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.091 s
[INFO] Finished at: 2018-06-11T15:06:06+08:00
[INFO] ------------------------------------------------------------------------
```
- **package**：将项目编译、测试并按照pom.xml中的package标签对项目进行打包，输出至target目录。
```
E:\myCode\Maven\EclipseMavenJavaProject>mvn package
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------< pers.tavish:EclipseMavenJavaProject >-----------------
[INFO] Building EclipseMavenJavaProject 0.0.1-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ EclipseMavenJavaProject ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory E:\myCode\Maven\EclipseMavenJavaProject\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ EclipseMavenJavaProject ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ EclipseMavenJavaProject ---
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] skip non existing resourceDirectory E:\myCode\Maven\EclipseMavenJavaProject\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ EclipseMavenJavaProject ---
[INFO] Nothing to compile - all classes are up to date
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ EclipseMavenJavaProject ---
[INFO] Surefire report directory: E:\myCode\Maven\EclipseMavenJavaProject\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running pers.tavish.AppTest
Hello World!
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.044 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ EclipseMavenJavaProject ---
[INFO] Building jar: E:\myCode\Maven\EclipseMavenJavaProject\target\EclipseMavenJavaProject-0.0.1-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.306 s
[INFO] Finished at: 2018-06-11T15:15:36+08:00
[INFO] ------------------------------------------------------------------------
```
- **install**：将本地项目编译、测试并打包后安装到本地资源库中。
```
E:\myCode\Maven\MavenJavaProject>mvn install
[INFO] Scanning for projects...
[INFO]
[INFO] --------------------< pers.tavish:MavenJavaProject >--------------------
[INFO] Building MavenJavaProject 1.0-SNAPSHOT
[INFO] --------------------------------[ jar ]---------------------------------
[INFO]
[INFO] --- maven-resources-plugin:2.6:resources (default-resources) @ MavenJavaProject ---
[WARNING] Using platform encoding (GBK actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory E:\myCode\Maven\MavenJavaProject\src\main\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:compile (default-compile) @ MavenJavaProject ---
[INFO] Changes detected - recompiling the module!
[WARNING] File encoding has not been set, using platform encoding GBK, i.e. build is platform dependent!
[INFO] Compiling 1 source file to E:\myCode\Maven\MavenJavaProject\target\classes
[INFO]
[INFO] --- maven-resources-plugin:2.6:testResources (default-testResources) @ MavenJavaProject ---
[WARNING] Using platform encoding (GBK actually) to copy filtered resources, i.e. build is platform dependent!
[INFO] skip non existing resourceDirectory E:\myCode\Maven\MavenJavaProject\src\test\resources
[INFO]
[INFO] --- maven-compiler-plugin:3.1:testCompile (default-testCompile) @ MavenJavaProject ---
[INFO] Changes detected - recompiling the module!
[WARNING] File encoding has not been set, using platform encoding GBK, i.e. build is platform dependent!
[INFO] Compiling 1 source file to E:\myCode\Maven\MavenJavaProject\target\test-classes
[INFO]
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ MavenJavaProject ---
[INFO] Surefire report directory: E:\myCode\Maven\MavenJavaProject\target\surefire-reports

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running pers.tavish.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.031 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO]
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ MavenJavaProject ---
[INFO] Building jar: E:\myCode\Maven\MavenJavaProject\target\MavenJavaProject-1.0-SNAPSHOT.jar
[INFO]
[INFO] --- maven-install-plugin:2.4:install (default-install) @ MavenJavaProject ---
[INFO] Installing E:\myCode\Maven\MavenJavaProject\target\MavenJavaProject-1.0-SNAPSHOT.jar to D:\Program Files\apache-maven-3.5.3\repository\pers\tavish\MavenJavaProject\1.0-SNAPSHOT\MavenJavaProject-1.0-SNAPSHOT.jar
[INFO] Installing E:\myCode\Maven\MavenJavaProject\pom.xml to D:\Program Files\apache-maven-3.5.3\repository\pers\tavish\MavenJavaProject\1.0-SNAPSHOT\MavenJavaProject-1.0-SNAPSHOT.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 1.816 s
[INFO] Finished at: 2018-06-11T15:33:52+08:00
[INFO] ------------------------------------------------------------------------
```

如果在install的过程中出现以下警告：

```
[WARNING] Using platform encoding (GBK actually) to copy filtered resources, i.e. build is platform dependent!
[WARNING] File encoding has not been set, using platform encoding GBK, i.e. build is platform dependent!
[WARNING] Using platform encoding (GBK actually) to copy filtered resources, i.e. build is platform dependent!
[WARNING] File encoding has not been set, using platform encoding GBK, i.e. build is platform dependent!
```

则说明没有对插件compiler和resources的编码进行配置，需要修改项目的pom.xml，对插件进行配置，指定utf-8编码：

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.7.0</version>
            <configuration>
                <encoding>utf-8</encoding>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <version>3.0.1</version>
            <configuration>
                <encoding>utf-8</encoding>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### Maven项目的生命周期

Maven存在“三套”生命周期，每一套生命周期相互独立，互不影响。在一套生命周期内，执行后面的命令，前面的命令会自动执行。

- CleanLifeCycle：清理生命周期，包含命令：
    + clean
- defaultLifeCycle：默认生命周期，包含命令：
    + compile
    + test
    + package
    + install
    + deploy
- siteLifeCycle：站点生命周期，包含命令：
    + site

## Maven依赖及依赖冲突

**直接依赖与传递依赖：**

假设现在A依赖B，B依赖C。那么有：

- B是A的直接依赖。
- C是A的传递依赖。

**什么是冲突？**

假设现在项目A（项目其实也是一个jar包或war包）依赖于jar包B，B依赖于jar包C，这里C的版本为1.1；现在我们向项目A中添加jar包D，D也依赖于jar包C，但这里C的版本是1.2。
现在问题出现了，jar包C在我们的项目中出现了两次，且版本不同，这时就出现了冲突（版本冲突）。

### Maven对冲突的调解原则

**1.第一声明者优先原则**

在这个原则下，当多个jar包导致了相同的传递依赖时，哪个jar包先在pom.xml中声明，Maven就使用谁的传递依赖。

假设我们在项目中依次添加了struts2-spring-plugin-2.3.24和spring-context-4.2.4两个jar包，它们的依赖层级关系如下：

![dependencies_conflict](images\dependencies_conflict.PNG)

我们可以发现现在出现冲突了：

比如spring-beans这个包分别被二者依赖，其中一个的版本是3.0.5，另一个版本是4.2.4。从图中我们也可以看到，当出现两个版本时，由于spring-context-4.2.4是后声明者，随意它依赖的spring-beans被忽略了（omitted for conflict with 3.0.5.RELEASE）。

**2.路径近者优先原则**

在这个原则下，直接依赖优先于作为传递依赖。

![dependencies_conflict](images\dependencies_conflict.PNG)

同样在这个层级关系中，我们可以发现struts2-spring-plugin依赖于spring-context，所以它引入版本为3.0.5的jar包。但由于我们直接声明了一个不同版本（4.2.4）的spring-context，所以struts2-spring-plugin依赖的spring-context-3.0.5.RELEASE就被忽略了（omitted for conflict with 3.0.5.RELEASE）。这是因为4.2.4的jar包是直接依赖，而3.0.5的jar包是传递依赖。

### 手工排除依赖

![dependencies_conflict](images\dependencies_conflict.PNG)

我们也可以手动排除依赖，只要选择要排除的版本将其exclude就可以了：

![exclude_dependency](images\exclude_dependency.PNG)

这样在pom.xml中会自动生成将其排除的代码：

```xml
<dependency>
    <groupId>org.apache.struts</groupId>
    <artifactId>struts2-spring-plugin</artifactId>
    <version>2.3.24</version>
    <exclusions>
        <!-- 对spring-beans进行了排除 -->
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.2.4.RELEASE</version>
    <!-- 对spring-beans进行了排除 -->
    <exclusions>
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

但上述代码同时屏蔽了两个版本的spring-beans传递依赖，我们这里选择版本高的jar包，删除第二个依赖中的exclusions标签：

```xml
<dependency>
    <groupId>org.apache.struts</groupId>
    <artifactId>struts2-spring-plugin</artifactId>
    <version>2.3.24</version>
    <exclusions>
        <!-- 对spring-beans进行了排除 -->
        <exclusion>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>4.2.4.RELEASE</version>
</dependency>
```

即我们可以通过exclusions标签，将不需要的传递依赖手工排除。

### 版本锁定

版本锁定是指我们人工确定项目中依赖的jar包的版本。通过标签dependencyManagement实现。

现在我们要把所有有关于spring的jar包的版本均设定为4.2.4：

```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.2.4.RELEASE</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

经过这样的设定以后，spring的所有相关包就都使用4.2.4版本了：

![spring4.2.4](images\spring4.2.4.PNG)

但要注意的是dependencyManagement标签只用于对版本进行锁定，这个标签并不会导入依赖。