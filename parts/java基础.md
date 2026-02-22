- [首页](../README.md "Java Interview Docs")
1. Java开发设计七大原则
    - 单一职责：一个类只负责一项职责，提高类的内聚性，减少类的耦合性
    - 开闭原则：对扩展开放，对修改关闭。通常通过抽象（接口或抽象类）和多态来实现
    - 里氏替换：子类必须能够完全替换父类。子类可以扩展父类的功能，但不能改变父类原有的功能
    - 接口隔离：将一个臃肿的接口拆分成更小，更具体的接口
    - 依赖倒置：使用抽象和接口来定义依赖关系，而不是使用具体的实现类。高层模块不应关心低层模块（如数据库访问、网络请求）的具体实现细节
    - 合成复用：尽量使用对象组合/聚合，而不是继承来达到复用的目的
    - 迪米特法则：一个对象应该对其它对象有最少的了解，减少对象间的耦合，让每个对象尽可能独立
2. 8大基本数据类型及占用字节数
    - byte （1）
    - short（2）
    - char（2）
    - int（4）
    - float（4）
    - double（8）
    - long（8）
    - boolean
3. 静态变量有什么作用？
   - 可以被类的所有实例共享，无论一个类创建了多少个对象，它们都共享同一份静态变量。
   - 也就是说，静态变量只会被分配一次内存，即使创建多个对象，这样可以节省内存。
4. 静态方法为什么不能调用非静态成员?
   - 静态方法是属于类的，在类加载的时候就会分配内存，可以通过类名直接访问。
   - 而非静态成员属于实例对象，只有在对象实例化之后才存在，需要通过类的实例对象去访问。
5. 重载和重写有什么区别？
   - 重载发生在同一个类中（或者父类和子类之间），方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同。
   - 重写方法名、参数列表必须相同，子类方法返回值类型应比父类方法返回值类型更小或相等，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。如果父类方法访问修饰符为 private/final/static 则子类就不能重写该方法，但是被 static 修饰的方法能够被再次声明。构造方法无法被重写
6. 接口和抽象类有什么共同点和区别？
   - 抽象类可以包含抽象方法和非抽象方法。抽象方法没有方法体，必须在子类中实现。非抽象方法有具体实现，可以直接在抽象类中使用或在子类中重写。
   - 一个类只能继承一个类（包括抽象类），因为 Java 不支持多继承。但一个类可以实现多个接口，一个接口也可以继承多个其他接口。
7. 深拷贝和浅拷贝区别了解吗？什么是引用拷贝？
   - 引用拷贝就是两个不同的引用指向同一个对象
   - 深拷贝会完全复制整个对象，包括这个对象所包含的内部对象
8. 为什么重写 equals() 时必须重写 hashCode() 方法？
   - 如果重写 equals() 时没有重写 hashCode() 方法的话就可能会导致 equals 方法判断是相等的两个对象，hashCode 值却不相等。
9. String、StringBuffer、StringBuilder 的区别？
   - String 是不可变的，StringBuilder 非线程安全，相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险
10. Java 9 为何要将 String 的底层实现由 char[] 改成了 byte[] ?
    - 新版的 String 其实支持两个编码方案：Latin-1 和 UTF-16。如果字符串中包含的汉字没有超过 Latin-1 可表示范围内的字符，那就会使用 Latin-1 作为编码方案。
    - Latin-1 编码方案下，byte 占一个字节(8 位)，char 占用 2 个字节（16），byte 相较 char 节省一半的内存空间。JDK 官方就说了绝大部分字符串对象只包含 Latin-1 可表示的字符
11. String s1 = new String("abc");这句话创建了几个字符串对象？
    - 会创建 1 或 2 个字符串对象。如果字符串常量池中不存在字符串对象“abc”的引用，那么它会在堆上创建两个字符串对象，其中一个字符串对象的引用会被保存在字符串常量池中
12. String的intern 方法有什么作用?
    - String.intern() 是一个 native（本地）方法，其作用是将指定的字符串对象的引用保存在字符串常量池中，可以简单分为两种情况：
       - 如果字符串常量池中保存了对应的字符串对象的引用，就直接返回该引用。
       - 如果字符串常量池中没有保存了对应的字符串对象的引用，那就在常量池中创建一个指向该字符串对象的引用并返回
13. java异常体系
     ```
     Throwable
     ├── Error（OutOfMemoryError、StackOverflowError）
     └── Exception
         ├── RuntimeException（NullPointerException、ClassCastException、ArrayIndexOutOfBoundsException、ArithmeticException）
         └── Checked Exception（IOException、ClassNotFoundException、SQLException）
     ```
14. 什么是反射
    - 程序运行时可以获取任意一个类的所有属性和方法，你还可以调用这些方法和属性。
15. 如果有些字段不想进行序列化怎么办？
    - transient 关键字：阻止实例中那些用此关键字修饰的的变量序列化；
    - 当对象被反序列化时，被 transient 修饰的变量值不会被持久化和恢复。
    - static 变量不会被序列化
16. 什么是语法糖？
    - Java 中的 for-each 就是一个常用的语法糖，其原理其实就是基于普通的 for 循环和迭代器。
    - Java 中真正支持语法糖的是 Java 编译器而不是 JVM。
    - Java 中最常用的语法糖主要有泛型、自动拆装箱、变长参数、枚举、内部类、增强 for 循环、try-with-resources 语法、lambda 表达式等。
17. Java 是值传递还是引用传递？
    - 值传递
      - 如果参数是基本类型的话，很简单，传递的就是基本类型的字面量值的拷贝，会创建副本。
      - 如果参数是引用类型，传递的就是实参所引用的对象在堆中地址值的拷贝，同样也会创建副本。
18. HashSet、LinkedHashSet 和 TreeSet 三者的异同
    - HashSet 的底层数据结构是哈希表（基于 HashMap 实现）。LinkedHashSet 的底层数据结构是链表和哈希表，元素的插入和取出顺序满足 FIFO。TreeSet 底层数据结构是红黑树，元素是有序的，排序的方式有自然排序和定制排序。
    - HashSet 用于不需要保证元素插入和取出顺序的场景，LinkedHashSet 用于保证元素的插入和取出顺序满足 FIFO 的场景，TreeSet 用于支持对元素自定义排序规则的场景。
19. HashMap 的长度是 2 的幂次方的原因
    - 位运算效率更高：位运算(&)比取余运算(%)更高效。当长度为 2 的幂次方时，hash % length 等价于 hash & (length - 1)。
    - 可以更好地保证哈希值的均匀分布：扩容之后，在旧数组元素 hash 值比较均匀的情况下，新数组元素也会被分配的比较均匀，最好的情况是会有一半在新数组的前半部分，一半在新数组后半部分。
    - 扩容机制变得简单和高效：扩容后只需检查哈希值高位的变化来决定元素的新位置，要么位置不变（高位为 0），要么就是移动到新位置（高位为 1，原索引位置+原容量）。
20. JDK 1.7 和 JDK 1.8 的 ConcurrentHashMap 实现有什么不同？
    - JDK 1.7 采用 Segment 分段锁来保证安全， Segment 是继承自 ReentrantLock。
    - JDK1.8 放弃了 Segment 分段锁的设计，采用 Node + CAS + synchronized 保证线程安全，锁粒度更细，synchronized 只锁定当前链表或红黑二叉树的首节点。
21. HashMap 底层数据结构
    - 在JDK1.7之前是由数组+链表组成的，1.8之后又加入了红黑树；链表长度小于8的时候，发生Hash冲突后会增加链表的长度，当链表长度大于8的时候，会先判读数组的容量，如果容量小于64会先扩容（原因是数组容量越小，越容易发生碰撞，因此当容量过小的时候，首先要考虑的是扩容），如果容量大于64，则会将链表转化成红黑树以提升效率
    - hashMap 的容量是2的n次幂，无论在初始化的时候传入的初始容量是多少，最终都会转化成2的n次幂，这样做的原因是为了在取模运算的时候可以使用&运算符，而不是%取余，可以极大的提上效率，同时也降低hash冲突
22. HashMap在JDK1.8为什么改用使用尾插法
    - 因为 1.7头插法扩容时，头插法会使链表发生反转，多线程环境下会产生环
    - A 线程在插入节点 B，B 线程也在插入，遇到容量不够开始扩容，重新 hash，放置元素，采用头插法，后遍历到的 B 节点放入了头部，这样形成了环
23. ConcurrentHashMap 为什么 key 和 value 不能为 null？
    - 多线程环境下，存在一个线程操作该 ConcurrentHashMap 时，其他的线程将该 ConcurrentHashMap 修改的情况，所以无法通过 containsKey(key) 来判断否存在这个键值对，也就没办法解决二义性问题了。
    - 与此形成对比的是，HashMap 可以存储 null 的 key 和 value，但 null 作为键只能有一个，null 作为值可以有多个。如果传入 null 作为参数，就会返回 hash 值为 0 的位置的值。
    - 单线程环境下，不存在一个线程操作该 HashMap 时，其他的线程将该 HashMap 修改的情况，所以可以通过 contains(key)来做判断是否存在这个键值对，从而做相应的处理，也就不存在二义性问题。
24. ConcurrentHashMap 能保证复合操作的原子性吗？
    - 不能，ConcurrentHashMap 提供了一些原子性的复合操作，如 putIfAbsent、compute、computeIfAbsent 、computeIfPresent、merge等
25. PermGen和Metaspace的区别
    - PerGen是JVM 内部（HotSpot 虚拟机堆外的一块固定区域），固定大小，容易 OOM；包含字符串常量池
    - Metaspace使用系统本地内存，大小可自动扩展，很少发生OOM；将字符串常量池移到了堆里面
26. Thread#sleep() 方法和 Object#wait() 方法对比
    - sleep() 方法没有释放锁，而 wait() 方法释放了锁。
    - wait() 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 notify()或者 notifyAll() 方法。
    - sleep()方法执行完成后，线程会自动苏醒，或者也可以使用 wait(long timeout) 超时后线程会自动苏醒。
27. 如何预防和避免线程死锁?
    - 一次性申请所有的资源。
    - 占用部分资源的线程进一步申请其他资源时，如果申请不到，可以主动释放它占有的资源。
    - 保证申请资源的顺序性。
28. volatile 关键字
    - 在 Java 中，volatile 关键字可以保证变量的可见性，但不能保证对变量的操作是原子性的。
    - 如果我们将变量声明为 volatile ，这就指示 JVM，这个变量是共享且不稳定的，每次使用它都到主存中进行读取。
    - 它最原始的意义就是禁用 CPU 缓存。
29. 如何禁止指令重排序？
    - 在 Java 中，volatile 关键字除了可以保证变量的可见性，还有一个重要的作用就是防止 JVM 的指令重排序。 
    - 如果我们将变量声明为 volatile，在对这个变量进行读写操作的时候，会通过插入特定的 内存屏障 的方式来禁止指令重排序
30. 单例模式为什么要使用volatile关键字？
    - 为了禁止指令的重排序。创建一个新的实例，不是一个原子操作，实际上有三步。
      - 第一步，申请一块内存区域。
      - 第二步，对象初始化。
      - 第三步，将变量指向这块内存区域。
    - 如果不用volatile关键字，第三步和第二步的顺序可能会颠倒。当一个线程已经将变量指向了内存区域，但是还没有初始化，而这块内存区域此时不为空。另一个线程此时如果判断变量不为空，直接返回，直接返回获取实例。此时，变量还没有初始化，如果使用就会出问题报错。
31. 什么是悲观锁？
    - 悲观锁总是假设最坏的情况，认为共享资源每次被访问的时候就会出现问题(比如共享数据被修改)，所以每次在获取资源操作的时候都会上锁，这样其他线程想拿到这个资源就会阻塞直到锁被上一个持有者释放。也就是说，共享资源每次只给一个线程使用，其它线程阻塞，用完后再把资源转让给其它线程。会造成死锁问题。
    - 像 Java 中synchronized和ReentrantLock等独占锁就是悲观锁思想的实现。
32. 什么是乐观锁？
    - 乐观锁总是假设最好的情况，认为共享资源每次被访问的时候不会出现问题，线程可以不停地执行，无需加锁也无需等待，只是在提交修改的时候去验证对应的资源（也就是数据）是否被其它线程修改了（具体方法可以使用版本号机制或 CAS 算法）。不会造成死锁问题。
    - 在 Java 中java.util.concurrent.atomic包下面的原子变量类（比如AtomicInteger、LongAdder）就是使用了乐观锁的一种实现方式 CAS 实现的。
33. Java 中 CAS 是如何实现的？
    - sun.misc包下的Unsafe类提供了compareAndSwapObject、compareAndSwapInt、compareAndSwapLong方法来实现的对Object、int、long类型的 CAS 操作。
    - Java 语言并没有直接实现 CAS，CAS 相关的实现是通过 C++ 内联汇编的形式实现的（JNI 调用）。因此， CAS 的具体实现和操作系统以及 CPU 都有关系。
34. CAS 算法存在哪些问题？
    - ABA 问题的解决思路是在变量前面追加上版本号或者时间戳
    - 只能保证一个共享变量的原子操作：CAS 操作仅能对单个共享变量有效。当需要操作多个共享变量时，CAS 就显得无能为力。
      - 从 JDK 1.5 开始，Java 提供了AtomicReference类，这使得我们能够保证引用对象之间的原子性。
      - 通过将多个变量封装在一个对象中，我们可以使用AtomicReference来执行 CAS 操作。
      - 除了 AtomicReference 这种方式之外，还可以利用加锁来保证。
35. synchronized 底层原理了解吗？
    - synchronized 同步语句块的实现使用的是 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。
    - 在 Java 虚拟机(HotSpot)中，Monitor 是基于 C++实现的，由ObjectMonitoropen in new window实现的。每个对象中都内置了一个 ObjectMonitor对象。
    - synchronized 修饰的方法并没有 monitorenter 指令和 monitorexit 指令，取得代之的确实是 ACC_SYNCHRONIZED 标识，该标识指明了该方法是一个同步方法。不过两者的本质都是对对象监视器 monitor 的获取。
36. synchronized 和 volatile 有什么区别？
    - volatile 关键字只能用于变量而 synchronized 关键字可以修饰方法以及代码块
    - volatile 关键字能保证数据的可见性，但不能保证数据的原子性。synchronized 关键字两者都能保证。
    - volatile关键字主要用于解决变量在多个线程之间的可见性，而 synchronized 关键字解决的是多个线程之间访问资源的同步性
37. ReentrantLock 实现原理？
    - ReentrantLock 里面有一个内部类 Sync，Sync 继承 AQS，添加锁和释放锁的大部分操作实际上都是在 Sync 中实现的。
    - ReentrantLock 默认使用非公平锁，也可以通过构造器来显式的指定使用公平锁。
38. 公平锁和非公平锁有什么区别？
    - 公平锁 : 锁被释放之后，先申请的线程先得到锁。性能较差一些，因为公平锁为了保证时间上的绝对顺序，上下文切换更频繁。
    - 非公平锁：锁被释放之后，后申请的线程可能会先获取到锁，是随机或者按照其他优先级排序的。性能更好，但可能会导致某些线程永远无法获取到锁。
39. synchronized 和 ReentrantLock 有什么区别？
    - 两者都是可重入锁 也叫递归锁，指的是线程可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果是不可重入锁的话，就会造成死锁。JDK 提供的所有现成的 Lock 实现类，包括 synchronized 关键字锁都是可重入的。
    - synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API
    - ReentrantLock 比 synchronized 增加了一些高级功能
      - 等待可中断 : ReentrantLock提供了一种能够中断等待锁的线程的机制，通过 lock.lockInterruptibly() 来实现这个机制。也就是说正在等待的线程可以选择放弃等待，改为处理其他事情。
      - 可实现公平锁 : ReentrantLock可以指定是公平锁还是非公平锁。而synchronized只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁。ReentrantLock默认情况是非公平的，可以通过 ReentrantLock类的ReentrantLock(boolean fair)构造方法来指定是否是公平的。
40. JVM常用参数调优
    - 显式指定堆内存–Xms和-Xmx
    - 显式新生代内存 -Xmn
      - 官方从 JDK9 开始弱化 -Xmn，推荐使用 -XX:MaxGCPauseMillis 作为主要调优参数。
      - G1 自动根据暂停目标动态调整 Young/Old 比例（自动调优）
      - 人为固定新生代可能 反而降低性能
    - Xss
      - 栈越大 → 单线程可用栈深度越深，但会占用更多内存
      - 栈越小 → 更容易 StackOverflowError
      - 一般保持 1m（默认值）
      - 使用大量线程（>5000）时可以降低到 512k
    - Metaspace
      - 对于64位JVM来说，元空间的默认初始大小是20.75MB（JDK21 依然如此），由于调整元空间的大小需要Full GC，这是非常昂贵的操作，如果在应用启动或者运行时发生大量Full GC，通常都是由于元空间发生了大小调整
      - 基于这种情况，一般建议在JVM参数中将MetaspaceSize和MaxMetaspaceSize设置成一样的值，并设置得比初始值要大
    - 垃圾收集器
      - 指定使用G1或者ZGC
      - MaxGCPauseMillis
    - 处理 OOM 相关
      - 可以用jmap转储堆文件
      - HeapDumpOnOutOfMemoryError 指示 JVM 在遇到 OutOfMemoryError 错误时将 heap 转储到物理文件中。
      - HeapDumpPath 表示要写入文件的路径; 可以给出任何文件名; 但是，如果 JVM 在名称中找到一个 <pid> 标记，则当前进程的进程 id 将附加到文件名中，并使用.hprof格式
      - OnOutOfMemoryError="sh restart.sh"用于发出紧急命令，以便在内存不足的情况下执行; 例如，如果我们想在内存不足时重启服务器（k8s无效，容器在 OOM 时会强制杀进程，容器直接被干掉，进入 CrashLoopBackOff，再由 K8s 重启）
      - Dominator Tree 分析，找到占用最大内存的对象排查
41. Metaspace的NonClass和Class分别指什么
    - Class → 纯类元数据（字段、方法、继承关系等）
    - NonClass → JVM 为支持类运行生成的 native / runtime 数据（动态代理、反射、Lambda、JIT 编译信息、运行时常量池）
42. JVM内存结构
    - JDK 1.8  
    ![java内存区域.png](../images/java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F.png)
     ```
    ├── 线程共享区域
    │   ├── 堆（Heap）：存放对象实例和数组
    │   │   └── 字符串常量池（String Constant Pool）
    │   └── 方法区（Method Area）：存储已被虚拟机加载的类信息、常量、静态变量等。JDK7之前：永久代（PermGen），JDK8+：元空间（Metaspace），使用本地内存
    │       └── 运行时常量池（Runtime Constant Pool）：类文件中常量池表的运行时表示，存放符号引用（类名、字段名、方法名）
    └── 线程私有区域
        ├── 程序计数器（Program Counter Register）：当前线程所执行的字节码的行号指示器，为了线程切换后能恢复到正确的执行位置，每条线程都需要有一个独立的程序计数器
        ├── 虚拟机栈（JVM Stack）：方法调用的数据需要通过栈进行传递，每一次方法调用都会有一个对应的栈帧被压入栈中，每一个方法调用结束后，都会有一个栈帧被弹出。栈由一个个栈帧（Stack Frame）组成
        └── 本地方法栈（Native Method Stack）：Native方法
     ```
     ```
     栈帧（Stack Frame）
     ├── 局部变量表（Local Variables）：主要存放了编译期可知的各种数据类型（boolean、byte、char、short、int、float、long、double）、对象引用（reference 类型，它不同于对象本身，可能是一个指向对象起始地址的引用指针，也可能是指向一个代表对象的句柄或其他与此对象相关的位置）
     ├── 操作数栈（Operand Stack）
     ├── 动态链接（Dynamic Linking）
     └── 方法返回地址（Return Address）
     ```
    - 大部分情况，对象都会首先在 Eden 区域分配，在一次新生代垃圾回收后，如果对象还存活，则会进入 S0 或者 S1，并且对象的年龄还会加 1(Eden 区->Survivor 区后对象的初始年龄变为 1)，当它的年龄增加到一定程度（默认为 15 岁），就会被晋升到老年代中。对象晋升到老年代的年龄阈值，可以通过参数 -XX:MaxTenuringThreshold 来设置。
      - Hotspot 遍历所有对象时，按照年龄从小到大对其所占用的大小进行累加，当累加到某个年龄时，所累加的大小超过了 Survivor 区的一半，则取这个年龄和 MaxTenuringThreshold 中更小的一个值，作为新的晋升年龄阈值”
43. 方法区和永久代以及元空间的关系
    - 方法区和永久代以及元空间的关系很像 Java 中接口和类的关系，类实现了接口，这里的类就可以看作是永久代和元空间，接口可以看作是方法区。
    - 永久代以及元空间是 HotSpot 虚拟机对虚拟机规范中方法区的两种实现方式。永久代是 JDK 1.8 之前的方法区实现，JDK 1.8 及以后方法区的实现变成了元空间。
44. JDK 1.8 为什么要将字符串常量池移动到堆中？
     - 主要是因为永久代（方法区实现）的 GC 回收效率太低，只有在整堆收集 (Full GC)的时候才会被执行 GC。Java 程序中通常会有大量的被创建的字符串等待回收，将字符串常量池放到堆中，能够更高效及时地回收字符串内存
45. 内存分配和回收原则
    - 对象优先在 Eden 区分配
    - 大对象直接进入老年代
    - 长期存活的对象将进入老年代
46. 死亡对象判断方法
    - 引用计数法（这个方法实现简单，效率高，但是目前主流的虚拟机中并没有选择这个算法来管理内存，其最主要的原因是它很难解决对象之间循环引用的问题。）
    - 可达性分析算法：这个算法的基本思想就是通过一系列的称为 “GC Roots” 的对象作为起点，从这些节点开始向下搜索，节点所走过的路径称为引用链，当一个对象到 GC Roots 没有任何引用链相连的话，则证明此对象是不可用的，需要被回收。
47. GC Roots
    - 虚拟机栈（栈帧）中的引用
    - 本地方法栈（Native Stack）的引用
    - 方法区中静态变量引用的对象
    - 方法区中常量引用的对象
    - 被 synchronized 锁定的对象
    - JVM 自身持有的系统级引用
48. 类加载过程
    - 加载（Loading）：把类的二进制数据读入内存，并创建 Class 对象
    - 验证（Verification）：文件格式验证、字节码语义验证、符号引用验证、元数据验证
    - 准备（Preparation）：给类变量（static） 分配内存并设默认值（零值）
    - 解析（Resolution）：将常量池内的符号引用替换为直接引用（如把类名转换为内存地址），也就是得到类或者字段、方法在内存中的指针或者偏移量。
    - 初始化（Initialization）：执行类构造方法 <clinit>，真正给 static 变量赋值
49. Class.forName 和 .loadClass 方法的区别
    - Class.forName()会触发类的初始化，包括执行类的静态代码块和静态字段的初始化
    - loadClass()不会触发类的初始化
50. 垃圾回收器
     ```
         JDK1.2  JDK5      JDK6       JDK7       JDK8        JDK9-11        JDK12       JDK15    JDK17      JDK21
        |       |          |          |          |            |             |           |         |           |
        |----> Serial GC---|----------|----------|------------|-------------|-----------|---------|----------|
        |----> Parallel GC----------> 改进持续优化 ------------------默认GC(9-17)-------------------|
        |----> CMS GC (JDK5, Deprecated in 9, Removed in 14)
        |----> G1 GC (JDK7u4, Default since JDK9)
        |----> ZGC (JDK11 实验) → JDK15 正式稳定 → 强化至 JDK21
        |----> Shenandoah (JDK12 实验 → JDK15 正式)
     ```
     - Serial GC（单线程串行 GC）：新生代采用标记-复制算法，老年代采用标记-整理算法，用于小型应用或容器内存极低场景（如 < 2GB）
     - Parallel GC（JDK1.8 默认收集器）：新生代并行复制，老年代并行标记整理，关注点是吞吐量（高效率的利用 CPU）
     - CMS：第一款真正意义上的并发收集器，它第一次实现了让垃圾收集线程与用户线程（基本上）同时工作
         - CMS 收集器是一种 “标记-清除”算法实现的，它的运作过程相比于前面几种垃圾收集器来说更加复杂一些。整个过程分为四个步骤：
           - 初始标记： 暂停所有的其他线程，并记录下直接与 root 相连的对象，速度很快 ；
           - 并发标记： 同时开启 GC 和用户线程，用一个闭包结构去记录可达对象。但在这个阶段结束，这个闭包结构并不能保证包含当前所有的可达对象。因为用户线程可能会不断的更新引用域，所以 GC 线程无法保证可达性分析的实时性。所以这个算法里会跟踪记录这些发生引用更新的地方。
           - 重新标记： 重新标记阶段就是为了修正并发标记期间因为用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段的时间稍长，远远比并发标记阶段时间短
           - 并发清除： 开启用户线程，同时 GC 线程开始对未标记的区域做清扫。
           - 它有下面三个明显的缺点：对 CPU 资源敏感；无法处理浮动垃圾；它使用的回收算法-“标记-清除”算法会导致收集结束时会有大量空间碎片产生。CMS 垃圾回收器在 Java 9 中已经被标记为过时(deprecated)，并在 Java 14 中被移除。
     - G1 GC（JDK 9~JDK17 默认 GC）：可预测的停顿：这是 G1 相对于 CMS 的另一个大优势，降低停顿时间是 G1 和 CMS 共同的关注点，但 G1 除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为 M 毫秒的时间片段内，消耗在垃圾收集上的时间不得超过 N 毫秒
         - 基于Region（分区）而非经典新生代/老年代物理分区
         - G1 收集器在后台维护了一个优先列表，每次根据允许的收集时间，优先选择回收价值最大的 Region(这也就是它的名字 Garbage-First 的由来) 。这种使用 Region 划分内存空间以及有优先级的区域回收方式，保证了 G1 收集器在有限时间内可以尽可能高的收集效率（把内存化整为零）。
     - ZGC：停顿时间极短（通常 < 1 ms），对大堆机器（16G、32G、128G）效果极好（几十 GB ~ TB，官方支持 16TB）
         - 指针染色+加载屏障是 ZGC 能让对象同时被访问 + 被搬迁 + 引用自动修正 的关键。
         - ZGC 迁移对象的方式：
             - 在另一个地址空间（new address view）为对象分配新位置
             - 将对象复制过去（并发）
             - 标记旧对象的指针为“已搬迁”
         - Colored Pointers（指针染色）：ZGC 在 对象引用的 64 位指针 中隐藏了几位“元信息”（metadata）
             - 标记是否已经被访问（Marked）
             - 标记对象是否在搬迁中（Relocation）
             - 指向对应的元数据页（Metadata Page）
             - 判断引用是否需要“修正”（remapping）
         - Load Barrier（加载屏障）：当访问对象时，通过加载屏障判断
             - 如果引用“旧地址”，自动修正成“新地址”
             - 如果对象正在迁移，也能安全等待或修正
             - 所有指针都通过 Load Barrier 自动修复指向最新地址
         - 虚拟内存：用“虚拟地址空间”模拟一个比物理内存更大的抽象的内存世界，ZGC 预定了一大段虚拟地址空间作为 Java heap，但不实际分配物理内存
51. 什么是"三色标记"？
    - 白色：表示对象尚未被垃圾回收器访问过
    - 黑色：表示对象已经被垃圾回收器访问过，且这个对象的所有引用都已经扫描过
    - 灰色：表示对象已经被垃圾回收器访问过，但这个对象至少存在一个引用还没有被扫描过
52. 并发标记要解决什么问题？并发标记带来了什么问题？如何解决并发扫描时对象消失问题？
    - "标记"过程的耗时是会随着java堆里面存储的对象增加而增加的，并发标记就是要消减这一部分的停顿时间。那就是让垃圾回收器和用户线程同时运行，并发工作。
    - 带来的问题：
      - 一种是把原本消亡的对象错误的标记为存活，这不是好事，但是其实是可以容忍的，只不过产生了一点逃过本次回收的浮动垃圾而已，下次清理就可以。（产生浮动垃圾）
      - 一种是把原本存活的对象错误的标记为已消亡，这就是非常严重的后果了，一个程序还需要使用的对象被回收了，那程序肯定会因此发生错误。
    - 解决方法：
      - 增量更新-黑色对象一旦插入了指向白色对象的引用之后，它就变回了灰色对象，等并发扫描结束之后会重新补扫一次它
      - 原始快照-无论引用关系删除与否，都会按照刚刚开始扫描那一刻的对象图快照开进行搜索
53. G1 的压缩为什么会停顿？ G1 在清理 Region 时，需要：
     - 将存活对象复制到新 Region
     - 修改对它的所有引用（Fix-up）
     - 这个步骤必须 STW，否则引用会乱套。所以 G1 的一次 GC 停顿往往在 10~50ms。
54. Java集合概览
    ```
    ├── Collection (接口)
    │   ├── List（有序）
    │   │   ├── ArrayList
    │   │   ├── LinkedList
    │   │   └── CopyOnWriteArrayList（并发）
    │   │
    │   ├── Set（不重复）
    │   │   ├── HashSet
    │   │   ├── LinkedHashSet（保持插入顺序）
    │   │   ├── TreeSet（红黑树、有序）
    │   │   └── CopyOnWriteArraySet（并发）
    │   │
    │   └── Queue（队列）
    │       ├── Deque（双端队列）
    │       │   ├── ArrayDeque（推荐的栈与队列实现，替代Stack，非线程安全）
    │       │   └── LinkedList
    │       ├── PriorityQueue（最小堆）
    │       └── BlockingQueue（并发：生产者/消费者）
    │           ├── ArrayBlockingQueue（有界）
    │           ├── LinkedBlockingQueue（无界）
    │           ├── PriorityBlockingQueue
    │           └── DelayQueue
    │
    └── Map（键值映射）
        ├── HashMap
        │   └── LinkedHashMap（可按访问/插入顺序）
        ├── ConcurrentHashMap（高性能并发 Map）
        ├── TreeMap（红黑树→有序）
        └── WeakHashMap（弱引用 key）
    ```
55. JDK11、17、21版本特性
    - JDK11：新的 HTTP/2 Client（标准 API）、ZGC（实验性）首次加入
    - JDK17：record、ZGC 从实验 → 稳定
    - JDK21：虚拟线程（Virtual Threads）、ZGC 大幅增强
56. 每个 Java 类都有一个引用指向加载它的 ClassLoader。不过,数组类不是通过 ClassLoader 创建的,
    而是 JVM 在需要的时候自动创建的,数组类通过getclassLoader()方法获取 ClassLoader 的时候和该数组的元素类型的 classLoader 是一致的。
57. 如果我们不想打破双亲委派模型,就重写ClassLoader 类中的findClass()方法即可,无法被父类加载器加载的类最终会通过这个方法被加载。
    但是,如果想打破双亲委派模型则需要重写 loadClass()方法。
58. Tomcat 打破双亲委派模型的原理:自定义了类加载器 WebAppClassLoader,每个 Web 应用都会创建一个单独的 WebAppClassLoader
59. 如何快速实现LRU
    - LinkedHashMap设置accessOrder = true → 按访问顺序维护链表
    - 继承 LinkedHashMap，重写 removeEldestEntry 方法，控制容量
60. Java中4种引用类型的区别
    - 强引用（StrongReference）垃圾回收器绝不会回收它
    - 软引用（SoftReference）如果内存空间不足了，就会回收这些对象的内存
    - 弱引用（WeakReference）不管当前内存空间足够与否，都会回收它的内存
    - 虚引用（PhantomReference）如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收。虚引用主要用来跟踪对象被垃圾回收的活动。
61. 单例模式经典实现 
    - 饿汉式：利用类加载机制保证线程安全
        ```java
        public class ThreadPoolManager {

          private static final ThreadPoolExecutor INSTANCE = new ThreadPoolExecutor(
                4, 8, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<>()
          );

          private ThreadPoolManager() {}

          public static ThreadPoolExecutor getInstance() {
              return INSTANCE;
          }
        }
        ```
    - 懒加载：静态内部类跟外部类是平级的，所以外部类加载的时候不会影响内部类，因此实现了lazy loading, 同时也是利用静态变量的方式，使得INSTANCE只会在SingletonHolder加载的时候初始化一次，从而保证不会有多线程初始化的情况，因此也是线程安全的
      ````java
      public class ThreadPoolManager {

        private ThreadPoolManager() {}

        private static class SingletonHolder {
            private static final ThreadPoolExecutor INSTANCE = new ThreadPoolExecutor(
                4, 8, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<>()
            );
        }

        public static ThreadPoolExecutor getInstance() {
            return SingletonHolder.INSTANCE;
        }
      }
      ````
    - 懒加载：双重检查锁（Double-Check Locking, DCL）：必须加 volatile 防止指令重排序
      ````java
      public class ThreadPoolManager {

        private static volatile ThreadPoolExecutor instance;

        private ThreadPoolManager() {}

        public static ThreadPoolExecutor getInstance() {
            if (instance == null) {
                synchronized (ThreadPoolManager.class) {
                    if (instance == null) {
                        instance = new ThreadPoolExecutor(
                        4, 8, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<>());
                    }
                }
            }
        return instance;
        }
      }
      ````
    - 饿汉式：枚举单例（最安全 & 简洁）：防止反射和序列化破坏单例
      ````java
      public enum ThreadPoolManager {
        INSTANCE;
    
        private final ThreadPoolExecutor executor;
    
        ThreadPoolManager() {
            executor = new ThreadPoolExecutor(
            4, 8, 60, TimeUnit.SECONDS, new LinkedBlockingQueue<>());
        }
    
        public ThreadPoolExecutor getExecutor() {
            return executor;
        }
      }
      ````
62. IO模型
    - 主要有五种：阻塞 I/O、非阻塞 I/O、多路复用、信号驱动和异步 I/O。
    - 阻塞 I/O：应用程序执行 I/O 操作时，会一直等待数据传输完成，期间无法执行其他任务。
    - 非阻塞 I/O：应用程序执行 I/O 操作时，如果数据未准备好，立即返回错误状态，不等待数据传输完成，可执行其他任务。
    - 多路复用：允许一个线程同时管理多个 I/O 连接，适用于高并发、低延迟和高吞吐量场景，减少线程数量和上下文切换开销。
    - 信号驱动：依赖信号通知应用程序 I/O 事件，适用于低并发、低延迟和低吞吐量场景，需要为每个 I/O 事件创建信号和信号处理函数。
    - 异步 I/O：应用程序发起 I/O 操作后，内核负责数据传输过程，完成后通知应用程序。应用程序无需等待数据传输，可执行其他任务。
63. NIO 核心组件
    - Buffer（缓冲区）：NIO 读写数据都是通过缓冲区进行操作的。读操作的时候将 Channel 中的数据填充到 Buffer 中，而写操作时将 Buffer 中的数据写入到 Channel 中。
    - Channel（通道）：Channel 是一个双向的、可读可写的数据传输通道，NIO 通过 Channel 来实现数据的输入输出。通道是一个抽象的概念，它可以代表文件、套接字或者其他数据源之间的连接。
    - Selector（选择器）：允许一个线程处理多个 Channel，基于事件驱动的 I/O 多路复用模型。所有的 Channel 都可以注册到 Selector 上，由 Selector 来分配线程来处理事件。
64. Java IO有哪些类
    - 基础 I/O 类
      - 字节流类（InputStream，FileInputStream，ByteArrayInputStream）适合处理非文本数据以及任何需要精确控制字节流动的场景
      - 字符流类（Reader，FileReader，CharArrayReader，BufferedReader）适合处理文本数据，如读取和写入文本文件、处理 XML、JSON 等文本格式数据
    - NIO（New I/O）类
      - 缓冲区类（ByteBuffer，CharBuffer） 通道类（FileChannel，SocketChannel） 选择器类（Selector）
65. Java中如何访问对象的内存位置
    - 句柄（Handle）
      - 当创建一个对象时，JVM为这个对象分配内存，并为其分配一个句柄。对象引用实际上是指向这个句柄的地址。
      - 句柄JVM在进行垃圾回收时可以更方便地移动对象（例如，在内存中重新分配对象位置）而不会影响对象引用。
      - 每次访问对象多一次内存间接寻址，每次访问对象 多一次内存间接寻址
    - 直接指针（Direct Pointer）
      - 对象创建时，JVM分配内存，并将对象的内存地址直接作为引用返回给应用程序
      - 指针在垃圾回收过程中，如果对象被移动，那么直接指针需要被更新，这会增加实现的复杂性。
    - HotSpot JVM 采用的是「直接指针（Direct Pointer）」方案
66. Java 面向对象三大特性
    - 封装是将对象的属性和行为封装在类内部，通过访问控制机制隐藏内部实现细节，并对外提供受控访问方式的机制。
    - 继承是子类获得父类属性和方法的机制
    - 同一个父类引用，在运行时可以表现出不同子类行为
    - 继承是通过 extends 建立类之间的 is-a 关系，实现代码复用和层级结构；而多态是在继承基础上，通过方法重写和向上转型，使父类引用在运行时表现出不同子类行为
67. Java的多态分两类
    - 多态是指同一个方法调用，因为对象不同而产生不同的执行结果
    - 编译时多态（静态绑定）
      - 方法重载
    - 运行时多态（动态绑定）
      - 方法重写，父类引用指向子类对象调用的方法在运行时根据实际对象类型决定
      - 同一个接口的不同实现类（接口本质上是“抽象父类”）