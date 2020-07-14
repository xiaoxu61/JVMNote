# JVMNote

## JVM探究
````
谈谈对JVM的理解？java8虚拟机和之前的变化更新？
什么是OOM，什么是栈溢出StackOverFlowError？怎么分析
JVM常用调优参数有哪些？
内存快照如何抓取，怎么分析Dump文件？知道吗？
谈谈JVM中，对类加载器的认识
````

1 JVM的位置
2 JVM的体系结构
3 类加载器
4 双委派机制
5 沙箱安全机制
6 Native
7 PC寄存器
8 方法区
9 栈
10三种JVM
11堆
12新生区、老年区
13永久区
14堆内存调优
15GC
  1、常用算法
16JMM
17总结
java --> Class File --> 类加载器(ClassLoader) <---> 运行时数据区(方法区、Java栈、本地方法栈、堆、程序计数器) <---> 本地方法接口
