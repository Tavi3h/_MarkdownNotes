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

Spring提供了这个接口的实现，名为`SpringServletContainerInitializer`，这个类反过来又会查找实现`WebApplicationInitializer`的类并将配置的任务交给它们来完成。Spring 3.2引入了一个便利的`WebApplicationInitializer`基础实现，也就是`AbstractAnnotationConfigDispatcherServletInitializer`。因为我们的`SpittrWebAppInitializer`继承了`AbstractAnnotationConfigDispatcherServlet-Initializer`（同时也就实现了`WebApplicationInitializer`），因此当部署到Servlet 3.0容器中的时候，容器会自动发现它，并用它来配置Servlet上下文。

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

>
处理请求只是Spring MVC功能的一部分。如果控制器所产生的结果想要让人看到，那么它们产生的模型数据就要渲染到视图中，并展现到用户的Web浏览器中。Spring的视图渲染是很灵活的，并提供了多个内置的可选方案，包括传统的JavaServer Pages以及流行的ApacheTiles布局引擎。
>
在本章中，我们首先快速了解了一下Spring所提供的视图和视图解析可选方案。我们还深入学习了如何在Spring MVC中使用JSP和ApacheTiles。
>
我们还看到了如何使用Thymeleaf作为Spring MVC应用的视图层，它被视为JSP的替代方案。Thymeleaf是一项很有吸引力的技术，因为它能创建原始的模板，这些模板是纯HTML，能像静态HTML那样以原始的方式编写和预览，并且能够在运行时渲染动态模型数据。除此之外，Thymeleaf是与Servlet没有耦合关系的，这样它就能够用在JSP所不能使用的领域中。
>
Spittr应用的视图定义完成之后，我们已经具有了一个虽然微小但是可部署且具有一定功能的Spring MVC Web应用。还有一些其他的特性需要更新进来，如数据持久化和安全性，我们会在合适的时候关注这些特性。但现在，这个应用开始变得有模有样了。
>
在深入学习应用的技术栈之前，在下一章我们将会继续讨论SpringMVC，学习这个框架中一些更为有用和高级的功能。

## 第七章 Spring MVC的高级技术

本章内容：

- Spring MVC配置的替代方案
- 处理文件上传
- 在控制器中处理异常
- 使用flash属性

在第5章中，我们学习了Spring MVC的基础知识，以及如何编写控制器来处理各种请求。基于这些知识，我们在第6章学习了如何创建JSP和Thymeleaf视图，这些视图会将模型数据展现给用户。你可能认为我们已经掌握了Spring MVC的全部知识。但是稍等！还没有结束！

在本章中，我们会继续Spring MVC的话题，本章所介绍的特性已经超出了第5章和第6章基础知识的范畴。我们将会看到如何编写控制器来处理文件上传、如何处理控制器所抛出的异常，以及如何在模型中传递数据，使其能够在重定向（redirect）之后依然存活。

前文中，我们是通过`AbstractAnnotationConfigDispatcherServletInitializer`搭建Spring MVC，在介绍本章内容之前，我们先花店时间探讨以下如何用其它方式搭建`DispatcherServlet`和`ContextLoaderListener`。

### 7.1 Spring MVC配置的替代方案

*以下内容代码在工程sia4e-P2_Spring_on_the_web-C07_Advanced_Spring_MVC中*。

在第5章中，我们通过继承`AbstractAnnotationConfigDispatcherServletInitializer`快速搭建了Spring MVC环境。在这个便利的基础类中，假设我们需要基本的`DispatcherServlet`和`ContextLoaderListener`环境，并且Spring配置是使用Java的，而不是XML。

尽管对很多Spring应用来说，这是一种安全的假设，但是并不一定总能满足我们的要求。除了`DispatcherServlet`以外，我们可能还需要额外的Servlet和Filter；我们可能还需要对`DispatcherServlet`本身做一些额外的配置；或者，如果我们需要将应用部署到Servlet 3.0之前的容器中，那么还需要将`DispatcherServlet`配置到传统的web.xml中。

#### 7.1.1 自定义DispatcherServlet配置

在`SpittrWebAppInitializer`中我们所编写的三个方法仅仅是必须要重载的abstract方法。但实际上还有更多的方法可以进行重载，从而实现额外的配置。

此类的方法之一就是`customizeRegistration()`。在`AbstractAnnotationConfigDispatcherServletInitializer`将`DispatcherServlet`注册到Servlet容器中之后，就会调用`customizeRegistration()`，并将Servlet注册后得到的`ServletRegistration.Dynamic`传递进来。通过重载`customizeRegistration()`方法，我们可以对`DispatcherServlet`进行额外的配置。

在稍后的内容中，我们会看到如何在Spring MVC中处理`multipart`请求和文件上传。如果计划使用Servlet 3.0对`multipart`配置的支持，那么需要使用`DispatcherServlet`的registration来启用multipart请求。我们可以重载`customizeRegistration()`方法来设置`MultipartConfigElement`，如下所示：

```java
@Override
protected void customizeRegistration(Dynamic registration) {
    resgistration.setMultipartConfig(new MultipartConfigElement("/tmp/spittr/uploads"));
}
```

借助`customizeRegistration()`方法中的`ServletRegistration.Dynamic`，我们能够完成多项任务，包括通过调用`setLoadOnStartup()`设置`load-on-startup`优先级，通过`setInitParameter()`设置初始化参数，通过调用`setMultipartConfig()`配置Servlet 3.0对`multipart`的支持。在前面的样例中，我们设置了对`multipart`的支持，将上传文件的临时存储目录设置在“/tmp/spittr/uploads”中。

#### 7.1.2 添加其它的Servlet和Filter

按照`AbstractAnnotationConfigDispatcherServletInitializer`的定义，它会创建`DispatcherServlet`和`ContextLoaderListener`。但是，如果你想注册其他的Servlet、Filter或Listener的话，那该怎么办呢？

基于Java的初始化器（initializer）的一个好处就在于**我们可以定义任意数量的初始化器类**。因此，如果我们想往Web容器中注册其他组件的话，只需创建一个新的初始化器就可以了。最简单的方式就是实现Spring的`WebApplicationInitializer`接口。

例如，实现`WebApplicationInitializer`实现并注册一个Servlet：

```java
package spittr.config;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRegistration;
import org.springframework.web.WebApplicationInitializer;

import spittr.web.MyServlet;

public class MyServletInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        
        ServletRegistration.Dynamic myServlet = servletContext.addServlet("myServlet", MyServlet.class);
        
        myServlet.addMapping("/custom/home");        
    }
}
```

MyServlet类：

```java
package spittr.web;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyServlet extends HttpServlet {
    
    private static final long serialVersionUID = 1L;

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        request.getRequestDispatcher("/WEB-INF/templates/home.html").forward(request, response);
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        doGet(request, response);
    }
}
```

上述配置特别简单，我们配置了一个Servlet，并将它映射到“/custom/home”。在Servlet类中，我们定义它会把请求转发到home页面。

通过上述配置，我们就注册了一个Servlet。这里`MyServletInitializer`不用使用任何Spring注解，也不用在任何配置文件或类中对其进行配置。根据`WebApplicationInitializer`接口的文档，任意实现了该接口的类都会被类`SpringServletContainerInitializer`检测，而任何Servlet 3.0+的容器都会自动引导该类。

>
 * Interface to be implemented in Servlet 3.0+ environments in order to configure the
 * {@link ServletContext} programmatically -- as opposed to (or possibly in conjunction
 * with) the traditional {@code web.xml}-based approach.
 *
 * <p>Implementations of this SPI will be detected automatically by {@link
 * SpringServletContainerInitializer}, which itself is bootstrapped automatically
 * by any Servlet 3.0 container. See {@linkplain SpringServletContainerInitializer its
 * Javadoc} for details on this bootstrapping mechanism.

类似地，我们还可以创建新的`WebApplicationInitializer`实现来注册Listener和Filter。

示例，注册一个Filter：

```java
package spittr.config;

import javax.servlet.FilterRegistration;
import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.ServletRegistration;
import org.springframework.web.WebApplicationInitializer;

import spittr.web.MyServlet;

public class MyServletInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {
        
        // 注册Servlet
        ServletRegistration.Dynamic myServlet = servletContext.addServlet("myServlet", MyServlet.class);
        
        // 添加映射
        myServlet.addMapping("/custom/home");        
        
        // 注册Filter
        FilterRegistration.Dynamic myFilter = servletContext.addFilter("myFilter", MyFilter.class);
        
        // 添加映射
        myFilter.addMappingForUrlPatterns(null, false, "/custom/*");
        
    }
}
```

如果要将应用部署到支持Servlet 3.0的容器中，那么`WebApplicationInitializer`提供了一种通用的方式，实现在Java中注册Servlet、Filter和Listener。不过，如果你只是注册Filter，并且该Filter只会映射到`DispatcherServlet`上的话，那么在`AbstractAnnotationConfigDispatcherServletInitializer`中还有一种快捷方式

为了注册Filter并将其映射到`DispatcherServlet`，所需要做的仅仅是重载`AbstractAnnotationConfigDispatcherServletInitializer`的`getServletFilters()`方法。

配置字符集过滤器CharacterEncodingFilter：

```java
@Override
protected Filter[] getServletFilters() {
    CharacterEncodingFilter filter = new CharacterEncodingFilter();
    filter.setEncoding("UTF-8");
    filter.setForceEncoding(true);
    return new Filter[] { filter };
}
```

这个方法返回的是一个`javax.servlet.Filter`的数组。在这里它只返回了一个Filter，但它实际上可以返回任意数量的Filter。在这里没有必要声明它的映射路径，`getServletFilters()`方法返回的所有Filter都会映射到`DispatcherServlet`上（也就是说我们无法配置映射）。

如果要将应用部署到Servlet 3.0容器中，那么Spring提供了多种方式来注册Servlet（包括DispatcherServlet）、Filter和Listener，而不必创建web.xml文件。但是，如果你不想采取以上所述方案的话，也是可以的。假设你需要将应用部署到不支持Servlet 3.0的容器中（或者你只是希望使用web.xml文件），那么我们完全可以按照传统的方式，通过web.xml配置Spring MVC。让我们看一下该怎么做。

#### 7.1.3 在web.xml中声明DispatcherServlet

在典型的Spring MVC应用中，我们会需要`DispatcherServlet`和`ContextLoaderListener`。

下面在XML中注册它们：

```xml
<!-- 注册Spring配置文件的位置 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:resources/spring-*.xml</param-value>
</context-param>

<!-- 注册ServletContext监听器 -->
<listener>
    <listener-class>
        org.springframework.web.context.ContextLoaderListener
    </listener-class>
</listener>

<!-- 注册DispatcherServlet -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <init-param>
        <!-- 在这里指定其配置文件 -->
        <!-- 通常我们将Spring的配置文件与Spring MVC的配置文件分开 -->
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:resources/spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

`ContextLoaderListener`和`DispatcherServlet`各自都会加载一个Spring应用上下文。上下文参数`contextConfigLocation`指定了一个XML文件的地址，这个文件（classpath:resources/spring-*.xml）定义了根应用上下文，它会被`ContextLoaderListener`加载。
而文件（classpath:resources/spring-mvc.xml）会被`DispatcherServlet`加载。

（`DispatcherServlet`会根据Servlet的名字找到一个文件，并基于该文件加载应用上下文。如果我们不对其`contextConfigLocation`进行配置，那么由于这里Servlet的名字是springmvc，因此`DispatcherServlet`默认会从`/WEBINF/springmvc-context.xml`文件中加载其应用上下文。）

下面我们进行如下配置，使用web.xml但不使用诸如spring-*.xml之类的配置文件，而是从Java类中加载配置。（我们之前的搭建方式是不用web.xml，而是定义一个类`SpittrWebAppInitializer`，这个类继承了`AbstractAnnotationConfigDispatcherServletInitializer`。）

要使用如上描述的配置方法，我们需要告诉`DispatcherServlet`和`ContextLoaderListener`使用`AnnotationConfigWebApplicationContext`，这是一个`WebApplicationContext`的实现类，它会加载Java配置类，而不是使用XML。要实现这种配置，我们可以设置`contextClass`上下文参数以及`DispatcherServlet`的初始化参数。

```xml
<!-- 设置contextClass属性，使用Java配置 -->
<context-param>
    <param-name>contextClass</param-name>
    <param-value>
        org.springframework.web.context.support.AnnotationConfigWebApplicationContext
    </param-value>
</context-param>

<!-- 指定根配置类 -->
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>foo.bar.config.RootConfig</param-value>
</context-param>

<!-- 注册ServletContext监听器 -->
<listener>
    <listener-class>
        org.springframework.web.context.ContextLoaderListener
    </listener-class>
</listener>

<!-- 注册DispatcherServlet -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <init-param>
        <!-- 设置contextClass属性，使用Java配置 -->
        <param-name>contextClass</param-name>
        <param-value>
            org.springframework.web.context.support.AnnotationConfigWebApplicationContext
        </param-value>
    </init-param>
    <init-param>
        <!-- 指定DispatcherServlet的配置类 -->
        <param-name>contextConfigLocation</param-name>
        <param-value>foo.bar.config.WebConfig</param-value>
    </init-param>
    <load-oun-startup>1</load-oun-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>    
```

### 7.2 处理multipart形式的数据

在Web应用中，允许用户上传内容是很常见的需求。

Spittr应用在两个地方需要文件上传。当新用户注册应用的时候，我们希望他们能够上传一张图片，从而与他们的个人信息相关联。当用户提交新的Spittle时，除了文本消息以外，他们可能还会上传一张照片。

`multipart`格式的数据会将一个表单拆分为多个部分（part），每个部分对应一个输入域。在一般的表单输入域中，它所对应的部分中会放置文本型数据，但是如果上传文件的话，它所对应的部分可以是二进制，下面展现了`multipart`的请求体：

```text
------WebKitFormBoundaryqgkaBn8IHJCuNmiW
Content-Disposition: form-data; name="firstName"
Charles
------WebKitFormBoundaryqgkaBn8IHJCuNmiW
Content-Disposition: form-data; name="lastName"
Xavier
------WebKitFormBoundaryqgkaBn8IHJCuNmiW
Content-Disposition: form-data; name="email"
charles@xmen.com
------WebKitFormBoundaryqgkaBn8IHJCuNmiW
Content-Disposition: form-data; name="username"
professorx
------WebKitFormBoundaryqgkaBn8IHJCuNmiW
Content-Disposition: form-data; name="password"
letmein01
------WebKitFormBoundaryqgkaBn8IHJCuNmiW
Content-Disposition: form-data; name="profilePicture"; filename="me.jpg"
Content-Type: image/jpeg
[[ Binary image data goes here ]]
------WebKitFormBoundaryqgkaBn8IHJCuNmiW--
```

在这个`multipart`的请求中，我们可以看到`profilePicture`部分与其他部分明显不同。除了其他内容以外，它还有自己的`Content-Type`头，表明它是一个JPEG图片。

尽管`multipart`请求看起来很复杂，但在Spring MVC中处理它们却很容易。在编写控制器方法处理文件上传之前，我们必须要配置一个`multipart`解析器，通过它来告诉`DispatcherServlet`该如何读取`multipart`请求。

#### 7.2.1 配置multipart解析器

`DispatcherServlet`并没有实现任何解析`multipart`请求数据的功能。它将该任务委托给了Spring中`MultipartResolver`策略接口的实现，通过这个实现类来解析`multipart`请求中的内容。从Spring 3.1开始，Spring内置了两个MultipartResolver的实现供我们选择：

- CommonsMultipartResolver：使用commons-fileupload解析`multipart`请求
- StandardServletMultipartResolver：依赖于Servlet 3.0对`multipart`请求的支持

一般来讲，在这两者之间，`StandardServletMultipartResolver`可能会是优选的方案（注意：该类无法指定字符集，默认字符集不是UTF-8。这意味着当你的文件名为中文时，文件名会乱码，解决方案只能是自己写一个过滤器进行过滤）。它使用Servlet所提供的功能支持，并不需要依赖任何其他的项目。如果我们需要将应用部署到Servlet 3.0之前的容器中，或者还没有使用Spring 3.1或更高版本，那么可能就需要`CommonsMultipartResolver`了。

兼容Servlet 3.0的`StandardServletMultipartResolver`没有构造器参数，也没有要设置的属性。这样，在Spring应用上下文中，将其声明为bean就会非常简单，如下所示：

```java
@Bean
public MultipartResolver multipartResolver() {
    return new StandardServletMultipartResolver();
}
```

既然这个`@Bean`方法如此简单，你可能就会怀疑我们到底该如何限制`StandardServletMultipartResolver`的工作方式呢。如果我们想要限制用户上传文件的大小，该怎么实现？如果我们想要指定文件在上传时，临时写入目录在什么位置的话，该如何实现？因为没有属性和构造器参数，`StandardServletMultipartResolver`的功能看起来似乎有些受限。

并不是这样的，我们是有办法配置`StandardServletMultipartResolver`的限制条件的。只不过不是在Spring中配置，而是要在Servlet中指定。具体来讲，我们必须要在web.xml或Servlet初始化类中，将`multipart`的具体细节作为`DispatcherServlet`配置的一部分。

如果我们是采用Servlet初始化类的方式来配置DispatcherServlet的话，由于这个类已经实现了`WebApplicationInitializer`，那我们可以进行如下配置：

```java
public class MyServletInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletContext) throws ServletException {

        DispatcherServlet ds = new DispatcherServlet();

        ServletRegistration.Dynamic registration = servletContext.addServlet("springmvc", ds);
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
        registration.setMultipartConfig(new MultipartConfigElement("/tmp/spittr/uploads"));
    }
}
```

如果我们配置`DispatcherServlet`的Servlet初始化类继承了`AbstractAnnotationConfigDispatcherServletInitializer`或`AbstractDispatcherServletInitializer`的话，那么我们不会直接创建`DispatcherServlet`实例并将其注册到Servlet上下文中。这样的话，将不会有对`ServletRegistration.Dynamic`的引用供我们使用了。但是，我们可以通过重载`customizeRegistration()`方法（它会得到一个Dynamic作为参数）来配置`multipart`的具体细节：

```java
package spittr.config;

import javax.servlet.Filter;
import javax.servlet.MultipartConfigElement;
import javax.servlet.ServletRegistration.Dynamic;

import org.springframework.web.filter.CharacterEncodingFilter;
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

    // 配置字符集过滤器
    @Override
    protected Filter[] getServletFilters() {
        CharacterEncodingFilter filter = new CharacterEncodingFilter();
        filter.setEncoding("UTF-8");
        filter.setForceEncoding(true);
        return new Filter[] { filter };
    }
    
    // 配置Multipart请求的上传路径
    @Override
    protected void customizeRegistration(Dynamic registration) {
        registration.setMultipartConfig(new MultipartConfigElement("/tmp/spittr/uploads"));
    }
}
```

除了临时路径的位置，我们还应该对接受的大小进行限制，假设我们想限制文件的大小不超过2MB，整个请求不超过4MB，而且所有的文件都要写到磁盘中。则应该使用如下配置：

```java
@Override
protected void customizeRegistration(Dynamic registration) {
    registration.setMultipartConfig(
            new MultipartConfigElement("/tmp/spittr/uploads", 2 * 1024 * 1024, 4 * 1024 * 1024, 0));
}
```

如果我们使用XML配置的话，则应该这样写：

```xml
<!-- 注册DispatcherServlet -->
<servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>
        org.springframework.web.servlet.DispatcherServlet
    </servlet-class>
    <init-param>
        <!-- 在这里指定其配置文件 -->
        <!-- 通常我们将Spring的配置文件与Spring MVC的配置文件分开 -->
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:resources/spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>

    <multipart-config>
        <location>/tmp/spittr/uploads</location>
        <max-file-size>2097152</max-file-size>
        <max-request-size>20971520</max-request-size>
        <file-size-threshold>0</file-size-threshold>
    </multipart-config>
</servlet>
<servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

无论是通过`customizeRegistration()`方法配置还是使用XML配置，`location`属性是必须要配置的。

**配置CommonsMultipartResolver**

通常来讲，`StandardServletMultipartResolver`会是最佳的选择，但是如果我们需要将应用部署到非Servlet 3.0的容器中，那么就得需要替代的方案。如果喜欢的话，我们可以编写自己的MultipartResolver实现。不过，除非想要在处理multipart请求的时候执行特定的逻辑，否则的话，没有必要这样做。Spring内置了`CommonsMultipartResolver`，可以作为`StandardServletMultipartResolver`的替代方案。（需要导入commons-fileupload的Jar包）

将`CommonsMultipartResolver`声明为Spring bean的最简单的方式如下：

```java
@Bean MultipartResolver multipartResolver() {
    return new CommonsMultipartResolver();
}
```

与`StandardServletMultipartResolver`有所不同，`CommonsMultipartResolver`不会强制要求设置临时文件路径。默认情况下，这个路径就是`Servlet`容器的临时目录。不过，通过设置`uploadTempDir`属性，我们可以将其指定为一个不同的位置：

```java
@Bean MultipartResolver multipartResolver() {
    CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();
    multipartResolver.setUploadTempDir(new FileSystemResources("/tmp/spittr/uploads"));
    return multipartResolver;
}
```

同样地，我们也可以指定上传文件的大小：

```java
@Bean MultipartResolver multipartResolver() {
    CommonsMultipartResolver multipartResolver = new CommonsMultipartResolver();
    multipartResolver.setUploadTempDir(new FileSystemResources("/tmp/spittr/uploads"));
    multipartResolver.setMaxUploadSize(4 * 1024 * 1024);
    multipartResolver.setMaxUploadSizePerFile(2 * 1024 * 1024);
    multipartResolver.setMaxInMemorySize(0);
    return multipartResolver;
}
```

在这里，我们将设置一次`Multipart`请求最大可以上传总共4MB、多文件下单个文件不能超过2MB，最大的内存大小设置为0字节，且不管文件的大小如何，所有的文件都会写到磁盘中。

#### 7.22 处理multipart请求

接下来我们就可以编写控制器方法来接收上传的文件。要实现这一点，最常见的方式就是在某个控制器方法参数上添加`@RequestPart`注解。

假设我们允许用户在注册Spittr应用的时候上传一张图片，那么我们需要修改表单，以允许用户选择要上传的图片，同时还需要修改`SpitterController`中的`processRegistration()`方法来接收上传的图片。

修改后的表单：

```html
<form method="POST" th:action="@{/spitter/register}" th:object="${spitter}" enctype="multipart/form-data">
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
    
    <label>Profile Picture</label>:
    <input type="file" name="profilePicture" accept="image/jpeg,image/png,image/gif" />
    <br />
    
    <input type="submit" value="Register" />
</form>
```

现在我们指定表单的`enctype`属性为`multipart/form-data`，这回告诉浏览器以`multipart`的形式提交表单，而不是以表单数据的形式提交。这里每个输入域都会对应一个part。

我们还添加了一个新的`<input>`域，其`type`属性为`file`。这能让用户选择要上传的文件。`accept`属性用来将文件类型限制为jpeg、png以及gif图片。根据其`name`属性，图片数据将会发送到`multipart`请求中的`profilePicture`part中。

现在我们需要修改`processRegistration()`方法，使其能够接受上传的图片。其中一种方式是添加`byte[]`参数，并为其添加`@RequestPart`注解：

```java
@RequestMapping(value = "/register", method = RequestMethod.POST)
public String processRegistration(
    @Validated Spitter spitter,
    @RequestPart("profilePicture") byte[] profilePicture, 
    BindingResult br) {

    // ...
}
```

当注册表单提交的时候，`profilePicture`属性将会给定一个`byte[]`，这个数组中包含了请求中对应part的数据（通过`@RequestPart`指定）。如果用户提交表单的时候没有选择文件，那么这个数组会是空（而不是`null`）。获取到图片数据后，`processRegistration()`方法剩下的任务就是将文件保存到某个位置。

我们将会稍后讨论如何保存文件。但首先，想一下，对于提交的图片数据我们都了解哪些信息呢。或者，更为重要的是，我们还不知道些什么呢？尽管我们已经得到了`byte[]`形式的图片数据，并且根据它能够得到图片的大小，但是对于其他内容我们就一无所知了。我们不知道文件的类型是什么，甚至不知道原始的文件名是什么。你需要判断如何将`byte[]`转换为可存储的文件。

**接受MultipartFile**

事实上，这里使用`byte[]`作为参数功能很有限。因此，Spring还提供了`MultipartFile`接口，它为处理`multipart`数据提供了内容更为丰富的对象。

`MultipartFile`接口：

```java
public interface MultipartFile extends InputStreamSource {

    String getName();

    String getOriginalFilename();

    String getContentType();

    boolean isEmpty();

    long getSize();

    byte[] getBytes() throws IOException;

    @Override
    InputStream getInputStream() throws IOException;

    void transferTo(File dest) throws IOException, IllegalStateException;
}
```

我们可以看到，`MultipartFile`提供了获取上传文件`byte[]`的方式，但是它所提供的功能并不仅限于此，还能获得原始的文件名、大小以及内容类型。它还提供了一个`InputStream`，用来将文件数据以流的方式进行读取。除此之外，`MultipartFile`还提供了一个便利的`transferTo()`方法，它能够帮助我们将上传的文件写入到文件系统中。

```java
profilePicture.transferTo(new File("/data/spittr/" + profilePicture.getOriginalFilename()));
```

完整代码如下：

```java
@RequestMapping(value = "/register", method = RequestMethod.POST)
public String processRegistration(
        @Validated Spitter spitter, 
        BindingResult br, 
        HttpSession session,
        MultipartFile profilePicture ) throws IllegalStateException, IOException {

    List<ObjectError> errors = br.getAllErrors();

    if (errors.size() > 0) {
        return "registerForm";
    }
    
    if (!profilePicture.isEmpty()) {
        
        // 通过sesion获取profilePictures文件夹在项目里的真正目录
        String path = session.getServletContext().getRealPath("/profilePictures");
        
        // 如果没有这个文件夹，则创建一个
        File file = new File(path);
        if (!file.exists()) {
            file.mkdir();
        }
        
        String filename = profilePicture.getOriginalFilename();
        
        if (filename.endsWith(".jpg") || filename.endsWith(".jpeg") || filename.endsWith(".png") || filename.endsWith(".gif")) {
            profilePicture.transferTo(new File(path, filename));
        } else {
            // 文件格式不对，返回表单
            return "registerForm";
        }
    }

    // 保存Spitter
    spitterRepository.save(spitter);

    // 重定向到基本信息页
    return "redirect:/spitter/" + spitter.getUsername();
}
```

注意，在tomcat下，如果我们只是配置了上述代码，则上传文件时会报错：

>
org.springframework.web.multipart.MultipartException: Could not parse multipart servlet request; nested exception is java.io.IOException: The temporary upload location [D:\Program Files\apache-tomcat-9.0.0.M26\work\Catalina\localhost\sia4e-P2_Spring_on_the_web-C07_Advanced_Spring_MVC\tmp\spittr\uploads] is not valid

也就是说，我们创建的临时目录不存在，即临时目录需要我们自己创建。

创建临时目录后，再次提交表单，上传成功。

**以Part的形式接受上传的文件**

如果你需要将应用部署到Servlet 3.0的容器中，那么会有`MultipartFile`的一个替代方案。Spring MVC也能接受`javax.servlet.http.Part`作为控制器方法的参数。

`Part`接口：

```java
/*
 * DO NOT ALTER OR REMOVE COPYRIGHT NOTICES OR THIS HEADER.
 *
 * Copyright (c) 1997-2013 Oracle and/or its affiliates. All rights reserved.
 *
 * The contents of this file are subject to the terms of either the GNU
 * General Public License Version 2 only ("GPL") or the Common Development
 * and Distribution License("CDDL") (collectively, the "License").  You
 * may not use this file except in compliance with the License.  You can
 * obtain a copy of the License at
 * https://glassfish.dev.java.net/public/CDDL+GPL_1_1.html
 * or packager/legal/LICENSE.txt.  See the License for the specific
 * language governing permissions and limitations under the License.
 *
 * When distributing the software, include this License Header Notice in each
 * file and include the License file at packager/legal/LICENSE.txt.
 *
 * GPL Classpath Exception:
 * Oracle designates this particular file as subject to the "Classpath"
 * exception as provided by Oracle in the GPL Version 2 section of the License
 * file that accompanied this code.
 *
 * Modifications:
 * If applicable, add the following below the License Header, with the fields
 * enclosed by brackets [] replaced by your own identifying information:
 * "Portions Copyright [year] [name of copyright owner]"
 *
 * Contributor(s):
 * If you wish your version of this file to be governed by only the CDDL or
 * only the GPL Version 2, indicate your decision by adding "[Contributor]
 * elects to include this software in this distribution under the [CDDL or GPL
 * Version 2] license."  If you don't indicate a single choice of license, a
 * recipient has the option to distribute your version of this file under
 * either the CDDL, the GPL Version 2 or to extend the choice of license to
 * its licensees as provided above.  However, if you add GPL Version 2 code
 * and therefore, elected the GPL Version 2 license, then the option applies
 * only if the new code is made subject to such option by the copyright
 * holder.
 */

package javax.servlet.http;

import java.io.*;
import java.util.*;

/**
 * <p> This class represents a part or form item that was received within a
 * <code>multipart/form-data</code> POST request.
 * 
 * @since Servlet 3.0
 */
public interface Part {

    public InputStream getInputStream() throws IOException;

    public String getContentType();

    public String getName();

    public String getSubmittedFileName();

    public long getSize();

    public void write(String fileName) throws IOException;

    public void delete() throws IOException;

    public String getHeader(String name);

    public Collection<String> getHeaders(String name);

    public Collection<String> getHeaderNames();

}
```

在很多情况下，`Part`方法的名称与`MultipartFile`方法的名称是完全相同的。有一些比较类似，但是稍有差异，比如`getSubmittedFileName()`对应于`getOriginalFilename()`。类似地，`write()`对应于`transferTo()`。

使用`Part`作为参数的`processRegistration()`的代码：

```java
@RequestMapping(value = "/register", method = RequestMethod.POST)
public String processRegistration(
        @Validated Spitter spitter, 
        BindingResult br, 
        HttpSession session,
        Part profilePicture ) throws IllegalStateException, IOException {

    List<ObjectError> errors = br.getAllErrors();

    if (errors.size() > 0) {
        return "registerForm";
    }
    
    if (profilePicture.getSize() != 0) {
        
        String path = session.getServletContext().getRealPath("/profilePictures");
        
        File file = new File(path);
        if (!file.exists()) {
            file.mkdir();
        }
        
        String filename = profilePicture.getSubmittedFileName();
        
        if (filename.endsWith(".jpg") || filename.endsWith(".jpeg") || filename.endsWith(".png") || filename.endsWith(".gif")) {
            profilePicture.write(new File(path, filename).toString());
        } else {
            return "registerForm";
        }
    }

    // 保存Spitter
    spitterRepository.save(spitter);

    // 重定向到基本信息页
    return "redirect:/spitter/" + spitter.getUsername();
}
```

另外，如果我们使用`Part`作为参数，那么就没有必要配置`MultipartResolver`。只有使用`MultipartFile`时我们才需要配置`MultipartResolver`。（注意，测试时即使我们不配置`MultipartResolver`，但也要通过`customizeRegistration()`方法设置临时目录。否则会出错，而错误信息是“First name must not be null.”等，很奇怪。）

### 7.3 处理异常

到现在为止，在Spittr应用中，我们假设所有的功能都正常运行。但是如果某个地方出错的话，该怎么办呢？当处理请求的时候，抛出异常该怎么处理呢？如果发生了这样的情况，该给客户端什么响应呢？

不管发生什么事情，不管是好的还是坏的，Servlet请求的输出都是一个Servlet响应。如果在请求处理的时候，出现了异常，那它的输出依然会是Servlet响应。异常必须要以某种方式转换为响应。

Spring提供了多种方式将异常转换为响应：

- 特定的Spring异常将会自动映射为指定的HTTP状态码；
- 异常上可以添加@ResponseStatus注解，从而将其映射为某一个HTTP状态码；
- 在方法上可以添加@ExceptionHandler注解，使其用来处理异常。

处理异常的最简单方式就是将其映射到HTTP状态码上，进而放到响应之中。接下来，我们看一下如何将异常映射为某一个HTTP状态码。

#### 7.3.1 将异常映射为HTTP状态码

在默认情况下，Spring会将自身的一些异常自动转换为合适的状态码：

Spring 异常 | HTTP状态码
-----|-----
`BindException` | 400 - Bad Request
`ConversionNotSupportedException` | 500 - Internal Server Error
`HttpMediaTypeNotAcceptableException` | 406 - Not Acceptable
`HttpMediaTypeNotSupportedException` | 415 - Unsupported Media Type
`HttpMessageNotReadableException` | 400 - Bad Request
`HttpMessageNotWritableException` | 500 - Internal Server Error
`HttpRequestMethodNotSupportedException` | 405 - Method Not Allowed
`MethodArgumentNotValidException` | 400 - Bad Request
`MissingServletRequestParameterException` | 400 - Bad Request
`MissingServletRequestPartException` | 400 - Bad Request
`NoSuchRequestHandlingMethodException` | 404 - Not Found
`TypeMismatchException` | 400 - Bad Request

上述异常一般会由Spring自身抛出，作为`DispatcherServlet`处理过程中或执行校验时出现问题的结果。例如，如果`DispatcherServlet`无法找到适合处理请求的控制器方法，那么将会抛出`NoSuchRequestHandlingMethodException`异常，最终的结果就是产生404状态码的响应（Not Found）。

尽管这些内置的映射是很有用的，但是对于应用所抛出的异常它们就无能为力了。幸好，Spring提供了一种机制，能够通过`@ResponseStatus`注解将异常映射为HTTP状态码。

参考以下代码：

```java
@RequestMapping(value = "/{spittleId}", method = RequestMethod.GET)
public String showSpittle(@PathVariable long spittleId, Model model) {

    Spittle spittle = spittleRepository.findOne(spittleId);

    if (spittle == null) {
        throw new SpittleNotFoundException();
    }

    model.addAttribute("spittle", spittleRepository.findOne(spittleId));
    return "spittle";
}
```

在这里，会从`SpittleRepository`中，通过ID检索`Spittle`对象。如果`findOne()`方法能够返回`Spittle`对象的话，那么会将`Spittle`放到模型中，然后名为`spittle`的视图会负责将其渲染到响应之中。但是如果`findOne()`方法返回`null`的话，那么将会抛出`SpittleNotFoundException`异常。

`SpittleNotFoundException`非检查型异常：

```java
public class SpittleNotFoundException extends RuntimeException {

}
```

在没有进行映射的情况下，如果发生了`SpittleNotFoundException`异常，那么会产生500状态码（Internal Server Error）响应。实际上，如果出现任何没有映射的异常，响应都会带有500状态码。

当抛出`SpittleNotFoundException`异常时，这是一种请求资源没有找到的场景。如果资源没有找到的话，HTTP状态码404是最为精确的响应状态码。所以，我们要使用`@ResponseStatus`注解将`SpittleNotFoundException`映射为HTTP状态码404：

```java
@ResponseStatus(value = HttpStatus.NOT_FOUND, reason = "Spittle Not Found")
public class SpittleNotFoundException extends RuntimeException {

    private static final long serialVersionUID = -697725134282567926L;

}
```

在引入`@ResponseStatus`注解之后，如果控制器方法抛出`SpittleNotFoundException`异常的话，响应将会具有404状态码，这是因为Spittle Not Found。

#### 7.3.2 编写异常处理的方法

在很多的场景下，将异常映射为状态码是很简单的方案，并且就功能来说也足够了。但是如果我们想在响应中不仅要包括状态码，还要包含所产生的错误，那该怎么办呢？此时的话，我们就不能将异常视为HTTP错误了，而是要按照处理请求的方式来处理异常了。

作为样例，假设用户试图创建的`Spittle`与已创建的`Spittle`文本完全相同，那么`SpittleRepository`的`save()`方法将会抛出`DuplicateSpittleException`异常。这意味着`SpittleController`的`saveSpittle()`方法可能需要处理这个异常：

```java
@RequestMapping(method = RequestMethod.POST)
public String saveSpittle(SpittleForm form, Model model) {
    try {
        spittleRepository.save(new Spittle(form.getMessage(), new Date(), form.getLatitude(), form.getLongitude()));
        return "redirect:/spittles";
    } catch (DuplicateSpittleException e) {
        return "error/duplicate";
    }
}
```

它运行起来没什么问题，但是这个方法有些复杂。该方法可以有两个路径，每个路径会有不同的输出。如果能让`saveSpittle()`方法只关注正确的路径，而让其他方法处理异常的话，那么它就能简单一些。

首先我们将方法中的异常处理逻辑剥离：

```java
@RequestMapping(method = RequestMethod.POST)
public String saveSpittle(SpittleForm form, Model model) {
    spittleRepository.save(new Spittle(form.getMessage(), new Date(), form.getLatitude(), form.getLongitude()));
    return "redirect:/spittles";
}
```

现在，我们为`SpittleController`添加一个新的方法，它会处理抛出`DuplicateSpittleException`的情况：

```java
@ExceptionHandler(DuplicateSpittleException.class) 
public String handleDuplicateSpittle() {
    return "error/duplicate";
}
```

这个方法上添加了`@ExceptionHandler`注解，当抛出`DuplicateSpittleException`异常的时候，将会委托该方法来处理。它返回的是一个`String`，这与处理请求的方法是一致的，指定了要渲染的逻辑视图名，它能够告诉用户他们正在试图创建一条重复的条目。

对于`@ExceptionHandler`注解标注的方法来说，比较有意思的一点在于它能处理同一个控制器中所有处理器方法所抛出的异常。所以，尽管我们从`saveSpittle()`中抽取代码创建了`handleDuplicateSpittle()`方法，但是它能够处理`SpittleController`中所有方法所抛出的`DuplicateSpittleException`异常。我们不用在每一个可能抛出`DuplicateSpittleException`的方法中添加异常处理代码，这一个方法就涵盖了所有的功能。

既然@ExceptionHandler注解所标注的方法能够处理同一个控制器类中所有处理器方法的异常，那么你可能会问有没有一种方法能够处理所有控制器中处理器方法所抛出的异常呢。从Spring 3.2开始，这肯定是能够实现的，我们只需将其定义到控制器通知类中即可。

### 7.4 为控制器添加通知

如果控制器类的特定切面能够运用到整个应用程序的所有控制器中，那么这将会便利很多。举例来说，如果要在多个控制器中处理异常，那`@ExceptionHandler`注解所标注的方法是很有用的。不过，如果多个控制器类中都会抛出某个特定的异常，那么你可能会发现要在所有的控制器方法中重复相同的`@ExceptionHandler`方法。或者，为了避免重复，我们会创建一个基础的控制器类，所有控制器类要扩展这个类，从而继承通用的`@ExceptionHandler`方法。

Spring 3.2为这类问题引入了一个新的解决方案：控制器通知。控制器通知（controller advice）是任意带有`@ControllerAdvice`注解的类，这个类会包含一个或多个如下类型的方法：

- @ExceptionHandler注解标注的方法；
- @InitBinder注解标注的方法；
- @ModelAttribute注解标注的方法。

在带有`@ControllerAdvice`注解的类中，以上所述的这些方法会运用到整个应用程序所有控制器中带有`@RequestMapping`注解的方法上。

`@ControllerAdvice`注解本身已经使用了`@Component`，因此`@ControllerAdvice`注解所标注的类将会自动被组件扫描获取到，就像带有`@Component`注解的类一样。

`@ControllerAdvice`最为实用的一个场景就是将所有的`@ExceptionHandler`方法收集到一个类中，这样所有控制器的异常就能在一个地方进行一致的处理。例如，我们想将`DuplicateSpittleException`的处理方法用到整个应用程序的所有控制器上：

```java
package spittr.web;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

import spittr.web.exceptions.DuplicateSpittleException;

@ControllerAdvice
public class AppWideExceptionHandler {

    @ExceptionHandler(DuplicateSpittleException.class)
    public String duplicateSpittleHandler() {
        return "error/duplicate";
    }
}
```

现在，如果任意的控制器方法抛出了`DuplicateSpittleException`，不管这个方法位于哪个控制器中，都会调用这个`duplicateSpittleHandler()`方法来处理异常。

### 7.5 跨重定向请求传递数据

在处理完POST请求后，通常来讲一个最佳实践就是执行一下重定向。除了其他的一些因素外，这样做能够防止用户点击浏览器的刷新按钮或后退箭头时，客户端重新执行危险的POST请求。

在控制器方法返回的视图名称中，当控制器方法返回的String值以“redirect:”开头的话，那么这个`String`不是用来查找视图的，而是用来指导浏览器进行重定向的路径。

“redirect:”前缀能够让重定向功能变得非常简单。你可能会想Spring很难再让重定向功能变得更简单了。但是，请稍等：Spring为重定向功能还提供了一些其他的辅助功能。

具体来讲，正在发起重定向功能的方法该如何发送数据给重定向的目标方法呢？一般来讲，当一个处理器方法完成之后，该方法所指定的模型数据将会复制到请求中，并作为请求中的属性，请求会转发（forward是默认的，当然也可以显式指定“forward:”）到视图上进行渲染。因为控制器方法和视图所处理的是同一个请求，所以在转发的过程中，请求属性能够得以保存。

但是，如下图所示，当控制器的结果是重定向的话，原始的请求就结束了，并且会发起一个新的GET请求。原始请求中所带有的模型数据也就随着请求一起消亡了。在新的请求属性中，没有任何的模型数据，这个请求必须要自己计算数据。

<center>
    ![图7.1-执行重定向](images\图7.1-执行重定向.PNG)
    **模型的属性是以请求属性的形式存放在请求中的，在重定向后无法存活**
</center>

显然，对于重定向来说，模型并不能用来传递数据。但是我们也有一些其他方案，能够从发起重定向的方法传递数据给处理重定向方法中：

- 使用UTL模板以路径变量和（或）查询参数的形式传递数据
- 通过flash属性发送数据

#### 7.5.1 通过URL模板进行重定向

通过路径变量和查询参数传递数据看起来非常简单。例如，我们以路径变量的形式传递了新创建`Spitter`的`username`。但是按照现在的写法，`username`的值是直接连接到重定向`String`上的。这能够正常运行，但是还远远不能说没有问题。当构建URL或SQL查询语句的时候，使用`String`连接是很危险的。

```java
return "redirect:/spitter/" + spitter.getUsername();
```

除了连接`String`的方式来构建重定向URL，Spring还提供了使用模板的方式来定义重定向URL。例如， `processRegistration()`方法的最后几行可以改写为如下的形式：

```java
@RequestMapping(value = "/register", method = RequestMethod.POST)
public String processRegistration(
        @Validated Spitter spitter, 
        BindingResult br, 
        HttpSession session,
        MultipartFile profilePicture,
        Model model ) throws IllegalStateException, IOException {

    // ..

    spitterRespository.save(spitter);

    model.addAttribute("username", spitter.getUsername());
    return "redirect:/spitter/{username}";
}
```

现在，`username`作为占位符填充到了URL模板中，而不是直接连接到重定向`String`中，所以`username`中所有的不安全字符都会进行转义。这样会更加安全，这里允许用户输入任何想要的内容作为`username`，并会将其附加到路径上。

除此之外，模型中所有其他的原始类型值都可以添加到URL中作为查询参数。作为样例，假设除了`username`以外，模型中还要包含新创建`Spitter`对象的`id`属性，那`processRegistration()`方法可以改写为如下的形式：

```java
model.addAttribute("username", spitter.getUsername());
model.addAttribute("spitterId", spitter.getId());
return "redirect:/spitter/{username}";
```

所返回的重定向`String`并没有太大的变化。但是，因为模型中的`spitterId`属性没有匹配重定向URL中的任何占位符，所以它会自动以查询参数的形式附加到重定向URL上。

如果`username`属性的值是habuma并且`spitterId`属性的值是42，那么结果得到的重定向URL路径将会是“/spitter/habuma?spitterId=42”。

通过路径变量和查询参数的形式跨重定向传递数据是很简单直接的方式，但它也有一定的限制。它只能用来发送简单的值，如String和数字的值。在URL中，并没有办法发送更为复杂的值。

#### 7.5.2 使用flash属性

假设我们不想在重定向中发送username或ID了，而是要发送实际的`Spitter`对象。如果我们只发送ID的话，那么处理重定向的方法还需要从数据库中查找才能得到`Spitter`对象。但是，在重定向之前，我们其实已经得到了`Spitter`对象。为什么不将其发送给处理重定向的方法，并将其展现出来呢？

`Spitter`对象要比`String`和`int`更为复杂。因此，我们不能像路径变量或查询参数那么容易地发送`Spitter`对象。它只能设置为模型中的属性。

但是，正如我们前面所讨论的那样，模型数据最终是以请求参数的形式复制到请求中的，当重定向发生的时候，这些数据就会丢失。因此，我们需要将`Spitter`对象放到一个位置，使其能够在重定向的过程中存活下来。

有个方案是将`Spitter`放到会话中。会话能够长期存在，并且能够跨多个请求。所以我们可以在重定向发生之前将`Spitter`放到会话中，并在重定向后，从会话中将其取出。当然，我们还要负责在重定向后在会话中将其清理掉。

实际上，Spring也认为将跨重定向存活的数据放到会话中是一个很不错的方式。但是，Spring认为我们并不需要管理这些数据（当然，我们也可以用`HttpSession`来自己实现），相反，Spring提供了将数据发送为flash属性（flash attribute）的功能。按照定义，flash属性会一直携带这些数据直到下一次请求，然后才会消失。

Spring提供了通过`RedirectAttributes`设置flash属性的方法，这是Spring 3.1引入的Model的一个子接口。`RedirectAttributes`提供了`Model`的所有功能，除此之外，还有几个方法是用来设置flash属性的。具体来讲，`RedirectAttributes`提供了一组`addFlashAttribute()`方法来添加flash属性

```java
model.addAttribute("username", spitter.getUsername());
model.addFlashAttribute("spitter", spitter);
return "redirect:/spitter/" + spitter.getUsername();
```

我们调用了`addFlashAttribute()`方法，并将spitter作为key，`Spitter`对象作为值。

在重定向执行之前，所有的flash属性都会复制到会话中。在重定向后，存在会话中的flash属性会被取出，并从会话转移到模型之中。处理重定向的方法就能从模型中访问`Spitter`对象了，就像获取其他的模型对象一样。下图展示了它是如何运行的：

<center>
    ![图7.2-flash属性在请求之间传递](images\图7.2-flash属性在请求之间传递.PNG)
    **flash属性保存在会话中，然后再放到模型中，因此能够在重定向的过程中存活**
</center>

为了完成flash属性的流程，如下展现了更新版本的`showSpitterProfile()`方法，在从数据库中查找之前，它会首先从模型中检查`Spitter`对象：

```java
@RequestMapping(value = "/{username}", method = RequestMethod.GET)
public String showSpitterProfile(@PathVariable String username, Model model) {
    
    if (!model.containsAttribute("spitter")) {
        Spitter spitter = spitterRepository.findByUsername(username);
        model.addAttribute("spitter", spitter);
    }
    return "profile";
}
```

可以看到，`showSpitterProfile()`方法所做的第一件事就是检查是否存有key为spitter的model属性。如果模型中包含spitter属性，那就什么都不用做了。这里面包含的`Spitter`对象将会传递到视图中进行渲染。但是如果模型中不包含spitter属性的话，那么`showSpitterProfile()`将会从Repository中查找`Spitter`，并将其存放到模型中。

### 7.6 小结

>
在Spring中，总是会有“还没有结束”的感觉：更多的特性、更多的选择以及实现开发目标的更多方式。Spring MVC有很多功能和技巧。
>
当然，Spring MVC的环境搭建是有多种可选方案的一个领域。在本章中，我们首先看了一下搭建Spring MVC中`DispatcherServlet`和`ContextLoaderListener`的多种方式。我们还看到了如何调整`DispatcherServlet`的注册功能以及如何注册自定义的Servlet和Filter。如果你需要将应用部署到更老的应用服务器上，我们还快速了解了如何使用web.xml声明`DispatcherServlet`和`ContextLoaderListener`。
>
然后，我们了解了如何处理Spring MVC控制器所抛出的异常。尽管带有`@RequestMapping`注解的方法可以在自身的代码中处理异常，但是如果我们将异常处理的代码抽取到单独的方法中，那么控制器的代码会整洁得多。
>
为了采用一致的方式处理通用的任务，包括在应用的所有控制器中处理异常，Spring 3.2引入了`@ControllerAdvice`，它所创建的类能够将控制器的通用行为抽取到同一个地方。
>
最后，我们看了一下如何跨重定向传递数据，包括Spring对flash属性的支持：类似于模型的属性，但是能在重定向后存活下来。这样的话，就能采用非常恰当的方式为POST请求执行一个重定向回应，而且能够将处理POST请求时的模型数据传递过来，然后在重定向后使用或展现这些模型数据。
>
如果你还有疑惑的话，那么可以告诉你，这就是我所说的“更多的功能”！其实，我们并没有讨论到Spring MVC的每个方面。我们将会在第16章中重新讨论Spring MVC，到时你会看到如何使用它来创建REST API。
>
但现在，我们将会暂时放下Spring MVC，看一下Spring Web Flow，这是一个构建在Spring MVC之上的流程框架，它能够引导用户执行一系列向导步骤。

## 第八章 使用Spring Web Flow

本章内容：

- 创建会话式的Web应用程序
- 定义流程状态和行为
- 保护Web流程

Spring Web Flow是一个Web框架，它适用于元素按规定流程运行的程序。在本章中，我们将会探索Spring Web Flow并了解它如何应用于Spring Web框架平台。

Spring Web Flow是Spring MVC的扩展，它支持开发基于流程的应用程序。它将流程的定义与实现流程行为的类和视图分离开来。

### 8.1 在Spring中配置Web Flow

*以下内容代码在工程sia4e-P2_Spring_on_the_web-C08_Working_with_Spring_Web_Flow中*。（原书中并没有完成该工程）

Spring Web Flow是构建于Spring MVC基础之上的。这意味着所有的流程请求都需要首先经过Spring MVC的`DispatcherServlet`。我们需要在Spring应用上下文中配置一些bean来处理流程请求并执行流程。

现在，还不支持在Java中配置Spring Web Flow，所以我们别无选择，只能在XML中对其进行配置。
（事实上，从Spring Web Flow 2.4开始，已经支持了使用JavaConfig的方式进行配置，具体参见[Spring Web Flow-System Setup](https://docs.spring.io/spring-webflow/docs/2.5.0.RELEASE/reference/html/system-setup.html){:target="_blank"}。这里我们使用XML配置）

要在XML中进行进行配置，我们需要引入命名空间：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:flow="http://www.springframework.org/schema/webflow-config"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/webflow-config 
        http://www.springframework.org/schema/webflow-config/spring-webflow-config-2.4.xsd">


</beans>
```

#### 8.1.1 装配流程执行器

流程执行器（flow executor）驱动流程的执行。当用户进入一个流程时，流程执行器会为用户创建并启动一个流程执行实例。当流程暂停的时候（如为用户展示视图时），流程执行器会在用户执行操作后恢复流程。

在Spring中，`<flow:flow-executor>`元素会创建一个流程执行器：

```xml
<flow:flow-executor id="flowExecutor" />
```

尽管流程执行器负责创建和执行流程，但它并不负责加载流程定义。这个责任落在了流程注册表（flow registry）身上，接下来我们会创建它。

#### 8.1.2 配置流程注册表

流程注册表（flow registry）的工作是加载流程定义并让流程执行器能够使用它们。我们可以在Spring中使用`<flow:flow-registry>`配置流程注册表，如下所示：

```xml
<!-- 配置流程注册表 -->
<flow:flow-registry id="flowRegistry" base-path="/WEB-INF/flows">
    <flow:flow-location-pattern value="*-flow.xml" />
</flow:flow-registry>
```

在这里的声明中，流程注册表会在“/WEB-INF/flows”目录下查找流程定义，这是通过base-path属性指明的。依据`<flow:flow-location-pattern>`元素的值，任何文件名以“-flow.xml”结尾的XML文件都将视为流程定义。

所有的流程都是通过其ID来引用的。这里我们使用了`<flow:flow-location-pattern>`元素，流程的ID就是相对于base-path的路径，或者双星号所代表的路径。下图展示了流程ID是如何计算的：

<center>
    ![图8.1-流程ID](images\图8.1-流程ID.PNG)
    **在使用流程定位模式的时候，流程定义文件相对于基本路径的路径将被用作流程的ID**
</center>

作为另一种方式，我们可以去除`base-path`属性，而显式声明流程定义文件的位置：

```xml
<flow:flow-registry id="flowRegistry">
    <flow:flow-location parh="/WEB-INF/flows/springpizza.xml" />
</flow:flow-registry>
```

在这里，使用了`<flow:flow-location>`而不是`<flow:flowlocation-pattern>`，path属性直接指明了“/WEB-INF/flows/springpizza.xml”作为流程定义。当我们这样配置的话，流程的ID是从流程定义文件的文件名中获得的，在这里就是springpizza。

如果我们希望更显式地指定流程ID，那么可以通过`<flow:flow-location>`元素的`id`属性来进行设置：

```xml
<flow:flow-registry id="flowRegistry">
    <flow:flow-location id="pizza" parh="/WEB-INF/flows/springpizza.xml" />
</flow:flow-registry>
```

#### 8.1.3 处理流程请求

`DispatcherServlet`一般将请求分发给控制器。但是对于流程而言，我们需要一个`FlowHandlerMapping`来帮助`DispatcherServlet`将流程请求发送给Spring Web Flow。

`FlowHandlerMapping`的配置如下：

```xml
<!-- 配置FlowHandlerMapping -->
<bean class="org.springframework.webflow.mvc.servlet.FlowHandlerMapping">
    <property name="flowRegistry" ref="flowRegistry" />
</bean>
```

`FlowHandlerMapping`装配了流程注册表的引用，这样它就能知道如何将请求的URL匹配到流程上。例如，如果我们有一个ID为pizza的流程，`FlowHandlerMapping`就会知道如果请求的URL模式（相对于应用程序的上下文路径）是“/pizza”的话，就要将其匹配到这个流程上。

然而，`FlowHandlerMapping`的工作仅仅是将流程请求定向到Spring Web Flow上，响应请求的是`FlowHandlerAdapter`。`FlowHandlerAdapter`可以像下面这样装配成一个Springbean，如下所示：

```xml
<!-- 配置FlowHandlerAdapter -->
<bean class="org.springframework.webflow.mvc.servlet.FlowHandlerAdapter">
    <property name="flowExecutor" ref="flowExecutor" />
</bean>
```

这个处理适配器是`DispatcherServlet`和Spring Web Flow之间的桥梁。它会处理流程请求并管理基于这些请求的流程。在这里，它装配了流程执行器的引用，而后者是为所处理的请求执行流程的。

我们已经配置了Spring WebFlow所需的bean和组件。剩下就是真正定义流程了。我们随后将会进行这项工作。但首先，让我们先了解一下组成流程的元素。

### 8.2 流程的组件

在Spring Web Flow中，流程是由三个主要元素定义的：

- 状态
- 转移
- 流程数据

状态（State）是流程中事件发生的地点。如果你将流程想象成公路旅行，那状态就是路途上的城镇、路边饭店以及风景点。流程中的状态是业务逻辑执行、做出决策或将页面展现给用户的地方。

如果流程状态就像公路旅行中停下来的地点，那转移（transition）就是连接这些点的公路。在流程中，你通过转移的方式从一个状态到另一个状态。

在流程处理中，它要收集一些数据：流程的当前状况。

#### 8.2.1 状态

Spring Web Flow定义了五种不同类型的状态，通过选择Spring Web Flow的状态几乎可以把任意的安排功能构造成会话式的Web应用。

状态类型 | 它是用来做什么的
----- | -----
行为（Action） | 行为状态是流程逻辑发生的地方
决策（Decision） | 决策状态将流程分成两个方向，它会基于流程数据的评估结果确定流程方向
结束（End） | 结束状态是流程的最后一站。一旦进入End状态，流程就会终止
子流程（Subflow） | 子流程状态会在当前正在运行的流程上下文中启动一个新的流程
视图（View） | 视图状态会暂停流程并邀请用户参与流程

首先我们了解一下这些流程元素在Spring Web Flow定义中是如何表现得。

**视图状态**

视图状态用于为用户展现信息并使用户在流程中发挥作用。实际的视图实现可以是Spring支持的任意视图类型。

在流程定义的XML文件中，`<view-state>`用于定义视图状态：

```xml
<view-state id="welcome" />
```

在这个简单的示例中，`id`属性有两个含义。它在流程内标示这个状态。除此以外，因为在这里没有在其他地方指定视图，所以它也指定了流程到达这个状态时要展现的逻辑视图名为welcome。

当然，我们可以显式的指定另外一个视图名，可以使用`view`属性：

```xml
<view-state id="welcome" view="greeting" />
```

如果流程为用户展现了一个表单，你可能希望指明表单所绑定的对象。为了做到这一点，可以设置model属性：

```xml
<view-state id="takePayment" model="flowScope.paymentDetails" />
```

这里我们指定takePayment视图中的表单将绑定流程作用域内的paymentDetails对象。

**行为状态**

视图状态会涉及到流程应用程序的用户，而行为状态则是应用程序自身在执行任务。行为状态一般会触发Spring所管理bean的一些方法并根据方法调用的执行结果转移到另一个状态。

行为状态使用`<action-state>`元素来声明：

```xml
<action-state id="saveOrder">
    <evalute expression="pizzaFlowActions.saveOrder(order)" />
    <transition to="thankYou" />
</action-state>
```

尽管不是严格需要的，但是`<action-state>`元素一般都会有一个`<evaluate>`作为子元素。`<evaluate>`元素给出了行为状态要做的事情。`expression`属性指定了进入这个状态时要评估的表达式。在本示例中，给出的`expression`是SpEL表达式，它表明将会找到ID为pizzaFlowActions的bean并调用其`saveOrder()`方法。

Spring Web Flow支持多种表达式语言、OGNL、Unified EL以及SpEL。尽管可以使用上述的任意表达式语言来配置Spring Web Flow，但SpEL是默认和推荐使用的表达式语言。

**决策状态**

有可能流程会完全按照线性执行，从一个状态进入另一个状态，没有其他的替代路线。但是更常见的情况是流程在某一个点根据流程的当前情况进入不同的分支。

决策状态能够在流程执行时产生两个分支。决策状态将评估一个布尔类型的表达式，然后在两个状态转移中选择一个，这要取决于表达式会计算出`true`还是`false`。在XML流程定义中，决策状态通过`<decision-state>`元素进行定义。典型的决策状态示例如下所示：

```xml
<decision-state id="checkDeliveryArea">
    <if test="pizzaFlowActions.checkDeliveryArea(customer.zipCode)"
        then="addCustomer"
        else="deliveryWarning" />
</decision-state>
```

可以看到`<decision-state>`并不是独立完成工作的。`<if>`元素是决策状态的核心。这是表达式进行评估的地方，如果表达式结果为`true`，流程将转移到`then`属性指定的状态中，如果结果为`false`，流程将会转移到`else`属性指定的状态中。

**子流程状态**

我们可能不会将应用程序的所有逻辑写在一个方法中，而是将其分散到多个类、方法以及其他结构中。

同样地，将流程分成独立的部分是个不错的主意。`<subflow-state>`允许在一个正在执行的流程中调用另一个流程。这类似于在一个方法中调用另一个方法。

`<subflow-state>`可以这样声明：

```xml
<subflow-state id="order" subflow="pizza/order">
    <input name="order" value="order"/>
    <transition on="orderCreated" to="payment" />
</subflow-state>
```

在这里，`<input>`元素用于传递订单对象作为子流程的输入。如果子流程结束的`<end-state>`状态ID为orderCreated，那么流程将会转移到名为payment的状态。

**结束状态**

最后，所有的流程都要结束。这就是当流程转移到结束状态时所要做的。`<end-state>`元素指定了流程的结束，它一般会是这样声明的：

```xml
<end-state id="customerReady" />
```

当到达结束状态时，流程会结束。接下来会发生什么取决于几个因素：

- 如果结束的流程是一个子流程，那调用它的流程将会从`<subflow-state>`处继续执行。`<end-state>`的ID将会用作事件触发从`<subflow-state>`开始的转移。
- 如果`<end-state>`设置了`view`属性，指定的视图将会被渲染。视图可以是相对于流程路径的视图模板，如果添加“externalRedirect:”前缀的话，将会重定向到流程外部的页面，如果添加“flowRedirect:”将重定向到另一个流程中。
- 如果结束的流程不是子流程，也没有指定view属性，那这个流程只是会结束而已。浏览器最后将会加载流程的基本URL地址，当前已没有活动的流程，所以会开始一个新的流程实例。

需要意识到流程可能会有不止一个结束状态。子流程的结束状态ID确定了激活的事件，所以你可能会希望通过多种结束状态来结束子流程，从而能够在调用流程中触发不同的事件。即使不是在子流程中，也有可能在结束流程后，根据流程的执行情况有多个显示页面供选择。

#### 8.2.2 转移

正如我在前面所提到的，转移连接了流程中的状态。流程中除结束状态之外的每个状态，至少都需要一个转移，这样就能够知道一旦这个状态完成时流程要去向哪里。状态可以有多个转移，分别对应于当前状态结束时可以执行的不同的路径。

转移使用`<transition>`元素来进行定义，它会作为各种状态元素（`<action-state>`、`<view-state>`、`<subflow-state>`）的子元素。最简单的形式就是`<transition>`元素在流程中指定下一个状态：

```xml
<transition to="customerReady" />
```

属性`to`用于指定流程的下一个状态。如果`<transition>`只使用了`to`属性，那这个转移就会是当前状态的默认转移选项，如果没有其他可用转移的话，就会使用它。

更常见的转移定义是基于事件的触发来进行的。在视图状态，事件通常会是用户采取的动作。在行为状态，事件是评估表达式得到的结果。而在子流程状态，事件取决于子流程结束状态的ID。在任意的事件中，我们可以使用属性`on`来指定触发转移的事件：

```xml
<transition on="phoneEntered" to="lookupCustomer" />
```

在上述代码中，如果触发了phoneEntered事件，流程将会进入lookupCustomer状态。

在抛出异常时，流程也可以进入另一个状态。例如，如果顾客的记录没有找到，你可能希望流程转移到一个展现注册表单的视图状态。以下的代码片段显示了这种类型的转移：

```xml
<transition on-exception="com.springinaction.pizza.service.CustomerNotFoundException" to="registrationForm" />
```

属性`on-exception`类似于`on`属性，只不过它指定了要发生转移的异常而不是一个事件。在本示例中，`CustomerNotFoundException`异常将导致流程转移到registrationForm状态。

**全局转移**

在创建完流程之后，你可能会发现有一些状态使用了一些通用的转移。

例如，如果在整个流程中到处都有如下`<transition>`的话，我一点也不感觉意外：

```xml
<transition on="cancel" to="endState" />
```

与其在多个状态中都重复通用的转移，我们可以将`<transition>`元素作为`<global-transitions>`的子元素，把它们定义为全局转移：

```xml
<global-transitions>
    <transition on="cancel" to="endState" />
</global-transitions>
```

定义完这个全局转移后，流程中的所有状态都会默认拥有这个cancel转移。

#### 8.2.3 流程数据

当流程从一个状态进行到另一个状态时，它会带走一些数据。有时候，这些数据只需要很短的时间（可能只要展现页面给用户）。有时候，这些数据会在整个流程中传递并在流程结束的时候使用。

**声明变量**

流程数据保存在变量中，而变量可以在流程的各个地方进行引用。它能够以多种方式创建。在流程中创建变量的最简单形式是使用`<var>`元素：

```xml
<var name="customer" class="com.springinaction.pizza.domain.Customer" />
```

这里，创建了一个新的`Customer`实例并将其放在名为customer的变量中。这个变量可以在流程的任意状态进行访问。

作为行为状态的一部分或者作为视图状态的入口，你有可能会使用`<evaluate>`元素来创建变量。例如：

```xml
<evaluate resulkt="viewScope.toppingsList" expression="T(com.springinaction.pizza.domain.Topping).asList()" />
```

在本例中，`<evaluate>`元素计算了一个表达式（SpEL表达式）并将结果放到了名为toppingsList的变量中。

类似地，我们也可以使用`<set>`元素设置变量的值：

```xml
<set name="flowScope.pizza" value="new com.springinaction.pizza.domain.Pizza()" />
```

`<set>`元素与`<evaluate>`元素很类似，都是将变量设置为表达式计算的结果。这里，我们设置了一个流程作用域内的pizza变量，它的值是Pizza对象的新实例。

**定义流程数据的作用域**

流程中携带的数据会拥有不同的作用域和可见性，这取决于保存数据的变量本身的作用域。Spring Web Flow定义了5种不同的生命作用域：

范围 | 生命作用域及可见性
-----|-----
Conversation | 最高层级的流程开始时创建，在最高层级的流程结束时销毁。被最高层级的流程和其所有的子流程所共享
Flow | 当流程开始时创建，在流程结束时销毁。只有在创建它的流程中是可见的
Request | 当一个请求进入流程时创建，在流程返回时销毁
Flash | 当流程开始时创建，在流程结束时销毁。在视图状态渲染后，它也会被清除
View | 当进入视图状态时创建，当这个状态退出时销毁。只在视图状态内是可见的

**当使用`<var>`元素声明变量时，变量始终是流程作用域的，也就是在定义变量的流程内有效。当使用`<set>`或`<evaluate>`的时候，作用域通过`name`或`result`属性的前缀指定**。

例如，将一个值赋给流程作用域的theAnswer变量：

```xml
<set name="flowScope.theAnswer" value="42" />
```

### 8.3 组合起来：披萨流程

订购披萨的过程可以很好地定义在一个流程中。我们首先从构建一个高层次的流程开始，它定义了订购披萨的整体过程。接下来，我们会将这个流程拆分成子流程，这些子流程在较低的层次定义了细节。

#### 8.3.1 定义基本流程

一个新的披萨连锁店Spizza决定允许用户在线订购以减轻店面电话的压力。当顾客访问Spizza站点时，他们需要进行用户识别，选择一个或更多披萨添加到订单中，提供支付信息然后提交订单并等待热乎又新鲜的披萨送过来。

<center>
    ![图8.2-将订购披萨的过程归结为一个简单的流程](images\图8.2-将订购披萨的过程归结为一个简单的流程.PNG)
    **将订购披萨的过程归结为一个简单的流程**
</center>

图中的方框代表了状态而箭头代表了转移。订购披萨的整个流程很简单且是线性的。在Spring Web Flow中，表示这个流程是很容易的。

下面我们使用XML流程定义来实现披萨订单的整体流程：

```xml
<var name="order" class="com.springinaction.pizza.domain.Order" />

<!-- 定义顾客子流程 -->
<subflow-state id="identifyCustomer" subflow="pizza/customer">
    <output name="customer" value="order.customer"/>
    <transition on="customerReady" to="buildOrder" />
</subflow-state>

<!-- 调用订单子流程 -->
<subflow-state id="buildOrder" subflow="pizza/order">
    <input name="order" value="order" />
    <transition on="orderCreated" to="takePayment" />
</subflow-state>

<!-- 调用支付子流程 -->
<subflow-state id="takePayment" subflow="pizza/payment">
    <input name="order" value="order" />
    <transition on="paymentTaken" to="saveOrder" />
</subflow-state>

<!-- 保存订单 -->
<action-state id="saveOrder">
    <evaluate expression="pizzaFlowActions.saveOrder(order)" />
    <transition to="thankCustomer" />
</action-state>

<!-- 感谢顾客 -->
<view-state id="thankCustomer">
    <transition to="endState" />
</view-state>

<!-- 全局取消转移 -->
<global-transitions>
    <transition on="cancel" to="endState" />
</global-transitions>
```

在流程定义中，我们看到的第一件事就是order变量的声明。每次流程开始的时候，都会创建一个`Order`实例。`Order`类会带有关于订单的所有信息，包含顾客信息、订购的披萨列表以及支付详情，如下面所示：

```java
package com.springinaction.pizza.domain;

import java.io.Serializable;
import java.util.ArrayList;
import java.util.List;

public class Order implements Serializable {

    private static final long serialVersionUID = 5500089874240597273L;

    private Customer customer;
    
    private List<Pizza> pizzas;
    
    private Payment payment;
    
    public Order() {
        pizzas = new ArrayList<>();
        customer = new Customer();
    }

    public Customer getCustomer() {
        return customer;
    }

    public void setCustomer(Customer customer) {
        this.customer = customer;
    }

    public List<Pizza> getPizzas() {
        return pizzas;
    }

    public void setPizzas(List<Pizza> pizzas) {
        this.pizzas = pizzas;
    }
    
    public void addPizza(Pizza pizza) {
        pizzas.add(pizza);
    }

    public Payment getPayment() {
        return payment;
    }

    public void setPayment(Payment payment) {
        this.payment = payment;
    }
    
    public float getTotal() {
        return 0.0f;
    }
}
```

流程定义的主要组成部分是流程的状态。默认情况下，流程定义文件中的第一个状态也会是流程访问中的第一个状态。在本例中，也就是`identifyCustomer`状态（一个子流程）。但是如果你愿意的话，你可以通过`<flow>`元素的`start-state`属性将任意状态指定为开始状态：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<flow xmlns="http://www.springframework.org/schema/webflow"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/webflow
        http://www.springframework.org/schema/webflow/spring-webflow.xsd"
        start-state="identifyCustomer">

    <var name="order" class="com.springinaction.pizza.domain.Order" />

    <!-- 定义顾客子流程 -->
    <subflow-state id="identifyCustomer" subflow="pizza/customer">
        <output name="customer" value="order.customer"/>
        <transition on="customerReady" to="buildOrder" />
    </subflow-state>
    
    <!-- 调用订单子流程 -->
    <subflow-state id="buildOrder" subflow="pizza/order">
        <input name="order" value="order" />
        <transition on="orderCreated" to="takePayment" />
    </subflow-state>
    
    <!-- 调用支付子流程 -->
    <subflow-state id="takePayment" subflow="pizza/payment">
        <input name="order" value="order" />
        <transition on="paymentTaken" to="saveOrder" />
    </subflow-state>
    
    <!-- 保存订单 -->
    <action-state id="saveOrder">
        <evaluate expression="pizzaFlowActions.saveOrder(order)" />
        <transition to="thankCustomer" />
    </action-state>
    
    <!-- 感谢顾客 -->
    <view-state id="thankCustomer">
        <transition to="endState" />
    </view-state>
    
    <!-- 全局取消转移 -->
    <global-transitions>
        <transition on="cancel" to="endState" />
    </global-transitions>
</flow>
```

识别顾客、构造披萨订单以及支付这样的活动太复杂了，并不适合将其强行塞入一个状态。这是我们为何在后面将其单独定义为流程的原因。但是为了更好地整体了解披萨流程，这些活动都是以`<subflow-state>`元素来进行展现的。

流程变量`order`将在前三个状态中进行填充并在第四个状态中进行保存。`identifyCustomer`子流程状态使用了`<output>`元素来填充order的`customer`属性。`buildOrder`和`takePayment`状态使用了不同的方式，它们使用`<input>`将`order`流程变量作为输入，这些子流程就能在其内部填充order对象。

在订单得到顾客、一些披萨以及支付细节后，就可以对其进行保存了。saveOrder是处理这个任务的行为状态。它使用`<evaluate>`来调用ID为pizzaFlowActions的bean的`saveOrder()`方法，并将保存的订单对象传递进来。订单完成保存后，它会转移到thankCustomer。

`thankCustomer`状态是一个简单的视图状态，后台使用了“/WEB-INF/flows/pizza/ thankCustomer.jsp”这个JSP文件，如下所示：

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Spizza</title>
</head>
<body>
    <h2>Thank you for your order!</h2>
    <a href="${flowExecutionUrl}&_eventId=finished">Finish</a>
</body>
</html>
```

在“感谢”页面中，会感谢顾客的订购并为其提供一个完成流程的链接。

Spring Web Flow为视图的用户提供了一个`flowExecutionUrl`变量，它包含了流程的URL。结束链接将一个“_eventId”参数关联到URL上，以便回到Web流程时触发finished事件。这个事件将会让流程到达结束状态。

流程将会在结束状态完成。鉴于在流程结束后没有下一步做什么的具体信息，流程将会重新从`identifyCustomer`状态开始，以准备接受另一个披萨订单。

#### 8.3.2 收集顾客信息

如果你曾经订购过披萨，你可能会知道流程。他们首先会询问你的电话号码。电话号码除了能够让送货司机在找不到你家的时候打电话给你，还可以作为你在这个披萨店的标识。如果你是回头客，他们可以使用这个电话号码来查找你的地址，这样他们就知道将你的订单派送到什么地方了。

对于一个新的顾客来讲，查询电话号码不会有什么结果。所以接下来，他们将询问你的地址。这样，披萨店的人就会知道你是谁以及将披萨送到哪里。但是在问你要哪种披萨之前，他们要确认你的地址在他们的配送范围之内。如果不在的话，你需要自己到店里并取走披萨。

在每个披萨订单开始前的提问和回答阶段可以用下图来表示：

<center>
    ![图8.3-识别顾客流程](images\图8.3-识别顾客流程.PNG)
    **识别顾客的流程比披萨流程有了更多的分支**
</center>

这个流程不是线性的而是在好几个地方根据不同的条件有了分支。例如，在查找顾客后，流程可能结束（如果找到了顾客），也有可能转移到注册表单（如果没有找到顾客）。同样，在`checkDeliveryArea`状态，顾客有可能会被警告也有可能不被警告他们的地址在配送范围之外。

下面定义识别顾客的流程：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<flow xmlns="http://www.springframework.org/schema/webflow"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/webflow
        http://www.springframework.org/schema/webflow/spring-webflow.xsd">

    <var name="customer" class="com.springinaction.pizza.domain.Customer" />

    <!-- 欢迎顾客 -->
    <view-state id="welcome">
        <transition on="phoneEntered" to="lookupCustomer" />
    </view-state>

    <!-- 查找顾客 -->
    <action-state id="lookupCustomer">
        <evaluate result="customer"
            expression="pizzaFlowActions.lookupCustomer(requestParameters.phoneNumber)" />
        <transition to="registrationForm"
            on-exception="com.springinaction.pizza.service.CustomerNotFoundException" />
        <transition to="customerReady" />
    </action-state>

    <!-- 注册新顾客 -->
    <view-state id="registrationForm" model="customer">
        <on-entry>
            <evaluate expression="customer.phoneNumber = requestParameters.phoneNumber" />
        </on-entry>
        <transition on="submit" to="checkDeliveryArea" />
    </view-state>
    
    <!-- 检查配送区域 -->
    <decision-state id="checkDeliveryArea">
        <if test="pizzaFlowActions.checkDeliveryArea(customer.zipCode)" then="addCustomer" else="deliveryWarning" />
    </decision-state>
    
    <!-- 显示配送警告 -->
    <view-state id="deliveryWarning">
        <transition on="accept" to="addCustomer" />
    </view-state>
    
    <!-- 添加顾客 -->
    <action-state id="addCustomer">
        <evaluate expression="pizzaFlowActions.addCustomer(customer)" />
        <transition to="customerReady" />
    </action-state>

    <!-- 流程结束 -->
    <end-state id="cancel"/>
    <end-state id="customerReady">
        <output name="customer" />
    </end-state>
    
    <global-transitions>
        <transition on="cancel" to="cancel" />
    </global-transitions>
</flow>
```

这个流程包含了几个新的技巧，包括我们首次使用的`<decision-state>`元素。因为它是`pizza`流程的子流程，所以它也可以接受`Order`对象作为输入。

与前面一样，我们还是将这个流程的定义分解成一个个的状态，让我们从`welcome`状态开始。

**询问电话号码**

`welcome`状态是一个很简单的视图状态，它欢迎访问Spizza站点的顾客并要求他们输入电话号码。这个状态并没有什么特殊的。它有两个转移：如果从视图触发`phoneEntered`事件的话，转移会将流程定向到`lookupCustomer`，另外一个就是在全局转移中定义的用来响应`cancel`事件的`cancel`转移。

welcome.jsp视图：

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Welcome to Spizza</title>
</head>
<body>
    <h2>Welcome to Spizza!</h2>
    <form:form>
        <!-- 激活流程执行的key -->
        <input type="hidden" name="_flowExecutionKey" value="${flowExecutionKey}" />
        <input type="text" name="phoneNumber" /> 
        <!-- 触发phoneEntered事件 -->
        <input type="submit" name="_eventId_phoneEntered" value="Lookup Customer" />
    </form:form>
</body>
</html>
```

这个简单的表单提示用户输入其电话号码。表单中有两个特殊的部分来驱动流程继续。

首先要注意的是隐藏的“\_flowExecutionKey”输入域。当进入视图状态时，流程暂停并等待用户采取一些行为。赋予视图的流程执行key（flow execution key）就是一种返回流程的“回程票”（claimticket）。当用户提交表单时，流程执行key会在“\_flowExecutionKey”输入域中返回并在流程暂停的位置进行恢复。

还要注意的是提交按钮的名字。按钮名字的“\_eventId\_”部分是提供给Spring Web Flow的一个线索，它表明了接下来要触发事件。当点击这个按钮提交表单时，会触发`phoneEntered`事件进而转移到`lookupCustomer`。

**查找顾客**

当欢迎表单提交后，顾客的电话号码将包含在请求参数中并准备用于查询顾客。`lookupCustomer`状态的`<evaluate>`元素是查找发生的地方。它将电话号码从请求参数中抽取出来并传递到pizzaFlowActions bean的`lookupCustomer()`方法中。

目前，`lookupCustomer()`的实现并不重要。只需知道它要么返回`Customer`对象，要么抛出`CustomerNotFoundException`异常。

在前一种情况下，`Customer`对象将会设置到customer变量中（通过`result`属性）并且默认的转移将把流程带到`customerReady`状态。但是如果不能找到顾客的话，将抛出`CustomerNotFoundException`并且流程被转移到`registrationForm`状态。

**注册新顾客**

`registrationForm`状态是要求用户填写配送地址的。就像我们之前看到的其他视图状态，它将被渲染成JSP。

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Customer Registration</title>
</head>
<body>
    <h2>Customer Registration</h2>
    <form:form commanName="customer">
        <input type="hidden" name="_flowExecutionKey" value="${flowExecutionKey}" />
        <b>Phone number: </b>
        <form:input path="name" />
        <br />
        <b>Name: </b>
        <form:input path="name" />
        <br />
        <b>Address: </b>
        <form:input path="address" />
        <br />
        <b>City: </b>
        <form:input path="city" />
        <br />
        <b>State: </b>
        <form:input path="state" />
        <br />
        <b>Zip Code: </b>
        <form:input path="zipCode" />
        <br />
        <input type="submit" name="_eventId_submit" value="Submit" />
        <input type="submit" name="_eventId_cancel" value="Cancel" />
    </form:form>
</body>
</html>
```

这并非我们在流程中看到的第一个表单。welcome视图状态也为顾客展现了一个表单，那个表单很简单，并且只有一个输入域，从请求参数中获得输入域的值也很简单。但是注册表单就比较复杂了。

在这里不是通过请求参数一个个地处理输入域，而是以更好的方式将表单绑定到`Customer`对象上。

**检查配送区域**

在顾客提供其地址后，我们需要确认他的住址在配送范围之内。如果Spizza不能派送给他们，那么我们要让顾客知道并建议他们自己到店面里取走披萨。

为了做出这个判断，我们使用了决策状态。决策状态`checkDeliveryArea`有一个`<if>`元素，它将顾客的邮政编码传递到pizzaFlowActions bean的`checkDeliveryArea()`方法中。这个方法将会返回一个布尔值：如果顾客在配送区域内则为`true`，否则为`false`。

如果顾客在配送区域内的话，那流程转移到`addCustomer`状态。否则，顾客被带入到`deliveryWarning`视图状态。`deliveryWarning`背后的视图就是“/WEBINF/flows/pizza/customer/deliveryWarning.jsp”：

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Spizza</title>
</head>
<body>
    <h2>Delivery Unavailable</h2>
    <p>The address is outside of our delivery area. You may still place the order, but you will need to pick it up yourself.</p>
    <a href="${flowExectuionUrl}&_eventId=accept">Contiune, I'll pick up the order</a> |
    <a href="${flowExecutionUrl}&_eventId=cancel">Never mind</a>
</body>
</html>
```

在页面中与流程相关的两个关键点就是那两个连接，它们允许用户继续订单或者将其取消。通过使用与welcome状态相同的`flowExecurtionUrl`变量，这些链接分别触发流程中的`accept`或`cancel`事件。如果发送的是`accept`事件，那么流程会转移到`addCustomer`状态。否则，接下来会是全局的取消转移，子流程将会转移到`cancel`结束状态。

**存储顾客数据**

当流程抵达`addCustomer`状态时，用户已经输入了他们的地址。为了将来使用，这个地址需要以某种方式存储起来（可能会存储在数据库中）。`addCustomer`状态有一个`<evaluate>`元素，它会调用pizzaFlowActions bean的`addCustomer()`方法，并将`customer`流程参数传递进去。

一旦这个过程完成，会执行默认的转移，流程将会转移到ID为`customerReady`的结束状态。

**结束流程**

一般来讲，流程的结束状态并不会那么有意思。但是这个流程中，它不仅仅只有一个结束状态，而是两个。当子流程完成时，它会触发一个与结束状态ID相同的流程事件。如果流程只有一个结束状态的话，那么它始终会触发相同的事件。但是如果有两个或更多的结束状态，流程能够影响到调用状态的执行方向。

当customer流程走完所有正常的路径后，它最终会到达ID为`customerReady`的结束状态。当调用它的披萨流程恢复时，它会接收到一个`customerReady`事件，这个事件将使得流程转移到`buildOrder`状态。

要注意的是`customerReady`结束状态包含了一个`<output>`元素。在流程中这个元素等同于Java中的`return`语句。它从子流程中传递一些数据到调用流程。在本示例中，`<output>`元素返回customer流程变量，这样在披萨流程中，就能够将`identifyCustomer`子流程的状态指定给订单。另一方面，如果在识别顾客流程的任意地方触发了`cancel`事件，将会通过ID为`cancel`的结束状态退出流程，这也会在披萨流程中触发`cancel`事件并导致转移（通过全局转移）到披萨流程的结束状态。

#### 8.3.3 构建订单

在识别完顾客之后，主流程的下一件事情就是确定他们想要什么类型的披萨。

订单子流程就是用于提示用户创建披萨并将其放入订单中的，如图所示：

<center>
    ![图8.4-通过订单子流程添加披萨](images\图8.4-通过订单子流程添加披萨.PNG)
    **通过订单子流程添加披萨**
</center>

`showOrder`状态位于订单子流程的中心位置。这是用户进入这个流程时看到的第一个状态，它也是用户在添加披萨到订单后要转移到的状态。它展现了订单的当前状态并允许用户添加其他的披萨到订单中。

要添加披萨到订单时，流程会转移到`createPizza`状态。这是另外一个视图状态，允许用户选择披萨的尺寸和面饼上面的配料。在这里，用户可以添加或取消披萨，两种事件都会使流程转移回`showOrder`状态。

从`showOrder`状态，用户可能提交订单也可能取消订单。两种选择都会结束订单子流程，但是主流程会根据选择不同进入不同的执行路径。

下面定义创建订单子流程：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<flow xmlns="http://www.springframework.org/schema/webflow"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/webflow
        http://www.springframework.org/schema/webflow/spring-webflow.xsd">

    <!-- 接收order作为输入 -->
    <input name="order" required="true" />

    <!-- 展现order的状态 -->
    <view-state id="showOrder">
        <transition on="createPizza" to="createPizza" />
        <transition on="checkout" to="orderCreated" />
        <transition on="cancel" to="cancel" />
    </view-state>

    <!-- 创建披萨的状态 -->
    <view-state id="createPizza" model="flowScope.pizza">
        <on-entry>
            <set name="flowScope.pizza" value="new com.springinaction.pizza.domain.Pizza()" />
            <evaluate result="viewScope.toppingsList"
                expression="T(com.springinaction.pizza.domain.Topping).asList()" />
        </on-entry>
        <transition on="addPizza" to="showOrder">
            <evaluate expression="order.addPizza(flowScope.pizza)" />
        </transition>
        <transition on="cancel" to="showOrder" />
    </view-state>


    <!-- 取消的结束状态 -->
    <end-state id="cancel" />
    <!-- 创建订单的结束状态 -->
    <end-state id="orderCreated" />
</flow>
```

这个子流程实际上会操作主流程创建的`Order`对象。因此，我们需要以某种方式将`Order`从主流程传到子流程。我们使用了`<input>`元素来将`Order`传递进流程。在这里，我们使用它来接收`Order`对象。如果你觉得这个流程与Java中的方法有些类似地话，那这里使用的`<input>`元素实际上就定义了这个子流程的签名。这个流程需要一个名为order的参数。

接下来，我们会看到`showOrder`状态，它是一个基本的视图状态并具有三个不同的转移，分别用于创建披萨、提交订单以及取消订单。`showOrder`视图如下所示：

```jsp
<%@ page language="java" contentType="text/html; charset=utf-8" pageEncoding="utf-8"%>
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt"%>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Spizza</title>
</head>
<body>
    <h2>Your order</h2>

    <h3>Deliver to:</h3>
    <b>${order.customer.name}</b>
    <br />
    <b>${order.customer.address}</b>
    <br />
    <b>${order.customer.city}, ${order.customer.state} ${order.customer.zipCode}</b>
    <br />
    <b>${order.customer.phoneNumber}</b>
    <br />
    <hr />
    <h3>
        Order total:
        <fmt:formatNumber type="currency" value="${order.total}" />
    </h3>
    <hr />
    <h3>Pizzas:</h3>

    <c:if test="${fn:length(order.pizzas) eq 0}">
        <b>No pizzas in this order.</b>
    </c:if>

    <br />
    <c:forEach items="${order.pizzas}" var="pizza">
        <li>${pizza.size}: 
          <c:forEach items="${pizza.toppings}" var="topping">
                <c:out value="${topping}" />,
          </c:forEach>
        </li>
    </c:forEach>

    <form:form>
        <input type="hidden" name="_flowExecutionKey" value="${flowExecutionKey}" />
        <input type="submit" name="_eventId_createPizza" value="Create Pizza" />
        <c:if test="${fn:length(order.pizzas) gt 0}">
            <input type="submit" name="_eventId_checkout" value="Checkout" />
        </c:if>
        <input type="submit" name="_eventId_cancel" value="Cancel" />
    </form:form>
</body>
</html>
```

`createPizza`状态的视图是一个表单，这个表单可以添加新的`Pizza`对象到订单中。`<on-entry>`元素添加了一个新的`Pizza`对象到流程作用域内，当表单提交时，表单的内容会填充到该对象中。需要注意的是，这个视图状态引用的`model`是流程作用域内的同一个`Pizza`对象。`Pizza`对象将绑定到创建披萨的表单中，如下所示：

```jsp
<div xmlns:form="http://www.springframework.org/tags/form">
    <h2>Create Pizza</h2>
    <form:form commandName="pizza">
        <input type="hidden" name="_flowExecutionKey" value="${flowExecutionKey}" />
        <b>Size: </b>
        <br />
        <form:radiobutton path="size" label="Small (12-inch)" value="SMALL" />
        <br />
        <form:radiobutton path="size" label="Medium (14-inch)" value="MEDIUM" />
        <br />
        <form:radiobutton path="size" label="Large (16-inch)" value="LARGE" />
        <br />
        <form:radiobutton path="size" label="Ginormous (20-inch)" value="GINORMOUS" />
        <br />
        <br />
        <b>Toppings: </b>
        <br />
        <form:checkboxes path="toppings" items="${toppingsList}" delimiter="<br/>" />
        <br />
        <br />
        <input type="submit" class="button" name="_eventId_addPizza" value="Continue" />
        <input type="submit" class="button" name="_eventId_cancel" value="Cancel" />
    </form:form>
</div>
```

当通过Continue按钮提交订单时，尺寸和配料选择将会绑定到`Pizza`对象中并且触发`addPizza`转移。与这个转移关联的`<evaluate>`元素表明在转移到`showOrder`状态之前，流程作用域内的Pizza对象将会传递给订单的`addPizza()`方法中。

有两种方法来结束这个流程。用户可以点击`showOrder`视图中的Cancel按钮或者Checkout按钮。这两种操作都会使流程转移到一个`<end-state>`。但是选择的结束状态id决定了退出这个流程时触发事件，进而最终确定了主流程的下一步行为。主流程要么基于`cancel`事件要么基于`orderCreated`事件进行状态转移。在前者情况下，外边的主流程会结束；在后者情况下，它将转移到`takePayment`子流程，这也是接下来我们要看的。

#### 8.3.4 支付

在披萨流程要结束的时候，最后的子流程提示用户输入他们的支付信息。这个简单的流程如下图所示：

<center>
    ![图8.5-通过支付子流程让用户进行支付](images\图8.5-通过支付子流程让用户进行支付.PNG)
    **订购披萨的最后一步是通过支付子流程让用户进行支付**
</center>

像订单子流程一样，支付子流程也使用`<input>`元素接收一个`Order`对象作为输入。

可以看到，进入支付子流程的时候，用户会到达`takePayment`状态。这是一个视图状态，在这里用户可以选择使用信用卡、支票或现金进行支付。提交支付信息后，将进入`verifyPayment`状态。这是一个行为状态，它将校验支付信息是否可以接受。

定义支付流程：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<flow xmlns="http://www.springframework.org/schema/webflow"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/webflow
        http://www.springframework.org/schema/webflow/spring-webflow.xsd">

    <!-- 接收order对象 -->
    <input name="order" required="true" />

    <view-state id="takePayment" model="flowScope.paymentDetails">
        <on-entry>
            <set name="flowScope.paymentDetails"
                value="new com.springinaction.pizza.domain.PaymentDetails()" />
            <evaluate result="viewScope.paymentTypeList"
                expression="T(com.springinaction.pizza.domain.PaymentType).asList()" />
        </on-entry>
        <transition on="paymentSubmitted" to="verifyPayment" />
        <transition on="cancel" to="cancel" />
    </view-state>

    <action-state id="verifyPayment">
        <evaluate result="order.payment"
            expression="pizzaFlowActions.verifyPayment(flowScope.paymentDetails)" />
        <transition to="paymentTaken" />
    </action-state>

    <end-state id="cancel" />
    <end-state id="paymentTaken" />
</flow>
```

在流程进入`takePayment`视图时，`<on-entry>`元素将构建一个支付表单并使用SpEL表达式在流程作用域内创建一个`PaymentDetails`实例，这是支撑表单的对象。它也会创建视图作用域的paymentTypeList变量，这个变量是一个列表包含了`PaymentType`枚举的值。在这里，SpEL的`T()`操作用于获得`PaymentType`类，这样就可以调用静态的`asList()`方法。

```java
package com.springinaction.pizza.domain;

import java.util.Arrays;
import java.util.List;

import org.apache.commons.text.WordUtils;

public enum PaymentType {
    
    CASH, CHECK, CREDIT_CARD;

    public static List<PaymentType> asList() {
        PaymentType[] all = PaymentType.values();
        return Arrays.asList(all);
    }

    @Override
    public String toString() {
        return WordUtils.capitalizeFully(name().replace('_', ' '));
    }
}
```

在面对支付表单的时候，用户可能提交支付也可能会取消。根据做出的选择，支付子流程将以名为`paymentTaken`或`cancel`的`<end-state>`结束。就像其他的子流程一样，不论哪种`<end-state>`都会结束子流程并将控制交给主流程。但是所采用`<end-state>`的id将决定主流程中接下来的转移。

现在，我们已经依次介绍了披萨流程及其子流程，并看到了Spring Web Flow的很多功能。在我们结束Spring Web Flow话题之前，让我们快速了解一下如何对流程及其状态的访问增加安全保护。

#### 8.4 保护Web流程

在下一章中，我们将会看到如何使用Spring Security来保护Spring应用程序。但现在我们讨论的是Spring Web Flow，让我们快速地看一下Spring Web Flow是如何结合Spring Security支持流程级别的安全性的。Spring Web Flow中的状态、转移甚至整个流程都可以借助`<secured>`元素实现安全性，该元素会作为这些元素的子元素。例如，为了保护对一个视图状态的访问，你可以这样使用`<secured>`：

```xml
<view-state id="restricted">
    <secured attributes="ROLE_ADMIN" match="all" />
</view-state>
```

按照这里的配置，只有授予`ROLE_ADMIN`访问权限（借助`attributes`属性）的用户才能访问这个视图状态。`attributes`属性使用逗号分隔的权限列表来表明用户要访问指定状态、转移或流程所需要的权限。`match`属性可以设置为`any`或`all`。如果设置为`any`，那么用户必须至少具有一个`attributes`属性所列的权限。如果设置为`all`，那么用户必须具有所有的权限。你可能想知道用户如何具备`<secured>`元素所检验的权限，甚至最开始的时候用户是如何进行登录的？这些问题的答案将在第9章给出。

### 8.5 小结

>
并不是所有的Web应用程序都是自由访问的。有时候，必须对用户进行指引、询问适当的问题并基于他们的响应将其引导到特定页面。在这些情况下，应用程序不太像一个菜单选项而更像应用程序与用户之间的对话。
>
在本章中，我们介绍了Spring Web Flow，它是能够构建会话式应用程序的Web框架。在介绍的同时，我们构建了一个基于流程的披萨订单应用。我们先定义了应用程序的整体流程，从收集顾客信息开始到保存订单到系统中结束。
>
流程由多个状态和转移组成，它们定义了会话如何从一个状态到另一个状态。状态本身分为好多种：行为状态执行业务逻辑，视图状态涉及到流程中的用户，决策状态动态地引导流程执行，结束状态表明流程的结束，除此之外，还有子流程状态，它们自身是通过流程来定义的。
>
最后，我们看到如何限制具有特定权限的用户才能访问流程、状态或转移。但是，我们还没有介绍应用程序对用户的认证以及如何授予用户权限。这就是Spring Security能够发挥作用的地方了，而Spring Security就是我们第9章将要介绍的内容。

## 第九章 保护Web应用

本章内容：

- Spring Security介绍
- 使用Servlet规范中的Filter保护Web应用
- 基于数据库和LDAP进行认证

现在，信息可能是我们最有价值的东西，一些不怀好意的人想尽办法试图偷偷进入不安全的应用程序来窃取我们的数据和身份信息。作为软件开发人员，我们必须采取措施来保护应用程序中的信息。无论你是通过用户名/密码来保护电子邮件账号，还是基于交易PIN来保护经纪账户，安全性都是绝大多数应用系统中的一个重要切面（aspect）。

这里有意选择了“切面”这个词来描述应用系统的安全性。安全性是超越应用程序功能的一个关注点。应用系统的绝大部分内容都不应该参与到与自己相关的安全性处理中。尽管我们可以直接在应用程序中编写安全性功能相关的代码（这种情况并不少见），但更好的方式还是将安全性相关的关注点与应用程序本身的关注点进行分离。

如果你觉得安全性听上去好像是使用面向切面技术实现的，那你猜对了。在本章中，我们将使用切面技术来探索保护应用程序的方式。不过我们不必自己开发这些切面，我们将介绍Spring Security，这是一种基于Spring AOP和Servlet规范中的Filter实现的安全框架。

### 9.1 Spring Security简介

Spring Security是为基于Spring的应用程序提供声明式安全保护的安全性框架。Spring Security提供了完整的安全性解决方案，它能够在Web请求级别和方法调用级别处理身份认证和授权。因为基于Spring框架，所以Spring Security充分利用了依赖注入（dependency injection，DI）和面向切面的技术。

最初，Spring Security被称为Acegi Security。Acegi是一个强大的安全框架，但是它存在一个严重的问题：那就是需要大量的XML配置。我不会向你介绍这种复杂配置的细节。总之一句话，典型的Acegi配置有几百行XML是很常见的。

到了2.0版本，Acegi Security更名为Spring Security。但是2.0发布版本所带来的不仅仅是表面上名字的变化。为了在Spring中配置安全性，Spring Security引入了一个全新的、与安全性相关的XML命名空间。这个新的命名空间连同注解和一些合理的默认设置，将典型的安全性配置从几百行XML减少到十几行。Spring Security 3.0融入了SpEL，这进一步简化了安全性的配置。

Spring Security从两个角度来解决安全性问题。它使用Servlet规范中的Filter保护Web请求并限制URL级别的访问。Spring Security还能够使用Spring AOP保护方法调用——借助于对象代理和使用通知，能够确保只有具备适当权限的用户才能访问安全保护的方法。

在本章中，我们将会关注如何将Spring Security用于Web层的安全性之中。在稍后的第14章中，我们会重新学习Spring Security，了解它如何保护方法的调用。

（原书介绍并使用的是3.2版本，这里使用版本4.2.7。[[Spring Security 4.2.7 Reference](https://docs.spring.io/spring-security/site/docs/4.2.7.RELEASE/reference/htmlsingle/){:target="_blank"}|[Migrating from 3.x to 4.x](https://docs.spring.io/spring-security/site/docs/4.2.7.RELEASE/reference/htmlsingle/#m3to4){:target="_blank"}]）

#### 9.1.1 理解Spring Security的模块

不管你想使用Spring Security保护哪种类型的应用程序，第一件需要做的事就是将Spring Security模块添加到应用程序的类路径下。

Spring Security的11个模块：

模块 | 描述
-----|-----
ACL | 支持通过访问控制列表（access control list，ACL）为域对象提供安全性
切面（Aspects） | 一个很小的模块，当使用Spring Security注解时，会使用基于AspectJ的切面，而不是使用标准的Spring AOP
CAS客户端（CAS Client）| 提供与Jasig的中心认证服务（Central Authentication Service，CAS）进行集成的功能
配置（Configuration） | 包含通过XML和Java配置Spring Security的功能支持
核心（Core） | 提供Spring Security基本库
加密（Cryptography） | 提供了加密和密码编码的功能
LDAP | 支持基于LDAP进行认证
OpenID | 支持使用OpenID进行集中式认证
Remoting | 提供了对Spring Remoting的支持
标签库（Tag Library）| Spring Security的JSP标签库
Web | 提供了Spring Security基于Filter的Web安全性支持

#### 9.1.2 过滤Web请求

Spring Security借助一系列Servlet Filter来提供各种安全性功能。你可能会想，这是否意味着我们需要在web.xml或`WebApplicationInitializer`中配置多个Filter呢？实际上，借助于Spring的小技巧，我们只需配置一个Filter就可以了。

`DelegatingFilterProxy`是一个特殊的Servlet Filter，它本身所做的工作并不多。只是将工作委托给一个`javax.servlet.Filter`实现类，这个实现类作为一个`<bean>`注册在Spring应用的上下文中：

<center>
    ![图9.1-DelegatingFilterProxy把Filter的处理逻辑委托给Spring应用上下文中所定义的一个代理Filter](images\图9.1-DelegatingFilterProxy把Filter的处理逻辑委托给Spring应用上下文中所定义的一个代理Filter.PNG)
    **DelegatingFilterProxy把Filter的处理逻辑委托给Spring应用上下文中所定义的一个代理Filter bean**
</center>

如果我们要在web.xml中配置这个Filter：

```xml
<filter>
    <filter-name>springSecurityFilterChain</filter-name>
    <filter-class>
        org.springframework.web.filter.DelegatingFilterProxy
    </filter-class>
</filter>
```

这里我们将`<filter-name>`设置为springSecurityFilterChain。这是因为我们马上就会将Spring Security配置在Web安全性之中，这里会有一个名为springSecurityFilterChain的Filter bean，`DelegatingFilterProxy`会将过滤逻辑委托给它。

我国我们要以JavaConfig的方式来配置`DelegatingFilterProxy`，那么我们所要做的说就是创建一个类，这个类要继承`AbstractSecurityWebApplicationInitializer`：

```java
package spittr.config;

import org.springframework.security.web.context.AbstractSecurityWebApplicationInitializer;

public class SecurityWebInitializer extends AbstractSecurityWebApplicationInitializer {
    
}

```

`AbstractSecurityWebApplicationInitializer`类实现了`WebApplicationInitializer`接口，因此Spring会发现它，并用它在Web容器中注册`DelegatingFilterProxy`。尽管我们可以重载它的`appendFilters()`或`insertFilters()`方法来注册自己选择的Filter，但是要注册`DelegatingFilterProxy`的话，我们并不需要重载任何方法。

不管我们通过web.xml还是通过`AbstractSecurityWebApplicationInitializer`的子类来配置`DelegatingFilterProxy`，它都会拦截发往应用中的请求，并将请求委托给ID为springSecurityFilterChain的bean。

springSecurityFilterChain本身是另一个特殊的`Filter`，它也被称为FilterChainProxy。它可以链接任意一个或多个其他的`Filter`。Spring Security依赖一系列Servlet Filter来提供不同的安全特性。但是，你几乎不需要知道这些细节，因为你不需要显式声明springSecurityFilterChain以及它所链接在一起的其他`Filter`。当我们启用Web安全性的时候，会自动创建这些`Filter`。

#### 9.1.3 编写简单的安全性配置

在Spring Security的早期版本中（在其还被称为Acegi Security之时），为了在Web应用中启用简单的安全功能，我们需要编写上百行的XML配置。Spring Security 2.0提供了安全性相关的XML配置命名空间，让情况有了一些好转。

Spring 3.2引入了新的Java配置方案，完全不再需要通过XML来配置安全性功能了。如下的程序清单展现了Spring Security最简单的Java配置。

```java
package spittr.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity // 启用Web安全性
public class SecurityConfig extends WebSecurityConfigurerAdapter {

}
```

`@EnableWebSecurity`注解将会启用Web安全功能。但它本身并没有什么用处，Spring Security必须配置在一个实现了WebSecurityConfigurer的bean中，或者（简单起见）扩展WebSecurityConfigurerAdapter。在Spring应用上下文中，任何实现了WebSecurityConfigurer的bean都可以用来配置Spring Security。

`@EnableWebSecurity`可以启用任意Web应用的安全性功能，不过由于我们是使用Spring MVC的，所以可以使用注解`@EnableWebMvcSecurity`替代它：

```java
package spittr.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.annotation.web.servlet.configuration.EnableWebMvcSecurity;

@Configuration
@EnableWebMvcSecurity // 启用Web安全性
public class SecurityConfig extends WebSecurityConfigurerAdapter {

}
```

（但是，注解`@EnableWebMvcSecurity`已经被标注为`@deprecated`了，Spring推荐我们使用`@EnableWebSecurity`。根据`@EnableWebMvcSecurity`的注释：
```text
/**
 * Add this annotation to an {@code @Configuration} class to have the Spring Security
 * configuration integrate with Spring MVC.
 *
 * @deprecated Use EnableWebSecurity instead which will automatically add the * Spring MVC related Security items.
 * @author Rob Winch
 * @since 3.2
 */
```
注解`@EnableWebSecurity`会自动添加与Spring MVC相关的安全事项。）

（以下这段内容针对注解`@EnableWebMvcSecurity`）

`@EnableWebMvcSecurity`注解还配置了一个Spring MVC参数解析解析器（argument resolver），这样的话处理器方法就能够通过带有`@AuthenticationPrincipal`注解的参数获得认证用户的principal（或username）。它同时还配置了一个bean，在使用Spring表单绑定标签库来定义表单时，这个bean会自动添加一个隐藏的跨站请求伪造（cross-site request forgery，CSRF）token输入域。

我们可能希望指定Web安全的细节，这要通过重写`WebSecurityConfigurerAdapter`中的一个或多个方法来实现。我们可以通过重写`WebSecurityConfigurerAdapter`的三个`configure()`方法来配置Web安全性，这个过程中会使用传递进来的参数设置行为：

- configure(WebSecurity)：通过重写，配置Spring Security的Filter链
- configure(HttpSecurity)：通过重写，配置如何通过拦截器保护请求
- configure(AuthenticationManagerBuilder)：通过重写，配置user-detail服务

在`SecurityConfig`类中我们，我们没有用重写上述方法的任意一个，那么当前情况下，默认的方法如下：

```java
public void configure(WebSecurity web) throws Exception {}

protected void configure(HttpSecurity http) throws Exception {
    logger.debug("Using default configure(HttpSecurity). If subclassed this will potentially override subclass configure(HttpSecurity).");

    http
        .authorizeRequests()
            .anyRequest().authenticated()
            .and()
        .formLogin().and()
        .httpBasic();
}

protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    this.disableLocalConfigureAuthenticationBldr = true;
}
```

这个简单的默认配置指定了该如何保护HTTP请求，以及客户端认证用户的方案。通过调用`authorizeRequests()`和`anyRequest().authenticated()`就会要求所有进入应用的HTTP请求都要进行认证。它也配置Spring Security支持基于表单的登录以及HTTP Basic方式的认证。

**因为我们没有重写`configure(AuthenticationManagerBuilder)`方法，所以没有用户存储支撑认证过程。没有用户存储，实际上就等于没有用户。所以，在这里所有的请求都需要认证，但是没有人能够登录成功。**

为了让Spring Security满足我们应用的需求，还需要再添加一点配置。具体来讲，我们需要：

- 配置用户存储
- 指定哪些请求需要认证，哪些请求不需要认证，以及所需要的权限
- 提供一个自定义的登录页面，替代原来简单的默认登录页

除了Spring Security的这些功能，我们可能还希望基于安全限制，有选择性地在Web视图上显示特定的内容。

### 9.2 选择查询用户详细信息的服务

我们所需要的是用户存储，也就是用户名、密码以及其他信息存储的地方，在进行认证决策的时候，会对其进行检索。

Spring Security非常灵活，能够基于各种数据存储来认证用户。它内置了多种常见的用户存储场景，如内存、关系型数据库以及LDAP。

借助Spring Security的Java配置，我们能够很容易地配置一个或多个数据存储方案。

#### 9.2.1 使用基于内存的用户存储

我们就从最简单的开始：在内存中维护用户存储。

因为我们的安全配置类扩展了`WebSecurityConfigurerAdapter`，因此配置用户存储的最简单方式就是重写`configure(AuthenticationManagerBuilder)`方法。`AuthenticationManagerBuilder`有多个方法可以用来配置Spring Security对认证的支持。通过`inMemoryAuthentication()`
方法，我们可以启用、配置并任意填充基于内存的用户存储。

```java
package spittr.config;


import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity // 启用Web安全性
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user").password("password").roles("USER").and()
            .withUser("admin").password("password").roles("USER", "ADMIN");
    }
    
}
```

configure()方法中的`AuthenticationManagerBuilder`使用构造者（Builder设计模式）风格的接口来构建认证配置。通过简单地调用`inMemoryAuthentication()`就能启用内存用户存储。但是我们还需要有一些用户，否则的话，这和没有用户并没有什么区别。

我们需要调用`withUser()`方法为内存用户存储添加新的用户，这个方法的参数是username。`withUser()`方法返回的是`UserDetailsBuilder`（这个类是`UserDetailsManagerConfigurer`类的一个内部类）对象，这个对象提供了多个进一步配置用户的方法，包括设置用户密码的`password()`方法以及为给定用户授予一个或多个角色权限的`role()`方法。

上述代码中，我们添加了两个用户，“user”和“admin”，密码均为“password”。“user”用户具有角色“USER”，而“admin”用户具有“USER”和“ADMIN”两个角色。这里`and()`方法能够将多个用户的配置连接起来。

`roles()`方法是`authorities()`方法的简写形式，二者有如下的对应关系：`builder.roles("USER","ADMIN");`等价于`builder.authorities("ROLE_USER","ROLE_ADMIN");`。

根据`roles()`方法的注释，该方法所给定的值都会添加一个`ROLE_`前缀，并将其作为权限授予给用户。

以下是配置用户详细信息的方法：

方法 | 描述
----- | -----
`accountExpired(boolean)` | 定义账号是否已经过期
`accountLocked(boolean)` | 定义账号是否已经锁定
`and()` | 用来连接配置
`authorities(GrantedAuthority...)` | 授予某个用户一项或多项权限
`authorities(List<? extends GrantedAuthority>)` | 授予某个用户一项或多项权限
`authorities(String...)` | 授予某个用户一项或多项权限
`credentialsExpired(boolean)` | 定义凭证是否已经过期
`disabled(boolean)` | 定义账号是否已被禁用
`password(String)` | 定义用户的密码
`roles(String...)` | 授予某个用户一项或多项角色

对于调试和开发人员测试来讲，基于内存的用户存储是很有用的，但是对于生产级别的应用来讲，这就不是最理想的可选方案了。为了用于生产环境，通常最好将用户数据保存在某种类型的数据库之中。

#### 9.2.2 基于数据库表进行认证

用户数据通常会存储在关系型数据库中，并通过JDBC进行访问。为了配置Spring Security使用以JDBC为支撑的用户存储，我们可以使用`jdbcAuthentication()`方法：

```java

@Autowired
private DataSource dataSource;

@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.jdbcAuthentication().dataSource(dataSource);
}
```

我们必须要配置的只是一个`DataSource`，这样的话，就能访问关系型数据库了。

**重写默认的用户查询功能**

尽管默认的最少配置能够让一切运转起来，但是它对我们的数据库模式有一些要求。它预期存在某些存储用户数据的表。更具体来说，下面的代码片段来源于Spring Security内部，这块代码展现了当查找用户信息时所执行的SQL查询语句：

```java
public static final String DEF_USERS_BY_USERNAME_QUERY =
    "select username,password,enabled " +
    "from users " +
    "where username = ?";
public static final String DEF_AUTHORITIES_BY_USERNAME_QUERY =
    "select username,authority " +
    "from authorities " +
    "where username = ?";
public static final String DEF_GROUP_AUTHORITIES_BY_USERNAME_QUERY =
    "select g.id, g.group_name, ga.authority " +
    "from groups g, group_members gm, group_authorities ga " +
    "where gm.username = ? " +
    "and g.id = ga.group_id " +
    "and g.id = gm.group_id";
```

在第一个查询中，我们获取了用户的用户名、密码以及是否启用的信息，这些信息会用来进行用户认证。接下来的查询查找了用户所授予的权限，用来进行鉴权，最后一个查询中，查找了用户作为群组的成员所授予的权限。

如果你能够在数据库中定义和填充满足这些查询的表，那么基本上就不需要你再做什么额外的事情了。但是，也有可能你的数据库与上面所述并不一致，那么你就会希望在查询上有更多的控制权。如果是这样的话，我们可以按照如下的方式配置自己的查询（基本上我们不会完全按照上述这种方式定义表）：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.jdbcAuthentication()
        .dataSource(dataSource)
        .usersByUsernameQuery(
            "select username, password, enabled from spitter where username=?"
        ).authoritiesByUsernameQuery(
            "select username, role from spitter where username=?"
        );
}
```

我们只重写了认证和基本权限的查询语句，但是通过调用`groupAuthoritiesByUsername()`方法，我们也能够将群组权限重写为自定义的查询语句。

将默认的SQL查询替换为自定义的设计时，很重要的一点就是要**遵循查询的基本协议**。所有查询都**将用户名作为唯一的参数**。认证查询会选取用户名、密码以及启用状态信息。权限查询会选取零行或多行包含该用户名及其权限信息的数据。群组权限查询会选取零行或多行数据，每行数据中都会包含群组ID、群组名称以及权限。

**使用转码后的密码**

上面的认证查询会预期用户密码存储在了数据库之中。这里唯一的问题在于如果密码明文存储的话，会很容易受到黑客的窃取。但是，如果数据库中的密码进行了转码的话，那么认证就会失败，因为它与用户提交的明文密码并不匹配。

为了解决这个问题，我们需要借助`passwordEncoder()`方法指定一个密码转码器：

（原书中使用的是`StandardPasswordEncoder`，这个类已经被标记为`@Deprecated`，因为它被认为是不安全的，这里使用`Pbkdf2PasswordEncoder`。）

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.jdbcAuthentication()
        .dataSource(dataSource)
        .usersByUsernameQuery(
            "select username, password, enabled from spitter where username=?"
        ).authoritiesByUsernameQuery(
            "select username, role from spitter where username=?"
        ).passwordEncoder(new Pbkdf2PasswordEncoder("53cr3t"));
}
```

`passwordEncoder()`方法可以接受Spring Security中`PasswordEncoder`接口的任意实现。Spring Security（版本4.2.7）的加密模块包含以下实现：

- 可以正常使用的：
    + BCryptPasswordEncoder
    + DelegatingPasswordEncoder
    + Pbkdf2PasswordEncoder
    + SCryptPasswordEncoder
- 已被标注为`@Deprecated`：
    + LdapShaPasswordEncoder
    + Md4PasswordEncoder
    + MessageDigestPasswordEncoder
    + NoOpPasswordEncoder
    + StandardPasswordEncoder

如果内置的实现无法满足我们的需求，那么我们也可以自定义一个实现，也就是实现`PasswordEncoder`接口：

```java
public interface PasswordEncoder {

    /**
     * Encode the raw password. Generally, a good encoding algorithm applies a SHA-1 or
     * greater hash combined with an 8-byte or greater randomly generated salt.
     */
    String encode(CharSequence rawPassword);

    /**
     * Verify the encoded password obtained from storage matches the submitted raw
     * password after it too is encoded. Returns true if the passwords match, false if
     * they do not. The stored password itself is never decoded.
     *
     * @param rawPassword the raw password to encode and match
     * @param encodedPassword the encoded password from storage to compare with
     * @return true if the raw password, after encoding, matches the encoded password from
     * storage
     */
    boolean matches(CharSequence rawPassword, String encodedPassword);

}
```

不管你使用哪一个密码转码器，都需要理解的一点是，数据库中的密码是永远不会解码的。所采取的策略与之相反，用户在登录时输入的密码会按照相同的算法进行转码，然后再与数据库中已经转码过的密码进行对比。这个对比是在`PasswordEncoder`的`matches()`方法中进行的。

#### 9.2.3 基于LDAP进行认证

为了让Spring Security使用基于LDAP的认证，我们可以使用`ldapAuthentication()`方法。这个方法在功能上类似于`jdbcAuthentication()`，只不过是LDAP版本。如下的configure()方法展现了LDAP认证的简单配置：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchFilter("(uid={0})")
        .groupSearchFilter("member={0}");
}
```
    
方法`userSearchFilter()`和`groupSearchFilter()`用来为基础LDAP查询提供过滤条件，它们分别用于搜索用户和组。默认情况下，对于用户和组的基础查询都是空的，也就是表明搜索会在LDAP层级结构的根开始。但是我们可以通过指定查询基础来改变这个默认行为：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}");
}
```

`userSearchBase()`属性为查找用户提供了基础查询。同样，`groupSearchBase()`为查找组指定了基础查询。我们声明用户应该在名为people的组织单元下搜索而不是从根开始。而组应该在名为groups的组织单元下搜索。

**配置密码比对**

基于LDAP进行认证的默认策略是进行绑定操作，直接通过LDAP服务器认证用户。另一种可选的方式是进行比对操作。这涉及将输入的密码发送到LDAP目录上，并要求服务器将这个密码和用户的密码进行比对。因为比对是在LDAP服务器内完成的，实际的密码能保持私密。

如果希望通过密码比对进行认证，可以通过`passwordCompare()`方法来实现：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}")
        .passwordCompare();
}
```

默认情况下，在登录表单中提供的密码将会与用户的LDAP条目中的`userPassword`属性进行比对。如果密码被保存在不同的属性中，可以通过`passwordAttribute()`方法来声明密码属性的名称：

（注意`passwordEncoder(new Md5PasswordEncoder())`中，`Md5PasswordEncoder`类实现的接口`org.springframework.security.authentication.encoding.PasswordEncoder`已经被标注为`@Deprecated`。）

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}")
        .passwordCompare()
        .passwordEncoder(new Md5PasswordEncoder())
        .passwordAttribute("passcode");
}
```

在本例中，我们指定了要与给定密码进行比对的是“passcode”属性。另外，我们还可以指定密码转码器。在进行服务器端密码比对时，有一点非常好，那就是实际的密码在服务器端是私密的。但是进行尝试的密码还是需要通过线路传输到LDAP服务器上，这可能会被黑客所拦截。为了避免这一点，我们可以通过调用`passwordEncoder()`方法指定加密策略。上述代码使用了MD5加密，，密码会进行MD5加密。这需要LDAP服务器上密码也使用MD5加密。

**引用远程的LDAP服务器**

到目前为止，我们忽略的一件事就是LDAP和实际的数据在哪里。

默认情况下，Spring Security的LDAP认证假设LDAP服务器监听本机的33389端口。但是，如果你的LDAP服务器在另一台机器上，那么可以使用`contextSource()`方法来配置这个地址：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}")
        .contextSource()
        .url("ldap://habuma.com:389/dc=habuma,dc=com");
}
```

`contextSource()`方法会返回一个`ContextSourceBuilder`对象，这个对象除了其他功能以外，还提供了`url()`方法用来指定LDAP服务器的地址。

**配置嵌入式的LDAP服务器**

如果我们没有现成的LDAP服务器供认证使用，Spring Security还为我们提供了嵌入式的LDAP服务器。我们不再需要设置远程LDAP服务器的URL，只需通过`root()`方法指定嵌入式服务器的根前缀就可以了：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}")
        .contextSource()
        .root("dc=habuma,dc=com");
}
```

当LDAP服务器启动时，它会尝试在类路径下寻找LDIF文件来加载数据。LDIF（LDAP Data Interchange Format，LDAP数据交换格式）是以文本文件展现LDAP数据的标准方式。每条记录可以有一行或多行，每项包含一个名值对。记录之间通过空行进行分割。

当然我们也可以显式指定要加载的LDIF文件：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.ldapAuthentication()
        .userSearchBase("ou=people")
        .userSearchFilter("(uid={0})")
        .groupSearchBase("ou=groups")
        .groupSearchFilter("member={0}")
        .contextSource()
        .root("dc=habuma,dc=com")
        .ldif("classpath:users.ldif");
}
```

在这里，我们明确要求LDAP服务器从类路径根目录下的users.ldif文件中加载内容

如下就是一个包含用户数据的LDIF文件：

```text
dn: ou=groups,dc=habuma,dc=com
objectclass: top
objectclass: organizationalUnit
ou: groups
dn: ou=people,dc=habuma,dc=com
objectclass: top
objectclass: organizationalUnit
ou: people
dn: uid=habuma,ou=people,dc=habuma,dc=com
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Craig Walls
sn: Walls
uid: habuma
userPassword: password
dn: uid=jsmith,ou=people,dc=habuma,dc=com
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: John Smith
sn: Smith
uid: jsmith
userPassword: password
dn: cn=spittr,ou=groups,dc=habuma,dc=com
objectclass: top
objectclass: groupOfNames
cn: spittr
member: uid=habuma,ou=people,dc=habuma,dc=com
```

Spring Security内置的用户存储非常便利，并且涵盖了最为常用的用户场景。但是，如果你的认证需求不是那么通用的话，那么就需要创建并配置自定义的用户详细信息服务了。

#### 9.2.4 配置自定义的用户服务

假设我们需要认证的用户存储在非关系型数据库中，如Mongo或Neo4j，在这种情况下，我们需要提供一个自定义的`org.springframework.security.core.userdetails.UserDetailsService`实现。这个接口非常简单：

```java
public interface UserDetailsService {
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```

我们所需要做的就是实现`loadUserByUsername()`方法，根据给定的用户名来查找用户。`loadUserByUsername()`方法会返回代表给定用户的`UserDetails`对象。

示例如下：

```java
public class SpitterUserService implements UserDetailsService {

    private final SpitterRepository spitterRepository;

    // 诸如SpitterRepository
    public SpitterUserService(SpitterRepository spitterRepository) {
        this.spitterRepository = spitterRepository;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws  UsernameNotFoundException {

        // 查找Spitter
        Spitter spitter = spitterRepository.findByUsername(username);

        if (spitter != null) {
            List<GrantedAuthority> authorities = new ArrayList<>();
            // 创建权限列表
            authorities.add(new SimpleGrantedAuthority("ROLE_SPITTER"));
            // 返回User
            return new User(spitter.getUsername(), spitter.getPassword(), authorities);
        }
        throw new UsernameNotFoundException("User '" + username + "' not found.");
    }
}
```

`SpitterUserService`有意思的地方在于它并不知道用户数据存储在什么地方。设置进来的`SpitterRepository`能够从关系型数据库、文档数据库或图数据中查找Spitter对象，甚至可以伪造一个。`SpitterUserService`不知道也不会关心底层所使用的数据存储。它只是获得`Spitter`对象，并使用它来创建`User`对象。

为了使用`SpitterUserService`来认证用户，我们可以通过`userDetailsService()`方法将其设置到安全配置中：

```java
@Autowired
private SpitterRepository spitterRepository;

@Override
protected void configure(AuthenticationManagerBuilder auth) {
    auth.userDetailsService(new SpitterUserService(spitterRepository);)
}
```

`userDetailsService()`方法（类似于`jdbcAuthentication()`、`ldapAuthentication()`以及`inMemoryAuthentication()`）会配置一个用户存储。不过，这里所使用的不是Spring所提供的用户存储，而是使用`UserDetailsService`的实现。

另一张方案就是修改`Spitter`类，让其实现`UserDetails`接口，这样`loadUserByUsername()`就能直接返回`Spiter`对象，而不必再将它的值复制到`User`对象中。

### 9.3 拦截请求

在任何应用中，并不是所有的请求都需要同等程度地保护。有些请求需要认证，而另一些可能并不需要。有些请求可能只有具备特定权限的用户才能访问，没有这些权限的用户会无法访问。

考虑Spittr应用的请求。首页当然是公开的，不需要进行保护。类似地，因为所有的Spittle都是公开的，所以展现Spittle的页面不需要安全性。但是，创建Spittle的请求只有认证用户才能执行。同样，尽管用户基本信息页面是公开的，不需要认证，但是，如果要处理“/spitter/me”请求，并展现当前用户的基本信息时，那么就需要进行认证，从而确定要展现谁的信息。

对每个请求进行细粒度安全性控制的关键在于重写`configure(HttpSecurity)`方法

示例如下：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
        .antMatchers("/spitter/me").authenticated()
        .antMatchers(HttpMethod.POST, "/spittles").authenticated()
        .anyRequest().permitAll();

}
```

`configure()`方法中得到的`HttpSecurity`对象可以在多个方面配置HTTP的安全性。在这里，我们首先调用`authorizeRequests()`，然后调用该方法所返回的对象的方法来配置请求级别的安全性细节。其中，第一次调用`antMatchers()`指定了对“/spitter/me”路径的请求需要进行认证。第二次调用`antMatchers()`更为具体，说明对“/spittles”路径的HTTP POST请求必须要经过认证。最后对anyRequests()的调用中，说明其他所有的请求都是允许的，不需要认证和任何的权限。

在`antMatchers()`方法中设定的路径支持Ant风格的通配符：

```java
.antMatchers("/spitter/**").authenticated();
```

也可以在一个`antMatchers()`方法中指定多个路径：

```java
.antMatchers("/spitter/**", "/spittles/mine").quthenticated();
```

我们也可以使用`regexMatchers()`来代替`antMatchers()`：

```java
.regexMatchers("/spitter/.*").authenticated();
```

上述代码与`.antMatchers("/spitter/**").authenticated()`效果是相同的。

除了路径选择，我们还通过`authenticated()`和`permitAll()`来定义该如何保护路径。`authenticated()`要求在执行该请求时，必须已经登录了应用。如果用户没有认证的话，Spring Security的Filter将会捕获该请求，并将用户重定向到应用的登录页面。同时，`permitAll()`方法允许请求没有任何的安全限制。

除了`authenticated()`和`permitAll()`以外，还有其他的一些方法能够用来定义该如何保护请求，如下所示：

方法 | 能够做什么
-----|-----
`access(String)` | 如果给定的SpEL表达式计算结果为true，就允许访问
`anonymous()` | 允许匿名用户访问
`authenticated()` | 允许认证过的用户访问
`denyAll()` | 无条件拒绝所有访问
`fullyAuthenticated()` | 如果用户是完整认证的话（不是通过Remember-me功能认证的），就允许访问
`hasAnyAuthority(String...)` | 如果用户具备给定权限中的某一个的话，就允许访问
`hasAnyRole(String...)` | 如果用户具备给定角色中的某一个的话，就允许访问
`hasAuthority(String)` | 如果用户具备给定权限的话，就允许访问
`hasIpAddress(String)` | 如果请求来自给定IP地址的话，就允许访问
`hasRole(String)` | 如果用户具备给定角色的话，就允许访问
`not()` | 对其他访问方法的结果求反
`permitAll()` | 无条件允许访问
`rememberMe()` | 如果用户是通过Remember-me功能认证的，就允许访问

通过上述方法，我们所配置的安全性能够不仅仅限于认证用户。例如我们可以要求用户不仅需要认证，还需要具备`ROLE_SPITTER`权限：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
        .antMatchers("/spitter/me").hasAuthority("ROLE_SPITTER")
        .antMatchers(HttpMethod.POST, "/spittles").hasAuthority("ROLE_SPITTER")
        .anyRequest().permitAll();

}
```

作为替代方案，我们还可以使用`hasRole()`方法，他会自动使用“ROLE_”前缀：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
        .antMatchers("/spitter/me").hasRole("SPITTER")
        .antMatchers(HttpMethod.POST, "/spittles").hasRole("SPITTER")
        .anyRequest().permitAll();

}
```

#### 9.3.1 使用Spring表达式进行安全保护

上一节中表内的大多数方法都是一维的，也就是我们可以使用`hasRole()`限制某个特定的角色，但是我们不能在相同的路径上同时通过`hasIpAddress()`限制特定的IP地址。

除了表9.4定义的方法以外，我们没有办法使用其他的条件。如果我们希望限制某个角色只能在星期二进行访问的话，该怎么办呢？

借助`access()`方法，我们可以将SpEL表达式作为生命访问限制的一种方式。例如，如下就是使用SpEL表达式来声明具有“SPITTER”角色才能访问“/spitter/me”：

```java
.antMatchers("/spitter/me").access("hasRole('SPITTER')")
```

让SpEL更强大的原因在于，`hasRole()`仅是Spring支持的安全相关表达式中的一种，下表列出了Spring Security支持的所有SpEL表达式：

表达式 | 计算结果
-----|-----
`authentication` | 用户的认证对象
`denyAll` | 结果始终为false
`hasAnyRole(list of roles)` | 如果用户被授予了列表中任意的指定角色，结果为true
`hasRole(role)` | 如果用户被授予了指定的角色，结果为true
`hasIpAddress(IP Address)` | 如果请求来自指定IP的话，结果为true
`isAnonymous()` | 如果当前用户为匿名用户，结果为true
`isAuthenticated()` | 如果当前用户进行了认证的话，结果为true
`isFullyAuthenticated()` | 如果当前用户进行了完整认证的话（不是通过Remember-me功能进行的认证），结果为true
`isRememberMe()` | 如果当前用户是通过Remember-me自动认证的，结果为true
`permitAll` | 结果始终为true
`principal` | 用户的principal对象

使用SpEL表达式后，我们就能够不再局限于基于用户的权限进行访问限制了。例如，如果我们希望限制“/spitter/me”的访问，不仅需要“ROLE_SPITTER”，还需要来自指定的IP地址，那么我们可以这样做：

```java
.antMatchers("/spitter/me")
    .access("hasRole('SPITTER') and hasIpAddress('192.168.1.2')")
```

我们可以使用SpEL实现各种各样的安全性限制。除此之外，Spring Security拦截请求还有另外一种方式：强制通道的安全性。

#### 9.3.2 强制通道的安全性

使用HTTP提交数据是一件具有风险的事情。如果使用HTTP发送无关紧要的信息，这可能不是什么大问题。但是如果你通过HTTP发送诸如密码和信用卡号这样的敏感信息的话，那你就是在找麻烦了。通过HTTP发送的数据没有经过加密，黑客就有机会拦截请求并且能够看到他们想看的数据。这就是为什么敏感信息要通过HTTPS来加密发送的原因。

使用HTTPS似乎很简单。你要做的事情只是在URL中的HTTP后加上一个字母“s”就可以了。通过添加“s”我们就能很容易地实现页面的安全性，但是忘记添加“s”同样也是很容易出现的。如果我们的应用中有多个链接需要HTTPS，估计在其中的一两个上忘记添加“s”的概率还是很高的。另一方面，你可能还会在原本并不需要HTTPS的地方，误用HTTPS。

传递到`configure()`方法中的`HttpSecurity`对象，除了具有`authorizeRequests()`方法以外，还有一个`requiresChannel()`方法，借助这个方法能够为各种URL模式声明所要求的通道。

考虑Spittr应用，尽管这个应用不需要新用卡号不需要信用卡号、社会保障号或其他特别敏感的信息，但用户有可能仍然希望信息是私密的。为了保证注册表单的数据通过HTTPS传送，我们可以在配置中添加`requiresChannel()`方法，如下所示：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.authorizeRequests()
        .antMatchers("/spitter/me").hasRole("SPITTER")
        .antMatchers(HttpMethod.POST, "/spittles").hasRole("SPITTER")
        .anyRequest().permitAll()
        .and()
        .requiresChannel()
        .antMatchers("/spitter/form").requiresSecure();

}
```

不论何时，只要是对“/spitter/form”的请求，Spring Security都视为需要安全通道（通过调用`requiresChannel()`确定的）并自动将请求重定向到HTTPS上。

与之相反，有些页面并不需要通过HTTPS传送。例如，首页不包含任何敏感信息，因此并不需要通过HTTPS传送。我们可以使用`requiresInsecure()`代替`requiresSecure()`方法，将首页声明为始终通过HTTP传送：

```java
.antMatchers("/").requiresInsecure()
```

如果通过HTTPS发送了对“/”的请求，Spring Security将会把请求重定向到不安全的HTTP通道上。

#### 9.3.3 防止跨站请求伪造

当一个POST请求提交到“/spittles”上时，`SpittleController`将会为用户创建一个新的Spittle对象。代码如下：

```java
@Controller
@RequestMapping("/spittles")
public class SpittleController {

    private SpittleRepository spittleRepository;

    @Autowired
    public SpittleController(SpittleRepository spittleRepository) {
        this.spittleRepository = spittleRepository;
    }

    // ...

    @RequestMapping(method = RequestMethod.POST)
    public String saveSpittle(SpittleForm form, Model model) throws Exception {
        spittleRepository
                .save(new Spittle(null, form.getMessage(), new Date(), form.getLongitude(), form.getLatitude()));
        return "redirect:/spittles";
    }
}
```

是，如果这个POST请求来源于其他站点的话，会怎么样呢？

例如我们有如下表单：

```html
<form method="POST" action="http://www.spittr.com/spittles">
    <input type="hidden" name="message" value="I'm stupid!" />
    <input type="submit" value="Click here to win a new car!" />
</form>
```

假设我们点击了按钮，那么你将会提交表单到如下地址http://www.spittr.com/spittles。如果你已经登录到了spittr.com，那么这就会广播一条消息，让每个人都知道你做了一件蠢事。

这是跨站请求伪造（cross-site request forgery，CSRF）的一个简单样例。

简单来讲，如果一个站点欺骗用户提交请求到其他服务器的话，就会发生CSRF攻击，这可能会带来消极的后果。

从Spring Security 3.2开始，默认就会启用CSRF防护。实际上，除非你采取行为处理CSRF防护或者将这个功能禁用，否则的话，在应用中提交表单时，你可能会遇到问题。

Spring Security通过一个同步token的方式来实现CSRF防护的功能。它将会拦截状态变化的请求（例如，非GET、HEAD、OPTIONS和TRACE的请求）并检查CSRF token。如果请求中不包含CSRF token的话，或者token不能与服务器端的token相匹配，请求将会失败，并抛出CsrfException异常。

这意味着在你的应用中，所有的表单必须在一个“_csrf”域中提交token，而且这个token必须要与服务器端计算并存储的token一致，这样的话当表单提交的时候，才能进行匹配。

好消息是，Spring Security已经简化了将token放到请求的属性中这一任务。

假如我们使用Thymeleaf作为页面模板，只要在`<form>`元素的`action`属性前添加Thymeleaf命名空间前缀即可，那么它会自动生成一个“_csrf”隐藏域：

```html
<form method="POST" th:action="@{/spittes}">

</form>
```

如果使用JSP作为页面模板的话，我们要做的事情如下：

```html
<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />
```

如果我们使用Spring的表单绑定标签的话，`<sf:form>`标签会自动为我们添加隐藏的CSRF token标签。

当然我们也可以禁用Spring Security的CSRF防护功能：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http
        // ...
        .csrf().disable();
}
```

禁用CSRF防护通常来说不是一个好主意。

由于CSRF防护是默认开启的，所以如果我们及不配置也不禁用，那么实际上我们根本无法访问应用。

### 9.4 认证用户

如果我们使用上一节中最简单的Spring Security配置的话，那么就能无偿地得到一个登录页。实际上，在重写`configure(HttpSecurity)`之前，我们都能使用一个简单却功能完备的登录页。但是，一旦重写了`configure(HttpSecurity`)方法，就失去了这个简单的登录页面。

不过，把这个功能找回来也很容易。我们所需要做的就是在`configure(HttpSecurity)`方法中，调用`formLogin()`。

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()
        .and()
            .authorizeRequests()
                .antMatchers("/spitter/me").hasRole("SPITTER")
                .antMatchers(HttpMethod.POST, "/spittles").hasRole("SPITTER")
                .anyRequest().permitAll()
        .and()
            .requiresChannel()
                .antMatchers("/spitter/form").requiresSecure();
}
```

但这个默认的登录页十分简陋，我们需要添加自定义登录页面。

#### 9.4.1 添加自定义的登录页

创建自定义登录页的第一步就是了解登录表单中都需要些什么。

以下是一个以Thymeleaf为模板的login页面：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Spittr</title>
<link rel="stylesheet" type="text/css" th:href="@{/resources/style.css}">
<link>
</head>
<body>
    <a th:href="@{/spitter/register}">Register</a>
    <form th:action="@{/login}" method="POST">
        <table>
            <tr>
                <td>User:</td>
                <td>
                    <input type="text" name="username" value="" />
                </td>
            </tr>
            <tr>
                <td>Password:</td>
                <td>
                    <input type="password" name="password" />
                </td>
            </tr>
            <tr>
                <td colspan="2">
                    <input id="remember_me" name="remember-me" type="checkbox" />
                    <label for="remember_me" class="inline">Remember me</label>
                </td>
            </tr>
            <tr>
                <td colspan="2">
                    <input name="submit" type="submit" value="Login" />
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

这个页面包含了username和password输入域，并提交到“/login”上。因为这是一个Thymeleaf模板，因此隐藏的“_csrf”域将会自动添加到表单中。

#### 9.4.2 启用HTTP Basic认证

对于应用程序的人类用户来说，基于表单的认证是比较理想的。但是在第16章中，将会看到如何将我们Web应用的页面转化为RESTful API。当应用程序的使用者是另外一个应用程序的话，使用表单来提示登录的方式就不太适合了。

HTTP Basic认证（HTTP Basic Authentication）会直接通过HTTP请求本身，对要访问应用程序的用户进行认证。

但这只是Web浏览器的显示方式。本质上，这是一个HTTP 401响应，表明必须要在请求中包含一个用户名和密码。在REST客户端向它使用的服务进行认证的场景中，这种方式比较适合。

如果要启用HTTP Basic认证的话，只需在`configure()`方法所传入的`HttpSecurity`对象上调用`httpBasic()`即可。另外，还可以通过调用`realmName()`方法指定域。如下是在Spring Security中启用HTTP Basic认证的典型配置：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin().loginPage("/login")
        .and()
           .httpBasic().realmName("Spittr")
        .and()
        // ...
}
```

在`httpBasic()`方法中，并没有太多的可配置项，甚至不需要什么额外配置。HTTP Basic认证要么开启要么关闭。

#### 9.4.3 启用Remember-me功能

对于应用程序来讲，能够对用户进行认证是非常重要的。但是站在用户的角度来讲，如果应用程序不用每次都提示他们登录是更好的。这就是为什么许多站点提供了Remember-me功能，你只要登录过一次，应用就会记住你，当再次回到应用的时候你就不需要登录了。

Spring Security使得为应用添加Remember-me功能变得非常容易。为了启用这项功能，只需在configure()方法所传入的`HttpSecurity`对象上调用`rememberMe()`即可。

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin().loginPage("/login")
        .and()
           .httpBasic().realmName("Spittr")
        .and()
            .rememberMe()
                .tokenValiditySeconds(2419200)
                .key("spitterKey")
        // ...
}
```

默认情况下，这个功能是通过在cookie中存储一个token完成的，这个token最多两周内有效。但是，在这里，我们指定这个token最多四周内有效（2,419,200秒）。

存储在cookie中的token包含用户名、密码、过期时间和一个私钥——在写入cookie前都进行了MD5哈希。默认情况下，私钥的名为SpringSecured，但在这里我们将其设置为spitterKey，使它专门用于Spittr应用。

如此简单。既然Remember-me功能已经启用，我们需要有一种方式来让用户表明他们希望应用程序能够记住他们。为了实现这一点，登录请求必须包含一个名为remember-me的参数。在登录表单中，增加一个简单复选框就可以完成这件事情：

```html
<tr>
    <td colspan="2">
        <input id="remember_me" name="remember-me" type="checkbox" />
        <label for="remember_me" class="inline">Remember me</label>
    </td>
</tr>
```

在应用中，与登录同等重要的功能就是退出。如果你启用Remember-me功能的话，更是如此，否则的话，用户将永远登录在这个系统中。我们下面将看一下如何添加退出功能。

#### 9.4.4 退出

事实上，按照我们的配置，退出功能已经启用了，不需要再做其他的配置了。我们需要的只是一个使用该功能的链接。

退出功能是通过Servlet容器中的Filter实现的（默认情况下），这个Filter会拦截针对“/logout”的请求。因此，为应用添加退出功能只需添加如下的链接即可（如下以Thymeleaf代码片段的形式进行了展现）：

```html
<a th:href="@{/logout}">Logout</a>
```

当用户点击这个链接的时候，会发起对“/logout”的请求，这个请求会被Spring Security的`LogoutFilter`所处理。用户会退出应用，所有的Remember-me token都会被清除掉。在退出完成后，用户浏览器将会重定向到“/login?logout”，从而允许用户进行再次登录。

当然，我们可以配置使其被重定向到其他页面，例如应用首页：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()
            .loginPage("/login")
        .and()
            .logout()
                .logoutSuccessUrl("/")
        // ...
}
```

我们还可以重写默认的`LogoutFilter`拦截路径：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin()
            .loginPage("/login")
        .and()
            .logout()
                .logoutUrl("/signout")
                .logoutSuccessUrl("/")
        // ...
}
```

到目前为止，我们已经看到了如何在发起请求的时候保护Web应用。这假设安全性主要涉及阻止用户访问没有权限的URL。但是，如果我们能够不给用户显示其无权访问的连接，那么这也是一个很好的思路。接下来，我们将会看一下如何添加视图级别的安全性。

### 9.5 保护视图

当为浏览器渲染HTML内容时，你可能希望视图中能够反映安全限制和相关的信息。一个简单的样例就是渲染用户的基本信息（比如显示“您已经以……身份登录”）。或者你想根据用户被授予了什么权限，有条件地渲染特定的视图元素。

我们看到了在Spring MVC应用中渲染视图的两个最重要的可选方案：JSP和Thymeleaf。不管你使用哪种方案，都有办法在视图上实现安全性。Spring Security本身提供了一个JSP标签库，而Thymeleaf通过特定的方言实现了与Spring Security的集成。

#### 9.5.1 使用Spring Security的JSP标签库

Spring Security的JSP标签库很小，只包含三个标签：

JSP标签 | 作用
-----|-----
`<security:accesscontrollist>` | 如果用户通过访问控制列表授予了指定的权限，那么渲染该标签体中的内容
`<security:authentication>` | 渲染当前用户认证对象的详细信息
`<security:authorize>` | 如果用户被授予了特定的权限或者SpEL表达式的计算结果为true，那么渲染该标签体中的内容

为了使用上述标签，我们需要在对应的JSP中声明：

```jsp
<%@ taglib prefix="security" uri="http://www.springframework.org/security/tags" %>
```

**访问认证信息的细节**

借助Spring Security JSP标签库，所能做到的最简单的一件事情就是便利地访问用户的认证信息。例如，对于Web站点来讲，在页面顶部以用户名标示显示“欢迎”或“您好”信息是很常见的。这恰恰是`<security:authentication>`能为我们所做的事情。

示例：

```jsp
Hello <security:authentication property="principal.username" />
```

其中，property用来标示用户认证对象的一个属性。可用的属性取决于用户认证的方式。但是，我们可以依赖几个通用的属性，在不同的认证方式下，它们都是可用的：

使用`<security:authentication>`标签来访问用户的认证详情：

认证属性 | 描述
-----|-----
`authorities` | 一组用于表示用户所授予权限的`GrantedAuthority`对象
`credentials` | 用于核实用户的凭证（通常，这会是用户的密码）
`details` | 认证的附加信息（IP地址、证件序列号、会话ID等）
`principal` | 用户的基本信息对象

在上述示例中，实际上渲染的是`principal`属性中嵌套`username`属性。

当像前面示例那样使用时，`<security:authentication>`将在视图中渲染属性的值。但是如果你愿意将其赋值给一个变量，那只需要在`var`属性中指明变量的名字即可。例如，如下展现了如何将其设置给名为loginId的属性：

```jsp
<security:authentication property="principal.username" var="loginId" />
```

这个变量默认是定义在页面作用域内的。但是我们可以使用`scope`属性来声明这个变量的作用域：

```jsp
<security:authentication property="principal.username" var="loginId" scope="request" />
```

**条件性的渲染内容**

有时候视图上的一部分内容需要根据用户被授予了什么权限来确定是否渲染。对于已经登录的用户显示登录表单，或者对还未登录的用户显示个性化的问候信息都是毫无意义的。

Spring Security的`<security:authorize>`JSP标签能够根据用户被授予的权限有条件地渲染页面的部分内容。例如，在Spittr应用中，对于没有`ROLE_SPITTER`角色的用户，我们不会为其显示添加新Spitter记录的表单。

```jsp
<%@ taglib uri="http://www.springframework.org/tags/form" prefix="sf" %>
<%@ taglib uri="http://www.springframework.org/tags" prefix="s"%>
<%@ taglib uri="http://www.springframework.org/security/tags" prefix="security"%>
<security:authorize access="hasRole('SPITTER')">
    <s:url value="/spittles" var="spittle_url"/>
    <sf:form modelAttribute="spittle" action="${spittle_url}">
        <sf:label path="text">
            <s:message code="label.spittle" text="Enter spittle:"/>
        </sf:label>
        <sf:textarea path="text" rows="2" cols="40" />
        <sf:errors path="text" />
        <br />
        <div class="spitItSubmitIt">
            <input type="submit" value="Spit it!" 
                class="status-btn round-btn disabled" />
        </div>
    </sf:form>
</security:authorize>
```

这里`access`属性被赋值为一个SpEL表达式，这个表达式的值将确定`<sec:authorize>`标签主体的内容是否渲染。这里我们使用`hasRole('SPITTER')`来确保用户具有`SPITTER`角色。事实上，当使用`access`属性时，我们可以任意发挥SpEL的强大威力。

借助于这些可用的表达式，可以构造出非常有意思的安全性约束。例如，假设应用中有一些管理功能只能对用户名为habuma的用户可用：

```jsp
<security:authorize access="isAuthenticated() and principal.username=='habuma'">
    <a href="/admin">Aaministration</a>
</security:authorize>
```

上述示例可能会令人困扰，尽管我们想限制管理功能只能给habuma用户，但使用JSP标签表达式并不见得理想。确实，它能在视图上阻止链接的渲染。但是没有什么可以阻止别人在浏览器的地址栏手动输入“/admin”这个URL。

结合我们前面所学，这个问题很容易解决。在安全配置中，添加一个对`antMatchers()`方法的调用将会严格限制对`/admin`这个URL的访问：

```java
.antMatchers("/admin")
    .access("isAuthenticated() and principal.username == 'habuma'");
```

现在，管理功能已经被锁定了。URL地址得到了保护，并且到这个URL的链接在用户没有授权使用的情况下不会显示。但是为了做到这一点，我们需要在两个地方声明SpEL表达式——在安全配置中以及在`<security:authorize>`标签的`access`属性中。有没有办法消除这种重复性，并且还要确保只有规则条件满足的情况下才渲染管理功能的链接呢？

这是`<security:authorize>`的url属性所要做的事情。它不像`access`属性那样明确声明安全性限制，`url`属性对一个给定的URL模式会间接引用其安全性约束。鉴于我们已经在Spring Security配置中为“/admin”声明了安全性约束，所以我们可以这样使用`url`属性：

```jsp
<security:authorize url="/admin">
    <s:url value="/admin" var="admin_url" />
    <br />
    <a href="${admin_url}">Admin</a>
</security:authorize>
```

因为只有基本信息中用户名为“habuma”的已认证用户才能访问“/admin” URL，所以只有满足以上条件，`<security:authorize>`标签主体中的内容才会被渲染。我们只在一个地方配置了表达式（安全配置中），但是在两个地方进行了应用。

#### 9.5.2 使用Thymeleaf的Spring Security方言

与Spring Security的JSP标签库类似，Thymeleaf的安全方言提供了条件化渲染和显示认证细节的能力。

属性 | 作用
-----|-----
`sec:authentication` | 渲染认证对象的属性。类似于Spring Security的`<sec:authentication/>`JSP标签
`sec:authorize` | 基于表达式的计算结果，条件性的渲染内容。类似于Spring Security的`<sec:authorize/>`JSP标签
`sec:authorize-acl` | 基于表达式的计算结果，条件性的渲染内容。类似于Spring Security的`<sec:accesscontrollist/>` JSP标签
`sec:authorize-expr` | `sec:authorize`属性的别名
`sec:authorize-url` | 基于给定URL路径相关的安全规则，条件性的渲染内容。类似于Spring Security的`<sec:authorize/>` JSP标签使用url属性时的场景

为了使用安全方言，我们需要确保依赖Thymeleaf Extras Spring Security（这里使用Thymeleaf Extras Springsecurity4）。然后，还需要在配置中使用`SpringTemplateEngine`来注册`SpringSecurityDialect`：

```java
@Bean
public TemplateEngine templateEngine(ITemplateResolver templateResolver) {
    SpringTemplateEngine templateEngine = new SpringTemplateEngine();
    templateEngine.setTemplateResolver(templateResolver);
    // 配置Thymeleaf的Spring Security方言
    templateEngine.addDialect(new SpringSecurityDialect());
    return templateEngine;
}
```

安全方言注册完成之后，我们就可以在Thymeleaf模板中使用它的属性了。首先，需要在使用这些属性的模板中声明安全命名空间：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4">

</html>
```

标准的Thymeleaf方法依旧与之前一样，使用th前缀，安全方言则设置为使用sec前缀。

这样我们就能在任意合适的地方使用Thymeleaf属性了。比如，假设我们想要为认证用户渲染“Hello”文本。如下的Thymeleaf模板代码片段就能完成这项任务：

```html
<div sec:authorize="isAuthenticated()">
    Hello <span sec:authentication="name">someone</span>
</div>
```

`sec:authorize`属性会接受一个SpEL表达式。如果表达式的计算结果为true，那么元素的主体内容就会渲染。在本例中，表达式为`isAuthenticated()`，所以只有用户已经进行了认证，才会渲染`<div>`标签的主体内容。就这个标签的主体内容部分而言，它的功能是使用认证对象的name属性提示“Hello”文本。

如果我们要在URL中使用已认证用户的username，例如我们要对已认证用户显示“profile”链接：

```html
<span sec:authorize="isAuthenticated()">
    <a th:href="@{'/spitter/' + ${#authentication.name}}">profile</a>
</span>
```

使用`<sec:authorize>`JSP标签的`url`属性能够基于给定URL的权限有条件地渲染内容。在Thymeleaf中，我们可以通过`sec:authorize-url`属性完成相同的功能：

```html
<span sec:authorize-url="/admin">
    <br />
    <a th:href="@{/admin}">Admin</a>
</span>
```

如果用户有权限访问“/admin”的话，那么到管理页面的链接就会渲染，否则的话，这个链接将不会渲染。

### 9.6　小结

>
对于许多应用而言，安全性都是非常重要的切面。Spring Security提供了一种简单、灵活且强大的机制来保护我们的应用程序。
>
借助于一系列Servlet Filter，Spring Security能够控制对Web资源的访问，包括Spring MVC控制器。借助于Spring Security的Java配置模型，我们不必直接处理Filter，能够非常简洁地声明Web安全性功能。
>
当认证用户时，Spring Security提供了多种选项。我们探讨了如何基于内存用户库、关系型数据库和LDAP目录服务器来配置认证功能。如果这些可选方案无法满足认证需求的话，我们还学习了如何创建和配置自定义的用户服务。
>
在前面的几章中，我们看到了如何将Spring运用到应用程序的前端。在接下来的章中，我们将会继续深入这个技术栈，学习Spring如何在后端发挥作用，下一章将会首先从Spring的JDBC抽象开始。

## 第十章 通过Spring和JDBC征服数据库

本章内容：

- 定义Spring对数据访问的支持
- 配置数据库资源
- 使用Spring的JDBC模板

Spring自带了一组数据访问框架，集成了多种数据访问技术。不管你是直接通过JDBC还是像Hibernate这样的对象关系映射（object-relational mapping，ORM）框架实现数据持久化，Spring都能够帮你消除持久化代码中那些单调枯燥的数据访问逻辑。我们可以依赖Spring来处理底层的数据访问，这样就可以专注于应用程序中数据的管理了。

当开发Spittr应用的持久层的时候，会面临多种选择，我们可以使用JDBC、Hibernate、Java持久化API（Java Persistence API，JPA）或者其他任意的持久化框架。你可能还会考虑使用最近很流行的NoSQL数据库。

不管我们选择哪种持久化方式，Spring都能够提供支持。在本章，我们主要关注于Spring对JDBC的支持。

### 10.1 Spring的数据访问哲学

Spring的目标之一就是允许我们在开发应用程序时，能够遵循面向对象（OO）原则中的“针对接口编程”。Spring对数据访问的支持也不例外。

像很多应用程序一样，Spittr应用需要从某种类型的数据库中读取和写入数据。为了避免持久化的逻辑分散到应用的各个组件中，最好将数据访问的功能放到一个或多个专注于此项任务的组件中。这样的组件通常称为数据访问对象（data access object，DAO）或Repository。

为了避免应用与特定的数据访问策略耦合在一起，编写良好的Repository应该以接口的方式暴露功能。下图展现了设计数据访问层的合理方式。

<center>
    ![图10.1-Repository接口确保其与服务对象的松耦合](images\图10.1-Repository接口确保其与服务对象的松耦合.PNG)
    **服务对象本身并不会处理数据访问，而是将数据访问委托给Repository。Repository接口确保其与服务对象的松耦合**
</center>

服务对象通过接口来访问Repository。这样做会有几个好处。第一，它使得服务对象易于测试，因为它们不再与特定的数据访问实现绑定在一起。实际上，你可以为这些数据访问接口创建mock实现，这样无需连接数据库就能测试服务对象，而且会显著提升单元测试的效率并排除因数据不一致所造成的测试失败。

此外，数据访问层是以持久化技术无关的方式来进行访问的。持久化方式的选择独立于Repository，同时只有数据访问相关的方法才通过接口进行暴露。这可以实现灵活的设计，并且切换持久化框架对应用程序其他部分所带来的影响最小。如果将数据访问层的实现细节渗透到应用程序的其他部分中，那么整个应用程序将与数据访问层耦合在一起，从而导致僵化的设计。

为了将数据访问层与应用程序的其他部分隔离开来，Spring采用的方式之一就是提供统一的异常体系，这个异常体系用在了它支持的所有持久化方案中。

#### 10.1.1 了解Spring的数据访问异常体系

在使用JDBC（不使用Spring）连接数据库时，我们会在代码中强制捕获`SQLException`。`SQLException`表示在访问数据库时出现了问题，但是这个异常却无法处理。

可能导致抛出`SQLException`的常见问题包括：

- 应用程序无法连接数据库
- 要执行的查询存在语法错误
- 查询中所使用的表和（或）列不存在
- 试图插入或更新的数据违反了数据库约束

`SQLException`的问题在于捕获到它的时候该如何处理。事实上，能够触发`SQLException`的问题通常是不能在`catch`代码块中解决的。大多数抛出`SQLException`的情况表明发生了致命性错误。如果应用程序不能连接到数据库，这通常意味着应用不能继续使用了。类似地，如果查询时出现了错误，那在运行时基本上也是无能为力。

如果无法从`SQLException`中恢复，那为什么我们还要强制捕获它呢？

即使对某些`SQLException`有处理方案，我们还是要捕获`SQLException`并查看其属性才能获知问题根源的更多信息。这是因为`SQLException`被视为处理数据访问所有问题的通用异常。对于所有的数据访问问题都会抛出`SQLException`，而不是对每种可能的问题都会有不同的异常类型。

一些持久化框架提供了相对丰富的异常体系。例如，Hibernate提供了二十个左右的异常，分别对应于特定的数据访问问题。这样就可以针对想处理的异常编写`catch`代码块。

即便如此，Hibernate的异常是其本身所特有的。正如前面所言，我们想将特定的持久化机制独立于数据访问层。如果抛出了Hibernate所特有的异常，那我们对Hibernate的使用将会渗透到应用程序的其他部分。如果不这样做的话，我们就得捕获持久化平台的异常，然后将其作为平台无关的异常再次抛出。

一方面，JDBC的异常体系过于简单了——实际上，它算不上一个体系。另一方面，Hibernate的异常体系是其本身所独有的。我们需要的数据访问异常要具有描述性而且又与特定的持久化框架无关。

**Spring所提供的平台无关的持久化异常**

Spring JDBC提供的数据访问异常体系解决了以上的两个问题。不同于JDBC，Spring提供了多个数据访问异常，分别描述了它们抛出时所对应的问题。

JDBC异常 | Spring的数据访问异常
-----|-----
BatchUpdateException<br>DataTruncation<br>SQLException<br>SQLWarning | BadSqlGrammarException<br>CannotAcquireLockException<br>CannotSerializeTransactionException<br>CannotGetJdbcConnectionException<br>CleanupFailureDataAccessException<br>ConcurrencyFailureException<br>DataAccessException<br>DataAccessResourceFailureException<br>DataIntegrityViolationException<br>DataRetrievalFailureException<br>DataSourceLookupApiUsageException<br>DeadlockLoserDataAccessException<br>DuplicateKeyException<br>EmptyResultDataAccessException<br>IncorrectResultSizeDataAccessException<br>IncorrectUpdateSemanticsDataAccessException<br>InvalidDataAccessApiUsageException<br>InvalidDataAccessResourceUsageException<br>InvalidResultSetAccessException<br>JdbcUpdateAffectedIncorrectNumberOfRowsException<br>LbRetrievalFailureException<br>NonTransientDataAccessResourceException<br>OptimisticLockingFailureException<br>PermissionDeniedDataAccessException<br>PessimisticLockingFailureException<br>QueryTimeoutException<br>RecoverableDataAccessException<br>SQLWarningException<br>SqlXmlFeatureNotImplementedException<br>TransientDataAccessException<br>TransientDataAccessResourceException<br>TypeMismatchDataAccessException<br>UncategorizedDataAccessException<br>UncategorizedSQLException<br>

（在此没有列出所有的异常）

尽管Spring的异常体系比JDBC简单的`SQLException`丰富得多，但它并没有与特定的持久化方式相关联。这意味着我们可以使用Spring抛出一致的异常，而不用关心所选择的持久化方案。这有助于我们将所选择持久化机制与数据访问层隔离开来。

上述表中的这些异常都继承自`DataAccessException`，而这个类继承了`RuntimeException`。也就是说，上述的这些异常都是非检查型异常，我们不必必须捕获Spring所报出的数据访问异常。

`DataAccessException`只是Sping处理检查型异常和非检查型异常哲学的一个范例。Spring认为触发异常的很多问题是不能在`catch`代码块中修复的。Spring使用了非检查型异常，而不是强制开发人员编写`catch`代码块（里面经常是空的）。这把是否要捕获异常的权力留给了开发人员。

#### 10.1.2 数据访问模板化

Spring在数据访问中使用模板方法模式。不管我们使用什么样的技术，都需要一些特定的数据访问步骤。例如，我们都需要获取一个到数据存储的连接并在处理完成后释放资源。这都是在数据访问处理过程中的固定步骤，但是每种数据访问方法又会有些不同，我们会查询不同的对象或以不同的方式更新数据，这都是数据访问过程中变化的部分。

Spring将数据访问过程中固定的和可变的部分明确划分为两个不同的类：模板（template）和回调（callback）。模板管理过程中固定的部分，而回调处理自定义的数据访问代码。如下图所示：

<center>
    ![图10.2-Repository模板和回调负责不同的任务.PNG](images\图10.2-Repository模板和回调负责不同的任务.PNG)
    **Spring的数据访问模板类负责通用的数据访问功能。对于应用程序特定的任务，则会调用自定义的回调对象**
</center>

如图所示，Spring的模板类处理数据访问的固定部分：事务控制、管理资源以及处理异常。同时，应用程序相关的数据访问——语句、绑定参数以及整理结果集：在回调的实现中处理。事实证明，这是一个优雅的架构，因为你只需关心自己的数据访问逻辑即可。

针对不同的持久化平台，Spring提供了多个可选的模板：

模板类 | 用途
-----|-----
`CciTemplate` | JCA CCI连接
`JdbcTemplate` | JDBC连接
`NamedParameterJdbcTemplate` | 支持命名参数的JDBC连接
`SimpleJdbcTemplate`（已废弃） | 通过Java 5简化后的JDBC连接
`HibernateTemplate` | Spring 4支持Hibernate3、Hibernate4以及Hibernate5（分别对应三个不同包下的类）
`SqlMapClientTemplate`（已废弃） | ibatis的sqlMap客户端
`JdoTemplate`（已废弃） | Java数据对象（Java Data Object）实现
`JpaTemplate`（已废弃） | Java持久化API的实体管理器

在本章中，我们将会从基础的JDBC访问开始，因为这是从数据库中读取和写入数据的最基本方式。在第11章中，我们将会了解Hibernate和JPA，我们会在第12章结束Spring持久化的话题，在这一章中，将会看到Spring Data项目是如何让Spring支持无模式数据的。

但首先要说明的是Spring所支持的大多数持久化功能都依赖于数据源。因此，在声明模板和Repository之前，我们需要在Spring中配置一个数据源用来连接数据库。

### 10.2 配置数据源

无论选择Spring的哪种数据访问方式，我们都需要配置一个数据源引用。Spring提供了在Spring上下文中配置数据源bean的多种方式，包括：

- 通过JDBC驱动程序定义的数据源
- 通过JNDI查找的数据源
- 连接池的数据源

#### 10.2.1 使用JNDI数据源

Spring应用程序经常部署在Java EE应用服务器中，如WebSphere、JBoss或甚至像Tomcat这样的Web容器中。这些服务器允许你配置通过JNDI获取数据源。这种配置的好处在于数据源完全可以在应用程序之外进行管理，这样应用程序只需在访问数据库的时候查找数据源就可以了。另外，在应用服务器中管理的数据源通常以池的方式组织，从而具备更好的性能，并且还支持系统管理员对其进行热切换。

利用Spring，我们可以像使用Spring bean那样配置JNDI中数据源的引用并将其装配到需要的类中。位于`jee`命名空间下的`<jee:jndi-lookup>`元素可以用于检索JNDI中的任何对象（包括数据源）并将其作为Spring的bean。例如，如果应用程序的数据源配置在JNDI中，我们可以使用`<jee:jndi-lookup>`元素将其装配到Spring中，如下所示：

```xml
<jee:jndi-lookup id="dataSource" jndi-name="/jdbc/SpitterDS" resource-ref="true" />
```

其中`jndi-name`属性用于指定JNDI中资源的名称。如果只设置了`jndi-name`属性，那么就会根据指定的名称查找数据源。但是，如果应用程序运行在Java应用服务器中，你需要将`resource-ref`属性设置为`true`，这样给定的`jndi-name`将会自动添加“java:comp/env/”前缀。

如果使用JavaConfig的方式配置，则需要借助`JndiObjectFactoryBean`从JNDI中查找`DataSource`：

```java
@Bean
public JndiObjectFactoryBean dataSource() {
    JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
    jndiObjectFB.setJndiName("jdbc/SpitterDS");
    jndiObjectFB.setResourceRef(true);
    jndiObjectFB.setProxyInterface(DataSource.class);
    return jndiObjectFB;
}
```

#### 10.2.2 使用数据源连接池

尽管Spring并没有提供数据源连接池实现，但时我们有多种可选的方案：

- Apache Commons DBCP/DBCP2
- c3p0
- BoneCP

这些连接池都能配置为Spring的数据源，在一定程度上与Spring自带的`DriverManagerDataSource`或`SingleConnectionDataSource`类似。

如下是使用XML配置DBCP数据源的方式（搭配H2数据库）：

```xml
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
    p:driverClassName="org.h2.Driver"
    p:url="jdbc:h2:tcp://localhost/~/spitter"
    p:username="admin"
    p:password="admin"
    p:initialSize="5"
    p:maxActive="10" />
```

使用JavaConfig的方式：

```java
@Bean
public BasicDataSource dataSource() {
    BasicDataSource ds = new BasicDataSource();
    ds.setDriverClassName("org.h2.Driver");
    ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
    ds.setUsername("admin");
    ds.setPassword("admin");
    ds.setInitialSize(5);
    ds.setMaxActive(10);
    return ds;
}
```

前四个属性（数据库连接四要素）是配置`BasicDataSource`所必需的。属性`driverClassName`指定了JDBC驱动类的全限定类名，在这里我们配置的是H2数据库。属性`url`用于设置数据库的JDBC URL。最后，`username`和`password`用于在连接数据库时进行认证。

以上四个基本属性定义了`BasicDataSource`的连接信息。除此以外，还有多个配置数据源连接池的属性：

属性 | 指定的内容
----- | -----
`initialSize` | 池启动时创建的连接数量
`maxActive` | 同一时间可从池中分配的最多连接数。如果设置为0，表示无限制
`maxIdle` | 池里不会被释放的最多空闲连接数。如果设置为0，表示无限制
`maxOpenPreparedStatements` | 在同一时间能够从语句池中分配的预处理语句（prepared statement）的最大数量。如果设置为0，表示无限制
`maxWait` | 在抛出异常之前，池等待连接回收的最大时间（当没有可用连接时）。如果设置为-1，表示无限等待
`minEvictableIdleTimeMillis` | 连接在池中保持空闲而不被回收的最大时间
`minIdle` | 在不创建新连接的情况下，池中保持空闲的最小连接数
`poolPreparedStatements` | 是否对预处理语句（prepared statement）进行池管理（布尔值）

（注意，DBCP2与DBCP使用的属性存在不同）

在我们的示例中，连接池启动时会创建5个连接；当需要的时候，允许`BasicDataSource`创建新的连接，但最大活跃连接数为10。

#### 10.2.3 基于JDBC驱动的数据源

在Spring中，通过JDBC驱动定义数据源是最简单的配置方式。Spring提供了三个这样的数据源类：

- DriverManagerDataSource：在每个连接请求时都会返回一个新建的连接。与DBCP的`BasicDataSource`不同，由`DriverManagerDataSource`提供的连接并没有进行池化管理
- SimpleDriverDataSource：与`DriverManagerDataSource`的工作方式类似，但是它直接使用JDBC驱动，来解决在特定环境下的类加载问题，这样的环境包括OSGi容器
- SingleConnectionDataSource：继承`DriverManagerDataSource`，在每个连接请求时都会返回同一个的连接。尽管它不是严格意义上的连接池，但可以将其视为只有一个连接的连接池

使用XML配置`DriverManagerDataSource`的方式：

```xml
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource"
    p:driverClassName="org.h2.Driver"
    p:url="jdbc:h2:tcp://localhost/~/spitter"
    p:username="admin"
    p:password="admin" />
```

使用JavaConfig的方式：

```java
@Bean
public DataSource dataSource() {
    DriverManagerDataSource ds = new DriverManagerDataSource();
    ds.setDriverClassName("org.h2.Driver");
    ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
    ds.setUsername("admin");
    ds.setPassword("admin");
    return ds;
}
```

与具备池功能的数据源相比，唯一的区别在于这些数据源bean都没有提供连接池功能，所以没有可配置的池相关的属性。

尽管这些数据源对于小应用或开发环境来说是不错的，但是要将其用于生产环境，你还是需要慎重考虑。因为`SingleConnectionDataSource`有且只有一个数据库连接，所以不适合用于多线程的应用程序，最好只在测试的时候使用。而`DriverManagerDataSource`和`SimpleDriverDataSource`尽管支持多线程，但是在每次请求连接的时候都会创建新连接，这是以性能为代价的。

#### 10.2.4 使用嵌入式的数据源

嵌入式数据库作为应用的一部分运行，而不是应用连接的独立数据库服务器。尽管在生产环境的设置中，它并没有太大的用处，但是对于开发和测试来讲，嵌入式数据库都是很好的可选方案。这是因为每次重启应用或运行测试的时候，都能够重新填充测试数据。

Spring的`jdbc`命名空间能够简化嵌入式数据库的配置。例如，如下的程序清单展现了如何使用jdbc命名空间来配置嵌入式的H2数据库：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/jdbc 
        http://www.springframework.org/schema/jdbc/spring-jdbc.xsd">
        
    <jdbc:embedded-database id="dataSource" type="H2">
        <jdbc:script location="com/habuma/spitter/db/jdbc/schema.sql" />
        <jdbc:script location="com/habuma/spitter/db/jdbc/test-sql" />
    </jdbc:embedded-database>

</beans>
```

`<jdbc:embedded-database>`的`type`属性设置为H2，表明嵌入式数据库应该是H2数据库（需要引入相关依赖）。另外，我们还可以将`type`设置为DERBY，以使用嵌入式的ApacheDerby数据库。

在`<jdbc:embedded-database>`中，我们可以不配置也可以配置多个`<jdbc:script>`元素来搭建数据库。上述配置中包含了两个·元素：第一个引用了schema.sql，它包含了在数据库中创建表的SQL；第二个引用了test-data.sql，用来将测试数据填充到数据库中。

除了搭建嵌入式数据库以外，`<jdbc:embedded-database>`元素还会暴露一个数据源，我们可以像使用其他的数据源那样来使用它。在这里，id属性被设置成了`dataSource`，这也是所暴露数据源的bean ID。因此，当我们需要`javax.sql.DataSource`的时候，就可以注入`dataSource` bean。

如果使用JavaConfig的方式进行配置，需要使用`EmbeddedDatabaseBuilder`：

```java
@Bean
public DataSource dataSource() {
    return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .addScript("classpath:schema.sql")
            .addScript("classpath:test-data.sql")
            .build();
}
```

#### 10.2.5 使用profile选择数据源

我们已经看到了多种在Spring中配置数据源的方法，我相信你已经找到了一两种适合你的应用程序的配置方式。实际上，我们很可能面临这样一种需求，那就是在某种环境下需要其中一种数据源，而在另外的环境中需要不同的数据源。

例如，在开发期，嵌入式的数据源可能很合适。但是在QA环境中，我们可能又要使用DBCP数据源连接池。

第三章中使用profile特性可以很好的应用在这里，所需要做的就是将每个数据源配置在不同的profile下：

```java
@Bean
@Profile("dev")
public DataSource embeddedDataSource() {
    return new EmbeddedDatabaseBuilder().
            setType(EmbeddedDatabaseType.H2)
            .addScript("classpath:schema.sql")
            .addScript("classpath:test-data.sql")
            .build();
}

@Bean
@Profile("qa")
public DataSource qaDataSource() {
    BasicDataSource ds = new BasicDataSource();
    ds.setDriverClassName("org.h2.Driver");
    ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
    ds.setUsername("sa");
    ds.setPassword("");
    ds.setInitialSize(5);
    ds.setMaxActive(10);
    return ds;
}

@Bean
@Profile("production")
public DataSource dataSource() {
    JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
    jndiObjectFactoryBean.setJndiName("jdbc/SpittrDS");
    jndiObjectFactoryBean.setResourceRef(true);
    jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
    return (DataSource) jndiObjectFactoryBean.getObject();
}
```

要使用XML配置，可以使用嵌套的`<beans>`：

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
        <jdbc:embedded-database id="dataSource" type="H2">
            <jdbc:script location="com/habuma/spitter/db/jdbc/schema.sql" />
            <jdbc:script location="com/habuma/spitter/db/jdbc/test-sql" />
        </jdbc:embedded-database>
    </beans>

    <!-- qa阶段 -->
    <beans profile="qa">
        <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
            p:driverClassName="org.h2.Driver"
            p:url="jdbc:h2:tcp://localhost/~/spitter"
            p:username="admin"
            p:password="admin"
            p:initialSize="5"
            p:maxActive="10" />
    </beans>

    <!-- 生产阶段 -->
    <beans profile="prod">
        <jee:jndi-lookup 
            id="dataSource" 
            jndi-name="/jdbc/SpitterDS" 
            resource-ref="true" />
    </beans>

</beans>
```

通过使用profile功能，会在运行时选择数据源，这取决于哪一个profile处于激活状态。

### 10.3 在Spring中使用JDBC

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C10_Hitting_the_database_with_Spring_and_JDBC中*。

持久化技术有很多种，而Hibernate、iBATIS（MyBatis）和JPA只是其中的几种而已。尽管如此，还是有很多的应用程序使用最古老的方式将Java对象保存到数据库中，那就是使用JDBC。

JDBC不要求我们掌握其他框架的查询语言。它是建立在SQL之上的，而SQL本身就是数据访问语言。此外，与其他的技术相比，使用JDBC能够更好地对数据访问的性能进行调优。JDBC允许你使用数据库的所有特性，而这是其他框架不鼓励甚至禁止的。

再者，相对于持久层框架，JDBC能够让我们在更低的层次上处理数据，我们可以完全控制应用程序如何读取和管理数据，包括访问和管理数据库中单独的列。这种细粒度的数据访问方式在很多应用程序中是很方便的。例如在报表应用中，如果将数据组织为对象，而接下来唯一要做的就是将其解包为原始数据，那就没有太大意义了。

但是JDBC也不是十全十美的。虽然JDBC具有强大、灵活和其他一些优点，但也有其不足之处。

#### 10.3.1 应对失控的JDBC代码

如果使用JDBC所提供的直接操作数据库的API，我们需要负责处理与数据库访问相关的所有事情，其中包含管理数据库资源和处理异常。

例如，使用JDBC在数据库中插入一行数据：

```java
private static final String SQL_INSERTION_SPITTER = "insert into spitter (username, password, firstname, lastname) values (?, ?, ?, ?)";

// 使用配置的数据源，而不是DriverManager
private DataSource dataSource;

// constructor init DataSource...

public void addSpitter(Spitter spitter) {

    Connection conn = null;
    PreparedStatement stmt = null;

    try {

        // 获取连接
        conn = dataSource.getConnection();

        // 创建语句
        stmt = conn.prepareStatement(SQL_INSERTION_SPITTER);

        // 绑定参数
        stmt.setString(1, spitter.getUsername());
        stmt.setString(2, spitter.getPassword());
        stmt.setString(3, spitter.getFirstName());
        stmt.setString(4, spitter.getLastName());

        // 执行语句
        stmt.execute();

    } catch (SQLException e) {
        // ...
    } finally {
        // 清理资源
        try {
            if (stmt != null) {
                stmt.close();
            }
            if (conn != null) {
                conn.close();
            }
        } catch (SQLException e) {
            // ...
        }
    }
}
```

上述代码仅仅是为了向数据库中插入一条记录，对于JDBC而言，这是最简单的了。但是为什么要用这么多行代码才能做如此简单的事情呢？实际上，并非如此，只有几行代码是真正用于进行插入数据的。但是JDBC要求你必须正确地管理连接和语句，并以某种方式处理可能抛出的`SQLException`异常。而且我们还要捕捉它两次，我们要在插入记录出错时捕捉它，同时还需要在关闭语句和连接出错的时候捕捉它。

使用传统的JDBC来更新数据：

```java
private static final String SQL_UPDATE_SPITTER = "update spitter set username = ?, password = ?, firstname = ?, lastname = ? where id = ?";

// 使用配置的数据源，而不是DriverManager
private DataSource dataSource;

// constructor init DataSource...

public void addSpitter(Spitter spitter) {

    Connection conn = null;
    PreparedStatement stmt = null;

    try {

        // 获取连接
        conn = dataSource.getConnection();

        // 创建语句
        stmt = conn.prepareStatement(SQL_INSERTION_SPITTER);

        // 绑定参数
        stmt.setString(1, spitter.getUsername());
        stmt.setString(2, spitter.getPassword());
        stmt.setString(3, spitter.getFirstName());
        stmt.setString(4, spitter.getLastName());
        stmt.setLong(5, spitter.getId());

        // 执行语句
        stmt.execute();

    } catch (SQLException e) {
        // ...
    } finally {
        // 清理资源
        try {
            if (stmt != null) {
                stmt.close();
            }
            if (conn != null) {
                conn.close();
            }
        } catch (SQLException e) {
            // ...
        }
    }
}
```

实际上，上述更新一行数据的代码与插入一行数据的代码除了在SQL语句字符串上有不同，其余几乎是完全相同的。大量的JDBC代码都是用于创建连接和语句以及异常处理的样板代码。但实际上，这些样板代码是非常重要的。清理资源和处理错误确保了数据访问的健壮性。如果没有它们的话，就不会发现错误而且资源也会处于打开的状态，这将会导致意外的代码和资源泄露。我们不仅需要这些代码，而且还要保证它是正确的。基于这样的原因，我们才需要框架来保证这些代码只写一次而且是正确的。

#### 10.3.2 使用JDBC模板

Spring的JDBC框架承担了资源管理和异常处理的工作，从而简化了JDBC代码，让我们只需编写从数据库读写数据的必需代码。

Spring将数据访问的样板代码抽象到模板类之中。Spring为JDBC提供了三个模板类供选择：

- JdbcTemplate：最基本的Spring JDBC模板，这个模板支持简单的JDBC数据库访问功能以及基于索引参数的查询
- NamedParameterJdbcTemplate：使用该模板类执行查询时可以将值以命名参数的形式绑定到SQL中，而不是使用简单的索引参数
- SimpleJdbcTemplate（已废弃）：该模板类利用Java 5的一些特性如自动装箱、泛型以及可变参数列表来简化JDBC模板的使用。

`SimpleJdbcTemplate`已经被废弃了，其Java 5的特性被转移到了`JdbcTemplate`中，并且只有在你需要使用命名参数的时候，才需要使用`NamedParameterJdbcTemplate`。这样的话，对于大多数的JDBC任务来说，`JdbcTemplate`就是最好的可选方案，这也是本小节中所关注的方案。

**使用JdbcTemplate来插入数据**

为了让`JdbcTemplate`正常工作，只需要为其设置`DataSource`就可以了，这使得在Spring中配置`JdbcTemplate`非常容易：

```java
// 配置JDBC模板
@Bean
public JdbcOperations jdbcOperations(DataSource dataSource) {
    return new JdbcTemplate(dataSource);
}

// 配置数据源
@Bean
public DataSource dataSource() throws PropertyVetoException {
    ComboPooledDataSource dataSource = new ComboPooledDataSource();
    dataSource.setDriverClass("com.mysql.jdbc.Driver");
    dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
    dataSource.setUser("root");
    dataSource.setPassword("mysql");
    return dataSource();
}
```

在这里，`DataSource`是通过构造器参数注入进来的。这里所引用的`dataSource` bean可以是`javax.sql.DataSource`的任意实现（这里使用c3p0数据源）。

定义好`DataSource`和`JdbcTemplate`后我们就可以创建Repository类了：

```java
@Repository("jdbcSpitter")
public class JdbcSpitterRepository implements SpitterRepository {

    private static final String SQL_INSERT_SPITTER = "insert into spitter (username, password, firstname, lastname) values (?, ?, ?, ?)";
    
    @Autowired
    private JdbcOperations jdbcOperations;
    
}
```

这里`JdbcSpitterRepository`使用了`@Repository`注解，这表明它会在组件扫描时自动创建。其中其成员变量`jdbcOperations`通过`@Autowired`注解注入。

`JdbcOperations`是一个接口，定义了`JdbcTemplate`所实现的操作。通过注入`JdbcOperations`，而不是具体的`JdbcTemplate`，能够保证`JdbcSpitterRepository`通过`JdbcOperations`接口达到与`JdbcTemplate`保持松耦合。

`JdbcSpitterRepository`类具有可用的`JdbcTemplate`后，我们就可以真正实现通过用户注册表单注册用户的功能了：

```java
@Override
public void addSpitter(Spitter spitter) {
    
    jdbcOperations.update(SQL_INSERT_SPITTER,
            spitter.getUsername(),
            spitter.getPassword(),
            spitter.getFirstName(),
            spitter.getLastName());
    
}
```

其对应的spitter表如下：

```sql
CREATE TABLE `spitter` (
    `id` bigint(20) NOT NULL AUTO_INCREMENT,
    `username` varchar(16) NOT NULL,
    `password` varchar(25) NOT NULL,
    `firstname` varchar(30) NOT NULL,
    `lastname` varchar(30) NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8
```

这个版本的`save()`方法简单多了（相对于之前的`addSpitter()`）。这里没有了创建连接和语句的代码，也没有异常处理的代码，只剩下单纯的数据插入代码。

不能因为我们看不到这些样板代码，就意味着它们不存在。样板代码被巧妙地隐藏到JDBC模板类中了。当`update()`方法被调用的时候`JdbcTemplate`将会获取连接、创建语句并执行插入SQL。

在这里也看不到任何处理`SQLException`的代码。在框架内部，`JdbcTemplate`将会捕获所有可能抛出的`SQLException`，并将其转换为Spring定义的对数据访问的异常并重新抛出。由于这些异常都是运行时异常，所以我们不必进行捕获。

**使用JdbcTemplate读取数据**

`JdbcTemplate`也简化了数据的读取操作：

```java
@Override
public Spitter findByUsername(String username) {
    
    return jdbcOperations.queryForObject(SQL_SELECT_SPITTER, new RowMapper<Spitter>() {

        @Override
        public Spitter mapRow(ResultSet rs, int rowNum) throws SQLException {
            
            return new Spitter(
                    rs.getLong("id"), 
                    rs.getString("username"), 
                    rs.getString("password"), 
                    rs.getString("firstname"), 
                    rs.getString("lastname"));
        }
        
    }, username);
}
```

`findByUsername()`方法中使用`JdbcTemplate`的`queryForObject()`方法来进行查询，这个方法有三个参数：

- String类型的SQL语句
- `RowMapper`接口的实现类，用来从`ResultSet`中提取数据并构建对象。这里使用一个匿名内部类实现`RowMapper`接口
- 一个可变参数列表，列出了要绑定到SQL语句上参数

**使用命名参数**

在`save()`方法中，我们使用的SQL语句是这样的：`insert into spitter (username, password, firstname, lastname) values (?, ?, ?, ?)`。这意味着我们要小心参数的数据，在将参数传递给`update()`方法时要保证正确的顺序。此时如果我们修改了SQL语句中参数的顺序，那么我们也要在方法中修改参数的顺序。

除了这种方法之外，我们还可以使用命名参数。命名参数可以赋予SQL中每个参数一个明确的名字，在绑定值到SQL语句时就通过该名字引用参数，此时绑定值的顺序就不重要了。即使SQL语句发生了变化导致参数顺序与原来不一致，我们也不需要修改方法代码。

`NamedParameterJdbcTemplate`是一个特殊的JDBC模板类，它支持使用命名参数。

声明`NamedParameterJdbcTemplate`：

```java
@Bean
public NamedParameterJdbcOperations namedParameterjdbcOperations(DataSource dataSource) {
    return new NamedParameterJdbcTemplate(dataSource);
}
```

注意，`NamedParameterJdbcTemplate`实现的接口并不是`JdbcOperations`，而是`NamedParameterJdbcOperations`。

使用`NamedParameterJdbcTemplate`实现`save()`方法：

```java
private static final String SQL_INSERT_SPITTER_NAMED_PARAMETER = 
        "insert into spitter (username, password, firstname, lastname)"
                + " values "
                + "(:username, :password, :firstname, :lastname)";

@Autowired 
private NamedParameterJdbcOperations jdbcOperations;

@Override
public void addSpitter(Spitter spitter) {
    Map<String, Object> paramMap = new HashMap<>();
    
    // 绑定参数
    paramMap.put("username", spitter.getUsername());
    paramMap.put("password", spitter.getPassword());
    paramMap.put("firstname", spitter.getFirstName());
    paramMap.put("lastname", spitter.getLastName());
    
    // 执行数据插入
    jdbcOperations.update(SQL_INSERT_SPITTER_NAMED_PARAMETER, paramMap);
}
```

### 10.4 小结

>
数据是应用程序的血液。有些数据中心论者甚至主张数据即应用。鉴于数据的重要地位，以健壮、简单和清晰的方式开发应用程序的数据访问部分就显得举足轻重了。
>
在Java中，JDBC是与关系型数据库交互的最基本方式。但是按照规范，JDBC有些太笨重了。Spring能够解除我们使用JDBC中的大多数痛苦，包括消除样板式代码、简化JDBC异常处理，你所需要做的仅仅是关注要执行的SQL语句。
>
在本章中，我们学习了Spring对数据持久化的支持，以及Spring为JDBC所提供的基于模板的抽象，它能够极大地简化JDBC的使用。
>
在下一章中，我们会继续Spring数据持久化这一话题，将会学习Spring为Java持久化API所提供的功能。

## 第十一章 使用对象-关系映射持久化数据

本章内容：

- 使用Spring和Hibernate
- 借助上下文Session，编写不依赖于Spring的Repository
- 通过Spring使用JPA
- 借助Spring Data实现自动化的JPA Repository

在数据持久化的世界中，JDBC是很基础的，对于份内的工作，它能很好地完成并且在一些特定的场景下表现出色。但随着应用程序变得越来越复杂，对持久化的需求也变得更复杂。我们需要将对象的属性映射到数据库的列上，并且需要自动生成语句和查询，这样我们就能从无休止的问号字符串中解脱出来。此外，我们还需要一些更复杂的特性：

- 延迟加载（Lazy loading）：随着我们的对象关系变得越来越复杂，有时候我们并不希望立即获取完整的对象间关系。，假设我们在查询一组PurchaseOrder对象，而每个对象中都包含一个LineItem对象集合。如果我们只关心PurchaseOrder的属性，那查询出LineItem的数据就毫无意义。而且这可能是开销很大的操作。延迟加载允许我们只在需要的时候获取数据。
- 预先抓取（Eager fetching）：这与延迟加载是相对的。借助于预先抓取，我们可以使用一个查询获取完整的关联对象。如果我们需要PurchaseOrder及其关联的LineItem对象，预先抓取的功能可以在一个操作中将它们全部从数据库中取出来，节省了多次查询的成本。
- 级联（Cascading）：有时，更改数据库中的表会同时修改其他表。回到我们订购单的例子中，当删除Order对象时，我们希望同时在数据库中删除关联的LineItem。

一些可用的框架提供了这样的服务，这些服务的通用名称是对象/关系映射（object-relational mapping，ORM）。在持久层使用ORM工具，可以节省数千行的代码和大量的开发时间。ORM工具能够把你的注意力从容易出错的SQL代码转向如何实现应用程序的真正需求。

Spring对多个持久化框架都提供了支持，包括Hibernate、iBATIS（MyBatis）、Java数据对象（Java Data Objects，JDO）以及Java持久化API（JavaPersistence API，JPA）。与Spring对JDBC的支持那样，Spring对ORM框架的支持提供了与这些框架的集成点以及一些附加的服务：

- 支持集成Spring声明式事务
- 透明的异常处理
- 线程安全的、轻量级的模板类
- DAO支持类
- 资源管理

在本章中，我们将会看到Spring如何与最常用的两种ORM方案集成：Hibernate和JPA。同时还会通过Spring Data JPA了解一下Spring Data项目。借助这种方式，我们不仅可以学习到如何借助Spring Data JPA移除JPA Repository中的样板式代码，还能为下一章的如何将Spring Data用于无模式的存储打下基础。

### 11.1 在Spring中集成Hibernate

Hibernate是在开发者社区很流行的开源持久化框架。它不仅提供了基本的对象关系映射，还提供了ORM工具所应具有的所有复杂功能，比如缓存、延迟加载、预先抓取以及分布式缓存。

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C11_Persisting_data_with_object-relational_mapping-01_hibernate中*。

#### 11.1.1 声明Hibernate的Session工厂

使用Hibernate所需的主要接口是`org.hibernate.Session`。`Session`接口提供了基本的数据访问功能，如保存、更新、删除以及从数据库加载对象的功能。通过这个接口，应用程序的Repository能够满足所有的持久化需求。

获取Hibernate Session对象的标准方式是借助于`HibernateSessionFactory`接口的实现类。除了一些其他的任务，`SessionFactory`主要负责Hibernate Session的打开、关闭以及管理。

在Spring中，我们要通过Spring的某一个Hibernate Session工厂bean来获取`Hibernate SessionFactory`。

这里使用的Spring 4.3.9提供了4个`SessionFactory` bean以供选择：

- org.springframework.orm.hibernate3.annotation.AnnotationSessionFactoryBean（已废弃）
- org.springframework.orm.hibernate3.LocalSessionFactoryBean（已废弃）
- org.springframework.orm.hibernate4.LocalSessionFactoryBean
- org.springframework.orm.hibernate5.LocalSessionFactoryBean（4.2版本开始支持）

从对Hibernate4的支持开始，Spring在`LocalSessionFactoryBean`中合并了`AnnotationSessionFactoryBean`的功能。

这些Session工厂bean都是Spring `FactoryBean`接口的实现，它们会产生一个`HibernateSessionFactory`，它能够装配进任何`SessionFactory`类型的属性中。这样的话，就能在应用的Spring上下文中，与其他的bean一起配置Hibernate Session工厂。（注意，`LocalSessionFactoryBean`并不是`SessionFactory`的实现类，实际上`LocalSessionFactoryBean`类中有一个`SessionFactory`类型的成员变量）

至于选择使用哪一个Session工厂，这取决于使用哪个版本的Hibernate。这里使用5.0.1，所以选择`org.springframework.orm.hibernate5`包中的`LocalSessionFactoryBean`。

```java
@Bean
public LocalSessionFactoryBean sessionFactory(DataSource dataSource) {
    LocalSessionFactoryBean sessionFactory = new LocalSessionFactoryBean();
    // 配置数据源
    sessionFactory.setDataSource(dataSource);
    // 配置映射文件的位置
    sessionFactory.setMappingResources("spittr/domain/Spitter.hbm.xml");
    // 设置Hibernate属性
    Properties props = new Properties();
    props.setProperty("hibernate.dialect", "org.hibernate.dialect.MySQL5Dialect");
    props.setProperty("hibernate.current_session_context_class",
            "org.springframework.orm.hibernate5.SpringSessionContext");
    props.setProperty("hibernate.show_sql", "true");
    props.setProperty("hibernate.format_sql", "true");
    sessionFactory.setHibernateProperties(props);
    return sessionFactory;

}
```

在配置`LocalSessionFactoryBean`时，我们使用了三个属性。属性`dataSource`装配了一个`DataSource` bean的引用。属性`mappingResources`列出了一个或多个的Hibernate映射文件，在这些文件中定义了应用程序的持久化策略。最后，`hibernateProperties`属性配置了Hibernate如何进行操作的细节。在本示例中，我们配置Hibernate使用MySQL数据库并且要按照`MySQL5Dialect`来构建SQL。

如果我们使用注解方式来定义持久化，那么应该这样定义：

```java
@Bean
public LocalSessionFactoryBean sessionFactory(DataSource dataSource) {
    LocalSessionFactoryBean sessionFactory = new LocalSessionFactoryBean();
    // 配置数据源
    sessionFactory.setDataSource(dataSource);
    // 配置要扫描的包
    sessionFactory.setPackagesToScan(new String[] { "spittr.domain" });
    // 设置Hibernate属性
    Properties props = new Properties();
    props.setProperty("hibernate.dialect", "org.hibernate.dialect.MySQL5Dialect");
    props.setProperty("hibernate.current_session_context_class",
            "org.springframework.orm.hibernate5.SpringSessionContext");
    props.setProperty("hibernate.show_sql", "true");
    props.setProperty("hibernate.format_sql", "true");
    sessionFactory.setHibernateProperties(props);
    return sessionFactory;

}
```

这里不再列出Hibernate配置的映射文件，而是使用`packagesToScan`属性来告诉框架应该扫描那个包来查找域类，这些类通过注解的方式（`@Entity`）表明要使用Hibernate进行持久化。

我们还可以使用`annotatedClasses`属性直接指定要持久化的类：

```java
sessionFactory.setAnnotatedClasses(new Class<?>[] { Spitter.class, Spittle.class });
```

如果使用注解而不是XML映射文件的方式配置要持久化的类，`annotatedClasses`属性对于准确指定少量的域类是不错的选择。如果你有很多的域类并且不想将其全部列出，又或者你想自由地添加或移除域类而不想修改Spring配置的话，那使用`packagesToScan`属性是更合适的。

#### 11.1.2 构建不依赖于Spring的Hibernate代码

在Spring和Hibernate的早期岁月中，编写Repository类将会涉及到使用Spring的`HibernateTemplate`。`HibernateTemplate`能够保证每个事务使用同一个Session。但是这种方式的弊端在于我们的Repository实现会直接与Spring耦合。

现在的最佳实践是不再使用`HibernateTemplate`，而是使用上下文Session（Contextual session）。通过这种方式，会直接将Hibernate SessionFactory装配到Repository中，并使用它来获取Session。

```java
package spittr.data;

import java.util.List;

import org.hibernate.Session;
import org.hibernate.SessionFactory;
import org.hibernate.criterion.Restrictions;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.security.crypto.password.Pbkdf2PasswordEncoder;
import org.springframework.stereotype.Repository;

import spittr.domain.Spitter;

@Repository
public class HibernateSpitterRepository implements SpitterRepository {
    
    @Autowired
    private SessionFactory sessionfactory;

    private Session currentSession() {
        return sessionfactory.getCurrentSession();
    }
    
    @Override
    public void addSpitter(Spitter spitter) {
        
        currentSession().save(new Spitter(
                spitter.getUsername(), 
                new Pbkdf2PasswordEncoder("53cr3t").encode(spitter.getPassword()), 
                spitter.getFirstName(), 
                spitter.getLastName())
            );
    }

    @Override
    public Spitter findByUsername(String username) {
        return (Spitter) currentSession().createCriteria(Spitter.class)
            .add(Restrictions.eq("username", username)).list().get(0);
    }

    @Override
    public long count() {
        return findAll().size();
    }

    @SuppressWarnings("unchecked")
    @Override
    public List<Spitter> findAll() {
        return (List<Spitter>) currentSession().createCriteria(Spitter.class).list();
    }

    @Override
    public Spitter findOne(long id) {
        return currentSession().get(Spitter.class, id);
    }

}
```

我们通过`@Autowired`注解让Spring自动装配一个`SessionFactory`，然后在`currentSession()`方法中，使用这个`SessionFactory`来获取当前事务的`Session`。

如果我们使用Hibernate上下文Session而不是Hibernate模板的话，那异常转换会怎么处理呢？

为了给不使用模板的Hibernate Repository添加异常转换功能，我们只需在Spring应用上下文中添加一个`PersistenceExceptionTranslationPostProcessor` bean：

```java
@Bean
public BeanPostProcessor persistenceTranslation() {
    return new PersistenceExceptionTranslationPostProcessor();
}
```

`PersistenceExceptionTranslationPostProcessor`是一个bean后置处理器（bean post-processor），他会在所有拥有`@Repository`注解的类上添加一个顾问（advisor），这样就会捕获任何平台相关的异常并以Spring非检查型数据访问异常的形式重新抛出。

由于我们获取Session时使用的是`SessionFactory`的`getCurrentSession()`方法，这个Session要求必须使用事务，所以我们还需要配置事务管理器：

```java
@Bean
public PlatformTransactionManager txManager(LocalSessionFactoryBean sessionFactory) {
    HibernateTransactionManager manager = new HibernateTransactionManager();
    manager.setSessionFactory(sessionFactory.getObject());
    return manager;
}
```

`HibernateTransactionManager`类的构造器需要我们传入一个`SessionFactory`作为参数，这个参数由`LocalSessionFactoryBean`的`getObject()`方法获取。

配置后还需要开启事务管理，这里需要使用注解：

```java
@Configuration
@EnableTransactionManagement
public class HibernateConfig {
    // bean definitions ...
}
```

最后，我们需要配置事务，例如：

```java
@Override
@Transactional
public void addSpitter(Spitter spitter) {
    
    currentSession().save(new Spitter(
            spitter.getUsername(), 
            new Pbkdf2PasswordEncoder("53cr3t").encode(spitter.getPassword()), 
            spitter.getFirstName(), 
            spitter.getLastName())
        );
}
```

使用`@Transactional`注解会在方法上使用事务（实际上事务通常不配置在这里，而是配置在service上。由于我们的应用只有controller和dao，所以将事务配置在了dao上）。这里的事务属性均使用默认配置。

接下来我们需要定义hibernate的映射文件，Spitter.hbm.xml，这个文件与Spitter类在一个包中：

```xml
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC 
    "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
    "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="spittr.domain">
    <class name="Spitter" table="spitter">
        <id name="id" column="id">
            <generator class="identity" />
        </id>
        <property name="username" column="username" />
        <property name="password" column="password" />
        <property name="firstName" column="firstname" />
        <property name="lastName" column="lastname" />
    </class>
</hibernate-mapping>
```

这里设置主键生成策略为`identity`，即由MySQL维护主键。

最后还有一个问题，由于我们使用了Hibernate validator来校验页面传来的参数是否合法，设置了密码长度在5-25个字符之内：`@Size(min = 5, max = 25, message = "{password.size}")`。同时，在`addSpitter()`方法中，我们对存入数据库的密码进行了加密：

```java
public void addSpitter(Spitter spitter) {
    
    currentSession().save(new Spitter(
            spitter.getUsername(), 
            new Pbkdf2PasswordEncoder("53cr3t").encode(spitter.getPassword()), 
            spitter.getFirstName(), 
            spitter.getLastName())
        );
}
```

这个加密会将密码转为一个长度为80的字符串，这会导致加密后的密码和我们设置的密码长度范围发生冲突（如果配置了Hibernate validator，则Hibernate在将数据存入数据库时会默认的使用这些规则），发生异常。即在将数据存入数据库时应该不使用我们自定义的校验规则（毕竟数据在传入时已经经过校验了），这需要设置Hibernate properties：

```java
props.setProperty("javax.persistence.validation.mode", "none");
```

现在，Hibernate版本的`Repository`已经完成了。我们开发时，没有依赖Spring的特定类（除了`@Repository`注解以外）。这种不使用模板的方式也适用于开发纯粹的基于JPA的Repository，让我们再尝试开发另一个`SpitterRepository`实现类，这次我们使用的是JPA。

### 11.2 Spring与Java持久化API

Java持久化API（Java Persistence API，JPA），是基于POJO的持久化机制，它从Hibernate和Java数据对象（Java Data Object，JDO）上借鉴了很多理念并假如了Java 5注解的特性。

在Spring中使用JPA的第一步是要在Spring应用上下文中将实体管理器工厂（entity manager factory）按照bean的形式来进行配置。

（JPA有多种实现提供者，这里使用hibernate-jpa-2.1-api）

#### 11.2.1 配置实体管理器工厂

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C11_Persisting_data_with_object-relational_mapping-02_jpa中*。

简单来讲，基于JPA的应用程序需要使用EntityManagerFactory的实现类来获取EntityManager实例。JPA定义了两种类型的实体管理器：

- 应用程序管理类型（Application-managed）：当应用程序向实体管理器工厂直接请求实体管理器时，工厂会创建一个实体管理器。在这种模式下，程序要负责打开或关闭实体管理器并在事务中对其进行控制。这种方式的实体管理器适合于不运行在Java EE容器中的独立应用程序。
- 容器管理类型（Container-managed）：实体管理器由Java EE容器创建和管理。应用程序根本不与实体管理器工厂打交道。相反，实体管理器直接通过注入或JNDI来获取。容器负责配置实体管理器工厂。这种类型的实体管理器最适用于Java EE容器，在这种情况下会希望在persistence.xml指定的JPA配置之外保持一些自己对JPA的控制。

以上的两种实体管理器实现了同一个`EntityManager`接口。关键的区别不在于`EntityManager`本身，而是在于`EntityManager`的创建和管理方式。应用程序管理类型的`EntityManager`是由`EntityManagerFactory`创建的，而后者是通过`PersistenceProvider`的`createEntityManagerFactory()`方法得到的。

这对想使用JPA的Spring开发者来说又意味着什么呢？其实这并没太大的关系。不管你希望使用哪种`EntityManagerFactory`，Spring都会负责管理`EntityManager`。如果你使用的是应用程序管理类型的实体管理器，Spring承担了应用程序的角色并以透明的方式处理`EntityManager`。在容器管理的场景下，Spring会担当容器的角色。

这两种实体管理器工厂分别由对应的Spring工厂Bean创建：

- `LocalEntityManagerFactoryBean`生成应用程序管理类型的`EntityManagerFactory`
- `LocalContainerEntityManagerFactoryBean`生成容器管理类型的`EntityManagerFactory`

需要说明的是，选择应用程序管理类型的还是容器管理类型的`EntityManagerFactory`，对于基于Spring的应用程序来讲是完全透明的。当组合使用Spring和JPA时，处理`EntityManagerFactory`的复杂细节被隐藏了起来，数据访问代码只需关注它们的真正目标即可，也就是数据访问。

应用程序管理类型和容器管理类型的实体管理器工厂之间唯一值得关注的区别是在Spring应用上下文中如何进行配置。让我们先看看如何在Spring中配置应用程序管理类型的`LocalEntityManagerFactoryBean`，然后再看看如何配置容器管理类型的`LocalContainerEntityManagerFactoryBean`。

**配置应用程序管理类型的JPA**

对于应用程序管理类型的实体管理器工厂来说，它绝大部分配置信息来源于一个名为persistence.xml的配置文件。这个文件必须位于类路径下的META-INF目录下。

persistence.xml的作用在于定义一个或多个持久化单元。持久化单元是同一个数据源下的一个或多个持久化类。简单来讲，persistence.xml列出了一个或多个的持久化类以及一些其他的配置如数据源和基于XML的配置文件。如下是一个典型的persistence.xml文件，它是用于Spittr应用程序的：

```xml
<persistence xmlns="http://java.sun.com/xml/ns/persistence" version="1.0">
    <persistence-unit name="spitterPU">
        <class>spittr.domain.Spitter</class>
        <class>spittr.domain.Spittle</class>
        <properties>
            <property name="toplink.jdbc.driver" value="com.mysql.jdbc.Driver" />
            <property name="toplink.jdbc.url" value="jdbc:mysql://localhost:3306/test" />
            <property name="toplink.jdbc.user" value="root" />
            <property name="toplink.jdbc.password" value="mysql" />
        </properties>
    </persistence-unit>
</persistence>
```

因为在persistence.xml文件中包含了大量的配置信息，所以在Spring中需要配置的就很少了。可以通过以下的`@Bean`注解方法在Spring中声明`LocalEntityManagerFactoryBean`：

```java
@Bean
public LocalEntityManagerFactoryBean entityManagerFactoryBean() {
    LocalEntityManagerFactoryBean lemfb = new LocalEntityManagerFactoryBean();
    lemfb.setPersistenceUnitName("spitterPU");
    return lemfb;
}
```

赋给`persistenceUnitName`属性的值就是persistence.xml中持久化单元的名称。

创建应用程序管理类型的`EntityManagerFactory`都是在persistence.xml中进行的，而这正是应用程序管理的本意。在应用程序管理的场景下（不考虑Spring时），完全由应用程序本身来负责获取`EntityManagerFactory`，这是通过JPA实现的`PersistenceProvider`做到的。如果每次请求`EntityManagerFactory`时都需要定义持久化单元，那代码将会迅速膨胀。通过将其配置在persistence.xml中，JPA就能够在这个特定的位置查找持久化单元定义了。

但借助于Spring对JPA的支持，我们不再需要直接处理`PersistenceProvider`了。因此，再将配置信息放在persistence.xml中就显得不那么明智了。

**使用容器管理类型的JPA**

容器管理的JPA采取了一个不同的方式。当运行在容器中时，可以使用容器（例如Spring）提供的信息来生成`EntityManagerFactory`。

现在我们可以将数据源信息配置在Spring应用上下文中，而不是在persistence.xml中了。例如使用`@Bean`注解方法声明了在Spring中如何使用`LocalContainerEntityManagerFactoryBean`来配置容器管理类型的JPA：

```java
@Bean
public LocalContainerEntityManagerFactoryBean entityManagerFactory(DataSource dataSource, JpaVendorAdapter jpaVendorAdapter) {
    
    LocalContainerEntityManagerFactoryBean manager = new LocalContainerEntityManagerFactoryBean();
    
    manager.setDataSource(dataSource);
    
    manager.setJpaVendorAdapter(jpaVendorAdapter);
    
    manager.setPackagesToScan("spittr.domain");
    
    // 由于我们要加密密码，所以在将数据储存到数据库时关闭了validation
    manager.setValidationMode(ValidationMode.NONE);

    return manager;
    
}
```

这里，我们使用了Spring配置的数据源来设置`dataSource`属性。任何`javax.sql.DataSource`的实现都是可以的。尽管数据源还可以在persistence.xml中进行配置，但是这个属性指定的数据源具有更高的优先级。

`jpaVendorAdapter`属性用于指明所使用的是哪一个厂商的JPA实现。Spring提供了多个JPA厂商适配器：

- EclipseLinkJpaVendorAdapter
- HibernateJpaVendorAdapter
- OpenJpaVendorAdaptor

这里我们使用Hibernate的JPA实现（需要添加依赖hibernate-entitymanager）：

```java
@Bean
public JpaVendorAdapter jpaVendorAdapter() {
    HibernateJpaVendorAdapter adapter = new HibernateJpaVendorAdapter();
    adapter.setDatabase(Database.MYSQL);
    adapter.setShowSql(true);
    adapter.setDatabasePlatform("org.hibernate.dialect.MySQL5Dialect");
    adapter.setGenerateDdl(false);
    return adapter;
}
```

Hibernate的JPA适配器支持多种数据库，可以通过其`database`属性配置使用哪个数据库：

```java
public enum Database {
    DEFAULT, DB2, DERBY, H2, HSQL, INFORMIX, MYSQL, ORACLE, POSTGRESQL,  SQL_SERVER, SYBASE
}
```

最后我们通过属性`packagesToScan`来指定我们要扫描的实体类所在包。在上述配置中`LocalContainerEntityManagerFactoryBean`会扫描`spittr.domain`包，查找带有`@Entity`注解的类。

例如：

```java
@Entity
public class Spitter {

    @Id
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username")
    @NotBlank(message = "{username.blank}")
    @Size(min = 5, max = 16, message = "{username.size}")
    private String username;

    @Column(name = "password")
    @NotBlank(message = "{password.blank}")
    @Size(min = 5, max = 25, message = "{password.size}")
    private String password;

    @Column(name = "firstname")
    @NotBlank(message = "{firstName.blank}")
    @Size(min = 2, max = 30, message = "{firstName.size}")
    private String firstName;

    @Column(name = "lastname")
    @NotBlank(message = "{lastName.blank}")
    @Size(min = 2, max = 30, message = "{lastName.size}")
    private String lastName;

    // ...

}
```

最后，我们还要配置事务管理并开启事务：

```java
@Configuration
@EnableTransactionManagement
public static class TransactionConfig implements TransactionManagementConfigurer {

    @Autowired
    private EntityManagerFactory emf;

    @Override
    public PlatformTransactionManager annotationDrivenTransactionManager() {
        JpaTransactionManager transactionManager = new JpaTransactionManager();
        transactionManager.setEntityManagerFactory(emf);
        return transactionManager;
    }
}
```

这个静态内部类定义在JpaConfig类中。

**从JNDI获取实体管理器工厂**

如果将Spring应用程序部署在应用服务器中，`EntityManagerFactory`可能已经创建好了并且位于JNDI中等待查询使用。在这种情况下可以使用Spring `jee`命名空间下的`<jee:jndi-lookup>`元素来获取对`EntityManagerFactory`的引用。

```xml
<jee:jndi-lookup id="lemfb" jndi-name="persistence/spitterPU" />
```

同样地，也可以使用JavaConfig的方式：

```java
@Bean
public JndiObjectFactoryBean entityManagerFactory() {
    JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
    jndiObjectFB.setJndiName("jdbc/SpitterDS");
    return jndiObjectFB;
}
```

尽管这种方法没有返回`EntityManagerFactory`，但是它的结果就是一个`EntityManagerFactory` bean。这是因为它所返回的`JndiObjectFactoryBean`是`FactoryBean`接口的实现，它能够创建`EntityManagerFactory`。

#### 11.2.2 编写基于JPA的Repository

正如Spring对其他持久化方案的集成一样，Spring对JPA集成也提供了`JpaTemplate`模板以及对应。的支持类`JpaDaoSupport`。但是，为了实现更纯粹的JPA方式，基于模板的JPA已经被弃用了。

鉴于纯粹的JPA方式远胜于基于模板的JPA，所以在本节中我们将会重点关注如何构建不依赖Spring的JPA Repository。

```java
@Repository
@Transactional
public class JpaSpitterRepository implements SpitterRepository {

    @PersistenceUnit
    private EntityManagerFactory emf;
    
    @Override
    public long count() {
        return findAll().size();
    }

    @Override
    public void addSpitter(Spitter spitter) {

        spitter.setPassword(new Pbkdf2PasswordEncoder("53cr3t").encode(spitter.getPassword()));

        emf.createEntityManager().persist(spitter);
    }

    @Override
    public Spitter findByUsername(String username) {
        return (Spitter) emf.createEntityManager()
                .createQuery("select s from Spitter s where s.username=?")
                .setParameter(1, username)
                .getSingleResult();
    }

    @SuppressWarnings("unchecked")
    @Override
    public List<Spitter> findAll() {
        return (List<Spitter>) emf.createEntityManager().createQuery("SELECT s FROM spitter").getResultList();
    }
    
    @Override
    public Spitter findOne(long id) {
        return emf.createEntityManager().find(Spitter.class, id);
    }

}
```

（这里`createQuery()`方法中的语句是JPQL语句）


这里使用了`@PersistenceUnit`注解，因此Spring会将`EntityManagerFactory`注入进来。有了`EntityManagerFactory`之后，`JpaSpitterRepository`的方法就能使用它来创建`EntityManager`了，然后`EntityManager`可以针对数据库执行操作。

在`JpaSpitterRepository`中，唯一的问题在于每个方法都会调用`createEntityManager()`。除了引入易出错的重复代码以外，这还意味着每次调用Repository的方法时，都会创建一个新的`EntityManager`。这种复杂性源于事务。如果我们能够预先准备好`EntityManager`，那会不会更加方便呢？

这里的问题在于`EntityManager`并不是线程安全的，一般来讲并不适合注入到像Repository这样共享的单例bean中。但是，这并不意味着我们没有办法要求注入`EntityManager`。

我们需要使用`@PersistenceContext`注解：

```java
@Repository
@Transactional
public class JpaSpitterRepository implements SpitterRepository {

    @PersistenceContext
    private EntityManager manager;
    
    @Override
    public long count() {
        return findAll().size();
    }

    @Override
    public void addSpitter(Spitter spitter) {

        spitter.setPassword(new Pbkdf2PasswordEncoder("53cr3t").encode(spitter.getPassword()));

        manager.persist(spitter);
    }

    @Override
    public Spitter findByUsername(String username) {
        
        return (Spitter) manager
                .createQuery("select s from Spitter s where s.username=:username")
                .setParameter("username", username)
                .getSingleResult();
    }

    @SuppressWarnings("unchecked")
    @Override
    public List<Spitter> findAll() {
        return (List<Spitter>) manager.createQuery("SELECT s FROM spitter").getResultList();
    }
    
    @Override
    public Spitter findOne(long id) {
        return manager.find(Spitter.class, id);
    }
}
```

在这个新版本的`JpaSpitterRepository`中，直接为其设置了`EntityManager`，这样的话，在每个方法中就没有必要再通过`EntityManagerFactory`创建`EntityManager`了。尽管这种方式非常便利，但是你可能会担心注入的`EntityManager`会有线程安全性的问题。

这里的真相是`@PersistenceContext`并不会真正注入`EntityManager`——至少，精确来讲不是这样的。它没有将真正的`EntityManager`设置给Repository，而是给了它一个`EntityManager`的代理。真正的`EntityManager`是与当前事务相关联的那一个，如果不存在这样的`EntityManager`的话，就会创建一个新的。这样的话，我们就能始终以线程安全的方式使用实体管理器。

另外，还需要了解`@PersistenceUnit`和`@PersistenceContext`并不是Spring的注解，它们是由JPA规范提供的。为了让Spring理解这些注解，并注入`EntityManagerFactory`或`EntityManager`，我们必须要配置Spring的`PersistenceAnnotationBeanPostProcessor`。

这里可以使用XML配置的`<context:annotation-config>`或`<context:component-scan>`（使用后者可以省略前者），这样这些配置元素会自动注册`PersistenceAnnotationBeanPostProcessor` bean。

我们也可以显式的注册它：

```java
@Bean
public PersistenceAnnotationBeanPostProcessor processor() {
    return new PersistenceAnnotationBeanPostProcessor();
}
```

最后，与Hibernate的配置相同，我么需要配置异常处理器：

```java
@Bean
public BeanPostProcessor persistenceTranslation() {
    return new PersistenceExceptionTranslationPostProcessor();
}
```

事实上，不管对于JPA还是Hibernate，异常转换都不是强制要求的。如果你希望在Repository中抛出特定的JPA或Hibernate异常，只需将`PersistenceExceptionTranslationPostProcessor`省略掉即可，这样原来的异常就会正常地处理。但是，如果使用了Spring的异常转换，你会将所有的数据访问异常置于Spring的体系之下，这样以后切换持久化机制的话会更容易。

### 11.3 借助Spring Data实现自动化的JPA Repository

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C11_Persisting_data_with_object-relational_mapping-03_springdata中*。

（这里我们使用的spring-data-jpa版本为1.11.14，由于我们使用的Spring框架版本不是5，我们配置的是Thymeleaf-spring4，所以不能使用spring-data-jpa 2。如果使用，在访问引用首页是就会报错：`java.lang.ClassNotFoundException: org.thymeleaf.spring5.expression.IThymeleafEvaluationContext`。）

尽管上一节的JPA实现很简单，但它们依然会直接与`EntityManager`交互来查询数据库。并且，仔细看一下的话，这些代码多少还是样板式的。例如`addSpitter()`方法：

```java
public void addSpitter(Spitter spitter) {
    entityManager.persisit(spitter);
}
```

在任何具有一定规模的应用中，你可能会以几乎完全相同的方式多次编写这种方法。

为什么我们需要一遍遍地编写相同的持久化方法呢？Spring Data JPA能够终结这种样板式的愚蠢行为。我们不再需要一遍遍地编写相同的Repository实现，Spring Data能够让我们只编写Repository接口就可以了。根本就不再需要实现类了。

首先重新定义`SpitterRepository`接口：

```java
public interface SpitterRepository extends JpaRepository<Spitter, Long> {
    
}
```

此时，`SpitterRepository`看上去并没有什么作用。但是，它的功能远超出了表面上所看到的那样。

编写Spring Data JPA Repository的关键在于要从一组接口中挑选一个进行扩展。这里，`SpitterRepository`扩展了Spring Data JPA的`JpaRepository`。通过这种方式，`JpaRepository`进行了参数化，所以它知道这是一个用来持久化`Spitter`对象的Repository，并且`Spitter`对象的ID类型为`Long`。如果此时有一个`SpitterRepository`的实现类，那么这个实现类需要实现24个相关的数据操作方法。

此时，我们可能会来编写这个实现类了。但是，事实上，我们根本不需要编写任何实现。相反，我们让Spring Data来做这件事，而我们要做的就是对它提出要求。

为了要求Spring Data创建`SpitterRepository`实现，我们需要在Spring配置中添加一个元素：

```xml
<jpa:repositories base-package="spittr.data" />
```

`<jpa:repositories>`元素与`<context:component-scan>`一样，需要指定一个要进行扫描的包。`<jpa:repositories>`会扫描指定的基础包以及子包来查找扩展自Spring Data Jpa Repository接口的所有接口。如果发现了这个接口，他会在运行时自动生成这个接口的实现。

当然，我们可以使用JavaConfig的方式来代替XML配置，这需要使用注解`@EnableJpaRepositories`：

```java
@Configuration
@EnableJpaRepositories("spittr.data")
public class SpringDataJpaConfig {
    // ...
}
```

让我们回到`SpitterRepository`接口，它扩展自`JpaRepository`，而`JpaRepository`又扩展自`Repository`标记接口（虽然是间接的，`JpaRepository`扩展了`PagingAndSortingRepository`和`QueryByExampleExecutor`两个接口。其中`PagingAndSortingRepository`接口扩展了`CrudRepository`接口，而`CrudRepository`接口扩展自`Repository`接口）。因此，`SpitterRepository`就传递性地扩展了`Repository`接口。当Spring Data找到它后，就会创建其的实现类，其中包含所有应该实现的24个方法。

（这里原书中说是18个方法，应该是没有算上`QueryByExampleExecutor`接口中的6个方法。因为这个接口是spring-data-commons 1.12中才出现的。由于我们引入的spring-data-jpa的版本为1.11.14，而这个jar包使用的spring-data-commons的版本为1.13.14，所以我们的`SpitterRepository`会获得24个方法）

很重要的一点在于Repository的实现类是在应用启动的时候生成的，也就是Spring的应用上下文创建的时候。它并不是在构建时通过代码生成技术产生的。

Spring Data JPA很棒的一点在于它能为Spitter对象提供24个便利的方法来进行通用的JPA操作，而无需你编写任何持久化代码。但是，如果你的需求超过了它所提供的这24个方法的话，该怎么办呢？幸好，Spring Data JPA提供了几种方式来为Repository添加自定义的方法。让我们看一下如何为Spring Data JPA编写自定义的查询方法。

#### 11.3.1 定义查询方法

现在，`SpitterRepository`需要完成的一项功能是根据给定的`username`查找`Spitter`对象。比如，我们将`SpitterRepository`接口修改为如下所示的样子：

```java
public interface SpitterRepository extends JpaRepository<Spitter, Long> {
    Spitter findByUsername(String username);
}
```

这个新的`findByUserName()`非常简单，但是足以满足我们的需求。现在，该如何让Spring Data JPA提供这个方法的实现呢？

**实际上，我们并不需要实现`findByUsername()`。方法签名已经告诉Spring Data JPA足够的信息来创建这个方法的实现了。**

当创建Repository实现的时候，Spring Data会检查Repository接口的所有方法，解析方法的名称，并基于被持久化的对象来试图推测方法的目的。

本质上，Spring Data定义了一组小型的领域特定语言（Domain Specific Language ，DSL），在这里，持久化的细节都是通过Repository方法的签名来描述的。

Spring Data能够知道这个方法是要查找`Spitter`的，因为我们使用`Spitter`对`JpaRepository`进行了参数化（泛型`<Spitter, Long>`）。方法名`findByUsername`确定该方法需要根据`username`属性相匹配来查找`Spitter`，而`username`是作为参数传递到方法中来的。另外，因为在方法签名中定义了该方法要返回一个`Spitter`对象，而不是一个集合，因此它只会查找一个`username`属性匹配的`Spitter`。

`findByUsername()`方法非常简单，但是Spring Data也能处理更加有意思的方法名称。Repository方法是由一个动词、一个可选的主题（Subject）、关键词*By*以及一个断言所组成。在`findByUsername()`这个样例中，动词是*find*，断言是*Username*，主题并没有指定，暗含的主题是*Spitter*。

作为示例，我们使用方法`readSpitterByFirstnameOrLastname()`来看一下方法中的各个部分是如何映射的。

<center>
    ![图10.3-Repository方法的命名遵循的模式](images\图10.3-Repository方法的命名遵循的模式.PNG)
    **Repository方法的命名遵循一种模式，有助于Spring Data生成针对数据库的查询**
</center>

这里的动词是*read*，与之前的*find*类似。Spring Data允许方法名中使用四种动词：*get*、*read*、*find*和*count*。其中*get*、*read*和*find*是同义的，这三个动词对应的Repository方法都会查询数据并返回对象。而动词*count*则返回匹配对象的数量而不是对象本身。

Repository方法的主题是可选的。它的主要目的是让我们在命名方法的时候有更多的灵活性。也就是说，在大多数场景下，主题会被省略，同时`readSpittersByFirstnameOrLastname()`与`readPuppiesByFirstnameOrLastname()`并没有什么差别，甚至与`readThoseThingsWeWantByFirstnameOrLastname()`也没什么区别。因为要查询的对象类型是通过`JpaRepository`接口的泛型决定的。

在省略主题的时候，有一种例外情况。如果主题的名称以*Distinct*开头的话，那么在生成查询的时候会确保所返回结果集中不包含重复记录。

断言是方法名称中最为有意思的部分，它指定了限制结果集的属性。在`readByFirstnameOrLastname()`这个样例中，会通过`firstname`属性或`lastname`属性的值来限制结果。

在断言中，会有一个或多个限制结果的条件。每个条件必须引用一个属性，并且还可以指定一种比较操作。如果省略比较操作符的话，那么这暗指是一种相等比较操作。不过，我们也可以选择其他的比较操作，包括如下的种类：

- IsAfter、After、IsGreaterThan、GreaterThan
- IsGreaterThanEqual、GreaterThanEqual
- IsBefore、Before、IsLessThan、LessThan
- IsLessThanEqual、LessThanEqual
- IsBetween、Between
- IsNull、Null
- IsNotNull、NotNull
- IsIn、In
- IsNotIn、NotIn
- IsStartingWith、StartingWith、StartsWith
- IsEndingWith、EndingWith、EndsWith
- IsContaining、Containing、Contains
- IsLike、Like
- IsNotLike、NotLike
- IsTrue、True
- IsFalse、False
- Is、Equals
- IsNot、Not

要对比的属性值就是方法的参数：

```java
List<Spitter> readByFirstnameOrLastname(String first, String last);
```

要处理`String`类型的属性时，条件中可能还可以包含*IgnoringCase*或*IgnoresCase*（二者同义），这样在执行对比的时候就会不再考虑字符是大写还是小写。例如，要在`firstname`和`lastname`属性上忽略大小写：

```java
List<Spitter> readByFirstnameIgnoringCaseOrLastnameIgnoresCase(String first, String last);
```

作为*IgnoringCase*/*IgnoresCase*的替代方案，我们还可以在所有条件的后面添加*AllIgnoringCase*或*AllIgnoresCase*，这样它就会忽略所有条件的大小写：

```java
List<Spitter> readByFirstnameOrLastnameAllIgnoresCase(String first, String last);
```

注意，参数的名称是无关紧要的，但是它们的顺序必须要与方法名称中的操作符相匹配。

最后，我们还可以在方法名称的结尾处添加*OrderBy*，实现结果集排序。例如，我们可以按照`lastname`属性升序排列结果集：

```java
List<Spitter> readByFirstnameOrLastnameOrderByLastnameAsc(String first, String last);
```

如果要根据多个属性排序的话，只需将其依序添加到*OrderBy*中即可。例如，下面的样例中，首先会根据`lastname`升序排列，然后根据`firstname`属性降序排列：

```java
List<Spitter> readByFirstnameOrLastnameOrderByLastnameAscFirstnameDesc(String first, String last);
```

可以看到，条件部分是通过*And*或者*Or*进行分割的。

我们只是初步体验了所能声明的方法种类，Spring Data JPA会为我们实现这些方法。现在，我们只需知道通过使用属性名和关键字构建Repository方法签名，就能让Spring Data JPA生成方法实现，完成几乎所有能够想象到的查询。

#### 11.3.2 声明自定义查询

假设我们想要创建一个Repository方法，用来查找E-mail地址是Gmail邮箱的Spitter。有一种方式就是定义一个`findByEmailLike()`方法，然后每次想查找Gmail用户的时候就将“%gmail.com”传递进来。不过，更好的方案是定义一个更加便利的`findAllGmailSpitters()`方法，这样的话，就不用将Email地址的一部分传递进来了：

```java
List<Spitter> findAllGmailSpitters();
```

**不过，这个方法并不符合Spring Data的方法命名约定。**当Spring Data试图生成这个方法的实现时，无法将方法名的内容与Spitter元模型进行匹配，因此会抛出异常。

如果所需的数据无法通过方法名称进行恰当地描述，那么我们可以使用`@Query`注解，为Spring Data提供要执行的查询。对于`findAllGmailSpitters()`方法，我们可以按照如下的方式来使用`@Query`注解：

```java
@Query("select s from spitter s where s.email like '%gmail.com'")
List<Spitter> findAllGmailSpitters();
```

我们依然不需要编写`findAllGmailSpitters()`方法的实现，只需提供查询即可，让Spring Data JPA知道如何实现这个方法。

可以看到，当使用方法命名约定很难表达预期的查询时，`@Query`注解能够发挥作用。如果按照命名约定，方法的名称特别长的时候，也可以使用这个注解。例如，考虑如下的查询方法：

```java
List<Order> findByCustomerAddressZipCodeOrCustomerNameAndCustomerAddressState();
```

在现实世界中，确实存在这样的需求，使用Repository方法所执行的查询要使用一个很长的方法名。在这种情况下，我们最好使用一个较短的方法名，并使用`@Query`来指定该方法要如何查询数据库。

对于Spring Data JPA的接口来说，`@Query`是一种添加自定义查询的便利方式。但是，它仅限于单个JPA查询。如果我们需要更为复杂的功能，无法在一个简单的查询中处理的话，该怎么办呢？

#### 11.3.3 混合自定义的功能

有些时候，我们需要Repository所提供的功能是无法用Spring Data的方法命名约定来描述的，甚至无法用`@Query`注解设置查询来实现。尽管Spring Data JPA非常棒，但是它依然有其局限性，可能需要我们按照传统的方式来编写Repository方法：也就是直接使用`EntityManager`。当遇到这种情况的时候，我们是不是要放弃Spring Data JPA，重新来编写Repository呢？

简单来说，是这样的。如果你需要做的事情无法通过Spring Data JPA来实现，那就必须要在一个比Spring Data JPA更低的层级上使用JPA。好消息是我们没有必要完全放弃Spring Data JPA。我们只需在必须使用较低层级JPA的方法上，才使用这种传统的方式即可，而对于Spring Data JPA知道该如何处理的功能，我们依然可以通过它来实现。

当Spring Data JPA为Repository接口生成实现的时候，它还会查找名字与接口相同，并且添加了Impl后缀的一个类。如果这个类存在的话，Spring Data JPA将会把它的方法与Spring Data JPA所生成的方法合并在一起。对于`SpitterRepository`接口而言，要查找的类名为`SpitterRepositoryImpl`。

为了阐述该功能，假设我们需要在`SpitterRepository`中添加一个方法，发表Spittle数量在10,000及以上的Spitter将会更新为Elite状态。使用Spring Data JPA的方法命名约定或使用`@Query`均没有办法声明这样的方法。最为可行的方案是使用如下的`eliteSweep()`方法。

```java
public class SpitterRepositoryImpl implements SpitterSweeper {
    @PersistenceContext
    private EntityManager manager;

    @Override
    public int eliteSweep() {
        String update = 
            "update Spitter spitter set spitter.status = 'Elite' " + 
            "where spitter.status = 'Newbie' and spitter id in " +
            "(select s from spitter s where " +
            "(select count(spittles) from s.spittles spittles) > 10000)"

        return manager.createQuery(update).executeUpdate();
    }
}
```

`SpitterRepositoryImpl`没有什么特殊之处，它使用被注入的`EntityManager`来完成预期的任务。

**需要注意的是，`SpitterRepositoryImpl`并没有实现`SpitterRepository`接口。**Spring Data JPA负责实现这个接口。`SpitterRepositoryImpl`（将它与Spring Data的Repository关联起来的是它的名字）实现了`SpitterSweeper`接口，它如下所示：

```java
public interface SpitterSweeper {
    int eliteSweep();
}
```

我们还需要确保`eliteSweep()`方法会被声明在`SpitterRepository`接口中。要实现这一点，避免代码重复的简单方式就是修改`SpitterRepository`，让它扩展`SpitterSweeper`：

```java
public interface SpitterRepostory extends JpaRepository<Spitter, Long>,
                                             SpitterSweeper {

}
```

如前所述，Spring Data JPA将实现类与接口关联起来是基于接口的名称。但是，Impl后缀只是默认的做法，如果你想使用其他后缀的话，只需在配置`@EnableJpaRepositories`的时候，设置`repositoryImplementationPostfix`属性即可：

```java
@EnableJpaRepository(
        basePackages = "spittr.data",
        repositoryImplementationPostfix = "Helper"
    )
```

或在XML中定义：

```xml
<jpa:repositories base-package="spittr.data" repository-impl-postfix="Helper" />
```

我们将后缀设置成了Helper，Spring Data JPA将会查找名为`SpitterRepositoryHelper`的类，用它来匹配`SpitterRepository`接口。

### 11.4 小结

>
对于很多应用来讲，关系型数据库是主流的数据存储形式，并且这种情况已经持续了很多年。使用JDBC并且将对象映射为数据库表是很烦琐乏味的事情，像Hibernate和JPA这样的ORM方案能够让我们以更加声明式的模型实现数据持久化。尽管Spring没有为ORM提供直接的支持，但是它能够与多种流行的ORM方案集成，包括Hibernate与Java持久化API。
>
在本章中，我们看到了如何在Spring应用中使用Hibernate的上下文Session，这样我们的Repository就能包含很少甚至不包含Spring相关的代码。与之类似，我们还看到了如何将EntityManagerFactory或EntityManager注入到Repository实现中，从而实现不依赖于Spring的JPA Repository。
>
我们稍后初步了解了Spring Data，在这个过程中，只需声明JPARepository接口即可，让Spring Data JPA在运行时自动生成这些接口的实现。当我们需要的Repository方法超出了Spring Data JPA所提供的功能时，可以借助@Query注解以及编写自定义的Repository方法来实现。
>
但是，对于Spring Data的整体功能来说，我们只是接触到了皮毛。在下一章中，我们将会更加深入地学习Spring Data的方法命名DSL，以及Spring Data如何为关系型数据库以外的领域带来帮助。也就是说：我们将会看到Spring Data如何支持新兴的NoSQL数据库，这些数据库在最近几年变得越来越流行。

## 第十二章 使用NoSQL数据库

本章内容：

- 为MongoDB和Neo4j编写Repository
- 为多种数据存储形式持久化数据
- 组合使用Spring和Redis

在数据库领域，多年来，我们一直被告知，我们可以使用任意想要的数据库，只要它是关系型数据库就行。关系型数据库已经垄断应用开发领域好多年了。

随着一些竞争者进入数据库领域，关系型数据库的垄断地位开始被弱化。所谓的“NoSQL”数据库开始侵入生产型的应用之中，我们也认识到并没有一种全能型的数据库。现在有了更多的可选方案，所以能够为要解决的问题选择最佳的数据库。

Spring Data还提供了对多种NoSQL数据库的支持，包括MongoDB、Neo4j和Redis。它不仅支持自动化的Repository，还支持基于模板的数据访问和映射注解。

### 12.1 使用MongoDB持久化文档数据

这里我们使用4.0.2版本的MongoDB和2.0.9版本的Spring Data MongoDB以及2.0.9版本的spring-data-jpa。同时这个版本的Spring Data MongoDB要求我们的Spring框架的版本为5.0.8。

有一些数据的最佳表现形式是文档（document）。也就是说，不要把这些数据分散到多个表、结点或实体中，将这些信息收集到一个非规范化（也就是文档）的结构中会更有意义。尽管两个或两个以上的文档有可能会彼此产生关联，但是通常来讲，文档是独立的实体。能够按照这种方式优化并处理文档的数据库，我们称之为文档数据库。

例如，假设我们要编写一个应用程序来获取大学生的成绩单，可能需要根据学生的名字来查询其成绩单，或者根据一些通用的属性来查询成绩单。但是，每个学生是相互独立的，任意的两个成绩单之间没有必要相互关联。尽管我们能够使用关系型数据库模式来获取成绩单数据（也许你曾经这样做过），但文档型数据库可能才是更好的方案。

**文档数据库不适用于什么场景**

了解文档型数据库能够用于什么场景是很重要的。但是，知道文档型数据库在什么情况下不适用同样也是很重要的。文档数据库不是通用的数据库，它们所擅长解决的是一个很小的问题集。

有些数据具有明显的关联关系，文档型数据库并没有针对存储这样的数据进行优化。例如，社交网络表现了应用中不同的用户之间是如何建立关联的，这种情况就不适合放到文档型数据库中。在文档数据库中存储具有丰富关联关系的数据也并非完全不可能，但这样做的话，你通常会发现遇到的挑战要多于所带来的收益。

MongoDB是最为流行的开源文档数据库之一。Spring Data MongoDB提供了三种方式在Spring应用中使用MongoDB：

- 通过注解实现对象-文档映射
- 使用`MongoTemplate`实现基于模板的数据库访问
- 自动化的运行时Repository生成功能

我们已经看到Spring Data JPA如何为基于JPA的数据访问实现自动化Repository生成功能。Spring Data MongoDB为基于MongoDB的数据访问提供了相同的功能。

不过，与Spring Data JPA不同的是，Spring Data MongoDB提供了将Java对象映射为文档的功能。（Spring Data JPA没有必要为JPA提供这样的注解，因为JPA规范本身就提供了对象-关系映射注解）。除此之外，Spring Data MongoDB为通用的文档操作任务提供了基于模板的数据访问方式。

#### 12.1.1 启用MongoDB

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C12_Working_with_NoSQL_databases-01_mongodb中*。

为了有效地使用Spring Data MongoDB，我们需要在Spring配置中添加几个必要的bean。首先，我们需要配置MongoClient，以便于访问MongoDB数据库。同时，我们还需要有一个`MongoTemplate` bean，实现基于模板的数据库访问。此外，不是必须，但是强烈推荐启用Spring Data MongoDB的自动化Repository生成功能。

```java
@Configuration
@EnableMongoRepositories("orders.db") // 启用MongoDB的Repository功能
public class MongoConfig {
    @Bean
    public MongoClientFactoryBean mongo() {
        MongoClientFactoryBean mongo = new MongoClientFactoryBean();
        mongo.setHost("localhost");
        return mongo;
    }
    
    @Bean
    public MongoOperations mongoTemplate(MongoClientFactoryBean mongo) throws Exception {
        return new MongoTemplate(mongo.getObject(), "OrdersDB");
    }
} 
```

`@EnableMongoRepositories`为MongoDB实现了自动化JPA Repository生成功能。

（原书使用的spring-data-mongodb的版本是1.5.2并使用`MongoFactoryBean`。在我们使用的2.0.9中，这个类已经被标记为`@Deprecated`，并推荐使用`MongoClientFactoryBean`）

第一个`@Bean`方法使用`MongoClientFactoryBean`声明了一个`Mongo`实例。这个bean将Spring Data MongoDB与数据库本身连接了起来。另外一个`@Bean`方法声明了`MongoTemplate` bean，在它构造时，使用了其他`@Bean`方法所创建的`Mongo`实例的引用以及数据库的名称。

除了上述的配置方法，我们还可以让配置类继承`AbstractMongoConfiguration`并重写`getDatabaseName()`和`mongoClient()`方法：

```java
@Configuration
@EnableMongoRepositories("orders.db") // 启用MongoDB的Repository功能
public class MongoConfig extends AbstractMongoConfiguration {

    // 指定数据库的名字
    @Override
    public MongoClient mongoClient() {
        return new MongoClient();
    }

    // 创建Mongo客户端
    @Override
    protected String getDatabaseName() {
        return "OrdersDB";
    }
} 
```

上述配置与第一个配置在功能上是相同的，但最显著的区别是这个配置中没有直接声明`MongoTemplate` bean，当然它还是会被隐式地创建。

如果我们的MongoDB运行在本地上，那么上述配置就可以了。如果MongoDB服务器运行在其他的的机器上，那么可以在创建`MongoClient`的时候进行指定：

```java
@Override
public MongoClient mongoClient() {
    return new MongoClient("mongodbserver");
}
```

另外MongoDB服务器有可能监听的端口并不是默认的27017，如果是这样的话，我们还需要在创建`MongoClient`的时候指定端口：

```java
@Override
public MongoClient mongoClient() {
    return new MongoClient("mongodbserver", 37017);
}
```

如果MongoDB服务器运行在生产配置上，我认为你可能还启用了认证功能。在这种情况下，为了访问数据库，我们还需要提供应用的凭证。访问需要认证的MongoDB服务器稍微有些复杂，如下所示：

```java
@Autowired
private Environment env;

@Override
public MongoClient mongoClient() {
    MongoCredential credential = MongoCredential.createCredential(
        env.getProperty("mongo.username"), 
        "OrdersDB", 
        env.getProperty("mongo.password").toCharArray());

    return new MongoClient(new ServerAddress("localhost", 37017), Arrays.asList(credential));

}
```

（上述代码中`MongoClient(ServerAddress, List)`这个构造器已经被标注为`@Deprecated`）

Spring Data MongoDB同样支持通过XML来进行配置，这需要使用`mongo`命名空间：

```xml
    <!-- 启用Repository生成功能 -->
    <mongo:repositories base-package="orders.db" />
    
    <!-- 声明MongoClient -->
    <mongo:mongo-client id="mongo-client"/>
    
    <!-- 创建MongoTemplate bean -->
    <bean id="mongoTemplate" class="org.springframework.data.mongodb.core.MongoTemplate">
        <constructor-arg ref="mongo-client" />
        <constructor-arg value="OrdersDB" />
    </bean>
```

现在Spring Data MongoDB已经配置完成了，我们很快就可以使用它来保存和查询文档了。但首先，需要使用Spring Data MongoDB的对象-文档映射注解为Java领域对象建立到持久化文档的映射关系。

#### 12.1.2 为模型添加注解，实现MongoDB持久化

当使用JPA的时候，我们需要将Java实体类映射到关系型表和列上。JPA规范提供了一些支持对象-关系映射的注解，而有一些JPA实现，如Hibernate，也添加了自己的映射注解。

但是，MongoDB并没有提供对象-文档映射的注解。Spring DataMongoDB填补了这一空白，提供了一些将Java类型映射为MongoDB文档的注解：

注解 | 描述
----- | -----
`@Document` | 标示映射到MongoDB文档上的领域对象
`@Id` | 标示某个域为ID域
`@DbRef` | 标示某个域要引用其他的文档，这个文档有可能位于另外一个数据库中
`@Field` | 为文档域指定自定义的元数据
`@Version` | 标示某个属性用作版本域

`@Document`和`@Id`注解类似于JPA的`@Entity`和`@Id`注解。我们将会经常使用这两个注解，对于要以文档形式保存到MongoDB数据库的每个Java类型都会使用这两个注解。

例如，如下的程序展现了如何为`Order`类添加注解，它会被持久化到MongoDB：

```java
package orders;

import java.util.Collection;
import java.util.LinkedHashSet;

import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;
import org.springframework.data.mongodb.core.mapping.Field;

@Document
public class Order {

    @Id
    private String id;

    @Field("client") // 覆盖默认的域名
    private String customer;

    private String type;

    private Collection<Item> items = new LinkedHashSet<>();

    public String getCustomer() {
        return customer;
    }

    public void setCustomer(String customer) {
        this.customer = customer;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public Collection<Item> getItems() {
        return items;
    }

    public void setItems(Collection<Item> items) {
        this.items = items;
    }

    public String getId() {
        return id;
    }

    @Override
    public String toString() {
        return "Order [id=" + id + ", customer=" + customer + ", type=" + type + ", items=" + items + "]";
    }
}
```

`Order`类使用了`@Document`注解，这样它就能够借助`MongoTemplate`或自动生成的Repository进行持久化。其`id`属性上使用`@Id`注解，用来指定它作为文档的ID。除此之外，`customer`属性上使用了`@Field`注解，这样的话，当文档持久化的时候`customer`属性将会映射为名为`client`的域。

注意，其他的属性并没有添加注解。除非将属性设置为瞬时态（`transient`）的，否则Java对象中所有的域都会持久化为文档中的域。并且如果我们不使用`@Field`注解进行设置的话，那么文档域中的名字将会与对应的Java属性相同。

同时，需要注意的是`items`属性，它指的是订单中具体条目的集合。在传统的关系型数据库中，这些条目将会保存在另外的一个数据库表中，通过外键进行应用，`items`域上很可能还会使用JPA的`@OneToMany`注解。但在这里，情形完全不同。

<center>
    ![图12.1-文档展现了关联但非规范化的数据](images\图12.1-文档展现了关联但非规范化的数据.PNG)
    **文档展现了关联但非规范化的数据。相关的概念（如订单中的条目）被嵌入到顶层的文档数据中**
</center>

如前所述，文档可以与其它文档产生关联，但这并不是文档数据库所擅长的功能。在本例购买订单与行条目之间的关联关系中，行条目只是同一个订单文档里面内嵌的一部分。因此，没有必要为这种关联关系添加任何注解。实际上，`Item`类本身并没有任何注解：

```java
package orders;

public class Item {

    private Long id;

    private Order order;

    private String product;

    private double price;

    private int quantity;

    public Order getOrder() {
        return order;
    }

    public String getProduct() {
        return product;
    }

    public void setProduct(String product) {
        this.product = product;
    }

    public double getPrice() {
        return price;
    }

    public void setPrice(double price) {
        this.price = price;
    }

    public int getQuantity() {
        return quantity;
    }

    public void setQuantity(int quantity) {
        this.quantity = quantity;
    }

    public Long getId() {
        return id;
    }

    @Override
    public String toString() {
        return "Item [id=" + id + ", order=" + order + ", product=" + product + ", price=" + price + ", quantity="
                + quantity + "]";
    }    
}
```

我们没有必要为`Item`添加`@Document`注解，也没有必要为它的域指定`@Id`。这是因为我们不会单独将`Item`持久化为文档。它始终会是`Order`文档中`Item`列表的一个成员，并且会作为文档中的嵌入元素。

#### 12.1.3 使用MongoTemplate访问MongoDB

我们已经在配置类中配置了`MongoTemplate` bean，不管是显式声明还是扩展`AbstractMongoConfiguration`都能实现相同的效果。接下来，需要做的就是将其注入到使用它的地方：

```java
@Autowired
private MongoOperations mongo;
```

`MongoOperations`暴露了多个使用MongoDB文档数据库的方法。在这里，我们不可能讨论所有的方法，但是可以看一下最为常用的几个操作，比如计算文档集合中有多少条文档。使用注入的`MongoOperations`，我们可以得到`Order`集合并调用`count()`来得到数量：

```java
long orderCount = mongo.getCollection("order").count();
```

假设要保存一个新的`Order`，我们可以使用`save()`方法：

```java
Order order = new Order();
// set properties...
mongo.save(order, "order");
```

`save()`方法的第一个参数是新创建的`Order`，第二个参数是要保存的文档存储的名称。

我们还可以调用`findById()`方法来根据ID查找顶顶那：

```java
String orderId = "oxd1125ss3gcf11sad";
Order order = mongo.findById(orderId, Order.class);
```

对于更高级的查询，我们需要构造Query对象并将其传递给`find()`方法。例如，要查找所有`client`域等于“Chuck Wagon”的订单，可以使用如下的代码：

```java
List<Order> orders = mongo.find(Query.query(Criteria.where("client").is("Chuck Wagon")), Order.class);
```

还可以更复杂，例如检查Chuck Wagon所有通过web创建的订单：

```java
List<Order> chucksWebOrders = mongo.find(Query.query(Criteria.where("customer").is("Chuck Wagon").and("type").is("WEB")), Order.class);
```

如果要移除某一个文档，可以使用`remove()`方法：

```java
mongo.remove(order);
```

通常来讲，我们会将`MongoOperations`注入到自己设计的Repository类中，并使用它的操作来实现Repository方法。但是，如果你不愿意编写Repository的话，那么Spring Data MongoDB能够自动在运行时生成Repository实现。

#### 12.1.4 编写MongoDB Repository

为了理解如何使用Spring Data MongoDB来创建Repository，让我们先回忆一下在第11章中是如何使用Spring Data JPA的。我们创建了一个扩展自`JpaRepository`的`SpitterRepository`接口。在那一小节中，我们还启用了SpringData JPA Repository功能。这样的结果就是Spring Data JPA能够自动创建接口的实现，其中包括了多个内置的方法以及我们所添加的遵循命名约定的方法

现在，我们已经通过`@EnableMongoRepositories`注解启用了Spring DataMongoDB的Repository功能，接下来需要做的就是创建一个接口，Repository实现要基于这个接口来生成。不过，在这里，我们不再扩展`JpaRepository`，而是要扩展`MongoRepository`。如下的`OrderRepository`扩展了`MongoRepository`，为Order文档提供了基本的CRUD操作。

```java
package orders.db;

import org.springframework.data.mongodb.repository.MongoRepository;

import orders.Order;

public interface OrderRepository extends MongoRepository<Order, String> {

}
```

因为`OrderRepository`扩展了`MongoRepository`，因此它就会传递性地扩展`Repository`标记接口。任何扩展Repository的接口将会在运行时自动生成实现。

`MongoRepository`接口有两个参数，第一个是带有`@Document`注解的对象类型，也就是该Repository要处理的类型。第二个参数是带有`@Id`注解的属性类型。

尽管`OrderRepository`本身并没有定义任何方法，但是它会继承多个方法，包括对Order文档进行CRUD操作的方法。并且与上一章相同，如果我们编写`OrderRepository`的实现类，那么我们要实现21个方法。

**添加自定义的查询方法**

通常来讲，CRUD操作是很有用的，但我们有时候可能希望Repository提供除内置方法以外的其他方法。

实际上，为MongoRepository自定义方法的命名约定与Spring Data JPA的约定相同。

例如：

```java
Order findOrderById(String id);
```

同样地，我们也可以使用`@Query`注解为Repository方法指定自定义的查询。与JPA唯一不同的是，这里`@Query`会接受一个JSON查询，而不是JPA查询。

例如：

```java
@Query("{'customer':'Tavish', 'type':?0}")
List<Order> findOrderByType(String type); 
```

`@Query`中给定的JSON将会与所有的Order文档进行匹配，并返回匹配的文档。需要注意的是，`type`属性映射成了“?0”，这表明`type`属性应该与查询方法的第零个参数相等。如果有多个参数的话，它们可以通过“?1”、“?2”等方式进行引用。

**混合自定义功能**

对于Spring Data MongoDB来说，创建完全自定义的方法混合到自动生成的Repository的步骤是与Spring Data JPA完全相同的。

假设我们想要查询文档中`type`属性匹配给定值的`Order`对象。我们可以通过创建签名为`List<Order> findByType(String t)`的方法，很容易实现这个功能。但是，如果给定的类型是“NET”，那我们就查找`type`值为“WEB”的`Order`对象。要实现这个功能的话，这就有些困难了，即便使用`@Query`注解也不容易实现。不过，混合实现的做法能够完成这项任务。

首先定义中间接口：

```java
package orders.db;

import java.util.List;

import orders.Order;

public interface OrderOperations {
    List<Order> findOrdersByType(String type);
}
```

接下来编写混合实现：

```java
package orders.db;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.mongodb.core.MongoOperations;
import org.springframework.data.mongodb.core.query.Criteria;
import org.springframework.data.mongodb.core.query.Query;

import orders.Order;

public class OrderRepositoryImpl implements OrderOperations {

    @Autowired
    private MongoOperations mongo;

    @Override
    public List<Order> findOrdersByType(String t) {

        String type = t.equals("NET") ? "WEB" : t;

        Criteria where = Criteria.where("type").is(type);

        return mongo.find(Query.query(where), Order.class);
    }
}
```

可以看到，混合实现中注入了`MongoOperations`（也就是`MongoTemplate`所实现的接口）。`findOrdersByType()`方法使用`MongoOperations`对数据库进行了查询，查找匹配条件的文档。

最后修改`OrderRepository`，让其扩展中间接口`OrderOperations`：

```java
public interface OrderRepository extends MongoRepository<Order, String>, OrderOperations {
    // ...    
}
```

将这些关联起来的关键点在于实现类的名称为`OrderRepositoryImpl`。这个名字前半部分与`OrderRepository`相同，只是添加了“Impl”后缀。当Spring Data MongoDB生成Repository实现时，它会查找这个类并将其混合到自动生成的实现中。

当然，我们可以更改后缀“Impl”：

```java
@EnableMongoRepositories(basePackages = "orders.db", repositoryImplementationPostfix = "Stuff")
```

使用XML配置的话可以使用`<mongo:repositories>`的`repository-impl-postfix`属性。

像MongoDB这样的文档数据库能够解决特定类型的问题，但是就像关系型数据库不是全能型数据库那样，MongoDB同样如此。有些问题并不是关系型数据库或文档型数据库适合解决的，不过，幸好我们的选择并不仅限于这两种。

### 12.2 使用Neo4j操作图数据

文档型数据库会将数据存储到粗粒度的文档中，而图数据库会将数据存储到多个细粒度的结点中，这些结点之间通过关系建立关联。图数据库中的一个结点通常会对应数据库中的一个概念（concept），它会具备描述结点状态的属性。连接两个结点的关联关系可能也会带有属性。

按照其最简单的形式，图数据库比文档数据库更加图用，有可能会成为关系型数据库的无模式（schemaless）提到带方案。因为数据的结构是图，所以可以遍历关联关系以查找数据中你所关心的内容，这在其他数据库中是很难甚至无法实现的。

Spring Data Neo4j提供了很多与Spring Data JPA和Spring Data MongoDB相同的功能，当然所针对的是Neo4j图数据库。它提供了将Java对象映射到结点和关联关系的注解、面向模板的Neo4j访问方式以及Repository实现的自动化生成功能。

#### 12.2.1 配置Spring Data Neo4j

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C12_Working_with_NoSQL_databases-02_neo4j中*。

这里使用的spring-data-neo4j版本为5.0.8，并搭配Spring 5.0.8.Neo4j数据库版本为3.4.7。

```java
package orders.config;

import org.neo4j.ogm.session.SessionFactory;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.neo4j.repository.config.EnableNeo4jRepositories;
import org.springframework.data.neo4j.transaction.Neo4jTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@Configuration
@EnableNeo4jRepositories("orders.db")
@EnableTransactionManagement
public class Neo4jConfig {
    
    // 注册SessionFactory，指定我们的domain所在包
    @Bean
    public SessionFactory sessionFactory() {
        return new SessionFactory(configuration(), "orders");
    }

    // 配置Neo4j事务管理器
    @Bean
    public Neo4jTransactionManager transactionManager(SessionFactory sessionFactory) throws Exception {
        return new Neo4jTransactionManager(sessionFactory);
    }

    // 配置链接数据库的uri以及用户名和密码
    @Bean
    public org.neo4j.ogm.config.Configuration configuration() {
        return new org.neo4j.ogm.config.Configuration.Builder().uri("bolt://localhost").credentials("neo4j", "myNeo4j").build();
    }
}
```

这里的配置与原书中的配置完全不同，原书中该配置类继承`Neo4jConfiguration`，并在此基础上进行配置。但在Spring Data Neo4j的4.2版本中，这个类被标注为`@Deprecated`。

这里`@EnableNeo4jRepositories`注解能够让Spring Data Neo4j自动生成Neo4j Repository实现。通过`value`属性或`basePackages`属性来指定要扫描的包，这样框架就会来查找（直接或间接）扩展Repository标记接口的其他接口。

（注意，`@EnableNeo4jRepositories`有一个属性`sessionFactoryRef`，默认值是sessionFactory。当我们没有显式指定这个属性时，在使用`Session`进行CRUD操作时，框架会默认的寻找名为`sessionFactory`的bean，并用其生成`Session`。也就是说，上述配置类中，如果我们不指定属性`sessionFactoryRef`，那么第一个配置方法的名字必须为`sessionFactory`，因为这个方法名会作为这个方法产生的bean的名字。）

当我们要连接远程的Neo4j服务器时，我们可以配置spring-data-neo4j-rest（需要导入依赖）中的`SpringCypherRestGraphDatabase`，它会通过RESTful API来访问远程的Neo4j数据库：

```java
@Bean
public GraphDatabaseService springCypherRestGraphDatabase() {
    return new SpringCypherRestGraphDatabase("http://graphdbserver/db/data");
}
```

（注意，`SpringCypherRestGraphDatabase`需要依赖`org.springframework.data.neo4j.core.GraphDatabase`，而从spring-data-neo4j 4.0.0开始，库中就不存在这个类了。由于这里我们使用的是5.0.8版本，所以编译会失败。）

也许我们想要使用XML来配置Spring Data Neo4j，但是从4.0.0版本开始，Spring Data Neo4j已经不再支持使用XML配置了：

>Right now SDN only supports JavaConfig. There is no XML based support but this may change in future.

#### 12.2.2 使用注解标注图实体

Neo4j定义了两种类型的实体：结点（node）和关联关系（relationship）。一般来讲，结点反映了应用中的事物，而关联关系定义了这些事物是如何联系在一起的。

Spring Data Neo4j提供了多个注解，它们可以应用在模型类型及其域上，实现Neo4j中的持久化。

注解 | 描述
-----|-----
`@NodeEntity` | 将Java类型声明为结点实体
`@RelationshipEntity` | 将Java类型声明为关联关系实体
`@StartNode` | 将某个属性声明为关联关系实体的开始结点
`@EndNode` | 将某个属性声明为关联关系实体的结束结点
`@Fetch` | 将实体的属性声明为立即加载
`@GraphId` | 将某个属性设置为实体的ID域（这个域的类型必须是`Long`）
`@GraphProperty` | 明确声明某个属性
`@GraphTraversal` | 声明某个属性会自动提供一个iterable元素，这个元素是图遍历所构建的
`@Indexed` | 声明某个属性应该被索引
`@Labels` | 为@NodeEntity声明标签
`@Query` | 声明某个属性会自动提供一个iterable元素，这个元素是执行给定的Cypher查询所构建的
`@QueryResult` | 声明某个Java或接口能够持有查询的结果
`@RelatedTo` | 通过某个属性，声明当前的`@NodeEntity`与另外一个`@NodeEntity`之间的关联关系
`@RelatedToVia` | 在`@NodeEntity`上声明某个属性，指定其引用该结点所属的某一个`@RelationshipEntity`
`@RelationshipType` | 将某个域声明为关联实体类型
`@ResultColumn` | 在带有`@QueryResult`注解的类型上，将某个属性声明为获取查询结果集中的某个特定列

（注意，上述很多注解在当前使用的版本中已不再使用了）

在我们的订单示例中，数据建模的一种方式就是将订单设定为一个结点，他会与一个或多个条目关联：

<center>
    ![图12.2-连接两个节点的简单关联关系](images\图12.2-连接两个节点的简单关联关系.PNG)
    **连接两个节点的简单关联关系，关系本身不包含任何属性**
</center>

为了将订单指定为节点，我们需要为`Order`类添加`@NodeEntity`注解。

```java
package orders;

import java.util.Collection;
import java.util.LinkedHashSet;

import org.neo4j.ogm.annotation.GeneratedValue;
import org.neo4j.ogm.annotation.Id;
import org.neo4j.ogm.annotation.NodeEntity;
import org.neo4j.ogm.id.InternalIdStrategy;

@NodeEntity
public class Order {

    @Id
    @GeneratedValue(strategy = InternalIdStrategy.class)
    private Long id;

    private String customer;

    private String type;

    @Relationship(type = "HAS_ITEMS")
    private Collection<Item> items = new LinkedHashSet<>();

    // ...
}
```

除了类级别上的`@NodeEntity`，还要注意id属性上使用了`@Id`注解和`@GeneratedValue`注解。（原书中使用`@GraphId`注解，但这个注解已经被标注为`@Deprecated`，并推荐组合使用`@Id`注解和`@GeneratedValue`注解）

`customer`和`type`属性上没有任何注解。只要这些属性不是瞬态的，它们都会成为数据库中节点的属性。

`items`属性上使用了`@Relationship`注解（原书使用`@RelateTo`注解，这个注解在当前版本已无法使用），这表明`Order`与一个`Item`的集合存在关联关系。注解的`type`属性实际上就是为关联关系建立了一个文本标记，它可以设置成任意的值，但通常会给定一个易于人类阅读的文本，用来简单描述这个关联关系的特征。

下面对`Item`添加注解实现图的持久化：

```java
package orders;

import org.neo4j.ogm.annotation.GeneratedValue;
import org.neo4j.ogm.annotation.Id;
import org.neo4j.ogm.annotation.NodeEntity;
import org.neo4j.ogm.id.InternalIdStrategy;

@NodeEntity
public class Item {

    @Id
    @GeneratedValue(strategy = InternalIdStrategy.class)
    private Long id;

    private Order order;

    private String product;

    private double price;

    private int quantity;

    // ...

}
```

`Item`也使用了`@NodeEntity`注解，将其标记为一个节点。它同时也有一个`Long`类型的属性，借助`@Id`和`@GeneratedValue`将其标注为结点的图ID，而其余属性均会作为图数据库中结点的属性。

`Order`和`Item`之间的关联关系很简单，关系本身并不包含任何的数据。因此`@Relationship`注解就足以定义关联关系。但是，并不是所有的关联关系都这么简单。

重新考虑该如何为数据建模，我们会发现订单会与一个或多个产品相关联。订单和铲平之间的关系构成了订单的一个条目：

<center>
    ![图12.3-关联关系实体自身具有属性](images\图12.3-关联关系实体自身具有属性.PNG)
    **关联关系实体自身具有属性**
</center>

在这个新的模型中，订单中产品的数量是条目中的一个属性，而产品本身是另外一个概念。与前面一样，订单和产品都是节点，而条目是关联关系。因为现在的条目必须要包含一个数量值，关联关系不像前面那么简单。我们需要定义一个类来代表条目：

```java
package orders;

import org.neo4j.ogm.annotation.EndNode;
import org.neo4j.ogm.annotation.GeneratedValue;
import org.neo4j.ogm.annotation.Id;
import org.neo4j.ogm.annotation.RelationshipEntity;
import org.neo4j.ogm.annotation.StartNode;

@RelationshipEntity(type = "HAS_LINE_ITEM_FOR")
public class LineItem {
    
    @Id
    @GeneratedValue(strategy = InternalIdStrategy.class)    
    private Long id;
    
    @StartNode
    private Order order;
    
    @EndNode
    private Product product;

    private int quantity;
    
    // ...
}
```

`LineItem`类则使用了`@RelationshipEntity`注解表明它是关联关系实体。关联关系实体的特殊之处在于它们连接了两个节点。`@StartNode`和`@EndNode`注解用在定义关联关系两端的属性上。在本例中，`Order`是开始节点，`Product`是结束节点。

最后，`LineItem`类有一个`quantity`属性，当关联关系创建的时候，它会持久化到数据库中。

#### 12.2.3 使用Neo4jTemplate

Spring Data Neo4j提供了`Neo4jTemplate`来操作Neo4j图数据库中的节点和关联关系。根据我们的配置，在Spring应用上下文中就已经具备了一个`Neo4jTemplate`bean。接下来需要做的就是将其注入到任意想使用它的地方。

例如：

```java
@Autowired
private Neo4jOperations neo4j;
```

`Neo4jTemplate`定义了很多的方法，包括保存节点、删除节点以及创建节点间的关联关系。

（实际上，当前版本已经移除了`Neo4jTemplate`，这里我们使用`Session`）

```java
package orders.test;

import java.util.LinkedHashSet;
import java.util.Set;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.Before;
import org.neo4j.ogm.session.Session;
import org.neo4j.ogm.session.SessionFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import orders.Item;
import orders.Order;
import orders.config.Neo4jConfig;

@ContextConfiguration(classes = Neo4jConfig.class)
@RunWith(SpringJUnit4ClassRunner.class)
public class SessionTest {
    
    // 注入SessionFactory
    @Autowired
    private SessionFactory sessionFactory;
    
    private Session session;
    
    @Before()
    public void Before() {
        // 测试方法执行前获取session
        session = sessionFactory.openSession();
    }
    
    @Test
    public void saveTest() {
        Order order = new Order();
        
        order.setCustomer("Tavish");

        order.setType("Web");
        
        Set<Item> items = new LinkedHashSet<>();
        
        Item item = new Item();
        item.setPrice(19.99);
        item.setProduct("T-shirt");
        item.setQuantity(3);
        items.add(item);
        
        order.setItems(items);
        
        session.save(order);
    }
    
    @Test
    public void findTest() {
        Order order = session.load(Order.class, 0L);
        System.out.println(order);
    }
}
```

当实体保存之后，`save()`方法将会返回被保存的实体，如果之前它使用`@Id`、`@GeneratedValue`注解的属性值为`null`的话，此时这个属性将会被填充上值。

#### 12.2.4 创建自动化的Neo4j Repository

大多数Spring Data项目都具备的最棒的一项功能就是为Repository接口自动生成实现。我们已经在Spring Data JPA和Spring Data MongoDB中看到了这项功能。Spring Data Neo4j也不例外，它同样支持Repository自动化生成功能。

这里我们的`OrderRepository`接口要继承的接口是`Neo4jRepository`：

（原书中同样使用了一个已经废弃的接口，`GraphRepository`）

```java
package orders.db;

import org.springframework.data.neo4j.repository.Neo4jRepository;

import orders.Order;

public interface OrderRepository extends Neo4jRepository<Order, Long> {

}
```

与其他的Spring Data项目一样，Spring Data Neo4j会为扩展Repository接口的其他接口生成Repository方法实现。

现在，我们就能够使用很多通用的CRUD操作，这与`JpaRepository`和`MongoRepository`所提供的功能类似。

```java
package orders.test;

import java.util.LinkedHashSet;
import java.util.Set;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import orders.Item;
import orders.Order;
import orders.config.Neo4jConfig;
import orders.db.OrderRepository;

@ContextConfiguration(classes = Neo4jConfig.class)
@RunWith(SpringJUnit4ClassRunner.class)
public class OrderRepositoryTest {
    
    @Autowired
    private OrderRepository repo;
    
    @Test
    public void saveTest() {
        Order order = new Order();
        
        order.setCustomer("Taco");

        order.setType("Online");
        
        Set<Item> items = new LinkedHashSet<>();
        
        Item item = new Item();
        item.setPrice(19.99);
        item.setProduct("T-Rex");
        item.setQuantity(1);
        items.add(item);
        
        order.setItems(items);
        
        repo.save(order);
    }
    
    @Test
    public void findTest() {
        Order order = repo.findById(20L).get();
        System.out.println(order);
    }
    
    @Test
    public void countTest() {
        System.out.println(repo.count());
    }
}
```

通过遵循命名约定来自定义查询方法这里不再赘述。

当命名约定无法满足需求时，我们还可以为方法添加@Query注解，为其指定自定义的查询。我们之前已经见过`@Query`注解。在SpringData JPA中，我们使用它来为Repository方法指定JPA查询。在SpringData MongoDB中，我们使用它来指定匹配JSON的查询。但是，在使用Spring Data Neo4j的时候，我们必须指定Cypher查询，例如：

```java
@Query("match (o:Order)-[:HAS_ITEMS]->(i:Item) where i.product='T-Rex' return o")
List<Order> findTRexOrders();
```

混合自定义功能在这里也不再赘述。

### 12.3 使用Redis操作key-value数据

Redis是一种特殊类型的数据库，它被称之为key-value存储。顾名思义，key-value存储保存的是键值对。实际上，key-value存储与HashMap有很大的相似性。可以不太夸张地说，它们就是持久化的HashMap。

对于HashMap或者key-value存储来说，其实并没有太多的操作。我们可以将某个value存储到特定的key上，并且能够根据特定key，获取value。差不多也就是这样了。因此，Spring Data的自动Repository生成功能并没有应用到Redis上。不过，Spring Data的另外一个关键特性，也就是面向模板的数据访问，能够在使用Redis的时候，为我们提供帮助。

Spring Data Redis包含了多个模板实现，用来完成Redis数据库的数据存取功能。稍后，我们就会看到如何使用它们。但是为了创建Spring Data Redis的模板，我们首先需要有一个Redis连接工厂。

#### 12.3.1 连接到Redis

（这里使用的spring-data-redis版本为2.0.9，并搭配Spring 5.0.8。同时，jedis的版本为2.9.0。）

Redis连接工厂会生成到Redis数据库服务器的连接。Spring Data Redis为4种Redis客户端实现提供了连接工厂：

- JedisConnectionFactory
- JredisConnectionFactory（@Deprecated）
- LettuceConnectionFactory
- SrpConnectionFactory（@Deprecated）

这里我们使用`JedisConnectionFactory`并进行配置：

```
@Bean
public RedisConnectionFactory jedisConnectionFactory() {
    return new JedisConnectionFactory(rsc);
}
```

通过默认构造器创建的连接工厂会向localhost上的6379端口创建连接，并且没有密码。如果你的Redis服务器运行在其他的主机或端口上，在创建连接工厂的时候，可以设置这些属性：

```java
@Bean
public RedisConnectionFactory jedisConnectionFactory() {
    RedisStandaloneConfiguration rsc = new RedisStandaloneConfiguration();
    // 设置主机名
    rsc.setHostName("redis-server");
    // 设置密码
    rsc.setPassword(RedisPassword.of("myredis"));
    // 设置端口号
    rsc.setPort(7379);
    return new JedisConnectionFactory(rsc);
}
```

在当前版本，`JedisConnectionFactory`类的`setHostName()`方法已经废弃了，并推荐使用`RedisStandaloneConfiguration`类进行配置。

如果要使用`LettuceConnectionFactory`的话那么只需要在最后`return`一个`LettuceConnectionFactory`实例就可以了：

```java
@Bean
public RedisConnectionFactory lettuceConnectionFactory() {
    RedisStandaloneConfiguration rsc = new RedisStandaloneConfiguration();
    // 设置主机名
    rsc.setHostName("redis-server");
    // 设置密码
    rsc.setPassword(RedisPassword.of("myredis"));
    // 设置端口号
    rsc.setPort(7379);
    return new LettuceConnectionFactory(rsc);
}
```

现在，我们有了Redis连接工厂，接下来就可以使用Spring Data Redis模板了。

Redis连接工厂会生成到Redis的链接（以`RedisConnection`的形式）。借助`RedisConnection`，可以存储和读取数据，例如：

```java
RedisConnection conn = cf.getConnection();
conn.stringCommands().set("greeting".getBytes(), "Hello World".getBytes());
```

与之类似，我们还可以获取之前存储的信息：

```java
byte[] greetingBytes = conn.stringCommands().get("greeting".getBytes());
String greeting = new String(greetingBytes);
```

上述代码可以正常运行，但通常我们不会直接操作字符数组。

与其他的Spring Data项目类似，Spring Data Redis以模板的形式提供了较高等级的数据访问方案。

- RedisTemplate
- StringRedisTemplate
- 
其中`StringRedisTemplate`继承了`RedisTemplate`。

`RedisTemplate`可以极大地简化Redis数据访问，能够让我们持久化各种类型的key和value，并不局限于字节数组。在认识到key和value通常是`String`类型之后，`StringRedisTemplate`扩展了`RedisTemplate`，只关注`String`类型。

例如我们已经有了`RedisConnectionFactory`，那么可以使用如下方式构建`RedisTemplate`:

```java
RedisConnectionFactory rcf = ...
RedisTemplate<String, Product> template = new RedisTemplate<>();
template.setConnectionFactory(rcf);
```

`RedisTemplate`使用两个类型进行了参数化。第一个是key的类型，第二个是value的类型。在这里所构建的`RedisTemplate`中，将会保存`Product`对象作为value，并将其赋予一个`String`类型的key。 

如果我们的key和value都是`String`类型，那么可以考虑使用`StringRedisTemplate`。

不过与`RedisTemplate`不同的是，`StringRedisTemplate`有一个接受`RedisConnectionFactory`的构造器，因此没有必要在构建后再调用`setConnectionFactory()`。

```java
StringRedisTemplate redis = new StringRedisTemplate(rcf);
```

当然我们可以将模板对象注册为bean，然后在需要的地方使用依赖注入：

```java
@SuppressWarnings("rawtypes")
@Bean
public RedisOperations templates(RedisConnectionFactory jcf) {
    return new StringRedisTemplate(jcf);
}
```

有了`RedisTemplate`（或`StringRedisTemplate`）之后，我们就可以开始保存、获取以及删除key-value条目了。`RedisTemplate`的大多数操作都是子API提供的。

方法 | 子API接口 | 描述
----- | ----- | -----
`opsForValue()`|`ValueOperations<K, V>`|操作具有简单值的条目
`opsForList()`|`ListOperations<K, V>`|操作具有list值的条目
`opsForSet()`|`SetOperations<K, V>`|操作具有set值的条目
`opsForZSet()`|`ZSetOperations<K, V>`|操作具有ZSet值（排序的set）的条目
`opsForHash()`|`HashOperations<K, HK, HV>`|操作具有hash值的条目
`boundValueOps(K)`|`BoundValueOperations<K,V>`|以绑定指定key的方式，操作具有简单值的条目
`boundListOps(K)`|`BoundListOperations<K,V>`|以绑定指定key的方式，操作具有list值的条目
`boundSetOps(K)`|`BoundSetOperations<K,V>`|以绑定指定key的方式，操作具有set值的条目
`boundZSet(K)`|`BoundZSetOperations<K,V>`|以绑定指定key的方式，操作具有ZSet值（排序的set）的条目
`boundHashOps(K)`|`BoundHashOperations<K,V>`|以绑定指定key的方式，操作具有hash值的条目

**使用简单的值**

假设我们想通过`RedisTemplate<String, Product>`保存`Product`，其中key是其`sku`属性的值。那么可以这样做：

```java
template.opsForValue().set(product.getSku(), product);
```

类似地，如果你希望获取`sku`属性为123456的产品，那么可以`get()`方法：

```java
Product product = redis.opsForValue().get("123456");
```

如果无法找到与指定key对应的value，则会返回`null`。

**使用List类型的值**

使用`List`类型的value与之类似，只需使用`opsForList()`方法即可：

```java
redis.opsForList().rightPush("cart", product);
```

通过这种方式，我们向列表的尾部添加了一个`Product`，列表名为cart，如果这个列表还不存在，那么会自动创建一个。

`rightPush()`会在列表的尾部添加一个元素，而`leftPush()`则会在列表的头部添加一个值。

类似地，可以使用`leftPop()`或`rightPop()`从列表中弹出一个元素：

```java
Product first = redis.opsForList().leftPop("cart");
Product last = redis.opsForList().right("cart");
```

除了从列表中获取值以外，这两个方法还有一个副作用就是从列表中移除所弹出的元素。如果我们只是想获取值的话（甚至可能要在列表的中间获取），那么可以使用`range()`方法：

`range()`方法不会从列表中移除任何元素，但是它会根据指定的key和索引范围，获取范围内的一个或多个值，例如：

```java
List<Product> products = redis.opsForList().range("cart", 2, 12);
```

如果范围超出了列表的边界，那么只会返回索引在范围内的元素。如果该索引范围内没有元素的话，将会返回一个空的列表。

**在Set上执行操作**

除了操作列表以外，我们还可以使用opsForSet()操作Set。在我们有多个Set并填充值之后，就可以对这些Set进行一些有意思的操作，如获取其交集、并集和差集：

```java
Set<Product> diff = redis.opsForSet().difference("cart1", "cart2");
Set<Product> union = redis.opsForSet().union("cart1", "cart2");
Set<Product> isect = redis.opsForSet().intersect("cart1", "cart2");
```

**绑定到某个key上**

上表中包含了5个子API，它们能够以绑定key的方式执行操作。这些子API与其他的API是对应的，但是关注于某一个给定的key。

假设我们想从一个key为cart的list的右侧弹出一个元素，然后在list的尾部新增三个元素。我们此时可以使用`boundListOps()`方法所返回的`BoundListOperations`：

```java
BoundListOperations<String, Product> cart = template.boundListOps("cart");
Product poped = cart.rightPop();
cart.rightPush(product1);
cart.rightPush(product2)
cart.rightPush(product3)
```

注意，我们只在一个地方使用了条目的key，也就是调用`boundListOps()`的时候。对返回的`BoundListOperations`执行的所有操作都会应用到这个key上。

#### 12.3.3 使用key和value的序列化器

当某个条目保存到Redis key-value存储的时候，key和value都会使用Redis的序列化器（serializer）进行序列化。Spring Data Redis提供了当某个条目保存到Redis key-value存储的时候，key和value都会使用Redis的序列化器（serializer）进行序列化。Spring Data Redis提供了多个这样的序列化器，包括：

- GenericToStringSerializer：使用Spring转换服务进行序列化；
- Jackson2JsonRedisSerializer：使用Jackson 2，将对象序列化为JSON；
- JdkSerializationRedisSerializer：使用Java序列化；
- OxmSerializer：使用Spring O/X映射的编排器和解排器（marshaler和unmarshaler）实现序列化，用于XML序列化；
- StringRedisSerializer：序列化String类型的key和value。

当然我们也可以自定义序列化器。

`RedisTemplate`会使用`JdkSerializationRedisSerializer`，这意味着key和value都会通过Java进行序列化。`StringRedisTemplate`默认会使用`StringRedisSerializer`。

如果我们要指定序列化器，那么可以调用`setKeySerializer()`和`setValueSerializer()`方法：

```java
@Bean
public RedisOperations<String, Product> templates(RedisConnectionFactory jcf) {
    RedisTemplate<String, Product> template = new RedisTemplate<>();
    template.setConnectionFactory(jcf);
    template.setKeySerializer(new StringRedisSerializer());
    template.setValueSerializer(new Jackson2JsonRedisSerializer<>(Product.class));
    return template;
}
```

这里我们设置在序列化key时使用`StringRedisSerializer`，在序列化value时使用`Jackson2JsonRedisSerializer`。

### 12.4 小结

>
关系型数据库作为数据持久化领域唯一可选方案的时代已经一去不返了。现在，我们有多种不同的数据库，每一种都代表了不同形式的数据，并提供了适应多种领域模型的功能。Spring Data能够让我们在Spring应用中使用这些数据库，并且使用一致的抽象方式访问各种数据库方案。
>
在本章中，我们基于前一章使用JPA时所学到的Spring Data知识，将其应用到了MongoDB文档数据库和Neo4j图数据库中。与JPA对应的功能类似，Spring Data MongoDB和Spring Data Neo4j项目都提供了基于接口定义自动生成Repository的功能。除此之外，我们还看到了如何使用Spring Data所提供的注解将领域模型映射为文档、节点和关联关系。
>
Spring Data还支持将数据持久化到Redis key-value存储中。Key-value存储明显要简单一些，因此没有必要支持自动化Repository和映射注解。不过，Spring Data Redis还是提供了两个不同的模板类来使用Redis key-value存储。
>
不管你选择使用哪种数据库，从数据库中获取数据都是消耗成本的操作。实际上，数据库查询是很多应用最大的性能瓶颈。我们已经看过了如何通过各种数据源存储和获取数据，现在看一下如何避免出现这种瓶颈。在下一章中，我们将会看到如何借助声明式缓存避免不必要的数据库查询。

## 第十三章 缓存数据

本章内容：

- 启用声明式缓存
- 使用Ehcache、Redis和GemFire实现缓存功能
- 注解驱动的缓存

缓存（Caching）可以存储经常会用到的信息，这样每次需要的时候，这些信息都是立即可用的。在本章中，我们将会了解到Spring的缓存抽象。尽管Spring自身并没有实现缓存解决方案，但是它对缓存功能提供了声明式的支持，能够与多种流行的缓存实现进行集成。

### 13.1 启用对缓存的支持

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C13_Caching_data中*。

Spring对缓存的支持有两种方式：

- 注解驱动的缓存
- XML声明的缓存

使用Spring的缓存抽象时，最为通用的方式就是在方法上添加`@Cacheable`和`@CacheEvict`注解。

在往bean上添加缓存注解之前，必须要启用Spring对注解驱动缓存的支持。如果我们使用Java配置的话，那么可以在其中的一个配置类上添加`@EnableCaching`，这样的话就能启用注解驱动的缓存。

```java
package spittr.config;

import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.concurrent.ConcurrentMapCacheManager;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableCaching
public class CachingConfig {

    @Bean 
    public CacheManager cacheManager() {
        return new ConcurrentMapCacheManager();
    }
  
}
```

通过XML配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:cache="http://www.springframework.org/schema/cache"
    xsi:schemaLocation="http://www.springframework.org/schema/cache 
    http://www.springframework.org/schema/cache/spring-cache-4.3.xsd
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- 启用缓存 -->
    <cache:annotation-driven />

    <!-- 声明缓存管理器 -->
    <bean id="cacheManager"
        class="org.springframework.cache.concurrent.ConcurrentMapCacheManager" />

</beans>
```

本质上，`@EnableCaching`和`<cache:annotation-driven />`的工作方式是相同的。它们都会创建一个切面（aspect）并触发Spring缓存注解的切点（pointcut）。根据所使用的注解以及缓存的状态，这个切面会从缓存中获取数据，将数据添加到缓存之中或者从缓存中移除某个值。

在上述配置中，缓存管理器是Spring缓存抽象的核心，它能够与多个流行的缓存实现进行集成。

这是使用了`ConcurrentMapCacheManager`，这个简单的缓存管理器使用`ConcurrentHashMap`作为缓存存储。它非常简单，因此对于开发、测试或基础的应用来讲，这是一个很不错的选择。但它的缓存存储是基于内存的，所以它的生命周期是与应用关联的，对于生产级别的大型企业级应用程序，这可能并不是理想的选择。

#### 13.1.1 配置缓存管理器

Spring提供了以下缓存管理器实现：

- SimpleCacheManager
- CompositeCacheManager
- ConcurrentMapCacheManager
- NoOpCacheManager
- RedisCacheManager
- EhCacheCacheManager
- JCacheCacheManager

在为Spring的缓存抽象选择缓存管理器时，我们有很多可选方案。具体选择哪一个要取决于想要使用的底层缓存供应商。每一个方案都可以为应用提供不同风格的缓存，其中有一些会比其他的更加适用于生产环境。尽管所做出的选择会影响到数据如何缓存，但是Spring声明缓存的方式上并没有什么差别。

**使用Ehcache缓存**

Spring提供集成Ehcache的缓存管理器是`EhCacheCacheManager`。

（由于这里我们使用的Ehcache2，所以使用`EhCacheCacheManager`，如果使用EhCache3，那么需要使用`JCacheCacheManager `）

配置如下：

```java
package spittr.config;

import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.ehcache.EhCacheCacheManager;
import org.springframework.cache.ehcache.EhCacheManagerFactoryBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;

import net.sf.ehcache.CacheManager;

@Configuration
@EnableCaching
public class CachingConfig {

    // 配置EhCacheCacheManager
    @Bean
    public EhCacheCacheManager cacheManager(CacheManager manager) {
        return new EhCacheCacheManager(manager);
    }

    // 配置EhCacheManagerFactoryBean
    @Bean
    public EhCacheManagerFactoryBean ehcache() {
        EhCacheManagerFactoryBean factory = new EhCacheManagerFactoryBean();
        factory.setConfigLocation(new ClassPathResource("ehcache.xml"));
        return factory;
    }

}
```

`cacheManager()`方法创建了一个`EhCacheCacheManager`的实例，这是通过传入`EhcacheCacheManager`实例实现的。在这里，稍微有点诡异的注入可能会让人感觉迷惑，这是因为Spring和EhCache都定义了`CacheManager`类型。

我们需要使用EhCache的`CacheManager`来进行注入，所以必须也要声明一个`CacheManager` bean。为了对其进行简化，Spring提供了`EhCacheManagerFactoryBean`来生成EhCache的`CacheManager`。方法`ehcache()`会创建并返回一个`EhCacheManagerFactoryBean`实例。因为它是一个工厂bean（也就是说，它实现了Spring的`FactoryBean`接口），所以注册在Spring应用上下文中的并不是`EhCacheManagerFactoryBean`的实例，而是`CacheManager`的一个实例，因此适合注入到`EhCacheCacheManager`之中。当然我们可以手动调用工厂的`getObject()`方法来返回`CacheManager`。

除了在Spring中配置的bean，还需要有针对EhCache的配置。EhCache为XML定义了自己的配置模式，我们需要在一个XML文件中配置缓存，该文件需要符合EhCache所定义的模式。在创建`EhCacheManagerFactoryBean`的过程中，需要告诉它EhCache配置文件在什么地方。在这里通过调用`setConfigLocation()`方法，传入`ClassPathResource`，用来指明EhCache XML配置文件相对于根类路径（classpath）的位置。

ehcache.xml文件的内容，根据不同的应用及配置会有所差别，但至少需要声明一个最小缓存：

```xml
<ehcache>
    <cache name="spittleCache" 
            maxBytesLocalHeap="50m"
            timeToLiveSecond="100">
    </cache>
</ehcache>
```

**使用Redis缓存**

如果你仔细想一下的话，缓存的条目不过是一个键值对（key-valuepair），其中key描述了产生value的操作和参数。因此，很自然地就会想到，Redis作为key-value存储，非常适合于存储缓存。

Redis可以用来为Spring缓存抽象机制存储缓存条目，Spring Data Redis提供了`RedisCacheManager`，这是`CacheManager`的一个实现。`RedisCacheManager`会与一个Redis服务器协作，并通过RedisTemplate将缓存条目存储到Redis中。

配置如下：

```java
@Configuration
@EnableCaching
public class CacheConfig {

    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory) {
        return RedisCacheManager.create(factory);
    }
    
    @Bean
    public RedisConnectionFactory jedisConnectionFactory() {
        RedisStandaloneConfiguration rsc = new RedisStandaloneConfiguration();
        // 设置主机名
        rsc.setHostName("localhost");
        // 设置密码
        rsc.setPassword(RedisPassword.of("myredis"));
        // 设置端口号
        rsc.setPort(6379);
        return new JedisConnectionFactory(rsc);
    }   
}
```

（当前版本的`RedisCacheManager`已经不再支持使用`RedisTemplate`来构造了）

**配置多个缓存管理器**

我们并不是只能有且仅有一个缓存管理器。如果你很难确定该使用哪个缓存管理器，或者有合法的技术理由使用超过一个缓存管理器的话，那么可以尝试使用Spring的`CompositeCacheManager`。

`CompositeCacheManager`要通过一个或更多的缓存管理器来进行配置，它会迭代这些缓存管理器，以查找之前所缓存的值。

```java
@Bean
public CacheManager manager(net.sf.ehcache.CacheManager cm,
                            javax.cache.CacheManager jcm, 
                            RedisConnectionFactory factory) {

    CompositeCacheManager cacheManager = new CompositeCacheManager();
    List<org.springframework.cache.CacheManager> managers = new ArrayList<>();
    managers.add(new JCacheCacheManager(jcm));
    managers.add(new EhCacheCacheManager(cm));
    managers.add(RedisCacheManager.create(factory));
    cacheManager.setCacheManagers(managers);
    return cacheManager;
}
```

当查找缓存条目时，`CompositeCacheManager`首先会从`JCacheCacheManager`开始检查JCache实现，然后通过`EhCacheCacheManager`检查Ehcache，最后会使用`RedisCacheManager`来检查Redis，完成缓存条目的查找。

### 13.2 为方法添加注解以支持缓存

如前文所述，Spring的缓存抽象在很大程度上是围绕切面构建的。在Spring中启用缓存时，会创建一个切面，它触发一个或更多的Spring的缓存注解。

缓存规则：

注解 | 描述
----- | -----
`@Cacheable` | 表明Spring在调用方法之前，首先应该在缓存中查找方法的返回值。如果这个值能够找到，就会返回缓存的值。否则的话，这个方法就会被调用，返回值会放到缓存之中
`@CachePut` | 表明Spring应该将方法的返回值放到缓存中。在方法的调用前并不会检查缓存，方法始终都会被调用
`@CacheEvict` | 表明Spring应该在缓存中清除一个或多个条目
`@Caching` | 这是一个分组的注解，能够同时应用多个其他的缓存注解

#### 13.2.1 填充缓存

我们可以看到，`@Cacheable`和`@CachePut`注解都可以填充缓存，但是它们的工作方式略有差异。

`@Cacheable`首先在缓存中查找条目，如果找到了匹配的条目，那么就不会对方法进行调用了。如果没有找到匹配的条目，方法会被调用并且返回值要放到缓存之中。而`@CachePut`并不会在缓存中检查匹配的值，目标方法总是会被调用，并将返回值添加到缓存之中。

`@Cacheable`和`@CachePut`有一些属性是共有的：

属性 | 类型 | 描述
----- | ----- | -----
`value` |`String[]`|要使用的缓存名称
`condition` |`String`|SpEL表达式，如果得到的值是`false`的话，不会将缓存应用到方法调用上
`key` |`String`|SpEL表达式，用来计算自定义的缓存key
`unless` |`String`|SpEL表达式，如果得到的值是`true`的话，返回值不会放到缓存之中

在最简单的情况下，在`@Cacheable`和`@CachePut`的这些属性中，只需使用value属性指定一个或多个缓存即可。例如，考虑`SpittleRepository`的`findOne()`方法。在初始保存之后，Spittle就不会再发生变化了。如果有的Spittle比较热门并且会被频繁请求，反复地在数据库中进行获取是对时间和资源的浪费。通过在`findOne()`方法上添加`@Cacheable`注解，将Spittle保存在缓存中，从而避免对数据库不必要的访问：

```java
@Cacheable("spittleCache")
Spittle findOne(long id);
```

当`findOne()`被调用时，缓存切面会拦截调用并在缓存中查找之前以名spittleCache存储的返回值。缓存的key是传递到`findOne()`方法中的`id`参数。如果按照这个key能够找到值的话，就会返回找到的值，方法不会再被调用。如果没有找到值的话，那么就会调用这个方法，并将返回值放到缓存之中，为下一次调用`findOne()`方法做好准备。

这里我们没有指定注解中的属性`key`，这意味着，方法所有的参数都会被作为key使用，即这里的方法参数`id`会作为缓存的key。

>
* Spring Expression Language (SpEL) expression for computing the key namically.
* Default is {@code ""}, meaning all method parameters are considered as a key,
* unless a custom {@link #keyGenerator} has been configured.

同时，这里我们将注解用在了接口方法上而不是其实现类方法上，当为接口方法添加注解后，@Cacheable注解会被`SpittleRepository`的所有实现继承，这些实现类都会应用相同的缓存规则。

**将值放到缓存之中**

`@Cacheable`会条件性地触发对方法的调用，这取决于缓存中是不是已经有了所需要的值，对于所注解的方法，`@CachePut`采用了一种更为直接的流程。带有`@CachePut`注解的方法始终都会被调用，而且它的返回值也会放到缓存中。这提供一种很便利的机制，能够让我们在请求之前预先加载缓存。

例如，当一个全新的Spittle通过`save()`方法保存后，很可能马上就会请求这条记录。所以，当`save()`方法调用后，立即将Spittle塞到缓存之中是很有意义的，这样当其他人通过`findOne()`对其进行查找时，它就已经准备就绪了。为了实现这一点，可以在`save()`方法上添加`@CachePut`注解，如下所示：

```java
@CachePut("spittleCache")
Spittle save(Spittle spittle);
```

当`save()`方法被调用时，它首先会做所有必要的事情来保存Spittle，然后返回的Spittle会被放到spittleCache缓存中。

在这里只有一个问题：缓存的key。如前文所述，默认的缓存key要基于方法的参数来确定。因为save()方法的唯一参数就是`Spittle`，所以它会用作缓存的key。将`Spittle`放在缓存中，而它的缓存key恰好是同一个`Spittle`，这是不是有一点诡异呢？

显然，在这个场景中，默认的缓存key并不是我们想要的。我们需要的缓存key是新保存`Spittle`的ID，而不是`Spittle`本身。所以，在这里需要指定一个key而不是使用默认的key。

**自定义缓存key**

换默认的key，它是通过一个SpEL表达式计算得到的。任意的SpEL表达式都是可行的，但是更常见的场景是所定义的表达式与存储在缓存中的值有关，据此计算得到key。

具体到我们这个场景，我们需要将key设置为所保存`Spittle`的ID。以参数形式传递给`save()`的`Spittle`还没有保存，因此并没有ID。我们只能通过`save()`返回的`Spittle`得到id属性。

幸好，在为缓存编写SpEL表达式的时候，Spring提供了多个用来定义缓存规则的SpEL扩展：

表达式 | 描述
----- | -----
`#root.args` | 传递给缓存方法的参数，形式为数组
`#root.caches` | 该方法执行时所对应的缓存，形式为数组
`#root.target` | 目标对象
`#root.targetClass` | 目标对象的类，是`#root.target.class`的简写形式
`#root.method` | 缓存方法
`#root.methodName` | 缓存方法的名字，是`#root.method.name`的简写形式
`#result` | 方法调用的返回值（不能用在`@Cacheable`注解上）
`#Argument` | 任意的方法参数名（如#argName）或参数索引（如#a0或#p0）

对于`save()`方法来说，我们需要的键是所返回`Spittle`对象的`id`属性。表达式`#result`能够得到返回的`Spittle`。借助这个对象，我们可以通过将key属性设置为`#result.id`来引用`id`属性：

```java
@CachePut(value = "spittleCache", key = "#result.id")
Spittle save(Spittle spittle);
```

按照这种方式配置`@CachePut`，缓存不会去干涉`save()`方法的执行，但是返回的`Spittle`将会保存在缓存中，并且缓存的key与`Spittle`的`id`属性相同。

**条件化缓存**

通过为方法添加Spring的缓存注解，Spring就会围绕着这个方法创建一个缓存切面。但是，在有些场景下我们可能希望将缓存功能关闭。

`@Cacheable`和`@CachePut`提供了两个属性用以实现条件化缓存：`unless`和`condition`，这两个属性都接受一个SpEL表达式。如果`unless`属性的SpEL表达式计算结果为`true`，那么缓存方法返回的数据就不会放到缓存中。与之类似，如果`condition`属性的SpEL表达式计算结果为`false`，那么对于这个方法缓存就会被禁用。

表面上来看，`unless`和`condition`属性做的是相同的事情。但是，这里有一点细微的差别。`unless`属性只能阻止将对象放进缓存，但是在这个方法调用的时候，依然会去缓存中进行查找，如果找到了匹配的值，就会返回找到的值。与之不同，如果`condition`的表达式计算结果为`false`，那么在这个方法调用的过程中，缓存是被禁用的。就是说，不会去缓存进行查找，同时返回值也不会放进缓存中。

假设要保存的`Spittle`对象的`message`属性中包含`NoCache`字样，那么我们就不对其进行缓存。为了阻止这样的`Spittle`被缓存起来，可以这样设置`unless`属性：

```java
@Cacheable(value = "spittleCache" unless = "#result.message.contains('NoCache')")
Spittle findOne(long id);
```

为`unless`设置的SpEL表达式会检查返回的`Spittle`对象（在表达式中通过`#result`来识别）的`message`属性。如果它包含“NoCache”文本内容，那么这个表达式的计算值为`true`，这个`Spittle`对象不会放进缓存中。否则的话，表达式的计算结果为`false`，无法满足`unless`的条件，这个`Spittle`对象会被缓存。

属性`unless`能够阻止将值写入到缓存中，但是有时候我们希望将缓存全部禁用。也就是说，在一定的条件下，我们既不希望将值添加到缓存中，也不希望从缓存中获取数据。

假如，对于ID小于10的`Spittle`对象，我们不希望对其使用缓存。这时可以使用`condition`属性：

```java
@Cacheable(value = "spittleCache" 
            unless = "#result.message.contains('NoCache')"
            condition = "#id >= 10")
Spittle findOne(long id);
```

此时，如果`findOne()`调用时，参数值小于10，那么将不会在缓存中进行查找，返回的Spittle也不会放进缓存中，就像这个方法没有添加`@Cacheable`注解一样。

#### 13.2.2 移除缓存条目

`@CacheEvict`并不会往缓存中添加任何东西。相反，如果带有`@CacheEvict`注解的方法被调用的话，那么会有一个或更多的条目会在缓存中移除。

那么在什么场景下需要从缓存中移除内容呢？当缓存值不再合法时，我们应该确保将其从缓存中移除，这样的话，后续的缓存命中就不会返回旧的或者已经不存在的值，其中一个这样的场景就是数据被删除掉了，例如：

```java
@CacheEvict("spittleCache")
void remove(long spittleId)
```

`@CacheEvict`的一个特点是可以应用在返回值为`void`的方法上，而`@Cacheable`和`@CachePut`需要非`void`的返回值。

从这里可以看到，当`remove()`调用时，会从缓存中删除一个条目。被删除条目的key与传递进来的`spittleId`参数的值相等。

`@CacheEvict`有多个属性，这些属性会影响到其行为：

属性 | 类型 | 描述
----- | ----- | -----
`value` |`String[]`|要使用的缓存名称
`condition` |`String`|SpEL表达式，如果得到的值是`false`的话，不会将缓存应用到方法调用上
`key` |`String`|SpEL表达式，用来计算自定义的缓存key
`allEntries` |`boolean`|如果为`true`的话，特定缓存的所有条目都会被移除掉
`beforeInvocation`|`boolean`|如果为`true`的话，在方法调用之前移除条目。如果为`false`（默认值）的话，在方法成功调用之后再移除条目

### 13.3 使用XML声明缓存

需要使用XML声明缓存的原因有两个：

- 单纯地不喜欢直接在源码上添加注解
- 需要在没有源码的bean上应用缓存功能

Spring的`cache`命名空间提供了使用XML声明缓存规则的方法，可以作为面向注解缓存的替代方案。因为缓存是一种面向切面的行为，所以`cache`命名空间会与Spring的`aop`命名空间结合起来使用，用来声明缓存所应用的切点在哪里。

`cache`命名空间定义了在XML配置文件中声明缓存的配置元素：

元素 | 描述
-----|-----
`<cache:annotation-driven>` | 启用注解驱动的缓存。等同于Java配置中的`@EnableCaching`
`<cache:advice>` | 定义缓存通知（advice）。结合`<aop:advisor>`，将通知应用
到切点上
`<cache:caching>` |在缓存通知中，定义一组特定的缓存规则
`<cache:cacheable>` |指明某个方法要进行缓存。等同于`@Cacheable`注解
`<cache:cache-put>` |指明某个方法要填充缓存，但不会考虑缓存中是否已有匹配的值。等同于`@CachePut`注解
`<cache:cache-evict>` |指明某个方法要从缓存中移除一个或多个条目，等同于`@CacheEvict`注解

配置示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:cache="http://www.springframework.org/schema/cache"
    xsi:schemaLocation="http://www.springframework.org/schema/cache 
    http://www.springframework.org/schema/cache/spring-cache-4.3.xsd
        http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-4.3.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

    <!-- 将缓存通知绑定到一个切点上 -->
    <aop:config>
        <aop:advisor advice-ref="cacheAdvice"
            pointcut="execution(* spittr.db.SpittleRepository.*(..))" />
    </aop:config>

    <!-- 配置缓存 -->
    <cache:advice id="cacheAdvice">
        <cache:caching>
            <cache:cacheable cache="spittleCache" method="findRecent" />
            <cache:cacheable cache="spittleCache" method="findOne" />
            <cache:cacheable cache="spittleCache" method="findBySpitterId" />
            <cache:cache-put cache="spittleCache" method="save" key="#result.id" />
            <cache:cache-evict cache="spittleCache" method="remove" />
        </cache:caching>
    </cache:advice>

    <!-- 配置缓存管理器 -->
    <bean id="cacheManager"
        class="org.springframework.cache.concurrent.ConcurrentMapCacheManager" />
</beans>
```

需要注意的是，`<cache:advice>`元素有一个`cache-manager`属性，用来指定作为缓存管理器的bean。它的默认值是cacheManager。因为我们恰巧声明了一个id为cacheManager的缓存管理器，所以这里可以不用显式地使用`cache-manager`属性。

这里我们多次使用了`cache="spittleCache"`来指明要使用的缓存，为了消除这种重复，我们可以将其抽出放在`<cache:caching>`元素上：

```xml
<cache:caching cache="spittleCache">
    <cache:cacheable method="findRecent" />
    <cache:cacheable method="findOne" />
    <cache:cacheable method="findBySpitterId" />
    <cache:cache-put method="save" key="#result.id" />
    <cache:cache-evict method="remove" />
</cache:caching>
```

对于上述使用的XML配置中的每个`cache`命名空间下的元素，它都有与注解属性相对应的属性，这里不再赘述。

### 13.4 小结

>
如果想让应用程序避免一遍遍地为同一个问题推导、计算或查询答案的话，缓存是一种很棒的方式。当以一组参数第一次调用某个方法时，返回值会被保存在缓存中，如果这个方法再次以相同的参数进行调用时，这个返回值会从缓存中查询获取。在很多场景中，从缓存查找值会比其他的方式（比如，执行数据库查询）成本更低。因此，缓存会对应用程序的性能带来正面的影响。
>
在本章中，我们看到了如何在Spring应用中声明缓存。首先，看到的是如何声明一个或更多的Spring缓存管理器。然后，将缓存用到了Spittr应用程序中，这是通过将@Cacheable、@CachePut和@CacheEvict添加到SpittleRepository上实现的。
>
我们还看到了如何借助XML将缓存规则的配置与应用程序代码分离开来。`<cache:cacheable>`、`<cache:cache-put>`和`<cache:cache-evict>`元素的作用与本章前面所使用的注解是一致的。
>
在这个过程中，我们讨论了缓存实际上是一种面向切面的行为。Spring将缓存实现为一个切面。在使用XML声明缓存规则时，这一点非常明显：我们必须要将缓存通知绑定到一个切点上。
>
Spring在将安全功能应用到方法上时，同样使用了切面。在下一章中，我们将会看到如何借助Spring Security确保bean方法的安全性。

## 第十四章 保护方法应用

本章内容：

- 保护方法调用
- 使用表达式定义安全规则
- 创建安全表达式计算器

在本章中，我们将会看到如何使用Spring Security保护bean方法。通过这种方式，就能声明安全规则，保证如果用户没有执行方法的权限，就不会执行相应的方法。

### 14.1 使用注解保护方法

在Spring Security中实现方法级安全性的最常见办法是使用特定的注解，将这些注解应用到需要保护的方法上。这样有几个好处，最重要的是当我们在编辑器中查看给定的方法时，能够很清楚地看到它的安全规则。

Spring Security提供了三种不同的安全注解：

- Spring Security自带的`@Secured`注解
- JSR-250的`@RolesAllowed`注解
- 表达式驱动的注解，包括`@PreAuthorize`、`@PostAuthorize`、`@PreFilter`和`@PostFilter`

`@Secured`和`@RolesAllowed`方案非常类似，能够基于用户所授予的权限限制对方法的访问。当我们需要在方法上定义更灵活的安全规则时，Spring Security提供了`@PreAuthorize`和`@PostAuthorize`，而`@PreFilter`/`@PostFilter`能够过滤方法返回的以及传入方法的集合。

#### 14.1.1 使用@Secured注解限制方法调用

*以下内容代码在工程sia4e-P3_Spring_in_the_back_end-C14_Securing_methods中*。

在Spring中，如果要启用基于注解的方法安全性需要在配置类中使用`@EnableGlobalMehtodSecurity`：

```java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration{

}
```

这里我们除了使用`@EnableGlobalMethodSecurity`注解，也继承了`GlobalMethodSecurityConfiguration`类。这个类能够为方法级别的安全性提供更精细的配置。

注意到`@EnableGloablMethodSecurity`注解的`securedEnable`属性设置为`true`，这会创建一个切点，这样的话Spring Security切面就会包装带有`@Secured`注解的方法，例如：

```java
@Secured("ROLE_SPITTER")
public void addSpittle(Spittle spittle) {
    // ...
}
```

`@Secured`注解会使用一个`String`数组作为参数。每个`String`值是一个权限，调用这个方法至少需要具备其中一个权限。通过传递进来的“ROLE_SPITTER”，我们告诉Spring Security只允许具有“ROLE_SPITTER”权限的认证用户才能调用`addSpittle()`方法。

如果传递给`@Secured`多个权限值，则认证用户必须至少具备其中的一个才能进行方法的调用。例如，下面使用`@Secured`的方式表明用户必须具备“ROLE_SPITTER”或“ROLE_ADMIN”才能调用这个方法：

```java
@Secured({ "ROLE_SPITTER", "ROLE_ADMIN" })
public void addSpittle(Spittle spittle) {
    // ...
}
```

如果方法被没有认证的用户或没有所需权限的用户调用，保护这个方法的切面将抛出异常（`AuthenticationException`或`AccessDeniedException`）。它们是非检查型异常，但这个异常最终必须被捕获和处理。如果被保护的方法是在Web请求中调用的，这个异常会被Spring Security的过滤器自动处理。否则的话我们需要编写代码来处理这个异常。

`@Secured`注解的不足之处在于它是Spring特定的注解。如果更倾向于使用Java标准定义的注解，那么可以考虑使用`@RolesAllowed`注解。

#### 14.1.2 在Spring Security中使用JSR-250的@RolesAllowed注解

`@RolesAllowed`注解和`@Secured`注解在各个方面基本上都是一致的。唯一显著的区别在于前者是JSR-250定义的Java标准注解。当使用其它框架或API来处理注解时，使用标准的`@RolesAllowed`注解会更有意义。

如果选择使用`@RolesAllowed`的话，需要将`@EnabledGlobalMethodSecurity`的`jsr250Enabled`属性设置为`true`：

```java
@EnableGlobalMethodSecurity(jsr250Enabled = true)
```

尽管我们这里只是启用了`jsr250Enabled`，但需要说明的一点是这与`securedEnabled`并不冲突。这两种注解风格可以同时启用。

在将`jsr250Enabled`设置为`true`之后，将会启用一个切点，这样带有`@RolesAllowed`注解的方法都会被Spring Security的切面包装起来。因此，在方法上使用`@RolesAllowed`的方式与使用`@Secured`类似。例如，如下的`addSpittle()`方法使用了`@RolesAllowed`注解来代替`@Secured`：

```java
@RolesAllowed("ROLE_SPITTER")
public void addSpittle(Spittle spittle) {
    // ...
}
```

`@Secured`和`@RolesAllowed`注解有一个共同的不足之处，它们只能根据用户有没有授予特定的权限来限制方法的调用。在判断方法是否执行方面，无法使用其它的因素。

接下来我们看一下如何组合使用SpEL和Spring Security所提供的方法调用前后注解实现基于表达式的方法安全性。

### 14.2 使用表达式实现方法级别的安全性

尽管`@Secured`和`@RolesAllowed`注解在拒绝未认证用户方面表现不错，但这也是它们所能做到的所有事情了。有时候，安全性约束不仅仅涉及用户是否有权限。

Spring Security 3.0引入了几个新注解，它们通过使用SpEL能够在方法调用上实现更有意思的安全性约束。这些注解的值能够接受一个SpEL表达式。表达式可以是任意合法的SpEL表达式，如果表达式的计算结果为`true`，那么安全规则通过，否则就会失败。安全规则通过或失败的结果会因为所使用注解的差异而有所不同。

注解 | 描述
-----|-----
`@PreAuthorize` | 在方法调用之前，基于表达式的计算结果来限制对方法的访问
`@PostAuthorize` | 允许方法调用，但是如果表达式计算结果为`false`，将抛出一个安全性异常
`@PostFilter` | 允许方法调用，但必须按照表达式来过滤方法的结果
`@PreFilter` | 允许方法调用，但必须在进入方法之前过滤输入值

在使用这些注解之前，我们必须先要启用它们，这需要将`@EnableGlobalMethodSecurity`注解的`prePostEnabled`属性设置为`true`：

```java
@EnableGlobalMethodSecurity(jsr250Enabled = true, prePostEnabled = true)
```

现在，方法调用前后的注解都已经启用了，我们可以使用它们了。我们首先看一下如何使用`@PreAuthorize`和`@PostAuthorize`注解限制对方法的调用。

#### 14.2.1 表达式访问规则

`@PreAuthorize`和`@PostAuthorize`，它们能够基于表达式的计算结果来限制方法的访问。在定义安全限制方面，表达式带了极大的灵活性。通过使用表达式，只要我们能够想象得到，就可以定义任意允许访问或不允许访问方法的条件。

二者的主要区别在于表达式执行的时机。`@PreAuthorize`的表达式会在方法调用之前执行，如果表达式的计算结果不为`true`，则会阻止方法执行。与之相反，`@PostAuthorize`表达式直到方法返回才会执行，然后决定是否抛出安全性异常。

**在方法调用之前验证权限**

例如，Spittr应用程序的一般用户只能写140个字以内的Spittle，而付费用户不限制字数：

```java
@PreAuthorize("(hasRole('SPITTER') and #spittle.text.length() <= 140)"
                + "or hasRole('PREMIUM')")
public void addSpittle(Spittle spittle) {
    // ...
}
```

表达式中的`#spittle`部分直接引用了方法中的同名参数。这使得Spring Security能够检查传入方法的参数，并将这些参数用于认证决策的制定。在这里，我们深入到Spitter的文本内容中，保证不超过Spittr标准用户的长度限制。如果是付费用户，那么就没有长度限制了。

**在方法调用之后验证权限**

在方法调用之后验证权限并不是比较常见的方式。事后验证一般需要基于安全保护方法的返回值来进行安全性决策。这种情况意味着方法必须被调用执行并且得到了返回值。

除了验证的时机之外，`@PostAuthorize`与`@PreAuthorize`的工作方式差不多，只不过它会在方法执行之后，才会应用安全规则。此时，它才有机会在做出安全决策时，考虑到返回值的因素。

例如，假设我们想对`getSpittleById()`方法进行保护，确保返回的Spittle对象属于当前的认证用户。我们只有得到`Spittle`对象之后，才能判断它是否属于当前用户。因此，`getSpittleById()`方法必须要先执行。在得到`Spittle`之后，如果它不属于当前用户的话，将会抛出安全性异常：

```java
@PostAuthorize("returnObject.spitter.username == principal.username")
public Spittle getSpittleById(long id) {
    // ...
}
```

为了便利地访问受保护方法的返回对象，Spring Security在SpEL中提供了名为returnObject的变量。在这里，我们知道返回对象是一个`Spittle`对象，所以这个表达式可以直接访问其`spitter`属性中的`username`属性。

在对比表达式双等号的另一侧，表达式到内置的`principal`对象中取出其`username`属性。`principal`是另一个Spring Security内置的特殊名称，它代表了当前认证用户的主要信息（通常是用户名）。

在`Spittle`对象所包含`Spitter`中，如果`username`属性与`principal`的`username`属性相同，这个`Spittle`将返回给调用者。否则，会抛出一个`AccessDeniedException`异常，而调用者也不会得到`Spittle`对象。

有一点需要注意，不像`@PreAuthorize`注解所标注的方法那样，`@PostAuthorize`注解的方法会首先执行然后被拦截。这意味着，你需要小心以保证如果验证失败的话不会有一些负面的结果。

#### 14.2.2 过滤方法的输入和输出

如果我们希望使用表达式来保护方法的话，那使用`@PreAuthorize`和`@PostAuthorize`是非常好的方案。但是，有时候限制方法调用太严格了。有时，需要保护的并不是对方法的调用，需要保护的是传入方法的数据和方法返回的数据。

例如，我们有一个名为`getOffensiveSpittles()`的方法，这个方法会返回标记为具有攻击性的`Spittle`列表。这个方法主要会给管理员使用，以保证Spittr应用中内容的和谐。但是，普通用户也可以使用这个方法，用来查看他们所发布的`Spittle`有没有被标记为具有攻击性。这个方法的签名大致如下所示：

```java
public List<Spittle> getOffensiveSpittles() {
    // ...
}
```

按照这种方法的定义，`getOffensiveSpittles()`方法与具体的用户并没有关联。它只会返回攻击性`Spittle`的一个列表，并不关心它们属于哪个用户。对于管理员使用来说，这是一个很好的方法，但是对于普通用户而言它无法限制列表中的Spittle都属于当前用户。

我们需要有一种方式过滤`getOffensiveSpittles()`方法返回的`Spittle`集合，将结果限制为允许当前用户看到的内容，而这就是Spring Security的`@PostFilter`所能做的事情。

（当然，我们也可以重载`getOffensiveSpittles()`，实现另一个版本，让它接受一个用户ID作为参数，查询给定用户的`Spittle`）

**时候对方法的返回值进行过滤**

`@PostFilter`也使用一个SpEL作为参数。但是这个表达式不是用来限制方法访问的，`@PostFilter`会使用这个表达式计算该方法所返回集合的每个成员，并将计算结果为`false`的成员移除掉。

例如：

```java
@PreAuthorize("hasAnyRole({'SPITTER', 'ADMIN'})")
@PostFilter("hasRole('ADMIN') || filterObject.spitter.username == principal.username")
public List<Spittle> getOffensiveSpittles() {
    // ...
}
```

在这里，`@PreAuthorize`限制只有具备`ROLE_SPITTER`或`ROLE_ADMIN`权限的用户才能访问该方法。如果用户能够通过这个检查点，那么方法将会执行，并且会返回`Spittle`所组成的一个`List`。但是，`@PostFilter`注解将会过滤这个列表，确保用户只能看到允许的`Spittle`。具体来讲，管理员能够看到所有攻击性的`Spittle`，非管理员只能看到属于自己的Spittle。

**实现对方法的参数进行过滤**

除了事后过滤方法的返回值，我们还可以预先过滤传入到方法中的值。这项技术不太常用，但是在有些场景下可能会很便利。

例如，假设我们希望以批处理的方式删除`Spittle`组成的列表。为了完成该功能，我们可能会编写一个方法，其签名大致如下所示：

```java
public void deleteSpittles(List<Spittle> spittles) {
    // ...
}
```

如果我们想在它上面应用一些安全规则的话，比如`Spittle`只能由其所有者或管理员删除，那该怎么做呢？如果是这样的话，我们可以将逻辑放在`deleteSpittles()`方法中，在这里循环列表中的`Spittle`，只删除属于当前用户的那一部分对象（如果当前用户是管理员的话，则会全部删除）。

这能够运行正常，但是这意味着我们需要将安全逻辑直接嵌入到方法之中。相对于删除`Spittle`来讲，安全逻辑是独立的关注点（当然，它们也有所关联）。如果列表中能够只包含实际要删除的`Spittle`，这样会更好一些，因为这能帮助`deleteSpittles()`方法中的逻辑更加简单，只关注于删除`Spittle`的任务。

`@PreFilter`注解能够很好地解决这个问题。与`@PostFilter`非常类似，`@PreFilter`也使用SpEL来过滤集合，只有满足SpEL表达式的元素才会留在集合中。但是它所过滤的不是方法的返回值，`@PreFilter`过滤的是要进入方法中的集合成员：

```java
@PreAuthorize("hasAnyRole({'SPITTER', 'ADMIN'})")
@PreFilter("hasRole('ADMIN') || targetObject.spitter.username == principal.username")
public void deleteSpittles(List<Spittle> spittles) {
    // ...
}
```

与前面一样，对于没有`ROLE_SPITTER`或`ROLE_ADMIN`权限的用户，`@PreAuthorize`注解会阻止对这个方法的调用。但同时，`@PreFilter`注解能够保证传递给`deleteSpittles()`方法的列表中，只包含当前用户有权限删除的`Spittle`。这个表达式会针对集合中的每个元素进行计算，只有表达式计算结果为`true`的元素才会保留在列表中。`targetObject`是Spring Security提供的另外一个值，它代表了要进行计算的当前列表元素。

Spring Security提供了注解驱动的功能，这是通过一系列注解来实现的，到此为止，我们已经对这些注解进行了介绍。相对于判断用户所授予的权限，使用表达式来定义安全限制是一种更为强大的方式。即便如此，我们也不应该让表达式过于聪明智能。我们应该避免编写非常复杂的安全表达式，或者在表达式中嵌入太多与安全无关的业务逻辑。

当我们觉得安全表达式难以控制了，那么就应该看一下如何编写自定义的许可计算器（permission evaluator），以简化SpEL表达式。

**定义许可计算器**

我们上述使用的SpEL表达式还不算太复杂。但是，在不断地开发中，这个表达式可能会不断膨胀。在变得很长之前，表达式就会笨重、复杂并难以测试。

下面我们将整个表达式替换为更加简单的版本：

```java
@PreAuthorize("hasAnyRole({'SPITTER', 'ADMIN'})")
@PreFilter("hasPermission(targerObject, 'delete')")
public void deleteSpittles(List<Spittle> spittles) {
    // ...
}
```

现在，设置给`@PreFilter`的表达式更加紧凑。它实际上只是在问一个问题“用户有权限删除目标对象吗？”。如果有的话，表达式的计算结果为`true`，`Spittle`会保存在列表中，并传递给`deleteSpittles()`方法。如果没有权限的话，它将会被移除掉。

但是，`hasPermission()`是哪来的方法？它的意思是什么。更为重要的是，它如何知道用户有没有权限删除`targetObject`所对应的`Spittle`呢？

`hasPermission()`方法是Spring Security为SpEL提供的扩展，它为开发者提供了一个时机，能够在执行计算的时候插入任意的逻辑。我们所需要做的就是编写并注册一个自定义的许可计算器。

```java
package spittr.security;

import java.io.Serializable;

import org.springframework.security.access.PermissionEvaluator;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;

import spittr.domain.Spittle;

public class SpittlePermissionEvaluator implements PermissionEvaluator {

    private static final GrantedAuthority ADMIN_AUTHORITY = new SimpleGrantedAuthority("ADMIN");
    
    @Override
    public boolean hasPermission(Authentication authentication, Object target, Object permission) {
        if (target instanceof Spittle) {
            Spittle spittle = (Spittle) target;
            String username = spittle.getSpitter().getUsername();
            if ("delete".equals(permission)) {
                return isAdmin(authentication) || username.equals(authentication.getName());
            }
        }
        
        throw new UnsupportedOperationException("hasPermission not supported for object <" + target + "> and permission <" + permission + ">");
    }

    @Override
    public boolean hasPermission(Authentication authentication, Serializable targetId, String targetType,
            Object permission) {
        throw new UnsupportedOperationException();
    }
    
    private boolean isAdmin(Authentication authentication) {
        return authentication.getAuthorities().contains(ADMIN_AUTHORITY);
    }
}
```

`SpittlePermissionEvaluator`实现了Spring Security的`PermissionEvaluator`接口，它需要实现两个不同的`hasPermission()`方法。其中的一个`hasPermission()`方法把要评估的对象作为第二个参数。第二个`hasPermission()`方法在只有目标对象的ID可以得到的时候才有用，并将ID作为`Serializable`传入第二个参数。

对于第一个`hasPermission()`方法，要检查所评估的对象是否为一个`Spittle`，并判断所检查的是否为删除权限。如果是这样，它将对比`Spitter`的用户名是否与认证用户的名称相等，或者当前用户是否具有`ROLE_ADMIN`权限。

编写完许可计算器，接下来需要将其注册到Spring Security中，一边在使用`@PreFilter`表达式的时候支持`hasPermission()`操作。

默认情况下，Spring Security会配置为使用`DefaultMethodSecurityExpressionHandler`，它会使用一个`DenyAllPermissionEvaluator`实例。顾名思义，`DenyAllPermissionEvaluator`将会在`hasPermission()`方法中始终返回`false`，拒绝所有的方法访问。但是，我们可以为Spring Security提供另外一个`DefaultMethodSecurityExpressionHandler`，让它使用我们自定义的`SpittlePermissionEvaluator`，这需要重载`GlobalMethodSecurityConfiguration`的`createExpressionHandler`方法：

```java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled = true, prePostEnabled = true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration{
    
    @Override
    protected MethodSecurityExpressionHandler createExpressionHandler() {
        DefaultMethodSecurityExpressionHandler handler = new DefaultMethodSecurityExpressionHandler();
        handler.setPermissionEvaluator(new SpittlePermissionEvaluator());
        return handler;
    }
}
```

现在，我们不管在任何地方的表达式中使用`hasPermission()`来保护方法，都会调用`SpittlePermissionEvaluator`来决定用户是否有权限调用方法。

### 14.3 小结

>
方法级别的安全性是Spring Security Web级别安全性的一个重要补充，我们曾在第9章讨论过Web安全性。对于非Web应用来说，方法级别的安全性则是最前沿的防护。对于Web应用来讲，基于安全规则所声明的方法级别安全性能够保护Web请求。
>
在本章中，我们看到了六个可以在方法上声明安全性限制的注解。对于简单场景来说，面向权限的注解，包括Spring Security的@Secured以及基于标准的@RolesAllowed都很便利。当安全规则更为复杂的时候，组合使用@PreAuthorize、@PostAuthorize以及SpEL能够发挥更强大的威力。我们还看到通过为@PreFilter和@PostFilter提供SpEL表达式，过滤方法的输入和输出。
>
最后，我们还看到了让安全规则更加易于维护、测试和调试的方法，那就是自定义表达式计算器，它能够用在SpEL表达式的hasPermission()函数中。
>
从下一章开始，我们将会转移方向，从使用Spring开发后端应用程序转向与其他应用集成。在接下来的几章中，我们将会看到各种集成技术，包括远程服务、异步消息、REST甚至还有发送E-mail。在下一章我们将会探讨第一项集成技术，也就是使用Spring远程服务。

## 第十五章 使用远程服务

本章内容：

- 访问和发布RMI服务
- 使用Hessian和Burlap服务
- 使用Spring的HTTP invoker
- 使用Spring开发Web服务

作为一个Java开发者，我们有多种可以使用的远程调用技术，包括：

- 远程方法调用（Remote Method Invocation，RMI）
- Caucho的Hessian和Burlap
- Spring基于HTTP的远程服务
- 使用JAX-RPC和JAX-WS的Web Service

不管我们选择哪种远程调用技术，Spring为使用这几种不同的技术访问和创建远程服务都提供了广泛的支持。

### 15.1 Spring远程调用概览

远程调用是客户端应用和服务端之间的会话。在客户端，它所需要的一些功能并不在该应用的实现范围之内，所以应用要向能提供这些功能的其他系统寻求帮助。而远程应用通过远程服务暴露这些功能。

假设我们想把Spittr应用中的某些功能发布为远程服务并提供给其他应用来使用。或许除了现有的基于浏览器的用户界面，我们还想为Spittr应用提供桌面应用或移动端应用，如图所示。为了实现此想法，我们需要把`SpitterService`接口的基本功能发布为远程服务。

<center>
    ![图15.1-第三方客户端能够远程调用Spittr的服务实现与Spittr应用交互](images\图15.1-第三方客户端能够远程调用Spittr的服务实现与Spittr应用交互.PNG)
    **第三方客户端能够远程调用Spittr的服务，从而实现与Spittr应用交互**
</center>

其他应用与Spittr之间的会话开始于客户端应用的一个远程过程调用（remote procedure call，RPC）。从表面上看，RPC类似于调用一个本地对象的一个方法。这两者都是同步操作，会阻塞调用代码的执行，直到被调用的过程执行完毕。

>
它们的差别仅仅是距离的问题，类似于人与人之间的交流。如果我们在公共场所的饮水机旁讨论周末足球比赛的结果，那我们就是在进行一个本地会话——两人之间的会话发生在同一房间内。同样，本地方法调用是指同一个应用中的两个代码块之间的执行流交换。
>
另一方面，如果我们拿起电话打给另一个城市的客户端，那我们之间的会话就是通过电话网络远程进行的。类似地，RPC调用就是执行流从一个应用传递给另一个应用，理论上另一个应用部署在跨网络的一台远程机器上。

Spring支持多种不同的RPC模型，下表简述了每一个模型，并简要讨论了它们所适用的不同场景：

RPC模型 | 适用场景
-----|-----
远程方法调用（RMI） | 不考虑网络限制时（例如防火墙），访问/发布基于Java的服务
Hessian或Burlap | 考虑网络限制时，通过HTTP访问/发布基于Java的服务。Hessian是二进制协议，而Burlap是基于XML的
HTTP invoker | 考虑网络限制，并希望使用基于XML或专有的序列化机制实现Java序列化时，访问/发布基于Spring的服务
JAX-RPC和JAX-WS | 访问/发布平台独立的、基于SOAP的Web服务

不管我们选择哪种远程调用模型，我们会发现Spring都提供了风格一致的支持。

在所有的模型中，服务都作为Spring所管理的bean配置到我们的应用中。这是通过一个代理工厂bean实现的，这个bean能够把远程服务像本地对象一样装配到其他bean的属性中去。下图展示了它是如何工作的。

<center>
    ![图15.2-远程服务被代理](images\图15.2-远程服务被代理.PNG)
    **在Spring中，远程服务被代理，所以它们能够像其他Spring bean一样被装配到客户端代码中**
</center>

客户端向代理发起调用，就像代理提供了这些服务一样。代理代表客户端与远程服务进行通信，由它负责处理连接的细节并向远程服务发起调用。

更重要的是，如果调用远程服务时发生`java.rmi.RemoteException`异常，代理会处理此异常并重新抛出非检查型异常`RemoteAccessException`。远程异常通常预示着系统发生了无法优雅恢复的问题，如网络或配置问题。既然客户端通常无法从远程异常中恢复，那么重新抛出`RemoteAccessException`异常就能让客户端来决定是否处理此异常。

在服务端，我们可以使用上表所列出的任意一种模型将Spring管理的bean发布为远程服务。下图展示了远程导出器（remote exporter）如何将bean方法发布为远程服务。

<center>
    ![图15.3-使用远程导出器将Spring管理的bean发布为远程服务](images\图15.3-使用远程导出器将Spring管理的bean发布为远程服务.PNG)
    **使用远程导出器将Spring管理的bean发布为远程服务**
</center>

无论我们开发的是使用远程服务的代码，还是实现这些服务的代码，或者两者兼而有之，在Spring中，使用远程服务纯粹是一个配置问题。我们不需要编写任何Java代码就可以支持远程调用。我们的服务bean也不需要关心它们是否参与了一个RPC（当然，任何传递给远程调用的bean或从远程调用返回的bean可能需要实现`java.io.Serializable`接口）。

### 15.2 使用RMI

RMI最初在JDK 1.1被引入到Java平台中，它为Java开发者提供了一种强大的方法来实现Java程序间的交互。在RMI之前，对于Java开发者来说，远程调用的唯一选择就是CORBA（在当时，需要购买一种第三方产品，叫作Object Request Broker[ORB]），或者手工编写Socket程序。

但是开发和访问RMI服务是非常乏味无聊的，它涉及到好几个步骤，包括程序的和手工的。Spring简化了RMI模型，它提供了一个代理工厂bean，能让我们把RMI服务像本地JavaBean那样装配到我们的Spring应用中。Spring还提供了一个远程导出器，用来简化把Spring管理的bean转换为RMI服务的工作。

#### 15.2.1 导出RMI服务

创建RMI服务会涉及如下几个步骤：

1. 编写一个服务实现类，类中的方法必须抛出`java.rmi.RemoteException`异常
2. 创建一个继承于`java.rmi.Remote`的服务接口
3. 运行RMI编译器（rmic），创建客户端stub类和服务端skeleton类
4. 启动一个RMI注册表，以便持有这些服务
5. 在RMI注册表中注册服务

除了这些必需的步骤外，我们注意到，程序还会抛出相当多的`RemoteException`和`MalformedURLException`异常。虽然这些异常通常意味着一个无法从`catch`代码块中恢复的致命错误，但是我们仍然需要编写样板式的代码来捕获并处理这些异常——即使我们不能修复它们。

**Spring中配置RMI服务**

幸运的是，Spring提供了更简单的方式来发布RMI服务，不用再编写那些需要抛出`RemoteException`异常的特定RMI类，只需简单地编写实现服务功能的POJO就可以了，Spring会处理剩余的其他事项。

我们将要创建的RMI服务需要发布`SpitterService`接口中的方法：

```java
package spittr.service;

import java.util.List;

import spittr.domain.Spitter;
import spittr.domain.Spittle;

public interface SpitterService {
    
    List<Spittle> getRecentSpittles(int count);
    void saveSpittle(Spittle spittle);
    void saveSpitter(Spitter spitter);
    Spitter getSpitter(long id);
    Spitter getSpitter(String username);
    void startFollowing(Spitter follower, Spitter followee);
    List<Spittle> getSpittlesForSpitter(Spitter spitter);
    List<Spittle> getSpittlesForSpitter(String username);
    Spittle getSpittleById(long id);
    void deleteSpittle(long id);
    List<Spitter> getAllSpitters();
}
```

如果我们使用传统的RMI来发布此服务，`SpitterService`和`SpitterServiceImpl`中的所有方法都需要抛出`java.rmi.RemoteException`。但是如果我们使用Spring的`RmiServiceExporter`把该类转变为RMI服务，那现有的实现不需要做任何改变。

`RmiServiceExporter`可以把任意Spring管理的bean发布为RMI服务。如下图所示，`RmiServiceExporter`把bean包装在一个适配器类中，然后适配器类被绑定到RMI注册表中，并且代理到服务类的请求，在本例中服务类就是`SpitterServiceImpl`。

<center>
    ![图15.4-RmiServiceExporter的工作](images\图15.4-RmiServiceExporter的工作.PNG)
    **RmiServiceExporter把POJO包装到服务适配器中，并将服务适配器绑定到RMI注册表中，从而将POJO转换为RMI服务**
</center>

使用`RmiServiceExporter`将`SpitterServiceImpl`发布为RMI服务的最简单方式是将其注册为bean：

```java
@Bean
public RmiServiceExporter rmiExporter(SpitterService spitterService) {
    RmiServiceExporter rmiExporter = new RmiServiceExporter();
    rmiExporter.setService(spitterService);
    rmiExporter.setServiceName("SpitterService");
    rmiExporter.setServiceInterface(SpitterService.class);
    return rmiExporter;
}
```

这里会把`spitterService` bean设置到`service`属性中，表明`RmiServiceExporter`要把该bean发布为一个RMI服务。`serviceName`属性命名了RMI服务，`serviceInterface`属性指定了此服务所实现的接口。

默认情况下，`RmiServiceExporter`会尝试绑定到本地机器1099端口上的RMI注册表。如果在这个端口没有发现RMI注册表，`RmiServiceExporter`将会启动一个注册表。如果希望绑定到不同端口或主机上的RMI注册表，那么我们可以通过`registryPort`和`registryHost`属性来指定。例如，下面的`RmiServiceExporter`会尝试绑定rmi.spitter.com主机1199端口上的RMI注册表：

```java
@Bean
public RmiServiceExporter rmiExporter(SpitterService spitterService) {
    RmiServiceExporter rmiExporter = new RmiServiceExporter();
    rmiExporter.setService(spitterService);
    rmiExporter.setServiceName("SpitterService");
    rmiExporter.setServiceInterface(SpitterService.class);
    rmiExporter.setRegistryHost("rmi.spitter.com");
    rmiExporter.setRegistryPort(1199);
    return rmiExporter;
}
```

这就是我们使用Spring把某个bean转变为RMI服务所需要做的全部工作。现在`SpitterService`已经导出为RMI服务，我们可以为Spittr应用创建其他的用户界面或邀请第三方使用此RMI服务创建新的客户端。如果使用Spring，客户端开发者访问Spitter的RMI服务会非常容易。

#### 15.2.2 装配RMI服务

传统上，RMI客户端必须使用RMI API的`Naming`类从RMI注册表中查找服务，例如：

```java
try {
    String serviceUrl = "rmi:/spitter/SpitterService";
    SpitterService spitterService = (SpitterService) Naming.lookip(serviceUrl);
    // ...
} catch (RemoteException e) {
    // ...
} catch (NoutBoundException e) {
    // ...
} catch (MalFormedURLException e) {
    // ...
}
```

虽然这段代码可以获取Spitter的RMI服务的引用，但是它存在两个问题：

- 传统的RMI查找可能会导致3种检查型异常的任意一种（`RemoteException`、`NotBoundException`和`MalformedURLException`），这些异常必须被捕获或重新抛出
- 需要`SpitterService`的任何代码都必须自己负责获取该服务。这属于样板代码，与客户端的功能并没有直接关系。

RMI查找过程中所抛出的异常通常意味着应用发生了致命的不可恢复的问题。例如，`MalformedURLException`异常意味着这个服务的地址是无效的。为了从这个异常中恢复，应用至少要重新配置，也可能需要重新编译。`try/catch`代码块并不能在发生异常时优雅地恢复，既然如此，为什么还要强制我们的代码捕获并处理这个异常呢？

更糟糕的事情是这段代码直接违反了依赖注入（DI）原则。因为客户端代码需要负责查找`SpitterService`，并且这个服务是RMI服务，我们甚至没有任何机会去提供`SpitterService`对象的不同实现。理想情况下，应该可以为任意一个bean注入`SpitterService`对象，而不是让bean自己去查找服务。利用DI，使用`SpitterService`的任何客户端都不需要关心此服务来源于何处。

Spring的`RmiProxyFactoryBean`是一个工厂bean，该bean可以为RMI服务创建代理。使用`RmiProxyFactoryBean`引用`SpitterService`的RMI服务是非常简单的，只需要在客户端的Spring配置中增加如下方法：

```java
@Bean
public RmiProxyFactoryBean rmiProxy() {
    RmiProxyFactoryBean rmiProxy = new RmiProxyFactoryBean();
    rmiProxy.setServiceUrl("rmi://localhost/SpitterService");
    rmiProxy.setServiceInterface(SpitterService.class);
    return rmiProxy;
}
```

服务的URL是通过`RmiProxyFactoryBean`的`serviceUrl`属性来设置的，在这里，服务名被设置为`SpitterService`，并且声明服务是在本地机器上的。同时，服务提供的接口由`serviceInterface`属性来指定。


下图展示了客户端和RMI代理的交互：

<center>
    ![图15.5-客户端和RMI代理的交互](images\图15.5-客户端和RMI代理的交互.PNG)
    **RmiProxyFactoryBean生成一个代理对象，该对象代表客户端来负责与远程的RMI服务进行通信。客户端通过服务的接口与代理进行交互，就如同远程服务就是一个本地的POJO**
</center>

注意，`RmiProxyFactoryBean`应该被配置在客户端，而`RmiServiceExporter`以及`SpitterServiceImpl`应该被配置在服务端。

服务端配置：

```java
package spittr.config.server;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.remoting.rmi.RmiServiceExporter;

import spittr.service.SpitterService;
import spittr.service.impl.SpitterServiceImpl;

@Configuration
public class RmiServerConfig {
    
    @Bean
    public RmiServiceExporter rmiExporter(SpitterService spitterService) {
        RmiServiceExporter rmiExporter = new RmiServiceExporter();
        rmiExporter.setService(spitterService);
        rmiExporter.setServiceName("SpitterService");
        rmiExporter.setServiceInterface(SpitterService.class);
        return rmiExporter;
    }
    
    @Bean
    public SpitterService spitterService() {
        return new SpitterServiceImpl();
    }
}
```

客户端配置：

```java
package spittr.config.client;

import org.springframework.context.annotation.Bean;
import org.springframework.remoting.rmi.RmiProxyFactoryBean;

import spittr.service.SpitterService;

@Configuration
public class RmiClientConfig {
    @Bean
    public RmiProxyFactoryBean rmiProxy() {
        RmiProxyFactoryBean rmiProxy = new RmiProxyFactoryBean();
        rmiProxy.setServiceUrl("rmi://localhost:1099/SpitterService");
        rmiProxy.setServiceInterface(SpitterService.class);
        return rmiProxy;
    }
}
```

现在已经把RMI服务声明为Spring管理的bean，我们就可以把它作为依赖装配进另一个bean中，就像任意非远程的bean那样。例如，假设客户端需要使用`SpitterService`为指定的用户获取Spittle列表，我们可以使用`@Autowired`注解把服务代理装配进客户端中：

```java
@Autowired
private SpitterService spitterService;
```

也可以像本地bean一样调用它的方法：

```java
public List<Spittle> getSpittles(String username) {
    Spitter spitter = spitterService.getSpitter(username);
    return spitterService.getSpittlesForSpitter(spitter);
}
```

这样客户端代码甚至不需要知道所处理的是一个RMI服务。它只是通过注入机制接受了一个`SpitterService`对象，根本不必关心它来自何处。

此外，代理捕获了这个服务所有可能抛出的`RemoteException`异常，并把它包装为运行期异常重新抛出，这样我们就可以放心地忽略这些异常。我们也可以非常容易地把远程服务bean替换为该服务的其他实现——或许是不同的远程服务，或者可能是客户端代码单元测试时的一个mock实现。

RMI是一种实现远程服务交互的好办法，但是它存在某些限制。首先，RMI很难穿越防火墙，这是因为RMI使用任意端口来交互——这是防火墙通常所不允许的。在企业内部网络环境中，我们通常不需要担心这个问题。但是如果在互联网上运行，我们用RMI可能会遇到麻烦。即使RMI提供了对HTTP的通道的支持（通常防火墙都允许），但是建立这个通道也不是件容易的事。

另外一件需要考虑的事情是RMI是基于Java的。这意味着客户端和服务端必须都是用Java开发的。因为RMI使用了Java的序列化机制，所以通过网络传输的对象类型必须要保证在调用两端的Java运行时中是完全相同的版本。对我们的应用而言，这可能是个问题，也可能不是问题。但是选择RMI做远程服务时，必须要牢记这一点。

### 15.3 使用Hessian和Burlap发布远程服务

Hessian和Burlap是Caucho Technology提供的两种基于HTTP的轻量级远程服务解决方案。借助于尽可能简单的API和通信协议，它们都致力于简化Web服务。

为什么Caucho对同一个问题会有两种解决方案？Hessian和Burlap就如同一个事物的两面，但是每一个解决方案都服务于略微不同的目的。

Hessian，像RMI一样，使用二进制消息进行客户端和服务端的交互。但与其他二进制远程调用技术（例如RMI）不同的是，它的二进制消息可以移植到其他非Java的语言中，包括PHP、Python、C++和C#。

Burlap是一种基于XML的远程调用技术，这使得它可以自然而然地移植到任何能够解析XML的语言上。正因为它基于XML，所以相比起Hessian的二进制格式而言，Burlap可读性更强。但是和其他基于XML的远程技术（例如SOAP或XML-RPC）不同，Burlap的消息结构尽可能的简单，不需要额外的外部定义语言（例如WSDL或IDL）。

很大程度上，它们是一样的。唯一的区别在于Hessian的消息是二进制的，而Burlap的消息是XML。由于Hessian的消息是二进制的，所以它在带宽上更具优势。但是如果我们更注重可读性（如出于调试的目的）或者我们的应用需要与没有Hessian实现的语言交互，那么Burlap的XML消息会是更好的选择。

#### 15.3.1 使用Hessian和Burlap导出bean的功能

*以下内容代码在工程sia4e-P4_Integrating_Spring-C15_Working_with_remote_services-02_HessianClient及sia4e-P4_Integrating_Spring-C15_Working_with_remote_services-02_HessianServer中*。（测试时报错，Hessian无法连接到/spitter.service，暂未解决）

像之前一样，我们希望把`SpitterServiceImpl`类的功能发布为远程服务——这次是一个Hessian服务。即使没有Spring，编写一个Hessian服务也是相当容易的。我们只需要编写一个继承`com.caucho.hessian.server.HessianServlet`的类，并确保所有的服务方法是`public`的（在Hessian里，所有`public`方法被视为服务方法）。

因为Hessian服务很容易实现，Spring并没有做更多简化Hessian模型的工作。但是和Spring一起使用时，Hessian服务可以在各方面利用Spring框架的优势，这是纯Hessian服务所不具备的。包括利用Spring的AOP来为Hessian服务提供系统级服务，例如声明式事务。

**导出Hessian服务**

在Spring中导出一个Hessian服务和在Spring中实现一个RMI服务十分类似。为了把Spitter服务发布为Hessian服务，我们需要配置另一个导出bean，只不过这次是`HessianServiceExporter`。

`HessianServiceExporter`对Hessian服务所执行的功能与`RmiServiceExporter`对RMI服务所执行的功能是相同的：它把POJO的public方法发布成Hessian服务的方法。

<cennter>
    ![图15.6-HessianServiceExporter将Hessian请求转为对POJO的调用](images\图15.6-HessianServiceExporter将Hessian请求转为对POJO的调用.PNG)
    **HessianServiceExporter是一个Spring MVC控制器，它可以接收Hessian请求，并把这些请求转换成对POJO的调用从而将POJO导出为一个Hessian服务**
</cennter>

如上图所示，`HessianServiceExporter`是一个Spring MVC控制器，它接收Hessian请求，并将这些请求转换成对被导出POJO的方法调用。在如下Spring的声明中，`HessianServiceExporter`会把`spitterService` bean导出为Hessian服务：

```java
@Bean
public HessianServiceExporter hessianExporter(SpitterService spitterService) {
    HessianServiceExporter exporter = new HessianServiceExporter();
    exporter.setService(spitterService);
    exporter.setServiceInterface(SpitterService.class);
    return exporter;
}
```

这里`service`属性的值被设置为实现了这个服务的bean引用。在这里，它引用的是`spitterService` bean。`serviceInterface`属性用来标识这个服务实现了`SpitterService`接口。

与`RmiServiceExporter`不同的是，我们不需要设置`serviceName`属性。在RMI中，`serviceName`属性用来在RMI注册表中注册一个服务。而Hessian没有注册表，因此也就没必要为Hessian服务进行命名。

**配置Hessian控制器**

`RmiServiceExporter`和`HessianServiceExporter`另外一个主要区别就是，由于Hessian是基于HTTP的，所以`HessianSeriviceExporter`实现为一个Spring MVC控制器。这意味着为了使用导出的Hessian服务，我们需要执行两个额外的配置步骤：

- 配置`DispatcherServlet`，并把我们的应用部署为Web应用
- 在Spring的配置文件中配置一个URL处理器，把Hessian服务的URL分发给对应的Hessian服务bean

`SpittrWebAppInitializer`：

```java
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
        return new String[] { "/", "*.service" };
    }

}
```

经过上述配置后，任何以“.service”结束的URL请求都将由`DispatcherServlet`处理，它会把请求传递给匹配这个URL的控制器。例如，将“/spitter.service”请求交给`hessianSpitterService` bean所处理（它实际上仅仅是一个`SpitterServiceImpl`的代理）。

那我们是如何知道这个请求会转给`hessianSpitterSevice`处理呢？我们还需要配置一个URL映射来确保DispatcherServlet把请求转给`hessianSpitterService`。使用`SimpleUrlHandlerMapping` bean可以做到这一点：

```java
@Bean
public HandlerMapping hessianMapping() {
    SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
    Properties mappings = new Properties();
    mappings.setProperty("/spitter.service", "hessianExportedSpitterService");
    mapping.setMappings(mappings);
    return mapping;
}
```

经过上述配置，此时请求“/spitter.service”会交给一个名为“hessianExportedSpitterService”的bean处理，即我们配置的`HessianServiceExporter`。

在客户端中我们同样要注册工厂bean：

```java
@Bean
public HessianProxyFactoryBean spitterService(){
    HessianProxyFactoryBean proxyFactoryBean = new HessianProxyFactoryBean();
    proxyFactoryBean.setServiceUrl("http://localhost:8080/sia4e-P4_Integrating_Spring-C15_Working_with_remote_services-02_HessianServer/spitter.service");
    proxyFactoryBean.setServiceInterface(SpitterService.class);
    return proxyFactoryBean;
}
```

**导出Burlap服务**

事实上，在Spring 4中，Burlap的相关类，例如`BurlapServiceExporter`已经废弃了：

>
* @deprecated as of Spring 4.0, since Burlap hasn't evolved in years
* and is effectively retired (in contrast to its sibling Hessian)

Spring推荐我们使用Hessian，所以这里不再详述Burlap。

### 15.4 使用Spring的HttpInvoker

Spring开发团队意识到RMI服务和基于HTTP的服务（例如Hessian和Burlap）之间的空白。一方面，RMI使用Java标准的对象序列化机制，但是很难穿透防火墙。另一方面，Hessian和Burlap能很好地穿透防火墙，但是使用私有的对象序列化机制。

就这样，Spring的HTTP invoker应运而生了。HTTP invoker是一个新的远程调用模型，作为Spring框架的一部分，能够执行基于HTTP的远程调用（让防火墙不为难），并使用Java的序列化机制（让开发者也乐观其变）。使用基于HTTP invoker的服务和使用基于Hessian/Burlap的服务非常相似。

#### 15.4.1 将bean导出为HTTP服务

*以下内容代码在工程sia4e-P4_Integrating_Spring-C15_Working_with_remote_services-03_HttpInvokerClient及sia4e-P4_Integrating_Spring-C15_Working_with_remote_services-03_HttpInvokerServer中*。（与Hessian相同，连接出错，暂未解决）

这里我们要使用的是`HttpInvokerServiceExporter`。

为了把Spitter服务导出为一个基于HTTP invoker的服务，我们需要像下面的配置一样声明一个`HttpInvokerServiceExporter`：

```java
@Bean
public HttpInvokerServiceExporter httpExporter(SpitterService service) {
    HttpInvokerServiceExporter exporter = new HttpInvokerServiceExporter();
    exporter.setService(service);
    exporter.setServiceInterface(SpitterService.class);
    return exporter;
}
```

`HttpInvokerServiceExporter`的工作方式与`HessianServiceExporter`很相似。`HttpInvokerServiceExporter`也是一个SpringMVC控制器，它通过`DispatcherServlet`接收来自与客户端的请求，并将这些请求转换成对实现服务的POJO的方法调用。

<center>
    ![图15.7-HttpInvokerServiceExporter工作方式](images\图15.7-HttpInvokerServiceExporter工作方式.PNG)
    **HttpInvokerServiceExporter工作方式与Hessian很相似，通过Spring MVC的DispatcherServlet接收请求，并将这些请求转换成对Spring bean的方法调用**
</center>

因为`HttpInvokerServiceExporter`是一个Spring MVC控制器，我们需要建立一个URL处理器，映射HTTP URL到对应的服务上，就像Hessian导出器所做的一样：

```java
@Bean
public HandlerMapping httpMapping() {
    SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
    Properties mappings = new Properties();
    mappings.setProperty("/spitter.service", "httpExporter");
    mapping.setMappings(mappings);
    return mapping;
}
```

同样地，我们还需要配置`DispatcherServlet`，使其能够处理对“*.service”的请求。

#### 15.4.2 通过HTTP访问服务

访问基于HTTP invoker的服务很类似于我们之前使用的其他远程服务代理。实际上就是一样的。如下图所示：

<center>
    ![图15.9-HttpInvokerProxyFactoryBean工作方式](images\图15.9-HttpInvokerProxyFactoryBean工作方式.PNG)
    **HttpInvokerProxyFactoryBean是一个代理工厂bean，用于生成一个代理，该代理使用Spring特有的基于HTTP协议进行远程通信**
</center>

为了把基于HTTP invoker的远程服务装配进我们的客户端Spring应用上下文中，我们必须将 `HttpInvokerProxyFactoryBean`配置为一个bean来代理它，如下所示：

```java
@Bean
public HttpInvokerProxyFactoryBean spitterService() {
    HttpInvokerProxyFactoryBean proxy = new HttpInvokerProxyFactoryBean();
    proxy.setServiceUrl("http://localhost:8080/sia4e-P4_Integrating_Spring-C15_Working_with_remote_services-03_HttpInvokerServer/spitter.service");
    proxy.setServiceInterface(SpitterService.class);
    return proxy;
}
```

Spring的HTTP invoker是作为两全其美的远程调用解决方案而出现的，把HTTP的简单性和Java内置的对象序列化机制融合在一起。这使得HTTP invoker服务成为一个引人注目的替代RMI或Hessian/Burlap的可选方案。

但是，HTTP invoker也有一个重大限制：它只是一个Spring框架所提供的远程调用解决方案。这意味着客户端和服务端必须都是Spring应用。并且，至少目前而言，也隐含表明客户端和服务端必须是基于Java的。另外，因为使用了Java的序列化机制，客户端和服务端必须使用相同版本的类（与RMI类似）。

### 15.5 发布和使用Web服务

（关于JAX-WS的内容，这里暂略）

### 15.6 小结

>
使用远程服务通常是一个乏味的苦差事，但是Spring提供了对远程服务的支持，让使用远程服务与使用普通的JavaBean一样简单。
>
在客户端，Spring提供了代理工厂bean，能让我们在Spring应用中配置远程服务。不管是使用RMI、Hessian、Burlap、Spring的HTTPinvoker，还是Web服务，都可以把远程服务装配进我们的应用中，好像它们就是POJO一样。Spring甚至捕获了所有的RemoteExecption异常，并在发生异常的地方重新抛出运行期异常RemoteAccessException，让我们的代码可以从处理不可恢复的异常中解放出来。
>
即便Spring隐藏了远程服务的很多细节，让它们表现得好像是本地JavaBean一样，但是我们应该时刻谨记它们是远程服务的事实。远程服务，本质上来讲，通常比本地服务更低效。当编写访问远程服务的代码时，我们必须考虑到这一点，限制远程调用，以规避性能瓶颈。
>
在本章，我们看到了Spring是如何使用几种基本的远程调用技术来发布和使用服务的。尽管这些远程调用方案在分布式应用中很有价值，但这只是涉及面向服务架构（SOA）的一鳞半爪。
>
我们还了解了如何将bean导出为基于SOAP的Web服务。尽管这是开发Web服务的一种简单方式，但从架构角度来看，它可能不是最佳的选择。在下一章，我们将学习构建分布式应用的另一种选择，把应用暴露为RESTful资源。

## 第十六章 使用Spring MVC创建REST API

本章内容：

- 编写处理REST资源的控制器
- 以XML、JSON及其他格式来表述资源
- 使用REST资源

作为开发人员，我们经常关注于构建伟大的软件来解决业务问题。数据只是软件完成工作时要处理的原材料。但是如果你问一下业务人员，数据和软件谁更重要的话，他们很可能会选择数据。数据是许多业务的生命之血。软件通常是可以替换的，但是多年积累的数据是永远不能替换的。

你是不是觉得有些奇怪，既然数据如此重要，为何在开发软件的时候却经常将其视为事后才考虑的事情？以我们前面上一章所介绍的远程服务为例，这些服务是以操作和处理为中心的，而不是信息和资源。

近几年来，以信息为中心的表述性状态转移（Representational StateTransfer，REST）已成为替换传统SOAP Web服务的流行方案。SOAP一般会关注行为和处理，而REST关注的是要处理的数据。

从Spring 3.0版本开始，Spring为创建REST API提供了良好的支持。Spring的REST实现在Spring 3.1、3.2和如今的4.0版本中不断得到发展。

Spring对REST的支持是构建在Spring MVC之上的，所以我们已经了解了许多在Spring中使用REST所需的知识。在本章中，我们将基于已了解的Spring MVC知识来开发处理RESTful资源的控制器。但在深入了解细节之前，先让我们看看使用REST到底是什么。

### 16.1 了解REST

我敢打赌这并不是你第一次听到或读到REST这个词。近些年来，关于REST已经有了许多讨论，在软件开发中你可能会发现有一种很流行的做法，那就是在推动REST替换SOAP Web服务的时候，会谈论到SOAP的不足。

诚然，对于许多应用程序而言，使用SOAP可能会有些大材小用了，而REST提供了一个更简单的可选方案。另外，很多的现代化应用都会有移动或富JavaScript客户端，它们都会使用运行在服务器上REST API。

问题在于并不是每个人都清楚REST到底是什么。结果就出现了许多误解。有很多打着REST幌子的事情其实并不符合REST真正的本意。在谈论Spring如何支持REST之前，我们需要对REST是什么达成共识。

#### 16.1.1 REST的基础知识

当谈论REST时，有一种常见的错误就是将其视为“基于URL的Web服务”——将REST作为另一种类型的远程过程调用（remote procedure call，RPC）机制，就像SOAP一样，只不过是通过简单的HTTP URL来触发，而不是使用SOAP大量的XML命名空间。

恰好相反，REST与RPC几乎没有任何关系。RPC是面向服务的，并关注于行为和动作；而REST是面向资源的，强调描述应用程序的事物和名词。

为了理解REST是什么，我们将它的首字母缩写拆分为不同的构成部分：

- 表述性（Representational）：REST资源实际上可以用各种形式来进行表述，包括XML、JSON（JavaScript Object Notation）甚至HTML
- 状态（State）：当使用REST的时候，我们更关注资源的状态而不是对资源采取的行为
- 转移（Transfer）：REST涉及到转移资源数据，它以某种表述性形式从一个应用转移到另一个应用

更简洁地讲，REST就是将资源的状态以最适合客户端或服务端的形式从服务器端转移到客户端（或者反过来）。

在REST中，资源通过URL进行识别和定位。至于RESTful URL的结构并没有严格的规则，但是URL应该能够识别资源，而不是简单的发一条命令到服务器上。再次强调，关注的核心是事物，而不是行为。

REST中会有行为，它们是通过HTTP方法来定义的。具体来讲，也就是`GET`、`POST`、`PUT`、`DELETE`、`PATCH`以及其他的HTTP方法构成了REST中的动作。这些HTTP方法通常会匹配为如下的CRUD动作：

- Create: POST
- Read: GET
- Update: PUT或PATCH
- Delete: DELETE

尽管通常来讲，HTTP方法会映射为CRUD动作，但这并不是严格的限制。有时候，PUT可以用来创建新资源，POST可以用来更新资源。实际上，POST请求非幂等性（non-idempotent）的特点使其成为一个非常灵活的方法，对于无法适应其他HTTP方法语义的操作，它都能够胜任。

基于对REST的这种观点，这里我们尽量避免使用诸如REST服务、REST Web服务或类似的术语，这些术语会不恰当地强调行为。相反，我们强调REST面向资源的本质，并讨论RESTful资源。

#### 16.1.2 Spring是如何支持REST的

Spring很早就有导出REST资源的需求。从3.0版本开始，Spring针对Spring MVC的一些增强功能对REST提供了良好的支持。当前的4.0版本中，Spring支持以下方式来创建REST资源：

- 控制器可以处理所有的HTTP方法，包含四个主要的REST方法：GET、PUT、DELETE以及POST。Spring 3.2及以上版本还支持PATCH方法；
- 借助`@PathVariable`注解，控制器能够处理参数化的URL（将变量输入作为URL的一部分）；
- 借助Spring的视图和视图解析器，资源能够以多种方式进行表述，包括将模型数据渲染为XML、JSON、Atom以及RSS的View实现；
- 可以使用`ContentNegotiatingViewResolver`来选择最适合客户端的表述；
- 借助`@ResponseBody`注解和各种`HttpMethodConverter`实现，能够替换基于视图的渲染方式；
- 类似地，`@RequestBody`注解以及`HttpMethodConverter`实现可以将传入的HTTP数据转化为传入控制器处理方法的Java对象；
- 借助`RestTemplate`，Spring应用能够方便地使用REST资源。

本章中，我们将会介绍Spring RESTful的所有特性，首先介绍如何借助Spring MVC生成资源。然后在16.4小节中，我们会转向REST的客户端，看一下如何使用这些资源。那么，就从了解RESTful SpringMVC控制器是什么样子开始吧。

### 16.2 创建第一个REST端点

从第5章到第7章中，我们学到了创建Web应用的知识，它们可以用在通过REST API暴露资源上。首先，我们会在名为`SpittleApiController`的新控制器中创建第一个REST端点。

```java
@RequestMapping(method = RequestMethod.GET)
public List<Spittle> spittles(@RequestParam(value = "max", defaultValue = MAX_LONG_AS_STRING) long max,
        @RequestParam(value = "count", defaultValue = "20") int count, Model model) {

    return spittleRepository.findSpittles(max, count));
}
```

实际上，现在我们并不能看出它服务于一个REST资源而不是Web页面。按照这个控制器的写法，并没有地方表明它是RESTful、服务于资源的控制器。

当发起对“/spittles”的GET请求时，将会调用`spittles()`方法。它会查找并返回Spittle列表，而这个列表会通过注入的`SpittleRepository`获取到。列表会放到模型中，用于视图的渲染。对于基于浏览器的Web应用，这可能意味着模型数据会渲染到HTML页面中。

但是，我们现在讨论的是创建REST API。在这种情况下，HTML并不是合适的数据表述形式。

表述是REST中很重要的一个方面。它是关于客户端和服务器端针对某一资源是如何通信的。任何给定的资源都几乎可以用任意的形式来进行表述。如果资源的使用者愿意使用JSON，那么资源就可以用JSON格式来表述。如果使用者喜欢尖括号，那相同的资源可以用XML来进行表述。同时，如果用户在浏览器中查看资源的话，可能更愿意以HTML的方式来展现（或者PDF、Excel及其他便于人类阅读的格式）。资源没有变化——只是它的表述方式变化了。

当然，如果内容要由人类用户来使用的话，那么我们可能需要支持HTML格式的资源。根据资源的特点和应用的需求，我们还可能选择使用PDF文档或Excel表格来展现资源。

对于非人类用户的使用者，比如其他的应用或调用REST端点的代码，资源表述的首选应该是XML和JSON。借助Spring同时支持这两种方案非常简单，所以没有必要做一个非此即彼的选择。

这里，我们至少要支持JSON，JSON使用起来和XML一样简单，并且如果客户端是JavaScript（最近一段时间以来，这种做法越来越常见）的话，JSON更是会成为优胜者，因为在JavaScript中使用JSON数据根本就不需要编排和解排（marshaling/demarshaling）。

需要了解的是控制器本身通常并不关心资源如何表述。控制器以Java对象的方式来处理资源。控制器完成了它的工作之后，资源才会被转化成最适合客户端的形式。

Spring提供了两种方法将资源的Java表述形式转换为发送给客户端的表述形式：

- 内容协商（Content negotiation）：选择一个视图，它能够将模型渲染为呈现给客户端的表述形式；
- 消息转换器（Message conversion）：通过一个消息转换器将控制器所返回的对象转换为呈现给客户端的表述形式。

#### 16.2.1 协商资源表述

*以下内容代码在工程sia4e-P4_Integrating_Spring-C16_Creating_REST_APIs_with_Spring_MVC-01_Content_Negotiation中*。

在之前的学习中，当控制器的处理方法完成时，通常会返回一个逻辑视图名。如果方法不直接返回逻辑视图名（例如方法返回void）那么逻辑视图名会根据请求的URL判断得出（或通过`HttpServletRequest`进行重定向或通过`HttpServletResponse`进行请求转发）。DispatcherServlet接下来会将视图的名字传递给一个视图解析器，要求它来帮助确定应该用哪个视图来渲染请求结果。

在面向人类访问的Web应用程序中，选择的视图通常来讲都会渲染为HTML。视图解析方案是个简单的一维活动。如果根据视图名匹配上了视图，那这就是我们要用的视图了。

当要将视图名解析为能够产生资源表述的视图时，我们就有另外一个维度需要考虑了。视图不仅要匹配视图名，而且所选择的视图要适合客户端。如果客户端想要JSON，那么渲染HTML的视图就不行了，尽管此时视图名可能匹配。

Spring的`ContentNegotiatingViewResolver`是一个特殊的视图解析器，它考虑到了客户端所需要的内容类型。按照其最简单的形式，`ContentNegotiatingViewResolver`可以按照下述形式进行配置：

```java
@Bean
public ViewResolver cnViewResolver() {
    return new ContentNegotiatingViewResolver();
}
```

在这个简单的bean声明背后会涉及到很多事情。要理解`ContentNegotiatingViewResolver`是如何工作的，这涉及内容协商的两个步骤：

1. 确定请求的媒体类型
2. 找到适合请求媒体类型的最佳视图

**确定请求的媒体类型**

在内容协商两步骤中，第一步是确定客户端想要什么类型的内容表述。表面上看，这似乎是一个很简单的事情。难道请求的Accept头部信息不是已经很清楚地表明要发送什么样的表述给客户端吗？

遗憾的是，Accept头部信息并不总是可靠的。如果客户端是Web浏览器，那并不能保证客户端需要的类型就是浏览器在Accept头部所发送的值。Web浏览器一般只接受对人类用户友好的内容类型（如text/html），所以没有办法（除了面向开发人员的浏览器插件）指定不同的内容类型。

`ContentNegotiatingViewResolver`将会考虑到Accept头部信息并使用它所请求的媒体类型，但是它会首先查看URL的文件扩展名。如果URL在结尾处有文件扩展名的话，`ContentNegotiatingViewResolver`将会基于该扩展名确定所需的类型。如果扩展名是“.json”的话，那么所需的内容类型必须是“application/json”。如果扩展名是“.xml”，那么客户端请求的就是“application/xml”。当然，“.html”扩展名表明客户端所需的资源表述为HTML（text/html）。

如果根据文件扩展名不能得到任何媒体类型的话，那就会考虑请求中的Accept头部信息。在这种情况下，Accept头部信息中的值就表明了客户端想要的MIME类型，没有必要再去查找了。

最后，如果没有Accept头部信息，并且扩展名也无法提供帮助的话，`ContentNegotiatingViewResolver`将会使用“/”作为默认的内容类型，这就意味着客户端必须要接收服务器发送的任何形式的表述。

一旦内容类型确定之后，`ContentNegotiatingViewResolver`就该将逻辑视图名解析为渲染模型的View。与Spring的其他视图解析器不同，`ContentNegotiatingViewResolver`本身不会解析视图。而是委托给其他的视图解析器，让它们来解析视图。

`ContentNegotiatingViewResolver`要求其他的视图解析器将逻辑视图名解析为视图。解析得到的每个视图都会放到一个列表中。这个列表装配完成后，`ContentNegotiatingViewResolver`会循环客户端请求的所有媒体类型，在候选的视图中查找能够产生对应内容类型的视图。第一个匹配的视图会用来渲染模型。

在上述的选择过程中，我们阐述了确定所请求媒体类型的默认策略。但是通过为其设置一个`ContentNegotiationManager`，我们能够改变它的行为。借助`ContentNegotiationManager`我们所能做到的事情如下所示：

- 指定默认的内容类型，如果根据请求无法得到内容类型的话，将会使用默认值
- 通过请求参数指定内容类型
- 忽视请求的Accept头部信息
- 将请求的扩展名映射为特定的媒体类型
- 将JAF（Java Activation Framework）作为根据扩展名查找媒体类型的备用方案

配置`ContentNegotiationManager`的方法有三种：

- 直接声明一个`ContentNegotiationManager`类型的bean
- 通过`ContentNegotiationManagerFactoryBean`间接创建
- 重载`WebMvcConfigurerAdapter`的`configureContentNegotiation()`方法

直接创建`ContentNegotiationManager`有一些复杂，除非有充分的原因，否则我们不会愿意这样做。后两种方案能够让创建`ContentNegotiationManager`更加简单。

`ContentNegotiationManager`是在Spring 3.2中引入的。在Spring 3.2之前，`ContentNegotiatingViewResolver`的很多行为都是通过直接设置`ContentNegotiatingViewResolver`的属性进行配置的。从Spring 3.2开始，`ContentNegotiatingViewResolver`的大多数Setter方法都废弃了，并鼓励通过`ContentNegotiationManager`来进行配置。

一般而言，如果我们使用XML配置`ContentNegotiationManager`的话，那最有用的将会是`ContentNegotiationManagerFactoryBean`。例如，我们可能希望在XML中配置`ContentNegotiationManager`使用“application/json”作为默认的内容类型：

```xml
<bean id="contentNegotiationManager" class="org.springframework.web.accept.ContentNegotiationManagerFactoryBean" p:defaultContentType="application/json" />
```

因为`ContentNegotiationManagerFactoryBean`是`FactoryBean`的实现，所以它会创建一个`ContentNegotiationManagerbean`。这个`ContentNegotiationManager`能够注入到`ContentNegotiatingViewResolver`的`contentNegotiationManager`属性中。

如果使用Java配置的话，获得`ContentNegotiationManager`的最简便方法就是扩展`WebMvcConfigurerAdapter`并重载`configureContentNegotiation()`方法。在创建Spring MVC应用的时候，我们很可能已经扩展了`WebMvcConfigurerAdapter`。例如，在Spittr应用中，我们已经有了`WebMvcConfigurerAdapter`的扩展类，名为`WebConfig`，所以需要做的就是重载`configureContentNegotiation()`方法。如下就是`configureContentNegotiation()`的一个实现，它设置了默认的内容类型：

```java
@Override
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.defaultContentType(MediaType.APPLICATION_JSON);
}
```

可以看到，`configureContentNegotiation()`方法给定了一个`ContentNegotiationConfigurer`对象。`ContentNegotiationConfigurer`中的一些方法对应于`ContentNegotiationManager`的Setter方法，这样我们就能在`ContentNegotiationManager`创建时，设置任意内容协商相关的属性。在本例中，我们调用`defaultContentType()`方法将默认的内容类型设置为“application/json”。

现在，我们已经有了`ContentNegotiationManager` bean，接下来就需要将它注入到`ContentNegotiatingViewResolver`的`contentNegotiationManager`属性中：

```java
@Bean
public ViewResolver cnViewResolver(ContentNegotiationManager manager) {
    ContentNegotiatingViewResolver cnvr = new ContentNegotiatingViewResolver();
    cnvr.setContentNegotiationManager(manager);
    return cnvr;
}
```

这个@Bean方法注入了`ContentNegotiationManager`，并使用它调用了`setContentNegotiationManager()`。这样的结果就是`ContentNegotiatingViewResolver`将会使用`ContentNegotiationManager`所定义的行为。

配置`ContentNegotiationManager`有很多的细节，在这里无法对它们进行一一介绍。如下的程序清单是一个非常简单的配置样例，当我使用`ContentNegotiatingViewResolver`的时候，通常会采用这种用法：它默认会使用HTML视图，但是对特定的视图名称将会渲染为JSON输出：

```java
@Bean
public ViewResolver cnViewResolver(ContentNegotiationManager manager) {
    ContentNegotiatingViewResolver cnvr = new ContentNegotiatingViewResolver();
    cnvr.setContentNegotiationManager(manager);
    return cnvr;
}

// 默认为HTML
@Override
public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
    configurer.defaultContentType(MediaType.TEXT_HTML);
}

// 以bean的形式查找视图
@Bean
public ViewResolver beanNameViewResolver() {
    return new BeanNameViewResolver();
}

// 将spittles定义为JSON视图
@Bean
public View spittles() {
    return new MappingJackson2JsonView();
}
```

除了上述内容之外，还应该有一个能够处理HTML的视图解析器（比如`ThymeleafViewResolver`）。在大多数场景下，`ContentNegotiatingViewResolver`会假设客户端需要HTML。但是，如果客户端指定了它想要JSON（通过在请求路径上使用“.json”扩展名或Accept头部信息）的话，那么`ContentNegotiatingViewReslover`将会查找能够处理JSON视图的视图解析器。

如果逻辑视图的名称为“spittles”，那么我们所配置的`BeanNameViewResolver`将会解析`spittles()`方法中所声明的View。这是因为bean名称匹配逻辑视图的名称。如果没有匹配的View的话，`ContentNegotiatingViewResolver`将会采用默认的行为，将其输出为HTML。

**ContentNegotiatingViewResolver的优势与限制**

`ContentNegotiatingViewResolver`的最大优势在于，它在Spring MVC之上构建了REST资源表述层，控制器代码无需更改。相同的一套控制器方法能够为面向人类的用户产生HTML内容，也能针对不是人类的客户端产生JSON或XML。

如果面向人类用户的接口与面向非人类客户端的接口之间有很多重叠的话，那么内容协商是一种很便利的方案。在实践中，面向人类用户的视图与REST API在细节上很少能够处于相同的级别。如果面向人类用户的接口与面向非人类客户端的接口之间没有太多重叠的话，那么`ContentNegotiatingViewResolver`的优势就体现不出来了。

`ContentNegotiatingViewResolver`还有一个严重的限制。作为`ViewResolver`的实现，它只能决定资源该如何渲染到客户端，并没有涉及到客户端要发送什么样的表述给控制器使用。如果客户端发送JSON或XML的话，那么`ContentNegotiatingViewResolver`就无法提供帮助了。

`ContentNegotiatingViewResolver`还有一个相关的小问题，所选中的View会渲染模型给客户端，而不是资源。这里有个细微但很重要的区别。当客户端请求JSON格式的Spittle对象列表时，客户端希望得到的响应可能如下所示：

```json
[
    {
        "id": 42,
        "latitude": 28.419489,
        "longtitude": -81.581184,
        "message": "Hello World!",
        "time": 1400389200000
    },
    {
        "id": 43,
        "latitude": 28.419436,
        "longtitude": -81.577225,
        "message": "Blast off",
        "time": 1400475600000 
    }
]
```

而由于模型是键值对组成的Map，那么响应可能会如下所示：

```json
{
    "spittleList": [
        {
            "id": 42,
            "latitude": 28.419489,
            "longtitude": -81.581184,
            "message": "Hello World!",
            "time": 1400389200000
        },
        {
            "id": 43,
            "latitude": 28.419436,
            "longtitude": -81.577225,
            "message": "Blast off",
            "time": 1400475600000 
        }
    ]
}
```

尽管这不是很严重的问题，但确实可能不是客户端所预期的结果。

因为有这些限制，我们通常建议不要使用`ContentNegotiatingViewResolver`。我更加倾向于使用Spring的消息转换功能来生成资源表述。接下来，我们看一下如何在控制器代码中使用Spring的消息转换器。

#### 16.2.2 使用HTTP信息转换器

*以下内容代码在工程sia4e-P4_Integrating_Spring-C16_Creating_REST_APIs_with_Spring_MVC-02_Message_Converters中*。

消息转换（message conversion）提供了一种更为直接的方式，它能够将控制器产生的数据转换为服务于客户端的表述形式。当使用消息转换功能时，`DispatcherServlet`不再需要那么麻烦地将模型数据传送到视图中。实际上，这里根本就没有模型，也没有视图，只有控制器产生的数据，以及消息转换器（message converter）转换数据之后所产生的资源表述。

Spring提供了各种各样的转换器，这些转换器满足了最常见的将对象转换为表述的需要。

信息转换器 | 描述
-----|-----
`AtomFeedHttpMessageConverter` | Rome Feed对象和Atom feed（媒体类型application/atom+xml）之间的互相转换。如果 Rome 包在类路径下将会进行注册
`BufferedImageHttpMessageConverter` | BufferedImages与图片二进制数据之间互相转换
`ByteArrayHttpMessageConverter` | 读取/写入字节数组。从所有媒体类型（*/*）中读取，并以application/octetstream格式写入 
`FormHttpMessageConverter` | 将application/x-www-form-urlencoded内容读入到MultiValueMap<String,String>中，也会将MultiValueMap<String,String>写入到application/x-www-form- urlencoded中或将MultiValueMap<String, Object>写入到multipart/form-data中 
`Jaxb2RootElementHttpMessageConverter` | 在XML（text/xml或application/xml）和使用JAXB2注解的对象间互相读取和写入。如果 JAXB v2 库在类路径下，将进行注册
`MappingJackson2HttpMessageConverter` | 在JSON和类型化的对象或非类型化的HashMap间互相读取和写入。如果 Jackson 2 JSON 库在类路径下，将进行注册
`MarshallingHttpMessageConverter` | 使用注入的编排器和解排器（marshaller和unmarshaller）来读入和写入XML。支持的编排器和解排器包括Castor、JAXB2、JIBX、XMLBeans以及Xstream
`ResourceHttpMessageConverter` | 读取或写入Resource
`RssChannelHttpMessageConverter` | 在RSS feed和Rome Channel对象间互相读取或写入。如果 Rome 库在类路径下，将进行注册
`SourceHttpMessageConverter` | 在XML和javax.xml.transform.Source对象间互相读取和写入。默认注册
`StringHttpMessageConverter` | 将所有媒体类型（*/*）读取为String。将String写入为text/plain
`XmlAwareFormHttpMessageConverter` | FormHttpMessageConverter的扩展，使用SourceHttp MessageConverter来支持基于XML的部分

为了支持消息转换，我们需要对Spring MVC的编程模型进行一些小调整。

**在响应体中返回资源状态**

正常情况下，当处理方法返回Java对象（除`String`外或`View`的实现以外）时，这个对象会放在模型中并在视图中渲染使用。但是，如果使用了消息转换功能的话，我们需要告诉Spring跳过正常的模型/视图流程，并使用消息转换器。有不少方式都能做到这一点，但是最简单的方法是为控制器方法添加`@ResponseBody`注解。

```java
@RequestMapping(method = RequestMethod.GET, produces = "application/json")
@ResponseBody
public List<Spittle> spittles(@RequestParam(value = "max", defaultValue = MAX_LONG_AS_STRING) long max,
        @RequestParam(value = "count", defaultValue = "20") int count, Model model) {
    return spittleRepository.findSpittles(max, count);
}
```

添加`@ResponseBode`注解，这样就能让Spring将方法返回的`List<Spittle>`转换为响应体。`@ResponseBody`注解会告知Spring，我们要将返回的对象作为资源发送给客户端，并将其转换为客户端可接受的表述形式。更具体地讲，`DispatcherServlet`将会考虑到请求中Accept头部信息，并查找能够为客户端提供所需表述形式的消息转换器。

举例来讲，假设客户端的Accept头部信息表明它接受“application/json”，并且Jackson JSON库位于应用的类路径下，那么此时将会选择`MappingJackson2HttpMessageConverter`。

消息转换器会将控制器返回的Spittle列表转换为JSON文档，并写入响应体。响应大致会如下所示：

```json
[
    {
        "id": 42,
        "latitude": 28.419489,
        "longtitude": -81.581184,
        "message": "Hello World!",
        "time": 1400389200000
    },
    {
        "id": 43,
        "latitude": 28.419436,
        "longtitude": -81.577225,
        "message": "Blast off",
        "time": 1400475600000 
    }
]
```

**Jackson默认会使用反射**

注意在默认情况下，Jackson JSON库在将返回的对象转换为JSON资源表述时，会使用反射。对于简单的表述内容来讲，这没有什么问题。但是如果我们重构了Java类型，比如添加、移除或重命名属性，那么所产生的JSON也将会发生变化（如果客户端依赖这些属性的话，那客户端有可能会出错）。

但是，我们可以在Java类型上使用Jackson的映射注解，从而改变产生JSON的行为。这样我们就能更多地控制所产生的JSON，从而防止它影响到API或客户端。这个部分参见Jackson-Annotations的文档。

上述`spittles()`方法的`@RequstMapping`注解中使用了`produces`属性，表明这个方法只处理预期输出为JSON的请求。也就是说，这个方法只会处理Accept头部信息包含“application/json”的请求。其他任何类型的请求，即使它的URL匹配指定的路径并且是GET请求也不会被这个方法处理。这样的请求会被其他的方法来进行处理（如果存在适当的方法的话），或者返回客户端HTTP 406（Not Acceptable）响应。

**在请求体中接收资源状态**

到目前为止，我们只关注了REST端点如何为客户端提供资源。但是REST并不是只读的，REST API也可以接受来自客户端的资源表述。如果要让控制器将客户端发送的JSON和XML转换为它所使用的Java对象，那是非常不方便的。在处理逻辑离开控制器的时候，Spring的消息转换器能够将对象转换为表述——它们能不能在表述传入的时候完成相同的任务呢？

`@ResponseBody`能够告诉Spring在把数据发送给客户端的时候，要使用某一个消息器，与之类似，`@RequestBody`也能告诉Spring查找一个消息转换器，将来自客户端的资源表述转换为对象。例如，假设我们需要一种方式将客户端提交的新`Spittle`保存起来。我们可以按照如下的方式编写控制器方法来处理这种请求：

```java
@RequestMapping(method=RequestMethod.POST, consumes="application/json")
@ResponseBody
public Spittle saveSpittle(@RequestBody Spittle spittle) {
    return spittleRepository.saveSpittle(spittle);
}
```

如果忽略掉注解的话，那`saveSpittle()`是一个非常简单的方法。它接受一个`Spittle`对象作为参数，并使用`SpittleRepository`进行保存，最终返回`spittleRepository.save()`方法所得到的`Spittle`对象。

但是，通过使用注解，他会变得更加强大。`@RequestMapping`表明它只能处理“/spittles”（在类级别的`@RequestMapping`中进行了声明）的POST请求。POST请求体中预期要包含一个`Spittle`的资源表述。因为`Spittle`参数上使用了`@RequestBody`，所以Spring将会查看请求中的Content-Type头部信息，并查找能够将请求体转换为`Spittle`的消息转换器。

例如，如果客户端发送的`Spittle`数据是JSON表述形式，那么Content-Type头部信息可能就会是“application/json”。在这种情况下，`DispatcherServlet`会查找能够将JSON转换为Java对象的消息转换器。如果Jackson 2库在类路径中，那么`MappingJackson2HttpMessageConverter`将会担此重任，将JSON表述转换为`Spittle`，然后传递到`saveSpittle()`方法中。这个方法还使用了`@ResponseBody`注解，因此方法返回的`Spittle`对象将会转换为某种资源表述，发送给客户端。

注意，`@RequestMapping`有一个`consumes`属性，我们将其设置为“application/json”。`consumes`属性的工作方式类似于`produces`，不过它会关注请求的Content-Type头部信息。它会告诉Spring这个方法只会处理对“/spittles”的POST请求，并且要求请求的Content-Type头部信息为“application/json”。如果无法满足这些条件的话，会由其他方法（如果存在合适的方法的话）来处理请求。

**为控制器默认设置消息转换**

当处理请求时，`@ResponseBody`和`@RequestBody`是启用消息转换的一种简洁和强大方式。但是，如果你所编写的控制器有多个方法，并且每个方法都需要信息转换功能的话，那么这些注解就会带来一定程度的重复性。

Spring 4.0引入了`@RestController`注解，能够在这个方面给我们提供帮助。如果在控制器类上使用`@RestController`来代替`@Controller`的话，Spring将会为该控制器的所有处理方法应用消息转换功能。我们不必为每个方法都添加`@ResponseBody`了。我们所定义的`SpittleController`可能就会如下所示：

```java
@RestController
@RequestMapping("/spittles")
public class SpittleController {

    private SpittleRepository spittleRepository;

    private final String MAX_LONG_AS_STRING = Long.MAX_VALUE + "";

    // 注入SpittleRepository
    @Autowired
    public SpittleController(SpittleRepository spittleRepository) {
        this.spittleRepository = spittleRepository;
    }

    @RequestMapping(method = RequestMethod.GET, produces = "application/json")
    public List<Spittle> spittles(@RequestParam(value = "max", defaultValue = MAX_LONG_AS_STRING) long max,
            @RequestParam(value = "count", defaultValue = "20") int count, Model model) {
        return spittleRepository.findSpittles(max, count);
    }
    
    @RequestMapping(method=RequestMethod.POST, consumes="application/json")
    public Spittle saveSpittle(@RequestBody Spittle spittle) {
        return spittleRepository.saveSpittle(spittle);
    }
}
```

这两个处理器方法都没有使用`@ResponseBody`注解，因为控制器使用了`@RestController`，所以它的方法所返回的对象将会通过消息转换机制，产生客户端所需的资源表述。

到目前为止，我们看到了如何使用Spring MVC编程模型将RESTful资源发布到响应体之中。但是响应除了负载以外还会有其他的内容。头部信息和状态码也能够为客户端提供响应的有用信息。接下来，我们看一下在提供资源的时候，如何填充头部信息和设置状态码。

### 16.3 提供资源之外的其他内容

`@ResponseBody`提供了一种很有用的方式，能够将控制器返回的Java对象转换为发送到客户端的资源表述。实际上，将资源表述发送给客户端只是整个过程的一部分。一个好的REST API不仅能够在客户端和服务器之间传递资源，它还能够给客户端提供额外的元数据，帮助客户端理解资源或者在请求中出现了什么情况。

#### 16.3.1 发送错误信息到客户端

例如，我们为`SpittleController`添加一个新的处理器方法，它会提供单个`Spittle`对象：

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
@ResponseBody
public Spittle spittleById(@PathVariable long id) {
    return spittleRepository.findOne(id);
}
```

在这里，通过id参数传入了一个ID，然后根据它调用Repository的`findOne()`方法，查找`Spittle`对象。处理器方法会返回`findOne()`方法得到的`Spittle`对象，消息转换器会负责产生客户端所需的资源表述。

但是，当通过ID无法找到`Spittle`对象时，`findOne()`方法返回null时会发生什么呢？

结果就是`spittleById()`方法会返回`null`，响应体为空，不会返回任何有用的数据给客户端。同时，响应中默认的HTTP状态码是200（OK），表示所有的事情运行正常。

但是，所有的事情都是不对的。客户端要求`Spittle`对象，但是它什么都没有得到。它既没有收到`Spittle`对象也没有收到任何消息表明出现了错误。服务器实际上是在说：“这是一个没用的响应，但是能够告诉你一切都正常！”

在这种场景下，至少状态码不应该是200，而应该404，告诉客户端，它们所要求的内容没有找到。如果响应体中能够包含错误信息而不是空的话就更好了。

Spring提供了多种方式来处理这样的场景：

- 使用`@ResponseStatus`注解可以指定状态码；
- 控制器方法可以返回`ResponseEntity`对象，该对象能够包含更多响应相关的元数据；
- 异常处理器能够应对错误场景，这样处理器方法就能关注于正常的状况

在这个方面，Spring提供了很多的灵活性，其实也不存在唯一正确的方式。

**使用ResponseEntity对象**

作为`@ResponseBody`的替代方案，控制器方法可以返回一个`ResponseEntity`对象。`ResponseEntity`中可以包含响应相关的元数据（如头部信息和状态码）以及要转换成资源表述的对象。

因为`ResponseEntity`允许我们指定响应的状态码，所以当无法找到`Spittle`的时候，我们可以返回HTTP 404错误。如下是新版本的`spittleById()`，它会返回`ResponseEntity`：

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
@ResponseBody
public ResponseEntity<Spittle> spittleById(@PathVariable long id) {
    Spittle spittle = spittleRepository.findOne(id);
    HttpStatus status = spittle != null ? HttpStatus.OK : HttpStatus.NOT_FOUND;
    return new ResponseEntity<Spittle>(spittle, status);
}
```

像前面一样，路径中得到的ID用来从Repository中检索`Spittle`。如果找到的话，状态码设置为`HttpStatus.OK`（这是之前的默认值），但是如果Repository返回`null`的话，状态码设置为`HttpStatus.NOT_FOUND`，这会转换为HTTP 404。最后，会创建一个新的`ResponseEntity`，它会把`Spittle`和状态码传送给客户端。

注意这个`spittleById()`方法没有使用`@ResponseBody`注解。除了包含响应头信息、状态码以及负载以外，`ResponseEntity`还包含了`@ResponseBody`的语义，因此负载部分将会渲染到响应体中，就像之前在方法上使用`@ResponseBody`注解一样。如果返回`ResponseEntity`的话，那就没有必要在方法上使用`@ResponseBody`注解了。

我们在正确的方向上走出了第一步，如果所要求的Spittle无法找到的话，客户端能够得到一个合适的状态码。但是在本例中，响应体依然为空。我们可能会希望在响应体中包含一些错误信息。

首先，定义一个包含错误信息的`Error`对象

```java
public class Error {
    
    private int code;
    private String message;

    public Error(int code, String message) {
        super();
        this.code = code;
        this.message = message;
    }

    public int getCode() {
        return code;
    }

    public String getMessage() {
        return message;
    }

    @Override
    public String toString() {
        return "Error [code=" + code + ", message=" + message + "]";
    }
}
```

然后修改`spittleById()`，让它返回`Error`对象：

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
public ResponseEntity<?> spittleById(@PathVariable long id) {
    Spittle spittle = spittleRepository.findOne(id);
    if (spittle == null) {
        Error error = new Error(404, "Spittle [" + id + "] not found");
        return new ResponseEntity<Error>(error, HttpStatus.NOT_FOUND);
    } else {
        return new ResponseEntity<Spittle>(spittle, HttpStatus.OK);
    }
}
```

现在，这个方法的行为已经符合我们的预期了。如果找到`Spittle`的话，就会把返回的对象以及200（OK）的状态码封装到`ResponseEntity`中。另一方面，如果`findOne()`返回null的话，将会创建一个`Error`对象，并将其与404（Not Found）状态码一起封装到`ResponseEntity`中，然后返回。

首先，这比我们开始的时候更为复杂。涉及到了更多的逻辑，包括条件语句。另外，方法返回`ResponseEntity<?>`感觉有些问题。`ResponseEntity`所使用的泛型为它的解析或出现错误留下了太多的空间。

**处理错误**

`spittleById()`方法中的if代码块是处理错误的，但这是控制器中错误处理器（error handler）所擅长的领域。错误处理器能够处理导致问题的场景，这样常规的处理器方法就能只关心正常的逻辑处理路径了。

我们重构一下代码来使用错误处理器。首先，定义能够对应`SpittleNotFoundException`的错误处理器：

```java
@ExceptionHandler(SpittleNotFoundException.class)
public ResponseEntity<Error> spittleNotFound(SpittleNotFoundException e) {
    long spittleId = e.getSpittleId();
    Error error = new Error(404, "Spittle [" + spittleId + "] not found");
    return new ResponseEntity<Error>(error, HttpStatus.NOT_FOUND);
}
```

`@ExceptionHandler`注解能够用到控制器方法中，用来处理特定的异常。这里，它表明如果在控制器的任意处理方法中抛出`SpittleNotFoundException`异常，就会调用`spittleNotFound()`方法来处理异常。

`SpittleNotFoundException`：

```java
public class SpittleNotFoundException extends RuntimeException {

    private static final long serialVersionUID = 2618420166836567451L;

    private long spittleId;

    public SpittleNotFoundException(long spittleId) {
        this.spittleId = spittleId;
    }

    public long getSpittleId() {
        return spittleId;
    }
}
```

现在，我们可以移除掉`spittleById()`方法中大多数的错误处理代码：

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
public ResponseEntity<?> spittleById(@PathVariable long id) {
    Spittle spittle = spittleRepository.findOne(id);
    if (spittle == null) {
        throw new SpittleNotFoundException(id);
    } else {
        return new ResponseEntity<Spittle>(spittle, HttpStatus.OK);
    }
}
```

这个版本的`spittleById()`方法确实干净了很多。除了对返回值进行`null`检查，它完全关注于成功的场景，也就是能够找到请求的`Spittle`。同时，在返回类型中，我们能移除掉奇怪的泛型了。

不过，我们能够让代码更加干净一些。现在我们已经知道`spittleById()`将返回`Spittle`并且HTTP状态码始终会是200，那么也就不再需要使用`ResponseEntity`：

```java
@RequestMapping(value = "/{id}", method = RequestMethod.GET)
@ResponseBody
public Spittle spittleById(@PathVariable long id) {
    Spittle spittle = spittleRepository.findOne(id);
    if (spittle == null) {
        throw new SpittleNotFoundException(id);
    } else {
        return spittle;
    }
}
```

同样地，对于错误处理方法始终返回`Error`，并且状态吗为404，那么我们也可以做类似清理：

```java
@ExceptionHandler(SpittleNotFoundException.class)
@ResponseStatus(HttpStatus.NOT_FOUND)
@ResponseBody
public Error spittleNotFound(SpittleNotFoundException e) {
    long spittleId = e.getSpittleId();
    return new Error(404, "Spittle [" + spittleId + "] not found");
}
```

在一定程度上，我们已经圆满达到了想要的效果。为了设置响应状态码，我们首先使用`ResponseEntity`，但是稍后我们借助异常处理器以及`@ResponseStatus`，避免使用`ResponseEntity`，从而让代码更加整洁。

似乎，我们不再需要使用`ResponseEntity`了。但是，有一种场景`ResponseEntity`能够很好地完成，但是其他的注解或异常处理器却做不到。现在，我们看一下如何在响应中设置头部信息。

#### 16.3.2 在响应中设置头部信息

在`saveSpittle()`方法中，我们在处理POST请求的过程中创建了一个新的`Spittle`资源。但是，按照目前的写法，我们无法准确地与客户端交流。

在`saveSpittle()`处理完请求之后，服务器在响应体中包含了`Spittle`的表述以及HTTP状态码200（OK），将其返回给客户端。这里没有什么大问题，但是还不是完全准确。

我们创建了新的内容，HTTP状态码也将这种情况告诉给了客户端。不过，HTTP 201不仅能够表明请求成功完成，而且还能描述创建了新资源。如果我们希望完整准确地与客户端交流，那么响应是不是应该为201（Created），而不仅仅是200（OK）呢？

根据上一节的写法，这个问题解决起来很容易，我们需要做的就是为这个方法添加`@ResponseStatus`注解，如下所示：

```java
@RequestMapping(method = RequestMethod.POST, consumes = "application/json")
@ResponseStatus(HttpStatus.CREATED)
@ResponseBody
public Spittle saveSpittle(@RequestBody Spittle spittle) {
    return spittleRepository.saveSpittle(spittle);
}
```

这应该能够完成我们的任务，现在状态码能够精确反应发生了什么情况。它告诉客户端我们新创建了资源。问题已经得以解决！

但这只是问题的一部分。客户端知道新创建了资源，你觉得客户端会不会感兴趣新创建的资源在哪里呢？毕竟，这是一个新创建的资源，会有一个新的URL与之关联。难道客户端只能猜测新创建资源的URL是什么吗？我们能不能以某种方式将其告诉客户端？

当创建新资源的时候，将资源的URL放在响应的Location头部信息中，并返回给客户端是一种很好的方式。因此，我们需要有一种方式来填充响应头部信息，此时我们的老朋友`ResponseEntity`就能提供帮助了。

如下所示：

```java
@RequestMapping(method = RequestMethod.POST, consumes = "application/json")
public ResponseEntity<Spittle> saveSpittle(@RequestBody Spittle spittle) {
    
    Spittle savedSpittle = spittleRepository.saveSpittle(spittle);
    
    HttpHeaders headers = new HttpHeaders();
    
    URI locationUri = URI.create("http://localhost:8080/spittr/spittles" + savedSpittle.getId());
    headers.setLocation(locationUri);
    
    return new ResponseEntity<Spittle>(savedSpittle, headers, HttpStatus.CREATED);        
    
}
```

在这个新的版本中，我们创建了一个`HttpHeaders`实例，用来存放希望在响应中包含的头部信息值。`HttpHeaders`是`MultiValueMap<String, String>`的特殊实现，它有一些便利的Setter方法（如`setLocation()`），用来设置常见的HTTP头部信息。在得到新创建`Spittle`资源的URL之后，接下来使用这个头部信息来创建`ResponseEntity`。

这里需要注意的是，我们使用硬编码的方式来构建Location头部信息。URL中“localhost”以及“8080”这两个部分尤其需要注意，因为如果我们将应用部署到其他地方，而不是在本地运行的话，它们就不适用了。

事实上，我们没有必要手动构建URL，Spring提供了`UriComponentsBuilder`，可以给我们一些帮助。它是一个构建类，通过逐步指定URL中的各种组成部分（如host、端口、路径以及查询），我们能够使用它来构建`UriComponents`实例。借助`UriComponentsBuilder`所构建的`UriComponents`对象，我们就能获得适合设置给Location头部信息的URI。

为了使用`UriComponentsBuilder`，我们需要做的就是在处理器方法中将其作为一个参数，

```java
@RequestMapping(method = RequestMethod.POST, consumes = "application/json")
public ResponseEntity<Spittle> saveSpittle(@RequestBody Spittle spittle, UriComponentsBuilder uriBuilder) {
    
    Spittle savedSpittle = spittleRepository.saveSpittle(spittle);
    
    HttpHeaders headers = new HttpHeaders();
    
    URI locationUri = uriBuilder.path("/spittles")
                                .path(String.valueOf(savedSpittle.getId()))
                                .build()
                                .toUri();
    
    headers.setLocation(locationUri);
    
    return new ResponseEntity<Spittle>(savedSpittle, headers, HttpStatus.CREATED);        
    
}
```

在处理器方法所得到的`UriComponentsBuilder`中，会预先配置已知的信息如host、端口以及Servlet内容。它会从处理器方法所对应的请求中获取这些基础信息。基于这些信息，代码会通过设置路径的方式构建`UriComponents`其余的部分。

这里路径的构建分为两步。第一步调用`path()`方法，将其设置为“/spittles/”，也就是这个控制器所能处理的基础路径。然后，在第二次调用`path()`的时候，使用了已保存`Spittle`的ID。我们可以推断出来，每次调用`path()`都会基于上次调用的结果。

在路径设置完成之后，调用`build()`方法来构建`UriComponents`对象，根据这个对象调用`toUri()`就能得到新创建`Spittle`的URI。

在REST API中暴露资源只代表了会话的一端。如果发布的API没有人关心和使用的话，那也没有什么价值。通常来讲，移动或JavaScript应用会是REST API的客户端，但是Spring应用也完全可以使用这些资源。我们换个方向，看一下如何编写Spring代码实现RESTful交互的客户端。

### 16.4 编写REST客户端

作为客户端，编写与REST资源交互的代码可能会比较乏味，并且所编写的代码都是样板式的。例如，假设我们需要借助Facebook的Graph API，编写方法来获取某人的Facebook基本信息。不过，获取基本信息的代码会有点复杂：

```java
public Profile fetchFacebookProfile(String id) {
    try {
        HttpClient client = HttpClient.createDefault();
        HttpGet request = new HttpGet("http://graph.facebook.com/" + id);
        request.setHeader("Accept", "application/json");
        HttpResponse response = client.execute(request);
        HttpEntity entity = response.getEntity();
        ObjectMapper mapper = new ObjectMapper();
        return mapper.readValue(entity.getContent(), Profile.class);
    } catch (IOException e) {
        throw new RuntimeException(e);
    }
}
```

可以看到，在使用REST资源的时候涉及很多代码，这里使用了Commons HTTP Client发起请求并使用Jackson JSONprocessor解析响应。

仔细观察上述方法，我们会发现实际上只有少量代码与获取Facebook个人信息直接相关。如果我们要编写另一个方法来使用其他的REST资源，很可能会有很多代码是与`fetchFacebookProfile()`相同的。

另外，还有一些地方可能会抛出的`IOException`异常。因为`IOException`是检查型异常，所以要么捕获它，要么抛出它。在这里，我们选择捕获它并在它的位置重新抛出一个非检查型异常`RuntimeException`。

鉴于在资源使用上有如此之多的样板代码，我们可能会觉得最好的方式是封装通用代码并参数化可变的部分。这正是Spring的`RestTemplate`所做的事情。就像`JdbcTemplate`处理了JDBC数据访问时的丑陋部分，`RestTemplate`让我们在使用RESTful资源时免于编写那些乏味的代码。

#### 16.4.1 了解RestTemplate的操作

`RestTemplate`定义了许多与REST资源交互的方法，其中大多数都对应于HTTP的方法。除了TRACE以外，`RestTemplate`涵盖了所有的HTTP动作。除此之外，`execute()`和`exchange()`提供了较低层次的通用方法来使用任意的HTTP方法。

`RestTemplate`定义了11个独立的操作，而每一个都有重载：

方法 | 描述
-----|-----
`delete()` | 在特定的URL上对资源执行HTTP DELETE操作
`exchange()` | 在URL上执行特定的HTTP方法，返回包含对象的`ResponseEntity`，这个对象是从响应体中映射得到的
`execute()` | 在URL上执行特定的HTTP方法，返回一个从响应体映射得到的对象
`getForEntity()` | 发送一个HTTP GET请求，返回的`ResponseEntity`包含了响应体所映射成的对象
`getForObject()` | 发送一个HTTP GET请求，返回的请求体将映射为一个对象
`headForHeaders()` | 发送HTTP HEAD请求，返回包含特定资源URL的HTTP头
`optionsForAllow()` | 发送HTTP OPTIONS请求，返回对特定URL的Allow头信息
`postForEntity()` | POST数据到一个URL，返回包含一个对象的`ResponseEntity`，这个对象是从响应体中映射得到的
`postForLocation()` | POST数据到一个URL，返回新创建资源的URL
`postForObject()` | POST数据到一个URL，返回根据响应体匹配形成的对象
`put()` | PUT资源到特定的URL

我们通过对四个主要HTTP方法的支持（也就是GET、PUT、DELETE和POST）来研究RestTemplate的操作。我们从GET方法的`getForObject()`和`getForEntity()`开始。

#### 16.4.2 GET资源

上表中列出了两种执行GET请求的方法：

- getForObject()
- getForEntity()

其中，每个方法又有三种重载形式：

- getForObject()
    + <T> T getForObject(String url, Class<T> responseType, Object... uriVariables)
    + <T> T getForObject(String url, Class<T> responseType, Map<String, ?> uriVariables)
    + <T> T getForObject(URI url, Class<T> responseType)
- getForEntity()
    + <T> ResponseEntity<T> getForEntity(String url, Class<T> responseType, Object... uriVariables)
    + <T> ResponseEntity<T> getForEntity(String url, Class<T> responseType, Map<String, ?> uriVariables)
    + <T> ResponseEntity<T> getForEntity(URI url, Class<T> responseType)

除了返回类型，`getForEntity()`方法就是`getForObject()`方法的镜像。实际上，它们的工作方式大同小异。它们都执行根据URL检索资源的GET请求。它们都将资源根据`responseType`参数匹配为一定的类型。唯一的区别在于`getForObject()`只返回所请求类型的对象，而`getForEntity()`方法会返回请求的对象以及响应相关的额外信息。

#### 16.4.3　检索资源

`getForObject()`方法是检索资源的合适选择。我们请求一个资源并按照所选择的Java类型接收该资源。作为`getForObject()`能够做什么的一个简单示例，让我们看一下`fetchFacebookProfile()`的另一个实现：

```java
public Profile fetchFacebookProfile(String id) {
    RestTemplate rest = new RestTemplate();
    return rest.getForObject("http://graph.facebook.com/{spitter}", Profile.class, id);
}
```

`fetchFacebookProfile()`首先构建了一个`RestTemplate`的实例（另一种可行的方式是注入实例）。接下来，它调用了`getForObject()`来得到Facebook个人信息。为了做到这一点，它要求结果是`Profile`对象。在接收到`Profile`对象后，该方法将其返回给调用者。

注意，在这个新版本的`fetchFacebookProfile()`中，我们没有使用字符串连接来构建URL，而是利用了`RestTemplate`可以接受参数化URL这一功能。URL中的“{id}”占位符最终将会用方法的id参数来填充。`getForObject()`方法的最后一个参数是大小可变的参数列表，每个参数都会按出现顺序插入到指定URL的占位符中。

另外一种替代方案是将id参数放到`Map`中，并以id作为key，然后将这个`Map`作为最后一个参数传递给`getForObject()`：

```java
public Profile fetchFacebookProfile(String id) {
    Map<String, String> urlVariables = new HashMap<>();
    urlVariables.put("id", id);
    RestTemplate rest = new RestTemplate();
    return rest.getForObject("http://graph.facebook.com/{id}", Profile.class, urlVariables);
}
```

这里没有任何形式的JSON解析和对象映射。在表面之下，`getForObject()`为我们将响应体转换为对象。它实现这些需要依赖HTTP消息转换器，与带有`@ResponseBody`注解的Spring MVC处理方法所使用的一样。

这个方法也没有任何异常处理。这不是因为`getForObject()`不能抛出异常，而是因为它抛出的异常都是非检查型的。如果在`getForObject()`中有错误，将抛出非检查型`RestClientException`异常（或者它的一些子类）。如果愿意的话，你可以捕获它，但编译器不会强制你捕获它。

#### 16.4.4 抽取响应的元数据

作为`getForObject()`的一个替代方案，`RestTemplate`还提供了`getForEntity()`。`getForEntity()`方法与`getForObject()`方法的工作很相似。`getForObject()`只返回资源（通过HTTP信息转换器将其转换为Java对象），`getForEntity()`会在`ResponseEntity`中返回相同的对象，而且`ResponseEntity`还带有关于响应的额外信息，如HTTP状态码和响应头。

我们可能想使用`ResponseEntity`所做的事就是获取响应头的一个值。例如，假设除了获取资源，还想要知道资源的最后修改时间。假设服务端在LastModified头部信息中提供了这个信息，我们可以这样像这样使用`getHeaders()`方法：

```java
Date lastModified = new Date(response.getHeaders().getLastModified());
```

`getHeaders()`方法返回一个`HttpHeaders`对象，该对象提供了多个便利的方法来查询响应头，包括`getLastModified()`，它将返回从1970年1月1日开始的毫秒数。

`HttpHeaders`还包含如下的方法来获取头信息：

- public List<MediaType> getAccept() { ... }
- public List<Charset> getAcceptCharset() { ... }
- public Set<HttpMethod> getAllow() { ... }
- public String getCacheControl() { ... }
- public List<String> getConnection() { ... }
- public long getContentLength() { ... }
- public MediaType getContentType() { ... }
- public long getDate() { ... }
- public String getETag() { ... }
- public long getExpires() { ... }
- public long getIfNotModifiedSince() { ... }
- public List<String> getIfNoneMatch() { ... }
- public long getLastModified() { ... }
- public URI getLocation() { ... }
- public String getOrigin() { ... }
- public String getPragma() { ... }
- public String getUpgrade() { ... }

为了实现更通用的HTTP头信息访问，`HttpHeaders`提供了`get()`方法和`getFirst()`方法。两个方法都接受`String`参数来标识所需要的头信息。`get()`将会返回一个`String`值的列表，其中的每个值都是赋给该头部信息的，而`getFirst()`方法只会返回第一个头信息的值。

如果对响应的HTTP状态码感兴趣，那么可以调用`getStatusCode()`方法：

```java
public Spittle fetchSpittle(long id) {
    RestTemplate rest = new RestTemplate();
    ResponseEntity<Spittle> response = rest.getForEntity("http://localhost:8080/spittr/spittles/{id}", Spittle.class, id);
    if (response.getStatusCode() == HttpStatus.NOT_MODIFIED) {
        throw new NotModifiedException();
    }
    return response.getBody();
}
```

在这里，如果服务器响应304状态，这意味着服务器端的内容自从上一次请求之后再也没有修改。在这种情况下，将会抛出自定义的`NotModifiedException`异常来表明客户端应该检查它的缓存来获取`Spittle`。

#### 16.4.5 PUT资源

为了对数据进行PUT操作，`RestTemplate`提供了三个简单的`put()`方法。就像其他的`RestTemplate`方法一样，`put()`方法有三种形式：

- void put(String url, Object request, Object... uriVariables)
- void put(String url, Object request, Map<String, ?> uriVariables)
- void put(URI url, Object request)

按照它最简单的形式，`put()`接受一个`java.net.URI`，用来标识（及定位）要将资源发送到服务器上，另外还接受一个对象，这代表了资源的Java表述。例如，使用`put()`方法来更新服务器上的Spittle资源：

```java
public void updateSpittle(Spittle spittle) throws SpitterException {
    RestTemplate rest = new RestTemplate();
    String url = "http://localhost:8080/spittr/spittles/" + spittle.getId();
    rest.put(URI.create(url), spittle);
}
```

在这里，尽管方法签名很简单，但是使用`java.net.URI`作为参数的影响很明显。为了创建所更新`Spittle`对象的URL，我们要进行字符串拼接。

使用基于String的其他`put()`方法能够为我们减少创建URI的不便。这些方法可以将URI指定为模板并对可变部分插入值。以下是使用基于String的`put()`方法重写的`updateSpittle()`：

```java
public void updateSpittle(Spittle spittle) throws SpitterException {
    RestTemplate rest = new RestTemplate();
    rest.put("http://localhost:8080/spittr/spittles/{id}", spittle, spittle.getId());
}
```

现在的URI使用简单的`String`模板来进行表示。当`RestTemplate`发送PUT请求时，URI模板将“{id}”部分用`spittle.getId()`方法的返回值来进行替换。就像`getForObject()`和`getForEntity()`一样，这个版本的put()方法最后一个参数是大小可变的参数列表，每一个值会出现按照顺序赋值给占位符变量。

当然，还可以使用Map传递参数：

```java
public void updateSpittle(Spittle spittle) throws SpitterException {
    RestTemplate rest = new RestTemplate();
    Map<String, String> params = new HashMap<>();
    params.put("id", spittle.getId());
    rest.put("http://localhost:8080/spittr/spittles/{id}", spittle, params);
}
```

当使用Map来传递模板参数时，Map条目的每个key值与URI模板中占位符变量的名字相同。

在所有版本的`put()`中，第二个参数都是表示资源的Java对象，它将按照指定的URI发送到服务器端。在本示例中，它是一个`Spittle`对象。`RestTemplate`将使用个HTTP消息转换器将Spittle对象转换为一种表述形式，并在请求体中将其发送到服务器端。

对象将被转换成什么样的内容类型很大程度上取决于传递给put()方法的类型。如果给定一个String值，那么将会使用`StringHttpMessageConverter`：这个值直接被写到请求体中，内容类型设置为“text/plain”。如果给定一个`MultiValueMap<String,String>`，那么这个Map中的值将会被`FormHttpMessageConverter`以“application/x-wwwform-urlencoded”的格式写到请求体中。

由于这里我们传递的是Spittle对象，所以需要一个能够处理任意对象的信息转换器。如果在类路径下包含Jackson 2库，那么`MappingJackson2HttpMessageConverter`将以application/json格式将`Spittle`写到请求中。

#### 16.4.63 DELETE资源

当你不需要在服务端保留某个资源时，那么可能需要调用`RestTemplate`的`delete()`方法。就像PUT方法那样，`delete()`方法有三个版本，它们的签名如下：

- delete(String url, Object... uriVariables)
- delete(String url, Map<String, ?> uriVariables)
- delete(URI url)

`delete()`方法是所有`RestTemplate`方法中最简单的。你唯一要提供的就是要删除资源的URI。例如，为了删除指定ID的`Spittle`，你可以这样调用`delete()`：

```java
public void deleteSpittle(long id) {
    RestTemplate rest = new RestTemplate();
    rest.delete(URI.create("http://localhost:8080/spittr/spittles/" + id));
}
```

或

```java
public void deleteSpittle(long id) {
    RestTemplate rest = new RestTemplate();
    rest.delete("http://localhost:8080/spittr/spittles/{id}" + id);
}
```

#### 16.4.7 POST资源数据

`RestTemplate`有三个不同类型的方法来发送POST请求。当再乘上每个方法的三个不同变种，那就是有九个方法来POST数据到服务器端。

这些方法中有两个的名字看起来比较类似。`postForObject()`和`postForEntity()`对POST请求的处理方式与发送GET请求的`getForObject()`和`getForEntity()`方法是类似的。另一个方法是`getForLocation()`，它是POST请求所特有的。

#### 16.4.8 在POST请求中获取响应对象

假设你正在使用`RestTemplate`来POST一个新的`Spitter`对象到Spittr应用程序的REST API。因为这是一个全新的`Spitter`，服务端并（还）不知道它。因此，它还不是真正的REST资源，也没有URL。另外，在服务端创建之前，客户端也不知道`Spitter`的ID。

POST资源到服务端的一种方式是使用`RestTemplate`的`postForObject()`方法：

- <T> T postForObject(String url, Object request, Class<T> responseType, Object... uriVariables)
- <T> T postForObject(String url, Object request, Class<T> responseType, Map<String, ?> uriVariables)
- <T> T postForObject(URI url, Object request, Class<T> responseType)

在所有情况下，第一个参数都是资源要POST到的URL，第二个参数是要发送的对象，而第三个参数是预期返回的Java类型。在将URL作为`String`类型的两个版本中，第四个参数指定了URL变量（要么是可变参数列表，要么是一个`Map`）。

当POST新的`Spitter`资源到Spitter REST API时，它们应该发送到`http://localhost:8080/spittr/spitters`。这里会有一个应对POST请求的处理方法来保存对象。因为这个URL不需要URL参数，所以我们可以使用任何版本的`postForObject()`。但为了保持尽可能简单，我们可以这样调用：

```java
public Spitter postSpitterForObject(Spitter spitter) {
    RestTemplate rest = new RestTemplate();
    return rest.postForObject("http://localhost:8080/spittr/spitters", spitter, Spitter.class);
}
```

`postSpitterForObject()`方法给定了一个新创建的`Spitter`对象，并使用`postForObject()`将其发送到服务器端。在响应中，它接收到一个`Spitter`对象并将其返回给调用者。

就像`getForEntity()`方法一样，我们可能想得到请求带回来的一些元数据。在这种情况下，`postForEntity()`是更合适的方法。`postForEntity()`方法有着与`postForObject()`几乎相同的一组签名：

- <T> ResponseEntity<T> postForEntity(String url, Object request, Class<T> responseType, Object... uriVariables)
- <T> ResponseEntity<T> postForEntity(String url, Object request, Class<T> responseType, Map<String, ?> uriVariables)
- <T> ResponseEntity<T> postForEntity(URI url, Object request, Class<T> responseType)

假设除了要获取返回的`Spitter`资源，还要查看响应中Location头信息的值。在这种情况下，可以这样调用`postForEntity()`：

```java
RestTemplate rest = new RestTemplate();
ResponseEntity<Spitter> response = rest.postForEntity("http://localhost:8080/spittr-api/spitters",spitter, Spitter.class);
Spitter spitter = response.getBody();
URI url = response.getHeaders().getLocation();
```

与`getForEntity()`方法一样，`postForEntity()`返回一个`ResponseEntity<T>`对象。你可以调用这个对象的`getBody()`方法以获取资源对象（在本示例中是`Spitter`）。`getHeaders()`会给你一个`HttpHeaders`，通过它可以访问响应中返回的各种HTTP头信息。这里，我们调用`getLocation()`来得到`java.net.URI`形式的`Location`头信息。

#### 16.4.9 在POST请求后获取资源位置

上一例中如果我们并不需要将资源发送回来（毕竟，将其发送到服务器端是第一位的）。而真正需要的是Location头信息的值，那么使用`RestTemplate`的`postForLocation()`方法会更简单。

类似于其他的POST方法，`postForLocation()`会在POST请求的请求体中发送一个资源到服务器端。但是，响应不再是相同的资源对象，`postForLocation()`的响应是新创建资源的位置。它有如下三个方法签名：

- URI postForLocation(String url, Object request, Object... uriVariables)
- URI postForLocation(String url, Object request, Map<String, ?> uriVariables)
- URI postForLocation(URI url, Object request)

例如，POST一个`Spitter`，返回包含资源的URL：

```java
public String postSpitter(Spitter spitter) {
    RestTemplate rest = new RestTemplate();
    return rest.postForLocation("http://localhost:8080/spittr/spitters", spitter).toString();
}
```

在这里，我们以`String`的形式将目标URL传递进来，还有要POST的`Spitter`对象（在本示例中没有URL参数）。在创建资源后，如果服务端在响应的Location头信息中返回新资源的URL，接下来`postForLocation()`会以`String`的格式返回该URL。

#### 16.4.10 交换资源

到目前为止，我们已经看到`RestTemplate`的各种方法来GRT、PUT、DELETE以及POST资源。在它们之中，我们看到两个特殊的方法：`getForEntity()`和`postForEntity()`，这两个方法将结果资源包含在一个`ResponseEntity`对象中，通过这个对象我们可以得到响应头和状态码。

能够从响应中读取头信息是很有用的。但是如果我们想在发送给服务端的请求中设置头信息的话，怎么办呢？这就是`RestTemplate`的`exchange()`的用武之地。

像`RestTemplate`的其他方法一样，`exchange()`也重载为三个签名格式。一个使用`java.net.URI`来标识目标URL，而其他两个以`String`的形式传入URL并带有URL变量。如下所示：

- <T> ResponseEntity<T> exchange(String url, HttpMethod method,          HttpEntity<?> requestEntity, Class<T> responseType, Object... uriVariables)
- <T> ResponseEntity<T> exchange(String url, HttpMethod method,            HttpEntity<?> requestEntity, Class<T> responseType, Map<String, ?> uriVariables)
- <T> ResponseEntity<T> exchange(URI url, HttpMethod method, HttpEntity<?> requestEntity, Class<T> responseType)

`exchange()`方法使用`HttpMethod`参数来表明要使用的HTTP动作。根据这个参数的值，`exchange()`能够执行与其他`RestTemplate`方法一样的工作。

例如，从服务端获取Spitter资源也可以使用`exchange()`来完成：

```java
ResponseEntity<Spitter> response = rest.exchange("http://localhost:8080/spittr/spitters/{id}", HttpMethod.GET, null, Spitter.class, spitterId);
Spitter spitter = response.getBody();
```

通过传入`HttpMethod.GET`作为HTTP动作，我们会要求`exchange()`发送一个GET请求。第三个参数是用于在请求中发送资源的，但因为这是一个GET请求，它可以是`null`。下一个参数表明我们希望将响应转换为`Spitter`对象。最后一个参数用于替换URL模板中“{id}”占位符的值。

不同于`getForEntity()`和`getForObject()`，`exchange()`方法允许在请求中设置头部信息。

如果不知名头部信息，`exchange()`对Spitter的GET请求会带有如下的头部信息：

```text
GET /Spitter/spitters/habuma HTTP/1.1
Accept: application/xml, text/xml, application/*+xml, application/json
Content-Length: 0
User-Agent: Java/1.6.0_20
Host: localhost:8080
Connection: keep-alive
```

让我们看一下Accept头信息。Accept头信息表明它能够接受多种不同的XML内容类型以及application/json。这样服务器端在决定采用哪种格式返回资源时，就有很大的可选空间。假设我们希望服务端以JSON格式发送资源。在这种情况下，我们需要将“application/json”设置为Accept头信息的唯一值。

设置请求头信息是很简单的，只需构造发送给`exchange()`方法的`HttpEntity`对象即可，`HttpEntity`中包含承载头信息的`MultiValueMap`：

```java
MultiValueMap<String, String> headers = new LinkedMultiValueMap<>();
headers.add("Accept", "application/json");
HttpEntity<Object> requestEntity = new HttpEntity<>(headers);
```

在这里，我们创建了一个`LinkedMultiValueMap`并添加值为“application/json”的Accept头信息。接下来，我们构建了一个`HttpEntity`（使用`Object`泛型类型），将`MultiValueMap`作为构造参数传入。如果这是一个PUT或POST请求，我们需要为`HttpEntity`设置在请求体中发送的对象——对于GET请求来说，这是没有必要的。

现在，将`HttpEntity`传入`exchange()`方法：

```java
ResponseEntity<Spitter> response = rest.exchange("http://localhost:8080/spittr/spitters/{id}", HttpMethod.GET, requestEntity, Spitter.class, spitterId);
Spitter spitter = response.getBody();
```

表面上看，结果是一样的。我们得到了请求的`Spitter`对象。但在表面之下，请求将会带有如下的头信息发送：

```text
GET /Spitter/spitters/habuma HTTP/1.1
Accept: application/json
Content-Length: 0
User-Agent: Java/1.6.0_20
Host: localhost:8080
Connection: keep-alive
```

假设服务器端能够将`Spitter`序列化为JSON，响应体将会以JSON格式来进行表述。

### 16.5 小结

>
RESTful架构使用Web标准来集成应用程序，使得交互变得简单自然。系统中的资源采用URL进行标识，使用HTTP方法进行管理并且会以一种或多种适合客户端的方式来进行表述。
>
在本章中，我们看到了如何编写响应RESTful资源管理请求的SpringMVC控制器。借助参数化的URL模式并将控制器处理方法与特定的HTTP方法关联，控制器能够响应对资源的GET、POST、PUT以及DELETE请求。
>
为了响应这些请求，Spring能够将资源背后的数据以最适合客户端的形式展现。对于基于视图的响应，ContentNegotiatingViewResolver能够在多个视图解析器产生的视图中选择出最适合客户端期望内容类型的那一个。或者，控制器的处理方法可以借助@ResponseBody注解完全绕过视图解析，并使用信息转换器将返回值转换为客户端的响应。
>
REST API为客户端暴露了应用的功能，它们暴露功能的方式恐怕最原始的API设计者做梦都想不到。REST API的客户端通常是移动应用或运行在Web浏览器中的JavaScript。但是，Spring应用也可以借助RestTemplate来使用这些API。
>
REST只是应用间通信的方法之一，在下一章中，我们将会学习如何在Spring应用中借助消息实现异步通信。

## 第十七章 Spring 消息

本章内容：

- 异步消息简介
- 基于JMS的消息功能
- 使用Spring的AMQP发送消息
- 消息驱动的POJO

在前面的一些章中，你看到了如何使用RMI、Hessian、Burlap、HTTPinvoker和Web服务在应用程序之间进行通信。所有这些通信机制都是同步的，客户端应用程序直接与远程服务相交互，并且一直等到远程过程完成后才继续执行。

同步通信有它自己的适用场景。不过，对于开发者而言，这种通信方式并不是应用程序之间进行交互的唯一方式。异步消息是一个应用程序向另一个应用程序间接发送消息的一种方式，这种方式无需等待对方的响应。相对于同步消息，异步消息具有多个优势，关于这一点你很快就会看到。

借助Spring，我们有多个实现异步消息的可选方案。在本章中，我们将会看到如何在Spring中使用Java消息服务（Java Message Service，JMS）和高级消息队列协议（Advanced Message Queuing Protocol，AMQP）发送和接收消息。除了基本的消息发送和接收之外，我们还会看到Spring对消息驱动POJO的支持，它是一种与EJB的消息驱动Bean（message-driven bean，MDB）类似的消息接收方式。

### 17.1 异步消息简介

与前面几章中介绍的远程调用机制以及REST接口类似，异步消息也是用于应用程序之间通信的。但是，在系统之间传递信息的方式上，它与其他机制有所不同。

像RMI和Hessian/Burlap这样的远程调用机制是同步的。如下图所示，当客户端调用远程方法时，客户端必须等到远程方法完成后，才能继续执行。即使远程方法不向客户端返回任何信息，客户端也要被阻塞直到服务完成。

<center>
    ![图17.1-同步通信](images\图17.1-同步通信.PNG)
    **如果通信是同步的，客户端必须等待服务完成**
</center>

消息则是异步发送的，如下图所示，客户端不需要等待服务处理消息，甚至不需要等待消息投递完成。客户端发送消息，然后继续执行，这是因为客户端假定服务最终可以收到并处理这条消息。

<center>
    ![图17.2-异步通信](images\图17.2-异步通信.PNG)
    **异步通信是一种不需要等待的通信形式**
</center>

相对于同步通信，异步通信具有多项优势。

#### 17.1.1 发送消息

大多数人都使用过邮政服务。每天会有数百万信件、明信片和包裹交到邮递员手上，我们相信自己邮寄的东西会被送到目的地。世界实在是太大了，我们无法自己去运送这些东西，因此我们依赖邮政系统为我们运送。我们在信封上写明地址，贴张邮票，接着把它们投到信箱里，而不需要考虑信件如何到达目的地。

与此类似，间接性也是异步消息的关键所在。当一个应用向另一个应用发送消息时，两个应用之间没有直接的联系。相反的是，发送方的应用程序会将消息交给一个服务，由服务确保将消息投递给接收方应用程序。

在异步消息中有两个主要的概念：

- 消息代理：message broker
- 目的地：destination

当一个应用发送消息时，会将消息交给一个消息代理。消息代理实际上类似于邮局。消息代理可以确保消息被投递到指定的目的地，同时解放发送者，使其能够继续进行其他的业务。

当我们通过邮局邮递信件时，最重要的是要写上地址，这样邮局就可以知道这封信应该被投递到哪里。与此类似，每条异步消息都带有一个目的地，目的地就好像一个邮箱，可以将消息放入这个邮箱，直到有人将它们取走。

但是，并不像信件地址那样必须标识特定的收件人或街道地址，消息中的目的地相对来说并不那么具体。目的地只关注消息应该从哪里获得——而不关心是由谁取走消息的。这种情况下，目的地就如同信件的地址为“本地居民”。

尽管不同的消息系统会提供不同的消息路由模式，但是有两种通用的目的地：队列（queue）和主题（topic）。每种类型都与特定的消息模型相关联，分别是点对点模型（队列）和发布/订阅模型（主题）。

**点对点消息模型**

在点对点模型中，每一条消息都有一个发送者和一个接收者，如下图所示。当消息代理得到消息时，它将消息放入一个队列中。当接收者请求队列中的下一条消息时，消息会从队列中取出，并投递给接收者。因为消息投递后会从队列中删除，这样就可以保证消息只能投递给一个接收者。

<center>
    ![图17.3-点对点消息模型](images\图17.3-点对点消息模型.PNG)
    **消息队列对消息发送者和消息接收者进行了解耦。虽然队列可以有多个接收者，但是每一条消息只能被一个接收者取走**
</center>

尽管消息队列中的每一条消息只被投递给一个接收者，但是并不意味着只能使用一个接收者从队列中获取消息。事实上，通常可以使用几个接收者来处理队列中的消息。不过，每个接收者都会处理自己所接收到的消息。

这与在银行排队等候类似。在等待时，我们可能注意到很多银行柜员都可以帮助我们处理金融业务。在柜员帮助客户完成业务后，她就空闲了，此时，她会要求排队等候的下一个人前来办理业务。如果我们排在队伍的最前边时，我们就会被叫到，然后由其中的一个空闲柜员来帮助我们处理业务，而其他的柜员则会帮助其他的银行客户。

从另一个角度看，我们在银行排队时，并不知道哪一个柜员会帮助我们办理业务。我们可以计算队伍中有多少人，与柜员的数目进行比较，注意哪一个柜员业务办理速度最快，然后猜测会由哪一个柜员办理我们的业务。但是，一般情况下我们都会猜错，最终会由另一个柜员来办理。

同样，在点对点的消息中，如果有多个接收者监听队列，我们也无法知道某条特定的消息会由哪一个接收者处理。这种不确定性实际上有很多好处，因为我们只需要简单地为队列添加新的监听器就能提高应用的消息处理能力。

**发布-订阅模型**

在发布—订阅消息模型中，消息会发送给一个主题。与队列类似，多个接收者都可以监听一个主题。但是，与队列不同的是，消息不再是只投递给一个接收者，而是主题的所有订阅者都会接收到此消息的副本，如下图所示：

<center>
    ![图17.4-发布-订阅模型](images\图17.4-发布-订阅模型.PNG)
    **与队列类似，主题可以将消息发送者与消息接收者进行解耦。与队列不同的是，主题消息可以发送给多个主题订阅者**
</center>

正如它的名字所暗示的，发布—订阅消息模型与杂志发行商和杂志订阅者很相似。杂志（消息）出版后，发送给邮局，然后所有的订阅者都会收到杂志的副本。

杂志的类比就到此为至，因为对于异步消息来讲，发布者并不知道谁订阅了它的消息。发布者只知道它的消息要发送到一个特定的主题——而不知道有谁在监听这个主题。也就是说，发布者并不知道消息是如何被处理的。

下面让我们异步消息与同步RPC的对比。

#### 17.1.2 评估异步消息的优点

虽然同步通信比较容易理解，建立起来也很简单，但是采用同步通信机制访问远程服务的客户端存在几个限制，最主要的是：

- 同步通信意味着等待。当客户端调用远程服务的方法时，它必须等待远程方法结束后才能继续执行。如果客户端与远程服务频繁通信，或者远程服务响应很慢，就会对客户端应用的性能带来负面影响。
- 客户端通过服务接口与远程服务相耦合。如果服务的接口发生变化，此服务的所有客户端都需要做相应的改变。
- 客户端与远程服务的位置耦合。客户端必须配置服务的网络位置，这样它才知道如何与远程服务进行交互。如果网络拓扑进行调整，客户端也需要重新配置新的网络位置。
- 客户端与服务的可用性相耦合。如果远程服务不可用，客户端实际上也无法正常运行。
- 
虽然同步通信仍然有它的适用场景，但是在决定应用程序更适合哪种通信机制时，我们必须考量以上的这些缺点。如果这些限制正是你所担心的，那你可能很想知道异步通信是如何解决这些问题的。

**无需等待**

当使用JMS发送消息时，客户端不必等待消息被处理，甚至是被投递。客户端只需要将消息发送给消息代理，就可以确信消息会被投递给相应的目的地。

因为不需要等待，所以客户端可以继续执行其他任务。这种方式可以有效地节省时间，所以客户端的性能能够极大的提高。

**面向消息解耦**

与面向方法调用的RPC通信不同，发送异步消息是以数据为中心的。这意味着客户端并没有与特定的方法签名绑定。任何可以处理数据的队列或主题订阅者都可以处理由客户端发送的消息，而客户端不必了解远程服务的任何规范。

**位置独立**

同步RPC服务通常需要网络地址来定位。这意味着客户端无法灵活地适应网络拓扑的改变。如果服务的IP地址改变了，或者服务被配置为监听其他端口，客户端必须进行相应的调整，否则无法访问服务。

与之相反，消息客户端不必知道谁会处理它们的消息，或者服务的位置在哪里。客户端只需要了解需要通过哪个队列或主题来发送消息。因此，只要服务能够从队列或主题中获取消息即可，消息客户端根本不需要关注服务来自哪里。

在点对点模型中，可以利用这种位置的独立性来创建服务的集群。如果客户端不知道服务的位置，并且服务的唯一要求就是可以访问消息代理，那么我们就可以配置多个服务从同一个队列中接收消息。如果服务过载，处理能力不足，我们只需要添加一些新的服务实例来监听相同的队列就可以了。

在发布-订阅模型中，位置独立性会产生另一种有趣的效应。多个服务可以订阅同一个主题，接收相同消息的副本。但是每一个服务对消息的处理逻辑却可能有所不同。例如，假设我们有一组服务可以共同处理描述新员工信息的消息。一个服务可能会在工资系统中增加该员工，另一个服务则会将新员工增加到HR门户中，同时还有一个服务为新员工分配可访问系统的权限。每一个服务都基于相同的数据（都是从同一个主题接收的），但各自进行独立的处理。

**确保投递**

为了使客户端可以与同步服务通信，服务必须监听指定的IP地址和端口。如果服务崩溃了，或者由于某种原因无法使用了，客户端将不能继续处理。

但是，当发送异步消息时，客户端完全可以相信消息会被投递。即使在消息发送时，服务无法使用，消息也会被存储起来，直到服务重新可以使用为止。

### 17.2 使用JMS发送消息

*以下内容代码在工程sia4e-P4_Integrating_Spring-C17_Messaging_in_Spring-01_jms中*。

Java消息服务（Java Message Service ，JMS）是一个Java标准，定义了使用消息代理的通用API。在JMS出现之前，每个消息代理都有私有的API，这就使得不同代理之间的消息代码很难通用。但是借助JMS，所有遵从规范的实现都使用通用的接口，这就类似于JDBC为数据库操作提供了通用的接口一样。

Spring通过基于模板的抽象为JMS功能提供了支持，这个模板也就是`JmsTemplate`。使用`JmsTemplate`，能够非常容易地在消息生产方发送队列和主题消息，在消费消息的那一方，也能够非常容易地接收这些消息。Spring还提供了消息驱动POJO的理念：这是一个简单的Java对象，它能够以异步的方式响应队列或主题上到达的消息。

我们将会讨论Spring对JMS的支持，包括`JmsTemplate`和消息驱动POJO。但是在发送和接收消息之前，我们首先需要一个消息代理，它能够在消息的生产者和消费者之间传递消息。

#### 17.2.1 在Spring中搭建消息代理

ActiveMQ是一个伟大的开源消息代理产品，也是使用JMS进行异步消息传递的最佳选择。

**创建连接工厂**

在本章中，我们将了解如何采用不同的方式在Spring中使用JMS发送和接收消息。在所有的示例中，我们都需要借助JMS连接工厂通过消息代理发送消息。因为选择了ActiveMQ作为我们的消息代理，所以我们必须配置JMS连接工厂，让它知道如何连接到ActiveMQ。ActiveMQConnectionFactory是ActiveMQ自带的连接工厂，在Spring中可以使用如下方式进行配置：

```xml
<bean id="connectionFactory" class="org.apache.activemq.spring.ActiveMQConnectionFactory" />
```

默认情况下，`ActiveMQConnectionFactory`会假设ActiveMQ代理监听localhost的61616端口。对于开发环境来说，这没有什么问题，但是在生产环境下，ActiveMQ可能会在不同的主机和/端口上。如果是这样的话，我们可以使用`brokerURL`属性来指定代理的URL：

```xml
<bean id="connectionFactory" class="org.apache.activemq.spring.ActiveMQConnectionFactory"
p:brokerURL="tcp://localhost:61616" />
```

配置连接工厂还有另外一种方式，既然我们知道正在与ActiveMQ打交道，那我们就可以使用ActiveMQ自己的Spring配置命名空间来声明连接工厂（适用于ActiveMQ 4.1之后的所有版本）。首先，我们必须确保在Spring的配置文件中声明了`amq`命名空间：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:jms="http://www.springframework.org/schema/jms"
    xmlns:amq="http://activemq.apache.org/schema/core"
    xsi:schemaLocation="http://activemq.apache.org/schema/core
        http://activemq.apache.org/schema/core/activemq-core.xsd
        http://www.springframework.org/schema/jms
        http://www.springframework.org/schema/jms/spring-jms.xsd
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

</beans>
```

然后使用`<amq:connectionFactory>`元素声明连接工厂：

```xml
<amq:connectionFactory id="connectionFactory" brokerURL="tcp://localhost:61616" />
```

注意，`<amq:connectionFactory>`元素很明显是为ActiveMQ所准备的。如果我们使用不同的消息代理实现，它们不一定会提供Spring配置命名空间。如果没有提供的话，那我们就需要使用`<bean>`来装配连接工厂。

在本章的后续内容中，我们会多次使用`connectionFactory` bean，但是现在，我们只需要通过配置brokerURL属性来告知连接工厂消息代理的位置就足够了。在本例中，`brokerURL`属性中的URL指定连接工厂要连接到本地机器的61616端口（这个端口是ActiveMQ监听的默认端口）上的ActiveMQ。

**声明ActiveMQ消息目的地**

除了连接工厂外，我们还需要消息传递的目的地。目的地可以是一个队列，也可以是一个主题，这取决于应用的需求。

不论使用的是队列还是主题，我们都必须使用特定的消息代理实现类在Spring中配置目的地bean。下面的`<bean>`声明定义了一个ActiveMQ队列：

```xml
<bean id="queue" class="org.apache.activemq.command.ActiveMQQueue" c:_0="spitter.queue"/>
```

或者声明一个ActiveMQ主题：

```xml
<bean id="topic" class="org.apache.activemq.command.ActiveMQTopic" c:_0="spitter.topic" />
```

与连接工厂相似的是，ActiveMQ命名空间提供了另一种方式来声明队列和主题。对于队列，我们可以使用`<amq:quence>`元素来声明：

```xml
<amq:queue id="spitteQueue" physicalName="spitter.alert.queue" />
```

如果是主题，那么可以使用`<amq:topic>`元素来声明：

```xml
<amq:topic id="spittleTopic" physicalName="spittle.alert.topic" />
```

不管是哪种类型，都是借助`physicalName`属性指定消息通道的名称。

到此为止，我们已经看到了如何声明使用JMS所需的组件。现在我们已经准备好发送和接收消息了。为此，我们将使用Spring的`JmsTemplate`——Spring 对JMS支持的核心部分。但是首先，让我们先看看如果没有`JmsTemplate`，JMS是怎样使用的，以此了解`JmsTemplate`到底提供了些什么。

#### 17.2.2 使用Spring的JMS模板

正如我们所看到的，JMS为Java开发者提供了与消息代理进行交互来发送和接收消息的标准API，而且几乎每个消息代理实现都支持JMS，因此我们不必因为使用不同的消息代理而学习私有的消息API。

虽然JMS为所有的消息代理提供了统一的接口，但是这种接口用起来并不是很方便。使用JMS发送和接收消息并不像拿一张邮票并贴在信封上那么简单。

**处理失控的JMS代码**

传统的JMS使用与传统的JDBC代码类似的编程模型：

```java
ConnectionFactory cf = new ActiveMQConnectionFactory("tcp//localhost:61616");
Connection conn = null;
Session session = null;
try {
    conn = cf.createConnection();
    session = conn.createSession(false, Session.AUTO_ACKNOWLEDGE);
    Destination destination = new ActiveMQQueue("spitter.queue");
    MessageProducer producer = session.createProducer(destination);
    TextMessage message = session.createTextMessage();
    message.setText("Hello world!");
    producer.send(message);
} catch (JMSException e) {
    // ...
} finally {
    try {
        if (session != null) {
            session.close();
        }
        if (conn != null) {
            conn.close();
        }
    } catch (JMSException ex) {
        // ...
    }
}
```

这是一段失控的代码！！就像JDBC示例一样，差不多使用了20行代码，只是为了发送一条“Hello world!”消息。实际上，其中只有几行代码是用来发送消息的，剩下的代码仅仅是为了发送消息而进行的设置。

同样地，接收端的代码也很复杂：

```java
ConnectionFactory cf = new ActiveMQConnectionFactory("tcp://localhost:61616");
Connection conn = null;
Session session = null;
try {
    conn = cf.createConnection();
    conn.start();
    session = conn.createSession(false, Session.AUTO_ACKNOWLEDGE);
    Destination destination = new ActiveMQQueue("spitter.queue");
    MessageConsumer consumer = session.createConsumer(destination);
    Message message = consumer.receive();
    TextMessage = consumer.receiver();
    System.out.println("GOT A Message: " + textMessage.getText());
} catch (JMSException e) {
    // ...
} finally {
    try {
        if (session != null) {
            session.close();
        }
        if (conn != null) {
            conn.close();
        }
    } catch (JMSException ex) {
        // ...
    }
}
```

因为这些样板式代码，我们每次使用JMS时都要不断地做很多重复工作。更糟糕的是，你会发现我们在重复编写其他开发者的JMS代码。

现在，让我来介绍一下Spring的`JmsTemplate`如何对JMS的样板式代码实现相同的功能。

**使用JMS模板**

针对如何消除冗长和重复的JMS代码，Spring给出的解决方案是`JmsTemplate`。`JmsTemplate`可以创建连接、获得会话以及发送和接收消息。这使得我们可以专注于构建要发送的消息或者处理接收到的消息。

另外，`JmsTemplate`可以处理所有抛出的笨拙的`JMSException`异常。如果在使用`JmsTemplate`时抛出`JMSException`异常，`JmsTemplate`将捕获该异常，然后抛出一个非检查型异常，该异常是Spring自带的`JmsException`异常的子类。

下表列出了标准`JMSException`异常与Spring的非检查型异常之间的映射关系：

Spring（org.spirngframework.jms） | 标准的JMS（javax.jms）
----- | -----
`DestinationResolutionException` | Spring特有的——当Spring无法解析目的地名称时抛出
`IllegalStateException` | `IllegalStateException`
`InvalidClientIDException` | `InvalidClientIDException`
`InvalidDestinationException` | `InvalidDestinationException`
`InvalidSelectorException` | `InvalidSelectorException`
`JmsSecurityException` | `JmsSecurityException`
`InvalidSelectorException` | `InvalidSelectorException`
`ListenerExecutionFailedException` | Spring特有的——当监听器方法执行失败时抛出
`MessageConversionException` | Spring特有的——当消息转换失败时抛出
`MessageEOFException` | `MessageEOFException`
`MessageFormatException` | `MessageFormatException`
`MessageNotReadableException` | `MessageNotReadableException`
`MessageNotWriteableException` | `MessageNotWriteableException`
`ResourceAllocationException` | `ResourceAllocationException`
`SynchedLocalTransactionFailedException` | Spring特有的——当同步的本地事务不能完成时抛出
`TransactionInprogressException` | `TransactionInprogressException`
`TransactionRolledBackException` | `TransactionRolledBackException`
`UncategorizedJmsException` | Spring特有的——当没有其他异常适用时抛出

对于JMS API来说，`JMSException`的确提供了丰富且具有描述性的子类集合，让我们更清楚地知道发生了什么错误。不过，所有的`JMSException`异常的子类都是检查型异常，因此必须要捕获。`JmsTemplate`为我们捕获这些异常，并重新抛出对应非检查型`JMSException`异常的子类。

为了使用`JmsTemplate`，我们需要在配置文件中将它声明为一个bean：

```xml
<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    <constructor-arg ref="connectionFactory" />
</bean>
```

因为`JmsTemplate`需要知道如何连接到消息代理，所以我们必须为`connectionFactory`属性设置实现了JMS的`ConnectionFactory`接口的bean引用。

**发送消息**

在我们想建立的Spittr应用程序中，其中有一个特性就是当创建Spittle的时候提醒其他用户（或许是通过E-mail）。我们可以在增加Spittle的地方直接实现该特性。但是搞清楚发送提醒给谁以及实际发送这些提醒可能需要一段时间，这会影响到应用的性能。当增加一个新的Spittle时，我们希望应用是敏捷的，能够快速做出响应。

与其在增加Spittle时浪费时间发送这些信息，不如对该项工作进行排队，在响应返回给用户之后再处理它。与直接发送消息给其他用户所花费的时间相比，发送消息给队列或主题所花费的时间是微不足道的。

为了在`Spittle`创建的时候异步发送spittle提醒，让我们为Spittr应用引入`AlertService`：

```java
package spittr.alerts;

import spittr.domain.Spittle;

public interface AlertService {
    void sendSpittleAlert(Spittle spittle);
}
```

`AlertService`是一个接口，只定义了一个操作：`sendSpittleAlert()`。

如下所示，`AlertServiceImpl`实现了`AlertService`接口，它使用`JmsOperation`（`JmsTemplate`所实现的接口）将`Spittle`对象发送给消息队列，而队列会在稍后得到处理：

```java
package spittr.alerts;

import javax.jms.JMSException;
import javax.jms.Message;
import javax.jms.Session;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jms.core.JmsOperations;
import org.springframework.jms.core.MessageCreator;

import spittr.domain.Spittle;

public class AlertServiceImpl implements AlertService {

    // 注入JMS模板
    @Autowired
    private JmsOperations jmsTemplate;

    @Override
    public void sendSpittleAlert(Spittle spittle) {
        // 发送消息，指定目的地
        jmsTemplate.send("spittle.alert.queue", new MessageCreator() {
            
            @Override
            public Message createMessage(Session session) throws JMSException {
                // 创建消息
                return session.createObjectMessage(spittle);
            }
        });
    }
}
```

`JmsOperations`的`send()`方法的第一个参数是JMS目的地名称，标识消息将发送给谁。当调用`send()`方法时，`JmsTemplate`将负责获得JMS连接、会话并代表发送者发送消息，如下图所示：

<center>
    ![图17.5-JmsTemplate代表发送者来负责处理发送消息的复杂过程](images\图17.5-JmsTemplate代表发送者来负责处理发送消息的复杂过程.PNG)
    **JmsTemplate代表发送者来负责处理发送消息的复杂过程**
</center>

这里使用一个匿名内部类来构造消息，通过`createMessage()`方法，我们通过`session`创建了一个对象消息：传入一个`Spittle`对象，返回一个对象消息。

注意，`sendSpittleAlert()`方法专注于组装和发送消息。在这里没有连接或会话管理的代码，`JmsTemplate`帮我们处理了所有的相关事项，而且我们也不需要捕获`JMSException`异常。`JmsTemplate`将捕获抛出的所有`JMSException`异常，然后重新抛出某一种非检查型异常。

**设置目的地**

在上述代码中，我们明确指定了一个目的地，在`send()`方法中将消息发向此目的地。当我们希望通过程序选择一个目的地时，这种形式的`send()`方法很适用。但是在`AlertServiceImpl`案例中，我们总是将`Spittle`消息发给相同的目的地，所以这种形式的`send()`方法并不能带来明显的好处。

与其每次发送消息都指定一个目的地，我们可以为`JmsTemplate`装配一个默认的目的地：

```xml
<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    <constructor-arg ref="connectionFactory" />
    <property name="defaultDestinationName" value="spittle.alert.queue" />
</bean>
```

在这里，将目的地的名称设置为`spittle.alert.queue`，但它只是一个名称：它并没有说明你所处理的目的地是什么类型。如果已经存在该名称的队列或主题的话，就会使用已有的。如果尚未存在的话，将会创建一个新的目的地（通常会是队列）。但是，如果你想指定要创建的目的地类型的话，那么你可以将之前创建的队列或主题的目的地bean装配进来：

```xml
<bean id="spittleQueue" class="org.apache.activemq.command.ActiveMQQueue">
    <constructor-arg value="spittle.alert.queue" />
</bean>
<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    <constructor-arg ref="connectionFactory" />
    <property name="defaultDestination" ref="spittleQueue" />
</bean>
```

现在，在调用`send()`方法时，我们可以去除第一个参数了：

```java
@Override
public void sendSpittleAlert(Spittle spittle) {
    // 发送消息
    jmsTemplate.send(new MessageCreator() {
        
        @Override
        public Message createMessage(Session session) throws JMSException {
            // 创建消息
            return session.createObjectMessage(spittle);
        }
    });
}
```

这种形式的`send()`方法只需要传入一个`MessageCreator`。因为希望消息发送给默认目的地，所以我们没有必要再指定特定的目的地。

在调用`send()`方法时，我们不必再显式指定目的地能够让任务得以简化。但是如果我们使用消息转换器的话，发送消息会更加简单。

**在发送消息时，对消息进行转换**

除了send()方法，`JmsTemplate`还提供了`convertAndSend()`方法。与`send()`方法不同，`convertAndSend()`方法并不需要`MessageCreator`作为参数。这是因为`convertAndSend()`会使用内置的消息转换器（message converter）为我们创建消息：

当我们使用`convertAndSend()`时，`sendSpittleAlert()`可以减少到方法体中只包含一行代码：

```java
@Override
public void sendSpittleAlert(Spittle spittle) {
    jmsTemplate.convertAndSend(spittle);
}
```

这里，`Spittle`会在发送之前转换为`Message`，这个步骤在`JmsTemplate`内部进行处理，它使用一个`MessageConverter`的实现类将对象转换为`Message`。

`MessageConverter`是Spring定义的接口，只有两个需要实现的方法：

```java
public interface MessageConverter {

    Message toMessage(Object object, Session session) throws JMSException, MessageConversionException;

    Object fromMessage(Message message) throws JMSException, MessageConversionException;
}
```

尽管这个接口实现起来很简单，但我们通常并没有必要创建自定义的实现。Spring已经提供了多个实现（所有的消息转换器都位于`org.springframework.jms.support.converter`包中中）：

- MappingJackson2MessageConverter：使用Jackson 2 JSON库实现消息与JSON格式之间的相互转换
- MarshallingMessageConverter：使用JAXB库实现消息与XML格式之间的相互转换
- SimpleMessageConverter：实现`String`与`TextMessage`之间的相互转换，字节数组与`BytesMessage`之间的相互转换，`Map`与`MapMessage`之间的相互转换以及`Serializable`对象与`ObjectMessage`之间的相互转换
- MessagingMessageConverter

默认情况下，`JmsTemplate`在`convertAndSend()`方法中会使用`SimpleMessageConverter`。但是通过将消息转换器声明为bean并将其注入到`JmsTemplate`的`messageConverter`属性中，我们可以重写这种行为。例如，如果我们想使用JSON消息的话，那么可以声明一个`MappingJackson2MessageConverter` bean：

```xml
<bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
    <constructor-arg ref="connectionFactory" />
    <property name="defaultDestination" ref="spittleQueue" />
    <property name="messageConverter" ref="messageConverter" />
</bean>

<bean id="messageConverter"
    class="org.springframework.jms.support.converter.MappingJackson2MessageConverter" />
```

各个消息转换器可能会有额外的配置，进而实现转换过程的细粒度控制。例如，`MappingJacksonMessageConverter`能够让我们配置转码以及自定义`ObjectMapper`。

**接收消息**

现在我们已经了解了如何使用`JmsTemplate`发送消息。但如果我们是接收端，那要怎么办呢？`JmsTemplate`是不是也可以接收消息呢？

事实上，使用`JmsTemplate`接收消息甚至更简单，我们只需要调用`JmsTemplate`的`receive()`方法即可。

```java
@Override
public Spittle receiveSpittleAlert() {
    ObjectMessage receivedMessage = (ObjectMessage) jmsTemplate.receive();
    try {
        return (Spittle) receivedMessage.getObject();
    } catch (JMSException e) {
        throw JmsUtils.convertJmsAccessException(e);
    }
}
```

当调用`JmsTemplate`的`receive()`方法时，`JmsTemplate`会尝试从消息代理中获取一个消息。如果没有可用的消息，`receive()`方法会一直等待，直到获得消息为止。

<center>
    ![图17.6-调用receive()方法接收消息](images\图17.6-调用receive()方法接收消息.PNG)
    **使用JmsTemplate从主题或队列中接收消息的时候，只需要简单地调用receive()方法。JmsTemplate会处理其他的事情**
</center>

因为我们知道Spittle消息是作为一个对象消息来发送的，所以它可以在到达后转型为`ObjectMessage`。然后，我们调用`getObject()`方法把`ObjectMessage`转换为`Spittle`对象并返回此对象。

但是这里存在一个问题，我们不得不对可能抛出的`JMSException`进行处理。正如我已经提到的，`JmsTemplate`可以很好地处理抛出的`JmsException`检查型异常，然后把异常转换为Spring非检查型异常`JmsException`并重新抛出。但是它只对调用`JmsTemplate`的方法时才适用。`JmsTemplate`无法处理调用`ObjectMessage`的`getObject()`方法时所抛出的`JMSException`异常。

因此，我们要么捕获`JMSException`异常，要么声明本方法抛出`JMSException`异常。为了遵循Spring规避检查型异常的设计理念，我们不建议本方法抛出`JMSException`异常，所以我们选择捕获该异常。在`catch`代码块中，我们使用Spring中`JmsUtils`的`convertJmsAccessException()`方法把检查型异常`JMSException`转换为非检查型异常`JmsException`。这其实是在其他场景中由`JmsTemplate`为我们做的事情。

在`receiveSpittleAlert()`方法中，我们可以改善的一点就是使用消息转换器。在`convertAndSend()`中，我们已经看到了如何将对象转换为`Message`。不过，它们还可以用在接收端，也就是使用`JmsTemplate`的`receiveAndConvert()`：

```java
@Override
public Spittle receiveSpittleAlert() {
    return (Spittle) jmsTemplate.receiveAndConvert();
}
```

现在，没有必要将`Message`转换为`ObjectMessage`，也没有必要通过调用`getObject()`来获取`Spittle`，更无需担心检查型的`JMSException`异常。这个新的`receiveSpittleAlert()`简洁了许多。但是，依然还有一个很小且不容易察觉的问题。

使用`JmsTemplate`接收消息的最大缺点在于`receive()`和`receiveAndConvert()`方法都是同步的。这意味着接收者必须耐心等待消息的到来，因此这些方法会一直被阻塞，直到有可用消息（或者直到超时）。同步接收异步发送的消息，是不是感觉很怪异？

这个问题需要使用消息驱动POJO来解决。让我们看看如何使用能够响应消息的组件异步接收消息，而不是一直等待消息的到来。

#### 17.2.3 创建消息驱动的POJO

当我们调用`receive()`方法时，`JmsTemplate`会查看队列或主题中是否有消息，直到收到消息或者等待超时才会返回。这期间，应用无法处理任何事情，只能等待是否有消息。如果应用能够继续进行其他业务处理，当消息到达时再去通知它，不是更好吗？

EJB2规范的一个重要内容是引入了消息驱动bean（message-drivenbean，MDB）。MDB是可以异步处理消息的EJB。换句话说，MDB将JMS目的地中的消息作为事件，并对这些事件进行响应。而与之相反的是，同步消息接收者在消息可用前会一直处于阻塞状态。

MDB是EJB中的一个亮点。即使那些狂热的EJB反对者也认为MDB可以优雅地处理消息。EJB 2 MDB的唯一缺点是它们必须要实现`java.ejb.MessageDrivenBean`。此外，它们还必须实现一些EJB生命周期的回调方法。简而言之，EJB 2 MDB 不是纯的POJO。

在EJB 3规范中，MDB进一步简化了，使其更像POJO。我们不再需要实现`MessageDrivenBean`接口，而是实现更通用的`javax.jms.MessageListener`接口，并使用`@MessageDriven`注解标注MDB。

Spring提供了它自己的消息驱动bean来满足异步接收消息的需求，这种形式与EJB 3的MDB很相似。

**创建消息监听器**

如果使用EJB的消息驱动模型来创建Spittle的提醒处理器，我们需要使用`@MessageDriven`注解进行标注，并实现`MessageListener`接口。那么，Spittle的提醒处理器最终可能是这样的：

```java
@MessageDriven(mappedName="jms/spittle.alert.queue")
public class SpittleAlertHandler implements MessageListener {
    @Resource
    private MessageDrivenContext context;

    public void onMessage(Message message) {
        // ...
    }
}
```

EJB 3规范所要求的MDB也算不上太麻烦。但是事实上，`SpittleAlertHandler`的EJB 3实现太依赖于EJB的消息驱动API，并不是我们所希望的POJO。理想情况下，我们希望提醒处理器能够处理消息，但是不用编码，就好像它知道应该做什么。

Spring提供了以POJO的方式处理消息的能力，这些消息来自于JMS的队列或主题中。例如，基于POJO实现`SpittleAlertHandler`就足以做到这一点：

```java
public class SpittleAlertHandler {
    public void handleSpittleAlert(Spittle spittle) {
        // ...
    }
}
```

我们稍后会编写`handleSpittleAlert()`方法的具体内容，但现在可以看到，这个方法没有任何JMS的痕迹，从任何一个角度观察，它都是一个纯粹的POJO。

**配置消息监听器**

为POJO赋予消息接收能力的诀窍是在Spring中把它配置为消息监听器。Spring的jms命名空间为我们提供了所需要的一切。首先，让我们先把处理器声明为bean：

```xml
<bean id="spittleHandler" class="spittr.alerts.SpittleAlertHandler" />
```

然后，为了将其转变为消息驱动的POJO，我们需要把这个bean声明为消息监听器：

```xml
<jms:listener-container connection-factory="connectionFactory">
    <jms:listener destination="spitter.alert.queue" ref="spittleHandler"
        method="handleSpittleAlert" />
</jms:listener-container>
```

在这里，我们在消息监听器容器中包含了一个消息监听器。消息监听器容器（message listener container）是一个特殊的bean，它可以监控JMS目的地并等待消息到达。一旦有消息到达，它取出消息，然后把消息传给任意一个对此消息感兴趣的消息监听器。下图展示了这个交互过程：

<center>
    ![图17.7-消息监听器容器监听队列和主题](images\图17.7-消息监听器容器监听队列和主题.PNG)
    **消息监听器容器监听队列和主题。当消息到达时，消息将转给消息监听器（例如消息驱动的POJO）**
</center>

为了在Spring中配置消息监听器容器和消息监听器，我们使用了Spring jms命名空间中的两个元素。`<jms:listener-container>`中包含了`<jms:listener>`元素。这里的`connection-factory`属性配置了对`connectionFactory`的引用，容器中的每个`<jms:listener>`都使用这个连接工厂进行消息监听。在本示例中，`connection-factory`属性可以移除，因为该属性的默认值就是`connectionFactory`。

对于`<jms:listener>`元素，它用于标识一个bean和一个可以处理消息的方法。为了处理`Spittle`提醒消息，`ref`元素引用了`spittleHandler` bean。当消息到达`spitter.alert.queue`队列（通过`destination`属性配置）时，`spittleHandler` bean的`handleSpittleAlert()`方法（通过`method`属性指定的）会被触发。

值得一提的是，如果`ref`属性所标示的bean实现了`MessageListener`，那就没有必要再指定`method`属性了，默认就会调用`onMessage()`方法。

#### 17.2.4 使用基于消息的RPC

在第15章中，我们展示了Spring把bean的方法暴露为远程服务以及从客户端向这些服务发起调用的几种方式。在本章，我们学习了如何通过队列和主题在应用程序之间发送消息。现在我们将了解一下如何使用JMS作为传输通道来进行远程调用。

为了支持基于消息的RPC，Spring提供了`JmsInvokerServiceExporter`，它可以把bean导出为基于消息的服务；为客户端提供了`JmsInvokerProxyFactoryBean`来使用这些服务。

**导出基于JMS的服务**

`JmsInvokerServiceExporter`很类似于其他的服务导出器。考虑如下的`AlertServiceImpl`：

```java
@Component("alertService")
public class AlertServiceImpl implements AlertService {

    @Autowired
    private JavaMailSender mailSender;

    @Value("foo@bar.com")
    private String alertEmailAddress;
    
    public void sendSpittleAlert(Spittle spittle) {
        SimpleMailMessage message = new SimpleMailMessage();
        String spitterName = spittle.getSpitter().getFullName();
        message.setFrom("noreply@spitter.com");
        message.setSubject("New Spittle from " + spitterName);
        message.setText(spitterName + " says: " + spittle.getText());
        mailSender.send(message);
    }
}
```

我们现在不要过于关注`sendSpittleAlert()`方法的细节。现在，我们需要关注的重点在于`AlertServiceImpl`是一个简单的POJO，没有任何迹象标示它要用来处理JMS消息。

正如我们所看到的，`AlertServiceImpl`使用了`@Component`注解来标注，所以它会被Spring自动发现并注册为Spring应用上下文中ID为`alertService`的bean。在配置`JmsInvokerServiceExporter`时，我们将引用这个bean：

```xml
<bean id="alertServiceExporter" class="org.springframework.jms.remoting.JmsInvokerServiceExporter">
    <property name="service" ref="alertService" />
    <property name="serviceInterface" value="spittr.alerts.AlertService"/>
</bean>
```

这个bean的属性描述了导出的服务应该是什么样子的。`service`属性设置为`alertService` bean的引用，它是远程服务的实现。同时，`serviceInterface`属性设置为远程服务对外提供接口的全限定类名。

导出器的属性并没有描述服务如何基于JMS通信的细节。。但好消息是JmsInvokerServiceExporter可以充当JMS监听器。因此，我们使用`<jms:listenercontainer>`元素配置它：

```xml
<jms:listener-container connection-factory="connectionFactory">
    <jms:listener destination="spitter.alert.queue" ref="alertServiceExporter"/>
</jms:listener-container>
```

**使用基于JMS的服务**

这时候，基于JMS的提醒服务已经准备好了，等待队列中名字为`spitter.alert.queue`的RPC消息到达。在客户端，`JmsInvokerProxyFactoryBean`用来访问服务。

`JmsInvokerProxyFactoryBean`隐藏了访问远程服务的细节，并提供一个易用的接口，通过该接口客户端与远程服务进行交互。与代理RMI服务或HTTP服务的最大区别在于，`JmsInvokerProxyFactoryBean`代理了通过`JmsInvokerServiceExporter`所导出的JMS服务。

为了使用提醒服务，我们需要如下配置：

```xml
<bean id="alertService" class="org.springframework.jms.remoting.JmsInvokerProxyFactoryBean">
    <property name="connectionFactory" ref="connectionFactory" />
    <property name="queueName" value="spittle.alert.queue" />
    <property name="serviceInterface" value="spittr.alerts.AlertService" />
</bean>
```

多年来，JMS一直是Java应用中主流的消息解决方案。但是对于Java和Spring开发者来说，JMS并不是唯一的消息可选方案。在过去的几年中，高级消息队列协议（Advanced Message Queuing Protocol ，AMQP）得到了广泛的关注。因此，Spring也为通过AMQP发送消息提供了支持，这就是我们下面要讲解的内容。

### 17.3.1 AMQP简介

简单回忆一下JMS的消息模型，可能会有助于理解AMQP的消息模型。在JMS中，有三个主要的参与者：消息的生产者、消息的消费者以及在生产者和消费者之间传递消息的通道（队列或主题）。

在JMS中，通道有助于解耦消息的生产者和消费者，但是这两者依然会与通道相耦合。生产者会将消息发布到一个特定的队列或主题上，消费者从特定的队列或主题上接收这些消息。通道具有双重责任，也就是传递数据以及确定这些消息该发送到什么地方，队列的话会使用点对点算法发送，主题的话就使用发布-订阅的方式。

与之不同的是，AMQP的生产者并不会直接将消息发布到队列中。AMQP在消息的生产者以及传递消息的队列之间引入了一种间接的机制：Exchange。这种关系如下图所示：

<center>
    ![图17.8-Exchange实现了消息的生产者与消息队列之间解耦](images\图17.8-Exchange实现了消息的生产者与消息队列之间解耦.PNG)
    **在AMQP中，通过引入处理信息路由的Exchange，消息的生产者 与消息队列之间实现了解耦**
</center>

可以看到，消息的生产者将信息发布到一个Exchange。Exchange会绑定到一个或多个队列上，它负责将信息路由到队列上。信息的消费者会从队列中提取数据并进行处理。

上图没有展现出来的一点是Exchange不是简单地将消息传递到队列中，并不仅仅是一种穿透（pass-through）机制。AMQP定义了四种不同类型的Exchange，每一种都有不同的路由算法，这些算法决定了是否要将信息放到队列中。根据Exchange的算法不同，它可能会使用消息的routing key和/或参数，并将其与Exchange和队列之间binding的routing key和参数进行对比。（routing key可以大致理解为Email的收件人地址，指定了预期的接收者。）如果对比结果满足相应的算法，那么消息将会路由到队列上。否则的话，将不会路由到队列上。

四种标准的AMQP Exchange如下所示：

- Direct：如果消息的routing key与binding的routing key直接匹配的话，消息将会路由到该队列上；
- Topic：如果消息的routing key与binding的routing key符合通配符匹配的话，消息将会路由到该队列上；
- Headers：如果消息参数表中的头信息和值都与bingding参数表中相匹配，消息将会路由到该队列上；
- Fanout：不管消息的routing key和参数表的头信息/值是什么，消息将会路由到所有队列上。

借助这四种类型的Exchange，很容易就能想到我们可以定义任意数量的路由模式，而不再仅限于点对点和发布-订阅的方式。

当发送和接收消息的时候，所涉及的路由算法对于如何编写消息的生产者和消费者并没有什么影响。简单来讲，生产者将信息发送给Exchange并带有一个routing key，消费者从队列中获取消息。

现在，我们结束对AMQP的抽象讨论，开始着手编写借助SpringAMQP发送和接收消息的代码。首先我们将看到的是一些通用的配置，它们同时适用于生产者和消费者。

#### 17.3.2 配置Spring支持AMQP消息

*以下内容代码在工程sia4e-P4_Integrating_Spring-C17_Messaging_in_Spring-02_amqp中*。

当我们第一次使用Spring JMS抽象的时候，首先配置了一个连接工厂。与之类似，使用Spring AMQP前也要配置一个连接工厂。只不过，所要配置的不是JMS的连接工厂，而是需要配置AMQP的连接工厂。更具体来讲，需要配置RabbitMQ连接工厂。

**什么是RabbitMQ**

RabbitMQ是一个流行的开源消息代理，它实现了AMQP。SpringAMQP为RabbitMQ提供了支持，包括RabbitMQ连接工厂、模板以及Spring配置命名空间。

在使用它发送和接收消息之前，我们必须预先安装RabbitMQ。

配置RabbitMQ连接工厂最简单的方式就是使用Spring AMQP所提供的rabbit配置命名空间。为了使用这项功能，需要确保在Spring配置文件中已经声明了该模式：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:rabbit="http://www.springframework.org/schema/rabbit"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/rabbit 
        http://www.springframework.org/schema/rabbit/spring-rabbit.xsd">


</beans>
```

`rabbit`命名空间包含了多个在Spring中配置RabbitMQ的元素。按照其最简单的形式，我们可以在配置RabbitMQ连接工厂的时候没有任何属性：

```xml
<rabbit:connection-factory />
```

这的确能够运行起来，但是所导致的结果就是连接工厂bean没有可用的bean ID，这样的话就难将连接工厂装配到需要它的bean中。因此，我们可能希望通过`id`属性为其设置一个bean ID：

```xml
<rabbit:connection-factory id="connectionFactory" />
```

默认情况下，连接工厂会假设RabbitMQ服务器监听localhost的5672端口，并且用户名和密码均为guest。对于开发来讲，这是合理的默认值，但是对于生产环境，我们可能希望修改这些默认值。如下`<connection-factory>`的设置重写了默认的做法：

```xml
<rabbit:connection-factory id="connectionFactory" 
    host="${rabbitmq.host}"
    port="${rabbitmq.port}" 
    username="${rabbitmq.username}" 
    password="${rabbitmq.password}" />
```

我们使用占位符来指定值，这样配置项可以在Spring配置文件之外进行管理（很可能位于属性文件中）。

除了连接工厂以外，我们还要考虑使用其他的几个配置元素。接下来，看一下如何创建队列、Exchange以及binding。

**声明队列、Exchange以及binding**

在JMS中，队列和主题的路由行为都是通过规范建立的，AMQP与之不同，它的路由更加丰富和灵活，依赖于如何定义队列和Exchange以及如何将它们绑定在一起。声明队列、Exchange和binding的一种方式是使用RabbitMQ Channel接口的各种方法。但是直接使用RabbitMQ的Channel接口非常麻烦。

幸好，`rabbit`命名空间包含了多个元素，帮助我们声明队列、Exchange以及将它们结合在一起的binding：

元素 | 作用
-----|-----
`<queue>` | 创建一个队列
`<fanout-exchange>` | 创建一个fanout类型的Exchange
`<header-exchange>` | 创建一个header类型的Exchange
`<topic-exchange>` | 创建一个topic类型的Exchange
`<direct-exchange>` | 创建一个direct类型的Exchange
`<bindings><binding/></bindings>` | 元素定义一个或多个元素的集合。元素创建Exchange和队列之间的binding

这些配置元素要与`<admin>`元素一起使用。`<admin>`元素会创建一个RabbitMQ管理组件（administrative component），它会自动创建（如果它们在RabbitMQ代理中尚未存在的话）上述这些元素所声明的队列、Exchange以及binding。

例如，如果我们希望声明名为`spittle.alerts`的队列，只需要在Spring配置中添加如下的两个元素即可：    

```xml
<rabbit:admin connection-factory="connectionFactory"/>
<rabbit:queue id="spittleAlertQueue" name="spittle.alerts" />
```

注意，这里的id属性用来在Spring应用上下文中设置队列的bean ID，而name属性指定了RabbitMQ代理中队列的名称。

对于简单的消息来说，我们只需做这些就足够了。这是因为默认会有一个没有名称的direct Exchange，所有的队列都会绑定到这个Exchange上，并且routing key与队列的名称相同。在这个简单的配置中，我们可以将消息发送到这个没有名称的Exchange上，并将routing key设定为`spittle.alerts`，这样消息就会路由到这个队列中。实际上，我们重新创建了JMS的点对点模型。

但是，如果我们声明一个或更多的Exchange，并将其绑定到队列上。例如，如果要将消息路由到多个队列中，而不管routing key是什么，我们可以按照如下的方式配置一个fanout以及多个队列：

```xml
<rabbit:admin connection-factory="connectionFactory"/>

<rabbit:queue name="spittle.alerts.queue.1" />
<rabbit:queue name="spittle.alerts.queue.2" />
<rabbit:queue name="spittle.alerts.queue.3" />

<rabbit:fanout-exchange name="spittle.fanout">
    <rabbit:bindings>
        <rabbit:binding queue="spittle.alerts.queue.1"/>
        <rabbit:binding queue="spittle.alerts.queue.2"/>
        <rabbit:binding queue="spittle.alerts.queue.3"/>
    </rabbit:bindings>
</rabbit:fanout-exchange>
```

借助上表中的元素，会有无数种在RabbitMQ配置路由的方式。

#### 17.3.3 使用RabbitTemplate发送消息

顾名思义，RabbitMQ连接工厂的作用是创建到RabbitMQ的连接。如果你希望通过RabbitMQ发送消息，那么你可以将`connectionFactory` bean注入到`AlertServiceImpl`类中，并使用它来创建`Connection`，使用这个`Connection`来创建`Channel`，然后使用这个`Channel`发布消息到Exchange上。

但是，如果这样做的话，你要做许多的工作并且会涉及到很多样板式代码。Spring所讨厌的一件事情就是样板式代码。我们已经看到Spring提供模板来消除样板式代码的多个例子——包括本章前面所介绍的`JmsTemplate`，它消除了JMS的样板式代码。因此，SpringAMQP提供`RabbitTemplate`来消除RabbitMQ发送和接收消息相关的样板式代码就一点也不让人感觉奇怪了。

配置`RabbitTemplate`的最简单方式是使用rabbit命名空间的`template`元素：

```xml
<rabbit:template id="rabbitTemplate" connection-factory="connectionFactory" />
```

现在，要发送消息的话，我们只需要将模板bean注入到`AlertServiceImpl`中，并使用它来发送`Spittle`：

```java
public class AlertServiceImpl implements AlertService {

    @Autowired
    private RabbitOperations template;
    
    @Override
    public void sendSpittleAlert(Spittle spittle) {
        template.convertAndSend("spittle.alert.exchange", "spittle.alerts", spittle);
    }
    
}
```

可以看到，现在`sendSpittleAlert()`调用`RabbitOperations`（`RabbitTemplate`实现的接口）的`convertAndSend()`方法，其中`RabbitOperations`是被注入进来的。它传入了三个参数：Exchange的名称、routing key以及要发送的对象。注意，这里并没有指定消息该路由到何处、要发送给哪个队列以及期望哪个消费者来获取消息。

`RabbitOperations`有多个重载版本的`convertAndSend()`方法，这些方法可以简化它的使用。例如，使用某个重载版本的`convertAndSend()`方法，我们可以在调用`convertAndSend()`的时候，不设置Exchange的名称：

```java
template.convertAndSend("spittle.alerts", spittle);
```

我们甚至可以同时省略Exchange名称和routing key：

```java
template.convertAndSend(spittle);
```

如果在参数列表中省略Exchange名称，或者同时省略Exchange名称和routing key的话，RabbitTemplate将会使用默认的Exchange名称和routing key。按照我们之前的配置，默认的Exchange名称为空（或者说是默认没有名称的那一个Exchange），默认的routing key也为空。但是，我们可以在`<template>`元素上借助exchange和routing key属性配置不同的默认值：

```xml
<rabbit:template id="rabbitTemplate" 
                 connection-factory="connectionFactory" 
                 exchange="spittle.alert.exchange"
                 routing-key="spittle.alerts" />
```

但是，不管设置的默认值是什么，我们都可以在调用`convertAndSend()`方法的时候，以参数的形式显式指定它们，从而覆盖掉默认值。

`RabbitTemplate`还有其他的方法来发送消息，你可能会对此感兴趣。例如，我们可以使用较低等级的`send()`方法来发送`org.springframework.amqp.core.Message`对象，如下所示：

```java
Message message = new Message("Hello World".getBytes(), new MessageProperties());
template.send("hello.exchange", "hello.routing", message);
```

与`convertAndSend()`方法类似，`send()`方法也有重载形式，它们不需要提供Exchange名称和/或routing key。

使用`send()`方法的技巧在于构造要发送的`Message`对象。在这个“Hello World”样例中，我们通过给定字符串的字节数组来构建`Message`实例。对于`String`值来说，这足够了，但是如果消息的负载是复杂对象的话，那它就会复杂得多。

鉴于这种情况，我们有了`convertAndSend()`方法，它会自动将对象转换为`Message`。它需要一个消息转换器的帮助来完成该任务，默认的消息转换器是`SimpleMessageConverter`，它适用于`String`、`Serializable`实例以及字节数组。Spring AMQP还提供了其他几个有用的消息转换器，其中包括使用JSON和XML数据的消息转换器。

现在，我们已经发送了消息，接下来我们转向回话的另外一端，看一下如何获取消息。

#### 17.3.4 接收AMQP消息

我们可以回忆一下，JMS提供了两种从队列中获取信息的方式：使用`JmsTemplate`的同步方式以及使用消息驱动POJO的异步方式。Spring AMQP提供了类似的方式来获取通过AMQP发送的消息。因为我们已经有了`RabbitTemplate`，所以首先看一下如何使用它同步地从队列中获取消息。

**使用RabbitTemplate接收消息**

`RabbitTemplate`提供了多个接收信息的方法。最简单就是`receive()`方法，它位于消息的消费者端，对应于`RabbitTemplate`的`send()`方法。借助`receive()`方法，我们可以从队列中获取一个`Message`对象：

```java
Message message = template.recieve("spittle.alerts");
```

或者，如果愿意的话，你还可以配置获取消息的默认队列，这是通过在配置模板的时候，设置`queue`属性实现的：

```xml
<rabbit:template id="rabbitTemplate" 
                 connection-factory="connectionFactory" 
                 exchange="spittle.alert.exchange"
                 routing-key="spittle.alerts" 
                 queue="spittle.alerts" />
```

这样的话，我们在调用`receive()`方法的时候，不需要设置任何参数就能从默认队列中获取消息了：

```java
Message message = template.recieve();
```

在获取到`Message`对象之后，我们可能需要将它body属性中的字节数组转换为想要的对象。就像在发送的时候将领域对象转换为`Message`一样，将接收到的`Message`转换为领域对象同样非常繁琐。因此，我们可以考虑使用`RabbitTemplate`的`receiveAndConvert()`方法作为替代方案：

```java
Spittle spittle = (Spittle) template.receiveAndConvert("spittle.alerts");
```

我们还可以省略调用参数中的队列名称，这样它就会使用模板的默认队列名称：

```java
Spittle spittle = (Spittle) template.receiveAndConvert();
```

`receiveAndConvert()`方法会使用与`sendAndConvert()`方法相同的消息转换器，将`Message`对象转换为原始的类型。

调用`receive()`和`receiveAndConvert()`方法都会立即返回，如果队列中没有等待的消息时，将会得到`null`。这就需要我们来管理轮询（polling）以及必要的线程，实现队列的监控。

我们并非必须同步轮询并等待消息到达，Spring AMQP还提供了消息驱动POJO的支持，这不禁使我们回忆起Spring JMS中的相同特性。让我们看一下如何通过消息驱动AMQP POJO的方式来接收消息。

**定义消息驱动的AMQP POJO**

如果你想在消息驱动POJO中异步地消费使用`Spittle`对象，首先要解决的问题就是这个POJO本身。如下的`SpittleAlertHandler`扮演了这个角色：

```java
package spittr.alerts;

import spittr.domain.Spittle;

public class SpittleAlertHandler {

    public void handleSpittleAlert(Spittle spittle) {
        // ...
    }
}
```

这个类与借助JMS消费`Spittle`时所用到`SpittleAlertHandler`完全一致。我们之所以能够重用相同的POJO是因为这个类丝毫没有依赖于JMS或AMQP，并且不管通过什么机制传递过来Spittle对象，它都能够进行处理。

首先，我们在Spirng应用上下文中将其声明为一个bean：

```xml
<bean id="spittleListener" class="spittr.alerts.SpittleAlertHandler" />
```

同样，在使用基于JMS的MDP时，我们已经做过相同的事情，没有什么丝毫的差异。

最后，我们需要声明一个监听器容器和监听器，当消息到达的时候，能够调用SpittleAlertHandler。在基于JMS的MDP中，我们做过相同的事情，但是基于AMQP的MDP在配置上有一个细微的差别：

```xml
<rabbit:listener-container connection-factory="connectionFactory">
    <rabbit:listener ref="spittleListener" method="handleSpittleAlert"
        queue-names="spittle.alerts" />
</rabbit:listener-container>
```

我们不再通过`destination`属性（JMS中的做法）来监听队列或主题，这里我们通过`queue-names`属性来指定要监听的队列。但是，除此之外，基于AMQP的MDP与基于JMS的MDP都非常类似。

`queue-names`属性的名称使用了复数形式。在这里我们只设定了一个要监听的队列，但是允许设置多个队列的名称，用逗号分割即可。

另外一种指定要监听队列的方法是引用`<queue>`元素所声明的队列bean。我们可以通过`queues`属性来进行设置：

```xml
<rabbit:listener-container connection-factory="connectionFactory">
    <rabbit:listener ref="spittleListener" method="handleSpittleAlert"
        queues="spittleAlertQueue" />
</rabbit:listener-container>
```

同样，这里可以接受逗号分割的queue ID列表。当然，这需要我们在声明队列的时候，为其指定ID。例如，如下是重新定义的提醒队列，这次指定了ID：

```xml
<queue id="spittleAlertQueue" name="spittle.alerts" />
```

### 17.4 小结

>
异步消息通信与同步RPC相比有几个优点。间接通信带来了应用之间的松散耦合，因此减轻了其中任意一个应用崩溃所带来的影响。此外，因为消息转发给了收件人，因此发送者不必等待响应。在很多情况下，这会提高应用的性能。
>
虽然JMS为所有的Java应用程序提供了异步通信的标准API，但是它使用起来很繁琐。Spring消除了JMS样板式代码和异常捕获代码，让异步消息通信更易于使用。
>
在本章中，我们了解了Spring通过消息代理和JMS建立应用程序之间异步通信的几种方式。Spring的JMS模板消除了传统的JMS编程模型所必需的样板式代码，而基于Spring的消息驱动bean可以通过声明bean的方法允许方法响应来自于队列或主题中的消息。我们同样了解了如何通过Spring的JMS invoker为Spring bean提供基于消息的RPC。
>
在本章中，我们已经看到了如何在应用程序之间使用异步通信。在下一章中，我们将会延续这一话题，了解如何借助WebSocket在基于浏览器的客户端和服务器之间实现异步通信。

## 第十八章 使用WebSocket和STOMP实现消息功能

本章内容：

- 在浏览器和服务器之间发送消息
- 在Spring MVC控制器中处理消息
- 为目标用户发送消息

在上一章中，我们看到了如何使用JMS和AMQP在应用程序之间发送消息。异步消息是应用程序之间通用的交流方式。但是，如果某一应用是运行在Web浏览器中，那我们就需要一些稍微不同的技巧了。

WebSocket协议提供了通过一个套接字实现全双工通信的功能。除了其他的功能之外，它能够实现Web浏览器和服务器之间的异步通信。全双工意味着服务器可以发送消息给浏览器，浏览器也可以发送消息给服务器。

Spring 4为WebSocket通信提供了支持，包括：

- 发送和接收消息的低层级API
- 发送和接收消息的高层级API
- 用来发送信息的模板
- 支持SockJS，用来解决浏览器端、服务器以及代理不支持WebSocket的问题

在本章中，我们将会学习借助Spring的WebSocket功能实现服务器端和基于浏览器的应用之间实现异步通信。

### 18.1 使用Spring的低层级WebSocket API

*以下内容代码在工程sia4e-P4_Integrating_Spring-C18_Messaging_with_WebSocket_And_STOMP-01_websocket中*。

按照其最简单的形式，WebSocket只是两个应用之间通信的通道。位于WebSocket一端的应用发送消息，另外一端处理消息。因为它是全双工的，所以每一端都可以发送和处理消息。

<center>
    ![图18.1-WebSocket是两个应用之间全双工的通信通道](images\图18.1-WebSocket是两个应用之间全双工的通信通道.PNG)
    **WebSocket是两个应用之间全双工的通信通道**
</center>

WebSocket通信可以应用于任何类型的应用中，但是WebSocket最常见的应用场景是实现服务器和基于浏览器的应用之间的通信。浏览器中的JavaScript客户端开启一个到服务器的连接，服务器通过这个连接发送更新给浏览器。相比历史上轮询服务端以查找更新的方案，这种技术更加高效和自然。

为了阐述Spring低层级的WebSocket API，让我们编写一个简单的WebSocket样例，基于JavaScript的客户端与服务器玩一个无休止的“Marco Polo”游戏。服务器端的应用会处理文本消息（“Marco!”），然后在相同的连接上往回发送文本消息（“Polo!”）。为了在Spring使用较低层级的API来处理消息，我们必须编写一个实现`WebSocketHandler`的类：

```java
public interface WebSocketHandler {
    void afterConnectionEstablished(WebSocketSession session) throws Exception;
    void handleMessage(WebSocketSession session, WebSocketMessage<?> message) throws Exception;
    void afterConnectionClosed(WebSocketSession session, CloseStatus closeStatus) throws Exception;
    boolean supportsPartialMessage();
}
```

可以看到，`WebSocketHandler`需要我们实现五个方法。相比直接实现`WebSocketHandler`，更为简单的方法是扩展`AbstractWebSocketHandler`，这是`WebSocketHandler`的一个抽象实现。例如，这个`MarcoHandler`，它是`AbstractWebSocketHandler`的一个子类，会在服务器端处理消息：

```java
public class MarcoHandler extends AbstractWebSocketHandler {

    private static final Logger LOGGER = LoggerFactory.getLogger(MarcoHandler.class);

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception {
        LOGGER.info("Received message: " + message.getPayload());
        TimeUnit.SECONDS.sleep(2);
        session.sendMessage(new TextMessage("Polo!"));
    }
}
```

尽管`AbstractWebSocketHandler`是一个抽象类，但是它并不要求我们必须重载任何特定的方法。相反，它让我们来决定该重写哪一个方法。除了重写`WebSocketHandler`中所定义的五个方法以外，我们还可以重载`AbstractWebSocketHandler`中所定义的三个方法：

- void handleTextMessage(WebSocketSession session, TextMessage message) throws Exception 
- void handleBinaryMessage(WebSocketSession session, BinaryMessage message) throws Exception 
- void handlePongMessage(WebSocketSession session, PongMessage message) throws Exception 

这三个方法是`handleMessage()`方法的具体化，每个方法对应于某一种特定类型的消息。

因为`MarcoHandler`将会处理文本类型的“Marco!”消息，因此我们应该重写`handleTextMessage()`方法。当有文本消息抵达的时候，日志会记录消息内容，在两秒钟的模拟延迟之后，在同一个连接上返回另外一条文本消息。

`MarcoHandler`所没有重写的方法都由`AbstractWebSocketHandler`以空操作的方式（no-op）进行了实现。这意味着`MarcoHandler`也能处理二进制和pong消息，只是对这些消息不进行任何操作而已。

另一种方案，我们可以扩展`TextWebSocketHandler`，不再扩展`AbstractWebSocketHandler`：

`TextWebSocketHandler`是`AbstractWebSocketHandler`的子类，它会拒绝处理二进制消息。它重写了`handleBinaryMessage()`方法，如果收到二进制消息的时候，将会关闭WebSocket连接：

```java
public class TextWebSocketHandler extends AbstractWebSocketHandler {

    @Override
    protected void handleBinaryMessage(WebSocketSession session, BinaryMessage message) {
        try {
            session.close(CloseStatus.NOT_ACCEPTABLE.withReason("Binary messages not supported"));
        }
        catch (IOException ex) {
            // ignore
        }
    }

}
```

与之类似，`BinaryWebSocketHandler`也是`AbstractWebSocketHandler`的子类，它重写了`handleTextMessage()`方法，如果接收到文本消息的话，将会关闭连接：

```java
public class BinaryWebSocketHandler extends AbstractWebSocketHandler {

    @Override
    protected void handleTextMessage(WebSocketSession session, TextMessage message) {
        try {
            session.close(CloseStatus.NOT_ACCEPTABLE.withReason("Text messages not supported"));
        }
        catch (IOException ex) {
            // ignore
        }
    }

}
```

尽管你会关心如何处理文本消息或二进制消息，或者二者兼而有之，但是你可能还会对建立和关闭连接感兴趣。在本例中，我们可以重写`afterConnectionEstablished()`和`afterConnectionClosed()`：

```java
@Override
public void afterConnectionEstablished(WebSocketSession session) throws Exception {
    LOGGER.info("Connection established");
}

@Override
public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
    LOGGER.info("Connection closed. status: " + status);
}
```

我们通过`afterConnectionEstablished()`和`afterConnectionClosed()`方法记录了连接信息。当新连接建立的时候，会调用`afterConnectionEstablished()`方法，类似地，当连接关闭时，会调用`afterConnectionClosed()`方法。在本例中，连接事件仅仅记录了日志，但是如果我们想在连接的生命周期上建立或销毁资源时，这些方法会很有用。

注意，这些方法都是以“after”开头。这意味着，这些事件只能在事件发生后才产生响应，因此并不能改变结果。

现在，已经有了消息处理器类，我们必须要对其进行配置，这样Spring才能将消息转发给它。在Spring的Java配置中，这需要在一个配置类上使用`@EnableWebSocket`，并实现`WebSocketConfigurer`接口：

```java
package marcopolo;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.config.annotation.WebSocketConfigurer;
import org.springframework.web.socket.config.annotation.WebSocketHandlerRegistry;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    // 将MarcoHandler映射到“/marco”
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(marcoHandler(), "/marco");
    }

    // 声明MarcoHandler bean
    @Bean
    public MarcoHandler marcoHandler() {
        return new MarcoHandler();
    }

}
```

`registerWebSocketHandlers()`方法是注册消息处理器的关键。通过重载该方法，我们得到了一个`WebSocketHandlerRegistry`对象，通过该对象可以调用`addHandler()`来注册信息处理器。在本例中，我们注册了`MarcoHandler`（以bean的方式进行声明）并将其与“/marco”路径相关联。

如果使用XML配置，可以使用`websocket`命名空间：

```xml
<websocket:handlers>
    <websocket:mapping handler="marcoHandler" path="/marco" />
</websocket:handlers>

<bean id="marcoHandler" class="marcopolo.MarcoHandler" />
```

现在我们将注意力转向客户端，它会发送文本“Marco！”消息到服务器，并监听来自服务器的文本消息：

```js
var url = "ws://" + window.location.host + "/websocket/marco";

// 打开WebSocket
var sock = new WebSocket(url);

// 处理连接开启事件
sock.onopen = function() {
    console.log("Opening");
    sayMarco();
};

// 处理信息
sock.onmessage = function(e) {
    console.log("Received message: " + e.data);
    setTimeout(function() {
        sayMarco()
    }, 2000);
}

// 处理连接关闭事件
sock.onclose = function() {
    console.log("Closing");
};

// 发送消息
function sayMarco() {
    console.log("Sending Marco!");
    sock.send("Marco!");
}
```

上述代码所做的第一件事情就是创建WebSocket实例。对于支持WebSocket的浏览器来说，这个类型是原生的。通过创建WebSocket实例，实际上打开了到给定URL的WebSocket。在本例中，URL使用了“ws://”前缀，表明这是一个基本的WebSocket连接。如果是安全WebSocket的话，协议的前缀将会是“wss://”。

WebSocket创建完毕之后，接下来的代码建立了WebSocket的事件处理功能。注意，WebSocket的`onopen`、`onmessage`和`onclose`事件对应于`MarcoHandler`的`afterConnectionEstablished()`、`handleTextMessage()`和`afterConnectionClosed()`方法。在`onopen`事件中，设置了一个函数，它会调用`sayMarco()`方法，在该WebSocket上发送“Marco!”消息。通过发送“Marco!”，这个无休止的Marco Polo游戏就开始了，因为服务器端的`MarcoHandler`作为响应会将“Polo!”发送回来，当客户端收到来自服务器的消息后，`onmessage`事件会发送另外一个“Marco!”给服务器。

这个过程会一直持续下去，直到调用`sock.close()`关闭连接。在服务端也可以关闭连接，或者浏览器转向其他的页面，都会关闭连接。如果发生以上任意的场景，只要连接关闭，都会触发`onclose`事件。

到此为止，我们已经编写完使用Spring低层级WebSocket API的所有代码，包括接收和发送消息的处理器类，以及在浏览器端完成相同功能的JavaScript客户端。如果我们构建这些代码并将其部署到Servlet容器中，那它有可能能够正常运行。

为什么是可能？这是因为我们不能保证它可以正常运行。实际上，它很有可能运行不起来。即便把所有的事情都做对了。

接下来让我们看一下都有什么事情会阻止WebSocket正常运行，并采取一些措施提高成功的几率。

### 18.2 应对不支持WebSocket的场景

WebSocket是一个相对比较新的规范。虽然它早在2011年底就实现了规范化，但即便如此，在Web浏览器和应用服务器上依然没有得到一致的支持。Firefox和Chrome早就已经完整支持WebSocket了，但是其他的一些浏览器刚刚开始支持WebSocket。

令人遗憾的是，很多的上网者并没有认识到或理解新Web浏览器的特性，因此升级很慢。另外，有的公司规定使用特定版本的浏览器，这样它们的员工很难（或不可能）使用更新的浏览器。鉴于这些情况，如果你的应用程序使用WebSocket的话，用户可能会无法使用。

服务器端对WebSocket的支持也好不到哪里去。GlassFish在几年前就开始支持一定形式的WebSocket，但是很多其他的应用服务器在最近的版本中刚刚开始支持WebSocket。

即便浏览器和应用服务器的版本都符合要求，两端都支持WebSocket，在这两者之间还有可能出现问题。防火墙代理通常会限制所有除HTTP以外的流量。它们有可能不支持或者（还）没有配置允许进行WebSocket通信。

在当前的WebSocket领域，我也许描述了一个很阴暗的前景。但是，不要因为这一些不支持，你就停止使用WebSocket的功能。当它能够正常使用的时候，WebSocket是一项非常棒的技术，但是如果它无法得到支持的话，我们所需要的仅仅是一种备用方案（fallback plan）。

幸好，提到WebSocket的备用方案，这恰是SockJS所擅长的。SockJS是WebSocket技术的一种模拟，在表面上，它尽可能对应WebSocket API，但是在底层它非常智能，如果WebSocket技术不可用的话，就会选择另外的通信方式。SockJS会优先选用WebSocket，但是如果WebSocket不可用的话，它将会从如下的方案中挑选最优的可行方案：

- XHR流
- XDR流
- iFrame事件源
- iFrame HTML文件
- XHR轮询
- XDR轮询
- iFrame XHR轮询
- JSONP轮询

好消息是在使用SockJS之前，我们并没有必要全部了解这些方案。SockJS让我们能够使用统一的编程模型，就好像在各个层面都完整支持WebSocket一样，SockJS在底层会提供备用方案。

例如，为了在服务端启用SockJS通信，我们在Spring配置中可以很简单地要求添加该功能。

```java
@Override
public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
    registry.addHandler(marcoHandler(), "/marco").withSockJS();
}
```

此时，`addHandler()`方法会返回`WebSocketHandlerRegistration`，通过简单地调用其`withSockJS()`方法就能声明我们想要使用SockJS功能，如果WebSocket不可用的话，SockJS的备用方案就会发挥作用。

如果你使用XML来配置Spring的话，启用SockJS只需在配置中添加`<websocket:sockjs>`元素即可：

```xml
<websocket:handlers>
    <websocket:mapping handler="marcoHandler" path="/marco" />
    <websocket:sockjs />
</websocket:handlers>
```

要在客户端使用SockJS，需要确保加载了SockJS客户端库。具体的做法在很大程度上依赖于使用JavaScript模块加载器还是简单地使用`<script>`标签加载JavaScript库。加载SockJS客户端的最简单方法是使用`<script>`标签从CDN中进行加载：

```html
<script src="https://cdn.jsdelivr.net/npm/sockjs-client@1/dist/sockjs.min.js"></script>
```

使用WebJars解析Web资源

我们使用WebJars来解析JavaScript库，使其作为项目构建的一部分，就像其他的依赖一样。为了支持该功能，我们需要在Spring MVC中配置一个资源处理器，它负责解析路径以“/webjars/**”开头的请求，这也是WebJars的标准路径：

```java
@Override
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/webjars/**")
            .addResourceLocations("classpath:/META-INF/resources/webjars/");
}
```

在资源处理器准备就绪后，我们可以在Web页面中使用如下的`<script>`标签加载SockJS库：

```html
<script th:src="@{/webjars/sockjs-client/1.1.5/dist/sockjs.js}"></script>
```

这里使用了Thymeleaf（需要配置），并使用“@{}”表达式来为JavaScript文件计算完整的相对于上下文的URL路径。

页面代码如下：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Home</title>
<script th:src="@{/webjars/sockjs-client/1.1.5/dist/sockjs.js}"></script>
<script th:src="@{/webjars/jquery/3.3.1/dist/jquery.js}"></script>
</head>
<body>
    <button id="stop">Stop</button>
    <script th:inline="javascript">
      var sock = new SockJS([[@{/marco}]]);
       
      sock.onopen = function() {
          console.log('Opening');
          sayMarco();
      }
      
      sock.onmessage = function(e) {
          console.log('Received message: ', e.data);
          $('#output').append('Received "' + e.data + '"<br/>');
          setTimeout(function(){
              sayMarco()
          }, 2000);
      }
      
      sock.onclose = function() {
          console.log('Closing');
      }
      
      function sayMarco() {
          console.log('Sending Marco!');
          $('#output').append('Sending "Marco!"<br/>');
          sock.send("Marco!");
      }
      
      $('#stop').click(function() {
          sock.close()
      });
    </script>
    
    <div id="output"></div>
</body>
</html>
```

`var sock = new SockJS([[@{/marco}]])`会创建到“http://localhost:8080/project_name/marco” 的链接。这里最核心的变化是创建SockJS实例来代替WebSocket。因为SockJS尽可能地模拟了WebSocket，所以其他代码并不需要变化。相同的`onopen`、`onmessage`和`onclose`事件处理函数用来响应对应的事件，相同的`send()`方法用来发送“Marco!”到服务器端。

我们并没有改变很多的代码，但是客户端-服务器之间通信的运行方式却有了很大的变化。我们可以完全相信客户端和服务器之间能够进行类似于WebSocket这样的通信，即便浏览器、服务器或位于中间的代理不支持WebSocket，我们也无需再担心了。

WebSocket提供了浏览器-服务器之间的通信方式，当运行环境不支持WebSocket的时候，SockJS提供了备用方案。但是不管哪种场景，对于实际应用来说，这种通信形式都显得层级过低。让我们看一下如何在WebSocket之上使用STOMP（Simple Text Oriented Messaging Protocol），为浏览器-服务器之间的通信增加恰当的消息语义。

### 18.3 使用STOMP消息

*以下内容代码在工程sia4e-P4_Integrating_Spring-C18_Messaging_with_WebSocket_And_STOMP-02_stomp中*。

如果我们要编写一个Web应用程序，在讨论需求之前，我们可能对于要采用的基础技术和框架就有了很好的想法。即便是简单的“HelloWorld”Web应用，可能也会考虑使用Spring MVC控制器来处理请求，并为响应使用JSP或Thymeleaf模板。至少，我们也应当会创建一个静态的HTML页面，并让Web服务器处理来自Web浏览器的相应请求。我们应该不会关心浏览器具体如何请求页面以及页面如何传递给浏览器这样的事情。

现在，我们假设HTTP协议并不存在，只能使用TCP套接字来编写Web应用。这是个疯狂的想法，当然，我们也许能够完成这一壮举，但是这需要自行设计客户端和服务器端都认可的协议，从而实现有效的通信。简单来说，这不是一件容易的事情。

不过，幸好我们有HTTP，它解决了Web浏览器发起请求以及Web服务器响应请求的细节。这样的话，大多数的开发人员并不需要编写低层级TCP套接字通信相关的代码。

直接使用WebSocket（或SockJS）就很类似于使用TCP套接字来编写Web应用。因为没有高层级的线路协议（wire protocol），因此就需要我们定义应用之间所发送消息的语义，还需要确保连接的两端都能遵循这些语义。

不过，好消息是我们并非必须要使用原生的WebSocket连接。就像HTTP在TCP套接字之上添加了请求-响应模型层一样，STOMP在WebSocket之上提供了一个基于帧的线路格式（frame-based wireformat）层，用来定义消息的语义。

乍看上去，STOMP的消息格式非常类似于HTTP请求的结构。与HTTP请求和响应类似，STOMP帧由命令、一个或多个头信息以及负载所组成。例如，如下就是发送数据的一个STOMP帧：

```text
SEND
destination:/ap/marco
{"message":"Marco!"}
```

在这个简单的样例中，STOMP命令是send，表明会发送一些内容。紧接着是两个头信息：一个用来表示消息要发送到哪里的目的地，另外一个则包含了负载的大小。然后，紧接着是一个空行，STOMP帧的最后是负载内容，在本例中，是一个JSON消息。

STOMP帧中最有意思的恐怕就是destination头信息了。它表明STOMP是一个消息协议，类似于JMS或AMQP。消息会发布到某个目的地，这个目的地实际上可能真的有消息代理（message broker）作为支撑。另一方面，消息处理器（message handler）也可以监听这些目的地，接收所发送过来的消息。

在WebSocket通信中，基于浏览器的JavaScript应用可能会发送消息到一个目的地，这个目的地由服务器端的组件来进行处理。其实，反过来是一样的，服务器端的组件也可以发布消息，由JavaScript客户端的目的地来接收。

Spring为STOMP消息提供了基于Spring MVC的编程模型。稍后将会看到，在Spring MVC控制器中处理STOMP消息与处理HTTP请求并没有太大的差别。但首先，我们需要配置Spring启用基于STOMP的消息。

#### 18.3.1 启用STOMP消息功能

稍后，我们将会看到如何在Spring MVC中为控制器方法添加`@MessageMapping`注解，使其处理STOMP消息，它与带有`@RequestMapping`注解的方法处理HTTP请求的方式非常类似。但是与`@RequestMapping`不同的是，`@MessageMapping`的功能无法通过`@EnableWebMvc`启用。Spring的Web消息功能基于消息代理（message broker）构建，因此除了告诉Spring我们想要处理消息以外，还有其他的内容需要配置。我们必须要配置一个消息代理和其他的一些消息目的地。

下面的代码展示了如何通过Java配置启用基于代理的Web消息功能：

```java
package marcopolo;

import org.springframework.context.annotation.Configuration;
import org.springframework.messaging.simp.config.MessageBrokerRegistry;
import org.springframework.web.socket.config.annotation.AbstractWebSocketMessageBrokerConfigurer;
import org.springframework.web.socket.config.annotation.EnableWebSocketMessageBroker;
import org.springframework.web.socket.config.annotation.StompEndpointRegistry;

@Configuration
@EnableWebSocketMessageBroker // 启用STOMP消息
public class WebSocketStompConfig extends AbstractWebSocketMessageBrokerConfigurer {

    @Override
    public void registerStompEndpoints(StompEndpointRegistry registry) {
        // 为“/marcopolo”路径启用SockJS功能
        registry.addEndpoint("/marcopolo").withSockJS();
    }
    
    @Override
    public void configureMessageBroker(MessageBrokerRegistry registry) {
        registry.enableSimpleBroker("/queue", "/topic");
        registry.setApplicationDestinationPrefixes("/app");
    }

}
```

`WebSocketStompConfig`使用了`@EnableWebSocketMessageBroker`注解，这表明这个配置类不仅配置了WebSocket，还配置了基于代理的STOMP消息。它重写了`registerStompEndpoints()`方法，将“/marcopolo”注册为STOMP端点。这个路径与之前发送和接收消息的目的地路径有所不同。这是一个端点，客户端在订阅或发布消息到目的地路径前，要连接该端点。

同时，从重写`configureMessageBroker()`方法配置了一个简单的消息代理。这个方法是可选的，如果不进行重写，那么将会自动配置一个简单的内存消息代理。由于这里进行了重写，所以消息代理将会处理前缀为“/topic”和“/queue”的消息。除此之外，发往应用程序的消息将会带有“/app”前缀。

下图展示了配置中的消息流：

<center>
    ![图18.2-STOMP配置中的消息流](images\图18.2-STOMP配置中的消息流.PNG)
    **Spring简单的STOMP代理是基于内存的，它模拟了STOMP代理的多项功能**
</center>

当消息到达时，目的地的前缀将会决定消息该如何处理。在上图中，应用程序的目的地以“/app”作为前缀，而代理的目的地以“/topic”和“/queue”作为前缀。以应用程序为目的地的消息将会直接路由到带有`@MessageMapping`注解的控制器方法中。而发送到代理上的消息，其中也包括`@MessageMapping`注解方法的返回值所形成的消息，将会路由到代理上，并最终发送到订阅这些目的地的客户端。

**启用STOMP代理中继**

对于初学来讲，简单的代理是很不错的，但是它也有一些限制。尽管它模拟了STOMP消息代理，但是它只支持STOMP命令的子集。因为它是基于内存的，所以它并不适合集群，因为如果集群的话，每个节点也只能管理自己的代理和自己的那部分消息。

对于生产环境下的应用来说，你可能会希望使用真正支持STOMP的代理来支撑WebSocket消息，如RabbitMQ或ActiveMQ。这样的代理提供了可扩展性和健壮性更好的消息功能，当然它们也会完整支持STOMP命令。我们需要根据相关的文档来为STOMP搭建代理。搭建就绪之后，就可以使用STOMP代理来替换内存代理了，只需按照如下方式重载`configureMessageBroker()`方法即可：

```java
@Override
public void configureMessageBroker(MessageBrokerRegistry registry) {
    registry.enableStompBrokerRelay("/topic", "/queue");
    registry.setApplicationDestinationPrefixes("/app");
}
```

上述`configureMessageBroker()`方法的第一行代码启用了STOMP代理中继（broker relay）功能，并将其目的地前缀设置为“/topic”和“/queue”。这样的话，Spring就能知道所有目的地前缀为“/topic”或“/queue”的消息都会发送到STOMP代理中。根据你所选择的STOMP代理不同，目的地的可选前缀也会有所限制。例如，RabbitMQ只允许目的地的类型为“/temp-queue”、“/exchange”、“/topic”、“/queue”、“/amq/queue”和“/replyqueue”。这需要我们参阅代理的文档来确定其所支持的目的地类型及使用场景。

除了目的地前缀，在第二行的`configureMessageBroker()`方法中将应用的前缀设置为“/app”。所有目的地以“/app”打头的消息都将会路由到带有`@MessageMapping`注解的方法中，而不会发布到代理队列或主题中。

下图说明了代理中继如何应用于Spring的STOMP消息处理值中。我们可以看到，关键的区别在于这里不再模拟STOMP代理的功能，而是由代理中继将消息传送到一个真正的消息代理中来进行处理。

<center>
    ![图18.3-STOMP代理中继会将STOMP消息的处理委托给一个真正的消息代理](images\图18.3-STOMP代理中继会将STOMP消息的处理委托给一个真正的消息代理.PNG)
    **STOMP代理中继会将STOMP消息的处理委托给一个真正的消息代理**
</center>

注意，`enableStompBrokerRelay()`和`setApplicationDestinationPrefixes()`方法都接收可变长度的`String`参数，所以我们可以配置多个目的地和应用前缀。例如：

```java
@Override
public void configureMessageBroker(MessageBrokerRegistry registry) {
    registry.enableStompBrokerRelay("/topic", "/queue");
    registry.setApplicationDestinationPrefixes("/app", "/foo");
}
```

默认情况下（这里应该是使用RabbitMQ作为中级代理的默认情况），STOMP代理中继会假设代理监听localhost的61613端口，并且客户端的username和password均为“guest”。如果你的STOMP代理位于其他的服务器上，或者配置成了不同的客户端凭证，那么我们可以在启用STOMP代理中继的时候，需要配置这些细节信息：

```java
@Override
public void configureMessageBroker(MessageBrokerRegistry registry) {
    
    registry.enableStompBrokerRelay("/topic", "/queue")
            .setRelayHost("rabbit.someohterserver")
            .setRelayPort(62623)
            .setClientLogin("marcopolo")
            .setClientPasscode("letmein");
    
    registry.setApplicationDestinationPrefixes("/app", "/foo");
}
```

以上的这个配置调整了服务器、端口以及凭证信息。但是，并不是必须要配置所有的这些选项。例如，如果我们只想修改中继端口，那么可以只调用`setRelayHost()`方法，在配置中不必使用其他的Setter方法。

#### 18.3.2 处理来自客户端的STOMP消息

STOMP和WebSocket更多的是关于异步消息，与HTTP的请求-响应方式有所不同。但是，Spring提供了非常类似于Spring MVC的编程模型来处理STOMP消息。它非常地相似，以至于对STOMP消息的处理器方法也会包含在带有@Controller注解的类中。

Spring 4.0引入了`@MessageMapping`注解，它用于STOMP消息的处理，类似于Spring MVC的`@RequestMapping`注解。当消息抵达某个特定的目的地时，带有`@MessageMapping`注解的方法能够处理这些消息：

```java
@Controller
public class MarcoController {
    
    private static final Logger LOGGER = LoggerFactory.getLogger(MarcoController.class);

    // 处理发往“/app/marco”目的地的消息
    @MessageMapping("/marco")
    public void handleShout(Shout incoming) {
        LOGGER.info("Received message: " + incoming.getMessage());
    }
}
```

上述控制器非常类似于其他的Spring MVC控制器类。它使用了`@Controller`注解，所以组件扫描能够找到它并将其注册为bean。就像其他的`@Controller`类一样，它也包含了处理器方法。

但是这个处理器方法与我们之前看到的有一点区别。`handleShout()`方法没有使用`@RequestMapping`注解，而是使用了`@MessageMapping`注解。这表示`handleShout()`方法能够处理指定目的地上到达的消息。在本例中，这个目的地也就是“/app/marco”（“/app”前缀是隐含的，因为我们将其配置为应用的目的地前缀）。

因为`handleShout()`方法接收一个`Shout`参数，所以Spring的某一个消息转换器会将STOMP消息的负载转换为`Shout`对象。`Shout`类非常简单，它是只具有一个属性的JavaBean，包含了消息的内容：

```java
package marcopolo;

public class Shout {

    private String message;

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }

}
```

因为我们现在处理的不是HTTP，所以无法使用Spring的`HttpMessageConverter`实现将负载转换为`Shout`对象。Spring 4.0提供了几个消息转换器，作为其消息API的一部分。下表描述了这些消息转换器，在处理STOMP消息的时候可能会用到它们：

消息转换器 | 描述
-----|-----
`ByteArrayMessageConverter` | 实现MIME类型为“application/octetstream”的消息与byte[]之间的相互转换
`MappingJackson2MessageConverter` | 实现MIME类型为“application/json”的消息与Java对象之间的相互转换
`StringMessageConverter` | 实现MIME类型为“text/plain”的消息与String之间的相互转换

假设handleShout()方法所处理消息的内容类型为“application/json”，`MappingJackson2MessageConverter`会负责将JSON消息转换为`Shout`对象。就像在HTTP中对应的`MappingJackson2HttpMessageConverter`一样，`MappingJackson2MessageConverter`会将其任务委托给底层的Jackson 2 JSON处理器。默认情况下，Jackson会使用反射将JSON属性映射为Java对象的属性。

**处理订阅**

除了`@MessagingMapping`注解以外，Spring还提供了`@SubscribeMapping`注解。与@MessagingMapping注解方法类似，当收到STOMP订阅消息的时候，带有`@SubscribeMapping`注解的方法将会触发。

与`@MessagingMapping`方法类似，`@SubscribeMapping`方法也是通过`AnnotationMethodMessageHandler`接收消息的这就意味着`@SubscribeMapping`方法只能处理目的地以“/app”为前缀的消息。

这可能看上去有些诡异，因为应用发出的消息都会经过代理，目的地要以“/topic”或“/queue”打头。客户端会订阅这些目的地，而不会订阅前缀为“/app”的目的地。如果客户端订阅“/topic”和“/queue”这样的目的地，那么`@SubscribeMapping`方法也就无法处理这样的订阅了。如果是这样的话，`@SubscribeMapping`有什么用处呢？

`@SubscribeMapping`的主要应用场景是实现请求-回应模式。在请求-回应模式中，客户端订阅某一个目的地，然后预期在这个目的地上获得一个一次性的响应。

例如：

```java
@SubscribeMapping("/marco")
public Shout handleSubscription() {
    Shout outgoing = new Shout();
    outgoing.setMessage("Polo!");
    return outgoing;
}
```

可以看到，`handleSubscription()`方法使用了`@SubscribeMapping`注解，用这个方法来处理对“/app/marco”目的地的订阅（与`@MessageMapping`类似，“/app”是隐含的）。当处理这个订阅时，`handleSubscription()`方法会产生一个输出的Shout对象并将其返回。然后，`Shout`对象会转换成一条消息，并且会按照客户端订阅时相同的目的地发送回客户端。

如果你觉得这种请求-回应模式与HTTP GET的请求-响应模式并没有太大差别的话，那么你基本上是正确的。但是，这里的关键区别在于HTTP GET请求是同步的，而订阅的请求-回应模式则是异步的，这样客户端能够在回应可用时再去处理，而不必等待。

**编写JavaScript客户端**

`handleShout()`方法已经可以处理发送过来的消息了。现在，我们需要的就是发送消息的客户端。

如下的代码会链接“/marcopolo”端点并发送“Marco！”消息：

```js
// 创建SockJS连接
var url = 'http://' + window.location.host + '/stomp/marcopolo';
var sock = new SockJS(url);

// 创建STOMP客户端
var stomp = Stomp.over(sock);

var payload = JSON.stringify({
    'message'： 'Marco!'    
});

// 连接STOMP端点
stomp.connect('guest', 'guest', funtion(frame)) {
    // 发送消息
    stomp.send("/marco", {}, payload);
}
```

与我们之前的JavaScript客户端样例类似，在这里首先针对给定的URL创建一个SockJS实例。

但是，这里的区别在于，我们不再直接使用SockJS，而是通过调用`Stomp.over(sock)`创建了一个STOMP客户端实例。这实际上封装了SockJS，这样就能在WebSocket连接上发送STOMP消息。

接下来，我们使用STOMP进行连接，假设连接成功，然后发送带有JSON负载的消息到名为“/marco”的目的地。往`send()`方法传递的第二个参数是一个头信息的Map，它会包含在STOMP的帧中，不过在这个例子中，我们没有提供任何参数，Map是空的。

现在，我们有了能够发送消息到服务器的客户端，以及用来处理消息的服务端处理器方法。这是一个好的开端，但是你可能已经发现这都是单向的。接下来，我们让服务器发出的声音，看一下如何发送消息给客户端。

#### 18.3.3 发送消息到客户端

到目前为止，客户端负责了所有的消息发送，服务器只能监听这些消息。对于WebSocket和STOMP来说，这是一种合法的用法，但是当你考虑使用WebSocket的时候，所设想的使用场景恐怕并非如此。WebSocket通常视为服务器发送数据给浏览器的一种方式，采用这种方式所发送的数据不必位于HTTP请求的响应中。使用Spring和WebSocket/STOMP的话，该如何与基于浏览器的客户端通信呢？

Spring提供了两种发送数据给客户端得方法：

- 作为处理消息或处理订阅的附带结果
- 使用消息模板

我们已经了解了一些处理消息和处理订阅的方法，所以首先看一下如何通过这些方法发送消息给客户端。然后，再看一下Spring的`SimpMessagingTemplate`，它能够在应用的任何地方发送消息。

**在处理消息之后，发送消息**

之前的`handleShout()`只是简单地返回void。它的任务就是处理消息，并不需要给客户端回应。

如果我们想要在接收消息的时候，同时在响应中发送一条消息，那么需要做的仅仅是将内容返回就可以了，方法签名不再是使用`void`。例如，如果你想发送“Polo!”消息作为“Marco!”消息的回应，那么只需将`handleShout()`修改为如下所示：

```java
@MessageMapping("/marco")
public Shout handleShout(Shout incoming) {
    LOGGER.info("Received message: " + incoming.getMessage());
    Shout outgoing = new Shout();
    outgoing.setMessage("Polo!");
    return outgoing;
}
```

在这个新版本的`handleShout()`方法中，会返回一个新的Shout对象。通过简单地返回一个对象，处理器方法同时也变成了发送方法。当`@MessageMapping`注解标示的方法有返回值的时候，返回的对象将会进行转换（通过消息转换器）并放到STOMP帧的负载中，然后发送给消息代理。

默认情况下，帧所发往的目的地会与触发处理器方法的目的地相同，只不过会添加上“/topic”前缀。就本例而言，这意味着`handleShout()`方法所返回的`Shout`对象会写入到STOMP帧的负载中，并发布到“/topic/marco”目的地。不过，我们可以通过为方法添加`@SendTo`注解，重载目的地：

```java
@MessageMapping("/marco")
@SendTo("/topic/shout")
public Shout handleShout(Shout incoming) {
    LOGGER.info("Received message: " + incoming.getMessage());
    Shout outgoing = new Shout();
    outgoing.setMessage("Polo!");
    return outgoing;
}
```

按照这个`@SendTo`注解，消息将会发布到“/topic/shout”。所有订阅这个主题的应用（如客户端）都会收到这条消息。

这样的话，`handleShout()`在收到一条消息的时候，作为响应也会发送一条消息。按照类似的方式，`@SubscribeMapping`注解标注的方式也能发送一条消息，作为订阅的回应。例如，通过为控制器添加如下的方法，当客户端订阅的时候，将会发送一条`Shout`信息：

```java
@SubscribeMapping("/marco")
public Shout handleSubcription() {
    Shout outgoing = new Shout();
    outgoing.setMessage("Polo!");
    return outgoing;
}
```

这里的`@SubscribeMapping`注解表明当客户端订阅“/app/marco”（“/app”是应用目的地的前缀）目的地的时候，将会调用`handleSubscription()`方法。它所返回的`Shout`对象将会进行转换并发送回客户端。

`@SubscribeMapping`的区别在于这里的`Shout`消息将会直接发送给客户端，而不必经过消息代理。如果你为方法添加`@SendTo`注解的话，那么消息将会发送到指定的目的地，这样会经过代理。

`@MessageMapping`和`@SubscribeMapping`提供了一种很简单的方式来发送消息，这是接收消息或处理订阅的附带结果。不过，Spring的`SimpMessagingTemplate`能够在应用的任何地方发送消息，甚至不必以首先接收一条消息作为前提。

使用`SimpMessagingTemplate`的最简单方式是将它（或者其接口`SimpMessageSendingOperations`）自动装配到所需的对象中。

假设我们要为Spittr的首页提供一个Spittle更新的实时feed。我们不必要求用户刷新页面，而是让首页订阅一个STOMP主题，在Spittle创建的时候，改主题能够收到Spittle更新的实时feed。在首页中，我们需要添加如下的JavaScript代码：

```html
<script>
    var sock = new SockJS('spittr');
    var stomp = Stomp.over(sock);

    stomp.connect('guest', 'guest', function(frame) {
        console.log('Connected');
        stomp.subscribe("/topic/spittlefeed", handleSpittle);
    });

    function handleSpittle(incoming) {
        var spittle = JSON.parse(incoming.body);
        console.log('Received: ', spittle);
        var source = $("#spittle-template").html();
        var template = Handlebars.compile(source);
        var spittleHtml = template(spittle);
        $('.spittleList').prepend(spittleHtml);
    }
</script>
```

与之前的样例一样，我们首先创建了SockJS实例，然后基于该SockJS实例创建了Stomp实例。在连接到STOMP代理之后，我们订阅了“/topic/spittlefeed”，并指定当消息达到的时候，由`handleSpittle()`函数来处理Spittle更新。`handleSpittle()`函数会将传入的消息体解析为对应的JavaScript对象，然后使用Handlebars库将`Spittle`数据渲染为HTML并插入到列表中。Handlebars模板定义在一个单独的`<script>`标签中，如下所示：

```html
<script id="spittle-template" type="text/x-handlebars-template">
    <li id="preexist">
        <div class="spittleMessage">{{message}}</div>
        <div>
            <span class="spittleTime">{{time}}</span>
            <span class="spittleLocation">({{latitude}}, {{longitude}})</span>
        </div>
    </li>
</script>
```

在服务器端，我们可以使用`SimpMessagingTemplate`将所有新创建的`Spittle`以消息的形式发布到“/topic/spittlefeed”主题上。如下程序清单展现的`SpittleFeedServiceImpl`就是实现该功能的简单服务：

```java
@Service
public class SpittleFeedServiceImpl implements SpittleFeedService {

    // 注入消息模板
    @Autowired
    private SimpMessageSendingOperations messaging;
    
    public void broadcastSpittle(Spittle spittle) {
        // 发送消息
        messaging.convertAndSend("/topic/spittlefeed", spittle);
    }
}
```

配置Spring支持STOMP的一个副作用就是在Spring应用上下文中已经包含了`SimpMessagingTemplate`。因此，我们在这里没有必要再创建新的实例。

发送Spittle消息的地方在`broadcastSpittle()`方法中。它在注入的`SimpMessageSendingOperations`上调用了`convertAndSend()`方法，将Spittle转换为消息，并将其发送到“/topic/spittlefeed”主题上。如果你觉得`convertAndSend()`方法看起来很眼熟的话，那是因为它模拟了`JmsTemplate`和`RabbitTemplate`所提供的同名方法。

不管我们通过`convertAndSend()`方法，还是借助处理器方法的结果，在发布消息给STOMP主题的时候，所有订阅该主题的客户端都会收到消息。在这个场景下，我们希望所有的客户端都能及时看到实时的Spittle feed，这种做法是很好的。但有的时候，我们希望发送消息给指定的用户，而不是所有的客户端。

### 18.4 为目标用户发送消息

到目前为止，我们所发送和接收的消息都是客户端（在Web浏览器中）和服务器端之间的，并没有考虑到客户端的用户。当带有`@MessageMapping`注解的方法触发时，我们知道收到了消息，但是并不知道消息来源于谁。类似地，因为我们不知道用户是谁，所以消息会发送到所有订阅对应主题的客户端上，没有办法发送消息给指定用户。

但是，如果你知道用户是谁的话，那么就能处理与某个用户相关的消息，而不仅仅是与所有客户端相关联。好消息是我们已经了解了如何识别用户。通过使用与第9章相同的认证机制，我们可以使用Spring Security来认证用户，并为目标用户处理消息。

在使用Spring和STOMP消息功能的时候，我们有三种方式利用认证用户：

- `@MessageMapping`和`@SubscribeMapping`标注的方法能够使用`Principal`来获取认证用户；
- `@MessageMapping`、`@SubscribeMapping`和`@MessageException`方法返回的值能够以消息的形式发送给认证用户；
- `SimpMessagingTemplate`能够发送消息给特定用户。

首先看一下前两种方式，它们都能让控制器的消息处理方法使用针对特定用户的消息。

#### 18.4.1 在控制器中处理用户的消息

如前所述，在控制器的`@MessageMapping`或`@SubscribeMapping`方法中，处理消息时有两种方式了解用户信息。在处理器方法中，通过简单地添加一个`Principal`参数，这个方法就能知道用户是谁并利用该信息关注此用户相关的数据。除此之外，处理器方法还可以使用`@SendToUser`注解，表明它的返回值要以消息的形式发送给某个认证用户的客户端（只发送给该客户端）。但是REST请求是同步的，当服务器处理的时候，客户端必须要等待。通过将Spittle发送为STOMP消息，我们可以充分发挥STOMP消息异步的优势。

考虑如下的`handleSpittle()`方法，他会处理传入的消息并将其存储为`Spittle`：

```java
@MessageMapping("/spittle")
@SendToUser("/queue/notifications")
public Notification handleSpittle(Principal principal, SpittleForm form) {
    Spittle spittle = new Spittle(principal.getName(), form.getText(), new Date());
    spittleRepo.save(spittle);
    return new Notification("Saved Spittle");
}
```

可以看到，`handleSpittle()`方法接受`Principal`对象和`SpittleForm`对象作为参数。它使用这两个对象创建一个`Spittle`实例并借助`SpittleRepository`将实例保存起来。最后，它返回一个新的`Notification`，表明`Spittle`已经保存成功。

因为这个方法使用了`@MessageMapping`注解，因此当有发往“/app/spittle”目的地的消息到达时，该方法就会触发，并且会根据消息创建`SpittleForm`对象，如果用户已经认证过的话，将会根据STOMP帧上的头信息得到`Principal`对象。

但是，需要特别关注的是，返回的`Notification`到哪里去了。`@SendToUser`注解指定返回的`Notification`要以消息的形式发送到“/queue/notifications”目的地上。在表面上，“/queue/notifications”并没有与特定用户关联。但因为这里使用的是`@SendToUser`注解而不是`@SendTo`，所以就会发生更多的事情了。

为了理解Spring如何发布消息，让我们先退后一步，看一下针对控制器方法发布`Notification`对象的目的地，客户端该如何进行订阅。考虑如下的这行JavaScript代码，它订阅了一个用户特定的目的地：

```js
stomp.subscribe("/user/queue/notifications", handleNotifications);
```

这个目的地使用了“/user”作为前缀，在内部，以“/user”作为前缀的目的地将会以特殊的方式进行处理。这种消息不会通过`AnnotationMethodMessageHandler`（像应用消息那样）来处理，也不会通过`SimpleBrokerMessageHandler`或`StompBrokerRelayMessageHandler`（像代理消息那样）来处理，以“/user”为前缀的消息将会通过`UserDestinationMessageHandler`进行处理，如下图所示：

<center>
    ![图18.4-用户消息流会通过UserDestinationMessageHandler进行处理](images\图18.4-用户消息流会通过UserDestinationMessageHandler进行处理.PNG)
    **用户消息流会通过UserDestinationMessageHandler进行处理，它会将消息重路由到某个用户独有的目的地上**
</center>    

`UserDestinationMessageHandle`r的主要任务是将用户消息重新路由到某个用户独有的目的地上。在处理订阅的时候，它会将目标地址中的“/user”前缀去掉，并基于用户的会话添加一个后缀。例如，对“/user/queue/notifications”的订阅最后可能路由到名为“/queue/notifications-user6hr83v6t”的目的地上。

在我们的样例中，`handleSpittle()`方法使用了@SendToUser("/queue/notifications")注解。这个新的目的地以“/queue”作为前缀，根据配置，这是`StompBrokerRelayMessageHandler`（或`SimpleBrokerMessageHandler`要处理的前缀，所以消息接下来会到达这里。最终，客户端会订阅这个目的地，因此客户端会收到`Notification`消息。

在控制器方法中，`@SendToUser`注解和`Principal`参数是很有用的。接下来看一下如何使用`SimpMessagingTemplate`将消息发送给特定用户。

#### 18.4.2 为指定用户发送消息

除了`convertAndSend()`以外，`SimpMessagingTemplate`还提供了`convertAndSendToUser()`方法。按照名字就可以判断出来，`convertAndSendToUser()`方法能够让我们给特定用户发送消息。

为了阐述该功能，我们要在Spittr应用中添加一项特性，当其他用户提交的Spittle提到某个用户时，将会提醒该用户。例如，如果Spittle文本中包含“@jbauer”，那么我们就应该发送一条消息给使用“jbauer”用户名登录的客户端。如下程序清单中的`broadcastSpittle()`方法使用了`convertAndSendToUser()`，从而能够提醒所谈论到的用户。

```java
@Service
public class SpittleFeedServiceImpl implements SpittleFeedService {

    @Autowired
    private SimpMessagingTemplate messaging;

    // 实现@功能的正则表达式
    private Pattern pattern = Pattern.compile("\\@(\\S+)");

    public void broadcastSpittle(Spittle spittle) {
        messaging.convertAndSend("/topic/spittlefeed", spittle);
        Matcher matcher = pattern.matcher(spittle.getMessage());
        if (matcher.find()) {
            String username = matcher.group(1);
            // 发送提醒给用户
            messaging.convertAndSendToUser(username, "/queue/notifications", new Notification("You just got mentioned!"));
        }
    }
}
```

在`broadcastSpittle()`中，如果给定`Spittle`对象的消息中包含了类似于用户名的内容（也就是以“@”开头的文本），那么一个新的`Notification`将会发送到名为“/queue/notifications”的目的地上。因此，如果Spittle中包含“@jbauer”的话，`Notification`将会发送到“/user/jbauer/queue/notifications”目的地上。

### 18.5 处理消息异常

有时候，事情并不会按照我们预期的那样发展。在处理消息的时候，有可能会出错并抛出异常。因为STOMP消息异步的特点，发送者可能永远也不会知道出现了错误。除了Spring的日志记录以外，异常有可能会丢失，没有资源或机会恢复。

在Spring MVC中，如果在请求处理中，出现异常的话，`@ExceptionHandler`方法将有机会处理异常。与之类似，我们也可以在某个控制器方法上添加`@MessageExceptionHandler`注解，让它来处理`@MessageMapping`方法所抛出的异常。

例如，如下的方法会处理消息方法所抛出的异常：

```java
@MessageExceptionHandler
public void handleExceptions(Throwable t) {
    LOGGER.error("Error handling message: " + t.getMessage());
}
```

按照最简单的形式，`@MessageExceptionHandler`标注的方法能够处理消息方法中所抛出的异常。但是，我们也可以以参数的形式声明它所能处理的异常：

```java
@MessageExceptionHandler("SpittleException.class")
public void handleExceptions(Throwable t) {
    LOGGER.error("Error handling message: " + t.getMessage());
}
```

尽管它只是以日志的方式记录了所发生的错误，但是这个方法可以做更多的事情。例如，它可以回应一个错误：

```java
@MessageExceptionHandler("SpittleException.class")
@SendToUser("/queue/errors")
public SpittleException handleExceptions(SpittleException e) {
    LOGGER.error("Error handling message: " + e.getMessage());
    return e;
}
```

在这里，如果抛出`SpittleException`的话，将会记录这个异常，然后将其返回。`UserDestinationMessageHandler`会重新路由这个消息到特定用户所对应的唯一路径。

### 18.6 小结

>
如果在应用间发送消息的话，那WebSocket是一种令人兴奋的通信方式，尤其是如果其中某个应用运行在Web浏览器中更是如此。当编写存在大量交互的Web应用程序时，它是很重要的，能够实现从服务器无缝的发送和接收数据。
>
Spring对WebSocket的支持包括低层级的API，它能够让我们使用原始的WebSocket连接。但是，WebSocket并没有在Web浏览器、服务器以及网络代理上得到广泛支持。因此，Spring同时还支持SockJS，这个协议能够在WebSocket不可用的时候提供备用的通信模式。
>
Spring还提供了高级的编程模型，也就是使用STOMP线路级协议来处理WebSocket消息。在这个更高级的模型中，能够在Spring MVC控制器中处理STOMP消息，类似于处理HTTP消息的方式。
>
在过去的两章中，我们看到了多种在应用间异步发送消息的方式。Spring还有另外一种处理异步消息的方式。在下一章中，我们将会看到如何使用Spring发送Email。


## 第十九章 使用Spring发送Email

本章内容：

- 配置Spring的Email抽象功能
- 发送丰富内容的Email消息
- 使用模板构建Email消息

毫无疑问，Email已经成为常见的通信形式，取代了很多传统的通信方式，如邮政邮件、电话，在一定程度上也替代了面对面的交流。Email能够提供了与第17章中所讨论的异步消息相同的收益，只不过发送者和接收者都是实际的人而已。只要你在邮件客户端上点击“发送”按钮，就可以转移到其他的任务中了，因为我们知道接收者最终将会收到并阅读（希望如此）你的Email。

但是，Email的发送者不一定是实际的人。有时候，Email消息是由应用程序发送给用户的。有可能是电子商务网站上的订单确认邮件，也有可能是银行账户某项交易的自动提醒。不管邮件的主题是什么，我们都可能需要开发发送Email消息的应用程序。幸好，在这个方面，Spring会为我们提供帮助。

### 19.1 配置Spring发送邮件

Spring Email抽象的核心是`MailSender`接口。这个接口的实现能够连接Email服务器实现邮件发送功能：

<center>
    ![图19.1-Spring的MailSender接口是Spring Email抽象API的核心组件](images\图19.1-Spring的MailSender接口是Spring Email抽象API的核心组件.PNG)
    **Spring的MailSender接口是Spring Email抽象API的核心组件。它把Email发送给邮件服务器，由服务器进行邮件投递**
</center>

Spring自带了一个`MailSender`实现，也就是`JavaMailSenderImpl`。它会使用JavaMail API来发送Email。Spring应用在发送Email之前，我们必须要将`JavaMailSenderImpl`装配为Spring应用上下文中的一个bean。

#### 19.1.1 配置邮件发送器

按照最简单的形式，我们只需要在`@Bean`方法中使用几行代码就能将`JavaMailSenderImpl`配置为一个bean：

```java
@Bean
public MailSender mailSender(Environment env) {
    JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
    mailSender.setHost(env.getProperty("mailserver.host"));
    return mailSender;
}
```

属性host是可选的（它默认是底层JavaMail会话的主机），它指定了要用来发送Email的邮件服务器主机名。按照这里的配置，会从注入的`Environment`中获取值，这样我们就能在Spring之外管理邮件服务器的配置（比如在属性文件中）。

默认情况下，JavaMailSenderImpl假设邮件服务器监听25端口（标准的SMTP端口）。当然，如果我们的邮件服务器监听不同的端口，那么我们也可以进行指定：

```java
@Bean
public MailSender mailSender(Environment env) {
    JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
    mailSender.setHost(env.getProperty("mailserver.host"));
    mailSender.setPort(Integer.parseInt(env.getProperty("mailserver.port")));
    return mailSender;
}
```

类似地，如果邮件服务器需要认证的话，我们还需要设置`username`和`password`属性：

```java
@Bean
public MailSender mailSender(Environment env) {
    JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
    mailSender.setHost(env.getProperty("mailserver.host"));
    mailSender.setPort(Integer.parseInt(env.getProperty("mailserver.port")));
    mailSender.setUsername(env.getProperty("mailserver.username"));
    mailSender.setPassword(env.getProperty("mailserver.password"));
    return mailSender;
}
```

到目前为止，`JavaMailSenderImpl`已经配置完成，它可以创建自己的邮件会话。但如果我们已经在JNDI中配置了`Session`，那就没有必要为`JavaMailSenderImpl`配置详细的服务器细节了，我们可以配置它使用JNDI中已经就绪的`Session`。

借助`JndiObjectFactoryBean`，我们可以在如下的`@Bean`方法中配置一个bean，他会从JNDI中查找`Session`：

```java
@Bean
public JndiObjectFactoryBean mailSession() {
    JndiObjectFactoryBean jndi = new JndiObjectFactoryBean();
    jndi.setJndiName("mail/Session");
    jndi.setProxyInterface(Session.class);
    jndi.setResourceRef(true);
    return jndi;
}
```

或者使用XML进行配置：

```xml
<jee:jndi-lookup id="mailSession" jndi-name="mail/Session" resource-ref="true" />
```

邮件会话准备就绪之后，我们现在可以将其装配到`mailSender` bean中了：

```java
@Bean
public MailSender mailSender(Session session) {
    JavaMailSenderImpl mailSender = new JavaMailSenderImpl();
    mailSender.setSession(session);
    return mailSender;
}
```

通过将邮件会话装配到JavaMailSenderImpl的session属性中，我们已经完全替换了原来的服务器（以及用户名/密码）配置。现在邮件会话完全通过JNDI进行配置和管理。JavaMailSenderImpl能够专注于发送邮件而不必自己处理邮件服务器了。

#### 19.1.2 装配和使用邮件发送器

邮件发送器已经配置完成，现在需要将其装配到使用它的bean中了。

定义一个用于发送邮件的`SpitterEmailServiceImpl`类，这个类实现接口`SpitterEmailService`：

```java
public interface SpitterMailService {
    void sendSimpleSpittleEmail(String to, Spittle spittle);
}
```

`SpitterEmailServiceImpl`类使用`@Autowired`注解自动注入`JavaMailSender`：

```java
@Autowired
private JavaMailSender mailSender;
```

`mailSender` bean装配完成后，我们就可以构建和发送Email了。

我们想要给Spitter用户发送Email提示他的朋友写了新的Spittle，所以我们需要一个方法来发送Email，这个方法要接受Email地址和`Spittle`对象信息。如下的`sendSimpleSpittleEmail()`方法使用邮件发送器完成了该功能：

```java
@Override
public void sendSimpleSpittleEmail(String to, Spittle spittle) {
    SimpleMailMessage message = new SimpleMailMessage();
    String spitterName = spittle.getSpitter().getFullName();
    // 设置发件人地址
    message.setFrom("noreply@spitter.com");
    // 设置收件人地址
    message.setTo(to);
    // 设置邮件标题
    message.setSubject("New spittle from " + spitterName);
    // 设置邮件正文
    message.setText(spitterName + " says: " + spittle.getText());
    // 发送Email
    mailSender.send(message);
}
```

`sendSimpleSpittleEmail()`方法所做的第一件事就是构造`SimpleMailMessage`实例。正如其名称所示，这个对象可以很便捷地发送Email消息。

接下来，将设置消息的细节。通过邮件消息的`setFrom()`和`setTo()`方法指定了Email的发送者和接收者。在通过`setSubject()`方法设置完主题后，虚拟的“信封”已经完成了。剩下的就是调用`setText()`方法来设置消息的内容。

最后一步是将消息传递给邮件发送器的`send()`方法，这样邮件就发送出去了。

现在，我们已经配置好了邮件发送器并使用它来发送简单的Email消息。可以看到，使用Spring的Email抽象非常简单。我们可以到此为止并转到下一章，但是如果这样的话将会错过Spring Email抽象中很有意思的内容。让我们更进一步，看一下如何添加附件并创建丰富内容的Email消息。

### 19.2 构建丰富内容的Email消息

对于简单的事情来讲，纯文本的Email消息是比较合适的。但是，如果你要发送照片或文档的话，这种方式就不那么理想了。如果作为市场推广Email的话，它也无法吸引接收者的注意。

幸好，Spring的Email功能并不局限于纯文本的Email。我们可以添加附件，甚至可以使用HTML来美化消息体的内容。让我们首先从基本的添加附件开始，然后更进一步，借助HTML使我们的Email消息更加美观。

#### 19.2.1 添加附件

如果发送带有附件的Email，关键技巧是创建`multipart`类型的消息——Email由多个部分组成，其中一部分是Email体，其他部分是附件。

对于发送附件这样的需求来说，`SimpleMailMessage`过于简单了。为了发送`multipart`类型的Email，你需要创建一个MIME（Multipurpose Internet Mail Extensions）的消息，我们可以从邮件发送器的`createMimeMessage()`方法开始。

```java
MimeMessage message = mailSender.createMimeMessage();
```

就这样，我们已经有了要使用的MIME消息。看起来，我们所需要做的就是指定收件人和发件人地址、主题、一些内容以及一个附件。尽管确实是这样，但并不是你想的那么简单。`javax.mail.internet.MimeMessage`本身的API有些笨重。好消息是，Spring提供的`MimeMessageHelper`可以帮助我们。

为了使用`MimeMessageHelper`，我们需要实例化它并将`MimeMessage`传给其构造器：

```java
MimeMessageHelper helper = new MimeMessageHelper(message, true);
```

构造方法的第二个参数，在这里是个布尔值`true`，表明这个消息是`multipart`类型的。

得到了`MimeMessageHelper`实例后，我们就可以组装Email消息了。这里最主要区别在于使用helper的方法来指定Email细节，而不再是设置消息对象。

重新定义接口，添加方法：

```java
public interface SpitterMailService {

    void sendSimpleSpittleEmail(String to, Spittle spittle);

    void sendSpittleEmailWithAttachment(String to, Spittle spittle) throws MessagingException;

}
```

接下来进行实现：

```java
@Override
public void sendSpittleEmailWithAttachment(String to, Spittle spittle) throws MessagingException {

    // 创建MimeMessage对象
    MimeMessage message = mailSender.createMimeMessage();

    // 创建MimeMessageHelper对象
    MimeMessageHelper helper = new MimeMessageHelper(message, true);
    String spitterName = spittle.getSpitter().getFullName();
    helper.setFrom("noreply@spitter.com");
    helper.setTo(to);
    helper.setSubject("New spittle from " + spitterName);
    helper.setText(spitterName + " says: " + spittle.getText());

    // 添加附件
    ClassPathResource couponImage = new ClassPathResource("/collateral/coupon.png");
    helper.addAttachment("Coupon.png", couponImage);
    mailSender.send(message);
}
```

在这里，我们使用Spring的`ClassPathResource`来加载位于应用类路径下的coupon.png。然后，调用`addAttachment()`。第一个参数是要添加到Email中附件的名称，第二个参数是图片资源。

`multipart`类型的Email能够实现很多的功能，添加附件只是其中之一。除此之外，通过将Email体指明为HTML，我们可以生成比简单文本更漂亮的Email。接下来，看一下如何使用`MimeMessageHelper`来发送更吸引人的Email。

#### 19.2.2 发送富文本内容的Email

发送富文本的Email与发送简单文本的Email并没有太大区别。关键是将消息的文本设置为HTML。要做到这一点只需将HTML字符串传递给helper的`setText()`方法，并将第二个参数设置为`true`：

```java
helper.setText(
    "<html><body><img src='cid:spitterLogo'>" + "<h4>" + spittle.getSpitter().getFullName() + " say...</h4>" + "<i>" + spittle.getText() + "</i>" + "</body></html>", true
);
```

上述方法与纯Servlet编写JSP页面很相似。第二个参数表明传递进来的第一个参数是HTML，所以需要对消息的内容类型进行相应的设置。

要注意的是，传递进来的HTML包含了一个`<img>`标签，用来在Email中展现Spittr应用程序的logo。`src`属性可以设置为标准的“http:”URL，以便于从Web中获取Spittr的logo。但在这里，我们将logo图片嵌入在了Email之中。值“cid:spitterLogo”表明在消息中会有一部分是图片并以spitterLogo来进行标识。

为消息添加嵌入式的图片与添加附件很类似。不过这次不再使用helper的`addAttachment()`方法，而是要调用`addInline()`方法：

```java
ClassPathResource image = new ClassPathResource("spitter_logo_50.png");
helper.addInline("spitterLogo", image);
```

`addInline()`的第一个参数表明内联图片的标识符——与`<img>`标签的`src`属性所指定的相同。第二个参数是图片的资源引用，这里使用`ClassPathResource`从应用程序的类路径中获取图片。

除了`setText()`方法稍微不同以及使用了`addInline()`方法以外，发送含有富文本内容的Email与发送带有附件的普通文本消息很类似。为了进行对比，以下是新的`sendRichSpitterEmail()`方法。

现在你发送的Email带有富文本内容和嵌入式图片了！你可以到此为止并完全结束你的Email代码。但创建Email体时，使用字符串拼接的办法来构建HTML消息依旧让我觉得美中不足。在结束Email话题之前，让我们看看如何用模板来代替字符串拼接消息。

### 19.3　使用模板生成Email

使用字符串拼接来构建Email消息的问题在于Email最终会是什么样子并不清晰。在你的大脑中解析HTML标签并想象它在渲染时会是什么样子是挺困难的。而将HTML混合在Java代码中又会使得这个问题更加复杂。如果能够将Email的布局抽取到一个模板中，而这个模板可以由美术设计师（可能是很讨厌Java代码的人）来完成将会是很棒的一件事。

我们需要与最终HTML接近的方式来表达Email布局，然后将模板转换成`String`并传递给helper的`setText()`方法。在将模板转换为`String`时，我们有多种模板方案可供选择，包括Apache Velocity和Thymeleaf。让我们看一下如何使用这两种方案创建富文本的Email消息，先从Velocity开始吧。

#### 19.3.1 使用Velocity构建Email消息

（注意，Spring已经不推荐使用Velocity了，`VelocityEngineFactoryBean`也已经被标注为`@Deprecated`。同时，Spring推荐使用FreeMaker）

Apache Velocity是由Apache提供的通用模板引擎。Velocity有挺长的历史了，并且已经应用于各种任务中，包括代码生成以及代替JSP。它还能用于格式化富文本Email消息，也就是我们在这里的用法。

为了使用Velocity对Email进行布局，我们需要将`VelocityEngine`装配到`SpitterEmailServiceImpl`中。Spring提供了一个名为`VelocityEngineFactoryBean`的工厂bean，它能够在Spring应用上下文中很便利地生成`VelocityEngine`。`VelocityEngineFactoryBean`的声明如下：

```java
@Bean
public VelocityEngineFactoryBean velocityEngine() {
    VelocityEngineFactoryBean engine = new VelocityEngineFactoryBean();
    Properties properties = new Properties();
    properties.setProperty("resource.loader", "class");
    properties.setProperty("class.resource.loader.class", ClasspathResourceLoader.class.getName());
    engine.setVelocityProperties(properties);
    return engine;
}
```

`VelocityEngineFactoryBean`唯一要设置的属性是`velocityProperties`。在本例中，我们将其配置为从类路径下加载Velocity模板。

现在，我们可以将Velocity引擎装配到`SpitterEmailServiceImpl`中。因为`SpitterEmailServiceImpl`是使用组件扫描实现自动注册的，我们可以使用`@Autowired`来自动装配`velocityEngine`属性：

```java
@Autowired
private VelocityEngine velocityEngine;
```

现在，`velocityEngine`已经可用了，我们可以使用它将Velocity模板转换为`String`，并作为Email文本进行发送。为了帮助我们完成这一点，Spring自带了`VelocityEngineUtils`来简化将Velocity模板与模型数据合并成String的工作。以下是我们可能的使用方式：

```java
Map<String, String> model = new HashMap<>();
model.put("spitterName", spitterName);
model.put("spitterText", spittle.getText());
String emailText = VelocityEngineUtils.mergeTemplateIntoString(velocityEngine, "emailTemplate.vm", model);
```

为了给处理模板做准备，我们首先创建了一个`Map`用来保存模板使用的模型数据。在前面字符串拼接的代码中，我们需要Spitter的全名及其Spittle的文本，这里也是一样。为了产生合并后的Email文本，我们只需调用`VelocityEngineUtils`的`mergeTemplateIntoString()`方法并将Velocity引擎、模板路径（相对于类路径根）以及模型`Map`传递进去。

最终，我们将合并后的Email文本传递给helper的setText()方法：

```java
helper.setText(emailText, true);
```

模板位于类路径的根目录下，是一个名为emailTemplate.vm的文件，它看起来可能是这样的：

```html
<html>
<body>
    <img src='cid:spitterLogo'>
    <h4>${spitterName} says...</h4>
    <i>${spittleText}</i>
</body>
</html>
```

接下来，我们看一下如何使用Thymeleaf来构建Spittle Email消息。

### 19.3.2　使用Thymeleaf构建Email消息

Thymeleaf是一种很有吸引力的HTML模板引擎，因为它能够创建WYSIWYG（What You See Is What You Get）的模板。与JSP和Velocity不同，Thymeleaf模板不包含任何特殊的标签库和特有的标签。这样模板设计师在工作的时候，能够使用任意他们所喜欢的HTML工具，而不必担心某个工具无法处理特定的标签。

当我们将Email模板转换为Thymeleaf模板时，Thymeleaf的WYSIWYG特性体现得非常明显：

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<body>
    <img src="spitterLogo.png" th:src='cid:spitterLogo'>
    <h4><span th:text="${spitterName}">Craig Walls</span> says...</h4>
    <i><span th:text="${spittleText}">Hello there!</span></i>
</body>
</html>
```

这里没有任何自定义的标签，尽管模型属性是通过“${}”标记的，但是它们仅用于属性的值中，不会像Velocity那样用在外边。这种模板可以很容易地在Web浏览器中打开，并且以完整的形式进行展现，不必依赖于Thymeleaf引擎的处理。

使用Thymeleaf来生成和发送Email消息的做法与Velocity十分类似：

```java
Context ctx = new Context();
ctx.setVariable("spitterName", spitterName);
ctx.setVariable("spittleText", spittle.getText());
String emailText = thymeleafEngine.process("emailTemplate.html", ctx);
// ...
helper.setText(emailText, true);
mailSender.send(message);
```

这里做的第一件事情就是创建Thymeleaf `Context`实例，并将模型数据填充进去。这与我们使用Velocity的时候，将模型数据填充到`Map`中很类似。然后，我们要求Thymeleaf处理模板，通过调用Thymeleaf引擎的`process()`方法，将上下文中的模型数据合并到模板中。最后，我们将结果形成的文本借助消息helper设置到Email消息中，并使用邮件发送器将消息发送出去。

这里的Thymeleaf引擎与我们在第六章构建Web视图时使用的`SpringTemplateEngine` bean是相同的。

不过，我们必要要对`SpringTemplateEngine` bean做一点小修改。在第六章中，它配置为从Servlet上下文中解析模板：

```java
@Bean
public ITemplateResolver webTemplateResolver() {

    WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();

    ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());
    
    templateResolver.setPrefix("/WEB-INF/thymeleaf/");
    templateResolver.setSuffix(".html");
    templateResolver.setTemplateMode(TemplateMode.HTML);
    return templateResolver;
}
```

而我们的Email模板需要从类路径中解析。所以我们还需要一个`ClassLoaderTemplateResolver`：

```java
@Bean
public ITemplateResolver emailTemplateResolver() {
    ClassLoaderTemplateResolver resolver = new ClassLoaderTemplateResolver();
    resolver.setPrefix("mail/");
    resolver.setTemplateMode(TemplateMode.HTML);
    resolver.setCharacterEncoding("UTF-8");
    resolver.setOrder(1);
    return resolver;
}
```

就大部分而言，配置`ClassLoaderTemplateResolver` bean的方式类似于`ServletContextTemplateResolver`。不过，需要注意，我们将prefix属性设置为“mail/”，这表明它会在类路径根的“mail”目录下开始查找Thymeleaf模板。因此，Email模板文件的名字必须是emailTemplate.html，并且位于类路径根的“mail”目录下。

因为我们现在有两个模板解析器，所以需要使用`order`属性表明优先使用哪一个。`ClassLoaderTemplateResolver`的`order`属性为1，因此我们修改一下`ServletContextTemplateResolver`，将其`order`属性设置为2：

```java
@Bean
public ITemplateResolver webTemplateResolver() {

    WebApplicationContext webApplicationContext = ContextLoader.getCurrentWebApplicationContext();
    ServletContextTemplateResolver templateResolver = new ServletContextTemplateResolver(webApplicationContext.getServletContext());
    templateResolver.setPrefix("/WEB-INF/thymeleaf/");
    templateResolver.setSuffix(".html");
    templateResolver.setTemplateMode(TemplateMode.HTML);
    templateResolver.setOrder(2);
    return templateResolver;
}
```

现在，剩下的任务就是修改`SpringTemplateEngine` bean的配置，让它使用这两个模板解析器：

```java
@Bean
public TemplateEngine templateEngine(Set<ITemplateResolver> templateResolvers) {
    SpringTemplateEngine templateEngine = new SpringTemplateEngine();
    templateEngine.setTemplateResolvers(templateResolvers);
    return templateEngine;
}
```

在此之前，我们只有一个模板解析器，所以可以将其注入到`SpringTemplateEngine`的`templateResolver`属性中。但现在我们有了两个模板解析器，所以必须将它们作为`Set`的成员，然后将这个Set注入到`templateResolvers`（复数）属性中。

### 19.4 小结

>
Email是人与人之间通信的重要形式，通常也是应用与人进行通信的一种形式。Spring基于Java所提供的Email功能，抽象了JavaMail，使得在Spring中使用和配置起来都更加简单。
>
在本章中，我们看到了如何使用Spring的Email抽象功能发送简单的Email消息，然后更进一步，学习了如何发送包含附件和经过HTML格式化的富文本消息。我们还看到了如何使用像Velocity和Thymeleaf这样的模板引擎生成富文本Email文本，避免了通过字符串拼接创建HTML。
>
在下一章中，我们将会学习如何借助Java管理扩展（Java Management Extensions，JMX）为Spring bean添加管理和通知功能。
