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

同步代码块包括两个部分：一个作为锁的对象引用，一个作为由这个锁保护的代码块。以关键字`synchroized`来修饰的方法就是一种横跨整个方法体的同步代码块，其中该同步代码块的锁就是方法调用所在的对象。静态的`synchroized`方法以`Class`对象作为锁。

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

使用`synchroinzed`修饰`service`方法可以确保在同一时刻只有一个线程可以执行该方法。现在`SynchronizedFactorizer`是线程安全的。然而，这种方法却过于极端，因为多个客户端无法同时使用因数分解Servlet，服务的响应性很低。这是一个性能问题，而不是线程安全问题。

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
class Widget {
    public synchronized void doSomething() {
        // ...
    }
}

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

最低安全性适用于绝大多数变量，但是存在一个例外：非`violate`类型的64位数值变量（`double`和`long`）。**Java内存模型要求，变量的读取操作和写入操作都必须是原子操作，但对于非`violate`类型的`long`和`double`变量，JVM允许将64位的读操作或写操作分解为两个位操作。**当读取一个非`violate`类型的`long`变量时，如果对该变量的读操作和写操作在不同的线程中执行，那么很可能会读取到某个值的高32位和另一个值的低32位。因此，即使不考虑失效数据问题，在多线程程序中使用共享的可变的`long`和`double`等类型的变量也是不安全的，除非用`violate`声明它们或者用锁保护起来。

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

**访问`violate`变量时不会执行加锁操作，也就不会使执行线程阻塞，因此`violate`变量是一种比`synchronized`关键字更轻量级的同步机制。**

`violate`变量的正确使用方式包括：

- 确保它们自身状态的可见性
- 确保它们所以用对象的状态的可见性
- 标识一些重要的程序声明周期时间的发生

程序清单3-4给出了`violate`变量的一种典型用法：检查某个状态标记以判断是否退出循环。

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

需要注意的是，尽管`violate`变量可以用于表示状态信息，但它的语义不足以确保递增操作的原子性，除非我们能确保只有一个线程对变量执行写操作。

当且仅当满足以下所有条件时，才应该使用`violate`变量：

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