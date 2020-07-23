# Spring

### 0.什么是Spring

Spring是一个轻量级控制反转(IOC)和面向切面(AOP)的容器框架。主要是为了解决企业应用开发的复杂性而诞生的，框架的主要优势之一就是其分层结构，分层结构允许使用者使用哪一个组件。同时也为J2EE开发提供了集成的框架。

![](https://img-blog.csdn.net/20160518231149640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

Spring的核心模块：

- **核心容器(SpringCore)**：提供了Spring框架的基本功能，核心容器的主要组件是BeanFactory，他是工厂模式的实现。Bean Factory使用控制反转的模式将应用程序的配置和依赖性规范与实际的应用程序代码相分开。
- **应用上下文(SpringContext)**：是一个配置文件，向Spring模块提供上下文信息。Spring上下文包含了一些企业服务，例如：JNDI、EJB、电子邮件、国际化、校验、调度等功能。
- **AOP模块(Spring AOP)** ：通过配置管理特性，Spring AOP模块直接将面向切面的编程功能集成到了Spring框架当中。所以，可以很容易的使Spring框架管理的任何对象都可以支持AOP。Spring的AOP模块为基于Spring的应用程序中的对象提供了事物管理功能，通过使用Spring AOP不用依赖EJB组件就可以将声明性事物管理集成到应用程序当中。
- **JDBC和DAO模块(Spring DAO)**：JDBC、DAO的抽象层提供了有意义的异常层次结构,可用该结构来管理异常处理和不同数据库供应商所抛出的异常信息。异常层次结构简化了错误处理,并且极大的降低了需要编写的异常处理代码数量。例如打开和关闭连接等等。SpringDAO的面向切面，JDBC的异常遵从通用的DAO异常层次结构。
- **对象实体映射（SpringORM）**:Spring框架插入了若干个ORM框架，从而提供了ORM对象的关系工具。其中包括JDO、Hibernate… …所有这些都遵从Spring的通用事物和DAO异常层次结构。
- **Web模块(Spring Web)**：Web上下文模块建立在应用程序上下文模块之上,为基于Web的应用程序提供了上下文，所以Spring框架支持与Struts的集成。Web模块还简化了处理多部分请求以及将请求参数绑定到预对象的操作。
- **MVC模块(Spring WebMVC)**：Spring的MVC是一个全功能的构建Web应用程序的MVC的实现。通过策略接口,MVC框架变成为高度可配置的。MVC容纳了大量视图技术。模型来由JavaBean来构成，存放与Map当中。而视图是一个接口，负责实现模型。控制器是一个逻辑代码，是Control的实现。

Spring 的核心是**控制反转（IoC）和面向切面（AOP）**。

IoC 不是一种技术，只是一种思想，一个重要的面向对象编程的法则，它能指导我们如何设计出松耦合、更优良的程序。

AOP即**Aspect-Oriented programming**的缩写，中文意思是面向切面（或方面）编程。他是一种思想，可在不改变程序源码的情况下为程序添加额外的功能。

### ==1.简单介绍一下 Spring bean ;知道 Spring 的 bean 的作用域与生命周期吗?==

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

### ==3.SpringMVC 原理了解吗?==

原先的Web开发模型是MVC模式的：

**M 代表 模型（Model）**
 模型是什么呢？ 模型就是数据，就是 dao,bean

**V 代表 视图（View）**
 视图是什么呢？ 就是网页, JSP，用来展示模型中的数据

**C 代表 控制器（controller)**
 控制器是什么？ 控制器的作用就是把不同的数据(Model)，显示在不同的视图(View)上，Servlet 扮演的就是这样的角色。

为解决持久层中一直未处理好的数据库事务的编程，又为了迎合 NoSQL 的强势崛起，Spring MVC 给出了方案：

![](./images/65.png)

**传统的模型层被拆分为了业务层(Service)和数据访问层（DAO,Data Access Object）。** 在 Service 下可以通过 Spring 的声明式事务操作数据访问层，而在业务层上还允许我们访问 NoSQL ，这样就能够满足异军突起的 NoSQL 的使用了，它可以大大提高互联网系统的性能。

也就是说**SpringMVC就是将原先的MVC结构变为了Controller、Service、Dao三层**。

SpringMVC的实现原理？SpringMVC的访问流程？

![66](./images/66.png)

首先客户端发送过来请求交由**前端控制器 DispatcherServlet** 进行处理，然后 DispatcherServlet 找到处理器映射来解析请求，之后会调用对应的 Controller 来处理请求，并执行响应的 Service 业务逻辑，执行完之后会返回一个模型试图 ModelAndView，然后将其交由对应的是图解析器进行解析，然后返回一个试图对象，最后前段控制器会对试图进行渲染，然后将试图进行返回。

这就是SpringMVC的实现原理，主要就是前端控制器 DispatcherServlet 发挥主要的工作，然后分别调用对应的 Controller、Service等创建视图，然后对视图进行渲染并返回。这也是 SpringMVC 接受请求所对应的执行过程。

### ==4.Spring AOP IOC 实现原理？==

spring核心框架里面，最关键的两个机制，就是 ioc 和 aop。

介绍的时候按照：**是什么？原理是什么？是怎么用的？解决了什么问题，实现的效果是什么样的？**重点是**思想**的介绍。

**1）Spring IOC**

**Spring IOC**: **控制反转也叫依赖注入**。之前使用 xml 表示，目前都是使用注解的方式实现。

IOC 概念看似很抽象，但是很容易理解。 说简单点就是将对象交给容器管理，你只需要在spring配置文件中配置对应的bean以及设置相关的属性，让spring 容器来生成类的实例对象以及管理对象。

（可以介绍下没有使用 Spring IOC的时候对象之间调用的耦合情况，在使用了Spring IOC 之后这种耦合情况由 Spring 容器来负责进行处理，做到了解耦合的效果）

在spring容器启动的时候，spring会把你在配置文件中配置的bean都初始化好，然后在你需要调用的时候，就把它已经初始化好的那些bean分配给你需要调用这些bean的类，然后根据 bean 之间的依赖关系来自动进行依赖的注入，让类与类之间彻底的解耦和。

总的来说就是：**根据注解去实例化对应的bean，管理bean之间的依赖注入，让类与类之间解耦，维护代码的时候可以更加轻松**

**2）Spring IOC的实现原理**

IOC底层利用**java反射机制**，利用反射的技术，会直接根据类来构建对应的对象出来，然后在需要的地方将其进行注入。

**3）Spring AOP** 

**Spring AOP：面向切面编程**。定义一个切面，然后织入一些代码，让所有程序执行到对应切面的时候去执行织入的代码。

比如事务的实现就是基于 AOP 来实现的，也就是 Transacional 注解。

**4）Spring AOP的实现原理**

Spring AOP利用**动态代理技术**，生成代理对象，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行。

动态代理：动态的创建一个代理类，在创建代理类的实例对象，然后在代理类的实例对象里引用自己写的类，所有的方法调用都会先走代理类对象，负责做一些代码上的增强，再去调用自己写的类。

动态代理的实现技术：**cglib动态代理、jdk动态代理**。

**5）cglib、jdk动态代理的区别**

主要的区别是根据 Spring 生成动态代理类的形式有关的。

如果需要动态代理的类**实现了某个接口**，那么Spring aop会使用**jdk动态代理**生成一个实现同样接口的一个代理类，然后构造一个实例对象出来。也就是说**jdk动态代理只有在类有接口的时候才会使用**。

假如需要动态代理的类**没有实现接口**，spring aop则会该用**cglib动态代理**来生成该类的一个子类，该子类可以动态的生成字节码，然后重写一些原有的方法，在方法中加入增强的代码。

### 5.SpringBoot介绍，如何启动一个Springboot工程？

SpringBoot是一个简化Spring项目开发的脚手架工具。

使用 @SpringBootApplication 注解来指定一个 Springboot 微服务的启动入口。在对应的启动入口的 main 方法中使用SpringApplication.run(ManageCourseApplication.class, args);来启动一个 Springboot 工程。

### 6.常见的Spring中的注解？

- SpringBootApplication：标注一个Springboot项目
- Service：标注业务逻辑
- Controller：标注视图控制器
- RestController：标注这是一个返回json对象的视图控制器
- RequestMapping：指定视图控制器的访问路径
- Getmapping：指定get请求的路径
- PostMapping：指定post请求的路径
- Configuration：指定这是一个配置对象
- FeignClient：指定feign远程调用的路径

### 7.Eureka是如何进⾏行行服务注册的？

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

### 8.如果服务宕机或者⽆无法访问了了，我还去请求该服务，Eureka会怎么处理？ 会有什么现象？

当服务提供者出现网络故障，无法与 Eureka Server 进行续约，Eureka Server 会将该实例移除。

会报服务不存在的错误。

### 9.Eureka自我保护模式

Eureka Server 的自我保护机制会检查最近`15分钟`内所有Eureka Client正常心跳的占比，如果低于`85%`就会被触发。

当触发自我保护机制后Eureka Server就会锁定服务列表，不让服务列表内的服务过期，不过这样我们在访问服务时，得到的服务很有可能是已经失效的实例，如果是这样我们就会无法访问到期望的资源，会导致服务调用失败，所以这时我们就需要有对应的容错机制、熔断机制。

### ==10.Transactional注解是什么？可能会带来的问题？==

**1）什么是Transactional注解**

在Spring中@Transactional提供一种声明式控制事务管理的方式，基于AOP动态代理的机制，提供了一种透明的事务管理方式，方便快捷解决在开发中碰到的问题。

一般在service里加@Transactional注解，不建议在接口上添加。添加了注解之后会对加了此注解后每个业务方法执行时，都会开启一个事务，不过都是按照相同的管理机制。

**2）Transactional注解会带来什么问题？**

Transactional会导致**事务失效**的问题发生，可能会发生事务失效的场景有如下几个：

1、**@Transactional注解只能应用到public修饰符上，应用在其它修饰符上会失效，但不报错**。可以**使用 AspectJ 取代 Spring AOP** 代理来解决。

2、**默认情况下此注解会对unchecked异常进行回滚，对checked异常不回滚。**（java里面将派生于Error或者RuntimeException（比如空指针，除0）的异常称为unchecked异常，其他继承自java.lang.Exception得异常统称为Checked Exception，如IOException、TimeoutException等。也就是说编译器能检测到的是checked，检测不到的就是unchecked）

3、**同类方法调用，假如被同类其他没有使用 Transactional 注解注释的方法去调用一个注释了 Transactional 注解的方法会导致这个 Transactional 注释失效。**这是由于使用 Spring AOP 代理造成的，因为只有当事务方法被当前类以外的代码调用时，才会由Spring生成的代理对象来管理。可以**使用 AspectJ 取代 Spring AOP** 代理来解决。

4、**事务 propagation（事务传播行为）属性配置出错**。若是错误的配置以下三种 propagation，事务将不会发生回滚：**TransactionDefinition.PROPAGATION_SUPPORTS**：如果当前存在事务，则加入该事务；如果当前没有事务，则以非事务的方式继续运行。** TransactionDefinition.PROPAGATION_NOT_SUPPORTED**：以非事务方式运行，如果当前存在事务，则把当前事务挂起。 **TransactionDefinition.PROPAGATION_NEVER**：以非事务方式运行，如果当前存在事务，则抛出异常。

5、**在 Transaction 注解中使用 try-catch**，catch会将异常进行处理，导致Transactional不会捕获到对应的异常。

6、**数据库引擎设置出错**，数据库引擎 MyISAM不支持事务，只有 InnoDB 是支持事务的。



