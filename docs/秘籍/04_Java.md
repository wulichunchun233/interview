# Java

## 基础

### 1.为什么要用单例模式?手写几种线程安全的单例模式?

简单来说使用单例模式可以带来下面几个好处:

- 对于频繁使用的对象，可以省略创建对象所花费的时间，这对于那些重量级对象而言，是非常可观的一笔系统开销； 
- 由于 new 操作的次数减少，因而对系统内存的使用频率也会降低，这将减轻 GC 压力，缩短 GC 停顿时间。

补充：（类加载的过程）

Java程序初始化顺序：

1. 父类的静态代码块
2. 子类的静态代码块
3. 父类的普通代码块
4. 父类的构造方法
5. 子类的普通代码块
6. 子类的构造方法

Java中实现的单例模式有以下几种：

- 懒汉式
- 饿汉式
- 静态内部类
- 枚举

以下是几种单例模式的实现：

**懒汉式(双重检查加锁版本)**

懒汉式在第一次调用的时候进行实例化

```java
public class Singleton{
    // 单例实体对象，volatile保证当uniqueInstance变量初始化成Singleton实例时，多个线程可以正确的处理uniqueInstance变量
    private volatile static Singleton uniqueInstance;
    // 构造函数私有化，不可被外部访问
    private Singleton(){}
    public static Singleton getInstance(){
        // 检查实例，如果不存在就进入同步代码块
        if(uniqueInstance == null){
            // 只有第一次执行才会彻底执行这里的代码
            synchronized(Singleton.class){
                // 进入同步代码块之后再检查一次，如果仍为null，才创建实例
                if(uniqueInstance == null){
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

**静态内部类方式**

静态内部实现的单例是**懒加载的且线程安全**。

只有通过显式调用 getInstance 方法时，才会显式装载 SingletonHolder 类，从而实例化 instance（只有第一次使用这个单例的实例的时候才加载，同时不会有线程安全问题）。

```java
public class Singleton{
    // 静态内部类中来创建单例对象
    private static class SingletonHolder{
        // 定义为静态常量，不可被修改
        private static final Singleton INSTANCE = new Singleton();
    }
    private Single(){}
    public static final Singleton getInstance(){
        // 只有通过显式调用 getInstance 方法时，才会显式装载 SingletonHolder 类，从而实例化 instance
        return SingletonHolder.INSTANCE;
    }
}
```

**饿汉式**

在类初始化时，已经自行实例化。

```java
public class Singleton{
    // 类加载时直接初始化实例
    private static final Singleton INSTANCE = new Singleton();
    private Singleton(){}
    // 直接返回初始化之后的实例对象
    public static Singleton getInstance(){
        return INSTANCE;
    }
}
```



**枚举方式**

创建枚举默认就是线程安全的，所以不需要担心 double checked locking，而且还能防止反序列化导致重新创建新的对象。保证只有一个实例（即使使用反射机制也无法多次实例化一个枚举量）。

```java
public class Singleton{
    public static void main(String[] args){
        Single single = Single.SINGLE;
        single.print();
    }
    // 枚举对象
    enum Single{
        SINGLE;
        private Single(){}
        public void print(){
            System.out.println("Hello World");
        }
    }
}
```



总结：一般情况下直接使用**饿汉式**就好了，如果明确要求要**懒加载（lazy initialization）**会倾向于使用**静态内部类**，如果涉及到**反序列化**创建对象时会试着使用**枚举**方式来实现单例。

手写单例模式的话需要掌握懒汉式、饿汉式、静态内部类三种。

### 2.Java 反射机制介绍？

**JAVA反射（Reflection）**：在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制。

Java的反射机制是java被称为动态语言的一个关键性质。

那么反射机制所能实现的功能有：

1.    只要给定类的全名，即可通过反射获取类的所有信息。
2.    反射可以在程序运行时获取任意一个对象所属的类对象。
3.    在运行时可以获取到类中所有属性对象，并对其操作（包括私有属性）。
4.    在运行时可以获取到类中、父类中所有方法，并调用。

### 3.类装载器介绍？

**ClassLoader类装载器**就是寻找类的字节码文件并构造出类在  JVM 内部表示的对象组件。主要工作由ClassLoader及其子类负责，ClassLoader是一个重要的 Java运行时系统组件，它负责在运行时查找和装入 Class 字节码文件。

在 JAVA中 java 虚拟机把一个类装入到 java 虚拟机当中需要经过以下的步骤：

1. **装载**：查找和导入Class文件。

2. **链接**：执行校验，准备和解析步骤。

其中校验主要是检查载入class文件数据的正确性，而准备工作就是给类的静态变量来分配存储空间，解析则是将符号引用来转变成直接引用。

3. **初始化**：对类的静态变量、静态代码块执行初始化工作。

类文件被装载并解析之后，在 java 虚拟机内将拥有一个对应的 java.lang.class 类描述对象。该类的对象实例则拥有指向这个类描述对象的引用。而类描述对象又拥有指向关联 ClassLoader 的引用。

JVM装载类时使用**全盘负责委托机制**

**全盘负责**：当一个ClassLoader装载一个类的时候，除非显式的使用另一个ClassLoader，否则该类所依赖即引用的类也由这个ClassLoader来载入。

**委托机制**：先委托父装载器寻找目标类，只有在找不到的情况下才从自己的类路径中查找并装载目标类。这一点是从安全角度来考虑的。

JVM在运行时会产生三个装载器字节码文件：**根装载器、ExtClassLoader（扩展类装载器）和AppClassLoader（系统类装载器）**。

- **根装载器**：不是ClassLoader的子类，由于他是使用C++来编写的，因此在JAVA中看不到他。根装载器来复制装载jre的核心类库，如jre目标下的rt.jar、charsets.jar等。
- **ExtClassLoader**：是ClassLoader的子类，负责装载 jre 扩展目录ext中的jar类包
- **AppClassLoader**：是ClassLoader的子类，负责装载 classpath 路径下的类

这三个装载器之间存在着父子层级的关系：

根装载器是ExtClassLoader的父装载器，而ExtClassLoader是AppClassLoader的父装载器。

在默认情况下使用AppClassLoader装载应用程序的类。

### 4.Java当中的class对象是什么？有几种获取的方法？

**Class**其实就是类的类型，比如字符串类型就是String，整形类型就是Integer，String和Integer类型就是Class。

Class对象的常用方法介绍：

- getName()    获得类中完整名称
- getDeclaredFields()    获得类中的所有属性
- getDeclaredMethods()     获得类中所有的方法
- getConstructors()     获得类构造方法
- newInstance()     实例化类对象注：newInstance()方法为实例化空参数的类对象时使用。

获取Class对象的有三种方法：

1、利用Class类**forName()**静态方法传入一个类的全路径来返回相对应的类的Class对象。

2、将实例化的类对象转型为Object对象，然后通过Object对象的getClass()方法来返回对应类的Class对象。

3、通过类的class属性返回类的Class对象。

在java反射机制中我们就是使用第一种方式来获取对应的class对象，并使用 newInstance() 方法来得到对应类的类对象，这样便间接的通过代码的方法获取到了对应类的对象。

### 5.Object类有哪些方法?

Object类是一个特殊的类，是所有类的父类。它主要提供了以下11个方法：

```java
public final native Class<?> getClass();//native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许子类重写。

public native int hashCode(); //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。 

public boolean equals(Object obj);//用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等。

protected native Object clone() throws CloneNotSupportedException;//naitive方法，用于创建并返回当前对象的一份拷贝。一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，x.clone().getClass() == x.getClass() 为true。Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生 CloneNotSupportedException异常。

public String toString();//返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。

public final native void notify();//native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。

public final native void notifyAll();//native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。

public final native void wait(long timeout) throws InterruptedException;//native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。

public final void wait(long timeout, int nanos) throws InterruptedException;//多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。

public final void wait() throws InterruptedException;//跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念

protected void finalize() throws Throwable { }//实例被垃圾回收器回收的时候触发的操作
```

### 6.hashCode与equals的关系 （你重写过 hashcode 和 equals 么，为什么重写equals时必须重写hashCode方法？）

**hashCode()** 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode() 定义在JDK的Object.java中，这就意味着Java中的任何类都包含有hashCode() 函数。另外需要注意的是： Object 的 hashcode 方法是本地方法，也就是用 c 语言或 c++ 实现的，该方法通常用来将 对象的内存地址 转换为整数之后返回。

HashSet 中保存数据的时候会首先调用 **hashCode** 方法获取对象 hashcode 值，然后和已经保存的数据的 hashcode 进行比较，假如出现重复的话就得继续调用 **equals** 方法来检查 hashcode 相同的两个对象是否内存地址也一致，如何继续相同则证明两个对象时同一个对象，因此 hashSet 就不会让其加入。如果不同的话就证明是 hashcode 出现冲突，则散列到其他位置即可。这样就大大减少了调用 equals 的次数，也就大大提高了执行速度。

hashCode()与equals()的相关关系：

1. 两个**对象相等**，则**hashcode一定也是相同**的；
2. 两个**对象相等**,对两个对象分别调用**equals方法都返回true**；
3. 两个对象hashcode相等，**不一定对象相同**；
4. 因此，**equals方法被覆盖过，则hashCode方法也必须被覆盖**，这样才能正确判断两个对象是否相等；
5. hashCode()的默认行为是对**堆**上的对象产生独特值。如果没有重写hashCode()，则**该class的两个对象无论如何都不会相等**（即使这两个对象指向相同的数据）

一句话总结：**保证可以正确判断两个对象是否相等**

### 7.==与equals的关系？

- == : 它的作用是判断两个对象的**地址是不是相等**。即**判断两个对象是不是同一个对象**。(**基本数据类型 == 比较的是值**，**引用数据类型 == 比较的是内存地址**)

- equals() : 它的作用也是**判断两个对象是否相等**。但它一般有两种使用情况：
  - 类**没有覆盖equals()**方法。则通过equals()比较该类的两个对象时，等价于通过“==”比较这两个对象（底层调用 == ）。 
  - 类**覆盖了equals()**方法。一般，我们都**覆盖equals()方法来比较两个对象的内容相等**（例如String）；若它们的内容相等， 则返回true(即认为这两个对象相等)。**覆盖了 equals 方法之后还需要覆盖 hashcode 方法**，具体内容查看上面的问题。

### 8.重载和重写的区别?

**重载**： 发生在同一个类中，**方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同**，发生在编译时。

**重写**： 发生在父子类中，**方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类， 访问修饰符范围大于等于父类（两同两小一大）**；如果父类方法访问修饰符为 private 则子类就不能重写该方法。

### 9.String 和 StringBuﬀer、StringBuilder 的区别是什么？String 为什 么是不可变的？

- String为不可变类型，因为 String 类使用 **final关键字修饰的字符数组保存字符串**；StringBuffer、StringBuilder为可变类型，也是使用字符数组，但没有 final 关键字修饰。
- String、StringBuffer 是线程安全的，String是不可变类型所以线程安全，StringBuffer对方法添加了**同步锁**所以是线程安全的。；StringBuilder 是线程不安全的。
- String每次改变都会产生一个新的 String 对象，性能较低；StringBuffer、StringBuilder直接对对象本身进行操作，因此性能高。

### 10.自动装箱与拆箱

**装箱**：将基本类型用它们对应的引用类型包装起来； 

**拆箱**：将包装类型转换为基本数据类型；

### 11.关于 ﬁnal 关键字的一些总结

ﬁnal关键字主要用在三个地方：**变量、方法、类**。

1. 对于一个**ﬁnal变量**，如果是**基本数据类型**的变量，则其**数值一旦在初始化之后便不能更改**；如果是**引用类型**的变量，则在对其**初始化之后便不能再让其指向另一个对象**。
2. 当用ﬁnal修饰一个**类**时，表明**这个类不能被继承**。ﬁnal类中的所有成员方法都会被隐式地指定为ﬁnal方法。
3. 使用ﬁnal**方法**的原因有两个。第一个原因是把方法锁定，**以防任何继承类修改它的含义**；第二个原因是效率。 在早期的Java实现版本中，会将ﬁnal方法转为内嵌调用。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升（现在的Java版本已经不需要使用ﬁnal方法进行这些优化了）。**类中所有的private方法都隐式地指定为ﬁanl**。

### 12.Java 中的异常处理

Java异常类层次结构图：

![41](/Users/wx/project/interview/docs/秘籍/images/41.png)

在 Java 中，所有的异常都有一个共同的祖先java.lang包中的 **Throwable类**。

Throwable： 有两个重要的子类： **Exception（异常）** 和 **Error（错误）** ，二者都是 Java 异常处理的重要子类，各自都包含大量子类。

**Error（错误）**:是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而表示代码运行时 JVM（Java 虚拟机）出现的问题。例如，Java虚拟机运行错误（Virtual MachineError），当 JVM 不再有继续执行操作所需的内存资源时，将出现 OutOfMemoryError。这些异常发生时，Java虚拟机（JVM）一 般会选择线程终止。

这些错误表示故障发生于虚拟机自身、或者发生在虚拟机试图执行应用时，如Java虚拟机运行错误（Virtual MachineError）、类定义错误（NoClassDefFoundError）等。这些错误是不可查的，因为它们在应用程序的控制和 处理能力之 外，而且绝大多数是程序运行时不允许出现的状况。对于设计合理的应用程序来说，即使确实发生了错误，本质上也不应该试图去处理它所引起的异常状况。在 Java中，错误通过Error的子类描述。

**Exception（异常）**:是程序本身可以处理的异常。Exception 类有一个重要的子类RuntimeException。 **RuntimeException 异常**由Java虚拟机抛出。NullPointerException（要访问的变量没有引用任何对象时，抛出该 异常）、ArithmeticException（算术运算异常，一个整数除以0时，抛出该异常）和 ArrayIndexOutOfBoundsException （下标越界异常）。

异常和错误的区别：**异常能被程序本身可以处理，错误是无法处理**。

### 13.获取用键盘输入常用的的两种方法?

方法1：通过 Scanner

```java
Scanner input = new Scanner(System.in); 
String s = input.nextLine(); 
input.close();
```

方法2：通过 BuﬀeredReader

```java
BufferedReader input = new BufferedReader(new InputStreamReader(System.in)); 
String s = input.readLine();
```

### 14.接口和抽象类的区别是什么?

1. 接口的方法默认是 **public**，所有方法在接口中不能有实现(Java 8 开始接口方法可以有**默认实现**），抽象类可以有非抽象的方法;
2. 接口中的实例变量默认是 **ﬁnal** 类型的常量，而抽象类中则不一定；
3. 一个类可以**实现多个接口**，但最多只能**继承一个抽象类**（单继承多实现）；
4. 一个类实现接口的话要实现接口的**所有方法**，而抽象类不一定；
5. 接口不能用 new 实例化，但可以**声明**，但是必须引用一个实现该接口的对象。
6. 从**设计层面**来说，抽象是**对类的抽象**，是一种模板设计，接口是**行为的抽象**，是一种行为的规范。

备注:在JDK8中，接口也可以定义静态方法，可以直接用接口名调用。实现类和实现是不可以调用的。如果同时实现两个接口，接口中定义了一样的默认方法，必须重写，不然会报错。

**异常处理总结:**

- **try 块**：用于捕获异常。其后可接零个或多个catch块，如果没有catch块，则必须跟一个ﬁnally块。
- **catch 块**：用于处理try捕获到的异常。 
- **ﬁnally 块**：无论是否捕获或处理异常，ﬁnally块里的语句都会被执行。当在try块或catch块中遇到return语句时，ﬁnally语句块将在方法**返回之前**被执行。

**在以下4种特殊情况下，ﬁnally块不会被执行：**

1. 在ﬁnally语句块中发生了异常。
2. 在前面的代码中用了System.exit()退出程序。
3. 程序所在的线程死亡。
4. 关闭CPU。

### 15.什么是泛型？为什么要使用泛型？



## 并发

### 0.有没有在项目中实际使用多线程？

有

在鑫课堂项目中为了实现选课功能的**分布式事务**实现，采用了消息队列来实现分布式事务的AP（可用性和分区容忍性）特性，并满足最终一致性。

为了将多个**选课消息**从**订单服务**发送到**学习服务**中进行处理，需要用到**定时任务**处理的方法，即在指定的时间间隔内重复扫描**任务消息表**并将其发送到消息队列中去。

因为项目是使用 Spring 全家桶来实现的，因此定时任务采用 Spring Task 来实现任务调度。但是 Spring Task 默认是**串行执行的**，无法实现将多个任务**并行执行**，降低了系统运行的性能。因此就使用**线程池**来实现**多线程任务调度**。

创建线程池的方法使用 Executor 的 ThreadPoolTaskScheduler。使用 @Configuration @EnableScheduling 注解来实现多线程的使用。

```java
    /**
     * 创建一个线程池
     * @return
     */
    @Bean
    public ThreadPoolTaskScheduler taskScheduler() {
        ThreadPoolTaskScheduler scheduler = new ThreadPoolTaskScheduler();
        // 初始化线程池
        scheduler.initialize();
        // 线程池容量
        scheduler.setPoolSize(corePoolSize);
        return scheduler;
    }
```

### 1.并发编程三要素

**并发编程三要素：原子性，有序性，可见性**

- **原子性**：一个不可再被分割的颗粒。 原子性指的是一个或多个操作要么全部执行成功要么全部执行失败。synchronized 或 Lock能够保证原子性。
- **有序性**：程序执行的顺序按照代码的先后顺序执行。（ 处理器可能会对指令进行重排序）。synchronized 或 Lock能够保证可见性。
- **可见性**： 一个线程对共享变量的修改,另一个线程能够立刻看到。synchronized 或 Lock 以及 volatile 关键字能够保证有序性。

### 2.多线程的好处（为什么要使用多线程？）

**1）发挥多核CPU的优势**

多线程，可以真正发挥出多核CPU的优势来，达到充分利用CPU的目的，采用多线程的方式去同时完成几件事情而不互相干扰。

**2）防止阻塞**

从程序运行效率的角度来看，单核CPU不但不会发挥出多线程的优势，反而会因为在单核CPU上运行多线程导致线程上下文的切换，而降低程序整体的效率。但是单核CPU我们还是要应用多线程，就是为了防止阻塞。试想，如果单核CPU使用单线程，那么只要这个线程阻塞了，比方说远程读取某个数据吧，对端迟迟未返回又没有设置超时时间，那么你的整个程序在数据返回回来之前就停止运行了。多线程可以防止这个问题，多条线程同时运行，哪怕一条线程的代码执行读取数据阻塞，也不会影响其它任务的执行。

**3）便于建模**

这是另外一个没有这么明显的优点了。假设有一个大的任务A，单线程编程，那么就要考虑很多，建立整个程序模型比较麻烦。但是如果把这个大的任务A分解成几个小任务，任务B、任务C、任务D，分别建立程序模型，并通过多线程分别运行这几个任务，那就简单很多了。

### 3.创建线程的方法？

- **继承Thread类**创建线程类
  - 定义Thread类的子类，并重写该类的**run方法**，该run方法的方法体就代表了线程要完成的任务。因此把run()方法称为执行体。
  - 创建Thread子类的实例，即创建了线程对象。
  - 调用线程对象的**start()**方法来启动该线程。
- 通过**Runnable接口**创建线程类
  - 定义runnable接口的实现类，并重写该接口的**run()方法**，该run()方法的方法体同样是该线程的线程执行体。
  - 创建 Runnable实现类的实例，并依此实例作为Thread的target来创建Thread对象，该Thread对象才是真正的线程对象。
  - 调用线程对象的**start()**方法来启动该线程。
- 通过**Callable接口和Future**创建线程
  - 创建Callable接口的实现类，并实现**call()**方法，该call()方法将作为线程执行体，并且有返回值。
  - 创建Callable实现类的实例，使用FutureTask类来包装Callable对象，该FutureTask对象封装了该Callable对象的call()方法的返回值。
  - 使用FutureTask对象作为Thread对象的target创建并启动新线程。
  - 调用FutureTask对象的get()方法来获得子线程执行结束后的返回值。
  - **Future就是对于具体的Runnable或者Callable任务的执行结果进行取消、查询是否完成、获取结果**。必要时可以通过**get方法**获取执行结果，该方法会阻塞直到任务返回结果。
  - FutureTask一个可取消的异步计算，**FutureTask 实现了Future的基本方法**，提供 start cancel 操作，可以查询计算是否已经完成，并且可以获取计算的结果。结果只可以在计算完成之后获取，get方法会阻塞当计算没有完成的时候，一旦计算已经完成，那么计算就不能再次启动或是取消。

创建线程的三种方式的对比：

1、采用**实现Runnable、Callable接口**的方式创见多线程时

**优势是**：

- 线程类只是实现了Runnable接口或Callable接口，还可以继承其他类。

- 在这种方式下，多个线程可以共享同一个target对象，所以非常适合多个相同线程来处理同一份资源的情况，从而可以将CPU、代码和数据分开，形成清晰的模型，较好地体现了面向对象的思想。

**劣势是**：

- 编程稍微复杂，如果要访问当前线程，则必须使用Thread.currentThread()方法。

2、使用**继承Thread类**的方式创建多线程时

**优势是**：

- 编写简单，如果需要访问当前线程，则无需使用Thread.currentThread()方法，直接使用this即可获得当前线程。

**劣势是**：

- 线程类已经继承了Thread类，所以不能再继承其他父类。

### 4.线程的生命周期和状态?

Java 线程在运行的生命周期中的指定时刻只可能处于下面 6 种不同状态的其中一个状态：

![7](/Users/wx/project/interview/docs/秘籍/images/7.png)

线程在生命周期中并不是固定处于某一个状态而是随着代码的执行在不同状态之间切换。Java 线程状态变迁如下图所示:

![8](/Users/wx/project/interview/docs/秘籍/images/8.png)

线程创建之后它将处于 **NEW（新建）** 状态，调用 `start()` 方法后开始运行，线程这时候处于 **READY（可运行）** 状态。可运行状态的线程获得了 CPU 时间片（timeslice）后就处于 **RUNNING（运行）** 状态（操作系统隐藏 Java 虚拟机（JVM）中的 RUNNABLE 和 RUNNING 状态，它只能看到 RUNNABLE 状态）当线程执行 `wait()`方法之后，线程进入 **WAITING（等待）** 状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 **TIME_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制，比如通过 `sleep（long millis）`方法或 `wait（long millis）`方法可以将 Java 线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。线程在执行 Runnable 的`run()`方法之后将会进入到 **TERMINATED（终止）** 状态。

### 6.线程池以及创建线程池的方法

线程池是一种池化技术，**池化技术的思想主要是为了减少每次获取资源的消耗，提高对资源的利用率。线程池提供了一种限制和管理资源（包括执行一个任务）的机制**。 每个线程池还维护一些基本统计信息，例如已完成任务的数量。

**使用线程池的好处**：

- **降低资源消耗**。通过重复利用已创建的线程降低线程创建和销毁造成的消耗。
- **提高响应速度**。当任务到达时，任务可以不需要的等到线程创建就能立即执行。
- **提高线程的可管理性**。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

创建线程池的方法：

《阿里巴巴Java开发手册》中强制线程池不允 许使用 Executors 去创建，而是通过 **ThreadPoolExecutor 构造函数**的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险。

**1、通过Executor 框架的工具类Executors来实现**。

- **newFixedThreadPool**：定长线程池，每当提交一个任务就创建一个线程，直到达到线程池的最大数量，这时线程数量不再变化，当线程发生错误结束时，线程池会补充一个新的线程。适用于为了满足资源管理需求，而需要限制当前线程数量的应用场景。它适用于负载比较 重的服务器；
- **newCachedThreadPool**：可缓存的线程池，如果线程池的容量超过了任务数，自动回收空闲线程，任务增加时可以自动添加新线程，线程池的容量不限制。适用于执行很多的短期异步任务的小程序，或者是负载较轻的服务器；
- **newScheduledThreadPool**：定长线程池，可执行周期性的任务。适用于需要多个后台执行周期任务，同时为了满足资源管理需求而需要限 制后台线程的数量的应用场景；
- **newSingleThreadExecutor**：单线程的线程池，线程异常结束，会创建一个新的线程，能确保任务按提交顺序执行。适用于需要保证顺序地执行各个任务并且在任意时间点，不会有多个线程是活动的 应用场景。
- **newSingleThreadScheduledExecutor**：单线程可执行周期性任务的线程池。适用于需要单个后台线程执行周期任务，同时保证顺序地执行各个任务 的应用场景。
- **newWorkStealingPool**：任务窃取线程池，不保证执行顺序，适合任务耗时差异较大。线程池中有多个线程队列，有的线程队列中有大量的比较耗时的任务堆积，而有的线程队列却是空的，就存在有的线程处于饥饿状态，当一个线程处于饥饿状态时，它就会去其它的线程队列中窃取任务。解决饥饿导致的效率问题。默认创建的并行 level 是 CPU 的核数。主线程结束，即使线程池有任务也会立即停止。

Executors 返回线程池对象的弊端如下：

- FixedThreadPool 和 SingleThreadExecutor ： 允许请求的队列长度为 Integer.MAX_VALUE,可能堆积大量的请 求，从而导致OOM。

- CachedThreadPool 和 ScheduledThreadPool ： 允许创建的线程数量为 Integer.MAX_VALUE ，可能会创建大量 线程，从而导致OOM。

**2、ThreadPoolExecutor的构造函数创建**

我们可以自己直接调用 ThreadPoolExecutor 的构造函数来自己创建线程池。在创建的同时，给 BlockQueue 指定容 量就可以了。示例如下：

```java
private static ExecutorService executor = new ThreadPoolExecutor(13, 13, 60L, TimeUnit.SECONDS, new ArrayBlockingQueue(13));
```

这种情况下，一旦提交的线程数超过当前可用线程数时，就会抛出 **java.util.concurrent.RejectedExecutionException**，这是因为当前线程池使用的队列是有边界队列，队列已经满了便无法继续处理新的请求。但是异常（Exception）总比发生错误（Error）要好。

**3、使用开源类库**

Hollis 大佬之前在他的文章中也提到了：“除了自己定义ThreadPoolExecutor外。还有其他方法。这个时候第一时间就应该想到**开源类库**，如 **apache和guava**等。”他推荐使用 **guava提供的ThreadFactoryBuilder** 来创建线程池。下面 是参考他的代码示例：

```java
public class ExecutorsDemo {
    private static ThreadFactory namedThreadFactory = new ThreadFactoryBuilder().setNameFormat("demo-pool-%d").build();
    private static ExecutorService pool = new ThreadPoolExecutor(5, 200, 0L, TimeUnit.MILLISECONDS, new 		LinkedBlockingQueue<Runnable>(1024), namedThreadFactory, new ThreadPoolExecutor.AbortPolicy());
    public static void main(String[] args) {
        for (int i = 0; i < Integer.MAX_VALUE; i++) { 
            pool.execute(new SubThread()); 
        }
    }
}
```

通过上述方式创建线程时，不仅可以避免OOM的问题，还可以自定义线程名称，更加方便的在出错的时候溯源。

### 7.Java中的并发工具类

- **CountDownLatch**允许一个或多个线程等待其他线程完成操作。`CountDownLatch`传入一个N当做计数器，每次执行countDown的时候N就会减1，`CountDownLatch`的await方法就会阻塞当前线程，直到N变成零。countDown可以是一个线程中的N个步骤或者是N个线程。
- **CyclicBarrier ** 同步屏障。让一组线程到达一个屏障（或者是同步点）的时候被阻塞，直到最后一个线程到达屏障，屏障才会打开，所有的线程继续往下执行。
- **Semaphore**（信号量）用来控制同时访问特定资源的线程数量，通过协调各个线程，以保证合理的使用公共资源。
- **Exchanger**是个用于线程间协作的工具类，用于线程之间的数据交换。它提供一个同步点，在这个同步点，两个线程可以交换彼此的数据。第一个线程先执行`exchange()`方法，第二个线程也执行`exchange()`方法，当两个线程同时到达同步点，这两个线程就可以交换数据。如果一个线程一直没有执行`exchange()`方法，那么会一直等下去，如果担心特殊情况，可以使用`exchange(V v,longtimeout, TimeUnit unit)`设置最大等待时间。

CyclicBarrier 和 CountDownLatch 的区别：

1. CountDownBatch 的计数器只能使用一次
2. CyclicBarrier 的计数器可以使用 reset() 方法重置，因此 CyclicBarrier 可以实现更加复杂的功能。

### 8.synchronized 关键字详解

synchronized关键字解决的是**多个线程之间访问资源的同步性**，synchronized关键字可以保证**被它修饰的方法或者代码块在任意时刻只能有一个线程执行**。但是 synchronized 属于重量级锁，很多时候会引起性能问题。

**synchronized关键字最主要的三种使用方式：**

- **修饰实例方法:** 作用于当前**对象实例**加锁，进入同步代码前要获得当前对象实例的锁；
- **修饰静态方法:** 也就是给**当前类**加锁，会作用于类的所有对象实例，因为静态成员不属于任何一个实例对象，是类成员（ static 表明这是该类的一个静态资源，不管new了多少个对象，只有一份）。所以如果一个线程 A 调用一个实例对象的非静态 synchronized 方法，而线程 B 需要调用这个实例对象所属类的静态 synchronized 方法，是允许的，不会发生互斥现象，**因为访问静态 synchronized 方法占用的锁是当前类的锁，而访问非静态 synchronized 方法占用的锁是当前实例对象锁**。
- **修饰代码块:** 指定加锁对象，对**给定对象**加锁，进入同步代码库前要获得给定对象的锁。

**总结：** synchronized 关键字加到 static 静态方法和 synchronized(class)代码块上都是是给 Class 类上锁。synchronized 关键字加到实例方法上是给对象实例上锁。尽量不要使用 synchronized(String a) 因为JVM中，字符串常量池具有缓存功能！

介绍完 sychronized 之后一般会要求手写一个单例模式，这里考察的是多重校验加锁的版本。

**双重校验锁实现对象单例（线程安全，懒汉式）**

```java
public class Singleton {
    // 注意这里得使用 volatile 关键字来修饰
    // 主要原因在于uniqueInstance = new Singleton();这句分三步执行的
    // 使用 volatile 可以禁止 JVM 的指令重排，保证在多线程环境下也能正常运行。
    private volatile static Singleton uniqueInstance;
    // 原先的构造函数私有
    private Singleton() {}
    // 自定义一个public的获取对象的方法
    public static Singleton getUniqueInstance() {
       //先判断对象是否已经实例过，没有实例化过才进入加锁代码
        if (uniqueInstance == null) {
            //类对象加锁
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    // 下面这句分三步执行
                    // 1.为 uniqueInstance分配内存空间
                    // 2.初始化 uniqueInstance
                    // 3.将 uniqueInstance 指向分配的内存地址
                    uniqueInstance = new Singleton();
                }
            }
        }
        return uniqueInstance;
    }
}
```

synchronized 关键字底层原理属于 JVM 层面。synchronized 同步语句块的实现使用的是 monitorenter 和 monitorexit 指令，其中 monitorenter 指令指向同步代码块的开始位置，monitorexit 指令则指明同步代码块的结束位置。

JDK1.6 对锁的实现引入了大量的优化，如**偏向锁、轻量级锁、自旋锁、适应性自旋锁、锁消除、锁粗化**等技术来减少锁操作的开销。

### 9.volatile 关键字详解

得先介绍一下 Java 的内存模型：

在当前的 Java 内存模型下，线程可以把变量保存**本地内存**（比如机器的寄存器）中，而不是直接在主存中进行读写。这就可能造成一个线程在主存中修改了一个变量的值，而另外一个线程还继续使用它在寄存器中的变量值的拷贝，造成**数据的不一致**。

要解决这个问题，就需要把变量声明为**volatile**，这就指示 JVM，这个变量是不稳定的，每次使用它都到**主存**中进行读取。

 **volatile** 关键字的主要作用就是保证**变量的可见性**然后还有一个作用是**防止指令重排序**。

### 10.CAS 技术详解

**CAS（Compare-and-Swap），即比较并替换**，是一种实现并发算法时常用到的技术。

像 synchronized 这种独占锁属于**悲观锁**，它是在假设一定会发生冲突的，那么加锁恰好有用。

除此之外，还有**乐观锁**，**乐观锁**的含义就是**假设没有发生冲突，那么我正好可以进行某项操作，如果要是发生冲突呢，那我就重试直到成功**，**乐观锁最常见的就是`CAS`。**

CAS需要有3个操作数：**内存地址V，旧的预期值A，即将要更新的目标值B**。

CAS指令执行时，**当且仅当内存地址V的值与预期值A相等时，将内存地址V的值修改为B，否则就什么都不做**。整个比较并替换的操作是一个**原子操作**。

CAS虽然很高效的解决了原子操作问题，但是CAS仍然存在三大问题。

1. **循环时间长开销很大。**我们可以看到getAndAddInt方法执行时，如果CAS失败，会一直进行尝试。如果CAS长时间一直不成功，可能会给CPU带来很大的开销。
2. **只能保证一个共享变量的原子操作。**当对一个共享变量执行操作时，我们可以使用循环CAS的方式来保证原子操作，但是对多个共享变量操作时，循环CAS就无法保证操作的原子性，这个时候就可以用锁来保证原子性。
3. **ABA问题。**如果内存地址V初次读取的值是A，如果在这段期间它的值曾经被改成了B，后来又被改回为A，那CAS操作就会误认为它从来没有被改变过。这个漏洞称为CAS操作的“ABA”问题。Java并发包为了解决这个问题，提供了一个带有标记的原子引用类 **AtomicStampedReference**，它可以通过控制变量值的**版本**来保证CAS的正确性。因此，在使用CAS前要考虑清楚“ABA”问题是否会影响程序并发的正确性，如果需要解决ABA问题，改用传统的互斥同步可能会比原子类更高效。

### 11.AQS 类介绍

AQS的全称为（AbstractQueuedSynchronizer），这个类在 java.util.concurrent.locks 包下面。

AQS是一个用来**构建锁和同步器**的框架，使用AQS能简单且高效地构造出应用广泛的大量的同步器，比如我们提到的ReentrantLock，Semaphore，其他的诸如ReentrantReadWriteLock，SynchronousQueue，FutureTask等等皆是基于AQS的。当然，我们自己也能利用AQS非常轻松容易地构造出符合我们自己需求的同步器。

AQS核心思想是，**如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的工作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制AQS是用CLH队列（CLH(Craig,Landin,and Hagersten)队列是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系）。AQS是将每条请求共享资源的线程封装成一个CLH锁队列的一个结点（Node）来实现锁的分配。）锁实现的，即将暂时获取不到锁的线程加入到队列中。**

![42](/Users/wx/project/interview/docs/秘籍/images/42.png)



**AQS定义两种资源共享方式**

- **Exclusive**（独占）：只有一个线程能执行，如ReentrantLock。又可分为公平锁和非公平锁：
  - 公平锁：按照线程在队列中的排队顺序，先到者先拿到锁
  - 非公平锁：当线程要获取锁时，无视队列顺序直接去抢锁，谁抢到就是谁的
- **Share**（共享）：多个线程可同时执行，如Semaphore/CountDownLatch。Semaphore、CountDownLatch、 CyclicBarrier、ReadWriteLock 我们都会在后面讲到。

ReentrantReadWriteLock 可以看成是组合式，因为ReentrantReadWriteLock也就是读写锁允许多个线程同时对某一资源进行读。

不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源 state 的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。

### 12.ReadWriteLock

Read-Write Lock Pattern将读取与写入分开处理，在读取数据之前必须获取用来读取的锁定，而写入的时候必须获取用来写入的锁定。因为读取时实例的状态不会改变，所以多个线程可以同时读取；但是，写入会改变实例的状态，所以当有一个线程写入的时候，其它线程既不能读取与不能写入。

### 13.synchronized 和 ReentrantLock 的区别

两者的共同点：

- 都是**协调多线程对共享对象、变量**的访问
- 都是**可重入锁**，同一线程可以多次获得同一个锁（自己可以再次获取自己的内部锁）
- 都保证了**可见性**和**互斥性**

两者的不同点：

- ReentrantLock 是 **API** 级别的， synchronized 是 **JVM** 级别的；
- **底层实现**不一样， synchronized 是**同步阻塞**，使用的是**悲观并发策略**，ReentrantLock是 **同步非阻塞**，采用的是**乐观并发策略**；
- ReentrantLock **显示获得、释放锁**，synchronized **隐式获得释放锁**；
- ReentrantLock **可响应中断、可轮回**，synchronized 是**不可以响应中断**的，为处理锁的不可用性提供了更高的灵活性；
- ReentrantLock 可以实现**公平锁**，所谓的公平锁就是先等待的线程先获得锁；
- ReentrantLock 通过 **Condition** 可以绑定多个条件，线程对象可以注册在指定的Condition中，从而可以有选择性的进行线程通知，在调度线程上更加灵活；

### 14.synchronized 和 volatile 的区别

- **volatile关键字**是线程同步的**轻量级实现**，所以**volatile性能肯定比synchronized关键字要好**。但是**volatile关键字只能用于变量而synchronized关键字可以修饰方法以及代码块**。**实际开发中使用 synchronized 关键字的场景还是更多一些**。
- 多线程访问volatile关键字**不会发生阻塞**，而synchronized关键字可能**会发生阻塞**。
- volatile关键字能保证数据的**可见性**，但不能保证数据的原子性。synchronized关键字**两者都能保证**。
- volatile关键字主要用于解决变量在多个线程之间的**可见性**，而 synchronized关键字解决的是多个线程之间访问资源的**同步性**。

### 15.synchronized、volatile、CAS 比较

- **synchronized** 是 **悲观锁**，属于**抢占式**，会引起其他线程**阻塞**。
- **volatile**提供多线程共享变量**可见性**和**禁止指令重排序**优化。
- **CAS**是基于**冲突检测**的**乐观锁（非阻塞）**。

### 16.sleep 方法和 wait 方法有什么区别?

sleep方法和wait方法都可以用来放弃CPU一定的时间，不同点在于如果线程持有某个对象的监视器，sleep方法不会放弃这个对象的监视器，wait方法会放弃这个对象的监视器。

### 17.ThreadLocal 是什么？有什么用？

ThreadLocal是一个本地线程副本变量工具类。主要用于将私有线程和该线程存放的副本对象做一个映射，各个线程之间的变量互不干扰，在高并发场景下，可以实现无状态的调用，特别适用于各个线程依赖不通的变量值完成操作的场景。

简单说ThreadLocal就是一种以空间换时间的做法，在每个Thread里面维护了一个以开地址法实现的ThreadLocal.ThreadLocalMap，把数据进行隔离，数据不共享，自然就没有线程安全方面的问题了。

### 18.为什么 wait()方法和 notify()/notifyAll()方法要在同步块中被调用？

这是JDK强制的，wait()方法和notify()/notifyAll()方法在调用前都必须先获得对象的锁

### 19.多线程同步有哪几种方法？

Synchronized关键字，Lock锁实现，分布式锁等。

### 20.线程的调度策略

线程调度器选择优先级最高的线程运行，但是，如果发生以下情况，就会终止线程的运行：

（1）线程体中调用了yield方法让出了对cpu的占用权利

（2）线程体中调用了sleep方法使线程进入睡眠状态

（3）线程由于IO操作受到阻塞

（4）另外一个更高优先级线程出现

（5）在支持时间片的系统中，该线程的时间片用完

### 21.ConcurrentHashMap 的并发度是什么？

ConcurrentHashMap的并发度就是segment的大小，默认为16，这意味着最多同时可以有16条线程操作ConcurrentHashMap，这也是ConcurrentHashMap对Hashtable的最大优势，任何情况下，Hashtable能同时有两条线程获取Hashtable中的数据。

### 22.Linux 环境下如何查找哪个线程使用 CPU 最长

使用 top 命令 top -H -p pid

### 23.Java 死锁以及如何避免？

Java中的死锁是一种编程情况，其中两个或多个线程被永久阻塞，Java死锁情况出现至少两个线程或更多资源。

Java发生死锁的根本原因是：在申请锁时发生了交叉闭环申请。

死锁的原因

1）是多个线程涉及到多个锁，这些锁存在着交叉，所以可能会导致了一个锁依赖的闭环。

例如：线程在获得了锁A并且没有释放的情况下去申请锁B，这时，另一个线程已经获得了锁B，在释放锁B之前又要先获得锁A，因此闭环发生，陷入死锁循环。

2）默认的锁申请操作是阻塞的。

所以要避免死锁，就要在一遇到多个对象锁交叉的情况，就要仔细审查这几个对象的类中的所有方法，是否存在着导致锁依赖的环路的可能性。 总之是尽量避免在一个同步方法中调用其它对象的延时方法和同步方法。

### 24.怎么唤醒一个阻塞的线程？

如果线程是因为调用了wait()、sleep()或者join()方法而导致的阻塞，可以中断线程，并且通过抛出InterruptedException来唤醒它；如果线程遇到了IO阻塞，无能为力，因为IO是操作系统实现的，Java代码并没有办法直接接触到操作系统。

### 25.不可变对象对多线程有什么帮助？

前面有提到过的一个问题，不可变对象保证了对象的内存可见性，对不可变对象的读取不需要进行额外的同步手段，提升了代码执行效率。

### 26.什么是多线程的上下文切换？

多线程的上下文切换是指CPU控制权由一个已经正在运行的线程切换到另外一个就绪并等待获取CPU执行权的线程的过程。

### 27.如果你提交任务时，线程池队列已满，这时会发生什么？

这里区分一下：

- 如果使用的是无界队列LinkedBlockingQueue，也就是无界队列的话，没关系，继续添加任务到阻塞队列中等待执行，因为LinkedBlockingQueue可以近乎认为是一个无穷大的队列，可以无限存放任务。
- 如果使用的是有界队列比如ArrayBlockingQueue，任务首先会被添加到ArrayBlockingQueue中，ArrayBlockingQueue满了，会根据maximumPoolSize的值增加线程数量，如果增加了线程数量还是处理不过来，ArrayBlockingQueue继续满，那么则会使用拒绝策略RejectedExecutionHandler处理满了的任务，默认是AbortPolicy。

### 28.Java 中用到的线程调度算法是什么？

抢占式。一个线程用完CPU之后，操作系统会根据线程优先级、线程饥饿情况等数据算出一个总的优先级并分配下一个时间片给某个线程执行。

### 29.什么是线程调度器(Thread Scheduler)和时间分片(Time Slicing)？

线程调度器是一个操作系统服务，它负责为Runnable状态的线程分配CPU时间。一旦我们创建一个线程并启动它，它的执行便依赖于线程调度器的实现。

时间分片是指将可用的CPU时间分配给可用的Runnable线程的过程。分配CPU时间可以基于线程优先级或者线程等待的时间。线程调度并不受到Java虚拟机控制，所以由应用程序来控制它是更好的选择（也就是说不要让你的程序依赖于线程的优先级）。

### 30.什么是自旋锁？

自旋锁（spinlock）：是指当一个线程在获取锁的时候，如果锁已经被其它线程获取，那么该线程将循环等待，然后不断的判断锁是否能够被成功获取，直到获取到锁才会退出循环。

### 31.JavaConcurrencyAPI 中的 Lock 接口(Lockinterface)是什么？对比同步它有什么优势？

Lock接口比同步方法和同步块提供了更具扩展性的锁操作。他们允许更灵活的结构，可以具有完全不同的性质，并且可以支持多个相关类的条件对象。

它的优势有：

- 可以使锁更公平
- 可以使线程在等待锁的时候响应中断
- 可以让线程尝试获取锁，并在无法获取锁的时候立即返回或者等待一段时间
- 可以在不同的范围，以不同的顺序获取和释放锁

### 32.单例模式的线程安全性？

首先要说的是单例模式的线程安全意味着：某个类的实例在多线程环境下只会被创建一次出来。

单例模式有很多种的写法，我总结一下：

（1）饿汉式单例模式的写法：线程安全

（2）懒汉式单例模式的写法：非线程安全

（3）双检锁单例模式的写法：线程安全

### 33.Semaphore 有什么作用？

Semaphore就是一个信号量，它的作用是限制某段代码块的并发数。

Semaphore有一个构造函数，可以传入一个int型整数n，表示某段代码最多只有n个线程可以访问，如果超出了n，那么请等待，等到某个线程执行完毕这段代码块，下一个线程再进入。

由此可以看出如果Semaphore构造函数中传入的int型整数n=1，相当于变成了一个synchronized了。

### 34.Executors 类是什么？

Executors为Executor，ExecutorService，ScheduledExecutorService，ThreadFactory和Callable类提供了一些工具方法。

Executors可以用于方便的创建线程池

### 35.线程类的构造方法、静态块是被哪个线程调用的？

线程类的构造方法、静态块是被new这个线程类所在的线程所调用的，而run方法里面的代码才是被线程自身所调用的。

如果说上面的说法让你感到困惑，那么我举个例子，假设Thread2中new了Thread1，main函数中new了Thread2，那么：

（1）Thread2的构造方法、静态块是main线程调用的，Thread2的run()方法是Thread2自己调用的

（2）Thread1的构造方法、静态块是Thread2调用的，Thread1的run()方法是Thread1自己调用的

### 36.同步方法和同步块，哪个是更好的选择?

同步块，这意味着同步块之外的代码是异步执行的，这比同步整个方法更提升代码的效率。请知道一条原则：**同步的范围越小越好**。

### 37.Java 线程数过多会造成什么异常？

1)线程的生命周期开销非常高

2)消耗过多的CPU资源

如果可运行的线程数量多于可用处理器的数量，那么有线程将会被闲置。大量空闲的线程会占用许多内存，给垃圾回收器带来压力，而且大量的线程在竞争CPU资源时还将产生其他性能的开销。

3)降低稳定性

JVM在可创建线程的数量上存在一个限制，这个限制值将随着平台的不同而不同，并且承受着多个因素制约，包括JVM的启动参数、Thread构造函数中请求栈的大小，以及底层操作系统对线程的限制等。如果破坏了这些限制，那么可能抛出OutOfMemoryError异常。

### 38.截止JDK1.8版本,java并发框架支持锁包括?

读写锁、自旋锁、乐观锁

### 39.介绍一下Atomic 原子类

原子类就是**具有原子/原子操作特征的类**。

并发包 java.util.concurrent 的原子类都存放在 java.util.concurrent.atomic 下

### 40.JUC 包中的原子类是哪4类?

**1、基本类型**

使用原子的方式更新基本类型

- AtomicInteger：整形原子类 
- AtomicLong：长整型原子类 
- AtomicBoolean ：布尔型原子类

**2、数组类型**

使用原子的方式更新数组里的某个元素

- AtomicIntegerArray：整形数组原子类 
- AtomicLongArray：长整形数组原子类 
- AtomicReferenceArray ：引用类型数组原子类

**3、引用类型**

- AtomicReference：引用类型原子类 
- AtomicStampedRerence：原子更新引用类型里的字段原子类 
- AtomicMarkableReference ：原子更新带有标记位的引用类型

**4、对象的属性修改类型**

- AtomicIntegerFieldUpdater: 原子更新整形字段的更新器
- AtomicLongFieldUpdater：原子更新长整形字段的更新器 
- AtomicStampedReference：原子更新带有版本号的引用类型。该类将整数值与引用关联起来，可用于解决原子的更新数据和数据的版本号，可以解决使用 CAS 进行原子更新时可能出现的 ABA 问题。

### 41.AtomicInteger 类的原理

AtomicInteger 类主要利用 **CAS (compare and swap) + volatile 和 native** 方法来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。

### 42.非公平锁和公平锁的区别？

**公平锁**是老老实实在队列中排队获取锁。

**非公平锁**有**两次**抢锁机会，但是一旦进入队列，就永远排队。“**一朝排队，永远排队。**

非公平锁插队就是永远在插队，实际上只要它第一次加锁失败之后，一旦进入队列，跟公平锁就没有区别了。

### 43.什么情况会造成内存泄露？

在 Java 中，内存泄漏就是**内存中存在一些被分配的对象**，这些对象有下面两个特点：

- 首先，这些对象是可达的，即在有向图中，存在通路可以与其相连；

- 其次，这些对象是无用的，即程序以后不会再使用这些对象。

如果对象满足这两个条件，这些对象就可以判定为 Java 中的内存泄漏，这些对象不会被 GC 所回收，然而它却占用内存。

### 44.什么是线程死锁，如何解决？

产生死锁的条件有四个：

1. **互斥条件**：所谓互斥就是进程在某一时间内独占资源。 

2. **请求与保持条件**：一个进程因请求资源而阻塞时，对已获得的资源保持不放。 

3. **不剥夺条件**：进程已获得资源，在末使用完之前，不能强行剥夺。 

4. **循环等待条件**：若干进程之间形成一种头尾相接的循环等待资源关系。

假如上述四个条件都满足则证明产生了死锁。

线程死锁是**因为多线程访问共享资源，由于访问的顺序不当所造成的**，通常是一个线程锁定了一个资源A，而又想去锁定资源B；在另一个线程中，锁定了资源B，而又想去锁定资源A以完成自身的操作，两个线程都想得到对方的资源，而不愿释放自己的资源，造成两个线程都在等待，而无法执行的情况。

要解决死锁，可以从死锁的四个条件出发，只要破坏了一个必要条件，那么我们的死锁就解决了。在 java 中使用多线程的时候一定要考虑是否有死锁的问题。

## 容器

### 1.Arraylist 与 LinkedList 有什么不同?(注意加上从数据结构分析的内容)

两者主要的区别都是**由于底层的数据结构来决定的**，Arraylist底层实现是Object数组，LinkedList底层实现为双向链表。

1. **是否保证线程安全**： ArrayList 和 LinkedList **都是不同步**的，也就是不保证线程安全；
2. **底层数据结构**： Arraylist 底层使用的是**Object数组**；LinkedList 底层使用的是**双向链表**数据结构（注意双向链表和双向循环链表的区别：）；
3. **插入和删除是否受元素位置的影响**： ① **ArrayList 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响**。 比如：执行 add(E e) 方法的时候， ArrayList 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是O(1)。但是如果要在指定位置 i 插入和删除元素的话（ add(int index, E element) ）时 间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向 前移一位的操作。 ② **LinkedList 采用链表存储，所以插入，删除元素时间复杂度不受元素位置的影响，都是近似 O（1）而数组为近似 O（n）**。
4. **是否支持快速随机访问**： LinkedList 不支持高效的随机元素访问，而 ArrayList 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于 get(int index) 方法)。
5. **内存空间占用**： ArrayList的空间浪费主要体现在在list列表的结尾会预留一定的容量空间，而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList更多的空间（因为要存放直接后继和直接前驱以及数据）。

RandomAccess 接口不过是一个标识罢了。标识什么？ 标识实现这个接口的类具有随机访问功能。

list 的遍历方式选择：

- 实现了RandomAccess接口的list，优先选择普通for循环 ，其次foreach, 
- 未实现RandomAccess接口的ist， 优先选择iterator遍历（foreach遍历底层也是通过iterator实现的），大size 的数据，千万不要使用普通for循环。

ArrayList 默认的容量大小为**10**，假如用户自定义则按照用户传入的大小初始化。假如容量用完或者新加入的元素需要将 ArrayList 的大小直接增加到一个大小 minCapacity 则需要进行**扩容**，每次扩容为原先大小的**1.5倍**：

```java
//将oldCapacity 右移一位，其效果相当于oldCapacity / 2 (2的一次方)，
//我们知道位运算的速度远远快于整除运算，整句运算式的结果就是将新容量更新为旧容量的1.5倍，
int newCapacity = oldCapacity + (oldCapacity >> 1);
```

假如扩容之后的容量还小于 minCapacity 的话则将容量直接设置为 minCapacity。

注意 ArrayList 的大小最大为 Integer.MAX_VALUE;

### 2.HashMap 的底层实现

**1) JDK1.8之前**

JDK1.8 之前 HashMap 底层是 **数组和链表** 结合在一起使用也就是 **链表散列**（这里适合解决冲突的拉链法有关）。HashMap 通过 key 的 **hashCode** 经过**扰动函数**处理过后得到 hash 值，然后通过 **(n - 1) & hash** 判断当前元素存放的位置（这里的 n 指的时数组的长度），如果当前位置存在元素的话，就判断该元素与要存入的元素的 hash 值以及 key 是否相同，如果相同的话，直接覆盖，不相同则证明出现**冲突**，通过**拉链法**（所谓 “拉链法” 就是：**将链表和数组相结合。也就是说创建一个链表数组，数组中每一格就是一个链表。若遇到哈希冲突，则将冲突的值加到链表中即可**）解决冲突。

所谓**扰动函数**指的就是 **HashMap 的 hash 方法**。使用 hash 方法也就是扰动函数是**为了防止一些实现比较差的 hashCode() 方法，换句话说使用扰动函数之后可以减少碰撞**。

JDK 1.8 HashMap 的 **hash 方法源码**（JDK 1.8 的 hash方法 相比于 JDK 1.7 hash 方法更加简化，但是原理不变。）：

```java
static final int hash(Object key){
    int h;
    // key.hashCode()：返回散列值也就是hashcode 
    // ^ ：按位异或 
    // >>>:无符号右移，忽略符号位，空位都以0补齐(扰动)
    return (key == null)? 0 : (h=key.hashCode()) ^ (h >>> 16);
}
```

**2) JDK1.8之后**

相比于之前的版本， JDK1.8之后在解决哈希冲突时有了较大的变化，当链表长度大于**阈值（默认为8）**时，将链表转化为 **红黑树**，以减少搜索时间。

![24](/Users/wx/project/interview/docs/秘籍/images/24.png)

**TreeMap、TreeSet以及JDK1.8之后的HashMap**底层都用到了**红黑树**。红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。

### 3.既然谈到了红黑树,你给我手绘一个出来吧,然后简单讲一下自己对于红黑树的理解

![25](/Users/wx/project/interview/docs/秘籍/images/25.png)

红黑树特点:

1. **每个节点非红即黑**；
2. **根节点总是黑色的**；
3. 每个**叶子节点都是黑色的空节点**（NIL节点）；
4. 如果节点是红色的，则它的**子节点必须是黑色**的（反之不一定）；
5. 从根节点到叶节点或空子节点的每条路径，**必须包含相同数目的黑色节点（即相同的黑色高度）**

红黑树的应用：

**TreeMap、TreeSet以及JDK1.8之后的HashMap**底层都用到了红黑树。

 为什么要用红黑树:

简单来说红黑树就是为了解决二叉查找树的缺陷，因为二叉查找树在某些情况下会退化成一个线性结构。

### 5.红黑树这么优秀,为何 HashMap 不直接使用红黑树得了?

我们知道红黑树属于**（自）平衡二叉树**，但是为了保持“平衡”是需要付出**代价**的， 红黑树在插入新数据后可能需要通过**左旋，右旋、变色**这些操作来保持平衡，这费事啊。你说说我们引入红黑树就是为了查找数据快，如果链表长度很短的话，根本不需要引入红黑树的，你引入之后还要付出代价维持它的平衡。但是链表过长就不一样了。

至于为什么选 8 这个值呢？

**通过概率统计所得，这个值是综合查询成本和新增元素成本得出的最好的一个值。**

### 6.HashMap 和 Hashtable 的区别/HashSet 和 HashMap 区别

**HashMap 和 Hashtable 的区别**

HashMap 和 Hashtable 都可以存储键值对的数据。

1. **线程是否安全**： **HashMap 是非线程安全的，HashTable 是线程安全的**；HashTable 内部的方法基本都经过 **synchronized** 修饰。（如果你要保证线程安全的话就使用 **ConcurrentHashMap** 吧）；
2. **效率**： 因为线程安全的问题，HashMap 要比 HashTable 效率高一点。另外，HashTable 基本被淘汰，不要在代码中使用它；
3. **对Null key 和Null value的支持**： HashMap 中，**null 可以作为键，这样的键只有一个，可以有一个或多个键所对应的值为 null**。但是 HashTable 不支持，在 HashTable 中 put 进的键值只要有一个 null，直接抛出 NullPointerException。
4. **初始容量大小和每次扩充容量大小的不同** ： ①**创建时如果不指定容量初始值**，Hashtable 默认的初始大小为 **11**，之后每次扩充，容量变为原来的**2n+1**。HashMap 默认的初始化大小为**16**。之后每次扩充，容量变为原来的**2倍**。②**创建时如果给定了容量初始值**，那么 **Hashtable 会直接使用你给定的大小**，而**HashMap 会将其扩充 为2的幂次方大小**（HashMap 中的 **tableSizeFor()** 方法保证，下面给出了源代码）。也就是说 **HashMap 总是使用2的幂作为哈希表的大小**,后面会介绍到为什么是2的幂次方。 
5. **底层数据结构**： JDK1.8 以后的 HashMap 在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默认为 8）时，将链表转化为红黑树，以减少搜索时间。Hashtable 没有这样的机制。

**HashSet 和 HashMap 区别**

如果你看过 HashSet 源码的话就应该知道：**HashSet 底层就是基于 HashMap 实现的**。（HashSet 的源码非常非常 少，因为除了 clone() 方法、writeObject()方法、readObject()方法是 HashSet 自己不得不实现之外，其他方法都是直接调用 HashMap 中的方法。）

![26](/Users/wx/project/interview/docs/秘籍/images/26.png)

### 7.HashMap源码分析

**类的属性：**

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable {
    // 序列号
    private static final long serialVersionUID = 362498820763181265L;    
    // 默认的初始容量是16
    static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;   
    // 最大容量
    static final int MAXIMUM_CAPACITY = 1 << 30; 
    // 默认的填充因子
    static final float DEFAULT_LOAD_FACTOR = 0.75f;
    // 当桶(bucket)上的结点数大于这个值时会转成红黑树
    static final int TREEIFY_THRESHOLD = 8; 
    // 当桶(bucket)上的结点数小于这个值时树转链表
    static final int UNTREEIFY_THRESHOLD = 6;
    // 桶中结构转化为红黑树对应的table的最小大小
    static final int MIN_TREEIFY_CAPACITY = 64;
    // 存储元素的数组，总是2的幂次倍
    transient Node<k,v>[] table; 
    // 存放具体元素的集
    transient Set<map.entry<k,v>> entrySet;
    // 存放元素的个数，注意这个不等于数组的长度。
    transient int size;
    // 每次扩容和更改map结构的计数器
    transient int modCount;   
    // 临界值 当实际大小(容量*填充因子)超过临界值时，会进行扩容
    int threshold;
    // 加载因子
    final float loadFactor;
}
```

- **loadFactor加载因子**

  loadFactor加载因子是控制数组存放数据的疏密程度，loadFactor越趋近于1，那么 数组中存放的数据(entry)也就越多，也就越密，也就是会让链表的长度增加，loadFactor越小，也就是趋近于0，数组中存放的数据(entry)也就越少，也就越稀疏。

  **loadFactor太大导致查找元素效率低，太小导致数组的利用率低，存放的数据会很分散。loadFactor的默认值为0.75f是官方给出的一个比较好的临界值**。

  给定的默认容量为 **16**，负载因子为 0.75。Map 在使用过程中不断的往里面存放数据，当数量达到了 16 * 0.75 = 12 就需要将当前 16 的容量进行扩容，而扩容这个过程涉及到 rehash、复制数据等操作，所以非常消耗性能。

- **threshold**

  **threshold = capacity \* loadFactor**，**当Size>=threshold**的时候，那么就要考虑对数组的扩增了，也就是说，这个的意思就是 **衡量数组是否需要扩增的一个标准**。

**Node节点类源码:**

```java
// 继承自 Map.Entry<K,V>
static class Node<K,V> implements Map.Entry<K,V> {
       final int hash;// 哈希值，存放元素到hashmap中时用来与其他元素hash值比较
       final K key;//键
       V value;//值
       // 指向下一个节点
       Node<K,V> next;
       Node(int hash, K key, V value, Node<K,V> next) {
            this.hash = hash;
            this.key = key;
            this.value = value;
            this.next = next;
        }
        public final K getKey()        { return key; }
        public final V getValue()      { return value; }
        public final String toString() { return key + "=" + value; }
        // 重写hashCode()方法
        public final int hashCode() {
            return Objects.hashCode(key) ^ Objects.hashCode(value);
        }

        public final V setValue(V newValue) {
            V oldValue = value;
            value = newValue;
            return oldValue;
        }
        // 重写 equals() 方法
        public final boolean equals(Object o) {
            if (o == this)
                return true;
            if (o instanceof Map.Entry) {
                Map.Entry<?,?> e = (Map.Entry<?,?>)o;
                if (Objects.equals(key, e.getKey()) &&
                    Objects.equals(value, e.getValue()))
                    return true;
            }
            return false;
        }
}
```

**树节点类源码:**

```java
static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
        TreeNode<K,V> parent;  // 父
        TreeNode<K,V> left;    // 左
        TreeNode<K,V> right;   // 右
        TreeNode<K,V> prev;    // needed to unlink next upon deletion
        boolean red;           // 判断颜色
        TreeNode(int hash, K key, V val, Node<K,V> next) {
            super(hash, key, val, next);
        }
        // 返回根节点
        final TreeNode<K,V> root() {
            for (TreeNode<K,V> r = this, p;;) {
                if ((p = r.parent) == null)
                    return r;
                r = p;
       }
```

**构造方法**

HashMap 中有四个构造方法，它们分别如下：

```java
    // 默认构造函数。
    public HashMap() {
        this.loadFactor = DEFAULT_LOAD_FACTOR; // all   other fields defaulted
     }
     
     // 包含另一个“Map”的构造函数
     public HashMap(Map<? extends K, ? extends V> m) {
         this.loadFactor = DEFAULT_LOAD_FACTOR;
         putMapEntries(m, false);//下面会分析到这个方法
     }
     
     // 指定“容量大小”的构造函数
     public HashMap(int initialCapacity) {
         this(initialCapacity, DEFAULT_LOAD_FACTOR);
     }
     
     // 指定“容量大小”和“加载因子”的构造函数
     public HashMap(int initialCapacity, float loadFactor) {
         if (initialCapacity < 0)
             throw new IllegalArgumentException("Illegal initial capacity: " + initialCapacity);
         if (initialCapacity > MAXIMUM_CAPACITY)
             initialCapacity = MAXIMUM_CAPACITY;
         if (loadFactor <= 0 || Float.isNaN(loadFactor))
             throw new IllegalArgumentException("Illegal load factor: " + loadFactor);
         this.loadFactor = loadFactor;
         this.threshold = tableSizeFor(initialCapacity);
     }
```

**putMapEntries方法：**

```java
final void putMapEntries(Map<? extends K, ? extends V> m, boolean evict) {
    int s = m.size();
    if (s > 0) {
        // 判断table是否已经初始化
        if (table == null) { // pre-size
            // 未初始化，s为m的实际元素个数
            float ft = ((float)s / loadFactor) + 1.0F;
            int t = ((ft < (float)MAXIMUM_CAPACITY) ?
                    (int)ft : MAXIMUM_CAPACITY);
            // 计算得到的t大于阈值，则初始化阈值
            if (t > threshold)
                threshold = tableSizeFor(t);
        }
        // 已初始化，并且m元素个数大于阈值，进行扩容处理
        else if (s > threshold)
            resize();
        // 将m中的所有元素添加至HashMap中
        for (Map.Entry<? extends K, ? extends V> e : m.entrySet()) {
            K key = e.getKey();
            V value = e.getValue();
            putVal(hash(key), key, value, false, evict);
        }
    }
}
```

**put方法**

HashMap只提供了put用于添加元素，putVal方法只是给put方法调用的一个方法，并没有提供给用户使用。

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}

final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
                   boolean evict) {
    Node<K,V>[] tab; Node<K,V> p; int n, i;
    // table未初始化或者长度为0，进行扩容
    if ((tab = table) == null || (n = tab.length) == 0)
        n = (tab = resize()).length;
    // (n - 1) & hash 确定元素存放在哪个桶中，桶为空，新生成结点放入桶中(此时，这个结点是放在数组中)
    if ((p = tab[i = (n - 1) & hash]) == null)
        tab[i] = newNode(hash, key, value, null);
    // 桶中已经存在元素
    else {
        Node<K,V> e; K k;
        // 比较桶中第一个元素(数组中的结点)的hash值相等，key相等
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
                // 将第一个元素赋值给e，用e来记录
                e = p;
        // hash值不相等，即key不相等；为红黑树结点
        else if (p instanceof TreeNode)
            // 放入树中
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        // 为链表结点
        else {
            // 在链表最末插入结点
            for (int binCount = 0; ; ++binCount) {
                // 到达链表的尾部
                if ((e = p.next) == null) {
                    // 在尾部插入新结点
                    p.next = newNode(hash, key, value, null);
                    // 结点数量达到阈值，转化为红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    // 跳出循环
                    break;
                }
                // 判断链表中结点的key值与插入的元素的key值是否相等
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    // 相等，跳出循环
                    break;
                // 用于遍历桶中的链表，与前面的e = p.next组合，可以遍历链表
                p = e;
            }
        }
        // 表示在桶中找到key值、hash值与插入元素相等的结点
        if (e != null) { 
            // 记录e的value
            V oldValue = e.value;
            // onlyIfAbsent为false或者旧值为null
            if (!onlyIfAbsent || oldValue == null)
                //用新值替换旧值
                e.value = value;
            // 访问后回调
            afterNodeAccess(e);
            // 返回旧值
            return oldValue;
        }
    }
    // 结构性修改
    ++modCount;
    // 实际大小大于阈值则扩容
    if (++size > threshold)
        resize();
    // 插入后回调
    afterNodeInsertion(evict);
    return null;
} 
```

**get方法**

```java
public V get(Object key) {
    Node<K,V> e;
    return (e = getNode(hash(key), key)) == null ? null : e.value;
}

final Node<K,V> getNode(int hash, Object key) {
    Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
    if ((tab = table) != null && (n = tab.length) > 0 &&
        (first = tab[(n - 1) & hash]) != null) {
        // 数组元素相等
        if (first.hash == hash && // always check first node
            ((k = first.key) == key || (key != null && key.equals(k))))
            return first;
        // 桶中不止一个节点
        if ((e = first.next) != null) {
            // 在树中get
            if (first instanceof TreeNode)
                return ((TreeNode<K,V>)first).getTreeNode(hash, key);
            // 在链表中get
            do {
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    return e;
            } while ((e = e.next) != null);
        }
    }
    return null;
}
```

**resize方法**

进行扩容，会伴随着一次重新hash分配，并且会遍历hash表中所有的元素，是非常耗时的。在编写程序中，要尽量避免resize。

```java
final Node<K,V>[] resize() {
    Node<K,V>[] oldTab = table;
    int oldCap = (oldTab == null) ? 0 : oldTab.length;
    int oldThr = threshold;
    int newCap, newThr = 0;
    if (oldCap > 0) {
        // 超过最大值就不再扩充了，就只好随你碰撞去吧
        if (oldCap >= MAXIMUM_CAPACITY) {
            threshold = Integer.MAX_VALUE;
            return oldTab;
        }
        // 没超过最大值，就扩充为原来的2倍
        else if ((newCap = oldCap << 1) < MAXIMUM_CAPACITY && oldCap >= DEFAULT_INITIAL_CAPACITY)
            newThr = oldThr << 1; // double threshold
    }
    else if (oldThr > 0) // initial capacity was placed in threshold
        newCap = oldThr;
    else { 
        // signifies using defaults
        newCap = DEFAULT_INITIAL_CAPACITY;
        newThr = (int)(DEFAULT_LOAD_FACTOR * DEFAULT_INITIAL_CAPACITY);
    }
    // 计算新的resize上限
    if (newThr == 0) {
        float ft = (float)newCap * loadFactor;
        newThr = (newCap < MAXIMUM_CAPACITY && ft < (float)MAXIMUM_CAPACITY ? (int)ft : Integer.MAX_VALUE);
    }
    threshold = newThr;
    @SuppressWarnings({"rawtypes","unchecked"})
        Node<K,V>[] newTab = (Node<K,V>[])new Node[newCap];
    table = newTab;
    if (oldTab != null) {
        // 把每个bucket都移动到新的buckets中
        for (int j = 0; j < oldCap; ++j) {
            Node<K,V> e;
            if ((e = oldTab[j]) != null) {
                oldTab[j] = null;
                if (e.next == null)
                    newTab[e.hash & (newCap - 1)] = e;
                else if (e instanceof TreeNode)
                    ((TreeNode<K,V>)e).split(this, newTab, j, oldCap);
                else { 
                    Node<K,V> loHead = null, loTail = null;
                    Node<K,V> hiHead = null, hiTail = null;
                    Node<K,V> next;
                    do {
                        next = e.next;
                        // 原索引
                        if ((e.hash & oldCap) == 0) {
                            if (loTail == null)
                                loHead = e;
                            else
                                loTail.next = e;
                            loTail = e;
                        }
                        // 原索引+oldCap
                        else {
                            if (hiTail == null)
                                hiHead = e;
                            else
                                hiTail.next = e;
                            hiTail = e;
                        }
                    } while ((e = next) != null);
                    // 原索引放到bucket里
                    if (loTail != null) {
                        loTail.next = null;
                        newTab[j] = loHead;
                    }
                    // 原索引+oldCap放到bucket里
                    if (hiTail != null) {
                        hiTail.next = null;
                        newTab[j + oldCap] = hiHead;
                    }
                }
            }
        }
    }
    return newTab;
}
```

### 8.ConcurrentHashMap 和 Hashtable 的区别？

ConcurrentHashMap 和 Hashtable 都是线程安全的，其区别主要体现在实现**线程安全的实现方式**上不同。

- **底层数据结构**： JDK1.7的 ConcurrentHashMap 底层采用 **分段的数组+链表** 实现，JDK1.8 采用的数据结构跟 HashMap1.8的结构一样，**数组+链表/红黑二叉树**。Hashtable 和 JDK1.8 之前的 HashMap 的底层数据结构类似都是采用 **数组+链表** 的形式，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的； 
- **实现线程安全的方式（重要）**： ① 在**JDK1.7**的时候，**ConcurrentHashMap（分段锁）** 对整个桶数组进行了 **分割分段(Segment)**，每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。（默认分配16个Segment，比Hashtable效率提高16倍。） 到了 **JDK1.8** 的时候已经摒弃了Segment的概念，而是直接用 **Node 数组+链表+红黑树**的数据结构来实现，并发控制使用 **synchronized 和 CAS** 来操作。**（JDK1.6以后 对 synchronized锁做了很多优化）** 整个看起来就像是优化过且线程安全的 HashMap，虽然在JDK1.8中还能看到 Segment 的数据结构，但是已经简化了属性，只是为了兼容旧版本；② **Hashtable(同一把锁)** :使用 **synchronized** 来保证线程安全，效率非常低下。当一个线程访问同步方法时，其他线程也访问同步方法，可能会进入阻塞或轮询状态，如使用 put 添加元素，另一个线程不能使用 put 添加元素，也不能使用 get，竞争会越来越激烈效率越低。

两者的对比图：

HashTable:

![27](/Users/wx/project/interview/docs/秘籍/images/27.png)

JDK1.7的ConcurrentHashMap：

![28](/Users/wx/project/interview/docs/秘籍/images/28.png)

JDK1.8的ConcurrentHashMap（TreeBin: 红黑二叉树节点 Node: 链表节点）：

![29](/Users/wx/project/interview/docs/秘籍/images/29.png)

### 9.ConcurrentHashMap线程安全的具体实现方式/底层具体实现?

**JDK1.7(上面有示意图)**

首先将数据分为一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据时，其他段的数据也能被其他线程访问。

**ConcurrentHashMap 是由 Segment 数组结构和 HashEntry 数组结构组成。**

```java
static class Segment<K,V> extends ReentrantLock implements Serializable { }
```

Segment 实现了 **ReentrantLock**,所以 Segment 是一种**可重入锁**，扮演锁的角色。HashEntry 用于存储键值对数据。

一个 ConcurrentHashMap 里包含一个 **Segment 数组**。Segment 的结构和HashMap类似，是一种**数组和链表**结构，**一个 Segment 包含一个 HashEntry 数组**，**每个 HashEntry 是一个链表结构的元素**，每个 Segment 守护着一个 HashEntry数组里的元素，当对 HashEntry 数组的数据进行修改时，必须首先获得对应的 Segment的锁。

**JDK1.8(上面有示意图)**

ConcurrentHashMap 取消了 Segment 分段锁，采用 **CAS和synchronized** 来保证并发安全。**数据结构跟HashMap1.8 **的结构类似，**数组+链表/红黑二叉树**。

synchronized只锁定**当前链表或红黑二叉树的首节点**，这样**只要hash不冲突，就不会产生并发**，效率又提升N倍。

### 10.HashMap 的长度为什么是2的幂次方？

为了能让 HashMap 存取高效，尽量较少碰撞，也就是要尽量把数据分配均匀。我们上面也讲到了过了，Hash 值的 范围值-2147483648到2147483647，前后加起来大概40亿的映射空间，只要哈希函数映射得比较均匀松散，一般应用是很难出现碰撞的。但问题是一个40亿长度的数组，内存是放不下的。所以这个散列值是不能直接拿来用的。用之前还要先做对数组的长度取模运算，得到的余数才能用来要存放的位置也就是对应的数组下标。这个数组下标的计算方法是“ **(n - 1) & hash** ”。（n代表数组长度）。

这也就解释了 HashMap 的长度为什么是2的幂次方。

这个算法应该如何设计呢？

我们首先可能会想到采用%取余的操作来实现。但是，重点来了：“取余(%)操作中如果除数是2的幂次则等价于与其除数减一的与(&)操作（也就是说 hash%length==hash&(length-1)的前提是 length 是2的 n 次方；）。” 并且采用二进制位操作 &，相对于%能够提高运算效率，这就解释了 HashMap 的长度为什么是2的幂次方。

### 11.集合框架底层数据结构总结

**Collection**

- **List**
  - **Arraylist**： Object数组；
  - **Vector**： Object数组；
  - **LinkedList**： 双向链表(JDK1.6之前为循环链表，JDK1.7取消了循环) 详细可阅读JDK1.7-LinkedList循环链表优化；

- **Set**
  - **HashSet（无序，唯一）**: 基于 HashMap 实现的，底层采用 HashMap 来保存元素； 
  - **LinkedHashSet**： LinkedHashSet 继承与 HashSet，并且其内部是通过 LinkedHashMap 来实现的。有点类似于我们之前说的LinkedHashMap 其内部是基于 Hashmap 实现一样，不过还是有一点点区别的。 
  - **TreeSet（有序，唯一）**： 红黑树(自平衡的排序二叉树)
- **Map**
  - **HashMap**： JDK1.8之前HashMap由 **数组+链表** 组成的，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的（“拉链法”解决冲突）.JDK1.8以后在解决哈希冲突时有了较大的变化，当链表长度大于阈值（默 认为8）时，将链表转化为**红黑树**，以减少搜索时间；
  - **LinkedHashMap**： LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构即由**数组和链表或红黑树**组成。另外，LinkedHashMap 在上面结构的基础上，增加了一条**双向链表**，使得上面的结构可以保持键值对的插入顺序。同时通过对链表进行相应的操作，实现了访问顺序相关逻辑；
  - **HashTable**: 数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的；
  - **TreeMap**: 红黑树（自平衡的排序二叉树）

### 12.十亿条淘宝购买记录,怎么获取出现最多的前十个？

这道题考查的是**堆**的知识点，使用多个堆就可以解决这个问题。

取模分成1W个小数据，然后保存在map中，做一个**最大堆**，每个堆取前十的数据，然后最后做一个**最大堆**，取前十。

## JVM

### 1.介绍下 Java 内存区域(运行时数据区域)

**JDK 1.8 之前：**

![43](/Users/wx/project/interview/docs/秘籍/images/43.png)

**JDK 1.8 ：**

![44](/Users/wx/project/interview/docs/秘籍/images/44.png)

**线程私有的：**

- 程序计数器
- 虚拟机栈
- 本地方法栈

**线程共享的：**

- 堆
- 方法区
- 直接内存 (非运行时数据区的一部分)

### 2.Java 对象的创建过程

![45](/Users/wx/project/interview/docs/秘籍/images/45.png)

**Step1:类加载检查**

虚拟机遇到一条 new 指令时，首先将去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已被加载过、解析和初始化过。如果没有，那必须先执行相应的类加载过程。

**Step2:分配内存**

在**类加载检查**通过后，接下来虚拟机将为新生对象**分配内存**。对象所需的内存大小在类加载完成后便可确定，为对象分配空间的任务等同于把一块确定大小的内存从 Java 堆中划分出来。**分配方式**有 **“指针碰撞”** 和 **“空闲列表”** 两种，**选择那种分配方式由 Java 堆是否规整决定，而 Java 堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定**。

**内存分配的两种方式：（补充内容，需要掌握）**

选择以上两种方式中的哪一种，取决于 Java 堆内存是否规整。而 Java 堆内存是否规整，取决于 GC 收集器的算法是"**标记-清除**"，还是"**标记-整理**"（也称作"标记-压缩"），值得注意的是，复制算法内存也是规整的

![46](/Users/wx/project/interview/docs/秘籍/images/46.png)

**内存分配并发问题（补充内容，需要掌握）**

在创建对象的时候有一个很重要的问题，就是线程安全，因为在实际开发过程中，创建对象是很频繁的事情，作为虚拟机来说，必须要保证线程是安全的，通常来讲，虚拟机采用两种方式来保证线程安全：

- **CAS+失败重试：** CAS 是乐观锁的一种实现方式。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。**虚拟机采用 CAS 配上失败重试的方式保证更新操作的原子性。**
- **TLAB：** 为每一个线程预先在 Eden 区分配一块儿内存，JVM 在给线程中的对象分配内存时，首先在 TLAB 分配，当对象大于 TLAB 中的剩余内存或 TLAB 的内存已用尽时，再采用上述的 CAS 进行内存分配

**Step3:初始化零值**

内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值（不包括对象头），这一步操作保证了对象的实例字段在 Java 代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对应的零值。

**Step4:设置对象头**

初始化零值完成之后，**虚拟机要对对象进行必要的设置**，例如这个对象是那个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄等信息。 **这些信息存放在对象头中。** 另外，根据虚拟机当前运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式。

**Step5:执行 init 方法**

在上面工作都完成之后，从虚拟机的视角来看，一个新的对象已经产生了，但从 Java 程序的视角来看，对象创建才刚开始，`<init>` 方法还没有执行，所有的字段都还为零。所以一般来说，执行 new 指令之后会接着执行 `<init>` 方法，把对象按照程序员的意愿进行初始化，这样一个真正可用的对象才算完全产生出来。

### 3.对象的访问定位

对象的访问方式由虚拟机实现而定，目前主流的访问方式有**①使用句柄**和**②直接指针**两种：

**句柄：** 如果使用句柄的话，那么 Java 堆中将会划分出一块内存来作为**句柄池**，reference 中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息；

**直接指针：** 如果使用直接指针访问，那么 Java 堆对象的布局中就必须考虑如何放置访问类型数据的相关信息，而 reference 中存储的直接就是对象的地址。

**这两种对象访问方式各有优势。使用句柄来访问的最大好处是 reference 中存储的是稳定的句柄地址，在对象被移动时只会改变句柄中的实例数据指针，而 reference 本身不需要修改。使用直接指针访问方式最大的好处就是速度快，它节省了一次指针定位的时间开销。**

### 4.String 类和常量池

**String 对象的两种创建方式：**

```java
String str1 = "abcd";//先检查字符串常量池中有没有"abcd"，如果字符串常量池中没有，则创建一个，然后 str1 指向字符串常量池中的对象，如果有，则直接将 str1 指向"abcd""；
String str2 = new String("abcd");//堆中创建一个新的对象
String str3 = new String("abcd");//堆中创建一个新的对象
System.out.println(str1==str2);//false
System.out.println(str2==str3);//false
```

这两种不同的创建方法是有差别的。

- 第一种方式是在**常量池中拿对象**；
- 第二种方式是**直接在堆内存空间创建一个新的对象**。

记住一点：**只要使用 new 方法，便需要创建新的对象。**

尽量避免多个字符串拼接，因为这样会重新创建对象。如果需要改变字符串的话，可以使用 StringBuilder 或者 StringBuffer。

```java
		  String str1 = "str";
		  String str2 = "ing";
		 
		  String str3 = "str" + "ing";//常量池中的对象
		  String str4 = str1 + str2; //在堆上创建的新的对象	  
		  String str5 = "string";//常量池中的对象
		  System.out.println(str3 == str4);//false
		  System.out.println(str3 == str5);//true
		  System.out.println(str4 == str5);//false
```

### 5.8 种基本类型的包装类和常量池

**Java 基本类型的包装类的大部分都实现了常量池技术，即 Byte,Short,Integer,Long,Character,Boolean；前面 4 种包装类默认创建了数值[-128，127] 的相应类型的缓存数据，Character创建了数值在[0,127]范围的缓存数据，Boolean 直接返回True Or False。如果超出对应范围仍然会去创建新的对象。**

### 6.如何判断对象已经死亡？

**1、引用计数法**

给对象中添加一个**引用计数器**，每当有一个地方引用它，计数器就加 1；当引用失效，计数器就减 1；任何时候计数器为 0 的对象就是不可能再被使用的。这个方法实现简单，效率高，但是目前主流的虚拟机中并没有选择这个算法来管理内存，其最主要的原因是它很难解决对象之间相互**循环引用**的问题。

**2、可达性分析法**

通过一系列的称为 **“GC Roots”** 的对象作为起点，从这些节点开始向下搜索，节点所走过的路径称为引用链，当一个对象到 GC Roots 没有任何引用链相连的话，则证明此对象是不可用的。

![47](/Users/wx/project/interview/docs/秘籍/images/47.png)

### 7.如何判断一个常量是废弃常量？

运行时常量池主要回收的是废弃的常量。那么，我们如何判断一个常量是废弃常量呢？

假如在常量池中存在字符串 "abc"，如果当前**没有任何 String 对象引用该字符串常量**的话，就说明常量 "abc" 就是废弃常量，如果这时发生内存回收的话而且有必要的话，"abc" 就会被系统清理出常量池。

### 8.如何判断一个类是无用的类?

判定一个常量是否是“废弃常量”比较简单，而要判定一个类是否是“无用的类”的条件则相对苛刻许多。类需要同时满足下面 3 个条件才能算是 **“无用的类”** ：

- 该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。
- 加载该类的 ClassLoader 已经被回收。
- 该类对应的 java.lang.Class 对象没有在任何地方被引用，无法在任何地方通过反射访问该类的方法。

虚拟机可以对满足上述 3 个条件的无用类进行回收，这里说的仅仅是“可以”，而并不是和对象一样不使用了就会必然被回收。

### 9.强引用、软引用、弱引用、虚引用

**1．强引用（StrongReference）**

以前我们使用的大部分引用实际上都是强引用，这是使用最普遍的引用。如果一个对象具有强引用，那就类似于**必不可少的生活用品**，垃圾回收器绝不会回收它。当内存空间不足，Java 虚拟机宁愿抛出 OutOfMemoryError 错误，使程序异常终止，也不会靠随意回收具有强引用的对象来解决内存不足问题。

**2．软引用（SoftReference）**

如果一个对象只具有软引用，那就类似于**可有可无的生活用品**。如果内存空间足够，垃圾回收器就不会回收它，如果内存空间不足了，就会回收这些对象的内存。只要垃圾回收器没有回收它，该对象就可以被程序使用。软引用可用来实现内存敏感的高速缓存。

软引用可以和一个引用队列（ReferenceQueue）联合使用，如果软引用所引用的对象被垃圾回收，JAVA 虚拟机就会把这个软引用加入到与之关联的引用队列中。

**3．弱引用（WeakReference）**

如果一个对象只具有弱引用，那就类似于**可有可无的生活用品**。弱引用与软引用的区别在于：**只具有弱引用的对象拥有更短暂的生命周期。在垃圾回收器线程扫描它所管辖的内存区域的过程中，一旦发现了只具有弱引用的对象，不管当前内存空间足够与否，都会回收它的内存**。不过，由于垃圾回收器是一个优先级很低的线程， 因此不一定会很快发现那些只具有弱引用的对象。

弱引用可以和一个引用队列（ReferenceQueue）联合使用，如果弱引用所引用的对象被垃圾回收，Java 虚拟机就会把这个弱引用加入到与之关联的引用队列中。

**4．虚引用（PhantomReference）**

"虚引用"顾名思义，就是**形同虚设**，与其他几种引用都不同，虚引用并不会决定对象的生命周期。如果一个对象仅持有虚引用，那么它就和没有任何引用一样，在任何时候都可能被垃圾回收。

**虚引用主要用来跟踪对象被垃圾回收的活动**。

**虚引用与软引用和弱引用的一个区别在于：** **虚引用必须和引用队列（ReferenceQueue）联合使用**。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前，把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是否已经加入了虚引用，来了解被引用的对象是否将要被垃圾回收。程序如果发现某个虚引用已经被加入到引用队列，那么就可以在所引用的对象的内存被回收之前采取必要的行动。

特别注意，在程序设计中一般很少使用弱引用与虚引用，使用软引用的情况较多，这是因为**软引用可以加速 JVM 对垃圾内存的回收速度，可以维护系统的运行安全，防止内存溢出（OutOfMemory）等问题的产生**。

### 10.垃圾收集有哪些算法？

![48](/Users/wx/project/interview/docs/秘籍/images/48.png)

**1、标记-清除算法**

该算法分为“标记”和“清除”阶段：首先标记出所有需要回收的对象，在标记完成后统一回收掉所有被标记的对象。它是最基础的收集算法，后续的算法都是对其不足进行改进得到。这种垃圾收集算法会带来两个明显的问题：

1. **效率问题**
2. **空间问题（标记清除后会产生大量不连续的碎片）**

![49](/Users/wx/project/interview/docs/秘籍/images/49.jpeg)

**2、复制算法**

为了解决效率问题，“复制”收集算法出现了。它可以将内存分为大小相同的两块，每次使用其中的一块。当这一块的内存使用完后，就将还存活的对象复制到另一块去，然后再把使用的空间一次清理掉。这样就使每次的内存回收都是对内存区间的一半进行回收。

![50](/Users/wx/project/interview/docs/秘籍/images/50.png)

**3、标记-整理算法**

根据老年代的特点提出的一种标记算法，标记过程仍然与“标记-清除”算法一样，但后续步骤不是直接对可回收对象回收，而是让所有存活的对象向一端移动，然后直接清理掉端边界以外的内存。

![51](/Users/wx/project/interview/docs/秘籍/images/51.png)

**3、分代收集算法（HotSpot为什么要分为新生代和老年代？）**

当前虚拟机的垃圾收集都采用分代收集算法，这种算法没有什么新的思想，只是根据对象存活周期的不同将内存分为几块。一般将 java 堆分为**新生代和老年代**，这样我们就可以根据各个年代的特点选择合适的垃圾收集算法。

**比如在新生代中，每次收集都会有大量对象死去，所以可以选择复制算法，只需要付出少量对象的复制成本就可以完成每次垃圾收集。而老年代的对象存活几率是比较高的，而且没有额外的空间对它进行分配担保，所以我们必须选择“标记-清除”或“标记-整理”算法进行垃圾收集。**

### 11.常见的垃圾回收器有那些？

![52](/Users/wx/project/interview/docs/秘籍/images/52.png)

**Serial 收集器**

Serial（串行）收集器收集器是最基本、历史最悠久的垃圾收集器了。大家看名字就知道这个收集器是一个单线程收集器了。它的 **“单线程”** 的意义不仅仅意味着它只会使用一条垃圾收集线程去完成垃圾收集工作，更重要的是它在进行垃圾收集工作的时候必须暂停其他所有的工作线程（ **"Stop The World"** ），直到它收集结束。

![53](/Users/wx/project/interview/docs/秘籍/images/53.png)

Serial收集器的优点是：**简单而高效（与其他收集器的单线程相比）**

**ParNew 收集器**

**ParNew 收集器其实就是 Serial 收集器的多线程版本，除了使用多线程进行垃圾收集外，其余行为（控制参数、收集算法、回收策略等等）和 Serial 收集器完全一样。**

![54](/Users/wx/project/interview/docs/秘籍/images/54.png)

**Parallel Scavenge 收集器**

Parallel Scavenge 收集器也是使用复制算法的多线程收集器，它看上去几乎和ParNew都一样。 **那么它有什么特别之处呢？**

**Parallel Scavenge 收集器关注点是吞吐量（高效率的利用 CPU）。CMS 等垃圾收集器的关注点更多的是用户线程的停顿时间（提高用户体验）。所谓吞吐量就是 CPU 中用于运行用户代码的时间与 CPU 总消耗时间的比值。** Parallel Scavenge 收集器提供了很多参数供用户找到最合适的停顿时间或最大吞吐量，如果对于收集器运作不太了解的话，手工优化存在困难的话可以选择把内存管理优化交给虚拟机去完成也是一个不错的选择。

![55](/Users/wx/project/interview/docs/秘籍/images/55.png)

**Serial Old 收集器**

**Serial 收集器的老年代版本**，它同样是一个单线程收集器。它主要有两大用途：一种用途是在 JDK1.5 以及以前的版本中与 Parallel Scavenge 收集器搭配使用，另一种用途是作为 CMS 收集器的后备方案。

**Parallel Old 收集器**

**Parallel Scavenge 收集器的老年代版本**。使用多线程和“标记-整理”算法。在注重吞吐量以及 CPU 资源的场合，都可以优先考虑 Parallel Scavenge 收集器和 Parallel Old 收集器。

**CMS 收集器**

**CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。它非常符合在注重用户体验的应用上使用。**

**CMS（Concurrent Mark Sweep）收集器是 HotSpot 虚拟机第一款真正意义上的并发收集器，它第一次实现了让垃圾收集线程与用户线程（基本上）同时工作。**

从名字中的**Mark Sweep**这两个词可以看出，CMS 收集器是一种 **“标记-清除”算法**实现的，它的运作过程相比于前面几种垃圾收集器来说更加复杂一些。整个过程分为四个步骤：

- **初始标记：** 暂停所有的其他线程，并记录下直接与 root 相连的对象，速度很快 ；
- **并发标记：** 同时开启 GC 和用户线程，用一个闭包结构去记录可达对象。但在这个阶段结束，这个闭包结构并不能保证包含当前所有的可达对象。因为用户线程可能会不断的更新引用域，所以 GC 线程无法保证可达性分析的实时性。所以这个算法里会跟踪记录这些发生引用更新的地方。
- **重新标记：** 重新标记阶段就是为了修正并发标记期间因为用户程序继续运行而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段的时间稍长，远远比并发标记阶段时间短
- **并发清除：** 开启用户线程，同时 GC 线程开始对未标记的区域做清扫。

![56](/Users/wx/project/interview/docs/秘籍/images/56.png)

从它的名字就可以看出它是一款优秀的垃圾收集器，主要优点：**并发收集、低停顿**。但是它有下面三个明显的缺点：

- **对 CPU 资源敏感；**
- **无法处理浮动垃圾；**
- **它使用的回收算法-“标记-清除”算法会导致收集结束时会有大量空间碎片产生。**

**G1 收集器**

**G1 (Garbage-First) 是一款面向服务器的垃圾收集器,主要针对配备多颗处理器及大容量内存的机器. 以极高概率满足 GC 停顿时间要求的同时,还具备高吞吐量性能特征.**

被视为 JDK1.7 中 HotSpot 虚拟机的一个重要进化特征。它具备一下特点：

- **并行与并发**：G1 能充分利用 CPU、多核环境下的硬件优势，使用多个 CPU（CPU 或者 CPU 核心）来缩短 Stop-The-World 停顿时间。部分其他收集器原本需要停顿 Java 线程执行的 GC 动作，G1 收集器仍然可以通过并发的方式让 java 程序继续执行。
- **分代收集**：虽然 G1 可以不需要其他收集器配合就能独立管理整个 GC 堆，但是还是保留了分代的概念。
- **空间整合**：与 CMS 的“标记--清理”算法不同，G1 从整体来看是基于“标记整理”算法实现的收集器；从局部上来看是基于“复制”算法实现的。
- **可预测的停顿**：这是 G1 相对于 CMS 的另一个大优势，降低停顿时间是 G1 和 CMS 共同的关注点，但 G1 除了追求低停顿外，还能建立可预测的停顿时间模型，能让使用者明确指定在一个长度为 M 毫秒的时间片段内。

G1 收集器的运作大致分为以下几个步骤：

- **初始标记**
- **并发标记**
- **最终标记**
- **筛选回收**

**G1 收集器在后台维护了一个优先列表，每次根据允许的收集时间，优先选择回收价值最大的 Region(这也就是它的名字 Garbage-First 的由来)**。这种使用 Region 划分内存空间以及有优先级的区域回收方式，保证了 G1 收集器在有限时间内可以尽可能高的收集效率（把内存化整为零）。

### 12.Minor Gc和Full GC 有什么不同呢？

**Full GC 就是收集整个堆，包括新生代，老年代，永久代(在JDK 1.8及以后，永久代会被移除，换为metaspace)等收集所有部分的模式**

Minor GC 是俗称，**新生代(新生代分为一个 Eden区和两个Survivor区)的垃圾收集叫做 Minor GC**。当 Eden 区的空间耗尽了怎么办？这个时候 Java虚拟机便会触发一次 **Minor GC**来收集新生代的垃圾，存活下来的对象，则会被送到 Survivor区。