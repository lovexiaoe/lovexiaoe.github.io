---
title: springboot-其他
published: true
category: springboot
---

### 配置SSL
ssl可以在application.properties文件中配置如下：
```
server.port=8443
server.ssl.key-store=classpath:keystore.jks
server.ssl.key-store-password=secret
server.ssl.key-password=another-secret
```
springboot只支持http或者https其中一种访问，如果需要支持两种都访问，那么就需要在程序中配置。
tomcat配置如下：
```java
@SpringBootApplication
public class SampleTomcatTwoConnectorsApplication {

	@Bean
	public ServletWebServerFactory servletContainer() {
		TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
		tomcat.addAdditionalTomcatConnectors(createStandardConnector());
		return tomcat;
	}

	private Connector createStandardConnector() {
		Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
		connector.setPort(0);
		return connector;
	}

	public static void main(String[] args) {
		SpringApplication.run(SampleTomcatTwoConnectorsApplication.class, args);
	}

}
```

### 配置访问日志
可以在springboot中配置tomcat等web服务器的日志，如下：
```
server.tomcat.basedir=my-tomcat
server.tomcat.accesslog.enabled=true
server.tomcat.accesslog.pattern=%t %a "%r" %s (%D ms)
```

