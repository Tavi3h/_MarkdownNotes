[TOC]

# Spring实战 第四版 -- Spring in Action 4th Edition

*使用Eclipse + Gradle构建工程，Spring版本为4.3.9，OS为Windows10。相较于书中给出的源码，这里对一些类或语句以及写法做了少许修改*。

- Part 1：**Spring核心**
    + Chapter：01-04
- Part 2：**Web中的Spring**
    + Chapter：05-09
- Part 3：**后端中的Spring**
    + Chapter：10-14
- Part 4：**Spring集成**
    + Chapter：15-21

## 第一章 Spring之旅

*以下内容代码在工程sia4e-P1_Core_Spring-C01_Springing_into_action中。*

本章主要简要介绍Spring所解决的各类问题，为其它章节奠定基础。

本章内容：

- Spring的bean容器
- 介绍Spring的核心模块
- 更为强大的Spring生态系统
- Spring的新功能

### 1.1 简化Java开发

Spring是一个开源框架，最早由Rod Johnson创建。Spring是为了解决企业级应用开发的复杂性而创建的，使用Spring可以让简单的JavaBean实现之前只有EJB才能完成的事情。但Spring不仅仅局限于服务器端开发，任何Java应用都能在简单性、可测试性和松耦合方面从Spring中获益。

虽然Spring用bean或者JavaBean来表示应用组件，**但并不意味着Spring组件必须要遵顼JavaBean规范。一个Spring组件可以是任何形式的POJO**。这里采用JavaBean的广泛定义，即POJO的同义词。

Spring可以做非常多的事情，但归根结底，支撑Spring的仅仅是少许的基本理念，所有的理念都可以追溯到Spring最根本的使命上：简化Java开发。

Spring是如何简化Java开发的？为了降低Java开发的复杂性，Spring采取了以下4种关键策略：

- 基于POJO的轻量级和最小侵入性编程；
- 通过依赖注入和面向接口实现松耦合；
- 基于切面和惯例进行声明式编程；
- 通过切面和模板减少样板式代码。

几乎Spring所做的任何事情都可以追溯到上述的一条或多条策略。

#### 1.1.1 激发POJO的潜能

Spring接力避免因自身的API而弄乱你的应用代码。Spring不会强迫你实现Spring规范的接口或继承Spring规范的类，相反，在基于Spring构建的应用中，它的类通常没有任何痕迹表明你使用了Spring。最坏的场景是，一个类或许会使用Spring注解，但它依旧是POJO。

参考下面的`HelloWorldBean`：

```java
public class HelloWorldBean {
    public String sayHello() {
        return "Hello World";
    }
}
```

可以看到，这是一个简单普通的Java类--POJO。没有任何地方表明它是一个Spring组件。Spring的非侵入编程模型意味着这个类在Spring应用和非Spring应用中都可以发挥同样的作用。

#### 1.1.2 依赖注入

依赖注入（Dependency Injection，DI）并不像它听上去那么复杂。在项目中应用DI会使得代码变得异常简单并且更容易理解和测试。

**DI功能是如何实现的**

任何一个有实际意义的应用（肯定比Hello World示例更复杂）都会由两个或者更多的类组成，这些类相互之间进行协作来完成特定的业务逻辑。按照传统的做法，每个对象负责管理与自己相互协作的对象（即它所依赖的对象）的引用，这将会导致高度耦合和难以测试的代码。

举例，有以下接口及实现类：

`Knight`接口:

```java
package sia.knights;

public interface Knight {
    void embarkOnQuest();
}
```

`DameselRescuingKnight`类：

```java
package sia.knights;

public class DamselRescuingKnight implements Knight {

    private RescueDamselQuest quest;

    public DamselRescuingKnight() {
        this.quest = new RescueDamselQuest();
    }

    @Override
    public void embarkOnQuest() {
        quest.embark();
    }
}
```

`RescueDamselQuest`类：

```java
package sia.knights;

public class RescueDamselQuest implements Quest {

    @Override
    public void embark() {
        System.out.println("Embarking on a quest to rescue the damsel.");
    }
}
```

可以看到，`DamselRescuingKnight`在它的构造函数中自行创建了`RescueDamselQuest`。这使得`DamselRescuingKnight`紧密地和`RescueDamselQuest`耦合到了一起，因此极大地限制了这个骑士执行探险的能力。

更糟糕的是，为这个`DamselRescuingKnight`编写单元测试将出奇地困难。在这样的一个测试中，你必须保证当骑士的`embarkOnQuest()`方法被调用的时候，探险的`embark()`方法也要被调用。

耦合具有两面性（two-headed beast）。一方面，紧密耦合的代码难以测试、难以复用、难以理解，并且典型地表现出“打地鼠”式的bug特性（修复一个bug，将会出现一个或者更多新的bug）。另一方面，一定程度的耦合又是必须的——完全没有耦合的代码什么也做不了。为了完成有实际意义的功能，不同的类必须以适当的方式进行交互。总而言之，**耦合是必须的，但应当被小心谨慎地管理**。

通过DI，对象的依赖关系将由系统中负责协调各对象的第三方组件在创建对象的时候进行设定。对象无需自行创建或管理它们的依赖关系，如下图所示，依赖关系将被自动注入到需要它们的对象当中去。
<center>
    ![图1.1-依赖注入.PNG](images\图1.1-依赖注入.PNG)
    **依赖注入会将所依赖的关系自动交给目标对象，而不是让对象自己去获取依赖**
</center>

示例，`BraveKnight`可以灵活地接受任何赋予他的探险任务：

`Quest`接口：

```java
package sia.knights;

public interface Quest {
    void embark();
}
```

`BraveKnight`类：

```java
package sia.knights;

public class BraveKnight implements Knight {
    private Quest quest;

    // 注入Quest
    public BraveKnight(Quest quest) {
        this.quest = quest;
    }

    @Override
    public void embarkOnQuest() {
        quest.embark();
    }
}
```

我们可以看到，不同于之前的`DamselRescuingKnight`，`BraveKnight`没有自行创建探险任务，而是在构造的时候把探险任务作为构造器参数传入。这是依赖注入的方式之一，即**构造器注入**（constructor injection）。

更重要的是，传入的探险类型是`Quest`，也就是所有探险任务都必须实现的一个接口。所以，`BraveKnight`能够响应`RescueDamselQuest`、 `SlayDragonQuest`、 `MakeRoundTableRounderQuest`等任意的`Quest`实现。

这里的要点是`BraveKnight`没有与任何特定的`Quest`实现发生耦合。对它来说，被要求挑战的探险任务只要实现了`Quest`接口，那么具体是哪种类型的探险就无关紧要了。**这就是DI所带来的最大收益——松耦合**。如果一个对象只通过接口（而不是具体实现或初始化过程）来表明依赖关系，那么这种依赖就能够在对象本身毫不知情的情况下，用不同的具体实现进行替换。

对依赖进行替换的一个最常用方法就是在测试的时候使用mock实现。我们无法充分地测试`DamselRescuingKnight`，因为它是紧耦合的；但是可以轻松地测试`BraveKnight`，只需给它一个`Quest`的mock实现即可。

使用mock框架测试`BraveKnight`：

```java
package sia.knights;

import org.junit.Test;
import static org.mockito.Mockito.*;

public class BraveKnightTest {
    
    @Test
    public void knightShouldEmbarkOnQuest() {
        // 创建mock Quest
        Quest mockQuest = mock(Quest.class);
        
        // 注入 mock Quest
        BraveKnight knight = new BraveKnight(mockQuest);
        
        knight.embarkOnQuest();
        
        verify(mockQuest, times(1)).embark();
    }
}
```

使用mock框架`Mockito`去创建一个`Quest`接口的mock实现。通过这个mock对象，就可以创建一个新的`BraveKnight`实例，并通过构造器注入这个mock Quest。当调用`embarkOnQuest()`方法时，可以要求`Mockito`框架验证`Quest`的mock实现的`embark()`方法仅仅被调用了一次。

**将Quest注入到Knight中**

现在`BraveKnight`类可以接受你传递给它的任意一种`Quest`的实现，但该怎样把特定的`Quest`实现传给它呢？假设，希望`BraveKnight`所要进行探险任务是杀死一只怪龙，那么`SlayDragonQuest`也许是挺合适的。

```java
package sia.knights;

import java.io.PrintStream;

public class SlayDragonQuest implements Quest {

    private PrintStream stream;

    public SlayDragonQuest(PrintStream stream) {
        this.stream = stream;
    }

    @Override
    public void embark() {
        stream.println("Embarking on quest to slay the dragon!");
    }
}
```

我们可以看到，`SlayDragonQuest`实现了`Quest`接口，这样它就适合注入到`BraveKnight`中去了。与其他的Java入门样例有所不同，`SlayDragonQuest`没有使用`System.out.println()`，而是在构造方法中请求一个更为通用的`PrintStream`。这里最大的问题在于，我们该如何将`SlayDragonQuest`交给`BraveKnight`呢？又如何将`PrintStream`交给`SlayDragonQuest`呢？

创建应用组件之间协作的行为通常称为装配（wiring）。Spring有多种装配bean的方式，采用XML是很常见的一种装配方式。下面一个简单的Spring配置文件：knights.xml，该配置文件将`BraveKnight`、`SlayDragonQuest`和`PrintStream`装配到了一起。

knights.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
      http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 声明BraveKnight -->
    <bean id="knight" class="sia.knights.BraveKnight">
        <!-- 通过构造函数注入SlayDragonQuest -->
        <constructor-arg ref="quest" />
    </bean>

    <!-- 声明SlayDragonQuest -->
    <bean id="quest" class="sia.knights.SlayDragonQuest">
        <!-- 通过构造函数注入System.out --> 
        <constructor-arg value="#{T(System).out}" />
    </bean>
</beans>
```

这里，`BraveKnight`和`SlayDragonQuest`被声明为Spring中的`bean`。就`BraveKnight` bean来讲，它在构造时传入了对`SlayDragonQuest` bean的引用，将其作为构造器参数。同时，`SlayDragonQuest` bean的声明使用了Spring表达式语言（Spring Expression Language），将`System.out`（这是一个`PrintStream`）传入到了`SlayDragonQuest`的构造器中。

如果XML配置不符合我们的喜好，Spring还支持使用Java来描述配置：

```java
package sia.knights.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import sia.knights.BraveKnight;
import sia.knights.Knight;
import sia.knights.Quest;
import sia.knights.SlayDragonQuest;

@Configuration
public class KnightConfig {
    
    @Bean
    public Knight knight() {
        return new BraveKnight(quest());
    }
    
    @Bean
    public Quest quest() {
        return new SlayDragonQuest(System.out);
    }
}

```

不管你使用的是基于XML的配置还是基于Java的配置，DI所带来的收益都是相同的。尽管`BraveKnight`依赖于`Quest`，但是它并不知道传递给它的是什么类型的`Quest`，也不知道这个`Quest`来自哪里。与之类似，`SlayDragonQuest`依赖于`PrintStream`，但是在编码时它并不需要知道这个`PrintStream`是什么样子的。只有Spring通过它的配置，能够了解这些组成部分是如何装配起来的。这样的话，就可以在不改变所依赖的类的情况下，修改依赖关系。

**观察它如何工作**

Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。Spring自带了多种应用上下文的实现，它们之间主要的区别仅仅在于如何加载配置。

因为knights.xml中的bean是使用XML文件进行配置的，所以选择`ClassPathXmlApplicationContext`作为应用上下文相对是比较合适的。该类加载位于应用程序类路径下的一个或多个XML配置文件。

```java
package sia.knights;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class KnightMain {
    public static void main(String[] args) {

        // 加载Spring上下文
        ApplicationContext context = new ClassPathXmlApplicationContext("knights.xml");

        // 获取knight bean
        Knight knight = context.getBean(Knight.class);

        // 使用bean
        knight.embarkOnQuest();

        ((ClassPathXmlApplicationContext) context).close();

    }
}
```

如果是基于Java的配置，需要使用`AnnotationConfigApplicationContext`：

```java
package sia.knights.test;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import sia.knights.Knight;
import sia.knights.config.KnightConfig;

public class KnightJavaConfigInjectionTest {
    
    private ApplicationContext context;

    @Before
    public void before() {
        context = new AnnotationConfigApplicationContext(KnightConfig.class);
    }

    @After
    public void after() {
        ((AnnotationConfigApplicationContext) context).close();
    }
    
    @Test
    public void annotationConfigInjectionTest() {

        // 获取knight bean
        Knight knight = context.getBean(Knight.class);

        // 使用bean
        knight.embarkOnQuest();
    }
}

```

这里的`main()`方法基于knights.xml文件创建了Spring应用上下文。随后它调用该应用上下文获取一个ID为knight的bean。得到Knight对象的引用后，只需简单调用`embarkOnQuest()`方法就可以执行所赋予的探险任务了。注意这个类完全不知道我们的英雄骑士接受哪种探险任务，而且完全没有意识到这是由`BraveKnight`来执行的。只有knights.xml文件知道哪个骑士执行哪种探险任务。

#### 1.1.3 应用切面

下面我们关注Spring简化Java开发的下一个理念：基于切面进行声明式编程。

DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成可重用的组件。

面向切面编程往往被定义为促使软件系统实现关注点的分离一项技术。系统由许多不同的组件组成，每一个组件各负责一块特定功能。除了实现自身核心的功能之外，这些组件还经常承担着额外的职责。**诸如日志、事务管理和安全这样的系统服务经常融入到自身具有核心业务逻辑的组件中去，这些系统服务通常被称为横切关注点（cross-cutting concerns）**，因为它们会跨越系统的多个组件。

如果将这些关注点分散到多个组件中去，你的代码将会带来双重的复杂性：

- 实现系统关注点功能的代码将会重复出现在多个组件中。这意味着如果你要改变这些关注点的逻辑，必须修改各个模块中的相关实现。即使你把这些关注点抽象为一个独立的模块，其他模块只是调用它的方法，但方法的调用还是会重复出现在各个模块中。
- 组件会因为那些与自身核心业务无关的代码而变得混乱。一个向地址簿增加地址条目的方法应该只关注如何添加地址，而不应该关注它是不是安全的或者是否需要支持事务。

<center>
    ![图1.2-业务对象与系统级服务结合过于紧密](images\图1.2-业务对象与系统级服务结合过于紧密.PNG)
    **在整个系统内，关注点（例如日志和安全）的调用经常散布到各个模块中，而这些关注点并不是模块的核心业务**
</center>

上图展示了这种复杂性。左边的业务对象与系统及服务结合的过于紧密。每个对象不但要知道它需要记录日志、进行安全控制和参与事务，还要亲自执行这些服务。

**AOP能够使这些服务模块化，并以声明的方式将它们应用到它们需要影响的组件中去。所造成的结果就是这些组件会具有更高的内聚性并且会更加关注自身的业务，完全不需要了解涉及系统服务所带来复杂性**。总之，AOP能够确保POJO的简单性。

如下图所示，我们可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。这是一个非常强大的理念，可以将安全、事务和日志关注点与核心业务逻辑相分离。

<center>
    ![图1.3-AOP使系统范围内的关注点覆盖在它们所影响的组件之上](images\图1.3-AOP使系统范围内的关注点覆盖在它们所影响的组件之上.PNG)
    **利用AOP，系统范围内的关注点覆盖在它们所影响的组件之上**
</center>

**AOP应用**

假设我们现在需要使用吟游诗人这个服务类来记载骑士的所有事迹：

```java
package sia.knights;

import java.io.PrintStream;

public class Minstrel {
    
    private PrintStream stream;

    public Minstrel(PrintStream stream) {
        this.stream = stream;
    }
    
    // 骑士探险之前调用
    public void singBeforeQuest() {
        stream.println("Fa la la, the knight is so brave!");
    }
    
    // 骑士探险之后调用
    public void singAfterQuest() {
        stream.println("Tee hee hee, the brave knight did embark on a quest");
    }
}
```

`Minstrel`是只有两个方法的简单类。在骑士执行每一个探险任务之前，`singBeforeQuest()`方法会被调用；在骑士完成探险任务之后，`singAfterQuest()`方法会被调用。在这两种情况下，Minstrel都会通过一个`PrintStream`类来歌颂骑士的事迹，这个类是通过构造器注入进来的。

把`Minstrel`加入你的代码中并使其运行起来，这对你来说是小事一桩。我们适当做一下调整从而让`BraveKnight`可以使用`Minstrel`。

将二者组合起来的第一次尝试：

```java
package sia.knights;

public class BraveKnight implements Knight {

    private Quest quest;
    
    private Minstrel minstrel;

    public BraveKnight(Quest quest, Minstrel minstrel) {
        this.quest = quest;
        this.minstrel = minstrel;
    }

    @Override
    public void embarkOnQuest() {
        minstrel.singBeforeQuest();
        quest.embark();
        minstrel.singAfterQuest();
    }
}
```

上述做法的确可以达到预期，现在我们要做的就是在Spring的配置文件中完成对`Minstrel` bean的声明并将其注入给`BraveKnight`。但是，管理吟游诗人真的是骑士指责范围内的工作吗？吟游诗人应该做他份内的事，根本不需要骑士命令他这么做。毕竟，用诗歌记载骑士的探险事迹，这是吟游诗人的职责。为什么骑士还需要提醒吟游诗人去做他份内的事情呢？

此外，因为骑士需要知道吟游诗人，所以就必须把吟游诗人注入到`BarveKnight`类中。这不仅使`BraveKnight`的代码复杂化了，而且还让我疑惑是否还需要一个不需要吟游诗人的骑士呢？如果`Minstrel`为`null`会发生什么呢？我是否应该引入一个空值校验逻辑来覆盖该场景？

简单的`BraveKnight`类开始变得复杂，如果你还需要应对没有吟游诗人时的场景，那代码会变得更复杂。但利用AOP，你可以声明吟游诗人必须歌颂骑士的探险事迹，而骑士本身并不用直接访问`Mistrel`的方法。

下面将`Minstrel`抽象为一个切面，我们要做的就是在Spring配置文件中声明它：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/aop 
      http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/beans 
      http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 声明BraveKnight -->
    <bean id="knight" class="sia.knights.BraveKnight">
        <!-- 通过构造函数注入SlayDragonQuest -->
        <constructor-arg ref="quest" />
    </bean>

    <!-- 声明SlayDragonQuest -->
    <bean id="quest" class="sia.knights.SlayDragonQuest">
        <!-- 通过构造函数注入System.out --> 
        <constructor-arg value="#{T(System).out}" />
    </bean>

    <!-- 声明Minstrel -->
    <bean id="minstrel" class="sia.knights.Minstrel">
        <!-- 通过构造函数注入System.out --> 
        <constructor-arg value="#{T(System).out}" />
    </bean>
    
    <!-- 配置AOP -->
    <aop:config>
        <!-- 声明切面，引用minstrel bean-->
        <aop:aspect ref="minstrel">
            <!-- 定义切点 -->
            <aop:pointcut id="embark" expression="execution(* *.embarkOnQuest(..))" />
            <!-- 声明前置通知 -->
            <aop:before pointcut-ref="embark" method="singBeforeQuest" />
            <!-- 声明后置通知 -->
            <aop:after pointcut-ref="embark" method="singAfterQuest" />
        </aop:aspect>
    </aop:config>
</beans>
```

这里使用了Spring的aop配置命名空间把`Minstrel` bean声明为一个切面。首先，需要把`Minstrel`声明为一个bean，然后在`<aop:aspect>`元素中引用该bean。为了进一步定义切面，声明（使用`<aop:before>`）在`embarkOnQuest()`方法执行前调用`Minstrel`的`singBeforeQuest()`方法。这种方式被称为前置通知（before advice）。同时声明（使用`<aop:after>`）在`embarkOnQuest()`方法执行后调用`singAfter Quest()`方法。这种方式被称为后置通知（after advice）。

在这两种方式中，`pointcut-ref`属性都引用了名字为embank的切入点。该切入点是在前边的`<pointcut>`元素中定义的，并配置`expression`属性来选择所应用的通知。表达式的语法采用的是AspectJ的切点表达式语言。

这就是我们需要做的所有的事情！通过少量的XML配置，就可以把`Minstrel`声明为一个Spring切面。如果你现在还没有完全理解，不必担心，在第4章你会看到更多的Spring AOP示例，那将会帮助你彻底弄清楚。现在我们可以从这个示例中获得两个重要的观点：

- 首先，`Minstrel`仍然是一个POJO，没有任何代码表明它要被作为一个切面使用。当我们按照上面那样进行配置后，在Spring的上下文中，`Minstrel`实际上已经变成一个切面了。
- 其次，也是最重要的，`Minstrel`可以被应用到`BraveKnight`中，而`BraveKnight`不需要显式地调用它。实际上，`BraveKnight`完全不知道`Minstrel`的存在。    

进行测试：

```java
package sia.knights.test;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import sia.knights.Knight;

public class KnightMainAOPTest {

    private ApplicationContext context;

    @Before
    public void before() {
        context = new ClassPathXmlApplicationContext("minstrel.xml");
    }

    @After
    public void after() {
        ((ClassPathXmlApplicationContext) context).close();
    }

    @Test
    public void aopTest() {

        // 获取knight bean
        Knight knight = context.getBean(Knight.class);

        // 使用bean
        knight.embarkOnQuest();

    }
}
```

控制台输出：

```text
Fa la la, the knight is so brave!
Embarking on quest to slay the dragon!
Tee hee hee, the brave knight did embark on a quest
```

#### 1.1.4 使用模板消除样板式代码

你是否写过这样的代码，当编写的时候总会感觉以前曾经这么写过？我的朋友，这不是似曾相识。这是样板式的代码（boilerplatecode）。通常为了实现通用的和简单的任务，你不得不一遍遍地重复编写这样的代码。

遗憾的是，它们中的很多是因为使用Java API而导致的样板式代码。样板式代码的一个常见范例是使用JDBC访问数据库查询数据。举个例子，如果你曾经用过JDBC，那么你或许会写出类似下面的代码。

一个简单的数据库管理器：

```java
package sia.jdbc.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class DBManager {

    private String classPath;
    private String username;
    private String password;
    private String url;

    public DBManager(String classPath, String username, String password, String url) {
        this.classPath = classPath;
        this.username = username;
        this.password = password;
        this.url = url;
        try {
            Class.forName(this.classPath);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }

    public Connection getConnection() {
        Connection conn = null;
        try {
            conn = DriverManager.getConnection(url, username, password);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return conn;
    }

    public void closeResource(Connection conn, Statement stmt, ResultSet rs) {
        try {
            if (rs != null) {
                rs.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (stmt != null) {
                try {
                    stmt.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                } finally {
                    if (conn != null) {
                        try {
                            conn.close();
                        } catch (SQLException e) {
                            e.printStackTrace();
                        }
                    }
                }
            }
        }
    }
}
```

实体类：

```java
package sia.jdbc.beans;

import java.math.BigDecimal;

public class Employee {
    
    private Long id;

    private String firstname;

    private String lastname;

    private BigDecimal salary;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getFirstname() {
        return firstname;
    }

    public void setFirstname(String firstname) {
        this.firstname = firstname;
    }

    public String getLastname() {
        return lastname;
    }

    public void setLastname(String lastname) {
        this.lastname = lastname;
    }

    public BigDecimal getSalary() {
        return salary;
    }

    public void setSalary(BigDecimal salary) {
        this.salary = salary;
    }

    @Override
    public String toString() {
        return "Employee [id=" + id + ", firstname=" + firstname + ", lastname=" + lastname + ", salary=" + salary
                + "]";
    }
}
```

创建employee表，并插入一条记录：

```sql
CREATE TABLE `employee` (
    `id` bigint(20) NOT NULL AUTO_INCREMENT,
    `firstname` varchar(20) DEFAULT NULL,
    `lastname` varchar(20) DEFAULT NULL,
    `salary` decimal(8,2) DEFAULT NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8

INSERT INTO `test`.`employee` (`firstname`, `lastname`, `salary`) VALUES ('Tavish', 'Zhao', '10000.00'); 
```

查询方法：

```java
package sia.jdbc;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import sia.jdbc.beans.Employee;
import sia.jdbc.util.DBManager;

public class QueryUsingJdbc {

    private DBManager dbManager;

    public void setDbManager(DBManager dbManager) {
        this.dbManager = dbManager;
    }

    public Employee getEmployeeById(long id, String sql) {
        Connection conn = null;
        PreparedStatement stmt = null;
        ResultSet rs = null;

        try {
            conn = dbManager.getConnection();
            stmt = conn.prepareStatement(sql);
            stmt.setLong(1, id);
            rs = stmt.executeQuery();

            Employee employee = null;
            if (rs.next()) {
                employee = new Employee();
                employee.setId(rs.getLong("id"));
                employee.setFirstname(rs.getString("firstname"));
                employee.setLastname(rs.getString("lastname"));
                employee.setSalary(rs.getBigDecimal("salary"));
            }
            return employee;
        } catch (SQLException e) {
            // ...
        } finally {
            dbManager.closeResource(conn, stmt, rs);
        }
        return null;
    }
}
```

正如你所看到的，这段JDBC代码查询数据库获得员工姓名和薪水。我打赌你很难把上面的代码逐行看完，这是因为少量查询员工的代码淹没在一堆JDBC的样板式代码中。首先你需要创建一个数据库连接，然后再创建一个语句对象，最后你才能进行查询。为了平息JDBC可能会出现的怒火，你必须捕捉`SQLException`，这是一个检查型异常，即使它抛出后你也做不了太多事情。最后，毕竟该说的也说了，该做的也做了，你不得不清理战场，关闭数据库连接、语句和结果集。同样为了平息JDBC可能会出现的怒火，你依然要捕捉`SQLException`（这个工作由DBManager完成了）。

JDBC不是产生样板式代码的唯一场景。在许多编程场景中往往都会导致类似的样板式代码，JMS、JNDI和使用REST服务通常也涉及大量的重复代码。
Spring旨在通过模板封装来消除样板式代码。Spring的JdbcTemplate使得执行数据库操作时，避免传统的JDBC样板代码成为了可能。
举个例子，使用Spring的JdbcTemplate（利用了 Java 5特性的JdbcTemplate实现）重写的`getEmployeeById()`方法仅仅关注于获取员工数据的核心逻辑，而不需要迎合JDBC API的需求。

使用JdbcTemplate：

```java
package sia.jdbc;

import java.sql.ResultSet;
import java.sql.SQLException;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

import sia.jdbc.beans.Employee;

public class QueryUsingJdbcTemplate {

    private JdbcTemplate jdbcTemplate;
    
    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public Employee getEmployeeById(long id, String sql) {
        
        return jdbcTemplate.queryForObject(sql, new RowMapper<Employee>() {

            @Override
            public Employee mapRow(ResultSet rs, int rowNum) throws SQLException {
                Employee employee = new Employee();
                employee.setId(rs.getLong("id"));
                employee.setFirstname(rs.getString("firstname"));
                employee.setLastname(rs.getString("lastname"));
                employee.setSalary(rs.getBigDecimal("salary"));
                return employee;
            }
        }, id);
    }
}
```

正如你所看到的，新版本的`getEmployeeById()`简单多了，而且仅仅关注于从数据库中查询员工。模板的`queryForObject()`方法需要一个SQL查询语句，一个`RowMapper`对象（把数据映射为一个域对象），零个或多个查询参数。`getEmployeeById()`方法再也看不到以前的JDBC样板式代码了，它们全部被封装到了模板中。

测试`QueryUsingJdbc`：

```java
package sia.jdbc.test;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import sia.jdbc.QueryUsingJdbc;
import sia.jdbc.beans.Employee;

public class QueryUsingJdbcTest {

    private ApplicationContext context;

    @Before
    public void before() {
        context = new ClassPathXmlApplicationContext("jdbc.xml");
    }

    @After
    public void after() {
        ((ClassPathXmlApplicationContext) context).close();
    }

    @Test
    public void query() {
        QueryUsingJdbc query = (QueryUsingJdbc) context.getBean("queryUsingjdbc");
        String sql = "select id, firstname, lastname, salary from employee where id = ?";
        Employee employee = query.getEmployeeById(1, sql);
        System.out.println(employee);
    }
}
```

配置文件jdbc.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
      http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 声明dbManager -->
    <bean id="dbManager" class="sia.jdbc.util.DBManager">
        <!-- 配置数据库连接四要素 -->
        <constructor-arg name="classPath" value="com.mysql.jdbc.Driver" />
        <constructor-arg name="username" value="root" />
        <constructor-arg name="password" value="mysql" />
        <constructor-arg name="url" value="jdbc:mysql://localhost:3306/test" />
    </bean>
    
    <!-- 声明queryUsingjdbc -->
    <bean id="queryUsingjdbc" class="sia.jdbc.QueryUsingJdbc">
        <!-- 注入dbManager -->
        <property name="dbManager" ref="dbManager" />   
    </bean>
</beans>
```

测试`QueryUsingJdbcTemplate`：

```java
package sia.jdbc.test;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import sia.jdbc.QueryUsingJdbcTemplate;
import sia.jdbc.beans.Employee;

public class QueryUsingJdbcTemplateTest {

    private ApplicationContext context;

    @Before
    public void before() {
        context = new ClassPathXmlApplicationContext("jdbcTemplate.xml");
    }

    @After
    public void after() {
        ((ClassPathXmlApplicationContext) context).close();
    }

    @Test
    public void query() {
        QueryUsingJdbcTemplate query = (QueryUsingJdbcTemplate) context.getBean("queryUsingJdbcTemplate");
        String sql = "select id, firstname, lastname, salary from employee where id = ?";
        Employee employee = query.getEmployeeById(1, sql);
        System.out.println(employee);
    }
}
```

配置文件jdbcTemplate.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
      http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 声明jdbcTemplate -->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <!-- 注入数据源 -->
        <property name="dataSource" ref="springDataSource" />
    </bean>

    <!-- 注册数据源：DriverManagerDataSource（Spring内置数据源） -->
    <bean id="springDataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- 数据库连接四要素 -->
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/test" />
        <property name="username" value="root" />
        <property name="password" value="mysql" />
    </bean>

    <!-- 声明queryUsingJdbcTemplate -->
    <bean id="queryUsingJdbcTemplate" class="sia.jdbc.QueryUsingJdbcTemplate">
        <!-- 注入jbdc模板 -->
        <property name="jdbcTemplate" ref="jdbcTemplate" />
    </bean>
</beans>
```

上述两种查询均可得到正确结果：

```text
Employee [id=1, firstname=Tavish, lastname=Zhao, salary=10000.00]
```

### 1.2 容纳你的bean

我们已经了解了Spring通过面向POJO编程、DI、切面和模板技术来简化Java开发中的复杂性。在这个过程中，我们简单学习了在基于XML的配置文件中如何配置bean和切面，但这些文件是如何加载的呢？它们被加载到哪里去了？让我们再了解下Spring容器，这是应用中的所有bean所驻留的地方。

在基于Spring的应用中，你的应用对象生存于Spring容器（container）中。如下图所示，Spring容器负责创建对象，装配它们，配置它们并管理它们的整个生命周期，从生存到死亡（在这里，可能就是`new`到`finalize()`）。

<center>
    ![图1.4-对象由Spring容器创建和装配并存在容器中](images\图1.4-对象由Spring容器创建和装配并存在容器中.PNG)
    **在Spring应用中，对象由Spring容器创建和装配，并存在容器中**
</center>

下一章，你将了解如何配置Spring，从而让它知道该创建、配置和组装哪些对象。但首先，最重要的是了解容纳对象的容器。理解容器将有助于理解对象是如何被管理的。

容器是Spring框架的核心。Spring容器使用DI管理构成应用的组件，它会创建相互协作的组件之间的关联。毫无疑问，这些对象更简单干净，更易于理解，更易于重用并且更易于进行单元测试。

Spring容器并不是只有一个。Spring自带了多个容器实现，可以归为两种不同的类型。bean工厂（由`org.springframework.beans.factory.BeanFactory`接口定义）是最简单的容器，提供基本的DI支持。应用上下文（由`org.springframework.context.ApplicationContext`接口定义）基于`BeanFactory`构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

虽然我们可以在bean工厂和应用上下文之间任选一种，但bean工厂对大多数应用来说往往太低级了，因此，应用上下文要比bean工厂更受欢迎。我们会把精力集中在应用上下文的使用上，不再浪费时间讨论bean工厂（实际上上下文`ApplicationContext`接口实现了`BeanFactory`接口）。

#### 1.2.1 使用应用上下文

Spring自带了多种类型的应用上下文。下面几个是我们最有可能遇到的：

- `AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。
- `AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
- `ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
- `FileSystemXmlapplicationcontext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。
- `XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。

当在第8章讨论基于Web的Spring应用时，我们会对`AnnotationConfigWebApplicationContext`和`XmlWebApplicationContext`进行更详细的讨论。现在我们先简单地使用`FileSystemXmlApplicationContext`从文件系统中加载应用上下文或者使用`ClassPathXmlApplicationContext`从类路径中加载应用上下文。

无论是从文件系统中加载应用上下文还是从类路径下装载应用上下文，将bean加载到bean工厂的过程都是相似的：

```java
// 使用FileSystemXmlApplicationContext
ApplicationContext context = new FileSystemXmlApplicationContext("c:/knight.xml");

// 使用ClassPathXmlApplicationContext
ApplicationContext context = new ClassPathXmlApplicationContext("knight.xml");
```

二者的区别在于`FileSystemXmlApplicationContext`在指定的文件系统路径下查找；而`ClassPathXmlApplicationContext`是在所有类路径（包含JAR文件）下查找。

如果想要从Java配置中加载应用上下文，那么可以使用`AnnotationConfigApplicationContext`：

```java
ApplicationContext context = new AnnotationConfigApplicationContext(Knight.class);
```

引用上下文准备就绪之后，我们就可以调用上下文的`getBean()`方法从Spring容器中获取bean了。

#### 1.2.2 bean的生命周期

在传统的Java应用中，bean的生命周期很简单。使用Java关键字new进行bean实例化，然后该bean就可以使用了。一旦该bean不再被使用，则由Java自动进行垃圾回收。

相比之下，Spring容器中的bean的生命周期就显得相对复杂多了。正确理解Spring bean的生命周期非常重要，因为你或许要利用Spring提供的扩展点来自定义bean的创建过程。下图展示了bean装载到Spring应用上下文中的一个典型的生命周期过程：

<center>
    ![图1.5-Spring中bean的生命周期](images\图1.5-Spring中bean的生命周期.PNG)
    **bean在Spring容器中从创建到销毁经历了若干阶段，每一阶段都可以针对Spring如何管理bean进行个性化定制**
</center>

对上图的详细描述：

1. Spring对bean进行实例化；
2. Spring将值和bean的引用注入到bean对应的属性中；
3. 如果bean实现了`BeanNameAware`接口，Spring将bean的ID传递给`setBeanName()`方法；
4. 如果bean实现了`BeanFactoryAware`接口，Spring将调用`setBeanFactory()`方法，将`BeanFactory`容器实例传入；
5. 如果bean实现了`ApplicationContextAware`接口，Spring将调用`setApplicationContext()`方法，将bean所在的应用上下文的引用传入进来；
6. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessBeforeInitialization()`方法；
7. 如果bean实现了`InitializingBean`接口，Spring将调用它们的`afterPropertiesSet()`方法。类似地，如果bean使用`init-method`声明了初始化方法，该方法也会被调用；
8. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessAfterInitialization()`方法；
9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
10. 如果bean实现了`DisposableBean`接口，Spring将调用它的`destroy()`接口方法。同样，如果bean使用`destroy-method`声明了销毁方法，该方法也会被调用。

我们将在第2章对bean装配进行更详细的探讨。


### 1.3 俯瞰Spring风景线

正如你所看到的，Spring框架关注于通过DI、AOP和消除样板式代码来简化企业级Java开发。即使这是Spring所能做的全部事情，那Spring也值得一用。但是，Spring实际上的功能超乎你的想象。

在Spring框架的范畴内，你会发现Spring简化Java开发的多种方式。但在Spring框架之外还存在一个构建在核心框架之上的庞大生态圈，它将Spring扩展到不同的领域，例如Web服务、REST、移动开发以及NoSQL。

#### 1.3.1 Spring模块

当我们下载Spring发布版本（这里使用4.3.9）并查看其lib目录时，会发现里面有多个JAR文件。Spring框架的发布版本包含了20个不同的模块，每个模块有3个JAR文件（二进制类库、源码以及JavaDoc的Jar文件）。

我们这里使用的库的完整Jar文件如下图所示：

<center>
    ![图1.6-Spring框架的各个模块](images\图1.6-Spring框架的各个模块.PNG)
    **Spring框架由20个不同的模块组成**
</center>

这些模块依据其所属的功能可以划分为6类不同的功能，如下图所示：

<center>
    ![图1.7-Spring模块划分](images\图1.7-Spring模块划分.PNG)
    **Spring框架由6个定义良好的模块分类组成**
</center>

总体而言，这些模块为开发企业级应用提供了所需的一切。但是你也不必将应用建立在整个Spring框架之上，你可以自由地选择适合自身应用需求的Spring模块；当Spring不能满足需求时，完全可以考虑其他选择。事实上，Spring甚至提供了与其他第三方框架和类库的集成点，这样你就不需要自己编写这样的代码了。

下面逐一浏览Spring的模块：

**Spring核心容器**

容器是Spring框架最核心的部分，它管理着Spring应用中bean的创建、配置和管理。在该模块中，包括了Spring bean工厂，它为Spring提供了DI的功能。基于bean工厂，我们还会发现有多种Spring应用上下文的实现，每一种都提供了配置Spring的不同方式。

除了bean工厂和应用上下文，该模块也提供了许多企业服务，例如Email、JNDI访问、EJB集成和调度。

所有的Spring模块都构建于核心容器之上。

**Spring的AOP模块**

在AOP模块中，Spring对面向切面编程提供了丰富的支持。这个模块是Spring应用系统中开发切面的基础。与DI一样，AOP可以帮助应用对象解耦。借助于AOP，可以将遍布系统的横切关注点（例如事务和安全）从它们所应用的对象中解耦出来。

**数据访问与集成**

使用JDBC编写代码通常会导致大量的样板式代码，例如获得数据库连接、创建语句、处理结果集到最后关闭数据库连接。Spring的JDBC和DAO（Data Access Object）模块抽象了这些样板式代码，使我们的数据库代码变得简单明了，还可以避免因为关闭数据库资源失败而引发的问题。该模块在多种数据库服务的错误信息之上构建了一个语义丰富的异常层，以后我们再也不需要解释那些隐晦专有的SQL错误信息了！

对于那些更喜欢ORM（Object-Relational Mapping）工具而不愿意直接使用JDBC的开发者，Spring提供了ORM模块。Spring的ORM模块建立在对DAO的支持之上，并为多个ORM框架提供了一种构建DAO的简便方式。Spring没有尝试去创建自己的ORM解决方案，而是对许多流行的ORM框架进行了集成，包括Hibernate、Java Persisternce API、Java Data Object和iBATIS SQL Maps。Spring的事务管理支持所有的ORM框架以及JDBC。

本模块同样包含了在JMS（Java Message Service）之上构建的Spring抽象层，它会使用消息以异步的方式与其他应用集成。从Spring 3.0开始，本模块还包含对象到XML映射的特性，它最初是Spring WebService项目的一部分。

除此之外，本模块会使用Spring AOP模块为Spring应用中的对象提供事务管理服务。

**Web与远程调用**

MVC（Model-View-Controller）模式是一种普遍被接受的构建Web应用的方法，它可以帮助用户将界面逻辑与应用逻辑分离。Java从来不缺少MVC框架，Apache的Struts、JSF、WebWork和Tapestry都是可选的最流行的MVC框架。

虽然Spring能够与多种流行的MVC框架进行集成，但它的Web和远程调用模块自带了一个强大的MVC框架，有助于在Web层提升应用的松耦合水平。

除了面向用户的Web应用，该模块还提供了多种构建与其他应用交互的远程调用方案。Spring远程调用功能集成了RMI（Remote MethodInvocation）、Hessian、Burlap、JAX-WS，同时Spring还自带了一个远程调用框架：HTTP invoker。Spring还提供了暴露和使用REST API的良好支持。

**Instrumentation**

Spring的Instrumentation模块提供了为JVM添加代理（agent）的功能。具体来讲，它为Tomcat提供了一个织入代理，能够为Tomcat传递类文件，就像这些文件是被类加载器加载的一样。

**测试**

鉴于开发者自测的重要性，Spring提供了测试模块以致力于Spring应用的测试。
通过该模块，你会发现Spring为使用JNDI、Servlet和Portlet编写单元测试提供了一系列的mock对象实现。对于集成测试，该模块为加载Spring应用上下文中的bean集合以及与Spring上下文中的bean进行交互提供了支持。

#### 1.3.2 Spring Portfolio

当谈论Spring时，其实它远远超出我们的想象。事实上，Spring远不是Spring框架所下载的那些。如果仅仅停留在核心的Spring框架层面，我们将错过Spring Portfolio所提供的巨额财富。整个Spring Portfolio包括多个构建于核心Spring框架之上的框架和类库。概括地讲，整个Spring Portfolio几乎为每一个领域的Java开发都提供了Spring编程模型。

关于这部分内容，可以参见[spring.io](https://spring.io/projects){:target=_blank}。

**Spring Web Flow**

Spring Web Flow建立于Spring MVC框架之上，它为基于流程的会话式Web应用（可以想一下购物车或者向导功能）提供了支持。

**Spring Web Service**

虽然核心的Spring框架提供了将Spring bean以声明的方式发布为WebService的功能，但是这些服务是基于一个具有争议性的架构（拙劣的契约后置模型）之上而构建的。这些服务的契约由bean的接口来决定。 Spring Web Service提供了契约优先的Web Service模型，服务的实现都是为了满足服务的契约而编写的。

**Spring Security**

安全对于许多应用都是一个非常关键的切面。利用Spring AOP，Spring Security为Spring应用提供了声明式的安全机制。

**Spring Integration**

许多企业级应用都需要与其他应用进行交互。Spring Integration提供了多种通用应用集成模式的Spring声明式风格实现。

**Spring Batch**

当我们需要对数据进行大量操作时，没有任何技术可以比批处理更胜任这种场景。如果需要开发一个批处理应用，你可以通过Spring Batch，使用Spring强大的面向POJO的编程模型。

**Spring Data**

Spring Data使得在Spring中使用任何数据库都变得非常容易。尽管关系型数据库统治企业级应用多年，但是现代化的应用正在认识到并不是所有的数据都适合放在一张表中的行和列中。一种新的数据库种类，通常被称之为NoSQL数据库，提供了使用数据的新方法，这些方法会比传统的关系型数据库更为合适。

不管你使用文档数据库，如MongoDB，图数据库，如Neo4j，还是传统的关系型数据库，Spring Data都为持久化提供了一种简单的编程模型。这包括为多种数据库类型提供了一种自动化的Repository机制，它负责为你创建Repository的实现。

**Spring Social**

社交网络是互联网领域中新兴的一种潮流，越来越多的应用正在融入社交网络网站，例如Facebook或者Twitter。如果对此感兴趣，你可以了解一下Spring Social，这是Spring的一个社交网络扩展模块。

不过，Spring Social并不仅仅是tweet和好友。尽管名字是这样，但Spring Social更多的是关注连接（connect），而不是社交（social）。它能够帮助你通过REST API连接Spring应用，其中有些Spring应用可能原本并没有任何社交方面的功能目标。

**Spring Mobile**

移动应用是另一个引人瞩目的软件开发领域。智能手机和平板设备已成为许多用户首选的客户端。Spring Mobile是Spring MVC新的扩展模块，用于支持移动Web应用开发。

**Spring for Android**

与Spring Mobile相关的是Spring Android项目。这个新项目，旨在通过Spring框架为开发基于Android设备的本地应用提供某些简单的支持。最初，这个项目提供了Spring RestTemplate的一个可以用于Android应用之中的版本。它还能与Spring Social协作，使得原生应用可以通过REST API进行社交网络的连接。

**Spring Boot**

Spring极大地简化了众多的编程任务，减少甚至消除了很多样板式代码，如果没有Spring的话，在日常工作中你不得不编写这样的样板代码。Spring Boot是一个崭新的令人兴奋的项目，它以Spring的视角，致力于简化Spring本身。
Spring Boot大量依赖于自动配置技术，它能够消除大部分（在很多场景中，甚至是全部）Spring配置。它还提供了多个Starter项目，不管你使用Maven还是Gradle，这都能减少Spring工程构建文件的大小。

### 1.4 Spring的新功能

这部分讲述Spring 3.1、3.2和4.0三个版本的新特性，略。

### 1.5 小结

>
现在，你应该对Spring的功能特性有了一个清晰的认识。Spring致力于简化企业级Java开发，促进代码的松散耦合。成功的关键在于依赖注入和AOP。
>
在本章，我们先体验了Spring的DI。DI是组装应用对象的一种方式，借助这种方式对象无需知道依赖来自何处或者依赖的实现方式。不同于自己获取依赖对象，对象会在运行期赋予它们所依赖的对象。依赖对象通常会通过接口了解所注入的对象，这样的话就能确保低耦合。
>
除了DI，我们还简单介绍了Spring对AOP的支持。AOP可以帮助应用将散落在各处的逻辑汇集于一处——切面。当Spring装配bean的时候，这些切面能够在运行期编织起来，这样就能非常有效地赋予bean新的行为。
>
依赖注入和AOP是Spring框架最核心的部分，因此只有理解了如何应用Spring最关键的功能，你才有能力使用Spring框架的其他功能。在本章，我们只是触及了Spring DI和AOP特性的皮毛。在以后的几章，我们将深入探讨DI和AOP。

## 第二章 装配bean

本章内容：

- 声明bean
- 构造器注入和setter方法注入
- 装配bean
- 控制bean的创建和销毁

在看电影的时候，你曾经在电影结束后留在位置上继续观看片尾字幕吗？一部电影需要由这么多人齐心协力才能制作出来，这真是有点令人难以置信！除了主要的参与人员——演员、编剧、导演和制片人，还有那些幕后人员——音乐师、特效制作人员和艺术指导，更不用说道具师、录音师、服装师、化妆师、特技演员、广告师、第一助理摄影师、第二助理摄影师、布景师、灯光师和伙食管理员（或许是最重要的人员）了。

现在想象一下，如果这些人彼此之间没有任何交流，你最喜爱的电影会变成什么样子？让我这么说吧，他们都出现在摄影棚中，开始各做各的事情，彼此之间互不合作。如果导演保持沉默不喊“开机”，摄影师就不会开始拍摄。或许这并没什么大不了的，因为女主角还呆在她的保姆车里，而且因为没有雇佣灯光师，一切处于黑暗之中。或许你曾经看过类似这样的电影。但是大多数电影（总之，都还是很优秀的）都是由成千上万的人一起协作来完成的，他们有着共同的目标：制作一部广受欢迎的佳作。

在这方面，一个优秀的软件与之相比并没有太大区别。任何一个成功的应用都是由多个为了实现某一个业务目标而相互协作的组件构成的。这些组件必须彼此了解，并且相互协作来完成工作。例如，在一个在线购物系统中，订单管理组件需要和产品管理组件以及信用卡认证组件协作。这些组件或许还需要与数据访问组件协作，从数据库读取数据以及把数据写入数据库。

但是，正如我们在第1章中所看到的，创建应用对象之间关联关系的传统方法（通过构造器或者查找）通常会导致结构复杂的代码，这些代码很难被复用也很难进行单元测试。如果情况不严重的话，这些对象所做的事情只是超出了它应该做的范围；而最坏的情况则是，这些对象彼此之间高度耦合，难以复用和测试。

在Spring中，对象无需自己查找或创建与其所关联的其他对象。相反，容器负责把需要相互协作的对象引用赋予各个对象。例如，一个订单管理组件需要信用卡认证组件，但它不需要自己创建信用卡认证组件。订单管理组件只需要表明自己两手空空，容器就会主动赋予它一个信用卡认证组件。

创建应用对象之间协作关系的行为通常称为装配（wiring），这也是依赖注入（DI）的本质。在本章我们将介绍使用Spring装配 bean的基础知识。因为DI是Spring的最基本要素，所以在开发基于Spring的应用时，你随时都在使用这些技术。

在Spring中装配bean有多种方式。作为本章的开始，我们先花一点时间来介绍一下配置Spring容器最常见的三种方法。

### 2.1 Spring配置的可选方案

Spring容器负责创建应用程序中的bean并通过DI来协调这些对象之间的关系。但是，作为开发人员，你需要告诉Spring要创建哪些bean并且如何将其装配在一起。当描述bean如何进行装配时，Spring具有非常大的灵活性，它提供了三种主要的装配机制：

- 在XML中进行显式配置。
- 在Java中进行显式配置。
- 隐式的bean发现机制和自动装配。

每种配置技术所提供的功能会有一些重叠，所以在特定的场景中，确定哪种技术最为合适就会变得有些困难。但是，不必紧张，在很多场景下，选择哪种方案很大程度上就是个人喜好的问题，我们可以尽可能选择自己最喜欢的方式。

Spring有多种可选方案来配置bean，这是非常棒的，但有时候你必须要在其中做出选择。

这方面，并没有唯一的正确答案。你所做出的选择必须要适合你和你的项目。而且，谁说我们只能选择其中的一种方案呢？Spring的配置风格是可以互相搭配的，所以你可以选择使用XML装配一些bean，使用Spring基于Java的配置（JavaConfig）来装配另一些bean，而将剩余的bean让Spring去自动发现。

本书的建议是**尽可能地使用自动配置的机制**。显式配置越少越好。当你必须要显式配置bean的时候（比如，有些源码不是由你来维护的，而当你需要为这些代码配置bean的时候），推荐使用类型安全并且比XML更加强大的JavaConfig。最后，只有当你想要使用便利的XML命名空间，并且在JavaConfig中没有同样的实现时，才应该使用XML。

### 2.2 自动化装配bean

*以下内容代码在工程sia4e-P1_Core_Spring-C02_Wiring_beans-01_autoconfig中。*

在本章稍后的内容中，你会看到如何借助Java和XML来进行Spring装配。尽管你会发现这些显式装配技术非常有用，但是在便利性方面，最强大的还是Spring的自动化配置。如果Spring能够进行自动化装配的话，那何苦还要显式地将这些bean装配在一起呢？

Spring从两个角度来实现自动化装配：

- 组件扫描（component scanning）：Spring会自动发现应用上下文中所创建的bean。
- 自动装配（autowiring）：Spring自动满足bean之间的依赖。

组件扫描和自动装配组合在一起就能发挥出强大的威力，它们能够将你的显式配置降低到最少。

为了阐述组件扫描和装配，我们需要创建几个bean，它们代表了一个音响系统中的组件。首先，要创建`CompactDisc`类，Spring会发现它并将其创建为一个bean。然后，会创建一个`CDPlayer`类，让Spring发现它，并将`CompactDisc` bean注入进来。

#### 2.2.1 创建可被发现的bean

CD为我们阐述DI如何运行提供了一个很好的样例。如果你不将CD插入（注入）到CD播放器中，那么CD播放器其实是没有太大用处的。所以，可以这样说，CD播放器依赖于CD才能完成它的使命。

创建`CompactDisc`接口：

```java
package soundsystem;

public interface CompactDisc {
    void play();
}
```

`CompactDisc`的具体内容并不重要，重要的是你将其定义为一个接口。作为接口，它定义了CD播放器对一盘CD所能进行的操作。它将CD播放器的任意实现与CD本身的耦合降低到了最小的程度。

创建`CompactDisc`的实现类`SgtPepper`：

```java
package soundsystem;

import org.springframework.stereotype.Component;

@Component
public class SgtPeppers implements CompactDisc {

    private String title = "Sgt. Pepper's Lonely Hearts Club Band";
    private String artist = "The Beatles";

    @Override
    public void play() {
        System.out.println("Playing " + title + " by " + artist);
    }
}
```

和`CompactDisc`接口一样，`SgtPeppers`的具体内容并不重要。你需要注意的就是·类上使用了`@Component`注解。这个简单的注解表明该类会作为组件类，并告知Spring要为这个类创建bean。没有必要显式配置`SgtPeppers` bean，因为这个类使用了`@Component`注解，所以Spring会为你把事情处理妥当。

不过，**组件扫描默认是不启用的**。我们还需要显式配置一下Spring，从而命令它去寻找带有`@Component`注解的类，并为其创建bean。

（注意，根据这个注解的文档“Configures component scanning directives for use with @{@link Configuration} classes.”，表明这个注解应该搭配`@Configuration`使用。但本例的测试没有使用`@Configuration`也通过了。但通常情况下我们还是应该在配置类上使用`@Configuration`。）

```java
package soundsystem;

import org.springframework.context.annotation.ComponentScan;

@ComponentScan
public class CDPlayerConfig {

}
```

`CDPlayerConfig`类并没有显式地声明任何bean，只不过它使用了`@ComponentScan`注解，这个注解能够在Spring中启用组件扫描。

如果没有其他配置的话，**`@ComponentScan`默认会扫描与配置类相同的包**。因为`CDPlayerConfig`类位于`soundsystem`包中，因此Spring将会**扫描这个包以及这个包下的所有子包**，查找带有`@Component`注解的类。这样的话，就能发现`CompactDisc`，并且会在Spring中自动为其创建一个bean。

使用XML来启用组件扫描的话，那么可以使用Spring context命名空间的`<context:component-scan>`元素，并在其中指定`base-package`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    
    <context:component-scan base-package="soundsystem" />
    
</beans>
```

尽管我们可以通过XML的方案来启用组件扫描，但是在后面的讨论中，我更多的还是会使用基于Java的配置。如果你更喜欢XML的话，`<context:component-scan>`元素会有与`@ComponentScan`注解相对应的属性和子元素。

为了测试组件扫描的功能，我们创建一个简单的JUnit测试，它会创建Spring上下文，并判断`CompactDisc`是不是真的创建出来了：

```java
package soundsystem;

import static org.junit.Assert.assertNotNull;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = CDPlayerConfig.class)
public class CDPlayerTest {

    @Autowired
    private CompactDisc cd;

    @Test
    public void cdShouldNotBeNull() {
        assertNotNull(cd);
    }
}
```

`CDPlayerTest`使用了Spring的`SpringJUnit4ClassRunner`，以便在测试开始的时候自动创建Spring的应用上下文。注解`@ContextConfiguration`会告诉它需要在`CDPlayerConfig`中加载配置。因为`CDPlayerConfig`类中包含了`@ComponentScan`，因此最终的应用上下文中应该包含`CompactDisc` bean。 

为了证明这一点，在测试代码中有一个`CompactDisc`类型的属性，并且这个属性带有`@Autowired`注解，以便于将`CompactDisc` bean注入到测试代码之中。最后，会有一个简单的测试方法断言cd不为`null`。如果它不为`null`的话，就意味着Spring能够发现`CompactDisc`类，自动在Spring上下文中将其创建为bean并将其注入到测试代码之中。

这个代码应该能够通过测试（当然，这个测试通过了），并以测试成功的颜色显示（在你的测试运行器中，或许会希望出现绿色）。尽管我们只用它创建了一个bean，但同样是这么少的配置能够用来发现和创建任意数量的bean。在`soundsystem`包及其子包中，所有带有@Component注解的类都会创建为bean。只添加一行`@ComponentScan`注解就能自动创建无数个bean，这种权衡还是很划算的。

#### 2.2.2 为组件扫描的bean命名

Spring应用上下文中所有的bean都会给定一个ID。在前面的例子中，尽管我们没有明确地为`SgtPeppersbean`设置ID，但Spring会根据类名为其指定一个ID。具体来讲，这个bean所给定的ID为sgtPeppers，也就是将类的简单类名的第一个字母变为小写。

如果想为这个bean设置不同的ID，你所要做的就是将期望的ID作为值传递给`@Component`注解。比如说，如果想将这个bean标识为lonelyHeartsClub，那么你需要将`SgtPeppers`类的`@Component`注解配置为如下所示：

```java
@Component("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
    // ...
}
```

还有另外一种为bean命名的方式，这种方式不使用`@Component`注解，而是使用Java依赖注入规范（Java Dependency Injection）中所提供的`@Named`注解来为bean设置ID：

```java
@Named("lonelyHeartsClub")
public class SgtPeppers implements CompactDisc {
    // ...
}
```

Spring支持将`@Named`作为`@Component`注解的替代方案。两者之间有一些细微的差异，但是在大多数场景中，它们是可以互相替换的。

#### 2.2.3 设置组件扫描的基本包

到现在为止，我们没有为`@ComponentScan`设置任何属性。这意味着，按照默认规则，它会以配置类所在的包作为基础包（basepackage）来扫描组件。但是，如果你想扫描不同的包，那该怎么办呢？或者，如果你想扫描多个基础包，那又该怎么办呢？

有一个原因会促使我们明确地设置基础包，那就是我们想要将配置类放在单独的包中，使其与其他的应用代码区分开来。如果是这样的话，那默认的基础包就不能满足要求了。

为了指定不同的基础包，我们要做的就是在`@ComponentScan`的`value`或`basePackages`属性中指明包的名称：

```java
@ComponentScan(basePackages = "soundsystem")
public class CDPlayerConfig {

}
```

根据源码，`value`和`basePackages`属性的类型是`String[]`，所以我们可以指定多个包：

```java
@ComponentScan(basePackages = {"soundsystem", "video"})
public class CDPlayerConfig {

}
```

在上面的例子中，所设置的基础包是以`String`类型逐一表示的。这样做是可以的，但这种方法是类型不安全（not type-safe）的。如果我们重构代码的话，那么所指定的基础包可能就会出现错误了。

`@ComponentScan`还提供了另外一种方法，那就是将其指定为包中所包含的类或接口，这需要使用属性`basePackageClasses`，它的类型是`Class<?>[]`：

```java
@ComponentScan(basePackageClasses = {CDPlayer.class, DVDPlayer.class})
public class CDPlayerConfig {

}
```

**为`basePackageClasses`属性所设置的数组中包含了类。这些类所在的包将会作为组件扫描的基础包。**

尽管在样例中，我为`basePackageClasses`设置的是组件类，但是你可以考虑在包中创建一个用来进行扫描的空标记接口（marker interface）。通过标记接口的方式，你依然能够保持对重构友好的接口引用，但是可以避免引用任何实际的应用程序代码，因为重构中，这些应用代码有可能会从想要扫描的包中移除掉。

在你的应用程序中，如果所有的对象都是独立的，彼此之间没有任何依赖，就像`SgtPeppers` bean这样，那么你所需要的可能就是组件扫描而已。但是，很多对象会依赖其他的对象才能完成任务。这样的话，我们就需要有一种方法能够将组件扫描得到的bean和它们的依赖装配在一起。要完成这项任务，我们需要了解一下Spring自动化配置的另外一方面内容，那就是自动装配。 

#### 2.2.4 通过为bean添加注解实现自动装配

简单来说，自动装配就是让Spring自动满足bean依赖的一种方法，在满足依赖的过程中，会在Spring应用上下文中寻找匹配某个bean需求的其他bean。为了声明要进行自动装配，我们可以借助Spring的`@Autowired`注解。

例如，在`CDPlayer`类的构造器上添加`@Autowired`注解，这表示当Spring创建`CDplayer` bean的时候会通过构造器来进行实例化并且会传入一个可设置给`CompactDisc`类型的bean。

`MeidaPlayer`接口：

```java
package soundsystem;

public interface MediaPlayer {
    void play();
}
```

`CDPlayer`类：

```java
package soundsystem;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {

    private CompactDisc cd;

    @Autowired
    public CDPlayer(CompactDisc cd) {
        this.cd = cd;
    }

    @Override
    public void play() {
        cd.play();
    }
}
```

`@Autowired`注解不仅能够用在构造器上，还能用在属性的Setter方法上。比如说，如果`CDPlayer`有一个`setCompactDisc()`方法，那么可以采用如下的注解形式进行自动装配：

```java
@Autowired
public void setCompactDisc(CompactDisc cd) {
    this.cd = cd;
}
```

在Spring初始化bean之后，它会尽可能得去满足bean的依赖，在本例中，依赖是通过带有`@Autowired`注解的方法进行声明的，也就是`setCompactDisc()`。

实际上，Setter方法并没有什么特殊之处。`@Autowired`注解可以用在类的任何方法上。假设`CDPlayer`类有一个`insertDisc()`方法，那么`@Autowired`能够像在`setCompactDisc()`上那样，发挥完全相同的作用：

```java
@Autowired
public void insertDisc(CompactDisc cd) {
    this.cd = cd;
}
```

不管是构造器、Setter方法还是其他的方法，Spring都会尝试满足方法参数上所声明的依赖。假如有且只有一个bean匹配依赖需求的话，那么这个bean将会被装配进来。

如果没有匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。为了避免异常的出现，你可以将`@Autowired`的`required`属性设置为`false`：

```java
@Autowired(required = false)
public CDPlayer(CompactDisc cd) {
    this.cd = cd;
}
```

将`required`属性设置为`false`时，Spring会尝试执行自动装配，但是如果没有匹配的bean的话，Spring将会让这个bean处于未装配的状态。但是，把`required`属性设置为`false`时，你需要谨慎对待。如果在你的代码中没有进行`null`检查的话，这个处于未装配状态的属性有可能会出现`NullPointerException`。

如果有多个bean都能满足依赖关系的话，Spring将会抛出一个异常，表明没有明确指定要选择哪个bean进行自动装配。后续章节中，我们会进一步讨论自动装配中的歧义性。

`@Autowired`是Spring特有的注解。如果你不愿意在代码中到处使用Spring的特定注解来完成自动装配任务的话，那么你可以考虑将其替换为`@Inject`：

```java
@Inject
public CDPlayer(CompactDisc cd) {
    this.cd = cd;
}
```

`@Inject`注解来源于Java依赖注入规范，该规范同时还为我们定义了`@Named`注解。在自动装配中，Spring同时支持`@Inject`和`@Autowired`。尽管`@Inject`和@Autowired之间有着一些细微的差别，但是在大多数场景下，它们都是可以互相替换的。

#### 2.2.5 验证自动装配

现在，我们已经在`CDPlayer`的构造器中添加了`@Autowired`注解，Spring将把一个可分配给`CompactDisc`类型的bean自动注入进来。为了验证这一点，让我们修改一下`CDPlayerTest`，使其能够借助`CDPlayer` bean播放CD：

```java
package soundsystem;

import static org.junit.Assert.assertEquals;
import static org.junit.Assert.assertNotNull;

import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.SystemOutRule;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = CDPlayerConfig.class)
public class CDPlayerTest {

    // 获取换行符\r\n
    private final String LINE_SEPARATOR = System.getProperty("line.separator");

    @Rule
    public final SystemOutRule log = new SystemOutRule().enableLog();

    @Autowired
    private MediaPlayer player;

    @Autowired
    private CompactDisc cd;

    @Test
    public void cdShouldNotBeNull() {
        assertNotNull(cd);
    }

    @Test
    public void play() {
        player.play();
        assertEquals("Playing Sgt. Pepper's Lonely Hearts Club Band by The Beatles" + LINE_SEPARATOR, log.getLog());
    }
}
```

现在，除了注入`CompactDisc`，我们还将`CDPlayer` bean注入到测试代码的player成员变量之中（它是更为通用的`MediaPlayer`类型）。在`play()`测试方法中，我们可以调用`CDPlayer`的`play()`方法，并断言它的行为与你的预期一致。

在测试代码中使用`System.out.println()`是稍微有点棘手的事情。因此，该样例中使用了`SystemOutRule`（原书的`StandardOutputStreamLog`类已经被标记`@deprecated`），这是来源于[System Rules库](http://stefanbirkner.github.io/system-rules/index.html){:target=_blank}的一个JUnit规则，该规则能够基于控制台的输出编写断言。在这里，我们断言`SgtPeppers.play()`方法的输出被发送到了控制台上。

### 2.3 通过Java代码装配bean

*以下内容代码在工程sia4e-P1_Core_Spring-C02_Wiring_beans-02_javaconfig中。*

尽管在很多场景下通过组件扫描和自动装配实现Spring的自动化配置是更为推荐的方式，但有时候自动化配置的方案行不通，因此需要明确配置Spring。比如说，你想要将第三方库中的组件装配到你的应用中，在这种情况下，是没有办法在它的类上添加`@Component`和`@Autowired`注解的，因此就不能使用自动化装配的方案了。

在这种情况下，你必须要采用显式装配的方式。在进行显式配置的时候，有两种可选方案：Java和XML。在这节中，我们将会学习如何使用Java配置，接下来的一节中将会继续学习Spring的XML配置。

**在进行显式配置时，JavaConfig是更好的方案，因为它更为强大、类型安全并且对重构友好**。因为它就是Java代码，就像应用程序中的其他Java代码一样。

同时，JavaConfig与其他的Java代码又有所区别，在概念上，它与应用程序中的业务逻辑和领域代码是不同的。尽管它与其他的组件一样都使用相同的语言进行表述，但JavaConfig是配置代码。这意味着它不应该包含任何业务逻辑，JavaConfig也不应该侵入到业务逻辑代码之中。尽管不是必须的，但**通常会将JavaConfig放到单独的包中**，使它与其他的应用程序逻辑分离开来，这样对于它的意图就不会产生困惑了。

#### 2.3.1 创建配置类

```java
package soundsystem;

import org.springframework.context.annotation.Configuration;

@Configuration
public class CDPlayerConfig {

}
```

创建JavaConfig类的关键在于为其添加`@Configuration`注解，`@Configuration`注解表明这个类是一个配置类，该类应该包含Spring应用上下文中如何创建bean的细节。

这里由于我们要尽量使用显式配置，所以`@ComponentScan`被移除了。

移除了`@ComponentScan`注解，此时的`CDPlayerConfig`类就没有任何作用了。如果你现在运行`CDPlayerTest`的话，测试会失败，并且会出现`NoSuchBeanDefinitionException`异常。测试期望被注入`CDPlayer`和`CompactDisc`，但是这些bean根本就没有创建。

让我们看一下如何使用JavaConfig装配`CDPlayer`和`CompactDisc`。

#### 2.3.2 声明简单的bean

要在JavaConfig中声明bean，我们需要编写一个方法，这个方法会创建所需类型的实例，然后给这个方法添加`@Bean`注解：

```java
@Bean
public CompactDisc sgtPeppers() {
    return new SgtPeppers();
}
```

`@Bean`注解会告诉Spring这个方法将会返回一个对象，该对象要注册为Spring应用上下文中的bean。**方法体中包含了最终产生bean实例的逻辑**。

**默认情况下，bean的ID与带有@Bean注解的方法名是一样的**。在本例中，bean的名字将会是sgtPeppers。如果你想为其设置成一个不同的名字的话，那么可以重命名该方法，也可以通过`name`或`value`属性指定一个不同的名字：

```java
@Bean("lonelyHeartsClubBand")
public CompactDisc sgtPeppers() {
    return new SgtPeppers();
}
```

不管你采用什么方法来为bean命名，bean声明都是非常简单的。方法体返回了一个新的`SgtPeppers`实例。这里是使用Java来进行描述的，因此我们**可以发挥Java提供的所有功能**，只要最终生成一个`CompactDisc`实例即可。比如说在一组CD中随机选择一个：

```java
@Bean
public CompactDisc randomBeatlesCD() {
    int choice = (int) Math.floor(Math.random() * 4);
    if (choice == 0) {
        return new SgtPeppers();
    } else if (choice == 1) {
        return new WhiteAlbum();
    } else if (choice == 2) {
        return new HardDaysNight();
    } else {
        return new Revolver();
    }
}
```

借助`@Bean`注解方法的形式，我们可以充分发回Java的全部威力来产生bean。

#### 2.3.3 借助JavaConfig实现注入

我们前面所声明的`CompactDisc` bean是非常简单的，它自身没有其他的依赖。但现在，我们需要声明`CDPlayer` bean，它依赖于`CompactDisc`。在JavaConfig中，要如何将它们装配在一起呢？

在`avaConfig中装配bean的最简单方式就是引用创建bean的方法。例如，下面就是一种声明`CDPlayer`的可行方案：

```java
@Bean
public CDPlayer cdPlayer() {
    return new CDPlayer(sgtPeppers());
}
```

`cdPlayer()`方法像`sgtPeppers()`方法一样，同样使用了`@Bean`注解，这表明这个方法会创建一个bean实例并将其注册到Spring应用上下文中。所创建的bean ID为cdPlayer，与方法的名字相同。

`cdPlayer()`的方法体与`sgtPeppers()`稍微有些区别。在这里并没有使用默认的构造器构建实例，而是调用了需要传入`CompactDisc`对象的构造器来创建`CDPlayer`实例。

**看起来，`CompactDisc`是通过调用`sgtPeppers()`得到的，但情况并非完全如此。因为`sgtPeppers()`方法上添加了`@Bean`注解，Spring将会拦截所有对它的调用，并确保直接返回该方法所创建的bean，而不是每次都对其进行实际的调用。**

例如，我们引入了一个其它的`CDPlayer` bean，它和之前的那个bean完全一样：

```java
@Bean
public CDPlayer cdPlayer() {
    return new CDPlayer(sgtPeppers());
}

@Bean
public CDPlayer anotherCDPlayer() {
    return new CDPlayer(sgtPeppers());
}
```

加入对`sgtPeppers()`的调用就像其他的Java方法调用一样，那么每个`CDPlayer`实例都会有一个自己特有的`SgtPeppers`实例。但是，**默认情况下Spring中的bean都是单例的**，我们并没有必要为第二个`CDPlayer` bean创建我完全相同的`SgtPeppers`实例。所以，Spring会拦截对`sgtPeppers()`的调用并确保返回的是Spring创建的bean，也就是Spring本身在调用`sgtPeppers()`时创建的`CompactDisc` bean。因此，两个`CDPlayer` bean会得到相同的`SgtPeppers`实例。

可以看到，通过调用方法来引用bean的方式有点令人困惑。其实还有一种理解起来更为简单的方式：

```java
@Bean
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    return new CDPlayer(compactDisc);
}
```

`cdPlayer()`方法请求一个`CompactDisc`作为参数。当Spring调用`cdPlayer()`创建`CDPlayer` bean的时候，它会自动装配一个`CompactDisc`到配置方法之中。然后，方法体就可以按照合适的方式来使用它。借助这种技术，`cdPlayer()`方法也能够将`CompactDisc`注入到`CDPlayer`的构造器中，而且不用明确引用`CompactDisc`的`@Bean`方法。

**通过这种方式引用其他的bean通常是最佳的选择，因为它不会要求将`CompactDisc`声明到同一个配置类之中。**在这里甚至没有要求`CompactDisc`必须要在JavaConfig中声明，实际上它可以通过组件扫描功能自动发现或者通过XML来进行配置。你可以将配置分散到多个配置类、XML文件以及自动扫描和装配bean之中，只要功能完整健全即可。不管`CompactDisc`是采用什么方式创建出来的，Spring都会将其传入到配置方法中，并用来创建`CDPlayer` bean。

另外，需要提醒的是，我们在这里使用`CDPlayer`的构造器实现了DI功能，但是我们完全可以采用其他风格的DI配置。比如说，如果你想通过Setter方法注入`CompactDisc`的话，那么代码看起来应该是这样的：

```java
public CDPlayer cdPlayer(CompactDisc compactDisc) {
    CDPlayer cdPlayer = new CDPlayer(); // 假设该类有无参构造器
    cdPlayer.setCompactDisc(compactDisc); // 以及Setter方法
    return cdPlayer;
}
```

再次强调一遍，**带有`@Bean`注解的方法可以采用任何必要的Java功能来产生bean实例**。构造器和Setter方法只是`@Bean`方法的两个简单样例（也就是说，这两种方法并不特殊）。这里所存在的可能性仅仅受到Java语言的限制。

### 2.4 通过XML装配bean

*以下内容代码在工程sia4e-P1_Core_Spring-C02_Wiring_beans-03_xmlconfig中。*

到此为止，我们已经看到了如何让Spring自动发现和装配bean，还看到了如何进行手动干预，即通过JavaConfig显式地装配bean。但是，在装配bean的时候，还有一种可选方案，尽管这种方案可能不太合乎大家的心意，但是它在Spring中已经有很长的历史了。

在Spring刚刚出现的时候，XML是描述配置的主要方式。在Spring的名义下，我们创建了无数行XML代码。在一定程度上，Spring成为了XML配置的同义词。

尽管Spring长期以来确实与XML有着关联，但现在需要明确的是，XML不再是配置Spring的唯一可选方案。Spring现在有了强大的自动化配置和基于Java的配置，XML不应该再是你的第一选择了。

不过，鉴于已经存在那么多基于XML的Spring配置，所以理解如何在Spring中使用XML还是很重要的。但是，我希望本节的内容只是用来帮助你维护已有的XML配置，在完成新的Spring工作时，希望你会使用自动化配置和JavaConfig。

#### 2.4.1 XML配置规范

在使用XML为Spring装配bean之前，我们需要创建XML约束：

Spring的XML配置文件以`<beans>`元素为根。

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

上述约束是最常用的约束，其中beans约束是最基本的约束。

这里的约束使用Eclipse的插件Spring Tools(aka Spring IDE and Spring Tool Suite)创建。

#### 2.4.2 声明一个简单的&lt;bean&gt;

要在基于XML的Sprint配置中声明一个bean，我们要在`<beans>`中声明`<bean>`元素。

例如，声明`CompactDisc` bean：

```xml
<bean class="soundsystem.SgtPeppers" />
```

这里声明了一个很简单的bean，创建这个bean的类通过`class`属性来指定的，并且要使用全限定的类名。

因为没有明确给定ID，所以这个bean将会根据全限定类名来进行命名。在本例中，bean的ID将会是“soundsystem.SgtPeppers#0”。其中，“#0”是一个计数的形式，用来区分相同类型的其他bean。如果你声明了另外一个SgtPeppers，并且没有明确进行标识，那么它自动得到的ID将会是“soundsystem.SgtPeppers#1”。

尽管自动化的bean命名方式非常方便，但如果你要稍后引用它的话，那自动产生的名字就没有多大的用处了。因此，通常来讲更好的办法是借助`id`属性，为每个bean设置一个你自己选择的名字：

```xml
<bean id="compactDisc" class="soundsystem.SgtPeppers" />
```

**减少繁琐为了减少XML中繁琐的配置，只对那些需要按名字引用的bean（比如，你需要将对它的引用注入到另外一个bean中）进行明确地命名。**

另外一个需要注意到的事情就是，在这个简单的`<bean>`声明中，我们将bean的类型以字符串的形式设置在了`class`属性中。谁能保证设置给`class`属性的值是真正的类呢？Spring的XML配置并不能从编译期的类型检查中受益。即便它所引用的是实际的类型，如果你重命名了类，会发生什么呢？

**借助IDE检查XML的合法性使用能够感知Spring功能的IDE，如Spring Tool Suite，能够在很大程度上帮助你确保Spring XML配置的合法性。**

#### 2.4.3 借助构造器注入初始化bean

在Spring XML配置中，只有一种声明bean的方式：使用`<bean>`元素并指定`class`属性。Spring会从这里获取必要的信息来创建bean。

但是，在XML中声明DI时，会有多种可选的配置方案和风格。具体到构造器注入，有两种基本的配置方案可供选择：

- `<constructor-arg>`元素
- 使用Spring 3.0所引入的`c-`命名空间

两者的区别在很大程度就是是否冗长烦琐。可以看到，`<constructor-arg>`元素比使用`c-`命名空间会更加冗长，从而导致XML更加难以读懂。另外，有些事情`<constructor-arg>`可以做到，但是使用`c-`命名空间却无法实现。

`<constructor-arg>`元素是按照顺序或根据类型进行注入的：
>
Arguments correspond to either a specific index of the constructor argument list or are supposed to be matched generically by type.
当然我们也可以在元素内使用index属性，并从0开始指定参数的索引。

**使用`<constructor-arg>`注入bean引用**

现在已经声明了`SgtPeppers` bean，并且`SgtPeppers`类实现了`CompactDisc`接口，所以实际上我们已经有了一个可以注入到`CDPlayer` bean中的bean。我们所需要做的就是在XML中声明`CDPlayer`并通过ID引用`SgtPeppers`：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer">
    <constructor-arg ref="compactDisc" />
</bean>
```

当Spring遇到这个`<bean>`元素时，它会创建一个`CDPlayer`实例。`<constructor-arg>`元素会告知Spring要将一个ID为compactDisc的bean引用传递到`CDPlayer`的构造器中。


**使用`c-`命名空间注入bean引用**

作为替代方案，我们也可以使用Spring的`c-`命名空间，这个命名空间是Spring3.0引入的，它是在XML中更为简洁地描述构造器参数的方式。要使用它的话必须要在XML顶部进行声明：

```xml
<?xml version="1.0" encoding="utf-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

在声明之后，我们就可以使用它来声明构造器参数了：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer" c:cd-ref="compactDisc" />
```

`c-`命名空间作为`<bean>`元素的一个属性出现，下图描述了这个属性名是如何组合而成的：

<center>
    ![图2.1-c-命名空间的构成](images\图2.1-c-命名空间的构成.PNG)
    **通过Spring的c-命名空间将bean引用注入到构造器参数中**
</center>

属性名以“c:”命名空间前缀开头。接下来就是要装配的构造器参数名，在此之后是“-ref”，这是一个命名的约定，它会告诉Spring，正在装配的是一个bean的引用，这个bean的名字是compactDisc，而不是字面量“compactDisc”。

>
???
在编写前面的样例时，关于c-命名空间，有一件让我感到困扰的事情就是它直接引用了构造器参数的名称。引用参数的名称看起来有些怪异，因为这需要在编译代码的时候，将调试标志（debug symbol）保存在类代码中。如果你优化构建过程，将调试标志移除掉，那么这种方式可能就无法正常执行了。

替代的方案是我们使用参数在整个参数列表中的位置信息：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer" c:_0-ref="compactDisc" />
```

这里将参数的名称替换为了“0”，也就是参数的索引。**因为XML中不允许数字作为属性的第一个字符，因此必须要添加一个下划线**。

>
???
使用索引来识别构造器参数感觉比使用名字更好一些。即便在构建的时候移除掉了调试标志，参数却会依然保持相同的顺序。

如果构造器只有一个参数，那么我们也可以根本不用标识参数：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer" c:_-ref="compactDisc" />
```

这里没有参数索引或参数名，只有一个下划线，然后用“-ref”来表示正在装配的是一个引用。

**使用`<constructor-arg>`注入字面量**

迄今为止，我们所做的DI通常指的都是类型的装配，也就是将对象的引用装配到依赖于它们的其他对象之中。而有时候，我们需要做的只是用一个字面量值来配置对象。

例如现在我们有`CompactDisc`接口的实现类`BlankDisc`：

```java
package soundsystem;

public class BlankDisc implements CompactDisc {

    private String title;
    private String artist;
    
    public BlankDisc(String title, String artist) {
        this.title = title;
        this.artist = artist;
    }

    @Override
    public void play() {
        System.out.println("Playing " + title + " by " + artist);
    }
}
```

在`SgtPeppers`中，唱片名称和艺术家的名字都是硬编码的，但是这个`CompactDisc`实现与之不同，它更加灵活。

下面将已有的`SgtPeppers`替换为这个类：

```xml
<bean id="compactDisc" class="soundsystem.BlankDisc">
    <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
    <constructor-arg value="The Beatles" />
</bean>
```

这一次我们没有使用“ref”属性来引用其他的bean，而是使用了`value`属性，通过该属性表明给定的值要以字面量的形式注入到构造器之中。

**使用`c-`命名空间注入字面量**

如果要使用`c-`命名空间，第一种方案是引用构造器参数的名字：

*原书中使用的是`c:_title`和`c:_artist`，但会报错。*

```xml
<bean id="compactDisc" class="soundsystem.BlankDisc"
    c:title="Sgt. Pepper's Lonely Hearts Club Band" c:artist="The Beatles" />
```

或者也可以使用参数索引：

```xml
<bean id="compactDisc" class="soundsystem.BlankDisc"
    c:_0="Sgt. Pepper's Lonely Hearts Club Band" c:_1="The Beatles" />
```

在装配bean引用和字面量值方面，`<constructor-arg>`和c-命名空间的功能是相同的。但是有一种情况是`<constructor-arg>`能够实现，`c-`命名空间却无法做到的。

**装配集合**

真正的CD应该包含多个音轨，每个音轨上都包含一首歌。

使用这个概念为`CompactDisc`建模，创建一个新的`BlankDisc`类：

```java
package soundsystem.collections;

import java.util.List;

import soundsystem.CompactDisc;

public class BlankDisc implements CompactDisc {

    private String title;
    private String artist;
    private List<String> tracks;

    public BlankDisc(String title, String artist, List<String> tracks) {
        this.title = title;
        this.artist = artist;
        this.tracks = tracks;
    }

    @Override
    public void play() {
        System.out.println("Playing " + title + " by " + artist);
        for (String track : tracks) {
            System.out.println("-Track: " + track);
        }
    }
}
```

此时，在声明bean的时，我们必须要提供一个音轨列表。

最简单的办法是将列表设置为`null`：

```xml
<bean id="compactDisc" class="soundsystem.collections.BlankDisc">
    <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
    <constructor-arg value="The Beatles" />
    <constructor-arg>
        <null />
    </constructor-arg>
</bean>
```

`<null/>`会将`null`传递给构造器。这并不是解决问题的好办法，但在注入期它能正常执行。当调用`play()`方法时，你会遇到`NullPointerException`异常，因此这并不是理想的方案。

更好的解决方法是提供一个磁道名称的列表。要达到这一点，我们可以有多个可选方案。首先，可以使用`<list>`元素将其声明为一个列表：

```xml
<bean id="compactDisc" class="soundsystem.collections.BlankDisc">
    <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
    <constructor-arg value="The Beatles" />
    <constructor-arg>
        <list>
            <value>Sgt. Pepper's Lonely Hearts Club Band</value>
            <value>With a Little Help from My Friends</value>
            <value>Lucy in the Sky with Diamonds</value>
            <value>Getting Better</value>
            <value>Fixing a Hole</value>
        </list>
    </constructor-arg>
</bean>
```

其中，`<list>`元素是`<constructor-arg>`的子元素，这表明一个包含值的列表将会传递到构造器中。其中，`<value>`元素用来指定列表中的每个元素。

与此类似，我们也可以用`<ref>`来代替`<value>`，实现bean引用的列表装配。

假设我们有一个`Discography`类，其构造器声明如下：

```java
public Discography(String artist, List<CompactDisc> cds) {
    // ...
} 
```

那么我们可以采用如下方式对列表进行注入：

```xml
<bean id="beatlesDiscography" class="soundsystem.Discography">
    <constructor-arg value="The Beatles" />
    <constructor-arg>
        <list>
            <ref bean="sgtPeppers" />
            <ref bean="whiteAlbum" />
            <ref bean="hardDaysNight" />
            <ref bean="revolver" />
        </list>
    </constructor-arg>
</bean>
```

同理，我们也可以使用`<set>`元素对参数类型为`Set`的参数进行装配，例如：

```xml
<bean id="compactDisc" class="soundsystem.collections.BlankDisc">
    <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
    <constructor-arg value="The Beatles" />
    <constructor-arg>
        <set>
            <value>Sgt. Pepper's Lonely Hearts Club Band</value>
            <value>With a Little Help from My Friends</value>
            <value>Lucy in the Sky with Diamonds</value>
            <value>Getting Better</value>
            <value>Fixing a Hole</value>
        </set>
    </constructor-arg>
</bean>
```

`<set>`和`<list>`元素的区别不大，其中最重要的不同在于当Spring创建要装配的集合时，所创建的是`java.util.Set`还是`java.util.List`。如果是Set的话，所有重复的值都会被忽略掉，存放顺序也不会得以保证。不过无论在哪种情况下，`<set>`或`<list>`都可以用来装配`List`、`Set`甚至数组（使用`<list>`元素）。

在装配集合方面，`<constructor-arg>`比c-命名空间的属性更有优势。目前，使用`c-`命名空间的属性无法实现装配集合的功能。

#### 2.4.4 设置属性

到目前为止，`CDPlayer`和`BlankDisc`类完全是通过构造器注入的，没有使用属性的Setter方法。接下来，我们就看一下如何使用Spring XML实现属性注入。

假设`CDPlayer`类如下所示：

```java
package soundsystem;

import org.springframework.beans.factory.annotation.Autowired;

import soundsystem.CompactDisc;
import soundsystem.MediaPlayer;

public class CDPlayer implements MediaPlayer {

    private CompactDisc compactDisc;

    public void setCompactDisc(CompactDisc compactDisc) {
        this.compactDisc = compactDisc;
    }

    public void play() {
        compactDisc.play();
    }
}
```

该选择构造器注入还是属性注入呢？**作为一个通用的规则，我倾向于对强依赖使用构造器注入，而对可选性的依赖使用属性注入**。按照这个规则，我们可以说对于`BlankDisc`来讲，唱片名称、艺术家以及磁道列表是强依赖，因此构造器注入是正确的方案。不过，对于`CDPlayer`来讲，它对`CompactDisc`是强依赖还是可选性依赖可能会有些争议。虽然我不太认同，但你可能会觉得即便没有将`CompactDisc`装入进去，`CDPlayer`依然还能具备一些有限的功能。

现在`CDPlayer`没有任何构造器（除了隐含的默认构造器），它也没有任何强依赖，因此我们可以采用如下的方式将其声明为bean：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer" />
```

现在，Spring创建这个bean是不会有任何问题的，但当这个bean调用`play()`方法时会发生`NullPointerException`异常，因为我们没有为其注入`compactDisc`属性。

通过XML配置注入属性：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer">
    <property name="compactDisc" ref="compactDisc" />
</bean>
```

`<property>`元素为属性的Setter方法所提供的功能与`<constructor-arg>`元素为构造器所提供的功能是一样的。在本例中，它引用了ID为`compactDisc`的bean（通过`ref`属性），并将其注入到`compactDisc`属性中（通过`setCompactDisc()`方法）。

Spring为`<constructor-arg>`元素提供了`c-`命名空间作为替代方案，与之类似，Spring提供了更加简洁的`p-`命名空间，作为`<property>`元素的替代方案。为了启用p-命名空间，必须要在XML文件中与其他的命名空间一起对其进行声明：

```xml
<?xml version="1.0" encoding="utf-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

使用`p-`命名空间进行注入：

```xml
<bean id="cdPlayer" class="soundsystem.CDPlayer" p:compactDisc-ref="compactDisc" />
```

`p-`命名空间中属性所遵循的命名约定与`c-`命名空间类似：

<center>
    ![图2.2-p-命名空间的构成](images\图2.2-p-命名空间的构成.PNG)
    **借助Spring的p-命名空间，将bean引用注入到属性中**
</center>

`p-`命名空间与`c-`命名空间的用法大致相同，通过带有“-ref”的名称注入bean，通过`value`属性注入字面量。`p-`命名空间同样不能用于集合的装配。

虽然`p-`命名空间没有遍历的方式来指定一个值或bean引用的列表，但是我们可以使用`util-`命名空间来简化操作。

首先声明`util-`命名空间：

```xml
<?xml version="1.0" encoding="utf-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:util="http://www.springframework.org/schema/util"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/util 
        http://www.springframework.org/schema/util/spring-util.xsd">

</beans>
```

`util-`命名空间所提供的功能之一就是`<util:list>`元素，它会创建一个列表的bean。借助`<util:list>`，我们可以将音轨列表转移到`BlankDisc` bean之外，并将其声明到单独的bean之中：

```xml
<util:list id="trackList">
    <value>Sgt. Pepper's Lonely Hearts Club Band</value>
    <value>With a Little Help from My Friends</value>
    <value>Lucy in the Sky with Diamonds</value>
    <value>Getting Better</value>
    <value>Fixing a Hole</value>
</util:list>
```

然后我们可以像使用其他bean那样，将音轨列表bean注入到`BlankDisc` bean的`tracks`属性中：

```xml
<bean id="compactDisc"
    class="soundsystem.BlankDisc"
    p:title="Sgt. Pepper's Lonely Hearts Club Band"
    p:artist="The Beatles"
    p:tracks-ref="trackList" />
```

`<util:list>`元素只是`util-`命名空间中的元素之一，下表列出了该命名空间所提供的所有元素：

元素|描述
-----|-----
&lt;util:constant&gt;|引用某个类型的public static域，并将其暴露为bean
&lt;util:list&gt;|创建一个java.util.List类型的bean，其中包含值或引用
&lt;util:set&gt;|创建一个java.util.Set类型的bean，其中包含值或引用
&lt;util:map&gt;|创建一个java.util.Map类型的bean，其中包含值或引用
&lt;util:properties&gt;|创建一个java.util.Properties类型的bean
&lt;util:property-path&gt;|引用一个bean的属性或内嵌属性，并将其暴露为bean

### 2.5 导入和混合配置

*以下内容代码在工程sia4e-P1_Core_Spring-C02_Wiring_beans-04_mixedconfig中。*

在典型的Spring应用中，通常我们可能会同时使用自动化和显式配置。即便你更喜欢通过JavaConfig实现显式配置，但有的时候XML却是最佳的方案。

在Spring中，这些配置方案都不是互斥的，我们可以将JavaConfig的组件扫描和自动装配以及XML配置混合在一起。

**关于混合配置，第一件需要了解的事情就是在自动装配时，它并不在意要装配的bean来自哪里。自动装配的时候会考虑到Spring容器中所有的bean，不管它是在JavaConfig或XML中声明的还是通过组件扫描获取到的**。

#### 2.5.1 在JavaConfig中引用XML配置

现在我们对`CDPlayerConfig`进行拆分。

第一种方案就是将`BlankDisc`从`CDPlayerConfig`拆分出来，定义到它自己的`CDConfig`类中：

```java
package soundsystem;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDConfig {

    @Bean 
    public CompactDisc sgtPeppers() {
        return new SgtPeppers();
    }
}
```

然后，我们需要有一种方式将`CDPlayerConfig`和`CDConfig`组合起来，一种方法就是在`CDPlayerConfig`中使用`@Import`注解到导入`CDConfig`：

```java
package soundsystem;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import(CDConfig.class)
public class CDPlayerConfig {
    
    @Bean
    public CDPlayer cdPlayer(CompactDisc compactDisc) {
        return new CDPlayer(compactDisc);
    }
}
```

另一种方式就是创建一个更高级别的`SoundSystemConfig`类，在这个类中使用`@Import`注解将两个配置类组合在一起：

```java
package soundsystem;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

@Configuration
@Import({ CDPlayerConfig.class, CDConfig.class })
public class SoundSystemConfig {

}
```

我们假设需要通过XML来配置`BlankDisc`：

cd-config.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="compactDisc" class="soundsystem.BlankDisc"
        c:_0="Sgt. Pepper's Lonely Hearts Club Band" c:_1="The Beatles">
        <constructor-arg>
            <list>
                <value>Sgt. Pepper's Lonely Hearts Club Band</value>
                <value>With a Little Help from My Friends</value>
                <value>Lucy in the Sky with Diamonds</value>
                <value>Getting Better</value>
                <value>Fixing a Hole</value>
            </list>
        </constructor-arg>
    </bean>
</beans>
```

现在`BlankDisc`配置在XML中，我们需要让Spring同时加载它和其它基于Java的配置。

这里在`SoundSystemConfig`中使用注解`@ImportResource`：

```xml
package soundsystem;

import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import org.springframework.context.annotation.ImportResource;

@Configuration
@Import({ CDPlayerConfig.class, CDConfig.class })
@ImportResource("classpath:cd-config.xml")
public class SoundSystemConfig {

}
```

通过以上配置，两个配置在JavaConfig中的bean以及配置在XML中的`BlankDisc`就都会被加载到Spring容器中。

#### 2.5.2 在XML配置中引用JavaConfig

现在我们假设不再将`BlankDisc`配置在XML中，而是使用JavaConfig进行配置。反过来将原来使用JavaConfig的`CDPlayer`用XML进行配置。

现在基于XML的配置如何引用一个JavaConfig类呢？

答案并不那么直观。XML的`<import>`元素只能导入其他的XML配置文件，并没有XML元素能够导入JavaConfig类。

事实上，我们可以使用`<bean>`元素将Java配置导入到XML配置中：

```xml
<?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:c="http://www.springframework.org/schema/c"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 假设CDConfig类对BlankDisc进行了配置 -->
        <bean class="soundsystem.CDConfig" />

        <!-- 配置CDPlayer -->
        <bean id="cdPlayer"
                class="soundsystem.CDPlayer"
                c:cd-ref="compactDisc" />
</beans>
```

采用上述的方式我们就在XML中引入了JavaConfig。

类似地，我们还可以创建一个更高的配置文件，这个配置文件不声明任何bean，只是负责将两个或更多的配置组合起来。例如`CDConfig`类对`BlankDisc`进行了配置，cdplayer-config.xml对`CDPlayer`进行了配置，此时我们使用一个配置文件将二者组合：

```xml
<?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 对BlankDisc进行了配置 -->
        <bean class="soundsystem.CDConfig" />

        <!-- 对CDPlayer进行了配置 -->
        <import resource="cdPlayer-config.xml" />
</beans>
```

### 2.6 小结

>
Spring框架的核心是Spring容器。容器负责管理应用中组件的生命周期，它会创建这些组件并保证它们的依赖能够得到满足，这样的话，组件才能完成预定的任务。
>
在本章中，我们看到了在Spring中装配bean的三种主要方式：自动化配置、基于Java的显式配置以及基于XML的显式配置。不管你采用什么方式，这些技术都描述了Spring应用中的组件以及这些组件之间的关系。
>
我同时建议尽可能使用自动化配置，以避免显式配置所带来的维护成本。但是，如果你确实需要显式配置Spring的话，应该优先选择基于Java的配置，它比基于XML的配置更加强大、类型安全并且易于重构。
>
在本书中的例子中，当决定如何装配组件时，我都会遵循这样的指导意见。因为依赖注入是Spring中非常重要的组成部分，所以本章中介绍的技术在本书中所有的地方都会用到。
>
基于这些基础知识，下一章将会介绍一些更为高级的bean装配技术，这些技术能够让你更加充分地发挥Spring容器的威力。

## 第三章 高级装配

本章内容：

- Spring profile
- 条件化的bean声明
- 自动装配与歧义性
- bean的作用域
- Spring表达式语言

在上一章中，我们看到了一些最为核心的bean装配技术。你可能会发现上一章学到的知识有很大的用处。但是，bean装配所涉及的领域并不仅仅局限于上一章所学习到的内容。Spring提供了多种技巧，借助它们可以实现更为高级的bean装配功能。在本章中，我们将会深入介绍一些这样的高级技术。

### 3.1 环境与profile

*以下内容代码在工程sia4e-P1_Core_Spring-C03_Advanced_wiring-01_profiles中。*

在开发软件的时候，有一个很大的挑战就是将应用程序从一个环境迁移到另外一个环境。开发阶段中，某些环境相关做法可能并不适合迁移到生产环境中，甚至即便迁移过去也无法正常工作。数据库配置、加密算法以及与外部系统的集成是跨环境部署时会发生变化的几个典型例子。

例如，考虑数据库的配置。在开发环境中，我们可能会使用嵌入式数据库，并预先加载测试数据：

```java
@Bean(destroyMethod = "shutdown")
public DataSource dataSource() {
    return new EmbeddedDatabaseBuilder()
    .addScript("classpath:schema.sql")
    .addScript("classpath:test-data.sql")
    .build();
}
```

这会创建一个类型为`javax.sql.DataSource`的bean。使用`EmbeddedDatabaseBuilder`会搭建一个内嵌的Hypersonic数据库，它的schema定义在schema.sql中，测试数据则是通过test-data.sql加载的。

当你在开发环境中运行集成测试或者启动应用进行手动测试的时候，这个`DataSource`是很有用的。每次启动它的时候，都能让数据库处于一个给定的状态。

但上述做法对于生产环境来说是一个糟糕的选择，在生产环境中我们可能会希望使用JNDI从容器中获取一个`DataSource`：

```java
@Bean
public DataSource dataSource() {
    JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
    jndiObjectFactoryBean.setJndiName("jdbc/myDS");
    jndiObjectFactoryBean.setResourceRef(true);
    jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
    return (DataSource) jndiObjectFactoryBean.getObject();
}
```

通过JNDI获取`DataSoruce`能够让容器决定该如何创建这个`DataSource`，甚至包括切换为容器管理的连接池。即便如此，JNDI管理的`DataSource`更加适合于生产环境，对于简单的继承和开发测试环境来说这回带来不必要的复杂性。

同时，在QA环境中，我们也可以选择完全不同的`DataSource`配置，例如使用DBCP连接池：

```java
@Bean(destroyMethod="close")
public DataSource dataSource() {
    BasicDataSource dataSource = new BasicDataSource();
    dataSource.setUrl("jdbc:h2:tcp://dbserver/~/test");
    dataSource.setDriverClassName("org.h2.Driver");
    dataSource.setUsername("sa");
    dataSource.setPassword("password");
    dataSource.setInitialSize(20);
    dataSource.setMaxActive(30);
    return dataSource;
}
```

显然，这里展现的三个版本的`dataSource()`方法互不相同。虽然它们都会生成一个类型为`javax.sql.DataSource`的bean，但它们的相似点也仅限于此了。每个方法都使用了完全不同的策略来生成`DataSource` bean。

这是一个很好的例子，它表现了在不同的环境中某个bean会有所不同。我们必须要有一种方法来配置`DataSource`，使其在每种环境下都会选择最为合适的配置。

其中一种方式就是在单独的配置类（或XML文件）中配置每个bean，然后在构建阶段（可能会使用Maven的profiles）确定要将哪一个配置编译到可部署的应用中。这种方式的问题在于要为每种环境重新构建应用。当从开发阶段迁移到QA阶段时，重新构建也许算不上什么大问题。但是，从QA阶段迁移到生产阶段时，重新构建可能会引入bug并且会在QA团队的成员中带来不安的情绪。

值得庆幸的是，Spring所提供的解决方案并不需要重新构建。

#### 3.1.1 配置profile bean

Spring为环境相关的bean所提供的解决方案其实与构建时的方案没有太大的差别。当然，在这个过程中需要根据环境决定该创建哪个bean和不创建哪个bean。**不过Spring并不是在构建的时候做出这样的决策，而是等到运行时再来确定。这样的结果就是同一个部署单元（可能会是WAR文件）能够适用于所有的环境，没有必要进行重新构建。**

在3.1版本中，Spring引入了bean profile功能。要使用profile，首先要将所有不同的bean定义整理到一个或多个profile中，在将应用部署到每个环境时要确保对应的profile处于active状态。

```java
package com.myapp;

import javax.sql.DataSource;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;

@Configuration
@Profile("dev")
public class DevlopmentProfileConfig {
    @Bean(destroyMethod = "shutdown")
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .addScript("classpath:schema.sql")
                .addScript("classpath:test-data.sql")
                .build();
    }
}
```

**注意`@Profile`注解引用在了类级别上，它会告诉Spring这个配置类中的bean只有在dev profile激活时才会创建，如果dev profile没有激活的话，那么带有`@Bean`注解的方法都会被忽略。**

同时，我们还需要一个引用于生产环境的配置：

```java
package com.myapp;

import javax.sql.DataSource;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.jndi.JndiObjectFactoryBean;

@Configuration
@Profile("prod")
public class ProductionProfileConfig {
    @Bean
    public DataSource dataSource() {
        JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
        jndiObjectFactoryBean.setJndiName("jdbc/myDS");
        jndiObjectFactoryBean.setResourceRef(true);
        jndiObjectFactoryBean.setProxyInterface(DataSource.class);
        return (DataSource) jndiObjectFactoryBean.getObject();
    }
}
```

在本例中，只有prod profile激活的时候，才会创建对应的bean。

在Spring3.1中，`@Profile`注解只能用在类级别，但从3.2开始，我们可以在方法级别上使用`@Profile`，这样就可以将上述两个bean的声明放到一个配置中：

```java
package com.myapp;

import javax.sql.DataSource;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Profile;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseBuilder;
import org.springframework.jdbc.datasource.embedded.EmbeddedDatabaseType;
import org.springframework.jndi.JndiObjectFactoryBean;

@Configuration
public class DataSourceConfig {

    @Bean(destroyMethod = "shutdown")
    @Profile("dev")
    public DataSource embeddedDataSource() {
        return new EmbeddedDatabaseBuilder()
                .setType(EmbeddedDatabaseType.H2)
                .addScript("classpath:schema.sql")
                .addScript("classpath:test-data.sql")
                .build();
    }

    @Bean
    @Profile("prod")
    public DataSource jndiDataSource() {
        JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
        jndiObjectFactoryBean.setJndiName("jdbc/myDS");
        jndiObjectFactoryBean.setResourceRef(true);
        jndiObjectFactoryBean.setProxyInterface(DataSource.class);
        return (DataSource) jndiObjectFactoryBean.getObject();
    }
}
```

尽管每个`DataSource` bean都被声明在一个profile中吗，并且只有当规定的profile激活时，相应的bean才会被创建。但是可能会有其它bean并没有声明在一个给定的profile范围内。**没有指定profile的bean始终都会被创建，与此时激活哪个profile没有关系。**

**在XML中配置profile**

我们也可以通过`<beans>`元素的`profile`属性在XML中进行配置，例如在XML中定义适用于开发阶段的嵌入式数据库：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xsi:schemaLocation="
        http://www.springframework.org/schema/jdbc
        http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd"
        profile="dev">

    <jdbc:embedded-database id="dataSource">
        <jdbc:script location="classpath:schema.sql" />
        <jdbc:script location="classpath:test-data.sql" />
    </jdbc:embedded-database>
</beans>
```

与之类似，我们也可以将`profile`设置为prod，创建适用于生产环境的从JNDI获取的`DataSource` bean。同样，可以创建基于连接池定义的`DataSource` bean，将其放在另外一个XML文件中，并标注为qa profile。所有的配置文件都会放到部署单元之中（如WAR文件），但是只有profile属性与当前激活profile相匹配的配置文件才会被用到。 

我们还可以在根`<beans>`元素中嵌套定义`<beans>`元素，而不是为每个环境都创建一个profile XML文件。这能够将所有的profile bean定义放到同一个XML文件中，如下所示：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:jee="http://www.springframework.org/schema/jee"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="
        http://www.springframework.org/schema/jee
        http://www.springframework.org/schema/jee/spring-jee.xsd
        http://www.springframework.org/schema/jdbc
        http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 开发阶段 -->
    <beans profile="dev">
        <jdbc:embedded-database id="dataSource">
            <jdbc:script location="classpath:schema.sql" />
            <jdbc:script location="classpath:test-data.sql" />
        </jdbc:embedded-database>
    </beans>

    <!-- qa阶段 -->
    <beans profile="qa">
        <bean id="dataSource"
            class="org.apache.commons.dbcp.BasicDataSource"
            destroy-method="close"
            p:url="jdbc:h2:tcp://dbserver/~/test"
            p:driverClassName="org.h2.Driver"
            p:username="sa"
            p:password="password"
            p:initialSize="20"
            p:maxActive="30" />
    </beans>

    <!-- 生产阶段 -->
    <beans profile="prod">
        <jee:jndi-lookup id="dataSource"
            jndi-name="jdbc/myDatabase"
            resource-ref="true"
            proxy-interface="javax.sql.DataSource" />
    </beans>

</beans>
```

除了所有的bean定义到了同一个XML文件之中，这种配置方式与定义在单独的XML文件中的实际效果是一样的。这里有三个bean，类型都是`javax.sql.DataSource`，并且ID都是`dataSource`。但是在运行时，只会创建一个bean，这取决于处于激活状态的是哪个profile。

#### 3.1.2 激活profile

Spring在确定哪个profile处于激活状态时，需要依赖两个独立的属性：

- `spring.profiles.active`
- `spring.profiles.default`
 
如果设置了`spring.profiles.active`属性的话，那么它的值就会用来确定哪个profile是激活的。但如果没有设置`spring.profiles.active`属性的话，那Spring将会查找`spring.profiles.default`的值。如果`spring.profiles.active`和`spring.profiles.default`均没有设置的话，那就没有激活的profile，因此只会创建那些没有定义在profile中的bean。

有多种方式来设置这两个属性：

- 作为`DispatcherServlet`的初始化参数
- 作为Web应用的上下文参数
- 作为JNDI条目
- 作为环境变量
- 作为JVM的系统属性
- 在集成测试类上使用`@ActiveProfiles`注解

示例，在web.xml中配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee 
    http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
    id="WebApp_ID" version="3.1">
    <display-name>myApp</display-name>

    <!-- 设置Spring配置文件的位置 -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/spring/root-context.xml</param-value>
    </context-param>

    <!-- 为上下文设置默认profile -->
    <context-param>
        <param-name>spring.profiles.default</param-name>
        <param-value>dev</param-value>
    </context-param>

    <!-- 设置启动web容器时加载Spring容器 -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- 配置SpringMVC -->
    <servlet>
        <servlet-name>appServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 为Servlet设置默认的profile -->
        <init-param>
            <param-name>spring.profiles.default</param-name>
            <param-value>dev</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>appServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

当应用程序部署到QA、生产或其他环境之中时，负责部署的人根据情况使用系统属性、环境变量或JNDI设置`spring.profiles.active`即可。当设置`spring.profiles.active`以后，至于`spring.profiles.default`置成什么值就已经无所谓了；系统会优先使用`spring.profiles.active`中所设置的profile。

在`spring.profiles.active`和`spring.profiles.default`中，profile使用的都是复数形式。这意味着你可以同时激活多个profile，这可以通过列出多个profile名称，并以逗号分隔来实现。当然，同时启用dev和prod profile可能也没有太大的意义，不过你可以同时设置多个彼此不相关的profile。

**使用profile进行测试**

当运行集成测试时，通常会希望采用与生产环境（或者是生产环境的部分子集）相同的配置进行测试。但是，如果配置中的bean定义在了profile中，那么在运行测试时，我们就需要有一种方式来启用合适的profile。Spring提供了`@ActiveProfiles`注解，我们可以使用它来指定运行测试时要激活哪个profile。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = { PersistenceTestConfig.class } )
@ActiveProfiles("dev")
public class PersistenceTest {
    // ...
}
```

在条件化创建bean方面，Spring的profile机制是一种很棒的方法，这里的条件要基于哪个profile处于激活状态来判断。Spring 4.0中提供了一种更为通用的机制来实现条件化的bean定义，在这种机制之中，条件完全由你来确定。

### 3.2 条件化的bean

*以下内容代码在工程sia4e-P1_Core_Spring-C03_Advanced_wiring-02_conditional中。*

假设你希望一个或多个bean只有在应用的类路径下包含特定的库时才创建。或者我们希望某个bean只有当另外某个特定的bean也声明了之后才会创建。我们还可能要求只有某个特定的环境变量设置之后，才会创建某个bean。

**在Spring 4之前，很难实现这种级别的条件化配置，但是Spring 4引入了一个新的`@Conditional`注解，它可以用到带有`@Bean`注解的方法上。如果给定的条件计算结果为`true`，就会创建这个bean，否则的话，这个bean会被忽略。**

例如，假设有一个名为`MagicBean`的类，我们希望只有设置了magic环境属性的时候，Spring才会实例化这个类。如果环境中没有这个属性，那么`MagicBean`将会被忽略：

`MagicBean`类：

```java
package com.habuma.restfun;

public class MagicBean {

}
```

`MagicConfig`类：

```java
package com.habuma.restfun;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Conditional;
import org.springframework.context.annotation.Configuration;


@Configuration
public class MagicConfig {
    
    @Bean
    @Conditional(MagicExistsCondition.class)
    public MagicBean magicBean() {
        return new MagicBean();
    }
}
```

这里使用`@Conditional`注解并指定了一个类，这个类即为我们的条件。

`MagicExistsCondition`类：

```java
package com.habuma.restfun;

import org.springframework.context.annotation.Condition;
import org.springframework.context.annotation.ConditionContext;
import org.springframework.core.type.AnnotatedTypeMetadata;

public class MagicExistsCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        // 检查环境中是否有名为magic的属性
        return context.getEnvironment().containsProperty("magic");
    }
}
```

这个类实现了`Condition`接口。`matches()`方法就是我们条件的具体逻辑。如果这个方法返回`true`，那么就会创建上面的`MagicBean`，反之则不会创建。

`matches()`方法很简单但功能强大。它通过给定的`ConditionContext`对象进而得到`Environment`对象，并使用这个对象检查环境中是否存在名为magic的环境属性。在本例中，属性的值是什么无所谓，只要属性存在即可满足要求。

上述`MagicExistsCondition`中只是使用了`ConditionContext`得到的`Environment`，但`Condition`实现的考量因素可能会比这更多。`matches()`方法会得到`ConditionContext`和`AnnotatedTypeMetadata`对象用来做出决策。

**对`ConditionContext`和`AnnotatedTypeMetadata`的说明**

`ConditionContext`接口：

```java
// javadoc omitted
public interface ConditionContext {

    BeanDefinitionRegistry getRegistry();

    ConfigurableListableBeanFactory getBeanFactory();

    Environment getEnvironment();

    ResourceLoader getResourceLoader();

    ClassLoader getClassLoader();

}
```

通过这个接口，我们可以做到以下几点：

- 借助`getRegistry()`返回的`BeanDefinitionRegistry`检查bean定义；
- 借助`getBeanFactory()`返回的`ConfigurableListableBeanFactory`检查bean是否存在，甚至探查bean的属性；
- 借助`getEnvironment()`返回的`Environment`检查环境变量是否存在以及它的值是什么；
- 读取并探查`getResourceLoader()`返回的`ResourceLoader`所加载的资源；
- 借助`getClassLoader()`返回的`ClassLoader`加载并检查类是否存在。

`AnnotatedTypeMetadata`则能够让我们检查带有@Bean注解的方法上还有什么其他的注解。

`AnnotatedTypeMetadata`接口：

```java
// javadoc omitted
public interface AnnotatedTypeMetadata {

    boolean isAnnotated(String annotationName);

    Map<String, Object> getAnnotationAttributes(String annotationName);

    Map<String, Object> getAnnotationAttributes(String annotationName, boolean classValuesAsString);

    MultiValueMap<String, Object> getAllAnnotationAttributes(String annotationName);

    MultiValueMap<String, Object> getAllAnnotationAttributes(String annotationName, boolean classValuesAsString);

}
```

借助`isAnnotated()`方法，我们可以判断带有`@Bean`注解的方法是不是还有其他特定的注解。借助其它的方法，我们能够检查`@Bean`注解的方法上其它注解的属性。

从Spring4开始，`@Profile`注解进行了重构，使其基于`@Conditional`和`Condition`实现：

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Conditional(ProfileCondition.class)
public @interface Profile {

    String[] value();

}
```

`@Profile`本身也使用了`@Conditional`注解，并且引用`ProfileCondition`作为`Condition`实现。如下所示，`ProfileCondition`实现了`Condition`接口，并且在做出决策的过程中，考虑到了`ConditionContext`和`AnnotatedTypeMetadata`中的多个因素。

`ProfileCondition`类：

```java
class ProfileCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        if (context.getEnvironment() != null) {
            MultiValueMap<String, Object> attrs = metadata.getAllAnnotationAttributes(Profile.class.getName());
            if (attrs != null) {
                for (Object value : attrs.get("value")) {
                    if (context.getEnvironment().acceptsProfiles(((String[]) value))) {
                        return true;
                    }
                }
                return false;
            }
        }
        return true;
    }

}
```

`ProfileCondition`通过`AnnotatedTypeMetadata`得到了用于`@Profile`注解的所有属性。借助该信息，它会明确地检查`value`属性，该属性包含了bean的profile名称。然后，它根据通过`ConditionContext`得到的`Environment`来检查（借助`acceptsProfiles()`方法）该profile是否处于激活状态。

### 3.3 处理自动装配的歧义性

*以下内容代码在工程sia4e-P1_Core_Spring-C03_Advanced_wiring-03_ambiguity中。*

我们了解了如何使用自动装配让Spring完全负责将bean引用注入到构造参数和属性中。自动装配能够提供很大的帮助，因为它会减少装配应用程序组件时所需要的显式配置的数量。

不过，仅有一个bean匹配所需的结果时，自动装配才是有效的。如果不仅有一个bean能够匹配结果的话，这种歧义性会阻碍Spring自动装配属性、构造器参数或方法参数。

例如我们使用`@Autowired`注解标注`setDessert()`方法：

```java
@Autowired
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

本例中，`Deesert`是个接口并有三个实现类：

```java
package com.desserteater;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public interface Dessert {

}

`Cake`类：

```java
package com.desserteater;

import org.springframework.stereotype.Component;

@Component
public class Cake implements Dessert {

}
```

`Cookies`类：

```java
package com.desserteater;

import org.springframework.stereotype.Component;

@Component
public class Cookies implements Dessert {

}
```

`IceCream`类：

```java
package com.desserteater;

import org.springframework.stereotype.Component;

@Component
public class IceCream implements Dessert {

}
```

因为这三个实现类均使用了`@Component`注解，所以在组件扫描时Spring能够发现它们并将其创建为Spring应用上下文里的bean。但当Spring视图自动装配`setDessert()`中的参数时，它并没有唯一、无歧义的可选值。在从多种甜点中做出选择时，尽管大多数人并不会有什么困难，但是Spring却无法做出选择。Spring此时别无他法，只好宣告失败并抛出异常。更精确地讲，Spring会抛出`NoUniqueBeanDefinitionException`异常。

当确实发生歧义性的时候，Spring提供了多种可选方案来解决这样的问题。你可以将可选bean中的某一个设为首选（primary）的bean，或者使用限定符（qualifier）来帮助Spring将可选的bean的范围缩小到只有一个bean。

#### 3.3.1 标示首选的bean

在声明bean的时候，通过将其中一个可选的bean设置为首选（primary）bean能够避免自动装配时的歧义性。当遇到歧义性的时候，Spring将会使用首选的bean，而不是其他可选的bean。

例如将`IceCream`类通过`@Primary`标记为首选的bean：

```java
package com.desserteater;

import org.springframework.context.annotation.Primary;
import org.springframework.stereotype.Component;

@Component
@Primary
public class IceCream implements Dessert {

}
```

或者，如果使用JavaConfig，则应该如下配置：

```java
@Bean
@Primary
public Dessert iceCream() {
    return new IceCream();
}
```

如果使用XML配置的画，可以如下配置：

```xml
<bean id="iceCream" class="com.desserteater.IceCream" primary="true" />
```

但是假如我们标示了多个首选bean，那么此时实际上也就没有首选bean了，因为Spring无法在多个首选bean中做出选择。

#### 3.3.2 限定自动装配的bean

设置首选bean的局限性在于`@Primary`无法将可选方案的范围限定到唯一一个无歧义性的选项中。它只能标示一个优先的可选方案。当首选bean的数量超过一个时，我们并没有其他的方法进一步缩小可选范围。

与之相反，Spring的限定符能够在所有可选的bean上进行缩小范围的操作，最终能够达到只有一个bean满足所规定的限制条件。如果将所有的限定符都用上后依然存在歧义性，那么你可以继续使用更多的限定符来缩小选择范围。

`@Qualifier`注解是使用限定符的主要方式。它可以与`@Autowired`和`@Inject`协同使用，在注入的时候指定想要注入进去的是哪个bean。例如我们要保证将`IceCream`
注入到`setDessert()`之中：

```java
@Autowired
@Qualifier("iceCream")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

这是使用限定符的最简单的例子。为`@Qualifier`注解所设置的参数就是想要注入的bean的ID。所有使用`@Component`注解声明的类都会创建为bean，并且bean的ID为首字母变为小写的类名。因此，`@Qualifier("iceCream")`指向的是组件扫描时所创建的bean，并且这个bean是`IceCream`类的实例。

更准确地讲，`@Qualifier("iceCream")`所引用的bean要具有`String`类型的“iceCream”作为限定符。如果没有指定其他的限定符的话，所有的bean都会给定一个默认的限定符，这个限定符与bean的ID相同。因此，框架会将具有“iceCream”限定符的bean注入到`setDessert()`方法中。这恰巧就是ID为“iceCream”的bean，它是`IceCream`类在组件扫描的时候创建的。

这里的问题在于`setDessert()`方法上所指定的限定符与要注入的bean的名称是紧耦合的。对类名称的任意改动都会导致限定符失效。

我们可以为bean设置自己的限定符，而不是依赖于将bean ID作为限定符。在这里所需要做的就是在bean声明上添加`@Qualifier`注解。例如，它可以与`@Component`组合使用：

```java
package com.desserteater;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
@Qualifier("cold")
public class IceCream implements Dessert {

}
```

在这种情况下，cold限定符分配给了`IceCream` bean。因为它没有耦合类名，因此你可以随意重构`IceCream`的类名，而不必担心会破坏自动装配。在注入的地方，只要引用cold限定符就可以了：

```java
@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

当使用自定义的`@Qualifier`值时，最佳实践是为bean选择特征性或描述性的术语，而不是使用随意的名字。

现在假如我们引入一个新的类，`Popsicle`，这个类也具有`@Qualifier("cold")`：

```java
package com.desserteater;

import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Component;

@Component
@Qualifier("cold")
public class Popsicle implements Dessert {
    
}
```

此时我们有两个太有限定符“cold”的甜点。在自动装配时会再次遇到歧义性问题。

可能的解决方案就是在注入点和bean定义的地方同时再添加另外的`@Qualifier`注解，例如：

```java
@Component
@Qualifier("cold")
@Qualifier("creamy")
public class IceCream implements Dessert {

}
```

这样在注入点我们可能会以如下方法：

```java
@Autowired
@Qualifier("cold")
@Qualifier("creamy")
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

**事实上，上述的解决方案并不可行！因为Java不允许在同一个条目上重复出现相同类型的多个注解。**

那么这里所要做的就是创建一个新注解`@Cold`，它本身被`@Qualifier`标注：

```java
package com.desserteater.annotations;

import static java.lang.annotation.ElementType.FIELD;
import static java.lang.annotation.ElementType.METHOD;
import static java.lang.annotation.ElementType.PARAMETER;
import static java.lang.annotation.ElementType.TYPE;
import static java.lang.annotation.ElementType.ANNOTATION_TYPE;
import static java.lang.annotation.RetentionPolicy.RUNTIME;

import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import org.springframework.beans.factory.annotation.Qualifier;

@Retention(RUNTIME)
@Target({ TYPE, FIELD, METHOD, PARAMETER, ANNOTATION_TYPE })
@Qualifier
public @interface Cold {

}
```

同样地，我们创建`@Creamy`，`@Crispy`，`@Fruity`。

由于这些注解在定义时添加了`@Qualifier`注解，它们就具有了`@Qualifier`注解的特性。它们本身实际上就成为了限定符注解。

现在我们可以使用自定义的注解来区分`IceCream`和`Popsicle`了：

```java
package com.desserteater;

import org.springframework.stereotype.Component;

import com.desserteater.annotations.Cold;
import com.desserteater.annotations.Creamy;

@Component
@Cold
@Creamy
public class IceCream implements Dessert {

}
```

```java
package com.desserteater;

import org.springframework.stereotype.Component;

import com.desserteater.annotations.Cold;
import com.desserteater.annotations.Fruity;

@Component
@Cold
@Fruity
public class Popsicle implements Dessert {
    
}
```

最终在注入点，我们使用必要的限定符注解进行组合，从而将可选范围缩小到只有一个bean满足要求：

```java
@Autowired
@Cold
@Fruity
public void setDessert(Dessert dessert) {
    this.dessert = dessert;
}
```

通过声明自定义的限定符注解，我们可以同时使用多个限定符，不会再有Java编译器的限制或错误。与此同时，相对于使用原始的`@Qualifier`并借助`String`类型来指定限定符，自定义的注解也更为类型安全。

*事实上为bean指定名字并不需要特意使用`@Qualifier`注解。*

- autoconfig中可以在`@Component`注解上直接命名。
- javaconfig中可以在`@Bean`注解上命名。
- xmlconfig中可以在bean元素中指定id属性。
- mixedconfig中多种配置方法可以自由搭配。

`@Qualifier`也可以用在方法参数上，例如：

```java
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public StoreService getStoreService(@Qualifier("prototypeCart") ShoppingCart shoppingCart) {
    StoreService storeService = new StoreService();
    storeService.setShoppingCart(shoppingCart);
    return storeService;
}
```

这里`@Qualifier`限定了传入方法的参数必须是名为“prototypeCart”的bean。

实际上，`@Qualifier`注解可以存在于很多地方：

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Qualifier {
    String value() default "";
}
```

### 3.4 bean的作用域

*以下内容代码在工程sia4e-P1_Core_Spring-C03_Advanced_wiring-04_scope中。*

**在默认情况下，Spring应用上下文中所有bean都是作为以单例（singleton）的形式创建的**。也就是说，不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例。

有时候，可能会发现，你所使用的类是易变的（mutable），它们会保持一些状态，因此重用是不安全的。在这种情况下，声明为单例的bean就不是什么好主意了，因为对象会被污染，稍后重用的时候会出现意想不到的问题。

Spring定义了多种作用域，可以基于这些作用域创建bean：

- 单例（Singleton）：在整个应用中，只创建bean的一个实例。
- 原型（Prototype）：每次注入通过Spring应用上下文获取时都会创建一个新的bean实例。
- 会话（Session）：在Web应用中，为每个会话创建一个bean实例。
- 请求（request）：在Web应用中，为每个请求创建一个bean实例。

单例是默认的作用域，但是正如之前所述，对于易变的类型，这并不合适。如果选择其他的作用域，要使用`@Scope`注解，它可以与`@Component`或`@Bean`一起使用。

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class Notepad {
    // ...
}
```

```java
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Notepad notepad() {
    return new Notepad();
}
```

这里，使用`ConfigurableBeanFactory`类的`SCOPE_PROTOTYPE`常量设置了原型作用域。你当然也可以使用`@Scope("prototype")`，但是使用`SCOPE_PROTOTYPE`常量更加安全并且不易出错。

如果使用XML来配置的话，需要使用`<bean>`元素的`scope`作用域：

```xml
<bean id="notepad" class="com.myapp.Notepad" scope="prototype" />
```

不管使用哪种方式来声明原型作用域，每次注入或从Spring应用上下文中检索该bean的时候，都会创建新的实例。这样所导致的结果就是每次操作都能得到自己的`Notepad`实例。

#### 3.4.1 使用会话和请求作用域

在Web应用中，如果能够实例化在会话和请求范围内共享的bean，那将是非常有价值的事情。例如，在典型的电子商务应用中，可能会有一个bean代表用户的购物车。如果购物车是单例的话，那么将会导致所有的用户都会向同一个购物车中添加商品。另一方面，如果购物车是原型作用域的，那么在应用中某一个地方往购物车中添加商品，在应用的另外一个地方可能就不可用了，因为在这里注入的是另外一个原型作用域的购物车。

就购物车bean来说，会话作用域是最为合适的，因为它与给定的用户关联性最大。要指定会话作用域，我们可以使用`@Scope`注解，它的使用方式与指定原型作用域是相同的：

```java
@Bean
@Scope(value = WebApplicationContext.SCOPE_SESSION, 
    proxyMode = ScopedProxyMode.INTERFACES)
public ShoppingCart cart() {
    // ...
}
```

这里，我们将`value`设置成了`WebApplicationContext`中的`SCOPE_SESSION`常量（它的值是session）。这会告诉Spring为Web应用中的每个会话创建一个`ShoppingCart`。这会创建多个`ShoppingCart` bean的实例，但是对于给定的会话只会创建一个实例，在当前会话相关的操作中，**这个bean实际上相当于单例的**。

`@Scope`同时还有一个`proxyMode`属性，它被设置成了`ScopedProxyMode.INTERFACES`。这个属性解决了将会话或请求作用域的bean注入到单例bean中所遇到的问题。

问题的场景是这样的，结社我们要将`ShoppingCart` bean注入到单例的`StoreService` bean的Setter方法中：

```java
@Component
public class StoreService {

    private ShoppingCart shoppingCart;

    @Autowired
    public void setShoppingCart(ShoppingCart shoppingCart) {
        this.shoppingCart = shoppingCart;
    }

    // ...
}
```

因为`StoreService`是一个单例的bean，会在Spring应用上下文加载的时候创建。当它创建的时候，Spring会试图将`ShoppingCart` bean注入到`setShoppingCart()`方法中。但是`ShoppingCart` bean是会话作用域的，此时并不存在。直到某个用户进入系统，创建了会话之后，才会出现`ShoppingCart`实例。

另外，系统中将会有多个`ShoppingCart`实例：每个用户一个。我们并不想让Spring注入某个固定的`ShoppingCart`实例到`StoreService`中。我们希望的是当`StoreService`处理购物车功能时，它所使用的`ShoppingCart`实例恰好是当前会话所对应的那一个。

Spring并不会将实际的`ShoppingCart` bean注入到`StoreService`中，Spring会注入一个到`ShoppingCart` bean的代理，这个代理会暴露与`ShoppingCart`相同的方法，所以`StoreService`
会认为它就是一个购物车。但是，当`StoreService`调用`ShoppingCart`的方法时，代理会对其进行懒解析并将调用委托给会话作用域内真正的`ShoppingCart` bean。

如下图所示：

<center>
    ![图3.1-作用域代理延迟注入请求和会话作用域的bean](images\图3.1-作用域代理延迟注入请求和会话作用域的bean.PNG)
    **作用域代理能够延迟注入请求和会话作用域的bean**
</center>

现在讨论一下proxyMode属性。如上代码所示`proxyMode`属性被设置成了`ScopedProxyMode.INTERFACES`，这表明这个代理要实现`ShoppingCart`接口，并将调用委托给实现bean。

如果ShoppingCart是接口而不是类的话，这是可以的（也是最为理想的代理模式）。但如果`ShoppingCart`是一个具体的类的话，Spring就没有办法创建基于接口的代理了。此时，它必须使用CGLib来生成基于类的代理。此时需要将`proxyMode`属性设置为`ScopedProxyMode.TARGET_CLASS`，以此来表明要以生成目标类扩展的方式创建代理。

这里主要关注了会话作用域，但请求作用域的bean也面临同样的装配问题，因此请求作用域bean也应该以作用域代理的方式注入。

#### 3.4.2 在XML中声明作用域代理

如果你需要使用XML来声明会话或请求作用域的bean，那么就不能使用@`Scope`注解及其`proxyMode`属性了。`<bean>`元素的`scope`属性能够设置bean的作用域，但是该怎样指定代理模式呢？

要设置代理模式，我们需要使用Spring aop命名空间的一个新元素：

```xml
<bean id="cart" class="com.myapp.ShoppingCartImpl" scope="session">
    <aop:scoped-proxy />
</bean>
```

`<aop:scoped-proxy>`是与`@Scope`注解的`proxyMode`属性功能相同的Spring XML配置元素。它会告诉Spring为bean创建一个作用域代理。**默认情况下，它会使用CGLib创建目标类的代理**。但是我们也可以将`proxy-target-class`属性设置为`false`，进而要求它生成基于接口的代理：

```xml
<bean id="cart" class="com.myapp.ShoppingCartImpl" scope="session">
    <aop:scoped-proxy proxy-target-class="false" />
</bean>
```

当然了，要使用`<aop:scoped-proxy>`元素需要在配置文件中使用aop约束。

### 3.5 运行时值注入

*以下内容代码在工程sia4e-P1_Core_Spring-C03_Advanced_wiring-04_external中。*

当讨论依赖注入的时候，我们通常所讨论的是将一个bean引用注入到另一个bean的属性或构造器参数中。它通常来讲指的是将一个对象与另一个对象进行关联。

但是bean装配的另外一个方面指的是将一个值注入到bean的属性或者构造器参数中。

例如：

```java
@Bean
public CompactDisc sgtPeppers() {
    return new BlankDisc("Sgt. Pepper's Lonelu Hearts Club Band", "The Beatles");
}
```

尽管上述代码可以实现我们的需求，也就是为一个`BlankDisc` bean设置title和artist，但这汇总实现是将值硬编码在配置类中的。与之类似，如果使用XML的话，那么值也是硬编码的：

```xml
<bean id="sgtPeppers" class="soundsystem.BlankDisc"
    c:title="Sgt. Pepper's Lonely Hearts Club Band" c:artist="The Beatles" />
```

有时候硬编码是可以的，但有的时候我们希望避免硬编码，而是让这些值在运行时再确定。为了实现这些功能，Spring提供了两种在运行时求值的方式：

- 属性占位符（Property placeholder）
- Spring表达式语言（SpEL）

#### 3.5.1 注入外部的值

在Spring中，处理外部值的最简单方式就是声明属性源并通过Spring的`Environment`来检索属性，这需要使用`@ProperySource`注解。

```java
package com.soundsystem;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.Environment;

@Configuration
// 声明属性来源
@PropertySource("classpath:com/soundsystem/app.properties")
public class ExpressiveConfig {

    @Autowired
    private Environment env;

    @Bean
    public BlankDisc disc() {
        // 使用Environment检索属性值
        return new BlankDisc(env.getProperty("disc.title"), env.getProperty("disc.artist"));
    }
}
```

上例中，`@PropertySource`引用了类路径下的一个名为app.properties
的文件：

```text
disc.title=Sgt. Peppers Lonely Hearts Club Band
disc.artist=The Beatles
```

这个属性文件会加载到Spring的`Environment`中，稍后可以从这里检索属性。同时，在`disc()`方法中，会创建一个新的`BlankDisc`，它的构造器参数是从属性文件中获取的，而这是通过调用`getProperty()`实现的。

`Environment`是个接口，它继承了接口`PropertyResolver`。`PropertyResolver`接口中定义了四中`getProperty()`方法的重载形式：

- String getProperty(String key);
- String getProperty(String key, String defaultValue);
- <T> T getProperty(String key, Class<T> targetType);
- <T> T getProperty(String key, Class<T> targetType, T defaultValue);

前两种形式都返回`String`类型的值。我们可以对`disc()`方法稍微修改，这样在指定属性不存在的时候会使用一个默认值：

```java
package com.soundsystem;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.Environment;

@Configuration
// 声明属性来源
@PropertySource("classpath:/com/soundsystem/app.properties")
public class ExpressiveConfig {

    @Autowired
    private Environment env;

    @Bean
    public BlankDisc disc() {
        // 使用Environment检索属性值
        return new BlankDisc(
                env.getProperty("disc.title", "Rattle and Hum"), 
                env.getProperty("disc.artist", "U2")
                );
    }
}
```

其余两种`getProperty()`方法不会将所有的值都视为`String`类型。例如，假设你想要获取的值所代表的含义是连接池中所维持的连接数量。如果我们从属性文件中得到的是一个`String`类型的值，那么在使用之前还需要将其转换为`Integer`类型。但是，如果使用重载形式的`getProperty()`的话，就能非常便利地解决这个问题：

```java
int connectionCount = env.getProperty("db.connection.count", Integer.class, 30);
```

`Environment`还提供了几个与属性相关的方法，如果你在使用`getProperty()`方法的时候没有指定默认值，并且这个属性没有定义的话，获取到的值是`null`。如果你希望这个属性必须要定义，那么可以使用`getRequiredProperty()`方法，如下所示：

```java
@Bean
public BlankDisc disc() {
    return new BlankDisc(env.getRequiredProperty("disc.title"), env.getRequiredProperty("disc.artist"));
}
```

这里如果我们没有定义上述属性的话，方法将抛出`IllegalStateException`异常。

如果我们想检查某个属性是否存在的话可以使用`containsProperty()`方法；如果想将属性解析为类的话可以使用`getPropertyAsClass()`方法（这个方法在4.3版本中已经被标注`@Deprecated`）。

直接从`Environment`中检索属性是非常方便的，尤其是在Java配置中装配bean的时候。但是，Spring也提供了通过占位符装配属性的方法，这些占位符的值会来源于一个属性源。

**解析属性占位符**

Spring一直支持将属性定义到外部的属性的文件中，并使用占位符值将其插入到Spring bean中。在Spring装配中，占位符的形式为使用“${...}”包装的属性名称。

例如使用在XML中按照如下的方式解析`BlankDisc`构造器参数：

```xml
<bean id="sgtPeppers" class="com.soundsystem.BlankDisc" c:title="${disc.title}" c:artist="${disc.artist}" />
```

按照这种方式，XML配置没有使用任何硬编码的值，它的值是从配置文件以外的一个源中解析得到的。

如果我们依赖于组件扫描和自动装配来创建和初始化应用组件的话，那么就没有指定占位符的配置文件或类了。在这种情况下，我们可以使用`@Value`注解，它的使用方式与`@Autowired`注解非常相似。比如，在`BlankDisc`类中，构造器可以改成如下所示：

（对于基本类型和`String`类型，`@Value`注解可以直接注入值，例如`@Value("The Beatles")`）

```java
@Component
public class BlankDisc implements CompactDisc {

    private String title;
    private String artist;
    
    public BlankDisc(@Value("${disc.title}") String title, @Value("${disc.artist}") String artist) {
        this.title = title;
        this.artist = artist;
    }

    @Override
    public void play() {
        System.out.println("Playing " + title + " by " + artist);
    }
}
```

为了使用占位符，我们必须要配置一个`PropertyPlaceholderConfigurer` bean或`PropertySourcesPlaceholderConfigurer` bean。从Spring3.1开始，推荐使用`PropertySourcesPlaceholderConfigurer`，因为它能够基于`Environment`及其属性源来解析占位符。

使用JavaConfig和XML的混合方法配置：

```java
@Configuration
// 引入配置bean的XML文件
@ImportResource("classpath:com/soundsystem/config.xml")
// 声明属性来源
//@PropertySource("classpath:/com/soundsystem/app.properties")
public class ExpressiveConfig {

//    @Autowired
//    private Environment env;
//
//    @Bean
//    public BlankDisc disc() {
//        // 使用Environment检索属性值
//        return new BlankDisc(env.getProperty("disc.title", "Rattle and Hum"), env.getProperty("disc.artist", "U2"));
//    }

    @Bean
    // 注意，这里要配置为static方法。
    public static PropertySourcesPlaceholderConfigurer placeholderConfigurer() {
        
        PropertySourcesPlaceholderConfigurer placeholderConfigurer = new PropertySourcesPlaceholderConfigurer();

        // 加载属性文件
        placeholderConfigurer.setLocations(new Resource[] { new ClassPathResource("/com/soundsystem/app.properties") });

        return placeholderConfigurer;
    }
}
```

config.xml文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="sgtPeppers" class="com.soundsystem.BlankDisc" c:title="${disc.title}" c:artist="${disc.artist}" />

</beans>
```

测试类：

```java
package com.soundsystem;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = ExpressiveConfig.class)
public class PropertyholderTest {
    
    @Autowired
    private CompactDisc disc;
    
    @Test
    public void propertyholderTest() {
        disc.play();
    }
}
```

如果使用XML配置的话，需要使用`context`命名空间下的`<context:propertyplaceholder>`元素，这个元素将为我们生成`PropertySourcesPlaceholderConfigurer` bean：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:c="http://www.springframework.org/schema/c"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="sgtPeppers" class="com.soundsystem.BlankDisc" c:title="${disc.title}" c:artist="${disc.artist}" />

    <context:property-placeholder location="classpath:com/soundsystem/app.properties" />

</beans>
```

#### 3.5.2 使用Spring表达式语言进行装配

Spring 3引入了Spring表达式语言（Spring Expression Language，SpEL），它能够以一种强大和简洁的方式将值装配到bean属性和构造器参数中，在这个过程中所使用的表达式会在运行时计算得到值。

SpEL拥有很多特性，包括：

- 使用bean的ID来引用bean
- 调用方法和访问对象的属性
- 对值进行算术、关系和逻辑运算
- 正则表达式匹配
- 集合操作

SpEL还能够用在依赖注入以外的其他地方。例如，Spring Security支持使用SpEL表达式定义安全限制规则。另外，如果你在Spring MVC应用中使用Thymeleaf模板作为视图的话，那么这些模板可以使用SpEL表达式引用模型数据。

**SpEL样例**

需要了解的第一件事情就是SpEL表达式要放到“#{ ... }”之中，这与属性占位符有些类似，属性占位符需要放到“${ ... }”之中。

例如：

```text
#{T(System).currentTimeMillis()}
```

它的最终结果是计算表达式的那一刻当前时间的毫秒数。T()表达式会将`java.lang.System视为Java`中对应的类型，因此可以调用其`static`修饰的`currentTimeMillis()`方法。

SpEL表达式也可以引用其他的bean或其他bean的属性：

```text
#{sgtPeppers.artist}
```

也可以通过`systemProperties`对象引用系统属性：

```text
#{systemProperties['discd.title']}
```

同样地，如果我们使用组件扫描的方式创建bean的话，在注入属性和构造器参数时，我们可以使用`@Value`注解并搭配SpEL表达式：

```java
public BlankDisc(@Value("#{systemProperties['disc.title']}") String title, @Value("#{systemProperties['disc.artist']}") String artist) {
    this.title = title;
    this.artist = artist;
}
```

在XML中同样可以使用：

```xml
<bean id="sgtPeppers" class="com.soundsystem.BlankDisc" c:title="#{systemProperties['disc.title']}" c:artist="#{systemProperties['disc.artist']}" />
```

**SpEL支持的基础表达式**

表示字面量：

- 浮点数：
    + `#{3.14159}`
    + `#{9.87E4}`
- String字面量：
    + `#{'Hello'}`
- 布尔值：
    + `#{true}`
    + `#{false}`

在SpEL中使用字面值其实没有太大的意思，毕竟将整型属性设置为1，或者将布尔属性设置为`false`时，我们并不需要使用SpEL。

**引用bean、属性和方法**

SpEL所能做的另外一件基础的事情就是通过ID引用其他的bean。

例如引用名为sgtPeppers的bean：

```text
#{sgtPeppers}
```

假设我们要引用该bean中的`artist`属性：

```text
#{sgtPeppers.artist}
```

我们甚至可以调用bean上的方法，假设一个名为artistSelector的bean有方法`selectArtist()`：

```text
#{artistSelector.selectArtist()}
```

对于被调用方法的返回值，我们同样可以继续调用方法：

```text
#{artistSelector.selectArtist().toUpperCase()}
```

如果`selectArtist()`的返回值不是`null`的话，这没有什么问题。为了避免出现`NullPointerException`，我们可以使用类型安全的运算符：

```text
#{artistSelector.selectArtist()?.toUpperCase()}
```

“?.”运算符能够在访问它右边内容之前确保它所对应的元素不是`null`。。所以，如果`selectArtist()`的返回值是`null`的话，那么SpEL将不会调用`toUpperCase()`方法。表达式的返回值会是`null`。

如果要在SpEL中访问类作用域的方法和常量的话，要依赖T()这个关键的运算符。例如，为了在SpEL中表达Java的`Math`类，需要按照如下的方式使用T()运算符：

```text
#{T(java.lang.Math)}
```

这里所示的`T()`运算符的结果会是一个`Class`对象，代表了`java.lang.Math`。如果需要的话，我们甚至可以将其装配到一个`Class`类型的bean属性中。但是T()运算符的真正价值在于它能够访问目标类型的静态方法和常量。

例如，如果我们要将PI值装配到bean属性中，可以这样做：

```text
#{T(java.lang.Math).PI}
```

**SpEL运算符**

SpEL提供了多个运算符：

运算符类型 | 运算符
-----|-----
算术运算 | `+`、`-`、`*`、`/`、`%`、`^`
比较运算 | `<`、`>`、`==`、`<=`、`=>`、`lt`、`gt`、`eq`、`le`、`ge`
逻辑运算 | `and`、`or`、`not`、`|`
条件运算 | `?:(ternary)`、`?:(Elvis)`
正则表达式 | `matches`

例如：

```text
#{2 * T(java.lang.Math).PI * circle.radius}
```

在这里PI的值乘以2，再乘`circle` bean的属性`radius`属性的值来计算这个bean定义的圆的周长。

类似地，计算面积：

```text
#{T(java.lang.Math).PI * circle.radius ^ 2}
```

当使用`String`类型时，“+”运算符执行的是连接操作，这与Java相同：

```text
#{disc.title + " by " + disc.artist}
```

“==”运算符和文本类型运算符“eq”军事用于对值进行对比，返回布尔类型：

```text
#{counter.total == 100}
#{counter.total eq 100}
```

SpEL还提供了三元运算符（ternary）：

```text
#{scoreboard.score > 1000 ? "Winner!" : "Loser"}
```

三元表达式的一个常用的场景是检查`null`值：

```text
#{disc.title ?: 'Rattle and Hum'}
```

计算正则表达式，返回布尔类型：

```text
#{admin.email matches '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.com'}
```

计算集合和数组是SpEL中最令人惊奇的技巧，例如引用列表中的一个元素：

```text
#{jukebox.songs[4].title}
```

这个表达式会计算songs集合中第五个（基于零开始）元素的`title`属性，这个集合来源于ID为jukebox bean。

假设我们要从jukebox中随机选择一首歌：

```text
#{jukebox.songs[T(java.lang.Math).random() * jukebox.songs.size()].title}
```

“[]”运算符用来从集合或数组中按照索引获取元素，但是它还可以从`String`中获取一个字符：

```text
#{'This is a test'[3]}
```

这个表达式引用了`String`中的第四个（基于零开始）字符，也就是“s”。

SpEL还提供了查询运算符“.?[]”，它会用来对集合进行过滤，得到集合的一个子集。假设我们希望得到jukebox中`artist`属性为Aerosmith的所有歌曲。如下的表达式就使用查询运算符得到了Aerosmith的所有歌曲：

```text
#{jukebox.songs.?[artist eq 'Aerosmith']}
```

SpEL还提供了另外两个查询运算符：“.^[]”和“.$[]”，它们分别用来在集合中查询第一个匹配项和最后一个匹配项。例如下面的表达式会查找列表中第一个`artist`属性为Aerosmith的歌曲：

```text
#{jukebox.songs.^[artist eq 'Aerosmith']}
```

最后，SpEL还提供了投影运算符“.![]”，它会从集合的每个成员中选择特定的属性放到另外一个集合中。作为样例，假设我们不想要歌曲对象的集合，而是所有歌曲名称的集合。如下的表达式会将`title`属性投影到一个新的`String`类型的集合中：

```text
#{jukebox.songs.![title]}
```

又例如我们可以使用如下的表达式获得Aerosmith所有歌曲的名称列表：

```text
#{jukebox.songs.?[artist eq 'Aerosmith'].![title]}
```

在动态注入值到Spring bean时，SpEL是一种很便利和强大的方式。我们有时会忍不住编写很复杂的表达式。但需要注意的是，不要让你的表达式太智能。你的表达式越智能，对它的测试就越重要。SpEL毕竟只是`String`类型的值，可能测试起来很困难。

### 3.6 小结

>
我们在本章介绍了许多背景知识，在第2章所介绍的基本bean装配基础之上，又学习了一些强大的高级装配技巧。
>
首先，我们学习了Spring profile，它解决了Spring bean要跨各种部署环境的通用问题。在运行时，通过将环境相关的bean与当前激活的profile进行匹配，Spring能够让相同的部署单元跨多种环境运行，而不需要进行重新构建。
>
Profile bean是在运行时条件化创建bean的一种方式，但是Spring 4提供了一种更为通用的方式，通过这种方式能够声明某些bean的创建与否要依赖于给定条件的输出结果。结合使用@Conditional注解和Spring Condition接口的实现，能够为开发人员提供一种强大和灵活的机制，实现条件化地创建bean。
>
我们还看了两种解决自动装配歧义性的方法：首选bean以及限定符。尽管将某个bean设置为首选bean是很简单的，但这种方式也有其局限性，所以我们讨论了如何将一组可选的自动装配bean，借助限定符将其范围缩小到只有一个符合条件的bean。除此之外，我们还看到了如何创建自定义的限定符注解，这些限定符描述了bean的特性。
>
尽管大多数的Spring bean都是以单例的方式创建的，但有的时候其他的创建策略更为合适。Spring能够让bean以单例、原型、请求作用域或会话作用域的方式来创建。在声明请求作用域或会话作用域的bean的时候，我们还学习了如何创建作用域代理，它分为基于类的代理和基于接口的代理的两种方式。
>
最后，我们学习了Spring表达式语言，它能够在运行时计算要注入到bean属性中的值。
>
对于bean装配，我们已经掌握了扎实的基础知识，现在我们要将注意力转向面向切面编程（aspect-oriented programming ，AOP）了。依赖注入能够将组件及其协作的其他组件解耦，与之类似，AOP有助于将应用组件与跨多个组件的任务进行解耦。在下一章，我们将会深入学习在Spring中如何创建和使用切面。

## 第四章 面向切面的Spring

本章内容：

- 面向切面编程的基本原理
- 通过POJO创建切面
- 使用`@AspectJ`注解
- 为AspectJ切面注入依赖

在软件开发中，散布于应用中多处的功能被称为横切关注点（cross-cutting concern），例如日志、安全和事务管理等。通常来讲，这些横切关注点从概念上是与应用的业务逻辑相分离的（但是往往会直接嵌入到应用的业务逻辑之中）。把这些横切关注点与业务逻辑相分离正是面向切面编程（AOP）所要解决的问题。

### 4.1 什么是面向切面编

切面能帮助我们模块化横切关注点。简而言之，横切关注点可以被描述为影响应用多处的功能。例如，安全就是一个横切关注点，应用中的许多方法都会涉及到安全规则。

<center>
    ![图4.1-切面实现了横切关注点的模块化](images\图4.1-切面实现了横切关注点的模块化.PNG)
    **切面实现了横切关注点（跨多个应用对象的逻辑）的模块化**
</center>

上图展现了一个被划分为模块的典型应用。每个模块的核心功能都是为特定业务领域提供服务，但是这些模块都需要类似的辅助功能，例如安全和事务管理。

如果要重用通用功能的话，最常见的面向对象技术是继承（inheritance）或委托（delegation）。但是，如果在整个应用中都使用相同的基类，继承往往会导致一个脆弱的对象体系；而使用委托可能需要对委托对象进行复杂的调用。

切面提供了取代继承和委托的另一种可选方案，而且在很多场景下更清晰简洁。在使用面向切面编程时，我们仍然在一个地方定义通用功能，但是可以通过声明的方式定义这个功能要以何种方式在何处应用，而无需修改受影响的类。**横切关注点可以被模块化为特殊的类，这些类被称为切面（aspect）**。这样做有两个好处：首先，现在每个关注点都集中于一个地方，而不是分散到多处代码中；其次，服务模块更简洁，因为它们只包含主要关注点（或核心功能）的代码，而次要关注点的代码被转移到切面中了。

#### 4.1.1 定义AOP术语

AOP已经形成了自己的术语。描述切面的常用术语有通知（advice）、切点（pointcut）和连接点（join point）。下图表示了这些概念是如何关联的：

<center>
    ![图4.2-通知、切点和连接点的关联](images\图4.2-通知、切点和连接点的关联.PNG)
    **在一个或多个连接点上，可以把切面的功能（通知）织入到程序的执行过程中**
</center>

>
遗憾的是，大多数用于描述AOP功能的术语并不直观，尽管如此，它们现在已经是AOP行话的组成部分了，为了理解AOP，我们必须了解这些术语。在我们进入某个领域之前，必须学会在这个领域该如何说话。

**通知（Advice）**

在AOP术语中，切面的工作被称为通知。通知定义了切面是什么以及何时使用。除了描述切面要完成的工作，通知还解决了何时执行这个工作的问题。

Spring切面可以应用5种类型的通知：

- 前置通知（Before）：在目标方法被调用之前调用通知功能。
- 后置通知（After）：在目标方法完成之后调用通知，此时不会关心方法的输出是什么。
- 返回通知（After-returning）：在目标方法成功执行之后调用通知。
- 异常通知（After-throwing）：在目标方法抛出异常后调用通知。
- 环绕通知（Around）：通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为。


**连接点（Joint point）**

我们的应用可能有数以千计的时机应用通知。这些时机被称为连接点。连接点是在应用执行过程中能够插入切面的一个点。这个点可以是调用方法时、抛出异常时、甚至修改一个字段时。切面代码可以利用这些点插入到应用的正常流程之中，并添加新的行为。

**切点（Pontcut）**

一个切面并不需要通知应用的所有连接点。切点有助于缩小切面所通知的连接点的范围。如果说通知定义了切面的“什么”和“何时”的话，那么切点就定义了“何处”。切点的定义会匹配通知所要织入的一个或多个连接点。我们通常使用明确的类和方法名称，或是利用正则表达式定义所匹配的类和方法名称来指定这些切点。有些AOP框架允许我们创建动态的切点，可以根据运行时的决策（比如方法的参数值）来决定是否应用通知。

**切面（Aspect）**

切面是通知和切点的结合。通知和切点共同定义了切面的全部内容：它是什么，在何时和何处完成其功能。

**引入（Introduction）**

引入允许我们向现有的类添加新方法或属性。

**织入（Weaving）**

织入是把切面应用到目标对象并创建新的代理对象的过程。切面在指定的连接点被织入到目标对象中。在目标对象的生命周期里有多个点可以进行织入：

- 编译期：切面在目标类编译时被织入。这种方式需要特殊的编译器。AspectJ的织入编译器就是以这种方式织入切面的。
- 类加载期：切面在目标类加载到JVM时被织入。这种方式需要特殊的类加载器（ClassLoader），它可以在目标类被引入应用之前增强该目标类的字节码。AspectJ 5的加载时织入（load-timeweaving，LTW）就支持以这种方式织入切面。
- 运行期：切面在应用运行的某个时刻被织入。一般情况下，在织入切面时，AOP容器会为目标对象动态地创建一个代理对象。Spring AOP就是以这种方式织入切面的。

#### 4.1.2 Spring对AOP的支持

并不是所有的AOP框架都是相同的，它们在连接点模型上可能有强弱之分。有些允许在字段修饰符级别应用通知，而另一些只支持与方法调用相关的连接点。它们织入切面的方式和时机也有所不同。但是无论如何，创建切点来定义切面所织入的连接点是AOP框架的基本功能。

Spring提供了4种类型的AOP支持：

- 基于代理的经典Spring AOP。
- 纯POJO切面。
- `@AspectJ`注解驱动的切面。
- 注入式AspectJ切面。

前三种都是Spring AOP实现的变体，Spring AOP构建在动态代理基础上，因此Spring对AOP的支持局限于方法拦截。

经典Spring AOP曾经非常好，但现在Spring提供了更简洁和干净的面向切面编程方式。引入了简单的声明式AOP和基于注解的AOP之后，Spring经典的AOP看起来就显得非常笨重和过于复杂，直接使用`ProxyFactory` Bean会让人感觉厌烦。

借助Spring的aop命名空间，我们可以将纯POJO转换为切面。实际上，这些POJO只是提供了满足切点条件时所要调用的方法。遗憾的是，这种技术需要XML配置，但这的确是声明式地将对象转换为切面的简便方式。

Spring借鉴了AspectJ的切面，以提供注解驱动的AOP。本质上，它依然是Spring基于代理的AOP，但是编程模型几乎与编写成熟的AspectJ注解切面完全一致。这种AOP风格的好处在于能够不使用XML来完成功能。

如果你的AOP需求超过了简单的方法调用（如构造器或属性拦截），那么你需要考虑使用AspectJ来实现切面。在这种情况下，上文所示的第四种类型能够帮助你将值注入到AspectJ驱动的切面中。

**Spring通知是Java编写的**

Spring所创建的通知都是用标准的Java类编写的。这样的话，我们就可以使用与普通Java开发一样的集成开发环境（IDE）来开发切面。而且，定义通知所应用的切点通常会使用注解或在Spring配置文件里采用XML来编写，这两种语法对于Java开发者来说都是相当熟悉的。

AspectJ与之相反。虽然AspectJ现在支持基于注解的切面，但AspectJ最初是以Java语言扩展的方式实现的。这种方式有优点也有缺点。通过特有的AOP语言，我们可以获得更强大和细粒度的控制，以及更丰富的AOP工具集，但是我们需要额外学习新的工具和语法。

**Spring在运行时通知对象**

通过在代理类中包裹切面，Spring在运行期把切面织入到Spring管理的bean中。

如下所示，代理类封装了目标类，并拦截被通知方法的调用，再把调用转发给真正的目标bean。当代理拦截到方法调用时，在调用目标bean方法之前，会执行切面逻辑。

<center>
    ![图4.3-Spring的切面由包裹了目标对象的代理类实现](images\图4.3-Spring的切面由包裹了目标对象的代理类实现.PNG)
    **Spring的切面包裹了目标对象的代理类实现。代理类处理方法的调用，执行额外的切面逻辑并调用目标方法**
</center>

直到应用需要被代理的bean时，Spring才创建代理对象。如果使用的是`ApplicationContext`的话，在`ApplicationContext`从`BeanFactory`中加载所有bean的时候，Spring才会创建被代理的对象。因为Spring运行时才创建代理对象，所以我们不需要特殊的编译器来织入Spring AOP的切面。

**Spring只支持方法级别的连接点**

正如前面所探讨过的，通过使用各种AOP方案可以支持多种连接点模型。因为Spring基于动态代理，所以Spring只支持方法连接点。这与一些其他的AOP框架是不同的，例如AspectJ和JBoss，除了方法切点，它们还提供了字段和构造器接入点。Spring缺少对字段连接点的支持，无法让我们创建细粒度的通知，例如拦截对象字段的修改。而且它不支持构造器连接点，我们就无法在bean创建时应用通知。

但是方法拦截可以满足绝大部分的需求。如果需要方法拦截之外的连接点拦截功能，那么我们可以利用Aspect来补充Spring AOP的功能。

### 4.2 通过切点来选择连接点

正如之前所提过的，**切点用于准确定位应该在什么地方应用切面的通知**。通知和切点是切面的最基本元素。因此，了解如何编写切点非常重要。

在Spring AOP中，要使用AspectJ的切点表达式语言来定义切点。关于Spring AOP的AspectJ切点，最重要的一点就是Spring仅支持AspectJ切点指示器（pointcut designator）的一个子集。Spring是基于代理的，而某些切点表达式是与基于代理的AOP无
关的。

Spring AOP支持的AspectJ切点指示器：

AspectJ指示器 | 描述
----- | -----
`arg()` | 限制连接点匹配参数为指定类型的执行方法
`@args()` | 限制连接点匹配参数由指定注解标注的执行方法
`execution()` | 用于匹配是连接点的执行方法
`this()` | 限制连接点匹配AOP代理的bean引用为指定类型的类
`target` | 限制连接点匹配目标对象为指定类型的类
`@target()` | 限制连接点匹配特定的执行对象，这些对象对应的类要具有指定类型的注解
`within()` | 限制连接点匹配指定的类型
`@within()` | 限制连接点匹配指定注解所标注的类型
`@annotation` | 限定匹配带有指定注解的连接点

在Spring中尝试使用AspectJ其他指示器时，将会抛出`IllegalArgumentException`异常。

当我们查看如上所展示的这些Spring支持的指示器时，**注意只有`execution()`指示器是实际执行匹配的，而其他的指示器都是用来限制匹配的**。这说明`execution()`指示器是我们在编写切点定义时最主要使用的指示器。在此基础上，我们使用其他指示器来限制所匹配的切点。

**`execution()`表达式的内容**

```text
execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) throws-pattern?)
```

- modifiers-pattern?：表示的是修饰符，其中?代表可以省略
- ret-type-pattern：方法的返回参数
- declaring-type-pattern?：表示方法所在的类的访问路径
- name-pattern(param-pattern)：表示的是方法名
- throws-pattern?：表示的是异常

#### 4.2.1 编写切点

为了阐述Spring中的切面，我们需要有个主题来定义切面的切点。为此，这里定义一个`Performance`接口：

```java
package concert;

public interface Performance {
    void perform();
}
```

`Performance`可以代表任何类型的现场表演，如舞台剧、电影或音乐会。

现在我们编写`Performace`的`perform()`方法触发的通知。

**（注意，这里的切点为接口的方法，不是实现类的方法。当括号里面解析为一个接口的方法时，Spring会去拦截其所有实现类的该方法。）**

```text
execution(* concert.Performance.perform(..))
```

<center>
    ![图4.4-使用execution切点表达式来选择Performance类中的方法](images\图4.4-使用execution切点表达式来选择Performance类中的方法.PNG)
    **使用execution()切点表达式来选择Performance的perform()方法**
</center>

- 第一个“*”表示任意的返回类型
- concert.Performance.：方法所述的类
- perform：方法名
- (..)：任意参数

假设我们需要配置的切点仅匹配concert包，再次场景下可以使用`within()`指示器来进行限制：

```text
execution(* concert.Performance.perform(..) && within(concert.*))
```

<center>
    ![图4.5-使用within指示器进行限制](images\图4.5-使用within指示器进行限制.PNG)
    **使用within()指示器限制切点范围**
</center>

这里使用了“&&”操作符将两个指示器连接在一起形成与关系（切点必须匹配所有指示器）。类似地还有“||”、“!”来表示或关系和非操作。

**因为“&”在XML种有特殊含义，所以当使用XML配置时要使用“and”代替“&&”、“or”代替“||”以及“not”代替“!”。**

#### 4.2.2 在切点中选择bean

除了上文中表所列出的指示器外，Spring还引入了一个新的指示器`bean()`，它允许我们在切点表达式中使用bean的ID来标识bean。这用来限定只匹配特定的bean。

例如：

```text
execution(* concert.Performance.perform(..) and bean('woodstock'))
```

这里我们希望在执行`Performance`的`perform()`方法时应用通知，但限定bean的ID为woodstock。

同样的我们还可以使用非操作符：

```text
execution(* concert.Performance.perform(..) and !bean('woodstock'))
```

这样就限定了除了特定ID以外的bean应用通知。

### 4.3 使用注解创建切面

*以下内容代码在工程sia4e-P1_Core_Spring-C04_Aspect-oriented_Spring-01_annotation中。*

使用注解来创建切面是AspectJ 5所引入的关键特性。AspectJ 5之前，编写AspectJ切面需要学习一种Java语言的扩展，但是AspectJ面向注解的模型可以非常简便地通过少量注解把任意类转变为切面。

我们已经定义了`Performance`接口，它是切面中切点的目标对象。现在，让我们使用AspecJ注解来定义切面。

#### 4.3.1 定义切面

如果一场演出没有观众的话，那不能称之为演出。对不对？从演出的角度来看，观众是非常重要的，但是对演出本身的功能来讲，它并不是核心，这是一个单独的关注点。因此，将观众定义为一个切面，并将其应用到演出上就是较为明智的做法。

`Audience`类：

```java
package concert;

import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class Audience {

    // 表演之前
    @Before("execution(** concert.Performance.perform(..))")
    public void silenceCellPhones() {
        System.out.println("Silencing cell phones");
    }

    // 表演之前
    @Before("execution(** concert.Performance.perform(..))")
    public void takeSeats() {
        System.out.println("Taking seats");
    }

    // 表演之后
    @AfterReturning("execution(** concert.Performance.perform(..))")
    public void applause() {
        System.out.println("CLAP CLAP CLAP!!!");
    }

    // 表演失败之后
    @AfterThrowing("execution(** concert.Performance.perform(..))")
    public void demandRefund() {
        System.out.println("Demanding a refund");
    }
}
```

`Audience`类使用`@AspectJ`注解，该注解表明`Audience`不仅仅是一个POJO，同时它还是一个切面。`Audience`类中的方法都使用注解来定义切面的具体行为。

`Audience`有四个方法，定义了一个观众在观看演出时可能会做的事情。在演出之前，观众要就坐（`takeSeats()`）并将手机调至静音状态（`silenceCellPhones()`）。如果演出很精彩的话，观众应该会鼓掌喝彩（`applause()`）。不过，如果演出没有达到观众预期的话，观众会要求退款（`demandRefund()`）。

可以看到这些方法都使用了通知注解来表明它们应该在什么时候调用。AspectJ提供了五个注解来定义通知：

注解 | 通知
-----|-----
`@After` | 通知方法会在目标方法返回或抛出异常后调用
`@AfterReturing` | 通知方法会在目标方法返回后调用
`@AfterThrowing` | 通知方法会在目标方法抛出异常后调用
`@Around` | 通知方法会将目标方法封装起来
`@Before` | 通知方法会在目标方法调用之前执行

`Audience`使用到了前面五个注解中的三个。`takeSeats()`和`silenceCellPhones()`方法都用到了`@Before`注解，表明它们应该在演出开始之前调用。`applause()`方法使用了`@AfterReturning`注解，它会在演出成功返回后调用。`demandRefund()`方法上添加了`@AfterThrowing`注解，这表明它会在抛出异常以后执行。

所有的这些注解都给定了一个切点表达式作为它的值，同时，这四个方法的切点表达式都是相同的。其实，它们可以设置成不同的切点表达式，但是在这里，这个切点表达式就能满足所有通知方法的需求。如果我们只定义这个切点一次，然后每次需要的时候引用它，那么这会是一个很好的方案。

我们完全可以这样做：`@Pointcut`注解能够在一个`@AspectJ`切面内定义可重用的切点：

在`Audience`中，`performance()`方法使用了`@Pointcut`注解。为`@Pointcut`注解设置的值是一个切点表达式，就像之前在通知注解上所设置的那样。通过在`performance()`方法上添加`@Pointcut`注解，我们实际上扩展了切点表达式语言，这样就可以在任何的切点表达式中使用`performance()`了。

**`performance()`方法的实际内容并不重要，在这里它实际上应该是空的。其实该方法本身只是一个标识，供`@Pointcut`注解依附。**

需要注意的是，除了注解和没有实际操作的`performance()`方法，`Audience`类依然是一个POJO。我们能够像使用其他的Java类那样调用它的方法，它的方法也能够独立地进行单元测试，这与其他的Java类并没有什么区别。`Audience`只是一个Java类，只不过它通过注解表明会作为切面使用而已。

像其它类一样，我们也可以将`Audience`装配为Spring中的bean：

```java
@Bean
public Audience audience() {
    return new Audience();
}
```

如果我们就此止步的话，`Audience`只会是Spring容器中的一个bean。即使我们用了AspectJ注解，它也不会被视为切面，这些注解被解析，也不会创建将其转为切面的代理。

如果我们使用JavaConfig配置，那么可以在配置类的类级别上通过使用`@EnableAspectJAutoProxy`注解启用自动代理功能：

```java
package concert;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan
@EnableAspectJAutoProxy
public class ConcertConfig {

    @Bean
    public Audience audience() {
        return new Audience();
    }
}
```

如果使用XML配置，则要使用元素`<aop:aspectj-autoproxy />`

无论我们使用什么配置，AspectJ都会为使用`@AspectJ`注解的bean创建一个代理，这个代理会围绕着所有该切面的切点所匹配的bean。在这种情况下，将会为`Concert` bean创建一个代理，`Audience`类中的通知方法将会在`perform()`调用前后执行。

我们需要记住的是，Spring的AspectJ自动代理仅仅使用`@AspectJ`作为创建切面的指导，切面依然是基于代理的。在本质上，它依然是Spring基于代理的切面。这一点非常重要，因为这意味着尽管使用的是`@AspectJ`注解，但我们仍然限于代理方法的调用。如果想利用AspectJ的所有能力，我们必须在运行时使用AspectJ并且不依赖Spring来创建基于代理的切面。

到现在为止，我们的切面在定义时，使用了不同的通知方法来实现前置通知和后置通知。但是我们还有另外的一种通知：环绕通知（around advice）。环绕通知与其他类型的通知有所不同，因此值得花点时间来介绍如何进行编写。

#### 4.3.2 创建环绕通知

环绕通知是最为强大的通知类型。它能够让你所编写的逻辑将被通知的目标方法完全包装起来。实际上就像在一个通知方法中同时编写前置通知和后置通知。

为了描述环绕通知，我们重写`Audience`切面。这次使用一个环绕通知来代替之前的多个不同的前置通知和后置通知：

```java
@Around("performance()")
public void watchPerformance(ProceedingJoinPoint jp) {
    System.out.println("Silencing cell phones");
    System.out.println("Taking seats");
    try {
        jp.proceed();
    } catch (Throwable e) {
        System.out.println("Demanding a refund");
    }
    System.out.println("CLAP CLAP CLAP!!!");
}
```

在这里，`@Around`注解表明`watchPerformance()`方法会作为performance()切点的环绕通知。在这个通知中，观众在演出之前会将手机调至静音并就坐，演出结束后会鼓掌喝彩。像前面一样，如果演出失败的话，观众会要求退款。

关于这个新的通知方法，你首先注意到的可能是它接受`ProceedingJoinPoint`作为参数。这个对象是必须要有的，因为你要在通知中通过它来调用被通知的方法。通知方法中可以做任何的事情，当要将控制权交给被通知的方法时，它需要调用`ProceedingJoinPoint`的`proceed()`方法。**如果不调这个方法的话，那么你的通知实际上会阻塞对被通知方法的调用。**

#### 4.3.3 处理通知中的参数

到目前为止，我们的切面都很简单，没有任何参数。唯一的例外是我们为环绕通知所编写的`watchPerformance()`示例方法中使用了`ProceedingJoinPoint`作为参数。除了环绕通知，我们编写的其他通知不需要关注传递给被通知方法的任意参数。这很正常，因为我们所通知的`perform()`方法本身没有任何参数。

但是，如果切面所通知的方法确实有参数该怎么办呢？切面能访问和使用传递给被通知方法的参数吗？

为了阐述这个问题，让我们重新看一下`BlankDisc`样例。

`CompactDisc`接口：

```java
package soundsystem;

public interface CompactDisc {
    void playTrack(int trackNumber);
}

```

`BlankDisc`类：

```java
package soundsystem;

import java.util.List;

import soundsystem.CompactDisc;

public class BlankDisc implements CompactDisc {

    private String title;
    private String artist;
    private List<String> tracks;

    public void setTitle(String title) {
        this.title = title;
    }

    public void setArtist(String artist) {
        this.artist = artist;
    }

    public void setTracks(List<String> tracks) {
        this.tracks = tracks;
    }

    public void play() {
        for (int i = 0; i < tracks.size(); i++) {
            playTrack(i);
        }
    }

    @Override
    public void playTrack(int trackNumber) {
        System.out.println("Playing " + title + " -Track: " + trackNumber + " " + tracks.get(trackNumber) + " by " + artist);
    }
}
```

这里`play()`方法会循环所有的音轨，并调用`playTrack()`方法。

现在我们假设想要记录每个音轨被播放的次数。其中一种方法就是修改`playTrack()`的实现，直接在每次调用的时候记录这个数量。但是记录音轨的播放次数与播放本身是不同的关注点，因此这个任务不应该属于`playTrack()`方法，而是术语切面要完成的任务。

为了记录每个磁道所播放的次数，我们创建了`TrackCounter`类，它是通知`playTrack()`方法的一个切面。

```java
package soundsystem;

import java.util.HashMap;
import java.util.Map;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.annotation.Pointcut;

@Aspect
public class TrackCounter {

    private Map<Integer, Integer> trackCounts = new HashMap<>();

    // 定义切点
    @Pointcut("execution(* soundsystem.CompactDisc.playTrack(int)) && args(trackNumber)")
    public void trackPlayed(int trackNumber) {

    }

    // 在播放前为该音轨计数
    @Before("trackPlayed(trackNumber)")
    public void countTrack(int trackNumber) {
        int currentCount = getPlayCount(trackNumber);
        trackCounts.put(trackNumber, currentCount + 1);
    }

    public int getPlayCount(int trackNumber) {
        return trackCounts.containsKey(trackNumber) ? trackCounts.get(trackNumber) : 0;
    }
}
```

与之前所创建的切面相同，这个切面使用`@Pointcut`注解定义，并使用`@Before`将一个方法声明为前置通知。但是，着谜的不同点在于切点声明了要提供给通知方法的参数：

<center>
    ![图4.6-在切点表达式中声明参数](images\图4.6-在切点表达式中声明参数.PNG)
    **在切点表达式中声明参数，这个参数传入到通知方法中**
</center>

需要关注的是切点表达式中的`args(trackNumber)`限定符。它表明传递给`playTrack()`方法的int类型参数也会传递到通知中去。参数的名称`trackNumber`也与切点方法签名中的参数相匹配。

这个参数会传递到通知方法中，这个通知方法是通过`@Before`注解和命名切点`trackPlayed(trackNumber)`定义的。切点定义中的参数与切点方法中的参数名称是一样的，这样就完成了从命名切点到通知方法的参数转移。

我们可以在Spring配置中将`BlankDisc`和`TrackCounter`定义为bean，并启用AspectJ自动代理：

```java
package soundsystem;

import java.util.ArrayList;
import java.util.List;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@EnableAspectJAutoProxy
public class TrackCounterConfig {

    @Bean("sgtPeppers")
    public CompactDisc sgtPeppers() {
        BlankDisc cd = new BlankDisc();
        cd.setTitle("Sgt. Pepper's Lonely Hearts Club Band");
        cd.setArtist("The Beatles");
        List<String> tracks = new ArrayList<>();
        tracks.add("Sgt. Pepper's Lonely Hearts Club Band");
        tracks.add("With a Little Help from My Friends");
        tracks.add("Lucy in the Sky with Diamonds");
        tracks.add("Getting Better");
        tracks.add("Fixing a Hole");
        cd.setTracks(tracks);
        return cd;
    }

    @Bean("trackCounter")
    public TrackCounter trackCounter() {
        return new TrackCounter();
    }
}
```

编写测试类：

```java
package soundsystem;

import static org.junit.Assert.assertEquals;

import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.SystemOutRule;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = TrackCounterConfig.class)
public class TrackCounterTest {
    
    @Rule
    public final SystemOutRule log = new SystemOutRule().enableLog();
    
    @Autowired
    private CompactDisc cd;
    
    @Autowired
    private TrackCounter counter;
    
    @Test
    public void testTrackCounter() {
        cd.playTrack(1);
        cd.playTrack(2);
        cd.playTrack(3);
        cd.playTrack(3);
        cd.playTrack(3);
        cd.playTrack(3);
        cd.playTrack(4);
        assertEquals(0, counter.getPlayCount(0));
        assertEquals(1, counter.getPlayCount(1));
        assertEquals(1, counter.getPlayCount(2));
        assertEquals(4, counter.getPlayCount(3));
        assertEquals(1, counter.getPlayCount(4));
    }
}
```

测试均正常通过。

到目前为止，在我们所使用的切面中，所包装的都是被通知对象的已有方法。但是，方法包装仅仅是切面所能实现的功能之一。让我们看一下如何通过编写切面，为被通知的对象引入全新的功能。

#### 4.3.4 通过注解引入新功能

一些编程语言，例如Ruby和Groovy，有开放类的理念。它们可以不用直接修改对象或类的定义就能够为对象或类增加新的方法。不过，Java并不是动态语言。一旦类编译完成了，我们就很难再为该类添加新的功能了。

但是如果仔细想想，我们在本章中不是一直在使用切面这样做吗？当然，我们还没有为对象增加任何新的方法，但是已经为对象拥有的方法添加了新功能。如果切面能够为现有的方法增加额外的功能，为什么不能为一个对象增加新的方法呢？实际上，利用被称为**引入（introduction）**的AOP概念，切面可以为Spring bean添加新方法。

在Spring中，切面只是实现了它们所包装bean相同接口的代理。如果除了实现这些接口，代理也能暴露新接口的话，会怎么样呢？那样的话，切面所通知的bean看起来像是实现了新的接口，即便底层实现类并没有实现这些接口也无所谓。

<center>
    ![图4.7-使用Spring AOP我们可以为bean引入新的方法](images\图4.7-为bean引入新的方法.PNG)
    **使用Spring AOP，我们可以为bean引入新的方法。代理拦截调用并委托给实现该方法的其他对象**
</center>

我们需要注意的是，当引入接口的方法被调用时，代理会把此调用委托给实现了新接口的某个其他对象。实际上，一个bean的实现被拆分到了多个类中。

下面来验证这个主意是否能行得通。我们为示例中的所有的`Performance`实现引入下面的`Encoreable`接口：

```java
package concert;

public interface Encoreable {
    void performEncore();
}
```

我们需要有一种方式将这个接口应用到`Performance`实现中。我们现在假设你能够访问`Performance`的所有实现，并对其进行修改，让它们都实现`Encoreable`接口。但是，从设计的角度来看，这并不是最好的做法，并不是所有的`Performance`都是具有`Encoreable`特性的。另一方面，我们有可能无法修改`Performance`实现，当使用第三方实现并且没有源码的时候更是如此。

值得庆幸的是，借助于AOP的引入功能，我们可以不必在设计上妥协或者侵入性地改变现有的实现。为了实现该功能，我们要创建一个新的切面：

```java
package concert;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.DeclareParents;

@Aspect
public class EncoreableIntroducer {

    @DeclareParents(value = "concert.Performance+", defaultImpl = DefaultEncoreable.class)
    public static Encoreable encoreable;
}
```

可以看到，`EncoreableIntroducer`是一个切面。但是，它与我们之前所创建的切面不同，它并没有提供前置、后置或环绕通知，而是通过`@DeclareParents`注解，将`Encoreable`接口引入到`Performance` bean中。

`@DeclareParents`注解由三部分组成：

- value属性指定了哪种类型的bean要引入该接口。在本例中，也就是所有实现`Performance`的类型。（标记符后面的加号表示是`Performance`的所有子类型，而不是`Performance`本身。）
- defaultImpl属性指定了为引入功能提供实现的类。在这里，我们指定的是`DefaultEncoreable`提供实现。
- `@DeclareParents`注解所标注的静态属性指明了要引入了接口。在这里，我们所引入的是`Encoreable`接口。

和其他的切面一样，我们需要在Spring应用中将`EncoreableIntroducer`声明为一个bean。

```java
@Bean
public EncoreableIntroducer introducer() {
    return new EncoreableIntroducer();
}
```

Spring的自动代理机制会获取到它的声明, 然后当Spring发现一个bean中使用了`@Aspect`注解时, Spring就会创建一个代理, 然后由代理调用目标bean的方法或是被引入接口的方法。

`EncoreableIntroducer`中声明了我们的默认实现为`DefaultEncoreable`，所以要创建这个类：

（该类不用注册为bean）

```java
package concert;

public class DefaultEncoreable implements Encoreable {

    @Override
    public void performEncore() {
        System.out.println("Final Encore!");
    }
}
```

下面进行测试。

`Performance`实现类：

```java
package concert;

public class OrchestralPerformance implements Performance {

    @Override
    public void perform() {
        System.out.println("Orchestral performance now !");
    }
}
```

将其注册为bean：

```java
@Bean
public Performance orchestral() {
    return new OrchestralPerformance();
}
```

测试类：

```java
package concert;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import concert.ConcertConfig;
import concert.Encoreable;
import concert.Performance;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = ConcertConfig.class)
public class IntroducerTest {

    @Autowired
    private Performance performance;

    @Test
    public void testEncore() {

        // perform()切点上定义了环绕通知
        performance.perform();

        // 将Performance接口的实现强转为Encoreable
        Encoreable encoreable = (Encoreable) performance;
        // 调用Encoreable接口的performEncore()方法会自动调用DefaultEncoreable的performEncore()方法
        encoreable.performEncore();
    }
}
```

输出：

```text
Silencing cell phones
Taking seats
Orchestral performance now !
CLAP CLAP CLAP!!!
Final Encore!
```

在Spring中，注解和自动代理提供了一种很便利的方式来创建切面。它非常简单，并且只涉及到最少的Spring配置。但是，面向注解的切面声明有一个明显的劣势：你必须能够为通知类添加注解。为了做到这一点，必须要有源码。

如果我们没有源码的话，或者不想将AspectJ注解放到你的代码之中，Spring为切面提供了另外一种可选方案，即使用XML声明切面。

### 4.4 在XML中声明切面

*以下内容代码在工程sia4e-P1_Core_Spring-C04_Aspect-oriented_Spring-02_xml中。*

在前面的内容中，我们曾经建立过这样一种原则，那就是基于注解的配置要优于基于Java的配置，基于Java的配置要优于基于XML的配置。**但是，如果你需要声明切面，但是又不能为通知类添加注解的时候，那么就必须转向XML配置了**。

Spring的aop命名空间提供了多个元素用来在XML中声明切面，如下表所示：

AOP配置元素 | 用途
-----|-----
`<aop:config>` | 顶层的AOP配置元素。大多数的`<aop:*>`元素必须包含在`<aop:config>`元素内
`<aop:advisor>` | 定义AOP通知器
`<aop:before>` | 定义一个AOP前置通知
`<aop:after>` | 定义AOP后置通知（不管被通知的方法是否执行成功）
`<aop:after-returning>` | 定义AOP返回通知
`<aop:after-throwing>` | 定义AOP异常通知
`<aop:around>` | 定义AOP环绕通知
`<aop:aspect>` | 定义一个切面
`<aop:aspectj-autoproxy>` | 启用`@AspectJ`注解驱动的切面
`<aop:declare-parents>` | 以透明的方式为被通知的对象引入额外的接口
`<aop:pointcut>` | 定义一个切点

我们已经看过了`<aop:aspectj-autoproxy>`元素，它能够自动代理AspectJ注解的通知类。aop命名空间的其他元素能够让我们直接在Spring配置中声明切面，而不需要使用注解。

例如，我们重新看一下`Audience`类，这一次我们将注解全部去掉：

```java
public class Audience {
    
    // 表演之前
    public void silenceCellPhones() {
        System.out.println("Silencing cell phones");
    }

    // 表演之前
    public void takeSeats() {
        System.out.println("Taking seats");
    }

    // 表演之后
    public void applause() {
        System.out.println("CLAP CLAP CLAP!!!");
    }

    // 表演失败之后
    public void demandRefund() {
        System.out.println("Demanding a refund");
    }
}
```

此时，这个类没有任何的特别之处，他就是有几个方法的简单Java类。尽管看起来并没有什么差别，但`Audience`已经具备了成为AOP通知的所有条件。

#### 4.4.1 声明前置通知和后置通知

使用Spring aop命名空间中的一些元素，将没有注解的`Audience`类转换为切面：

```xml
<!-- 声明Audience -->
<bean id="audience" class="concert.Audience" />

<aop:config>
    <!-- 定义切面，引用audience -->
    <aop:aspect ref="audience">
        <!-- 前置通知 -->
        <aop:before 
            pointcut="execution(** concert.Performance.perform(..))"
            method="silenceCellPhones" />
        <!-- 前置通知 -->
        <aop:before 
            pointcut="execution(** concert.Performance.perform(..))"
            method="takeSeats" />
        <!-- 返回通知 -->
        <aop:after-returning
            pointcut="execution(** concert.Performance.perform(..))" 
            method="applause" />
        <!-- 异常通知 -->
        <aop:after-throwing
            pointcut="execution(** concert.Performance.perform(..))" 
            method="demandRefund" />
    </aop:aspect>
</aop:config>
```

关于Spring AOP配置元素，第一个需要注意的事项是大多数的AOP配置元素必须在`<aop:config>`元素的上下文内使用。这条规则有几种例外场景，但是把bean声明为一个切面时，我们总是从`<aop:config>`元素开始配置的。

在`<aop:config>`元素内，我们可以声明一个或多个通知器、切面或者切点。这里我们使用`<aop:aspect>`元素声明了一个简单的切面。`ref`元素引用了一个POJO bean，该bean实现了切面的功能。`ref`元素所引用的bean提供了在切面中通知所调用的方法。

该切面应用了四个不同的通知。两个`<aop:before>`元素定义了匹配切点的方法执行之前调用前置通知方法，也就是`Audience` bean的`takeSeats()`和`turnOffCellPhones()`方法（由method属性所声明）。`<aop:after-returning>`元素定义了一个返回（after-returning）通知，在切点所匹配的方法调用之后再调用`applause()`方法。同样，`<aop:after-throwing>`元素定义了异常（after-throwing）通知，如果所匹配的方法执行时抛出任何的异常，都将会调用`demandRefund()`方法。

下图展示了通知逻辑如何织入到业务逻辑中：

<center>
    ![图4.8-Audience方法包含的4种通知](images\图4.8-Audience方法包含的4种通知.PNG)
    **Audience切面包含四种通知，它们把通知逻辑织入进匹配切面切点的方法中**
</center>

注意到所有通知元素中的`pointcut`属性的值都是一样的，这是因为所有的通知都要应用到相同的切点上。在基于AspectJ注解的通知中，当发现这种类型的重复时，我们使用`@Pointcut`注解消除了这些重复的内容。而在基于XML的切面声明中，我们需要使用`<aop:pointcut>`元素。

下面将通用的起点表达式抽取到一个切点声明种，这样这个声明就能在所有通知中使用了：

```xml
<aop:config>
    <!-- 定义切面，引用audience -->
    <aop:aspect ref="audience">
        <!-- 定义切点 -->
        <aop:pointcut 
            expression="execution(** concert.Performance.perform(..))"
            id="performance" />
        <!-- 前置通知，引用切点 -->
        <aop:before pointcut-ref="performance" method="silenceCellPhones" />
        <!-- 前置通知，引用切点 -->
        <aop:before pointcut-ref="performance" method="takeSeats" />
        <!-- 返回通知，引用切点 -->
        <aop:after-returning pointcut-ref="performance" method="applause" />
        <!-- 异常通知，引用切点 -->
        <aop:after-throwing pointcut-ref="performance" method="demandRefund" />
    </aop:aspect>
</aop:config>
```

现在切点是在一个地方定义的，并且被多个通知元素所引用。`<aop:pointcut>`元素定义了一个`id`为performance的切点。同时修改所有的通知元素，用`pointcut-ref`属性来引用这个命名切点。

`<aop:pointcut>`元素所定义的切点可以被同一个`<aop:aspect>`元素之内的所有通知元素引用。如果想让定义的切点能够在多个切面使用，我们可以把`<aop:pointcut`>元素放在`<aop:config>`元素的范围内。

#### 4.4.2 声明环绕通知

目前`Audience`的实现工作得非常棒，但是前置通知和后置通知有一些限制。具体来说，如果不使用成员变量存储信息的话，在前置通知和后置通知之间共享信息非常麻烦。

假设除了进场关闭手机和表演结束后鼓掌，我们还希望观众确保一直关注演出，并报告每个参赛者表演了多长时间。使用前置通知和后置通知实现该功能的唯一方式是在前置通知中记录开始时间并在某个后置通知中报告表演耗费的时间。但这样的话我们必须在一个成员变量中保存开始时间。因为`Audience`是单例的，如果像这样保存状态的话，将会存在线程安全问题。

相对于前置通知和后置通知，环绕通知在这点上有明显的优势。使用环绕通知，我们可以完成前置通知和后置通知所实现的相同功能，而且只需要在一个方法中 实现。因为整个通知逻辑是在一个方法内实现的，所以不需要使用成员变量保存状态。

修改`Audience`类：

```java
package concert;

import org.aspectj.lang.ProceedingJoinPoint;

public class Audience {  

    public void watchPerformance(ProceedingJoinPoint jp) {
        System.out.println("Silencing cell phones");
        System.out.println("Taking seats");
        try {
            jp.proceed();
        } catch (Throwable e) {
            System.out.println("Demanding a refund");
        }
        System.out.println("CLAP CLAP CLAP!!!");
    }
}
```

在观众切面中，`watchPerformance()`方法包含了之前四个通知方法的所有功能。

声明环绕通知与声明其他类型的通知并没有太大区别。我们所需要做的仅仅是使用`<aop:around>`元素。

```xml
<aop:config>
    <!-- 定义切面，引用audience -->
    <aop:aspect ref="audience">
        <!-- 定义切点 -->
        <aop:pointcut 
            expression="execution(** concert.Performance.perform(..))"
            id="performance" />
        <!-- 声明环绕通知 -->
        <aop:around method="watchPerformance" pointcut-ref="performance"/>
    </aop:aspect>
</aop:config>
```

#### 4.4.3 为通知传递参数

现在我们不用注解来配置`TrackCounter`切面，无注解的TrackCounter类：

```java
package soundsystem;

import java.util.HashMap;
import java.util.Map;

public class TrackCounter {

    private Map<Integer, Integer> trackCounts = new HashMap<>();

    public void trackPlayed(int trackNumber) {

    }

    public void countTrack(int trackNumber) {
        int currentCount = getPlayCount(trackNumber);
        trackCounts.put(trackNumber, currentCount + 1);
    }

    public int getPlayCount(int trackNumber) {
        return trackCounts.containsKey(trackNumber) ? trackCounts.get(trackNumber) : 0;
    }
}
```

使用XML配置，让`TrackCounter`重新变为切面：

```xml
<!-- 声明TrackCounter -->
<bean id="trackCounter" class="soundsystem.TrackCounter" />

<!-- 声明BlankDisc -->
<bean id="sgtPeppers" class="soundsystem.BlankDisc">
    <property name="title" value="Sgt. Pepper's Lonely Hearts Club Band" />
    <property name="artist" value="The Beatles" />
    <property name="tracks">
        <list>
            <value>Sgt. Pepper's Lonely Hearts Club Band</value>
            <value>With a Little Help from My Friends</value>
            <value>Lucy in the Sky with Diamonds</value>
            <value>Getting Better</value>
            <value>Fixing a Hole</value>
        </list>
    </property>
</bean>

<aop:config>
    <aop:aspect ref="trackCounter">
        <aop:pointcut
            expression="execution(* soundsystem.CompactDisc.playTrack(int)) and args(trackNumber)"
            id="trackPlayed" />
        <aop:before method="countTrack" pointcut-ref="trackPlayed" />
    </aop:aspect>
</aop:config>
```

这里注意在切点表达式中使用的是“and”而不是“&&”，因为在XML中“&”会被解析为实体的开始。

#### 4.4.4 通过切面引入新功能

在使用注解的配置中，我们使用了`@DeclareParents`注解为被通知当方法引入新的方法。使用aop命名空间中的`<aop:declare-parents>`元素，我们可以实现相同的功能。

```xml
<aop:aspect>
    <aop:declare-parents 
        types-matching="concert.Performance+" 
        implement-interface="concert.Encoreable"
        default-impl="concert.DefaultEncoreable" />
</aop:aspect>
```

`<aop:declare-parents>`声明了此切面所通知的bean要在它的对象层次结构中拥有新的父类型。具体到本例中，类型匹配`Performance`接口（由`types-matching`属性指定）的那些bean在父类结构中会增加`Encoreable`接口（由`implement-interface`属性指定）。最后要解决的问题是`Encoreable`接口中的方法实现要来自于何处。

这里有两种方式标识要引入接口的实现，上述配置使用`default-impl`属性通过指定全限定类名来显式指定`Encorable`的实现。我们还可以使用`delegate-ref`属性来标识：

```xml
<aop:aspect>
    <aop:declare-parents 
        types-matching="concert.Performance+" 
        implement-interface="concert.Encoreable"
        delegate-ref="encorableDelegate" />
</aop:aspect>
```

`delegate-ref`属性引用了一个Spring bean作为引入的委托。这需要在Spring上下文中存在一个ID为`encoreableDelegate`的bean。

```xml
<bean id="encorableDelegate" class="concert.DefaultEncoreable" />
```

完整的配置：

soundsystem.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 声明TrackCounter -->
    <bean id="trackCounter" class="soundsystem.TrackCounter" />

    <!-- 声明BlankDisc -->
    <bean id="sgtPeppers" class="soundsystem.BlankDisc">
        <property name="title" value="Sgt. Pepper's Lonely Hearts Club Band" />
        <property name="artist" value="The Beatles" />
        <property name="tracks">
            <list>
                <value>Sgt. Pepper's Lonely Hearts Club Band</value>
                <value>With a Little Help from My Friends</value>
                <value>Lucy in the Sky with Diamonds</value>
                <value>Getting Better</value>
                <value>Fixing a Hole</value>
            </list>
        </property>
    </bean>
    
    <aop:config>
        <!-- 声明切面 -->
        <aop:aspect ref="trackCounter">
            <!-- 声明切点 -->
            <aop:pointcut
                expression="execution(* soundsystem.CompactDisc.playTrack(int)) and args(trackNumber)"
                id="trackPlayed" />
            <!-- 声明前置通知 -->
            <aop:before method="countTrack" pointcut-ref="trackPlayed" />
        </aop:aspect>
    </aop:config>
</beans> 
```

concert.xml:：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 声明Audience -->
    <bean id="audience" class="concert.Audience" />

    <!-- 声明DefaultEncoreable -->
    <bean id="encorableDelegate" class="concert.DefaultEncoreable" />
    
    <!-- 声明Performance的实现类 -->
    <bean id="orchestra" class="concert.OrchestralPerformance" />

    <aop:config>
        <!-- 声明切面，引用audience -->
        <aop:aspect ref="audience">
            <!-- 声明切点 -->
            <aop:pointcut expression="execution(** concert.Performance.perform(..))"
                id="performance" />
            <!-- 声明环绕通知 -->
            <aop:around method="watchPerformance" pointcut-ref="performance" />
        </aop:aspect>

        <aop:aspect>
            <!-- 声明引入 -->
            <aop:declare-parents 
                types-matching="concert.Performance+"
                implement-interface="concert.Encoreable" 
                delegate-ref="encorableDelegate" />
        </aop:aspect>
    </aop:config>
</beans>
```

使用上述XML配置，去掉JavaConfig和注解，测试依然全部正常通过。

### 4.5 注入AspectJ切面

*以下内容代码在工程sia4e-P1_Core_Spring-C04_Aspect-oriented_Spring-03_aspectj中。*

虽然Spring AOP能够满足许多应用的切面需求，但是与AspectJ相比，Spring AOP 是一个功能比较弱的AOP解决方案。AspectJ提供了SpringAOP所不能支持的许多类型的切点。

例如，当我们需要在创建对象时应用通知，构造器切点就非常方便。不像某些其他面向对象语言中的构造器，Java构造器不同于其他的正常方法。这使得Spring基于代理的AOP无法把通知应用于对象的创建过程。

对于大部分功能来讲，AspectJ切面与Spring是相互独立的。虽然它们可以织入到任意的Java应用中，这也包括了Spring应用，但是在应用AspectJ切面时几乎不会涉及到Spring。

但是精心设计且有意义的切面很可能依赖其他类来完成它们的工作。如果在执行通知时，切面依赖于一个或多个类，我们可以在切面内部实例化这些协作的对象。但更好的方式是，我们可以借助Spring的依赖注入把bean装配进AspectJ切面中。

为了演示，我们为上面的演出创建一个新切面。具体来讲，我们以切面的方式创建一个评论员的角色，他会观看演出并且会在演出之后提供一些批评意见。

```java
package concert;

import com.springinaction.springidol.CriticismEngine;

// 注意不是class是aspect
// 工程环境为Gradle + Spring + AspectJ
public aspect CriticAspect {

    public CriticAspect() {
    }

    // 定义切点
    pointcut performance() : execution(* perform(..));

    // 定义返回通知
    after() returning : performance()  {
        System.out.println(criticismEngine.getCriticism());
    }

    private CriticismEngine criticismEngine;

    // 注入CriticismEngine
    public void setCriticismEngine(CriticismEngine criticismEngine) {
        this.criticismEngine = criticismEngine;
    }
}
```


`performance()`切点匹配`perform()`方法。当它与`after() returning`通知一起配合使用时，我们可以让该切面在表演结束时起作用。

实际上，`CriticAspect`与一个`CriticismEngine`对象相协作，在表演结束时，调用该对象的getCriticism()方法来发表一个苛刻的评论。为了避免`CriticAspect`和`CriticismEngine`之间产生不必要的耦合，我们通过Setter依赖注入为`CriticAspect`设置`CriticismEngine`。

它们的关系如下图：

<center>
    ![图4.9-切面也需要注入](images\图4.9-切面也需要注入.PNG)
    **切面也需要注入。像其他的bean一样，Spring可以为AspectJ切面注入依赖**
</center>

`CriticismEngine`及实现类：

```java
package com.springinaction.springidol;

public interface CriticismEngine {
    String getCriticism();
}
```

```java
package com.springinaction.springidol;

public class CriticismEngineImpl implements CriticismEngine {

    private String[] criticismPool;

    // 注入criticismPool
    public void setCriticismPool(String[] criticismPool) {
        this.criticismPool = criticismPool;
    }

    @Override
    public String getCriticism() {
        int criticismIndex = (int) (Math.random() * criticismPool.length);
        return criticismPool[criticismIndex];
    }

}
```

`CriticismEngineImpl`实现了`CriticismEngine`接口，通过从注入的评论池中随机选择一个苛刻的评论。

将`CriticismEngineImpl`注册为bean：

```xml
<!-- 声明CriticismEngineImpl -->
<bean id="criticismEngine" class="com.springinaction.springidol.CriticismEngineImpl">
    <property name="criticismPool">
        <list>
            <value>Worst performance ever!</value>
            <value>I laughed, I cried, then I realized I was at the wrong show.</value>
            <value>A must see show!</value>
        </list>
    </property>
</bean>
```

我们现在有了一个要赋予`CriticAspect`的`CriticismEngine`实现。剩下的就是为`CriticAspect`装配`CriticismEngineImple`。

在展示如何实现注入之前，我们必须清楚AspectJ切面根本不需要Spring就可以织入到我们的应用中。如果想使用Spring的依赖注入为AspectJ切面注入协作者，那我们就需要在Spring配置中把切面声明为一个Spring配置中的`<bean>`。如下的`<bean>`声明会把`criticismEngine` bean注入到`CriticAspect`中：

```xml
<bean class="concert.CriticAspect" factory-method="aspectOf">
    <property name="criticismEngine" ref="criticismEngine" />
</bean>
```

很大程度上，`<bean>`的声明与我们在Spring中所看到的其他`<bean>`配置并没有太多的区别，但是最大的不同在于使用了`factory-method`属性。通常情况下，Spring bean由Spring容器初始化，但是AspectJ切面是由AspectJ在运行期创建的。等到Spring有机会为`CriticAspect`注入`CriticismEngine`时，`CriticAspect`已经被实例化了。

因为Spring不能负责创建`CriticAspect`，那就不能在 Spring中简单地把`CriticAspect`声明为一个bean。相反，我们需要一种方式为Spring获得已经由AspectJ创建的`CriticAspect`实例的句柄，从而可以注入`CriticismEngine`。幸好，所有的AspectJ切面都提供了一个静态的`aspectOf()`方法，该方法返回切面的一个单例。所以为了获得切面的实例，我们必须使用`factory-method`来调用`aspectOf()`方法而不是调用`CriticAspect`的构造器方法。    

简而言之，Spring不能像之前那样使用`<bean>`声明来创建一个`CriticAspect`实例——它已经在运行时由AspectJ创建完成了。Spring需要通过`aspectOf()`工厂方法获得切面的引用，然后像`<bean>`元素规定的那样在该对象上执行依赖注入。

综上所述，完整的配置如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 声明Audience -->
    <bean id="audience" class="concert.Audience" />

    <!-- 声明DefaultEncoreable -->
    <bean id="encorableDelegate" class="concert.DefaultEncoreable" />

    <!-- 声明Performance的实现类 -->
    <bean id="orchestra" class="concert.OrchestralPerformance" />

    <!-- 声明CriticismEngineImpl -->
    <bean id="criticismEngine" class="com.springinaction.springidol.CriticismEngineImpl">
        <property name="criticismPool">
            <list>
                <value>Worst performance ever!</value>
                <value>I laughed, I cried, then I realized I was at the wrong show.</value>
                <value>A must see show!</value>
            </list>
        </property>
    </bean>
    
    <!-- 声明AspectJ切面 -->
    <bean class="concert.CriticAspect" factory-method="aspectOf">
        <property name="criticismEngine" ref="criticismEngine" />
    </bean>

    <aop:config>
        <!-- 声明切面，引用audience -->
        <aop:aspect ref="audience">
            <!-- 声明切点 -->
            <aop:pointcut expression="execution(** concert.Performance.perform(..))"
                id="performance" />
            <!-- 声明环绕通知 -->
            <aop:around method="watchPerformance" pointcut-ref="performance" />
        </aop:aspect>

        <aop:aspect>
            <!-- 声明引入 -->
            <aop:declare-parents types-matching="concert.Performance+"
                implement-interface="concert.Encoreable" delegate-ref="encorableDelegate" />
        </aop:aspect>
    </aop:config>
</beans>
```

测试类：

```java
package concert;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import concert.Encoreable;
import concert.Performance;

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:concert.xml")
public class ConcertTest {

    @Autowired
    private Performance performance;

    @Test
    public void testEncore() {

        // 测试Spring AOP和AspectJ AOP
        performance.perform();

        // 测试引入
        Encoreable encoreable = (Encoreable) performance;

        encoreable.performEncore();
    }
}
```

输出结果：

```text
Silencing cell phones
Taking seats
Orchestral performance now !
I laughed, I cried, then I realized I was at the wrong show.
CLAP CLAP CLAP!!!
Final Encore!
```

### 4.6 小结

>
AOP是面向对象编程的一个强大补充。通过AspectJ，我们现在可以把之前分散在应用各处的行为放入可重用的模块中。我们显示地声明在何处如何应用该行为。这有效减少了代码冗余，并让我们的类关注自身的主要功能。
>
Spring提供了一个AOP框架，让我们把切面插入到方法执行的周围。现在我们已经学会如何把通知织入前置、后置和环绕方法的调用中，以及为处理异常增加自定义的行为。
>
关于在Spring应用中如何使用切面，我们可以有多种选择。通过使用@AspectJ注解和简化的配置命名空间，在Spring中装配通知和切点变得非常简单。
>
最后，当Spring AOP不能满足需求时，我们必须转向更为强大的AspectJ。对于这些场景，我们了解了如何使用Spring为AspectJ切面注入依赖。
>
此时此刻，我们已经覆盖了Spring框架的基础知识，了解到如何配置Spring容器以及如何为Spring管理的对象应用切面。正如我们所看到的，这些核心技术为创建松散耦合的应用奠定了坚实的基础。

## 第五章 构建Spring Web应用程序

本章内容：

- 映射请求到Spring控制器
- 透明地绑定表单参数
- 校验表单提交

对于很多Java开发人员来说，基于Web的应用程序是他们主要的关注点。如果你有这方面经验的话，你会意识到这种系统所面临的挑战。具体来讲，状态管理、工作流以及验证都是需要解决的重要特性。HTTP协议的无状态性决定了这些问题都不那么容易解决。

Spring的Web框架就是为了帮你解决这些关注点而设计的。SpringMVC基于模型-视图-控制器（Model-View-Controller，MVC）模式实现，它能够帮你构建像Spring框架那样灵活和松耦合的Web应用程序。

在本章中，我们将会介绍Spring MVC Web框架，并使用新的SpringMVC注解来构建处理各种Web请求、参数和表单输入的控制器。

### 5.1 Spring MVC起步

Spring MVC框架内含有多种组件，框架会把到来的请求在调度Servlet、处理器映射（handler mapping）、控制器以及视图解析器（view resolver）之间移动，每一个Spring MVC中的组件
都有特定的目的，并且它也没有那么复杂。

#### 5.1.1 跟踪Spring MVC的请求

每当用户在Web浏览器中点击链接或提交表单的时候，请求就开始工作了。对请求的工作描述就像是快递投送员。与邮局投递员或FedEx投送员一样，请求会将信息从一个地方带到另一个地方。

请求是一个十分繁忙的家伙。从离开浏览器开始到获取响应返回，它会经历好多站，在每站都会留下一些信息同时也会带上其他信息。

下图展示了请求在Spring MVC中所经历的所有站点：

<center>
    ![图5.1-请求经过SpringMVC的各个组件](images\图5.1-请求经过SpringMVC的各个组件.PNG)
    **一路上请求会将信息带到很多站点，并生产期望的结果**
</center>

在请求离开浏览器时**①**，会带有用户所请求内容的信息，至少会包含请求的URL。但是还可能带有其他的信息，例如用户提交的表单信息。

请求到达的第一站是Spring的`DispatcherServlet`。与大多数基于Java的Web框架一样，Spring MVC所有的请求都会通过一个前端控制器（front controller）Servlet。前端控制器是常用的Web应用程序模式，在这里一个单实例的Servlet将请求委托给应用程序的其他组件来执行实际的处理。在Spring MVC中，`DispatcherServlet`就是前端控制器。

`DispatcherServlet`的任务就是将请求发送给Spring MVC控制器（controller）。控制器是一个用于处理请求的Spring组件。在一个典型的应用程序中可能会有多个控制器，`DispatcherServlet`需要知道应该将请求发送给哪个控制器。所以`DispatcherServlet`会查询一个或多个处理器映射器（handler mapping）**②**来确定请求的下一站在哪里。处理器映射器会根据请求所携带的URL信息来进行决策。

一旦选择了合适的控制器，`DispatcherServlet`会将请求发送给选中的控制器**③**。到了控制器，请求会卸下其负载（用户提交的信息）并等待控制器处理这些信息。（实际上，设计良好的控制器本身只处理很少甚至不处理工作，而是将业务逻辑委托给一个或多个服务对象进行处理。）

控制器在完成逻辑处理后，通常会产生一些信息，这些信息需要返回给用户并在浏览器上显示。这些信息被称为模型（model）。不过仅仅给用户返回原始的信息是不够的——这些信息需要以用户友好的方式进行格式化，一般会是HTML。所以，信息需要发送给一个视图（view），通常会是JSP。（当然，现在可能更多的是页面通过Ajax与服务器进行交互，服务器返回JSON数据）

控制器所做的最后一件事就是将模型数据打包，并且标示出用于渲染输出的视图名。它接下来会将请求连同模型和视图名发送回`DispatcherServlet`**④**。

这样，控制器就不会与特定的视图相耦合，传递给`DispatcherServlet`的视图名并不直接表示某个特定的JSP。实际上，它甚至并不能确定视图就是JSP。相反，它仅仅传递了一个逻辑名称，这个名字将会用来查找产生结果的真正视图。`DispatcherServlet`将会使用视图解析器（view resolver）**⑤**来将逻辑视图名匹配为一个特定的视图实现，它可能是也可能不是JSP。

既然`DispatcherServlet`已经知道由哪个视图渲染结果，那请求的任务基本上也就完成了。它的最后一站是视图的实现（可能是JSP）**⑥** ，在这里它交付模型数据。请求的任务就完成了。视图将使用模型数据渲染输出，这个输出会通过响应对象传递给客户端（不会像听上去那样硬编码）**⑦** 。

可以看到，请求要经过很多的步骤，最终才能形成返回给客户端的响应。

#### 5.1.2 搭建Spring MVC

*以下内容代码在工程sia4e-P2_Spring_on_the_web-C05_Building_Spring_web_applications中*。

看上去我们需要配置很多的组成部分。幸好，借助于最近几个Spring新版本的功能增强，开始使用Spring MVC变得非常简单了。现在，我们要使用最简单的方式来配置Spring MVC：所要实现的功能仅限于运行我们所创建的控制器。在第7章中，我们会看一些其他的配置选项。

**配置DispatcherServlet**

`DispatcherServlet`是Spring MVC的核心。在这里请求会第一次接触到框架，它要负责将请求路由到其它的组件中。

按照传统的方式，像DispatcherServlet这样的Servlet会配置在web.xml文件中，这个文件会放到应用的WAR包里面。当然，这是配置DispatcherServlet的方法之一。

例如：

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

但是，借助于Servlet 3规范和Spring 3.1的功能增强，这种方式已经不是唯一的方案了，这也不是我们本章所使用的配置方法。

这里使用JavaConfig的方式进行配置：

```java
package spittr.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

public class SpittrWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        // 指定的配置类
        return new Class<?>[] { WebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        // 将DispatcherServlet映射为“/”
        return new String[] { "/" };
    }
}
```

这个类的名字是`SpittrWebAppInitializer`，它位于名为spittr.config的包中。

我们稍后再对这个类进行介绍，但现在我们只需要知道我们所创建的应用名为Spittr。

要理解`SpittrWebAppInitializer`这个类是如何工作的，我们需要知道继承`AbstractAnnotationConfigDispatcherServletInitializer`的任意类都会自动地配置`DispatcherServlet`和Spring应用上下文，Spring的应用上下问会位于应用程序的Servlet上下文中。

在Servlet 3.0环境中，容器会在类路径中查找实现了`javax.servlet.ServletContainerInitializer`接口的类，如果能发现的话，就会用它来配置Servlet容器。

Spring提供了这个接口的实现，名为`SpringServletContainerInitializer`，这个类反过来又会查找实现`WebApplicationInitializer`的类并将配置的任务交给它们来完成。Spring 3.2引入了一个便利的`WebApplicationInitializer`基础实现，也就是`AbstractAnnotationConfigDispatcherServletInitializer`因为我们的`SpittrWebAppInitializer`继承了`AbstractAnnotationConfigDispatcherServlet-Initializer`（同时也就实现了`WebApplicationInitializer`），因此当部署到Servlet 3.0容器中的时候，容器会自动发现它，并用它来配置Servlet上下文。

尽管它的名字很长，但是`AbstractAnnotationConfigDispatcherServletInitializer`使用起来很简便。我们的`SpittrWebAppInitializer`类重写了三个方法：

- Class<?>[] getRootConfigClasses()
- Class<?>[] getServletConfigClasses()
- String[] getServletMappings()

`getServletMappings()`方法会将一个或多个路径映射到`DispatcherServlet`上。在本例中，**它映射的是“/”，这表示它会是应用的默认Servlet。他会处理进入应用的所有请求。**

为了理解其他的两个方法，我们首先要理解`DispatcherServlet`和一个Servlet监听器（也就是`ContextLoaderListener`）的关系。

当`DispatcherServlet`启动的时候，它会创建Spring应用上下文，并加载配置文件或配置类中所声明的bean。在`getServletConfigClasses()`方法中，我们要求`DispatcherServlet`加载应用上下文时，使用定义在`WebConfig`配置类（使用Java配置）中的bean。

但是在Spring Web应用中，通常还会有另外一个应用上下文。另外的这个应用上下文是由`ContextLoaderListener`创建的。

我们希望`DispatcherServlet`加载包含Web组件的bean，如控制器、视图解析器以及处理器映射，而`ContextLoaderListener`要加载应用中的其他bean。这些bean通常是驱动应用后端的中间层和数据层组件。

实际上，`AbstractAnnotationConfigDispatcherServletInitializer`会同时创建`DispatcherServlet`和`ContextLoaderListener`。`getServletConfigClasses()`方法返回的带有`@Configuration`注解的类将会用来定义`DispatcherServlet`应用上下文中的bean。`getRootConfigClasses()`方法返回的带有`@Configuration`注解的类将会用来配置`ContextLoaderListener`创建的应用上下文中的bean。

（通常情况下，如果我们只是单独使用Spring MVC，不使用IoC、DI、AOP等Spring提供的功能，则只需要配置`DispatcherServlet`即可，而无需配置`ContextLoaderListener`。）

在本例中，根配置定义在`RootConfig`中，`DispatcherServlet`的配置声明在`WebConfig`中。

**需要注意的是，通过`AbstractAnnotationConfigDispatcherServletInitializer`来配置`DispatcherServlet`是传统web.xml方式的替代方案。我们也可以同时包含web.xml和`AbstractAnnotationConfigDispatcherServletInitializer`，但这其实并没有必要。（我们当然可以只使用web.xml进行配置）**

**启用Spring MVC**

启用Spring MVC组件的方法也不仅一种。过去Spring是使用XML进行配置的，这需要使用元素`<mvc:annotation-driven />`来启用注解驱动的Spring MVC。

由于这里我们尽量让搭建过程尽可能简单并基于Java，所以我们不使用这种方法。Spring MVC配置的可选项将在第七章再进行讨论。

我们所能创建的最简单的Spring MVC配置就是一个带有`@EnableWebMvc`注解的类：

```java
package spittr.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@EnableWebMvc
public class WebConfig {

}
```

这样确实能够启用Spring MVC，但我们还有一些问题需要解决：

- 没有配置视图解析器：如果我们没有配置视图解析器（以及处理器映射器、处理器适配器等），那么Spring MVC会提供默认的配置。
- 没有启用组件扫描：这样的结果就是，Spring只能找到显式声明在配置类中的控制器。
- 将`DispatcherServlet`映射为“/”：将`DispatcherServlet`映射为默认的Servlet，所以它会处理所有的默认Servlet，这包括了对静态资源的请求，如js、css、jpg等文件。

`DispatcherServlet`的默认配置如下所示，它定义在spring-webmvc包的DispatcherServlet.properties文件中：

（原书写到默认的视图解析器为`BeanNameViewResolver`，但根据配置文件，这个说法并不正确，默认的视图解析器应该为`InternalResourceViewResolver`）

```text
# Default implementation classes for DispatcherServlet's strategy interfaces.
# Used as fallback when no matching beans are found in the DispatcherServlet context.
# Not meant to be customized by application developers.

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

因此，我们需要在`WebConfig`这个最小的Spring MVC配置上增加一些内容，从而使它变得真正可用：

```java
package spittr.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.DefaultServletHandlerConfigurer;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurerAdapter;
import org.springframework.web.servlet.view.InternalResourceViewResolver;

@Configuration // 表明该类是一个配置类
@EnableWebMvc // 启用Spring MVC
@ComponentScan("spittr.web") // 启用组件扫描
public class WebConfig extends WebMvcConfigurerAdapter {

    // 配置JSP视图解析器
    @Bean
    public ViewResolver viewResolver() {
        InternalResourceViewResolver resolver = new InternalResourceViewResolver();
        resolver.setPrefix("/WEB-INF/views/");
        resolver.setSuffix(".jsp");
        resolver.setExposeContextBeansAsAttributes(true);
        return resolver;
    }
    
    // 配置静态资源的处理
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }
}
```

现在我们配置了注解`@ComponentScan("spittr.web")`，因此框架会扫描spittr.web包及其子包来查找组件。我们所编写的控制器将会带有`@Controller`注解，这会使其成为组件扫描时的候选bean。因此，我们不需要在配置类中显式声明任何的控制器。

接下来我们添加了一个视图解析器（ViewResolver）bean，这里使用`InternalResourceViewResolver`，并配置了查找视图（这里是JSP）时使用的前缀和后缀（例如我们要查找名为home的视图，那么视图解析器会自动去查找/WEB-INF/views/home.jsp）。关于视图解析器，我们会在第六章详细讨论。

最后我们的`WebCondig`类继承了`WebMvcConfigurerAdapter`并重写了其`configureDefaultServletHandling()`方法。通过调用`DefaultServletHandlerConfigurer`的`enable()`方法，我们要求`DispatcherServlet`将对静态资源的请求转发到Servlet容器中的默认Servlet（`org.apache.catalina.servlets.DefaultServlet`）上，而不是其自身来处理。这样做避免了静态资源无法加载的问题。

接下来配置`RootConfig`类。因为本章聚焦于Spring MVC的相关开发，所以这里`RootConfig`类比较简单：

```java
package spittr.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

@Configuration
@ComponentScan(basePackages = { "spittr" }, excludeFilters = {
        @Filter(type = FilterType.ANNOTATION, value = EnableWebMvc.class) })
public class RootConfig {

}
```

#### 5.1.3 Spittr应用简介

>
因为从Twitter借鉴了灵感并且通过Spring来进行实现，所以它就有了一个名字：Spitter。再进一步，应用网站命名中流行的模式，如Flickr，我们去掉字母e，这样的话，我们就将这个应用称为Spittr。

Spittr应用有两个有两个基本的领域概念：

- Spitter：应用的用户
- Spittle：用户发布的简短状态更新

当我们在书中完善Spittr应用的功能时，将会介绍这两个领域概念。在本章中，我们会构建应用的Web层，创建展现Spittle的控制器以及处理用户注册成为Spitter的表单。

下面进入本章的核心内容：使用Spring MVC控制器处理Web请求。

### 5.2 编写基本的控制器

在Spring MVC中，控制器只是方法上添加了`@RequestMapping`注解的类，这个注解声明了它们所要处理的请求。

我们先从简单的控制器入手，假设这个控制器要处理对“/”的请求：

```java
package spittr.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

// 声明该类为一个控制器
@Controller
public class HomeController {

    // 处理对“/”的GET请求
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String home() {
        // 返回的视图名为home
        return "home";
    }
}
```

这个类使用了`@Controller`注解，这个注解是用来声明控制器的。`@Controller`是一个构造型（stereotype）注解，它基于`@Component`注解。在这里，它的目的就是辅助实现组件扫描。因为`HomeController`带有`@Controller`注解，因此组件扫描器会自动找到`HomeController`，并将其声明为Spring应用上下文中的一个bean。

事实上我们也可以直接使用`@Component`，但在表意上会差很多，我们无法确定这个类究竟是什么类型的组件。

`HomeController`唯一的一个方法，也就是`home()`方法，带有`@RequestMapping`注解。它的`value`属性指定了这个方法所要处理的请求路径，`method`属性细化了它所处理的HTTP方法。在本例中，当收到对“/”的HTTP GET请求时，就会调用`home()`方法。

`home()`方法其实并没有做太多的事情：它返回了一个`String`类型的“home”。这个`String`将会被Spring MVC解读为要渲染的视图名称。`DispatcherServlet`会要求视图解析器将这个逻辑名称解析为实际的视图。

鉴于我们配置`InternalResourceViewResolver`的方式，视图名“home”将会解析为“/WEB-INF/views/home.jsp”路径的JSP。

下面定义Spittr应用的首页，home.jsp：

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
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
<title>Spittr</title>
<link rel="stylesheet" type="text/css" href="<c:url value="resources/style.css"/>">
</head>
<body>
    <h1>Welcome to Spittr!</h1>
    <a href="<c:url value="spittles" />">Spittles</a> |
    <a href="<c:url value="spitter/register" />">Register</a>
</body>
</html>
```

这个页面定义在/WEB-INF/views下，它只是欢迎应用的用户，并提供了两个链接：一个是查看Spittle列表，另一个是在应用中进行注册。

#### 5.2.1 测试控制器

下面对我们的控制器`HomeController`进行测试：

```java
package spittr.web;

import static org.junit.Assert.assertEquals;

import org.junit.Test;

public class HomeControllerTest {
    @Test
    public void testHomePage() {
        HomeController controller = new HomeController();
        assertEquals("home", controller.home());
    }
}
```

这个测试很简单，但它只测试了`home()`方法的返回值。它没有站在Spring MVC控制器的视角进行测试。这个测试没有断言当接收到针对“/”的GET请求时会调用`home()`方法。因为它返回的值就是“home”，所以也没有真正判断home是视图的名称。

从Spring 3.2开始，我们可以按照控制器的方式来测试Spring MVC中的控制器了，而不仅仅是作为POJO进行测试。Spring现在包含了一种mock Spring MVC并针对控制器执行HTTP请求的机制。这样的话，在测试控制器的时候，就没有必要再启动Web服务器和Web浏览器了。

重写我们的测试类：

```java
package spittr.web;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.setup.MockMvcBuilders.*;

import org.junit.Test;
import org.springframework.test.web.servlet.MockMvc;

public class HomeControllerTest {
    
    @Test
    public void testHomePage() throws Exception {
        
        HomeController controller = new HomeController();
        
        // 搭建MockMvc
        MockMvc mockMvc = standaloneSetup(controller).build();
        
        // 对“/”执行GET请求，预期得到“home”视图
        mockMvc.perform(get("/")).andExpect(view().name("home"));
        
    }
}
```

新版本的测试更加完整地测试了`HomeController`。这次我们不是直接调用`home()`方法并测试它的返回值，而是发起了对“/”的GET请求，并断言结果视图的名称为home。它首先传递一个`HomeController`实例到`MockMvcBuilders.standaloneSetup()`并调用`build()`来构建MockMvc实例。然后它使用MockMvc实例来执行针对“/”的GET请求并设置期望得到的视图名称。

#### 5.2.2 定义类级别的请求处理

现在对`HomeController`进行重构，我们可以做的一件事就是拆分`@RequestMapping`，并将其路径映射部分放到类级别上：

```java
package spittr.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

// 声明该类为一个控制器
@Controller
// 将控制器映射到“/”
@RequestMapping("/")
public class HomeController {

    // 处理GET请求
    @RequestMapping(method = RequestMethod.GET)
    public String home() {
        // 返回的视图名为home
        return "home";
    }
}
```

在这个新版本的`HomeController`中，路径现在被转移到类级别的`@RequestMapping`上，而HTTP方法依然映射在方法级别上。当控制器在类级别上添加`@RequestMapping`注解时，这个注解会应用到控制器的所有处理器方法上。处理器方法上的`@RequestMapping`注解会对类级别上的`@RequestMapping`的声明进行补充。

就`HomeController`而言，这里只有一个控制器方法。与类级别的`@RequestMapping`合并之后，这个方法的`@RequestMapping`表明`home()`将会处理对“/”路径的GET请求。

当前来看，我们其实并没有修改这个类的功能。

我们还可以对`HomeController`做另外一个变更。`@RequestMapping`的`value`属性能够接受一个`String`类型的数组。到目前为止，我们给它设置的都是一个`String`类型的“/”。但是，我们还可以将它映射到对“/homepage”的请求，只需将类级别的`@RequestMapping`改为如下所示：

```java
package spittr.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

// 声明该类为一个控制器
@Controller
// 将控制器映射到“/”和/homepage
@RequestMapping({ "/", "/homepage" })
public class HomeController {

    // 处理GET请求
    @RequestMapping(method = RequestMethod.GET)
    public String home() {
        // 返回的视图名为home
        return "home";
    }
}
```

现在，`HomeController`的`home()`方法能够映射到对“/”和“/homepage”的GET请求。

#### 5.2.3 传递模型数据到视图中

就编写超级简单的控制器来说，`HomeController`已经是一个不错的样例了。但是大多数的控制器并不是这么简单。在Spittr应用中，我们需要有一个页面展现最近提交的Spittle列表。因此，我们需要一个新的方法来处理这个页面。

我们首先需要定义一个数据访问的Respository，这里我们将定义一个接口：

```java
package spittr.data;

import java.util.List;

public interface SpittleRepository {
    List<Spittle> findSpittles(long max, int count);
}
```

`findSpittles()`方法接受两个参数。其中`max`参数代表所返回的Spittle中，Spittle ID属性的最大值，而`count`参数表明要返回多少个Spittle对象。为了获得最新的20个Spittle对象，我们可以这样调用`findSpittles()`：

```java
List<Spittle> recent = spittleRepository.findSpittles(Long.MAX_VALUE, 20);
```

接下来我们定义`Spittle`类，它的属性包括消息内容、时间戳以及Spittle发布时对应的经纬度：

```java
package spittr;

import java.util.Date;

import org.apache.commons.lang3.builder.EqualsBuilder;
import org.apache.commons.lang3.builder.HashCodeBuilder;

public class Spittle {
    private final Long id;
    private final String message;
    private final Date time;
    private Double latitude;
    private Double longitude;

    public Spittle(String message, Date time) {
        this(message, time, null, null);
    }

    public Spittle(String message, Date time, Double latitude, Double longitude) {
        this.id = null;
        this.message = message;
        this.time = time;
        this.longitude = longitude;
        this.latitude = latitude;
    }

    public Long getId() {
        return id;
    }

    public String getMessage() {
        return message;
    }

    public Date getTime() {
        return time;
    }

    public Double getLatitude() {
        return latitude;
    }

    public Double getLongitude() {
        return longitude;
    }

    @Override
    public boolean equals(Object that) {
        return EqualsBuilder.reflectionEquals(this, that, "id", "time");
    }

    @Override
    public int hashCode() {
        return HashCodeBuilder.reflectionHashCode(this, "id", "time");
    }
}
```

就大部分内容来看，`Spittle`就是一个基本的POJO数据对象——没有什么复杂的。唯一要注意的是，我们使用Apache Common Lang3包来实现`equals()`和`hashCode()`方法。这些方法除了常规的作用以外，当我们为控制器的处理器方法编写测试时，它们也是有用的。

编写`SpittleController`，在模型中放入最新的spittle列表：

```java
package spittr.web;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import spittr.data.SpittleRepository;

@Controller
@RequestMapping("spittles")
public class SpittleController {
    
    private SpittleRepository spittleRepository;
    
    // 注入SpittleRepository
    @Autowired
    public SpittleController(SpittleRepository spittleRepository) {
        this.spittleRepository = spittleRepository;
    }
    
    @RequestMapping(method = RequestMethod.GET)
    public String spittles(Model model) {
        // 将spittleList添加到模型中
        model.addAttribute(spittleRepository.findSpittles(Long.MAX_VALUE, 20));
        // 返回视图名
        return "spittles";
    }
}
```

我们可以看到`SpittleController`有一个构造器，这个构造器使用了`@Autowired`注解，用来注入`SpittleRepository`。这个`SpittleRepository`随后又用在`spittles()`方法中，用来获取最新的spittle列表。

需要注意的是，我们在`spittles()`方法中给定了一个`Model`作为参数。这样，`spittles()`方法就能将Repository中获取到的Spittle列表填充到模型中。`Model`实际上就是一个`Map`（也就是key-value对的集合），它会传递给视图，这样数据就能渲染到客户端了。**当调用`addAttribute()`方法并且不指定key的时候，那么key会根据值的对象类型推断确定。在本例中，因为它是一个`List<Spittle>`，因此，键将会推断为`spittleList`**。最后`spittles()`方法返回“spittles”作为视图的名字。

下面是各个版本的`spittles()`方法：

```java
// 显式声明key
public String spittles(Model model) {
    // 将spittleList添加到模型中
    model.addAttribute("spittleList", spittleRepository.findSpittles(Long.MAX_VALUE, 20));
    // 返回视图名
    return "spittles";
}

// 使用非Spring类型，用Map代替Model
public String spittles(Map model) {
    // 将spittleList添加到模型中
    model.put("spittleList", spittleRepository.findSpittles(Long.MAX_VALUE, 20));
    // 返回视图名
    return "spittles";
}

// 隐式指定
@RequestMapping(method = RequestMethod.GET)
public List<Spittle> spittles() {
    return spittleRepository.findSpittles(Long.MAX_VALUE, 20);
}
```

最后这个版本与其他的版本有些差别。它并没有返回视图名称，也没有显式地设定模型，这个方法返回的是`Spittle`列表。当处理器方法像这样返回对象或集合时，这个值会放到模型中，模型的key会根据其类型推断得出（在本例中，也就是`spittleList`）。而逻辑视图的名称将会根据请求路径推断得出。因为这个方法处理针对“/spittles”的GET请求，因此视图的名称将会是spittles（去掉开头的斜线）。

无论我们选择哪种方式编写`spittles()`方法，所达成的结果都是相同的。模型中会存储一个`Spittle`列表，key为`spittleList`，然后这个列表会发送到名为spittles的视图。

现在，数据已经放到了模型中，在JSP中该如何访问它呢？实际上，当视图是JSP的时候，模型数据会作为请求属性放到请求（`request`）之中。因此，在spittles.jsp文件中可以使用JSTL（JavaServer PagesStandard Tag Library）的`<c:forEach>`标签渲染spittle列表：

```html
<c:forEach items="${spittleList}" var="spittle">
    <li id="spittle_<c:out value="spittle.id"/>">
        <div class="spittleMessage">
            <c:out value="${spittle.message}" />
        </div>
        <div>
            <span class="spittleTime"><c:out value="${spittle.time}" /></span> 
            <span class="spittleLocation"> 
                (<c:out value="${spittle.latitude}" />, <c:out value="${spittle.longitude}" />)
            </span>
        </div>
    </li>
</c:forEach>
```

尽管`SpittleController`很简单，但是它依然比`HomeController`更进一步了。不过，`SpittleController`和`HomeController`都没有处理任何形式的输入。现在，让我们扩展`SpittleController`，让它从客户端接受一些输入。

编写测试类，测试`SpittleController`处理针对`/spittles`的GET请求：

```java
package spittr.web;

import static org.hamcrest.CoreMatchers.hasItems;
import static org.mockito.Mockito.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.setup.MockMvcBuilders.*;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import org.junit.Test;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.web.servlet.view.InternalResourceView;

import spittr.Spittle;
import spittr.data.SpittleRepository;

public class SpittleControllerTest {

    @Test
    public void shouldShowRecentSpittles() throws Exception {

        List<Spittle> expectedSpittles = createSpittleList(20);

        // Mock Repository
        SpittleRepository mockRepository = mock(SpittleRepository.class);

        when(mockRepository.findSpittles(Long.MAX_VALUE, 20)).thenReturn(expectedSpittles);

        SpittleController controller = new SpittleController(mockRepository);
        
        // Mock Spring MVC
        MockMvc mockMvc = standaloneSetup(controller)
                .setSingleView(new InternalResourceView("/WEB-INF/views/spittles.jsp"))
                .build();

        // 对“/spittles”发起GET请求
        mockMvc.perform(get("/spittles"))
                .andExpect(view().name("spittles")) // 期望视图的名称为“spittles”
                .andExpect(model().attributeExists("spittleList")) // 期望模型中含有数据“spittleList”
                .andExpect(model().attribute("spittleList", hasItems(expectedSpittles.toArray()))); // 期望“spittleList”中含有预期的数据
    }

    private List<Spittle> createSpittleList(int count) {
        List<Spittle> spittles = new ArrayList<>();
        for (int i = 0; i < count; i++) {
            spittles.add(new Spittle("Spittle " + i, new Date()));
        }
        return spittles;
    }
}
```

测试首先会创建`SpittleRepository`接口的mock实现，这个实现会从它的`findSpittles()`方法中返回20个`Spittle`对象。然后将这个Repo注入到一个`SpittleController`实例中，然后创建`MockMvc`。

需要注意的是，与`HomeController`不同，这个测试在`MockMvc`构造器上调用了`setSingleView()`。这样的话，**mock框架就不用解析控制器中的视图名了**。在很多场景中，其实没有必要这样做。但是对于这个控制器方法，视图名与请求路径是非常相似的（视图名：spittles，请求路径：/spittles），这样按照默认的视图解析规则时，`MockMvc`就会发生失败（error, not failure），因为无法区分视图路径和控制器的路径。在这个测试中，构建`InternalResourceView`时所设置的实际路径是无关紧要的，但我们将其设置为与`InternalResourceViewResolver`配置一致。

### 5.3 接收请求的输入

Spring MVC允许以多种方式将客户端中的数据传送到控制器的处理器方法中：

- 查询参数（Query Parameter）
- 表单参数（Form Parameter）
- 路径变量（Path Variable）

#### 5.3.1 处理查询参数

下面我们增加一个简单的分页的功能。

在确定该如何实现时，假设我们要查看某一页`Spittle`列表，这个列表会按照最新的`Spittle`在前的方式进行排序。因此，下一页中第一条的ID肯定会早于当前页最后一条的ID。所以，为了显示下一页的`Spittle`，我们需要将一个`Spittle`的ID传入进来，这个ID要恰好小于当前页最后一条`Spittle`的ID。另外，你还可以传入一个参数来确定要展现的`Spittle`数量。

为了实现这个分页的功能，我们所编写的处理器方法要接受如下的参数：

- `max`参数（表明结果中所有Spittle的ID均应该在这个值之前）
- `count`参数（表明在结果中要包含的Spittle数量）

这里我们先编写测试方法，这个方法反映了新`spittles()`方法的功能：

```java
@Test
public void shouldShowPagedSpittles() throws Exception {
    
    List<Spittle> expectedSpittles = createSpittleList(50);
    
    SpittleRepository mockRepository = mock(SpittleRepository.class);
    
    when(mockRepository.findSpittles(238900L, 50)).thenReturn(expectedSpittles);
    
    SpittleController controller = new SpittleController(mockRepository);
    
    // Mock Spring MVC
    MockMvc mockMvc = standaloneSetup(controller)
            .setSingleView(new InternalResourceView("/WEB-INF/views/spittles.jsp"))
            .build();
    
    // 传入max和count参数
    mockMvc.perform(get("/spittles?max=238900&count=50"))
            .andExpect(view().name("spittles")) // 期望视图的名称为“spittles”
            .andExpect(model().attributeExists("spittleList")) // 期望模型中含有数据“spittleList”
            .andExpect(model().attribute("spittleList", hasItems(expectedSpittles.toArray()))); // 期望“spittleList”中含有预期的数据
}
```

这个测试方法对“/spittles”发送GET请求，同时还传入了`max`和`count`参数。它测试
了这些参数存在时的处理器方法，而原来的测试方法则测试了没有这些参数时的情景。这两个测试就绪后，我们就能确保不管控制器发生什么样的变化，它都能够处理这两种类型的请求。

`SpittleController`中的处理器方法要同时处理有参数和没有参数的场景，那我们需要对其进行修改，让它能接受参数，同时，如果这些参数在请求中不存在的话，就使用默认值`Long.MAX_VALUE`和20。`@RequestParam`注解的`defaultValue`属性可以完成这项任务：

```java
@RequestMapping(method = RequestMethod.GET)
public String spittles(
            @RequestParam(value = "max", defaultValue = MAX_LONG_AS_STRING) long max,
            @RequestParam(value = "count", defaultValue = "20") int count, 
            Model model) {
    // 将spittleList添加到模型中
    model.addAttribute("spittleList", spittleRepository.findSpittles(max, count));
    // 返回视图名
    return "spittles";
}
```

现在，如果`max`参数没有指定的话，它将会是`Long`类型的最大值。因为查询参数都是String类型的，因此`defaultValue`属性需要`String`类型的值。因此，使用`Long.MAX_VALUE`是不行的。我们可以将`Long.MAX_VALUE`转换为名为`MAX_LONG_AS_STRING`的`String`类型常量：

```java
// 原书中使用的是private static final String MAX_LONG_AS_STRING = Long.toString(Long.MAX_VALUE);
// 但实际上这样会报错，不能通过编译。原因是@RequestParam的defaultValue属性的值必须是一个常量
// The value for annotation attribute RequestParam.defaultValue must be a constant
private final String MAX_LONG_AS_STRING = Long.MAX_VALUE + "";
```

尽管`defaultValue`属性给定的是`String`类型的值，但是当绑定到方法的`max`参数时，它会转换为`Long`类型。

请求中的查询参数是往控制器中传递信息的常用手段。另外一种方式也很流行，尤其是在构建面向资源的控制器时，这种方式就是将传递参数作为请求路径的一部分。让我们看一下如何将路径变量作为请求路径的一部分，从而实现信息的输入。

#### 5.3.2 通过路径参数接收输入

假设我们的应用程序需要根据给定的ID来展现某一个`Spittle`记录。其中一种方案就是编写处理器方法，通过使用`@RequestParam`注解，让它接受ID作为查询参数：

```java
@RequestMapping(value = "/show", method = RequestMethod.GET)
public String showSpittle(@RequestParam("spittle_id") long spittleId, Model model) {
    model.addAttribute("spittleId", spittleRepository.findOne(spittleId));
    return "spittle";
}
```

这个处理器方法将会处理形如“/spittles/show?spittle_id=12345”这样的请求。尽管这也可以正常工作，但是从面向资源的角度来看这并不理想。在理想情况下，要识别的资源（`Spittle`）应该通过URL路径进行标示，而不是通过查询参数。对“/spittles/12345”发起GET请求要优于对“/spittles/show?spittle_id=12345”发起请求。前者能够识别出要查询的资源，而后者描述的是带有参数的一个操作——本质上是通过HTTP发起的RPC。

将方法改为如下所示：

```java
@RequestMapping(value = "/{spittleId}", method = RequestMethod.GET)
public String showSpittle(@PathVariable("spittleId") long spittleId, Model model) {
    model.addAttribute("spittle", spittleRepository.findOne(spittleId));
    return "spittle";
}
```

这个方法能够处理形如“/spittles/12345”的请求。参数`spittleId`上添加了注解`@PathVariable("spittleId")`，这表明在请求路径中，不管占位符（{}内的内容）部分的值是什么都会传递到处理器方法的`spittleId`参数中作为它的值。

需要注意的是：在样例中`spittleId`这个词出现了好几次：先是在`@RequestMapping`的路径中，然后作为`@PathVariable`属性的值，最后又作为方法的参数名称。因为方法的参数名碰巧与占位符的名称相同，因此我们可以去掉`@PathVariable`中的`value`属性：

```java
@RequestMapping(value = "/{spittleId}", method = RequestMethod.GET)
public String showSpittle(@PathVariable long spittleId, Model model) {
    model.addAttribute("spittle", spittleRepository.findOne(spittleId));
    return "spittle";
}
```

如果`@PathVariable`中没有`value`属性的话，它会假设占位符的名称与方法的参数名相同。这能够让代码稍微简洁一些，因为不必重复写占位符的名称了。但需要注意的是，如果你想要重命名参数时，必须要同时修改占位符的名称，使其互相匹配。

下面针对形如“/spittles/12345”的请求编写测试方法：

```java
@Test
public void testSpittle() throws Exception {
    
    Spittle expectedSpittle = new Spittle("Hello", new Date());
    
    SpittleRepository mockRepository = mock(SpittleRepository.class);
    
    when(mockRepository.findOne(12345)).thenReturn(expectedSpittle);
    
    SpittleController controller = new SpittleController(mockRepository);
    
    MockMvc mockMvc = standaloneSetup(controller).build();
    
    mockMvc.perform(get("/spittles/12345"))
            .andExpect(view().name("spittle")) // 期望视图的名称为“spittle”
            .andExpect(model().attributeExists("spittle")) // 期望模型中含有数据“spittle”
            .andExpect(model().attribute("spittle", expectedSpittle)); // 期望“spittle”与expectedSpittle相等
}
```
这个测试中最重要的部分是最后几行，它对“/spittles/12345”发起GET请求，然后断言视图的名称是`spittle`，并且预期的`Spittle`对象放到了模型之中。

最后编写spittle视图：

```html
<body>
    <div class="spittleView">
        <div class="spittleMessage">
            <c:out value="${spittle.message}" />
        </div>
        <div>
            <span class="spittleTime"><c:out value="${spittle.time}" /></span>
        </div>
    </div>
</body>
```

如果传递请求中少量的数据，那查询参数和路径变量是很合适的。但通常我们还需要传递很多的数据（也许是表单提交的数据），那查询参数显得有些笨拙和受限了。下面让我们来看一下如何编写控制器方法来处理表单提交。

### 5.4 处理表单

Web应用的功能通常并不局限于为用户推送内容。大多数的应用允许用户填充表单并将数据提交回应用中，通过这种方式实现与用户的交互。像提供内容一样，Spring MVC的控制器也为表单处理提供了良好的支持。

在Spittr应用中，我们需要有个表单让新用户进行注册。`SpitterController`是一个新的控制器，目前只有一个请求处理的方法来展现注册表单：

```java
package spittr.web;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
@RequestMapping("/spitter")
public class SpitterController {
    // 处理对“/spitter/register”的GET请求
    @RequestMapping(value = "/register", method = RequestMethod.GET)
    public String showRegistrationForm() {
        return "registerForm";
    }
}
```

这是一个简单的方法，没有任何输入并且只是返回名为registerForm的逻辑视图。按照我们配置`InternalResourceViewResolver`的方式，这意味着将会使用“/WEB-INF/views/registerForm.jsp”这个JSP来渲染注册表单。

对应的测试方法：

```java
@Test
public void shouldShowRegistration() throws Exception {
    
    SpitterController controller = new SpitterController();
    
    MockMvc mockMvc = standaloneSetup(controller).build();
    
    mockMvc.perform(get("/spitter/register")).andExpect(view().name("registerForm"));
}
```

这个测试方法与首页控制器的测试非常类似。它对“/spitter/register”发送GET请求，然后断言结果的视图名为registerForm。

因为视图的名称为registerForm，所以JSP的名称需要是registerForm.jsp，下面创建这个JSP：

```html
<body>
    <h1>Register</h1>
    <form action="spitter/register" method="POST">
        <label>
            First Name:
            <input type="text" name="firstName" />
        </label>
        <br>
        <label>
            Last Name:
            <input type="text" name="lastName" />
        </label>
        <br>
        <label>
            Username:
            <input type="text" name="username" />
        </label>
        <br>
        <label>
            Password:
            <input type="text" name="password" />
        </label>
        <br>
        <input type="submit" value="Register" />
    </form>
</body>
```

可以看到，这个JSP非常基础。它的HTML表单域中记录用户的名字、姓氏、用户名以及密码，然后还包含一个提交表单的按钮。

（原书中的`<form>`标签没有设置`action`属性，当不设置时，表单会提交到与展现时相同的URL路径
上。也就是说，它会提交到“/spitter/register”上。也就是说会回到我们的`SpitterController`，这里为了代码易读，添加了`action`属性）

#### 5.4.1 编写表单的控制器

控制器需要接受表单数据并将表单数据保存为`Spitter`对象。最后，为了防止重复提交（用户点击浏
览器的刷新按钮有可能会发生这种情况），应该将浏览器重定向到新创建用户的基本信息页面。

重新编写`SpitterController`：

```java
@Controller
@RequestMapping("/spitter")
public class SpitterController {
    
    private SpitterRepository spitterRepository;
    
    @Autowired
    public SpitterController(SpitterRepository spitterRepository) {
        this.spitterRepository = spitterRepository;
    }
    
    // 处理对“/spitter/register”的GET请求
    @RequestMapping(value = "/register", method = RequestMethod.GET)
    public String showRegistrationForm() {
        return "registerForm";
    }
    
    // 处理对“/spitter/register”的POST请求
    @RequestMapping(value = "/register", method = RequestMethod.POST)
    public String processRegistration(Spitter spitter) {
        
        // 保存Spitter
        spitterRepository.save(spitter);
        
        // 重定向到基本信息页
        return "redirect:/spitter/" + spitter.getUsername();
    }
}
```

我们之前创建的`showRegistrationForm()`方法依然还在，不过请注意新创建的`processRegistration()`方法，它接受一个`Spitter`对象作为参数。这个对象有`firstName`、`lastName`、`username`和`password`属性，这些属性将会使用请求中同名的参数进行填充。这种参数传递方法要求参数名和属性名必须相同。

`processRegistration()`方法会进而调用`SpitterRepository`的`save()`方法，`SpitterRepository`是在`SpitterController`的构造器中注入进来的。

`processRegistration()`方法做的最后一件事就是返回一个`String`类型，用来指定视图。但是这个视图格式和以前我们所看到的视图有所不同。这里不仅返回了视图的名称供视图解析器查找目标视图，而且返回的值还带有重定向的格式。

当`InternalResourceViewResolver`看到视图格式中的“redirect:”前缀时，它就知道要将其解析为重定向。在本例中，它将会重定向到用户基本信息的页面。例如，如果`Spitter.username`属性的值为“jbauer”，那么视图将会重定向到“/spitter/jbauer”。

除了“redirect:”，`InternalResourceViewResolver`还能识别“forward:”前缀。当它发现视图格式中以“forward:”作为前缀时，请求将会前往（forward）指定的URL路径，而不再是重定向。

下面编写测试类，由于我们的`SpitterController`没有显式定义无参构造器，所以测试方法`shouldShowRegistration()`也要重写：

```java
package spittr.web;

import org.junit.Test;
import org.springframework.test.web.servlet.MockMvc;

import spittr.Spitter;
import spittr.data.SpitterRepository;

import static org.mockito.Mockito.atLeastOnce;
import static org.mockito.Mockito.mock;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.redirectedUrl;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.view;
import static org.springframework.test.web.servlet.setup.MockMvcBuilders.*;

public class SpitterControllerTest {

    @Test
    public void shouldShowRegistration() throws Exception {

        SpitterRepository mockRepository = mock(SpitterRepository.class);

        SpitterController controller = new SpitterController(mockRepository);

        MockMvc mockMvc = standaloneSetup(controller).build();

        mockMvc.perform(get("/spitter/register")).andExpect(view().name("registerForm"));
    }

    @Test
    public void shouldProcessRegistration() throws Exception {
        
        SpitterRepository mockRepository = mock(SpitterRepository.class);
        
        Spitter unsaved = new Spitter("jbauer", "24hours", "Jack", "Bauer");
        Spitter saved = new Spitter(24L, "jbauer", "24hours", "Jack", "Bauer");
        when(mockRepository.save(unsaved)).thenReturn(saved);

        SpitterController controller = new SpitterController(mockRepository);
        MockMvc mockMvc = standaloneSetup(controller).build();

        mockMvc.perform(post("/spitter/register")
                .param("firstName", "Jack")
                .param("lastName", "Bauer")
                .param("username", "jbauer")
                .param("password", "24hours")
                .param("email", "jbauer@ctu.gov"))
                .andExpect(redirectedUrl("/spitter/jbauer"));

        verify(mockRepository, atLeastOnce()).save(unsaved);
    }
}
```

测试方法对“/spitter/register”发起了一个POST请求。作为请求的一部分，用户信息以参数的形式放到`request`中，从而模拟提交的表单。

因为我们重定向到了用户基本信息页面，那么我们应该往`SpitterController`中添加一个处理器方法，用来处理对基本信息页面的请求。如下的`showSpitterProfile()`将会完成这项任务：

```java
// 处理显示用户信息的请求
@RequestMapping(value = "/{username}", method = RequestMethod.GET)
public String showSpitterProfile(@PathVariable String username, Model model) {
    Spitter spitter = spitterRepository.findByUsername(username);
    model.addAttribute("spitter", spitter);
    return "profile";
}
```

`SpitterRepository`通过用户名获取一个`Spitter`对象，`showSpitterProfile()`得到这个对象并将其添加到模型中，然后返回profile，也就是基本信息页面的逻辑视图名。像本章展现的其他视图一样，现在的基本信息视图非常简单：

```html
<body>
    <h1>Your Profile</h1>
    <c:out value="${spitter.username}" />
    <br />
    <c:out value="${spitter.firstName}" />
    <c:out value="${spitter.lastName}" />
    <br />
    <c:out value="${spitter.email}" />
</body>
```

如果表单中没有发送`username`或`password`的话，会发生什么情况呢？或者说，如果firstName或`lastName`的值为空或太长的话，又会怎么样呢？接下来，让我们看一下如何为表单提交添加校验，从而避免数据呈现的不一致性。

#### 5.4.2 校验表单

如果用户在提交表单的时候，`username`或`password`文本域为空的话，那么将会导致在新建`Spitter`对象中，username或`password`是空的`String`。至少这是一种怪异的行为。如果这种现象不处理的话，这将会出现安全问题，因为不管是谁只要提交一个空的表单就能登录应用。

同时，我们还应该阻止用户提交空的`firstName`和（或）`lastName`，使应用仅在一定程度上保持匿名性。有个好的办法就是限制这些输入域值的长度，保持它们的值在一个合理的长度范围，避免这些输入域的误用。

比较初级的校验方式是在`processRegistration()`方法中手工添加代码来检查值的合法性，如果值不合法，就将请求重新转会到注册页面。这种方法需要我们在方法中增加大量的if-else语句。

与其让校验逻辑弄乱我们的处理器方法，还不如使用Spring对Java校验API（Java Validation API，又称JSR-303）的支持。从Spring 3.0开始，在Spring MVC中提供了对Java校验API的支持。在Spring MVC中要使用Java校验API的话，并不需要什么额外的配置。只要保证在类路径下包含这个Java API的实现即可，比如Hibernate Validator。

Java校验API定义了多个注解，这些注解可以放到属性上，从而限制
这些属性的值：

常用验证注解：

序号 | 注解 | 说明
-----|-----|-----
1 | @AssertFalse | 验证注解的元素值是false
2 | @AssertTrue | 验证注解的元素值是true
3 | @DecimalMax(value=x) | 验证注解的元素值小于等于指定的十进制value值
4 | @DecimalMin(value=x) | 验证注解的元素值大于等于指定的十进制value值
5 | @Digits(integer=整数位数, fraction=小数位数) | 验证注解的元素值的整数位数和小数位数上限
6 | @Max(value=x) | 验证注解的元素值小于等于指定的value值
7 | @Min(value=x) | 验证注解的元素值大于等于指定的value值
8 | @NotNull | 验证注解的元素值不是null
9 | @Null | 验证注解的元素值是null
10 | @Future | 验证注解的元素值（日期类型）比当前时间晚
11 | @Past | 验证注解的元素值（日期类型）比当前时间早
12 | @Pattern(regex=正则表达式) | 验证注解的元素值与指定的正则表达式匹配
13 | @Size(min=最小值, max=最大值) | 验证注解的元素值在min和max指定区间（包含）之内，如字符长度、集合大小
14 | @Valid | 验证关联的对象，如账户对象里有一个订单对象，指定验证订单对象
15 | @NotEmpty | 验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0）
16 | @Range(min=最小值, max=最大值) | 验证注解的元素值在最小值和最大值之间
17 | @NotBlank | 验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，该注解只应用于字符串且在比较时会去除字符串两端的空格
18 | @Length(min=下限 ,max=上限) | 验证注解的元素值长度在min和max区间内 
19 | @Email | 验证注解的元素值是Email，也可以通过正则表达式和flag指定自定义的email格式

上述的注解有些是Hibernate Validation提供的，有些是Java自己定义的。

**`@NotNull`、`@NotEmpty`和`@NotBlank`的区别**：

```java
String name = null;
@NotNull:  false
@NotEmpty: false 
@NotBlank: false 

String name = "";
@NotNull:  true
@NotEmpty: false
@NotBlank: false

String name = " ";
@NotNull:  true
@NotEmpty: true
@NotBlank: false

String name = "Great answer!";
@NotNull:  true
@NotEmpty: true
@NotBlank: true
```

通常情况下：

- @NotNull 用在基本类型的包装类型上
- @NotEmpty 用在集合类上面
- @NotBlank 用在String上面

请考虑要添加到`Spitter`域上的限制条件，似乎需要使用`@NotBlank`和`@Size`注解。我们所要做的事情就是将这些注解添加到`Spitter`的属性上。如下的程序清单展现了`Spitter`类，它的属性已经添加了校验注解。

（原书这里使用的是`@NotNull`和`@Size`搭配来校验`username`等`String`类型的参数，但这样无法避免提交`"   "`也会通过的问题。）

```java
package spittr;

import javax.validation.constraints.Size;

import org.apache.commons.lang3.builder.EqualsBuilder;
import org.apache.commons.lang3.builder.HashCodeBuilder;
import org.hibernate.validator.constraints.NotBlank;

public class Spitter {

    private Long id;

    @NotBlank
    @Size(min = 5, max = 16)
    private String username;

    @NotBlank
    @Size(min = 5, max = 25)
    private String password;

    @NotBlank
    @Size(min = 2, max = 30)
    private String firstName;

    @NotBlank
    @Size(min = 2, max = 30)
    private String lastName;

    public Spitter() {
    }

    public Spitter(String username, String password, String firstName, String lastName) {
        this(null, username, password, firstName, lastName);
    }

    public Spitter(Long id, String username, String password, String firstName, String lastName) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return "Spitter [id=" + id + ", username=" + username + ", password=" + password + ", firstName=" + firstName
                + ", lastName=" + lastName + "]";
    }

    @Override
    public boolean equals(Object that) {
        return EqualsBuilder.reflectionEquals(this, that, "firstName", "lastName", "username", "password");
    }

    @Override
    public int hashCode() {
        return HashCodeBuilder.reflectionHashCode(this, "firstName", "lastName", "username", "password");
    }
}
```

添加完上述注解，对于我们的应用来说，这意味着用户必须要填完注册表单，且值的长度要在给定的范围内。

接下来修改`processRegistration()`方法来应用校验功能：

```java
@RequestMapping(value = "/register", method = RequestMethod.POST)
public String processRegistration(@Validated Spitter spitter, BindingResult br) {

    List<ObjectError> errors = br.getAllErrors();

    // 如果发生验证异常，则该List的size > 0
    if (errors.size() > 0) {
        return "registerForm";
    }

    // 保存Spitter
    spitterRepository.save(spitter);

    // 重定向到基本信息页
    return "redirect:/spitter/" + spitter.getUsername();
}
```

由于这里使用的验证器为bean对象验证器，所以对于要验证的参数数据需要打包后由处理器方法以bean形参类型的方式接收，并由`@Validated`注解标注。*注意，不能将该注解用于`String`类型与基本类型的形参前。*

紧跟着`@Validated`所注解的形参后面是一个`BindingResult`类型的形参。通过该形参可获取到所有验证异常的信息。

通过在代码中检查是否发生验证异常，从而决定是否要返回到注册页面。如果没有错误的话，`Spitter`对象将会通过`SpitterRepository`进行保存，控制器会像之前那样重定向到基本信息页面。

（原书这里使用的注解是`@Valid`，这个注解与`@Validated`的区别在于前者是javax提供的，后者是Spring提供的，二者的大致用法相同。书中这里没有使用`BindingResult`，而是使用了一个`Errors`类型（不是Java中的`Error`，而是Spring框架内的一个接口）的形参，并在方法内检查`errors.hasErrors()`，从而判断是否要返回到注册页面。事实上`BindingResult`接口继承了`Errors`接口。）

### 5.5 小结

>
在本章中，我们为编写应用程序的Web部分开了一个好头。可以看到，Spring有一个强大灵活的Web框架。借助于注解，Spring MVC提供了近似于POJO的开发模式，这使得开发处理请求的控制器变得非常简单，同时也易于测试。
>
当编写控制器的处理器方法时，Spring MVC极其灵活。概括来讲，如果你的处理器方法需要内容的话，只需将对应的对象作为参数，而它不需要的内容，则没有必要出现在参数列表中。这样，就为请求处理带来了无限的可能性，同时还能保持一种简单的编程模型。
>
尽管本章中的很多内容都是关于控制器的请求处理的，但是渲染响应同样也是很重要的。我们通过使用JSP的方式，简单了解了如何为控制器编写视图。但是就Spring MVC的视图来说，它并不限于本章所看到的简单JSP。
>
在接下来的第6章中，我们将会更深入地学习Spring视图，包括如何在JSP中使用Spring标签库。我们还会学习如何借助Apache Tiles为视图添加一致的布局结构。同时，还会了解Thymeleaf，这是一个很有意思的JSP替代方案，Spring为其提供了内置的支持。

## 第六章 渲染Web视图

本章内容：

- 将模型数据渲染为HTML
- 使用JSP视图
- 通过tiles定义视图布局
- 使用Thymeleaf视图

上一章主要关注于如何编写处理Web请求的控制器。我们也创建了一些简单的视图，用来渲染控制器产生的模型数据，但我们并没有花太多时间讨论视图，也没有讨论控制器完成请求到结果渲染到用户的浏览器中的这段时间内到底发生了什么，而这正是本章的主要内容。

### 6.1 理解视图解析

上一章中，我们所编写的控制器方法没有直接产生浏览器中渲染所需的HTML。这些方法只是将一些数据填充到模型中，然后将模型传递给一个用来渲染的视图。这些方法会返回一个String类型的值，这个值是视图的逻辑名称，不会直接引用具体的视图实现。尽管我们也编写了几个简单的JavaServer Page（JSP）视图，但是控制器并不关心这些。

将控制器中请求处理的逻辑和视图中的渲染实现解耦是Spring MVC的一个重要特性。如果控制器中的方法直接负责产生HTML的话，就很难在不影响请求处理逻辑的前提下，维护和更新视图。控制器方法和视图的实现会在模型内容上达成一致，这是两者的最大关联，除此之外，两者应该保持足够的距离。

但是，如果控制器只通过逻辑视图名来了解视图的话，那Spring该如何确定使用哪一个视图实现来渲染模型呢？这就是Spring视图解析器的任务了。

上一章中，我们使用了名为`InternalResourceViewResolver`的视图解析器。在它的配置中，为了得到视图的名字，会使用“/WEBINF/views/”前缀和“.jsp”后缀，从而确定来渲染模型的JSP文件的物理位置。

下面我们来看一下视图解析的基础只是以及Spring提供的其它视图解析器。

Spring MVC定义了一个名为`ViewResolver`的接口：

```java
public interface ViewResolver {
    View resolveViewName(String viewName, Locale locale) throws Exception;
}
```

当给`resolveViewName()`方法传入一个视图名和`Locale`对象时，它会返回一个`View`实例。`View`是另外一个接口：

```java
public interface View {

    String RESPONSE_STATUS_ATTRIBUTE = View.class.getName() + ".responseStatus";

    String PATH_VARIABLES = View.class.getName() + ".pathVariables";

    String SELECTED_CONTENT_TYPE = View.class.getName() + ".selectedContentType";

    String getContentType();

    void render(Map<String, ?> model, HttpServletRequest request, HttpServletResponse response) throws Exception;

}
```

`View`接口的任务就是接受模型以及`HttpServletRequest`、`HttpServletResponse`对象，并将输出结果渲染到响应中。

这看起来非常简单。我们所需要做的就是编写`ViewResolver`和`View`的实现，将要渲染的内容放到响应中，进而展现到用户的浏览器中。对吧？

实际上，我们并不需要这么麻烦。尽管我们可以编写`ViewResolver`和`View`的实现，在有些特定的场景下，这样做也是有必要的，但是一般来讲，我们并不需要关心这些接口。我在这里提及这些接口只是为了让你对视图解析内部如何工作有所了解。Spring提供了多个内置的实现，它们能够适应大多数的场景：

视图解析器 | 描述
-----|-----
`BeanNameViewResolver` | 将视图解析为Spring应用上下文中的bean，其中bean的ID与视图的名字相同
`ContentNegotiatingViewResolver` | 通过考虑客户端需要的内容类型来解析视图，委托给另外一个能够产生对应内容类型的视图解析器
`FreeMarkerViewResolver` | 将视图解析为FreeMarker模板
`InternalResourceViewResolver` | 将视图解析为Web应用的内部资源（一般为JSP）
`JasperReportsViewResolver` | 将视图解析为JasperReports定义
`ResourceBundleViewResolver` | 将视图解析为资源bundle（一般为属性文件）
`TilesViewResolver` | 将视图解析为Apache Tile定义，其中tile ID与视图名称相同。
`UrlBasedViewResolver` | 直接根据视图的名称解析视图，视图的名称会匹配一个物理视图的定义
`VelocityLayoutViewResolver` | 将视图解析为Velocity布局，从不同的Velocity模板中组合页面
`VelocityViewResolver` | 将视图解析为Velocity模板
`XmlViewResolver` | 将视图解析为特定XML文件中的bean定义。
`XsltViewResolver` | 将视图解析为XSLT转换后的结果

这里我们不会逐一介绍上述所有的视图解析器，因为在大多数应用中，我们只会用到其中很少的一部分。

### 6.2 创建JSP视图

（JSP基本上快要死透了，现在的主流是使用Ajax和JSON进行前后端交互了）

JavaServer Pages作为Java Web应用程序的视图技术已经超过15年了。尽管开始的时候它很丑陋，只是类似模板技术（如Microsoft的Active Server Pages）的Java版本，但JSP这些年在不断进化，包含了对表达式语言和自定义标签库的支持。

Spring提供了两种支持JSP视图的方式：

- `InternalResourceViewResolver`可以将视图名解析为JSP文件。另外，如果在你的JSP页面中使用了JSP标准标签库（JavaServer Pages Standard Tag Library，JSTL）的话，InternalResourceViewResolver能够将视图名解析为JstlView形式的JSP文件，从而将JSTL本地化和资源bundle变量暴露给JSTL的格式化（formatting）和信息（message）标签。
- Spring提供了两个JSP标签库，一个用于表单到模型的绑定，另一个提供了通用的工具类特性。

尽管Spring还有其他的几个视图解析器都能将视图名映射为JSP文件，但就这项任务来讲，`InternalResourceViewResolver`是最简单和最常用的视图解析器。

接下来，我们将会更加仔细地了解`InternalResourceViewResolver`，看看如何让它完全听命于我们。

#### 6.2.1 配置适用于JSP的视图解析器

有一些视图解析器，如`ResourceBundleViewResolver`会直接将逻辑视图名映射为特定的`View`接口实现，而`InternalResourceViewResolver`所采取的方式并不那么直接。它遵循一种约定，会在视图名上添加前缀和后缀，进而确定一个Web应用中视图资源的物理路径（事实上我们也可以在控制器方法中使用视图的物理地址全名，例如“/WEB-INF/views/home.jsp”，而不定义前缀后缀，在此情况下，前后缀均为空字符串）。

考虑一个简单的场景，假设逻辑视图名为home。通用的实践是将JSP文件放到Web应用的WEB-INF目录下，防止对它的直接访问。如果我们将所有的JSP文件都放在“/WEB-INF/views/”目录下，并且home页的JSP名为home.jsp，那么我们可以确定物理视图的路径就是逻辑视图名home再加上“/WEB-INF/views/”前缀和“.jsp”后缀。

<center>
    ![图6.1-InternalResourceViewResolver添加前后缀](images\图6.1-InternalResourceViewResolver添加前后缀.PNG)
    **InternalResourceViewResolver解析视图时会在视图名上添加前缀和后缀.PNG**
</center>

当使用`@Bean`注解时，我们可以按照如下方式配置，使其在解析视图时遵循上述的约定：

```java
@Bean
public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    return resolver;
}
```

如果使用XML的方式，可以进行如下配置：

```xml
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/" />
    <property name="suffix" value=".jsp" />    
</bean>
```

进行上述配置后，`InternalResourceViewResolver`就会将逻辑视图名解析为JSP文件。

- home将会解析为“/WEB-INF/views/home.jsp”
- productList将会解析为“/WEB-INF/views/productList.jsp”
- books/detail将会解析为“/WEB-INF/views/books/detail.jsp”

重点看一下最后一个样例。当逻辑视图名中包含斜线时，这个斜线也会带到资源的路径名中。因此，它会对应到`prefix`属性所引用目录的子目录下的JSP文件。这样的话，我们就可以很方便地将视图模板组织为层级目录结构，而不是将它们都放到同一个目录之中。

**解析JSTL视图**

到目前为止，我们对`InternalResourceViewResolver`的配置都很基础和简单。它最终会将逻辑视图名解析为`InternalResourceView`实例，这个实例会引用JSP文件。但是如果这些JSP使用JSTL标签来处理格式化和信息的话，那么我们会希望`InternalResourceViewResolver`将视图解析为`JstlView`。

JSTL的格式化标签需要一个`Locale`对象，以便于恰当地格式化地域相关的值，如日期和货币。信息标签可以借助Spring的信息资源和`Locale`，从而选择适当的信息渲染到HTML之中。通过解析`JstlView`，JSTL能够获得`Locale`对象以及Spring中配置的信息资源。

如果想让`InternalResourceViewResolver`将视图解析为`JstlView`，而不是`InternalResourceView`的话，那么我们只需设置它的`viewClass`属性即可：

```java
@Bean
public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    resolver.setViewClass("org.springframework.web.servlet.view.JstlView");
    return resolver;
}
```

使用XML配置：

```xml
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/views/" />
    <property name="suffix" value=".jsp" />    
    <property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
</bean>
```

不管使用Java配置还是使用XML，都能确保JSTL的格式化和信息标签能够获得`Locale`对象以及Spring中配置的信息资源。

#### 6.2.2 使用Spring的JSP库

*以下内容代码在工程sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-01_jsp中。*

当为JSP添加功能时，标签库是一种很强大的方式，能够避免在脚本块中直接编写Java代码。Spring提供了两个JSP标签库，用来帮助定义Spring MVC Web的视图。其中一个标签库会用来渲染HTML表单标签，这些标签可以绑定模型中的某个属性。另外一个标签库包含了一些工具类标签，我们随时都可以非常便利地使用它们。

**将表单绑定到模型上**

Spring的表单绑定JSP标签库包含了14个标签，它们中的大多数都用来渲染HTML中的表单标签。但是，它们与原生HTML标签的区别在于它们会绑定模型中的一个对象，能够根据模型中对象的属性填充值。标签库中还包含了一个为用户展现错误的标签，它会将错误信息渲染到最终的HTML之中。

为了使用表单绑定库，我们需要在JSP页面中对其进行声明：

```jsp
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="sf" %>
```

这里使用前缀“sf”。

下面时Spring提供的14个相关标签：

JSP标签 | 描述
----- | -----
`<sf:checkbox>` | 渲染成一个HTML`<input>`标签，其中`type`属性设置为`checkbox`
`<sf:checkboxes>` | 渲染成多个HTML`<input>`标签，其中`type`属性设置为`checkbox`
`<sf:errors>` | 在一个HTML`<span>`中渲染输入域的错误
`<sf:form>` | 渲染成一个HTML`<form>`标签，并为其内部标签暴露绑定路径，用于数据绑定
`<sf:hidden>` | 渲染成一个HTML`<input>`标签，其中`type`属性设置为`hidden`
`<sf:input>` | 渲染成一个HTML`<input>`标签，其中`type`属性设置为`text`
`<sf:label>` | 渲染成一个HTML`<label>`标签
`<sf:option>` | 渲染成一个HTML`<option>`标签，其`selected`属性根据所绑定的值进行设置
`<sf:options>` | 按照绑定的集合、数组或`Map`，渲染成一个HTML`<option>`标签的列表
`<sf:password>` | 渲染成一个HTML`<input>`标签，其中`type`属性设置为`password`
`<sf:radiobutton>` | 渲染成一个HTML`<input>`标签，其中`type`属性设置为`radio`
`<sf:radiobuttons>` | 渲染成多个HTML`<input>`标签，其中`type`属性设置为`radio`
`<sf:select>` | 渲染为一个HTML`<select>`标签
`<sf:textarea>` | 渲染为一个HTML`<textarea>`标签

要在一个样例中介绍所有的这些标签是很困难的，如果一定要这样做的话，肯定也会非常牵强。就Spittr样例来说，我们只会用到适合于Spittr应用中注册表单的标签。具体来讲，也就是`<sf:form>`、`<sf:input>`和`<sf:password>`。

修改registerForm.jsp的表单：

```html
<sf:form action="spitter/register" method="POST" commandName="spitter">
    First Name:<sf:input path="firstName" />
    <br/>
    Last Name: <sf:input path="lastName" />
    <br/>
    Username: <sf:input path="username" />
    <br/>
    Password: <sf:password path="password" />
    <br/>
    <input type="submit" value="Register" />
</sf:form>
```

`<sf:form>`会渲染会一个HTML`<form>`标签，但它也会通过`commandName`属性构建针对某个模型对象的上下文信息。在其他的表单绑定标签中，会引用这个模型对象的属性。

我们在表单中将`commandName`属性设置为spitter，因此，在模型中必须要有一个key为spitter的对象，否则，表单不能正常渲染（报错）。这意味着我们需要修改一下`SpitterController`，以确保模型中存在以spitter为key的`Spitter`对象：

```java
@RequestMapping(value = "/register", method = RequestMethod.GET)
public String showRegistrationForm(Model model) {
    model.addAttribute("spitter", new Spitter());
    return "registerForm";
}
```

修改后的`showRegistrationForm()`方法中，新增了一个`Spitter`实例到模型中。

回到表单，前四个输入域将HTML `<input>`标签改成了`<sf:input>`。这个标签会渲染成一个HTML`<input>`标签，并且`type`属性将会设置为`text`。我们在这里设置了`path`属性，`<input>`标签的`value`属性值将会设置为模型对象中`path`属性所对应的值。例如，如果在模型中`Spitter`对象的`firstName`属性值为Jack，那么`<sf:input path="firstName"/>`所渲染的`<input>`标签中，会存在`value="Jack"`。

对于password输入域，我们使用`<sf:password>`来代替`<sf:input>`。`<sf:password>`与`<sf:input>`类似，但是它所渲染的HTML`<input>`标签中，会将`type`属性设置为`password`，这样当输入的时候，它的值不会直接明文显示。

从Spring 3.1开始，`<sf:input>`标签能够允许我们指定`type`属性，这样的话，除了其他可选的类型外，还能指定HTML 5特定类型的文本域，如`date`、`range`和`email`。例如，我们可以按照如下的方式指定`email`域：

```html
Email: <sf:input path="email" type="email" /><br/>
```

现在假设有个用户提交表单，但其First Name不合法，校验失败后请求会回到注册表单，此时表单中的First Name部分会如下所示（假设在用户在First Name上只填写了一个字母“J”）：

```html
First Name:<input id="firstName" name="firstName" type="text" value="J"><br/>
```

相对于标准的HTML标签，使用Spring的表单绑定标签能够带来一定的功能提升，在校验失败后，表单中会预先填充之前输入的值。（当然，这个功能我们也可以用其它方法来实现）但是，这依然没有告诉用户错在什么地方。为了指导用户矫正错误，我们需要使用`<sf:errors>`。

**展现错误**

如果存在校验错误的话，请求中会包含错误的详细信息，这些信息是与模型数据放到一起的。我们所需要做的就是到模型中将这些数据抽取出来，并展现给用户。`<sf:errors>`能够让这项任务变得很简单。

修改表单页面：

```html
<sf:form action="spitter/register" method="POST" commandName="spitter">
    First Name: <sf:input path="firstName" />
    <sf:errors path="firstName" />
    <br />
    Last Name: <sf:input path="lastName" />
    <sf:errors path="lastName" />
    <br />
    Username: <sf:input path="username" />
    <sf:errors path="username" />
    <br />
    Password: <sf:password path="password" />
    <sf:errors path="password" />
    <br />
    <input type="submit" value="Register" />
</sf:form>
```

在这里，它的`path`属性设置成了`firstName`，也就是指定了要显示`Spitter`模型对象中哪个属性的错误。如果`firstName`属性没有错误的话，那么`<sf:errors>`不会渲染任何内容。但如果有校验错误的话，那么它将会在一个HTML`<span>`标签中显示错误信息。

例如，如果用户提交字母“J”作为名字的话，那么如下的HTML片段就是针对First Name输入域所显示的内容：

```html
First Name: <input id="firstName" name="firstName" type="text" value="J"/>
<span id="firstName.errors">size must be between 2 and 30</span>
```

由于这里的信息会按照地区和语言设置给出，所以我们这里实际上得到的HTML代码应该为：

```html
First Name: <input id="firstName" name="firstName" type="text" value="J"/>
<span id="firstName.errors">个数必须在2和30之间</span>
```

现在，我们已经可以为用户展现错误信息，这样他们就能修正这些错误了。我们可以更进一步，修改错误的样式，使其更加突出显示。为了做到这一点，可以设置`cssClass`属性：

```html
<sf:errors path="firstName" cssClass="error" />
```

现在errors的`<span>`会有一个值为`error`的`class`属性。剩下需要做的就是为这个类定义CSS样式（将如下代码添加到style.css中）。如下就是一个简单的CSS样式，它会将错误信息渲染为红色：

```css
span.error {
    color: red
}
```

在输入域的旁边展现错误信息是一种很好的方式（我们当前表单页面的布局就是这样的），这样能够引起用户的关注，提醒他们修正错误。但这样也会带来布局的问题。另外一种处理校验错误方式就是将所有的错误信息在同一个地方进行显示。为了做到这一点，我们可以移除每个输入域上的`<sf:errors>`元素，并将其放到表单的顶部，如下所示：

```html
<sf:form action="spitter/register" method="POST" commandName="spitter">
    <sf:error path="*" element="div" cssClass="errors" />
    <!-- ... -->
</sf:form>
```

与之前相比，值得注意的地方在于它的`path`属性被设置为了“\*”，这是一个通配符选择器，它会告诉`<sf:errors>`显示所有错误。

同样需要注意的是，我们将`element`属性设置成了`div`。默认情况下，错误都会渲染在一个HTML`<span>`标签中，如果只显示一个错误的话，这是不错的选择。但是，如果要渲染所有输入域的错误的话，很可能要展现不止一个错误，这时候使用`<span>`标签（行内元素）就不合适了。像`<div>`这样的块级元素会更为合适。因此，我们可以将`element`属性设置为`div`，这样的话，错误就会渲染在一个`<div>`标签中。

像之前一样，`cssClass`属性被设置`errors`，这样我们就能为`<div>`设置样式。如下为`<div>`的CSS样式，它具有红色的边框和浅红色的背景：

```css
div.errors {
    background-color: #ffcccc;
    border: 2px solid red;
}
```

现在，我们在表单的上方显示所有的错误，这样页面布局可能会更加容易一些。

但是，我们还没有着重显示需要修正的输入域。通过为每个输入域设置`cssErrorClass`属性，这个问题很容易解决。我们也可以使用`<sf: label>`，并设置它的`cssErrorClass`属性：

```html
<sf:form action="spitter/register" method="POST" commandName="spitter">
    <sf:errors path="*" element="div" cssClass="errors" />
    <sf:label path="firstName" cssErrorClass="error">First Name</sf:label>: 
    <sf:input path="firstName" cssErrorClass="error" />
    <br />
    <sf:label path="lastName" cssErrorClass="error">Last Name</sf:label>: 
    <sf:input path="lastName" cssErrorClass="error" />
    <br />
    <sf:label path="username" cssErrorClass="error">Username</sf:label>: 
    <sf:input path="username" cssErrorClass="error" />
    <br />
    <sf:label path="password" cssErrorClass="error">Password</sf:label>: 
    <sf:password path="password" cssErrorClass="error" />
    <br />
    <input type="submit" value="Register" />
</sf:form>
```

`<sf: label>`标签像其他的表单绑定标签一样，使用`path`来指定它属于模型对象中的哪个属性。假设校验First Name输入域没有任何错误，则其对应的`<sf:label>`会被渲染为HTML的`<label>`元素：

```html
<label for="firstName">First Name</label>
```

就其自身来说，设置`<sf:label>`的`path`属性并没有完成太多的功能。但是，我们还同时设置了`cssErrorClass`属性。如果它所绑定的属性有任何错误的话，在渲染得到的`<label>`元素中，`class`属性将会被设置为`error`，如下所示：

```html
<label for="firstName" class="error">First Name</label>
```

与之类似，`<sf:input>`标签的`cssErrorClass`属性被设置为`error`。如果有任何校验错误的话，在渲染得到的`<input>`标签中，`class`属性将会被设置为`error`。现在我们已经为文本标记和输入域设置了样式，这样当出现错误的时候，会将用户的注意力转移到此处。例如，如下的CSS会将文本标记渲染为红色，并将输入域设置为浅红色背景：

```css
label.error {
    color: red;
}

input.error {
    background-color: #ffcccc;
}
```

现在，我们有了很好的方式为用户展现错误信息。不过，我们还可以做另外一件事情，能够让这些错误信息更加易读。重新看一下`Spitter`类，我们可以在校验注解上设置`message`属性，使其引
用对用户更为友好的信息，而这些信息可以定义在属性文件中：

```java
@NotBlank(message = "{username.blank}")
@Size(min = 5, max = 16, message = "{username.size}")
private String username;

@NotBlank(message = "{password.blank}")
@Size(min = 5, max = 25, message = "{password.size}")
private String password;

@NotBlank(message = "{firstName.blank}")
@Size(min = 2, max = 30, message = "{firstName.size}")
private String firstName;

@NotBlank(message = "{lastName.blank}")
@Size(min = 2, max = 30, message = "{lastName.size}")
private String lastName;
```

对于上面每个域，我们都将其`@NotBlank`、`@Size`注解的message设置为一个字符串，这个字符串是用大括号括起来的。如果没有大括号的话，`message`中的值将会作为展现给用户的错误信息。但是使用了大括号之后，我们使用的就是属性文件中的某一个属性，该属性包含了实际的信息。

接下来我们需要做的就是创建一个名为ValidationMessages.properties的文件，并将其放在根类路径下：

```text
firstName.blank=First name must not be null.
firstName.size=First name must be between {min} and {max} characters long.
lastName.blank=Last name must not be null.
lastName.size=Last name must be between {min} and {max} characters long.
username.blank=Username must not be null.
username.size=Username must be between {min} and {max} characters long.
password.blank=Password must not be null.
password.size=Password must be between {min} and {max} characters long.
```

**Spring通用标签库**

除了表单绑定标签库之外，Spring还提供了更为通用的JSP标签库。实际上，这个标签库是Spring中最早的标签库。这么多年来，它有所变化，但是在最早版本的Spring中，它就已经存在了。

要使用Spring通用的标签库，我们必须要在页面上对其进行声明：

```jsp
<%@ taglib uri="http://www.springframework.org/tags" prefix="s" %>
```

与其他JSP标签库一样，prefix可以是任意你所喜欢的值。在这里，
通用的做法是将这个标签库的前缀设置为spring。但是，我将其设置为“s”，因为它更加简洁，更易于阅读和输入。

Spring的JSP标签库提供的标签：

JSP标签 | 描述
----- | ----
`<s:bind>` | 将绑定属性的状态导出到一个名为`status`的页面作用域属性中，与`<s:path>`组合使用获取绑定属性的值
`<s:escapeBody>` | 将标签体中的内容进行HTML和（或）JavaScript转义
`<s:hasBindErrors>` | 根据指定模型对象（在请求属性中）是否有绑定错误，有条件地渲染内容
`<s:htmlEscape>` | 为当前页面设置默认的HTML转义值
`<s:message>` | 根据给定的编码获取信息，然后要么进行渲染（默认行为），要么将其设置为页面作用域、请求作用域、会话作用域或应用作用域的变量（通过使用`var`和`scope`属性实现）
`<s:nestedPath>` | 设置嵌入式的path，用于`<s:bind>`之中
`<s:theme>` | 根据给定的编码获取主题信息，然后要么进行渲染（默认行为），要么将其设置为页面作用域、请求作用域、会话作用域或应用作用域的变量（通过使用`var`和`scope`属性实现）
`<s:transform>` | 使用命令对象的属性编辑器转换命令对象中不包含的属性
`<s:url>` | 创建相对于上下文的URL，支持URI模板变量以及HTML/XML/JavaScript转义。可以渲染URL（默认行为），也可以将其设置为页面作用域、请求作用域、会话作用域或应用作用域的变量（通过使用`var`和`scope`属性实现）
`<s:eval>` | 计算符合Spring表达式语言（Spring Expression Language，SpEL）语法的某个表达式的值，然后要么进行渲染（默认行为），要么将其设置为页面作用域、请求作用域、会话作用域或应用作用域的变量（通过使用`var`和`scope`属性实现）

上表中的一些标签已经被Spring表单绑定标签库淘汰了。例如，`<s:bind>`标签就是Spring最初所提供的表单绑定标签，它比我们在前面所介绍的标签复杂得多。

**展现国际化信息**

到现在为止，我们的JSP模板包含了很多硬编码的文本。这其实也算不上什么大问题，但是如果你要修改这些文本的话，就不那么容易了。而且，没有办法根据用户的语言设置国际化这些文本。

例如，考虑首页中的欢迎信息：

```html
<h1>Welcome to Spittr!</h1>
```

修改这个信息的唯一办法是打开home.jsp，然后对其进行变更。我觉得，这算不上什么大事。但是，应用中的文本散布到多个模板中，如果要大规模修改应用的信息时，你需要修改大量的JSP文件。

另外一个更为重要的问题在于，不管你选择什么样的欢迎信息，所有的用户都会看到同样的信息。Web是全球性的网络，你所构建的应用很可能会有全球化用户。因此，最好能够使用用户的语言与其进行交流，而不是只使用某一种语言。

对于渲染文本来说，是很好的方案，文本能够位于一个或多个属性文件中。借助`<s:message>`，我们可以将硬编码的欢迎信息替换为如下的形式：

```html
<h1><s:message code="spittr.welcome" /></h1>
```

按照这里的方式，`<s:message>`将会根据key为spittr.welcome的信息源来渲染文本。因此，如果我们希望`<s:message>`能够正常完成任务的话，就需要配置一个这样的信息源。

Spring有多个信息源的类，它们都实现了`MessageSource`接口。在这些类中，更为常见和有用的是`ResourceBundleMessageSource`。它会从一个属性文件中加载信息，这个属性文件的名称是根据基础名称（base name）衍生而来的。如下的`@Bean`方法配置了`ResourceBundleMessageSource`：

```java
@Bean
public MessageSource messageSource() {
    ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
    messageSource.setBasename("messages");
    return messageSource;
}
```

在这个bean声明中，核心在于设置`basename`属性。你可以将其设置为任意你喜欢的值，在这里，我将其设置为message。将其设置为message后，`ResourceBundleMessageSource`就会试图在根路径的属性文件中解析信息，这些属性文件的名称是根据这个基础名称衍生得到的。

根据这个方法的注释，如果我们设置为“message”，那么messages.properties、messages_en.properties、messages.xml、messages_en.xml等都会被接受。

```text
/**
 * Set an array of basenames, each following the basic ResourceBundle convention
 * of not specifying file extension or language codes. The resource location
 * format is up to the specific {@code MessageSource} implementation.
 * <p>Regular and XMl properties files are supported: e.g. "messages" will find
 * a "messages.properties", "messages_en.properties" etc arrangement as well
 * as "messages.xml", "messages_en.xml" etc.
 * <p>The associated resource bundles will be checked sequentially when resolving
 * a message code. Note that message definitions in a <i>previous</i> resource
 * bundle will override ones in a later bundle, due to the sequential lookup.
 * <p>Note: In contrast to {@link #addBasenames}, this replaces existing entries
 * with the given names and can therefore also be used to reset the configuration.
 * @param basenames an array of basenames
 * @see #setBasename
 * @see java.util.ResourceBundle
 */
```

令外的可选方案是使用`用ReloadableResourceBundleMessageSource`，它的工作方式与`ResourceBundleMessageSource`非常类似，但是它能够重新加载信息属性，而不必重新编译或重启应用。

下面是一个配置示例：

```java
@Bean
public MessageSource messageSource() {
    ReloadableResourceBundleMessageSource messageSource = new ReloadableResourceBundleMessageSource();
    messageSource.setBasename("file:///etc/spittr/messages");
    messageSource.setCacheSeconds(10);
    return messageSource;
}
```

这里的关键区别在于`basename`属性设置为在应用的外部查找（而不是像`ResourceBundleMessageSource`那样在类路径下查找）。`basename`属性可以设置为在类路径下（以“classpath:”作为前缀）、文件系统中（以“file:”作为前缀）或Web应用的根路径下（没有前缀）查找属性。在这里，我将其配置为在服务器文件系统的“/etc/spittr”目录下的属性文件中查找信息，并且基础的文件名为“message”。

**创建URL**

`<s:url>`是一个很小的标签。它主要的任务就是创建URL，然后将其赋值给一个变量或者渲染到响应中。它是JSTL中`<c:url>`标签的替代者，但是它具备几项特殊的技巧。

按照其最简单的形式，`<s:url>`会接受一个相对于Servlet上下文的URL，并在渲染的时候，预先添加上Servlet上下文路径。例如，考虑如下`<s:url>`的基本用法：

```html
<a href="<s:url href="/spitter/register" />">Register</a>
```

例如，这里我们的Servlet上下文为“spittr”，那么在响应中将会渲染如下的HTML：

```html
<a href="/spittr/spitter/register">Register</a>
```

这样，我们在创建URL的时候，就不必再担心Servlet上下文路径是什么了，`<s:url>`将会负责这件事。

我们还可以使用`<s:url>`创建URL，并将其赋值给一个变量供模板在稍后使用：

由于我们的工程代码的JSP中全部配置了如下代码：

```jsp
<%
    String path = request.getContextPath();
    String basePath = request.getScheme() + "://" + request.getServerName() + ":" + request.getServerPort()
            + path + "/";
%>
<base href="<%=basePath%>">
```

并且之前的所有`<a>`元素的`href`属性均不以“/”开头。所以，下文中关于URL的路径，我们全部去掉了开头的“/”。不过从测试来看，在使用`<s:url>`时，加上“/”也不会出错（无论加不加上述的配置，`<a>`元素的`href`属性均不能以“/”开头，因为这样会导致其参照路径是Web容器的根），但为了保持代码的一致性，下文还是去掉了开头的“/”。

```html
<s:url value="spitter/register" var="registerUrl" />
<a href="${registerUrl}">Register</a>
```

默认情况下，URL是在页面作用域内创建的。但是通过设置`scope`属性，我们可以让`<s:url>`在应用作用域内、会话作用域内或请求作用域内创建URL：

```html
<s:url value="spitter/register" var="registerUrl" scope="request" />
```

如果希望在URL上添加参数的话，那么你可以使用`<s:param>`标签。比如，如下的<s:url>使用两个内嵌的`<s:param>`标签，来设置“spittles”的max和count参数：

```html
<s:url href="spittles" var="spittlesUrl">
    <s:param name="max" value="60" />
    <s:param name="count" value="20" />
</s:url>
```

到目前为止，我们还没有看到`<s:url>`能够实现，而JSTL的`<c:url>`无法实现的功能。但是，如果我们需要创建带有路径（path）参数的URL该怎么办呢？我们该如何设置`value`属性，使其具有路径变量的占位符呢？

例如，假设我们需要为特定用户的基本信息页面创建一个URL。那没有问题，`<s:param>`标签可以承担此任：

```html
<s:url value="spitter/{username}" var="spitterUrl">
    <s:param name="username" value="jbauer" />
</s:url>
```

然后我们就可以使用`<a>`元素的`href`属性引用“spitterUrl”了。

`<s:url>`标签还可以解决URL的转义需求。例如，如果你希望将渲染得到的URL内容展现在Web页面上（而不是作为超链接），那么你应该要求`<s:url>`进行HTML转义，这需要将`htmlEscape`属性设置为`true`。例如，如下的`<s:url>`将会渲染HTML转义后的URL：

```html
<s:url value="spittles" htmlEscape="true">
    <s:param name="max" value="60" />
    <s:param name="count" value="20" />
</s:url>
```

在我们的工程下，上述代码被转义为“spittles?max=60&count=20”。（注意，这里如果`value`属性以“/”开头，即“/spittles”，则会被转义为“/sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-01_jsp/spittles?max=60&count=20”）

如果希望在JavaScript中使用URL，那么可以设置`javaScriptEscape`属性为`true`：

```html
<s:url value="spittles" var="spittlesJSUrl" javaScriptEscape="true">
    <s:param name="max" value="60" />
    <s:param name="count" value="20" />
</s:url>
<script>
    var spittlesUrl = "${spittlesJSUrl}"
</script>
```

此时，spittlesUrl的值为“spittles?max=60&count=20”。（如果是“/spittles”，则其值为“\/sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-01_jsp\/spittles?max=60&count=20”）

既然提到了转义，有一个标签专门用来转义内容，而不是转义标签。

**转义内容**

`<s:escapeBody>`标签是一个通用的转义标签。它会渲染标签体中内嵌的内容，并且在必要的时候进行转义。

例如，假设你希望在页面上展现一个HTML代码片段。为了正确显示，我们需要将“<”和“>”字符替换为`&lt;`和`&gt;`，否则的话，浏览器将会像解析页面上其他HTML那样解析这段HTML内容。

当然，没有人禁止我们手动将其转义为`&lt;`和`&gt;`，但是这很烦琐，并且代码难以阅读。我们可以使用`<s:escapeBody>`，并让Spring完成这项任务：

```html
<s:escapeBody htmlEscape="true">
    <h1>Hello</h1>
</s:escapeBody>
```

上述内容在浏览器中会被显示为：

```text
<h1>Hello</h1>
```

通过设置`javaScriptEscape`属性为`true`，`<s:escapeBody>`标签还支持对JavaScript代码的转义。

现在，我们已经看到了如何使用JSP来定义Spring视图，现在让我们考虑一下如何使其在审美上更加有吸引力。我们可以在页面上增加一些通用的元素，比如添加包含站点Logo的头部、使用样式并在底部展现版权信息。我们不会在Spittr应用中的每个JSP都进行这样的修改，而是借助Apache Tiles来为模板实现一些通用且可重用的布局。

### 6.3 使用Apache Tiles视图定义布局

*以下内容代码在工程sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-02_tiles中*。

到现在为止，我们很少关心应用中Web页面的布局问题。每个JSP完全负责定义自身的布局，在这方面其实这些JSP也没有做太多工作。

假设我们想为应用中的所有页面定义一个通用的头部和底部。最原始的方式就是查找每个JSP模板，并为其添加头部和底部的HTML。但是这种方法的扩展性并不好，也难以维护。为每个页面添加这些元素会有一些初始成本，而后续的每次变更都会耗费类似的成本。

更好的方式是使用布局引擎，如Apache Tiles，定义适用于所有页面的通用页面布局。Spring MVC以视图解析器的形式为Apache Tiles提供了支持，这个视图解析器能够将逻辑视图名解析为Tiles定义。

#### 6.3.1 配置Tiles视图解析器

为了在Spring中使用Tiles，需要配置几个bean。我们需要一个`TilesConfigurer`bean，它会负责定位和加载Tile定义并协调生成Tiles。除此之外，`还需要TilesViewResolver`bean将逻辑视图名称解析为Tile定义。

这里使用Tiles3：

```java
// 配置TileConfigurer
@Bean
public TilesConfigurer tilesConfigurer() {
    TilesConfigurer tiles = new TilesConfigurer();
    // 指定Tiles定义的位置
    tiles.setDefinitions(new String[] { "/WEB-INF/layout/tiles.xml" });
    // 启用刷新功能
    tiles.setCheckRefresh(true);
    return tiles;
}
```

当配置`TilesConfigurer`的时候，所要设置的最重要的属性就是`definitions`。这个属性接受一个`String`类型的数组，其中每个条目都指定一个Tile定义的XML文件。对于Spittr应用来讲，我们让它在“/WEB-INF/layout/”目录下查找tiles.xml。

我们还可以指定多个Tile定义文件，甚至能够在路径位置上使用通配符，当然在上例中我们没有使用该功能。例如，我们要求TilesConfigurer加载“/WEB-INF/”目录下的所有名字为tiles.xml的文件，那么可以按照如下的方式设置definitions属性：

```java
tiles.setDefinitions(new String[] {"/WEB-INF/**/tiles.xml"});
```

配置Tiles视图解析器：

```java
@Bean
public ViewResolver viewResolver() {
    TilesViewResolver tilesViewResolver = new TilesViewResolver();
    return tilesViewResolver;
}
```

使用XML配置：

```xml
<bean id="tilesConfigurer" class=
"org.springframework.web.servlet.view.tiles3.TilesConfigurer">
    <property name="definitions">
        <list>
            <value>/WEB-INF/layout/tiles.xml</value>
        </list>
    </property>
</bean>

<bean id="viewResolver" class=
"org.springframework.web.servlet.view.tiles3.TilesViewResolver">
    <property name="order" value="1" />
</bean>
```

`TilesConfigurer`会加载Tile定义并与Apache Tiles协作，而`TilesViewResolver`会将逻辑视图名称解析为引用Tile定义的视图。它是通过查找与逻辑视图名称相匹配的Tile定义实现该功能的。

定义Tiles：

Apache Tiles提供了一个文档类型定义（document type definition，DTD），用来在XML文件中指定Tile的定义。每个定义中需要包含一个`<definition>`元素，这个元素会有一个或多个`<put-attribute>`元素。例如，如下的XML文档为Spittr应用定义了几个Tile：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tiles-definitions PUBLIC
    "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN"
    "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">
<tiles-definitions>

    <!-- 定义base Tile -->
    <definition name="base" template="/WEB-INF/layout/page.jsp">
        <!-- 设置属性 -->
        <put-attribute name="header" value="/WEB-INF/layout/header.jsp" />
        <put-attribute name="footer" value="/WEB-INF/layout/footer.jsp" />
    </definition>

    <!-- 扩展base Tile -->
    <definition name="home" extends="base">
        <put-attribute name="body" value="/WEB-INF/views/home.jsp" />
    </definition>

    <definition name="registerForm" extends="base">
        <put-attribute name="body" value="/WEB-INF/views/registerForm.jsp" />
    </definition>

    <definition name="profile" extends="base">
        <put-attribute name="body" value="/WEB-INF/views/profile.jsp" />
    </definition>

    <definition name="spittles" extends="base">
        <put-attribute name="body" value="/WEB-INF/views/spittles.jsp" />
    </definition>

    <definition name="spittle" extends="base">
        <put-attribute name="body" value="/WEB-INF/views/spittle.jsp" />
    </definition>
</tiles-definitions>
```

每个<definition>元素都定义了一个Tile，它最终引用的是一个JSP模板。在名为base的Tile中，模板引用的是“/WEBINF/layout/page.jsp”。某个Tile可能还会引用其他的JSP模板，使这些JSP模板嵌入到主模板中。对于base Tile来讲，它引用的是一个头部JSP模板和一个底部JSP模板。

编写主配置模板page.jsp：

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@ taglib uri="http://www.springframework.org/tags" prefix="s"%>
<%@ taglib uri="http://tiles.apache.org/tags-tiles" prefix="t"%>
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
<title>Spittr</title>
<link rel="stylesheet" type="text/css" href="<s:url value="resources/style.css"/>">
</head>
<body>
    <!-- 插入头部 -->
    <div id="header">
        <t:insertAttribute name="header" />
    </div>

    <!-- 插入主体内容 -->
    <div id="content">
        <t:insertAttribute name="body" />
    </div>

    <!-- 插入底部 -->
    <div id="footer">
        <t:insertAttribute name="footer" />
    </div>
</body>
</html>
```

需要重点关注的事情就是如何使用Tile标签库中的`<t:insertAttribute>` JSP标签来插入其他的模板。在这里，用它来插入名为header、body和footer的模板。

在base Tile定义中，`header`和`footer`属性分别被设置为引用“/WEB-INF/layout/header. jsp”和“/WEB-INF/layout/footer.jsp”。但是body属性呢？它是在哪里设置的呢？

在这里，base Tile不会期望单独使用。它会作为基础定义，供其他的Tile定义扩展。我们可以看到其他的Tile定义都是扩展自base Tile。它意味着它们会继承其`header`和`footer`属性的设置（当然，Tile定义中也可以覆盖掉这些属性），但是每一个都设置了`body`属性，用来指定每个Tile特有的JSP模板。

现在，我们关注一下home Tile，它扩展了base。因为它扩展了base，因此它会继承base中的模板和所有的属性。尽管home Tile定义相对来说很简单，但是它实际上包含了如下的定义：

```xml
<definition name="home" extends="base">
    <put-attribute name="header" value="/WEB-INF/layout/header.jsp" />
    <put-attribute name="footer" value="/WEB-INF/layout/footer.jsp" />
    <put-attribute name="body" value="/WEB-INF/views/home.jsp" />
</definition>
```

下面配置header.jsp和footer.jsp。

header.jsp：

```jsp
<%@ taglib uri="http://www.springframework.org/tags" prefix="s"%>
<a href="<s:url value="/" />">
    <img src="<s:url value="/resources" />/images/logo.png" border="0" />
</a>
```

footer:

```jsp
<br/>Copyright &copy; Tavish
```

每个扩展自base的Tile都定义了自己的主体区模板，所以每个都会与其他的有所区别。但是为了完整地了解home Tile，如下展现了home.jsp：

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ taglib uri="http://www.springframework.org/tags" prefix="s" %>

<h1><s:message code="spittr.welcome" /></h1>
<a href="<c:url value="spittles" />">Spittles</a> |
<a href="<c:url value="spitter/register" />">Register</a>
```

这里的关键点在于通用的元素放到了page.jsp、header.jsp以及footer.jsp中，其他的Tile模板中不再包含这部分内容。这使得它们能够跨页面重用，这些元素的维护也得以简化。

### 6.4 使用Thymeleaf

*以下内容代码在工程sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-03_thymeleaf中*。

尽管JSP已经存在了很长的时间，并且在Java Web服务器中无处不在，但是它却存在一些缺陷。JSP最明显的问题在于它看起来像HTML或XML，但它其实上并不是。大多数的JSP模板都是采用HTML的形式，但是又掺杂上了各种JSP标签库的标签，使其变得很混乱。这些标签库能够以很便利的方式为JSP带来动态渲染的强大功能，但是它也摧毁了我们想维持一个格式良好的文档的可能性。作为一个极端的样例，如下的JSP标签甚至作为HTML参数的值：

```html
<input type="text" value="<c:out value="${thing.name}" />" />
```

标签库和JSP缺乏良好格式的一个副作用就是它很少能够与其产生的HTML类似。所以，在Web浏览器或HTML编辑器中查看未经渲染的JSP模板是非常令人困惑的，而且得到的结果看上去也非常丑陋。这个结果是不完整的——在视觉上这简直就是一场灾难！因为JSP不是真正的HTML，很多浏览器和编辑器展现的效果都很难在审美上接近模板最终渲染出来的效果。

同时，JSP规范是与Servlet规范紧密耦合的。这意味着它只能用在基于Servlet的Web应用之中。JSP模板不能作为通用的模板（如格式化Email），也不能用于非Servlet的Web应用。

多年来，在Java应用中，有多个项目试图挑战JSP在视图领域的统治性地位。最新的挑战者是Thymeleaf，它展现了一些切实的承诺，是一项很令人兴奋的可选方案。Thymeleaf模板是原生的，不依赖于标签库。它能在接受原始HTML的地方进行编辑和渲染。因为它没有与Servlet规范耦合，因此Thymeleaf模板能够进入JSP所无法涉足的领域。现在，我们看一下如何在Spring MVC中使用Thymeleaf。

#### 6.4.1 配置Thymeleaf视图解析器

为了要在Spring中使用Thymeleaf，我们需要配置三个启用Thymeleaf与Spring集成的bean：

- `ThymeleafViewResolver`：将逻辑视图名称解析为Thymeleaf模板视图
- `SpringTemplateEngine`：处理模板并渲染结果
- `TemplateResolver`：加载Thymeleaf模板

配置如下：

```java
// 配置Thymeleaf视图解析器
@Bean
public ViewResolver viewResolver(TemplateEngine templateEngine) {
    ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
    viewResolver.setTemplateEngine(templateEngine);
    return viewResolver;
}

// 配置模板引擎
@Bean
public TemplateEngine templateEngine(ITemplateResolver templateResolver) {
    SpringTemplateEngine templateEngine = new SpringTemplateEngine();
    templateEngine.setTemplateResolver(templateResolver);
    return templateEngine;
}

// 配置模板解析器
@Bean
public ITemplateResolver templateResolver() {

    WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();

    ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(
            webApplicationContext.getServletContext());
    
    templateResolver.setPrefix("/WEB-INF/templates/");
    templateResolver.setSuffix(".html");
    templateResolver.setTemplateMode(TemplateMode.HTML);
    return templateResolver;
}
```

这里如果使用XML配置，需要获取`ServletContext`，然后通过构造器注入给`ServletContextTemplateResolver`。而使用XML配置方式获取`ServletContext`需要我们定义一个类来实现`BeanFactory<ServletContext>`、`ServletContextAware`两个接口，然后将这个类当作`ServletContext`使用。完成此步骤后，剩下的就只是处理三个bean之间的关系了。

创建代表`ServletContext`的类的代码如下：

```java
public class ServletContextFactory implements FactoryBean<ServletContext>, ServletContextAware {
    
    private ServletContext servletContext;

    @Override
    public ServletContext getObject() throws Exception {
        return servletContext;
    }

    @Override
    public Class<?> getObjectType() {
        return ServletContext.class;
    }

    @Override
    public boolean isSingleton() {
        return true;
    }

    @Override
    public void setServletContext(ServletContext servletContext) {
        this.servletContext = servletContext;
    }
}
```

XML配置如下：

```xml
<!-- 配置ServletContext -->
<bean id="servletContext" class="foo.bar.ServletContextFactory" />

<!-- 配置模板解析器 -->
<bean id="templateResolver" class="org.thymeleaf.templateresolver.ServletContextTemplateResolver">
    <constructor-arg ref="servletContext"/> 
    <property name="prefix" value="/WEB-INF/templates/" />
    <property name="suffix" value=".html" />
    <property name="templateMode" value="HTML" />
</bean>

<!-- 配置模板引擎 -->
<bean id="templateEngine" class="org.thymeleaf.spring4.SpringTemplateEngine">
    <property name="templateResolver" ref="templateResolver" />
</bean>

<!-- 视图解析器 -->
<bean id="viewResolver" class="org.thymeleaf.spring4.view.ThymeleafViewResolver">
    <property name="templateEngine" ref="templateEngine" />
</bean>
```

不管使用哪种配置方式，Thymeleaf都已经准备就绪了，它可以将响应中的模板渲染到Spring MVC控制器所处理的请求中。

`ThymeleafViewResolver`是Spring MVC中`ViewResolver`的一个实现类。像其他的视图解析器一样，它会接受一个逻辑视图名称，并将其解析为视图。不过在该场景下，视图会是一个Thymeleaf模板。

这里模板解析器会最终定位和查找模板。它使用了`prefix`和`suffix`属性，前后缀会与逻辑视图名组合使用。它的`templateMode`属性被设置成了HTML（原书是设置成HTML5，但是`TemplateMode.HTML5`已经被标记为`@Deprecated`，并推荐使用`TemplateMode.HTML`了），这表明我们预期要解析的模板会渲染成HTML输出。

所有的Thymeleaf bean都已经配置完成了，那么接下来我们该创建几个视图了。

#### 6.4.2 定义Thymeleaf模板

Thymeleaf在很大程度上就是HTML文件，与JSP不同，它没有什么特殊的标签或标签库。Thymeleaf之所以能够发挥作用，是因为它通过自定义的命名空间，为标准的HTML标签集合添加Thymeleaf属性。

如下的程序清单展现了home.html，也就是使用Thymeleaf命名空间的首页模板：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Spittr</title>
<link rel="stylesheet" type="text/css" th:href="@{/resources/style.css}"><link>
</head>
<body>
    <h1>Welcome to Spittr</h1>
    <a th:href="@{/spittle}">Spittles</a> |
    <a th:href="@{/spitter/register}">Register</a>
</body>
</html>
```

首页模板相对来讲很简单，只使用了`th:href`属性。这个属性与对应的原生HTML属性很类似，也就是`href`属性，并且可以按照相同的方式来使用。`th:href`属性的特殊之处在于它的值中可以包含Thymeleaf表达式，用来计算动态的值。它会渲染成一个标准的`href`属性，其中会包含在渲染时动态创建得到的值。这是Thymeleaf命名空间中很多属性的运行方式：它们对应标准的HTML属性，并且具有相同的名称，但是会渲染一些计算后得到的值。在本例中，使用`th:href`属性的三个地方都用到了“@{}”表达式，用来计算相对于URL的路径。

上述代码在网页中查看源代码变为：

```html

<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Spittr</title>
<link rel="stylesheet" type="text/css" href="/sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-03_thymeleaf/resources/style.css"><link>
</head>
<body>
    <h1>Welcome to Spittr</h1>
    <a href="/sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-03_thymeleaf/spittle">Spittles</a> |
    <a href="/sia4e-P2_Spring_on_the_web-C06_Rendering_web_views-03_thymeleaf/spitter/register">Register</a>
</body>
</html>
```

尽管home.html是一个相当简单的Thymeleaf模板，但是它依然很有价值，这在于它与纯HTML模板非常接近。唯一的区别之处在于`th:href`属性，否则的话，它就是基础且功能丰富的HTML文件。

这意味着Thymeleaf模板与JSP不同，它能够按照原始的方式进行编辑甚至渲染，而不必经过任何类型的处理器。当然，我们需要Thymeleaf来处理模板并渲染得到最终期望的输出。即便如此，如果没有任何特殊的处理，home.html也能够加载到Web浏览器中，并且看上去与完整渲染的效果很类似。

但是Spring的JSP标签所擅长的是表单绑定。如果我们抛弃JSP的话，那是不是也要抛弃表单绑定呢？不必担心。Thymeleaf提供了与之相匹敌的功能。

**借助Thymeleaf实现表单绑定**

表单绑定是Spring MVC的一项重要特性。它能够将表单提交的数据填充到命令对象中，并将其传递给控制器，而在展现表单的时候，表单中也会填充命令对象中的值。如果没有表单绑定功能的话，我们需要确保HTML表单域要映射后端命令对象中的属性，并且在校验失败后展现表单的时候，还要负责确保输入域中值要设置为命令对象的属性。

作为阐述的样例，请参考如下的Thymeleaf模板片段，它会渲染First Name输入域：

```html
<label th:class="${#fields.hasErrors('firstName')}? 'error'">First Name</label>:
<input type="text" th:field="*{firstName}" th:class="${#fields.hasErrors('firstName')}? 'error'" /><br/>
```

在这里，我们不再使用Spring JSP标签中的`cssClassName`属性，而是在标准的HTML标签上使用`th:class`属性。`th:class`属性会渲染为一个`class`属性，它的值是根据给定的表达式计算得到的。在上面的这两个`th:class`属性中，它会直接检查`firstName`域有没有校验错误。如果有的话，`class`属性在渲染时的值为`error`。如果这个域没有错误的话，将不会渲染`class`属性。

`<input>`标签使用了`th:field`属性，用来引用后端对象的`firstName`域。因为我们是在将这个输入域绑定到后端对象的`firstName`属性上，因此使用`th:field`属性引用`firstName`域。通过使用`th:field`，我们将`value`属性设置为`firstName`的值，同时还会将`name`属性设置为`firstName`。

完整的表单页面：

```html
<form method="POST" th:action="@{/spitter/register}" th:object="${spitter}">
    <!-- 展示错误 -->
    <div class="errors" th:if="${#fields.hasErrors('*')}">
        <ul>
            <li th:each="err : ${#fields.errors('*')}" th:text="${err}">Input is incorrect</li>
        </ul>
    </div>
    
    <!-- First Name -->
    <label th:class="${#fields.hasErrors('firstName')}? 'error'"> First Name</label>
    :
    <input type="text" th:field="*{firstName}" th:class="${#fields.hasErrors('firstName')}? 'error'" />
    <br />
    
    <!-- Last Name -->
    <label th:class="${#fields.hasErrors('lastName')}? 'error'"> Last Name</label>
    :
    <input type="text" th:field="*{lastName}" th:class="${#fields.hasErrors('lastName')}? 'error'" />
    <br />
    
    <!-- Username -->
    <label th:class="${#fields.hasErrors('username')}? 'error'"> Username</label>
    :
    <input type="text" th:field="*{username}" th:class="${#fields.hasErrors('username')}? 'error'" />
    <br />
    
    <!-- Password -->
    <label th:class="${#fields.hasErrors('password')}? 'error'"> Password</label>
    :
    <input type="password" th:field="*{password}" th:class="${#fields.hasErrors('password')}? 'error'" />
    <br />
    
    <input type="submit" value="Register" />
</form>
```

上述表单使用了相同的Thymeleaf属性和“*{}”表达式，为所有的表单域绑定后端对象。这其实重复了我们在First Name域中所做的事情。

我们在表单的顶部了也使用了Thymeleaf，它会用来渲染所有的错误。`<div>`元素使用`th:if`属性来检查是否有校验错误。如果有的话，会渲染`<div>`，否则的话，它将不会渲染。

在`<div>`中，会使用一个无顺序的列表来展现每项错误。`<li>`标签上的`th:each`属性将会通知Thymeleaf为每项错误都渲染一个`<li>`，在每次迭代中会将当前错误设置到一个名为`err`的变量中。

`<li>`标签还有一个`th:text`属性。这个命令会通知Thymeleaf计算某一个表达式（在本例中，也就是`err`变量）并将它的值渲染为`<li>`标签的内容体。实际上的效果就是每项错误对应一个`<li>`元素，并展现错误的文本。

你可能会想知道`${}`和`*{}`括起来的表达式到底有什么区别。`${}`表达式（如`${spitter}`）是变量表达式（variable expression）。一般来讲，它们会是对象图导航语言（Object-Graph Navigation Language，OGNL）表达式。但在使用Spring的时候，它们是SpEL表达式。在`${spitter}`这个例子中，它会解析为key为spitter的`model`属性。

而对于`*{}`表达式，它们是选择表达式（selection expression）。变量表达式是基于整个SpEL上下文计算的，而选择表达式是基于某一个选中对象计算的。在本例的表单中，选中对象就是`<form>`标签中`th:object`属性所设置的对象：模型中的`Spitter`对象。因此，`*{firstName}`表达式就会计算为`Spitter`对象的`firstName`属性。

（注意，因为此时工程中的数据库实现为Mock实现（没有实现接口的任何方法），所以不具备查询数据库的功能。所以，当我们在注册表单页面填写好参数并提交后，页面会跳转到“/spitter/username”，但是不会生成任何数据（查不了数据库）。这个时候，当前工程的后台会报错：“org.springframework.expression.spel.SpelEvaluationException: EL1007E: Property or field 'username' cannot be found on null”，其中username是我们页面中的第一个SpEL表达式`${spitter.username}`。但实际测试，jsp工程和tiles工程均不会报出该错误，推测为是由于三个工程配置了不同的视图解析器导致的区别。）

### 6.5 小结

