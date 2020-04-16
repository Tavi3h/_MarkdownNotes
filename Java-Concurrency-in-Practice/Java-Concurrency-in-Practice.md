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
// Don't do this.
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
// Don't do this.
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
// Don't do this.
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

同步代码块包括两个部分：一个作为锁的对象引用，一个作为由这个锁保护的代码块。以关键字`synchronized`来修饰的方法就是一种横跨整个方法体的同步代码块，其中该同步代码块的锁就是方法调用所在的对象。静态的`synchronized`方法以`Class`对象作为锁。

每个Java对象都可以用做一个实现同步的锁，这些锁被称为内置锁或监视器锁。线程在进入同步代码块之前会自动获得锁，并且在退出同步代码块时自动释放锁。**获得内置锁的唯一途径就是进入由这个锁保护的同步代码块或方法。**

**Java的内置锁相当于一种互斥体（互斥锁），这意味着最多只有一个线程能持有这种锁。当线程A尝试获取一个由线程B持有的锁时，线程A必须等待或者阻塞，知道线程B释放这个锁。如果线程B永远不会释放锁，那么A也将永远地等下去。**

由于每次只能有一个线程执行内置锁保护的代码块，因此，由这个锁保护的同步代码块会以原子方式执行，多个线程在执行该代码块时也不会相互干扰。并发环境中的原子性与事务应用程序中的原子性有着相同的含义————一组语句作为一个不可分割的单元被执行。任何一个执行同步代码块的线程，都不可能看到有其他线程正在执行由同一个锁保护的同步代码块。

上述机制使得要确保因数分解Servlet的线程安全性变得简单，如下程序清单2-6所示：

```java
// 程序清单2-6
// Don't do this.
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

使用`synchronized`修饰`service`方法可以确保在同一时刻只有一个线程可以执行该方法。现在`SynchronizedFactorizer`是线程安全的。然而，这种方法却过于极端，因为多个客户端无法同时使用因数分解Servlet，服务的响应性很低。这是一个性能问题，而不是线程安全问题。

##### 2.3.2 重入

当某个线程请求一个由其他线程持有的锁时，发出请求的线程会阻塞。然而，由于内置锁是可重入的，因此如果某个线程试图获得一个已经由它自己持有的锁，那么这个请求就会成功。**“重入”意味着获取锁的操作的粒度是“线程”，而不是“调用”。**

重入的一种实现方法是为每个锁关联一个获取计数值和一个所有者线程。当计数值为0时，这个锁就被认为是没有被任何线程持有。当线程请求一个未被持有的锁时，JVM将记下锁的持有者，并且将获取计数器置为1。如果同一个线程再次获取这个锁，计数值将递增。而当线程退出同步代码块时，计数器会相应地递减。当计数值为0时，这个锁将释放。

如下程序手动实现了一个可重入锁：

```java
public class Lock {

    private boolean isLocked;
    private Thread lockedBy; 
    private int lockedCount;

    public synchronized void lock() throws InterruptedException {
        Thread thread = Thread.currentThread();
        while (isLocked && lockedBy != thread) {
            wait();
        }
        isLocked = true;
        lockedCount++;
        lockedBy = thread;
    }

    public synchronized void unlock() {
        if (Thread.currentThread() == this.lockedBy) {
            lockedCount--;
            if(lockedCount == 0) {
                isLocked = false;
                notify();
            }
        }
    }
}
```

在程序清单2-7中，子类重写了父类的`doSometing()`方法，然后调用父类中的方法，此时如果没有可重入的锁，那么这段代码将发生死锁。

```java
// 程序清单2-7
public class Widget {
    public synchronized void doSomething() {
        // ...
    }
}

// 程序清单2-7
public class LoggingWidget extends Widget {
    @Override
    public synchronized void doSomething() {
        // ...
        super.doSomething();
    }
}
```

由于`Widget`和`LoggingWidget`中`doSomething()`方法都是`synchronized`方法，因此每个`doSomething`方法在执行前都会获取`Widget`上的锁。如果内置锁是不可重入的，那么在调用`super.doSomething`时将会无法获得`Widget`上的锁，因为这个锁已被持有了，从而线程将永远停顿下去，发生死锁。重入则避免了这种死锁情况的发生。

#### 2.4 用锁来保护状态

由于锁能使其保护的代码路径以串行形式（意味着多个线程依次以独占的方式访问对象，而不是并发地访问）来访问，因此可以通过锁来构造一些协议来实现对共享状态的独占访问。

**如果在复合操作的执行过程中持有一个锁，那么会使复合操作成为原子操作。如果用同步来协调对某个变量的访问，那么在访问这个变量的所有位置都需要使用同步。当使用锁协调对某个变量访问时，在访问变量的所有位置都要使用同一个锁。**

在`SynchronizedFactorizer`中，`lastNumber`和`lastFactors`着两个变量都是由这个Servlet对象的内置锁来保护的，标注的`@GuardedBy`也说明了这一点。

**一种常见的加锁约定是，将所有的可变状态都封装在对象内部，并通过对象的内置锁对所有访问可变状态的代码路径进行同步，使得在该对象上不会发生并发访问。**

许多线程安全类中都使用了这种模式，例如`Vector`和其他的同步集合类。在这种情况下，对象状态中的所有变量都由对象的内置锁保护起来。但是这种模式并没有什么特殊之处，编译器或运行时都不会强制检查并使用这种模式。也就是说如果添加新的方法或代码路径时忘记了使用同步，那么这种加锁协议就可能会被破坏。

**当类的不变性条件涉及多个状态变量时，那么在不变性条件中的每个变量都必须由同一个锁来保护。因此可以在单个原子操作中访问或更新这些变量，从而确保不变性条件不被破坏。**

事实上如果不加区别地滥用`synchronized`，可能会导致程序中出现过多的同步。此外，如果只是将每个方法都作为同步方法，例如`Vector`，那么并不足以确保`Vector`上复合操作都是原子的。例如：

```java
if (!vector.contains(element)) {
    vector.add(element);
}
```

虽然`contains`和`add`均为原子方法，但在上述代码中仍然存在竞态条件（A线程执行完上述代码中的`contains`方法后，B线程执行）。虽然`synchronized`方法可以确保单个操作的原子性，但如果要把多个操作合并为一个复合操作，还是需要额外的加锁机制。

此外，将每个方法都作为同步方法还可能导致活跃性问题（Liveness）或性能问题（Performance）。

#### 2.5 活跃性与性能

在`UnsafeCachingFactorizer`中，我们通过在因数分解Servlet中引入缓存机制来提升性能。在缓存中需要使用共享状态，因此需要通过同步来维护状态的完整性。然而如果使用`SynchronizedFactorizer`中的同步方式，那么代码的执行性将非常糟糕。该策略的实现方式也就是对整个`service`方法进行同步。虽然这种简单且粗粒度的方法能确保线程安全性，但付出的代价却很高。

由于`service`是一个同步方法，因此每次只有一个线程可以执行该方法，如图2-1所示。这就背离了Servlet框架的初衷，即Servlet需要能同时处理多个请求。尤其在高负载形况下将带来糟糕的用户体验，因为这些请求将排队等待处理。

![图2-1-SynchronizedFactorizer中的不良并发](_images\图2-1-SynchronizedFactorizer中的不良并发.PNG)
<center><b>图2-1 SynchronizedFactorizer中的不良并发</b></center>
这种应用程序被称为不良并发（Poor Concurrency）应用程序：可同时调用的数量不仅受到可用处理资源的限制，还受到应用程序本身结构的限制。

通过缩小同步代码块的作用范围，我们可以做到既确保Servlet的并发性，同时又维护线程安全性。要确保同步代码块不要过小，并且不要将本应是原子的操作拆分到多个同步代码块中。应该尽量将不影响共享状态且执行时间较长的操作从同步代码块中分离出去，从而在这些操作的执行过程中，其他线程可以访问共享状态。

程序清单2-8中的`CachedFactorizer`将Servlet的代码修改为使用两个独立的同步代码块，每个同步代码块都只包含一小段代码。其中一个同步代码块负责保护判断是否只需返回缓存结果“先检查后执行”操作序列，另一个同步代码块则负责确保对缓存的数值和因数分解结果进行同步更新。此外，还重新引入了“命中计数器”，添加了一个“缓存命中”计数器，并在第一个同步代码块中更新这两个变量。由于这两个计数器也是共享可变状态的一部分，因此必须在所有访问它们的位置都使用同步。

```java
package pers.tavish.jcip.ch2threadsafety;

import net.jcip.annotations.GuardedBy;
import net.jcip.annotations.ThreadSafe;

import javax.servlet.GenericServlet;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.math.BigInteger;

// 程序清单2-8
@ThreadSafe
public class CachedFactorizer extends GenericServlet {

    @GuardedBy("this")
    private BigInteger lastNumber;

    @GuardedBy("this")
    private BigInteger[] lastFactors;

    @GuardedBy("this")
    private long hits;

    @GuardedBy("this")
    private long cacheHits;

    public synchronized long getHits() {
        return hits;
    }

    public synchronized double getCacheHitRatio() {
        return (double) cacheHits / (double) hits;
    }

    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = null;
        synchronized (this) {
            ++hits;
            if (i.equals(lastNumber)) {。。
                factors = lastFactors.clone();
            }
        }

        if (factors == null) {
            factors = factor(i);
            synchronized (this) {
                lastNumber = i;
                lastFactors = factors.clone();
            }
        }
        encodeIntoResponse(resp, factors);
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

`CachedFactorizer`中不再使用`AtomicLong`类型的计数器，而是使用`long`类型的变量。这是由于我们已经使用同步代码块来构造原子操作，同时使用两种不同的同步机制不仅会带来混乱，而且也不会对性能和安全性带来额外的好处。

重构的`CachedFactorizer`实现了在简单性（对整个方法进行同步）与并发性（对尽可能短的代码路径进行同步）之间的平衡。由于获取与释放锁等操作都需要一定开销，因此如果将同步代码块分解得过细（例如将`++hits`分解到自己的同步代码块中），那么通常可能会带来性能问题，尽管不会破坏原子性。当访问状态变量或者在复合操作的执行期间，`CachedFactorizer`需要持有锁，但在执行较长的因数分解运算之前需要释放锁。这样既确保了线程安全性，也不会过多地影响并发性，而且在每个同步代码块中的代码路径都“足够短”。

判断同步代码块的合理大小需要在各种设计需求之间进行权衡，包括安全性、简单性和性能。

**通常，在简单性与性能之间存在着相互制约的因素。当实现某个同步策略时，一定不要盲目地追求性能而牺牲简单性，因为这样可能会破坏安全性。**

当使用锁时，我们应该清除代码块实现的功能，以及在执行该代码块时是否需要很长时间。无论是执行计算密集型操作，还是执行某个可能阻塞的操作，如果持有锁的时间过长，那么都会带来活跃性或性能问题。

**当执行时间较长的计算或者可能无法快速完成的操作时（例如网络I/O），一定不要持有锁。**

### 第3章 对象的共享

#### 3.1 可见性

在单线程环境中，如果向某个变量先写入值，然后在没有其他写入操作的情况下读取这个变量，那么总能得到相同的值。然而，当读写操作在不同的线程执行时，情况就并非如此了。通常，我们无法确保执行读操作的线程能适时地看到其他线程写入的值，有时候甚至是不可能的。为了确保多个线程之间对内存写入操作的可见性，必须使用同步机制。

程序清单3-1中的`NoVisibility`说明了当多个线程在某有同步的情况下共享数据时出现的错误。

```java
package pers.tavish.jcip.ch3sharingobjects;

import net.jcip.annotations.NotThreadSafe;

// 程序清单3-1
@NotThreadSafe
public class NoVisibility {

    private static boolean ready;
    private static int number;

    public static void main(String[] args) {
        new ReaderThread().start();
        number = 42;
        ready = true;
    }

    private static class ReaderThread extends Thread {

        @Override
        public void run() {
            while (!ready) {
                Thread.yield();
            }
            System.out.println(number);
        }
    }
}
```

在上述这段代码中，主线程和读线程都将访问共享变量`ready`和`number`。这里主线程启动读线程，然后将`number`设为42，将`ready`设为true。读线程一直循环直到发现`ready`的值变为`true`，然后输出`number`的值。

虽然`NoVisibility`看起来会输出42，但事实上可能会输出0，或者根本无法终止。这是因为代码中没有足够的同步机制，因此无法保证主线程写入的`ready`值和`number`值对于读线程来说是可见的。

`NoVisibility`可能无法看到`ready`的值被设置为了`true`，从而持续循环下去。另一种情况`NoVisibility`可能会输出0，因为读线程可能看到了`ready`的值，但却没有看到写入`number`的值，这种现象被称为“重排序（Reordering）”。

**在没有同步的情况下，编译器、处理器以及运行时等都可能对操作的执行顺序进行一些意想不到的调整。在缺乏足够同步的多线程程序中，要对内存操作的执行顺序进行判断，几乎无法得出正确的结论。**

例如，主线程首先写入`number`，然后在没有同步的情况下写入`ready`，那么读线程看到的顺序可能与写入顺序完全相反。

##### 3.1.1 失效数据

`NoVisibility`展示了在缺乏同步的程序中可能产生错误结果的一种情况：失效数据。当读线程查看`ready`变量时可能会得到一个已失效的值。更糟糕的是，失效值可能不会同时出现：一个线程可能得到某个变量的最新值，而获得另一个变量的失效值。

虽然在Web应用程序中失效的命中计数器可能不会导致太糟糕的情况，但在其他情况中，失效值可能会导致一些严重的安全问题或者活跃性问题。

程序清单3-2中的`MutableInteger`不是线程安全的，因为`get`和`set`都是在没有同步的情况下访问`value`的。与其他问题相比，失效值问题更容易出现：如果某个线程调用`set`，那么另一个正在调用`get`的线程可能会看到更新后的`value`值，也可能看不到。

```java
package pers.tavish.jcip.ch3sharingobjects;

import net.jcip.annotations.NotThreadSafe;

@NotThreadSafe
public class MutableInteger {

    private  int value;

    public int get() {
        return value;
    }

    public void set(int value) {
        this.value = value;
    }
}
```

在程序清单3-3的`SynchronizedInteger`中，通过对`get`和`set`等方法进行同步，可以使`MutableInteger`成为一个线程安全的类。

```java
package pers.tavish.jcip.ch3sharingobjects;

import net.jcip.annotations.GuardedBy;
import net.jcip.annotations.ThreadSafe;

@ThreadSafe
public class SynchronizedInteger {

    @GuardedBy("this")
    private int value;

    public synchronized int get() {
        return value;
    }

    public synchronized void set(int value) {
        this.value = value;
    }
}
```

##### 3.1.2 非原子的64位操作

当线程在没有同步的情况下读取变量时，可能会得到一个失效值，但至少这个失效值是由之前某个线程设置的值，而不是一个随机值。这种安全性保证也被称为最低安全性（out-of-thin-air safety）。

最低安全性适用于绝大多数变量，但是存在一个例外：非`volatile`类型的64位数值变量（`double`和`long`）。**Java内存模型要求，变量的读取操作和写入操作都必须是原子操作，但对于非`volatile`类型的`long`和`double`变量，JVM允许将64位的读操作或写操作分解为两个位操作。**当读取一个非`volatile`类型的`long`变量时，如果对该变量的读操作和写操作在不同的线程中执行，那么很可能会读取到某个值的高32位和另一个值的低32位。因此，即使不考虑失效数据问题，在多线程程序中使用共享的可变的`long`和`double`等类型的变量也是不安全的，除非用`volatile`声明它们或者用锁保护起来。

##### 3.1.3 加锁与可见性

内置锁可以用于确保某个线程以一种可预测的方式来查看另一个线程的执行结果。

JMM关于`synchronized`的两条规定：

1. 线程解锁前，必须把共享变量的最新值刷新到主内存中。
2. 线程加锁时，将清空工作内存中共享变量的值，从而使用共享变量时需要从主内存中重新获取最新的值。

（注意：加锁与解锁需要是同一把锁）

通过以上两点，可以看到`synchronized`能够实现可见性。同时，由于`synchronized`具有同步锁，所以它也具有原子性。即加锁机制既可以确保可见性又可以确保原子性。

如图3-1所示，当线程A执行某个同步代码块时，线程B随后进入由同一个锁保护的同步代码块。在这种情况下可以保证，在锁被释放前，A看到的变量值在B获得锁后同样可以由B看到。换句话说，当线程B执行由锁保护的同步代码块时，可以看到线程A之前在同一个同步代码块中的所有操作结果。如果没有同步，那么就无法实现上述保证。

![图3-1-同步的可见性](_images\图3-1-同步的可见性.PNG)
<center><b>图3-1 同步的可见性</b></center>
现在，我们可以进一步理解为什么在访问某个共享且可变的变量时要求所有线程在同一个锁上同步，就是**为了确保某个线程写入该变量的值对于其他线程来说都是可见的。**否则，如果一个线程在未持有正确锁的情况下读取某个变量，那么读到的可能是一个失效值。

**加锁的含义不仅仅局限于互斥行为，还包括内存的可见性。为了确保所有线程都能看到共享变量的最新值，所有执行读操作或者写操作的线程都必须在同一个锁上同步。**

##### 3.1.4 volatile

Java语言提供了一种稍弱的同步机制，即`volatile`变量。**该关键字用来确保将变量的更新操作通知到其他线程。当把变量声明为`volatile`类型后，编译器与运行时都会注意到这个变量是共享的，因此不会将该变量上的操作与其他内存操作一起重排序。`volatile`变量不会被缓存在寄存器或者对其他处理器不可见的地方，因此在读取`volatile`类型的变量时总会返回最新写入的值。**

**访问`volatile`变量时不会执行加锁操作，也就不会使执行线程阻塞，因此`volatile`变量是一种比`synchronized`关键字更轻量级的同步机制。**

`volatile`变量的正确使用方式包括：

- 确保它们自身状态的可见性
- 确保它们所以用对象的状态的可见性
- 标识一些重要的程序声明周期时间的发生

程序清单3-4给出了`volatile`变量的一种典型用法：检查某个状态标记以判断是否退出循环。

```java
package pers.tavish.jcip.ch3sharingobjects;

// 程序清单3-4
public class CountingSheep {

    private volatile boolean asleep;

    public void tryToSleep() {
        while (!asleep) {
            countSomeSheep();
        }
    }

    private void countSomeSheep() {
        // One, two, three...
    }
}
```

我们也可以使用锁来确保`asleep`更新操作的可见性，但这将使代码变得更加复杂。

需要注意的是，尽管`volatile`变量可以用于表示状态信息，但它的语义不足以确保递增操作的原子性，除非我们能确保只有一个线程对变量执行写操作。

当且仅当满足以下所有条件时，才应该使用`volatile`变量：

- 对变量的写入操作不依赖变量的当前值，或者能够确保只有一个线程更新变量的值。
- 该变量不会与其他状态变量一起纳入不变性条件中。
- 在访问变量时不需要加锁。

#### 3.2 发布与逸出

“发布（Publish）”一个对象的意思是指，使对象能够在当前作用域之外的代码中使用。

例如，将一个指向该对象的引用保存到其他代码可以访问的地方，或者在某一个非私有的方法中返回该引用，或者将引用传递到其他类的方法中。

在许多情况中，我们要确保对象及其内部状态不被发布。而在某些情况下，我们又需要发布某个对象，但如果在发布时要确保线程安全性，则可能需要同步。发布内部状态可能会破坏封装性，并使得程序难以维持不变性条件。例如，如果在对象构造完成之前就发布该对象，就会破坏线程安全性。当某个不应该发布的对象被发布时，这种情况就被称为逸出（Escape）。

如程序清单3-5所示，在`initialize`方法中实例化一个新的`HashSet`对象，并将对象的引用保存到`knownSecrets`中以发布该对象：

```java
package pers.tavish.jcip.ch3sharingobjects;

import java.util.HashSet;
import java.util.Set;

// 程序清单3-5
class Secrets {

    public static Set<Secret> knownSecrets;

    public void initialize() {
        knownSecrets = new HashSet<Secret>();
    }
}

class Secret {

}
```

当发布某个对象时，可能会间接地发布其他对象。如果将一个`Secret`对象添加到集合`knownSecrets`中，那么同样会发布这个对象，因为任何代码都可以遍历这个集合，并获取到这个`Secret`对象的引用。同样，如果从非私有方法中返回一个引用，那么同样会发布返回的对象。

程序清单3-6中的`UnsafeStates`发布了本应为私有的状态数组：

```java
package pers.tavish.jcip.ch3sharingobjects;

import lombok.Getter;

// 程序清单3-6
// Don't do this.
@Getter
public class UnsafeStates {
    private String[] states = new String[]{
            "AK", "AL" // ...
    };
}
```

如果按照上述方式来发布`states`，就会出现问题，因为任何调用者都能修改这个数组的内容。在这个示例中，数组`states`已经逸出了它所在的作用域，因为这个本应是私有的变量已经被发布了。

当发布一个对象时，在该对象的非私有域中引用的所有对象同样会被发布。一般来说，如果一个已经发布的对象能够通过非私有的变量引用和方法调用到其他对象，那么这些对象也会被发布。

当把一个对象传递给某个外部方法时，就相当于发布了这个对象。这个时候我们无法知道哪些代码会执行，也不知道在外部方法中究竟会发布这个对象，还是会保留对象的引用并在随后由另一个线程使用。

当某个对象逸出后，我们必须假设有某个类或线程可能会误用该对象。这正是需要使用封装的最主要原因：**封装能够使得对程序的正确性进行分析变得可能，并使得无意中破坏设计约束条件变得更难。**

最后一种发布对象或其内部状态的机制就是发布一个内部类实例，如程序清单3-7的`ThisEscape`所示。当`ThisEscape`发布`EventListener`时，也隐含地发布了`ThisEscape`实例本身，因为在这个内部类的实例中包含了对`ThisEscape`实例的隐含引用：

```java
package pers.tavish.jcip.ch3sharingobjects;

// 程序清单3-7
// Don't do this.
public class ThisEscape {
    public ThisEscape(EventSource source) {
        source.registerListener(new EventListener() {
            public void onEvent(Event e) {
                doSomething(e);
            }
        });
    }

    private void doSomething(Event e) {

    }

    interface EventSource {
        void registerListener(EventListener e);
    }

    interface EventListener {
        void onEvent(Event e);
    }

    interface Event {

    }
}
```

**安全的对象构造过程**

在`ThisEscape`中给出了逸出的一个特殊示例，即`this`引用在构造函数中逸出。当内部的`EventListener`实例发布时，在外部封装的`ThisEscape`实例也逸出了。当且仅当对象的构造函数返回时，对象才处于可预测的和一致的状态。因此，当从对象的构造函数中发布对象时，只是发布了一个尚未构造完成的对象。即使发布对象的语句位于构造函数的最后一行也是如此。如果`this`引用在构造过程中逸出，那么这种对象就被认为是不正确构造。**具体而言，只有当构造函数返回时，`this`引用才应该从线程中逸出。构造函数可以将`this`引用到某个地方，只要其他线程不会在构造函数完成之前使用它。**

我们对程序清单3-7稍作修改：

```java
public class ThisEscape {

    private int num;

    public ThisEscape(EventSource source) {
        source.registerListener(new EventListener() {
            public void onEvent(Event e) {
                doSomething(e);
            }
        });

        // other TODO ...

        num = 42;
    }

    private void doSomething(Event e) {
        if (num != 42) {
            System.out.println("Race condition detected");
        }
    }

    interface EventSource {
        void registerListener(EventListener e);
    }

    interface EventListener {
        void onEvent(Event e);
    }

    interface Event {

    }
}
```

如果我们调用`new ThisEscape(source)`构造方法来创建一个对象，那么如果当构造函数执行到注释标记的代码时，其他的线程触发了`source`上注册的事件监听器，此时控制台就会输出“Race condition detected”。这是因为此时`ThisEscape`对象还没有执行`num = 42`，而我们调用`doSomething()`的方式正是`ThisEscape.this.doSomething()`。所以这个时候就发生了`ThisEscape`对象`this`的逸出。

在构造过程中使`this`逸出的一个常见错误是，在构造函数中启动一个线程。 当对象在其构造函数中创建一个线程时，`this`引用都会被新创建的线程共享。在对象尚未完全构造之前，新的线程就可以看见它。在构造函数中创建线程并没有错误，但最好不要立即启动它。

在构造函数中调用一个可改写的实例方法时，同样会导致`this`引用在构造过程中逸出。

如果想要在构造函数中注册一个事件监听器或者启动线程，那么可以使用一个私有的构造函数和一个公共的工厂方法，从而避免不正确的构造过程。

```java
package pers.tavish.jcip.ch3sharingobjects;

// 程序清单3-8
public class SafeListener {

    private final EventListener listener;

    private SafeListener() {
        listener = new EventListener() {
            public void onEvent(Event e) {
                doSomething(e);
            }
        };
    }

    public static SafeListener newInstance(EventSource source) {
        SafeListener safe = new SafeListener();
        source.registerListener(safe.listener);
        return safe;
    }

    private void doSomething(Event e) {

    }

    interface EventSource {
        void registerListener(EventListener e);
    }

    interface EventListener {
        void onEvent(Event e);
    }

    interface Event {

    }
}
```

#### 3.3 线程封闭

当访问共享的可变数据时，通常需要使用同步。一种避免使用同步的方式就是不共享数据。如果仅在单线程内访问数据，就不需要同步。这种技术被称为线程封闭（Thread Confinement），它是实现线程安全性的最简单方式之一。当某个对象封闭在一个线程中时，这种用法将自动实现线程安全性，即使被封闭的对象本身不是线程安全的。

线程封闭的一个常见应用是JDBC的`Connection`对象。JDBC规范并不要求`Connection`对象必须是线程安全的。在典型的服务器应用程序中，线程从连接池中获得一个`Connection`对象，并且用该对象来处理请求，使用完再将对象返还给连接池。由于大多数请求都是由单个线程采用同步的方式来处理，并且在`Connection`对象返回之前，连接池不会再将它分配给其他线程。因此，这种链接管理模式在处理请求时隐含地将`Connection`对象封闭在线程中。

##### 3.3.1 Ad-hoc线程封闭

Ad-hoc线程封闭是指，维护线程封闭性的职责完全由程序实现来承担。

Ad-hoc线程封闭是比较脆弱的，因为没有一种寓言特性，例如可见性修饰符或局部变量能将对象封闭到目标线程上。事实上，对线程封闭对象的引用通常保存在公有变量。

**当决定使用线程封闭技术时，通常是因为要将某个特定的子系统实现为一个单线程子系统。在某些情况下，单线程子系统提供的简便性要胜过Ad-hoc线程封闭技术的脆弱性。**

在`volatile`变量上存在一种特殊的线程封闭。只有你能确保只有单个线程对共享`volatile`变量执行写入操作，那么就可以安全地在这些共享的`volatile`变量上执行“读取-修改-写入”的操作。在这种情况下，相当于将修改操作封闭在单个线程中以防止发生竞态条件，并且`volatile`变量的可见性保证还确保了其他线程能看到最新的值。

由于Ad-hoc线程封闭技术的脆弱性，因此在程序中尽量少用它，在可能的情况下，应该使用更强的线程封闭技术。

##### 3.3.2 栈封闭

**栈封闭是线程封闭的一种特例，在栈封闭中，只能通过局部变量才能访问对象。**

局部变量的固有属性之一就是封闭在执行线程中。它们位于执行线程的栈中，其他线程无法访问这个栈。栈封闭比Ad-hoc线程封闭更易于维护，也更加健壮。

对于基本类型的局部变量，如下程序清单3-9中的`loadTheArk`方法中的`numPairs`，无论如何都不会破坏栈封闭性。由于任何方法都无法获得对基本类型的引用，因此Java语言的这种语义就确保了基本类型的局部变量始终封闭在线程中。

```java
public int loadTheArk(Collection<Animal> candidates) {
    SortedSet<Animal> animals;
    int numPairs = 0;
    Animal candidate = null;

    animals = new TreeSet<Animal>(new SpeciesGenderComparator());
    animals.addAll(candidates);
    for (Animal a : animals) {
        if (candidate == null || !candidate.isPotentialMate(a))
            candidate = a;
        else {
            ark.load(new AnimalPair(candidate, a));
            ++numPairs;
            candidate = null;
        }
    }
    return numPairs;
}
```

在上述方法中只有一个引用指向`animals`集合，这个引用被封闭在局部变量中，因此也被封闭在执行线程中。然而如果发布了对集合`animals`的引用，那么封闭性将遭到破坏，并导致对象`animals`逸出。

如果在线程内部上下文中使用非线程安全的对象，那么该对象仍然是线程安全的。

##### 3.3.3 ThreadLocal类

维持先锋封闭性的一种更规范的方法是使用`ThreadLocal`，这个类能使线程中的某个值与保持值的对象关联起来。 `ThreadLocal`提供了`get`和`set`等访问接口或方法，这些方法为每个使用该变量的线程都存有一份独立的副本，因此`get`总是返回由当前执行线程在调用`set`时设置的最新值。

`ThreadLocal`对象通常用于防止对可变的单实例变量或全局变量进行共享。例如，由于JDBC链接对象不一是线程安全的，因此多线程应用程序在没有协同的情况下使用全局变量时就不是线程安全的。通过将JDBC链接对象保存到`ThreadLocal`对象中，每个线程都会拥有自己的连接，如下所示：

```java
package pers.tavish.jcip.ch3sharingobjects;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class ConnectionDispenser {

    private static String DB_URL = "jdbc:mysql://localhost:3306/test?user=root&password=mysql";

    private static ThreadLocal<Connection> connectionHolder = ThreadLocal.withInitial(() -> {
        try {
            return DriverManager.getConnection(DB_URL);
        } catch (SQLException e) {
            throw new RuntimeException("Unable to acquire Connection, e");
        }
    });

    public static Connection getConnection() {
        return connectionHolder.get();
    }
}
```

当一个线程调用`ConnectionDispenser.getConnection()`时就会获得只属于当前线程的`Connection`对象：

```java
    @Test
    public void testConnectionDispenser() throws InterruptedException {

        int threads = 2;
        CountDownLatch countDownLatch = new CountDownLatch(2);

        for (int i = 0; i < threads; i++) {
            new Thread(() -> {
                Connection connection1 = ConnectionDispenser.getConnection();
                Connection connection2 = ConnectionDispenser.getConnection();
                System.out.println(Thread.currentThread().getName() + " connection1: " + connection1);
                System.out.println(Thread.currentThread().getName() + " connection2: " + connection2);
                countDownLatch.countDown();
            }).start();
        }
        countDownLatch.await();
    }
```

输出：

```text
Thread-1 connection1: com.mysql.jdbc.JDBC4Connection@418167a6
Thread-1 connection2: com.mysql.jdbc.JDBC4Connection@418167a6
Thread-2 connection1: com.mysql.jdbc.JDBC4Connection@3d9e530d
Thread-2 connection2: com.mysql.jdbc.JDBC4Connection@3d9e530d
```

当某个线程初次调用`ThreadLocal`的`get`方法时，就会调用`initial`来获取初始值（没有覆盖`initialValue()`方法的话将得到`null`）。

#### 3.4 不变性

**满足同步需求的另一种方式是使用不可变对象（Immutable Object）。**如果对象的状态不可变，那么所有线程观察到对象的状态就是一致的了，那么前述所有的问题与复杂性也就自然消失了。

如果某个对象在被创建之后其状态就不能被修改，那么这个对象就称为不可变对象。线程安全性是不可变对象的固有属性之一，它的不变性条件是由构造函数创建的，只要它们的状态不变，那么这些不变性条件就能得到维持。

虽然Java语言规范和Java内存模型中都没有给出不变性的正式定义，但不可变性并不等于将对象中所有的域都声明为`final`类型。即使此时对象中所有的域都是`final`类型的，这个对象也仍然是可变的，因为`final`域中可以保存对可变对象的引用。

**当满足下述条件时，对象将成为不可变对象：**

- 对象创建以后其状态就不能修改。
- 对象的所有域都是`final`的（技术角度来看其实并不需要）。
- 对象是正确创建的（即在构造期间`this`引用没有逸出）。

在不可变对象内部我们仍然可以使用可变对象来管理它们的状态，如程序清单3-11所示：

```java
package pers.tavish.jcip.ch3sharingobjects;

import net.jcip.annotations.Immutable;

import java.util.HashSet;
import java.util.Set;

// 程序清单3-11
@Immutable
public class ThreeStooges {

    private final Set<String> stooges = new HashSet<>();

    public ThreeStooges() {
        stooges.add("Moe");
        stooges.add("Larry");
        stooges.add("Curly");
    }

    public boolean isStooge(String name) {
        return stooges.contains(name);
    }
}

```

尽管`Set`对象是可变的，但从代码的设计上可以看到，在对象构造完毕后无法对其进行修改。

##### 3.4.1 `final`域

`final`类型的域具有不可变性。这里的不可变指的是引用的不可变，即它只能指向初始时指向的那个对象，而不关心指向对象内容的变化。

在Java内存模型中，`final`域还有着特殊的语义。`final`能确保初始化过程的安全性，从而可以不受限制地访问不可变对象，并在共享这些对象时无需同步。

**除非需要更高的可见性，否则应将所有域都声明为私有域。除非需要某个域是可变的，否则应将其声明为`final`域。**

##### 3.4.2 示例：使用`volatile`类型来发布不可变对象

在`UnsafeCachingFactorizer`中，我们使用两个`AtomicReference`来保存最新的数值及其因数分解结果，但这种方式并非线程安全的，因为我们无法以原子方式同时读取或更新两个变量。同样使用`volatile`类型的变量来保存这些值也是不安全的。然而，在某些情况下，不可变对象可以提供一种弱形式的原子性。

例如程序清单3-12：

```java
package pers.tavish.jcip.ch3sharingobjects;

import net.jcip.annotations.Immutable;

import java.math.BigInteger;
import java.util.Arrays;

// 程序清单3-12
@Immutable
public class OneValueCache {
    private final BigInteger lastNumber;
    private final BigInteger[] lastFactors;

    public OneValueCache(BigInteger i, BigInteger[] factors) {
        lastNumber = i;
        lastFactors = Arrays.copyOf(factors, factors.length);
    }

    public BigInteger[] getFactors(BigInteger i) {
        return lastNumber == null || !lastNumber.equals(i) ? null : Arrays.copyOf(lastFactors, lastFactors.length);
    }
}
```

对于在访问和更新多个相关变量时出现的竞态条件问题，可以通过将这些变量全部保存在一个不可变对象中来消除。如果是一个可变对象，那么就必须使用锁来确保原子性。如果是一个不可变对象，那么当线程获得了对该对象的引用后，就不必担心另一个线程会修改对象的状态。如果要更新这些变量，那么可以创建一个新得容器对象，但其他使用原有对象的线程仍然会看到处于一致的状态。

如下程序清单3-13中的`VolatileCachedFactorizer`使用了`OneValueCache`来保存缓存的数值及其因数：

```java
package pers.tavish.jcip.ch3sharingobjects;

import net.jcip.annotations.ThreadSafe;

import javax.servlet.GenericServlet;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import java.math.BigInteger;

// 程序清单3-13
@ThreadSafe
public class VolatileCachedFactorizer extends GenericServlet {

    private volatile OneValueCache cache = new OneValueCache(null, null);

    @Override
    public void service(ServletRequest req, ServletResponse resp) {
        BigInteger i = extractFromRequest(req);
        BigInteger[] factors = cache.getFactors(i);
        if (factors == null) {
            factors = factor(i);
            cache = new OneValueCache(i, factors);
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

与`cache`相关的操作不会互相干扰，因为`OneValueCache`是不可变的，并且在每条相应的代码路径中只会访问它一次（即使这里存在代码会对`factors`对象进行修改，`cache`对象也会保持不变，这也就保证了`cache`对象的`lastNumber`和`lastFactors`之间永远不会存在不一致的情况）。通过使用包含多个状态变量的不可变容器来维持不变性条件，并使用一个`volatile`类型的引用来确保可见性，使得`VolatileCachedFactorizer`在没有显式地使用锁的情况下仍然是线程安全的。

#### 3.5 安全发布

前述中我们讨论了如何确保对象不被发布。当然在某些情况下我们希望在多个线程间共享对象，此时必须确保安全地共享。

如果只是像程序清单3-14那样将对象引用保存到共有域中，那么还不足以安全地发布这个对象。

```java
package pers.tavish.jcip.ch3sharingobjects;

// 程序清单3-14
// Don't do this.
public class StuffIntoPublic {

    public Holder holder;

    public void initialize() {
        holder = new Holder(42);
    }
}
```

这里由于存在可见性问题，其他线程看到`holder`对象将处于不一致的状态。

##### 3.5.1 不正确的发布：正确的对象被破坏

我们不能指望一个尚未被完全创建的对象拥有完整性。

如果程序清单3-15中的`Holder`使用程序清单3-14中不安全发布方式，那么另一个线程调用`assertSanity`时可能抛出`AssertionError`：

```java
// 程序清单3-15
public class Holder {

    private int n;

    public Holder(int n) {
        this.n = n;
    }

    public void assertSanity(int n) {
        if (this.n != n) {
            throw new AssertionError("This statement is false.");
        }
    }
}
```

假设我们有语句`Holder holder = new Holder(1);`则实际执行动作如下：

```text
// 1. 分配空间
tmpRef = allocate(Holder.class)
// 2. 执行构造函数 
invokeConstructor(tmpRef) 
// 3. 赋值
holder = tmpRef 
```

但是由于指令重排，可能是先执行3，再执行2。这就会导致此时`holder`对象已经非空了，但是其`n`还是默认值0。这样，当另一个线程此时调用`assertSanity()`方法时就会抛出错误。

##### 3.5.2 不可变对象与初始化安全性

我们已经知道，即使某个对象的引用对其他线程是可见的，也不意味着对象状态对于使用该对象的线程来说一定是可见的。为了确保对象状态能呈现出一致的视图，就必须使用同步。

即使在发布不可变对象的引用时没有使用同步，也仍然可以安全地访问该对象。为了维持这种初始化安全性的保证，必须满足不可变性的所有需求：

- 状态不可修改
- 所有域都是`final`类型
- 正确的构造方式

将`Holder`类的`n`声明为`final`，则`Holder`将不可变，从而避免出现不正确发布的问题。

这是因为JLS（Java Language Specification，Java语言规范） 17.5中规定：在对象的构造器中设置`final`域，并且不要在另一个线程可以在该对象的构造器执行完成之前看到它的覅那个，对该对象的引用执行写操作。如果遵循了这一点，那么当该对象被另一个线程看到时，这个线程就总是会看到该对象的`final`域的正确构造版本。

也就是说，对象的构造函数结束，就认为这个对象已经被完全初始化了。 为了保证这个对象的final字段有正确的初始值，只有在这个对象被完全初始化后，这个对象的引用才能被其他线程看到。

**任何线程都可以在不需要额外同步的情况下安全地访问不可变对象，即使在发布这些对象时没有使用同步。**

然而，如果`final`类型的域所指向的是可变对象，那么在访问这些域所指向的对象的状态时仍然需要同步。

##### 3.5.3 安全发布的常用模式

可变对象必须通过安全的方式来发布，这通常意味着在发布和使用该对象都必须使用同步。现在我们将重点说明如何确保使用对象的线程能够看到该对象处于已发布的状态。

一个正确构造的对象可以通过以下方式来安全地发布：

- 在静态初始化函数中初始化一个对象引用
- 将对象的引用保存到`volatile`类型的域或者`AtomicReference`对象中
- 将对象的引用保存到某个正确构造对象的`final`类型域中
- 将对象的引用保存到一个由锁保护的域中

在线程安全容器内部的同步意味着，在将对象放入到某个容器，例如`Vector`或`synchronizedList`时将满足上述最后一条需求。如果线程A将对象X放入一个线程安全的容器，随后线程B读取这个对象，那么可以确保B看到A设置的X状态，即便在这段读写X的代码中没有包含显式的同步。

线程安全库中的容器类提供了以下的安全发布保证：

- 通过将一个键或值放入`Hashtable`、`synchronizedMap`或者`ConcurrentMap`中们可以安全地将它发布给任何从这些容器中访问它的线程（无论是直接访问还是通过迭代器访问）。
- 通过将元素放入`Vector`、`CopyOnWriteArrayList`、`CopyOnWriteArraySet`、`synchronizedList`、`synchronizedSet`中，可以将该元素安全地发布到任何从这些容器中访问该元素的线程。
- 通过将某个元素放入`BlockingQueue`或者`ConcurrentLinkedQueue`中，可以将该元素安全地发布到任何从这些队列中访问该元素的线程。

juc类库中得其他数据传递机制（例如`Future`和`Exchanger`）同样可以实现安全发布。

通常而言，要发布一个静态构造的对象，最简单和最安全的方式是使用静态的初始化器，例如：

```java
public static Holder holder = new Holder(1);
```

静态初始化器由JVM在类的初始化阶段执行。由于JVM内部存在同步机制，因此通过这种方式初始化的任何对象都可以被安全地发布。

##### 3.5.4 事实不可变对象

如果对象在发布后不会被修改，那么对于其他没有额外同步的情况下安全地访问这些对象的线程来说，安全发布是足够的。所有的安全发布机制都能保证，当对象的引用对所有访问该对象的线程可见时，对象发布时的状态对于所有线程也将是可见的，并且如果对象状态不会再改变，那么就足以保证任何访问都是安全的。

如果对象从技术上看是可以改变的，但其状态在发布后不会再改变，那么把这种对象成为“事实不可变对象（Effectively Immutable Object）”。这些对象不需要满足不可变性的严格定义。

**在没有额外同步的情况下，任何线程都可以安全地使用被安全发布的事实不可变对象。**

例如，`Date`对象本身是可变的，但如果将它作为不可变对象来使用，那么在多个线程之间共享`Date`对象时，就可以省去对锁的使用。假设需要维护一个`Map`对象，其中保存了每位用户的最近登录时间：

```java
public Map<String, Date> lastLogin = Collections.synchronizedMap(new HashMap<String, Date>());
```

如果`Date`对象的值在放入`Map`中后就不再改变，那么`synchronizedMap`的同步机制就足以使`Date`值被安全的发布，并且在访问`Date`值时不需要额外同步。

##### 3.5.5 可变对象

如果对象在构造后可以修改，那么安全发布只能确保“发布当时”状态的可见性。**对于可变对象，不仅在发布对象时需要使用同步，而且在每次对象访问时同样需要使用同步来确保后续修改操作的可见性。**要安全地共享可变对象，这些对象就必须被安全地发布，并且必须时线程安全的或者由某个锁保护起来。

对象的发布需求取决于它的可变性：

- 不可变对象可以通过任意机制来发布。
- 事实不可变对象必须通过安全方式来发布。
- 可变对象必须通过安全方式来发布，并且必须是线程安全的或者由某个锁保护起来。

##### 3.5.6 安全地共享对象

当获得一个对象的引用时，我们需要知道在这个引用上可以执行哪些操作。在使用它之前是否需要获得一个锁？是否可以修改它的状态，或者只能读取它？许多并发错误都是由于没有理解共享对象这些“既定规则”而导致的。当发布一个对象时，必须明确地说明对象的访问方式。

在并发陈序中使用和共享对象时，可以使用一些策略：

- **线程封闭。**线程封闭的对象只能由一个线程拥有，对象被封闭在该线程中，并且只能由这个线程修改。
- **只读共享。**在没有额外同步的情况下，共享的只读对象可以由多个线程并发访问，但任何线程都不能修改它。共享的只读对象包括不可变对象和事实不可变对象。
- **线程安全共享。**线程安全的对象在其内部实现同步，因此多个线程可以通过 对象的公有接口来进行访问而不需要进一步的同步。
- **保护对象。**被保护的对象只能通过持有特定的锁来访问。保护对象包括封装在其他线程安全对象中的对象，以及已发布的并且由某个特定锁保护的对象。

### 第4章 对象的组合

本章将介绍一些组合模式，这些模式能使一个类更容易成为线程安全的，并且在维护这些类时不会无意中破坏类的安全性。

#### 4.1 设计线程安全的类

在设计线程安全类的过程中，需要包含以下三个基本要素：

- 找出构成对象状态的所有变量
- 找出约束状态变量的不变性条件
- 建立对象状态的并发访问管理策略

要分析对象的状态，首先从对象的域开始。如果对象中所有的域都是基本类型的变量，那么这些域将构成对象的全部状态。例如程序清单4-1：

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.GuardedBy;
import net.jcip.annotations.ThreadSafe;

// 程序清单4-1
@ThreadSafe
public final class Counter {

    @GuardedBy("this")
    private long value = 0;

    public synchronized long getValue() {
        return value;
    }

    public synchronized long increment() {
        if (value == Long.MAX_VALUE) {
            throw new IllegalStateException("counter overflow");
        }
        return ++value;
    }
}
```

程序清单4-1的`Counter`只有一个域`value`，因此这个域就是`Counter`的全部状态。

**对于含有n个基本类型域的对象，其状态就是这些域构成的n元组。**

同步策略（Synchronized Policy）定义了如何在不违背对象不变性条件或后验条件下对其状态的访问操作进行协同。同步策略规定了如何将不可变性、线程封闭与加锁机制等结合起来以维护线程的安全性，并且还规定了哪些变量由哪些锁来保护。

##### 4.1.1 收集同步需求

对象与变量都有一个状态空间，即所有可能的取值。状态空间越小，就越容易判断线程的状态。`final`类型的域使用得越多，就越能简化对象可能状态的分析过程。

在许多类中都定义了一些不可变条件， 用于判断状态是有效的还是无效的。`Counter`中的`value`域是`long`类型的变量，其状态空间为从`Long.MIN_VALUE`到`Long.MAX_VALUE`，但`Counter`中`value`在取值范围上存在着一个限制， 即不能是负值。

同样， 在操作中还会包含一些后验条件来判断状态迁移是否是有效的。如果`Counter`的当前状态为17，那么下一个有效状态只能是18。当下一个状态需要依赖当前状态时，这个操作就必须是一个复合操作。并非所有的操作都会在状态转换上施加限制，例如，当更新一个保存当前温度的变量时， 该变量之前的状态并不会影响计算结果。

由于不变性条件以及后验条件在状态及状态转换上施加了各种约束，因此就需要额外的同步与封装。如果某些状态是无效的，那么必须对底层的状态变量进行封装，否则客户代码可能会使对象处于无效状态。如果在某个操作中存在无效的状态转换，那么该操作必须是原子的。另外，如果在类中没有施加这种约束，那么就可以放宽封装性或序列化等需求，以便获得更高的灵活性或性能。

在类中也可以包含同时约束多个状态变量的不变性条件。在一个表示数值范围的类中可以包含两个状态变量，分别表示范围的上界和下界。这些变量必须遵循的约束是，下界值应该小于或等于上界值。类似于这种包含多个变量的不变性条件将带来原子性需求：这些相关的变量必须在单个原子操作中进行读取或更新。不能首先更新一个变量，然后释放锁并再次获得锁，然后再更新其他的变量。因为释放锁后，可能会使对象处于无效状态。如果在一个不变性条件中包含多个变量，那么在执行任何访问相关变量的操作时，都必须持有保护这些变量的锁。

**如果不了解对象的不变性条件与后验条件，那么就不能确保线程安全性。要满足在状态变量的有效值或状态转换上的各种约束条件，就需要借助于原子性与封装性。**

##### 4.1.2 依赖状态的操作

类的不变性条件与后验条件约束了在对象上有哪些状态和状态转换是有效的。在某些对象的方法中还包含一些基于状态的先验条件(Precondition)。例如，不能从空队列中移除一个元素，在删除元素前，队列必须处于“非空的”状态。如果在某个操作中包含有基千状态的先验条件，那么这个操作就称为依赖状态的操作。

在单线程程序中，如果某个操作无法满足先验条件，那么就只能失败。但在并发程序中，先验条件可能会由于其他线程执行的操作而变成真。在并发程序中要一直等到先验条件为真，然后再执行该操作。

在Java中，等待某个条件为真的各种内置机制（包括等待和通知等机制）都与内置加锁机制紧密关联，要想正确地使用它们并不容易。 要想实现某个等待先验条件为真时才执行的操作，一种更简单的方法是通过现有库中的类（例如阻塞队列[`Blocking Queue`]或信号量[`Semaphore`])来实现依赖状态的行为。第5章将介绍一些阻塞类，例如`BlockingQueue`、`Semaphore`以及其他的同步工具类。第14章将介绍如何使用在平台与类库中提供的各种底层机制来创建依赖状态的类

##### 4.1.3 状态的所有权

在定义哪些变量将构成对象的状态时，只考虑对象拥有的数据。所有权（Ownership）在Java中并没有得到充分的体现，而是属于类设计中的一个要素。如果分配并填充了一个HashMap对象，那么就相当于创建了多个对象：`HashMap`对象，在`HashMap`对象中包含的多个对象，以及在`Map.Entry`中可能包含的内部对象。`HashMap`对象的逻辑状态包括所有的`Map.Entry`对象以及内部对象，即使这些对象都是一些独立的对象。

无论如何，垃圾回收机制使我们避免了如何处理所有权的问题。在C++中，当把一个对象传递给某个方法时，必须认真考虑这种操作是否传递对象的所有权，是短期的所有权还是长期的所有权。在Java中同样存在这些所有权模型，只不过垃圾回收器为我们减少了许多在引用共享方面常见的错误，因此降低了在所有权处理上的开销。

许多情况下，所有权与封装性总是相互关联的：对象封装它拥有的状态，反之也成立，即对它封装的状态拥有所有权。状态变量的所有者将决定采用何种加锁协议来维持变量状态的完整性。所有权意味着控制权。然而，如果发布了某个可变对象的引用，那么就不再拥有独占的控制权，最多是“共享控制权”。对于从构造函数或者从方法中传递进来的对象，类通常并不拥有这些对象，除非这些方法是被专门设计为转移传递进来的对象的所有权（例如，同步容器封装器的工厂方法）。

容器类通常表现出一种“所有权分离”的形式，其中容器类拥有其自身的状态，而客户代码则拥有容器中各个对象的状态。Servlet框架中的`ServletContext`就是其中一个示例。`ServletContext`为Servlet提供了类似于Map形式的对象容器服务，在`ServletContext`中可以通过名称来注册（`setAttribute`）或获取（`getAttribute`）应用程序对象。由Servlet容器实现的`ServletContext`对象必须是线程安全的，因为它肯定会被多个线程同时访问。当调用`setAttribute`和`getAttribute`时，Servlet不需要使用同步，但当使用保存在`ServletContext`中的对象时，则可能需要使用同步。这些对象由应用程序拥有，Servlet容器只是替应用程序保管它们。与所有共享对象一样，它们必须安全地被共享。为了防止多个线程在并发访问同一个对象时产生的相互干扰，这些对象应该要么是线程安全的对象，要么是事实不可变的对象，或者由锁来保护的对象。

#### 4.2 实例封闭

如果某对象不是线程安全的，那么可以通过多种技术使其在多线程程序中安全地使用。我们可以确保该对象只能由单个线程访问（线程封闭），或者通过一个锁来保护对该线程的所有访问。

封装简化了线程安全类的实现过程，它提供了一种实例封闭机制（Instance Confienement）。当一个对象被封装到另一个对象中时，能够访问被封装对象的所有代码路径都是已知的。这样与对象可以由整个程序访问的情况相比，更易于对代码进行分析。通过将封闭机制与合适的加锁策略结合起来，可以确保以线程安全的方式来使用非线程安全对象。

**对数据封装在对象内部，可以将数据的访问限制在对象的方法上，从而更容易确保线程在访问数据时总能持有正确的锁。**

被封闭的对象一定不能超出它们既定的作用域。对象可以封闭在类的一个实例（例如作为类的一个私有成员）中，或者封闭在某个作用域（例如作为一个局部变量）内，再或者封闭在线程（例如在某个线程中将对象从一个方法传递到另一个方法，而不是在多个线程之间共享该对象）内。当然，对象本身不会逸出，出现逸出的情况通常是由于开发人员在发布对象时超出了对象既定的作用域。

程序清单4-2中的`PersonSet`说明了如何通过封闭与加锁等机制使一个类成为线程安全的（即使这个类的状态变量并不是线程安全的）。

```java
// 程序清单4-2
@ThreadSafe
public class PersonSet {
    @GuardedBy("this")
    private final Set<Person> mySet = new HashSet<>();

    public synchronized void addPerson(Person p) {
        mySet.add(p);
    }

    public synchronized boolean containsPerson(Person p) {
        return mySet.contains(p);
    }

    interface Person {
    }
}
```

`PersonSet`的状态由`HashSet`来管理，而`HashSet`并非是线程安全的。但由于`mySet`是私有的并且不会逸出，因此`HashSet`被封闭在`PersonSet`中。唯一可以访问`mySet`的代码路径是`addPerson`与`containsPerson`，在执行它们时都要获得`PersonSet`上的锁。`PersonSet`的状态完全由它的内置锁保护，因而`PersonSet`是一个线程安全类。

这个示例并没有对`Person`的线程安全性做任何假设，但如果`Person`类是可变的，那么再访问从`PersonSet`中获得的`Person`对象时，还需要额外的同步。要想安全地使用`Person`对象，最可靠的方法就是使得`Person`成为一个线程安全的类。当然，也可以使用锁来保护`Person`对象，并确保所有客户代码在访问`Person`对象之前获得正确的锁。

实例封闭是构建线程安全类的一个最简单的方式，它还使得在锁策略的选择上有了更多的灵活性。实例封闭可以使得不同的状态变量由不同的锁来保护。

Java类库中还有很多线程封闭的示例，其中有些类的唯一用途就是将非线程安全的类转化为线程安全的类。一些基本的容器类并非线程安全的，例如`ArrayList`和`HashMap`，但类库提供了包装器工厂方法（例如，`Collections.synchronizedList()`及其类似方法），使得这些非线程安全的类可以在多线程环境中安全地使用。这些工厂方法通过装饰器模式将容器类封装在一个同步的包装器对象中，而包装器对象能将接口中的每个方法都实现为同步方法，并将调用请求转发到底层的容器对象上。只要包装器对象拥有对底层容器对象的唯一引用（即把底层容器对象封闭在包装器中），那么它就是线程安全的。这些方法的Javadoc指出，对底层容器对象的所有访问必须通过包装器进行。

当然，如果将一个本该被封闭的对象发布出去，那么也能破坏封装性。如果一个对象本应该封闭在特定的作用域内，那么让该对象逸出作用域就是一个错误。当发布其他对象时（例如迭代器或内部类实例），可能会间接地发布被封闭对象，同样会使被封闭对象逸出。

##### 4.2.1 Java监视器模式

从线程封闭原则及其逻辑推论可以得出Java监视器模式。遵循Java监视器模式的对象会把对象的所有可变状态都封装起来，并由对象自己的内置锁来保护。

程序清单4-1中的`Counter`给出了这种模式的一个示例。在`Counter`中封装了一个状态变量`value`，对该变量的所有访问都需要通过`Counter`的方法来执行，并且这些方法都是同步的。

监视器模式仅仅是一种编写代码的约定，对于任何一种锁对象，只要自始至终都使用该锁对象，都可以用来保护对象状态，例如程序清单4-3给出的如果使用私有锁来保护状态：

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.GuardedBy;
import pers.tavish.jcip.ch2threadsafety.Widget;

// 程序清单4-3
public class PrivateLock {
    private final Object myLock = new Object();

    @GuardedBy("myLock")
    private Widget widget;

    void someMethod() {
        synchronized (myLock) {
            // Access or modify the state of widget
        }
    }
}
```

使用私有的锁对象而不是对象的内置锁，有许多优点。私有的锁对象可以将锁封闭起来，使客户代码无法得到锁，但客户代码可以通过公有方法来访问锁，以便参与到同步策略中。如果客户代码错误地获得了另一个对象锁，那么可能会产生活跃性问题。

##### 4.2.2 车辆追踪

我们来看一个用于调度车辆的“车辆追踪器”，例如出租车、警车、货车等。首先使用监视器模式来构建车辆追踪器，然后再尝试放宽某些封装性需求同时又保持线程安全性。

每台车都由一个`String`对象来标识，并且拥有一个相应的位置坐标(x, y)。在`VehicleTracker`类中封装了车辆的标识和位置，并且该模型将由一个视图线程和多个执行更新操作的线程共享。视图线程会读取车辆的名字和位置，并将它们显示在界面上：

```java
Map<String, Point> locations = vehicles.getLocations();
for (String key : locations.keySet()) {
    renderVehicle(key, locations.get(key));
}
```

类似地，执行更新操作的线程通过从GPS设备上获取的数据或者调度员从GUI界面上输入的数据来修改车辆的位置：

```java
void vehicleMoved(VehicleMovedEvent evt) {
    Point loc = evt.getNewLocation();
    vehicles.setLocation(evt.getVehicleId(), loc.x, loc.y);
}
```

视图线程与执行更新操作的线程将并发地访问数据模型，因此该模型必须是线程安全的。程序清单4-4给出了一个基于Java监视器模型实现的“车辆追踪器”，其中使用了程序清单4-5中的`MutablePoint`来表示车辆的位置。

```java
// 程序清单4-4
@ThreadSafe
public class MonitorVehicleTracker {

    @GuardedBy("this")
    private final Map<String, MutablePoint> locations;

    public MonitorVehicleTracker(Map<String, MutablePoint> locations) {
        this.locations = deepCopy(locations);
    }

    public synchronized Map<String, MutablePoint> getLocations() {
        return deepCopy(locations);
    }

    public synchronized MutablePoint getLocation(String id) {
        MutablePoint loc = locations.get(id);
        return loc == null ? null : new MutablePoint(loc);
    }

    public synchronized void setLocation(String id, int x, int y) {
        MutablePoint loc = locations.get(id);
        if (loc == null) {
            throw new IllegalArgumentException("No such ID: " + id);
        }
        loc.x = x;
        loc.y = y;
    }

    private static Map<String, MutablePoint> deepCopy(Map<String, MutablePoint> m) {
        Map<String, MutablePoint> result = new HashMap<String, MutablePoint>();

        for (String id : m.keySet()) {
            result.put(id, new MutablePoint(m.get(id)));
        }

        return Collections.unmodifiableMap(result);
    }
}
```

虽然`MutablePoint`不是线程安全的，但追踪器类是线程安全的。它所包含的`Map`对象和可变的`Point`对象都未曾发布。当需要返回车辆的位置时，通过`MutablePoint`拷贝构造函数或者`deepCopy`方法来复制正确的值，从而生成一个新的`Map`对象，并且该对象中的值与原有`Map`对象中的`key`值和`value`值都相同。

```java
// 程序清单4-5
// Don't do this.
@NotThreadSafe
public class MutablePoint {
    public int x, y;

    public MutablePoint() {
        this.x = 0;
        this.y = 0;
    }

    public MutablePoint(MutablePoint p) {
        this.x = p.x;
        this.y = p.y;
    }
}
```

通常情况下上述程序不存在性能问题，但在车辆容器非常大的情况下将极大地降低性能。

#### 4.3 线程安全性的委托

大多数对象都是组合对象。当从头开始构建一个类，或者将多个非线程安全的类组合成一个类时，Java监视器模式是非常有用的。但是，如果类中的各个组件都已经是线程安全的，会是什么情况呢？我们是否需要再增加一个额外的线程安全层？答案是“视情况而定”。在某些情况下，通过多个线程安全类组合而成的类是线程安全的，而在某些情况下，这仅仅是一个好的开端。

##### 4.3.1 示例：基于委托的车辆追踪器

下面构造一个委托给线程安全类的车辆追踪器。我们将车辆的位置保存到一个`Map`对象中，因此首先要实现一个线程安全的`Map`类，`ConcurrentHashMap`。我们还可以用一个不可变的`Point`类来代替`MutablePoint`以保存位置，如下程序清单4-6所示：

```java
// 程序清单4-6
@Immutable
public class Point {
    public final int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

由于`Point`类是不可变的，因而是线程安全的。不可变的值可以被自由地共享与发布，因此在返回`locations`时不需要复制。

程序清单4-7的`DelegatingVehicleTracker`中没有使用任何显式的同步，所有对状态的访问都由`ConcurrentHashMap`来管理，且其`key`和`value`均是不可变的。

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.ThreadSafe;

import java.util.Collections;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.ConcurrentMap;

// 程序清单4-7
@ThreadSafe
public class DelegatingVehicleTracker {

    private final ConcurrentMap<String, Point> locations;
    private final Map<String, Point> unmodifiableMap;

    public DelegatingVehicleTracker(Map<String, Point> points) {
        locations = new ConcurrentHashMap<>(points);
        unmodifiableMap = Collections.unmodifiableMap(locations);
    }

    public Map<String, Point> getLocations() {
        return unmodifiableMap;
    }

    public Point getLocation(String id) {
        return locations.get(id);
    }

    public void setLocations(String id, int x, int y) {
        if (locations.replace(id, new Point(x, y)) == null) {
            throw new IllegalArgumentException("invalid vehicle name: " + id);
        }
    }
}
```

如果使用最初的`MutablePoint`类而不是`Point`类就会破坏封装性，因为`getLocations`会发布一个指向可变状态的引用，而这个引用不是线程安全的。需要注意的是，我们稍微改变了车辆追踪器类的行为。在使用监视器模式的车辆追踪器中返回的是车辆位置的快照，而在使用委托的车辆追踪器中时返回的是一个不可修改但却实时的车辆位置试图。这意味着，如果线程A调用`getLocations`，而线程B随后修改了某些点的位置，那么在返回给线程A的`Map`中将反映出这些变化。

如果需要一个不发生变化的车辆视图，那么`getLocations`可以返回对`locations`这个`Map`对象的一个浅拷贝（Shallow Copy），如程序清单4-8所示：

```java
// 程序清单4-8
public Map<String, Point> getLocationsAsStatic() {
    return Collections.unmodifiableMap(new HashMap<>(locations));
}
```

##### 4.3.2 独立的状态变量

到目前为止，这些委托示例都仅仅委托给了单个线程安全的状态变量。我们还可以将线程安全性委托给多个状态变量，只要这些变量是彼此独立的，即组合而成的类并不会仔其包含的多个状态变量上增加任何不变性条件。

程序清单4-9中的`VisualComponent`是一个图形组件，允许客户程序注册监控鼠标和键盘等事件的监听器。它为每种类型的事件都备有一个已注册监听器列表，因此当某个事件发生时，就会调用响应的监听器。然而，在鼠标事件建通器与键盘事件监听器之间不存在任何关联，二者是彼此独立的，因此`VisualComponent`可以将其线程安全性委托给这两个线程安全的监听器列表。

```java
package pers.tavish.jcip.ch4composingobjects;

import java.awt.event.KeyListener;
import java.awt.event.MouseListener;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;

// 程序清单4-9
public class VisualComponent {
    
    private final List<KeyListener> keyListeners = new CopyOnWriteArrayList<>();
    private final List<MouseListener> mouseListeners = new CopyOnWriteArrayList<>();

    public void addKeyListener(KeyListener listener) {
        keyListeners.add(listener);
    }

    public void addMouseListener(MouseListener listener) {
        mouseListeners.add(listener);
    }

    public void removeKeyListener(KeyListener listener) {
        keyListeners.remove(listener);
    }

    public void removeMouseListener(MouseListener listener) {
        mouseListeners.remove(listener);
    }
}
```

`VisualComponent`使用`CopyOnWriteArrayList`来保存各个监听器列表。该列表是线程安全的。由于每个列表都是线程安全的，此外，由于各个状态之间不存在耦合关系，因此`VisualComponent`可以将它的线程安全性委托给`mouseListeners`和`keyListeners`对象。

##### 4.3.3 当委托失效时

大多数组合对象都不会像`VisualComponent`这样简单：在它们的状态变量之间存在着某些不变性条件。程序清单4-10中的`NumberRange`使用了两个`AtomicInteger`来管理状态，并且含有一个约束条件，即第一个数值要小于或等于第二个数值。

```java
package pers.tavish.jcip.ch4composingobjects;

import java.util.concurrent.atomic.AtomicInteger;

// 程序清单4-10
// Don't do this.
public class NumberRange {

    // 不变性条件 lower <= upper
    private final AtomicInteger lower = new AtomicInteger(0);
    private final AtomicInteger upper = new AtomicInteger(0);

    public void setLower(int i) {
        // 注意，不安全的“先检查后执行”
        if (i > upper.get()) {
            throw new IllegalArgumentException("can't set lower to " + i + " > upper");
        }
        lower.set(i);
    }

    public void setUpper(int i) {
        if (i < lower.get()) {
            throw new IllegalArgumentException("can't set upper to " + i + " < lower");
        }
        upper.set(i);
    }

    public boolean isInRange(int i) {
        return i >= lower.get() && i <= upper.get();
    }
}
```

`NumberRange`不是线程安全的，没有维持对下界和上界进行约束的不变性条件。`setLower`和`setUpper`等方法都在尝试维持不变性条件，但却无法做到。因为二者都是“先检查后执行”的操作，但它们没有足够的加锁机制来保证这些操作的原子性。假设取值范围为(0, 10)，如果一个线程调用`setLower(5)`，另一个线程调用`setUpper(4)`，那么在一些错误的执行时序中，这两个调用将通过检查并设置成功，此时就将得到一个无效的范围(5, 4)。由于`lower`和`upper`不是彼此独立的，因此`NumberRange`不能将线程安全性委托给它的线程安全变量。

如果某个类含有复合操作，例如`NumberRange`，那么仅靠委托并不足以实现线程安全性。在这种情况下，这个类必须提供自己的加锁机制以保证这些复合操作都是原子操作，除非整个符合操作都可以委托给状态变量。

**如果一个类是由多个独立且线程安全的状态变量组成，并且在所有的操作中都不包含无效状态转转换，那么可以将线程安全性委托给底层的状态变量。**

##### 4.3.4 发布底层的状态变量

当把线程安全性委托给某个对象的底层状态变量时，在什么条件下才可以发布这些变量从而使其他类能修改它们？这取决于在类中对这些变量施加了哪些不变性条件。虽然`Counter`中的`value`域可以为任何整数值，但`Counter`施加的约束条件是只能取正整数，此外递增操作同样约束了下一个状态的有效取值范围。如果将`value`声明为一个公有域，那么客户代码可以将它修改为一个无效值，因此发布`value`会导致这个类出错。另一方面，如果某个变量表示的是当前温度或者最近登录用户的ID，那么即使另一个类在某个时刻修改了这个值，也不会破坏任何不变性条件，因此发布这个变量也是可以接受的。

**如果一个状态变量是线程安全的，并且没有任何不变性条件来约束它的值，在变量的操作上也不存在任何不允许的状态转换，那么就可以安全地发布这个变量。**

##### 4.3.5 示例：发布状态的车辆追踪器

现在我们来构造车辆追踪器的另一个版本，并在这个版本中发布底层的可变状态。我们需要修改接口以适应这种变化，即使用可变且线程安全的`SafePoint`类。

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.GuardedBy;
import net.jcip.annotations.ThreadSafe;

// 程序清单4-11
@ThreadSafe
public class SafePoint {

    @GuardedBy("this")
    private int x, y;

    private SafePoint(int[] a) {
        this(a[0], a[1]);
    }

    public SafePoint(SafePoint p) {
        this(p.get());
    }

    public SafePoint(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public synchronized int[] get() {
        return new int[]{x, y};
    }

    public synchronized void set(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

程序清单4-11中的`SafePoint`提供的`get`方法同事获得`x`和`y`的值，并将二者放在一个数组中返回。如果分别为二者提供`get`方法，那么在获得这两个不同坐标的操作之间，它们的值可能发生变化，从而导致调用者看到不一致的值：车辆从来没有到达过位置(x, y)。通过使用`SafePoint`，可以构造一个发布其底层可变状态的车辆追踪器，还能确保其线程安全性不被破坏，如程序清单4-12所示：

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.ThreadSafe;

import java.util.Collections;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

@ThreadSafe
public class PublishingVehicleTracker {

    private final Map<String, SafePoint> locations;
    private final Map<String, SafePoint> unmodifiableMap;

    public PublishingVehicleTracker(Map<String, SafePoint> locations) {
        this.locations = new ConcurrentHashMap<>(locations);
        this.unmodifiableMap = Collections.unmodifiableMap(this.locations);
    }

    public Map<String, SafePoint> getLocations() {
        return unmodifiableMap;
    }

    public SafePoint getLocation(String id) {
        return locations.get(id);
    }

    public void setLocation(String id, int x, int y) {
        if (!locations.containsKey(id)) {
            throw new IllegalArgumentException("invalid vehicle name: " + id);
        }
        locations.get(id).set(x, y);
    }
}
```

`PublishingVehicleTracker`将其线程安全性委托给底层的`ConcurrentHashMap`，只是`Map`中的元素是线程安全的且可变的`SafePoint`，而并非不可变的。`getLocations`方法返回底层`Map`对象的一个不可变的副本。调用者不能增加或删除车辆，但却可以通过修改返回`Map`中的`SafePoint`值来改变车辆的位置。

#### 4.4 在现有的线程安全类中添加功能

有时候某个现有的线程安全类能支持我们所需要的所有操作，但更多时候，现有的类只能支持大部分的操作，此时就需要在不破坏线程安全的情况下添加一个新的操作。

例如，假设需要为一个线程安全的列表提供一个原子操作-----“若没有则添加（Put-If- Absent）”的操作。同步的`List`类已经实现了大部分功能，我们可以根据它提供的`contains`方法和`add`方法来构造一个“若没有则添加”的操作。

“若没有则添加”的概念很简单，在向容器添加元素前，首先检查该元素是否已经存在，如果存在就不再添加。由于这个类必须是线程安全的，因此就隐含地增加另一个需求，即该操作必须是原子操作。因为如果不是原子操作，那么在某些执行时序下，有两个线程都将看到元素X不在容器中，并且都执行了添加X的操作，从而使容器中包含两个相同的X对象。

要添加一个新的原子操作，最安全的方法是修改原始的类，但这通常无法做到，因为我们可能无法访问或修改类的源代码。另一种方法是扩展这个类，并假定在这几这个类的时候考虑了可扩展性。

程序清单4-13中的`BetterVector`对`Vector`进行了扩展：

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.ThreadSafe;

import java.util.Vector;

// 程序清单4-13
@ThreadSafe
public class BetterVector<E> extends Vector<E> {
    public synchronized boolean putIfAbsent(E x) {
        boolean absent = !contains(x);
        if (absent) {
            add(x);
        }
        return absent;
    }
}
```

“扩展3”方法比直接将代码添加到类中更加脆弱，因为现在的同步策略实现被分布到多个单独维护的源代码文件中。如果底层的类改变了同步策略并选择了不同的锁来保护它的状态变量，那么子类会被破坏，因为在同步策略改变后无法再使用正确的锁来控制对基类状态的并发访问。

##### 4.4.1 客户端加锁机制

程序清单4-14实现了一个包含“若没有则添加”操作的辅助类，用于读线程安全的`List`执行操作，但其中的代码是错误的。

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.NotThreadSafe;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

// 程序清单4-14
@NotThreadSafe
public class ListHelper<E> {

    public List<E> list = Collections.synchronizedList(new ArrayList<>());

    public synchronized boolean putIfAbsent(E x) {
        boolean absent = !list.contains(x);
        if (absent) {
            list.add(x);
        }
        return absent;
    }
}
```

为什么这种方式不能实现线程安全性？毕竟，`putIfAbsent`已经声明了`synchronized`。问题在于在错误的锁上进行了同步。无论`List`使用哪一个锁来保护它的状态，可以确定的是，这个锁并不是`ListHelper`的内置锁。尽管所有的列表操作都被声明为`synchronized`，但却使用了不同的锁，这意味着`putIfAbsent`相对于`List`的其他操作来说并不是原子的，因此就无法确保当`putIfAbsent`执行时另一个线程不会修改列表。

要使这个方法正确执行，必须使`List`在实现客户端加锁或外部加锁时使用同一个锁。客户端加锁是指，对于使用某个对象X的客户端代码，使用X本身用于保护其状态的锁来保护这段客户代码。**要使用客户端加锁，你必须知道对象X使用的是哪一个锁。**

在`Vecotr`和同步封装器类的文档中指出，它们通过使用`Vector`或封装器容器的内置锁来支持客户端加锁。程序清单4-15给出了在线程安全的`List`上执行`putIfAbsent`操作，其中使用了正确的客户端加锁。

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.ThreadSafe;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

// 程序清单4-15
@ThreadSafe
public class ListHelperCorrect<E> {
    
    public List<E> list = Collections.synchronizedList(new ArrayList<>());

    public boolean putIfAbsent(E x) {
        synchronized (list) {
            boolean absent = !list.contains(x);
            if (absent) {
                list.add(x);
            }
            return absent;
        }
    }
}
```

通过添加一个原子操作来扩展类是脆弱的，因为它将类的加锁代码分布到多个类中。客户端加锁则更加脆弱，因为它将类C的加锁代码放到与C完全无关的其他类中。

客户端加锁机制与扩展类机制有许多共同点，二者都是将派生类的行为与基类的实现耦合在一起。正如扩展会破坏实现的封装性，客户端加锁同样会破坏同步策略的封装性。

##### 4.4.2 组合

当为现有的类添加一个原子操作时，有一个更好的方法：组合（Composition）。程序清单4-16中的`ImprovedList`通过将操作委托给底层的`List`实例来实现`List`操作，同时还添加了一个`putIfAbsent`方法。

```java
package pers.tavish.jcip.ch4composingobjects;

import net.jcip.annotations.ThreadSafe;

import java.util.Collection;
import java.util.Iterator;
import java.util.List;
import java.util.ListIterator;

// 程序清单4-16
@ThreadSafe
public class ImprovedList<T> implements List<T> {

    private final List<T> list;

    public ImprovedList(List<T> list) { this.list = list; }

    public synchronized boolean putIfAbsent(T x) {
        boolean contains = list.contains(x);
        if (!contains) {
            list.add(x);
        }
        return !contains;
    }

    // ...
}
```

`ImprovedList`通过自身的内置锁增加了一层额外的加锁。它并不关心底层的`List`是否是线程安全的。即使`List`不是线程安全的或者修改了它的加锁实现，`ImprovedList`也会提供一致的加锁机制来实现线程安全性。`ImprovedList`更为健壮，事实上，我们使用了Java监视器模式来封装现有的`List`，并且只要在类中拥有指向底层`List`的唯一外部引用，就能确保线程安全性。（我们假设将列表对象传给构造函数后，客户代码不会再直接使用这个对象，而只能通过`ImprovedList`来访问它。）

### 第5章 基础构建模块

Java平台类库包含了丰富的并发基础构建模块，例如线程安全容器类以及各种用于协调多个相互协作的线程控制流的同步工具类（`Synchronizer`）。本章将介绍一些有用的构建模块，以及在使用这些模块来构建并发应用程序时的一些常用模式。

#### 5.1 同步容器类

同步容器类包括`Vector`和`Hashtable`，二者是早期JDK的一部分，还包括`Collections.synchronizedXxx`等工厂方法创建的容器类。这些类实现线程安全的方式是：将它们的状态封装起来，并对每个共有方法都进行同步，使得每次只有一个线程能访问容器的状态。

#### 5.1.1 同步容器类的问题

同步容器类都是线程安全的，但在某些情况下可能需要额外的客户端加锁来保护复合操作。容器上常见的复合操作包括：迭代（反复访问元素，直到遍历完容器中所有元素）、跳转（根据指定顺序找到当前元素的下一个元素）以及条件运算（例如，“若没有则添加”）。在同步容器类中，这些复合操作在没有客户端加锁的情况下仍然是线程安全的，但当其他线程并发地修改容器时，它们可能出现意料之外的行为。

例如程序清单5-1：

```java
package pers.tavish.jcip.ch5buildingblocks;

import java.util.Vector;

// 程序清单5-1
public class UnsafeVectorHelpers {
    public static Object getLast(Vector list) {
        int lastIndex = list.size() - 1;
        return list.get(lastIndex);
    }

    public static void deleteLast(Vector list) {
        int lastIndex = list.size() - 1;
        list.remove(lastIndex);
    }
}
```

这两个方法都会执行“先检查再运行”操作，每个方法首先都会获取到列表大小，然后通过结果来获取或删除最后一个元素。

这些方法看似没有任何问题，从某种程度上来看也确实如此，无论多少个线程同时调用它们，也不会破坏`Vector`。但从这些方法的调用者角度来看，情况就不同了。如果线程B在一个包含10个元素的`Vector`上调用`getLast`，同时线程A在同一个`Vector`上调用`deleteLast`，这些操作在某些执行时序上将产生`ArrayIndexOutOfBoundsException`。

![数组角标越界异常](_images\图5-1-数组角标越界异常.PNG)

这种情况很好地遵循了`Vector`的使用规范，如果请求一个不存在的元素，那么将抛出一个异常。但这并不是方法调用者所期望的结果，除非`Vector`一开始就是空的。

由于同步容器类要遵守同步策略，即支持客户端加锁，因此可能会创建一些新的操作，只要我们直到应该使用哪一个锁，那么这些新操作就与容器的其他操作一样都是原子操作。

