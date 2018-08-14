[TOC]

# Spring

## 第一章 Spring概述

### 1.1 什么是Spring

*Spring是一个容器。*

Spring的主要作用就是为代码“解耦”，降低代码间的耦合度。

根据功能的不同，可以将一个系统中的代码分为**主业务逻辑**与**系统级业务逻辑**两类。它们各自具有鲜明的特点：主业务代码间逻辑联系紧密，有具体的专业业务应用场景，复用性相对较低；系统级业务逻辑相对功能独立，没有具体的专业业务场景，主要是为主业务提供系统级服务，如日志、安全、事务等，复用性强。

Spring根据代码的功能特点，将降低耦合度的方式分为了两类：**IoC和AOP，**它们也是Spring的核心。

- IoC（控制反转）使得主业务在相互调用过程中，不用再维护关系，即不用再自己创建要使用的对象，而是由Spring容器统一管理，自动“注入”。
- AOP（面向切面编程）使得系统级服务得到了最大复用，且不用再由程序员手工将系统级服务“混杂”到主业务逻辑中，而是由Spring容器统一完成“织入”。

**Spring是整个应用程序（View Level、Service Level & Dao Level）的管理者。整个应用中所有Bean的生命周期行为，均有Spring来管理。即整个应用中所有对象的创建、初始化、销毁以及对象间关联关系的维护，均由Spring进行管理。**

### 1.2 Spring体系结构

![Framework](images\Framework.png)

Spring由20多个模块组成，它们可以分为数据访问/集成（Data Access/Integration）、Web、面向切面编程（AOP, Aspect Oriented Programming）、应用服务器设备管理（Instrumentation）、消息发送（Messaging）、核心容器（Core Container）和测试（Test）。

### 1.3 Spring的特点

- **非侵入式：**所谓非侵入式指的是，Spring框架的API不会在业务逻辑上出现，即业务逻辑是POJO。由于业务逻辑中没有Spring的API，所以业务逻辑可以从Spring框架快速移植到其他框架，即与环境无关。
- **容器：**Spring作为一个容器，可以管理对象的生命周期、对象与对象之间的依赖关系。可以通过配置文件，来定义对象，以及设置与其他对象的依赖关系。
- **IoC：**控制反转（Inversion of Control），即创建被调用者的实例不是由调用者完成，而是由Spring容器完成，并注入调用者。当应用了IoC，一个对象依赖的其它对象会通过被动的方式传递进来，而不是这个对象自己创建或者查找依赖对象。即，不是对象从容器中查找依赖，而是容器在对象初始化时不等对象请求就主动将依赖传递给它。
- **AOP：**面向切面编程（AOP，Aspect Orient Programming），是一种编程思想，是面向对象编程OOP的补充。很多框架都实现了对AOP编程思想的实现。Spring也提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务（例如日志和事务管理）进行开发。应用对象只实现它们应该做的——完成业务逻辑——仅此而已。它们并不负责其它的系统级关注点，例如日志或事务支持。我们可以把日志、安全、事务管理等服务理解成一个“切面”，那么以前这些服务一直是直接写在业务逻辑的代码当中的，这有两点不好：首先业务逻辑不纯净；其次这些服务被很多业务逻辑反复使用，完全可以剥离出来做到复用。那么AOP就是这些问题的解决方案，可以把这些服务剥离出来形成一个“切面”，以期复用，然后将“切面”动态的“织入”到业务逻辑中，让业务逻辑能够享受到此“切面”的服务。

## 第二章 Spring与IoC

控制反转（IOC，Inversion of Control），是一个概念，是一种思想。指将传统上由程序代码直接操控的对象调用权交给容器，通过容器来实现对象的装配和管理。控制反转就是对对象控制权的转移，从程序代码本身反转到了外部容器。但是，需要注意，IoC也是有局限性的，其不能使用在分布式系统中。即其所依赖的反转到的外部容器，必须要与控制权出让方同处于一个JVM中。

IoC是一个概念，是一种思想，其实现方式多种多样。当前比较流行的实现方式有两种：依赖注入和依赖查找。依赖注入方式应用更为广泛。

- **依赖查找**：Dependency Lookup，DL，容器提供回调接口和上下文环境给组件，程序代码则需要提供具体的查找方式。比较典型的是依赖于JNDI系统的查找。
- **依赖注入**：Dependency Injection，DI，程序代码不做定位查询，这些工作由容器自行完成。

依赖注入（DI，Dependency Injection）是指程序运行过程中，若需要调用另一个对象协助时，无须在代码中创建被调用者，而是依赖于外部容器，由外部容器创建后传递给程序。

Spring的依赖注入对调用者与被调用者几乎没有任何要求，完全支持POJO之间依赖关系的管理。
依赖注入是目前最优秀的解耦方式。依赖注入让Spring的Bean之间以配置文件的方式组织在一起，而不是以硬编码的方式耦合在一起的。

### 2.1 Spring程序开发

#### 2.1.1 导入所需Jar包

四个基本Jar包：对应结构中的Core Container部分

- spring-beans-4.3.9.RELEASE
- spring-context-4.3.9.RELEASE
- spring-core-4.3.9.RELEASE
- spring-expression-4.3.9.RELEASE

Junit包：

- hamcrest-core-1.3
- junit-4.12

log4j2：

- log4j-api-2.9.1
- log4j-core-2.9.1
- commons-logging-1.2

#### 2.1.2 创建配置文件

*名字可以随意，官方建议使用applicationContext.xml。*

applicationContext.xml：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- bean definitions here -->

    </beans>

#### 2.1.3 创建测试程序

*源代码在Spring-01-primary下。*

定义服务接口，ISomeService.java：

    package tavish.bit.service;

    public interface ISomeService {
        
        public String doFirst();
        
        public void doSecond();
    }

定义服务实现类SomeServiceImpl.java：

    package tavish.bit.service;

    public class SomeServiceImpl implements ISomeService {

        @Override
        public String doFirst() {
            System.out.println("执行doFirst()方法");
            return "abcde";
        }

        @Override
        public void doSecond() {
            System.out.println("执行doSecond()方法");
        }
    }

不使用Spring进行测试，测试方法：

    @Test
    public void test01() {
        ISomeService service = new SomeServiceImpl();
        service.doFirst();
        service.doSecond();
    }

输出：

    执行doFirst()方法
    执行doSecond()方法

使用Spring：

添加配置文件applicationContext.xml：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 注册Service对象 -->
        <bean id="someService" class="tavish.bit.service.SomeServiceImpl" />
    </beans>

**
&lt;bean /&gt;：用于定义一个实例对象。一个实例对应一个bean元素。
id：该属性是Bean实例的唯一标识，程序通过id属性访问Bean，Bean与Bean间的依赖关系也是通过id属性关联的。
class：指定该Bean所属的类，注意这里只能是类，不能是接口。
**

使用Spring进行测试，测试方法：

    @Test
    public void test02() {
        
        // 1.加载Spring配置文件，创建Spring容器对象。
        @SuppressWarnings("resource")
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        
        // 2.从容器中获取指定Bean对象
        ISomeService service = (ISomeService) ac.getBean("someService");
        
        service.doFirst();
        service.doSecond();
        
    }

输出：

    执行doFirst()方法
    执行doSecond()方法

#### 2.1.4 接口容器

##### 2.1.4.1 ApplicationContext接口容器

ApplicationContext用于加载Spring的配置文件，在程序中充当“容器”的角色。其实现类有两个：

- ClassPathXmlApplicationContext：若Spring配置文件存放在项目的类路径下，则使用ClassPathXmlApplicationContext实现类进行加载。
- FileSystemXmlApplicationContext：若Spring配置文件存放在本地磁盘目录中，则使用FileSystemXmlApplicationContext实现类进行加载。

例如：

    // 使用ClassPathXmlApplicationContext
    ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");

    // 使用FileSystemXmlApplicationContext
    ApplicationContext ac = new FileSystemXmlApplicationContext("D:/applicationContext.xml");

若配置文件在项目根路径（与src文件夹同级）下，同样使用FileSystemXmlApplicationContext实现类进行加载。

例如：

    // 使用FileSystemXmlApplicationContext
    ApplicationContext ac = new FileSystemXmlApplicationContext("applicationContext.xml");

##### 2.1.4.2 BeanFactory接口容器

*注意：该容器的实现类XmlBeanFactory已经被标注为deprecate。*

BeanFactory接口对象也可作为Spring容器出现。BeanFactory接口是ApplicationContext接口的父接口。

若要创建BeanFactory容器，需要使用其实现类XmlBeanFactory，该类可以加载Spring配置文件。

而Spring配置文件以资源Resouce的形式出现在XmlBeanFactory类的构造器参数中。Resouce是一个接口，其具有两个实现类：

- ClassPathResource：指定类路径下的资源文件。
- FileSystemResource：指定项目根路径或本地磁盘路径下的资源文件。

在创建了BeanFactory容器后，便可使用其重载的getBean()方法，从容器中获取指定的Bean对象。

例如：

    // 使用BeanFactory和ClassPathResource，获取类路径下配置文件
    BeanFactory factory = new XmlBeanFactory(new ClassPathResource("appliactionContext.xml"));

    // 使用BeanFactory和ClassPathResource，获取当前项目跟路径下配置文件
    BeanFactory factory = new XmlBeanFactory(new FileSystemResource("appliactionContext.xml"));

##### 2.1.4.3 两个接口容器的区别

虽然这两个接口容器所要加载的Spring配置文件是同一个文件，但在代码中的这两个容器对象却不是同一个对象，即不是同一个容器：它们对于容器内对象的装配（创建）时机是不同的。

**
ApplicationContext容器中对象的装配时机：
**

ApplicationContext容器，会在容器对象初始化时，将其中的所有对象一次性全部装配好。以后代码中若要使用到这些对象，只需从内存中直接获取即可。执行效率较高。但占用内存。

例如：

    // 获取容器，此时容器中所有对象均已装配完毕
    ApplicationContext context = new ClassPathXmlAppliactionContext("applicationContext.xml");

**
BeanFactory容器中对象的装配时机：
**

BeanFactory容器，对容器中对象的装配与加载采用延迟加载策略，即在第一次调用getBean()时，才真正装配该对象。

例如：

    // 获取容器：此时容器中的对象均未进行装配
    BeanFactory factory = new XmlBeanFactory(new ClassPathResource("applicationContext.xml"));
    // 执行下面语句时，才会对该对象进行装配
    IStudentService service = (IStudentService) factory.getBean("studentService");

### 2.2 Bean的装配

*源代码在Spring-02-beanAssemble下。*

Bean的装配，即Bean对象的创建。容器根据代码要求创建Bean对象后再传递给代码的过程，称为Bean的装配。

#### 2.2.1 默认装配方式

代码通过getBean()方式从容器获取指定的Bean实例，容器首先会调用Bean类的无参构造器，创建空值的实例对象。

如果Bean类没有无参构造器，则会报错。

org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'someService' defined in class path resource [tavish/bit/beanAssemble/defaultmode/applicationContext.xml]: Instantiation of bean failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [tavish.bit.beanAssemble.defaultmode.SomeServiceImpl]: No default constructor found; nested exception is java.lang.NoSuchMethodException: tavish.bit.beanAssemble.defaultmode.SomeServiceImpl.<init>()

#### 2.2.2 动态工厂Bean

有些时候，项目中需要通过工厂类来创建Bean实例，而不能像前面例子中似的，直接由Spring容器来装配Bean实例。**使用工厂模式创建Bean实例，就会使工厂类与要创建的Bean类耦合到一起。但通过Spring容器，完全可以将调用者（测试类）与工厂类解耦。**

Spring提供了专门的使用动态工厂的装配Bean的方式。此时配置文件中至少要有两个Bean的定义：工厂类的Bean，与工厂类所要创建的对象Bean。

对于工厂类的Bean，与普通Bean的定义完全相同。而对于工厂类所要创建的Bean实例，由于其是由工厂类对象创建，所以，这个Bean无需class属性。但需要由factory-bean指定相应的工厂Bean，由factory-method指定创建所用方法。

示例：

定义工厂类，ServiceFactory.java：

    package tavish.bit.beanAssemble.dynamicFactoryMode;

    public class ServiceFactory {
        
        public ISomeService getSomeService() {
            return new SomeServiceImpl();
        }
    }

在配置文件中配置工厂及工厂生成的类：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 容器负责创建工厂 -->
        <bean
            id="serviceFactory"
            class="tavish.bit.beanAssemble.dynamicFactoryMode.ServiceFactory" />

        <!-- 设置由工厂创建的Bean： someservice对应的bean是由serviceFactory对应的工厂类调用名为getSomeService方法创建的。 -->
        <bean
            id="someService"
            factory-bean="serviceFactory"
            factory-method="getSomeService" />
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/dynamicFactoryMode/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        // 使用动态工厂创建Bean
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        service.doSecond();
    }

输出：

    Constructor Run
    执行doFirst()方法
    执行doSecond()方法

#### 2.2.3 静态工厂Bean

使用工厂模式中的静态工厂来创建实例Bean。

此时需要注意，静态工厂无需工厂实例，所以不用为静态工厂定义&lt;bean /&gt;。

而对于工厂所要创建的Bean，其不是由自己的类创建的，所以无需指定自己的类。但其是由工厂类创建的，所以需要指定所用工厂类。故class属性指定的是工厂类而非自己的类。当然，还需要通过factory-method属性指定工厂方法。

创建静态工厂类，ServiceFactory.java：

    package tavish.bit.beanAssemble.staticFactoryMode;

    public class ServiceFactory {
        
        public static ISomeService getSomeService() {
            return new SomeServiceImpl();
        }
    }

修改配置文件，静态工厂不用配置工厂Bean：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 指定Bean时，需要指定创建这个Bean所用的静态工厂类，以及具体的创建方法 -->
        <bean
            id="someService"
            class="tavish.bit.beanAssemble.staticFactoryMode.ServiceFactory"
            factory-method="getSomeService" />
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/staticFactoryMode/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        service.doSecond();
    }

输出：

    Constructor Run
    执行doFirst()方法
    执行doSecond()方法

#### 2.2.4 容器中Bean的作用域

当通过Spring容器创建一个Bean实例时，不仅可以完成Bean的实例化，还可以通过scope属性，为Bean指定特定的作用域。

Spring支持5种作用域：

- **singleton**：单例模式。即在整个Spring容器中，使用singleton定义的Bean将是单例的，只有一个实例。**Spirng默认使用该配置**。
- **prototype**：原型模式。即每次使用getBean方法获取的同一个&lt;bean /&gt;的实例都是一个新的实例。
- **request**：对于每次HTTP请求，都将会产生一个不同的Bean实例。
- **session**：对于每个不同的HTTP session，都将产生一个不同的Bean实例。
- **global session**：每个全局的HTTP session对应一个Bean实例。典型情况下，仅在使用portlet集群时有效，多个Web应用共享一个session。一般应用中，global-session与session是等同的。

注意：

1. 对于scope的值request、session与global session，只有在Web应用中使用Spring时，该作用域才有效。
2. **对于scope为singleton的单例模式，该Bean是在容器被创建时即被装配好了。**
3. **对于scope为prototype的原型模式，Bean实例是在代码中使用该Bean实例时才进行装配的。**

测试，设置scope="prototype"：

配置文件：applicationContext_scopePrototype.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean
            id="someService"
            class="tavish.bit.beanAssemble.beanScope.SomeServiceImpl"
            scope="prototype" />
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/beanScope/applicationContext_scopePrototype.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service1 = (ISomeService) context.getBean("someService");
        ISomeService service2 = (ISomeService) context.getBean("someService");
        
        System.out.println(service1 == service2);
    }

在获取容器（context）行加入断点，使用debug模式进行分析：

当程序执行了获取容器行时并没有构造器输出（Constructor Run），意味着此时并没有装配对象。

程序执行完毕，共输出：

    Constructor Run
    Constructor Run
    false

这意味着，一共创建了两个不同的service对象。即每次使用getBean方法获取的同一个&l;tbean /&gt;的实例都是一个新的实例。

测试，设置scope="singleton"或去掉scope属性设置：

配置文件，applicationContext_scopeDefault.xml：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean
            id="someService"
            class="tavish.bit.beanAssemble.beanScope.SomeServiceImpl" />
    </beans>

测试方法：

    @Test
    public void test02() {
        String configLocation = "tavish/bit/beanAssemble/beanScope/applicationContext_scopeDefault.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service1 = (ISomeService) context.getBean("someService");
        ISomeService service2 = (ISomeService) context.getBean("someService");
        
        System.out.println(service1 == service2);
    }

在获取容器（context）行加入断点，使用debug模式进行分析：

当程序执行了获取容器行时有构造器输出（Constructor Run），意味着容器进行了对象的装配。

程序执行完毕，共输出：

    Constructor Run
    true

这意味着，程序一共创建了一个service对象，即两次使用getBean方法获取的是同一个对象。

#### 2.2.5 Bean后处理器

Bean后处理器是一种**特殊的Bean**，容器中所有的Bean在初始化时，均会自动执行该类的两个方法。由于该Bean是由其它Bean自动调用执行，不是程序员手工调用，故此Bean无须id属性。

需要做的是，在Bean后处理器类方法中，只要对Bean类与Bean类中的方法进行判断，就可实现对指定的Bean的指定方法进行功能扩展与增强。方法返回的Bean对象，即是加强过的对象。

代码中需要自定义Bean后处理器类。该类就是实现了接口BeanPostProcessor的类。该接口中包含两个方法，分别在其它Bean初始化之前之后执行。它们的返回值为：功能被扩展或增强后的Bean对象。

*Bean的初始化完毕有一个标志：一个方法将会被执行。即当该方法被执行时，表示该Bean被初始化完毕。所以Bean后处理器中的两个方法的执行，实在这个方法之前之后执行。这个方法在后面会讲到。*

    public Object postProcessBeforeInitialization(Object bean, String beanId)throws BeansException

该方法会在目标**Bean初始化完毕之前**由容器自动调用。

    public Object postProcessAfterInitialization(Object bean, String beanId) throws BeansException

该方法会在目标**Bean初始化完毕之后**由容器自动调用。

它们的参数是：第一个参数是系统即将初始化的Bean实例，第二个参数是该Bean实例的id属性值。若Bean没有id就是name属性值。

示例：

定义Bean后处理器类，MyBeanPostProcessor：

    package tavish.bit.beanAssemble.postProcess;

    import org.springframework.beans.BeansException;
    import org.springframework.beans.factory.config.BeanPostProcessor;

    // Bean后处理器
    public class MyBeanPostProcessor implements BeanPostProcessor {
        @Override
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("执行***Before()***");
            return bean;
        }

        @Override
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("执行***After()***");
            return bean;
        }
    }

在配置文件中注册后处理器：

    <!-- 注册Bean后处理器 -->
    <bean class="tavish.bit.beanAssemble.postProcess.MyBeanPostProcessor" />

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/postProcess/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        service.doSecond();
    }

输出：

    Constructor Run
    执行***Before()***
    执行***After()***
    执行doFirst()方法
    执行doSecond()方法

**Bean后处理器应用：**

需求1：将doFirst()方法的返回值调整为全大写，但不能改变源码。
需求1：只对id为someService1的Bean类的doFirst()方法进行增强。

定义Bean后处理器：

    package tavish.bit.beanAssemble.postProcessApp;

    import java.lang.reflect.InvocationHandler;
    import java.lang.reflect.Method;
    import java.lang.reflect.Proxy;

    import org.springframework.beans.BeansException;
    import org.springframework.beans.factory.config.BeanPostProcessor;

    // Bean后处理器
    public class MyBeanPostProcessor implements BeanPostProcessor {

        // bean：当前调用执行Bean后处理器的对象。
        // beanName：当前调用执行Bean后处理器的对象的id。
        // 返回值为调用执行Bean后处理器的对象。
        
        @Override
        public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("执行***Before()***");
            return bean;
        }

        // 业务需求1，使用代理。
        @Override
        public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
            System.out.println("执行***After()***");
            
            Object proxy = null;
            // 业务需求2，判断beanName
            if ("someService1".equals(beanName)) {
                proxy = Proxy.newProxyInstance(bean.getClass().getClassLoader(), bean.getClass().getInterfaces(),
                        new InvocationHandler() {
                            @Override
                            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                                Object result = method.invoke(bean, args);
                                return result != null ? ((String) result).toUpperCase() : result;
                            }
                        });
                return proxy;
            }
            return bean;
        }
    }

配置文件，注册两个Bean类以及后处理器：

    <bean
        id="someService1"
        class="tavish.bit.beanAssemble.postProcessApp.SomeServiceImpl" />
    <bean
        id="someService2"
        class="tavish.bit.beanAssemble.postProcessApp.SomeServiceImpl" />

    <!-- 注册Bean后处理器 -->
    <bean class="tavish.bit.beanAssemble.postProcessApp.MyBeanPostProcessor" />

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/postProcessApp/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service1 = (ISomeService) context.getBean("someService1");
        System.out.println(service1.doFirst());
        System.out.println("==============================");
        ISomeService service2 = (ISomeService) context.getBean("someService2");
        System.out.println(service2.doFirst());
    }

输出：

    Constructor Run
    执行***Before()***
    执行***After()***
    Constructor Run
    执行***Before()***
    执行***After()***
    执行doFirst()方法
    ABCDE
    ==============================
    执行doFirst()方法
    abcde

#### 2.2.6 定制Bean的生命始末

可以为Bean定制初始化后的生命行为，也可以为Bean定制销毁前的生命行为。

这些方法需要在Bean类中事先定义好：是方法名随意的public void方法。

示例：

给实体类增加方法：

    package tavish.bit.beanAssemble.afterInitbeforeDestory;

    public class SomeServiceImpl implements ISomeService {
        
        public SomeServiceImpl() {
            System.out.println("Constructor Run");
        }

        @Override
        public String doFirst() {
            System.out.println("执行doFirst()方法");
            return "abcde";
        }

        @Override
        public void doSecond() {
            System.out.println("执行doSecond()方法");
        }

        public void afterInit() {
            System.out.println("初始化之后");
        }
        
        public void beforeDestory() {
            System.out.println("销毁之前");
        }
    }

在配置文件中注册这两个方法：

    <bean
        id="someService"
        class="tavish.bit.beanAssemble.afterInitbeforeDestory.SomeServiceImpl"
        init-method="afterInit" 
        destroy-method="beforeDestory"/>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/afterInitbeforeDestory/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("someService");
        
        service.doFirst();
        service.doSecond();
    }

输出：

    Constructor Run
    初始化之后
    执行doFirst()方法
    执行doSecond()方法

destroy-method的执行有两个要求：

- 被销毁对象的scope属性为singleton。
- 容器要被显示关闭。

修改测试方法，加入：

    ((ClassPathXmlApplicationContext)context).close();

输出：

    Constructor Run
    初始化之后
    执行doFirst()方法
    执行doSecond()方法
    销毁之前

#### 2.2.7 Bean的生命周期

Bean实例从创建到最后销毁，需要经过很多过程，执行很多生命周期方法。

1. 调用无参构造器，创建实例对象。
2. 调用参数的setter，为属性注入值。
3. 若Bean实现了BeanNameAware接口，则会执行接口方法setBeanName(String beanId)，使Bean类可以获取其在容器中的id名称。
4. 若Bean实现了BeanFactoryAware接口，则执行接口方法setBeanFactory(BeanFactory factory)，使Bean类可以获取到BeanFactory对象。
5. 若定义并注册了Bean后处理器BeanPostProcessor，则执行接口方法postProcessBeforeInitialization()。
6. 若Bean实现了InitializingBean接口，则执行接口方法afterPropertiesSet()。该方法是当属性设置完毕后执行，即Bean实例化结束。
7. 若设置了init-method方法，则执行。
8. 若定义并注册了Bean后处理器BeanPostProcessor，则执行接口方法postProcessAfterInitialization()。
9. 执行业务方法。
10. 若Bean实现了DisposableBean接口，则执行接口方法destroy()。
    - destroy()方法的执行同样需要singleton以及容器的显式关闭。
11. 若设置了destroy-method方法，则执行。

示例：

定义Bean类：

    package tavish.bit.beanAssemble.lifeCycle;

    import org.springframework.beans.BeansException;
    import org.springframework.beans.factory.BeanFactory;
    import org.springframework.beans.factory.BeanFactoryAware;
    import org.springframework.beans.factory.BeanNameAware;
    import org.springframework.beans.factory.DisposableBean;
    import org.springframework.beans.factory.InitializingBean;

    public class SomeServiceImpl
            implements ISomeService, BeanNameAware, BeanFactoryAware, InitializingBean, DisposableBean {

        @SuppressWarnings("unused")
        private String adao;

        public void setAdao(String adao) {
            System.out.println("Step2：执行setter方法");
            this.adao = adao;
        }

        public SomeServiceImpl() {
            System.out.println("Step1：对象的创建");
        }

        @Override
        public String doFirst() {
            System.out.println("Step9：执行业务方法");
            return "abcde";
        }

        @Override
        public void doSecond() {
            System.out.println("执行doSecond()方法");
        }

        public void afterInit() {
            System.out.println("Step7：初始化之后执行init-method");
        }

        public void beforeDestory() {
            System.out.println("Step11：销毁之前执行destroy-method");
        }

        @Override
        public void setBeanName(String name) {
            System.out.println("Step3：beanName = " + name);
        }

        @Override
        public void setBeanFactory(BeanFactory beanFactory) throws BeansException {
            System.out.println("Step4：获取到BeanFactory容器");
        }

        @Override
        public void afterPropertiesSet() throws Exception {
            System.out.println("Step6：该方法的完成标志Bean的初始化工作完毕");
        }

        @Override
        public void destroy() throws Exception {
            System.out.println("Step10：准备销毁工作，进入销毁的流程");
        }

    }

配置文件：

    <bean
        id="someService"
        class="tavish.bit.beanAssemble.lifeCycle.SomeServiceImpl"
        init-method="afterInit"
        destroy-method="beforeDestory">
        <!-- 属性注入 -->
        <property
            name="adao"
            value="AAA" />
    </bean>

    <bean class="tavish.bit.beanAssemble.lifeCycle.MyBeanPostProcessor" />

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/beanAssemble/lifeCycle/applicationContext.xml";
        
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Step1：对象的创建
    Step2：执行setter方法
    Step3：beanName = someService
    Step4：获取到BeanFactory容器
    Step5：执行Bean后处理器的Before方法
    Step6：该方法的完成标志Bean的初始化工作完毕
    Step7：初始化之后执行init-method
    Step8：执行After后处理器的Before方法
    Step9：执行业务方法
    Step10：准备销毁工作，进入销毁的流程
    Step11：销毁之前执行destroy-method

#### 2.2.8 &lt;bean/&gt;的id属性与name属性

一般情况下，命名&lt;bean/&gt;使用id属性，而不使用name属性。在没有id属性的情况下，name属性与id属性作用是相同的。但，当&lt;bean/&gt;中含有一些特殊字符时，就需要使用name属性了。

id的命名需要满足XML对ID属性命名规范：必须以字母开头，可以包含字母、数字、下划线、连字符、句号、冒号。且要求名称在容器中必须唯一。

name则可以包含各种字符，且对名称没有唯一性要求。若名称不唯一，则后面的会覆盖前面的。

### 2.3 基于XML的依赖注入

*以下内容源码在Spring-03-DependencyInjection-XML-1下。*

#### 2.3.1 注入分类

Bean实例在调用无参构造器创建了空值对象后，就要对Bean对象的属性进行初始化。初始化是由容器自动完成的，称为注入。根据注入方式的不同，常用的有两类：设值注入、构造注入
。
还有另外一种，实现特定接口注入。由于这种方式采用侵入式编程，污染了代码，所以几乎不用。

##### 2.3.1.1 设值注入

设值注入是指，通过setter方法传入被调用者的实例。这种注入方式简单、直观，因而在Spring的依赖注入中大量使用。

示例：

定义Bean类，School.java：

    package tavish.bit.di.setterInjection;

    public class School {
        private String sname;

        public void setSname(String sname) {
            this.sname = sname;
        }

        @Override
        public String toString() {
            return "School [sname=" + sname + "]";
        }
    }

定义Bean类，Student.java：

package tavish.bit.di.setterInjection;

    public class Student {
        private String name;
        private int age;
        private School school; // 域属性

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public void setSchool(School school) {
            this.school = school;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

在配置文件中进行设值注入：

当指定bean的某属性值为另一bean的实例时，通过ref指定它们间的引用关系。ref的值必须为某bean的id值。

    <bean
        id="mySchool"
        class="tavish.bit.di.setterInjection.School">
        <property name="sname" value="清华大学" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.setterInjection.Student">
        <property name="name" value="张三" />
        <property name="age" value="23" />
        <property name="school" ref="mySchool" />
    </bean>

对于其它Bean对象的引用，除了&lt;bean/&gt;标签的ref属性外，还可以使用&lt;ref/&gt;标签。

    <property name="school">
        <ref bean="mySchool" />
    </property>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/setterInjection/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
    }

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]

##### 2.3.1.2 构造注入

构造注入是指，在构造调用者实例的同时，完成被调用者的实例化。即，使用构造器设置依赖关系。

**构造注入不需要无参构造器，直接调用有参构造器。但是在创建Bean类时如果创建了带参构造器还是要同时给出无参构造器，避免其他地方出错。**

示例：

定义bean类，School.java不变。

修改Student类，去掉Setter方法，加入两个构造器。

    package tavish.bit.di.constructorInjection;

    public class Student {
        private String name;
        private int age;
        private School school; // 域属性
        
        public Student() {
            super();
        }

        public Student(String name, int age, School school) {
            super();
            this.name = name;
            this.age = age;
            this.school = school;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

修改配置文件，去掉property标签，使用constructor-arg标签：

使用index索引指示参数：

    <bean
        id="mySchool"
        class="tavish.bit.di.constructorInjection.School">
        <property name="sname" value="清华大学" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.constructorInjection.Student">
        <constructor-arg index="0" value="李四" />
        <constructor-arg index="1" value="24" />
        <constructor-arg index="2" ref="mySchool" />
    </bean>

使用name属性指示参数：

    <bean
        id="myStudent"
        class="tavish.bit.di.constructorInjection.Student">
        <constructor-arg name="name" value="李四" />
        <constructor-arg name="age" value="24" />
        <constructor-arg name="school" ref="mySchool" />
    </bean>

不使用index、name属性：此时要保证标签的顺序与构造器参数列表顺序一致。

    <bean
        id="myStudent"
        class="tavish.bit.di.constructorInjection.Student">
        <constructor-arg value="李四" />
        <constructor-arg value="24" />
        <constructor-arg ref="mySchool" />
    </bean>


测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/constructorInjection/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
    }

输出：

    Student [name=李四, age=24, school=School [sname=清华大学]]

#### 2.3.2 命名空间注入

对于设值注入与构造注入，在配置文件中，除了使用&lt;property&gt;或&lt;constructor-arg&gt;标签外，还可使用命名空间注入的方式，让注入的值以&lt;bean&gt;标签属性的方式出现。根据注入实现方式的不同，分为p命名空间注入与c命名空间注入。

- p命名空间注入：采用设值注入方式，故需要有相应的setter。
- c命名空间注入：采用构造注入方式，故需要有相应的构造器。

##### 2.3.2.1 p命名空间注入- - -设值注入

步骤一：修改配置文件头，添加相应约束，在其中声明p命名空间。

    xmlns:p="http://www.springframework.org/schema/p"

步骤二：去掉property标签，使用p:属性。

    <bean
        id="mySchool"
        class="tavish.bit.di.pnameSpace.setterInjection.School"
        p:sname="北京大学" />
    <bean
        id="myStudent"
        class="tavish.bit.di.pnameSpace.setterInjection.Student"
        p:name="王五"
        p:age="25"
        p:school-ref="mySchool" />

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/pnameSpace/setterInjection/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
    }

输出：

    Student [name=王五, age=25, school=School [sname=北京大学]]

##### 2.3.2.2 c命名空间注入- - -构造注入

步骤一：修改配置文件头，添加相应约束，在其中声明c命名空间。

    xmlns:c="http://www.springframework.org/schema/c"

步骤二：去掉constructor标签，使用c:属性。

    <bean
        id="myStudent"
        class="tavish.bit.di.cnameSpace.constructorInjection.Student"
        c:name="赵六"
        c:age="26"
        c:school-ref="mySchool" />

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/cnameSpace/constructorInjection/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
    }

输出：

    Student [name=赵六, age=26, school=School [sname=清华大学]]

#### 2.3.3 集合属性注入

*以下内容源码在Spring-03-DependencyInjection-XML-2下。*

定义Bean类，Some.java：

    package tavish.bit.di.setInjection;

    import java.util.Arrays;
    import java.util.List;
    import java.util.Map;
    import java.util.Properties;
    import java.util.Set;

    public class Some {
        private School[] schools;
        private List<String> myList;
        private Set<String> mySet;
        private Map<String, Object> myMap;
        private Properties myPros;

        public void setSchools(School[] schools) {
            this.schools = schools;
        }

        public void setMyList(List<String> myList) {
            this.myList = myList;
        }

        public void setMySet(Set<String> mySet) {
            this.mySet = mySet;
        }

        public void setMyMap(Map<String, Object> myMap) {
            this.myMap = myMap;
        }

        public void setMyPros(Properties myPros) {
            this.myPros = myPros;
        }

        @Override
        public String toString() {
            return "Some [schools=" + Arrays.toString(schools) + ", myList=" + myList + ", mySet=" + mySet + ", myMap="
                    + myMap + ", myPros=" + myPros + "]";
        }
    }

定义Bean类，School.java：

    package tavish.bit.di.setInjection;

    public class School {
        private String name;

        public void setName(String name) {
            this.name = name;
        }

        @Override
        public String toString() {
            return "School [name=" + name + "]";
        }
    }

配置文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean
            id="school1"
            class="tavish.bit.di.setInjection.School">
            <property
                name="name"
                value="清华大学" />
        </bean>
        <bean
            id="school2"
            class="tavish.bit.di.setInjection.School">
            <property
                name="name"
                value="北京大学" />
        </bean>

        <!-- 集合属性注入 -->
        <bean
            id="some"
            class="tavish.bit.di.setInjection.Some">
            <!-- 为数组注入的值 -->
            <property name="schools">
                <array>
                    <ref bean="school1" />
                    <ref bean="school2" />
                </array>
            </property>

            <!-- 为List注入值 -->
            <property name="myList">
                <list>
                    <value>张三</value>
                    <value>李四</value>
                </list>
            </property>

            <!-- 为Set注入值 -->
            <property name="mySet">
                <set>
                    <value>中国北京</value>
                    <value>大兴亦庄</value>
                </set>
            </property>

            <!-- 为Map注入值 -->
            <property name="myMap">
                <map>
                    <entry
                        key="QQ"
                        value="7654321" />
                    <entry
                        key="WeiXin"
                        value="1234567" />
                </map>
            </property>

            <!-- 为Properties注入值 -->
            <property name="myPros">
                <props>
                    <prop key="地址">房山良乡</prop>
                    <prop key="名称">北京理工大学</prop>
                </props>
            </property>
        </bean>
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/setInjection/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Some some = (Some) context.getBean("some");
        
        System.out.println(some);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Some [schools=[School [name=清华大学], School [name=北京大学]], myList=[张三, 李四], mySet=[中国北京, 大兴亦庄], myMap={QQ=7654321, WeiXin=1234567}, myPros={名称=北京理工大学, 地址=房山良乡}]

另一种写法，对非引用赋值简化：

    <!-- 为List注入值 -->
    <property name="myList" value="张三,李四" />

    <!-- 为Set注入值 -->
    <property name="mySet" value="长阳,篱笆房" />

    <!-- 为数组注入值 -->
    <property name="arr" value="abc,def" />

此时，这些集合内存放的都是基本类型值，而非对象的引用值。为存放对象引用值的集合赋值只能用ref。如：

    <!-- 为数组注入的值 -->
    <property name="schools">
        <array>
            <ref bean="school1" />
            <ref bean="school2" />
        </array>
    </property>
    <!-- 为List注入的值 -->
    <property name="students">
        <list>
            <ref bean="student1" />
            <ref bean="student2" />
        </list>
    </property>

#### 2.3.4 对域属性的自动注入

对于域属性的注入，也可不在配置文件中显示的注入。可以通过为<bean/>标签设置autowire属性值，为域属性进行隐式自动注入。根据自动注入判断标准的不同，可以分为两种：

- byName：根据名称自动注入
- byType：根据类型自动注入

##### 2.3.4.1 byName自动注入

**当配置文件中被调用者Bean的id值与代码中调用者Bean类的属性名相同时**，可使用byName方式，让容器自动将被调用者Bean注入给调用者Bean。容器是通过调用者的Bean类的属性名与配置文件的被调用者bean的id进行比较而实现自动注入的。

示例：

Bean类，Student.java：具有域属性school。

    package tavish.bit.di.fieldAutowire.byName;

    public class Student {
        private String name;
        private int age;
        private School school; // 域属性

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public void setSchool(School school) {
            this.school = school;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

配置文件：在Student的bean标签上增加属性autowire="byName"，将School的bean标签中的id改为与Student类中同名的school。

    <bean
        id="school"
        class="tavish.bit.di.fieldAutowire.byName.School">
        <property name="name" value="清华大学" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.fieldAutowire.byName.Student" autowire="byName">
        <property name="name" value="张三" />
        <property name="age" value="23" />
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/fieldAutowire/byName/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=张三, age=23, school=School [name=清华大学]]

##### 2.3.4.2 byType自动注入

使用byType方式自动注入，根据域属性类型到容器中查找具有is-a关系的Bean进行自动注入。但这样bean在配置文件中只能有一个。多于一个，容器就不知该匹配哪一个了。

示例：

配置文件：

    <bean
        id="mySchool"
        class="tavish.bit.di.fieldAutowire.byType.School">
        <property name="name" value="清华大学" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.fieldAutowire.byType.Student" autowire="byType">
        <property name="name" value="张三" />
        <property name="age" value="23" />
    </bean>

**
此时，使用class="tavish.bit.di.fieldAutowire.byType.School"或该class子类的bean标签只能有一个，否则容器就不知道该使用哪个Bean了。
**

配置文件：

    <bean
        id="primarySchool"
        class="tavish.bit.di.fieldAutowire.byType.PrimarySchool"/>
    <bean
        id="mySchool"
        class="tavish.bit.di.fieldAutowire.byType.School">
        <property name="name" value="清华大学" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.fieldAutowire.byType.Student" autowire="byType">
        <property name="name" value="张三" />
        <property name="age" value="23" />
    </bean>


测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/fieldAutowire/byType/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=张三, age=23, school=School [name=清华大学]]

#### 2.3.5 使用SpEL注入

SpEL，Spring Expression Language，即Spring EL表达式语言。即，在Spring配置文件中为Bean的属性注入值时，可直接使用SPEL表达式计算的结果。**SpEL表达式以#开头，后跟一对大括号。**用法： &lt;bean id="abc" value="#{…}"/&gt;。其文档中有其用法举例。在Spring框架解压目录\docs\spring-framework-reference\htmlsingle\index.html中。

示例，需求：

- 使Student类的name与Person类的pname相同。
- 使Person类的page随机生成，且不大于50.
- 使Student类的age随着Person类的page改变：
    + 若Person类的page大于25，则Student类的age为25；若小于25，则相同。

定义Person类：Person类需要getter方法，以及一个动态返回age的computeAge()方法。

    package tavish.bit.di.spel;

    public class Person {
        private String pname;
        private int page;

        public void setPname(String pname) {
            this.pname = pname;
        }

        public void setPage(int page) {
            this.page = page;
        }

        public String getPname() {
            return pname;
        }

        public int getPage() {
            return page;
        }
        
        // 动态返回age的方法
        public int computeAge() {
            return page > 25 ? 25 : page;
        }

        @Override
        public String toString() {
            return "Person [pname=" + pname + ", page=" + page + "]";
        }

    }

配置文件：使用SpEL表达式。

    <bean id="myPerson" class="tavish.bit.di.spel.Person">
        <property name="pname" value="李四" />
        <!-- 随机生成年龄 -->
        <property name="page" value="#{T(java.lang.Math).random() * 50}" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.spel.Student">

        <!-- #{myPerson['pname']}也可以写成#{myPerson.pname} -->

        <property name="name" value="#{myPerson['pname']}" />
        <property name="age" value="#{myPerson.computeAge()}" />

        <!-- 不使用Person类的方法，也可以直接使用三元表达式进行运算 -->
        <!-- <property name="age" value="#{myPerson.page > 25 ? 25 : myPerson.page}" /> -->
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/spel/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Person person = (Person) context.getBean("myPerson");
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(person);
        System.out.println(student);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Person [pname=李四, page=20]
    Student [name=李四, age=20]

    Person [pname=李四, page=39]
    Student [name=李四, age=25]

    Person [pname=李四, page=26]
    Student [name=李四, age=25]

#### 2.3.6 匿名Bean注入

*以下内容源码在Spring-03-DependencyInjection-XML-3下。*

匿名Bean：配置文件中没有id属性的bean，只能用在byType域属性自动注入中。

示例：

配置文件：

    <!-- 匿名Bean，没有id属性。只能在byType中使用 -->
    <bean
        class="tavish.bit.di.annoymousBean.School">
        <property name="name" value="清华大学" />
    </bean>
    <bean
        id="myStudent"
        class="tavish.bit.di.annoymousBean.Student" autowire="byType">
        <property name="name" value="张三" />
        <property name="age" value="23" />
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/annoymousBean/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=张三, age=23, school=School [name=清华大学]]

#### 2.3.7 内部匿名Bean注入

若不希望代码直接访问某个bean，即，在代码中通过getBean方法获取该Bean实例，则可将该Bean的定义放入调用者bean定义的内部。

示例：

配置文件：

    <bean
        id="myStudent"
        class="tavish.bit.di.innerAnnoymousBean.Student">
        <property name="name" value="张三" />
        <property name="age" value="23" />
        <property name="school">
            <!-- 定义内部匿名Bean -->
            <bean
                class="tavish.bit.di.innerAnnoymousBean.School">
                <property name="name" value="清华大学" />
            </bean>
        </property>
    </bean>

此时这个匿名Bean只是用来给school属性赋值用的，其他Bean不能访问它。

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/innerAnnoymousBean/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=张三, age=23, school=School [name=清华大学]]

#### 2.3.8 同类抽象Bean注入

当若干Bean实例同属于一个类，且这些实例的属性值又有相同值时，可以使用抽象Bean，以简化配置文件。

抽象Bean是用于让其它bean继承的。这个bean在Bean类中是不能通过getBean方法获取的。设置abstract属性为true来指明该bean为抽象bean， 默认值为false。 不过，该bean不为抽象bean时，也可被继承。只不过，在应用中，用于被继承的bean一般为抽象bean。

示例：

定义Bean类，Student.java：

    package tavish.bit.di.abstractBean.same;

    public class Student {
        private String name;
        private int age;
        private String school;
        private String department;

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public void setSchool(String school) {
            this.school = school;
        }

        public void setDepartment(String department) {
            this.department = department;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + ", department=" + department + "]";
        }
    }

配置文件：

    <!-- 
        同类抽象Bean，对公共部分进行抽取。
        同时指定这个Bean为abstract的，即不能通过getBean获取。
    -->
    <bean id="baseStudent" class="tavish.bit.di.abstractBean.same.Student" abstract="true">
        <property name="school" value="清华大学" />
        <property name="department" value="计算机学院" />
    </bean>
    
    <!-- 使用parent属性进行继承 -->
    <bean
        id="student1" parent="baseStudent">
        <property name="name" value="张三" />
        <property name="age" value="23" />
    </bean>
    <bean
        id="student2" parent="baseStudent">
        <property name="name" value="李四" />
        <property name="age" value="24" />
    </bean>
    <bean
        id="student3" parent="baseStudent">
        <property name="name" value="王五" />
        <property name="age" value="25" />
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/abstractBean/same/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student1 = (Student) context.getBean("student1");
        Student student2 = (Student) context.getBean("student2");
        Student student3 = (Student) context.getBean("student3");
        
        System.out.println(student1);
        System.out.println(student2);
        System.out.println(student3);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=张三, age=23, school=清华大学, department=计算机学院]
    Student [name=李四, age=24, school=清华大学, department=计算机学院]
    Student [name=王五, age=25, school=清华大学, department=计算机学院]

#### 2.3.9 异类抽象Bean注入

当若干不同的类对象具有相同的属性，且其值也相同时，可使用异类抽象Bean。

定义Bean类，Teacher.java

    package tavish.bit.di.abstractBean.alien;

    public class Teacher {
        private String name;
        private int workAge;
        private String school;
        private String department;

        public void setName(String name) {
            this.name = name;
        }

        public void setWorkAge(int workAge) {
            this.workAge = workAge;
        }

        public void setSchool(String school) {
            this.school = school;
        }

        public void setDepartment(String department) {
            this.department = department;
        }

        @Override
        public String toString() {
            return "Teacher [name=" + name + ", workAge=" + workAge + ", school=" + school + ", department=" + department
                    + "]";
        }

    }

配置文件：

    <!-- 
        异类抽象Bean，必须指定为抽象的。
        否则容器装配时会出错，因为这个Bean没有class属性。
     -->
    <bean id="base" abstract="true">
        <property name="school" value="清华大学" />
        <property name="department" value="计算机学院" />
    </bean>
    
    <bean
        id="student" 
        class="tavish.bit.di.abstractBean.alien.Student"
        parent="base">
        <property name="name" value="张三" />
        <property name="age" value="23" />
    </bean>

    <bean
        id="teacher"
        class="tavish.bit.di.abstractBean.alien.Teacher"
        parent="base">
        <property name="name" value="Tavish" />
        <property name="workAge" value="5" />
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/di/abstractBean/alien/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("student");
        Teacher teacher = (Teacher) context.getBean("teacher");
        
        System.out.println(student);
        System.out.println(teacher);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=张三, age=23, school=清华大学, department=计算机学院]
    Teacher [name=Tavish, workAge=5, school=清华大学, department=计算机学院]

**
同类、异类抽象Bean中abstract属性的区别：
**

- 在同类抽象Bean中abstract属性是可选的，设置为true后，该抽象Bean就不能使用getBean方法获取。
- 在异类抽象Bean中abstract属性是必选的，因其没有class属性，所以会导致在容器进行Bean装配时报错。

#### 2.3.10 为应用指定多个Spring配置文件

在实际应用里，随着应用规模的增加，系统中Bean数量也大量增加，导致配置文件变得非常庞大、臃肿。为了避免这种情况的产生，提高配置文件的可读性与可维护性，可以将Spring配置文件分解成多个配置文件。

##### 2.3.10.1 平等关系的配置文件

将配置文件分解为地位平等的多个配置文件，并将所有配置文件的路径定义为一个String数组，将其作为容器初始化参数。其将与可变参的容器构造器匹配。

    ClassPathXmlApplicationContext(String... configLocations)

此时各配置文件间为并列关系，不分主次。

示例：

配置文件，applicationContext-base.xml：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">
        <bean id="base" abstract="true">
            <property name="school" value="清华大学" />
            <property name="department" value="计算机学院" />
        </bean>
    </beans>

配置文件，applicationContext-beans.xml：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean
            id="student" 
            class="tavish.bit.di.multiConfig.parity.Student"
            parent="base">
            <property name="name" value="张三" />
            <property name="age" value="23" />
        </bean>

        <bean
            id="teacher"
            class="tavish.bit.di.multiConfig.parity.Teacher"
            parent="base">
            <property name="name" value="Tavish" />
            <property name="workAge" value="5" />
        </bean>
    </beans>

测试方法1：

    @Test
    public void test01() {
        
        // 使用可变参数列表
        String configLocation1 = "tavish/bit/di/multiConfig/parity/applicationContext-base.xml";
        String configLocation2 = "tavish/bit/di/multiConfig/parity/applicationContext-beans.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation1, configLocation2);

        Student student = (Student) context.getBean("student");
        Teacher teacher = (Teacher) context.getBean("teacher");

        System.out.println(student);
        System.out.println(teacher);

        ((ClassPathXmlApplicationContext) context).close();
    }

测试方法2：

    @Test
    public void test02() {
        
        // 使用数组形式的可变参数列表
        String configLocation1 = "tavish/bit/di/multiConfig/parity/applicationContext-base.xml";
        String configLocation2 = "tavish/bit/di/multiConfig/parity/applicationContext-beans.xml";
        String[] configLocations = {configLocation1, configLocation2};
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocations);

        Student student = (Student) context.getBean("student");
        Teacher teacher = (Teacher) context.getBean("teacher");

        System.out.println(student);
        System.out.println(teacher);

        ((ClassPathXmlApplicationContext) context).close();
    }

**除了使用可变参数列表，还可以使用通配符，这也是常用方法。**

测试方法3：

    @Test
    public void test03() {
        
        // 使用通配符
        String configLocation = "tavish/bit/di/multiConfig/parity/applicationContext-*.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student = (Student) context.getBean("student");
        Teacher teacher = (Teacher) context.getBean("teacher");

        System.out.println(student);
        System.out.println(teacher);

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    Student [name=张三, age=23, school=清华大学, department=计算机学院]
    Teacher [name=Tavish, workAge=5, school=清华大学, department=计算机学院]

##### 2.3.10.2 包含关系的配置文件

各配置文件中有一个总文件，总配置文件将各其它子文件通过&lt;import/&gt;引入。在Java代码中只需要使用总配置文件对容器进行初始化即可。

总主配置文件，applicationContext.xml：

    <!--         
    <import resource="applicationContext-base.xml"/>
    <import resource="applicationContext-beans.xml"/>
     -->
    <import resource="applicationContext-*.xml" />

**注意：当这里使用通配符时，切记总主配置文件不能也符合通配符的匹配模式，否则会形成递归加载配置文件，造成报错。**

即在这里，总配置文件不能命名为：applicationContext-xxx.xml。

### 2.4 基于注解的依赖注入

对于DI使用注解，将不再需要在Spring配置文件中声明Bean实例。Spring中使用注解，需要在原有Spring运行环境基础上再做一些改变，完成以下三个步骤。

*源代码在Spring-04-DependencyInjection-Annotation-1下。*

步骤一：

导入AOP的Jar包（spring-aop-4.3.9.RELEASE.jar）。因为注解的后台实现用到了AOP编程。

步骤二：

更换配置文件头，即添加相应的约束。

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context 
            http://www.springframework.org/schema/context/spring-context.xsd"> 

    </beans>

步骤三：

需要在Spring配置文件中配置组件扫描器，用于在指定的基本包中扫描注解。

    <context:component-scan base-package="" />

示例：

定义Bean类，School.java：

    package tavish.bit.annoDi;

    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;

    @Component("mySchool")
    public class School {
        
        @Value("清华大学")
        private String sname;

        public void setSname(String sname) {
            this.sname = sname;
        }

        @Override
        public String toString() {
            return "School [sname=" + sname + "]";
        }
    }

定义Bean类，Student.java：

    package tavish.bit.annoDi;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;

    @Component("myStudent")
    public class Student {
        
        @Value("张三")
        private String name;
        @Value("23")
        private int age;
        @Autowired // byType自动注入
        private School school; 

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public void setSchool(School school) {
            this.school = school;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

配置文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context 
            http://www.springframework.org/schema/context/spring-context.xsd">

        <!-- 在指定的基本包中扫描注解 -->
        <context:component-scan base-package="tavish.bit.annoDi" />
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/applicationContext.xml";
        
        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        Student student = (Student) context.getBean("myStudent");
        
        System.out.println(student);
    }

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]

#### 2.4.1 组件扫描器的写法

当有多个包时可以定义多个context:component-scan，例如：

    <context:component-scan base-package="tavish.bit.annoDi1" />
    <context:component-scan base-package="tavish.bit.annoDi2" />
    <context:component-scan base-package="tavish.bit.annoDi3" />

通常在实际开发中，我们使用通配符写法：

    <context:component-scan base-package="tavish.bit.*" />

或可以更加省略：

    <context:component-scan base-package="tavish.bit" />

二者的区别在于：

- tavish.bit.*：扫描tavish.bit的所有子包，但不扫描tavish.bit这个包。
- tavish.bit：扫描tavish.bit这个包及其所有子包。

*无论使用上述哪写法都要注意冲突问题。*

#### 2.4.2 定义Bean@Component

需要在类上使用注解@Component，该注解的value属性用于指定该bean的id值。

另外，Spring还提供了3个功能基本和@Component等效的注解：

- @Repository 用于对DAO实现类进行注解
- @Service 用于对Service实现类进行注解
- @Controller 用于对Controller实现类进行注解

之所以创建这三个功能与@Component等效的注解，是为了以后对其进行功能上的扩展，使它们不再等效。

#### 2.4.3 Bean作用域@Scope

需要在类上使用注解@Scope，其value属性用于指定作用域。默认为singleton。

示例：

    @Scope("prototype")
    @Component("myStudent")
    public class Student {...}

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/applicationContext.xml";

        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student1 = (Student) context.getBean("myStudent");
        Student student2 = (Student) context.getBean("myStudent");

        System.out.println(student1);
        System.out.println(student2);
        System.out.println(student1 == student2);
    }

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]
    Student [name=张三, age=23, school=School [sname=清华大学]]
    false

#### 2.4.4 基本类型属性注入@Value

需要在属性上使用注解@Value，该注解的value属性用于指定要注入的值。

**
使用该注解完成属性注入时，类中无需setter。
**

示例：

去掉School类的setter方法：

    package tavish.bit.annoDi;

    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;

    @Component("mySchool")
    public class School {
        
        @Value("清华大学")
        private String sname;

        @Override
        public String toString() {
            return "School [sname=" + sname + "]";
        }
    }

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/applicationContext.xml";

        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student1 = (Student) context.getBean("myStudent");
        Student student2 = (Student) context.getBean("myStudent");

        System.out.println(student1);
        System.out.println(student2);
        System.out.println(student1 == student2);
    }

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]
    Student [name=张三, age=23, school=School [sname=清华大学]]
    false

#### 2.4.5 注入方式@Autowired

要使用byName注入方式，不仅需要@Qualifier同时也需要@Autowired。

示例：
    
    // byName自动注入
    @Autowired 
    @Qualifier("mySchool")
    private School school; 

与此不同的是，byType方式只需要@Autowired。

    // byType自动注入
    @Autowired 
    private School school; 

#### 2.4.5 域属性注解@Resource

Spring提供了对JSR-250规范中定义@Resource标准注解的支持。@Resource注解既可以按名称匹配Bean，也可以按类型匹配Bean。使用该注解，要求JDK必须是6及以上版本。

##### 2.4.5.1 按类型注入域属性

*源代码在Spring-04-DependencyInjection-Annotation-2下。*

@Resource注解若不带任何参数，则会按照类型（byType）进行Bean的匹配注入。

示例：

Student类的域属性：

    @Resource
    private School school; 

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/resource/applicationContext.xml";

        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student1 = (Student) context.getBean("myStudent");
        Student student2 = (Student) context.getBean("myStudent");

        System.out.println(student1);
        System.out.println(student2);
        System.out.println(student1 == student2);
    }

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]
    Student [name=张三, age=23, school=School [sname=清华大学]]
    false

##### 2.4.5.2 按名称注入域属性

@Resource注解指定其name属性，则name的值即为按照名称进行匹配的Bean的id。

示例：

Student类的域属性：

    @Resource(name="mySchool")
    private School school; 

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/resource/applicationContext.xml";

        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student1 = (Student) context.getBean("myStudent");
        Student student2 = (Student) context.getBean("myStudent");

        System.out.println(student1);
        System.out.println(student2);
        System.out.println(student1 == student2);
    }

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]
    Student [name=张三, age=23, school=School [sname=清华大学]]
    false

#### 2.4.7 Bean的生命始末注解

在方法上使用@PostConstruct，与原来的init-method等效。在方法上使用@PreDestroy，与destroy-method等效。

示例：

在Student类中加入两个方法，同时去掉其@Scope注解：

    @PostConstruct
    public void afterInit() {
        System.out.println("初始化完毕后");
    }
    
    @PreDestroy
    public void beforeDestory() {
        System.out.println("销毁前");
    }

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/lifeCycle/applicationContext.xml";

        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student1 = (Student) context.getBean("myStudent");
        Student student2 = (Student) context.getBean("myStudent");

        System.out.println(student1);
        System.out.println(student2);
        System.out.println(student1 == student2);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    初始化完毕后
    Student [name=张三, age=23, school=School [sname=清华大学]]
    Student [name=张三, age=23, school=School [sname=清华大学]]
    true
    销毁前

#### 2.4.8 使用JavaConfig进行配置

JavaConfig，是在Spring 3.0开始从一个独立的项目并入到Spring中的。JavaConfig可以看成一个用于完成Bean装配的配置文件，即Spring容器，只不过该容器不是XML文件，而是由程序员使用Java自己编写的Java类。

示例：

定义实体类：

School.java：

    package tavish.bit.annoDi.javaConfig.byName;

    public class School {

        private String sname;

        public School(String sname) {
            super();
            this.sname = sname;
        }

        public School() {
            super();
        }

        public void setSname(String sname) {
            this.sname = sname;
        }

        @Override
        public String toString() {
            return "School [sname=" + sname + "]";
        }
    }

Student.java：

    package tavish.bit.annoDi.javaConfig.byName;

    public class Student {

        private String name;

        private int age;

        private School school;

        public Student(String name, int age) {
            super();
            this.name = name;
            this.age = age;
        }

        public Student() {
            super();
        }

        public void setSchool(School school) {
            this.school = school;
        }

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(int age) {
            this.age = age;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

定义JavaConfig类：

    package tavish.bit.annoDi.javaConfig.byType;

    import org.springframework.beans.factory.annotation.Autowire;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration 
    public class MyJavaConfig {
        
        @Bean(name="mySchool")
        public School mySchoolCreator() {
            return new School("北京大学");
        }

        @Bean(name="myStudent", autowire=Autowire.BY_TYPE)
        public Student myStudentCreator() {
            return new Student("李四", 24);
        }
    }

- @Configuration：表明该类作为Spring容器使用。
- @Bean：方法返回的对象Bean的名称（id）为name指定名称。
- @autowire属性：指定自动注入的方式。
    + Autowire.BY_TYPE：表示使用byType自动注入。此时代码示例如上。
    + Autowire.BY_NAME：表示使用byName自动注入。此时@Bean的name值要与类属性中的属性名一致。

定义JavaConfig类：

    // 此Bean的name值要与Student类中的属性名一致
    @Bean(name = "school")
    public School mySchoolCreator() {
        return new School("北京大学");
    }

    @Bean(name = "myStudent", autowire = Autowire.BY_NAME)
    public Student myStudentCreator() {
        return new Student("李四", 24);
    }

测试方法：使用byType方式。

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/javaConfig/byType/applicationContext.xml";

        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student = (Student) context.getBean("myStudent");
        School school = (School) context.getBean("mySchool");

        System.out.println(student);
        System.out.println(school);
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    Student [name=李四, age=24, school=School [sname=北京大学]]
    School [sname=北京大学]

#### 2.4.9 使用junit4测试Spring

*源代码在Spring-04-DependencyInjection-Annotation-3下。*

这里的测试指的是体系结构中的Test部分。需导入Jar包：spring-test-4.3.9.RELEASE。

示例：

定义实体类，Student.java：

    package tavish.bit.annoDi.test;

    import javax.annotation.Resource;

    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;

    @Component("myStudent")
    public class Student {
        
        @Value("张三")
        private String name;
        
        @Value("23")
        private int age;
        
        @Resource(name="mySchool")
        private School school; 

        public void setSchool(School school) {
            this.school = school;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

定义实体类，School.java：

    package tavish.bit.annoDi.test;

    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;

    @Component("mySchool")
    public class School {
        
        @Value("清华大学")
        private String sname;

        @Override
        public String toString() {
            return "School [sname=" + sname + "]";
        }
    }

测试类：

    package tavish.bit.annoDi.test;

    import javax.annotation.Resource;

    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(locations="classpath:tavish/bit/annoDi/test/applicationContext.xml")
    public class MyTest {

        @Resource(name="myStudent")
        private Student student;
        
        @Test
        public void test01() {
            System.out.println(student);
        }
    }

- @RunWith：指定运行环境。
- @ContextConfiguration：指定配置文件位置。

这里使用了@Resource的byName方式为student赋值。

输出：

    Student [name=张三, age=23, school=School [sname=清华大学]]

#### 2.4.10 注解与XML共同使用

注解的好处是，配置方便，直观。但其弊端也显而易见：以硬编码的方式写入到了Java代码中，其修改是需要重新编译代码的。

XML配置方式的最大好处是，对其所做修改，无需编译代码，只需重启服务器即可将新的配置加载。

若注解与XML同用：**XML的优先级要高于注解。**

这样做的好处是，需要对某个Bean做修改，只需修改配置文件即可。但是，此时，**Bean类要有setter或构造器（根据注入方法的不同）。**

示例：

bean类，School.java：

    package tavish.bit.annoDi.xmlWithAnno;

    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.stereotype.Component;

    @Component("mySchool")
    public class School {

        @Value("清华大学")
        private String sname;

        public void setSname(String sname) {
            this.sname = sname;
        }

        @Override
        public String toString() {
            return "School [sname=" + sname + "]";
        }
    }

bean类，Student.java：

    package tavish.bit.annoDi.xmlWithAnno;

    import javax.annotation.Resource;

    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Scope;
    import org.springframework.stereotype.Component;

    @Scope("prototype")
    @Component("myStudent")
    public class Student {

        @Value("张三")
        private String name;

        @Value("23")
        private int age;

        @Resource(name = "mySchool")
        private School school;

        public void setName(String name) {
            this.name = name;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public void setSchool(School school) {
            this.school = school;
        }

        @Override
        public String toString() {
            return "Student [name=" + name + ", age=" + age + ", school=" + school + "]";
        }
    }

    配置文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context 
            http://www.springframework.org/schema/context/spring-context.xsd">

        <!-- 在指定的基本包中扫描注解 -->
        <context:component-scan base-package="tavish.bit.annoDi.xmlWithAnno" />
        
        <bean id="mySchool" class="tavish.bit.annoDi.xmlWithAnno.School">
            <property name="sname" value="北京大学" />
        </bean>
        
        <bean id="myStudent" class="tavish.bit.annoDi.xmlWithAnno.Student">
            <property name="name" value="李四" />
            <property name="age" value="24" />
            <property name="school" ref="mySchool" />
        </bean>
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/annoDi/xmlWithAnno/applicationContext.xml";

        @SuppressWarnings("resource")
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        Student student = (Student) context.getBean("myStudent");

        System.out.println(student);
    }

输出：

    Student [name=李四, age=24, school=School [sname=北京大学]]

此时XML的设置覆盖了注解。

## 第三章 Spring与AOP

### 3.1 AOP概述

#### 3.1.1 AOP简介

AOP（Aspect Orient Programming），面向切面编程，是面向对象编程OOP的一种补充。面向对象编程是从静态角度考虑程序的结构，而面向切面编程是从动态角度考虑程序运行过程。

AOP底层，就是采用动态代理模式实现的。采用了两种代理：**JDK的动态代理，与CGLIB的动态代理。**

面向切面编程，就是将交叉业务逻辑封装成切面，利用AOP容器的功能将切面织入到主业务逻辑中。所谓交叉业务逻辑是指，通用的、与主业务逻辑无关的代码，如安全检查、事务、日志等。

若不使用AOP，则会出现代码纠缠，即交叉业务逻辑与主业务逻辑混合在一起。这样，会使主业务逻辑变的混杂不清。

例如，转账，在真正转账业务逻辑前后，需要权限控制、日志记录、加载事务、结束事务等交叉业务逻辑，而这些业务逻辑与主业务逻辑间并无直接关系。但，它们的代码量所占比重能达到总代码量的一半甚至还多。它们的存在，不仅产生了大量的“冗余”代码，还大大干扰了主业务逻辑---转账。

#### 3.1.2 AOP编程术语

- **切面（Aspect）**：切面泛指交叉业务逻辑。上例中的事务处理、日志处理就可以理解为切面。常用的切面有通知与顾问。实际就是对主业务逻辑的一种增强。
- **织入（Weaving）**：织入是指将切面代码插入到目标对象的过程。
- **连接点（JoinPoint）**：连接点指切面可以织入的方法。通常业务逻辑中的方法均为连接点。
- **切入点（PointCut）**：切入点指切面具体织入的位置。被标记为final的方法是不能作为连接点与切入点的。因为最终的是不能被修改的，不能被增强的。
- **目标对象（Target）**：目标对象指将要被增强的对象。即包含主业务逻辑的类的对象。
- **通知（Advice）**：通知是切面的一种实现，可以完成简单织入功能（织入功能就是在这里完成的）。InvocationHandler就可以理解为是一种通知。换个角度来说，通知定义了增强代码切入到目标代码的时间点，是目标方法执行之前执行，还是之后执行等。通知类型不同，切入时间不同。切入点定义切入的位置，通知定义切入的时间。
- **顾问（Advisor）**：顾问是切面的另一种实现，能够将通知以更为复杂的方式织入到目标对象中，是将通知包装为更复杂切面的装配器。顾问包装了通知，既可以指定切入时间，又能指定切入位置。
- **代理（Proxy）**：代理对象 = 目标对象 + 交叉业务逻辑。

#### 3.1.3 AOP编程环境搭建

导入Jar包：

- spring-aop-4.3.9.RELEASE
- aopalliance

配置文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd">

    </beans>

### 3.2 通知

*源代码在Spring-06-AOP-advice下。*

#### 3.1.1 通知的用法步骤

对于通知的定义、配置与使用，主要分为以下几步：

**第一步：定义目标类**

定义目标类，就是定义之前的普通Bean类，也就是即将被增强的Bean类。

示例：

    package tavish.bit.aop.methodBeforeAdvice.service;

    // 目标类
    public class SomeServiceImpl implements ISomeService {

        @Override
        public void doFirst() {
            System.out.println("执行主业务逻辑doFisrt()");
        }

        @Override
        public void doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
        }
    }

**第二步：定义通知类**

通知类是指，实现了相应通知类型接口的类。当前，实现了这些接口，就要实现这些接口中的方法，而这些方法的执行，则是根据不同类型的通知，其执行时机不同。

- 前置通知：在目标方法执行之前执行。
- 后置通知：在目标方法执行之后执行。
- 环绕通知：在目标方法执行之前与之后均执行。
- 异常处理通知：在目标方法执行过程中，若发生指定异常，则执行通知中的方法。

**第三步：注册目标类**

即在Spring配置文件中注册目标对象Bean。

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.methodBeforeAdvice.service.SomeServiceImpl" />

**第四步：注册通知切面**

即在Spring配置文件中注册定义的通知对象Bean。

以前置通知为例：

    <!-- 注册前置通知 -->
    <bean id="myMethodBeforeAdvice" class="tavish.bit.aop.methodBeforeAdvice.advice.MyMethodBeforeAdvice"/>

**第五步：注册代理工厂Bean类对象**

示例：

    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <!-- <property name="targetName" value="someService"/> -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myMethodBeforeAdvice"/>
    </bean>

这里的代理使用的是ProxyFactoryBean类。代理对象的配置，是与JDK的Proxy代理参数是一致的，都需要指定三部分：目标类，接口，切面。

指定目标类：

    <property name="target" ref="目标对象Bean的id" />

指定目标对象的Bean的id。也可写为如下形式：

    <property name="targetName" value="目标对象Bean的id" />
    <property name="proxyInterfaces" value="接口全限定性名" />

指定接口：

设置目标对象所实现的业务接口，要求给出接口的全既定性类名。此属性可以不进行设置，因为打开ProxyFactoryBean的源码，可以看到其有个自动检测目标类的所有接口属性autodetectInterfaces，默认值为true。即不设置也可以自动检测到。当然，此时使用的是jdk的Proxy动态代理。

如果目标对象没有实现业务接口，则可以不设置。此时使用的是CGLIB动态代理。

指定切面：

    <property name="interceptorNames" value= "通知的id" />

指定切面，这里指通知。注意，这里对于id的指定使用的是value属性，而非ref。因为该属性名为xxxNames，是名称，所以其值为字符串，而非对象。

**第六步：客户端访问动态代理对象**

例如：

    ISomeService service = (ISomeService) context.getBean("serviceProxy");

这里serviceProxy指的是代理对象。

#### 3.2.2 通知详解

##### 3.2.2.1 前置通知----MethodBeforeAdvice

定义前置通知，需要实现MethodBeforeAdvice接口。该接口中有一个方法before()，会在目标方法执行之前执行。前置通知的特点：

- 在目标方法执行之前先执行。
- 不改变目标方法的执行流程，前置通知代码不能阻止目标方法执行。
- 不改变目标方法执行的结果。

示例：

定义主业务接口与目标类：

主业务接口：

    package tavish.bit.aop.methodBeforeAdvice.service;

    // 主业务接口
    public interface ISomeService {
        // 目标方法
        void doFirst();
        void doSecond();
    }

目标类：

    package tavish.bit.aop.methodBeforeAdvice.service;

    // 目标类
    public class SomeServiceImpl implements ISomeService {

        @Override
        public void doFirst() {
            System.out.println("执行主业务逻辑doFisrt()");
        }

        @Override
        public void doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
        }
    }

定义前置通知：

    package tavish.bit.aop.methodBeforeAdvice.advice;

    import java.lang.reflect.Method;

    import org.springframework.aop.MethodBeforeAdvice;

    // 前置通知
    public class MyMethodBeforeAdvice implements MethodBeforeAdvice {

        // 前置通知方法：在目标方法执行之前执行。
        // method：目标方法
        // args：目标方法的参数列表
        // target：目标对象
        @Override
        public void before(Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行前置通知方法");
        }
    }

配置文件配置：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.methodBeforeAdvice.service.SomeServiceImpl" />
    
    <!-- 注册前置通知 -->
    <bean id="myMethodBeforeAdvice" class="tavish.bit.aop.methodBeforeAdvice.advice.MyMethodBeforeAdvice"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <!-- <property name="targetName" value="someService"/> -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myMethodBeforeAdvice"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/methodBeforeAdvice/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        ISomeService service = (ISomeService) context.getBean("serviceProxy");

        service.doFirst();
        
        System.out.println("============");
        
        service.doSecond();

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    执行前置通知方法
    执行主业务逻辑doFisrt()
    ============
    执行前置通知方法
    执行主业务逻辑doSecond()

##### 3.2.2.2 后置通知----AfterReturningAdvice

定义后置通知，需要实现接口AfterReturningAdvice。该接口中有一个方法afterReturning ()，会在目标方法执行之后执行。后置通知的特点：

- 在目标方法执行之后执行。
- 不改变目标方法的执行流程，后置通知代码不能阻止目标方法执行。
- 不改变目标方法执行的结果。
- 虽然可以获取到目标方法的返回结果，但不能修改。

示例：

主业务接口与目标类同上一节，略。

定义后置通知：

    package tavish.bit.aop.afterReturningAdvice.advice;

    import java.lang.reflect.Method;

    import org.springframework.aop.AfterReturningAdvice;

    // 后置通知：
    public class MyAfterReturningAdvice implements AfterReturningAdvice {

        // 后置通知方法：在目标方法执行之后执行。
        // returnValue：目标方法返回值
        // method：目标方法
        // args：目标方法的参数列表
        // target：目标对象
        @Override
        public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行后置通知方法");
        } 
    }

配置文件：

    <!-- 注册后置通知 -->
    <bean id="myAfterReturningAdvice" class="tavish.bit.aop.afterReturningAdvice.advice.MyAfterReturningAdvice"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <property name="target" ref="someService"/>
        <property name="interceptorNames" value="myAfterReturningAdvice"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/afterReturningAdvice/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        ISomeService service = (ISomeService) context.getBean("serviceProxy");

        service.doFirst();
        
        System.out.println("============");
        
        service.doSecond();

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    执行主业务逻辑doFisrt()
    执行后置通知方法
    ============
    执行主业务逻辑doSecond()
    执行后置通知方法

##### 3.2.2.3 环绕通知----MethodInterceptor

定义环绕通知，需要实现MethodInterceptor接口。

**
环绕通知，也叫方法拦截器，可以在目标方法调用之前及之后做处理，可以改变目标方法的返回值，也可以改变程序执行流程。
**

*注意， org.aopalliance.intercept.MethodInterceptor才是需要的包。*

示例：

定义主业务接口及目标类：

    // 主业务接口
    public interface ISomeService {
        // 目标方法
        String doFirst();
        void doSecond();
    }

package tavish.bit.aop.methodInterceptor.service;

    // 目标类
    public class SomeServiceImpl implements ISomeService {

        @Override
        public String doFirst() {
            System.out.println("执行主业务逻辑doFisrt()");
            return "abcde";
        }

        @Override
        public void doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
        }
    }

定义环绕通知：

    // 将目标方法的返回值由小写转为大写
    public class MyMethodInterceptor implements MethodInterceptor {

        @Override
        public Object invoke(MethodInvocation invocation) throws Throwable {
            System.out.println("执行环绕通知，目标方法执行之前。");
            // 执行目标方法
            Object result = invocation.proceed();
            System.out.println("执行环绕通知，目标方法执行之后。");
            return result == null ? result : ((String) result).toUpperCase();
        }
    }

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.methodInterceptor.service.SomeServiceImpl" />
    
    <!-- 注册环绕通知 -->
    <bean id="myMethodInterceptor" class="tavish.bit.aop.methodInterceptor.advice.MyMethodInterceptor"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myMethodInterceptor"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/methodInterceptor/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        ISomeService service = (ISomeService) context.getBean("serviceProxy");

        String result = service.doFirst();
        
        System.out.println("result = " + result);
        System.out.println("============");
        
        service.doSecond();

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    执行环绕通知，目标方法执行之前。
    执行主业务逻辑doFisrt()
    执行环绕通知，目标方法执行之后。
    result = ABCDE
    ============
    执行环绕通知，目标方法执行之前。
    执行主业务逻辑doSecond()
    执行环绕通知，目标方法执行之后。

##### 3.2.2.4 异常通知----ThrowsAdvice

定义异常通知，需要实现ThrowsAdvice接口。该接口的主要作用是，在目标方法抛出异常后，根据异常的不同做出相应的处理。当该接口处理完异常后，会简单地将异常再次抛出给目标方法。
不过，这个接口较为特殊，从形式上看，该接口中没有必须要实现的方法。但，这个接口却确实有必须要实现的方法afterThrowing()。这个方法重载了四种形式。由于使用时，一般只使用其中一种，若要都定义到接口中，则势必要使程序员在使用时必须要实现这四个方法。这是很麻烦的。所以就将该接口定义为了标识接口。

    public void afterThrowing(Exception ex)
    public void afterThrowing(RemoteException)
    public void afterThrowing(Method method, Object[] args, Object target, Exception ex)
    public void afterThrowing(Method method, Object[] args, Object target, ServletException ex)

示例：

主业务接口，略。

目标类：doFirst()方法会抛出异常。

package tavish.bit.aop.throwsAdvice.service;

    // 目标类
    public class SomeServiceImpl implements ISomeService {

        @Override
        public void doFirst() {
            System.out.println("执行主业务逻辑doFisrt()" + 3 / 0);
        }

        @Override
        public void doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
        }
    }

异常通知：

    package tavish.bit.aop.throwsAdvice.advice;

    import org.springframework.aop.ThrowsAdvice;

    // 异常通知
    public class MyThrowsAdvice implements ThrowsAdvice {
        
        public void afterThrowing(Exception ex) {
            System.out.println("执行异常通知方法 ex = " + ex.getMessage());
        }
    }

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.throwsAdvice.service.SomeServiceImpl" />
    
    <!-- 注册异常通知 -->
    <bean id="myThrowsAdvice" class="tavish.bit.aop.throwsAdvice.advice.MyThrowsAdvice"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myThrowsAdvice"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/throwsAdvice/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        ISomeService service = (ISomeService) context.getBean("serviceProxy");

        service.doFirst();
        
        System.out.println("============");
        
        service.doSecond();

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    执行异常通知方法 ex = / by zero

示例2：

本例实现用户身份验证。当用户名不正确时，抛出用户名有误异常；当密码不正确时，抛出密码有误异常。

主业务接口和目标类：

    package tavish.bit.aop.throwsAdvice2.service;

    import tavish.bit.aop.throwsAdvice2.exception.UserException;

    public interface ISomeService {
        boolean checkUser(String username, String password) throws UserException;
    }

    package tavish.bit.aop.throwsAdvice2.service;

    import tavish.bit.aop.throwsAdvice2.exception.PasswordException;
    import tavish.bit.aop.throwsAdvice2.exception.UserException;
    import tavish.bit.aop.throwsAdvice2.exception.UsernameException;

    public class SomeServiceImpl implements ISomeService {

        @Override
        public boolean checkUser(String username, String password) throws UserException {
            
            if (!"Tavish".equals(username.trim())) {
                throw new UsernameException("用户名有误");
            }
            
            if(!"1111".equals(password.trim())) {
                throw new PasswordException("密码有误");
            }
            
            return true;
        }
    }

自定义异常：

    package tavish.bit.aop.throwsAdvice2.exception;

    public class UserException extends Exception {

        private static final long serialVersionUID = 2411887317589296825L;

        public UserException() {
            super();
        }

        public UserException(String message) {
            super(message);
        }

    }


    package tavish.bit.aop.throwsAdvice2.exception;

    public class UsernameException extends UserException {

        private static final long serialVersionUID = -4509857821392759158L;

        public UsernameException() {
            super();
        }

        public UsernameException(String message) {
            super(message);
        }
    }

    package tavish.bit.aop.throwsAdvice2.exception;

    public class PasswordException extends UserException {

        private static final long serialVersionUID = 2230247107104456859L;

        public PasswordException() {
            super();
        }

        public PasswordException(String message) {
            super(message);
        }
    }

异常通知：

    package tavish.bit.aop.throwsAdvice2.advice;

    import org.springframework.aop.ThrowsAdvice;

    import tavish.bit.aop.throwsAdvice2.exception.PasswordException;
    import tavish.bit.aop.throwsAdvice2.exception.UsernameException;

    public class MyThrowsAdvice implements ThrowsAdvice {
        
        public void afterThrowing(UsernameException ex) {
            System.out.println("用户名输错了！---- ex = " + ex.getMessage());
        }
        
        public void afterThrowing(PasswordException ex) {
            System.out.println("密码输错了！---- ex = " + ex.getMessage());
        }
    }

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.throwsAdvice2.service.SomeServiceImpl" />
    
    <!-- 注册异常通知 -->
    <bean id="myThrowsAdvice" class="tavish.bit.aop.throwsAdvice2.advice.MyThrowsAdvice"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myThrowsAdvice"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        
        String configLocation = "tavish/bit/aop/throwsAdvice2/applicationContext.xml";
        
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("serviceProxy");
        
        try {
            service.checkUser("Tavish", "1111");
        } catch (UserException e) {
            e.printStackTrace();
        }
        
        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

- 使用try/catch语句捕获异常：
    + 用户名和密码都正确时：无输出，Junit测试通过。
    + 用户名错误，密码正确：用户名输错了！---- ex = 用户名有误，有栈轨迹....Junit测试通过。
    + 用户名正确，密码错误：密码输错了！---- ex = 密码有误，有栈轨迹....Junit测试通过。
- 去掉try/catch语句，声明抛出异常：
    + 用户名和密码都正确时：无输出，Junit测试通过。
    + 用户名错误，密码正确：用户名输错了！---- ex = 用户名有误，无栈轨迹，Junit测试失败。
    + 用户名正确，密码错误：密码输错了！---- ex = 密码有误，无栈轨迹，Junit测试失败。

#### 3.2.3 通知的其他用法

*源代码再Spring-06-AOP-advice-otherUsage下。*

##### 3.2.3.1 给目标方法织入多个切面

两个通知：

    package tavish.bit.aop.multiAspect.advice;

    import java.lang.reflect.Method;
    import org.springframework.aop.MethodBeforeAdvice;

    // 前置通知
    public class MyMethodBeforeAdvice implements MethodBeforeAdvice {

        @Override
        public void before(Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行前置通知方法");
        }
    }

    package tavish.bit.aop.multiAspect.advice;

    import java.lang.reflect.Method;
    import org.springframework.aop.AfterReturningAdvice;

    // 后置通知：
    public class MyAfterReturningAdvice implements AfterReturningAdvice {

        @Override
        public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行后置通知方法");
        }
    }


配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.multiAspect.service.SomeServiceImpl" />
    
    <!-- 注册通知 -->
    <bean id="myAfterReturningAdvice" class="tavish.bit.aop.multiAspect.advice.MyAfterReturningAdvice"/>
    <bean id="myMethodBeforeAdvice" class="tavish.bit.aop.multiAspect.advice.MyMethodBeforeAdvice"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myMethodBeforeAdvice, myAfterReturningAdvice"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/multiAspect/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        ISomeService service =  (ISomeService) context.getBean("serviceProxy");

        service.doFirst();
        
        System.out.println("============");
        
        service.doSecond();

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    执行前置通知方法
    执行主业务逻辑doFisrt()
    执行后置通知方法
    ============
    执行前置通知方法
    执行主业务逻辑doSecond()
    执行后置通知方法

##### 3.2.3.2 无接口的CGLIB动态代理

若不存在接口，则ProxyFactoryBean会自动采用CGLIB方式生成动态代理。

示例：

目标类：

    package tavish.bit.aop.cglibWithoutInterface;

    // 目标类
    public class SomeService {

        public void doFirst() {
            System.out.println("执行主业务逻辑doFisrt()");
        }

        public void doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
        }
    }

后置通知：

    package tavish.bit.aop.cglibWithoutInterface;

    import java.lang.reflect.Method;

    import org.springframework.aop.AfterReturningAdvice;

    // 后置通知：
    public class MyAfterReturningAdvice implements AfterReturningAdvice {

        @Override
        public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行后置通知方法");
        }
    }

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.cglibWithoutInterface.SomeService" />
    
    <!-- 注册通知 -->
    <bean id="myAfterReturningAdvice" class="tavish.bit.aop.cglibWithoutInterface.MyAfterReturningAdvice"/>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myAfterReturningAdvice"/>
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/cglibWithoutInterface/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);

        SomeService service =  (SomeService) context.getBean("serviceProxy");

        service.doFirst();
        
        System.out.println("============");
        
        service.doSecond();

        ((ClassPathXmlApplicationContext) context).close();
    }

输出：

    执行主业务逻辑doFisrt()
    执行后置通知方法
    ============
    执行主业务逻辑doSecond()
    执行后置通知方法

##### 3.2.3.3 有接口的CGLIB动态代理

若存在接口，但又需要使用CGLIB生成代理对象，此时，只需要在配置文件中增加一个proxyTargetClass属性设置，用于指定强制使用CGLIB代理机制。

    <!-- 指定使用CGLIB代理 -->
    <property name="proxyTargetClass" value="true"/>

或者使用optimize属性也可以达到同样的效果：

    <!-- 指定使用CGLIB代理 -->
    <property name="optimize" value="true"/>

示例，略。

### 3.3 顾问

*源代码在Spring-07-AOP-advisor下。*

通知（Advice）是Spring提供的一种切面（Aspect）。但其功能过于简单：只能将切面织入到目标类的所有目标方法中。无法完成将切面织入到指定目标方法中，无法完成在不改变目标类的前提下增加目标方法的功能。

顾问（Advisor）是Spring提供的另一种切面。其可以完成更为复杂的切面织入功能。PointcutAdvisor是顾问的一种，可以指定具体的切入点。顾问将通知进行了包装，会根据不同的通知类型，在不同的时间点，将切面织入到不同的切入点。

PointcutAdvisor接口有两个较为常用的实现类：

- NameMatchMethodPointcutAdvisor：名称匹配方法切入点顾问。
- RegexpMethodPointcutAdvisor：正则表达式匹配方法切入点顾问。

#### 3.3.1 名称匹配方法切入点顾问

NameMatchMethodPointcutAdvisor，即名称匹配方法切入点顾问。容器可根据配置文件中指定的方法名来设置切入点。

*匹配的名称是简单方法名。*

示例：

主业务接口与目标类：

    package tavish.bit.aop.nameMatchMethodPointcutAdvisor;

    public interface ISomeService {
        void doFirst();
        void doSecond();
        void doThird();
    }

    package tavish.bit.aop.nameMatchMethodPointcutAdvisor;

    public class SomeServiceImpl implements ISomeService {

        @Override
        public void doFirst() {
            System.out.println("执行主业务逻辑doFirst()");
        }

        @Override
        public void doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
        }

        @Override
        public void doThird() {
            System.out.println("执行主业务逻辑doThird()");
        }
    }

定义通知：

    package tavish.bit.aop.nameMatchMethodPointcutAdvisor;

    import java.lang.reflect.Method;

    import org.springframework.aop.AfterReturningAdvice;

    public class MyAfterReturningAdvice implements AfterReturningAdvice {

        @Override
        public void afterReturning(Object returnValue, Method method, Object[] args, Object target) throws Throwable {
            System.out.println("执行后置通知方法");
        }
    }

在配置文件中定义顾问：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.nameMatchMethodPointcutAdvisor.SomeServiceImpl" />
    
    <!-- 注册通知 advice-->
    <bean id="myAdvice" class="tavish.bit.aop.nameMatchMethodPointcutAdvisor.MyAfterReturningAdvice"/>
    
    <!-- 注册顾问 advisor-->
    <bean id="myAdvisor" class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="advice" ref="myAdvice" />
        <!-- <property name="mappedName" value="doFirst" /> -->
        <!-- <property name="mappedNames" value="doFirst, doSecond" /> -->
        <!-- 匹配的是简单方法名 -->
        <property name="mappedNames" value="*ir*" />
    </bean>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myAdvisor"/>
    </bean>

测试：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/nameMatchMethodPointcutAdvisor/applicationContext.xml";
        
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("serviceProxy");
        
        service.doFirst();
        System.out.println("=================");
        service.doSecond();
        System.out.println("=================");
        service.doThird();
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    执行主业务逻辑doFirst()
    执行后置通知方法
    =================
    执行主业务逻辑doSecond()
    =================
    执行主业务逻辑doThird()
    执行后置通知方法

#### 3.3.2 正则表达式匹配方法切入点顾问

RegexpMethodPointcutAdvisor，即正则表达式方法顾问。容器可根据正则表达式来设置切入点。注意，与正则表达式进行匹配的对象是接口中的方法名，而非目标类（接口的实现类）的方法名。

*匹配的名称是全限定方法名。*

示例：

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.regexpMethodPointcutAdvisor.SomeServiceImpl" />
    
    <!-- 注册通知 advice-->
    <bean id="myAdvice" class="tavish.bit.aop.regexpMethodPointcutAdvisor.MyAfterReturningAdvice"/>
    
    <!-- 注册顾问 advisor-->
    <bean id="myAdvisor" class="org.springframework.aop.support.RegexpMethodPointcutAdvisor">
        <property name="advice" ref="myAdvice" />
        <!-- 匹配的是全限定方法名 -->
        <property name="pattern" value=".*ir.*" />
    </bean>
    
    <!-- 生成代理对象 -->
    <bean id="serviceProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定要生成代理的目标对象 -->
        <property name="target" ref="someService"/>
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myAdvisor"/>
    </bean>

当使用patterns时，value写法：regex1,regex2,...
当使用pattern时，value写法：regex1|regex2|...

二者均表示“或”的意思。

### 3.5 自动代理生成器

前面代码中所使用的代理对象，均是由ProxyFactoryBean代理工具类生成的。而该代理工具类存在着如下缺点：

1. 一个代理对象只能代理一个Bean，即如果有两个Bean同时都要织入同一个切面，这时，不仅要配置这两个Bean，即两个目标对象，同时还要配置两个代理对象。
2. 在客户类中获取Bean时，使用的是代理类的id，而非我们定义的目标对象Bean的id。我们真正想要执行的应该是目标对象。从形式上看，不符合正常的逻辑。

Spring提供了自动代理生成器，用于解决ProxyFactoryBean的问题。常用的自动代理生成器有两个：

- 默认advisor自动代理生成器
- Bean名称自动代理生成器

需要注意的是，自动代理生成器均继承自Bean后处理器BeanPostProcessor。容器中所有Bean在初始化时均会自动执行Bean后处理器中的方法，故其无需id属性。所以自动代理生成器的Bean也没有id属性，客户类直接使用目标对象bean的id。

#### 3.5.1 默认advisor自动代理生成器

DefaultAdvisorAutoProxyCreator代理的生成方式是，将所有的目标对象与Advisor自动结合，生成代理对象。无需给生成器做任何的注入配置。

该自动代理生成器的缺点：

- **只能与Advisor配合使用。**
- **对于多个目标对象，无法进行选择性增强。**

示例：

主业务接口，目标类略。

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.autoProxy.defaultAdvisorAutoProxyCreator.SomeServiceImpl" />
    <bean id="someService2" class="tavish.bit.aop.autoProxy.defaultAdvisorAutoProxyCreator.SomeServiceImpl" />
    
    <!-- 注册通知 advice-->
    <bean id="myAdvice" class="tavish.bit.aop.autoProxy.defaultAdvisorAutoProxyCreator.MyAfterReturningAdvice"/>
    
    <!-- 注册顾问 advisor-->
    <bean id="myAdvisor" class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="advice" ref="myAdvice" />
        <property name="mappedName" value="doFirst" />
    </bean>
    
    <!-- 注册默认Advisor自动代理生成器 -->
    <bean class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" />

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/autoProxy/defaultAdvisorAutoProxyCreator/applicationContext.xml";
        
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("someService");
        ISomeService service2 = (ISomeService) context.getBean("someService2");
        
        service.doFirst();
        System.out.println("=================");
        service.doSecond();
        System.out.println("=================");
        service.doThird();
        System.out.println("*******************");
        service2.doFirst();
        System.out.println("=================");
        service2.doSecond();
        System.out.println("=================");
        service2.doThird();
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    执行主业务逻辑doFirst()
    执行后置通知方法
    =================
    执行主业务逻辑doSecond()
    =================
    执行主业务逻辑doThird()
    *******************
    执行主业务逻辑doFirst()
    执行后置通知方法
    =================
    执行主业务逻辑doSecond()
    =================
    执行主业务逻辑doThird()

#### 3.5.2 Bean名称自动代理生成器

DefaultAdvisorAutoProxyCreator会为每一个目标对象织入所有匹配的Advisor，不具有选择性。而BeanNameAutoProxyCreator的代理生成方式是，根据bean的id，来为符合相应名称的类生成相应代理对象。

同时，Bean名称自动代理生成器可以搭配通知使用。

示例：

主业务接口及目标类，略。

配置文件：

    <!-- 注册目标对象 -->
    <bean id="someService" class="tavish.bit.aop.autoProxy.beanNameAutoProxyCreator.SomeServiceImpl" />
    <bean id="someService2" class="tavish.bit.aop.autoProxy.beanNameAutoProxyCreator.SomeServiceImpl" />
    
    <!-- 注册通知 advice-->
    <bean id="myAdvice" class="tavish.bit.aop.autoProxy.beanNameAutoProxyCreator.MyAfterReturningAdvice"/>
    
    <!-- 注册顾问 advisor-->
    <bean id="myAdvisor" class="org.springframework.aop.support.NameMatchMethodPointcutAdvisor">
        <property name="advice" ref="myAdvice" />
        <property name="mappedName" value="doFirst" />
    </bean>
    
    <!-- 注册Bean名称自动代理生成器 -->
    <bean class="org.springframework.aop.framework.autoproxy.BeanNameAutoProxyCreator" >
        <!-- 只增强someService，不增强someService2 -->
        <property name="beanNames" value="someService" />
        <!-- 指定切面 -->
        <property name="interceptorNames" value="myAdvisor" />
    </bean>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aop/autoProxy/beanNameAutoProxyCreator/applicationContext.xml";
        
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        
        ISomeService service = (ISomeService) context.getBean("someService");
        ISomeService service2 = (ISomeService) context.getBean("someService2");
        
        service.doFirst();
        System.out.println("=================");
        service.doSecond();
        System.out.println("=================");
        service.doThird();
        System.out.println("*******************");
        service2.doFirst();
        System.out.println("=================");
        service2.doSecond();
        System.out.println("=================");
        service2.doThird();
        
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    执行主业务逻辑doFirst()
    执行后置通知方法
    =================
    执行主业务逻辑doSecond()
    =================
    执行主业务逻辑doThird()
    *******************
    执行主业务逻辑doFirst()
    =================
    执行主业务逻辑doSecond()
    =================
    执行主业务逻辑doThird()

### 3.6 AspectJ对AOP的实现

对于AOP这种编程思想，很多框架都进行了实现。Spring就是其中之一，可以完成面向切面编程。然而，AspectJ也实现了AOP的功能，且其实现方式更为简捷，使用更为方便，而且还支持注解式开发。所以，Spring又将AspectJ的对于AOP的实现也引入到了自己的框架中。

在Spring中使用AOP开发时，一般使用AspectJ的实现方式。

#### 3.6.1 AspectJ的通知类型

五种类型：

- 前置通知
- 后置通知
- 环绕通知
- 异常通知
- 最终通知

其中最终通知是指，无论程序执行是否正常，该通知都会执行。类似于try/catch中的finally代码块。

#### 3.6.2 AspectJ的切入点表达式execution()

AspectJ除了提供了六种通知外，还定义了专门的表达式用于指定切入点。表达式的原型是：

execution([modifiers-pattern] ret-type-pattern [declaring-type-pattern] name-pattern(param-pattern) [throws-pattern])

各部分含义：

- modifiers-pattern：访问权限类型
- ret-type-pattern：返回值类型
- declaring-type-pattern：全限定性类名
- name-pattern：方法名
- param-pattern：参数名
- throws-pattern：抛出异常类型

**切入点表达式要匹配的对象就是目标方法的方法名**。所以，execution表达式中明显就是方法的签名。注意，表达式中加[]的部分表示可省略部分，各部分间用空格分开。

在表达式中可以使用以下符号：

- **\*** ：0至多个任意字符。
- **..** ：
    + 用在方法参数中：表示任意多个参数。
    + 用在包名后：表示当前包及其子包路径。
- **+**：
    + 用在类名后：表示当前类及其子类。
    + 用在接口后：表示当前接口及其实现类。

示例：

1.指定切入点为：任意公共方法。

    execution(public * *(..))

2.指定切入点为：任何一个以“set”开始的方法。

    execution(* set *(..))

3.指定切入点为：定义在service包里的任意类的任意方法。

    execution(* com.xyz.service.*.*(..))

4.指定切入点为：定义在service包或者子包里的任意类的任意方法。“..”出现在类名中时，后面必须跟“*”，表示包、子包下的所有类。

    execution(* com.xyz.service..*.*(..))

5.指定只有一级包下的IUserSerivce下的doSome()方法为切入点。

    execution(* *.IUserService.doSome())

6.指定所有包下的IUserSerivce下的doSome()方法为切入点。

    execution(* *..IUserService.doSome())

7.指定切入点为： IAccountService 接口中的任意方法。

    execution(* com.xyz.service.IAccountService.*(..))

8.指定切入点为： IAccountService 若为接口，则为接口中的任意方法及其所有实现类中的任意方法；若为类，则为该类及其子类中的任意方法。

    execution(* com.xyz.service.IAccountService+.*(..))

9.指定切入点为：所有的joke(String,int)方法，且joke()方法的第一个参数是String，第二个参数是int。如果方法中的参数类型是java.lang包下的类，可以直接使用类名，否则必须使用全限定类名，如joke( java.util.List, int)。

    execution(* joke(String,int)))

10.指定切入点为：所有的joke()方法，该方法第一个参数为String，第二个参数可以是任意类型，如joke(String s1,String s2)和joke(String s1,double d2)都是，但joke(String s1,double d2,String s3)不是。

    execution(* joke(String,*))

11.指定切入点为：所有的joke()方法，该方法第 一个参数为String，后面可以有任意个参数且参数类型不限，如joke(String s1)、joke(String s1,String s2)和joke(String s1,double d2,String s3)都是。

    execution(* joke(String,..))

12.指定切入点为：所有的joke()方法，方法拥有一个参数，且参数是Object类型。 joke(Object ob)是，但，joke(String s)与joke(User u)均不是。

    execution(* joke(Object))

13.指定切入点为：所有的joke()方法，方法拥有一个参数，且参数是Object类型或该类的子类。不仅joke(Object ob)是，joke(String s)和joke(User u)也是。

    execution(* joke(Object+))

#### 3.6.3 AspectJ的开发环境

步骤一：导入两个Jar包

- aspectjweaver-1.8.10
- spring-aspects-4.3.9.RELEASE

步骤二：引入AOP约束

    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
            http://www.springframework.org/schema/beans 
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop 
            http://www.springframework.org/schema/aop/spring-aop.xsd"> 

    </beans>

在前面Spring实现AOP时，并未引入AOP的约束，而在AspectJ实现AOP时，才提出要引入AOP的约束。说明，配置文件中使用的AOP约束中的标签，均是AspectJ框架使用的，而非Spring框架本身在实现AOP时使用的。

#### 3.6.4 AspectJ基于注解的AOP实现

*源代码在Spring-08-AOP-AspectJ-annotation下。*

##### 3.6.4.1 实现步骤

1. **定义业务接口与实现类。**
2. **定义切面POJO类**：该类为一个POJO类，将作为切面出现。其中定义了若干普通方法，将作为不同的通知方法。
3. **在切面类上添加@Aspect注解**：在定义的POJO类上添加@Aspect注解，指定当前POJO类将作为切面。
4. **在POJO类的普通方法上添加通知注解**：切面类是用于定义增强代码的，即用于定义增强目标类中目标方法的增强方法。这些增强方法使用不同的“通知”注解，会在不同的时间点完成织入。当然，对于增强代码，还要通过execution表达式指定具体应用的目标类与目标方法，即切入点。
5. **注册目标对象与POJO切面类。**
6. **注册AspectJ的自动代理**：在定义好切面Aspect后，需要通知Spring容器，让容器生成“目标类 + 切面”的代理对象。这个代理是由容器自动生成的。只需要在Spring配置文件中注册一个基于aspectj的自动代理生成器，其就会自动扫描到@Aspect注解，并按通知类型与切入点，将其织入，并生成代理。&lt;aop:aspectj-autoproxy/&gt;的底层是由AnnotationAwareAspectJAutoProxyCreator实现的。从其类名就可看出，是基于AspectJ的注解适配自动代理生成器。其工作原理是，&lt;aop:aspectj-autoproxy/&gt;通过扫描找到@Aspect定义的切面类，再由切面类根据切入点找到目标类的目标方法，再由通知类型找到切入的时间点。
7. **测试类中使用目标对象的id。**

##### 3.6.4.2 @Before前置通知

在目标方法执行之前执行。被注解为前置通知的方法，可以包含一个JoinPoint类型参数。该类型的对象本身就是切入点表达式。通过该参数，可获取切入点表达式、方法签名、目标对象等。

示例：

主业务接口及目标类：

    package tavish.bit.aspectj;

    public interface ISomeService {
        void doFirst();
        String doSecond();
        void doThird();
    }

    package tavish.bit.aspectj;

    public class SomeServiceImpl implements ISomeService {

        @Override
        public void doFirst() {
            System.out.println("执行主业务逻辑doFirst()");
        }

        @Override
        public String doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
            return "abcde";
        }

        @Override
        public void doThird() {
            System.out.println("执行主业务逻辑doThird()");
        }
    }

POJO类：

    package tavish.bit.aspectj;

    import org.aspectj.lang.JoinPoint;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;

    @Aspect // 表示当前类为切面
    public class MyAspect {
        
        @Before("execution(* *..ISomeService.doFirst(..))")
        public void before() {
            System.out.println("前置通知方法");
        }
        
        // 附带JoinPoint参数的前置通知方法
        @Before("execution(* *..ISomeService.doFirst(..))")
        public void before(JoinPoint jp) {
            System.out.println("前置通知方法() jp = " + jp);
        }
    }

配置文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <beans
        xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="
                http://www.springframework.org/schema/beans 
                http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/aop 
                http://www.springframework.org/schema/aop/spring-aop.xsd">
                
        <bean id="someService" class="tavish.bit.aspectj.SomeServiceImpl" />
        
        <!-- 注册切面 -->
        <bean id="myAspect" class="tavish.bit.aspectj.MyAspect" />
        
        <!-- 使用AspectJ的自动代理 -->
        <aop:aspectj-autoproxy />
    </beans>

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aspectj/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        System.out.println("-----------");
        String result = service.doSecond();
        System.out.println("result = " + result);
        System.out.println("-----------");
        service.doThird();
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    执行主业务逻辑doSecond()
    result = abcde
    -----------
    执行主业务逻辑doThird()

*可以看到jp就是我们的切入点表达式。*

##### 3.6.4.3 @AfterReturning后置通知

在目标方法执行之后执行。由于是目标方法之后执行，所以可以获取到目标方法的返回值。该注解的returning属性就是用于指定接收方法返回值的变量名的。所以，被注解为后置通知的方法，除了可以包含JoinPoint参数外，还可以包含用于接收返回值的变量。该变量最好为Object类型，因为目标方法的返回值可能是任何类型。

示例：

POJO类中定义：

    // 后置通知
    @AfterReturning("execution(* *..ISomeService.doSecond(..))")
    public void afterReturning() {
        System.out.println("后置通知方法");
    }
    
    // 后置通知，获取返回值
    @AfterReturning(value="execution(* *..ISomeService.doSecond(..))", returning="result")
    public void afterReturning(Object result) {
        System.out.println("后置通知方法 result = " + result);
    }

测试方法及其他同上一节，略。

输出：

    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    前置通知方法
    执行主业务逻辑doFirst()
    -----------
    执行主业务逻辑doSecond()
    后置通知方法
    后置通知方法 result = abcde
    result = abcde
    -----------
    执行主业务逻辑doThird()

##### 3.6.4.4 @Around环绕通知

在目标方法执行之前之后执行。被注解为环绕增强的方法要有返回值，Object类型。并且方法可以包含一个ProceedingJoinPoint类型的参数。接口ProceedingJoinPoint其有一个proceed()方法，用于执行目标方法。若目标方法有返回值，则该方法的返回值就是目标方法的返回值。最后，环绕增强方法将其返回值返回。该增强方法实际是拦截了目标方法的执行。

示例：

    // 环绕通知
    @Around("execution(* *..ISomeService.doSecond(..))")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕通知方法，目标方法执行之前");
        // 执行目标方法
        Object result = pjp.proceed();
        System.out.println("环绕通知方法，目标方法执行之后");
        return ((String) result).toUpperCase();
    }

输出：

    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    前置通知方法
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法 result = ABCDE
    后置通知方法
    result = ABCDE
    -----------
    执行主业务逻辑doThird()

##### 3.6.4.5 @AfterThrowing异常通知

在目标方法抛出异常后执行。该注解的throwing属性用于指定所发生的异常类对象。当然，被注解为异常通知的方法可以包含一个参数Throwable，参数名称为throwing指定的名称，表示发生的异常对象。

示例：

修改目标类方法，使其产生异常：

    @Override
    public void doThird() {
        System.out.println("执行主业务逻辑doThird()");
        System.out.println(3 / 0);
    }

定义异常通知：

    // 异常通知
    @AfterThrowing("execution(* *..ISomeService.doThird(..))")
    public void afterThrowing() {
        System.out.println("异常通知方法");
    }

    @AfterThrowing(value="execution(* *..ISomeService.doThird(..))",throwing="ex")
    public void afterThrowing(Exception ex) {
        System.out.println("异常通知方法 ex = " + ex.getMessage());
    }

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aspectj/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        System.out.println("-----------");
        String result = service.doSecond();
        System.out.println("result = " + result);
        System.out.println("-----------");
        try {
            service.doThird();
        } catch (Exception e) {
        }
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    前置通知方法
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法 result = ABCDE
    后置通知方法
    result = ABCDE
    -----------
    执行主业务逻辑doThird()
    异常通知方法
    异常通知方法 ex = / by zero

##### 3.6.4.6 @After最终通知

无论目标方法是否抛出异常，该增强均会被执行。

示例：

    // 最终通知
    @After("execution(* *..ISomeService.doThird(..))")
    public void after() {
        System.out.println("最终通知方法");
    }

输出：

    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    前置通知方法
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法 result = ABCDE
    后置通知方法
    result = ABCDE
    -----------
    执行主业务逻辑doThird()
    最终通知方法
    异常通知方法 ex = / by zero
    异常通知方法

##### 3.6.4.7 @Pointcut定义切入点

当较多的通知增强方法使用相同的execution切入点表达式时，编写、维护均较为麻烦。AspectJ提供了@Pointcut注解，用于定义execution切入点表达式。

其用法是，将@Pointcut注解在一个方法之上，以后所有的executeion的value属性值均可使用该方法名作为切入点。代表的就是@Pointcut定义的切入点。这个使用@Pointcute注解的方法一般使用private的标识方法，即没有实际作用的方法。

示例：

定义切入点：

    // 定义切入点
    @Pointcut("execution(* *..ISomeService.doThird(..))")
    private void doThirdPointcut() {
        
    }

使用切入点：

    // 异常通知
    @AfterThrowing("doThirdPointcut()")
    public void afterThrowing() {
        System.out.println("异常通知方法");
    }

    @AfterThrowing(value="doThirdPointcut()",throwing="ex")
    public void afterThrowing(Exception ex) {
        System.out.println("异常通知方法 ex = " + ex.getMessage());
    }
    
    // 最终通知
    @After("doThirdPointcut()")
    public void after() {
        System.out.println("最终通知方法");
    }

测试输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法
    后置通知方法 result = ABCDE
    result = ABCDE
    -----------
    执行主业务逻辑doThird()
    最终通知方法
    异常通知方法 ex = / by zero
    异常通知方法

#### 3.6.5  AspectJ基于XML的AOP实现

*源代码在Spring-08-AOP-AspectJ-XML下。*

AspectJ除了提供了基于注解的AOP实现外，还提供了以XML方式的实现。
切面就是一个POJO类，而用于增强的方法就是普通的方法。通过配置文件，将切面中的功能增强织入到目标类的目标方法中。

##### 3.6.5.1 实现步骤

1. 定义业务接口与实现类。
2. 定义切面POJO类：该类为一个POJO类，将作为切面出现。其中定义了若干普通方法，将作为不同的通知方法。
3. 注册目标对象与POJO切面类。
4. 在容器中定义AOP配置：
    - 在&lt;aop:config/&gt;中进行aop的配置。
    - 通过子标签&lt;aop:aspect/&gt;定义具体的织入规则：
        + 根据不同的通知类型，确定不同的织入时间。
        + 将method指定的增强方法，按照指定织入时间，织入到切入点指定的目标方法中。
    - &lt;aop:aspect/&gt;的ref属性用于指定使用哪个切面。
    - &lt;aop:aspect/&gt;的子标签是各种不同的通知类型。不同的通知所包含的属性是不同的，但也有共同的属性：
        + method：指定该通知使用的切面中的增强方法。
        + pointcut-ref：指定该通知要应用的切入点。
5. 测试类中使用目标对象的id

AspectJ的6种通知的XML标签如下：

- &lt;aop:before/&gt;：前置通知
- &lt;aop:after-returning/&gt;： 后置通知
- &lt;aop:around/&gt;：环绕通知
- &lt;aop:after-throwing/&gt;：异常通知
- &lt;aop:after/&gt;：最终通知
- &lt;aop:declare-parents/&gt;：引入通知

##### 3.6.5.2 &lt;aop:before/&gt;前置通知

定义业务接口与实现类：

    package tavish.bit.aspectj;

    public interface ISomeService {
        void doFirst();
        String doSecond();
        void doThird();
    }

    package tavish.bit.aspectj;

    public class SomeServiceImpl implements ISomeService {

        @Override
        public void doFirst() {
            System.out.println("执行主业务逻辑doFirst()");
        }

        @Override
        public String doSecond() {
            System.out.println("执行主业务逻辑doSecond()");
            return "abcde";
        }

        @Override
        public void doThird() {
            System.out.println("执行主业务逻辑doThird()");
            System.out.println(3 / 0);
        }
    }

定义POJO类：

    // 前置通知
    public void before() {
        System.out.println("前置通知方法");
    }
    
    public void before(JoinPoint jp) {
        System.out.println("前置通知方法() jp = " + jp);
    }

配置文件：

    <bean id="someService" class="tavish.bit.aspectj.SomeServiceImpl" />
    
    <!-- 注册切面 -->
    <bean id="myAspect" class="tavish.bit.aspectj.MyAspect" />
    
    <!-- aop配置 -->
    <aop:config>
        <!-- 定义切入点 -->
        <aop:pointcut expression="execution(* *..ISomeService.doFirst(..))" id="doFirstPointcut" />
        <aop:pointcut expression="execution(* *..ISomeService.doSecond(..))" id="doSecondPointcut" />
        <aop:pointcut expression="execution(* *..ISomeService.doThird(..))" id="doThirdPointcut" />
        <!-- 定义具体的织入规则 -->
        <aop:aspect ref="myAspect">
            <!-- 前置通知 -->
            <aop:before method="before" pointcut-ref="doFirstPointcut" />
            <aop:before method="before(org.aspectj.lang.JoinPoint)" pointcut-ref="doFirstPointcut" />
        </aop:aspect>
    </aop:config>

**选择重载的方法：在method属性赋值时，不仅要放方法名，还要放入方法的参数类型全类名。**

测试方法：

    @Test
    public void test01() {
        String configLocation = "tavish/bit/aspectj/applicationContext.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(configLocation);
        ISomeService service = (ISomeService) context.getBean("someService");
        service.doFirst();
        System.out.println("-----------");
        String result = service.doSecond();
        System.out.println("result = " + result);
        System.out.println("-----------");
        try {
            service.doThird();
        } catch (Exception e) {
        }
        ((ClassPathXmlApplicationContext)context).close();
    }

输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    执行主业务逻辑doSecond()
    result = abcde
    -----------
    执行主业务逻辑doThird()

##### 3.6.5.2 &lt;aop:after-returning/&gt;后置通知

定义POJO类：

    // 后置通知
    public void afterReturning() {
        System.out.println("后置通知方法");
    }
    
    // 后置通知，获取返回值
    public void afterReturning(Object result) {
        System.out.println("后置通知方法 result = " + result);
    }

配置文件：

    <!-- 后置通知 -->
    <aop:after-returning method="afterReturning" pointcut-ref="doSecondPointcut"/>
    <aop:after-returning method="afterReturning(java.lang.Object)" pointcut-ref="doSecondPointcut" returning="result" />

其中returning="result"的result要与参数方法名（Object result）相同。

输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    执行主业务逻辑doSecond()
    后置通知方法
    后置通知方法 result = abcde
    result = abcde
    -----------
    执行主业务逻辑doThird()

##### 3.6.5.3 &lt;aop:around/&gt;环绕通知

定义POJO类：

    // 环绕通知
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("环绕通知方法，目标方法执行之前");
        Object result = pjp.proceed();
        System.out.println("环绕通知方法，目标方法执行之后");
        return ((String) result).toUpperCase();
    }

配置文件：

    <!-- 环绕通知 -->
    <aop:around method="around" pointcut-ref="doSecondPointcut" />

输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法 result = ABCDE
    后置通知方法
    result = ABCDE
    -----------
    执行主业务逻辑doThird()

##### 3.6.5.4 &lt;aop:after-throwing/&gt;异常通知

定义POJO类：

    // 异常通知
    public void afterThrowing() {
        System.out.println("异常通知方法");
    }

    public void afterThrowing(Exception ex) {
        System.out.println("异常通知方法 ex = " + ex.getMessage());
    }

配置文件：

    <aop:after-throwing method="afterThrowing" pointcut-ref="doThirdPointcut" />
    <aop:after-throwing method="afterThrowing(java.lang.Exception)" pointcut-ref="doThirdPointcut" throwing="ex"/>

其XML的配置中，有一个属性throwing，指定用于接收目标方法所抛出异常的变量名。其可作为增强方法的参数出现，该参数为Throwable类型。

输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法 result = ABCDE
    后置通知方法
    result = ABCDE
    -----------
    执行主业务逻辑doThird()
    异常通知方法 ex = / by zero
    异常通知方法

##### 3.6.5.5 &lt;aop:after/&gt;最终通知

定义POJO类：

    // 最终通知
    public void after() {
        System.out.println("最终通知方法");
    }

配置文件：

    <!-- 最终通知 -->
    <aop:after method="after" pointcut-ref="doThirdPointcut"/>

输出：

    前置通知方法
    前置通知方法() jp = execution(void tavish.bit.aspectj.ISomeService.doFirst())
    执行主业务逻辑doFirst()
    -----------
    环绕通知方法，目标方法执行之前
    执行主业务逻辑doSecond()
    环绕通知方法，目标方法执行之后
    后置通知方法 result = ABCDE
    后置通知方法
    result = ABCDE
    -----------
    执行主业务逻辑doThird()
    最终通知方法
    异常通知方法 ex = / by zero
    异常通知方法

## 第四章 Spring与DAO

本章内容主要包含两部分：

- Spring所使用的操作数据库的技术之一，JDBC模板的使用；
- 另一部分则为Spring对于事务的管理。

Spring与Dao部分，是Spring的两大核心技术IoC与AOP的典型应用体现：

- 对于JDBC模板的使用，是IoC的应用，是将JDBC模板对象注入给了Dao层的实现类。
- 对于Spring的事务管理，又是AOP的应用，将事务作为切面织入到了Service层的业务方法中。

### 4.1 Spring与JDBC模板

*源代码在Spring-09-Dao-jdbcTemplate下。*

为了避免直接使用JDBC而带来的复杂且冗长的代码，Spring提供了一个强有力的模板类---JdbcTemplate来简化JDBC操作。并且，数据源DataSource对象与模板JdbcTemplate对象均可通过Bean的形式定义在配置文件中，充分发挥了依赖注入的威力。

#### 4.1.1  详情配置

（1）Jar包：

- Spring框架Jar包：
    + spring-tx-4.3.9.RELEASE.jar
    + spring-jdbc-4.3.9.RELEASE.jar
    + spring-expression-4.3.9.RELEASE.jar
    + spring-core-4.3.9.RELEASE.jar
    + spring-context-4.3.9.RELEASE.jar
    + spring-beans-4.3.9.RELEASE.jar
- 数据源Jar包:
    + c3p0数据源：
        * c3p0-0.9.2.1.jar
        * mchange-commons-java-0.2.3.4.jar
    + dbcp数据源： 
        * commons-dbcp2-2.1.1.jar
        * commons-pool2-2.4.3.jar
- JUnit & log4j2：
    + JUnit：
        * junit-4.12.jar
        * hamcrest-core-1.3.jar
    + log4j2：
        * log4j-api-2.9.1.jar
        * log4j-core-2.9.1.jar
        * commons-logging-1.2.jar

（2）POJO类

1）bean类：Student.java

    package tavish.bit.beans;

    public class Student {
        private Integer id;
        private String name;
        private int age;

        public Student(String name, int age) {
            super();
            this.name = name;
            this.age = age;
        }

        public Student() {
            super();
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

        @Override
        public String toString() {
            return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
        }
    }

2）Service层

业务接口：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentService {
        // 添加学生
        void addStudent(Student student);
        // 移除学生
        void removeStudent(Student student);
        // 修改学生
        void modifyStudent(Student student);
        // 查找出表中所有学生的名字
        List<String> findAllStudentsNames();
        // 根据id查找学生的名字
        String findStudentNameById(int id);
        // 查找出表中所有学生
        List<Student> findAllStudents();
        // 根据id查找学生
        Student findStudentById(int id);
    }

实现类：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;
    import tavish.bit.dao.IStudentDao;

    public class StudentServiceImpl implements IStudentService {

        private IStudentDao dao;

        public void setDao(IStudentDao dao) {
            this.dao = dao;
        }

        @Override
        public void addStudent(Student student) {
            dao.insertStudent(student);
        }

        @Override
        public void removeStudent(Student student) {
            dao.deleteStudent(student);
        }

        @Override
        public void modifyStudent(Student student) {
            dao.updateStudent(student);
        }

        @Override
        public List<String> findAllStudentsNames() {
            return dao.selectAllStudentsNames();
        }

        @Override
        public String findStudentNameById(int id) {
            return dao.selectStudentNameById(id);
        }

        @Override
        public List<Student> findAllStudents() {
            return dao.selectAllStudents();
        }

        @Override
        public Student findStudentById(int id) {
            return dao.selectStudentById(id);
        }
    }

3）Dao层

业务接口：

    package tavish.bit.dao;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        void insertStudent(Student student);

        void deleteStudent(Student student);

        void updateStudent(Student student);

        List<String> selectAllStudentsNames();

        String selectStudentNameById(int id);

        List<Student> selectAllStudents();

        Student selectStudentById(int id);
    }

实现类：

    package tavish.bit.dao;

    import java.util.List;

    import org.springframework.jdbc.core.support.JdbcDaoSupport;

    import tavish.bit.beans.Student;

    public class StudentDaoImpl extends JdbcDaoSupport implements IStudentDao {

        @Override
        public void insertStudent(Student student) {
            String sql = "insert into student(name,age) values(?,?)";
            this.getJdbcTemplate().update(sql, student.getName(), student.getAge());
        }

        @Override
        public void deleteStudent(Student student) {
            String sql = "delete from student where id=?";
            this.getJdbcTemplate().update(sql, student.getId());
        }

        @Override
        public void updateStudent(Student student) {
            String sql = "update student set name=?, age=? where id=?";
            this.getJdbcTemplate().update(sql, student.getName(), student.getAge(), student.getId());
        }

        @Override
        public List<String> selectAllStudentsNames() {
            return null;
        }

        @Override
        public String selectStudentNameById(int id) {
            return null;
        }

        @Override
        public List<Student> selectAllStudents() {
            return null;
        }

        @Override
        public Student selectStudentById(int id) {
            return null;
        }
    }

（3）配置文件

1）注册Service

    <!-- 注册Service -->
    <bean id="studentService" class="tavish.bit.service.StudentServiceImpl" >
        <!-- 配置设值注入 -->
        <property name="dao" ref="studentDao" />
    </bean>

2）注册Dao

    <!-- 注册Dao -->
    <bean id="studentDao" class="tavish.bit.dao.StudentDaoImpl" >
        <!-- 配置JDBC模板 -->
        <property name="jdbcTemplate" ref="jdbcTemplate" />
    </bean>

3）注册jdbc模板

    <!-- 注册JDBC模板对象 -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <!-- 配置数据源 -->
        <property name="dataSource" ref="springDataSource" />
    </bean>

4）注册数据源，任意选择其一：

A. Spring默认数据源：

    <!-- 注册数据源：DriverManagerDataSource（Spring内置数据源） -->
    <bean id="springDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- 数据库连接四要素 -->
        <property name="driverClassName" value="${jdbc.driver}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
    </bean>

B. DBCP2数据源：

    <!-- 注册数据源：DBCP2数据源 -->
    <bean id="dbcpDataSource" class="org.apache.commons.dbcp2.BasicDataSource">
        <!-- 数据库连接四要素 -->
        <property name="driverClassName" value="${jdbc.driver}" />
        <property name="url" value="${jdbc.url}" />
        <property name="username" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
    </bean>

C. c3p0数据源：

    <!-- 注册数据源：c3p0数据源 -->
    <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 数据库连接四要素 -->
        <property name="driverClass" value="${jdbc.driver}" />
        <property name="jdbcUrl" value="${jdbc.url}" />
        <property name="user" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
    </bean>

5）注册JDBC属性文件，任意选择其一：

A. 以Bean的方式进行注册（不常用）：

    <bean class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="location" value="classpath:jdbc.properties" />
    </bean>

B. 修改约束，加入context约束，使用如下标签注册：

    <context:property-placeholder location="classpath:jdbc.properties" />

（4）JDBC属性文件（可选）：

classpath路径下：jdbc.properties

    jdbc.driver=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/test
    jdbc.user=root
    jdbc.password=mysql

#### 4.1.2 对DB的增删改操作

JdbcTemplate类中提供了对DB进行修改、查询的方法。Dao实现类使用继承自JdbcDaoSupport的getTemplate()方法，可以获取到JDBC模板对象。

（1）测试类，使用外置的JUnit4，MyTest：

    package tavish.bit.test;

    import java.util.List;

    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    public class MyTest {
        
        private ApplicationContext context;
        private IStudentService service;

        @Before
        public void before() {
            context = new ClassPathXmlApplicationContext("applicationContext.xml");
            service = (IStudentService) context.getBean("studentService");
        }
        
        @After
        public void After() {
            ((ClassPathXmlApplicationContext)context).close();
        }
        
        // 增
        @Test
        public void testAdd() {
            Student student = new Student("张三", 20);
            service.addStudent(student);
        }
        
        // 删
        @Test
        public void testRemove() {
            Student student = new Student();
            student.setId(3);
            service.removeStudent(student);
        }
        
        // 改
        @Test
        public void testModify() {
            Student student = new Student("李四", 24);
            student.setId(1);
            service.modifyStudent(student);
        }
    }


（2）使用Spring的JUnit4：

导入Jar包：

- spring-aop-4.3.9.RELEASE.jar
- spring-test-4.3.9.RELEASE.jar

测试类，MyTestSpringJunit：

    package tavish.bit.test;

    import org.junit.Test;
    import org.junit.runner.RunWith;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.test.context.ContextConfiguration;
    import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(locations="classpath:applicationContext.xml")
    public class MyTestSpringJunit {
        
        @Autowired
        private IStudentService service;
        
        @Test
        public void testAdd() {
            Student student = new Student("张三", 20);
            service.addStudent(student);
        }
        
        @Test
        public void testRemove() {
            Student student = new Student();
            student.setId(4);
            service.removeStudent(student);
        }
        
        @Test
        public void testModify() {
            Student student = new Student("李小四", 24);
            student.setId(1);
            service.modifyStudent(student);
        }
    }


#### 4.1.3 将数据源直接注入给Dao

根据JdbcDaoSupport类源码中的setDataSource方法：

    /**
     * Set the JDBC DataSource to be used by this DAO.
     */
    public final void setDataSource(DataSource dataSource) {
        if (this.jdbcTemplate == null || dataSource != this.jdbcTemplate.getDataSource()) {
            this.jdbcTemplate = createJdbcTemplate(dataSource);
            initTemplateConfig();
        }
    }

可以将配置文件中的以下部分：

    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>
    <bean id="studentDao" class="tavish.bit.dao.StudentDaoImpl" >
        <property name="jdbcTemplate" ref="jdbcTemplate" />
    </bean>

合并为：

    <bean id="studentDao" class="tavish.bit.dao.StudentDaoImpl" >
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>

即我们无需手工注册jdbcTemplate，我们可以直接向Dao注入数据源，在注入时，如果没有jdbcTemplate，则会自动创建。

#### 4.1.4 查询方法的实现

查询方法：

    @Override
    public List<String> selectAllStudentsNames() {
        String sql = "select name from student";
        return this.getJdbcTemplate().queryForList(sql, String.class);
    }

    @Override
    public String selectStudentNameById(int id) {
        String sql = "select name from student where id=?";
        return this.getJdbcTemplate().queryForObject(sql, String.class, id);
    }

    @Override
    public List<Student> selectAllStudents() {
        String sql = "select * from student";
        return this.getJdbcTemplate().query(sql, new StudentRowMapper());
    }

    @Override
    public Student selectStudentById(int id) {
        String sql = "select * from student where id=?";
        return this.getJdbcTemplate().queryForObject(sql, new StudentRowMapper(), id);
    }

由于queryForList和queryForObject方法不能自动包装数据为对象，所以需要自己实现。

RowMapper实现类：

    package tavish.bit.dao;

    import java.sql.ResultSet;
    import java.sql.SQLException;

    import org.springframework.jdbc.core.RowMapper;

    import tavish.bit.beans.Student;

    // 行映射器
    public class StudentRowMapper implements RowMapper<Student> {

        // rs：不是select查询的所有结果集，而是这个结果集中当前遍历的这一行记录
        @Override
        public Student mapRow(ResultSet rs, int rowNum) throws SQLException {
            Student student = new Student();
            student.setId(rs.getInt("id"));
            student.setName(rs.getString("name"));
            student.setAge(rs.getInt("age"));
            return student;
        }
    }

完整的测试类，MyTest：

    package tavish.bit.test;

    import java.util.List;

    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    public class MyTest {
        
        private ApplicationContext context;
        private IStudentService service;

        @Before
        public void before() {
            context = new ClassPathXmlApplicationContext("applicationContext.xml");
            service = (IStudentService) context.getBean("studentService");
        }
        
        @After
        public void After() {
            ((ClassPathXmlApplicationContext)context).close();
        }
        
        // 增
        @Test
        public void testAdd() {
            Student student = new Student("张三", 20);
            service.addStudent(student);
        }
        
        // 删
        @Test
        public void testRemove() {
            Student student = new Student();
            student.setId(3);
            service.removeStudent(student);
        }
        
        // 改
        @Test
        public void testModify() {
            Student student = new Student("李四", 24);
            student.setId(1);
            service.modifyStudent(student);
        }
        
        // 查询所有名字
        @Test
        public void testFindAllStudentsNames() {
            List<String> names = service.findAllStudentsNames();
            System.out.println(names);
        }
        
        // 根据Id查询名字
        @Test
        public void testFindStudentNameById() {
            String name = service.findStudentNameById(1);
            System.out.println(name);
        }
        
        // 查询所有学生
        @Test
        public void testFindAllStudents() {
            List<Student> students = service.findAllStudents();
            System.out.println(students);
        }
        
        // 根据Id查询学生
        @Test
        public void testFindStudentById() {
            Student student = service.findStudentById(1);
            System.out.println(student);
        }
    }

#### 4.1.5 JDBC模板对象是多例的

JdbcTemplate对象是多例的，即系统会为每一个使用模板对象的线程（方法）创建一个JdbcTemplate实例，并且在该线程（方法）结束时，自动释放JdbcTemplate实例。

**所以在每次使用JdbcTemplate对象时，都需要通过getJdbcTemplate()方法获取。**

测试：

StudentDaoImpl2：将JdbcTemplate提出作为域，并在构造器中对其进行初始化。之后每次使用都使用该引用。即不在每次使用JdbcTemplate对象时都通过getJdbcTemplate()方法获取。

    package tavish.bit.dao;

    import java.util.List;

    import org.springframework.jdbc.core.JdbcTemplate;
    import org.springframework.jdbc.core.support.JdbcDaoSupport;

    import tavish.bit.beans.Student;

    public class StudentDaoImpl2 extends JdbcDaoSupport implements IStudentDao {
        
        private JdbcTemplate jt;
        
        public StudentDaoImpl2() {
            jt = this.getJdbcTemplate();
        }

        @Override
        public void insertStudent(Student student) {
            String sql = "insert into student(name,age) values(?,?)";
            jt.update(sql, student.getName(), student.getAge());
        }

        @Override
        public void deleteStudent(Student student) {
            String sql = "delete from student where id=?";
            jt.update(sql, student.getId());
        }

        @Override
        public void updateStudent(Student student) {
            String sql = "update student set name=?, age=? where id=?";
            jt.update(sql, student.getName(), student.getAge(), student.getId());
        }

        @Override
        public List<String> selectAllStudentsNames() {
            String sql = "select name from student";
            return jt.queryForList(sql, String.class);
        }

        @Override
        public String selectStudentNameById(int id) {
            String sql = "select name from student where id=?";
            return jt.queryForObject(sql, String.class, id);
        }

        // 由于queryForList不能自动包装数据为对象，所以需要自己实现
        @Override
        public List<Student> selectAllStudents() {
            String sql = "select * from student";
            return jt.query(sql, new StudentRowMapper());
        }

        @Override
        public Student selectStudentById(int id) {
            String sql = "select * from student where id=?";
            return jt.queryForObject(sql, new StudentRowMapper(), id);
        }
        
    }

配置文件，applicationContext2.xml，注册class为StudentDaoImpl2：

    <bean id="studentDao" class="tavish.bit.dao.StudentDaoImpl2" >
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>

测试类，MyTest2：

    package tavish.bit.test;

    import java.util.List;

    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    public class MyTest2 {
        
        private ApplicationContext context;
        private IStudentService service;

        @Before
        public void before() {
            context = new ClassPathXmlApplicationContext("applicationContext2.xml");
            service = (IStudentService) context.getBean("studentService");
        }
        
        @After
        public void After() {
            ((ClassPathXmlApplicationContext)context).close();
        }
        
        // 增
        @Test
        public void testAdd() {
            Student student = new Student("张三", 20);
            service.addStudent(student);
        }
        
        // 删
        @Test
        public void testRemove() {
            Student student = new Student();
            student.setId(3);
            service.removeStudent(student);
        }
        
        // 改
        @Test
        public void testModify() {
            Student student = new Student("李四", 24);
            student.setId(1);
            service.modifyStudent(student);
        }
        
        // 查询所有名字
        @Test
        public void testFindAllStudentsNames() {
            List<String> names = service.findAllStudentsNames();
            System.out.println(names);
        }
        
        // 根据Id查询名字
        @Test
        public void testFindStudentNameById() {
            String name = service.findStudentNameById(1);
            System.out.println(name);
        }
        
        // 查询所有学生
        @Test
        public void testFindAllStudents() {
            List<Student> students = service.findAllStudents();
            System.out.println(students);
        }
        
        // 根据Id查询学生
        @Test
        public void testFindStudentById() {
            Student student = service.findStudentById(1);
            System.out.println(student);
        }
    }

测试执行添加方法，报错：**空指针异常。**

### 4.2 Spirng的事务管理

事务原本是数据库中的概念，在Dao层。但一般情况下，需要将事务提升到业务层，即Service层。这样做是为了能够使用事务的特性来管理具体的业务。
在Spring中通常可以通过以下三种方式来实现对事务的管理：

- 使用Spring的事务代理工厂管理事务。
- 使用Spring的事务注解管理事务。
- 使用AspectJ的AOP配置管理事务。

#### 4.2.1 Spring事务管理器接口

事务管理器是PlatformTransactionManager接口对象。其主要用于完成事务的回滚，及获取事务的状态信息。

**接口内含有3个方法：**

- void commit(TransactionStatus status)：提交事务。
- TransactionStatus getTransaction(TransactionDefinition definition)：获取事务。
- void rollback(TransactionStatus status)：事务回滚。

**常用的两个实现类：**

- DataSourceTransactionManager：使用JDBC或iBatis进行持久化数据时使用。
- HibernateTransactionManager：使用Hibernate进行持久化数据时使用。

**Spring的回滚方式：**

Spring事务的默认回滚方式是：**发生运行时异常时回滚，发生受查异常时提交。**不过对于受查异常，程序员也可以手工设置其回滚方式。

#### 4.2.2 Spring事务定义接口

事务定义接口TransactionDefinition 中定义了事务描述相关的三类常量：事务隔离级别、
事务传播行为、事务默认超时时限，及对它们的操作。

**接口内含有5个方法：**

- int getIsolationLevel()：获取隔离级别。
- String getName()：获取事务名称。
- int getPropagationBehavior()：获取传播行为。
- int getTimeout()：获取超时时限。
- boolean isReadOnly()：是否是只读的。

**五个事务隔离级别常量：**

这些常量均是以ISOLATION_开头，即形如ISOLATION_XXX。

- DEFAULT：采用DB默认的事务隔离级别。MySql 的默认为REPEATABLE_READ； Oracle
默认为READ_COMMITTED。
- READ_UNCOMMITTED：读未提交。未解决任何并发问题。
- READ_COMMITTED：读已提交。解决脏读，存在不可重复读与幻读。
- REPEATABLE_READ：可重复读。解决脏读、不可重复读，存在幻读
- SERIALIZABLE：串行化。不存在并发问题。

**七个事务传播行为常量：**

所谓事务传播行为是指，处于不同事务中的方法在相互调用时，执行期间事务的维护情况。如，A事务中的方法doSome()调用B事务中的方法doOther()，在调用执行期间事务的维护情况，就称为事务传播行为。事务传播行为是加在方法上的。

事务传播行为常量都是以PROPAGATION_ 开头，形如PROPAGATION_XXX。

- REQUIRED（默认）：指定的方法必须在事务内执行。若当前存在事务，就加入到当前事务中；若当前没有事务，则创建一个新事务。这种传播行为是最常见的选择，也是Spring默认的事务传播行为。
如该传播行为加在doOther()方法上。若doSome()方法在执行时就是在事务内的，则doOther()方法的执行也加入到该事务内执行。若doSome()方法没有在事务内执行，则doOther()方法会创建一个事务，并在其中执行。
- SUPPORTS：指定的方法支持当前事务，但若当前没有事务，也可以以非事务方式执行。
- MANDATORY：指定的方法必须在当前事务内执行，若当前没有事务，则直接抛出异常。
- REQUIRES_NEW：总是新建一个事务，若当前存在事务，就将当前事务挂起，直到新事务执行完毕。
- NOT_SUPPORTED：指定的方法不能在事务环境中执行，若当前存在事务，就将当前事务挂起。
- NEVER：指定的方法不能在事务环境下执行，若当前存在事务，就直接抛出异常。
- NESTED：指定的方法必须在事务内执行。若当前存在事务，则在嵌套事务内执行；若当前没有事务，则创建一个新事务。

**默认事务超时时限：**

常量TIMEOUT_DEFAULT定义了事务底层默认的超时时限，及不支持事务超时时限设置的none值。
注意，事务的超时时限起作用的条件比较多，且超时的时间计算点较复杂。所以，该值一般就使用默认值即可。

#### 4.2.3 程序举例环境搭建

*源代码在Spring-10-Transaction-buyStock下。*

示例：购买股票

本例要实现一个简单的购买股票过程。存在两个实体：银行账户Account与股票账户Stock。当要购买股票时，需要从Account中扣除相应金额的存款，然后在Stock中增加相应的股票数量。而在这个过程中，可能会抛出一个用户自定义的异常。异常的抛出，将会使两个操作回滚。

步骤一：定义实体Bean

Account类：

    package tavish.bit.beans;

    public class Account {
        private Integer aid; // 数据库id
        private String aname; // 用户银行账户名称
        private double balance; // 用户银行账户余额

        public Account() {
            super();
        }

        public Account(String aname, double balance) {
            super();
            this.aname = aname;
            this.balance = balance;
        }

        public Integer getAid() {
            return aid;
        }

        public void setAid(Integer aid) {
            this.aid = aid;
        }

        public String getAname() {
            return aname;
        }

        public void setAname(String aname) {
            this.aname = aname;
        }

        public double getBalance() {
            return balance;
        }

        public void setBalance(double balance) {
            this.balance = balance;
        }

        @Override
        public String toString() {
            return "Account [aid=" + aid + ", aname=" + aname + ", balance=" + balance + "]";
        }
    }

Stock类：

    package tavish.bit.beans;

    public class Stock {
        private Integer sid; // 数据库id
        private String sname; // 用户持有的该股票名称
        private int amount; // 用户持有的该股票数量
        private int unitPrice; // 该股票单价

        public Stock() {
            super();
        }

        public Stock(String sname, int amount, int unitPrice) {
            super();
            this.sname = sname;
            this.amount = amount;
            this.unitPrice = unitPrice;
        }

        public Integer getSid() {
            return sid;
        }

        public void setSid(Integer sid) {
            this.sid = sid;
        }

        public String getSname() {
            return sname;
        }

        public void setSname(String sname) {
            this.sname = sname;
        }

        public int getAmount() {
            return amount;
        }

        public void setAmount(int amount) {
            this.amount = amount;
        }

        public void setUnitPrice(int unitPrice) {
            this.unitPrice = unitPrice;
        }

        public int getUnitPrice() {
            return unitPrice;
        }

        @Override
        public String toString() {
            return "Stock [sid=" + sid + ", sname=" + sname + ", amount=" + amount + ", unitPrice=" + unitPrice + "]";
        }
    }

步骤二：创建Service及其实现类

主业务接口：

    package tavish.bit.service;

    import tavish.bit.beans.Account;
    import tavish.bit.beans.Stock;

    public interface IBuyStockService {
        
        // 开户
        void openAccount(Account account);

        void openStock(Stock stock);
        
        // 查询账户
        Account getAccount(int id);
        
        Stock getStock(int id);
        
        // 购买&卖出
        void updateStock(Account account, Stock stock, int amount, boolean isBuy);
    }

实现类：

    package tavish.bit.service;

    import tavish.bit.beans.Account;
    import tavish.bit.beans.Stock;
    import tavish.bit.dao.IAccountDao;
    import tavish.bit.dao.IStockDao;

    public class BuyStockServiceImpl implements IBuyStockService {

        private IAccountDao adao;

        private IStockDao sdao;

        public void setAdao(IAccountDao adao) {
            this.adao = adao;
        }

        public void setSdao(IStockDao sdao) {
            this.sdao = sdao;
        }

        @Override
        public void openAccount(Account account) {
            adao.insertAccount(account);
        }

        @Override
        public void openStock(Stock stock) {
            sdao.insertStock(stock);
        }

        @Override
        public Account getAccount(int id) {
            return adao.selectAccount(id);
        }

        @Override
        public Stock getStock(int id) {
            return sdao.selectStock(id);
        }

        @Override
        public void updateStock(Account account, Stock stock, int amount, boolean isBuy) {
            adao.updateAccount(account, stock, amount, isBuy);
            sdao.updateStock(stock, amount, isBuy);
        }
    }

步骤三：创建Dao及其实现类

Dao接口：

    package tavish.bit.dao;

    import tavish.bit.beans.Account;
    import tavish.bit.beans.Stock;

    public interface IAccountDao {

        void insertAccount(Account account);

        Account selectAccount(int aid);

        void updateAccount(Account account, Stock stock, int amount, boolean isBuy);

    }

    package tavish.bit.dao;

    import tavish.bit.beans.Stock;

    public interface IStockDao {

        void insertStock(Stock stock);

        Stock selectStock(int sid);

        void updateStock(Stock stock, int amount, boolean isBuy);

    }

实现类：

    package tavish.bit.dao;

    import org.springframework.jdbc.core.support.JdbcDaoSupport;

    import tavish.bit.beans.Account;
    import tavish.bit.beans.Stock;

    public class AccountDaoImpl extends JdbcDaoSupport implements IAccountDao {

        @Override
        public void insertAccount(Account account) {
            String sql = "insert into account(aname, balance) values(?, ?)";
            this.getJdbcTemplate().update(sql, account.getAname(), account.getBalance());
        }
        
        @Override
        public Account selectAccount(int aid) {
            String sql = "select * from account where aid = ?";
            return this.getJdbcTemplate().queryForObject(sql, new AccountRowMapper(), aid);
        }

        @Override
        public void updateAccount(Account account, Stock stock, int amount, boolean isBuy) {
            String sql = isBuy ? "update account set balance = balance - ? where aname = ?"
                    : "update account set balance = balance + ? where aname = ?";
            this.getJdbcTemplate().update(sql, stock.getUnitPrice() * amount, account.getAname());
        }

    }

    package tavish.bit.dao;

    import org.springframework.jdbc.core.support.JdbcDaoSupport;

    import tavish.bit.beans.Stock;

    public class StockDaoImpl extends JdbcDaoSupport implements IStockDao {


        @Override
        public void insertStock(Stock stock) {
            String sql = "insert into stock(sname, amount, unitprice) values(?, ?, ?)";
            this.getJdbcTemplate().update(sql, stock.getSname(),stock.getAmount(),stock.getUnitPrice());    
        }

        @Override
        public Stock selectStock(int sid) {
            String sql = "select * from stock where sid = ?";
            return this.getJdbcTemplate().queryForObject(sql, new StockRowMapper(), sid);
        }

        @Override
        public void updateStock(Stock stock, int amount, boolean isBuy) {
            String sql = isBuy ? "update stock set amount = amount + ? where sname = ?"
                    : "update stock set amount = amount - ? where sname = ?";
            this.getJdbcTemplate().update(sql, amount, stock.getSname());
        }

    }

行映射器：

    package tavish.bit.dao;

    import java.sql.ResultSet;
    import java.sql.SQLException;

    import org.springframework.jdbc.core.RowMapper;

    import tavish.bit.beans.Account;


    public class AccountRowMapper implements RowMapper<Account>{

        @Override
        public Account mapRow(ResultSet rs, int rowNum) throws SQLException {
            Account account = new Account();
            account.setAid(rs.getInt("aid"));
            account.setAname(rs.getString("aname"));
            account.setBalance(rs.getDouble("balance"));
            return account;
        }

    }

    package tavish.bit.dao;

    import java.sql.ResultSet;
    import java.sql.SQLException;

    import org.springframework.jdbc.core.RowMapper;

    import tavish.bit.beans.Stock;

    public class StockRowMapper implements RowMapper<Stock>{

        @Override
        public Stock mapRow(ResultSet rs, int rowNum) throws SQLException {
            Stock stock = new Stock();
            stock.setSid(rs.getInt("sid"));
            stock.setSname(rs.getString("sname"));
            stock.setAmount(rs.getInt("amount"));
            stock.setUnitPrice(rs.getInt("unitprice"));
            return stock;
        }

    }


步骤四：创建数据库表account和stock

    CREATE TABLE `account` (
      `aid` INT(5) NOT NULL AUTO_INCREMENT,
      `aname` VARCHAR(20) DEFAULT NULL,
      `balance` DOUBLE DEFAULT NULL,
      PRIMARY KEY (`aid`)
    ) ENGINE=INNODB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8

    CREATE TABLE `stock` (
      `sid` INT(5) NOT NULL AUTO_INCREMENT,
      `sname` VARCHAR(20) DEFAULT NULL,
      `amount` INT(5) DEFAULT NULL,
      `unitprice` INT(5) DEFAULT NULL,
      PRIMARY KEY (`sid`)
    ) ENGINE=INNODB DEFAULT CHARSET=utf8

步骤五：配置文件

    <!-- 注册数据源：c3p0数据源 -->
    <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 数据库连接四要素 -->
        <property name="driverClass" value="${jdbc.driver}" />
        <property name="jdbcUrl" value="${jdbc.url}" />
        <property name="user" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
    </bean> 
         
    <!-- 添加context约束，然后使用如下标签注册属性文件 -->
    <context:property-placeholder location="classpath:jdbc.properties" />
     
    <!-- 注册Dao -->
    <bean id="accountDao" class="tavish.bit.dao.AccountDaoImpl" >
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>
    <bean id="stockDao" class="tavish.bit.dao.StockDaoImpl" >
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>
     
    <!-- 注册Service -->
    <bean id="buyStockService" class="tavish.bit.service.BuyStockServiceImpl" >
        <!-- 配置设值注入 -->
        <property name="adao" ref="accountDao" />
        <property name="sdao" ref="stockDao" />
    </bean>

步骤六：测试

    package tavish.bit.test;

    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    import tavish.bit.beans.Account;
    import tavish.bit.beans.Stock;
    import tavish.bit.service.IBuyStockService;

    public class MyTest {

        private IBuyStockService service;
        private ApplicationContext context;
        
        @Before
        public void before() {
            context = new ClassPathXmlApplicationContext("applicationContext.xml");
            service = (IBuyStockService) context.getBean("buyStockService");
        }
        
        @After
        public void after() {
            ((ClassPathXmlApplicationContext)context).close();
        }
        
        @Test
        public void testOpen() {
            Account account = new Account("Tavish", 10000);
            Stock stock = new Stock("BIT", 0, 50);
            service.openAccount(account);
            service.openStock(stock);
        }
        
        @Test
        public void testGet() {
            System.out.println(service.getAccount(1));
            System.out.println(service.getStock(1));
        }
        
        @Test
        public void testBuyStock() {
            Account account = service.getAccount(1);
            Stock stock = service.getStock(1);
            service.updateStock(account, stock, 50, true);
        }
        
        @Test
        public void testSoldStock() {
            Account account = service.getAccount(1);
            Stock stock = service.getStock(1);
            service.updateStock(account, stock, 20, false);
        }
    }

步骤七：模拟购买时发生异常

定义异常：

    package tavish.bit.exception;

    public class BuyStockException extends Exception {
        
        private static final long serialVersionUID = -2302395152805564386L;

        public BuyStockException() {
            super();
        }

        public BuyStockException(String message) {
            super(message);
        }
        
    }

设置Service主业务接口，抛出异常：

    void updateStock(Account account, Stock stock, int amount, boolean isBuy) throws BuyStockException;

实现类抛出异常：

    @Override
    public void updateStock(Account account, Stock stock, int amount, boolean isBuy) throws BuyStockException {
        adao.updateAccount(account, stock, amount, isBuy);
        sdao.updateStock(stock, amount, isBuy);
    }

测试类抛出该异常：

    @Test
    public void testBuyStock() throws BuyStockException {
        Account account = service.getAccount(1);
        Stock stock = service.getStock(1);
        service.updateStock(account, stock, 50, true);
    }
    
    @Test
    public void testSoldStock() throws BuyStockException {
        Account account = service.getAccount(1);
        Stock stock = service.getStock(1);
        service.updateStock(account, stock, 20, false);
    }

下面模拟买股票发生异常的情况，使updateStock方法发生异常：

    @Override
    public void updateStock(Account account, Stock stock, int amount, boolean isBuy) throws BuyStockException {
        adao.updateAccount(account, stock, amount, isBuy);
        if (isBuy) {
            throw new BuyStockException("购买股票没有成功");
        }
        sdao.updateStock(stock, amount, isBuy);
    }

此时买股票会发生异常，即钱少了，股票没多。

**
这个问题是由于在updateStock方法中，两个dao的方法只执行了一个。要修正这个问题，要使程序达到这样的效果：这两个dao方法只能同时生效或同时失败，即，使这两个操作具有原子性。
所以我们要添加事务，把事务从DAO层提升到Service层。
**

#### 4.2.4 使用Spring的事务代理工厂管理事务

*源代码在Spring-10-Transaction-buyStock-txProxyFactoryBean下。*

该方式是，需要为目标类，即Service的实现类创建事务代理。事务代理使用的类是TransactionProxyFactoryBean，该类需要初始化如下一些属性：

- transactionManager：事务管理器
- target：目标对象，即Service实现类对象
- transactionAttributes：事务属性设置

对于XML配置代理方式实现事务管理时，受查异常的回滚方式，程序员可以通过以下方式进行设置：**通过“-异常”方式，可使发生指定的异常时事务回滚；通过“+异常”方式，可使发生指定的异常时事务提交。**

配置文件，增加事务配置：

    <!-- 注册事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>
    
    <!-- 生成Service的事务代理对象 -->
    <bean id="serviceProxy" class="org.springframework.transaction.interceptor.TransactionProxyFactoryBean">
        <property name="target" ref="buyStockService" />
        <property name="transcationManager" ref="transactionManager" />
        <property name="transcationAttributes">
            <props>
                <!-- 在指定的切入点方法上要应用什么事务属性 -->
                <prop key="open*">ISOLATION_DEFAULT, PROPAGATION_REQUIRED</prop>
                <prop key="get*">ISOLATION_DEFAULT, PROPAGATION_REQUIRED</prop>
                <prop key="updateStock">ISOLATION_DEFAULT, PROPAGATION_REQUIRED, -BuyStockException</prop>
            </props>
        </property>
    </bean>

测试方法，getBean方法使用代理类的id：

    @Before
    public void before() {
        context = new ClassPathXmlApplicationContext("applicationContext.xml");
        service = (IBuyStockService) context.getBean("serviceProxy");
    }

    @Test
    public void testBuyStock() throws BuyStockException {
        Account account = service.getAccount(1);
        Stock stock = service.getStock(1);
        service.updateStock(account, stock, 50, true);
    }

测试结果：

发生“购买股票没有成功”异常，但是钱没有少，股票也没有多。

#### 4.2.5 使用Spring的事务注解管理事务

*源代码在Spring-10-Transaction-buyStock-txProxyFactoryBean下。*

通过@Transactional注解方式，也可将事务织入到相应方法中。而使用注解方式，只需在配置文件中加入一个tx标签，以告诉spring使用注解来完成事务的织入。该标签只需指定一个属性，事务管理器。

配置文件，需增加tx约束：

    <!-- ======================== 事务 ======================== -->
    <!-- 注册事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>
    
    <!-- 注册事务注解驱动 -->
    <tx:annotation-driven transaction-manager="transactionManager"/>

主业务实现类，增加注解：

    @Transactional(isolation=Isolation.DEFAULT, propagation=Propagation.REQUIRED)
    @Override
    public void openAccount(Account account) {
        adao.insertAccount(account);
    }

    @Transactional(isolation=Isolation.DEFAULT, propagation=Propagation.REQUIRED)
    @Override
    public void openStock(Stock stock) {
        sdao.insertStock(stock);
    }

    @Transactional(isolation=Isolation.DEFAULT, propagation=Propagation.REQUIRED)
    @Override
    public Account getAccount(int id) {
        return adao.selectAccount(id);
    }

    @Transactional(isolation=Isolation.DEFAULT, propagation=Propagation.REQUIRED)
    @Override
    public Stock getStock(int id) {
        return sdao.selectStock(id);
    }

    @Transactional(isolation=Isolation.DEFAULT, propagation=Propagation.REQUIRED, rollbackFor=BuyStockException.class)
    @Override
    public void updateStock(Account account, Stock stock, int amount, boolean isBuy) throws BuyStockException {
        adao.updateAccount(account, stock, amount, isBuy);
        if (isBuy) {
            throw new BuyStockException("购买股票没有成功");
        }
        sdao.updateStock(stock, amount, isBuy);
    }

测试结果正常，发生异常时回滚。

#### 4.2.6 使用AspectJ的AOP配置管理事务

配置文件：

    <!-- ======================== 事务 ======================== -->
    <!-- 注册事务管理器 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="c3p0DataSource" />
    </bean>
    
    <!-- 注册事务通知 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- 对连接点方法上要使用的事务属性进行配置 -->
            <tx:method name="open*" isolation="DEFAULT" propagation="REQUIRED"/>
            <tx:method name="get*" isolation="DEFAULT" propagation="REQUIRED"/>
            <tx:method name="updateStock" isolation="DEFAULT" propagation="REQUIRED" rollback-for="BuyStockException"/>
        </tx:attributes>
    </tx:advice>
    
    <!-- AOP配置 -->
    <aop:config>
        <!-- 指定切入点 -->
        <aop:pointcut expression="execution(* *..service.*.*(..))" id="myPointcut"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="myPointcut"/>
    </aop:config>

测试结果正常。

## 第五章 SSH框架整合

### 5.1 Spring与Hibernate整合

*源码在Spring-11-SSH-Spring_Hibernate下。*

除了jdbcTemplate外，Spring还可通过Hibernate来完成Dao工作。即将Spring与Hibernate进行整合。

*Spring中一般不适用Hibernate模板对象。*

**
整合的重点：将Hibernate的SessionFactory交给Spring管理。
**

具体整合示例：

（1） 整合Jar包

- Spring_Jar:
    + spring-aop-4.3.9.RELEASE
    + spring-aspects-4.3.9.RELEASE
    + spring-beans-4.3.9.RELEASE
    + spring-context-4.3.9.RELEASE
    + spring-core-4.3.9.RELEASE
    + spring-expression-4.3.9.RELEASE
    + spring-jdbc-4.3.9.RELEASE
    + spring-orm-4.3.9.RELEASE
    + spring-test-4.3.9.RELEASE
    + spring-tx-4.3.9.RELEASE
- AspectJ:
    + aopalliance-1.0
    + aspectjweaver-1.8.10
- Hibernate_Jar:
    + hibernate-jpamodelgen-5.0.1.Final
    + hibernate-jpa-2.1-api-1.0.0.Final
    + hibernate-entitymanager-5.0.1.Final
    + hibernate-core-5.0.1.Final
    + hibernate-commons-annotations-5.0.0.Final
    + hibernate-c3p0-5.0.1.Final
    + antlr-2.7.7
    + geronimo-jta_1.1_spec-1.1.1
    + javassist-3.18.1-GA
    + jandex-1.2.2.Final
    + jboss-logging-3.3.0.Final
- c3p0:
    + c3p0-0.9.2.1
    + mchange-commons-java-0.2.3.4
- dbcp2:
    + commons-dbcp2-2.1.1
    + commons-pool2-2.4.3
- mysql:
    + mysql-connector-java-5.1.44-bin
- log:
    + commons-logging-1.2
    + slf4j-api-1.6.1
    + slf4j-log4j12-1.7.12
    + log4j-1.2.17
    + log4j-api-2.9.1
    + log4j-core-2.9.1
- dom4j:
    + dom4j-1.6.1
- JUnit:
    + junit-4.12
    + hamcrest-core-1.3

（2） SessionFactory的注册

Spring的精髓是，所有的Bean均由Spring容器统一管理，所以在SPring中若要使用Hibernate，就需要将SessionFactory交由Spring来管理。
配置SessionFactory的Bean，将hibernate.cfg.xml文件替换掉。使用的实现类为LocalSessionFactoryBean，注意，是hibernate5包下的。其用于设置的属性主要有三个：数据源，映射文件，及hibernate特性。其设置内容，与Hibernate主配置文件的基本相同。

    <!-- 注册SessionFactory -->
    <bean id="mySessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
        <!-- 配置数据源 -->
        <property name="dataSource" ref="c3p0DataSource" />
        <!-- 配置映射文件所在文件夹 -->
        <property name="mappingDirectoryLocations" value="tavish/bit/beans" />      
        <!-- 配置Hibernate属性 -->
        <property name="hibernateProperties">
            <props>
                <!-- 配置方言 -->
                <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                <!-- 配置session策略 -->
                <prop key="hibernate.current_session_context_class">org.springframework.orm.hibernate5.SpringSessionContext</prop>
                <!-- 配置自动建表 -->
                <prop key="hibernate.hbm2ddl.auto">update</prop>
                <!-- 显示SQL语句 -->
                <prop key="hibernate.show_sql">true</prop>
                <!-- sql语句格式化输出 -->
                <prop key="hibernate.format_sql">true</prop>
            </props>
        </property>
    </bean>

（3） 配置数据源

这里使用c3p0数据源。

    <!-- 注册数据源：c3p0数据源 -->
    <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!-- 数据库连接四要素 -->
        <property name="driverClass" value="${jdbc.driver}" />
        <property name="jdbcUrl" value="${jdbc.url}" />
        <property name="user" value="${jdbc.user}" />
        <property name="password" value="${jdbc.password}" />
    </bean>
    
    <!-- 添加context约束，然后使用如下标签注册属性文件 -->
    <context:property-placeholder location="classpath:jdbc.properties" />

（4） 注册事务管理器及AspectJ事务管理

    <!-- 注册事务管理器 -->
    <bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
        <property name="sessionFactory" ref="mySessionFactory" />
    </bean> 
    
    <!-- 使用AspectJ的AOP事务管理 -->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="add*" isolation="DEFAULT" propagation="REQUIRED"/>
            <tx:method name="remove*" isolation="DEFAULT" propagation="REQUIRED"/>
            <tx:method name="modify*" isolation="DEFAULT" propagation="REQUIRED"/>
            <tx:method name="find*" isolation="DEFAULT" propagation="REQUIRED" read-only="true"/>
        </tx:attributes>
    </tx:advice>
    
    <aop:config>
        <aop:pointcut expression="execution(* *..service.*.*(..))" id="myPointcut"/>
        <aop:advisor advice-ref="txAdvice" pointcut-ref="myPointcut"/>
    </aop:config>

（5） 注册Dao及Service

    <!-- 注册Dao -->
    <bean id="studentDao" class="tavish.bit.dao.StudentDaoHbnImpl" >
        <property name="sessionFactory" ref="mySessionFactory" />
    </bean>
     
    <!-- 注册Service -->
    <bean id="studentService" class="tavish.bit.service.StudentServiceImpl" >
        <!-- 配置设值注入 -->
        <property name="dao" ref="studentDao" />
    </bean>

（6） 业务代码

1）Service层：

主业务接口：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentService {
        void addStudent(Student student);

        void removeStudent(Student student);

        void modifyStudent(Student student);

        List<Student> findAllStudents();

        Student findStudentById(int id);
    }

实现类：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;
    import tavish.bit.dao.IStudentDao;

    public class StudentServiceImpl implements IStudentService {

        private IStudentDao dao;

        public void setDao(IStudentDao dao) {
            this.dao = dao;
        }

        @Override
        public void addStudent(Student student) {
            dao.insertStudent(student);
        }

        @Override
        public void removeStudent(Student student) {
            dao.deleteStudent(student);
        }

        @Override
        public void modifyStudent(Student student) {
            dao.updateStudent(student);
        }

        @Override
        public List<Student> findAllStudents() {
            return dao.selectAllStudents();
        }

        @Override
        public Student findStudentById(int id) {
            return dao.selectStudentById(id);
        }
    }

2）Dao层：

Dao接口：

    package tavish.bit.dao;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        void insertStudent(Student student);

        void deleteStudent(Student student);

        void updateStudent(Student student);

        List<Student> selectAllStudents();

        Student selectStudentById(int id);
    }

实现类：

    package tavish.bit.dao;

    import java.util.List;

    import org.hibernate.SessionFactory;

    import tavish.bit.beans.Student;

    public class StudentDaoHbnImpl implements IStudentDao {

        private SessionFactory sessionFactory;
        
        public void setSessionFactory(SessionFactory sessionFactory) {
            this.sessionFactory = sessionFactory;
        }

        @Override
        public void insertStudent(Student student) {
            sessionFactory.getCurrentSession().save(student);
        }

        @Override
        public void deleteStudent(Student student) {
            sessionFactory.getCurrentSession().delete(student);
        }

        @Override
        public void updateStudent(Student student) {
            sessionFactory.getCurrentSession().update(student);
        }

        @SuppressWarnings("unchecked")
        @Override
        public List<Student> selectAllStudents() {
            String hql = "from Student";
            return sessionFactory.getCurrentSession().createQuery(hql).list();
        }

        @Override
        public Student selectStudentById(int id) {
            return sessionFactory.getCurrentSession().get(Student.class, id);
        }
    }

3）Bean类及映射文件：

Bean类：

    package tavish.bit.beans;

    public class Student {
        private Integer id;
        private String name;
        private int age;

        public Student(String name, int age) {
            super();
            this.name = name;
            this.age = age;
        }

        public Student() {
            super();
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

        @Override
        public String toString() {
            return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
        }

    }

映射文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE hibernate-mapping PUBLIC 
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

    <hibernate-mapping package="tavish.bit.beans">

        <class name="Student">
            <id name="id">
                <generator class="native" />
            </id>
            <property name="name" />
            <property name="age" />
        </class>
        
    </hibernate-mapping>

4）测试类

    package tavish.bit.test;

    import java.util.List;

    import org.junit.After;
    import org.junit.Before;
    import org.junit.Test;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    public class MyTest {
        
        private ApplicationContext context;
        private IStudentService service;

        @Before
        public void before() {
            context = new ClassPathXmlApplicationContext("applicationContext.xml");
            service = (IStudentService) context.getBean("studentService");
        }
        
        @After
        public void After() {
            ((ClassPathXmlApplicationContext)context).close();
        }
        
        // 增
        @Test
        public void testAdd() {
            Student student = new Student("张三", 20);
            service.addStudent(student);
        }
        
        // 删
        @Test
        public void testRemove() {
            Student student = new Student();
            student.setId(3);
            service.removeStudent(student);
        }
        
        // 改
        @Test
        public void testModify() {
            Student student = new Student("李四", 24);
            student.setId(1);
            service.modifyStudent(student);
        }
        
        // 查询所有学生
        @Test
        public void testFindAllStudents() {
            List<Student> students = service.findAllStudents();
            System.out.println(students);
        }
        
        // 根据Id查询学生
        @Test
        public void testFindStudentById() {
            Student student = service.findStudentById(1);
            System.out.println(student);
        }
    }

### 5.2 Spring在Web项目中的应用

在Web项目中使用Spring框架，首先要解决在Servlet中（暂时不使用Struts2）获取
到Spring容器的问题。只要在View层获取到了Spring容器，便可从容器中获取到Service对象。

#### 5.2.1 Spring、Hibernate与Web整合的简单实现

##### 5.2.1.1 在页面上提交表单对数据库进行插入

*源代码在Spring-11-SSH-web下。*

步骤一：创建实体及映射文件

实体：

    package tavish.bit.beans;

    public class Student {
        private Integer id;
        private String name;
        private int age;

        public Student(String name, int age) {
            super();
            this.name = name;
            this.age = age;
        }

        public Student() {
            super();
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

        @Override
        public String toString() {
            return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
        }

    }

映射文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE hibernate-mapping PUBLIC 
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

    <hibernate-mapping package="tavish.bit.beans">

        <class name="Student">
            <id name="id">
                <generator class="native" />
            </id>
            <property name="name" />
            <property name="age" />
        </class>
        
    </hibernate-mapping>

步骤二：创建Service接口及实现类

Service接口：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentService {
        void addStudent(Student student);

        void removeStudent(Student student);

        void modifyStudent(Student student);

        List<Student> findAllStudents();

        Student findStudentById(int id);
    }

实现类：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;
    import tavish.bit.dao.IStudentDao;

    public class StudentServiceImpl implements IStudentService {

        private IStudentDao dao;

        public void setDao(IStudentDao dao) {
            this.dao = dao;
        }

        @Override
        public void addStudent(Student student) {
            dao.insertStudent(student);
        }

        @Override
        public void removeStudent(Student student) {
            dao.deleteStudent(student);
        }

        @Override
        public void modifyStudent(Student student) {
            dao.updateStudent(student);
        }

        @Override
        public List<Student> findAllStudents() {
            return dao.selectAllStudents();
        }

        @Override
        public Student findStudentById(int id) {
            return dao.selectStudentById(id);
        }
    }

步骤三：创建Dao接口及实现类

接口：

    package tavish.bit.dao;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        void insertStudent(Student student);

        void deleteStudent(Student student);

        void updateStudent(Student student);

        List<Student> selectAllStudents();

        Student selectStudentById(int id);
    }


实现类：

    package tavish.bit.dao;

    import java.util.List;

    import org.hibernate.SessionFactory;

    import tavish.bit.beans.Student;

    public class StudentDaoHbnImpl implements IStudentDao {

        private SessionFactory sessionFactory;
        
        public void setSessionFactory(SessionFactory sessionFactory) {
            this.sessionFactory = sessionFactory;
        }

        @Override
        public void insertStudent(Student student) {
            sessionFactory.getCurrentSession().save(student);
        }

        @Override
        public void deleteStudent(Student student) {
            sessionFactory.getCurrentSession().delete(student);
        }

        @Override
        public void updateStudent(Student student) {
            sessionFactory.getCurrentSession().update(student);
        }

        @SuppressWarnings("unchecked")
        @Override
        public List<Student> selectAllStudents() {
            String hql = "from Student";
            return sessionFactory.getCurrentSession().createQuery(hql).list();
        }

        @Override
        public Student selectStudentById(int id) {
            return sessionFactory.getCurrentSession().get(Student.class, id);
        }
    }

步骤四：配置Spring容器

Spring配置文件：

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
            
        <!-- 注册数据源：c3p0数据源 -->
        <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <!-- 数据库连接四要素 -->
            <property name="driverClass" value="${jdbc.driver}" />
            <property name="jdbcUrl" value="${jdbc.url}" />
            <property name="user" value="${jdbc.user}" />
            <property name="password" value="${jdbc.password}" />
        </bean>
        
        <!-- 添加context约束，然后使用如下标签注册属性文件 -->
        <context:property-placeholder location="classpath:jdbc.properties" />
        
        <!-- 注册SessionFactory -->
        <bean id="mySessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
            <!-- 配置数据源 -->
            <property name="dataSource" ref="c3p0DataSource" />
            <!-- 配置映射文件所在文件夹 -->
            <property name="mappingDirectoryLocations" value="tavish/bit/beans" />      
            <!-- 配置Hibernate属性 -->
            <property name="hibernateProperties">
                <props>
                    <!-- 配置方言 -->
                    <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                    <!-- 配置session策略 -->
                    <prop key="hibernate.current_session_context_class">org.springframework.orm.hibernate5.SpringSessionContext</prop>
                    <!-- 配置自动建表 -->
                    <prop key="hibernate.hbm2ddl.auto">update</prop>
                    <!-- 显示SQL语句 -->
                    <prop key="hibernate.show_sql">true</prop>
                    <!-- sql语句格式化输出 -->
                    <prop key="hibernate.format_sql">true</prop>
                </props>
            </property>
        </bean>
        
        <!-- 注册事务管理器 -->
        <bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
            <property name="sessionFactory" ref="mySessionFactory" />
        </bean> 
        
        <!-- 使用AspectJ的AOP事务管理 -->
        <tx:advice id="txAdvice" transaction-manager="transactionManager">
            <tx:attributes>
                <tx:method name="add*" isolation="DEFAULT" propagation="REQUIRED"/>
                <tx:method name="remove*" isolation="DEFAULT" propagation="REQUIRED"/>
                <tx:method name="modify*" isolation="DEFAULT" propagation="REQUIRED"/>
                <tx:method name="find*" isolation="DEFAULT" propagation="REQUIRED" read-only="true"/>
            </tx:attributes>
        </tx:advice>
        
        <aop:config>
            <aop:pointcut expression="execution(* *..service.*.*(..))" id="myPointcut"/>
            <aop:advisor advice-ref="txAdvice" pointcut-ref="myPointcut"/>
        </aop:config>
         
        <!-- 注册Dao -->
        <bean id="studentDao" class="tavish.bit.dao.StudentDaoHbnImpl" >
            <property name="sessionFactory" ref="mySessionFactory" />
        </bean>
         
        <!-- 注册Service -->
        <bean id="studentService" class="tavish.bit.service.StudentServiceImpl" >
            <!-- 配置设值注入 -->
            <property name="dao" ref="studentDao" />
        </bean>

    </beans>

步骤五：创建Servlet及Jsp页面

Servlet：

    package tavish.bit.servlets;

    import java.io.IOException;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    import org.springframework.context.support.AbstractApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    @WebServlet(urlPatterns = { "/registerServlet" })
    public class RegisterServlet extends HttpServlet {
        private static final long serialVersionUID = 1L;

        public RegisterServlet() {
            super();
        }

        protected void doGet(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            request.setCharacterEncoding("utf-8");
            // 获取页面提交的参数
            String name = request.getParameter("name");
            int age = Integer.parseInt(request.getParameter("age"));
            // 创建实体
            Student student = new Student(name, age);

            // 1. 创建Spring容器
            AbstractApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
            // 2. 从容器中获取Service对象
            IStudentService service = (IStudentService) context.getBean("studentService");
            // 3. Service对象调用addStudent()方法
            service.addStudent(student);

            request.getRequestDispatcher("/welcome.jsp").forward(request, response);

            context.close();
        }

        protected void doPost(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            doGet(request, response);
        }
    }

Jsp：

register.jsp：

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Register</title>
    </head>
    <body>
        <form action="registerServlet" method="post">
            <label>姓名：<input type="text" name="name" /></label><br>
            <label>年龄：<input type="text" name="age" id="age"/></label><br>
            <input type="submit" value="注册"/>
        </form>
    </body>
    </html>

welcome.jsp：

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Welcome</title>
    </head>
    <body>
        Welcome Page!
    </body>
    </html>

**发布项目，测试结果正常。**

##### 5.2.1.2 在Web容器初始化时初始化Spring容器

但上述实现有个很严重的问题：**每次访问这个Servlet时，都会创建一个Spring容器。这是不正确的，Spring容器应该在Web项目启动时只初始化一次。**

**
通过配置监听器解决这个问题：
**

*源代码在Spring-11-SSH-web-2下。*

修改web.xml，注册监听器（需要导入spring-web-4.3.9.RELEASE.jar）：

    <!-- 注册ServletContext监听器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

**此时需要将Spring的配置文件放在WEB-INF下，并且文件名必须是applicationContext.xml。因为默认情况下会到WEB-INF下找名为applicationContext.xml的配置文件。**

同时修改SessionFactory的配置属性，指明类路径：

    <!-- 配置映射文件所在文件夹 -->
    <property name="mappingDirectoryLocations" value="classpath:tavish/bit/beans" />       

修改Servlet：

    package tavish.bit.servlets;

    import java.io.IOException;

    import javax.servlet.ServletContext;
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    import org.springframework.web.context.WebApplicationContext;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    @WebServlet(urlPatterns = { "/registerServlet" })
    public class RegisterServlet extends HttpServlet {
        private static final long serialVersionUID = 1L;

        protected void doGet(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            request.setCharacterEncoding("utf-8");
            // 获取页面提交的参数
            String name = request.getParameter("name");
            int age = Integer.parseInt(request.getParameter("age"));
            // 创建实体
            Student student = new Student(name, age);

            // 1. 从ServletContext中获取Spring容器
            ServletContext servletContext = this.getServletContext();
            WebApplicationContext context = (WebApplicationContext) servletContext.getAttribute(WebApplicationContext.ROOT_WEB_APPLICATION_CONTEXT_ATTRIBUTE);
            // 2. 从容器中获取Service对象
            IStudentService service = (IStudentService) context.getBean("studentService");
            // 3. Service对象调用addStudent()方法
            service.addStudent(student);

            request.getRequestDispatcher("/welcome.jsp").forward(request, response);
        }

        protected void doPost(HttpServletRequest request, HttpServletResponse response)
                throws ServletException, IOException {
            doGet(request, response);
        }

    }

**发布项目，测试结果正常。**

**自动建表问题：**

在Spring容器初始化时，如果没有student表，则会由于自动建表而创建一个空表。此时可以正常在页面上提交表单进行数据库的插入；但如果在容器启动之后，手动删除student表，此时再通过提交表单进行数据库的插入则会发生异常，因为此时没有student表。
即自动建表动作只会在Spring容器初始化时发生一次。

##### 5.2.1.3 修改Spring配置文件的位置及名称

但是，此时还有一些问题：Spring的配置文件被放在了WEB-INF下，不符合我们的习惯，同时还对配置文件的文件名加了限制。下面我们将改变Spring配置文件的存放位置及其文件名。

*源代码在Spring-11-SSH-web-3下。*

修改web.xml文件：

    <context-param>
        <description>设置Spring配置文件位置及名称</description>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>

此时设置了Spring的配置文件位置为类路径，同时指定名字为Spring.xml。

**发布项目，测试结果正常。**

##### 5.2.1.4 使用工具获取容器对象

*源代码在Spring-11-SSH-web-3下。*

将获取Spring容器的代码改为：

    // 1. 使用工具获取Spring容器对象
    WebApplicationContext context = WebApplicationContextUtils.getRequiredWebApplicationContext(this.getServletContext());

**发布项目，测试结果正常。**

#### 5.2.2 Spring与Struts2、Hibernate整合

Spring与Struts2整合的目的有两个：

1. 在Struts2的Action中，即View层，获取到Service。然后就实现了在Struts2项目中view层、Service层、Dao层的联通。
2. 将Action实例交给Spring容器管理。

##### 5.2.2.1 Action实例由Strust2创建

*源代码在Spring-12-SSH-Integration下。*

示例：

（1）实体类及映射文件：

bean类：

    package tavish.bit.beans;

    public class Student {
        private Integer id;
        private String name;
        private int age;

        public Student(String name, int age) {
            super();
            this.name = name;
            this.age = age;
        }

        public Student() {
            super();
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

        @Override
        public String toString() {
            return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
        }

    }

映射文件：

    <?xml version="1.0" encoding="UTF-8"?>
    <!DOCTYPE hibernate-mapping PUBLIC 
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

    <hibernate-mapping package="tavish.bit.beans">
        <class name="Student">
            <id name="id">
                <generator class="native" />
            </id>
            <property name="name" />
            <property name="age" />
        </class>
    </hibernate-mapping>

（2）Dao接口及实现类：

接口：

    package tavish.bit.dao;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentDao {
        void insertStudent(Student student);

        void deleteStudent(Student student);

        void updateStudent(Student student);

        List<Student> selectAllStudents();

        Student selectStudentById(int id);
    }

实现类：

    package tavish.bit.dao;

    import java.util.List;

    import org.hibernate.SessionFactory;

    import tavish.bit.beans.Student;

    public class StudentDaoHbnImpl implements IStudentDao {

        private SessionFactory sessionFactory;
        
        public void setSessionFactory(SessionFactory sessionFactory) {
            this.sessionFactory = sessionFactory;
        }

        @Override
        public void insertStudent(Student student) {
            sessionFactory.getCurrentSession().save(student);
        }

        @Override
        public void deleteStudent(Student student) {
            sessionFactory.getCurrentSession().delete(student);
        }

        @Override
        public void updateStudent(Student student) {
            sessionFactory.getCurrentSession().update(student);
        }

        @SuppressWarnings("unchecked")
        @Override
        public List<Student> selectAllStudents() {
            String hql = "from Student";
            return sessionFactory.getCurrentSession().createQuery(hql).list();
        }

        @Override
        public Student selectStudentById(int id) {
            return sessionFactory.getCurrentSession().get(Student.class, id);
        }
    }

（3）Service接口及实现类

接口：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;

    public interface IStudentService {
        void addStudent(Student student);

        void removeStudent(Student student);

        void modifyStudent(Student student);

        List<Student> findAllStudents();

        Student findStudentById(int id);
    }

实现类：

    package tavish.bit.service;

    import java.util.List;

    import tavish.bit.beans.Student;
    import tavish.bit.dao.IStudentDao;

    public class StudentServiceImpl implements IStudentService {

        private IStudentDao dao;

        public void setDao(IStudentDao dao) {
            this.dao = dao;
        }

        @Override
        public void addStudent(Student student) {
            dao.insertStudent(student);
        }

        @Override
        public void removeStudent(Student student) {
            dao.deleteStudent(student);
        }

        @Override
        public void modifyStudent(Student student) {
            dao.updateStudent(student);
        }

        @Override
        public List<Student> findAllStudents() {
            return dao.selectAllStudents();
        }

        @Override
        public Student findStudentById(int id) {
            return dao.selectStudentById(id);
        }
    }

（4）Action

    package tavish.bit.actions;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    public class RegisterAction {
        private String name;
        private int age;
        
        // 要保证这里的Service属性名与Spring容器中该Bean的id名称相同
        // 采用的是byName域属性的自动注入
        private IStudentService studentService;

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

        public void setStudentService(IStudentService studentService) {
            this.studentService = studentService;
        }

        public String execute() {
            Student student = new Student(name, age);
            studentService.addStudent(student);
            return "success";
        }
    }


（5）配置文件

Spring：

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
            
        <!-- 注册数据源：c3p0数据源 -->
        <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <!-- 数据库连接四要素 -->
            <property name="driverClass" value="${jdbc.driver}" />
            <property name="jdbcUrl" value="${jdbc.url}" />
            <property name="user" value="${jdbc.user}" />
            <property name="password" value="${jdbc.password}" />
        </bean>
        
        <!-- 添加context约束，然后使用如下标签注册属性文件 -->
        <context:property-placeholder location="classpath:jdbc.properties" />
         
        
        <!-- 注册SessionFactory -->
        <bean id="mySessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
            <!-- 配置数据源 -->
            <property name="dataSource" ref="c3p0DataSource" />
            <!-- 配置映射文件所在文件夹 -->
            <property name="mappingDirectoryLocations" value="classpath:tavish/bit/beans" />        
            <!-- 配置Hibernate属性 -->
            <property name="hibernateProperties">
                <props>
                    <!-- 配置方言 -->
                    <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                    <!-- 配置session策略 -->
                    <prop key="hibernate.current_session_context_class">org.springframework.orm.hibernate5.SpringSessionContext</prop>
                    <!-- 配置自动建表 -->
                    <prop key="hibernate.hbm2ddl.auto">update</prop>
                    <!-- 显示SQL语句 -->
                    <prop key="hibernate.show_sql">true</prop>
                    <!-- sql语句格式化输出 -->
                    <prop key="hibernate.format_sql">true</prop>
                </props>
            </property>
        </bean>
        
        <!-- 注册事务管理器 -->
        <bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
            <property name="sessionFactory" ref="mySessionFactory" />
        </bean> 
        
        <!-- 使用AspectJ的AOP事务管理 -->
        <tx:advice id="txAdvice" transaction-manager="transactionManager">
            <tx:attributes>
                <tx:method name="add*" isolation="DEFAULT" propagation="REQUIRED"/>
                <tx:method name="remove*" isolation="DEFAULT" propagation="REQUIRED"/>
                <tx:method name="modify*" isolation="DEFAULT" propagation="REQUIRED"/>
                <tx:method name="find*" isolation="DEFAULT" propagation="REQUIRED" read-only="true"/>
            </tx:attributes>
        </tx:advice>
        
        <aop:config>
            <aop:pointcut expression="execution(* *..service.*.*(..))" id="myPointcut"/>
            <aop:advisor advice-ref="txAdvice" pointcut-ref="myPointcut"/>
        </aop:config>
         
        <!-- 注册Dao -->
        <bean id="studentDao" class="tavish.bit.dao.StudentDaoHbnImpl" >
            <property name="sessionFactory" ref="mySessionFactory" />
        </bean>
         
        <!-- 注册Service -->
        <bean id="studentService" class="tavish.bit.service.StudentServiceImpl" >
            <!-- 配置设值注入 -->
            <property name="dao" ref="studentDao" />
        </bean>
    </beans>

Struts2：

    <?xml version="1.0" encoding="UTF-8" ?>

    <!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

    <struts>
        <package name="myAction" namespace="/test" extends="struts-default">
            <action name="register" class="tavish.bit.actions.RegisterAction">
                <result>/welcome.jsp</result>
            </action>
        </package>
    </struts>

tomcat：

    <?xml version="1.0" encoding="UTF-8"?>
    <web-app
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
        id="WebApp_ID"
        version="3.1">
        <display-name>Spring-12-SSH-Integration</display-name>

        <!-- 设置Spring配置文件位置及名称 -->
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring.xml</param-value>
        </context-param>

        <!-- 注册ServletContext监听器 -->
        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
        
        <!-- 配置Struts2过滤器 -->
        <filter>
            <filter-name>Struts2</filter-name>
            <filter-class>org.apache.struts2.dispatcher.filter.StrutsPrepareAndExecuteFilter</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>Struts2</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping>
    </web-app>

（6）View

register.jsp：

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Register</title>
    </head>
    <body>
        <form action="test/register.action" method="post">
            <label>姓名：<input type="text" name="name" /></label><br>
            <label>年龄：<input type="text" name="age" id="age"/></label><br>
            <input type="submit" value="注册"/>
        </form>
    </body>
    </html>

welcome.jsp：

    <%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="utf-8">
    <title>Welcome</title>
    </head>
    <body>
        Welcome Page!
    </body>
    </html>

**测试成功，一切正常。**

关于Action中IStudentService的注入问题：


这个注入是由Struts2插件提供的：struts2-spring-plugin-2.5.13.jar。

该Jar包下的struts-plugin.xml有如下设置：

    <!--  Make the Spring object factory the automatic default -->
    <constant name="struts.objectFactory" value="spring" />

同时搭配struts2-core-2.5.13.jar的default.properties的设置：

    struts.objectFactory.spring.autoWire = name

这样就完成了byName注入。


##### 5.2.2.2 Action实例由Spring创建

将Action交由Spring管理，需要在Spring的配置文件中注册：

    <!-- 注册Action，必须指定scope="prototype" -->
    <bean id="registerAction" class="tavish.bit.actions.RegisterAction" scope="prototype">
        <!-- 配置设值注入 -->
        <property name="service" ref="studentService" />
    </bean>

此时Action类中service域的名字可以任意，只要和设置注入的属性名对应即可。

    public class RegisterAction {
        private String name;
        private int age;
        
        private IStudentService service;

        // ...
    }

同时还需要更改struts2的配置文件：

    <package name="myAction" namespace="/test" extends="struts-default">
        <!-- byName自动注入，此时会自动去Spring的设置文件中找同名的bean -->
        <!-- 此时class中的值称为“伪类” -->
        <action name="register" class="registerAction">
            <result>/welcome.jsp</result>
        </action>
    </package>

**测试成功，一切正常。**

##### 5.2.2.3 当前程序存在的问题

*源代码在Spring-12-SSH-Integration-3下。*

添加一个根据id查询学生的页面及Action。

queryById.jsp：

    <form action="test/query.action" method="post">
        <label>id：<input type="text" name="id" /></label><br>
        <input type="submit" value="query"/>
    </form>

queryresult.jsp：

    查询结果：<s:property value="student" default="null" />

QueryAction.java：

    package tavish.bit.actions;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    public class QueryAction {
        private int id;
        private IStudentService service;
        
        private Student student;
        
        public int getId() {
            return id;
        }
        public void setId(int id) {
            this.id = id;
        }
        public IStudentService getService() {
            return service;
        }
        public void setService(IStudentService service) {
            this.service = service;
        }
        
        public Student getStudent() {
            return student;
        }
        public void setStudent(Student student) {
            this.student = student;
        }
        public String execute() {
            student = service.findStudentById(id);
            System.out.println(student);
            return "success";
        }
    }

Spring配置：

    <bean id="queryAction" class="tavish.bit.actions.QueryAction" scope="prototype">
        <!-- 配置设值注入 -->
        <property name="service" ref="studentService" />
    </bean>

struts2配置：

    <action name="query" class="queryAction">
        <result>/queryresult.jsp</result>
    </action>

测试查询id为3的Student，页面上输出：

    查询结果：Student [id=3, name=Tavish, age=23]

控制台输出：

    Student [id=3, name=Tavish, age=23]

**
下面根据上述代码来说明问题。
**

将Dao实现类的查询方法由get改为load。

    @Override
    public Student selectStudentById(int id) {
        //return sessionFactory.getCurrentSession().get(Student.class, id);
        return sessionFactory.getCurrentSession().load(Student.class, id);
    }

再次进行查询发生报错：

    HTTP Status 500 – Internal Server Error

原因为：

    could not initialize proxy - no Session

**
因为load方法返回的是一个代理，而非真正的Student对象。当使用输出语句对Student对象进行输出时，此时才会真正执行sql语句对Student对象进行查询。
但是此时已经没有事务了，事务是由AOP织入到Service的find方法中的，该方法一旦返回，事务就结束了。同时，在由getCurrentSession得到的Session中进行CURD操作必须要有事务，所以此时load方法无法进行sql查询，导致出错。但是为什么报错为no Session？因为getCurrentSession获取的Session对象在事务提交或回滚后就被自动关闭了，也就是说在find方法返回后，此时既没有Session也没有事务。所以load方法也无法初始化代理，即could not initialize proxy。
**

修改方案：

**
在由OpenSession得到的Session中进行CURD操作无需事务。所以我们要在Action类中通过OpenSession创建一个Session来执行这次查询。
这里我们增加一个过滤器---OpenSessionInViewFilter
**

web.xml中注册filter：

    <!-- 注册OpenSessionInViewFilter -->
    <filter>
        <filter-name>openSessionInView</filter-name>
        <filter-class>org.springframework.orm.hibernate5.support.OpenSessionInViewFilter</filter-class>
        <init-param>
            <param-name>sessionFactoryBeanName</param-name>
            <param-value>mySessionFactory</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>openSessionInView</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

**
在这里需要为这个过滤器指定初始化参数sessionFactoryBeanName，其值应为Spring配置文件中的SessionFactory这个Bean的id。如果不指定这个初始化参数，需要Spring配置文件中sessionFactory这个Bean的id为"sessionFactory"，因为这个过滤器会默认地用这个名字去加载SessionFactory。
**
**
重新发布，测试通过，使用load进行延迟加载没有问题。
**

#### 5.2.3 SSH全注解开发

*源代码在Spring-13-SSH-Annotation下。*

（1）实体类：

去掉映射文件，使用Hibernate注解。


    package tavish.bit.beans;

    import javax.persistence.Entity;
    import javax.persistence.GeneratedValue;
    import javax.persistence.Id;

    import org.hibernate.annotations.GenericGenerator;

    @Entity
    public class Student {
        @Id
        @GeneratedValue(generator="xxx")
        @GenericGenerator(name="xxx", strategy="native")
        private Integer id;
        private String name;
        private int age;

        public Student(String name, int age) {
            super();
            this.name = name;
            this.age = age;
        }

        public Student() {
            super();
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

        @Override
        public String toString() {
            return "Student [id=" + id + ", name=" + name + ", age=" + age + "]";
        }
    }

（2）组件---Action：

为Action组件添加注解，去掉struts.xml

QueryAction：

    package tavish.bit.actions;

    import org.apache.struts2.convention.annotation.Action;
    import org.apache.struts2.convention.annotation.Namespace;
    import org.apache.struts2.convention.annotation.ParentPackage;
    import org.apache.struts2.convention.annotation.Result;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    @Namespace("/test")
    @ParentPackage("struts-default")
    @Controller("queryAction")
    public class QueryAction {

        private int id;

        @Autowired
        private IStudentService service;

        private Student student;

        public int getId() {
            return id;
        }

        public void setId(int id) {
            this.id = id;
        }

        public IStudentService getService() {
            return service;
        }

        public void setService(IStudentService service) {
            this.service = service;
        }

        public Student getStudent() {
            return student;
        }

        public void setStudent(Student student) {
            this.student = student;
        }

        @Action(value = "query", results = { @Result(location = "/queryresult.jsp") })
        public String execute() {
            student = service.findStudentById(id);
            System.out.println(student);
            return "success";
        }
    }

RegisterAction：

    package tavish.bit.actions;

    import org.apache.struts2.convention.annotation.Action;
    import org.apache.struts2.convention.annotation.Namespace;
    import org.apache.struts2.convention.annotation.ParentPackage;
    import org.apache.struts2.convention.annotation.Result;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;

    import tavish.bit.beans.Student;
    import tavish.bit.service.IStudentService;

    @Namespace("/test")
    @ParentPackage("struts-default")
    @Controller("registerAction")
    public class RegisterAction {
        private String name;
        private int age;

        @Autowired
        private IStudentService service;

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

        public void setService(IStudentService service) {
            this.service = service;
        }

        @Action(value = "register", results = { @Result(location = "/welcome.jsp") })
        public String execute() {
            Student student = new Student(name, age);
            service.addStudent(student);
            return "success";
        }
    }

其中@Controller和@Autowired注解是Spring定义组件和byType注入使用的注解。

（3）组件---Dao：

    package tavish.bit.dao;

    import java.util.List;

    import org.hibernate.SessionFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Repository;

    import tavish.bit.beans.Student;

    @Repository("studentDao")
    public class StudentDaoHbnImpl implements IStudentDao {

        @Autowired // byType
        private SessionFactory sessionFactory;
        
        public void setSessionFactory(SessionFactory sessionFactory) {
            this.sessionFactory = sessionFactory;
        }

        @Override
        public void insertStudent(Student student) {
            sessionFactory.getCurrentSession().save(student);
        }

        @Override
        public void deleteStudent(Student student) {
            sessionFactory.getCurrentSession().delete(student);
        }

        @Override
        public void updateStudent(Student student) {
            sessionFactory.getCurrentSession().update(student);
        }

        @SuppressWarnings("unchecked")
        @Override
        public List<Student> selectAllStudents() {
            String hql = "from Student";
            return sessionFactory.getCurrentSession().createQuery(hql).list();
        }

        @Override
        public Student selectStudentById(int id) {
            //return sessionFactory.getCurrentSession().get(Student.class, id);
            return sessionFactory.getCurrentSession().load(Student.class, id);
        }
    }

其中@Repository和@Autowired注解是Spring定义组件和byType注入使用的注解。

（4）组件---Service：

    package tavish.bit.service;

    import java.util.List;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;
    import org.springframework.transaction.annotation.Transactional;

    import tavish.bit.beans.Student;
    import tavish.bit.dao.IStudentDao;

    @Service("studentService")
    public class StudentServiceImpl implements IStudentService {

        @Autowired
        private IStudentDao dao;

        public void setDao(IStudentDao dao) {
            this.dao = dao;
        }

        @Override
        @Transactional
        public void addStudent(Student student) {
            dao.insertStudent(student);
        }

        @Override
        @Transactional
        public void removeStudent(Student student) {
            dao.deleteStudent(student);
        }

        @Override
        @Transactional
        public void modifyStudent(Student student) {
            dao.updateStudent(student);
        }

        @Override
        @Transactional(readOnly=true)
        public List<Student> findAllStudents() {
            return dao.selectAllStudents();
        }

        @Override
        @Transactional
        public Student findStudentById(int id) {
            return dao.selectStudentById(id);
        }
    }

其中@Service和@Autowired注解是Spring定义组件和byType注入使用的注解。
@Transactional注解用于定义事务。默认值为isolation="DEFAULT" propagation="REQUIRED"。

（5）Spring配置文件：

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
            
        <!-- 注册数据源：c3p0数据源 -->
        <bean id="c3p0DataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <!-- 数据库连接四要素 -->
            <property name="driverClass" value="${jdbc.driver}" />
            <property name="jdbcUrl" value="${jdbc.url}" />
            <property name="user" value="${jdbc.user}" />
            <property name="password" value="${jdbc.password}" />
        </bean>
        
        <!-- 添加context约束，然后使用如下标签注册属性文件 -->
        <context:property-placeholder location="classpath:jdbc.properties" />
        
        <!-- 注册SessionFactory -->
        <bean id="mySessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
            <!-- 配置数据源 -->
            <property name="dataSource" ref="c3p0DataSource" />
            <!-- 配置要扫描的包 -->
            <property name="packagesToScan" value="tavish.bit.beans" />     
            <!-- 配置Hibernate属性 -->
            <property name="hibernateProperties">
                <props>
                    <!-- 配置方言 -->
                    <prop key="hibernate.dialect">org.hibernate.dialect.MySQL5Dialect</prop>
                    <!-- 配置session策略 -->
                    <prop key="hibernate.current_session_context_class">org.springframework.orm.hibernate5.SpringSessionContext</prop>
                    <!-- 配置自动建表 -->
                    <prop key="hibernate.hbm2ddl.auto">update</prop>
                    <!-- 显示SQL语句 -->
                    <prop key="hibernate.show_sql">true</prop>
                    <!-- sql语句格式化输出 -->
                    <prop key="hibernate.format_sql">true</prop>
                </props>
            </property>
        </bean>
        
        <!-- 注册事务管理器 -->
        <bean id="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager">
            <property name="sessionFactory" ref="mySessionFactory" />
        </bean> 
        
        <!-- 事务注解驱动 -->
        <tx:annotation-driven transaction-manager="transactionManager"/>
        
        <!-- 组件扫描器 -->
        <context:component-scan base-package="tavish.bit.*" />
    </beans>

在“注册SessionFactory”这个配置中，不再配置映射文件存放位置，而是配置packagesToScan，这个包下放置Hibernate使用的实体类。

**
重新发布，测试成功。
**