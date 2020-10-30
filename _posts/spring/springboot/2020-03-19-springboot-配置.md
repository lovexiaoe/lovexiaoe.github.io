---
title: springboot-配置
published: true
category: springboot
---

### 修改默认的web内嵌服务器
内嵌的HTTP 服务器很容易被自定义，Spring默认支持tomcat,jetty和undertow。如下，tomcat被修改为undertow。
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```
### spring-boot-starter-parent
maven可以从spring-boot-starter-parent继承默认的特性，如下：
1. 默认的java1.8编译级别。
2. UTF-8 代码编码。
3. 提供了一些列定制的依赖清单。
4. 插件配置，资源过滤，开发环境配置等等。

#### 不使用spring-boot-starter-parent
不是每个项目都需要继承spring-boot-starter-parent项目，spring-boot-dependencies是spring-boot-starter-parent的父项目，
如果不想使用spring-boot-starter-parent，你可以使用spring-boot-dependencies。只能在dependencyManagement中使用，如下：
```xml
<dependencyManagement>
		<dependencies>
		<dependency>
			<!-- Import dependency management from Spring Boot -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.0.3.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
			<!--scope=import，type=pom表示在此pom中引入spring-boot-dependencies的pom的所有内容-->
		</dependency>
	</dependencies>
</dependencyManagement>
```
如果要覆盖spring-boot-dependencies中的依赖条目，可以在spring-boot-dependencies之前声明。如覆盖Spring Data release train的
版本。
```xml
<dependencyManagement>
	<dependencies>
		<!-- Override Spring Data release train provided by Spring Boot -->
		<dependency>
			<groupId>org.springframework.data</groupId>
			<artifactId>spring-data-releasetrain</artifactId>
			<version>Fowler-SR2</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-dependencies</artifactId>
			<version>2.0.3.RELEASE</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```

### starter
starter是包含了一组让项目启动的依赖。正式的Starter样式是`spring-boot-starter-*`。如`spring-boot-starter-data-jpa`。
除了spring-boot提供的正式starter，一些第三方社区也提供了一系列starter:
`https://github.com/spring-projects/spring-boot/blob/master/spring-boot-project/spring-boot-starters/README.adoc`

### 禁用自动配置
springBoot的自动配置是非侵入性的。可以使用自己的配置替换。如排除DataSource:
```java
@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```
也可以使用spring.autoconfigure.exclude在配置文件中定义。

如果想要查看项目中使用了哪些自动配置，用`--debug`选项启动项目。在idea中，在启动配置program arguments 中添加`--debug`。

### 获取自定义配置
通常项目中会将配置信息外置，放在yml、或者properties文件中。可以通过如下方式获取,如下获取配置的name属性。
```java
import org.springframework.stereotype.*;
import org.springframework.beans.factory.annotation.*;
@Component
public class MyBean {
    @Value("${name}")
    private String name;
    // ...
}
```

#### 配置随机值
在很多情况下可以配置随机值进去，这些随机配置可以生产数字、字符串、uuid等。
```
my.secret=${random.value}
my.number=${random.int}
my.bignumber=${random.long}
my.uuid=${random.uuid}
my.number.less.than.ten=${random.int(10)}
my.number.in.range=${random.int[1024,65536]}
```
random.int* 不包括最大值在内。

#### @Value配置默认值
```java
@Value("${NamesrvAddr:192.168.0.1}")
private String namesrvAddr;
```

#### @Value配置list和map
@Value只能处理单条属性，没有强的类型验证，在yaml格式配置map和list时，会比较麻烦。在配置list或者map时，推荐使用
ConfigurationProperties注解。
```java
@Value("#{'${scio.cloud.list}'.split(',')}")
private List<String> list;

@Value("#{${scio.cloud.maps}}")
private Map<String,String> maps;
```
yaml文件如下，这里需要使用如下格式，使用yaml的冒号格式会报错。
```yaml
scio.cloud.list: topic1,topic2,topic3
scio.cloud.maps: "{key1: 'value1', key2: 'value2'}"
```


#### 类型安全的配置属性值。
通过`@Value("${property}")`配置属性会有一些麻烦，springboot提供了ConfigurationProperties注解，引入了强类型管理和验证。
需要属性的设置setter方法。
```java
@Component
@ConfigurationProperties("acme")
public class AcmeProperties {
	private boolean enabled;
	private InetAddress remoteAddress;
	private final Security security = new Security();
	public static class Security {
		private String username;
		private String password;
		private List<String> roles = new ArrayList<>(Collections.singleton("USER"));
	}
    private Map<String,String> maps;
}
```
其yml配置如下：
```yaml
acme:
	remote-address: 192.168.1.1
	security:
		username: admin
		roles:
		  - USER
		  - ADMIN
    maps: 
        key1: value1
        key2: value2
```
在其他bean中使用配置，可以直接注入，如下：
```java
@Service
public class MyService {

	private final AcmeProperties properties;
	@Autowired
	public MyService(AcmeProperties properties) {
	    this.properties = properties;
	}
 	//...
	@PostConstruct
	public void openConnection() {
		Server server = new Server(this.properties.getRemoteAddress());
		// ...
	}

}
```

#### 从配置文件中加载
配置文件可以使用@PropertySource标注
```java
@Data
@Component
@ConfigurationProperties(prefix = "cnf")
@PropertySource(value = "ToolConfig.properties")
public class ToolConfig {
    private String redisHost;
    private String version;
}
```

#### 从pom.xml中获取配置
springboot无法直接读取pom.xml文件中的变量，需要通过properties文件变量做为沟通桥梁
```properties
cnf.version=1.0.0
cnf.redisHost=@cnf.redisHost@
```
如上图中的cnf.redisHost，是在pom.xml中的cnf.redisHost标签
```xml
<properties>
    <cnf.redisHost>192.168.0.6</cnf.redisHost>
</properties>
```


#### 配置属性转换
##### 时间段转换
springboot支持是时间段表示，默认是ms，如下：
```java
@ConfigurationProperties("app.system")
public class AppSystemProperties {
    //如果指定非ms的其他时间单位，需要使用DurationUnit注解。
	@DurationUnit(ChronoUnit.SECONDS)
	private Duration sessionTimeout;
	private Duration readTimeout;
}
```
属性支持的单位如下：
* ns 纳秒
* ms 毫秒
* s 秒
* m 分钟
* h 小时
* d 天

