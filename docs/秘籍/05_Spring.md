# Spring

### 0.什么是Spring

Spring 是一个开源框架，是为了解决企业应用程序开发复杂性而创建的。框架的主要优势之一就是其分层架构，分层架构允许您选择使用哪一个组件，同时为 J2EE 应用程序开发提供集成的框架。Spring使用基本的JavaBean来完成以前只可能由EJB完成的事情。任何 Java 应用都可以从 Spring 中受益。Spring 的核心是**控制反转（IoC）和面向切面（AOP）**。简单来说，Spring 是一个分层的 JavaSE/EE full-stack(一站式) **轻量级开源框架**。

IoC 不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。

AOP即**Aspect-Oriented programming**的缩写，中文意思是面向切面（或方面）编程。他是一种思想，可在不改变程序源码的情况下为程序添加额外的功能。

### 1.简单介绍一下 Spring bean ;知道 Spring 的 bean 的作用域与生命周期吗?

在 Spring 中，那些组成**应用程序的主体及由 Spring IOC（控制反转） 容器所管理的对象**，被称之为 bean。

简单地讲，bean 就是**由 IOC 容器初始化、装配及管理的对象**，除此之外，bean 就与应用程序中的其他对象没有什么区别了。

而 bean 的定义以及 bean 相互间的依赖关系将通过**配置元数据**来描述。

Spring中的bean默认都是**单例**的，这些单例Bean在多线程程序下如何保证线程安全呢？ 例如对于Web应用来说， Web容器对于每个用户请求都创建一个单独的Sevlet线程来处理请求，引入Spring框架之后，每个Action都是单例的，那么对于 Spring 托管的单例 Service Bean，如何保证其安全呢？ 

Spring 的单例是**基于 BeanFactory 也就是 Spring 容器**的，单例 Bean 在此**容器内只有一个**，Java的单例是基于 JVM，每个 JVM 内只有一个实例。

**作用域一般指对象或变量之间的可见范围。**

而在Spring容器中**是指其创建的Bean对象相对于其他Bean对象的请求可见范围**。

在Spring 容器当中，一共提供了5种作用域类型，在配置文件中，通过属性scope来设置bean的作用域范围。

![18](/Users/wx/project/interview/docs/秘籍/images/18.png)

Spring 容器可以管理 singleton 作用域下 bean 的生命周期，在此作用域下，Spring 能够精确地知道bean何时被创建，何时初始化完成，以及何时被销毁。而对于 prototype 作用域的bean，Spring只负责创建，当容器创建了 bean 的实例后，bean 的实例就交给了客户端的代码管理，Spring容器将不再跟踪其生命周期，并且不会管理那些被配置成prototype作用域的bean的生命周期。

Spring Bean的生命周期是这样的：

- Bean容器找到配置文件中 Spring Bean 的定义。
- Bean容器利用 Java Reflection API 创建一个Bean的实例。
- 如果涉及到一些属性值利用set方法设置一些属性值。
- 如果Bean实现了BeanNameAware接口，调用setBeanName()方法，传入Bean的名字。
- 如果Bean实现了BeanClassLoaderAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 如果Bean实现了BeanFactoryAware接口，调用setBeanClassLoader()方法，传入ClassLoader对象的实例。
- 与上面的类似，如果实现了其他*Aware接口，就调用相应的方法。
- 如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessBeforeInitialization()方法
- 如果Bean实现了InitializingBean接口，执行afterPropertiesSet()方法。
- 如果Bean在配置文件中的定义包含init-method属性，执行指定的方法。
- 如果有和加载这个Bean的Spring容器相关的BeanPostProcessor对象，执行postProcessAfterInitialization()方法
- 当要销毁Bean的时候，如果Bean实现了DisposableBean接口，执行destroy()方法。
- 当要销毁Bean的时候，如果Bean在配置文件中的定义包含destroy-method属性，执行指定的方法。

![19](/Users/wx/project/interview/docs/秘籍/images/19.png)

### 2.Spring 中的事务传播行为了解吗? TransactionDeﬁnition 接口中哪五个表示隔离级别的常量?

**事务传播行为**

事务传播行为（为了解决业务层方法之间互相调用的事务问题）： **当事务方法被另一个事务方法调用时，必须指定事务应该如何传播**。例如：方法可能继续在现有事务中运行，也可能开启一个新事务，并在自己的事务中运行。

在 TransactionDeﬁnition定义中包括了如下几个表示传播行为的常量：

**支持当前事务的情况：**

- **TransactionDeﬁnition.PROPAGATION_REQUIRED**： 如果当前存在事务，则加入该事务；如果当前没有事务， 则创建一个新的事务。
- **TransactionDeﬁnition.PROPAGATION_SUPPORTS**： 如果当前存在事务，则加入该事务；如果当前没有事务， 则以非事务的方式继续运行。 
- **TransactionDeﬁnition.PROPAGATION_MANDATORY**： 如果当前存在事务，则加入该事务；如果当前没有事务，则抛出异常。（mandatory：强制性）

**不支持当前事务的情况：**

- **TransactionDeﬁnition.PROPAGATION_REQUIRES_NEW**： 创建一个新的事务，如果当前存在事务，则把当前事务挂起。 
- **TransactionDeﬁnition.PROPAGATION_NOT_SUPPORTED**： 以非事务方式运行，如果当前存在事务，则把当前事务挂起。 
- **TransactionDeﬁnition.PROPAGATION_NEVER**： 以非事务方式运行，如果当前存在事务，则抛出异常。

**其他情况：**

- **TransactionDeﬁnition.PROPAGATION_NESTED**： 如果当前存在事务，则创建一个事务作为当前事务的嵌套事 务来运行；如果当前没有事务，则该取值等价于TransactionDeﬁnition.PROPAGATION_REQUIRED。

**隔离级别**

TransactionDeﬁnition 接口中定义了**五个**表示隔离级别的常量（后四个分别对应于mysql的事物隔离级别）：

- **TransactionDeﬁnition.ISOLATION_DEFAULT**: 使用后端数据库默认的隔离级别，Mysql 默认采用的 **REPEATABLE_READ** 隔离级别（加锁之后可以实现到SERIALIZABLE级别） Oracle 默认采用的 READ_COMMITTED隔离级别；
- **TransactionDeﬁnition.ISOLATION_READ_UNCOMMITTED**: 最低的隔离级别，**允许读取尚未提交的数据变更**，可能会导致**脏读、幻读或不可重复读** ；
- **TransactionDeﬁnition.ISOLATION_READ_COMMITTED**: **允许读取并发事务已经提交的数据**，可以**阻止脏读，但是幻读或不可重复读仍有可能发生** ；
- **TransactionDeﬁnition.ISOLATION_REPEATABLE_READ**: **对同一字段的多次读取结果都是一致的**，除非数据是被本身事务自己所修改，可以**阻止脏读和不可重复读，但幻读仍有可能发生**。 
- **TransactionDeﬁnition.ISOLATION_SERIALIZABLE**: 最高的隔离级别，完全服从ACID的隔离级别。**所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰**，也就是说，该级别**可以防止脏读、不可重复读以及幻读**。但是这将严重影响程序的性能。通常情况下也不会用到该级别。

### 3.SpringMVC 原理了解吗?

![20](/Users/wx/project/interview/docs/秘籍/images/20.png)

客户端发送请求 ->  前端控制器 DispatcherServlet 接受客户端请求  ->  找到处理器映射 HandlerMapping 解析请求对应的 Handler ->  HandlerAdapter 会根据 Handler 来调用真正的处理器（Controller）开始处理请求，并处理相应的业务逻辑（Service）  ->  处理器返回一个模型视图 ModelAndView  ->  视图解析器进行解析  ->  返回一个视图对象 -> 前端控制器 DispatcherServlet 渲染数据（Model）-> 将得到视图对象返回给用户。

### 4.Spring AOP IOC 实现原理？

**IOC**: **控制反转也叫依赖注入**。

IOC利用**java反射机制**，AOP利用**代理模式**。

IOC 概念看似很抽象，但是很容易理解。 说简单点就是将对象交给容器管理，你只需要在spring配置文件中配置对应的bean以及设置相关的属性，让spring 容器来生成类的实例对象以及管理对象。在spring容器启动的时候，spring会把你在配置文件中配置的bean都初始化好，然后在你需要调用的时候，就把它已经初始化好的那些bean分配给你需要调用这些bean的类。

**AOP**： **面向切面编程**。（Aspect-Oriented Programming）。

AOP可以说是对OOP的补充和完善。OOP引入封装、 继承和多态性等概念来建立一种对象层次结构，用以模拟公共行为的一个集合。

实现AOP的技术，主要分为两大类： 

- 采用**动态代理技术**，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；
- 采用**静态织入**的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码，属于静态代理。

### 5.Eureka是如何进⾏行行服务注册的？

Eureka是Netflix开发的服务发现框架，本身是一个基于REST的服务，主要用于定位运行在AWS域中的中间层服务，以达到负载均衡和中间层服务故障转移的目的。SpringCloud将它集成在其子项目spring-cloud-netflix中，以实现SpringCloud的服务发现功能。

Eureka包含两个组件：Eureka Server和Eureka Client。

调用关系说明：

1.服务提供者在启动时，向注册中心注册自己提供的服务。
 2.服务消费者在启动时，向注册中心订阅自己所需的服务。
 3.注册中心返回服务提供者地址给消费者。
 4.服务消费者从提供者地址中调用消费者。
 注意！ 下面的服务端指：注册中心，客户端指：服务提供者和消费者

**Eureka Server**：提供服务注册服务，各个节点启动后，会在Eureka Server中进行注册，包括**主机与端口号、服务版本号、通讯协议**等。这样EurekaServer中的**服务注册表**中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观的看到。
Eureka服务端支持集群模式部署，首尾相连形成一个闭环即可，集群中的的不同服务注册中心通过**异步模式**互相复制各自的状态，这也意味着在给定的时间点每个实例关于所有服务的状态可能存在不一致的现象。

**eureka客户端**，主要处理服务的注册和发现。客户端服务通过注册和参数配置的方式，嵌入在客户端应用程序的代码中。在应用程序启动时，Eureka客户端向服务注册中心注册自身提供的服务，并周期性的发送**心跳**来更新它的服务租约。同时，他也能从服务端查询当前注册的服务信息并把它们缓存到本地并周期性的刷新服务状态。

**服务调用**
服务消费者在获取服务清单后，通过服务名可以获取具体提供服务的实例名和该实例的元数据信息。因为有这些服务实例的详细信息，所以客户端可以根据自己的需要决定具体调用哪个实例，在Ribbon中会默认采用轮询的方式进行调用，从而实现客户端的负载均衡。

### 6.如果服务宕机或者⽆无法访问了了，我还去请求该服务，Eureka会怎么处理？ 会有什么现象？

当服务提供者出现网络故障，无法与 Eureka Server 进行续约，Eureka Server 会将该实例移除。

会报服务不存在的错误。

### 7.Eureka自我保护模式

Eureka Server 的自我保护机制会检查最近`15分钟`内所有Eureka Client正常心跳的占比，如果低于`85%`就会被触发。

当触发自我保护机制后Eureka Server就会锁定服务列表，不让服务列表内的服务过期，不过这样我们在访问服务时，得到的服务很有可能是已经失效的实例，如果是这样我们就会无法访问到期望的资源，会导致服务调用失败，所以这时我们就需要有对应的容错机制、熔断机制。