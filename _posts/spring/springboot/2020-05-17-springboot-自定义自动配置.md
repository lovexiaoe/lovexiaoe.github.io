---
title: springboot-自定义自动配置
published: true
category: springboot
---

### 理解自动配置beans
自动配置使用标准的@Configuration 类实现的。附加的@Conditional注解用于约束自动配置何时生效。通常，自动配置类使用
@ConditionalOnClass 和 @ConditionalOnMissingBean 注解。这确保了只有相关类被发现并且没有声明自己的@Configuration时，
自动配置才会生效。

### 自动配置生效
springboot从META-INF/spring.factories中检测自动配置的参与者，所有的配置类必须放在EnableAutoConfiguration 关键字后面。例如：
```
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.mycorp.libx.autoconfigure.LibXAutoConfiguration,\
com.mycorp.libx.autoconfigure.LibXWebAutoConfiguration
```
如果你希望有你的配置按照一定的顺序执行，可以使用 @AutoConfigureAfter 和 @AutoConfigureBefore 注解。如，你有一个web相关的配置，
那么需要在WebMvcAutoConfiguration后执行。

### 条件注解
springboot 包含了一系列@Conditional注解，让我们可以在@Configuration类或者单独的@Bean方法上使用。
#### class 条件
@ConditionalOnClass 和 @ConditionalOnMissingClass 注解根据指定类的出现和未出现让配置生效。

#### bean 条件
@ConditionalOnBean 和 @ConditionalOnMissingBean 根据class的bean实例是否存在生效配置。
```java
@Configuration
public class MyAutoConfiguration {
	@Bean
	@ConditionalOnMissingBean
	public MyService myService() {}
}
```
上面代码表示：如果ApplicationContext没有MyService类型的bean，那么myService方法会创建一个bean。       
为了避免加载出现异常情况，建议只在自动配置类中使用@ConditionalOnBean 和 @ConditionalOnMissingBean。

#### Property 条件
@ConditionalOnProperty根据某个spring的环境属性让配置生效。使用`prefix`和`name`指定需要检查的属性。也可以使用更高级的`havingValue`和
`matchIfMissing`属性。havingValue表示属性中有某个值时生效，matchIfMissing表示属性中没有某个值时生效。

#### starter命名
命名通常是starter的组合，如创建一个一个acme的starter，那么可以命名为acme-spring-boot-autoconfigure 和 acme-spring-boot-starter。
