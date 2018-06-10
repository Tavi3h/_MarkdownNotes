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

**Maven启用代理：**

如果你的公司正在建立一个防火墙，并使用HTTP代理服务器来阻止用户直接连接到互联网。如果您使用代理，Maven将无法下载任何依赖。为了使它工作，你必须声明在Maven的配置文件中设置代理服务器。

打开apache-maven-3.5.3\conf\setting.xml，找到proxies标签：

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

我们可以更改本地库的位置，同样在setting中进行修改：

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

保存文件即可。

### Maven中央存储库

当我们建立一个Maven项目时，Maven会检查pom.xml，以确定哪些依赖需要下载。首先Maven将从本地资源库查找依赖资源，如果没有找到，Maven会默认从[中央资源库](http://repo1.maven.org/maven2/){:target="_blank"}查找下载。Maven中央存储库是由Maven社区提供的资源库。它包含了大量的常用程序库。

要浏览中央存储仓库的内容可以从[The Search Engine for The Central Repository](http://search.maven.org/){:target="_blank"}进行搜索。

常用的Maven中央库镜像：

（如果需要使用镜像，同样在setting.xml中进行配置）

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

举例，目录结构如下：

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