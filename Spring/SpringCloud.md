# 最原始用 RestTemplate 调用服务

## 先创建好一个父工程，引入依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.zym</groupId>
    <artifactId>springcloudparent</artifactId>
    <packaging>pom</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <modules>
        <module>commons</module>
        <module>service</module>
    </modules>
    <name>springcloudparent</name>
    <description>springcloudparent</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-boot.version>2.7.1</spring-boot.version>
        <spring-cloud.version>2021.0.2</spring-cloud.version>
        <spring-cloud-alibaba.version>2.1.0.RELEASE</spring-cloud-alibaba.version>
        <mybatis-plus.version>3.5.2</mybatis-plus.version>
        <mysql-connector-java.version>8.0.29</mysql-connector-java.version>
        <junit.version>4.12</junit.version>
        <druid.version>1.2.8</druid.version>
        <log4j.version>1.2.17</log4j.version>
        <spring-boot-devtools.version>2.5.2</spring-boot-devtools.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.baomidou</groupId>
                <artifactId>mybatis-plus-boot-starter</artifactId>
                <version>${mybatis-plus.version}</version>
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql-connector-java.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
                <scope>test</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid-spring-boot-starter</artifactId>
                <version>${druid.version}</version>
            </dependency>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-devtools</artifactId>
                <version>${spring-boot-devtools.version}</version>
                <scope>runtime</scope>
                <optional>true</optional>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <build>
        <finalName>springcloudparent</finalName>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>
                    <addResources>true</addResources>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

```

## 创建工具类，写入实体类、工具类等共享内容

![image-20220803152050998](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220803152050998.png)

## 服务提供者

创建 `cloud-provider-payment8001` 工程

pom 文件中引入必须的依赖

```xml
<!--工具类-->
<dependency>
    <groupId>com.zym.commons</groupId>
    <artifactId>commons</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
<!--spring cloud 相关依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter</artifactId>
</dependency>
<!--web相关依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

创建好控制器

```java
/**
 * 用户控制类
 * @author zym
 */
@RestController
@RequestMapping("/user")
public class UserController {

    @GetMapping("/getUser")
    public R getUser() {
        User user = new User();
        user.setId("1111");
        user.setName("xxy");
        return R.ok("查询成功").setData(user);
    }

    @PostMapping("/createUser")
    public R createUser(@RequestBody User user) {
        return R.ok("插入成功").setData(user);
    }

}
```

## 服务调用者

创建 `cloud-consumer-order80` 工程

与提供者引入相同的依赖

配置 RestTemplate 用于服务调用

```java
package com.zym.order.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;
/**
 * 配置类
 * @author zym
 */
@Configuration
public class MyConfig {
    @Bean
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

创建控制器类

```java
/**
 * 消费控制器
 * @author zym
 */
@RestController
@RequestMapping("/order")
public class OrderController {

    /**
     * 使用restTemplate访问提供者的接口
     */
    private RestTemplate restTemplate;

    @Autowired
    public OrderController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    // 通过 restTemplate 调用提供者的 /user/getUser 接口
    @RequestMapping("/getOrder")
    public R getOrder() {
        ResponseEntity<R> forEntity = restTemplate.getForEntity("http://localhost:8001/user/getUser", R.class);
        return forEntity.getBody();
    }

    // 通过 restTemplate 调用提供者的 /user/createUser 接口
    @RequestMapping("/createOrder")
    public R createOrder() {
        User user = new User();
        user.setId("1111");
        user.setName("yyb");
        ResponseEntity<R> forEntity = restTemplate.postForEntity("http://localhost:8001/user/createUser", user, R.class);
        return forEntity.getBody();
    }
}
```

## 测试

浏览器请求 80 服务，得到 8001 返回的数据

![image-20220803152352707](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220803152352707.png)

# 服务注册与发现 Eureka

在传统的 rpc 远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，管理比较复杂，所以需要使用服务治理，管理服务于服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务发现与注册。

Eureka 是一个完成服务员治理的工具，由 Natflix 公司开发

> Eureka包含两个组件：Eureka Server 和 Eureka Client
>
> Eureka Server 提供服务注册服务
> 各个微服务节点通过配置启动后，会在 Eureka Server 中进行注册，这样 Eureka Server 中的服务注册表中将会存储所有可用服务节点的信息，服务节点的信息可以在界面中直观看到。
>
> Eureka Client 通过注册中心进行访问
> 是一个 Java 客户端，用于简化 Eureka Server 的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将会向 Eureka Server 发送心跳(默认周期为30秒)。如果 Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，Eureka Server 将会从服务注册表中把这个服务节点移除（默认90秒）

Eureka 采用了 CS 的设计架构，Eureka Server 作为服务注册功能的服务器，它是服务注册中心。而系统中的其他微服务，使用 Eureka 的客户端连接到 Eureka Server 并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。

注册中心：存放服务地址相关信息(接口地址)

<div>
    <img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220803152941918.png">
    <center>
    <font color="red">左边是 Eureka，右边是 doubbo</font>
    </center>
</div>

## 单机版使用

### 创建 Eureka 服务端

#### 添加依赖

使用 Eureka 需要先创建其服务端应用，需要新建一个模块 `cloud-eureka-server7001`，用作 Eureka 服务端

为其添加 Maven 依赖，主要是 `spring-cloud-starter-netflix-eureka-server`

```xml
<dependencies>
    <!--公共模块-->
    <dependency>
        <groupId>com.zym.commons</groupId>
        <artifactId>commons</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>
    <!--热部署-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
    </dependency>
    <!--springcloud-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter</artifactId>
    </dependency>
    <!--springmvc-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- actuator 监控中心 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <!--eureka 依赖-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
    </dependency>
</dependencies>
```

#### 修改配置文件

```yaml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

#### 在启动类上添加 `@EnableEurekaServer` 注解

```java
@SpringBootApplication
@EnableEurekaServer
public class EurikaServer {
    public static void main(String[] args) {
        SpringApplication.run(EurikaServer.class, args);
    }
}
```

### 将服务提供者和消费者都注册进 Eureka Server

#### 添加依赖

在所有需要使用 eureka 服务的模块中添加 eureka 依赖

```xml
<!--eureka 依赖-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

#### 修改配置文件

修改 `application.yml` 配置文件，添加如下配置，将其注册进 eureka

> 注意：不同的服务使用不同的服务名称

```yml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # eureka server 地址
      defaultZone: http://localhost:7001/eureka
spring:
  application:
    # 服务名称
    name: cloud-provider-payment8001
```

#### 在启动类上添加 `@EnableEurekaClient` 注解

```java
@SpringBootApplication
@EnableEurekaClient
public class Payment8001 {
    public static void main(String[] args) {
        SpringApplication.run(Payment8001.class, args);
    }
}
```

#### 启动

先启动 eureka 服务端模块，然后启动服务提供者和消费者

启动完成后，访问 eureka 服务模块可以看到注册进注册中心的服务

![image-20220803173641691](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20220803173641691.png)

### 注意

使用 eureka 后，返回值默认是 xml 格式，我们需要指定返回值格式为 json

在控制器上添加 `@RequestMapping(value = "/order",produces = "application/json; charset=utf-8")` 即可

`produces = "application/json; charset=utf-8"` 表示指定返回值格式和编码

也可以通过 `getForObject` 调用服务，如下

```java
    @RequestMapping("/getOrder")
    public R getOrder() {
        R forEntity = restTemplate.getForObject(PAYMENT_URL + "/user/getUser", R.class);
        return forEntity;
    }
```

## 集群版

创建两个 eureka 服务端，上面创建了一个 `cloud-eureka-server7001`，还需要创建一个 `cloud-eureka-server7002`

7001 与 7002 一样只是需要修改配置文件，修改 `defaultZone` 配置将 7001 和 7002 相互注册

> 注意：eureka 服务端的实例名称不能设置相同

### 7001 配置文件

```yml
server:
  port: 7001
eureka:
  instance:
    hostname: localhost7001 #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://localhost:7002/eureka/
```

### 7002 配置文件

```yml
server:
  port: 7002
eureka:
  instance:
    hostname: localhost7002 #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己。
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址。
      defaultZone: http://localhost:7001/eureka/
```

### 将消费者（80）和服务提供者的（8001）同时注册进两个 Eureka Server

修改 defaultZone ，使用逗号分隔两个 Eureka 服务地址

消费者配置文件

```yml
server:
  port: 80
spring:
  application:
    name: cloud-consumer-order80
eureka:
  client:
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka, http://localhost:7002/eureka
```

服务提供者配置文件

```yml
server:
  port: 8001
spring:
  application:
    # 服务名称
    name: payment8001
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # eureka server 地址
      defaultZone: http://localhost:7001/eureka, http://localhost:7002/eureka
```

### 测试

#### 访问 Eureka Server 7001

![image-20220805101842733](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805101842733.png)

#### 访问 Eureka Server 7002

![image-20220805101915497](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805101915497.png)

#### 通过消费者（80）调用服务提供者接口

![image-20220805102024310](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805102024310.png)

## 基础的负载均衡

详细情况查看后面: [负载均衡](#负载均衡)

此时已经有了一个服务提供者（8001），一个消费者（80），两个 Eureka Server（7001、7002）

通常情况下，我们会将一个服务部署到多台服务器上，他们同时提供服务就可以让更多的用户访问

现在 8001 的 getUser 接口返回值中添加端口信息

```java
/**
 * 用户控制类
 * @author zym
 */
@RestController
@RequestMapping(value = "/user",produces = "application/json; charset=utf-8")
public class UserController {

    @GetMapping("/getUser")
    public R getUser() {
        User user = new User();
        user.setId("1111");
        user.setName("xxy");
        return R.ok("查询成功:8001").setData(user);
    }

    @PostMapping("/createUser")
    public R createUser(@RequestBody User user) {
        return R.ok("插入成功").setData(user);
    }

}
```

### 复制一个新的 8001

> 注意：为了方便，修改其端口为 8002

将 8002 和 8001 以相同的服务名称注册进 Eureka 注册中心

可以看到 8001 有了两个服务

![image-20220805103041783](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805103041783.png)

### 在服务消费者（80）获取 `RestTemplate` 的地方使用 `@LoadBalanced` 注解

```java
/**
 * 配置类
 * @author zym
 */
@Configuration
public class MyConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

### 修改控制器，通过服务名称调用服务提供者（8001）的接口

只需要修改原来的 `PAYMENT_URL` 通过 ip 调用，变成通过注册进 Eureka 的服务名称调用 

```java
/**
 * 消费控制器
 * @author zym
 */
@RestController
@RequestMapping(value = "/order",produces = "application/json; charset=utf-8")
public class OrderController {

    /**
     * 使用restTemplate访问提供者的接口
     */
    private RestTemplate restTemplate;
    private static final String PAYMENT_URL = "http://PAYMENT8001";

    @Autowired
    public OrderController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    @RequestMapping("/getOrder")
    public R getOrder() {
        ResponseEntity<R> forEntity = restTemplate.getForEntity(PAYMENT_URL + "/user/getUser", R.class);
        return forEntity.getBody();
    }

    @RequestMapping("/createOrder")
    public R createOrder() {
        User user = new User();
        user.setId("1111");
        user.setName("yyb");
        ResponseEntity<R> forEntity = restTemplate.postForEntity(PAYMENT_URL + "/user/createUser", user, R.class);
        return forEntity.getBody();
    }
}
```

### 测试

通过消费者（80）访问 getUser 可以看到返回值中的端口信息发生变化

![image-20220805104724581](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805104724581.png)

![image-20220805104751222](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805104751222.png)

## actuator微服务信息完善

### 服务器名称修改

查看当前 Eureka 中的服务会出现主机信息

![image-20220805105314774](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805105314774.png)

在配置文件中为服务添加 `instance-id`,如下 8001

```yml
server:
  port: 8001
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # eureka server 地址
      defaultZone: http://localhost:7001/eureka, http://localhost:7002/eureka
  instance:
    instance-id: payment8001 #eureka服务端的实例名称
spring:
  application:
    # 服务名称
    name: payment8001
```

![image-20220805105821564](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805105821564.png)

### 指向服务名称时显示 IP

当前鼠标指向服务名称时，左下角显示的是主机信息

![image-20220805105948494](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805105948494.png)

在配置文件中添加 `prefer-ip-address: true ` 显示 IP 信息，如下 8001

```yml
server:
  port: 8001
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # eureka server 地址
      defaultZone: http://localhost:7001/eureka, http://localhost:7002/eureka
  instance:
    instance-id: payment8001 #eureka服务端的实例名称
    prefer-ip-address: true #显示本机的ip地址
spring:
  application:
    # 服务名称
    name: payment8001
```

![image-20220805110335022](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805110335022.png)



## 获取 Eureka 中的服务信息

以 8001 为例

在启动类上添加 `@EnableDiscoveryClient` 注解

```java
/**
 * 启动类
 * @author zym
 */
@SpringBootApplication
@EnableEurekaClient
@EnableDiscoveryClient
public class Payment8001 {
    public static void main(String[] args) {
        SpringApplication.run(Payment8001.class, args);
    }
}
```

在控制器中注入 `DiscoveryClient` 然后即可通过 `DiscoveryClient` 获取注册中心中的服务信息，如 `discoveryClient()` 方法

```java
/**
 * 用户控制类
 * @author zym
 */
@RestController
@RequestMapping(value = "/user",produces = "application/json; charset=utf-8")
public class UserController {

    private DiscoveryClient discoveryClient;

    @Autowired
    public UserController(DiscoveryClient discoveryClient) {
        this.discoveryClient = discoveryClient;
    }

    @GetMapping("/getUser")
    public R getUser() {
        User user = new User();
        user.setId("1111");
        user.setName("xxy");
        return R.ok("查询成功:8001").setData(user);
    }

    @PostMapping("/createUser")
    public R createUser(@RequestBody User user) {
        return R.ok("插入成功").setData(user);
    }

    @GetMapping("/discoveryClient")
    public R discoveryClient() {
        // 查询所有的服务
        discoveryClient.getServices().stream().forEach( server -> {
            System.out.println(server);
        });
        // 查询指定服务的详细信息
        discoveryClient.getInstances("PAYMENT8001").stream().forEach( server -> {
            System.out.println(server);
        });
        return R.ok("查询成功").setData(discoveryClient.getServices());
    }
}
```

## Eureka 自我保护

保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护。

一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。

访问 Eureka 服务，看到下面内容即进入保护模式

![image-20220805112020906](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805112020906.png)

保护模式默认是开启的，可以通过 `eureka.server.enable-self-preservation= false` 关闭保护模式，关闭后看到如下信息

![image-20220805112702113](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805112702113.png)

### 在服务中还可以设置心跳发送时间建间隔和最后一次收到心跳的时间上线

```yml
    # Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    # Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
```

以 8001 为例

```yml
server:
  port: 8001
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # eureka server 地址
      defaultZone: http://localhost:7001/eureka, http://localhost:7002/eureka
  instance:
    instance-id: payment8001 #eureka服务端的实例名称
    prefer-ip-address: true #显示本机的ip地址
    # Eureka客户端向服务端发送心跳的时间间隔，单位为秒(默认是30秒)
    lease-renewal-interval-in-seconds: 1
    # Eureka服务端在收到最后一次心跳后等待时间上限，单位为秒(默认是90秒)，超时将剔除服务
    lease-expiration-duration-in-seconds: 2
spring:
  application:
    # 服务名称
    name: payment8001
```

# 服务注册与发现 Zookeeper

安装 Zookeeper（官网）：https://zookeeper.apache.org/

启动 Zookeeper

## 编写服务提供者（cloud-provider-payment8004）

pom 文件如下

```xml
    <dependencies>
        <dependency>
            <groupId>com.zym.commons</groupId>
            <artifactId>commons</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
    </dependencies>
```

编写配置文件，指定 zookeeper 注册中心的地址

```yml
server:
  port: 8004
#服务别名----注册zookeeper到注册中心名称
spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: localhost:2181
```

 编写控制器方法

```java
@RestController
@RequestMapping("/user")
public class UserController {

    @RequestMapping("/getUser")
    public R getUser() {
        return R.ok("获取用户成功8004");
    }

}
```

在主配置文件中添加 `@EnableDiscoveryClient` 注解

```java
@SpringBootApplication
@EnableDiscoveryClient //该注解用于向使用consul或者zookeeper作为注册中心时注册服务
public class Payment8004 {

    public static void main(String[] args) {
        SpringApplication.run(Payment8004.class, args);
    }
}

```

## 编写服务消费者（cloud-consumerzk-order80）

基本内容与服务提供者（8004）类似

需要添加配置类，注入 RestTemplate

```java
@Configuration
public class MyConfig {

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate(){
        return new RestTemplate();
    }
}
```

在控制器类中通过 RestTemplate 调用服务

```java
@RestController
@RequestMapping("/order")
public class OrderController {

    private RestTemplate restTemplate;

    @Autowired
    public OrderController(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }

    private static final String PAYMENT_URL = "http://cloud-provider-payment";

    @GetMapping("/getOrder")
    public R getOrder() {
        return restTemplate.getForObject(PAYMENT_URL + "/user/getUser", R.class);
    }
}
```

## 测试

![image-20220805153246902](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220805153246902.png)

# Ribbon 负载均衡

## 两种解决方案

集中式 LB：即在服务的消费方和提供方之间使用独立的 LB 设施(可以是硬件，如 F5, 也可以是软件，如 nginx), 由该设施负责把访问请求通过某种策略转发至服务的提供方；

进程式 LB：将 LB 逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选择出一个合适的服务器。

Ribbon 就属于进程内 LB，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方的地址。

## Ribbon

Spring Cloud Ribbon 是基于 Netflix Ribbon 实现的一套客户端负载均衡的工具。

简单的说，Ribbon 是 Netflix 发布的开源项目，主要功能是提供客户端的软件负载均衡算法和服务调用。Ribbon 客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出 Load Balancer（简称LB）后面所有的机器，Ribbon 会自动的帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们很容易使用 Ribbon 实现自定义的负载均衡算法。

**<font color=red>注意：回到 Eureka 注册中心那个例子演示</font>**

### 为消费者（80）添加 ribbon 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-netflix-ribbon</artifactId>
</dependency>
```

### Ribbon 的核心组件 Rule

Rule 的作用就是根据特定算法中从服务列表中选取一个要访问的服务

Rule 提供的负载均衡规则：

|         规则名称          |                             特点                             |
| :-----------------------: | :----------------------------------------------------------: |
|      RoundRobinRule       |                         轮询（默认）                         |
|        RandomRule         |                             随机                             |
|     ZoneAvoidanceRule     |    复合判断server所在区域的性能和server的可用性选择服务器    |
| AvailabilityFilteringRule |            先过滤掉故障实例，再选择并发较小的实例            |
|     BestAvailableRule     | 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务 |
| ResponseTimeWeightedRule  |            已废弃，作用同WeightedResponseTimeRule            |
| WeightedResponseTimeRule  | 对RoundRobinRule的扩展，响应速度越快的实例选择权重越大，越容易被选择 |
|         RetryRule         | 先按照RoundRobinRule的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务 |

### 修改负载均衡策略

参考：https://chowdera.com/2022/140/202205200536083201.html#22_LoadBalancerClients_165

以服务消费者 cloud-consumer-order80 为例 

**<font color=red>注意：ribbon 官方提示，自定义负载均衡配置类不要放在 @ComponentScan 能扫描的地方</font>**

先在 @ComponentScan 扫描不到的地方创建一个新的包（`myRule`）

这个自定义配置类不能放在 @ComponentScan 所扫描的当前包下以及子包下，否则我们自定义的这个配置类就会被所有的 Ribbon 客户端所共享，达不到特殊化定制的目的了。

项目结构如下：

![image-20220808151939936](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220808151939936.png)

#### 创建负载均衡类，这里将默认的轮询方式改成随机方式

> 注意：springcloud 2020.0.1 版本之后 删除了 eureka 中的 ribbon
>
> 替代 ribbon 的是 spring cloud 自带的 LoadBalancer，Spring-cloud-starter-loadbalancer 的jar 这个应该是和 Ribbon 的功能一样。
>
> 但是他就提供了 RandomLoadBalancer 随机和 RoundRobinLoadBalancer 轮询的方式，默认使用的是轮询的方式

```java
public class MySelfRule {
    
    /**
     * 老版本 springcloud （springcloud 2020.0.1 之前）使用这种方式，
     */
    @Bean
    public IRule myRule() {
        return new RandomRule();
    }

    /**
     * 新版本的 springcloud 使用这种方式
     */
    @Bean
    ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(
        Environment environment,LoadBalancerClientFactory loadBalancerClientFactory) {
        
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
        return new RandomLoadBalancer(
            loadBalancerClientFactory.getLazyProvider(name, ServiceInstanceListSupplier.class),name);
    }
}
```

#### 使用 @LoadBalancerClient 注解指定负载均衡配置

> 注意：这个注解可以在配置类上，也可以在主启动类上

```java
@Configuration
@LoadBalancerClient(name = "PAYMENT8001", configuration= MySelfRule.class)
public class MyConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

#### 重启消费者即可使用新的负载均衡模式

![chrome_pd7f9sqUfa](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_pd7f9sqUfa.gif)

### 自定义负载均衡算法

本次自定义轮询算法进行测试

轮询算法核心：rest 接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标  ，每次服务重启动后 rest 接口计数从 1 开始。

#### 第一步

删除 RestTemplate 上的 @LoadBalanced 注解

注意，如果有添加修改负载均衡策略的注解 `@LoadBalancerClient(name = "PAYMENT8001", configuration= MySelfRule.class)` 也需要将其删除

```java
@Configuration
public class MyConfig {

    @Bean
    // @LoadBalanced
    public RestTemplate getRestTemplate() {
        return new RestTemplate();
    }
}
```

#### 第二步

编写 LoadBalancer 接口及其实现类

```java
public interface LoadBalancer {
    ServiceInstance instances(List<ServiceInstance> serviceInstances);
}
```

```java
@Component
public class MyLB implements LoadBalancer {

    private AtomicInteger atomicInteger = new AtomicInteger(0);

    public final int getAndIncrement()
    {
        int current;
        int next;
        do
        {
            current = this.atomicInteger.get();
            next = current >= 2147483647 ? 0 : current + 1;
        } while(!this.atomicInteger.compareAndSet(current, next));
        System.out.println("*****next: "+next);
        return next;
    }


    @Override
    public ServiceInstance instances(List<ServiceInstance> serviceInstances)
    {
        int index = getAndIncrement() % serviceInstances.size();
        return serviceInstances.get(index);
    }
}
```

#### 第三步

修改控制器方法，注入 `loadBalancer` 和 `discoveryClient`

```java
@RestController
@RequestMapping(value = "/order",produces = "application/json; charset=utf-8")
public class OrderController {

    /**
     * 使用restTemplate访问提供者的接口
     */
    private RestTemplate restTemplate;
    private LoadBalancer loadBalancer;
    private DiscoveryClient discoveryClient;
    private static final String PAYMENT_URL = "http://PAYMENT8001";

    @Autowired
    public OrderController(RestTemplate restTemplate, LoadBalancer loadBalancer, DiscoveryClient discoveryClient) {
        this.restTemplate = restTemplate;
        this.loadBalancer = loadBalancer;
        this.discoveryClient = discoveryClient;
    }

    /**
     * 之前调用远程服务
     */
    @RequestMapping("/getOrder")
    public R getOrder() {
        return restTemplate.getForObject(PAYMENT_URL + "/user/getUser", R.class);
    }

    /**
     * 使用自定义放在均衡策略调用服务
     */
    @GetMapping("/consumer/payment/getUser")
    public R getPaymentLB()
    {
        List<ServiceInstance> instances = discoveryClient.getInstances("PAYMENT8001");
        if(instances == null || instances.size()<=0) {
            return null;
        }
        ServiceInstance serviceInstance = loadBalancer.instances(instances);
        URI uri = serviceInstance.getUri();

        return restTemplate.getForObject(PAYMENT_URL+"/user/getUser",R.class);
    }
}
```

# OpenFeign 服务调用

## Feign 是什么？

Feign 是一个声明式的 Web 服务客户端，让编写 Web 服务客户端变得非常容易，只需创建一个接口并在接口上添加注解即可

## Feign 能干什么？

Feign 旨在使编写 Java Http 客户端变得更容易。

前面在使用 Ribbon+RestTemplate 时，利用 RestTemplate 对 http 请求的封装处理，形成了一套模版化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign 在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在 Feign 的实现下，我们只需创建一个接口并使用注解的方式来配置它(以前是 Dao 接口上面标注 Mapper 注解,现在是一个微服务接口上面标注一个 Feign 注解即可)，即可完成对服务提供方的接口绑定，简化了使用 Spring cloud Ribbon 时，自动封装服务调用客户端的开发量。

Feign 集成了 Ribbon

利用 Ribbon 维护了 Payment 的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与 Ribbon 不同的是，通过 feign 只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用

## OpenFeign 和 Feign 的区别

| Feign                                                        | OpenFeign                                                    |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Feign 是 Spring Cloud 组件中的一个轻量级RESTful 的 HTTP 服务客户端<br/>Feign 内置了 Ribbon，用来做客户端负载均衡，去调用服务注册中心的服务。<br/>Feign 的使用方式是：使用 Feign 的注解定义接口，调用这个接口，就可以调用服务注册中心的服务 | OpenFeign是Spring Cloud 在Feign的基础上支持了SpringMVC的注解，如@RequesMapping等等。<br/>OpenFeign 的 @FeignClient 可以解析 SpringMVC 的 @RequestMapping 注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。 |
| <dependency><br/>    <groupId>org.springframework.cloud</groupId><br/>    <artifactId>spring-cloud-starter-feign</artifactId><br/></dependency> | <dependency><br/>    <groupId>org.springframework.cloud</groupId><br/>    <artifactId>spring-cloud-starter-openfeign</artifactId><br/></dependency> |

## OpenFeign 的使用

重新创建一个服务消费者（80），依赖与原来一样

### 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

### 启动类上添加 @EnableFeignClients 注解

```java
@SpringBootApplication
@EnableFeignClients	// 开启OpenFeign
public class Order2 {
    public static void main(String[] args) {
        SpringApplication.run(Order2.class, args);
    }
}
```

### 创建 OpenFeign 接口

```java
@Component
@FeignClient(name = "PAYMENT8001") // 指定服务名
public interface OpenFdignService {

    @GetMapping(value = "/user/getUser")
    R getUser();
}
```

### 编写控制器

将 OpenFdignService 接口注入控制器，调用接口中的方法就是调用服务 `PAYMENT8001` 的方法

```java
@RestController
@RequestMapping(value = "/order",produces = "application/json; charset=utf-8")
public class OrderController {

    private OpenFdignService openFdignService;

    @Autowired
    public OrderController(OpenFdignService openFdignService) {
        this.openFdignService = openFdignService;
    }

    @RequestMapping("/getOrder")
    public R getOrder() {
        return openFdignService.getUser();
    }

}
```

### 测试

![image-20220808173451493](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220808173451493.png)

 ## OpenFeign 超时控制

### 制造超时条件

修改服务提供者的 getUser 方法，添加 sleep ，暂停两秒钟

```java
@RestController
@RequestMapping(value = "/user",produces = "application/json; charset=utf-8")
public class UserController {

    @GetMapping("/getUser")
    public R getUser() {
        try {
            Thread.sleep(100000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        User user = new User();
        user.setId("1111");
        user.setName("xxy");
        r
    }
}
```

开启上面的消费者 (80)，访问该方法，等待一段时间，看到超时报错

![image-20220808180119754](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220808180119754.png)

### 设置超时时间

超时时间是可以修改的，如下，在服务消费者（80）的配置文件中添加配置：

```yml
feign:
  client:
    config:
      default:
        readTimeout: 1000 # 连接成功后的超时时间
        connectTimeout: 1000 # 连接超时时间
```

重启服务，再次访问该方法，一秒钟就爆出了超时错误

### OpenFeign 日志打印功能

Feign 提供了日志打印功能，我们可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节。说白了就是对Feign接口的调用情况进行监控和输出

```
日志级别
NONE：默认的，不显示任何日志；
BASIC：仅记录请求方法、URL、响应状态码及执行时间；
HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息；
FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据。
```

#### 配置日志 Bean

> 注意：Logger 必须导入 feign.Logger 包

```java
import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {

    @Bean
    Logger.Level feignLoggerLevel() {
        return Logger.Level.FULL;
    }
}
```

#### 在 yml 配置文件中开启需要日志的 Feign 客户端

```yml
logging:
  level:
    # feign 日志以什么级别监控哪个接口
    com.zym.order.config.OpenFdignService: debug
```

#### 项目结构如下

![image-20220809112149556](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809112149556.png)

#### 重启该消费者服务，调用使用了 Feign 接口的方法

![image-20220809112320165](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809112320165.png)

可以在控制台看到输出的日志，如下

![image-20220809112417225](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809112417225.png)

# Histrix 断路器

## Histrix 概述

### 分布式系统面临的问题：

复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地失败。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809112702903.png" alt="image-20220809112702903" style="zoom:67%;" />

### 服务雪崩
多个微服务之间调用的时候，假设微服务 A 调用微服务 B 和微服务 C，微服务 B 和微服务 C 又调用其它的微服务，这就是所谓的 “`扇出`”。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务 A 的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的 “`雪崩效应`”.

对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。

所以，通常当你发现一个模块下的某个实例失败后，这时候这个模块依然还会接收流量，然后这个有问题的模块还调用了其他的模块，这样就会发生级联故障，或者叫`雪崩`。

### Histrix 是什么

Hystrix 是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等，Hystrix 能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。

“`断路器`” 本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间、不必要地占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

> 它可以完成服务降级，服务熔断、接近实时的监控等功能

官网：https://github.com/Netflix/Hystrix/wiki/How-To-Use

## Histrix 的重要概念

### 服务降级

服务器忙，短时间内无法处理该请求，不让客户端等待并立刻返回一个友好提示，fallback

```
哪些情况会出发降级?
	程序运行异常
	超时
	服务熔断触发服务降级
	线程池/信号量打满也会导致服务降级
```

### 服务熔断

类比保险丝达到最大服务访问后，直接拒绝访问，拉闸限电，然后调用服务降级的方法并返回友好提示

服务的降级->进而熔断->恢复调用链路

### 服务限流

秒杀高并发等操作，严禁一窝蜂的过来拥挤，大家排队，一秒钟 N 个，有序进行

## Histrix 案例

新建服务提供者： `cloud-provider-hystrix-payment8001`

将消费者注入原来的 Eureka Server，编写 Controller

```java
package com.zym.hystrixpayment.controller;

import com.zym.commons.utils.R;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping(value = "/payment", produces = "application/json;charset=UTF-8")
public class PaymentController {

    @GetMapping("/payMoney/{money}")
    public R payMoney(@PathVariable(name = "money") String money){
        System.out.println(money);
        return R.ok("支付成功").setData(money);
    }

    @GetMapping("/payMoneyTimeOut/{money}")
    public R payMoneyTimeOut(@PathVariable(name = "money") String money){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        return R.ok("支付成功").setData(money);
    }
}
```

新建消费者：`cloud-consumer-feign-hystrix-order80`

通过 OpenFeign 调用提供者，OpenFeign 的接口如下所示：

```java
@Component
@FeignClient(name = "CLOUD-PROVIDER-HYSTRIX-PAYMENT8001")
public interface FeignPaymentService {

    @GetMapping("/payment/payMoney/{money}")
    R payMoney(@PathVariable(name = "money") String money);

    @GetMapping("/payment/payMoneyTimeOut/{money}")
    R payMoneyTimeOut(@PathVariable(name = "money") String money);

}
```

控制器如下所示

```java
@RestController
@RequestMapping(value = "/order", produces = "application/json;charset=UTF-8")
public class OrderController {

    private FeignPaymentService feignPaymentService;

    @Autowired
    public OrderController(FeignPaymentService feignPaymentService) {
        this.feignPaymentService = feignPaymentService;
    }

    @RequestMapping("/payMoney")
    public R payMoney(){
        return feignPaymentService.payMoney("1010");
    }

    @RequestMapping("/payMoneyTimeOut")
    public R payMoneyTimeOut(){
        System.out.println("test");
        return feignPaymentService.payMoneyTimeOut("3000");
    }
}
```

这时，服务消费者调用 payMoneyTimeOut 会出现超时报错

## 服务降级

### 提供者中设置服务降级

为上面两个服务都添加 Histrix 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

**<font color=red>降级注解：@HystrixCommand</font>**

它可以放在消费者（80）的控制器上，也可以放在提供者（8001）的控制器上

修改提供者（8001）控制器，示例： 

```java
@RestController
@RequestMapping(value = "/payment", produces = "application/json;charset=UTF-8")
public class PaymentController {

    @GetMapping("/payMoney/{money}")
    public R payMoney(@PathVariable(name = "money") String money){
        System.out.println(money);
        return R.ok("支付成功").setData(money);
    }

    /**
     * HystrixCommand注解：
     * 		fallbackMethod 表示：payMoneyTimeOut 调用出现问题，会调用 payMoneyTimeOutFallback 方法进行降级返回
     *		@HystrixProperty 表示：设置超时时间为 1000 毫秒，超时也会通过 payMoneyTimeOutFallback 降级返回
     */
    @HystrixCommand(fallbackMethod = "payMoneyTimeOutFallback",commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "1000")
    })
    @GetMapping("/payMoneyTimeOut/{money}")
    public R payMoneyTimeOut(@PathVariable(name = "money") String money){
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        return R.ok("支付成功").setData(money);
    }

    public R payMoneyTimeOutFallback(@PathVariable(name = "money") String money){
        return R.ok("失败").setData(money);
    }
}
```

在主启动类上添加 `@EnableCircuitBreaker` 注解开启服务降级

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableCircuitBreaker
public class HystrixStarter {
    public static void main(String[] args) {
        SpringApplication.run(HystrixStarter.class, args);
    }
}

```

测试：

![image-20220809165917178](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809165917178.png)

### 消费者中设置服务降级

在消费者洪设置服务降级与提供者中类似，先修改控制器：

```java
@RestController
@RequestMapping(value = "/order", produces = "application/json;charset=UTF-8")
public class OrderController {

    private FeignPaymentService feignPaymentService;

    @Autowired
    public OrderController(FeignPaymentService feignPaymentService) {
        this.feignPaymentService = feignPaymentService;
    }

    @RequestMapping("/payMoney")
    public R payMoney(){
        return feignPaymentService.payMoney("1010");
    }

    @HystrixCommand(fallbackMethod = "payMoneyTimeOutFallback",commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "1000")
    })
    @RequestMapping("/payMoneyTimeOut")
    public R payMoneyTimeOut(){
        System.out.println("test");
        return feignPaymentService.payMoneyTimeOut("3000");
    }

    public R payMoneyTimeOutFallback(){
        System.out.println("test");
        return R.error("支付失败");
    }
}
```

在主启动类上添加 `@EnableHystrix`，或者在配置文件中添加  `hystrix.metrics.enabled=true` 注解开启 `Hystrix`

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
@EnableHystrix
public class HystrixOrderStarter {

    public static void main(String[] args) {
        SpringApplication.run(HystrixOrderStarter.class, args);
    }
}
```

测试：

![image-20220809171306055](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809171306055.png)

### 解决问题

现在服务降级完成，但是出现了两个问题

- 每个业务方法对应一个兜底的方法，代码膨胀

    使用 `@DefaultProperties(defaultFallback = "")` 可以解决这个问题

    将 `@DefaultProperties(defaultFallback = "")` 添加到控制器类上，并编写一个默认降级方法，然后在需要降级的方法上添加 `@HystrixCommand`，不写任何参数，如果发生错误，就会调用默认的降级方法

    ```java
    @RestController
    @DefaultProperties(defaultFallback = "payMoneyTimeOutFallback",commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds", value = "1000")
    })
    @RequestMapping(value = "/order", produces = "application/json;charset=UTF-8")
    public class OrderController {
    
        private FeignPaymentService feignPaymentService;
    
        @Autowired
        public OrderController(FeignPaymentService feignPaymentService) {
            this.feignPaymentService = feignPaymentService;
        }
    
        @RequestMapping("/payMoney")
        public R payMoney(){
            return feignPaymentService.payMoney("1010");
        }
    
        @HystrixCommand
        @RequestMapping("/payMoneyTimeOut")
        public R payMoneyTimeOut(){
            System.out.println("test");
            return feignPaymentService.payMoneyTimeOut("3000");
        }
    
        public R payMoneyTimeOutFallback(){
            System.out.println("test");
            return R.error("支付失败");
        }
    }
    
    ```

- 降级方法和业务逻辑混在一起，比较混乱

    **<font color=red>最终方案</font>**

    修改消费者代码（80）,编写 OpenFeign 接口的实现类

    ```java
    @Component
    public class FeignPaymentServiceImpl implements FeignPaymentService{
    
        @Override
        public R payMoney(String money) {
            System.out.println("aaa");
            return R.ok("支付失败").setData(money);
        }
    
        @Override
        public R payMoneyTimeOut(String money) {
            System.out.println("aaa");
            return  R.ok("支付失败").setData(money);
        }
    }
    ```
    
    修改 OpenFeign 接口代码，在 @FeignClient 中设置 fallback 为实现类
    
    ```java
    @Component
    @FeignClient(name = "CLOUD-PROVIDER-HYSTRIX-PAYMENT8001", fallback = FeignPaymentServiceImpl.class)
    public interface FeignPaymentService {
    
        @GetMapping("/payment/payMoney/{money}")
        R payMoney(@PathVariable(name = "money") String money);
    
        @GetMapping("/payment/payMoneyTimeOut/{money}")
        R payMoneyTimeOut(@PathVariable(name = "money") String money);
    
    }
    ```
    
    删除掉之前的 @HystrixProperty 子类的注解 
    
    ```java
    @RestController
    @RequestMapping(value = "/order", produces = "application/json;charset=UTF-8")
    public class OrderController {
    
        private FeignPaymentService feignPaymentService;
    
        @Autowired
        public OrderController(FeignPaymentService feignPaymentService) {
            this.feignPaymentService = feignPaymentService;
        }
    
        @RequestMapping("/payMoney")
        public R payMoney(){
            return feignPaymentService.payMoney("1010");
        }
    
        @RequestMapping("/payMoneyTimeOut")
        public R payMoneyTimeOut(){
            System.out.println("test");
            return feignPaymentService.payMoneyTimeOut("3000");
        }
    
    }
    ```
    
    测试：
    
    ![image-20220809193018631](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220809193018631.png)

## 服务熔断

### 熔断机制概述

技术论文：https://martinfowler.com/bliki/CircuitBreaker.html

熔断机制是应对雪崩效应的一种微服务链路保护机制。当扇出链路的某个微服务出错不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。

当检测到该节点微服务调用响应正常后，恢复调用链路。

在 Spring Cloud 框架里，熔断机制通过 Hystrix 实现。Hystrix 会监控微服务间调用的状况，当失败的调用到一定阈值，缺省是 5 秒内 20 次调用失败，就会启动熔断机制。熔断机制的注解是 @HystrixCommand。

### 具体设置

修改 `cloud-provider-hystrix-payment8001`

在需要熔断的方法上设置 `@HystrixCommand`，在 `payMoneyTimeOut` 方法中，当传入参数为 “0” 就会报错，由于设置了 `@HystrixCommand` ，当错误请求达到设置的标准时，就会发生熔断。

```java
@RestController
@RequestMapping(value = "/payment", produces = "application/json;charset=UTF-8")
public class PaymentController {

    @GetMapping("/payMoney/{money}")
    public R payMoney(@PathVariable(name = "money") String money){
        System.out.println(money);
        return R.ok("支付成功").setData(money);
    }

    @GetMapping("/payMoneyTimeOut/{money}")
    @HystrixCommand(fallbackMethod = "payMonetTimeOutFallback",commandProperties = {
            @HystrixProperty(name = "circuitBreaker.enabled",value = "true"),
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60"),
    })
    public R payMoneyTimeOut(@PathVariable(name = "money") String money){
        if (money.equals("0")){
            throw new RuntimeException("支付失败");
        }
        return R.ok("支付成功").setData(money);
    }
    public R payMonetTimeOutFallback(@PathVariable(name = "money") String money){
        return R.ok("支付失败o(╥﹏╥)o").setData(money);
    }

}
```

熔断之后，正常的请求也会走错误的 `payMonetTimeOutFallback` 降级方法。一段时间后， Hystrix 会尝试让少量请求走正常的 `payMoneyTimeOut` 方法，如果还是报错，就继续熔断，否则会将状态变成正常。

### 测试：

`payMoneyTimeOut` 为正确请求，`payMoneyTimeOut2` 为错误请求

![chrome_baTelbRux5](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_baTelbRux5.gif)

## 工作流程

![image-20220810152858538](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810152858538.png)

| 1    | 创建 HystrixCommand（用在依赖的服务返回单个操作结果的时候） 或 HystrixObserableCommand（用在依赖的服务返回多个操作结果的时候） 对象。 |
| ---- | ------------------------------------------------------------ |
| 2    | 命令执行。其中 HystrixComand 实现了下面前两种执行方式；而 HystrixObservableCommand 实现了后两种执行方式：execute()：同步执行，从依赖的服务返回一个单一的结果对象， 或是在发生错误的时候抛出异常。queue()：异步执行， 直接返回 一个Future对象， 其中包含了服务执行结束时要返回的单一结果对象。observe()：返回 Observable 对象，它代表了操作的多个结果，它是一个 Hot Obserable（不论 "事件源" 是否有 "订阅者"，都会在创建后对事件进行发布，所以对于 Hot Observable 的每一个 "订阅者" 都有可能是从 "事件源" 的中途开始的，并可能只是看到了整个操作的局部过程）。toObservable()： 同样会返回 Observable 对象，也代表了操作的多个结果，但它返回的是一个Cold Observable（没有 "订阅者" 的时候并不会发布事件，而是进行等待，直到有 "订阅者" 之后才发布事件，所以对于 Cold Observable 的订阅者，它可以保证从一开始看到整个操作的全部过程）。 |
| 3    | 若当前命令的请求缓存功能是被启用的， 并且该命令缓存命中， 那么缓存的结果会立即以 Observable 对象的形式 返回。 |
| 4    | 检查断路器是否为打开状态。如果断路器是打开的，那么Hystrix不会执行命令，而是转接到 fallback 处理逻辑（第 8 步）；如果断路器是关闭的，检查是否有可用资源来执行命令（第 5 步）。 |
| 5    | 线程池/请求队列/信号量是否占满。如果命令依赖服务的专有线程池和请求队列，或者信号量（不使用线程池的时候）已经被占满， 那么 Hystrix 也不会执行命令， 而是转接到 fallback 处理逻辑（第8步）。 |
| 6    | Hystrix 会根据我们编写的方法来决定采取什么样的方式去请求依赖服务。HystrixCommand.run() ：返回一个单一的结果，或者抛出异常。HystrixObservableCommand.construct()： 返回一个Observable 对象来发射多个结果，或通过 onError 发送错误通知。 |
| 7    | Hystrix会将 "成功"、"失败"、"拒绝"、"超时" 等信息报告给断路器， 而断路器会维护一组计数器来统计这些数据。断路器会使用这些统计数据来决定是否要将断路器打开，来对某个依赖服务的请求进行 "熔断/短路"。 |
| 8    | 当命令执行失败的时候， Hystrix 会进入 fallback 尝试回退处理， 我们通常也称该操作为 "服务降级"。而能够引起服务降级处理的情况有下面几种：第4步： 当前命令处于"熔断/短路"状态，断路器是打开的时候。第5步： 当前命令的线程池、 请求队列或 者信号量被占满的时候。第6步：HystrixObservableCommand.construct() 或 HystrixCommand.run() 抛出异常的时候。 |
| 9    | 当Hystrix命令执行成功之后， 它会将处理结果直接返回或是以Observable 的形式返回。 |
## 服务监控

除了隔离依赖服务的调用以外，Hystrix 还提供了准实时的调用监控（Hystrix Dashboard），Hystrix 会持续地记录所有通过 Hystrix 发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求多少成功，多少失败等。Netflix 通过 hystrix-metrics-event-stream 项目实现了对以上指标的监控。Spring Cloud 也提供了 Hystrix Dashboard 的整合，对监控内容转化成可视化界面。

### 新建监控模块 `cloud-consumer-hystrix-dashboard9001`

为其添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
```

在主启动类上添加 `@EnableHystrixDashboard` 注解

```java
@SpringBootApplication
@EnableHystrixDashboard
public class DashboardStart {

    public static void main(String[] args) {
        SpringApplication.run(DashboardStart.class, args);
    }

}
```

主配置文件如下

```yml
server:
  port: 9001
  
# 如果出现下面报错，则添加此配置
hystrix:
  dashboard:
    proxy-stream-allow-list: "localhost"
```

如果出现报错，在主配置文件中添加上面的配置
```shell
ashboardConfiguration$ProxyStreamServlet : Origin parameter: http://localhost:9000/actuator/hystrix.stream is not in the allowed list of proxy host names.  If it should be allowed add it to hystrix.dashboard.proxyStreamAllowList.
```

> 注意：所有此模块和所有被监控的模块都需要添加监控依赖
>
> ```java
> <dependency>
>     <groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-starter-actuator</artifactId>
> </dependency
> ```

### 修改被监控的模块（8001）

在主启动类中添加下面内容

```java
@Bean
public ServletRegistrationBean getServlet() {
    HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
    ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
    registrationBean.setLoadOnStartup(1);
    registrationBean.addUrlMappings("/hystrix.stream");
    registrationBean.setName("HystrixMetricsStreamServlet");
    return registrationBean;
}
```

### 测试

访问：`http://localhost:9001/hystrix `，监听 8001 服务，需要在端口后添加 `/hystrix.stream`

![image-20220810173005731](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810173005731.png)

发起 `http://localhost:8001/payment/payMoneyTimeOut/100` 请求，得到如下监控信息

![image-20220810173210343](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810173210343.png)

### 监控内容详解

![image-20220810173247818](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810173247818.png)

![image-20220810173329825](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810173329825.png)

# Gateway 网关基础

gateway 类似于 nginx，可以帮助我们完成请求转发、解决跨域问题、负载均衡等操作

## 概述

SpringCloud Gateway 是 Spring Cloud 的一个全新项目，基于 Spring 5.0+Spring Boot 2.0 和 Project Reactor 等技术开发的网关，它旨在为微服务架构提供一种简单有效的统一的 API 路由管理方式。

SpringCloud Gateway 作为 Spring Cloud 生态系统中的网关，目标是替代 Zuul，在 Spring Cloud 2.0 以上版本中，没有对新版本的 Zuul 2.0以上最新高性能版本进行集成，仍然还是使用的 Zuul 1.x 非 Reactor 模式的老版本。而为了提升网关的性能，SpringCloud Gateway 是基于 WebFlux 框架实现的，而 WebFlux 框架底层则使用了高性能的 Reactor 模式通信框架 Netty。

Spring Cloud Gateway 的目标提供统一的路由方式且基于 Filter 链的方式提供了网关基本的功能，例如：安全，监控/指标，和限流。

> Cloud 全家桶中有个很重要的组件就是网关，在 1.x 版本中都是采用的 Zuul 网关；
> 但在 2.x 版本中，zuul 的升级一直跳票，SpringCloud 最后自己研发了一个网关替代 Zuul，
> 那就是 SpringCloud Gateway 一句话：gateway 是原 zuul1.x 版的替代

SpringCloud Gateway 使用的 Webflux 中的 reactor-netty 响应式编程组件，底层使用了 Netty 通讯框架。

### 作用

反向代理、鉴权、流量控制、熔断、日志监控

### 用在哪里

![image-20220810175305516](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810175305516.png)

### 特性

- 基于Spring Framework 5, Project Reactor 和 Spring Boot 2.0 进行构建；
- 动态路由：能够匹配任何请求属性；
- 可以对路由指定 Predicate（断言）和 Filter（过滤器）；
- 集成Hystrix的断路器功能；
- 集成 Spring Cloud 服务发现功能；
- 易于编写的 Predicate（断言）和 Filter（过滤器）；
- 请求限流功能；
- 支持路径重写。

### 核心概念

Route(路由)：路由是构建网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

Predicate(断言)：参考的是Java8的java.util.function.Predicate 开发人员可以匹配HTTP请求中的所有内容(例如请求头或请求参数)，如果请求与断言相匹配则进行路由

Filter(过滤)：指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。

> web请求，通过一些匹配条件，定位到真正的服务节点。并在这个转发过程的前后，进行一些精细化控制。
> predicate就是我们的匹配条件；
> 而filter，就可以理解为一个无所不能的拦截器。有了这两个元素，再加上目标uri，就可以实现一个具体的路由了

### 工作流程

官网总结：

![image-20220810175941235](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220810175941235.png)

客户端向 Spring Cloud Gateway 发出请求。然后在 Gateway Handler Mapping 中找到与请求相匹配的路由，将其发送到 Gateway Web Handler。

Handler 再通过指定的过滤器链来将请求发送到我们实际的服务执行业务逻辑，然后返回。

过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。

Filter 在 “pre” 类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转换等，在 “post” 类型的过滤器中可以做响应内容、响应头的修改，日志的输出，流量监控等有着非常重要的作用。

核心逻辑：路由转发+执行过滤器链

## 基本使用

新建网关服务 `cloud-gateway-gateway9527`

### 添加依赖

> 注意：不要添加 web 依赖
>
> ```xml
> <dependency>
>     <groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-starter-web</artifactId>
> </dependency>
> <dependency>
>     <groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-starter-actuator</artifactId>
> </dependency>
> ```

除了通用配置之外，需要添加网关和注册中心依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

### 修改主配置文件

将网关服务注册进注册中心

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-consumer-feign-hystrix-order80
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true。
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      # eureka server 地址
      defaultZone: http://localhost:7001/eureka
```

### 主启动类添加 @EnableEurekaClient 注解

```java
@SpringBootApplication
@EnableEurekaClient
public class GatewayStarter {
    public static void main(String[] args) {
        SpringApplication.run(GatewayStarter.class, args);
    }
}
```

### 在配置文件中新增网关配置

```yml
spring:
  application:
    name: cloud-consumer-feign-hystrix-order80
  cloud:
    # 网关配置
    gateway:
      # 路由
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          # 断言
          predicates:
            - Path=/payment/payMoney/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/payMoneyTimeOut/**         # 断言，路径相匹配的进行路由
```

### 启动网关

先启动注册中心 ——> 网关 ——> 8001

通过网关（9527）访问 8001，与直接通过 8001 访问相同

![image-20220811093659475](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811093659475.png)![image-20220811093817957](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811093817957.png)

## 通过配置文件配置路由

```java
@Configuration
public class GatewayConfig {

    @Bean
    public RouteLocator routeLocator (RouteLocatorBuilder builder) {
        RouteLocatorBuilder.Builder routes = builder.routes();
        routes.route("wangyi"/**id 随意，只是做区分，但不要重复**/, r -> r.path("/guonei"/**路由，断言**/)
                .uri("http://news.baidu.com/guonei"/**真实路径**/));
        return routes.build();
    }
}
```

重启网关，访问 `http://localhost:9527/guonei`

![image-20220811095244094](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811095244094.png)

## 通过微服务名实现动态路由

默认情况下 Gateway 会根据注册中心注册的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能。

修改 yml 文件，需要注意的是 uri 的协议为 lb，表示通过注册中心查找服务，并启用 Gateway 的负载均衡功能。

```yml
spring:
  application:
    name: cloud-gateway-9527
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态创建路由的功能，利用微服务名进行路由
      routes:
        - id: payment_routh #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: lb://CLOUD-PROVIDER-HYSTRIX-PAYMENT8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/payMoney/**         # 断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: lb://CLOUD-PROVIDER-HYSTRIX-PAYMENT8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/payMoneyTimeOut/**         # 断言，路径相匹配的进行路由
```

![chrome_p8Hlhm6qOf](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_p8Hlhm6qOf.gif)

## Predicate（路由谓词工厂）的使用

参考官网：[Route Predicate Factories 官网](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gateway-request-predicates-factories)

> 下方给出两个示例

```
Spring Cloud Gateway 将路由匹配作为 Spring WebFlux HandlerMapping 基础架构的一部分。
Spring Cloud Gateway 包括许多内置的Route Predicate工厂。所有这些Predicate都与HTTP请求的不同属性匹配。多个 Route Predicat 工厂可以进行组合
Spring Cloud Gateway 创建 Route 对象时， 使用 RoutePredicateFactory 创建 Predicate 对象，Predicate 对象可以赋值给 Route。Spring Cloud Gateway 包含许多内置的 Route Predicate Factories。
所有这些谓词都匹配 HTTP 请求的不同属性。多种谓词工厂可以组合，并通过逻辑 and。
```

![image-20220811140935356](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811140935356.png)

### After，Before，Between

`After` 路由谓词工厂采用一个参数 a （`datetime` 它是 java `ZonedDateTime`）。此谓词匹配在指定日期时间之后发生的请求。以下示例配置了一个 after 路由谓词

`Before` 路由谓词工厂采用一个参数 a （`datetime` 它是 java `ZonedDateTime`）。此谓词匹配在指定日期时间之前发生的请求。

`Between` 路由谓词工厂有两个参数，它们是 `datetime1` 和 `datetime2` java ZonedDateTime 对象。此谓词匹配发生在 `datetime1` 和 `datetime2` 之间可以请求 。`datetime2` 参数必须在之后 `datetime1`

```yml
spring:
  cloud:
    gateway:
      routes:
        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: lb://CLOUD-PROVIDER-HYSTRIX-PAYMENT8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/payMoneyTimeOut/**         # 断言，路径相匹配的进行路由
            # 2022-08-11T14:37:00.789-07:00[Asia/Shanghai] 时间之后才能访问此路由
            - After=2022-08-11T14:37:00.789-07:00[Asia/Shanghai]
```

### Cookie，Header，Host

`Cookie` 路由谓词工厂有两个参数，cookie 和 `name` 和 `regexp`（这是一个 Java 正则表达式）。此谓词匹配具有给定名称且其值与正则表达式匹配的 cookie。以下示例配置 cookie 路由谓词工厂：

Header，Host 与 Cookie 相同

```yml
spring:
  cloud:
    gateway:
      routes:
        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: lb://CLOUD-PROVIDER-HYSTRIX-PAYMENT8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/payMoneyTimeOut/**         # 断言，路径相匹配的进行路由
            # 具有名为 name 其值与 yyb 正则表达式匹配的 cookie 的请求。
            - Cookie=name, yyb
```

![image-20220811145045644](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811145045644.png)

## Filter 的使用

路由过滤器可用于修改进入的 HTTP 请求和返回的 HTTP 响应，路由过滤器只能指定路由进行使用。

Spring Cloud Gateway 内置了多种路由过滤器，他们都由 GatewayFilter 的工厂类来产生

Gateway 中，Filter 分为 [GatewayFilter](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#gatewayfilter-factories) 和 [Global Filters](https://docs.spring.io/spring-cloud-gateway/docs/current/reference/html/#global-filters)

AddRequestParameter：向请求的 Request 中添加名为 name 值为 yyb 的数据

```yml
spring:
  cloud:
    gateway:
      routes:
        - id: payment_routh2 #payment_route    #路由的ID，没有固定规则但要求唯一，建议配合服务名
          uri: lb://CLOUD-PROVIDER-HYSTRIX-PAYMENT8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/payMoneyTimeOut/**         # 断言，路径相匹配的进行路由
          filters:
            - AddRequestParameter=name, yyb
```

在 8001 中将其值返回给客户端

```java
    public R payMoneyTimeOut(HttpServletRequest request, @PathVariable(name = "money") String money){
        String name = request.getParameter("name");
        return R.ok("支付成功8001").setData(name);
    }
```

![image-20220811152826989](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811152826989.png)

## 自定义过滤器

创建一个自定义过滤器需要实现 GlobalFilter 和 Ordered 两个接口，他可以做全局日志记录和统一网关鉴权等作用。

示例：

```java
@Component
public class MyLogGateWayFilter implements GlobalFilter, Ordered {

    public MyLogGateWayFilter() {
        System.out.println("MyLogGateWayFilter.MyLogGateWayFilter()");
    }

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        System.out.println("time:"+new Date()+"\t 执行了自定义的全局过滤器: "+"MyLogGateWayFilter"+"hello");
        // 得到参数 uname
        String uname = exchange.getRequest().getQueryParams().getFirst("uname");
        if (uname == null) {
            System.out.println("****用户名为null，无法登录");
            // 设置响应状态码
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            // 返回给客户端
            return exchange.getResponse().setComplete();
        }
        // 过滤器执行完毕，继续向下执行
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;
    }
}

```

测试：

有 uname 参数，请求成功

![image-20220811175100164](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811175100164.png)

![image-20220811175123378](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811175123378.png)

没有 uname 参数，请求失败

![image-20220811175007562](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811175007562.png)

![image-20220811174954915](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811174954915.png)



# Config 配置中心

## 概述

### 简介

> 微服务意味着要将单体应用中的业务拆分成一个个子服务，每个服务的粒度相对较小，因此系统中会出现大量的服务。由于每个服务都需要必要的配置信息才能运行，所以一套集中式的、动态的配置管理设施是必不可少的。
>
> SpringCloud提供了ConfigServer来解决这个问题，我们每一个微服务自己带着一个application.yml，上百个配置文件的管理......

![image-20220811180118309](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220811180118309.png)

SpringCloud Config 为微服务架构中的微服务提供集中化的外部配置支持，配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置。

SpringCloud Config分为服务端和客户端两部分。

- 服务端也称为分布式配置中心，它是一个独立的微服务应用，用来连接配置服务器并为客户端提供获取配置信息，加密/解密信息等访问接口。
- 客户端则是通过指定的配置中心来管理应用资源，以及与业务相关的配置内容，并在启动的时候从配置中心获取和加载配置信息配置服务器默认采用git来存储配置信息，这样就有助于对环境配置进行版本管理，并且可以通过git客户端工具来方便的管理和访问配置内容。

### 作用

集中管理配置文件

不同环境不同配置，动态化的配置更新，分环境部署比如：dev/test/prod/beta/release

运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件，服务会向配置中心统一拉取配置自己的信息

当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置

将配置信息以 REST 接口的形式暴露：post、curl 访问刷新均可......

>由于 SpringCloud Config 默认使用 Git 来存储配置文件(也有其它方式,比如支持SVN和本地文件)；
>
>但最推荐的还是Git，而且使用的是http/https访问的形式

## 基本使用，创建 config 服务端

参考官网：[spring cloud 官方文档](https://cloud.spring.io/spring-cloud-static/spring-cloud-config/2.2.1.RELEASE/reference/html/#_spring_cloud_config_server)

### 先在 GitHub 中新建一个仓库，用于存放配置文件，`GitHub 访问很慢，可以使用 Gitee`

![image-20220812112255456](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812112255456.png)

application-dev.yml

```yml
spring: 
  cloud:
    config:
      dev: 1
usernames: yyb
```

### 创建配置中心模块 `cloud-config-center-3344`

引入依赖，除了固定依赖，还需要引入 eurika 和 config-service

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

修改配置文件，配置存放文件的远程仓库和 eureka 注册中心

```yml
server:
  port: 3344

spring:
  application:
    name:  cloud-config-center #注册进Eureka服务器的微服务名
  cloud:
    config:
      # 读取分支
      label: master
      server:
        git:
          uri: https://github.com/allure11/springcloud-config.git #GitHub上面的git仓库名字
          # 搜索目录
          search-paths:
            - springcloud-config
          timeout: 5000 #超时时间
#          使用Gitee需要添加思路用户名和密码
#          username: zym2894290453@gmail.com
#          password: zhong20010911
#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

在启动类上添加 `@EnableConfigServer` 注解

```java
@SpringBootApplication
@EnableConfigServer
public class CloudConfigStart {

    public static void main(String[] args) {
        SpringApplication.run(CloudConfigStart.class, args);
    }
}
```

启动配置中心模块，测试通过 Config 微服务是否可以从 GitHub 上获取配置内容

浏览器访问 3344 模块，获取配置文件信息 `http://localhost:3344/master/application-dev.yml`

![image-20220812134829585](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812134829585.png)

### 读取配置规则

label：分支(branch)
name ：服务名
profiles：环境(dev/test/prod)

![image-20220812132156053](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812132156053.png) 

## 创建客户端 `cloud-config-client-3355`

与服务端一样，需要添加固定配置及 config 和 eureka 客户端依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

创建 bootstrap 配置文件，yml 和 porperties 都可以

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: application #配置文件名称
      profile: dev #读取后缀名称   上述3个综合：master分支上config-dev.yml的配置文件被读取http://config-3344.com:3344/master/application-dev.yml
      uri: http://localhost:3344 #配置中心地址

#服务注册到eureka地址
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
```

创建控制器，读取配置中心中配置文件的 `usernames` 属性

```java
package com.zym.cloudservice.cloudconfig.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ConfigController
{
    @Value("${usernames}")
    private String usernames;

    @GetMapping("/usernames")
    public String getConfigInfo()
    {
        return usernames;
    }
}
```

主启动类添加 `@EnableEurekaClient` 将服务注册进注册中心

```java
@EnableEurekaClient
@SpringBootApplication
public class ConfigClient {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClient.class, args);
    }
}
```

启动服务

通过控制器读取到配置中心中的 `usernames`

![image-20220812135136475](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812135136475.png)

## 问题

修改远程库中的配置文件，username 改成 `xxy`，分别查看配置中心服务（3344）和业务服务（3355）

发现配置中心（3344）中的配置文件已经刷新

![image-20220812135500915](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812135500915.png)

但是业务服务（3355）中读取到的数据仍然是 `yyb`，并没有刷新配置文件

![image-20220812135546229](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812135546229.png)

难到每次运维修改配置文件，客户端都需要重启？？

## 解决上面问题

在业务服务（3355）引入监控模块

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

修改 bootstrap.yml 暴露监控端点

```yml
# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

在业务类添加 @RefreshScope 注解

```java
@RestController
@RefreshScope
public class ConfigController
{
    @Value("${usernames}")
    private String usernames;

    @GetMapping("/usernames")
    public String getConfigInfo()
    {
        return usernames;
    }
}
```

启动 3355 ，用于刚刚启动，3355 读取到的 `usernames` 为之前的 `xxy`

再次修改 github 进行测试，将 `username` 修改成 `pink`

发现，3344 配置中心中数据已经修改，但 3355 还是没变

![image-20220812143528158](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812143528158.png)

向 3355 中发送 post 求 `curl -X POST "http://localhost:3355/actuator/refresh"`

![image-20220812143506323](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812143506323.png)

成功后发现 3355 中的配置更新成功

![chrome_dyV1OrglxN](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_dyV1OrglxN.png)

## 还有问题

假如有多个微服务客户端3355/3366/3377。。。。。。

每个微服务都要执行一次post请求，手动刷新？

可否广播，一次通知，处处生效？

我们想大范围的自动刷新，求方法

# Spring Bus 消息总线

## 概述

Spring Cloud Bus 配合 Spring Cloud Config 使用可以实现配置的动态刷新。

Spring Cloud Bus 是用来将分布式系统的节点与轻量级消息系统链接起来的框架，它整合了 Java 的事件处理机制和消息中间件的功能。

Spring Clud Bus 目前支持 RabbitMQ 和 Kafka。

Spring Cloud Bus 能管理和传播分布式系统间的消息，就像一个分布式执行器，可用于广播状态更改、事件推送等，也可以当作微服务间的通信通道。

### 什么是总线

在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线。在总线上的各个实例，都可以方便地广播一些需要让其他连接在该主题上的实例都知道的消息。

### 基本原理

ConfigClient 实例都监听 MQ 中同一个 topic (默认是 springCloudBus)。当一个服务刷新数据的时候，它会把这个信息放入到 Topic 中，这样其它监听同一 Topic 的服务就能得到通知，然后去更新自身的配置。

## 设计思想

- 利用消息总线触发一个客户端 /bus/refresh，而刷新所有客户端的配置

    <img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812152818191.png" alt="image-20220812152818191" style="zoom:50%;" />

- 利用消息总线触发一个服务端 ConfigServer 的 /bus/refresh 端点，而刷新所有客户端的配置

    <img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812152910702.png" alt="image-20220812152910702" style="zoom:50%;" />

图二的架构显然更加适合，图一不适合的原因如下

> 打破了微服务的职责单一性，因为微服务本身是业务模块，它本不应该承担配置刷新的职责。
>
> 破坏了微服务各节点的对等性。
>
> 有一定的局限性。例如，微服务在迁移时，它的网络地址常常会发生变化，此时如果想要做到自动刷新，那就会增加更多的修改

## 基本使用

安装 rabbitMQ 自行百度

为了演示复杂度，在根据 3355 创建一个 3366

为配置中心（3344）和业务服务（3355,3366）都添加消息总线支持

添加依赖

```xml
<!--添加消息总线RabbitMQ支持-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```

添加 rabbitmq 的配置，注意不能错

```yml
spring:
  #rabbitmq相关配置 15672是Web管理界面的端口；5672是MQ访问的端口
  rabbitmq:
    host: www.springzym.ltd
    port: 5673
    username: admin
    password: 123
```

在配置中心（3344）暴露 `bus-refresh` 端口，新版本可能是`busrefresh` 

```yml
# 暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: bus-refresh
```

将 github 中配置文件的 `usernames` 值改为 `yyb`

发现配置中心已经修改完成，但 3355 和 3366 还是原来的值

![image-20220812173915125](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812173915125.png)

发送 post 请求到配置中心（3344）`curl -X POST http://localhost:3344/actuator/busrefresh`

![image-20220812174014545](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812174014545.png)

再次查看 3355 和 3366 的 usernames 值，发现已经全部修改完成

![image-20220812174109208](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812174109208.png)

## 如果只想全部修改一部分服务怎么办

公式：http://localhost:配置中心的端口号/actuator/bus-refresh/{destination}

> destination 为服务名称加端口号

例如：`curl -X POST "http://localhost:3344/actuator/bus-refresh/config-client:3355"` 只修改端口号为 3355 的 config-client 服务

![image-20220812174907840](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220812174907840.png)

# Strem 消息驱动

## 概述

官网：https://docs.spring.io/spring-cloud-stream/docs/current/reference/html/index.html

屏蔽底层消息中间件的差异，降低切换成本，统一消息的编程模型

### 标准 MQ

生产者/消费者之间靠消息媒介传递信息内容（Message）

消息必须走特定的通道（消息通道MessageChannel）

消息通道里的消息如何被消费呢，谁负责收发处理：

​	消息通道 MessageChannel 的子接口 SubscribableChannel，由 MessageHandler 消息处理器所订阅

![image-20220815091512237](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815091512237.png)

### 为什么用 Stream

比方说我们用到了 RabbitMQ 和 Kafka，由于这两个消息中间件的架构上的不同，像 RabbitMQ 有 exchange，kafka 有 Topic 和 Partitions 分区。

这些中间件的差异性导致我们实际项目开发给我们造成了一定的困扰，我们如果用了两个消息队列的其中一种，后面的业务需求，我想往另外一种消息队列进行迁移，这时候无疑就是一个灾难性的，一大堆东西都要重新推倒重新做，因为它跟我们的系统耦合了，这时候 springcloud Stream 给我们提供了一种解耦合的方式。

Stream 对消息中间件的进一步封装，可以做到代码层面对中间件的无感知，甚至于动态的切换中间件(rabbitmq 切换为 kafka)，使得微服务开发的高度解耦，服务可以关注更多自己的业务流程.

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815091939907.png" alt="image-20220815091939907" style="zoom: 80%;" />

在没有绑定器这个概念的情况下，我们的 SpringBoot 应用要直接与消息中间件进行信息交互的时候，由于各消息中间件构建的初衷不同，它们的实现细节上会有较大的差异性。

通过定义绑定器作为`中间层`，完美地`实现了应用程序与消息中间件细节之间的隔离`。

通过向应用程序暴露统一的 Channel 通道，使得应用程序不需要再考虑各种不同的消息中间件实现。

通过定义`绑定器 Binder 作为中间层`，实现了应用程序与消息中间件细节之间的隔离。

### Binder

Binder 可以生成 Binding，Binding 用来绑定消息容器的生产者和消费者，它有两种类型，INPUT 和 OUTPUT，INPUT 对应于消费者，OUTPUT 对应于生产者。

Stream 中的消息通信方式遵循了发布-订阅模式。

![image-20220815092422051](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815092422051.png)

### 标准流程

Binder：很方便的连接中间件，屏蔽差异

Channel：通道，是队列 Queue 的一种抽象，在消息通讯系统中就是实现存储和转发的媒介，通过 Channel 对队列进行配置

Source 和 Sink：简单的可理解为参照对象是 Spring Cloud Stream 自身，从 Stream 发布消息就是输出，接受消息就是输入。

![image-20220815092615748](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815092615748.png)

## 常用 API 和注解

| 组成            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| Middleware      | 中间件，目前只支持 RabbitMQ 和 Kafka                         |
| Binder          | Binder 是应用于消息中间件之间的封装，目前实行了 Kafka 和 RabbitMQ 的 Binder，通过 Binder 可以很方便的连接中间件，可以动态的改变消息类型（对应于 Kafka 的 topic，RabbitMQ 的 exchange），这些都可以通过配置文件来实现 |
| @Input          | 注解标识输入通道，通过该输入通道接收到的消息进入应用程序     |
| @Output         | 注解标识输出通道，发布的消息将通过该通道离开应用程序         |
| @StreamListener | 监听队列，用于消费者的队列的消息接收                         |
| @EnableBinding  | 指信道 channel 和 exchange 绑定在一起                        |

## 基本使用

创建三个子模块

### cloud-stream-rabbitmq-provider8801，作为生产者进行发消息模块

#### 依赖导入

除了固定依赖，还需要引入 stream-rabbit ，如下

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-stream-rabbit</artifactId>
</dependency>
```

### 主配置文件

```yml
server:
  port: 8801

spring:
  rabbitmq:
    host: www.springzym.ltd
    port: 5673
    username: guest
    password: guest
  application:
    name: cloud-stream-provider
  cloud:
  	# 重点
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
      bindings: # 服务的整合处理
        output: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为json，文本则设置“text/plain”
          binder: defaultRabbit # 表示使用的绑定服务的名称

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: send-8801  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

#### 编写发送消息的业务类

接口

```java
public interface IMessageProvider {
    String send();
}
```

实现

```java
@Service
@EnableBinding(Source.class)
public class MessageProviderImpl implements IMessageProvider {

    /**
     * 消息的发送管道
     */
    @Resource
    private MessageChannel output;

    @Override
    public String send()
    {
        String serial = UUID.randomUUID().toString();
        // 创建并发送消息
        this.output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("***serial: "+serial);

        return serial;
    }
}

```

#### 创建 controller 发送消息

```java
@RestController
public class SendMessageController {

    @Resource
    private IMessageProvider messageProvider;

    @GetMapping(value = "/sendMessage")
    public String sendMessage()
    {
        return messageProvider.send();
    }
}

```

### cloud-stream-rabbitmq-consumer8802，作为消息接收模块

#### pom 文件与上面的消息发送模块一样

#### 主配置文件也大致相同，秩序修改端口等不能一样的位置

```yml
server:
  port: 8802

spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置
  rabbitmq:
    host: www.springzym.ltd
    port: 5673
    username: guest
    password: guest

eureka:
  client: # 客户端进行Eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2 # 设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 # 如果现在超过了5秒的间隔（默认是90秒）
    instance-id: receive-8802  # 在信息列表时显示主机名称
    prefer-ip-address: true     # 访问的路径变为IP地址
```

#### 添加消息接收的业务类

```java
@Service
@EnableBinding(Sink.class)
public class ReceiveMessageListener {

    @Value("${server.port}")
    private String serverPort;

    @StreamListener(Sink.INPUT)
    public void input(Message<String> message)
    {
        System.out.println("消费者1号，------->接收到的消息：" + message.getPayload()+"\t port: "+serverPort);
    }

}
```

#### 测试

依次启动 Eureka 服务，消息发送者和接收者

访问发送方的接口，向 MQ 发送消息

![image-20220815104706926](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815104706926.png)

查看控制台

8001:![image-20220815104737324](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815104737324.png)

8002:![image-20220815104757171](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815104757171.png)

### cloud-stream-rabbitmq-consumer8803，作为消息接收模块

直接复制 cloud-stream-rabbitmq-consumer8802，修改端口号等，创建出 8003，作为第二个消息接收模块

测试：

请求消息发送方的接口，发送消息

![image-20220815104958444](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815104958444.png)

发现 8002 和 8003 同时对消息进行了消费

![image-20220815105029658](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815105029658.png)

![image-20220815105037933](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815105037933.png)

## 问题

重复消费：消费者一号和消费者二号同时消费了一条消息

持久化：如果消息发出时，消费者不在（宕机或者没有启动），那么这个消息将会丢失，消费者重新连接后也不会此消息

### 解决办法

分组和持久化属性 group

比如在如下场景中，订单系统我们做集群部署，都会从 RabbitMQ 中获取订单信息，那如果一个订单同时被两个服务获取到，那么就会造成数据错误，我们得避免这种情况。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815110328397.png" alt="image-20220815110328397" style="zoom: 67%;" />

这时我们就可以使用 Stream 中的消息分组来解决

注意在 Stream 中处于同一个 group 中的多个消费者是竞争关系，就能够保证消息只会被其中一个应用消费一次。

不同组是可以全面消费的(重复消费)，同一组内会发生竞争关系，只有其中一个可以消费。

**<font color=red>原理:</font>**微服务应用放置于同一个 group 中，就能够保证消息只会被其中一个应用消费一次。不同的组是可以消费的，同一个组内会发生竞争关系，只有其中一个可以消费。

### 测试将 8002 和 8003 设置在不同组

修改 8802 和 8803 的配置文件，将其设置在不同的组中，并重新启动

8003 配置文件：

```yml
spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置
          group: condumer8803
```

8002 配置文件：

```yml
spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置
          group: condumer8802
```



调用消息发送接口

![image-20220815110917101](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815110917101.png)

![image-20220815110934389](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815110934389.png)

发现两个消息接收者同时接收了该消息

![image-20220815111013122](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815111013122.png)

![image-20220815111028449](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815111028449.png)

### 测试将 8002 和 8003 设置在相同组

```yml
spring:
  application:
    name: cloud-stream-consumer
  cloud:
    stream:
      binders: # 在此处配置要绑定的rabbitmq的服务信息；
        defaultRabbit: # 表示定义的名称，用于于binding整合
          type: rabbit # 消息组件类型
      bindings: # 服务的整合处理
        input: # 这个名字是一个通道的名称
          destination: studyExchange # 表示要使用的Exchange名称定义
          content-type: application/json # 设置消息类型，本次为对象json，如果是文本则设置“text/plain”
          binder: defaultRabbit # 设置要绑定的消息服务的具体设置
          group: condumer
```

调用消息发送者发送两条消息

![image-20220815111648902](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815111648902.png)

可以看到 8002 和 8003 各消费一条

![image-20220815111717768](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815111717768.png)

![image-20220815111730852](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815111730852.png)

### 结论

8802/8803 实现了轮询分组，每次只有一个消费者进行消费。

8801 模块的发的消息只能被 8802 或 8803 其中一个接收到，这样避免了重复消费。

### 测试持久化问题

停止 8802 和 8803，删除 8802 中的 group 配置

通过消费者发送 4 条消息

![image-20220815113101147](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815113101147.png)

先启动 8802，发现消息未被消费

再启动 8803，发现其消费了 4 条消息

![image-20220815113318476](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815113318476.png)

# Sleuth 分布式请求链路跟踪

## 概述

在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的的服务节点调用来协同产生最后的请求结果，每一个前段请求都会形成一条复杂的分布式服务调用链路，链路中的任何一环出现高延时或错误都会引起整个请求最后的失败。

Spring Cloud Sleuth 提供了一套完整的服务跟踪的解决方案，在分布式系统中提供追踪解决方案并且兼容支持了 zipkin

![image-20220815114606350](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815114606350.png)

## 基本使用

安装 zipkin：[官网](https://zipkin.io/pages/quickstart.html)

启动成功

![image-20220815135107944](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815135107944.png)

### 术语

Trace:类似于树结构的Span集合，表示一条调用链路，存在唯一标识

span:表示调用链路来源，通俗的理解span就是一次请求信息

下图表示一请求链路，一条链路通过 Trace Id 唯一标识，Span 标识发起的请求信息，各 span 通过 parent id 关联起来

![image-20220815135222892](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815135222892.png)

---

上图核心：

![image-20220815135246465](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815135246465.png)

---

结果：

![image-20220815135320138](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815135320138.png)

### 操作实践

修改服务提供者和服务消费者，添加如下依赖

```xml
<!--包含了sleuth+zipkin-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

服务消费者和服务提供者都添加如下配置

```yml
spring:
  zipkin:
    base-url: http://www.springzym.ltd:9411
  sleuth:
    sampler:
      probability: 0.5 #采样率值介于 0 到 1 之间，1 则表示全部采集
```

启动服务，进行测试

顺序：zipkin ——》 Eureka ——》 提供者和消费者

发起请求，通过服务消费者调用服务

![image-20220815153215184](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815153215184.png)

访问 zipkin 服务：点击搜索（run query）可以看到请求链路，点击搜索栏的加号可以添加搜索条件

![image-20220815153402966](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220815153402966.png)

# SpringCloud Alibaba

## 作用

**服务限流降级**：默认支持 Servlet、Feign、RestTemplate、Dubbo 和 RocketMQ 限流降级功能的接入，可以在运行时通过控制台实时修改限流降级规则，还支持查看限流降级 Metrics 监控。

**服务注册与发现**：适配 Spring Cloud 服务注册与发现标准，默认集成了 Ribbon 的支持。

**分布式配置管理**：支持分布式系统中的外部化配置，配置更改时自动刷新。

**消息驱动能力**：基于 Spring Cloud Stream 为微服务应用构建消息驱动能力。

**阿里云对象存储**：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。

**分布式任务调度**：提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有 Worker（schedulerx-client）上执行。

![image-20220816165651509](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220816165651509.png)

# 基本使用（注册中心 nacos）

## 父工程添加依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<modules>
		<module>common</module>
		<module>service</module>
	</modules>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.7.0</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<groupId>com.zym</groupId>
	<artifactId>guli_parent</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<name>guli_parent</name>
	<description>guli_parent</description>

	<packaging>pom</packaging>

	<properties>
		<java.version>1.8</java.version>
		<guli.version>0.0.1-SNAPSHOT</guli.version>
		<cloud-alibaba.version>0.2.2.RELEASE</cloud-alibaba.version>
		<spring-cloud-dependencies.version>2021.0.1</spring-cloud-dependencies.version>
		<nacos.version>2021.1</nacos.version>
		<hystrix.version>2.2.0.RELEASE</hystrix.version>
		<ribbon.version>2.2.0.RELEASE</ribbon.version>

	</properties>

	<dependencyManagement>
		<dependencies>
			<!--nacos注册中心-->
			<dependency>
				<groupId>com.alibaba.cloud</groupId>
				<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
				<version>${nacos.version}</version>
			</dependency>

            <!--熔断器-->
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
				<version>${hystrix.version}</version>
			</dependency>

            <!--负载均衡-->
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-netflix-ribbon</artifactId>
				<version>${ribbon.version}</version>
			</dependency>

            <!--Spring Cloud-->
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud-dependencies.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>

```

## 生产者添加 nacos 注册中心依赖

```xml
<!--服务注册-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

## 生产者配置注册中心地址和服务名

```yml
spring:
  cloud:
    nacos:
      config:
        file-extension: yaml
      discovery:
        # nacos服务地址
        server-addr: 127.0.0.1:8848
  application:
    name: service-vod
```

## 生产者 controller 添加 `@RefreshScope` 注解

```java
package com.zym.controller;
/**
 * 视频点播控制器
 * @author zym
 */
@CrossOrigin
@RequestMapping("/video")
@RestController
@RefreshScope
public class VideoController {

    @Resource
    private VodService vodServiceImpl;

    /**
     * 视频上传
     * @param file 视频文件
     * @return
     */
    @PostMapping("/uploadVideo")
    public R videoUpload(MultipartFile file) {
        try {
            String title = file.getOriginalFilename();
            String fileName = file.getResource().getFilename();
            Long categoryId = 1000410185L;
            InputStream inputStream = file.getInputStream();
            System.out.println(title);
            System.out.println(fileName);
            return vodServiceImpl.videoUpload(title, fileName, categoryId, inputStream);
        } catch (IOException e) {
            e.printStackTrace();
            return R.error();
        }
    }

    /**
     * 获取播放地址
     * @param videoId 视频id
     */
    @PostMapping("/getPlayAddress/{videoId}")
    public R getPlayAddress(@PathVariable String videoId) {
        return vodServiceImpl.getPlayAddress(videoId);
    }

    /**
     * 删除视频
     * @param videoId
     * @return
     */
    @DeleteMapping("/{videoId}")
    public R removeVideo(@PathVariable String videoId){
        return vodServiceImpl.deleteVideo(videoId);
    }
}
```

## 生产者启动类添加 `@EnableDiscoveryClient` 注解

```java
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class})
@EnableDiscoveryClient	// 服务注册
public class ServiceVodApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServiceVodApplication.class, args);
    }
}
```

## 消费者添加依赖

```xml
<!--服务调用-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<!--服务注册-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

## 消费者配置注册中心地址和服务名

```yml
spring:
  cloud:
    nacos:
      discovery:
        # nacos服务地址
        server-addr: localhost:8848
  application:
    name: service-edu
```

## 消费者启动类添加注解 `@EnableDiscoveryClient` 和 `@EnableFeignClients`

```java
@SpringBootApplication
@EnableDiscoveryClient	// 服务注册
@EnableFeignClients		// 服务发现
public class ServiceEduApplication {

    public static void main(String[] args) {
        SpringApplication.run(ServiceEduApplication.class, args);
    }
}
```

## 消费者中创建服务接口

```java
/**
 * vod 服务调用
 * @author zym
 */
@FeignClient(value = "service-vod")
@Component
public interface VodClient {

    /**
     * 删除视频
     * @param videoId
     * @return
     */
    @DeleteMapping("/video/{videoId}")
    R removeVideo(@PathVariable String videoId);
}
```

## 业务类中注入接口，即可调用服务

```java
/**
 * 课程视频 服务实现类
 * @author baomidou
 * @since 2022-05-29
 */
@Service
public class VideoServiceImpl extends ServiceImpl<VideoMapper, Video> implements VideoService {

    
    /**
    * 注入服务接口
    */
    @Resource
    private VodClient vodClient;

    /**
     * 删除小节
     * @param videoId
     * @return
     */
    @Override
    public R removeVideo(String videoId) {
        Video video = getById(videoId);
        // 调用服务删除视频
        R r = vodClient.removeVideo(video.getVideoSourceId());
        if (r.getStatus() == true){
            boolean b = removeById(videoId);
            if (b){
                return R.ok().setData("message", "删除成功");
            }
        }
        return R.error().setData("message", "删除失败");
    }
}
```

# 整合 hystrix 熔断器

## 消费者添加熔断器依赖

```java
<!--熔断器-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
<!--负载均衡-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-netflix-ribbon</artifactId>
</dependency>
```

## 为服务调用接口添加实现类，调用远程服务失败就会调用对应的方法

```java
/**
 * vod 服务调用失败处理
 * @author zym
 */
@Component
public class VodClientImpl implements VodClient {

    /**
     * 删除视频
     * @param videoId
     * @return
     */
    @Override
    public R removeVideo(String videoId) {
        return R.error().setData("message", "删除视频失败");
    }
}
```

## 修改接口注解 `@FeignClient`，添加 `fallback` 等于实现类的 class

```java
/**
 * vod 服务调用
 * @author zym
 */
@FeignClient(value = "service-vod",fallback = VodClientImpl.class)
@Component
public interface VodClient {

    /**
     * 删除视频
     * @param videoId
     * @return
     */
    @DeleteMapping("/video/{videoId}")
    R removeVideo(@PathVariable String videoId);
}
```

## 修改配置文件，开启熔断器

```yml
feign:
  circuitbreaker:
    # 开启熔断器
    enabled: true
hystrix:
  metrics:
  	# 设置超时时间
    polling-interval-ms: 1000
```











# Gateway 网关

## 创建 gateway 模块（一个 SpringBoot 的 Web 项目），引入依赖

```xml
<!--Spring Cloud-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure</artifactId>
</dependency>
<!--客户端负载均衡loadbalancer-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
<!--gson-->
<dependency>
    <groupId>com.google.code.gson</groupId>
    <artifactId>gson</artifactId>
</dependency>
<!--服务调用-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>

```

## 编写配置文件

```yaml
server:
  port: 10000
  servlet:
    application-display-name: api-gateway
spring:
  cloud:
    nacos:
      discovery:
        # nacos服务地址
        server-addr: localhost:8848
    gateway:
      discovery:
        locator:
          # 是否启用网关
          enabled: true
      # 路由设置
      routes:
        - id: edu
          # lb 表示从nacos中查找服务，也可以使用 http 或 https 形式的地址
          uri: lb://service-edu
          # 这里可以配置一些参数，/teacher/** 表示这种类型的请求会交给 /service-edu/teacher/** 下的控制器
          predicates:
            - Path=/teacher/**
        - id: sta
          uri: lb://service-sta
          predicates:
            - Path=/sta/**
          # 这里可以配置一些过滤器
          filters:
             # 这个过滤器配置表示解决多重跨域问题
             - DedupeResponseHeader=Access-Control-Allow-Credentials Access-Control-Allow-Origin # 过滤跨域重复
  application:
    name: api-gateway
```

## 测试

service-edu 需要时 nacos 中注册的服务，这里这个服务的请求地址有：http://localhost:8080/teacher/getAll

启动项目，访问网关地址进行测试：

http://loaclhost:10000/service-edu/teacher/getAll

http://loaclhost:10000/teacher/getAll

这两个地址访问相同的东西，与上面直接访问 http://localhost:8080/teacher/getAll 服务一样

## 解决跨域问题

在网关项目中添加解决跨域的配置类就能够解决跨域问题：（尽量不要配置多次跨域，可以先把 `@CrossOrigin` 配置的跨域删除）

```java
package com.zym.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.web.cors.reactive.CorsUtils;
import org.springframework.web.server.ServerWebExchange;
import org.springframework.web.server.WebFilter;
import org.springframework.web.server.WebFilterChain;
import reactor.core.publisher.Mono;

/**
 * 处理跨域
 * @author zym
 * @date 2019/01/02
 */
@Configuration
public class CorsConfig {

    @Bean
    public WebFilter corsFilter() {
        return (ServerWebExchange ctx, WebFilterChain chain) -> {
            ServerHttpRequest request = ctx.getRequest();
            if (CorsUtils.isCorsRequest(request)) {
                HttpHeaders requestHeaders = request.getHeaders();
                ServerHttpResponse response = ctx.getResponse();
                HttpMethod requestMethod = requestHeaders.getAccessControlRequestMethod();
                HttpHeaders headers = response.getHeaders();
                headers.add(HttpHeaders.ACCESS_CONTROL_ALLOW_ORIGIN, requestHeaders.getOrigin());
                headers.addAll(HttpHeaders.ACCESS_CONTROL_ALLOW_HEADERS,
                        requestHeaders.getAccessControlRequestHeaders());
                if (requestMethod != null) {
                    headers.add(HttpHeaders.ACCESS_CONTROL_ALLOW_METHODS, requestMethod.name());
                }

                headers.add(HttpHeaders.ACCESS_CONTROL_ALLOW_CREDENTIALS, "true");
                headers.add(HttpHeaders.ACCESS_CONTROL_EXPOSE_HEADERS, "*");
                if (request.getMethod() == HttpMethod.OPTIONS) {
                    response.setStatusCode(HttpStatus.OK);
                    return Mono.empty();
                }
            }
            return chain.filter(ctx);
        };
    }
}
```

# Nacos 配置中心

## 引入依赖

```xml
<!-- nacos 配置中心依赖 -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    <version>2.1.0.RELEASE</version>
</dependency>
<!-- 加载 bootstrap 配置文件 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bootstrap</artifactId>
</dependency>
```

## 进入 Nacos 创建配置文件

地址是 nacos 服务器的 http://localhost:8848/nacos

默认用户名：nacos		密码：nacos

点击配置管理可以创建配置文件

![image-20220628164531526](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220628164531526.png)

![image-20220628165122928](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220628165122928.png)

## 名称空间

默认只有一个 public 名称空间，可以在左侧点击名称空间进行添加

![image-20220628165615509](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220628165615509.png)

## 在不同的名称空间中创建配置文件

这里创建了三个配置文件，后面会演示引用多个配置文件的用法

test.yaml 中定义了一个数据，如下

```yaml
age: 80
```

test1.yaml 中定义了一个数据，如下

```yaml
name: xxy
```

![image-20220628165926278](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220628165926278.png)

## 在服务中创建 bootstrap 文件，可以是 properties 和 yaml 两种格式

```properties
#配置中心地址
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
# 该配置影响统一配置中心中的dataId（配置文件名称）
spring.application.name=service-sta
# 配置文件的名称空间
spring.cloud.nacos.config.namespace=294d213f-3abc-4034-8fae-a161800e7d3c
#当前的分组，配置的是啥Group，读取的就是啥group下的配置文件
spring.cloud.nacos.config.group=DEFAULT_GROUP
#配置文件的文件后缀
spring.cloud.nacos.config.fileExtension=yaml

# 加载多个配置文件
spring.cloud.nacos.config.ext-config[0].data-id=test.yaml
# 开启动态刷新配置，否则配置文件修改，工程无法感知
spring.cloud.nacos.config.ext-config[0].refresh=true

spring.cloud.nacos.config.ext-config[1].data-id=test1.yaml
spring.cloud.nacos.config.ext-config[1].refresh=true
```

## 测试

编写测试业务类：

```java
package com.zym.service.impl;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

@Service
public class test {

    /**
    *	注入 test 和 test1 两个配置文件中定义的数据
    */
    @Value("${name}")
    private String name;
    @Value("${age}")
    private String age;

    public void test() {
        System.out.println(name+" "+age);
    }
}
```

编写测试类，调用业务类的 test 方法进行测试：

```java
@SpringBootTest
class ServiceStaApplicationTests {

    @Resource
    private test test;

    @Test
    public void TowYaml(){
        test.test();
    }
}
```

运行 TowYaml 方法可以看到：

从nacos中加载了三个配置文件

![image-20220628170938099](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220628170938099.png)

输出结果

![image-20220628171028720](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220628171028720.png)

# Nacos 集群搭建

官网： https://nacos.io/zh-cn/docs/cluster-mode-quick-start.html

## 搭建数据库

运行 Nacos 安装目录下 conf 目录中的 nacos-mysql.sql 文件，创建好 Nacos 的 Mysql 数据库

![image-20220817094443717](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817094443717.png)

## 修改配置文件

在 config 目录下的 application.properties 中添加 MySQL 数据库配置

![image-20220817100042287](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817100042287.png)

## 测试

以单机版启动，添加一条配置，数据库是否配置成功

![image-20220817100918130](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817100918130.png)

![image-20220817100940490](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817100940490.png)

查看数据库 config_info 表，发现有配置信息，即表示配置成功

![image-20220817101024219](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817101024219.png)

## 修改 cluster.conf 文件

将集群 ip 和端口配置进文件

![image-20220817121016519](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817121016519.png)

## 修改 Nginx 配置文件

修改 nginx-1.22.0/conf/nginx.conf 文件，让请求 81 端口时，由 nginx 放在均衡到 nacos 服务器，如下

![image-20220817103032117](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817103032117.png)

## 启动 nacos

修改 startup.sh 文件，添加参数选项，通过 startup.sh 启动 nacos 时就可以 -p 指定端口号：

![image-20220817121249761](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817121249761.png)

![image-20220817121333853](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817121333853.png)

```shell
[root@ecs-352864 bin]# ./startup.sh -p 5555 -m cluster
···
nacos is starting with cluster # 以集群方式启动
nacos is starting，you can check the /usr/local/soft/nacos/nacos/logs/start.out

[root@ecs-352864 bin]# ./startup.sh -p 3333 -m cluster
···
nacos is starting with cluster
nacos is starting，you can check the /usr/local/soft/nacos/nacos/logs/start.out

[root@ecs-352864 bin]# ./startup.sh -p 4444 -m cluster
···
nacos is starting with cluster
nacos is starting，you can check the /usr/local/soft/nacos/nacos/logs/start.out
```

## 启动 Nginx

通过 nginx 访问到 nacos 成功

![image-20220817121921903](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817121921903.png)

## SpringCloud 整合 nacos 集群

> 注意：通过 springcloud 连接 nacos 集群可能需要开放除 nacos 端口之外的偏移端口，一般是偏移 1000 和 1001
>
> | 端口 | 与主端口的偏移量 | 描述                                                       |
> | :--- | :--------------- | :--------------------------------------------------------- |
> | 9848 | 1000             | 客户端gRPC请求服务端端口，用于客户端向服务端发起连接和请求 |
> | 9849 | 1001             | 服务端gRPC请求服务端端口，用于服务间同步等                 |

springcloud 整合 nacos 集群的时候，只需要将原来的 nacos 地址修改成通过 nginx 访问的地址即可，如下

```yml
spring:
  application:
    name: spring-cloud
  cloud:
    nacos:
      discovery:
        server-addr: 121.37.217.252:81
      config:
        server-addr: 121.37.217.252:81
        file-extension: yaml
```

## 体系总结

![image-20220817140345513](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817140345513.png)



# Sentinel 实现熔断与限流

## 概述

官网：https://sentinelguard.io/zh-cn/docs/logs.html

Sentinel 是面向分布式、多语言异构化服务架构的流量治理组件，主要以流量为切入点，从流量路由、流量控制、流量整形、熔断降级、系统自适应过载保护、热点流量防护等多个维度来帮助开发者保障微服务的稳定性。

### 功能：

![image-20220817141138439](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817141138439.png)

## 环境搭建

github 下载 jar 包：https://github.com/alibaba/Sentinel/tags

![image-20220817142540742](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817142540742.png)

运行 jar 包：

![image-20220817142627015](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817142627015.png)

命令执行成功后，访问本机 8080 端口，出现如下界面，即运行成功，默认用户名和密码都是 `sentinel`

![image-20220817142741319](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817142741319.png)

登录成功后显示如下页面

![image-20220817142918057](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817142918057.png)

## 测试项目搭建

创建模块 `cloudalibaba-sentinel-service8401`

引入依赖：除了正常注册中心、配置中心等依赖之外，还需要引入 sentinel 依赖，如下

```xml
<!--SpringCloud ailibaba sentinel-datasource-nacos 后续做持久化用到-->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
</dependency>
<!--SpringCloud ailibaba sentinel -->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
</dependency>
```

修改配置

aplication.yml 中的 sentinel 配置

```yml
spring:
  cloud:
    sentinel:
      transport:
        #配置Sentinel dashboard地址
        dashboard: http://localhost:8080
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描,直至找到未被占用的端口
        port: 8719
management:
  endpoints:
    web:
      exposure:
        include: '*'
```

bootstrap.yml 中的 nacos 配置

```yml
spring:
  application:
    name: spring-cloud
  cloud:
    nacos:
      discovery:
        server-addr: 121.37.217.252:81
      config:
        server-addr: 121.37.217.252:81
        file-extension: yaml
```

创建控制器，定义两个请求

```java
@RestController
public class SentinelController {

    @GetMapping("/testa")
    public String testa() {
        return "---Test-A---";
    }

    @GetMapping("/testb")
    public String testb() {
        return "---Test-B---";
    }

}

```

启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class SentinelStarter {

    public static void main(String[] args) {
        SpringApplication.run(SentinelStarter.class, args);
    }

}
```

## 启动项目

启动 `cloudalibaba-sentinel-service8401`，查看 sentinel 监控页面，没有显示

发送请求，访问 `/testa` 和 `/testb`，再次刷新 sentinel 监控页面，出现了当前服务

![image-20220817145307622](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817145307622.png)

## Sentinel 功能测试

所有请求都会显示在触点链路中，点击右边按钮，可以对各个请求作出设置

![image-20220817145640938](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817145640938.png)

### 流控规则

`FlowSlot` 会根据预设的规则，结合前面 `NodeSelectorSlot`、`ClusterNodeBuilderSlot`、`StatistcSlot` 统计出来的实时信息进行流量控制。

限流的直接表现是在执行 `Entry nodeA = SphU.entry(资源名字)` 的时候抛出 `FlowException` 异常。`FlowException` 是 `BlockException` 的子类，您可以捕捉 `BlockException` 来自定义被限流之后的处理逻辑。

同一个资源可以对应多条限流规则。`FlowSlot` 会对该资源的所有限流规则依次遍历，直到有规则触发限流或者所有规则遍历完毕。

一条限流规则主要由下面几个因素组成，我们可以组合这些元素来实现不同的限流效果：

- `resource`：资源名，即限流规则的作用对象
- `count`: 限流阈值
- `grade`: 限流阈值类型，QPS 或线程数
- `strategy`: 根据调用关系选择策略

#### 流控模式

直接（默认）：表示1秒钟内查询1次就是OK，若超过次数1，就直接-快速失败，报默认错误 `Blocked by Sentinel (flow limiting)`

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817150140190.png" alt="image-20220817150140190" style="zoom:67%;" />

![chrome_OaspEFRDBz](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_OaspEFRDBz.png)

关联：当关联的资源达到阈值时，就限流自己，testb 关联 testa，当 testa 请求量大时，testb 就会熔断报错

通过 apipost 发送 100 此请求到 testa，此时发现 testb 报错 `Blocked by Sentinel (flow limiting)`

![image-20220817153241822](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817153241822.png)

链路：多个请求调用了同一个微服务，多个请求同时调用 testa 时，testb 报错

![image-20220817153846273](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817153846273.png)

#### 流控效果

直接->快速失败 (默认)：直接失败，抛出异常，上面都是

Warm Up 预热：

​	公式：阈值除以coldFactor(默认值为3),经过预热时长后才会达到阈值

​	默认coldFactor为3，即请求 QPS 从 threshold / 3 开始，经预热时长逐渐升至设定的 QPS 阈值。

![image-20220817154427950](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817154427950.png)

![chrome_40ZIDEQD4D](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_40ZIDEQD4D.gif)

排队等待：

​	匀速排队，让请求以均匀的速度通过，阀值类型必须设成QPS，否则无效。

​	设置含义：/testA 每秒 1 次请求，超过的话就排队等待，等待的超时时间为 100 毫秒。

![image-20220817155113312](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817155113312.png)

![chrome_hzxeH16yKT](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_hzxeH16yKT.gif)

### 熔断降级

Sentinel 熔断降级会在调用链路中某个资源出现不稳定状态时（例如调用超时或异常比例升高），对这个资源的调用进行限制，
让请求快速失败，避免影响到其它的资源而导致级联错误。

当资源被降级后，在接下来的降级时间窗口之内，对该资源的调用都自动熔断（默认行为是抛出 DegradeException）。

![image-20220817160605048](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817160605048.png)

#### 慢调用比例 RT（平均响应时间，秒级）

>平均响应时间超出阈值，且在时间窗口内通过的请求>=5，两个条件同时满足后触发降级
>窗口期过后关闭断路器
>RT最大4900（更大的需要通过-Dcsp.sentinel.statistic.max.rt=XXXX才能生效）

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817171042101.png" alt="image-20220817171042101" style="zoom:80%;" /><img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817172505517.png" alt="image-20220817172505517" style="zoom:80%;" />

#### 异常比列（秒级）

> QPS >= 5 且异常比例（秒级统计）超过阈值时，触发降级；时间窗口结束后，关闭降级

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817173038054.png" alt="image-20220817173038054" style="zoom:80%;" /><img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817173056004.png" alt="image-20220817173056004" style="zoom:80%;" />

#### 异常数（分钟级）

> 异常数（分钟统计）超过阈值时，触发降级；时间窗口结束后，关闭降级
>
> 注意：异常数是按照分钟统计的，时间窗口一定要大于等于60秒。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817173417845.png" alt="image-20220817173417845" style="zoom:80%;" /><img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220817173508173.png" alt="image-20220817173508173" style="zoom:80%;" />

### 热点 key

何为热点：热点即经常访问的数据，很多时候我们希望统计或者限制某个热点数据中访问频次最高的 TopN 数据，并对其访问进行限流或者其它操作

@SentinelResource：设置降级方法，@SentinelResource 主管配置出错，运行出错该走异常走异常

修改 `cloudalibaba-sentinel-service8401` 的 `SentinelController`，添加下面方法

```java
@GetMapping("/hotSpot")
@SentinelResource(value = "hotSpot", fallback = "hotSpotException")
public String hotSpot(String param1,String param2) {
    log.info(param1+"    "+param2);
    return "---HotSpot---";
}
public String hotSpotException(String param1,String param2) {
    log.info(param1+"    "+param2);
    return "---hotSpotException---";
}
```

#### 普通情况

重启服务，添加热点规则：参数索引表示控制方法中参数的位置，下面设置的 `0` 就表示 `param1`

![image-20220818141054958](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818141054958.png)

测试发现：

http://localhost:8401/hotSpot?param1=123 和 http://localhost:8401/hotSpot?param1=123&&param2=456 被限流，达到单机阈值后会熔断

![chrome_nT0cOjNIgL](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_nT0cOjNIgL.gif)

http://localhost:8401/hotSpot?param2=456 不会被限流

#### 特殊情况

> 注意：热点参数的注意点，参数必须是基本类型或者String

我们期望 `param1` 参数当它是某个特殊值时，它的限流值和平时不一样，假如当 `param1` 的值等于 `5` 时，它的阈值可以达到 `100`

![image-20220818141840464](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818141840464.png)

测试：当参数 param1 的值是 yyb 时，限流阈值是 100，参数值为其他值时，阈值还是 1

![chrome_ZNhA6aUq9d](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/chrome_ZNhA6aUq9d.gif)

### 系统规则

官网：https://sentinelguard.io/zh-cn/docs/system-adaptive-protection.html

![image-20220818144805441](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818144805441.png)

![image-20220818144151760](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818144151760.png)

### @SentinelResource

官网：https://sentinelguard.io/zh-cn/docs/annotation-support.html

#### 注意

**<font color=red>这个注解只针对异常生效，对下面输出的信息无效</font>**

![image-20220818160255981](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818160255981.png)

#### 基本使用

@SentinelResource 中，value 参数就是为该方法指定资源名称

![image-20220818161028137](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818161028137.png)

超过限流规则后，显示自定义信息：

> 注意：
>
> fallback 管运行异常
>
> blockHandler 管配置违规

![image-20220818150124127](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818150124127.png)

![image-20220818150139406](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818150139406.png)

#### 解决问题

上面的方式所有控制方法都需要写一个降级方法，出现很多问题

>系统默认的，没有体现我们自己的业务要求。
>
>依照现有条件，我们自定义的处理方法又和业务代码耦合在一块，不直观。
>
>每个业务方法都添加一个兜底的，那代码膨胀加剧。
>
>全局统一的处理方法没有体现。

添加降级类：

> 注意：必须是 static 方法，参数列表必须和控制器方法相同，在参数最后必须有 BlockException 参数接收异常

```java
public class CustomerBlockHandler {

    public static String handleException(String param1,String param2,BlockException ex) {
        return "---CustomerBlockHandler---";
    }
}
```

修改控制方法：

```java
    @GetMapping("/hotSpot")
    @SentinelResource(value = "hotSpot",
                      blockHandlerClass = CustomerBlockHandler.class,	// 指定降级类
                      blockHandler = "handleException")		// 指定降级方法
    public String hotSpot(String param1,String param2) {
        log.info(param1+"    "+param2);
        return "---HotSpot---";
    }
```

### 服务熔断

#### 整合 ribbon

**新建提供者**

新建提供者 `cloudalibaba-provider-payment9003` 和 `cloudalibaba-provider-payment9004`

添加依赖：除了固定依赖之外，添加 nacos 依赖即可

修改 yml 配置文件，添加 nacos 注册中心和端口号

```yml
server:
  port: 9003
spring:
  application:
    name: payment9003
  cloud:
    nacos:
      discovery:
        server-addr: 121.37.217.252:81
```

创建控制器

```java
@RestController
public class PaymentController {

    @Value("${server.port}")
    private String port;

    static Map<Integer,String> map = new HashMap<>();

    static {
        map.put(1, UUID.randomUUID().toString());
        map.put(2, UUID.randomUUID().toString());
        map.put(3, UUID.randomUUID().toString());
    }

    @GetMapping(value = "/paymentSQL/{id}")
    public R paymentSQL(@PathVariable("id") Long id)
    {
        return R.ok("请求成功").setData(map.get(id.intValue())+"："+port);
    }

}
```

主启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
public class Payment9004Starter {

    public static void main(String[] args) {
        SpringApplication.run(Payment9004Starter.class, args);
    }
}
```

复制一份，修改端口号等，当做 `cloudalibaba-provider-payment9004`

启动，通过 nacos 看到 payment9003 服务有两个实例

![image-20220818165138484](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220818165138484.png)

**用 8401 当做消费者**

添加 RestTemplate 配置

```java
@Configuration
public class RibbonConfig {
    
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

