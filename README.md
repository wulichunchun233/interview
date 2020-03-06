# 技术面试知识点总结

---

## 1.编程语言

### c++
- [在编程中遇到的零散的知识点](/docs/notes/1.编程语言/c++/在编程中遇到的零散的知识点.md)
- [string、vector和数组](/docs/notes/1.编程语言/c++/string和vector.md)
- [表达式](/docs/notes/1.编程语言/c++/expression.md)
- [函数](/docs/notes/1.编程语言/c++/function.md)
- [类](/docs/notes/1.编程语言/c++/class.md)

### Java

#### CyC整理

- [Java 基础](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md)
- [Java 容器](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%AE%B9%E5%99%A8.md)
- [Java 并发](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%B9%B6%E5%8F%91.md)
- [Java 虚拟机](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA.md)
- [Java I/O](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20IO.md)

#### JavaGuild 整理

##### 基础

基础知识系统总结：

- [Java 基础知识回顾](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md)
- [Java 基础知识疑难点/易错点](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Java%E7%96%91%E9%9A%BE%E7%82%B9.md)
- [一些重要的Java程序设计题](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Java%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E9%A2%98.md)
- [J2EE 基础知识回顾](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/J2EE%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md)

重要知识点详解：

- [枚举](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/basic/%E7%94%A8%E5%A5%BDJava%E4%B8%AD%E7%9A%84%E6%9E%9A%E4%B8%BE%E7%9C%9F%E7%9A%84%E6%B2%A1%E6%9C%89%E9%82%A3%E4%B9%88%E7%AE%80%E5%8D%95.md) （很重要的一个数据结构，用好枚举真的没有那么简单！）
- [Java 常见关键字总结：final、static、this、super!](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/basic/final%E3%80%81static%E3%80%81this%E3%80%81super.md)
- [什么是反射机制?反射机制的应用场景有哪些?](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/basic/reflection.md)

其他：

- [JAD反编译](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/JAD%E5%8F%8D%E7%BC%96%E8%AF%91tricks.md)

##### 容器

总结：

- [Java容器常见面试题/知识点总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/Java%E9%9B%86%E5%90%88%E6%A1%86%E6%9E%B6%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md)

源码学习：

- [ArrayList 源码学习](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/ArrayList.md)
- [LinkedList 源码学习](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/LinkedList.md)
- [HashMap(JDK1.8)源码学习](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/collection/HashMap.md)

##### 并发

面试题总结：

- [Java 并发基础常见面试题总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/JavaConcurrencyBasicsCommonInterviewQuestionsSummary.md)
- [Java 并发进阶常见面试题总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/JavaConcurrencyBasicsCommonInterviewQuestionsSummary.md)

必备知识点：

- [并发容器总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/%E5%B9%B6%E5%8F%91%E5%AE%B9%E5%99%A8%E6%80%BB%E7%BB%93.md)
- [Java线程池学习总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/java%E7%BA%BF%E7%A8%8B%E6%B1%A0%E5%AD%A6%E4%B9%A0%E6%80%BB%E7%BB%93.md)
- [乐观锁与悲观锁](https://github.com/Snailclimb/JavaGuide/blob/master/docs/essential-content-for-interview/%E9%9D%A2%E8%AF%95%E5%BF%85%E5%A4%87%E4%B9%8B%E4%B9%90%E8%A7%82%E9%94%81%E4%B8%8E%E6%82%B2%E8%A7%82%E9%94%81.md)
- [JUC 中的 Atomic 原子类总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/Atomic.md)
- [AQS 原理以及 AQS 同步组件总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Multithread/AQS.md)

##### JVM

- [一 Java内存区域](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/Java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F.md)
- [二 JVM垃圾回收](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/JVM%E5%9E%83%E5%9C%BE%E5%9B%9E%E6%94%B6.md)
- [三 JDK 监控和故障处理工具](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/JDK%E7%9B%91%E6%8E%A7%E5%92%8C%E6%95%85%E9%9A%9C%E5%A4%84%E7%90%86%E5%B7%A5%E5%85%B7%E6%80%BB%E7%BB%93.md)
- [四 类文件结构](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/%E7%B1%BB%E6%96%87%E4%BB%B6%E7%BB%93%E6%9E%84.md)
- [五 类加载过程](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E8%BF%87%E7%A8%8B.md)
- [六 类加载器](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/%E7%B1%BB%E5%8A%A0%E8%BD%BD%E5%99%A8.md)
- [【待完成】八 最重要的 JVM 参数指南（翻译完善了一半）](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/%E6%9C%80%E9%87%8D%E8%A6%81%E7%9A%84JVM%E5%8F%82%E6%95%B0%E6%8C%87%E5%8D%97.md)
- [九 JVM 配置常用参数和常用 GC 调优策略](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/GC%E8%B0%83%E4%BC%98%E5%8F%82%E6%95%B0.md)
- [【加餐】大白话带你认识JVM](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/jvm/%5B%E5%8A%A0%E9%A4%90%5D%E5%A4%A7%E7%99%BD%E8%AF%9D%E5%B8%A6%E4%BD%A0%E8%AE%A4%E8%AF%86JVM.md)

##### I/O

- [BIO,NIO,AIO 总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/BIO-NIO-AIO.md)
- [Java IO 与 NIO系列文章](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Java%20IO%E4%B8%8ENIO.md)

##### Java 8

- [Java 8 新特性总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/What's%20New%20in%20JDK8/Java8Tutorial.md)
- [Java 8 学习资源推荐](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/What's%20New%20in%20JDK8/Java8%E6%95%99%E7%A8%8B%E6%8E%A8%E8%8D%90.md)
- [Java8 forEach 指南](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/What's%20New%20in%20JDK8/Java8foreach%E6%8C%87%E5%8D%97.md)

##### 优雅 Java 代码必备实践(Java编程规范)

- [Java 编程规范以及优雅 Java 代码实践总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/java/Java%E7%BC%96%E7%A8%8B%E8%A7%84%E8%8C%83.md)

### python

- [python基础](/docs/notes/1.编程语言/python/python_base.md)
- [python高级](/docs/notes/1.编程语言/python/python_high.md)
- [python面试问题](/docs/notes/1.编程语言/python/python_interview.md)

---

## 2.数据结构

- [数据结构](/docs/notes/2.数据结构/数据结构.md)

---

## 3.计算机网络

### JavaGuild 整理

- [计算机网络常见面试题](https://github.com/Snailclimb/JavaGuide/blob/master/docs/network/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C.md)
- [计算机网络基础知识总结](https://github.com/Snailclimb/JavaGuide/blob/master/docs/network/%E5%B9%B2%E8%B4%A7%EF%BC%9A%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%E7%9F%A5%E8%AF%86%E6%80%BB%E7%BB%93.md)
- [HTTPS中的TLS](https://github.com/Snailclimb/JavaGuide/blob/master/docs/network/HTTPS%E4%B8%AD%E7%9A%84TLS.md)

### CyC整理

- [计算机网络](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%BD%91%E7%BB%9C%20-%20%E7%9B%AE%E5%BD%95.md)
- [HTTP](https://github.com/CyC2018/CS-Notes/blob/master/notes/HTTP.md)
- [Socket](https://github.com/CyC2018/CS-Notes/blob/master/notes/Socket.md)

### 自己整理

- [计算机网络面试常考知识点整理](/docs/notes/3.计算机网络/network.md)

---

## 4.操作系统

### CyC整理

- [计算机操作系统](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E8%AE%A1%E7%AE%97%E6%9C%BA%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%20-%20%E7%9B%AE%E5%BD%95.md)

### 自己整理

- [操作系统面试常考知识点整理](/docs/notes/4.操作系统/os.md)

### linux

- [linux](/docs/notes/4.操作系统/linux/linux.md)
- [shell](/docs/notes/4.操作系统/linux/shell.md)
- [nginx](/docs/notes/4.操作系统/linux/nginx.md)
- [自动化部署](/docs/notes/4.操作系统/linux/自动化部署.md)
- [linux面试常考知识点整理](/docs/notes/4.操作系统/linux/linux面试问题.md)

---

## 5.数据库

### CyC整理

- [数据库系统原理](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%95%B0%E6%8D%AE%E5%BA%93%E7%B3%BB%E7%BB%9F%E5%8E%9F%E7%90%86.md)
- [SQL](https://github.com/CyC2018/CS-Notes/blob/master/notes/SQL.md)
- [Leetcode-Database 题解](https://github.com/CyC2018/CS-Notes/blob/master/notes/Leetcode-Database%20%E9%A2%98%E8%A7%A3.md)
- [MySQL](https://github.com/CyC2018/CS-Notes/blob/master/notes/MySQL.md)
- [Redis](https://github.com/CyC2018/CS-Notes/blob/master/notes/Redis.md)

---

## 6.算法

- [算法做题技巧](/docs/notes/8.算法/算法做题技巧.md)

## 7.工具

### CyC整理

- [Git](https://github.com/CyC2018/CS-Notes/blob/master/notes/Git.md)
- [正则表达式](https://github.com/CyC2018/CS-Notes/blob/master/notes/%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F.md)

---

## 面经整理

- [Java开发工程师](/docs/面经/Java研发工程师/)
- [C++后台开发工程师](/docs/面经/C++后台/)
