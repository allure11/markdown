[TOC]

# 分布式概述

## Dubbo的发展史

2011年托管到GitHub上开源——>2014年11月发布2.4.11版本后停止更新——>2017年SpringClode发布后，Dubbo开始更新——>2018年阿里联合当当网合并Dubbo和Dubbo X，发布2.6版本——>2018年除夕，阿里将Dubbo贡献给Apache基金会，从此Dubbo由Apache维护和更新

## 什么是分布式框架

分布式系统是若干独立的系统的集合，但用户使用起来像是在使用一台系统

## 为什么要使用分布式系统

规模的逐渐扩大和业务的复杂，单台计算机扛不住双十一那样大的流量，必须使用分布式架构，将项目分部在几台服务器。[^三个臭皮匠，顶个诸葛亮]

可以对某个分量大的模块的节点单独扩展和提高

## 应用框架的发展演变

### 单一架构

​		当网站流量很小的时候，我们将所有的业务放到一台服务器上打包运行。例如：公司管理系统，超市收银系统

​		优点：开发简单，部署简单

​		缺点：扩展麻烦，维护困难，性能提升困难

### 垂直应用架构

​		将大应用拆分成小应用[^一般按照业务拆分]，根据不同的访问频率决定各自业务部署的服务器数量

​		优点：扩展容易

​		缺点：页面更改可能造成整个业务重新部署，业务和界面没有分离，随着业务种类的增加，怎么解决业务之间相互调用的问题，订单服务器的业务服务器交互效率的问题

### 分布式架构[^基于RPC：远程过程调用]

​		将业务拆分后，用某种方式实现各个业务模块的远程调用和复用，这时，一个好的RPC框架决定了你的分布式架构的性能，怎么调用、何时调用服务器挂了怎么办……，我们需要一个框架来帮助解决这个问题。（国内最nb的应该就是Dubbo，抗住了双十一的流量）

其他RPC框架：gRpc、Thrift、HSF……

### 为什么说Dubbo性能高

​		高性能要从底层原理说起，既然是一个RPC框架，主要就是远程过程（方法）调用，那么，提升性能就要从最关键，最耗时的两个方面入手：序列化和网络通信

​	**序列化：**我们学习java网络开发的时候，知道本地对象在网络上传输，必须实现Serializable接口，也就是必须序列化。我们序列化的方式很多，xml、json、二进制流……，其中效率最高的就是二进制流，然而，Dubbo采用的就是二进制流。

​		**网络通信：**不同于Http需要进行7走，Dubbo采用Socket通信机制，一步到位，提升了通信效率，并且可以建立长连接，不用反复连接，直接传输数据。



# Dubbo概述

​		Dubbo是一款高性能，轻量级的java RPC框架，它提供了三大核心功能：面向接口的方法调用，智能容错和负载均衡，以及服务自动注册和发现

​		Dubbo是一个分布式服务框架，致力于提供高性能和透明化的RPC远程服调用方案，服务治理方案

​		Dubbo支持的协议很多，官方推荐使用dubbo协议，dubbo协议的默认端口是20880

**官网：**[http://dubbo.apache.org/zh](https://dubbo.apache.org/zh/)

**如果使用监控中心，推荐使用  2.7  以上版本的dubbo**

![image-20210713215207834]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210713215207834.png)

**架构：**[详细信息](https://dubbo.apache.org/zh/docs/v2.7/user/preface/architecture/)

![image-20210713170511339]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210713170511339.png)

# 第一个Dubbo分布式应用的例子

## 步骤

1. ##### 创建两个模块：dubbo01[^提供者]，dubbo02[^消费者]

2. ##### dubbo01的pom文件中添加Dubbo依赖，消费者的依赖于提供者一样，只不过消费者需要依赖提供者

```xml
	<!--Dubbo依赖-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>dubbo</artifactId>
      <version>2.6.2</version>
    </dependency>
    <!--spring依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>5.3.8</version>
    </dependency>
    <!--springMVC依赖-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.8</version>
    </dependency>
```

3. ##### 在提供者dubbo01中创建业务类及其接口

   ```java
   //接口
   package com.zym.Service;
   public interface SomeService {
       String hello(String msg);
   }
   
   //实现类
   package com.zym.Service.Impl;
   import com.zym.Service.SomeService;
   public class SomeServiceImpl implements SomeService {
       @Override
       public String hello(String msg) {
           //业务
           return "Hello"+msg;
       }
   }
   ```

4. ##### 消费者依赖提供者

   使用maven，将提供者打包并发送到本地仓库（install），注销`<packaging>`，打jar包

   ![image-20210713181253710]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210713181253710.png)

   在dubbo02中添加dubbo01中的所有依赖，并且依赖dubbo01，目的是查看dubbo01暴露了那些接口

   ```xml
   <!--添加提供者的依赖-->
       <dependency>
         <groupId>com.zym</groupId>
         <artifactId>dubbo01</artifactId>
         <version>1.0-SNAPSHOT</version>
       </dependency>
   ```

5. ##### 在dubbo01中创建核心Dubbo配置文件(直接使用spring配置文件模板)

   ![image-20210714125108543]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714125108543.png)

   ```xml
    <!--声明服务提供者名称，保证其唯一性，他是dubbo内部使用的唯一标识-->
       <dubbo:application name="dubbo01"/>
       <!--
           指定dubbo的协议名称和端口号
               name : 指定协议名称
               port ：指定协议的端口号
       -->
       <dubbo:protocol name="dubbo" port="20880"/>
       <!--
       暴露服务
           interface:暴露服务接口的全限定名
           ref：引用接口在spring容器中的标识名称
           registry：使用直连方式，不使用注册中心，值为 N/A
   		timeout：超时时间，单位毫秒
       -->
       <dubbo:service interface="com.zym.Service.SomeService" ref="someServiceImpl" registry="N/A"/>
       <!--加载接口实现类-->
       <bean id="someServiceImpl" class="com.zym.Service.Impl.SomeServiceImpl"/>
   ```

6. ##### 在dubbo01的web.xml中加载dubbo的核心配置文件

   ```xml
   <!--通过监听器加载dubbo的核心配置文件-->
     <listener>
       <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
     </listener>
     <context-param>
       <param-name>contextConfigLocation</param-name>
       <param-value>classpath:dubbo-link-provider.xml</param-value>
     </context-param>
   ```

   

7. ##### 在消费者dubbo02中调用提供者dubbo01的业务类

   ```java
   package com.zym.Controller;
   
   import com.zym.Service.SomeService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   public class SomeController {
   
       @Autowired
       private SomeService someService;
   
       @RequestMapping("/hello")
       public String hello(Model model){
           //调用远程Service服务
           String hello = someService.hello(" world");
           model.addAttribute("hello",hello);
           return "index";
       }
   }
   ```

   

8. ##### 在dubbo02中创建消费者的核心配置文件（直接使用spring配置文件模板）

   ```xml
    <!--声明服务提供者名称，保证其唯一性，他是dubbo内部使用的唯一标识-->
       <dubbo:application name="dubbo02"/>
       <!--
           引用远程接口
               id: 远程接口服务的代理对象名称
               interface：接口的全限定类名
               url：调用远程接口服务的url地址(提供者的地址)
               registry：直连方式，不使用注册中心  N/A  
       -->
       <dubbo:reference interface="com.zym.Service.SomeService" id="someService" url="dubbo://localhost:20880" registry="N/A"/>
   ```

   

9. ##### 创建springmvc的配置文件

   ```xml
    <!--注解扫描-->
       <contxet:component-scan base-package="com.zym.Controller"/>
       <!--注解驱动-->
       <mvc:annotation-driven/>
       <!--视图解析器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="suffix" value=".jsp"/>
           <property name="prefix" value="/"/>
       </bean>
   ```

   

10. ##### dubbo02配置中央调度器

    ```xml
     <!--中央调度器-->
        <servlet>
            <servlet-name>dispatcherServlet</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:dubbo-link-consumer.xml,classpath:springmvc.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatcherServlet</servlet-name>
            <url-pattern>*.do</url-pattern>
        </servlet-mapping>
    ```

11. ##### 创建index视图

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <%=request.getAttribute("hello")%>
    </body>
    </html>
    ```

12. ##### 测试

    定义两个tomcat，一个用来部署项目dubbo01，一个用来部署项目dubbo02，两个tomcat端口不能一样，访问时使用dubbo02的tomcat端口和地址，直接访问消费者dubbo02的controller

13. ##### 结果

    ![image-20210713194349791]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210713194349791.png)

## 这个例子存在的问题：

​		**问题：**没有为开发者屏蔽远程调用的底层细节。

​		**解决方法：**使用dubbo官方推荐的项目结构。

#### dubbo官方推荐使用的项目结构：

>服务提供者工程
>
>> 实现接口工程中的业务接口
>>
>> web工程
>
>服务消费者工程
>
>> 消费服务提供者提供的业务接口
>>
>> web工程
>
>接口工程
>
>> 业务接口和实体类
>>
>> 普通java工程

# 使用官方推荐的目录结构做的第二个例子

## 步骤

1. ##### 创建一个java工程作为接口工程[^dubbo03],服务提供者[^dubbo04],消费者[^dubbo05]

2.  ##### dubbo03中添加实体类、业务类

   <font color="red">**注意：分布式开发中，所有的本地对象必须进行序列化，也就是实现Serializable接口。例如：`public class User implements Serializable{`**</font>

   ```java
   //实体类
   package com.zym.Model;
   public class User implements Serializable{
       private int id;
       private String name;
       @Override
       public String toString() {
           return "User{" +
                   "id=" + id +
                   ", name='" + name + '\'' +
                   '}';
       }
       public int getId() {return id;}
       public void setId(int id) {this.id = id;}
       public String getName() {return name;}
       public void setName(String name) {this.name = name;}
   }
   ```

   ```java
   //业务类接口
   package com.zym.Service;
   import com.zym.Model.User;
   	public interface SomeService {
       	String hello();
       	User queryUserById();
   	}
   }
   ```

3. ##### 将接口工程打包并发布到本地仓库

   ![image-20210713233654140]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210713233654140.png)

4. ##### 为提供者添加依赖，dubbo，spring，springmvc和接口工程[^dubbo3]的依赖

   ```xml
   <!--接口工程依赖-->
       <dependency>
         <groupId>com.zym</groupId>
         <artifactId>dubbo03</artifactId>
         <version>1.0-SNAPSHOT</version>
       </dependency>
   ```

5. ##### 在提供者内为接口工程添加业务接口的实现类

   ```java
   package com.zym.ServiceImpl;
   
   import com.zym.Model.User;
   import com.zym.Service.SomeService;
   
   public class SomeServiceImpl implements SomeService {
       @Override
       public String hello() {
           return "Hello";
       }
       @Override
       public User queryUserById(Integer id) {
           User user = new User();
           user.setId(id);
           user.setName("lisi-"+id);
           return user;
       }
   }
   ```

6. ##### 创建提供者的核心配置文件

   ```xml
   	<!--声明服务提供者名称，保证其唯一性-->
       <dubbo:application name="dubbo04"/>
       <!--指定协议和端口号,官方推荐dubbo协议，默认端口20880-->
       <dubbo:protocol name="dubbo" port="20880"/>
       <!--暴露服务接口-->
       <dubbo:service interface="com.zym.Service.SomeService" ref="someServiceImpl" registry="N/A"/>
       <!--加载接口的实现类-->
       <bean id="someServiceImpl" class="com.zym.ServiceImpl.SomeServiceImpl"/>
   ```

7. ##### dubbo04提供者中的web.xml配置文件中配置监听器，加载核心配置文件

   ```xml
   	<listener>
           <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
       </listener>
       <context-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:dubbo-link-provider.xml</param-value>
       </context-param>
   ```

8. ##### 为消费者添加依赖，如果没有特殊需求，可以与提供者的依赖相同

   添加dubbo，spring，springmvc和接口工程[^dubbo3]的依赖

9. ##### 在消费者中创建Controller

   ```java
   @Controller
   public class SomeController {
   
       @Autowired
       private SomeService someService;
   
       @RequestMapping("/hello1.do")
       public String hello(Model model){
           //调用远程接口服务
           String hello = someService.hello();
           model.addAttribute("hello",hello);
           return "hello";
       }
   
       @RequestMapping("/user/detail.do")
       public String userDetail(Model model,Integer id){
           User user = someService.queryUserById(id);
           model.addAttribute("user",user);
           return "userDetail";
       }
   }
   ```

10. ##### 创建消费者的主配置文件

    ```xml
        <!--服务消费者名称，保证他的唯一性-->
        <dubbo:application name="dubbo05"/>
        <!--引用远程接口服务-->
        <dubbo:reference interface="com.zym.Service.SomeService" id="someService" url="dubbo://localhost:20880" registry="N/A"/>
    ```

11. ##### 创建消费者中的mvc配置文件

    ```xml
    	<!--注解扫描-->
        <context:component-scan base-package="com.zym.controller"/>
        <!--注解驱动-->
        <mvc:annotation-driven/>
        <!--视图解析器-->
        <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <property name="prefix" value="/"/>
            <property name="suffix" value=".jsp"/>
        </bean>
    ```

12. ##### 创建需要的页面，hello.jsp、userDetail.jsp

    ```html
    <!--
    hello.jsp
    -->
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <%=request.getAttribute("hello")%>
    </body>
    </html>
    ```

    ```html
    <!--
    userDetail.jsp
    -->
    <%@ page import="com.zym.Model.User" %><%--
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <html>
    <head>
        <title>Title</title>
    </head>
    <body>
    <%
        User user = (User) request.getAttribute("user");
    %>
    id : <%=user.getId()%>
    name : <%=user.getName()%>
    </body>
    </html>
    ```

13. ##### 在消费者的web.xml文件中配置中央调度器

    ```xml
    <!--中央调度器-->
        <servlet>
            <servlet-name>dispatcherServlet</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <init-param>
                <param-name>contextConfigLocation</param-name>
                <param-value>classpath:dubbo-link-consumer.xml,classpath:springmvc.xml</param-value>
            </init-param>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatcherServlet</servlet-name>
            <url-pattern>*.do</url-pattern>
        </servlet-mapping>
    ```

14. ##### 在tomcat服务器上部署提供者[^dubbo04]，消费者[^dubbo05]

15. ##### 测试

    访问 /hello1.do:

    ![image-20210713235007149]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210713235007149.png)

访问 /user/detail.do：

![image-20210714000721417]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714000721417.png)

<font color="red">**注意：当接口工程中的代码发生修改后，提供者和消费者项目都需要重启tomcat服务**</font>

# 注册中心zookeeper

## 概述

​		对于服务提供方，它需要发布服务，服务的数量，类型也不断膨胀；对于消费方，它关心如何霍获取它所需要的服务，而面对复杂的应用系统，需要管理大量的服务调用

​		对于服务的提供者和消费者来说，他们还可能兼具这两种角色，即一个模块即需要提供服务，也需要消费服务。通过服统一管理起来，可以有效的优化内部应用对服务的发布和使用流程的流程和管理。服务注册中心可以通过特定的协议来完成服务的对外统一。

## 第一个使用注册中心的例子

**接口工程可以不用更改直接使用原来的dubbo03**

1. ##### 创建提供者[^duubo-zk1]和消费者[^duubo-zk2]

2. ##### 给对应的提供者[^duubo-zk1]和消费者[^duubo-zk2]添加依赖，之前的基础上在加上zookeeper依赖

   ```xml
    	<!--zookeeper依赖-->
       <dependency>
         <groupId>org.apache.curator</groupId>
         <artifactId>curator-framework</artifactId>
         <version>4.1.0</version>
       </dependency>
   ```

3. ##### 添加业务接口的实现类

   ```java
   package com.zym.ServiceImpl;
   
   import com.zym.Model.User;
   import com.zym.Service.SomeService;
   
   public class SomServiceImpl implements SomeService {
       @Override
       public String hello() {
           return "Hello zookeeper";
       }
   
       @Override
       public User queryUserById(Integer id) {
           User user = new User();
           user.setId(id);
           user.setName("zs"+id);
           return user;
       }
   }
   ```

4. ##### 创建服务提供者[^dubbo-zk1]的主配置文件

   ```xml
    <!--声明服务提供者名称-->
       <dubbo:application name="dubbo-zk1"/>
       <!--指定协议及端口号-->
       <dubbo:protocol name="dubbo" port="20880"/>
       <!--指定注册中心-->
       <dubbo:registry address="zookeeper://localhost:2181"/>
       <!--暴露服务-->
       <dubbo:service interface="com.zym.Service.SomeService" ref="someServiceImpl"/>
       <!--加载接口实现类-->
       <bean id="someServiceImpl" class="com.zym.ServiceImpl.SomServiceImpl"/>
   ```

5. ##### 在提供者的web.xml文件中配置监听器，加载核心配置文件

   ```xml
   	<listener>
           <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
       </listener>
       <context-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:dubbo-link-provider.xml</param-value>
       </context-param>
   ```

6. ##### 在消费者中创建Controller类

   ```java
   package com.zym.controller;
   
   import com.zym.Model.User;
   import com.zym.Service.SomeService;
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.stereotype.Controller;
   import org.springframework.ui.Model;
   import org.springframework.web.bind.annotation.RequestMapping;
   
   @Controller
   public class SomeController {
   
       @Autowired
       private SomeService someService;
   
       @RequestMapping("/hello2.do")
       public String hello(Model model){
           //调用远程接口服务
           String hello = someService.hello();
           model.addAttribute("hello",hello);
           return "hello";
       }
   
       @RequestMapping("/user/detail-zk.do")
       public String userDetail(Model model,Integer id){
           User user = someService.queryUserById(id);
           model.addAttribute("user",user);
           return "userDetail";
       }
   }
   ```

7. ##### 创建消费者的dubbo核心配置文件

   ```xml
       <!--声明消费者名称-->
       <dubbo:application name="dubbo-zk2"/>
       <!--指定注册中心-->
       <dubbo:registry address="zookeeper://localhost:2181"/>
       <!--引用远程接口服务-->
       <dubbo:reference id="someService" interface="com.zym.Service.SomeService"/>
   ```

8. ##### 消费者中创建springmvc的配置文件

   ```xml
       <!--注解扫描-->
       <context:component-scan base-package="com.zym.controller"/>
       <!--注解驱动-->
       <mvc:annotation-driven/>
       <!--视图解析器-->
       <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
           <property name="prefix" value="/"/>
           <property name="suffix" value=".jsp"/>
       </bean>
   ```

9. ##### 消费者的web.xml中配置中央调度器

   ```xml
       <!--中央调度器-->
       <servlet>
           <servlet-name>dispatcherServlet</servlet-name>
           <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
           <init-param>
               <param-name>contextConfigLocation</param-name>
               <param-value>classpath:dubbo-zk-consumer.xml,classpath:springmvc.xml</param-value>
           </init-param>
           <load-on-startup>1</load-on-startup>
       </servlet>
   	<!--视图解析器-->
       <servlet-mapping>
           <servlet-name>dispatcherServlet</servlet-name>
           <url-pattern>*.do</url-pattern>
       </servlet-mapping>
   ```

10. ##### 开启zookeeper服务，再部署项目

    <font color="red">**一定要先开启zookeeper服务**</font>

11. ##### 测试

    ![image-20210714133043454]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714133043454.png)

![image-20210714133236544]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714133236544.png)

# Dubbo的配置

## 配置原则

<font color="red">**能在服务提供者配置访问参数就尽量在提供者里面设置，因为服务提供者更了解服务的各种参数**</font>

## 关闭检查

Dubbo会在启动时检查依赖的服务是否可用，不可用时会抛出异常，阻止spring初始化完成，以便上线时能够及早发现问题，默认`check=true`,通过`check=flse`来关闭检查。

例：

```xml
<!--关闭某个服务的启动时检查，在消费者核心配置文件中-->
<!--引用远程接口服务-->
<dubbo:reference id="someService" interface="com.zym.Service.SomeService" check="false"/>

<!--关闭注册中心启动时检查-->
<!--默认在启动服务时检查注册中心存在并已运行，注册中心不启动会报错-->
<!--指定注册中心-->
<dubbo:registry address="zookeeper://localhost:2181" check="false"/>
```

## 重试次数[^不建议设置,默认会重试一次]

消费者访问服务者，如果分失败，则重试或切换其他服务器，但重试会带来工厂的延迟，访问时间变长，用户体验较差，多次重试可能访问成功，可通过`retries=“2”`来设置重试次数（不包含第一次）

例：

```xml
<!--暴露服务-->
    <dubbo:service interface="com.zym.Service.SomeService" ref="someServiceImpl" retries="2"/>

<!--引用远程接口服务-->
    <dubbo:reference id="someService" interface="com.zym.Service.SomeService" retries="2"/>
```

## 超时时间

由于网络或服务端的不可靠，会导致调用出现一种不确定的状态（超时），为了避免超时导致客户端资源挂起耗尽，必须设置超时时间。

`timeout="2000"`,时间的单位是毫秒

例：

```xml
<!--暴露服务-->
    <dubbo:service interface="com.zym.Service.SomeService" ref="someServiceImpl" retries="2" timeout="2000"/>

<!--引用远程接口服务-->
    <dubbo:reference id="someService" interface="com.zym.Service.SomeService" retries="2" timeout="2000"/>
```

## 本地存根

远程服务后，客户端通常只剩下接口，而实现全在服务器端，但提供方有些时候想在客户端也执行部分逻辑，那么就在服务消费者这一端提供了一个Stub类，然后当消费者调用provider方提供的dubbo服务时，客户端生成 Proxy 实例，**这个Proxy实例就是我们正常调用dubbo远程服务要生成的代理实例**，然后消费者这方会把 Proxy 通过构造函数传给 消费者方的Stub ，然后把 Stub 暴露给用户，Stub 可以决定要不要去调 Proxy。**会通过代理类去完成这个调用，这样在Stub类中，就可以做一些额外的事，来对服务的调用过程进行优化或者容错的处理。**

写一个远程接口的本地存根实现类（实现远程接口）,最好写在接口工程中

```java
package com.zym.stub;

import com.zym.Service.SomeService;

//本地存根
public class SomeServiceStub implements SomeService {

    private final SomeService someService;
    /**
     * dubbo自动传入UserService的远程代理对象
     */
    public SomeServiceStub(SomeService someService) {
        this.someService = someService;
    }

    @Override
    public String hello() {
        //进行判断,参数是否存在什么的,做一些验证
        /**
         * if(参数 ！= null){
         *      //远程调用
         *      return someService.hello();
         * }
         * //return null; 会直接退出，不会执行需要调用的方法
         * return null;
         */
        System.out.println("SomServiceStub");
        return someService.hello();
    }
}

```

在提供者内配置

```xml
<dubbo:service interface="com.zym.Service.SomeService" stub="本地存根的全类型名"/>
<!--例-->
<dubbo:service interface="com.zym.Service.SomeService" stub="com.zym.stub.SomeServiceStub"/>

<dubbo:reference interface="com.zym.Service.SomeService" stub="com.zym.stub.SomeServiceStub"/>
```



## 版本号

每个接口都定义版本号，为后续不兼容升级通过可能，当一个接口有不同的实现，项目早期使用的一个实现类，之后创建接口的新的实现类。区分不同接口实现使用version。提供者和消费者版本号对应，才能找到正确的实现类

### 例子：在注册中心例子的基础上完成

**创建新的实现类**

```java
package com.zym.ServiceImpl;

import com.zym.Model.User;
import com.zym.Service.SomeService;

public class ServiceImpl implements SomeService {
    @Override
    public String hello() {
        return "Hello zookeeper 第二个 222222222";
    }

    @Override
    public User queryUserById(Integer id) {
        User user = new User();
        user.setId(id);
        user.setName("yyb 222"+id);
        return user;
    }
}
```

**提供者中暴露接口时，指定版本号**

```xml
	<!--暴露服务-->
    <dubbo:service interface="com.zym.Service.SomeService" ref="someServiceImpl" version="1.0"/>
    <dubbo:service interface="com.zym.Service.SomeService" ref="ServiceImpl" version="2.0"/>
    <!--加载接口实现类-->
    <bean id="someServiceImpl" class="com.zym.ServiceImpl.SomServiceImpl"/>
    <bean id="ServiceImpl" class="com.zym.ServiceImpl.ServiceImpl"/>
```

**消费者中，引用远程接口时也需要指定版本号**

```xml
	<!--引用远程接口服务-->
    <dubbo:reference id="someService" interface="com.zym.Service.SomeService" version="1.0"/>
    <dubbo:reference id="newSomeService" interface="com.zym.Service.SomeService" version="2.0"/>
```

**消费者的controller方法中使用name注入的方式注入业务接口的实现类对象，使用两个对象实现业务，进行对比**

```java
package com.zym.controller;

import com.zym.Model.User;
import com.zym.Service.SomeService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class SomeController {

    @Autowired
    private SomeService someService;              //1.0版本的实现类

    @Autowired
    private SomeService newSomeService;           //2.0版本的实现类

    /**
    *		使用2.0版本实现类完成业务
    */
    @RequestMapping("/hello3.do")
    public String hello2(Model model){
        //调用远程接口服务
        String hello = newSomeService.hello();
        model.addAttribute("hello",hello);
        return "hello";
    }
    @RequestMapping("/user/detail-zk2.do")
    public String userDetail2(Model model,Integer id){
        User user = newSomeService.queryUserById(id);
        model.addAttribute("user",user);
        return "userDetail";
    }

    /**
    *		使用1.0版本实现类完成业务
    */
    @RequestMapping("/hello2.do")
    public String hello(Model model){
        //调用远程接口服务
        String hello = someService.hello();
        model.addAttribute("hello",hello);
        return "hello";
    }
    @RequestMapping("/user/detail-zk.do")
    public String userDetail(Model model,Integer id){
        User user = someService.queryUserById(id);
        model.addAttribute("user",user);
        return "userDetail";
    }
}
```

**重启tomcat服务器，进行测试对比**

|                  1.0版本的实现类完成的业务                   |                  2.0版本的实现类完成的业务                   |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![image-20210714144555634]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714144555634.png) | ![image-20210714144616999]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714144616999.png) |
| <img src=" https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714144636953.png" alt="image-20210714144636953" style="zoom:80%;" /> | <img src=" https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714144658361.png" alt="image-20210714144658361" style="zoom:80%;" /> |

# 监控中心

## 什么是监控中心？

Dubbo的使用，其实只需要有注册中心，提供者和消费者就可以使用了，但是，并不能看到有哪些消费者和提供者，为了更好地调试，发现问题，解决问题，因此引入dubbo-admin[^监控中心]。通过dubbo-admin可以对消费者和提供者进行管理。可以在dubbo应用部署动态调整，服务的管理。

dubbo-admin：是一个图形化的服务管理页面，安装时必须制定注册中心地址，即可中注册中心获取到所有提供者和消费者，进行配置管理

dubbomonitor-simple：简单的监控中心

dubbo-admin下载地址：https://github.com/apache/dubbo-admin/tags

下载后使用maven命令打jar包

使用压缩[^WinRAR]工具打开:dubbo-admin-0.2.0.jar

![image-20210714153804852]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714153804852.png)

修改配置文件：application.properties[^springBoot的主配置文件]

![image-20210714153957033]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714153957033.png)

修改zookeeper服务地址：默认是本机的2181端口

![image-20210714154344951]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714154344951.png)

使用cmd进入jar包所在目录，java命令运行jar包

![image-20210714154734707]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714154734707.png)

浏览器搜索地址：http://127.0.0.1:7001/

# SpringBoot集成Dubbo

## 思路

1. 接口工程

   >存放实体类和业务接口
   >
   >java工程（使用maven就行，不需要使用SpringBoot）

2. 服务提供者

   > Spring Web工程
   >
   > 实现业务接口中的接口
   >
   > 集成Mybctis，Redis等永久层框架

3. 服务消费者

   > SpringBoot Web工程
   >
   > 处理浏览器端发送的请求
   >
   > 消费服务提供者所提供的请求

## 步骤

1. 创建所需要的三个工程

   接口工程（maven 管理的java工程即可），内容提供者，内容消费者

2. 添加接口

   ```java
   package com.zym.Service;
   public interface SomeService {
       String hello();
   }
   ```
   
   
   
3. 将接口工程打包并发布到maven本地依赖库

   

4. 添加依赖，提供者和服务者一样

   ```xml
   		<!--dubbo整合springboot框架的起步依赖-->
   		<dependency>
   			<groupId>com.alibaba.boot</groupId>
   			<artifactId>dubbo-spring-boot-starter</artifactId>
   			<version>0.2.0</version>
   		</dependency>
   		<!--zookeeper依赖-->
   		<dependency>
   			<groupId>com.101tec</groupId>
   			<artifactId>zkclient</artifactId>
   			<version>0.10</version>
   		</dependency>
   		<!--接口工程-->
   		<dependency>
   			<groupId>com.zym</groupId>
   			<artifactId>springboot-java</artifactId>
   			<version>1.0-SNAPSHOT</version>
   		</dependency>
   ```

5. 在提供者中配置dubbo信息

   在主配置文件(application.properties)中配置

   ```properties
   server.port=8081
   #配置dubbo的信息
   #声明服务提供者的名称
   dubbo.application.name=springboot-dubbo01
   #声明自己是服务提供者工程
   dubbo.server=true
   #指定注册中心
   dubbo.registry.address=zookeeper://127.0.0.1:2181
   ```

6. 实现业务接口（SomeServiceImpl）

   ```java
   package com.zym.ServiceImpl;
   
   import com.alibaba.dubbo.config.annotation.Service;
   import com.zym.Service.SomeService;
   import org.springframework.stereotype.Component;
   
   @Component
   //相当于<dubbo:service interface="" ref="" version="" tomeout="">
   @Service(interfaceClass = SomeService.class,version = "1.0")
   public class SomServiceImpl implements SomeService {
       @Override
       public String hello() {
           return "springboot-dubbo hello";
       }
   }
   ```

7. 在提供者启动类声明Dubbo的扫描器，扫描实现类的@Service注解

   ```java
   package com.zym;
   
   import com.alibaba.dubbo.config.spring.context.annotation.DubboComponentScan;
   import com.alibaba.dubbo.config.spring.context.annotation.EnableDubboConfig;
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.context.annotation.ComponentScan;
   
   @SpringBootApplication
   //@EnableDubboConfig
   //扫描实现类
   @DubboComponentScan(basePackages = "com.zym.ServiceImpl")
   public class SpringDubbo01Application {
   
   	public static void main(String[] args) {
   		SpringApplication.run(SpringDubbo01Application.class, args);
   	}
   }
   ```

8. 配置消费者核心配置文件

   ```properties
   server.port=8080
   #dubbo配置
   dubbo.application.name=springboot-dubbo01
   dubbo.registry.address=zookeeper://127.0.0.1:2181
   ```

9. 编写消费者的controller类

   ```java
   package com.zym.controller;
   
   import com.alibaba.dubbo.config.annotation.Reference;
   import com.zym.Service.SomeService;
   import org.springframework.stereotype.Controller;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.ResponseBody;
   
   @Controller
   public class SomServiceController {
   
       //相当于<dubbo:reference interface="" version="" chek="">
       @Reference(interfaceClass = SomeService.class,version = "1.0")
       private SomeService someService;
   
       @RequestMapping("/hello")
       @ResponseBody
       public Object hello(){
           System.out.println("hello");
           return someService.hello();
       }
   }
   ```

10. 在提供者和消费者的启动类添加开启dubbo的注解

    ```java
    package com.zym;
    
    import com.alibaba.dubbo.config.spring.context.annotation.EnableDubboConfig;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.context.annotation.ComponentScan;
    
    @SpringBootApplication
    //@EnableDubboConfiguration 一般加这个注解开启Dubbo，但我没有这个注解，不知道为什么
    @EnableDubboConfig  //表示开启dubbo，提供者如果有@DubboComponentScan扫描器可以不加
    public class SpringbootDubbo02Application {
    
    	public static void main(String[] args) {
    		SpringApplication.run(SpringbootDubbo02Application.class, args);
    	}
    
    }
    ```

11. 启动提供者和消费者的启动类，

    <font color="red">**注意：先开启注册中心**</font>

12. 测试，访问消费者的controller

    ![image-20210719125936728]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210719125936728.png)


# 高可用

## zookeeper服务器宕机

现象：监控中心宕机后依然不影响服务的使用，可用正常暴露提供者的服务

原因：注册中心宕机后，消费者和服务者通过本地缓存进行通讯

**没有注册中心的话可以通过直连的方式访问提供者的服务**

# 负载均衡

在集群负载均衡时，Dubbo提供了多种均衡策略，缺省为random，随机调用

## 负载均衡策略

#### RandomLoadBalance 基于权重的随机调用负载均衡， **默认方式**

基于权重，随机调用所有的服务器，调用的概率是当前服务器的权重，占所有服务器权重总和的百分比

#### RoundRobinLoadBlance 基于权重的轮询调用

基于权重，随机调用所有的服务器，调用的概率是当前服务器的权重，占所有服务器权重总和的百分比，但如果当前随机的服务器在上一次调用过，就会自动调用下一个服务器

如果没有设置权重，就循环依次调用所有的服务器

#### LeastActiveLoadBlance 最少活跃数调用法

统计上一次的调用时间，哪台服务器响应速度快，就会调用它

#### ConsistentHashLoadBalance 一致性Hash算法

如果请求的路径为 User?id=1 就使用一号服务器

如果请求的路径为 User?id=2 就使用二号服务器

只要调用的方法和参数一样，就使用一样的服务器，不会发生改变

## 使用方法：

```xml
<!--设置负载均衡策略-->
<!--提供者-->
<dubbo:service interface="..." loadbalance="roundrobin" />
<!--消费者-->
<dubbo:reference interface="..." loadbalance="roundrobin" />
```

可以在监控中心设置权重

![image-20210720102702903]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210720102702903.png)

# 服务降级

## 什么是服务降级

当前服务器压力剧增的情况下，根据实际业务情况及流量，对一些服务和页面有策略的不处理或换一种简单的方式处理，从而释放服务器资源，以保证核心交易正常或高校运作

可以通过服务降级功能临时屏蔽某个出错的非关键业务，并定义降级后的返回策略

其中：

- `mock=force:return+null` 表示消费方对该服务的方法调用都直接返回 null 值，不发起远程调用。用来屏蔽不重要服务不可用时对调用方的影响。
- 还可以改为 `mock=fail:return+null` 表示消费方对该服务的方法调用在失败后，再返回 null 值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响。

## 使用方法：

向注册中心写入动态配置覆盖规则

```xml
RegistryFactory registryFactory = ExtensionLoader.getExtensionLoader(RegistryFactory.class).getAdaptiveExtension();
Registry registry = registryFactory.getRegistry(URL.valueOf("zookeeper://10.20.153.10:2181"));
registry.register(URL.valueOf("override://0.0.0.0/com.foo.BarService?category=configurators&dynamic=false&application=foo&mock=force:return+null"));
```

可以直接在监控中心设置

![image-20210720104113984]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210720104113984.png)

# 集群容错

<font color="red">集群容错可以集成hystrix</font>

## 集群容错模式

### Failover Cluster

失败自动切换，当出现失败，重试其它服务器。通常用于读操作，但重试会带来更长延迟。可通过 `retries="2"` 来设置重试次数(不含第一次)。

重试次数配置如下：

```xml
<dubbo:service retries="2" />
```

或

```xml
<dubbo:reference retries="2" />
```

或

```xml
<dubbo:reference>
    <dubbo:method name="findFoo" retries="2" />
</dubbo:reference>
```

**提示**：该配置为缺省配置

### Failfast Cluster

快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

### Failsafe Cluster

失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

### Failback Cluster

失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

### Forking Cluster

并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 `forks="2"` 来设置最大并行数。

### Broadcast Cluster

广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。

现在广播调用中，可以通过 broadcast.fail.percent 配置节点调用失败的比例，当达到这个比例后，BroadcastClusterInvoker 将不再调用其他节点，直接抛出异常。 broadcast.fail.percent 取值在 0～100 范围内。默认情况下当全部调用失败后，才会抛出异常。 broadcast.fail.percent 只是控制的当失败后是否继续调用其他节点，并不改变结果(任意一台报错则报错)。broadcast.fail.percent 参数 在 dubbo2.7.10 及以上版本生效。

Broadcast Cluster 配置 broadcast.fail.percent。

broadcast.fail.percent=20 代表了当 20% 的节点调用失败就抛出异常，不再调用其他节点。

```text
@reference(cluster = "broadcast", parameters = {"broadcast.fail.percent", "20"})
```

**提示**：`2.1.0` 开始支持

# rpc原理

![img]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/24481811-78cff6556e000e55.png)

### 一次完整的RPC调用流程（同步调用，异步另说）如下：
1. 服务消费方（client）调用以本地调用方式调用服务；
2. client stub接收到调用后负责将方法、参数等组装成能够进行网络传输的消息体；
3. client stub找到服务地址，并将消息发送到服务端；
4. server stub收到消息后进行解码；
5. server stub根据解码结果调用本地的服务；
6. 本地服务执行并将结果返回给server stub；
7. server stub将返回结果打包成消息并发送至消费方；
8. client stub接收到消息，并进行解码；
9. 服务消费方得到最终结果。

RPC框架的目标就是要2~8这些步骤都封装起来，这些细节对用户来说是透明的，不可见的。

# 服务器之间通过netty通信

Netty是一个异步事件驱动的网络应用程序框架， 用于快速开发可维护的高性能协议服务器和客户端。它极大地简化并简化了TCP和UD

套接字服务器等网络编程。Netty堆成基于java的NIO

BIO：(Blocking IO)

![img]( https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/24481811-13c4f45be1dcc0a0.png)

NIO (Non-Blocking IO)

<img src=" https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/24481811-300a96b24fdd0d2c.png" alt="img" style="zoom:50%;" />

Selector 一般称 为选择器 ，也可以翻译为 多路复用器，

Connect（连接就绪）、Accept（接受就绪）、Read（读就绪）、Write（写就绪）

Netty基本原理：

<img src=" https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/24481811-4ceda7a3328f0fdd.png" alt="img" style="zoom: 67%;" />

# Dubbo的原理

## 框架设计

参考官网 https://dubbo.apache.org/zh/docs/v2.7/dev/design/

## 启动解析，加载配置信息



