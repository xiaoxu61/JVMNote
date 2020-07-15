# JVMNote

## JVM探究

`谈谈对JVM的理解`？java8虚拟机和之前的变化更新？<br>
什么是OOM，什么是栈溢出StackOverFlowError？怎么分析<br>
JVM常用调优参数有哪些？<br>
内存快照如何抓取，怎么分析Dump文件？知道吗？<br>
谈谈JVM中，对类加载器的认识<br>


1 JVM的位置<br>
2 JVM的体系结构<br>

## 3 类加载器
````
作用：加载Class文件 ~ 
加载初始化--
类是抽象的是模板，对象是具体的
1、虚拟机自带的加载器
2、启动(根)加载器
3、扩展类加载器
4、应用程序加载器
````
### 4 双委派机制
````
1、类加载器收到类加载的请求
2、将这个请求向上委托给父类加载器去完成，一直向上委托，直到启动类加载器
3、启动类加载器检查是否能够加载当前这个类，能加载就结束，使用当前的加载器，否则，抛出异常，通知子加载器进行加载
4、重复步骤 3 
机制的作用：
1、防止重复加载同一个.class。
   通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。
2、保证核心.class不能被篡改。通过委托方式，不会去篡改核心.clas，即使篡改也不会去加载，即使加载也不会是同一个.class对象了。
  不同的加载器加载同一个.class也不是同一个Class对象。这样保证了Class执行安全。

````

### 5 沙箱安全机制
````
    Java安全模型的核心就是Java沙箱（sandbox），什么是沙箱？沙箱是一个 `限制程序运行的环境`。
    沙箱机制就是将 Java 代码限定在虚拟机(JVM)特定的运行范围中，并且严格限制代码对本地系统资源访问，通过这样的措施来保证对代码的有效隔离，防止对本地系统造成破坏。
    沙箱主要限制系统资源访问，那系统资源包括什么？——CPU、内存、文件系统、网络。不同级别的沙箱对这些资源访问的限制也可以不一样。
    
组成沙箱的基本组件：
  # 字节码校验器（bytecode verifier）：确保Java类文件遵循Java语言规范。这样可以帮助Java程序实现内存保护。但并不是所有的类文件都会经过字节码校验，比如核心类。
  # 类装载器（class loader）：其中类装载器在3个方面对Java沙箱起作用
    它防止恶意代码去干涉善意的代码；
    它守护了被信任的类库边界；
    它将代码归入保护域，确定了代码可以进行哪些操作。
    虚拟机为不同的类加载器载入的类提供不同的命名空间，命名空间由一系列唯一的名称组成，每一个被装载的类将有一个名字，这个命名空间是由Java虚拟机为每一个类装载器维护的，它们互相之间甚至不可见。
类装载器采用的机制是双亲委派模式。
1、从最内层JVM自带类加载器开始加载，外层恶意同名类得不到加载从而无法使用；
2、由于严格通过包来区分了访问域，外层恶意的类通过内置代码也无法获得权限访问到内层类，破坏代码就自然无法生效。
    存取控制器（access controller）：存取控制器可以控制核心API对操作系统的存取权限，而这个控制的策略设定，可以由用户指定。
    安全管理器（security manager）：是核心API和操作系统之间的主要接口。实现权限控制，比存取控制器优先级高。
    安全软件包（security package）：java.security下的类和扩展包下的类，允许用户为自己的应用增加新的安全特性，包括：安全提供者、消息摘要、数字签名(keytools)、加密、鉴别
````
## 6 Native
````
具体做法是Native Method Stack中登记native方法，在Execution Engine 执行引擎执行的时候加载Native Libraries 本地库
public native void 类名();
凡是带有native关键字的，说明java的作用范围达不到了，java就会调用底层c语言的库（本地方法接口JNI）
会进入本地方法栈,调用本地方法接口 Java Native Interface
JNI作用：扩展Java 的使用，融合不同的编程语言为Java所用！ 最初：c/c++ ；因此在内存区域中专门开辟了一块标记区域--> 本地方法栈  登记native方法
在最终执行的时候，加载本地方法库中的方法通过JNI

//Java程序驱动打印机、管理系统、一些硬件会用到调用本地方法的情况，在企业级应用中较为少见

调用其他接口： Socket，WebService~..http
````
## 7 PC寄存器
````
  程序计数器：Program Counter Register
  每个程序都有一个程序计数器，是线程私有的，就是一个指针，指向方法区中的方法字节码(同来存储指向像一条指令的地址，也即将要执行的指令代码)，在执行引擎读取下一条指令，是一个非常小的内存空间，几乎可以忽略不记
````
## 8 方法区
````
Method Area
方法区是被所有线程共享，所有字段和方法字节码，以及一些特殊方法，如构造函数，接口代码也在此定义，简单说，所有定义的方法的信息都保存在该区域，此区域属于共享取键；
静态变量(static)、常量(final)、类信息(构造方法、接口定义)、运行时的常量池存在方法区中，但是 实例变量存在堆内存中，和方法区无关
````

## 9 栈
````
是一种数据结构

程序=数据结构+算法
先进后出
与之相应的就是队列： 先进先出
栈：栈内存，主程序的运行，生命周期和线程同步
线程结束，栈内存也就是释放，对于栈来说，不存在垃圾回收问题
一旦线程结束，栈就Over
栈中会存放：8大基本类型、实例方法、对象引用(地址值会指向堆中相应的地址)；------栈中的东西具体是怎么存的？
栈运行原理：栈帧（子帧、父帧）
栈满了：StackOverflowError

栈+堆+方法区的一些交互关系

自己画一个对象实例化的过程在内存中
````

## 10三种JVM

* sun公司 HotSpot(TM) 
* BEA JRockit
* IBM J9 VM

## 11堆
**一个JVM只有一个堆内存**，堆内存的大小是可以调节的。<br>
类加载器读取了类文件后，一般会有什么东西放到堆中？类、方法、常量，变量，保存我们所有引用类型的真实对象。<br>
堆内存中还要细分为三个区域：<br>
* 新生区（伊甸园）Young/New <br>
   类：诞生和成长的地方，甚至死亡 <br>
   伊甸园区：所有对象都是在伊甸园区**new出来的**  （伊甸园区空间满了会触发一次轻GC，存活下来的会存到幸存者区）<br>
   幸存者区（0区和1区）：（多次轻GC后幸存区中空间满了，会触发一次重GC，幸存下来的对象在0<-->1区多次交换筛选后幸存下来的会存到老年区） <br>
* 老年区（养老区）Old： （当经过多次重GC，幸存下来的把老年区**内存都塞满后会触发OOM**）<br>
* 永久区 <br>
   这个区域常驻内存，存放JDK自身携带的Class对象，Interface元数据，存储的是Java运行时的一些环境或类信息，这个区域不存在垃圾回收！关闭虚拟机就会释放这个区域的内存~<br>
   jdk 1.6 之前：永久带，常量池是在方法区<br>
   jdk 1.7    ： 永久带，但是慢慢的退化了，`去永久代`，常量池在堆中 <br>
   jdk 1.8 之后：无永久带，常量池在元空间 <br>
垃圾回收主要是在伊甸园去与养老区 <br>
假设内存满了，OOM（OutOfMemoryError），堆内存不够 <br>
在JDK8以后，永久存储区改名为（元空间） <br>
什么情况下在永久区就崩了：一个启动类加载了大量的第三方jar包，tomcat部署了太多的应用，大量动态生成的反射类。不断被加载，直到内存满就会出现OOM <br>
默认情况下：分配的内存 是电脑内存的1/4   而初始化的内存是1/64 <br>
元空间：逻辑上存在，物理上不存在  <br>
在一个项目中，突然出现OOM故障，该如何排除，研究为什么出错  <br>
* 能够看到代码第几行出错：内存快照分析工具，MAT，JProfile <br>
* Debug 一行行代码分析 <br>
### MAT JProfile
* 分析Dump内存文件，快速定位内存泄露
* 获得堆中数据
* 获得大的对象
## 14堆内存调优
15GC
  1、常用算法
16JMM
17总结
java --> Class File --> 类加载器(ClassLoader) <---> 运行时数据区(方法区、Java栈、本地方法栈、堆、程序计数器) <---> 本地方法接口
