---
title: springboot-database
published: true
category: springboot
---

### 支持内嵌数据库
使用内存的内嵌数据库非常方便，springboot可以自动配置H2,HSQL，Derby数据库。如下：
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
	<groupId>org.hsqldb</groupId>
	<artifactId>hsqldb</artifactId>
	<scope>runtime</scope>
</dependency>
```

### 使用连接池
生产库使用连接池，springboot使用如下算法选择连接池。
1. HikariCP性能和并发优秀，如果可用，那么始终会选择它。
1. 否则，tomcat数据源连接池可用，我们选择使用它。
1. 最后，如果Commons DBCP2可用，选择它。

如果使用了spring-boot-starter-jdbc和spring-boot-starter-data-jpa starter。会自动使用HikariCP。
