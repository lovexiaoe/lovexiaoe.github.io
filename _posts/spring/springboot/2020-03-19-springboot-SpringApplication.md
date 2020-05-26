---
title: springboot-SpringApplication
published: true
category: springboot
---

SpringApplication 提供了一种简便的通过main方法启动一个spring 应用的方式。多数情况下只需要代理给静态方法
SpringApplication.run。
```java
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```
### web环境
SpringApplication根据你的需求创建正确的ApplicationContext，其机制如下：
* 如果Spring MVC被使用，AnnotationConfigServletWebServerApplicationContext 会被创建。
* 如果Spring MVC没有被使用，Spring WebFlux被使用AnnotationConfigReactiveWebServerApplicationContext 会被创建。
* 其他情况，AnnotationConfigApplicationContext 。

### 访问应用参数。
如果通过`SpringApplication.run(…​)`向应用传递了参数，那么可以通过ApplicationArguments 获取应用参数。
```java

@Component
public class MyBean {

	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt" 
	}
}
```

### 应用退出
每个SpringApplication 向JVM中注册了一个shutdown hook,用于正常关闭ApplicationContext。
例如可以通过实现`org.springframework.boot.ExitCodeGenerator`接口自定义系统退出码。此退出码会被传递给`System.exit()`。
```java
@SpringBootApplication
public class ExitCodeApplication {
	@Bean
	public ExitCodeGenerator exitCodeGenerator() {
		return () -> 42;
	}
	public static void main(String[] args) {
		System.exit(SpringApplication
				.exit(SpringApplication.run(ExitCodeApplication.class, args)));
	}
}
```