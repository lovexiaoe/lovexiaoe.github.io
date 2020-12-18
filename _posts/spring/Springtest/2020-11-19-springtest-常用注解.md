---
title: springtest-常用注解
published: true
category: springtest
---

### 主要注解
1. @RunWith(SpringRunner.class)： 让测试运行在spring环境中。
1. @SpringBootTest：代表一个springboot测试 启动springboot环境。
1. @AutoConfigureMockMvc：注入一个MockMvc实例，用于模拟Mvc对象。
1. @WebAppConfiguration：用来声明ApplicationContext将是一个WebApplicationContext。
1. @Rollback：测试事务回滚。
1. @Test：声明测试的方法。
1. @Before：在测试开始前进行的初始化工作。
1. @After：在测试方法之后执行的操作方法。
1. @WithUserDetails：登录spring security的用户。如下指定admin登录。
```
@WithUserDetails(value = "admin", userDetailsServiceBeanName = "customUserDetailsService")
```
