# 概述
Spring 为视图层提供的基于 MVC 设计理念的优秀 Web 框架，目前最主流的 web 框架之一
Spring3.0 后全面超越 Struts2，成为最优秀的 MVC 框架。
Spring MVC 通过一套 MVC 注解，让 POJO 成为处理请求的控制器，而无须实现任何接口。
支持 REST 风格的 URL 请求。
采用了松散耦合可插拔组件结构，比其他 MVC 框架更具扩展性和灵活性。
就是 spring 的 web 模块：
![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647311135961-56a65262-428a-4d87-a229-f63a2dcf845b.png)

# 常用组件
**DispatcherServlet**：前端控制器
**Controller**：处理器/页面控制器，做的是MVC中的C的事情，但控制逻辑转移到前端控制器了，用于对请求进行处理
**HandlerMapping**：请求映射到处理器，找谁来处理，如果映射成功返回一个HandlerExecutiongChain对象（包含一个Handler处理器(页面控制器)对象、多个**HandlerInterceptor**拦截器对象）
**ViewResolver** : 视图解析器，找谁来处理返回的页面。把逻辑视图解析为具体的View,进行这种策略模式，很容易更换其他视图技术；
如InternalResourceViewResolver将逻辑视图名映射为JSP视图
**LocalResolver**：本地化、国际化
**MultipartResolver**：文件上传解析器
**HandlerExceptionResolver**：异常处理器

# 第一个 SpringMVC 程序
## 导包
```java
spring-aop-4.0.0.RELEASE.jar
spring-beans-4.0.0.RELEASE.jar
spring-context-4.0.0.RELEASE.jar
spring-core-4.0.0.RELEASE.jar
spring-expression-4.0.0.RELEASE.jar
commons-logging-1.1.3.jar

spring-web-4.0.0.RELEASE.jar
spring-webmvc-4.0.0.RELEASE.jar
```
## 在 web.xml 中配置 DispatcherServlet
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
          http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--配置 DispatcherServlet-->
    <servlet>
        <servlet-name>MyDispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--
            指定配置文件
            不指定的话会默认在 /WEB-INF/ 下找 [servlet-name]-servlet.xml 文件，如果没有就会报错
        -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <!--
            load-on-startup 元素标记容器应该在web应用程序启动的时候就加载这个servlet
            它的值必须是一个整数，表示servlet被加载的先后顺序
            值越小，优先级越高，没有设置或为负数表示用到的时候再加载
        -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <!--
            指定 MyDispatcherServlet 处理那些请求
                *.do      *.action ： 这种表示以 .xxx 结尾的请求
                / ：表示所有请求，但是不包含 .jsp
                /* ：表示包含 .jsp 的所有请求
        -->
        <servlet-name>MyDispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```
## 编写 springMVC 的配置文件
在上面配置的对应位置，创建对应的文件
只需要添加注解扫描即可
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--组件扫描-->
    <context:component-scan base-package="com.zym"/>

</beans>
```
## 编写控制层代码
通过 @Controller 注解声明这是一个控制器
@RequestMapping("/hello") 指定方法的请求路径，对应路径的请求就会交给这个方法处理
返回值 String 是页面的地址，springMVC 会将对应路径下的页面返回给浏览器
```java
@Controller
public class Hello {

    @RequestMapping("/hello")
    public String hello(){
        return "/WEB-INF/success.jsp";
    }
}
```
## 编写需要的页面
success.jsp 页面代码如下
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>请求成功</h1>
</body>
</html>
```
## 配置 tomcat
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647312869576-3baeca1a-4538-4ce7-b688-f15038ca6cc7.png)
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647312900177-c7632b8c-96b0-4d44-9a2e-3821da999237.png)

## 测试
启动项目，发起 `http://localhost:8080/SpringMVC_war_exploded/hello`请求，返回指定的 success.jsp 页面
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647313013835-25da7ee8-ee51-4fb9-b098-cd27c6cf9116.png)

## 简单的视图解析
在上面的项目中，我们返回页面时，需要指定到页面的路径名 `return "/WEB-INF/success.jsp";`大多数页面其实都放到这个路径，后缀也一样，只是文件名称不同，每次都写这么多也很繁琐
我们就可以配置视图解析器，帮助我们处理那些公共的前缀和后缀
视图解析器在 springMVC.xml 中配置，如下：
```xml
<!--
    视图解析器：
        prefix ： 表示请求前面部分（路径）
        suffix ： 表示请求的后面部分（文件后缀）
-->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```
配置视图解析器后我们就可以只使用文件名来指定需要返回的文件了：
```java
@Controller
public class Hello {

    @RequestMapping("/hello")
    public String hello(){
        return "success";
    }
}
```
注意：`/WEB-INF/`配置成 `/WEB-IN` 是不对滴
我们还可以通过 <mvc:view-controller/> 标签来指定请求不经过控制器方法，直接转发到哪个页面。
例如：
path 属性表示请求的 url
view-name 即为对应的页面，视图解析器也会生效
配置 <mvc:view-controller/> 会导致其他的琴请求失效，这时加上 <mvc:annotation-driven/> （注解驱动，后面讲）标签会解决这个问题
```xml
<mvc:view-controller path="/test" view-name="list"/>
<mvc:annotation-driven/>
```
# springMVC 请求的流程
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647316161344-5d69fd22-ff40-472d-ae72-2ef8cb44c1a3.png)
用户发起请求到前端控制器（DispatcherServlet)

前端控制器调用处理器映射器（HandlerMapping）：，得到处理器执行链（找到与请求对应的 controller 方法）

前端控制器通过处理器执行链找到处理器适配器，并调用，处理器适配器执行完执行链拦截器方法后，会直接调用控制器方法（controller），然后将 ModelAndView（视图和数据）返回给前端控制器

前端控制器得到 ModelAndView 后遍历所有的视图解析器，找到与之对应的视图解析器

前端控制器调用视图解析器，得到视图对象

前端控制器调用视图渲染方法渲染视图对象

中央调度器对请求进行响应

浏览器接收到响应
# @RequestMapping 请求映射
@RequestMapping 注解指定控制器可以处理那些 url 请求，它可以在控制器中方法定义时使用，也可以在控制器类定义时使用
**标记在方法上（必须有）**：指定请求中的 URL 映射信息
**标记在类上**：指定当前控制器所有请求的公共前缀
作用：DispatcherServlet 截获请求后，就通过控制器上 @RequestMapping 提供的映射信息确定请求所对应的处理方法。 
## 可选属性示例
注意：params 和 headers 属性设置参数时，等号两边不要写空格
```java
@Controller
public class Hello {

    /**
     * @RequestMapping 的属性
     *      value：指定请求路径
     *      method：指定请求方式（GET/POST/PUT/DELETE......）
     *      params：指定请求参数，多个参数可以使用逗号分隔
     *          "a=b" 表示一定要有参数 a，并且它的值为 b
     *          "a!=b" 表示一定要有参数 a，并且它的值一定不能为 b
     *      headers：指定请求头信息
     *          使用方式与 params 相同
     */
    @RequestMapping(value = "/hello",
            method = RequestMethod.GET,
            params = {"username=yyb","id!=123"},
            headers = {"Host=localhost:8080"})
    public String hello(String username){
        return "success";
    }
}
```
## @RequestMapping 的模糊匹配功能
@RequestMapping 设置请求 URL 时，可以使用模糊匹配功能，模糊匹配通配符如下：
？：能替代任意一个字符
*：能替代任意多个字符，和一层路径
**：能替代多层路径
注意：不管注意匹配，请求路径中不要带 `%`和 `#`
示例：
```java
/**
 * 请求示例：hello0? 表示最后一位可以是任何字符，只能是一位
 *      http://127.0.0.1:8080/SpringMVC_war_exploded/hello02
 *      http://127.0.0.1:8080/SpringMVC_war_exploded/hello0a
 */
@RequestMapping("/hello0?")
public String hello02(){
    return "success";
}

/**
 * 请求示例：hello0* 表示 hello0 后面可以跟上任意个任意字符，可以是多位
 *      http://127.0.0.1:8080/SpringMVC_war_exploded/hello0asdiuhiasd
 * 将 * 写在两个 / 之间，还可以匹配一层任意路径，例如下面：
 *      请求地址可以为：http://127.0.0.1:8080/SpringMVC_war_exploded/ugyjkhkjhoug/hello
 *                     http://127.0.0.1:8080/SpringMVC_war_exploded/aslhj&*&/hello
 */
@RequestMapping("/*/hello")
public String hello03(){
    return "success";
}

/**
 * 请求示例：如下
 *      表示在hello前可以有多层任意路径
 *      http://127.0.0.1:8080/SpringMVC_war_exploded/aaa/bbbbb/cccccc/hello
 *      http://127.0.0.1:8080/SpringMVC_war_exploded/sajhdi/215)(*&%5E/$@/hello
 */
@RequestMapping("/**/hello")
public String hello04(){
    return "success";
}
```
## 请求占位符 和 @PathVariable 注解
在 @RequestMapping 的请求路径上，可以使用占位符 ：{变量名}
然后使用 @PathVariable 注解获取对应位置的值
@PathVariable 不写参数默认是按照控制方法参数列表中对应的参数名称获取数据
```java
/**
 * @PathVariable(value = "username",required = false)
 *      value 需要绑定的变量名
 */
@RequestMapping("/hello/{username}")
public String hello05(@PathVariable(value = "username") String username){
    return "success";
}
```
# 字符编码过滤器
在我们参数传递时，如果传递中文信息，是很有可能发生乱码现象的，我们可以通过在 web.xml 中设置字符编码过滤器来解决这一问题。如下：
乱码的情况有分为请求乱码和转发乱码，我们在字符编码过滤器中配置好编码格式后，可以通过 forceRequestEncoding 和 forceResponseEncoding 两个参数对其进行强制
```xml
<filter>
    <filter-name>encodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceRequestEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>encodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```
# REST 请求风格
REST：即 Representational State Transfer。**（资源）表现层状态转化。是目前最流行的一种互联网软件架构**。它结构清晰、符合标准、易于理解、扩展方便，所以正得到越来越多网站的采用
**资源（Resources）**：网络上的一个实体，或者说是网络上的一个具体信息。一段文字，一首歌......
**表现层（Representation）**：把资源具体呈现出来的形式，叫做它的表现层（Representation）。比如，文本可以用 txt 格式表现，也可以用 HTML 格式、XML 格式、JSON 格式表现，甚至可以采用二进制格式。
**状态转化（State Transfer）**：每发出一个请求，就代表了客户端和服务器的一次交互过程。HTTP协议是一个无状态协议，即所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”（State Transfer）。而这种转化是建立在表现层之上的，所以就是“表现层状态转化”。

通俗来说，就是我们以前只接触 GET、POST 两种请求方式，现在，我们使用 POST、DELETE、PUT、GET 四种请求方式对应增、删、改、查四种操作

这样，我们请求时，就可以使用一个路径，根据请求方式的不同，分别请求不同的控制器方法，在不同的方法中进行不同的操作：
/book/01		GET：查询一号图书
/book/01		POST：添加一号图书
/book/01		PUT：增加一号图书
/book/01		DELETE：删除一号图书
## 控制层方法示例
```java
@Controller
public class RESTful {

    @RequestMapping(value = "/book/{id}", method = RequestMethod.GET)
    public String getBook(@PathVariable String id){
        System.out.println("查询图书......"+id);
        return "success";
    }

    @RequestMapping(value = "/book/{id}", method = RequestMethod.POST)
    public String addBook(@PathVariable String id){
        System.out.println("添加图书......"+id);
        return "success";
    }

    @RequestMapping(value = "/book/{id}", method = RequestMethod.PUT)
    public String putBook(@PathVariable String id){
        System.out.println("修改图书......"+id);
        return "success";
    }


    @RequestMapping(value = "/book/{id}", method = RequestMethod.DELETE)
    public String deleteBook(@PathVariable String id){
        System.out.println("删除图书......"+id);
        return "success";
    }
}
```
## 配置 HiddenHttpMethodFilter
如果需要 REST 风格的请求生效，还需要配置 REST 风格的过滤器 HiddenHttpMethodFilter 
```xml
    <filter>
        <filter-name>hiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>hiddenHttpMethodFilter</filter-name>
        <!--表示过滤所有请求-->
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```
## 编写前端页面发起请求
除了 GET 请求方式可以直接在浏览器地址栏中直接发起请求外，其他的请求方式都需要通过表单指定请求方式，向后台发起请求
由于前端页面只支持 GET、POST 两种请求方式，所以我们在使用表单提交请求 PUT 和 DELETE 方式的控制器方法时，需要先将请求方式设置成 POST ，然后添加参数 _method，用来指定请求方式
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>

<a href="book/01">获取图书</a>
<p/>
<form action="book/01" method="post">
    <input type="submit" value="添加图书"/>
</form>

<form action="book/01" method="post">
    <!--hidden 表示隐藏此标签-->
    <input name="_method" value="PUT" hidden>
    <input type="submit" value="修改图书"/>
</form>

<form action="book/01" method="post">
    <input name="_method" value="DELETE" hidden>
    <input type="submit" value="删除图书"/>
</form>
</body>
</html>
```
## 测试：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647328482663-2b446b80-231b-4188-a992-a0e56142e048.png)
注意：通过什么方式发起的请求，就以什么样的方式转发，7 以上版本的 tomcat 不支持 put 和 delete 请求方式，转发请求成功页面时就会报错，如下：需要在 jsp 页面中添加 `isErrorPage="true"`解决
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647328689790-b358b0dc-38ac-4279-afac-0a1211d4d98b.png)
解决方式：
`<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>`

# 请求参数接收
## 直接使用形参接收参数（逐个接收）
会根据传递进来的参数，按照名称直接接收
```java
@Controller
public class parameterAcquisition {

    @RequestMapping("/parameter")
    public String parameter(String id){
        return "success";
    }
}
```
## 使用注解接收参数：
@RequestParam 注解
@RequestHeader 注解
@CookieValue 注解
这些注解都有三个属性：
value：参数名
required：是否必须，默认为 true, 表示请求参数中必须包含对应的参数，若不存在，将抛出异常
   ![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647329771820-2674d8c3-d0fe-44c4-9121-b23d5a522733.png)
   设置为 false ，则请求参数中不一定要绑定对应的参数，没有就赋值 null
defaultValue: 默认值，当没有传递参数时使用该值
在 SpringMVC 中，接收请求的参数有很多，只要在请求时携带了参数，就可以在形参列表中通过这些注解获取到值，如下：

```java
@Controller
public class parameterAcquisition {

    @RequestMapping(value = "/annotation",method = RequestMethod.GET)
    public String  annotation(@RequestParam(value = "username",required = true) String username,
                              @RequestHeader(value = "userAgent",required = false,defaultValue = "他没传进来") String userAgent,
                              @CookieValue(value = "JSESSIONID",required = true) String cookieId){
        System.out.println("获取到参数 username:"+username);
        System.out.println("获取到参数 userAgent:"+userAgent);
        System.out.println("获取到参数 cookieId:"+cookieId);
        return "success";
    }
}
```
测试：`http://localhost:8080/SpringMVC_war_exploded/annotation?username=yyb`
![image.png](https://cdn.nlark.com/yuque/0/2022/png/25916449/1647330283082-0d602723-8634-40f4-9db7-cb199ef915b9.png#clientId=ua08a0d94-1e9a-4&crop=0&crop=0&crop=1&crop=1&from=paste&height=173&id=u016c5601&margin=%5Bobject%20Object%5D&name=image.png&originHeight=173&originWidth=578&originalType=binary&ratio=1&rotation=0&showTitle=false&size=11445&status=done&style=none&taskId=u46f445de-c628-4733-b04c-7ea6365912b&title=&width=578)
## 使用对象接收参数
创建实体类
```java
public class Book {

    private String name;
    private String price;
    private String author;

    @Override
    public String toString() {
        return "Book{" +
                "name='" + name + '\'' +
                ", price='" + price + '\'' +
                ", author='" + author + '\'' +
                '}';
    }

    public String getName() {return name;}

    public void setName(String name) {this.name = name;}

    public String getPrice() {return price;}

    public void setPrice(String price) {this.price = price;}

    public String getAuthor() {return author;}

    public void setAuthor(String author) {this.author = author;}
}
```
控制层方法形参中直接使用 Book 对象接收
```java
@Controller
public class parameterAcquisition {

    @RequestMapping("/receivingBook")
    public String receivingBook(Book book){
        System.out.println(book);
        return "success";
    }
}
```
测试：
`http://localhost:8080/SpringMVC_war_exploded/receivingBook?name=西游记&price=88&author=我是你爹`
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647331865212-a4befef1-7392-4a51-8e83-9204608eae33.png)

## 使用Servlet原生API作为参数
MVC 的 Handler 方法可以接受哪些 ServletAPI 类型的参数
		**HttpServletRequest**
		**HttpServletResponse**
		**HttpSession**
		java.security.Principal
		Locale
		InputStream
		OutputStream
		Reader
		Writer
注意：使用 Servley 原生 API 需要 Servlet 支持，springMVC 可能没有支持 Servlet，需要导入 Servlet 依赖

```xml
<!--servlet-->
<dependency>
  <groupId>javax.servlet</groupId>
  <artifactId>javax.servlet-api</artifactId>
  <version>4.0.0-b03</version>
  <scope>provided</scope>
</dependency>
```
控制器方法：
```java
@Controller
public class parameterAcquisition {

    @RequestMapping("/servletAPI")
    public String servletAPI(HttpServletRequest request, HttpServletResponse response, HttpSession session){
        request.setAttribute("a","a");
        response.setHeader("b","b");
        session.setAttribute("c","c");
        return "success";
    }
}
```
前端接收参数：
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<h1>请求成功</h1>
a = ${requestScope.get("a")}<br/>
b = <%=response.getHeader("b")%><br/>
c = <%=session.getAttribute("c")%>
</body>
</html>
```
发起请求：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647333405671-24912a5e-f025-4ebc-9cc7-68a0f9cf621a.png)

# 响应数据传出
## SpringMVC 输出模型数据概述
提供了以下几种途径输出模型数据：

- **ModelAndView**：处理方法返回值类型为 ModelAndView 时, 方法体即可通过该对象添加模型数据
- **Map、ModelMap 及 Model**：入参为 org.springframework.ui.Model、org.springframework.ui.ModelMap 或 java.uti.Map 时，处理方法返回时，Map 中的数据会自动添加到模型中。
- **@SessionAttributes**：将模型中的某个属性暂存到 HttpSession 中，以便多个请求之间可以共享这个属性
- **@ModelAttribute**：方法入参标注该注解后, 入参的对象就会放到数据模型中

ModelAndView、Map 、Model、ModelMap ，给这些参数里面保存的所有数据都会放在请求域中。可以在页面获取。
ModelAndView 既包含视图信息（页面地址）也包含模型数据（给页面带的数据），而且数据是放在请求域中
Map，Model，ModelMap：最终都是 BindingAwareModelMap 在工作，相当于给 BindingAwareModelMap 中保存的东西都会被放在请求域中，在这三个模型中，推荐使用 Map ，它便于框架移植
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647335957404-b30daa1f-d406-4b71-8213-6d66e9545169.png)

## 处理模型数据之ModelAndView
控制器处理方法的返回值如果为ModelAndView, 则其既包含视图信息，也包含模型数据信息。
```java
@Controller
public class returnValue {

    @RequestMapping("/modelAndView")
    public ModelAndView modelAndView(ModelAndView modelAndView){
        //设置视图
        modelAndView.setViewName("success");
        //传入数据
        modelAndView.addObject("name","yyb");
        return modelAndView;
    }
}
```
前端返回页面代码：
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
modelAndView 的数据：${name}
<h1>请求成功</h1>
</body>
</html>
```
测试：`http://localhost:8080/SpringMVC_war_exploded/modelAndView`
![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647334318206-792f2bf8-a7cb-4ad6-a650-bb5d217a6776.png)

## 处理模型数据之 Map、ModelMap 和 Model
Spring MVC 在内部使用了一个 org.springframework.ui.Model 接口存储模型数据的具体使用步骤：
**Spring MVC 在调用方法前会创建一个隐含的模型对象作为模型数据的存储容器**。
**如果方法的入参为 Map 或 Model 类型**，Spring MVC 会将隐含模型的引用传递给这些入参。
在方法体内，开发者可以通过这个入参对象访问到模型中的所有数据，也可以向模型中添加**新的属性数据**
实验代码：Model 和 Map的使用方式相同
控制器代码

```java
@RequestMapping("/model")
public String  model(Model model){
    //传入数据
    model.addAttribute("name","yyb");
    return "success";
}
```
```java
@RequestMapping("/model")
public String  model(Map map){
    //传入数据
    map.put("name","pink");
    return "success";
}
```
```java
@RequestMapping("/model")
public String  model(ModelMap map){
    //传入数据 addAttribute 和 put效果相同
    //map.addAttribute("name","pink");
    map.put("name","pink");

    return "success";
}
```
请求成功页面代码：
```html
<%@ page contentType="text/html;charset=UTF-8" language="java" isErrorPage="true" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
获取到的数据：${name}
<h1>请求成功</h1>
</body>
</html>
```
测试：`http://localhost:8080/SpringMVC_war_exploded/model`
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647335188662-75191ece-6938-478c-9807-12439923cc95.png)

## SessionAttributes 注解
**这个注解只能使用在类上面**
**若希望在多个请求之间共用某个模型属性数据**，则可以在控制器类上标注一个**@SessionAttributes**, Spring MVC 将在模型中对应的属性暂存到 HttpSession 中。
@SessionAttributes 除了可以通过**属性名**指定需要放到会话中的属性外，还可以通过模型属性的**对象类型**指定哪些模型属性需要放到会话中
使用方式：
@SessionAttributes(types=User.class) 		//会将隐含模型中所有类型为 User.class 的属性添加到会话中。
@SessionAttributes(value={"user1", "user2"})		//会将隐含模型中所有名称为 user1、user2 的属性添加到会话中。
@SessionAttributes(types={User.class, Dept.class})
@SessionAttributes(value={"user1", "user2"}, types={Dept.class}) 
```java
//将名称为 name 的数据放进session中备
@SessionAttributes(value = {"name"})
@Controller
public class returnValue {

    @RequestMapping("/modelAndView")
    public ModelAndView modelAndView(ModelAndView modelAndView){
        //设置视图
        modelAndView.setViewName("success");
        //传入数据
        modelAndView.addObject("name","yyb");
        return modelAndView;
    }
}
```
在页面上通过 session 取值：`<%=session.getAttribute("name")%>`
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647339170771-1042da0f-2b7d-4179-a0be-6b80b7f7af96.png)

## ModelAttribute 注解
引用场景：
当我们需要向数据库中修改某条数据的某些字段，例如
我们需要修改用户信息的密码字段，前端修改密码后只会传递密码到后台，后台调用方法将数据全字段修改，前端没传进来的数据就获取到 null，如果全字段修改的话就会将所有其他值设置成 null

这时，我们可以先查询一下数据库，获取到当前用户的信息，然后将前端传递进的数据存入到这个对象中，这时使用这个进行全字段修改就不会有问题

@ModelAttribute 使用在方法上，该方法就会在控制方法调用前调用，我们就可以使用该方法先查询出原始数据

然后在控制方法的形参中使用该注解获取到 @ModelAttribute 方法传入的 Book 对象接收，从前端传入的参数就会对应的修改之前的 Book 对象中的内容。
示例：
```java
@Controller
public class returnValue {

    /**
     * 这里的 map 就是我们 BindingAwareModelMap，都是同一个东西
     * 所以，我们也可以在此通过 map 取到 book 的值
     * 并且这里的 book 与 getBook 方法传入的 book 内存地址相同
    */
    @RequestMapping("/testModelAttribute")
    public String testModelAttribute(@ModelAttribute("book") Book book,Map map){
        System.out.println(book);	//Book{name='西游记', price='99', author='吴承恩'}
        return "success";
    }

    @ModelAttribute
    public void getBook(Map map){
        //从数据库获取对象模拟获取
        Book book = new Book();
        book.setName("西游记");
        book.setAuthor("吴承恩");
        book.setPrice("88");
        map.put("book",book);
        System.out.println(book);	//Book{name='西游记', price='88', author='吴承恩'}
    }
}
```
测试：`http://localhost:8080/SpringMVC_war_exploded/testModelAttribute?price=99`
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647424775035-5f86cc1f-eb24-4f21-8f67-d5c3f184e518.png)

## 由 SessionAttributes 引发的异常
注意：如果在处理类定义处标注了@SessionAttributes(“xxx”)
    ModelAttribute  就会尝试从会话中获取该属性，并将其赋给该入参，然后再用请求消息填充该入参象。
    如果在会话中找不到对应的属性，则抛出 HttpSessionRequiredException 异常
# 请求转发与重定向
一般情况下，控制器方法返回字符串类型的值会被当成逻辑视图名处理
如果返回的字符串中带**forward: 或 redirect: **前缀时，SpringMVC 会对他们进行特殊处理：
将 forward: 和redirect: 当成指示符，其后的字符串作为 URL 来处理
redirect:success.jsp：会完成一个到 success.jsp 的重定向的操作
forward:success.jsp：会完成一个到 success.jsp 的转发操作
## 转发
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647506181617-83cffc0a-2a17-4a83-8515-2c74c18febcc.png)
## 重定向
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647506196397-22642956-a754-438a-90f1-e8517c0e7b09.png)
# 视图解析
在 SpringMVC 中，不管控制器方法返回 String、ModelAndView 还是 View，都会封装成 ModelAndView 进行处理。
视图解析器会将 ModelAndView 解析成对应的视图对象 View ，进过渲染后返回给 浏览器。
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647569203966-33ed8014-9372-487e-bd6c-1d52bc391099.png)

## 两个重要接口
ViewResolver（重要实现类 InternalResourceViewResolver）视图解析器和 View（重要实现InternalResourceView） 视图
## 视图和视图解析器
请求方法（控制器方法）执行完成后，我们会返回 String、View、ModelAndView 其中的一个，用来找到我们的页面，处理器适配器在得到这些类型后会将它们全部转为 ModelAndView 对象。它包含了页面地址和模型中的数据。
视图解析器（ViewResolver）回将这些 ModelAndView 进行解析，得到最终的视图对象（View），最终的视图可以是 JSP ，也可能是 Excel、JFreeChart等各种表现形式的视图
对于最终究竟采取何种视图对象对模型数据进行渲染，处理器并不关心，处理器工作重点聚焦在生产模型数据的工作上，从而实现 MVC 的充分解耦
## 视图
视图的作用是渲染模型数据，将模型里的数据以某种形式呈现给客户。
为了实现视图模型和具体实现技术的解耦，Spring 在 org.springframework.web.servlet 包中定义了一个高度抽象的 View 接口：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647577160267-b12fe5b6-4c5c-43f6-9f64-5c4dbc409e75.png)
视图对象由视图解析器负责实例化。由于视图是无状态的，所以他们不会有线程安全的问题

## 常用的视图实现类
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647577229800-cab32b51-37fe-4e02-b1d1-1e3f65d27308.png)
## 视图解析器
SpringMVC 为逻辑视图名的解析提供了不同的策略，可以在 Spring WEB 上下文中配置一种或多种解析策略，并指定他们之间的先后顺序。每一种映射策略对应一个具体的视图解析器实现类。
视图解析器的作用比较单一：将逻辑视图解析为一个具体的视图（View）对象。
所有的视图解析器都必须实现 ViewResolver 接口：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647577405890-a4ab021d-f361-40ac-9755-7a95a94b4e9f.png)

## 常用的视图解析器实现类
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647577454032-85f8ecb4-8186-4b84-ab4e-84a70d0e9ed3.png)
我们可以选择一种，或者混用多种视图解析器，
每个视图解析器都实现了 Ordered 接口并开放出一个 order 属性，可以通过 order 属性指定解析器的优先顺序，order  越小优先级越高。
SpringMVC 会按视图解析器顺序的优先顺序对逻辑视图名进行解析，直到解析成功并返回视图对象，否则将抛出 ServletException 异常
InternalResourceViewResolver：
JSP 是最常见的视图技术，可以使用 InternalResourceViewResolve作为视图解析器： 

## JstlView
若项目中使用了 JSTL，则 SpringMVC 会自动把视图由 InternalResourceView 转为 JstlView  （断点调试，将JSTL的jar包增加到项目中，视图解析器会自动修改为 JstlView）
若使用 JSTL 的 fmt 标签则需要在 SpringMVC 的配置文件中配置国际化资源文件
## 自定义视图
### 实现视图接口，自定义视图解析规则
如果想使用自定义视图，就需要实现 SpringMVC 的接口，还需要将这个实现类交给 springMVC 容器管理
```java
@Component(value = "customView01")
public class CustomView01 implements View {

    /**
     * 自定义视图
     */
    @Override
    public void render(Map<String, ?> map, HttpServletRequest request, HttpServletResponse response) throws Exception {
        /**
         * 这里的 map 就是控制方法形参列表中的 map
         * 设置响应头信息
         *      text/html;      响应内容
         *      charset=UTF-8   编码格式
         */
        System.out.println(model.get("message"));
        response.setHeader("Content-Type","text/html;charset=UTF-8");
        response.getWriter().write("<h1>欢迎来到自定义视图</h1>");
    }

    /**
     * 设置请求头
     */
    @Override
    public String getContentType() {
        return "text/html;charset=UTF-8";
    }
}
```
### 配置视图解析器
```xml
<!--配置视图解析器，根据 Bean 的名称解析视图-->
<bean class="org.springframework.web.servlet.view.BeanNameViewResolver">
    <!--配置优先级，数值越低优先级越高，如果可以，会优先使用其对视图解析-->
    <property name="order" value="100"/>
</bean>
```
### 控制器方法
返回的视图名称必须和自定义视图在容器中的名称一样，才能按名称交给自定义视图进行解析
```java
@RequestMapping("/customViewTest")
public String customViewTest(Map<String,Object> map){
    map.put("message","success");
    return "customView01";
}
```
# SpringMVC 对数据的操作
页面提交的数据往往都是 String 类型的，我们使用对象进行接收的时候，SpringMVC 需要完成：
数据类型的转换
数据格式的格式化问题
数据的校验：
看数据是否合法，例如出生日期：
我们要看提交的数据是不是一个日期
这个日期是否还在未来
WebDataBinder：数据绑定器负责数据绑定工作
数据绑定期间产生的类型转换、格式化、数据校验等问题
ConversionService组件：负责数据类型的转换以及格式化功能；
ConversionService中有非常多的**converter；**
不同类型的转换和格式化用它自己的**converter**
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647586042234-7b41fd0d-34d0-4571-9ab3-2cbc826766df.png)
validators：负责数据校验工作
bindingResult：负责保存以及解析数据绑定期间数据校验产生的错误

## 数据绑定原理：
Spring MVC 主框架将 ServletRequest  对象及目标方法的入参实例传递给 WebDataBinderFactory 实例，以创建**DataBinder **实例对象
DataBinder 调用装配在 Spring MVC 上下文中的**ConversionService**组件进行**数据类型转换、数据格式化**工作。将 Servlet 中的请求信息填充到入参对象中
调用**Validator**组件对已经绑定了请求消息的入参对象进行数据合法性校验，并最终生成数据绑定结果**BindingData**对象
Spring MVC 抽取**BindingResult**中的入参对象和校验错误对象，将它们赋给处理方法的响应入参

Spring MVC 通过反射机制对目标处理方法进行解析，将请求消息绑定到处理方法的入参中。数据绑定的核心部件是**DataBinder**，运行机制如下：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647584057776-15021188-c818-4db0-87a5-527a561ff6cd.png)

## 数据转换
在实体类的 set 方法加上断点进行调试，可以看到：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647587020346-881f0ccc-046d-40bb-afee-23f992a63241.png)

## 自定义类型转换器
转换器就是  ConversionService 接口中的 Converter（转换器）进行工作
我们的类型转换器就是 Converter，我们得将 Converter 配置进 ConversionService  中
还要将 WebDataBinder 中的 ConversionService 设置成我们这个加了自定义类型转换器的 ConversionService

步骤：
实现 Converter 接口，写一个自定义类型转换器
```java

/**
 * 字符串转 Employee 对象
 */
@Component("customTypeConverterString_Date")
public class CustomTypeConverterString_Date implements Converter<String, Employee> {

    @Resource
    DepartmentDao departmentDao;

    @Override
    public Employee convert(String source) {
        System.out.println("ok");
        Employee employee = null;
        //将请求参数按 - 拆分，传入 employee 的构造方法中创建对象
        if (source != null && source != "") {
            String[] split = source.split("-");
            employee = new Employee(null,
                    split[0],
                    split[1],
                    Integer.valueOf(split[2]),
                    departmentDao.getDepartment(Integer.valueOf(split[3])));
        }
        System.out.println(employee);
        //返回 employee 对象，就是我们控制器方法形参中的那个
        return employee;
    }

    @Override
    public <U> Converter<String, U> andThen(Converter<? super Employee, ? extends U> after) {
        return null;
    }
}

```
声明类型转换器：
```xml
<bean id="a" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters" ref="customTypeConverterString_Date"/>
</bean>
```
开启注解驱动：在 conversion-service 属性中指定我们自定义的 ConversionServiceFactoryBean
他会将我们自定义的转换器注册到 springMVC 的上下文中
```xml
<mvc:annotation-driven conversion-service="a"/>
```
控制器方法：
```java
@RequestMapping(value = "/addEmployees",method = RequestMethod.POST)
public String addEmployees(@RequestParam Employee employee){
    System.out.println(employee);
    employeeDao.save(employee);
    return "redirect:/listEmployee";
}
```
测试：
JSP 页面
```html
<form action="<%=contextPath%>/addEmployees" method="post">
    使用自定义的数据绑定器添加员工，各个参数使用 '-' 分开<br/>
    <input type="text" name="employee">
    <input type="submit" value="自定义提交">
</form>
```
提交名为 employee 的参数：李四-123@qq.com-0-102
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647590430947-f9336e61-8454-4539-9b6d-7beae8ad4ebe.png)
结果：添加成功
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647590468197-47c4103d-77a5-4315-89a2-ba7f86a0a011.png)

### <mvc:annotation-driven /> 的作用
<mvc:annotation-driven /> 会自动注册三个bean：
**RequestMappingHandlerMapping**
**RequestMappingHandlerAdapter**
**ExceptionHandlerExceptionResolver**  

还将提供以下支持：
	支持使用 **ConversionService**实例对表单参数进行类型转换
	支持使用 **@NumberFormat、@DateTimeFormat **注解完成数据类型的格式化
	支持使用 **@Valid **注解对 JavaBean 实例进行JSR 303 验证
	支持使用 **@RequestBody **和 **@ResponseBody **注解

## 数据格式化
如果后台无法将页面提交的数据格式转换成我们需要的格式，就会报错 404
例如：
	表单提交上来时，往往格式很不统一，对于日期类型的数据来说，通常有 2022-01-12、2022.01.12 、2022/01/12（默认）等很多种格式，怎样转换成我们 java 中的 Date 数据类型呢？

### 日期格式化
**@DateTimeFormat 注解可对 java.util.Date、java.util.Calendar、java.long.Long 时间类型进行标注**：
	**pattern 属性**：类型为字符串。指定解析/格式化字段数据的模式，如：”**yyyy-MM-dd hh:mm:ss**”
	iso 属性：类型为 DateTimeFormat.ISO。指定解析/格式化字段数据的ISO模式，包括四种：ISO.NONE（不使用） -- 默认、	ISO.DATE(yyyy-MM-dd) 、ISO.TIME(hh:mm:ss.SSSZ)、  ISO.DATE_TIME(yyyy-MM-dd hh:mm:ss.SSSZ)
	style 属性：字符串类型。通过样式指定日期时间的格式，由两位字符组成，第一位表示日期的格式，第二位表示时间的格式：S：短日期/时间格式、M：中日期/时间格式、L：长日期/时间格式、F：完整日期/时间格式、-：忽略日期或时间格式

在我们实体类中的出生日期属性上添加 @DateTimeFormat 可以解决问题"
pattern 属性指定我们提交的格式，默认是 `2022/02/12` 改成使用 `-`分隔，此时需要传入 `2022-02-12`
```java
public class Employee {

	private Integer id;
	private String lastName;

	@DateTimeFormat(pattern = "yyyy-MM-DD")
	private Date birthday;
```
同时，需要添加注解驱动，这里的注解驱动不能有 conversion-service 属性，否者不会生效
```xml
<mvc:annotation-driven/>
```
测试：
添加数据
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647592469052-70f16cd4-941e-470b-8128-9c39cc7a5ad1.png)![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647592487362-a6041912-eaef-4bfe-a9de-5a192c7559c0.png)

### 数字格式化
**@NumberFormat 可对类似数字类型的属性进行标注**，它拥有两个互斥的属性：
style：类型为 NumberFormat.Style。用于指定样式类型
包括三种：**Style.NUMBER**（正常数字类型）、**Style.CURRENCY**（货币类型）、**Style.PERCENT**（百分数类型）
**pattern**：类型为 String，自定义样式，如pattern="**#,###**"；

```java
public class Employee {

	private Integer id;
	private String lastName;

	@DateTimeFormat(pattern = "yyyy-MM-DD")
	private Date birthday;
    
    @NumberFormat(pattern = "#,###.####")
	private Integer wage;
```
### 如果类型转换失败，我们可以如何获取错误信息
在 controller 中获取参数时，我们可以在后面紧跟着添加一个 BindingResult 参数，通过 BindingResult 得到错误信息。
**注意：BindingResult 个目标参数之间一定不要写其他参数**

```java
@RequestMapping(value = "/Employee", method = RequestMethod.PUT)
public String updateEmployee(@ModelAttribute Employee employee,BindingResult bindingResult, Map<String,Object> map){
    System.out.println("aaa");
    if (bindingResult.getErrorCount() > 0){
        S
            ystem.out.println("类型转换异常...");
        List<FieldError> fieldErrors = bindingResult.getFieldErrors();
        for (FieldError fieldError : fieldErrors) {
            System.out.println(fieldError.getField()+"出现了错误，"+fieldError.getDefaultMessage());
        }
    }else {
        employeeDao.save(employee);
    }
    return "redirect:/listEmployee";
}
```
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647595540212-99a01d63-4cd7-4b44-904f-22a29219891d.png)
## 数据校验
只做前端校验是不安全的
在重要数据一定要加上后端验证
可以写程序将我们每一个数据取出进行校验，如果失败直接来到添加页面，提示其重新填写：
SpringMVC：可以JSR303来做数据校验
     		JDBC：规范---实现（各个厂商的驱动包）
     		JSR303：规范-----Hibernate Validator（第三方校验框架）
如何快速的进行后端校验？
添加 hibernate-validator 的 jar 包
```xml
<dependency>
  <groupId>org.hibernate.validator</groupId>
  <artifactId>hibernate-validator</artifactId>
  <version>6.0.16.Final</version>
</dependency>
```
配置校验器:
如果在校验注解上定义错误信息，可以不用配置错误信息文件
```xml
<!-- 
    校验器
    初始化LocalValidatorFactoryBean 
-->
<bean id="validator" class="org.springframework.validation.beanvalidation.LocalValidatorFactoryBean">
    <!-- 指定校验器提供方的 -->
    <property name="providerClass" value="org.hibernate.validator.HibernateValidator" />
    <!-- 效验错误使用的资源文件，在文件中配置错误信息。不指定默认是classpath下的ValidationMessageSource.properties -->
    <!-- 也就是发现错误返回到页面的错误信息， -->
    <property name="validationMessageSource" ref="messageSource"></property>
</bean>

<!-- 效验错误信息配置 -->
<bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
    <!-- basenames是ReloadableResourceBundleMessageSource中的一个属性：要求指定资源位置，这里是指定错误资源位置 -->
    <property name="basenames">
        <list>
            <value>classpath:errorMessage</value>
        </list>
    </property>
    <!-- 资源文件编码格式 -->
    <!-- 设置默认编码格式为UTF-8 -->
    <property name="defaultEncoding" value="utf-8"></property>
</bean>

<mvc:annotation-driven validator="validator"/>
```
控制器方法：
使用 @Valid 注解表名此参数需要进行数据验证
跟数据格式化相同，在需要验证的参数后紧跟 BindingResult 获取错误信息
获取到错误信息后可以将信息封装到 map 中，传给前端进行回显
```java
@RequestMapping(value = "/Employee", method = RequestMethod.PUT)
public String updateEmployee(@Valid @ModelAttribute Employee employee, BindingResult bindingResult, Map<String,Object> map){
    System.out.println("aaa");
    if (bindingResult.getErrorCount() > 0){
        List<FieldError> fieldErrors = bindingResult.getFieldErrors();
        for (FieldError fieldError : fieldErrors) {
            map.put(fieldError.getField(),fieldError.getDefaultMessage());
            System.out.println(fieldError.getField()+"出现了错误，"+fieldError.getDefaultMessage());
        }
        Collection<Department> departments = departmentDao.getDepartments();
        map.put("departments",departments);
        return "add";
    }else {
        employeeDao.save(employee);
    }
    return "redirect:/listEmployee";
}
```
测试：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647663823472-948a45a7-ae71-4b06-a6a7-ffa998e9e880.png)

# 返回 JSON 数据
原生 javaWeb 中返回 JSON 格式的数据，需要先在 Servlet 中将数据转成 JSON 格式，然后返回给请求，在前端还需要将数据转成 js 对象才能使用
我们在 SpringMVC 中，可以在 控制方法上添加 **@ResponseBody  **注解，他会将我们的返回值转换成 JSON 对应的对象或集合。
**@ResponseBody  **注解使用在参数上，就需要客户端传入对应的 JSON 格式字符串

使用此注解需要导入依赖，springmvc 会使用这些依赖将数据转换成 JSON 格式：
```xml
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-core</artifactId>
  <version>2.12.1</version>
</dependency>
<dependency>
  <groupId>com.fasterxml.jackson.core</groupId>
  <artifactId>jackson-databind</artifactId>
  <version>2.12.1</version>
</dependency>
```
控制层方法：
```java
@ResponseBody
@RequestMapping(value = "/addEmployees",method = RequestMethod.GET)
public String  addEmployees(String  employee){
    System.out.println(employee);
    return employee;
}
```
# 拦截器
SpringMVC提供了拦截器机制；允许运行目标方法之前进行一些拦截工作，或者目标方法运行之后进行一些其他处理；
使用拦截器需要先创建一个拦截器类，继承 HandlerInterceptor 接口，并实现接口中的三个方法：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647665591526-5c3e6a5a-8456-48f4-8ffe-09b624e3fb37.png)
preHandle：在目标方法运行之前调用，返回值是 boolean 类型
return true：(chain.doFilter()) 放行； 
return false：不放行，请求直接中断，但是，已经方形的拦截器，会执行完 afterCompletion
postHandle：在目标方法运行之后调用：目标方法调用之后
afterCompletion：在请求整个完成之，chain.doFilter()放行，资源响应之，来到目标页面之后调用
一般用来释放资源
使用步骤：
编写拦截器类：

```java
@Component
public class MyInterceptor implements HandlerInterceptor {
    /**
     * preHandle 预先处理请求的方法
     * 参数：
     *      Object ：被拦截的控制器对象
     *      返回值： boolean
     *              true：请求正确，可以被controller处理
     *                      ========MyInterceptor的preHandle方法==========
     *                      执行了dosome方法
     *                      ========MyInterceptor的postHandle方法==========
     *                      ========MyInterceptor的afterCompletion方法==========
     *              false：请求不能被处理，只有拦截器的preHandle方法被执行，控制器方法不会被执行，请求到此截止
     *                      ========MyInterceptor的preHandle方法==========
     *  特点：
     *      1、执行时间是在控制器方法执行之前先执行
     *      2、可以对请求做处理，登录的检查，权限的判断，统计数据等等
     *      3、界定请求是否执行
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("========MyInterceptor的preHandle方法==========");
        //提示
//        request.getRequestDispatcher("tisi.jsp").forward(request,response);
        return false;
    }

    /**
     * postHandle 后处理方法
     * 参数：
     *      Object :被拦截的控制器对象
     *      ModelAndView ：控制器方法的返回值
     * 特点：
     *      1、在控制器方法执行之后执行
     *      2、可以获得控制器方法的返货结果，可以修改原来的执行结果
     *          修改数据，修改视图
     *      3、可以对请求做出二次处理
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("========MyInterceptor的postHandle方法==========");
        //对请求做二次处理
        if (modelAndView!=null){
            //xxxxxxxxxxx
        }
    }

    /**
     * afterCompletion ：最后执行的方法
     * 参数：
     *      Onject ：被拦截的控制器对象
     *      Exception ：异常对象
     * 特点：
     *      1、在请求处理完成后执行
     *          请求处理完成的标志是视图处理完成，对视图执行forward
     *      2、可以做程序最后要做的工作，释放内存，清理临时变量
     *      3、该方法执行的条件：
     *              当前拦截器的preHandle（）方法必须执行
     *              preHandle方法必须返回true
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("========MyInterceptor的afterCompletion方法==========");
    }
}
```
配置拦截器：
```xml
<!--声明拦截器-->
<mvc:interceptors>
    <!-- 一个interceptor表示一个拦截器-->
    <mvc:interceptor>
        <!--需要拦截的请求-->
        <mvc:mapping path="/*"/>
        <!--使用哪个自定义拦截器-->
        <bean class="com.zym.interceptor.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```
测试：`http://localhost:8080/test_war_exploded/test01`
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647666956247-8e13721e-ce64-41e1-aa35-dae121c71de4.png)

## 多个拦截器的执行顺序
在配置文件中，先配置的先执行，其 preHandle 方法
postHandle 和 afterCompletion 的执行顺序与 preHandle 相反，先配置的后执行
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647667043022-cd1703fb-1fca-41c9-84af-37ae5bf1e76f.png)

## 为什么要多个拦截器
我们要将验证功能放到多个拦截器中处理，每个拦截器进行不同的验证
# 异常处理
Spring MVC 通过 HandlerExceptionResolver  处理程序的异常，包括 Handler 映射、数据绑定以及目标方法执行时发生的异常。
SpringMVC 提供的 HandlerExceptionResolver 的实现类 
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647667751949-080565e1-6ee7-4b3b-8f51-684162b67b71.png)

## @ExceptionHandler 定义异常处理方法
在控制器中增加处理异常的方法，当请求出现异常，会进入异常处理的方法，可以在异常方法中对异常进行处理
```java
@Controller
public class hello {

    @RequestMapping("/test01")
    public String test01(){
        System.out.println("控制器方法执行");
        int a = 1/0;
        return "/index.jsp";
    }

    @ExceptionHandler({Exception.class})
    public String ecxeption01(Exception e){
        System.out.println("有异常");
        return "/index.jsp";
    }
}
```
## 自定义公共异常类 @ControllerAdvice
我们可以自定义一个异常类，将所有异常的处理方法都放到这个类中
```java
@ControllerAdvice
public class MyErrore {

    @ExceptionHandler({Exception.class})
    public String ecxeption01(Exception e){
        System.out.println("有异常");
        return "/index.jsp";
    }
}
```
# springMVC 的运行流程
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647669595173-02fc3db3-4341-45a6-b54a-7e6d4164d823.png)
# 静态资源解析
两种方式：
第一种，直接将所有无法处理的请求交给 tomcat 处理，需要在 springMVC 的配置文件中进行如下配置
```xml
<!--添加tomcat的处理器-->
<mvc:default-servlet-handler/>
<!--开启注解驱动-->
<mvc:annotation-driven/>
```
第二种，在 springmvc 的配置文件中配置静态资源
mapping 是请求 url
location 是静态资源的地址
```xml
<mvc:resources mapping="/static/**" location="/static/"/>
```
# 文件上传
Spring MVC 为文件上传提供了直接的支持，这种支持是通过即插即用的 MultipartResolver 实现的。 
Spring 用 Jakarta Commons FileUpload 技术实现了一个 MultipartResolver 实现类：CommonsMultipartResovler  
Spring MVC 上下文中默认没有装配 MultipartResovler，因此默认情况下不能处理文件的上传工作，如果想使用 Spring 的文件上传功能，需现在上下文中配置 MultipartResolver

## 导入依赖
```xml
    <dependency>
      <groupId>commons-io</groupId>
      <artifactId>commons-io</artifactId>
      <version>2.11.0</version>
    </dependency>

    <dependency>
      <groupId>commons-fileupload</groupId>
      <artifactId>commons-fileupload</artifactId>
      <version>1.4</version>
    </dependency>
```
## 配置 MultipartResolver
defaultEncoding: 必须和用户 JSP 的 pageEncoding 属性一致，以便正确解析表单的内容
为了让 CommonsMultipartResovler 正确工作，必须先将 Jakarta Commons FileUpload 及 Jakarta Commons io 的类包添加到类路径下。
文件上传解析器 id 必须为 multipartResolver
```xml
<mvc:annotation-driven/>
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="defaultEncoding" value="UTF-8"/>
</bean>
```
## 编写控制器代码
```java
@RequestMapping(value = "/upload",method = RequestMethod.POST)
@ResponseBody
public String  upload(HttpServletRequest request, @RequestParam("file") MultipartFile multipartFile) throws IOException {
    if (!multipartFile.isEmpty()){
        System.out.println("文件上传");
        multipartFile.transferTo(new File("d:/"+multipartFile.getOriginalFilename()));
        System.out.println("文件上传成功。。。");
    }
    return "success";
}
```
## 编写前端页面发起请求
```html
<form action="upload" method="post" enctype="multipart/form-data">
    <input type="file" name="file">
    <input type="submit" value="上传">
</form>
```
## 测试：
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647769472027-18d84a70-8cc9-4d99-b5d0-877aacf2f081.png)
## 多个文件上传，使用文件数组接收参数集合
```java
@RequestMapping(value = "/upload",method = RequestMethod.POST)
@ResponseBody
public String  upload(HttpServletRequest request, @RequestParam("file") MultipartFile... multipartFile) throws IOException {
```
# SpringMVC 与 Spring 的整合
**注意：spring 和 springMVC 整合时有一个父子容器的概念**
父容器就是 ioc 容器，启动 toncat 就会被 web.xml 中的那个监听器监听到，立马就会创建 ioc 容器，里面放的是service，dao，以及其他的各种bean；
子容器是 springmvc 的容器，里里面放着 web 层的所有组件，处理器映射器，处理器适配器，视图解析器， controller 等；
父容器会先创建（tomcat启动时），子容器后创建，父容器的引用会保存在子容器中，可以通过子容器获取父容器的实例，然后获取其中的 bean。
spring 和 springmvc 整合时如果将 Controller 层的注解扫描器配置到了 spring 的配置文件中，将出现无法获取到对应请求路径。
如果将其它层的注解扫描配置到了 springmvc 的配置文件中，那么一些 spring 中一些功能将会失效，例如：事务
## 整合步骤
导包，pom.xml 中配置最基础的依赖
```xml
<dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-aspects</artifactId>
      <version>5.3.6</version>
    </dependency>
    <dependency>
      <groupId>org.aspectj</groupId>
      <artifactId>com.springsource.org.aspectj.weaver</artifactId>
      <version>1.6.4.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.3.6</version>
    </dependency>
    <!--servlet-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>4.0.0-b03</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>
```
创建 spring 和 springMVC 的配置文件
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647670118471-cad7fcff-cb1d-4b26-ae13-ff45f9c4c1e1.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.zym">
        <context:exclude-filter type="aspectj" expression="com.zym.controller"/>
    </context:component-scan>
</beans>
```
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.zym.controller"/>
</beans>
```
在 web.xml 中通过 监听器创建 spring 的容器，通过 servlet 配置前端控制器
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
          http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
  
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring-mvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```
编写测试类：
```java

@Controller
public class ControllerTest {

    @Resource
    testService testService;

    @ResponseBody
    @RequestMapping("/test01")
    public void testController(){
        testService.test01();
    }
}
```
```java
@Service
public class testService {

    public void test01(){
        System.out.println("这里是 Service 方法......");
    }
}
```
测试：
发起请求：`http://localhost:8080/spring_springmvc_war_exploded/test01`
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647766668265-cda21618-3dd1-4849-97d3-13290a004589.png)
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1647766688789-82eddfa5-c733-4eb2-b14f-db8d43ccddf7.png)
