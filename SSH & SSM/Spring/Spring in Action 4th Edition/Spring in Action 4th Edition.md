[TOC]

# Spring实战 第四版 -- Spring in Action 4th Edition

*使用Eclipse + Gradle构建工程，Spring版本为4.3.9，OS为Windows10。相较于书中给出的源码，这里对一些类或语句以及写法做了少许修改*。

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
package sia.knights.test;

import org.junit.Test;

import sia.knights.BraveKnight;
import sia.knights.Quest;

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

和`CompactDisc`接口一样，`SgtPeppers`的具体内容并不重要。你需要注意的就是SgtPeppers类上使用了`@Component`注解。这个简单的注解表明该类会作为组件类，并告知Spring要为这个类创建bean。没有必要显式配置`SgtPeppers` bean，因为这个类使用了`@Component`注解，所以Spring会为你把事情处理妥当。

不过，**组件扫描默认是不启用的**。我们还需要显式配置一下Spring，从而命令它去寻找带有`@Component`注解的类，并为其创建bean。

```java
package soundsystem;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class CDPlayerConfig {

}
```

类`CDPlayerConfig`通过Java代码定义了Spring的装配规则。在2.3节中，我们还会更为详细地介绍基于Java的Spring配置。不过，现在我们只需观察一下`CDPlayerConfig`类并没有显式地声明任何bean，只不过它使用了`@ComponentScan`注解，这个注解能够在Spring中启用组件扫描。

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
@Configuration
// @ComponentScan("soundsystem")
@ComponentScan(basePackages = "soundsystem")
public class CDPlayerConfig {

}
```

根据源码，`value`和`basePackages`属性的类型是`String[]`，所以我们可以指定多个包：

```java
@Configuration
@ComponentScan(basePackages = {"soundsystem", "video"})
public class CDPlayerConfig {

}
```

在上面的例子中，所设置的基础包是以`String`类型逐一表示的。这样做是可以的，但这种方法是类型不安全（not type-safe）的。如果我们重构代码的话，那么所指定的基础包可能就会出现错误了。

`@ComponentScan`还提供了另外一种方法，那就是将其指定为包中所包含的类或接口，这需要使用属性`basePackageClasses`，它的类型是`Class<?>[]`：

```java
@Configuration
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

上一节的`CDPlayerConfig`类就是JavaConfig的一个样例：

```java
package soundsystem;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDPlayerConfig {

}
```

创建JavaConfig类的关键在于为其添加`@Configuration`注解，`@Configuration`注解表明这个类是一个配置类，该类应该包含在Spring应用上下文中如何创建bean的细节。

这里由于我们要尽量使用显式配置，所以`@ComponentScan`被移除了。

移除了`@ComponentScan`注解，此时的`CDPlayerConfig`类就没有任何作用了。如果你现在运行`CDPlayerTest`的话，测试会失败，并且会出现`NoSuchBeanDefinitionException`异常。测试期望被注入`CDPlayer`和`CompactDisc`，但是这些bean根本就没有创建，因为组件扫描不会发现它们。

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
