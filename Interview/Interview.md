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

sleep()方法是Thread的静态方法，让调用的线程进入指定时间睡眠状态，使得当前线程进入阻塞状态，告诉系统至少在指定时间内不需要为线程调度器为该线程分配执行时间片，把执行机会给其他线程（实际上，调用sleep()方法时并不要求持有任何锁，即sleep()可在任何地方使用。），但是监控状态依然保持，到时后会自动恢复。

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
    + newFixedThreadPool：创建一个可重用固定线程数的线程池，采用无界的阻塞队列，所以实际线程数量永远不会变化，适用于可以预测线程数量的业务中。
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

## 第五部分 Java Web

*64. **JSP与Servlet有什么区别？**

- JSP经编译后会成为Servlet。
- JSP更擅长页面显示，Servlet更擅长逻辑控制。
- Servlet中没有内置对象，JSP具有内置对象。

*65. **JSP有哪些内置对象？作用分别是什么？**

内置对象 | 类型 | 说明 | 作用域
----- | ----- | ----- | -----
request | javax.servlet.ServletRequest | 请求对象——在一次Request请求周期 中传递数据，请求结束后，数据不可访问，用于页面之间的值的传递 | 用户请求期
response |javax.servlet.ServletResponse |响应对象——用来响应客户端请求并向客户端输出信息|页面执行期
out | javax.servlet.jsp.JspWriter|输出对象——表示输出流，该流将作为请求的响应发送到客户端 |页面执行期
session |javax.servlet.http.HttpSession |会话对象——在一个session会话周期中，用于存储有关用户会话的所有信息 |会话周期
application |javax.servlet.ServletContext |应用程序对象——用于实现用户之间的数据共享，负责提供应用程序在服务器中运行时的一些全局信息 |整个程序运行期
pageContext |javax.servlet.jsp.PageContext |页面上下文对象——此对象提供所有四个作用域层次的属性查询和修改能力，它也提供了转发请求到其它资源和包含其他资源的方法 |页面执行期
page |javax.lang.Object |页面对象——只在当前JSP页面内有效，提供对网页上定义的所有对象的访问，其表示的就是页面本身 |页面执行期
config |javax.servlet.ServletConfig |配置对象——是ServletConfig的实例，代理当前jsp页面配置，提供配置信息 |页面执行期
execpiton |javax.lang.Throwable |例外对象——此对象指JSP文件运行时所产生的例外对象，也就是JSP引擎在执行代码时抛出的异常，此对象不能在一般JSP文件中直接使用，而只能在使用了“<%@ page isErrorPage="true"%>”的JSP文件中使用，也就是说只有在page指令中具有属性isErrorPage="true"时才有效 |页面执行期

*66. **说一下JSP的四种作用域？**

- page：代表与一个页面相关的对象和属性。
- request：代表与Web客户机发出的一个请求相关的对象和属性。一个请求可能跨越多个页面，涉及多个Web组件。
- session：代表与某个用户与服务器建立的一次会话相关的对象和属性。
- application：代表与整个Web应用程序相关的对象和属性，它实质上是跨越整个Web应用程序，包括多个页面、请求和会话的一个全局作用域。

*67. **session和cookie有什么区别？**

由于HTTP协议是无状态的协议，所以服务端需要记录用户的状态时，就需要用某种机制来识别具体的用户，这个机制就是Session。Session用于标识用户、跟踪用户。这个Session是保存在服务端的，有一个唯一标识。在服务端保存Session的方法很多，内存、数据库、文件都有。集群的时候也要考虑Session的转移，在大型的网站，一般会有专门的Session服务器集群，用来保存用户会话，这个时候 Session 信息都是放在内存的，使用一些缓存服务比如Memcached之类的来放Session。

每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。实际上大多数的应用都是用 Cookie来实现Session跟踪的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在Cookie里面记录一个Session ID，以后每次请求把这个会话ID发送到服务器，我就知道你是谁了。有人问，如果客户端的浏览器禁用了 Cookie 怎么办？一般这种情况下，会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。某些信息也信息可以写到Cookie里面，然后在访问网站的时候，网站页面的脚本可以读取这个信息，就会自动帮助我们做一些工作，例如表单的自动填充。

Session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。

*68. **说一下session的工作原理？**

session是一个存在服务器上的类似于一个散列表格的文件。里面存有我们需要的信息，在我们需要用的时候可以从里面取出来。其与Map类似，内部为存储键值对。Session通过sessionId进行标识。

*69. **如果客户端禁止cookie那么session还能用吗？**

通过URL重写或者隐藏表单来传递sessionId。

*70. **spring mvc 和 struts 的区别是什么？**

**拦截机制：**

sturts2是基于类的设计，是类级别的拦截，每次发一次请求都会实例一个action，然后调用setter getter方法把request中的数据注入，struts2实际上是通过setter getter方法与request打交道的，struts2中，一个Action对象对应 一个request上下文；spring mvc是基于方法的设计，粒度更细，是方法级别的拦截，拦截到方法后根据参数上的注解，把request数据注入进 去，在spring mvc中，一个方法对应一个request上下文；

**加载机制：**

spring mvc的入口是servlet，而struts2是filter；

**性能方面：**

Struts2是类级别的拦截，每次请求对应实例一个新的Action，需要加载所有的属性值注入，由于Spring MVC基于方法的拦截，并使用单例模式bean注入。所以，SpringMVC开发效率和性能高于Struts2。

**配置方面：**

在与Spring的整合方面，Spring MVC更好。

*71. **如何避免SQL注入？**

- 使用PreparedStatement
- 使用正则表达式过滤传入的参数
- 在页面上进行判断

*72. **什么是 XSS 攻击，如何避免？**

XSS攻击又称CSS,全称Cross Site Script  （跨站脚本攻击），其原理是攻击者向有XSS漏洞的网站中输入恶意的 HTML 代码，当用户浏览该网站时，这段 HTML 代码会自动执行，从而达到攻击的目的。XSS 攻击类似于 SQL 注入攻击，SQL注入攻击中以SQL语句作为用户输入，从而达到查询/修改/删除数据的目的，而在xss攻击中，通过插入恶意脚本，实现对用户游览器的控制，获取用户的一些信息。 XSS是 Web 程序中常见的漏洞，XSS 属于被动式且用于客户端的攻击方式。

XSS防范的总体思路是：对输入(和URL参数)进行过滤，对输出进行编码。

*73. **什么是 CSRF 攻击，如何避免？**

CSRF（Cross-site request forgery）跨站请求伪造：攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。

防护策略，如下：

- 阻止不明外域的访问：
    + 同源检测
    + Samesite Cookie
- 提交时要求附加本域才能获取的信息：
    + CSRF Token    
    + 双重Cookie验证

具体参见：[前端安全系列之二：如何防止CSRF攻击？](https://www.cnblogs.com/meituantech/p/9777222.html){:target="_blank"}

## 第六部分 异常

*74. **throw和throws的区别？**

throws是用来声明一个方法可能抛出的所有异常信息，throws是将异常声明但是不处理，而是将异常往上传，谁调用我就交给谁处理。而throw则是指抛出一个具体的异常。

*75. **final、finally、finalize有什么区别？**

- final：
    + 修饰类表示类不可被继承
    + 修饰成员变量：
        * 基本类型：值不变
        * 引用类型：引用指向的地址不变
    + 修饰成员方法表示该方法不可被重写
- finally：finally一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。
- finalize：finalize是一个方法，属于Object类的一个方法，而Object类是所有类的父类，该方法一般由垃圾回收器来调用，当我们调用System的gc()方法的时候，由垃圾回收器调用finalize(),回收垃圾。

*76. **try-catch-finally 中哪个部分可以省略？**

try-catch-finally 其中 catch 和 finally 都可以被省略，但是不能同时省略，也就是说有 try 的时候，必须后面跟一个 catch 或者 finally。

*77. **try-catch-finally 中，如果 catch 中 return 了，finally 还会执行吗？**

finally 一定会执行，即使是 catch 中 return 了，catch 中的 return 会等 finally 中的代码执行完之后，才会执行。

*78. **常见的异常类有哪些？**

- NullPointerException 空指针异常
- ClassNotFoundException 指定类不存在
- NumberFormatException 字符串转换为数字异常
- IndexOutOfBoundsException 数组下标越界异常
- ClassCastException 数据类型转换异常
- FileNotFoundException 文件未找到异常
- NoSuchMethodException 方法不存在异常
- IOException IO 异常
- SocketException Socket 异常

## 第七部分 网络

*79. **http 响应码 301 和 302 代表的是什么？有什么区别？**

- 301：永久重定向。
- 302：暂时重定向。

它们的区别是，301对搜索引擎优化（SEO）更加有利；302有被提示为网络拦截的风险。

*80. **forward 和 redirect 的区别？**

forward 是转发，而 redirect 是重定向，它们的区别如下：

- 地址栏 url 显示：foward url 不会发生改变，redirect url 会发生改变；
- 数据共享：forward 可以共享 request 里的数据，redirect 不能共享；
- 效率：forward 比 redirect 效率高。

*81. **简述 tcp 和 udp的区别**？

tcp 和 udp 是 OSI 模型中的传输层中的协议。tcp 提供可靠的通信传输，而 udp 则常被用于让广播和细节控制交给应用的通信传输。

两者的区别大致如下：

- tcp 面向连接，udp 面向非连接，即发送数据前不需要建立链接；
- tcp 提供可靠的数据传输，udp 无法保证；
- tcp 面向字节流，udp 面向报文；
- tcp 数据传输慢，udp 数据传输快。

*82. **tcp 为什么要三次握手，两次不行吗？为什么？**

- 为了实现可靠数据传输，TCP协议的通信双方，都必须维护一个序列号，以标识发送出去的数据包中， 哪些是已经被对方收到的。三次握手的过程即是通信双方相互告知序列号起始值， 并确认对方已经收到了序列号起始值的必经步骤。
- 如果只是两次握手， 至多只有连接发起方的起始序列号能被确认， 另一方选择的序列号则得不到确认。

具体参见[TCP 为什么三次握手而不是两次握手（正解版）](https://blog.csdn.net/lengxiao1993/article/details/82771768){:target="_blank"}。

*83. **说一下 tcp 粘包是怎么产生的？**

粘包发生在发送或接收缓冲区中；应用程序从缓冲区中取数据是整个缓冲区中有多少取多少；那么就有可能第一个数据的尾部和第二个数据的头部同时存在缓冲区，而TCP是流式的，数据无边界，这时发生粘包。 

- 发送方产生粘包：采用TCP协议传输数据的客户端与服务器经常是保持一个长连接的状态，双方在连接不断开的情况下，可以一直传输数据；但当发送的数据包过于的小时，那么TCP协议默认的会启用Nagle算法，将这些较小的数据包进行合并发送（缓冲区数据发送是一个堆压的过程）；这个合并过程就是在发送缓冲区中进行的，也就是说数据发送出来它已经是粘包的状态了。
- 接收方发生粘包：数据到接收方，从网络模型的下方传递至传输层，传输层的TCP协议处理是将其放置接收缓冲区，然后由应用层来主动获取。我们在程序中调用的读取数据函数不能及时的把缓冲区中的数据拿出来，而下一个数据又到来并有一部分放入的缓冲区末尾，等我们读取数据时就是一个粘包。（放数据的速度 > 应用层拿数据速度）

*84. **说一下OSI七层模型？**

- 应用层：为应用程序提供服务。
- 表示层：数据格式转化、数据加密和压缩。
- 会话层：建立、管理和维护会话。
- 传输层：定义传输数据的协议、端口号，以及流控和差错校验。
- 网络层：进行逻辑地址寻址，实现不同网络之间的路径选择。
- 数据链路层：建立逻辑连接、进行硬件地址寻址、差错校验等功能。
- 物理层：建立、维护、断开物理连接。

*85. **get和post请求有哪些区别？**

- GET在浏览器回退时是无害的，而POST会再次提交请求。
- GET产生的URL地址可以被Bookmark，而POST不可以。
- GET请求会被浏览器主动cache，而POST不会，除非手动设置。
- GET请求只能进行url编码，而POST支持多种编码方式。
- GET请求参数会被完整保留在浏览器历史记录里，而POST中的参数不会被保留。
- GET请求在URL中传送的参数是有长度限制的，而POST么有。
- 对参数的数据类型，GET只接受ASCII字符，而POST没有限制。
- GET比POST更不安全，因为参数直接暴露在URL上，所以不能用来传递敏感信息。
- GET参数通过URL传递，POST放在Request body中。

*86. **如何实现跨域？**

略。

*87. **说一下 JSONP 实现原理？**

略。

## 第八部分 设计模式

*88. **说一下你熟悉的设计模式？**

- 单例模式：保证被创建一次，节省系统开销。
- 工厂模式（简单工厂、抽象工厂）：解耦代码。
- 观察者模式：定义了对象之间的一对多的依赖，这样一来，当一个对象改变时，它的所有的依赖者都会收到通知并自动更新。
- 外观模式：提供一个统一的接口，用来访问子系统中的一群接口，外观定义了一个高层的接口，让子系统更容易使用。
- 模版方法模式：定义了一个算法的骨架，而将一些步骤延迟到子类中，模版方法使得子类可以在不改变算法结构的情况下，重新定义算法的步骤。
- 状态模式：允许对象在内部状态改变时改变它的行为，对象看起来好像修改了它的类。

*89. **简单工厂和抽象工厂有什么区别？**

- 简单工厂：用来生产同一等级结构中的任意产品，对于增加新的产品，无能为力。
- 工厂方法：用来生产同一等级结构中的固定产品，支持增加任意产品。
- 抽象工厂：用来生产不同产品族的全部产品，对于增加新的产品，无能为力；支持增加产品族。

## 第九部分 Spring / SpringMVC

*90. **为什么要使用Spring？**

- 提供了IOC，方便解耦，便于开发。
- 支持AOP。
- 支持声明式的事务。
- 方便程序测试。
- 方便集成其他框架
- 降低了JavaEE API的使用难度。
- 框架轻量。
- 非侵入性。

*91. **解释一下什么是aop？**

aop 是面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。简单来说就是统一处理某一“切面”（类）的问题的编程思想，比如统一处理日志、异常等。

*92. **解释一下什么是 ioc？**

IoC（控制反转），是spring的核心，对于 spring 框架来讲，就是由 spring 来负责控制对象的生命周期和对象间的关系。简单来说，控制指的是当前对象对内部成员的控制权；控制反转指的是，这种控制权不由当前对象管理了，由其他（类，第三方容器）来管理。

*93. **spring 有哪些主要模块？**

![spring模块](images\spring模块.png)

Spring由20多个模块组成，它们可以分为数据访问/集成（Data Access/Integration）、Web、面向切面编程（AOP, Aspect Oriented Programming）、应用服务器设备管理（Instrumentation）、消息发送（Messaging）、核心容器（Core Container）和测试（Test）。

*94. **spring中常用的注入方式有哪些？**

- setter属性注入
- 构造方法注入
- 注解方法注入

*95. **spring 中的 bean 是线程安全的吗？**

spring 中的 bean 默认是单例模式，spring 框架并没有对单例 bean 进行多线程的封装处理。

实际上大部分时候 spring bean 无状态的（比如 dao 类），所以某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean 的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean()了，所以就可以保证线程安全了。

*96. **spring 支持几种 bean 的作用域？**

- singleton：spring ioc 容器中只存在一个 bean 实例，bean 以单例模式存在，是系统默认值；
- prototype：每次从容器调用 bean 时都会创建一个新的示例，即每次 getBean()相当于执行 new Bean()操作；
- Web环境下：
    + request：每次 http 请求都会创建一个 bean；
    + session：同一个 http session 共享一个 bean 实例；
    + global-session：用于 portlet 容器，因为每个 portlet 有单独的 session，globalsession 提供一个全局性的 http session。

*97. **spring 自动装配 bean 有哪些方式？**

- no：默认值，表示没有自动装配，应使用显式 bean 引用进行装配。
- byName：它根据 bean 的名称注入对象依赖项。
- byType：它根据类型注入对象依赖项。
- 构造函数：通过构造函数来注入依赖项，需要设置大量的参数。
- autodetect：容器首先通过构造函数使用 autowire 装配，如果不能，则通过 byType 自动装配。

*98. **spring 事务实现方式有哪些？**

- 声明式事务：声明式事务也有两种实现方式，基于xml配置文件的方式和注解方式（在类上添加@Transaction注解）。
- 编码方式：提供编码的形式管理和维护事务。

*99. **说一下 spring 的事务隔离？**

spring 有五大隔离级别，默认值为 ISOLATION_DEFAULT（使用数据库的设置），其他四个隔离级别和数据库的隔离级别一致：

- 默认（default）：这是一个PlatfromTransactionManager默认的隔离级别，使用数据库默认的事务隔离级别. 
- 未提交读（read uncommited）：脏读，不可重复读，虚读都有可能发生 
- 已提交读 （read commited）：避免脏读。但是不可重复读和虚读有可能发生 
- 可重复读 （repeatable read）：避免脏读和不可重复读.但是虚读有可能发生. 
- 串行化的 （serializable）：避免以上所有读问题. 

脏读、不可重复读、虚读：

- 脏读 ：表示一个事务能够读取另一个事务中还未提交的数据。比如，某个事务尝试插入记录 A，此时该事务还未提交，然后另一个事务尝试读取到了记录 A。
- 不可重复读 ：一个事务读到了另一个事务已经提交的update的数据导致多次查询结果不一致.。
- 虚读 ：指同一个事务内多次查询返回的结果集不一样。比如同一个事务 A 第一次查询时候有 n 条记录，但是第二次同等条件下查询却有 n+1 条记录，这就好像产生了幻觉。发生幻读的原因也是另外一个事务新增或者删除或者修改了第一个事务结果集里面的数据，同一个记录的数据内容被修改了，所有数据行的记录就变多或者变少了。

*100. **说一下 spring mvc 运行流程？**

1、用户发送请求至前端控制器DispatcherServlet 
2、DispatcherServlet收到请求调用HandlerMapping处理器映射器。 
3、处理器映射器找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。 
4、DispatcherServlet调用HandlerAdapter处理器适配器 
5、HandlerAdapter经过适配调用具体的处理器(Controller，也叫后端控制器)。 
6、Controller执行完成返回ModelAndView 
7、HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet 
8、DispatcherServlet将ModelAndView传给ViewReslover视图解析器 
9、ViewReslover解析后返回具体View 
10、DispatcherServlet根据View进行渲染视图（即将模型数据填充至视图中）。 
11、DispatcherServlet响应用户

*101. **spring mvc 有哪些组件？**

- 前端控制器 DispatcherServlet。
- 处理器映射器 HandlerMapping。
- 处理器适配器 HandlerAdapter。
- 处理器 Controller。
- 视图解析器 ViewReslover。
- 模型和视图 ModelAndView。

*102. **@RequestMapping 的作用是什么？**

将 http 请求映射到相应的类/方法上。

*103. **@Autowired 的作用是什么？**

@Autowired 是一个注释，它可以对类成员变量、方法及构造函数进行标注，让 spring 完成 bean 自动装配的工作。
@Autowired 默认是按照类去匹配，配合 @Qualifier 指定按照名称去装配 bean。

## 第十部分 Spring Boot / Spring Cloud

*104. **什么是 spring boot？**

SpringBoot是一个框架，一种全新的编程规范，他的产生简化了框架的使用，所谓简化是指简化了Spring众多框架中所需的大量且繁琐的配置文件，所以SpringBoot是一个服务于框架的框架，服务范围是简化配置文件。

*105 **为什么要用 spring boot？**

- 配置简单
- 独立运行
- 自动装配
- 提供应用监控
- 易上手
- 开发效率高

*106. **spring boot 核心配置文件是什么？**

spring boot 核心的两个配置文件：

- bootstrap：.yml/.properties，boostrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，且 boostrap 里面的属性不能被覆盖；
- application：.yml/.properties，用于 spring boot 项目的自动化配置。

*107. **spring boot 配置文件有哪几种类型？它们有什么区别？**

两种。

- properties
- yaml

主要是书写风格不同。

*108. **spring boot 有哪些方式可以实现热部署？**

- 使用 devtools  启动热部署，添加 devtools 库，在配置文件中把 spring.devtools.restart.enabled 设置为 true。
- 使用 Intellij IDE 编辑器，勾上自动编译或者手动重新编译。

*109. **jpa 和 hibernate 有什么区别？**

jpa 全称 Java Persistence API，是 Java 持久化接口规范，hibernate 属于 jpa 的具体实现。

*110. **什么是 spring cloud？**

spring cloud 是一系列框架的有序集合。它利用 spring boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 spring boot 的开发风格做到一键启动和部署。

*111. **spring cloud 断路器的作用是什么？**

在分布式架构中，断路器模式的作用也是类似的，当某个服务单元发生故障（类似用电器发生短路）之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个错误响应，而不是长时间的等待。这样就不会使得线程因调用故障服务被长时间占用不释放，避免了故障在分布式系统中的蔓延。

*112. **spring cloud 的核心组件有哪些？**

- Eureka：主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。
- Feign：基于动态代理机制，根据注解和选择的机器，拼接请求 url 地址，发起请求。
- Ribbon：实现负载均衡，从一个服务的多台机器中选择一台。
- Hystrix：提供线程池，不同的服务走不同的线程池，实现了不同服务调用的隔离，避免了服务雪崩的问题。
- Zuul：网关管理，由 Zuul 网关转发请求给对应的服务。

## 第十一部分 Hibernate

*113. **为什么要使用 hibernate？**

- hibernate 是对 jdbc 的封装，大大简化了数据访问层的繁琐的重复性代码。
- hibernate 是一个优秀的 ORM 实现，很多程度上简化了 DAO 层的编码功能。
- 可以很方便的进行数据库的移植工作。
- 提供了缓存机制，使程序执行更改的高效。

*114. **什么是 ORM 框架？**

ORM（Object Relation Mapping），对象关系映射，是把数据库中的关系数据映射成为程序中的对象。

使用 ORM 的优点：提高了开发效率降低了开发成本、开发更简单更对象化、可移植更强。

*115. **hibernate 中如何在控制台查看打印的 SQL 语句？**

在 Config 里面把hibernate.show_SQL设置为true就可以。但不建议开启，开启之后会降低程序的运行效率。

*116. **hibernate 有几种查询方式？**

三种：

- 原生SQL
- HQL
- Critieria

*117. **hibernate 实体类可以被定义为 final 吗？**

实体类可以定义为 final 类，但这样的话就不能使用 hibernate 代理模式下的延迟关联提供性能了，所以不建议定义实体类为 final

*118. **在 hibernate 中使用 Integer 和 int 做映射有什么区别？**

Integer 类型为对象，它的值允许为 null，而 int 属于基础数据类型，值不能为 null。

在Hibernate中，如果将OID定义为Integer类型，那么Hibernate就可以根据其值是否为null而判断一个对象是否是临时的，如果将OID定义为了int类型，还需要在hbm映射文件中设置其unsaved-value属性为0。

*119. **hibernate 是如何工作的？**

- 读取并解析配置文件。
- 读取并解析映射文件，创建 SessionFactory。
- 打开 Session。
- 创建事务。
- 进行持久化操作。
- 提交事务。
- 关闭 Session。
- 关闭 SessionFactory。

*120. **get()和 load()的区别？**

- 返回值：get()返回的是查询出来的实体对象，而load()查询出来的是一个目标实体的代理对象。
- 查询时机：get()在调用的时候就立即发出SQL语句查询，而load()在访问非ID属性的时候才会发出查询语句并且将被代理对象target填充上，但是如果这个动作发生在Session被关闭后的话就会抛出LazyInitializationException。
- 查询结果为空时：get()抛出NullPointerException，load()抛出ObjectNotFoundException

*121. **说一下hibernate的缓存机制？**

hibernate 常用的缓存有一级缓存和二级缓存：

一级缓存就是  Session 级别的缓存，在事务范围内有效是,内置的不能被卸载。二级缓存是 SesionFactory级别的缓存，从应用启动到应用结束有效。是可选的，默认没有二级缓存，需要手动开启。保存数据库后，缓存在内存中保存一份，如果更新了数据库就要同步更新。

什么样的数据适合存放到第二级缓存中？

- 很少被修改的数据
- 经常被查询的数据
- 不是很重要的数据，允许出现偶尔并发的数据
- 不会被并发访问的数据
- 常量数据

*122. **hibernate 对象有哪些状态？**

hibernate里对象有三种状态：

- Transient（瞬时）：对象刚new出来，还没设id，设了其他值。
- Persistent（持久）：调用了save()、saveOrUpdate()，就变成Persistent，有id。
- Detached（游离）：当session close()之后，变成Detached。

完整关系图：

![hibernate对象状态](images\hibernate对象状态.png)

*123. **在 hibernate 中 getCurrentSession 和 openSession 的区别是什么？**

openSession 从字面上可以看得出来，是打开一个新的session对象，而且每次使用都是打开一个新的session，假如连续使用多次，则获得的session不是同一个对象，并且使用完需要调用close方法关闭session。

getCurrentSession ，从字面上可以看得出来，是获取当前上下文一个session对象，当第一次使用此方法时，会自动产生一个session对象，并且连续使用多次时，得到的session都是同一个对象，这就是与openSession的区别之一，简单而言，getCurrentSession 就是：如果有已经使用的，用旧的，如果没有，建新的。

*124. **hibernate 实体类必须要有无参构造函数吗？为什么？**

必须。

因为hibernate框架会调用这个默认构造方法来构造实例对象，即Class类的newInstance方法，这个方法就是通过调用默认构造方法来创建实例对象的。

## 第十二部分 Mybatis

*125. **mybatis 中 #{}和 ${}的区别是什么？**

- \#{}是预编译处理，${}是字符串替换；
- Mybatis在处理#{}时，会将sql中的#{}替换为?号，调用PreparedStatement的set方法来赋值；
- Mybatis在处理\${}时，就是把\${}替换成变量的值；
- 使用#{}可以有效的防止SQL注入，提高系统安全性。

*126. **mybatis 有几种分页方式？**

- 逻辑分页：使用MyBatis自带的RowBounds进行分页，它是一次性查询很多数据，然后在数据中再进行检索。
- 物理分页：自己手写SQL分页或使用分页插件PageHelper，去数据库查询指定条数的分页数据的形式。

*127. **RowBounds 是一次性查询全部结果吗？为什么？**

RowBounds 表面是在“所有”数据中检索数据，其实并非是一次性查询出所有数据，因为 MyBatis 是对 jdbc 的封装，在 jdbc 驱动中有一个 Fetch Size 的配置，它规定了每次最多从数据库查询多少条数据，假如你要查询更多数据，它会在你执行 next()的时候，去查询更多的数据。

*128. **mybatis 逻辑分页和物理分页的区别是什么？**

- 逻辑分页是一次性查询很多数据，然后再在结果中检索分页的数据。这样做弊端是需要消耗大量的内存、有内存溢出的风险、对数据库压力较大。
- 物理分页是从数据库查询指定条数的数据，弥补了一次性全部查出的所有数据的种种缺点，比如需要大量的内存，对数据库查询压力较大等问题。

*129. **mybatis 是否支持延迟加载？延迟加载的原理是什么？**

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

延迟加载的原理是调用的时候触发加载，而不是在初始化的时候就加载信息。比如调用 a. getB(). getName()，这个时候发现 a. getB() 的值为 null，此时会单独触发事先保存好的关联 B 对象的 SQL，先查询出来 B，然后再调用 a. setB(b)，而这时候再调用 a. getB(). getName() 就有值了，这就是延迟加载的基本原理。

*130. **说一下 MyBatis 的一级缓存和二级缓存？**

- 一级缓存：基于 PerpetualCache 的 HashMap 本地缓存，它的声明周期是和 SQLSession 一致的，有多个 SQLSession 或者分布式的环境中数据库操作，可能会出现脏数据。当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认一级缓存是开启的。
- 二级缓存：也是基于 PerpetualCache 的 HashMap 本地缓存，不同在于其存储作用域为 Mapper 级别的，如果多个SQLSession之间需要共享缓存，则需要使用到二级缓存，并且二级缓存可自定义存储源，如Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现 Serializable 序列化接口(可用来保存对象的状态)。

开启二级缓存数据查询流程：二级缓存 -> 一级缓存 -> 数据库。

缓存更新机制：当某一个作用域(一级缓存 Session/二级缓存 Mapper)进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。

*131. **MyBatis 和 hibernate 的区别有哪些？**

- 灵活性：MyBatis 更加灵活，自己可以写 SQL 语句，使用起来比较方便。
- 可移植性：MyBatis 有很多自己写的 SQL，因为每个数据库的 SQL 可以不相同，所以可移植性比较差。
- 学习和使用门槛：MyBatis 入门比较简单，使用门槛也更低。
- 二级缓存：hibernate 拥有更好的二级缓存，它的二级缓存可以自行更换为第三方的二级缓存。

*132. **MyBatis 有哪些执行器（Executor）？**

MyBatis 有三种基本的Executor执行器：

- SimpleExecutor：每执行一次 update 或 select 就开启一个 Statement 对象，用完立刻关闭 Statement 对象；
- ReuseExecutor：执行 update 或 select，以 SQL 作为 key 查找 Statement 对象，存在就使用，不存在就创建，用完后不关闭 Statement 对象，而是放置于 Map 内供下一次使用。简言之，就是重复使用 Statement 对象；
- BatchExecutor：执行 update（没有 select，jdbc 批处理不支持 select），将所有 SQL 都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个 Statement 对象，每个 Statement 对象都是 addBatch()完毕后，等待逐一执行 executeBatch()批处理，与 jdbc 批处理相同。

*133. **MyBatis 分页插件的实现原理是什么？**

分页插件的基本原理是使用MyBatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的 SQL，然后重写 SQL，根据 dialect 方言，添加对应的物理分页语句和物理分页参数。

*134. **MyBatis 如何编写一个自定义插件？**

MyBatis 自定义插件针对 MyBatis 四大对象（Executor、StatementHandler、ParameterHandler、ResultSetHandler）进行拦截：

- Executor：拦截内部执行器，它负责调用 StatementHandler 操作数据库，并把结果集通过 ResultSetHandler 进行自动映射，另外它还处理了二级缓存的操作；
- StatementHandler：拦截 SQL 语法构建的处理，它是 MyBatis 直接和数据库执行 SQL 脚本的对象，另外它也实现了 MyBatis 的一级缓存；
- ParameterHandler：拦截参数的处理；
- ResultSetHandler：拦截结果集的处理。

自定义插件需要实现Interceptor接口：

```java
public interface Interceptor {   
    Object intercept(Invocation invocation) throws Throwable;       
    Object plugin(Object target);    
    void setProperties(Properties properties);
}
```

- setProperties方法：是在MyBatis进行配置插件的时候可以配置自定义相关属性，即：接口实现对象的参数配置；
- plugin方法：是插件用于封装目标对象的，通过该方法我们可以返回目标对象本身，也可以返回一个它的代理，可以决定是否要进行拦截进而决定要返回一个什么样的目标对象，官方提供了示例：return Plugin.wrap(target, this)；
- intercept方法：就是要进行拦截的时候要执行的方法。

## 第十三部分 RabbitMQ

*135. **rabbitmq 的使用场景有哪些？**

- 跨系统的异步通信，所有需要异步交互的地方都可以使用消息队列。就像我们除了打电话（同步）以外，还需要发短信，发电子邮件（异步）的通讯方式。
- 多个应用之间的耦合，由于消息是平台无关和语言无关的，而且语义上也不再是函数调用，因此更适合作为多个应用之间的松耦合的接口。基于消息队列的耦合，不需要发送方和接收方同时在线。在企业应用集成（EAI）中，文件传输，共享数据库，消息队列，远程过程调用都可以作为集成的方法。
- 应用内的同步变异步，比如订单处理，就可以由前端应用将订单信息放到队列，后端应用从队列里依次获得消息处理，高峰时的大量订单可以积压在队列里慢慢处理掉。由于同步通常意味着阻塞，而大量线程的阻塞会降低计算机的性能。
- 消息驱动的架构（EDA），系统分解为消息队列，和消息制造者和消息消费者，一个处理流程可以根据需要拆成多个阶段（Stage），阶段之间用队列连接起来，前一个阶段处理的结果放入队列，后一个阶段从队列中获取消息继续处理。
- 应用需要更灵活的耦合方式，如发布订阅，比如可以指定路由规则。
- 跨局域网，甚至跨城市的通讯（CDN行业），比如北京机房与广州机房的应用程序的通信。

*136. **rabbitmq 有哪些重要的角色？**

RabbitMQ 中重要的角色有：生产者、消费者和代理：

- 生产者：消息的创建者，负责创建和推送数据到消息服务器；
- 消费者：消息的接收方，用于处理数据和确认消息；
- 代理：就是RabbitMQ本身，用于扮演“快递”的角色，本身不生产消息，只是扮演“快递”的角色。

*137. **RabbitMQ 有哪些重要的组件？**

- ConnectionFactory（连接管理器）：应用程序与Rabbit之间建立连接的管理器，程序代码中使用。
- Channel（信道）：消息推送使用的通道。
- Exchange（交换器）：用于接受、分配消息。
- Queue（队列）：用于存储生产者的消息。
- RoutingKey（路由键）：用于把生成者的数据分配到交换器上。
- BindingKey（绑定键）：用于把交换器的消息绑定到队列上。

*138. **RabbitMQ 中 vhost 的作用是什么？**

vhost：每个 RabbitMQ 都能创建很多 vhost，我们称之为虚拟主机，每个虚拟主机其实都是 mini 版的RabbitMQ，它拥有自己的队列，交换器和绑定，拥有自己的权限机制。

*139. **rabbitmq 的消息是怎么发送的？**

首先客户端必须连接到 RabbitMQ 服务器才能发布和消费消息，客户端和 rabbit server 之间会创建一个 tcp 连接，一旦 tcp 打开并通过了认证（认证就是你发送给 rabbit 服务器的用户名和密码），你的客户端和 RabbitMQ 就创建了一条 amqp 信道（channel），信道是创建在“真实” tcp 上的虚拟连接，amqp 命令都是通过信道发送出去的，每个信道都会有一个唯一的 id，不论是发布消息，订阅队列都是通过这个信道完成的。

*140. **RabbitMQ 怎么保证消息的稳定性？**

- 提供了事务的功能。
- 通过将 channel 设置为 confirm（确认）模式。

*141. **RabbitMQ 怎么避免消息丢失？**

- 消息持久化
- ACK确认机制
- 设置集群镜像模式
- 消息补偿机制

*142. **要保证消息持久化成功的条件有哪些？**

- 声明队列必须设置持久化 durable 设置为 true。
- 消息推送投递模式必须设置持久化，deliveryMode 设置为 2（持久）。
- 消息已经到达持久化交换器。
- 消息已经到达持久化队列。

以上四个条件都满足才能保证消息持久化成功。

*143. **RabbitMQ 持久化有什么缺点？**

持久化的缺点就是降低了服务器的吞吐量，因为使用的是磁盘而非内存存储，从而降低了吞吐量。可尽量使用 ssd 硬盘来缓解吞吐量的问题。

*144. **RabbitMQ 有几种广播类型？**

- direct（默认方式）：最基础最简单的模式，发送方把消息发送给订阅方，如果有多个订阅者，默认采取轮询的方式进行消息发送。
- headers：与 direct 类似，只是性能很差，此类型几乎用不到。
- fanout：分发模式，把消费分发给所有订阅者。
- topic：匹配订阅模式，使用正则匹配到消息队列，能匹配到的都能接收到

*145. **RabbitMQ怎么实现延迟消息队列？**

延迟队列的实现有两种方式：

- 通过消息过期后进入死信交换器，再由交换器转发到延迟消费队列，实现延迟功能；
- 使用 RabbitMQ-delayed-message-exchange 插件实现延迟功能。

*146. **RabbitMQ 集群有什么用？**

- 高可用：某个服务器出现问题，整个 RabbitMQ 还可以继续使用；
- 高容量：集群可以承载更多的消息量。

*147. **RabbitMQ 节点的类型有哪些？**

- 磁盘节点：消息会存储到磁盘。
- 内存节点：消息都存储在内存中，重启服务器消息丢失，性能高于磁盘类型。

*148. **RabbitMQ 集群搭建需要注意哪些问题？**

- 各节点之间使用“--link”连接，此属性不能忽略。
- 各节点使用的 erlang cookie 值必须相同，此值相当于“秘钥”的功能，用于各节点的认证。
- 整个集群中必须包含一个磁盘节点

*149. **RabbitMQ 每个节点是其他节点的完整拷贝吗？为什么？**

不是，原因有以下两个：

- 存储空间的考虑：如果每个节点都拥有所有队列的完全拷贝，这样新增节点不但没有新增存储空间，反而增加了更多的冗余数据；
- 性能的考虑：如果每条消息都需要完整拷贝到每一个集群节点，那新增节点并没有提升处理消息的能力，最多是保持和单节点相同的性能甚至是更糟。

*150. **RabbitMQ 集群中唯一一个磁盘节点崩溃了会发生什么情况？**

如果唯一磁盘的磁盘节点崩溃了，不能进行以下操作：

- 不能创建队列
- 不能创建交换器
- 不能创建绑定
- 不能添加用户
- 不能更改权限
- 不能添加和删除集群节点

唯一磁盘节点崩溃了，集群是可以保持运行的，但你不能更改任何东西。

*151. **RabbitMQ 对集群节点停止顺序有要求吗？**

RabbitMQ 对集群的停止的顺序是有要求的，应该先关闭内存节点，最后再关闭磁盘节点。如果顺序恰好相反的话，可能会造成消息的丢失。

## 第十四部分 Kafka

*152. **kafka可以脱离 zookeeper 单独使用吗？为什么？**

kafka 不能脱离 zookeeper 单独使用，因为 kafka 使用 zookeeper 管理和协调 kafka 的节点服务器。

*153. **kafka有几种数据保留的策略？**

kafka 有两种数据保存策略：按照过期时间保留和按照存储的消息大小保留。

*154. **kafka 同时设置了 7 天和 10G 清除数据，到第五天的时候消息达到了 10G，这个时候 kafka 将如何处理？**

这个时候kafka会执行数据清除工作，时间和大小不论那个满足条件，都会清空数据。

*155. **什么情况会导致 kafka 运行变慢？**

- cpu性能瓶颈
- 磁盘读写瓶颈
- 网络瓶颈

*156. **使用 kafka 集群需要注意什么？**

- 集群的数量不是越多越好，最好不要超过7个，因为节点越多，消息复制需要的时间就越长，整个群组的吞吐量就越低。
- 集群数量最好是单数，因为超过一半故障集群就不能用了，设置为单数容错率更高。

## 第十五部分 Zookeeper

*157. **zookeeper 是什么？**

zookeeper 是一个分布式的，开放源码的分布式应用程序协调服务，是 google chubby 的开源实现，是 hadoop 和 hbase 的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

*158. **zookeeper 都有哪些功能？**

- 集群管理：监控节点存活状态、运行请求等。
- 主节点选举：主节点挂掉了之后可以从备用的节点开始新一轮选主，主节点选举说的就是这个选举的过程，使用 zookeeper 可以协助完成这个过程。
- 分布式锁：zookeeper 提供两种锁：独占锁、共享锁。独占锁即一次只能有一个线程使用资源，共享锁是读锁共享，读写互斥，即可以有多线线程同时读同一个资源，如果要使用写锁也只能有一个线程使用。zookeeper可以对分布式锁进行控制。
- 命名服务：在分布式系统中，通过使用命名服务，客户端应用能够根据指定名字来获取资源或服务的地址，提供者等信息。

*159. **zookeeper 有几种部署模式？**

zookeeper 有三种部署模式：

- 单机部署：一台集群上运行；
- 集群部署：多台集群运行；
- 伪集群部署：一台集群启动多个 zookeeper 实例运行。

*160. **zookeeper 怎么保证主从节点的状态同步？**

zookeeper 的核心是原子广播，这个机制保证了各个 server 之间的同步。实现这个机制的协议叫做 zab 协议。 zab 协议有两种模式，分别是恢复模式（选主）和广播模式（同步）。当服务启动或者在领导者崩溃后，zab 就进入了恢复模式，当领导者被选举出来，且大多数 server 完成了和 leader 的状态同步以后，恢复模式就结束了。状态同步保证了 leader 和 server 具有相同的系统状态。

*161. **集群中为什么要有主节点？**

在分布式环境中，有些业务逻辑只需要集群中的某一台机器进行执行，其他的机器可以共享这个结果，这样可以大大减少重复计算，提高性能，所以就需要主节点。

*162. **集群中有 3 台服务器，其中一个节点宕机，这个时候 zookeeper 还可以使用吗？**

可以继续使用，单数服务器只要没超过一半的服务器宕机就可以继续使用

*163. **说一下 zookeeper 的通知机制？**

客户端端会对某个 znode 建立一个 watcher 事件，当该 znode 发生变化时，这些客户端会收到 zookeeper 的通知，然后客户端可以根据 znode 变化来做出业务上的改变。

## 第十六部分 MySQL

*164. **数据库的三范式是什么？**

- 第一范式：强调的是列的原子性，即数据库表的每一列都是不可分割的原子数据项。
- 第二范式：属性完全依赖于主键（满足第一范式的前提下），即任意一个字段只依赖于表中的同一个字段。
- 第三范式：任何非主属性不依赖于其它非主属性（满足第二范式的前提下）。即不存在传递依赖。

165. 一张自增表里面总共有 7 条数据，删除了最后 2 条数据，重启 MySQL 数据库，又插入了一条数据，此时 id 是几？

- 数据库引擎如果是 MyISAM ，那 id 就是 8。
- 数据库引擎如果是 InnoDB，那 id 就是 6。

InnoDB 表只会把自增主键的最大 id 记录在内存中，所以重启之后会导致最大 id 丢失。

*166. **如何获取当前数据库版本？**

使用 select version() 获取当前 MySQL 数据库版本。

*167. **说一下 ACID 是什么？**

- Atomicity（原子性）：一个事务（transaction）中的所有操作，或者全部完成，或者全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被恢复（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。即，事务不可分割、不可约简。
- Consistency（一致性）：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设约束、触发器、级联回滚等。
- Isolation（隔离性）：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
- Durability（持久性）：事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。

*168. **char 和 varchar 的区别是什么？**

- char：固定长度类型，比如定义 char(10)，当你输入"abc"三个字符的时候，它们占的空间还是 10 个字节，其他 7 个是空字节。
- varchar(n)：可变长度，存储的值是每个值占用的字节再加上一个用来记录其长度的字节的长度。

char效率高，但占用空间，适用于存储固定长度的值。而从空间考虑，verchar则比较合适。

*169. **float 和 double 的区别是什么？**

- float 最多可以存储 8 位的十进制数，并在内存中占 4 字节。
- double 最可可以存储 16 位的十进制数，并在内存中占 8 字节

*170. **mysql 的内连接、左连接、右连接有什么区别？**

内连接是把匹配的关联数据显示出来；左连接是左边的表全部显示出来，右边的表显示出符合条件的数据；右连接正好相反。

*171. **MySQL 索引是怎么实现的？**

索引是满足某种特定查找算法的数据结构，而这些数据结构会以某种方式指向数据，从而实现高效查找数据。

具体来说 MySQL 中的索引，不同的数据引擎实现有所不同，但目前主流的数据库引擎的索引都是 B+ 树实现的，B+ 树的搜索效率，可以到达二分法的性能，找到数据区域之后就找到了完整的数据结构了，所有索引的性能也是更好的。

*172. **怎么验证 MySQL 的索引是否满足需求？**

使用 explain 查看 SQL 是如何执行查询语句的，从而分析你的索引是否满足需求。

explain 语法：explain select * from table where type=1。

*173. **说一下数据库的事务隔离？**

可用的配置值：READ-UNCOMMITTED、READ-COMMITTED、REPEATABLE-READ、SERIALIZABLE。

- READ-UNCOMMITTED：未提交读，最低隔离级别、事务未提交前，就可被其他事务读取（会出现幻读、脏读、不可重复读）。
- READ-COMMITTED：提交读，一个事务提交后才能被其他事务读取到（会造成幻读、不可重复读）。
- REPEATABLE-READ：可重复读，默认级别，保证多次读取同一个数据时，其值都和事务开始时候的内容是一致，禁止读取到别的事务未提交的数据（会造成幻读）。
- SERIALIZABLE：序列化，代价最高最可靠的隔离级别，该隔离级别能防止脏读、不可重复读、幻读

- 脏读 ：表示一个事务能够读取另一个事务中还未提交的数据。比如，某个事务尝试插入记录 A，此时该事务还未提交，然后另一个事务尝试读取到了记录 A。
- 不可重复读 ：是指在一个事务内，多次读同一数据。读到的数据不一致。
- 幻读 ：指同一个事务内多次查询返回的结果集不一样。比如同一个事务 A 第一次查询时候有 n 条记录，但是第二次同等条件下查询却有 n+1 条记录，这就好像产生了幻觉。发生幻读的原因也是另外一个事务新增或者删除或者修改了第一个事务结果集里面的数据，同一个记录的数据内容被修改了，所有数据行的记录就变多或者变少了。

*174. **说一下 MySQL 常用的引擎？**

- InnoDB 引擎：mysql 5.1 后默认的数据库引擎，提供了对数据库 acid 事务的支持，并且还提供了行级锁和外键的约束，它的设计的目标就是处理大数据容量的数据库系统。MySQL 运行的时候，InnoDB 会在内存中建立缓冲池，用于缓冲数据和索引。但是该引擎是不支持全文搜索，同时启动也比较的慢，它是不会保存表的行数的，所以当进行 select count(*) from table 指令的时候，需要进行扫描全表。由于锁的粒度小，写操作是不会锁定全表的,所以在并发度较高的场景下使用会提升效率的。
- MyIASM 引擎：不提供事务的支持，也不支持行级锁和外键。因此当执行插入和更新语句时，即执行写操作的时候需要锁定这个表，所以会导致效率会降低。不过和 InnoDB 不同的是，MyIASM 引擎是保存了表的行数，于是当进行 select count(*) from table 语句时，可以直接的读取已经保存的值而不需要进行扫描全表。所以，如果表的读操作远远多于写操作时，并且不需要事务的支持的，可以将 MyIASM 作为数据库引擎的首选。

*175. **说一下 MySQL 的行锁和表锁？**

MyISAM 只支持表锁，InnoDB 支持表锁和行锁，默认为行锁。

- 表级锁：开销小，加锁快，不会出现死锁。锁定粒度大，发生锁冲突的概率最高，并发量最低。
- 行级锁：开销大，加锁慢，会出现死锁。锁力度小，发生锁冲突的概率小，并发度最高。

*176. **说一下乐观锁和悲观锁？**

- 乐观锁：每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在提交更新的时候会判断一下在此期间别人有没有去更新这个数据。
- 悲观锁：每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻止，直到这个锁被释放。

*177. **MySQL 问题排查都有哪些手段？**

- 使用 show processlist 命令查看当前所有连接信息。
- 使用 explain 命令查询 SQL 语句执行计划。
- 开启慢查询日志，查看慢查询的 SQL。

*178. **如何做 MySQL 的性能优化？**

- 为搜索字段创建索引。
- 避免使用 select *，列出需要查询的字段。
- 垂直分割分表。
- 选择正确的存储引擎。

## 第十七部分 Redis

*179. **redis 是什么？都有哪些使用场景？**

Redis 是一个使用 C 语言开发的高速缓存数据库。

Redis 使用场景：

- 记录帖子点赞数、点击数、评论数；
- 缓存近期热帖；
- 缓存文章详情信息；
- 记录用户会话信息。

*180. **Redis 有哪些功能？**

- 数据缓存功能
- 分布式锁的功能
- 支持数据持久化
- 支持事务
- 支持消息队列

*181. **Redis 和 memcache 有什么区别？**

- 存储方式不同：memcache 把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小；Redis 有部份存在硬盘上，这样能保证数据的持久性。
- 数据支持类型：memcache 对数据类型支持相对简单；Redis 有复杂的数据类型。
- 使用底层模型不同：它们之间底层实现方式，以及与客户端之间通信的应用协议不一样，Redis 自己构建了 vm 机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求。
- value 值大小不同：Redis 最大可以达到 512mb；memcache 只有 1mb。

*182. **Redis 为什么是单线程的？**

因为 cpu 不是 Redis 的瓶颈，Redis 的瓶颈最有可能是机器内存或者网络带宽。既然单线程容易实现，而且 cpu 又不会成为瓶颈，那就顺理成章地采用单线程的方案了。

关于 Redis 的性能，官方网站也有，普通笔记本轻松处理每秒几十万的请求。

而且单线程并不代表就慢 nginx 和 nodejs 也都是高性能单线程的代表。

*183. **什么是缓存穿透？怎么解决？**

缓存穿透：指查询一个一定不存在的数据，由于缓存是不命中时需要从数据库查询，查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到数据库去查询，造成缓存穿透

解决方案：最简单粗暴的方法如果一个查询返回的数据为空（不管是数据不存在，还是系统故障），我们就把这个空结果进行缓存，但它的过期时间会很短，最长不超过五分钟。

*184. **Redis 支持的数据类型有哪些？**

Redis 支持的数据类型：string（字符串）、list（列表）、hash（散列表）、set（集合）、zset（有序集合）。

*185. **Redis 支持的 Java 客户端都有哪些？**

支持的 Java 客户端有 Redisson、jedis、lettuce 等。

*186. **jedis 和 Redisson 有哪些区别？**

- jedis：提供了比较全面的 Redis 命令的支持。
- Redisson：实现了分布式和可扩展的 Java 数据结构，与 jedis 相比 Redisson 的功能相对简单，不支持排序、事务、管道、分区等 Redis 特性

*187. **怎么保证缓存和数据库数据的一致性？**

合理设置缓存的过期时间。
新增、更改、删除数据库操作时同步更新 Redis，可以使用事物机制来保证数据的一致性。

*188. **Redis 持久化有几种方式？**

Redis 的持久化有两种方式，或者说有两种策略：

- RDB（Redis Database）：指定的时间间隔能对你的数据进行快照存储。
- AOF（Append Only File）：每一个收到的写命令都通过write函数追加到文件中。

*189. **Redis 怎么实现分布式锁？**

Redis 分布式锁其实就是在系统里面占一个“坑”，其他程序也要占“坑”的时候，占用成功了就可以继续执行，失败了就只能放弃或稍后重试。

占坑一般使用 setnx(set if not exists)指令，只允许被一个程序占有，使用完调用 del 释放锁。

*190. **Redis 分布式锁有什么缺陷？**

Redis 分布式锁不能解决超时的问题，分布式锁有一个超时时间，程序的执行如果超出了锁的超时时间就会出现问题。

*191. **Redis 如何做内存优化？**

尽量使用 Redis 的散列表，把相关的信息放到散列表里面存储，而不是把每个字段单独存储，这样可以有效的减少内存使用。比如将 Web 系统的用户对象，应该放到散列表里面再整体存储到 Redis，而不是把用户的姓名、年龄、密码、邮箱等字段分别设置 key 进行存储。

*192. **Redis 淘汰策略有哪些？**

- volatile-lru：从已设置过期时间的数据集（server.db[i]. expires）中挑选最近最少使用的数据淘汰。
- volatile-ttl：从已设置过期时间的数据集（server.db[i]. expires）中挑选将要过期的数据淘汰。
- volatile-random：从已设置过期时间的数据集（server.db[i]. expires）中任意选择数据淘汰。
- allkeys-lru：从数据集（server.db[i].dict）中挑选最近最少使用的数据淘汰。
- allkeys-random：从数据集（server.db[i].dict）中任意选择数据淘汰。
- no-enviction（驱逐）：禁止驱逐数据。

*193. **Redis 常见的性能问题有哪些？该如何解决？**

- 主服务器写内存快照，会阻塞主线程的工作，当快照比较大时对性能影响是非常大的，会间断性暂停服务，所以主服务器最好不要写内存快照。
- Redis主从复制的性能问题，为了主从复制的速度和连接的稳定性，主从库最好在同一个局域网内。

## 第十八部分 JVM

*194. **说一下 jvm 的主要组成部分？及其作用？**

- 类加载器（ClassLoader）
- 运行时数据区（Runtime Data Area）
- 执行引擎（Execution Engine）
- 本地库接口（Native Interface）

首先通过类加载器（ClassLoader）会把 Java 代码转换成字节码，运行时数据区（Runtime Data Area）再把字节码加载到内存中，而字节码文件只是 JVM 的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。

*195. **说一下 JVM 运行时数据区？**

Java 虚拟机规范规定的区域分为以下 5 个部分：

- 程序计数器（Program Counter Register）：当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成；
- Java 虚拟机栈（Java Virtual Machine Stacks）：用于存储局部变量表、操作数栈、动态链接、方法出口等信息；
- 本地方法栈（Native Method Stack）：与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的；
- Java 堆（Java Heap）：Java 虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的对象实例都在这里分配内存；
- 方法区（Methed Area）：用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。

*196. **说一下堆栈的区别？**

- 栈内存存储的是局部变量而堆内存存储的是实体；
- 栈内存的更新速度要快于堆内存，因为局部变量的生命周期很短；
- 栈内存存放的变量生命周期一旦结束就会被释放，而堆内存存放的实体会被垃圾回收机制不定时的回收。

*197. **队列和栈是什么？有什么区别？**

- 队列和栈都是被用来预存储数据的。
- 队列允许先进先出检索元素，但也有例外的情况，Deque 接口允许从两端检索元素。
- 栈和队列很相似，但它运行对元素进行后进先出进行检索。

*198. **什么是双亲委派模型？**

如果一个类加载器收到了类加载的请求，它首先不会自己去加载这个类，而是把这个请求委派给父类加载器去完成，每一层的类加载器都是如此，这样所有的加载请求都会被传送到顶层的启动类加载器中，只有当父加载无法完成加载请求（它的搜索范围中没找到所需的类）时，子加载器才会尝试去加载类。

*199. **说一下类装载的执行过程？**

类装载分为以下 5 个步骤：

- 加载：根据查找路径找到相应的 class 文件然后导入；
- 检查：检查加载的 class 文件的正确性；
- 准备：给类中的静态变量分配内存空间；
- 解析：虚拟机将常量池中的符号引用替换成直接引用的过程。符号引用就理解为一个标示，而在直接引用直接指向内存中的地址；
- 初始化：对静态变量和静态代码块执行初始化工作。

*200. **怎么判断对象是否可以被回收？**

对于一个对象而言，如果没有任何变量去引用它，那么该对象将不可能被程序访问，因此可以认为它是垃圾信息，可以被回收。

对于垃圾回收器而言，它使用有向图来记录和管理堆内存中的所有对象，通过这个有向图就可以识别哪些对象是“可达的”，哪些对象是“不可达”的，所有“不可达”的对象都是可以被回收的。

*201. **Java 中都有哪些引用类型？**

- 强引用：形如Object obj = new Object()这类的为强引用。
- 软引用：一些有用但并非必须，用软引用关联的对象，当系统将要发生OOM前，这些对象就会被回收。
- 弱引用：一些有用（程度比软引用更低）但是并非必需，用弱引用关联的对象，只能生存到下一次垃圾回收之前，GC发生时，不管内存够不够，都会被回收。
- 虚引用：任何时候可能被GC回收，就像没有引用一样

*202. **说一下 JVM 有哪些垃圾回收算法？**

- 标记-清除算法：标记无用对象，然后进行清除回收。缺点：效率不高，无法清除垃圾碎片。
- 标记-整理算法：标记无用对象，让所有存活的对象都向一端移动，然后直接清除掉端边界以外的内存。
- 复制算法：按照容量划分二个大小相等的内存区域，当一块用完的时候将活着的对象复制到另一块上，然后再把已使用的内存空间一次清理掉。缺点：内存使用率不高，只有原来的一半。
- 分代算法：根据对象存活周期的不同将内存划分为几块，一般是新生代和老年代，新生代基本采用复制算法，老年代采用标记整理算法。

*203. **说一下 JVM 有哪些垃圾回收器？**

- Serial：最早的单线程串行垃圾回收器。
- Serial Old：Serial 垃圾回收器的老年版本，同样也是单线程的，可以作为 CMS 垃圾回收器的备选预案。
- ParNew：是 Serial 的多线程版本。
- Parallel 和 ParNew 收集器类似是多线程的，但 Parallel 是吞吐量优先的收集器，可以牺牲等待时间换取系统的吞吐量。
- Parallel Old 是 Parallel 老生代版本，Parallel 使用的是复制的内存回收算法，Parallel Old 使用的是标记-整理的内存回收算法。
- CMS：一种以获得最短停顿时间为目标的收集器，非常适用 B/S 系统。
- G1：一种兼顾吞吐量和停顿时间的 GC 实现，是 JDK 9 以后的默认 GC 选项。

*204. **详细介绍一下 CMS 垃圾回收器？**

CMS 是英文 Concurrent Mark-Sweep 的简称，是以牺牲吞吐量为代价来获得最短回收停顿时间的垃圾回收器。对于要求服务器响应速度的应用上，这种垃圾回收器非常适合。在启动 JVM 的参数加上“-XX:+UseConcMarkSweepGC”来指定使用 CMS 垃圾回收器。

CMS 使用的是标记-清除的算法实现的，所以在 gc 的时候回产生大量的内存碎片，当剩余内存不能满足程序运行要求时，系统将会出现 Concurrent Mode Failure，临时 CMS 会采用 Serial Old 回收器进行垃圾清除，此时的性能将会被降低。

*205. **新生代垃圾回收器和老生代垃圾回收器都有哪些？有什么区别？**

- 新生代回收器：Serial、ParNew、Parallel Scavenge
- 老年代回收器：Serial Old、Parallel Old、CMS
- 整堆回收器：G1

新生代垃圾回收器一般采用的是复制算法，复制算法的优点是效率高，缺点是内存利用率低；老年代回收器一般采用的是标记-整理的算法进行垃圾回收。

*206. **简述分代垃圾回收器是怎么工作的？**

分代回收器有两个分区：老生代和新生代，新生代默认的空间占比总空间的 1/3，老生代的默认占比是 2/3。

新生代使用的是复制算法，新生代里有 3 个分区：Eden、To Survivor、From Survivor，它们的默认占比是 8:1:1，它的执行流程如下：

- 把 Eden + From Survivor 存活的对象放入 To Survivor 区；
- 清空 Eden 和 From Survivor 分区；
- From Survivor 和 To Survivor 分区交换，From Survivor 变 To Survivor，To Survivor 变 From Survivor。

每次在 From Survivor 到 To Survivor 移动时都存活的对象，年龄就 +1，当年龄到达 15（默认配置是 15）时，升级为老生代。大对象也会直接进入老生代。

老生代当空间占用到达某个值之后就会触发全局垃圾收回，一般使用标记整理的执行算法。以上这些循环往复就构成了整个分代垃圾回收的整体执行流程。

*207. **说一下 JVM 调优的工具？**

JDK 自带了很多监控工具，都位于 JDK 的 bin 目录下，其中最常用的是 jconsole 和 jvisualvm 这两款视图监控工具。

- jconsole：用于对 JVM 中的内存、线程和类等进行监控；
- jvisualvm：JDK自带的全能分析工具，可以分析：内存快照、线程快照、程序死锁、监控内存的变化、gc 变化等。

*208. **常用的 JVM 调优的参数都有哪些？**

- -Xms2g：初始化推大小为 2g；
- -Xmx2g：堆最大内存为 2g；
- -XX:NewRatio=4：设置年轻的和老年代的内存比例为 1:4；
- -XX:SurvivorRatio=8：设置新生代 Eden 和 Survivor 比例为 8:2；
- –XX:+UseParNewGC：指定使用 ParNew + Serial Old 垃圾回收器组合；
- -XX:+UseParallelOldGC：指定使用 ParNew + ParNew Old 垃圾回收器组合；
- -XX:+UseConcMarkSweepGC：指定使用 CMS + Serial Old 垃圾回收器组合；
- -XX:+PrintGC：开启打印 gc 信息；
- -XX:+PrintGCDetails：打印 gc 详细信息。

## 其他

*1. **三种获得1970年1月1日0时0分0秒到现在的毫秒数的方法。**

```java
public static void main(String[] args) {
    System.out.println(System.currentTimeMillis());
    System.out.println(new Date().getTime());
    System.out.println(Calendar.getInstance().getTimeInMillis());
}
```

*2. **switch可以作用的类型？**

String、byte、short、char、int或者其对应的封装类以及Enum类型。

*3. **Arrays.asList()的size问题。**

```java
int[] test = new int[] {1, 2, 3, 4};
List list = Arrays.asList(test);
System.out.println(list.size());
```

结果输出是1。因为test的类型为int[]，则此时得到的List的类型为List<int[]>，即整个数组被当作了一个元素。

*4. **execute，executeQuery，executeUpdate的区别是什么？ **

- Statement的execute(String query)方法用来执行任意的SQL查询，如果查询的结果是一个ResultSet，这个方法就返回true。如果结果不是ResultSet，比如insert或者update查询，它就会返回false。我们可以通过它的getResultSet方法来获取ResultSet，或者通过getUpdateCount()方法来获取更新的记录条数。 
- Statement的executeQuery(String query)接口用来执行select查询，并且返回ResultSet。即使查询不到记录返回的ResultSet也不会为null。我们通常使用executeQuery来执行查询语句，这样的话如果传进来的是insert或者update语句的话，它会抛出错误信息为 “executeQuery method can not be used for update”的java.util.SQLException。
- Statement的executeUpdate(String query)方法用来执行insert或者update/delete（DML）语句，或者 什么也不返回，对于DDL语句，返回值是int类型，如果是DML语句的话，它就是更新的条数，如果是DDL的话，就返回0。 
只有当你不确定是什么语句的时候才应该使用execute()方法，否则应该使用executeQuery或者executeUpdate方法。