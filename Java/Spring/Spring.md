# Spring 源码

## 请求处理

```java
public class DispatcherServlet extends FrameworkServlet{
    
	protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
		HttpServletRequest processedRequest = request;
		HandlerExecutionChain mappedHandler = null;
		boolean multipartRequestParsed = false;

		WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

		try {
			ModelAndView mv = null;
			Exception dispatchException = null;

			try {
				processedRequest = checkMultipart(request);
				multipartRequestParsed = (processedRequest != request);

				// Determine handler for the current request.
                /**
                *	处理器映射器
                */
				mappedHandler = getHandler(processedRequest);
				if (mappedHandler == null) {
					noHandlerFound(processedRequest, response);
					return;
				}
```

![image-20220302135835335](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302135835335.png) 

![image-20220302135718502](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302135718502.png) 

```java
// Determine handler adapter for the current request.
/**
*	处理器适配器
*/
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```

![image-20220302140616824](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302140616824.png) 

![image-20220302140724313](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302140724313.png) 

![image-20220302140810303](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302140810303.png) 

```java
//找到需要调用的控制器方法
String method = request.getMethod();
```

# SpringBoot 自动配置原理

参考：

> https://blog.csdn.net/ProGram_Java521/article/details/124892398
>
> https://static.kancloud.cn/fold/spring_boot/1607481

在 SpringBoot 项目中，引入依赖是很重要的，自动配置会需要启动一个 Start 场景启动器依赖，例如：

```xml
1、见到很多 spring-boot-starter-* ： *就某种场景
2、只要引入starter，这个场景的所有常规需要的依赖我们都自动引入
3、SpringBoot所有支持的场景
https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#using-boot-starter
4、见到的  *-spring-boot-starter： 第三方为我们提供的简化开发的场景启动器。
5、所有场景启动器最底层的依赖
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <version>2.3.4.RELEASE</version>
  <scope>compile</scope>
</dependency>
```

![image-20230203143419494](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20230203143419494.png)

## 主启动类 

### `@SpringBootApplication`

添加了 `@SpringBootApplication` 注解的类就是主启动类，SpringBoot 项目就应该运行这个类的 main 方法来启动 SpringBoot 应用。

```java
//@SpringBootApplication 来标注一个主程序类
//说明这是一个Spring Boot应用
@SpringBootApplication
public class SpringbootApplication {

   public static void main(String[] args) {
     //以为是启动了一个方法，没想到启动了一个服务
      SpringApplication.run(SpringbootApplication.class, args);
   }

}
```

进入这个注解：可以看到上面还有很多其他注解！其中，与自动配置相关的重要注解有 `@SpringBootConfiguration`，`@EnableAutoConfiguration`，`@ComponentScan`

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

#### @ComponentScan

这个注解在 Spring 中很重要，它对应 XML 配置中的元素。

作用：自动扫描包（扫描当前主启动类同级的包）并加载符合条件的组件或者 bean，将这个 bean 定义加载到 IOC 容器中。

#### @SpringBootConfiguration

作用：SpringBoot 的配置类，标注在某个类上，表示这是一个 SpringBoot 的配置类。

往里看，发现这个注解其实就是一个我们常用的 `@Configuration` 注解，在看 `@Configuration` 注解里面最终是一个 `@Component` 注解，这说明启动类本身也只是 SpringBoot 应用中的一个组件，它负责启动应用。

#### :star: @EnableAutoConfiguration

这个注解的主要功能就是开启自动配置，之前我们需要自己配置的内容现在 SpringBoot 可以帮我们自动配置，`@EnableAutoConfiguration` 注解就相当于告诉 SpringBoot ，让其帮我们配置好所需的内容，这样，自动配置才能生效。

进入 `@EnableAutoConfiguration` 发现其中的两个重要注解,`@AutoConfigurationPackage` 和 `@Import`

```java
@AutoConfigurationPackage
@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
```

##### @AutoConfigurationPackage

自动配置包，其实就是通过 `@Import` 引入了 `Registrar` 类

  作用：将主启动类所在包及包下面所有子包里面的所有组件扫描到 Spring 容器；

```java
@Import(AutoConfigurationPackages.Registrar.class)
public @interface AutoConfigurationPackage {
```

##### :star: @Import({AutoConfigurationImportSelector.class})

> **@import**：自动注册包和 @ComponentScan 自动扫描包，是联动的）Spring 底层注解 @import，给容器中导入一个组件。

AutoConfigurationImportSelector：自动配置导入选择器

AutoConfigurationImportSelector 中 getAutoConfigurationEntry 方法自动获取配置实体，其中 this.getCandidateConfigurations(annotationMetadata, attributes) 是获得候选配置的方法； 

```java
protected AutoConfigurationEntry getAutoConfigurationEntry(AnnotationMetadata annotationMetadata) {
    ···
	List<String> configurations = getCandidateConfigurations(annotationMetadata, attributes);
    ···
}

protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
    //这里的getSpringFactoriesLoaderFactoryClass（）方法
    //返回的就是我们最开始看的启动自动导入配置文件的注解类；EnableAutoConfiguration
    List<String> configurations = SpringFactoriesLoader.loadFactoryNames(getSpringFactoriesLoaderFactoryClass(),
                                                                         getBeanClassLoader());
    Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you "
                    + "are using a custom packaging, make sure that file is correct.");
    return configurations;
}
```

getSpringFactoriesLoaderFactoryClass() 方法

```java
/**
 * 返回 SpringFactoriesLoader 用于加载配置候选的类。
 */
protected Class<?> getSpringFactoriesLoaderFactoryClass() {
   return EnableAutoConfiguration.class;
}
```

loadFactoryNames 使用给定的类加载器从 "META-INF/spring.factories" 加载给定类型的工厂实现的完全限定类名。

```java
public static List<String> loadFactoryNames(Class<?> factoryType, @Nullable ClassLoader classLoader) {
   ClassLoader classLoaderToUse = classLoader;
   if (classLoaderToUse == null) {
      classLoaderToUse = SpringFactoriesLoader.class.getClassLoader();
   }
   String factoryTypeName = factoryType.getName();
   return loadSpringFactories(classLoaderToUse).getOrDefault(factoryTypeName, Collections.emptyList());
}
```

loadSpringFactories 从 FACTORIES_RESOURCE_LOCATION 加载

```java
private static Map<String, List<String>> loadSpringFactories(ClassLoader classLoader) {
    // 获得classLoader ， 我们返回可以看到这里得到的就是EnableAutoConfiguration标注的类本身
    Map<String, List<String>> result = cache.get(classLoader);
    if (result != null) {
        return result;
    }

    result = new HashMap<>();
    try {
        // 去获取一个资源 "META-INF/spring.factories"
        Enumeration<URL> urls = classLoader.getResources(FACTORIES_RESOURCE_LOCATION);
        // 从这些资源中遍历了所有的 nextElement（自动配置类）
        // 将读取到的资源遍历，封装成为一个 Properties 供我们使用
        while (urls.hasMoreElements()) {
            ···
        }
    }
}
```

###### spring.factories 

该文件在每个启动器的 META-INF/spring.factories 位置，里面存放了各种配置的全限定类名

![image-20220908162844059](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220908162844059.png)

格式如下：

```properties
# Logging Systems
org.springframework.boot.logging.LoggingSystemFactory=\
org.springframework.boot.logging.logback.LogbackLoggingSystem.Factory,\
org.springframework.boot.logging.log4j2.Log4J2LoggingSystem.Factory,\
org.springframework.boot.logging.java.JavaLoggingSystem.Factory

# PropertySource Loaders
org.springframework.boot.env.PropertySourceLoader=\
org.springframework.boot.env.PropertiesPropertySourceLoader,\
org.springframework.boot.env.YamlPropertySourceLoader

# ConfigData Location Resolvers
org.springframework.boot.context.config.ConfigDataLocationResolver=\
org.springframework.boot.context.config.ConfigTreeConfigDataLocationResolver,\
org.springframework.boot.context.config.StandardConfigDataLocationResolver

# ConfigData Loaders
org.springframework.boot.context.config.ConfigDataLoader=\
org.springframework.boot.context.config.ConfigTreeConfigDataLoader,\
org.springframework.boot.context.config.StandardConfigDataLoader

# Application Context Factories
org.springframework.boot.ApplicationContextFactory=\
org.springframework.boot.web.reactive.context.AnnotationConfigReactiveWebServerApplicationContext.Factory,\
org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext.Factory
```

## @Conditional

作用：必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

| @Conditional扩展注解            | 作用（判断是否满足当前指定条件）                 |
| :------------------------------ | :----------------------------------------------- |
| @ConditionalOnJava              | 系统的java版本是否符合要求                       |
| @ConditionalOnBean              | 容器中存在指定Bean；                             |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                           |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                     |
| @ConditionalOnWebApplication    | 当前是web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是web环境                                  |
| @ConditionalOnJndi              | JNDI存在指定项                                   |

配置文件中这么多组件，为什么有的生效了，有的并没有生效？

通过 @ConditionalOnxxx 指定条件，可以控制组件是否注入，以 WebMvcAutoConfiguration 中的组件为例：

![image-20220908174714988](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220908174714988.png)

所以自动配置真正的实现是从 `classpath` 中搜寻所有的 `META-INF/spring.factories` 配置文件 ，并将其中对应的`org.springframework.boot.autoconfigure.*` 包下的配置项。但是不一定生效，要判断条件是否成立，只要导入了对应的 `start`，就有对应的启动器了，有了启动器，我们自动装配才会生效。

通过反射实例化为对应标注了 `@Configuration` 的 `JavaConfig` 形式的 IOC 容器配置类，然后将这些都汇总成为一个实例并加载到 IOC 容器中。

> 1、SpringBoot启动会加载大量的自动配置类；
>
> 2、我们看我们需要的功能有没有在SpringBoot默认写好的自动配置类当中；
>
> 3、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件存在在其中，我们就不需要再手动配置了）
>
> 4、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们只需要在配置文件中指定这些属性的值即可；
>
> **xxxxAutoConfigurartion：自动配置类；**给容器中添加组件



