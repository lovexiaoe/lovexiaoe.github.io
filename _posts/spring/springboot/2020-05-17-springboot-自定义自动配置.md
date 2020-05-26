---
title: springboot-自定义自动配置
published: true
category: springboot
---

### 理解自动配置beans
自动配置使用标准的@Configuration 类实现的。附加的@Conditional注解用于约束自动配置何时生效。通常，自动配置类使用
@ConditionalOnClass 和 @ConditionalOnMissingBean 注解。这确保了只有相关类被发现并且没有声明自己的@Configuration时，
自动配置才会生效。

### 自动配置参与者
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