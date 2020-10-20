---
title: Synchronized
published: true
category: java
---

### synchronized锁机制升级
Java中对悲观锁思想的实现就是我们最常使用的synchronized同步块。在java1.6前，synchronized被称为重量级锁，java1.6对synchronized进行了
各种优化。减轻了获取锁和释放锁带来的性能开销，引入了偏向锁和轻量级锁。

#### synchronized使用场景
* Synchronized修饰普通同步方法：锁对象当前实例对象；
* Synchronized修饰静态同步方法：锁对象是当前的类Class对象；
* Synchronized修饰同步代码块：锁对象是Synchronized后面括号里配置的对象，这个对象可以是某个对象（xlock），也可以是某个类（Xlock.class）。

**注意：**

* 使用synchronized修饰非静态方法或者代码块指定修饰的对象为实例对象时，同一个类的不同对象拥有自己的monitor，因此不会相互阻塞。
* 使用synchronized修饰类和对象时，由于类和实例都拥有自己的monitor，因此不会相互阻塞。
* 一个对象只有一个monitor，所以当一个线程正在访问实例对象的一个synchronized方法时，其它线程不能访问使用该对象monitor的代码。
* 线程A访问实例对象的非static synchronized方法时，线程B也可以同时访问实例对象的static synchronized方法，因为前者获取的是实例对象的monitor，
而后者获取的是类对象的monitor，两者不存在互斥关系。

### Object Monitor
java对象在堆内存中的可以分为三部分：对象头（Header）、实例数据（Instance Data）和对齐填充区域（可能存在）。对齐填充区域为了让对象大小是8字节的
整数倍。实例数据描述真是的对象数据。而对象头中又有三部分：
* 数组长度      
表达数组长度，只有数组形式的对象才有
* klass     
是一个指针区域，指向元数据区中（JDK1.8）该对象所代表的类描述，这样JVM才知道这个对象是哪一个类的实例。
* markword      
对象关键的运行时数据，主要就是这个对象当前锁机制的记录信息。同时也记录了对象的锁信息。如下图所示：       
[](/assets/java/synchronized-1.jpg)




