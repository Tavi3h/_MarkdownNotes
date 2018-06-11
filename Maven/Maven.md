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

#### 将Maven项目导入Eclipse

导入项目前需要设置Eclipse，在Preferences-Maven-User Settings中设置User Settings为Maven\conf下的settings.xml。

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

### 使用Eclipse创建Maven项目

我们同样可以直接使用Eclipse创建Maven项目。

在New Project中选择Maven Project，根据向导设置相关Maven属性即可。

### 使用Eclipse将Java项目转为Maven

右键项目，在configure菜单中选择convert to Maven Project，根据向导设置相关Maven属性即可。

### 示例：Maven整合Struts2

以Maven整合Struts2为例。

创建Maven工程：

![create_maven_project](images\create_maven_project.PNG)

跳过原型选择：

![skip_archetype_selection](images\skip_archetype_selection.PNG)

填写项目信息，父工程为空：

![configure_project](images\configure_project.PNG)

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

整合Struts2，添加Struts2的依赖：

![add_dependency](images\add_dependency.PNG)

添加了Struts2核心包后，其核心包的依赖包会被自动添加：

![maven_dependencies](images\maven_dependencies.PNG)

添加


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

（在编写jsp页面或使用Servlet时，可能会发生找不到servlet-api的问题，这是因为我们的maven项目没有添加Apache Tomcat的库。在项目的build path中添加这个library就可以了。）

![success](images\success.PNG)

在tomcat上运行项目，项目右键Run as选择Maven build...，Goals命令填写：tomcat:run。填写完毕后点击Run运行项目。

成功的话，根据上述配置，访问 http://localhost:8080/maven_struts2/customerAction_execute 会在页面上显示Hello World，同时控制台输出Call CustomerAction.execute()。

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