---
title: springboot-WebApplication
published: true
category: springboot
---

web应用通过引入spring-boot-starter-web可以快速创建和启动web应用。如果想创建交互式的web应用，通过引入
spring-boot-starter-webflux模块。

### SpringMVC

#### SpringMVC自动配置
springboot提供了Spring MVC的自动配置，默认引入如下特性。
* 包括ContentNegotiatingViewResolver 和BeanNameViewResolver 。
* 支持静态资源服务，包括WebJars。
* Converter，GenericConverter，Formatter 自动注册。
* 支持HttpMessageConverters 。
* MessageCodesResolver自动注册。
* 静态index.html支持。
* 自定义的Favicon 支持。
* 自动使用一个ConfigurableWebBindingInitializer bean。

#### HttpMessageConverters
Spring MVC 使用 HttpMessageConverters接口转换Http请求和响应，如，对象可以自动转换为json或者xml。字符串默认使用utf-8编码。
自定义转换器如下：
```java
@Configuration
public class MyConfiguration {
	@Bean
	public HttpMessageConverters customConverters() {
		HttpMessageConverter<?> additional = ...
		HttpMessageConverter<?> another = ...
		return new HttpMessageConverters(additional, another);
	}
}
```
HttpMessageConverter会被放入转换器列表。使用这种方法可以覆盖默认的转换器。

#### 自定义json转换
如果使用jackson序列化json对象，那么可以自定义对象的序列化和反序列化。需要实现JsonSerializer 和JsonDeserializer 接口，然后
添加@JsonComponent注解。
```java
public class User {
    private Color favoriteColor;
    // standard getters/constructors
}

@JsonComponent
public class UserCombinedSerializer {
    public static class Serializer extends JsonSerializer<User> {
        @Override
        public void serialize(User user, JsonGenerator jsonGenerator, SerializerProvider serializerProvider) 
            throws IOException, JsonProcessingException {
            jsonGenerator.writeStartObject();
            jsonGenerator.writeStringField(
              "favoriteColor", getColorAsWebColor(user.getFavoriteColor()));
            jsonGenerator.writeEndObject();
        }
 
        private static String getColorAsWebColor(Color color) {
            int r = (int) Math.round(color.getRed() * 255.0);
            int g = (int) Math.round(color.getGreen() * 255.0);
            int b = (int) Math.round(color.getBlue() * 255.0);
            return String.format("#%02x%02x%02x", r, g, b);
        }
    }
 
    public static class UserJsonDeserializer extends JsonDeserializer<User> {
        @Override
        public User deserialize(JsonParser jsonParser, 
          DeserializationContext deserializationContext)
          throws IOException, JsonProcessingException {
            TreeNode treeNode = jsonParser.getCodec().readTree(jsonParser);
            TextNode favoriteColor = (TextNode) treeNode.get(
              "favoriteColor");
            return new User(Color.web(favoriteColor.asText()));
        }
    }
}
```
在ApplicationContext 中的@JsonComponent自动使用jackson。SpringBoot也提供了JsonObjectSerializer和JsonObjectDeserializer
基类，他们分别实现了JsonSerializer和JsonDeserializer接口，扩展了jackson框架提供的基础功能。

#### CORS支持
协议，ip，端口相同称为同源。浏览器都有同源机制，即源A设置的数据，源B不能打开，但是也有了一些限制：
* Cookie、LocalStorage 和 IndexDB 无法读取。
* DOM 无法获得。
* AJAX 请求不能发送。
Cross-origin resource sharing 是多数浏览器支持的W3C标准，较其他两种IFRAME和JSONP更加安全和强大。
Spring 配置跨源，使用@CrossOrigin注解定义一个WebMvcConfigurer，如下：
```java
@Configuration
public class MyConfiguration {
	@Bean
	public WebMvcConfigurer corsConfigurer() {
		return new WebMvcConfigurer() {
			@Override
			public void addCorsMappings(CorsRegistry registry) {
				registry.addMapping("/api/**");
			}
		};
	}
}
```

#### Servlets, Filters, 和 listeners
当使用内嵌servlet容器时，我们可以注册servlet,filter,listener。      
任何Servlet,filter,*Listener实例都是被容器注册的Spring Bean。可以很方便地从application.properties引用一个值。
SpringBoot加载了很多自动配置的filter,每个filter有对应的排序。如：OrderedCharacterEncodingFilter，WebMvcMetricsFilter，
ErrorPageFilter等。

当使用内嵌的容器时，通过@WebServlet, @WebFilter, 和 @WebListener注册的类可以使用注解@ServletComponentScan启用。


