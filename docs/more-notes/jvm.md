---
title: JVM
tags:
 - JVM
categories:
 - 学习笔记
---

## 1. JVM 与 Java 体系结构

###  JVM 的位置

JVM 运行在操作系统之上，JDK 包含 JRE, JRE 包含 JVM

![image-20211214122438958](http://image.xiaobailx.top/images/20211214122448.png)



### JVM 的整体结构

![image-20211214162004600](http://image.xiaobailx.top/images/20211214162004.png)



- JVM 调优是指对 **方法区和堆** 进行优化；
- Java 栈、本地方法栈、程序计数器不会有垃圾；
- 程序计数器、虚拟机栈、本地方法栈为线程私有，堆和内存为线程共享。



### Java 代码执行流程

![image-20211214192750977](http://image.xiaobailx.top/images/20211214192751.png)



## 2. 类加载子系统

![image-20211214122827036](http://image.xiaobailx.top/images/20211214122827.png)

**类加载器子系统的作用**

1. 类加载器子系统负责从文件系统或者网络中加载Class文件，class文件在文件开头有特定的文件标识。
2. ClassLoader只负责class文件的加载，至于它是否可以运行，则由Execution Engine决定。
3. **加载的类信息存放于一块称为方法区的内存空间**。除了类的信息外，方法区中还会存放运行时常量池信息，可能还包括字符串字面量和数字常量（这部分常量信息是Class文件中常量池部分的内存映射）



### 类加载器ClassLoader角色

在 .class --> JVM --> DNA 元数据模板，ClassLoader 承担运输工作，是一个快递员角色。

![image-20211214134706025](http://image.xiaobailx.top/images/20211214134706.png)



### 类加载器分类

JVM严格支持两种类型的类加载器。分别为**引导类加载器（Bootstrap ClassLoader）和自定义类加载器（User-Defined ClassLoader）**。

![image-20211214143544718](http://image.xiaobailx.top/images/20211214143544.png)



**启动类加载器（引导类加载器，Bootstrap ClassLoader）** 

由 C/C++ 语言实现，打包加载 Java 的核心库，用于提供 JVM 自身需要的类，作为加载扩展类和应用程序类加载器的父类。

**扩展类加载器（Extension Class Loader）** 

Java 语言编写，父类加载器为启动类加载器，从java.ext.dirs系统属性所指定的目录中加载类库，或从JDK的安装目录的jre/lib/ext子目录（扩展目录）下加载类库。

**应用程序类加载器（系统类加载器，AppClassLoader）**

Java 语言编写，父类加载器是扩展类加载器，负责加载环境变量类路径或系统属性java.class.path指定路径下的类库。



### 双亲委派机制

1. 如果一个类加载器收到了类加载请求，它并不会自己先去加载，就可以把这个请求委托给父类的加载器去执行；
2. 如果父类加载器还其自身，则可以进一步提升父类加载器，具体加载对象，目标最终将达到一个启动类加载；
3. 如果父类加载器可以完成类加载任务，就成功返回，如果父类加载器无法完成此加载任务，子加载器就会尝试自己去，这就是双亲委派模式。
4. 父类加载器一组往下分配任务，如果子类加载器能加载，则加载状态，如果将加载任务分配至系统类加载器也无法加载，则抛出异常

![image-20211214144007861](http://image.xiaobailx.top/images/20211214144007.png)



### 沙箱安全机制

沙箱是一个限制程序运行的环境，使Java代码限定在虚拟机(JVM)特定的运行范围中，并且严格限制代码对本地系统资源访问，通过这样的措施来保证对代码的有效隔离，防止对本地系统造成破坏。

沙箱 **主要限制系统资源访问**，包括 CPU、内存、文件系统、网络。不同级别的沙箱对这些资源访问的限制也可以不一样。

**沙箱的基本组件**

- 字节码校验器 (bytecode verifier)：确保 Java 类文件遵循 Java 语言规范，帮助 Java 程序实现内存保护。但不是所有类文件都会进行字节码校验，如核心类。
- 类加载器 (class loader)：其中类装载器在3个方面对Java沙箱起作用：
  - 它防止恶意代码去干涉善意的代码;
  - 它守护了被信任的类库边界;
  - 它将代码归入保护域，确定了代码可以进行哪些操作。

 虚拟机为不同的类加载器载入的类提供不同的命名空间，命名空间由一系列唯一的名称组成，每一个被装载的类将有一个名字，这个命名空间是由Java虚拟机为每一个类装载器维护的，它们互相之间甚至不可见。

==类装载器采用的机制是双亲委派模式。==

1.从最内层VM自带类加载器开始加载，外层恶意同名类得不到加载从而无法使用;

2.由于严格通过包来区分了访问域，外层恶意的类通过内置代码也无法获得权限访问到内层类，破坏代码就自然无法生效。



## 3.  运行时数据区

![image-20211214202525451](http://image.xiaobailx.top/images/20211214202525.png)



###  程序计数器

1. 程序计数器（Program Counter Register） ，也称 PC 寄存器，是对物理 PC 寄存器的抽象模拟，存储指令相关的现场信息。
2. 它是一块很小的内存空间，几乎可以忽略不记；同时也是运行速度最快的存储区域。
3. JVM 中的每个线程都有一个程序计数器，是线程私有的，生命周期与线程的生命周期保持一致。
4. 任何时间一个线程都只有一个方法在执行，也就是所谓的**当前方法**。程序计数器会存储当前线程正在执行的Java方法的JVM指令地址；或者，如果是在执行native方法，则是未指定值（undefned）。
5. 它是**程序控制流**的指示器，分支、循环、跳转、异常处理、线程恢复等基础功能都需要依赖这个计数器来完成。
6. 字节码解释器工作时就是通过改变这个计数器的值来选取下一条需要执行的字节码指令。

7. 它是**唯一一个**在Java虚拟机规范中没有规定任何OutofMemoryError情况的区域。



### 本地方法接口

本地接口的作用是融合不同的编程语言为 Java 所用，它的初衷是融合 C/C++ 程序，于是在内存中专门开辟了一块区域处理标记为native的代码，它的具体做法是 在 Native Method Stack 中登记 native 方法，在 ( ExecutionEngine ) 执行引擎执行的时候加载 Native Libraies。

**native 关键字**

native 关键字修饰方法执行过程：调用本地方法栈 ---> JNI (Java Native Interface本地方法接口) 调用外部语言程序。

- 凡是带了native关键字的，说明 Java 的作用范围达不到，去调用底层 C/C++ 语言的库；


- 凡是带了native关键字的方法就会进入本地方法栈；

- 



### 本地方法栈

1. **Java虚拟机栈于管理Java方法的调用，而本地方法栈用于管理本地方法的调用**。
2. 本地方法栈，也是线程私有的。
3. 允许被实现成固定或者是可动态扩展的内存大小（在内存溢出方面和虚拟机栈相同）
   - 如果线程请求分配的栈容量超过本地方法栈允许的最大容量，Java虚拟机将会抛出一个 stackoverflowError 异常。
   - 如果本地方法栈可以动态扩展，并且在尝试扩展的时候无法申请到足够的内存，或者在创建新的线程时没有足够的内存去创建对应的本地方法栈，那么Java虚拟机将会抛出一个 OutOfMemoryError 异常。
4. 本地方法一般是使用 C 语言或 C++ 语言实现的。
5. 它的具体做法是 Native Method Stack 中登记 native 方法，在 Execution Engine 执行时加载本地方法库。



## 4. 虚拟机栈

Java 虚拟机栈（Java Virtual Machine Stack），早期也叫 Java栈。每个线程在创建时都会创建一个虚拟机栈，其内部保存一个个的栈帧（Stack Frame），**对应着一次次的 Java 方法调用**，栈是线程私有的。

**生命周期：** 和线程一致，也就是线程结束了，该虚拟机栈也销毁了

**作用：** 主管 Java 程序的运行，保存方法的局部变量（8 种基本数据类型、对象的引用地址）、部分结果，并参与方法的调用和返回。

**异常：**

Java 虚拟机规范允许Java栈的大小是动态的或者是固定不变的。

- 如果采用固定大小的Java虚拟机栈，那每一个线程的Java虚拟机栈容量可以在线程创建的时候独立选定。如果线程请求分配的栈容量超过Java虚拟机栈允许的最大容量，Java虚拟机将会抛出一个**StackoverflowError** 异常。
- 如果Java虚拟机栈可以动态扩展，并且在尝试扩展的时候无法申请到足够的内存，或者在创建新的线程时没有足够的内存去创建对应的虚拟机栈，那Java虚拟机将会抛出一个 **OutofMemoryError** 异常。

**设置栈大小：**

```java
-Xss1m
-Xss1024k
-Xss1048576
```



### 栈的存储单位

1. 每个线程都有自己的栈，栈中的数据都是以 **栈帧**（Stack Frame）的格式存在
2. 在这个线程上正在执行的每个方法都各自对应一个栈帧（Stack Frame）。
3. 栈帧是一个内存区块，是一个数据集，维系着方法执行过程中的各种数据信息。



**栈的运行原理**

1. JVM 直接对 Java 栈的操作只有两个，就是对栈帧的 **压栈和出栈** ，遵循先进后出（后进先出）原则
2. 在一条活动线程中，一个时间点上，只会有一个活动的栈帧。即只有当前正在执行的方法的栈帧（栈顶栈帧）是有效的。这个栈帧被称为 **当前栈帧（Current Frame）**，与当前栈帧相对应的方法就是 **当前方法（Current Method）** ，定义这个方法的类就是 **当前类（Current Class）**
3. 执行引擎运行的所有字节码指令只针对当前栈帧进行操作。
4. 如果在该方法中调用了其他方法，对应的新的栈帧会被创建出来，放在栈的顶端，成为新的当前帧。

![image-20211214213116697](http://image.xiaobailx.top/images/20211214213116.png)

**栈帧的内部结构**

![image-20211214214322404](http://image.xiaobailx.top/images/20211214214322.png)



## 5. 堆

![image-20211214164631285](http://image.xiaobailx.top/images/20211214164631.png)

### 堆与进程

1. **一个进程只有一个JVM实例**，一个JVM实例中就有一个运行时数据区，一个运行时数据区只有一个堆和一个方法区。
2. **同一个进程的多个线程共享同一堆空间** 。
3. **所有的对象实例以及数组都应当在运行时分配在堆上** 。



### 堆内存细分

- JDK7 及之前堆内存逻辑上分为三部分：新生区+养老区+永久区

![image-20211214171115383](http://image.xiaobailx.top/images/20211214171115.png)



- Java 8及之后堆内存逻辑上分为三部分：新生区+养老区+元空间

![image-20211214171506173](http://image.xiaobailx.top/images/20211214171506.png)



### 谁空谁是 to

**GC** 垃圾回收主要是在新生区和养老区，又分为轻GC 和 重GC，如果内存不够，或者存在死循环，就会导致 ```java.lang.OutOfMemoryError: Java heap space```



###  生区、养老区

- 新生区是类诞生，成长，消亡的区域，一个类在这里产生，应用，最后被垃圾回收器收集，结束生命。
- 新生区又分为两部分：伊甸区（Eden Space）和幸存者区（Survivor Space），所有的类都是在伊甸区被new出来的，幸存区有两个：0区 和 1区，当伊甸园的空间用完时，程序又需要创建对象，JVM的垃圾回收器将对伊甸园区进行垃圾回收（Minor GC）。将伊甸园中的剩余对象移动到幸存0区，若幸存0区也满了，再对该区进行垃圾回收，然后移动到1区，那如果1区也满了呢？（这里幸存0区和1区是一个互相交替的过程）再移动到养老区，若养老区也满了，那么这个时候将产生MajorGC（Full GC），进行养老区的内存清理，若养老区执行了Full GC后发现依然无法进行对象的保存，就会产生OOM异常 "OutOfMemoryError"。如果出现 java.lang.OutOfMemoryError：java heap space异常，说明 Java 虚拟机的堆内存不够，原因如下：
  - 1、Java虚拟机的堆内存设置不够，可以通过参数 -Xms（初始值大小），-Xmx（最大大小）来调整。
  - 2、代码中创建了大量大对象，并且长时间不能被垃圾收集器收集（存在被引用）或者死循环。

### 永久区

- 永久存储区是一个常驻内存区域，用于存放 JDK 自身所携带的 Class，Interface 的元数据，也就是说它存储的是运行环境必须的类信息，被装载进此区域的数据是不会被垃圾回收器回收掉的，关闭JVM才会释放此区域所占用的内存。
- 如果出现 java.lang.OutOfMemoryError：PermGen space，说明是 Java虚拟机对永久代 Perm 内存设置不够。一般出现这种情况，都是程序启动需要加载大量的第三方 jar 包，
- 例如：在一个Tomcat下部署了太多的应用。或者大量动态反射生成的类不断被加载，最终导致 Perm 区被占满。



### 堆内存调优

> - -Xms：设置初始分配大小，默认为物理内存的 “1/64”。
> - -Xmx：最大分配内存，默认为物理内存的 “1/4”。
> - -XX：+PrintGCDetails：输出详细的GC处理日志。
> - -XX：+HeapDumpOnOutMemoryError //OOM



## 6 方法区





## 10. 垃圾回收相关算法

### 标记阶段

#### 1. 引用计数法

![image-20211214181159568](http://image.xiaobailx.top/images/20211214181159.png)

每个对象有一个引用计数器，当对象被引用一次则计数器加 1，当对象引用失效一次，则计数器减 1，对于计数器为 0 的对象意味着是垃圾对象，可以被 GC 回收。

**优点：**  实现简单，垃圾对象便于辨识；判定效率高、回收没有延时。

**缺点：**  

- 需要单独的字段存储计数器，增加了 **存储空间的开销** ；
- 每次赋值都需要更新计数器，伴随着加法和减法操作，这增加了 **时间开销**

- **无法处理循环引用** 的情况。这是一条致命缺陷，导致在Java的垃圾回收器中没有使用这类算法。



#### 2. 可达性分析算法

![image-20211214181959704](http://image.xiaobailx.top/images/20211214181959.png)



相较于引用计数算法，不仅同样具备实现简单、执行高效的特点，更重要的是 **解决了循环引用问题**，防止内存泄露问题。

**实现思路**

1. 可达性分析算法是以根对象集合（GCRoots）为起始点，按照从上至下的方式 **搜索被根对象集合所连接的目标对象是否可达。**
2. 使用可达性分析算法后，内存中的存活对象都会被根对象集合直接或间接连接着，搜索所走过的路径称为 **引用链**（Reference Chain）
3. 如果目标对象没有任何引用链相连，则是不可达的，就意味着该对象己经死亡，可以标记为垃圾对象。
4. 在可达性分析算法中，只有能够被根对象集合直接或者间接连接的对象才是存活对象。



### 清除阶段

#### 1. 标记 - 清除算法

**执行过程**

当堆中的有效内存空间（available memory）被耗尽的时候，就会停止整个程序（也被称为stop the world），然后进行两项工作，第一项则是标记，第二项则是清除

1. 标记：Collector 从引用根节点开始遍历，标记所有被引用的对象。一般是在对象的 Header 中记录为可达对象。
   - 注意：标记的是被引用的对象，也就是可达对象，并非标记的是即将被清除的垃圾对象
2. 清除：Collector 对堆内存从头到尾进行线性的遍历，如果发现某个对象在其 Header 中没有标记为可达对象，则将其回收

![image-20211214190901315](http://image.xiaobailx.top/images/20211214190901.png)

**缺点**

1. 标记清除算法的效率不算高
2. 在进行GC的时候，需要停止整个应用程序，用户体验较差
3. 这种方式清理出来的空闲内存是不连续的，产生内碎片，需要维护一个空闲列表



#### 2. 复制算法

**核心思想**

将活着的内存空间分为两块，每次只使用其中一块，在垃圾回收时将正在使用的内存中的存活对象复制到未被使用的内存块中，之后清除正在使用的内存块中的所有对象，交换两个内存的角色，最后完成垃圾回收。

![image-20211214190801752](http://image.xiaobailx.top/images/20211214190801.png)

**优点：**

1. 没有标记和清除过程，实现简单，运行高效。
2. 复制过去以后保证空间的连续性，不会出现“碎片”问题。

**缺点**

1. 此算法的缺点也是很明显的，就是需要两倍的内存空间。
2. 对于 G1 这种分拆成为大量 region 的 GC，复制而不是移动，意味着GC需要维护 region 之间对象引用关系，不管是内存占用或者时间开销也不小。



#### 3. 标记 - 压缩算法

**执行过程**

1. 第一阶段和标记清除算法一样，从根节点开始标记所有被引用对象
2. 第二阶段将所有的存活对象压缩到内存的一端，按顺序排放。之后，清理边界外所有的空间。

![image-20211214191737327](http://image.xiaobailx.top/images/20211214191737.png)

**标记 - 压缩算法与标记 - 清除算法的比较**

1. 标记-压缩算法的最终效果等同于标记-清除算法执行完成后，再进行一次内存碎片整理，因此，也可以把它称为标记-清除-压缩（Mark-Sweep-Compact）算法。
2. 二者的本质差异在于标记-清除算法是一种**非移动式的回收算法**，标记-压缩是**移动式的**。是否移动回收后的存活对象是一项优缺点并存的风险决策。
3. 可以看到，标记的存活对象将会被整理，按照内存地址依次排列，而未被标记的内存会被清理掉。如此一来，当我们需要给新对象分配内存时，JVM只需要持有一个内存的起始地址即可，这比维护一个空闲列表显然少了许多开销。

**优点**

1. 消除了标记-清除算法当中，内存区域分散的缺点，我们需要给新对象分配内存时，JVM只需要持有一个内存的起始地址即可。
2. 消除了复制算法当中，内存减半的高额代价。

**缺点**

1. 从效率上来说，标记-整理算法要低于复制算法。
2. 移动对象的同时，如果对象被其他对象引用，则还需要调整引用的地址（因为HotSpot虚拟机采用的不是句柄池的方式，而是直接指针）
3. 移动过程中，需要全程暂停用户应用程序。即：STW



### 总结

**对比三种清除阶段的算法**

1. 效率上来说，复制算法是当之无愧的老大，但是却浪费了太多内存。
2. 而为了尽量兼顾上面提到的三个指标，标记-整理算法相对来说更平滑一些，但是效率上不尽如人意，它比复制算法多了一个标记的阶段，比标记-清除多了一个整理内存的阶段。

| 标记清除     | 标记整理           | 复制             |                                       |
| ------------ | ------------------ | ---------------- | ------------------------------------- |
| **速率**     | 中等               | 最慢             | 最快                                  |
| **空间开销** | 少（但会堆积碎片） | 少（不堆积碎片） | 通常需要活对象的2倍空间（不堆积碎片） |
| **移动对象** | 否                 | 是               | 是                                    |





参考资料：[狂神说笔记 —— JVM 入门](https://www.cnblogs.com/gh110/p/14917326.html)、[尚硅谷详解 Java 虚拟机](https://youthlql.gitee.io/javayouth/#/?id=jvm)