# 京东-Java研发工程师-一面

- 时间：8.12 14:30 ~ 14:50
- 形式：电话面试

## 题目

### 1、自我介绍

### 2、项目介绍

### 3、redis中常用的数据结构？

### 4、redis为什么那么快？底层实现原理

### 5、HashMap的底层实现数据结构，平时看源码的方式？

### 6、Spring和Spring Boot的关系？

### ==7、Spring循环依赖问题，可以正常启动吗？==

### 8、ElasticSearch为什么查询快？

### 9、select id from A where type = 1， select name from A where type = 1，id是主键，type是索引，查询速度一样吗？

### 10、计算机网络层次结构？

### 11、Jvm内存区域？

### ==12、如何编程实现堆溢出？==

```java
public class HeapTest{
    byte[] a = new byte[1024*100];
    public static void main(String[] args){
        ArrayList<HeapTest> heaptests = new ArrayList<>();
        while(true){
            heaptests.add(new HeapTest());
        }
    }
}
```

heaptests 局部变量会被 GC Roots 引用，而 heaptests 中又大量引用了创建的对象。这些对象不会被垃圾回收也就导致会将内存撑爆。

### ==13、AQS介绍，源码介绍？==

### ==14、ThreadLocal介绍？==

### ==15、原子类实现原理？==

### ==16、Tomcat获取一个请求到java代码的执行过程？==

### ==17、Servelet介绍？==

### 18、反问

## 总结

- Java web 的基础需要去复习，而不是简单的使用框架。
- 并发还是需要继续补充。
- 问的都比较基础，包括项目、集合、并发、数据库、web

