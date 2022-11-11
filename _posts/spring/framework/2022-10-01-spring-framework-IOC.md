---
title: spring-IOC
published: true
category: spring
---

IOC(控制翻转)是指bean对象由spring容器管理，包括bean对象的创建和销毁等阶段。DI(依赖注入)是spring实现控制翻转的途径。
spring官方文档里说明了IOC即DI。可以理解为IOC是一种目的。而DI即手段。

IOC传统上是通过加载XML文件来获取bean对象的，其流程和获取方式如下：      
![](/assets/spring/framework/ioc-1.png)     

IOC的整个过程是将对象的定义转换为bean实例的过程。其流程大概如下：        
![](/assets/spring/framework/IOC-2.png)     

其中BeanDefinition接口承载了xml定义转换为java对象的定义信息。它描述了一个bean实例，有哪些属性，构造方法参数以及scope,懒加载、等xml
中提供的更多信息。spring中实现类为AbstractBeanDefinition，这个类中包括了instanceSupplier(实例提供者)，
constructorArgumentValues（构造参数）initMethodNames（初始化方法数组），destroyMethodNames（销毁方法数组等）。            
bean在spring中会被放在一个容器中，而实现这个容器的形式就是map,使用多个map存放对象。方便通过多种方式（byName,byType）获取bean。通过这些
多组map实现一个三级缓存，解决循环依赖的问题，这点和IOC关系不大。可以暂时不用管。

BeanFactory是IOC的根接口。其中实现的默认类是DefaultListableBeanFactory，其类图如下：      
![](/assets/spring/framework/DefaultListableBeanFactory类图.png)

Bean的生命周期，也就是bean的实例化和销毁的过程。在这个过程中，spring实现了许多扩展。在容器初始化的时候，容器也需要很多对象，包括
DefaultListableBeanFactory等，需要提前创建好，源码中是通过new来创建的。其主要流程如下：      
![](/assets/spring/framework/bean的实例化.png)      
bean的初始化的流程顺序是由spring定义好的，在BeanFactory注解中有写明。

spring实例化对象的时候也是通过反射来实现的，普通对象的反射过程如下：       
![](/assets/spring/framework/反射创建对象的方法.png)

一个普通的类实例化的过程如下，方便我们理解bean的实例化。      
![](/assets/spring/framework/创建对象的过程和后置处理器的分类.png)
init-method可以定义我们在初始化过程中需要执行的任何动作，如打印一些信息等。Spring中bean的init-method类似。
PostProcessor称为后置处理器，也称为增强器。Aware接口用于设置Spring框架对象，如BeanFactory等。通过框架对象
我们可以实现更强大的功能。
