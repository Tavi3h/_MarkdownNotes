[TOC]

# 面试题

## 第一部分 Java基础

*1. **JDK 和 JRE 有什么区别？**

- JDK：Java Development Kit 的简称，java 开发工具包，提供了 java 的开发环境和运行环境。
- JRE：Java Runtime Environment 的简称，java 运行环境，为 java 的运行提供了所需环境。

具体来说 JDK 其实包含了 JRE，同时还包含了编译 java 源码的编译器 javac，还包含了很多 java 程序调试和分析的工具。简单来说：如果我们仅需要运行 java 程序，只需安装 JRE 就可以了。但如果需要编写 java 程序，那么需要安装 JDK。

*2. **== 和 equals 的区别？**

== 在比较基本类型时是比较其值是否相同，在比较引用类型时是比较二者的地址值是否相同。

equals 是 Object 类的方法，用于引用类型的比较。在没有重写前，其功能与 == 相同。重写后可以自定义某一类型的具体比较规则，进而确定两个引用是否相等。

*3. **两个对象的 hashCode() 相同，则 equals() 也一定为 true，对吗？**

不对。

hashCode() 与 equals() 的约定如下：

如果 equals() 方法返回 true，则二者的 hashCode() 必然相同。
如果二者的 hashCode() 相同，那么 equals() 方法可以返回true也可以返回 false。

*4. **final 在 Java 中有什么作用？**

- 修饰类，表示该类不可被继承。
- 修饰方法，表示该方法不可被重写。
- 修饰成员变量，表示该引用不可变。

*5. **Java 中 Math.round(-1.5) 等于多少？**

该方法表示四舍五入，其规则为当前数字增加0.5，然后向下取整。

*6. **String 属于基本数据类型吗？**

不是，String 是引用类型。

*7. **Java 中操作字符串的的类都有哪些？它们有什么区别？**

String 声明的是不可变的对象，每次操作都会生成新的 String 对象，然后将指针指向新的 String 对象。

StringBuffer 和 StringBuilder 可以在原有对象的基础上进行操作，并具有很多实用方法。

StringBuffer 和 StringBuilder 最大的区别在于，StringBuffer 是线程安全的，而 StringBuilder 是非线程安全的，但 StringBuilder 的性能却高于 StringBuffer。

*8. **String str="i"与 String str=new String("i")一样吗？**

不一样，因为内存的分配方式不一样。String str="i"的方式，Java 虚拟机会将其分配到常量池中；而 String str=new String("i") 则会被分到堆内存中。

*9. **如何反转字符串？**

使用 StringBuilder 类的 reverse() 方法。

*10. **String 类的常用方法有哪些？**

- indexOf()：返回指定字符的索引。
- charAt()：返回指定索引处的字符。
- replace()：字符串替换。
- trim()：去除字符串两端空白。
- split()：分割字符串，返回一个分割后的字符串数组。
- getBytes()：返回字符串的 byte 类型数组。
- length()：返回字符串长度。
- toLowerCase()：将字符串转成小写字母。
- toUpperCase()：将字符串转成大写字符。
- substring()：截取字符串。
- equals()：字符串比较。

*11. **抽象类一定要有抽象方法吗？**

不必。

*12. **普通类和抽象类有哪些区别？**

相比于普通类，抽象类可以包含抽象方法，且不能被实例化。

*13. **抽象类能使用 final 修饰吗？**

不能。定义抽象类的目的就是要让其他类继承的，而如果定义为 final 则无法被继承。

实际上，在编译阶段，如果同时使用 final 和 abstract 修饰类，那么会发生报错。

*14. **接口和抽象类有什么区别？**

接口需要 implements，而抽象类需要 extends。
接口中的方法默认为 public abstract，而抽象类中的方法可以是任意访问修饰符的。
接口不能有 main 方法，而抽象类可以有 main 方法，并可以运行。
接口不能有构造函数，而抽象类可以有构造函数。
一个类可以实现多个接口，但只能继承一个抽象类。

*15. **Java 中 IO 流分为几种？**

按照功能分：输入流和输出流

按照类型分：字节流和字符流

*16. **BIO、NIO 和 AIO 有什么区别？**

- BIO：Block IO 同步阻塞式 IO，就是我们平常使用的传统 IO，它的特点是模式简单使用方便，并发处理能力低。
- NIO：New IO（Non-Blocking IO） 同步非阻塞 IO，是传统 IO 的升级，客户端和服务器端通过 Channel（通道）通讯，实现了多路复用。
- AIO：Asynchronous IO 是 NIO 的升级，也叫 NIO2，实现了异步非堵塞 IO，异步 IO 的操作基于事件和回调机制。

*17. **Files 的常用方法都有哪些？**

java.nio.file.Files 提供了大量处理文件的方法，例如文件复制、读取、写入，获取文件属性、快捷遍历文件目录等.....

- Files.exists()：检测文件路径是否存在。
- Files.createFile()：创建文件。
- Files.createDirectory()：创建文件夹。
- Files.delete()：删除一个文件或目录。
- Files.copy()：复制文件。
- Files.move()：移动文件。
- Files.size()：查看文件个数。
- Files.read()：读取文件。
- Files.write()：写入文件

*18. **Java 容器都有哪些？**

简易版如图：

![Java容器](images\Java容器.png)

*19. **Collection 和 Collections 有什么区别？**

- java.util.Collection 是一个集合接口（集合类的一个顶级接口）。它提供了对集合对象进行基本操作的通用接口方法。Collection 接口在Java 类库中有很多具体的实现。Collection 接口的意义是为各种具体的集合提供了最大化的统一操作方式，其直接继承接口有 List 与 Set。
- Collections 则是集合类的一个工具类/帮助类，其中提供了一系列静态方法，用于对集合中元素进行排序、搜索以及线程安全等各种操作。

*20. **List、Set、Map 之间的区别是什么？**

![ListSetMap](images\ListSetMap.png)

*21. **HashMap 和 Hashtable 有什么区别？**

- HashMap 是 Hashtable 的轻量级实现，主要区别在于 HashMap 允许空键值，而 Hashtable 不允许。
- HashMap 是非线程安全的，而 Hashtable 是线程安全的。
- HashMap 使用 Iterator，而 Hashtable 使用 Enumeration。
- HashMap 的底层数组默认大小为16，增长为2的指数，而 Hashtable 的底层数组默认大小为11，增长方式为old*2+1。
- HashMap 去掉了 Hashtable 的 contains 方法，但是加上了 containsValue() 和 containsKey() 方法。
- Hashtable 直接使用key的 hashCode，而 HashMap 则在取得hashCode后做了位运算，h ^ (h >>> 16)。
- HashMap 在 JDK 1.8 时使用红黑树进行了优化。当 HashMap 桶中的元素个数超过一定数量时，就会发生树化，将链表结构转化为红黑树的结构。

*22. **如何决定使用 HashMap 还是 TreeMap？**

TreeMap 的 Key 值是要求实现 java.lang.Comparable，所以迭代的时候 TreeMap 默认是按照 Key 值升序排序，TreeMap 的实现也是基于红黑树结构。
而HashMap 的 Key 值实现散列 hashCode()，分布是散列的均匀的，不支持排序，数据结构主要是桶(数组)，链表或红黑树。
大多情况下 HashMap 有更好的性能，所以大多不需要排序的时候我们会使用 HashMap。

*23. **HashMap的实现原理？**

HashMap 是基于哈希表的 Map 接口的非同步实现。此类不保证映射的顺序，特别是它不保证该顺序恒久不变。HashMap 实际上是一个“链表散列”的数据结构，即数组和链表的结合体。

当我们往 HashMap 中放入元素时，首先根据 key 的 hashcode 重新计算 hash 值，根据hash 值得到这个元素在数组中的位置(下标)，如果该数组在该位置上已经存放了其他元素，那么在这个位置上的元素将以链表的形式存放，新加入的放在链头，最先加入的放入链尾。如果数组中该位置没有元素，就直接将该元素放到数组的该位置上。

JDK 1.8 对 HashMap 的实现做了优化，当链表中的节点数据超过8个之后，该链表就会转化为红黑树来提高查询效率。

*24. **说一下 HashSet 的实现原理？**

- HashSet 底层由 HashMap 实现。
- HashSet 的值存放于 HashMap 的 key 上。
- HashMap 的 value 统一为 PRESENT。

*25. **ArrayList 和 LinkedList 的区别是什么？**

ArrayList 底层的数据结构是数组，支持随机访问（实现了 RandomAccess 接口），而 LinkedList 的底层数据结构是双向循环链表，不支持随机访问。这也导致遍历 ArrayList 使用for循环遍历速度更快，而 LinkedList 使用迭代器遍历更快。

*26. **如何实现数组和List之间的转换？**

- 数组转List：使用Arrays的asList()方法。
- List转数组：使用List的toArray()方法。

*27. **ArrayList 和 Vector 的区别是什么？**

ArrayLis和Vector`都是基于Object[]实现的，它们会在内存中开辟一块连续的空间来存储，由于数据存储是连续的，因此它们支持用序号对元素进行访问。由于这个特性，所以二者查询元素的速度很快，而插入元素比较慢，由于此时可能会涉及到对数组中元素的移动。
二者均有一个默认的初始化容量大小，当里面存储的元素超过这个大小时就会动态地扩充。Vector默认扩充为原来的2倍，并提供了设置扩充大小的方法，而ArrayList则是1.5倍，并且没有提供设置扩充大小的方法。二者的最大区别在于Vector是线程安全的，由于其绝大多数方法都是直接或间接同步的，而ArrayList则不是。

*28. **数组和ArrayList有何区别？**

- 数组可以容纳基本类型和对象，而ArrayList只能容纳对象。 
- 数组是指定大小的，而ArrayList大小是固定的。 
- 数组没有提供ArrayList那么多功能，比如addAll、removeAll和iterator等。
- 数组是定长的，不能扩充的，而ArrayList的空间可以动态增长。

*29. **在Queue中poll()和remove()有什么区别？**

二者的区别仅在于当队列中没有元素时，poll()方法返回null，而remove()方法抛出异常。

*30. **哪些集合类是线程安全的？**

- Vector
- Stack
- Hashtable

*31. **什么是迭代器Iterator？**

迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。

*32. **Iterator如何使用？有什么特点？**

Java中的Iterator功能比较简单，通常只能单向移动：

- 使用方法iterator()要求容器返回一个Iterator。第一次调用Iterator的next()方法时，它返回序列的第一个元素。
- 使用next()获得序列中的下一个元素。
- 使用hasNext()检查序列中是否还有元素。
- 使用remove()将迭代器新返回的元素删除。

*33. **Iterator和ListIterator有什么区别？**

- Iterator可以用来遍历Set、List和Queue，但是ListIterator只能用来遍历List。
- Iterator只能向前遍历，ListIterator可以双向遍历。
- ListIterator继承了Iterator，并包含其他功能，例如：增加元素，替换元素，获取前一个和后一个元素的索引，等等。

*34. **什么是fail-fast机制？**

在用迭代器遍历一个容器时，如果遍历过程中对集合对象的内容进行修改，则会抛出ConcurrentModificationException。

假设存在两个线程（线程1、线程2），线程1通过Iterator在遍历集合A中的元素，在某个时候线程2修改了集合A的结构（是结构上面的修改，而不是简单的修改集合元素的内容），那么这个时候程序就会抛出ConcurrentModificationException异常，从而产生fail-fast机制。

## 第二部分 多线程

*35. **并行和并发有什么区别？**

并发（concurrency）和并行（parallellism）是：

并发是指在同一时刻只能有一条指令执行，但多个进程指令被快速的轮换执行，使得在宏观上具有多个进程同时执行的效果，但在微观上并不是同时执行的，只是把时间分成若干段，使多个进程快速交替的执行。

并行指在同一时刻，有多条指令在多个处理器上同时执行。所以无论从微观还是从宏观来看，二者都是一起执行的。

并行在多处理器系统中存在，而并发可以在单处理器和多处理器系统中都存在，并发能够在单处理器系统中存在是因为并发是并行的假象，并行要求程序能够同时执行多个操作，而并发只是要求程序假装同时执行多个操作（每个小时间片执行一个操作，多个操作快速切换执行）。

当有多个线程在操作时，如果系统只有一个CPU，则它根本不可能真正同时进行一个以上的线程，它只能把CPU运行时间划分成若干个时间段，再将时间段分配给各个线程执行，在一个时间段的线程代码运行时,其它线程处于挂起状态.这种方式我们称之为并发（Concurrent）。
当系统有一个以上CPU时，则线程的操作有可能非并发。当一个CPU执行一个线程时，另一个CPU可以执行另一个线程，两个线程互不抢占CPU资源，可以同时进行，这种方式我们称之为并行（Parallel）。

*36. **线程和进程的区别？**

在操作系统级别上，程序的执行都是以进程为单位的，它程序运行和资源分配的基本单位，一个程序至少有一个进程，一个进程至少有一个线程。进程在执行过程中拥有独立的内存单元，而多个线程共享内存资源，减少切换次数，从而效率更高，并且线程的切换开销要比进程切换小。线程是进程的一个实体，是cpu调度和分派的基本单位，是比程序更小的能独立运行的基本单位。同一进程中的多个线程之间可以并发执行。

*37. **守护线程是什么？**

守护线程（Daemon Thread），也称“服务线程”，在没有用户线程可服务时会自动离开。守护线程的优先级比较低，用于为系统中的其它对象和线程提供服务。

*38. **创建线程有哪几种方式？**

（1）Threa类

- 定义Thread类的子类，并重写该类的run方法，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。
- 创建Thread子类的实例，即创建了线程对象。
- 调用线程对象的start()方法来启动该线程。

（2）Runnable接口

- 定义runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
- 创建Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。
- 调用线程对象的start()方法来启动该线程。

（3）Callable接口

- 创建Callable接口的实现类，并实现call()方法，该call()方法将作为线程执行体，并且有返回值。
- 创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值。
- 使用FutureTask对象作为Thread对象的target创建并启动新线程。
- 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值。

（4）Executor框架

使用ExecutorService提交Runnable或Callable以及FutureTask。

*39. **说一下Runnable和Callable有什么区别？**

- Callable的call()方法可以在任务结束后提供一个返回值，而Runnable的run()方法无此功能。
- Callable中的call()方法可以抛出异常，而Runnable接口中的run()方法不能抛出异常。
- 运行Callable可以拿到一个Future对象，Future对象提供了检查计算是否完成，并可以获取执行的结果。

*40. **线程有哪几种状态？**

- NEW
- RUNNABLE
- BLOCKED
- WAITING
- TIMED_WAITING
- TERMINATED

*41. **sleep()和wait()有什么区别？**

sleep()方法是Thread的静态方法，让调用的线程进入指定时间睡眠状态，使得当前线程进入阻塞状态，告诉系统至少在指定时间内不需要为线程调度器为该线程分配执行时间片，给执行机会给其他线程（实际上，调用sleep()方法时并不要求持有任何锁，即sleep()可在任何地方使用。），但是监控状态依然保持，到时后会自动恢复。

当线程处于上锁时，sleep()方法不会释放对象锁，即睡眠时也持有对象锁。只会让出CPU执行时间片，并不会释放同步资源锁。

sleep()休眠时间满后，该线程不一定会立即执行，这是因为其他线程可能正在运行而起没有被调度为放弃执行，除非此线程具有更高的优先级。

sleep()必须捕获异常，在sleep的过程中过程中有可能被其他对象调用它的interrupt()，产生InterruptedException异常，如果你的程序不捕获这个异常，线程就会异常终止，进入TERMINATED状态，如果你的程序捕获了这个异常，那么程序就会继续执行catch语句块(可能还有finally语句块)以及以后的代码。

在没有锁的情况下，sleep()可以使低优先级的线程得到执行的机会，当然也可以让同优先级、高优先级的线程有执行的机会。

wait()方法是Object类里的方法，当一个线程执行wait()方法时，它就进入到一个和该对象相关的等待池中（进入等待队列，也就是阻塞的一种，叫等待阻塞），同时释放对象锁，并让出CPU资源，待指定时间结束后返还得到对象锁。

wait()使用notify()方法、notifyAll()方法或者等待指定时间来唤醒当前等待池中的线程。等待的线程只是被激活，但是必须得再次获得锁才能继续往下执行，也就是说只要锁没被释放，原等待线程因为为获取锁仍然无法继续执行。notify的作用只负责唤醒线程，线程被唤醒后有权利重新参与线程的调度。

wait()方法、notify()方法和notifyAll()方法用于协调多线程对共享数据的存取，所以只能在同步方法或者同步块中使用，否则抛出IllegalMonitorStateException。

二者的区别：

- 属于不同的两个类，sleep()方法是线程类（Thread）的静态方法，wait()方法是Object类里的方法。
- sleep()方法不会释放锁，wait()方法释放对象锁。
- sleep()方法可以在任何地方使用，wait()方法则只能在同步方法或同步块中使用。
- sleep()必须捕获异常，wait()方法、notify()方法和notiftAll()方法不需要捕获异常。
- sleep()使线程进入阻塞状态（线程睡眠），wait()方法使线程进入等待队列（线程挂起），也就是阻塞类别不同。

*42. **notify()和notifyAll()有什么区别？**

- 如果线程调用了对象的wait()方法，那么线程便会处于该对象的等待池中，等待池中的线程不会去竞争该对象的锁。
- 当有线程调用了对象的notifyAll()方法（唤醒所有wait线程）或notify()方法（只随机唤醒一个wait()线程），被唤醒的的线程便会进入该对象的锁池中，锁池中的线程会去竞争该对象锁。也就是说，调用了notify()后只要一个线程会由等待池进入锁池，而notifyAll()会将该对象等待池内的所有线程移动到锁池中，等待锁竞争。

*43. **线程的run()和start()有什么区别？**

start()方法来启动一个线程，真正实现了多线程运行。这时无需等待run()方法体代码执行完毕，可以直接继续执行下面的代码；这时此线程是处于就绪状态，并没有运行。然后通过此Thread类调用方法run()来完成其运行状态， 这里方法run()称为线程体，它包含了要执行的这个线程的内容，run()方法运行结束，此线程终止。然后CPU再调度其它线程。

run()方法是在本线程里的，只是线程里的一个函数,而不是多线程的。 如果直接调用run()，其实就相当于是调用了一个普通函数而已，直接待用run()方法必须等待run()方法执行完毕才能执行下面的代码，所以执行路径还是只有一条，根本就没有线程的特征，所以在多线程执行时要使用start()方法而不是run()方法。

*44. **创建线程池有哪些方式？**

- ThreadPoolExecutor：
    + newCachedThreadPool：创建一个可缓存线程池，如果线程池长度超过处理需要，可灵活回收空闲线程。而当需求增加时，则可以自动添加新线程，线程池的规模不存在任何限制。
    + newFiexedThreadPool：创建一个可重用固定线程数的线程池，采用无界的阻塞队列，所以实际线程数量永远不会变化，适用于可以预测线程数量的业务中。
    + newScheduledThreadPool：创建一个定长线程池，支持定时及周期性任务执行，可以延时启动，定时启动的线程池，适用于需要多个后台线程执行周期任务的场景。
    + newSingleThreadExecutor：创建一个单线程化的线程池，它只会用唯一的工作线程来执行任务，保证所有任务按照指定顺序（FIFO，LIFO，优先级）执行。
- ForkJoinPool：
    + newWorkStealingPool：创建一个拥有多个任务队列的线程池，可以减少连接数，创建当前可用cpu数量的线程来并行执行，适用于大耗时的操作，可以并行来执行。

*45. **线程池都有哪些状态？**

线程池有5种状态：

- Running：接受新task，处理等待的task;
- ShutDown：不接受新task，但处理等待的task;
- Stop：不接受新task，不处理等待的task，尝试打断正在执行的task;
- Tidying：所有task都被终止，workCount == 0的时候。
- Terminated：执行完terminated()方法。

*46. **线程池中submit()和execute()方法有什么区别？**

- 接收参数不一样，submit()可以接收Runnable和Callable；execute()只能接受Runnable。
- submit()方法具有返回值，execute()方法没有。
- submit()方便Excpetion处理。

*47. **在Java程序中怎么保证多线程的运行安全？**

三个方面体现：

- 原子性：提供互斥访问，同一时刻只能有一个线程对数据进行操作。
- 可见性：一个线程对内存的修改可以及时地被其他线程看到。
- 有序性：一个线程观察其他线程中的指令执行顺序，由于指令重排序，该观察结果一般杂乱无序。

*48. **多线程的锁升级原理是什么？**

在Java中，锁共有4种状态，级别从低到高依次为：无状态锁，偏向锁，轻量级锁和重量级锁状态，这几个状态会随着竞争情况逐渐升级。

![锁升级](images\锁升级.png)

偏向锁（乐观锁）： 
当锁对象第一次被线程获取的时候，虚拟机会将锁对象的对象头中的锁标志位设置成为01，并将偏向锁标志设置为1，线程通过CAS的方式将自己的ID值放置到对象头中（因为在这个过程中有可能会有其他线程来竞争锁，所以要通过CAS的方式，一旦有竞争就会升级为轻量级锁了），如果成功线程就获得了该轻量级锁。这样每次再进入该锁对象的时候不用进行任何的同步操作，直接比较当前锁对象的对象头是不是该线程的ID，如果是就可以直接进入。

偏向锁升级为轻量级锁：
1）偏向锁是一种无竞争锁，一旦出现了竞争大多数情况下就会升级为轻量级锁。现在我们假设有线程1持有偏向锁，线程2来竞争偏向锁会经历以下几个过程： 
首先线程2会先检查偏向锁标记，如果是1，说明当前是偏向锁，那么JVM会找到线程1，看线程1是否还存活着。
2）如果线程1已经执行完毕，就是说线程1不存在了（线程1自己是不会去释放偏向锁的），那么先将偏向锁置为0，对象头设置成为无锁的状态，用CAS的方式尝试将线程2的ID放入对象头中，不进行锁升级，还是偏向锁。
3）如果线程1还活着，先暂停线程1，将锁标志位变成00（轻量级锁）然后在线程1的栈帧中开辟出一块空间（Display Mark Word）将对象头的Mark Word置换到线程一的栈帧当中，而对象头中此时存储的是指向当前线程栈帧的指针。此时就变成了轻量级锁。继续执行线程1，然后线程2采用CAS的方式尝试获取锁。

轻量级锁： 
轻量级锁是通过CAS也就是自旋的方式尝试获取锁对象，一旦失败会先检查，对象头中存储的是否是指向当前线程栈帧的指针，如果是，就可以获取对象，如果不是说明存在竞争那么就要膨胀为重量级锁。轻量级锁的解锁也是通过CAS的方式尝试将对象头的Mark Word和线程中的Display Mark Word替换回来，如果成功，就释放锁，如果失败说明还有许多其他等待锁的线程（说明此时已经不是轻量级锁而是重量级锁了），会将这些线程唤醒，然后释放锁。

重量级锁：
一旦有两条以上的线程竞争锁，轻量级锁膨胀为重量级锁，锁的状态变成10，此时对象头中存储的就是指向重量级锁的栈帧的指针。而且其他等待锁的线程要进入阻塞状态，等待重量级锁释放再来被唤醒然后去竞争。

*49. **什么是死锁？**

死锁是指两个或两个以上的进程在执行过程中，由于竞争资源或者由于彼此通信而造成的一种阻塞的现象，若无外力作用，它们都将无法推进下去。此时称系统处于死锁状态或系统产生了死锁，这些永远在互相等待的进程称为死锁进程。

*50. **如何避免死锁？**

死锁的四个必要条件：

- 互斥条件：进程对所分配到的资源不允许其他进程进行访问，若其他进程访问该资源，只能等待，直至占有该资源的进程使用完成后释放该资源。
- 请求和保持条件：进程获得一定的资源之后，又对其他资源发出请求，但是该资源可能被其他进程占有，此时请求阻塞，但又对自己获得的资源保持不放。
- 不可剥夺条件：是指进程已获得的资源，在未完成使用之前，不可被剥夺，只能在使用完后自己释放。
- 环路等待条件：指进程发生死锁后，若干进程之间形成一种头尾相接的循环等待资源关系。

这四个条件是死锁的必要条件，只要系统发生死锁，这些条件必然成立，而只要上述条件之一不满足，就不会发生死锁。所以要避免死锁就要最大可能地避免和预防这四个条件。

*51. **ThreadLocal是什么？有哪些使用场景？**

线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java提供ThreadLocal类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如web服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java应用就存在内存泄露的风险。

*52. **说一下synchronized底层实现原理？**

参见：[synchronized原理](https://nicky-chen.github.io/2018/05/14/synchronized-principle/){:target="_blank"}

*53. **synchronized和volatile的区别是什么？**

- volatile本质是在告诉jvm当前变量在寄存器（工作内存）中的值是不确定的，需要从主存中读取；synchronized则是锁定当前变量，只有当前线程可以访问该变量，其他线程被阻塞住。
- volatile仅能使用在变量级别；synchronized则可以使用在变量、方法、和类级别的。
- volatile仅能实现变量的修改可见性，不能保证原子性；而synchronized则可以保证变量的修改可见性和原子性。
- volatile不会造成线程的阻塞；synchronized可能会造成线程的阻塞。

*54. **synchronized和Lock有什么区别？**

- synchronized是Java内置的关键字，而Lock本质上是一个接口。
- synchronzied无法判断是否获取锁的状态，而Lock可以。
- synchronized可以自动释放锁，而Lock需要手动释放。
- synchronized会使拿不到锁的线程持续等待，而Lock允许尝试获取锁，获取不到时无需一直等待。
- synchronized是不可公平、不可中断的，而Lock是可中断可公平的。

*55. **synchronized和ReentrantLock区别是什么？**

同上。

*56. **说一下Atomic的原理？**

Atomic包中的类基本的特性就是在多线程环境下，当有多个线程同时对单个（包括基本类型及引用类型）变量进行操作时，具有排他性，即当多个线程同时对该变量的值进行更新时，仅有一个线程能成功，而未成功的线程可以向自旋锁一样，继续尝试，一直等到执行成功。

Atomic系列的类中的核心方法都会调用Unsafe类中的几个本地方法。我们需要先知道一个东西就是Unsafe类，全名为：sun.misc.Unsafe。这个类使用了大量的native方法来实现原子操作。

## 第三部分 反射

*57. **什么是反射？**

反射主要是指程序可以访问、检测和修改它本身状态或行为的一种能力。

Java反射机制主要提供了以下功能：

- 在运行时判断任意一个对象所属的类。
- 在运行时构造任意一个类的对象。
- 在运行时修改任意一个类所的成员变量。
- 在运行时调用任意一个对象的方法。 

*58. **什么是序列化？什么情况下需要序列化？**

序列化：为了保存在内存中各种对象的状态（成员变量），Java提供的一种保存对象和读取已保存对象的机制。

需要序列化的情况：

- 将对象保存为一个文件或数据库时。
- 通过网络传输对象时。

*59. **动态代理是什么？有哪些应用？**

动态代理：

当想要给实现了某个接口的类中的方法，加一些额外的处理。比如说加日志，加事务等。可以给这个类创建一个代理，故名思议就是创建一个新的类，这个类不仅包含原来类方法的功能，而且还在原来的基础上添加了额外处理的新类。这个代理类并不是定义好的，是动态生成的。具有解耦意义，灵活，扩展性强。

应用：

- AOP
- 增加事物
- 增加权限
- 增加日志

*60. **如何实现动态代理？**

示例1：

```java
// 接口
interface A {
    void func();
}

// 实现类
class B implements A {

    @Override
    public void func() {
        System.out.println("Call funcB");
    }
}

// 实现类
class C implements A {

    @Override
    public void func() {
        System.out.println("Call funcC");
    }
}

// 代理类，实现InvocationHandler
class DynamicProxy implements InvocationHandler {

    private Object object;

    public DynamicProxy(Object object) {
        this.object = object;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("Before...");
        Object invoke = method.invoke(object, args);
        System.out.println("After...");
        return invoke;
    }
}

public class TestClass {

    @Test
    public void testCase() {
        B b = new B();
        C c = new C();
        A proxyB = (A) Proxy.newProxyInstance(b.getClass().getClassLoader(), b.getClass().getInterfaces(), new DynamicProxy(b));
        A proxyC = (A) Proxy.newProxyInstance(c.getClass().getClassLoader(), c.getClass().getInterfaces(), new DynamicProxy(c));
        proxyB.func();
        proxyC.func();
    }
}
/*
Before...
Call funcB
After...
Before...
Call funcC
After...
*/
```

示例2：

```java
// 接口
interface A {
    void func();
}

// 实现类
class B implements A {

    @Override
    public void func() {
        System.out.println("Call funcB");
    }
}

// 实现类
class C implements A {

    @Override
    public void func() {
        System.out.println("Call funcC");
    }
}

class DynamicProxy {

    private Object object;

    public DynamicProxy(Object object) {
        this.object = object;
    }

    public Object getProxy() {
        return Proxy.newProxyInstance(object.getClass().getClassLoader(), object.getClass().getInterfaces(), new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("Before...");
                Object invoke = method.invoke(object, args);
                System.out.println("After...");
                return invoke;
            }
        });
    }
}

public class TestClass {

    @Test
    public void testCase() {
        B b = new B();
        C c = new C();
        A proxyB = (A) new DynamicProxy(b).getProxy();
        A proxyC = (A) new DynamicProxy(c).getProxy();
        proxyB.func();
        proxyC.func();
    }
}

/*
方法执行前...
你好！
方法执行后...
*/
```

## 第四部分 对象拷贝

*61. **为什么要使用克隆？**

当我们想对一个对象进行处理，又想保留原有的数据时，就需要克隆一个新的实例。

*62. **如何实现对象克隆？**

- 实现Cloneable接口，该接口为标记接口，无任何方法。
- 在类中重写Object类中的clone()方法。
- 在clone()方法中调用super.clone()。
- 把浅复制的引用指向原型对象的新克隆体。

也可以通过序列化和反序列化的方式实现。

*63. **浅复制与深复制的区别？**

- 浅复制：被复制对象的所有变量都含有与原来对象相同的值，而所有其他对象的引用仍然指向原来的对象。浅复制仅仅复制所考虑的对象，而不复制它引用的对象。
- 深复制：被复制对象的所有变量都含有与原来对象相同的值，除去那些引用其他对象的变量。那些引用其他对象的变量将指向被复制的新对象，而不再是原有的那些被引用的对象。即深复制把复制的对象所引用的对象都复制了一遍。


