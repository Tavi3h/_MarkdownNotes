[TOC]

# Java并发编程实战

## 第一部分

### 第2章 线程安全性

一个对象是否需要是线程安全的，取决于它是否被多个线程访问。这指的是在程序中访问对象的方式，而不是对象要实现的功能。要使得对象是线程安全的，需要采用同步机制来协同对对象可变状态的访问。如果无法实现协同，那么可能会导致数据破坏以及其他不该出现的结果。

Java中的主要同步机制是关键字`synchronized`，它提供了一种独占的加锁方式，但“同步”这个术语还包括`volatile`类型的变量、显式锁（Explicit Lock）以及原子变量。

如果当多个线程访问同一个可变的状态变量时没有使用合适的同步，那么程序就会出现错误。有三种方式可以修复这个问题：

- 不在线程之间共享该状态变量。
- 将状态变量修改为不可变的变量。
- 在访问状态变量时使用同步。

在编写并发应用程序时，一种正确的编程方法就是：首先使代码正确运行，然后再提高代码的速度。即便如此，最好也只是当性能测试结果和应用需求告诉你必须提高性能，以及测试结果表明这种优化在实际环境中确实能带来性能提升时，才进行优化。

#### 2.1 什么是线程安全性

**线程安全性：**当多个线程访问某个类时，这个类始终都能表现出正确的行为，那么就称这个类是线程安全的。

示例：一个无状态的的Servlet

如下是一个简单的因数分解Servlet。这个Servlet从请求中提取出数值，执行因数分解，然后将结果封装到响应中。

```java
package pers.tavish.jcip.ch2threadsafety;

import net.jcip.annotations.ThreadSafe;

import javax.servlet.GenericServlet;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.math.BigInteger;

// 程序清单2-1
@ThreadSafe
public class StatelessFactorizer extends GenericServlet {

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        encodeIntoResponse(resp, factors);
    }

    private void encodeIntoResponse(ServletResponse resp, BigInteger[] factors) {

    }

    // Doesn't really factor
    private BigInteger[] factor(BigInteger i) {
        return new BigInteger[]{i};
    }

    private BigInteger extractFromRequest(ServletRequest req) {
        return new BigInteger("7");
    }
}
```

与大多数Servlet相同，`StatelessFactorizer`是无状态的：

- 它不包含任何域。
- 它不包含任何对其他类中域的引用。
- 计算过程中的临时状态仅存在与线程栈上的局部变量，并且只能由正在执行的线程访问。

访问`StatelessFactorizer`的线程不会影响另一个访问同一个`StatelessFactorizer`的线程的计算结果，因为这两个线程并没有共享状态，就好像它们在访问不同的实例。

**由于线程访问无状态对象的行为并不会影响其他线程中操作的正确性，因此无状态对象是线程安全的。**

大多数Servlet都是无状态的，从而极大地降低了在实现Servlet线程安全时的复杂性。只有当Servlet在处理请求时需要保存一些信息时，线程安全性才会成为一个问题。


#### 2.2 原子性

假设我们希望增加一个计数器来统计所处理的请求数量。一种直观的方法是在Servlet中增加一个`long`类型的域，并且每处理一个请求就将这个值加1，如下`UnSafeCountingFactorizer`所示：

```java
package pers.tavish.jcip.ch2threadsafety;

import lombok.Getter;
import net.jcip.annotations.NotThreadSafe;

import javax.servlet.GenericServlet;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.math.BigInteger;

// 程序清单2-2
@Getter
@NotThreadSafe
public class UnsafeCountingFactorizer extends GenericServlet {

    private long count = 0;

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        ++count;
        encodeIntoResponse(resp, factors);
    }

    private void encodeIntoResponse(ServletResponse resp, BigInteger[] factors) {

    }

    // Doesn't really factor
    private BigInteger[] factor(BigInteger i) {
        return new BigInteger[]{i};
    }

    private BigInteger extractFromRequest(ServletRequest req) {
        return new BigInteger("7");
    }
}
```

`UnsafeCountingFactorizer`并非线程安全的，尽管在单线程中能正确运行。与`UnSafeSequence`一样，这个类很可能会丢失一些更新操作。虽然递增操作`++count`是一个紧凑的语法，但这个操作并非原子的，因而它并不会作为一个不可分割的操作来执行。实际上，它包含了三个独立的操作：读取`count`的值，将值加1，然后将计算结果写入`count`。这是一个“读取-修改-写入”的操作序列，并且其结果状态依赖于之前的状态

例如，在某些情况下，每个线程读到的值都为9，接着执行递增操作，并且都将计数器的值设置为10。显然，这并不是我们希望看到的，如果有一次递增操作丢失了，计数器的值就将偏差1.

一些人可能会认为，在基于Web的服务中，计数器的值的少量偏差或许是可以接受的。诚然，在某些情况下确实如此。但如果该计数器被用来生成数值序列或者唯一的对象标识符，那么在多次调用中返回相同的值将导致严重的数据完整性问题、或者出现失效数据（Stale Data）问题。

在并发编程中，这种由于不恰当的执行时序而出现不正确的结果是一种非常重要的情况，它有一个正式的名字：**竞态条件（Race Condition）。**

##### 2.2.1 竞态条件

在`UnsafeCountingFactorizer`中存在多个竞态条件，从而使结果变得不可靠。**当某个计算的正确性取决于多个线程的交替执行时序时，那么就会发生竞态条件。**最常见的竞态条件类型就是“先检查后执行（Check-Then-Act）”操作，即通过一个可能失效的观测结果来决定下一步的动作。

在实际情况中经常会遇到竞态条件。例如，假定你计划中午在University Avenue的星巴克与一位朋友会面。但当你到达那里时，发现在University Avenue上有两家星巴克，并且你不知道是哪一家。在12:10时，你没有在星巴克A看到朋友，那么就会去星巴B看看他是否在那里，但他也不在那里。这有几种可能：你的朋友迟到了，还没到任何一家星巴克；你的朋友在你离开后到星巴克A; 你的朋友在星巴克B，但他去星巴克A找你，并且，此时正在去星巴克A的途中。假设是最糟糕的情况，即最后一种可能。现在是你们两个都去过了两家星巴克，且开始怀疑对方是否失约了。现在你会怎么做？到另一家星巴克？来来回回要走多少次？除非你们之间约定了其种协议，否则你们整天都在University Avenue上走来走去，倍感沮丧。在“我去看看他是否在另一家星巴克”这种方法中，问题在于：当你在街上走时，你的朋友可能已经离开了你要去的星巴克。你首先看看星巴克A，发现“他不在”，并且开始去找他。你可以在星巴克B中做同样的选择，但不是同时发生。两家星巴克之间有几分钟的路程, 而就在这几分钟的时间里，系统的状态可能会发生变化。

上述示例中说明了一种竞态条件。因为要获得正确结果（与朋友会面），必须取决于事件的发生时序（当你们到达星巴克时，在离开并去另一家星巴克之前会等待多长时间......）。当你迈出前门时，你在星巴克A的观察结果将变得无效，你的朋友可能从后门进来了，而你却不知道。这种观察结果的失效就是大多数竞态条件的本质------**基于一种可能失效的观察结果来做出判断或执行某个计算**。这种类型的竞态条件称为“先检查后执行”：首先观察到某个条件为真（例如文件X不存在），然后根据这个观察结果采用相应的动作（创建文件X），但事实上在你观察到这个结果以及开始创建文件之间，观察结果可能变得无效（另一个线程在这期间创建了文件X），从而导致各种问题（未预期的异常、数据覆盖、文件被破坏等）。

##### 2.2.2 示例：延迟初始化中的竞态条件

使用“先检查后执行”的一种常见情况就是延迟初始化。延迟初始化的目的是将对象的初始化操作推迟到实际被使用时才进行，同时要保证只被初始化一次。程序清单2-3中的`LazyInitRace`说明了这种延迟初始化情况。`getInstance`方法首先判断`ExpensiveObject`是否已经被初始化，如果已经初始化则返回现有的实例，否则，它将创建一个新的实例，并返回一个引用，从而在后来的调用中就无需再执行这段高开销的代码路径。

```java
// 程序清单2-3
@NotThreadSafe
public class LazyInitRace {

    private ExpensiveObject instance;

    public ExpensiveObject getInstance() {
        if (instance == null) {
            instance = new ExpensiveObject();
        }
        return instance;
    }
}

class ExpensiveObject {

}
```

在`LazyInitRace`中包含了一个竞态条件，它可能会破坏这个类的正确性。假定线程A和线程B同时执行`getInstance`。A观察到`instance`为空，因而创建了一个新的`ExpensiveObject`实例。B同样需要判断`instance`是否为空，此时`instance`是否为空，要取决于不可预测的时序，包括线程的调度方式，以及A需要花多长时间来初始化`ExpensiveObject`并设置`instance`。如果当B检查时，`instance`为空，那么在两次调用`getInstace`时可能会得到不同的结果，即使`getInstance`通常被认为是返回相同的实例。

与大多数并发错误一样，竞态条件并不总是会产生错误，还需要某种不恰当的执行时序。然而，竞态条件可能会导致严重的问题。假定`LazyInitRace`被用于初始化应用程序范围内的注册表，如果在多次调用中返回不同的实例，那么要么会丢失部分注册信息，要么多个行为对同一组注册对象表现出不一致的视图。

##### 2.2.3 复合操作

`LazyInitRace`和`UnsafeCountingFactorizer`都包含一组需要以原子方式执行的操作。要避免竞态条件问题，就必须在某个线程修改该变量时，通过某种方式防止其他线程使用这个变量，从而确保其他线程只能在修改操作完成之前或之后读取和修改状态，而不是在修改状态过程中。

**假定有两个操作A和B，如果从执行A的线程来看，当另一个线程执行B时，要么将B全部执行完，要么完全不执行B，那么A和B对彼此来说是原子的。原子操作是指，对于访问同一个状态的所有操作（包括该操作本身）来说，这个操作是一个以原子方式执行的操作。**

如果`UnsafeSequence`中的递增操作是原子操作，那么竞态条件就不会发生，并且递增操作在每次执行时都会把计数器增加1。为了确保线程安全性，“先检查后执行”（例如延迟初始化）和“读取-修改-写入”（例如递增运算）等操作必须时原子的。我们将“先检查后执行”以及“读取-修改-写入”等操作统称为复合操作：**包含了一组必须以原子方式执行的操作以确保线程安全性。**

在2.3节中，我们将介绍加锁机制，这是Java中确保原子性的内置机制。就目前而言，我们先采用另一种方式修正这个问题，即使用现有的线程安全类，如程序清单2-4所示：

```java
package pers.tavish.jcip.ch2threadsafety;

import net.jcip.annotations.ThreadSafe;

import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.math.BigInteger;
import java.util.concurrent.atomic.AtomicLong;

// 程序清单2-4
@ThreadSafe
public class CountingFactorizer {

    private final AtomicLong count = new AtomicLong(0);

    public long getCount() {
        return count.get();
    }

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = factor(i);
        count.incrementAndGet();
        encodeIntoResponse(resp, factors);
    }

    private void encodeIntoResponse(ServletResponse res, BigInteger[] factors) {

    }

    private BigInteger extractFromRequest(ServletRequest req) {
        return new BigInteger("7");
    }

    private BigInteger[] factor(BigInteger i) {
        return new BigInteger[]{i};
    }
}
```

在`java.util.concurrent.atomic`包中包含了一些原子变量类，用于实现在数值和对象引用上的原子状态转换。通过用`AtomicLong`来代替`long`类型的计数器，能够确保所有对计数器状态的访问操作都是原子的。由于Servlet的状态就是计数器的状态，并且计数器是线程安全的，因此这里的Servlet也是线程安全的。

**在无状态的类中添加一个状态时，如果该状态完全由线程安全的对象来管理，那么这个类仍然是线程安全的。**然而，当状态变量的数量由一个变为多个时，并不会像状态变量数量由零个变为一个那样简单。

**在实际情况中，应尽可能地使用先用的线程安全对象来管理类的状态。与非线程安全的对象相比，判断线程安全对象的可能状态及其状态转换情况要更为容易，从而也更容易维护和验证线程安全性。**

#### 2.3 加锁机制

当在Servlet中添加一个状态变量时，可以通过线程安全的对象来管理Servlet的状态以维护Servlet的线程安全性。但如果想在Servlet中添加更多的状态，那么是否只需要添加更多的线程安全状态变量就足够了？

现在假设我们希望提升Servlet的性能：将最近的计算结果缓存起来，当两个连续的请求对相同的数值进行因数分解时，可以直接使用上一次的计算结果，而无需重新计算。要实现该缓存策略（实际上该策略并不好），需要保存两个状态：最近执行引述分解的数值，以及分解结果。

我们从通过`AtomicLong`以线程安全的方式来管理计数器的状态。那么，在这里是否可以使用类似的`AtomicReference`来管理最近执行因数分解的数值及其结果呢？如下程序禽蛋2-5所示：

```java
// 程序清单2-5
@NotThreadSafe
public class UnSafeCachingFactorizer extends GenericServlet {

    private final AtomicReference<BigInteger> lastNumber = new AtomicReference<BigInteger>();
    private final AtomicReference<BigInteger[]> lastFactors = new AtomicReference<BigInteger[]>();

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        if(i.equals(lastNumber.get())) {
            encodeIntoResponse(resp, lastFactors.get());
        } else {
            BigInteger[] factors = factor(i);
            lastNumber.set(i);
            lastFactors.set(factors);
            encodeIntoResponse(resp, factors);
        }
    }

    private void encodeIntoResponse(ServletResponse resp, BigInteger[] factors) {

    }

    private BigInteger extractFromRequest(ServletRequest req) {
        return new BigInteger("7");
    }

    private BigInteger[] factor(BigInteger i) {
        return new BigInteger[]{i};
    }
}
```

然而，这种方法并不正确。尽管这些原子引用本身都是线程安全的，但在`UnSafeCachingFactorizer`中存在着竞态条件，这可能产生错误的结果。

在线程安全性的定义中要求，多个线程之间的操作无论采用何种执行时序或交替方式，都要保证不变性条件不被破坏。`UnSafeCachingFactorizer`的不变性条件之一是：在`lastFactors`中缓存的因数之积应该等于在`lastNumber`中缓存的数值。只有确保了这个不变性条件不被破坏，上面的Servlet才是正确的。当在不变性条件中涉及多个变量时，各个变量之间并不是彼此独立的，而是某个变量的值会对其他变量的值产生约束。因此，**当更新某一个变量时，需要在同一个原子操作中对其他变量同时进行更新。**

在某些执行时序中，`UnSafeCachingFactorizer`可能会破坏这个不变性条件。在使用原子引用的情况下，尽管对`set`方法的调用是原子的，但仍然无法同时更新`lastNumber`和`lastFactors`。如果只修改了其中一个变量，那么在这两次修改操作之间，其他线程将发现不变性条件被破坏了。同样地，我们也不能保证会同时获取这两个值：在线程A获取这两个值的过程中，线程B可能修改了它们，这样线程A也会发现不变性条件被破坏了。

**要保持状态的一致性，就需要在单个原子操作中更新所有相关的状态变量。**

##### 2.3.1 内置锁

Java提供了一种内置的锁机制来支持原子性：同步代码块（Synchronized Block）。

同步代码块包括两个部分：一个作为锁的对象引用，一个作为由这个锁保护的代码块。以关键字`synchroized`来修饰的方法就是一种横跨整个方法体的同步代码块，其中该同步代码块的锁就是方法调用所在的对象。静态的`synchroized`方法以`Class`对象作为锁。

每个Java对象都可以用做一个实现同步的锁，这些锁被称为内置锁或监视器锁。线程在进入同步代码块之前会自动获得锁，并且在退出同步代码块时自动释放锁。**获得内置锁的唯一途径就是进入由这个锁保护的同步代码块或方法。**

**Java的内置锁相当于一种互斥体（互斥锁），这意味着最多只有一个线程能持有这种锁。当线程A尝试获取一个由线程B持有的锁时，线程A必须等待或者阻塞，知道线程B释放这个锁。如果线程B永远不会释放锁，那么A也将永远地等下去。**

由于每次只能有一个线程执行内置锁保护的代码块，因此，由这个锁保护的同步代码块会以原子方式执行，多个线程在执行该代码块时也不会相互干扰。并发环境中的原子性与事务应用程序中的原子性有着相同的含义————一组语句作为一个不可分割的单元被执行。任何一个执行同步代码块的线程，都不可能看到有其他线程正在执行由同一个锁保护的同步代码块。

上述机制使得要确保因数分解Servlet的线程安全性变得简单，如下程序清单2-6所示：

```java
// 程序清单2-6
@ThreadSafe
public class SynchronizedFactorizer extends GenericServlet {

    @GuardedBy("this")
    private BigInteger lastNumber;

    @GuardedBy("this")
    private BigInteger[] lastFactors;

    public synchronized void service(ServletRequest req, ServletResponse resp) throws ServletException, IOException {
        BigInteger i = extractFromRequest(req);
        if (i.equals(lastNumber)) {
            encodeIntoResponse(resp, lastFactors);
        } else {
            BigInteger[] factors = factor(i);
            lastNumber = i;
            lastFactors = factors;
            encodeIntoResponse(resp, factors);
        }
    }

    private void encodeIntoResponse(ServletResponse resp, BigInteger[] factors) {

    }

    private BigInteger extractFromRequest(ServletRequest req) {
        return new BigInteger("7");
    }

    private BigInteger[] factor(BigInteger i) {
        return new BigInteger[]{i};
    }
}
```

使用`synchroinzed`修饰`service`方法可以确保在同一时刻只有一个线程可以执行该方法。现在`SynchronizedFactorizer`是线程安全的。然而，这种方法却过于极端，因为多个客户端无法同时使用因数分解Servlet，服务的响应性很低。这是一个性能问题，而不是线程安全问题。

##### 2.3.2 重入