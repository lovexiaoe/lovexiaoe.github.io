---
title: springboot-常用注解
published: true
category: springboot
---
### @EnableAutoConfiguration
该标记加载springboot的默认配置。springboot会给项目自动加载一系列默认的配置。如：
* ServerPropertiesAutoConfiguration
* RepositoryRestMvcAutoConfiguration
* JpaRepositoriesAutoConfiguration
* JmsAutoConfiguration

也可以排除某些自动加载项。如DataSource
```
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
```

### @SpringBootApplication
SpringBootApplication 通常声明在main方法上，项目是基于该注解所在的包来扫描注解的。如JPA项目是基于SpringBootApplication注解
的包来 搜索@Entity对象的。

SpringBootApplication注解包含了EnableAutoConfiguration、Configuration和ComponentScan 注解。所以，如果你不想使用
SpringBootApplication，可以使用EnableAutoConfiguration、Configuration和ComponentScan注解代替SpringBootApplication。

### @Configuration
这是spring基于java的配置方式，代替基于xml的配置方式，同样用@Bean注解代替xml中的`<Bean>`标记。可以将需要
加载的java类都放到@Configuration中。@Configuration通常定义在有main方法的类上，可以定义多个@Configuration。

### @Import
当然也可以不将所有的java类都放到@Configuration中，使用@Import导入外部的配置类。        
声明一个@Configuration外的Bean，如：
```java
public class TestBean1 {
}

@Import({TestBean1.class})
@Configuration
public class AppConfig {
}
```
当然Import还可以导入ImportSelector的实现类或ImportBeanDefinitionRegistrar的实现类。

### 导入XML配置
如果你必须要使用XML配置，仍然建议建立一个@Configuration注解的类，使用@ImportResource加载一个XML配置文件。