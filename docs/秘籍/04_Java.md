# 基础

### ==1.什么是单例模式？为什么要用单例模式?手写几种线程安全的单例模式?==

**1）什么是单例模式**

单例模式是一种常用的软件设计模式，属于创建型模式的一种。在应用这个模式时，单例对象的类必须保证只有一个实例存在。

首先我们要先了解下单例的四大原则：

1. 构造函数私有化。
2. 以静态方法或者枚举返回实例。
3. 确保实例只有一个，尤其是多线程环境。
4. 确保反序列转换时不会重新构建对象。

**2）使用单例模式可以带来下面几个好处**

- 对于频繁使用的对象，可以省略创建对象所花费的时间（这对于那些重量级对象而言，是非常可观的一笔系统开销）
- 由于 new 操作的次数减少，因而对系统内存的使用频率也会降低，这将减轻 GC 压力，缩短 GC 停顿时间。

**3）Java程序初始化顺序**

1. 父类的静态代码块
2. 子类的静态代码块
3. 父类的普通代码块
4. 父类的构造方法
5. 子类的普通代码块
6. 子类的构造方法

**4）Java中实现的单例模式**

- 懒汉式
- 饿汉式
- 静态内部类
- 枚举

以下是几种单例模式的实现：



**懒汉式(双重检查加锁版本)**

懒汉式在第一次调用 getInstance 的时候进行实例化

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

**懒汉式为什么要加锁？**

加锁是为了保证在多线程环境下我们还是只能得到该类的一个实例。

**锁加在方法为什么不可以？**

锁加在 getinstance 方法上的话，会导致每次调用 getInstance() 方法时都被 synchronized 关键字锁住了，会引起线程阻塞，影响程序的性能。

为了在多线程环境下，不影响程序的性能，不让线程每次调用 getInstance() 方法时都加锁，而只是在实例未被创建时再加锁，在加锁处理里面还需要判断一次实例是否已存在。这样就可以比较高效率的实现在多线程环境下的单例模式。



**静态内部类方式**

静态内部实现的单例是**懒加载的且线程安全（这也是他的优点）**。

只有通过显式调用 getInstance 方法时，才会显式装载 SingletonHolder 类，从而实例化 instance（只有第一次使用这个单例的实例的时候才加载，同时不会有线程安全问题）。

静态内部类的优点是：**外部类加载时并不需要立即加载内部类，内部类不被加载则不去初始化INSTANCE，故而不占内存**。即当SingleTon第一次被加载时，并不需要去加载SingleTonHoler，只有当getInstance()方法第一次被调用时，才会去初始化INSTANCE,第一次调用getInstance()方法会导致虚拟机加载SingleTonHoler类，**这种方法不仅能确保线程安全，也能保证单例的唯一性，同时也延迟了单例的实例化**。

那么，静态内部类又是如何实现线程安全的呢？首先，我们先了解下类的加载时机。

类加载时机：

JAVA虚拟机在有且仅有的5种场景下会对类进行初始化。

1. 遇到new、getstatic、setstatic或者invokestatic这4个字节码指令时，对应的java代码场景为：new一个关键字或者一个实例化对象时、读取或设置一个静态字段时(final修饰、已在编译期把结果放入常量池的除外)、调用一个类的静态方法时。
2. 使用java.lang.reflect包的方法对类进行反射调用的时候，如果类没进行初始化，需要先调用其初始化方法进行初始化。
3. 当初始化一个类时，如果其父类还未进行初始化，会先触发其父类的初始化。
4. 当虚拟机启动时，用户需要指定一个要执行的主类(包含main()方法的类)，虚拟机会先初始化这个类。
5. 当使用JDK 1.7等动态语言支持时，如果一个java.lang.invoke.MethodHandle实例最后的解析结果REF_getStatic、REF_putStatic、REF_invokeStatic的方法句柄，并且这个方法句柄所对应的类没有进行过初始化，则需要先触发其初始化。、

这5种情况被称为是类的主动引用，注意，这里《虚拟机规范》中使用的限定词是"有且仅有"，那么，除此之外的所有引用类都不会对类进行初始化，称为被动引用。静态内部类就属于被动引用的行列。

我们再回头看下getInstance()方法，调用的是SingleTonHoler.INSTANCE，取的是SingleTonHoler里的INSTANCE对象，跟上面那个DCL方法不同的是，getInstance()方法并没有多次去new对象，故不管多少个线程去调用getInstance()方法，取的都是同一个INSTANCE对象，而不用去重新创建。

当getInstance()方法被调用时，SingleTonHoler才在SingleTon的运行时常量池里，把符号引用替换为直接引用，这时静态对象INSTANCE也真正被创建，然后再被getInstance()方法返回出去，这点同饿汉模式。那么INSTANCE在创建过程中又是如何保证线程安全的呢？在《深入理解JAVA虚拟机》中，有这么一句话:

 虚拟机会保证一个类的<clinit>()方法在多线程环境中被正确地加锁、同步，如果多个线程同时去初始化一个类，那么只会有一个线程去执行这个类的<clinit>()方法，其他线程都需要阻塞等待，直到活动线程执行<clinit>()方法完毕。如果在一个类的<clinit>()方法中有耗时很长的操作，就可能造成多个进程阻塞(需要注意的是，其他线程虽然会被阻塞，但如果执行<clinit>()方法后，其他线程唤醒之后不会再次进入<clinit>()方法。同一个加载器下，一个类型只会初始化一次。)，在实际应用中，这种阻塞往往是很隐蔽的。

故而，可以看出INSTANCE在创建过程中是线程安全的，所以说静态内部类形式的单例可保证线程安全，也能保证单例的唯一性，同时也延迟了单例的实例化。

```java
public class Singleton{
    // 静态内部类中来创建单例对象
    private static class SingletonHolder{
        // 定义为静态常量，不可被修改
        private static final Singleton INSTANCE = new Singleton();
    }
    private Single(){}
    // 注意这里有final
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

### ==3.类装载器介绍？双亲委派机制？破坏双亲委派机制？==

**1）类装载器介绍**

**ClassLoader类装载器**就是寻找类的字节码文件并构造出类在  JVM 内部表示的对象组件。

主要工作由ClassLoader及其子类负责，ClassLoader是一个重要的 Java运行时系统组件，它负责在运行时查找和装入 Class 字节码文件。

在 JAVA中 java 虚拟机把一个类装入到 java 虚拟机当中需要经过以下的步骤：

1. **装载**：查找和导入Class文件。

2. **链接**：执行校验，准备和解析步骤。其中校验主要是检查载入class文件数据的正确性，而准备工作就是给类的静态变量来分配存储空间，解析则是将符号引用来转变成直接引用。

3. **初始化**：对类的静态变量、静态代码块执行初始化工作。

类文件被装载并解析之后，在 java 虚拟机内将拥有一个对应的 java.lang.class 类描述对象。该类的对象实例则拥有指向这个类描述对象的引用。而类描述对象又拥有指向关联 ClassLoader 的引用。

**2）全盘负责、双亲委派机制**

JVM装载类时使用**全盘负责委托机制**

**全盘负责**：当一个ClassLoader装载一个类的时候，除非显式的使用另一个ClassLoader，否则该类所依赖即引用的类也由这个ClassLoader来载入。

**双亲委派机制**：先委托父装载器寻找目标类，只有在找不到的情况下才从自己的类路径中查找并装载目标类。

为什么使用双亲委派机制：一个是安全性，另一个就是性能；（避免**重复加载**和**避免核心类被篡改**）

JVM在运行时会产生三个装载器字节码文件：**根装载器、ExtClassLoader（扩展类装载器）和AppClassLoader（系统类装载器）**。

- **根装载器**：不是ClassLoader的子类，由于他是使用C++来编写的，因此在JAVA中看不到他。根装载器来**装载jre的核心类库**，如jre目标下的rt.jar、charsets.jar等。
- **ExtClassLoader**：是ClassLoader的子类，负责装载 **jre 扩展目录ext中的jar类包**。
- **AppClassLoader**：是ClassLoader的子类，负责**装载 classpath 路径**下的类。

这三个装载器之间存在着父子层级的关系：根装载器是ExtClassLoader的父装载器，而ExtClassLoader是AppClassLoader的父装载器。

在默认情况下**使用AppClassLoader装载应用程序的类**。

**3）破坏双亲委派场景**

- **JDBC破坏双亲委派模型**

**原因**：**因为类加载器受到加载范围的限制，在某些情况下父类加载器无法加载到需要的文件，这时候就需要委托子类加载器去加载class文件。**

**具体介绍**：在某些情况下父类加载器需要委托子类加载器去加载class文件。比如 JDBC 数据库连接驱动，本身只是一个接口，并没有具体的实现，具体的实现是由不同数据库类型去实现的。例如，MySQL的mysql-connector-.jar中的Driver类具体实现的。 原生的JDBC中的类是放在rt.jar包的，是由启动类加载器进行类加载的，在JDBC中的Driver类中需要动态去加载不同数据库类型的Driver类，而mysql-connector-.jar中的Driver类是用户自己写的代码，那启动类加载器（Boot strap classloader）肯定是不能进行加载的，既然是自己编写的代码，那就需要由应用程序启动类（appclassloader）去进行类加载。这个时候就需要启动类加载器来委托子类来加载Driver实现，从而破坏了双亲委派。

**实现：**

在JDBC 4.0之后实际上我们不需要再调用Class.forName来加载驱动程序了，我们只需要把驱动的jar包放到工程的类加载路径里，那么驱动就会被自动加载。

这个自动加载采用的技术叫做**SPI**，数据库驱动厂商也都做了更新。可以看一下jar包里面的META-INF/services目录，里面有一个java.sql.Driver的文件，文件里面包含了驱动的全路径名。

引入线程**上下文类加载器(Thread Context ClassLoader)**。有了这个东西之后，程序就可以把原本需要由启动类加载器进行加载的类，由应用程序类加载器去进行加载了。在 sun.misc.Launcher 初始化的时候，**会获取AppClassLoader，然后将其设置为上下文类加载器**，所以**线程上下文类加载器默认情况下就是系统加载器**。

- **Tomcat破坏双亲委派模型**

**每个Tomcat的webappClassLoader加载自己的目录下的class文件，不会传递给父类加载器。**

Tomcat类加载器：

![](https://www.javazhiyin.com/wp-content/uploads/2019/10/java10-1571123781.png)



**4）能不能自己写个类叫java.lang.System？**

**答案：**不可以
**解释：**为了不让我们写System类，类加载采用委托机制。根装载器会首先将 java 写的 System类进行加载，自己写的System类根本没有机会得到加载。

### 4.Java当中的class对象是什么？有几种获取的方法？

**Class**其实就是类的类型，比如字符串类型就是String，整形类型就是Integer，String和Integer类型就是Class。

Class对象的常用方法介绍：

- getName()    获得类中完整名称
- getDeclaredFields()    获得类中的所有属性
- getDeclaredMethods()     获得类中所有的方法
- getConstructors()     获得类构造方法
- newInstance()     实例化类对象，注：newInstance()方法为实例化空参数的类对象时使用。

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

HashSet 中保存数据的时候会首先调用 **hashCode** 方法获取对象 hashcode 值，然后和已经保存的数据的 hashcode 进行比较，假如出现重复的话就得继续调用 **equals** 方法来检查 hashcode 相同的两个对象是否内存地址也一致，如何继续相同则证明两个对象是同一个对象，因此 hashSet 就不会让其加入。如果不同的话就证明是 hashcode 出现冲突，则散列到其他位置即可。这样就大大减少了调用 equals 的次数，也就大大提高了执行速度。

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

- **可变**：String为不可变类型，因为 String 类使用 **final关键字修饰的字符数组保存字符串**；StringBuffer、StringBuilder为可变类型，也是使用字符数组，但没有 final 关键字修饰。
- **线程安全**：String、StringBuffer 是线程安全的，String是不可变类型所以线程安全，StringBuffer对方法添加了**同步锁**所以是线程安全的。；StringBuilder 是线程不安全的。
- **性能**：String每次改变都会产生一个新的 String 对象，性能较低；StringBuffer、StringBuilder直接对对象本身进行操作，因此性能高。

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

### ==15.什么是泛型？为什么要使用泛型？==

泛型就是编写模板代码来适应任意类型；

泛型的好处是**使用时不必对类型进行强制转换，它通过编译器对类型进行检查**；

使用泛型意义和作用有：

- **类型的参数化**，就是可以把类型像方法的参数那样传递。这一点意义非凡。

- 泛型使编译器可以**在编译期间对类型进行检查以提高类型安全，减少运行时由于对象类型不匹配引发的异常**。

### 16.32位JDK和64位JDK的区别？

32位的JDK限制了程序的最大寻址空间仅为 2^32 即4GB。但是64位的JDK程序的最大寻址空间可以到2^64即64TB。

### ==17.Java的三大特征、五大原则==

**面向对象三大特征：**

**(1)封装(Encapsulation)**

所谓封装，也就是把客观事物封装成抽象的类，并且类可以把自己的数据和方法只让可信的类或者对象操作，对不可信的进行信息隐藏。封装是面向对象的特征之一，是对象和类概念的主要特性。简单的说，一个类就是一个封装了数据以及操作这些数据的代码的逻辑实体。在一个对象内部，某些代码或某些数据可以是私有的，不能被外界访问。通过这种方式，对象对内部数据提供了不同级别的保护，以防止程序中无关的部分意外的改变或错误的使用了对象的私有部分。

封装隐藏了类的内部实现机制，可以在不影响使用的情况下改变类的内部结构，同时也保护了数据。对外界而已它的内部细节是隐藏的，暴露给外界的只是它的访问方法。

**(2)继承(Inheritance)**

继承是指这样一种能力：它可以使用现有类的所有功能，并在无需重新编写原来的类的情况下对这些功能进行扩展。通过继承创建的新类称为“子类”或“派生类”，被继承的类称为“基类”、“父类”或“超类”。继承的过程，就是从一般到特殊的过程。要实现继承，可以通过“继承”（Inheritance）和“组合”（Composition）来实现。继承概念的实现方式有二类：实现继承与接口继承。实现继承是指直接使用基类的属性和方法而无需额外编码的能力；接口继承是指仅使用属性和方法的名称、但是子类必须提供实现的能力；

继承是为了重用父类代码。两个类若存在IS-A的关系就可以使用继承。同时继承也为实现多态做了铺垫。

**(3)多态(Polymorphism)**

所谓多态就是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用**在编程时并不确定，而是在程序运行期间才确定，即一个引用变量倒底会指向哪个类的实例对象，该引用变量发出的方法调用到底是哪个类中实现的方法，必须在由程序运行期间才能决定**。因为在程序运行时才确定具体的类，这样，不用修改源程序代码，就可以让引用变量绑定到各种不同的类实现上，从而导致该引用调用的具体方法随之改变，即不修改程序代码就可以改变程序运行时所绑定的具体代码，让程序可以选择多个运行状态，这就是多态性。

最常见的多态就是**将子类传入父类参数中，运行时调用父类方法时通过传入的子类决定具体的内部结构或行为**。

**多态实现条件**

在刚刚开始就提到了继承在为多态的实现做了准备。子类Child继承父类Father，我们可以编写一个指向子类的父类类型引用，该引用既可以处理父类Father对象，也可以处理子类Child对象，当相同的消息发送给子类或者父类对象时，该对象就会根据自己所属的引用而执行不同的行为，这就是多态。

即多态性就是相同的消息使得不同的类做出不同的响应。

Java实现多态有三个必要条件：**继承、重写、向上转型**。

- **继承**：在多态中必须存在有继承关系的子类和父类。
- **重写**：子类对父类中某些方法进行重新定义，在调用这些方法时就会调用子类的方法。
- **向上转型**：在多态中需要将子类的引用赋给父类对象，只有这样该引用才能够具备技能调用父类的方法和子类的方法。

只有满足了上述三个条件，我们才能够在同一个继承结构中使用统一的逻辑实现代码处理不同的对象，从而达到执行不同的行为。

对于Java而言，它多态的实现机制遵循一个原则：当超类对象引用变量引用子类对象时，被引用对象的类型而不是引用变量的类型决定了调用谁的成员方法，但是这个被调用的方法必须是在超类中定义过的，也就是说被子类覆盖的方法。

基于继承的实现机制主要表现在父类和继承该父类的一个或多个子类对某些方法的重写，多个子类对同一方法的重写可以表现出不同的行为。

在接口的多态中，指向接口的引用必须是指定这实现了该接口的一个类的实例程序，在运行时，根据对象引用的实际类型来执行对应的方法。

**多态实现形式**

在Java中有两种形式可以实现多态。继承和接口。

**面向对象五大原则：**

**(1)单一职责原则（Single-Resposibility Principle）**

**•一个类应该仅有一个引起它变化的原因**

职员类例子： 比如在职员类里，将工程师、销售人员、销售经理这些情况都放在职员类里考虑，其结果将会非常混乱，在这个假设下，职员类里的每个方法都要ifelse判断是哪种情况，从类结构上来说将会十分臃肿，并且上述三种的职员类型，不论哪一种发生需求变化，都会改变职员类！这个是大家所不愿意看到的！

**(2)开放封闭原则（Open-Closed principle）**

**•对扩展是开放的，对更改是封闭的！**

变化来临时，如果不必改动软件实体裁的源代码，就能扩充它的行为，那么这个软件实体设计就是满足开放封闭原则的。如果说我们预测到某种变化，或者某种变化发生了，我们应当创建抽象类来隔离以后发生的同类变化。

**(3)里氏替换原则（Liskov-Substituion Principle）**

**•子类可以替换父类并且出现在父类能够出现的任何地方，贯彻GOF倡导的面向接口编程****

在这个原则中父类应尽可能使用接口或者抽象类来实现！

子类通过实现了父类接口，能够替父类的使用地方！

通过这个原则，我们客户端在使用父类接口的时候，通过子类实现！

意思就是说我们依赖父类接口，在客户端声明一个父类接口，通过其子类来实现

这个时候就要求子类必须能够替换父类所出现的任何地方，这样做的好处就是，在根据新要求扩展父类接口的新子类的时候而不影响当前客户端的使用！

**(4)依赖倒置原则（Dependecy-Inversion Principle）**

**•传统的结构化编程中，最上层的模块通常都要依赖下面的子模块来实现，也称为高层依赖低层！**

所以DIP原则就是要逆转这种依赖关系，让高层模块不要依赖低层模块，所以称之为依赖倒置原则！

**(5)ISP 接口隔离原则(Interface-Segregation Principle)**

**•使用多个专门的接口比使用单个接口要好的多！**

这个我有体会，在我实际编程中，为了减少接口的定义，将许多类似的方法都放在一个接口中，最后发现，维护和实现接口的时候花了太多精力，而接口所定义的操作相当于对客户端的一种承诺，这种承诺当然是越少越好，越精练越好，过多的承诺带来的就是你的大量精力和时间去维护！

### ==18.java修饰符==

| 修饰符      | 当前类 | 同一包内 | 子孙类(同一包) | 子孙类(不同包) | 其他包 |
| :---------- | :----- | :------- | :------------- | :------------- | :----- |
| `public`    | Y      | Y        | Y              | Y              | Y      |
| `protected` | Y      | Y        | Y              | Y              | N      |
| `default`   | Y      | Y        | Y              | N              | N      |
| `private`   | Y      | N        | N              | N              | N      |

**声明为私有访问类型的变量只能通过类中公共的 getter 方法被外部类访问。**

### ==19.Spring循环依赖？==

spring对循环依赖的处理有三种情况： 

①**构造器的循环依赖**：这种依赖spring是处理不了的，直接抛出BeanCurrentlylnCreationException异常。 

②**单例模式下的setter循环依赖**：通过“三级缓存”处理循环依赖。 

③**非单例循环依赖**：无法处理。

spring单例对象的初始化大略分为三步：

1. createBeanInstance：实例化，其实也就是调用对象的构造方法实例化对象
2. populateBean：填充属性，这一步主要是多bean的依赖属性进行填充
3. initializeBean：调用spring xml中的init 方法。

从上面讲述的单例bean初始化步骤我们可以知道，循环依赖主要发生在第一、第二步。也就是构造器循环依赖和field循环依赖。 接下来，我们具体看看spring是如何处理三种循环依赖的。

#### 构造器循环依赖

this .singletonsCurrentlylnCreation.add(beanName）将当前正要创建的bean 记录在缓存中 Spring 容器将每一个正在创建的bean 标识符放在一个“当前创建bean 池”中， bean 标识符：在创建过程中将一直保持在这个池中，因此如果在创建bean 过程中发现自己已经在“当前 创建bean 池” 里时，将抛出BeanCurrentlylnCreationException 异常表示循环依赖；而对于创建 完毕的bean 将从“ 当前创建bean 池”中清除掉。

#### 单例模式 setter 循环依赖

Spring 为了解决单例的循环依赖问题，使用了 **三级缓存** ，递归调用时发现 Bean 还在创建中即为循环依赖

单例模式的 Bean 保存在如下的数据结构中：

```java
/** 一级缓存：用于存放完全初始化好的 bean **/
private final Map<String, Object> singletonObjects = new ConcurrentHashMap<String, Object>(256);

/** 二级缓存：存放原始的 bean 对象（尚未填充属性），用于解决循环依赖 */
private final Map<String, Object> earlySingletonObjects = new HashMap<String, Object>(16);

/** 三级级缓存：存放 bean 工厂对象，用于解决循环依赖 */
private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<String, ObjectFactory<?>>(16);

/**
bean 的获取过程：先从一级获取，失败再从二级、三级里面获取

创建中状态：是指对象已经 new 出来了但是所有的属性均为 null 等待被 init
*/
```

检测循环依赖的过程如下：

- A 创建过程中需要 B，于是 **A 将自己放到三级缓里面** ，去实例化 B

- B 实例化的时候发现需要 A，于是 B 先查一级缓存，没有，再查二级缓存，还是没有，再查三级缓存，找到了！

- - **然后把三级缓存里面的这个 A 放到二级缓存里面，并删除三级缓存里面的 A**
  - B 顺利初始化完毕，**将自己放到一级缓存里面**（此时B里面的A依然是创建中状态）

- 然后回来接着创建 A，此时 B 已经创建结束，直接从一级缓存里面拿到 B ，然后完成创建，**并将自己放到一级缓存里面**

- 如此一来便解决了循环依赖的问题

一句话：先让最底层对象完成初始化，通过三级缓存与二级缓存提前曝光创建中的 Bean，让其他 Bean 率先完成初始化。

#### 非单例循环依赖

对于“prototype”作用域bean, Spring 容器无法完成依赖注入，因为Spring 容器不进行缓 存“prototype”作用域的bean ，因此无法提前暴露一个创建中的bean 。

### ==20.Tomcat运行过程==

Tomcat 最重要的是两个组件是：Connector（连接器） 和 Container（容器/集装箱），Connector 组件是可以被替换，这样可以提供给服务器设计者更多的选择，因为这个组件是如此重要，不仅跟服务器的设计的本身，而且和不同的应用场景也十分相关，所以一个 Container 可以选择对应多个 Connector。

多个 Connector 和一个 Container 就组成一个 Service，有了 Service 就可以对外提供服务了，但是 Service 还要一个生存的环境， Server 就提供了这样一个环境。所以整个 Tomcat 的生命周期由 Server 控制。

![72](./images/72.png)

#### Server

Server 要完成的任务很简单，就是要能够提供一个接口让其它程序能够访问到 Service 集合，同时要维护它所包含的所有 Service 的生命周期，包括**如何初始化**、**如何结束服务**、**如何找到别人要访问的 Service**。还有一些次要的任务，如记录Service运行日志，维护Session等等。Server包含的组件结构如下：

![73](./images/73.png)

#### Service

Service 是在 Connector 和 Container 外面多包一层，把它们组装在一起，向外面提供服务，一个 Service 可以设置多个 Connector，但是只能有一个 Container 容器。

#### Container

Container本意是集装箱的意思，是一个接口，定义了下属的各种容器，重要的是Wrapper、Host、Engine、Context等

![74](./images/74.png)

**Engine(引擎)**

负责处理来自相关联的service的所有请求，处理后，将结果返回给service，而connector是作为service与engine的中间媒介出现的。
 一个engine下可以配置一个默认主机，每个虚拟主机都有一个域名。当engine获得一个请求时，它把该请求匹配到虚拟主机(host)上，然后把请求交给该主机来处理。
 Engine有一个默认主机，当请求无法匹配到任何一个虚拟主机时，将交给默认host来处理。Engine以线程的方式启动Host。

**Host**

代表一个虚拟主机，每个虚拟主机和某个网络域名（Domain Name）相匹配。
 每个虚拟主机下都可以部署一个或多个web应用，每个web应用对应于一个context，有一个context path。
 当Host获得一个请求时，将把该请求匹配到某个Context上，然后把该请求交给该Context来处理匹配的方法是“最长匹配”，所以一个path==””的Context将成为该Host的默认Context所有无法和其它Context的路径名匹配的请求都将最终和该默认Context匹配。

**Context**

一个Context对应于一个Web应用，一个Web应用由一个或者多个Servlet组成Context在创建的时候将根据配置文件`$CATALINA_HOME/conf/web.xml`和`$ WEBAPP_HOME/WEB-INF/web.xml`载入Servlet类。当Context获得请求时，将在自己的映射表(mapping table)中寻找相匹配的Servlet类，如果找到，则执行该类，获得请求的回应，并返回。

**Wrapper**

Wrapper 代表一个 Servlet，它负责管理一个 Servlet，包括的 Servlet 的装载、初始化、执行以及资源回收。Wrapper 是最底层的容器，它没有子容器了，所以调用它的 addChild 将会报错。
 Wrapper 的实现类是 StandardWrapper，StandardWrapper 还实现了拥有一个 Servlet 初始化信息的 ServletConfig，由此看出 StandardWrapper 将直接和 Servlet 的各种信息打交道。

**Connector**

Connector将在某个指定的端口上来监听客户的请求，把从socket传递过来的数据，封装成Request，传递给Engine来处理，并从Engine处获得响应并返回给客户。

Tomcat通常会用到两种Connector：

> 1. Http Connector 在端口8080处侦听来自客户browser的http请求。 AJP Connector
> 2. 在端口8009处侦听来自其它WebServer(Apache)的servlet/jsp代理请求。

**Lifecycle**

现实生活中大部分的事物都有生命周期，就像人的生老病死一样。

在编程中也有很多对象是具有生命周期的，从初始化、运行、回收等 会经历几个不同的阶段。 在tomcat中容器相关的好多组建都实现了Lifecycle接口，当tomcat启动时，其依赖的下层组件会全部进行初始化。 并且可以***对每个组件生命周期中的事件添加监听器\***。

例如当服务器启动的时候，tomcat需要去调用servlet的init方法和初始化容器等一系列操作，而停止的时候，也需要调用servlet的destory方法。而这些都是通过org.apache.catalina.Lifecycle接口来实现的。由这个类来制定各个组件生命周期的规范。

**LifecycleListener**

在Lifecycle的介绍中提到，Lifecycle会对每个组件生命周期中的事件添加监听器，也就是addLifecycleListener(LifecycleListener listener)方法，而LifecycleListener就是上面提到的监听器。

**LifecycleEvent**

顾名思义，就是当有监听事件发生的时候，LifecycleEvent会存储时间类型和数据

#### tomcat 的启动过程

tomcat的启动的起点是Server.start()方法，在这里它会依次启动`Container`和 `Connector`相关组件，最后到达EndPoint（Tomcat启动的Socket管理者），完成整个启动过程。如下图是个简易过程：

![75](./images/75.png)

#### Tomcat Server处理一个http请求的过程

假设来自客户的请求为：
http://localhost:8080/wsota/wsota_index.jsp

1) 请求被发送到本机端口8080，被在那里侦听的Coyote HTTP/1.1 Connector获得
2) Connector把该请求交给它所在的Service的Engine来处理，并等待来自Engine的回应
3) Engine获得请求localhost/wsota/wsota_index.jsp，匹配它所拥有的所有虚拟主机Host
4) Engine匹配到名为localhost的Host（即使匹配不到也把请求交给该Host处理，因为该Host被定义为该Engine的默认主机）
5) localhost Host获得请求/wsota/wsota_index.jsp，匹配它所拥有的所有Context
6) Host匹配到路径为/wsota的Context（如果匹配不到就把该请求交给路径名为””的Context去处理）
7) path=”/wsota”的Context获得请求/wsota_index.jsp，在它的mapping table中寻找对应的servlet
8) Context匹配到URL PATTERN为*.jsp的servlet，对应于JspServlet类
9) 构造HttpServletRequest对象和HttpServletResponse对象，作为参数调用JspServlet的doGet或doPost方法
10)Context把执行完了之后的HttpServletResponse对象返回给Host
11)Host把HttpServletResponse对象返回给Engine
12)Engine把HttpServletResponse对象返回给Connector
13)Connector把HttpServletResponse对象返回给客户browser

### ==21.Servlet介绍==

**servlet就是一个Java接口**

![](https://pic2.zhimg.com/80/v2-85bf84640fbc6b6e195b9c5b513b918f_1440w.jpg?source=1940ef5c)

servlet接口定义的是**一套处理网络请求的规范**，所有实现servlet的类，都需要实现它那五个方法，其中最主要的是两个生命周期方法 init()和destroy()，还有一个处理请求的service()，也就是说，所有实现servlet接口的类，或者说，所有想要处理网络请求的类，都需要回答这三个问题：

- 你初始化时要做什么
- 你销毁时要做什么
- 你接受到请求时要做什么

servlet部署在容器中（Tomcat），然后Tomcat负责和客户端进行交互。