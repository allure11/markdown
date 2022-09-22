# MyBatis 

官方文档很详细：[https://mybatis.org/mybatis-3/zh/getting-started.html](https://mybatis.org/mybatis-3/zh/getting-started.html)

逆向工程：[http://mybatis.org/generator/quickstart.html](http://mybatis.org/generator/quickstart.html)

## Mybatis 的核心

### 核心组件
```java
SqlSessionFactoryBuilder
专门用来构建 sql 会话工厂（SqlSessionFactory）

SqlSessionFactory
SQL会话工厂，用来创建 sql 会话（SqlSession），是线程安全的，它一旦被创建，在整个应用的执行期间都会存在

SqlSession
SQL 会话，mybatis 最主要的组件，其中包含了很多的方法，执行sql、数据回滚、提交、事务、获取映射驱动对象等等

Mapper
主要通过调用 java 接口中的方法来执行与其绑定的 sql
```
### 核心对象
```java
SqlSession 对象
该对象包含了执行SQL语句的所有方法

Executor 接口
他根据 SqlSession 传递的参数，动态的生成需要执行的 sql 语句， 同时负责查询缓存的维护

MappedStatement对象
对映射 SQL 的封装，用于存储要映射的 sql 的 id、参数等信息

ResultHandler 对象
用于对返回结果进行处理
```
### 工作原理及核心流程，如下图：
	先加载全局配置文件，用于获取数据库信息<br />	根据全局配置文件中的配置加载 Mapper 映射文件，该文件中配置了操作数据库的 SQL 语句<br />	通过 MyBatis 环境配置信息构建会话工厂（SqlSessionFactory）<br />	Executor 执行器通过 SqlSession 传递的参数，动态的生成 sql 语句，同时负责缓存的维护<br />	MappedStatement 在 Excutor 接口的执行方法中有一个 MappedStatement 类型的参数，他是对映射信息的封装，同于存储 sql 的 id，参数等信息。他会在 SQL 执行前完成输入参数的映射，并在 SQL 执行后，完成输出结果的映射<br />

 ![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1648520042844-709291d0-a355-4339-a362-bb743da60f1e.png)

## SSM整合：

新建 javaWeb 项目

创建数据库连接的属性配置文件：jdbc.properties

```xml
mysql.userName=root
mysql.password=root
mysql.url=jdbc:mysql://192.168.83.134/bookstore_0519
mysql.driverClass=com.mysql.cj.jdbc.Driver
```
创建 spring 、springMVC 、mybatis 的配置文件

spring：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
<!--    组件扫描-->
    <context:component-scan base-package="com.zym">
        <context:exclude-filter type="aspectj" expression="com.zym.controller"/>
    </context:component-scan>
<!--    外部属性配置文件-->
    <context:property-placeholder location="jdbc.properties" />
<!--    数据源-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${mysql.driverClass}"/>
        <property name="url" value="${mysql.url}"/>
        <property name="username" value="${mysql.userName}"/>
        <property name="password" value="${mysql.password}"/>
    </bean>
<!--    mybatis的配置-->
    <bean id="sessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="mybatisConfig.xml"/>
    </bean>
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="sqlSessionFactoryBeanName" value="sessionFactoryBean"/>
        <property name="basePackage" value="com.zym.dao"/>
    </bean>
</beans>
```
springMVC：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--    组件扫描-->
    <context:component-scan base-package="com.zym.controller"/>

</beans>
```
mybatis:
```xml
<?xml version="1.0" encoding="UTF-8" ?>

<!--约束文件的说明-->
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <!--日志设置-->
    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>
</configuration>
```
编写 web.xml 文件
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
          http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
<!--    字符过滤器-->
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

<!--    加载spring容器的监听器-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
<!--    springMVC 前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContextMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```
编写实体类
```java
package com.zym.pojo;

public class Users {
    private Integer id;
    private String username;
    private String password;
    private String email;
    private Integer balance;

    public Users(){}

    public Users(Integer id, String username, String password, String email, Integer balance) {
        this.id = id;
        this.username = username;
        this.password = password;
        this.email = email;
        this.balance = balance;
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return username;
    }

    public void setName(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Integer getBalance() {
        return balance;
    }

    public void setBalance(Integer balance) {
        this.balance = balance;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", email='" + email + '\'' +
                ", balance=" + balance +
                '}';
    }
}

```
编写 dao 层文件：dao 接口
```java
public interface UserDao {

    /**
     * 获取所有的用户信息
     * @return
     */
    List<Users> getAll();

    Users getUserById(Integer id);
}
```
mapper.xml 映射文件
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.zym.dao.UserDao">
    <!--使用SQL语句的标签编写SQL语句-->
    <select id="getAll" resultType="com.zym.pojo.Users">
        select * from bs_user
    </select>

    <select id="getUserById" resultType="com.zym.pojo.Users">
        select * from bs_user where id = #{id}
    </select>

</mapper>
```
编写 service 层文件：service 接口
```java
public interface UserService {

    List<Users> getAll();

    Users getUserById(Integer id);
}
```
service 实现类
```java
@Service
public class UserServiceImpl implements UserService {

    @Resource
    UserDao userDao;

    @Override
    public List<Users> getAll() {
        return userDao.getAll();
    }

    @Override
    public Users getUserById(Integer id) {
        return userDao.getUserById(id);
    }
}
```
mybatis 日志：添加对应的日志依赖，以 log4j 为例

```xml
    <!--mybatis 日志-->
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-core</artifactId>
      <version>2.17.2</version>
    </dependency>
    <dependency>
      <groupId>log4j</groupId>
      <artifactId>log4j</artifactId>
      <version>1.2.17</version>
    </dependency>
```
添加日志配置文件：log4j2.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration xmlns="http://logging.apache.org/log4j/2.0/config">

    <Appenders>
        <Console name="stdout" target="SYSTEM_OUT">
            <PatternLayout pattern="%5level [%t] - %msg%n"/>
        </Console>
    </Appenders>

    <Loggers>
        <Logger name="com.zym.dao" level="trace"/>
        <Root level="error" >
            <AppenderRef ref="stdout"/>
        </Root>
    </Loggers>

</Configuration>
```
编写测试类
```java
@ContextConfiguration("classpath:applicationContext.xml")
@RunWith(SpringJUnit4ClassRunner.class)
public class UserTest {

    @Resource
    UserService userService;

    @Test
    public void testGetAll(){
        List<Users> all = userService.getAll();
        all.forEach( user -> System.out.println(user));
    }

    @Test
    public void testGetUserById(){
        Users userById = userService.getUserById(1);
        System.out.println(userById);
    }
}
```
## 对象中放集合（map中放list）

```java
Map<String,List<Map<String, String>>> getUserOrders(int uid);
```

```xml
<!--此时返回值是 Map<String,Map<String,List<String, Map<String,String>>>>-->
<resultMap id="userOrdersMap" type="java.util.Map">
    <id column="id" property="id"/>
    <result column="username" property="username"/>
    <result column="password" property="password"/>
    <result column="money" property="money"/>
    <association property="orders" column="id" javaType="list" select="selectOrders"/>
</resultMap>

<select id="getUserOrders" resultMap="userOrdersMap" >
    select *
    from user1
    where id = #{userId}
</select>

<resultMap id="OrdersMap" type="Map">
    <id property="id" column="id" javaType="int" jdbcType="INTEGER"/>
    <result property="userId" column="user_id"/>
    <result property="info" column="info"/>
    <result property="address" column="address"/>
    <result property="price" column="price"/>
</resultMap>

<select id="selectOrders" resultMap="OrdersMap">
    select *
    from order2
    where user_id = #{id}
</select>
```
![image.png](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/1651155405171-289d1557-0b2d-4c7d-9c2a-4c44987e7976.png)
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class UserAndOrders {

    private Integer id;
    private String username;
    private String password;
    private Integer money;
    private List<Order1> orders;
}
```
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Order1 {

    private Integer id;
    private Integer userId;
    private String info;
    private String address;
    private Integer price;

}
```

# Mybatis-plus

MyBatis-Plus（简称 MP）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

官网：http://mp.baomidou.com/

参考教程：https://baomidou.com/pages/24112f/

## 测试

### 创建 SpringBoot 项目，引入依赖：

```xml
<!--mybatis-plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.1</version>
</dependency>
<!--mysql-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

### 对数据库进行相关配置：

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://121.37.217.252:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC
    username: root
    password: 123456
```

### 创建实体类：

```java
package com.zym.entity;

import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;

@TableName("user")
public class User {

    @TableId
    private Long id;
    private String name;
    private Integer age;
    private String email;

    ···
}

```

### 实现 dao 接口：

```java
package com.zym.dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.zym.entity.User;

public interface UserDao extends BaseMapper<User> {

}
```

### 配置 mybatis-plus 日志

```yml
#mybatis日志
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

### 编写测试方法

```java
@SpringBootTest
class MybatisplusApplicationTests {
    @Resource
    private UserDao userDao;
    @Test
    public void userDaoTest() {
        userDao.selectList(null).forEach(System.out::println);
    }
}
```

### 运行结果

```
JDBC Connection [HikariProxyConnection@542641486 wrapping com.mysql.cj.jdbc.ConnectionImpl@21bd128b] will not be managed by Spring
==>  Preparing: SELECT id,name,age,email FROM user
==> Parameters: 
<==    Columns: id, name, age, email
<==        Row: 1, Jone, 18, test1@baomidou.com
<==        Row: 2, Jack, 20, test2@baomidou.com
<==        Row: 3, Tom, 28, test3@baomidou.com
<==        Row: 4, Sandy, 21, test4@baomidou.com
<==        Row: 5, Billie, 24, test5@baomidou.com
<==      Total: 5
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@45aca496]
User{id=1, name='Jone', age=18, email='test1@baomidou.com'}
User{id=2, name='Jack', age=20, email='test2@baomidou.com'}
User{id=3, name='Tom', age=28, email='test3@baomidou.com'}
User{id=4, name='Sandy', age=21, email='test4@baomidou.com'}
User{id=5, name='Billie', age=24, email='test5@baomidou.com'}
```

## Insert

### 测试代码：

```java
@Test
public void insertTest() {
    User user = new User();
    user.setName("zym");
    user.setAge(18);
    user.setEmail("zym@qq.com");
    int insert = userDao.insert(user);
    System.out.println(insert);
}
```

### 结果：

```
JDBC Connection [HikariProxyConnection@1474529475 wrapping com.mysql.cj.jdbc.ConnectionImpl@20580d4e] will not be managed by Spring
==>  Preparing: INSERT INTO user ( id, name, age, email ) VALUES ( ?, ?, ?, ? )
==> Parameters: 1528611777477124097(Long), zym(String), 18(Integer), zym@qq.com(String)
<==    Updates: 1
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@30839e44]
1
```

### 问题：

在我们插入数据时，没有指定主键 id 的值，但是成功插入了数据。

mybatis-plus 在插入数据时，帮我们设置了 id 值。

mybatis-plus 的主键策略：我们可以在实体类中的 id 实现上配置主键自增策略

```java
@TableId(type = IdType.ASSIGN_ID)
private Long id;
```

| 值                | 描述                                                         |
| :---------------- | :----------------------------------------------------------- |
| AUTO              | 数据库 ID 自增，需要在创建表是时候添加主键自增               |
| NONE              | 无状态，该类型为未设置主键类型（注解里等于跟随全局，全局里约等于 INPUT） |
| INPUT             | insert 前自行 set 主键值                                     |
| ASSIGN_ID         | 分配 ID(主键类型为 Number(Long 和 Integer)或 String)(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextId`(默认实现类为`DefaultIdentifierGenerator`雪花算法) |
| ASSIGN_UUID       | 分配 UUID,主键类型为 String(since 3.3.0),使用接口`IdentifierGenerator`的方法`nextUUID`(默认 default 方法) |
| ~~ID_WORKER~~     | 分布式全局唯一 ID 长整型类型(please use `ASSIGN_ID`)         |
| ~~UUID~~          | 32 位 UUID 字符串(please use `ASSIGN_UUID`)                  |
| ~~ID_WORKER_STR~~ | 分布式全局唯一 ID 字符串类型(please use `ASSIGN_ID`)         |

要想影响所有实体的配置，可以设置全局主键配置

```yml
mybatis-plus:
  global-config:
    db-config:
      id-type: assign_id
```

## update

注意：update 时生成的 sql 自动是动态 sql：UPDATE user SET age=? WHERE id=?

```java
@Test
public void updateTest() {
    User user = new User();
    user.setId(1L);
    user.setName("zym");
    int i = userDao.updateById(user);
    System.out.println(i);
}
```

## 自动填充

例如填充年龄

在实体类属性上添加注解：

```java
@TableField(fill = FieldFill.INSERT)	// insert 操作时填充
private Integer age;
@TableField(fill = FieldFill.INSERT_UPDATE)		// update 操作时填充
private String email;
```

实现源对象处理接口：

> 注意：实现类必须被 spring 容器管理，可以添加 @Component 等注解实现

```java
@Component
public class MyMateObjectHandller implements MetaObjectHandler {
    /**
     * 插入元对象字段填充（用于插入时对公共字段的填充）
     * 在执行 insert 操作时，会自动为添加了 @TableField(fill = FieldFill.INSERT) 的字段填充数据 18
     * @param metaObject 元对象
     */
    @Override
    public void insertFill(MetaObject metaObject) {
        this.setFieldValByName("age", 18, metaObject);
    }

    /**
     * 更新元对象字段填充（用于更新时对公共字段的填充）
     * 在执行 update 操作时，会自动为添加了 @TableField(fill = FieldFill.INSERT) 的字段填充数据 81
     * @param metaObject 元对象
     */
    @Override
    public void updateFill(MetaObject metaObject) {
        this.setFieldValByName("email", "1@1.com", metaObject);
    }
}
```

## 乐观锁

主要适用场景：当要更新一条记录的时候，希望这条记录没有被别人更新，也就是说实现线程安全的数据更新

乐观锁实现方式：

```
取出记录时，获取当前version
更新时，带上这个version
执行更新时， set version = newVersion where version = oldVersion
如果version不对，就更新失败
```

数据库中添加version字段

```sql
ALTER TABLE `user` ADD COLUMN `version` INT
```

实体类添加 version 字段，并添加 @Version 注解
```java
@Version
@TableField(fill = FieldFill.INSERT)
private Integer version;
```

元对象处理器接口添加version的insert默认值

```java
@Override
public void insertFill(MetaObject metaObject) {
    this.setFieldValByName("age", 18, metaObject);
    this.setFieldValByName("version", 1, metaObject);
}
```

<font color=red>**特别说明：**</font>

>支持的数据类型只有 int,Integer,long,Long,Date,Timestamp,LocalDateTime
>
>整数类型下 newVersion = oldVersion + 1
>
>newVersion 会回写到 entity 中
>
>仅支持 updateById(id) 与 update(entity, wrapper) 方法
>
><font color="red">在 update(entity, wrapper) 方法下, wrapper 不能复用!!!</font>

创建配置类 MybatisPlusConfig ，在 MybatisPlusConfig 中注册乐观锁插件

```java
@EnableTransactionManagement
@SpringBootConfiguration
@MapperScan("com.zym.dao")
public class MyBatisConfig {

    /**
    * 配置乐观锁插件
    */
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor() {
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
        mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
        return mybatisPlusInterceptor;
    }
}
```

测试：

​		修改成功后，数据更新的同时 version 也会跟着加一

​		修改失败所有数据不会有变化

```java
@Test
public void lockTest() {
    User user = userDao.selectById(1528626109904609282L);
    user.setName("123");
    /**
     * 模拟数据库被其他线程更改，修改后数据库中的version会比之前查出的数据大（这里减一后可达到同样的效果）
     */
    user.setVersion(user.getVersion()-1);
    userDao.updateById(user);
}
```

```java
// 修改失败
JDBC Connection [HikariProxyConnection@487369776 wrapping com.mysql.cj.jdbc.ConnectionImpl@2eda072] will not be managed by Spring
==>  Preparing: UPDATE user SET name=?, age=?, email=?, version=? WHERE id=? AND version=?
==> Parameters: 123(String), 18(Integer), 1@1.com(String), 2(Integer), 1528626109904609282(Long), 1(Integer)
<==    Updates: 0
```

## select

### 根据id查

```java
@Test
public void lockTest() {
    User user = userDao.selectById(1528626109904609282L);
    System.out.println(user);
}
```

### 多个 id 批量查询：使用集合

```java
@Test
public void lockTest() {
    ArrayList<Long> longs = new ArrayList<>();
    longs.add(1528611777477124097L);
    longs.add(1528622478144843778L);
    userDao.selectBatchIds(longs).forEach(System.out::println);
}
```

### 简单条件查询

注意：map 中的 key 对应的是数据库中的列名。例如数据库 user_id，实体类是 userId，这时 map 的 key 需要填写 user_id

```java
@Test
public void selectTest(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name", "xxy");
    map.put("id","1528622478144843778");
    userDao.selectByMap(map).forEach(System.out::println);
}
```

### 分页

配置分页插件

```java
@Bean
public MybatisPlusInterceptor mybatisPlusInterceptor() {
    MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
    mybatisPlusInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
    // 分页插件
    mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.H2));
    return mybatisPlusInterceptor;
}
```

测试

```java
@Test
public void userDaoTest() {
    Page<User> userPage = new Page<>(1, 2);
    userDao.selectPage(userPage, null).getRecords().forEach(System.out::println);
    System.out.println("一共多少数据："+userPage.getTotal());
    System.out.println("一共有几页："+userPage.getPages());
    System.out.println("当前第几页："+userPage.getCurrent());
    System.out.println("一页多少数据"+userPage.getSize());
    System.out.println("是否有下一页："+userPage.hasNext());
    System.out.println("是否有上一页："+userPage.hasPrevious());
}
```

## delete

### 根据 id 删除记录

```java
@Test
public void deteteTest(){
    int i = userDao.deleteById(1L);
    System.out.println(i);
}
```

### 批量删除

```java
@Test
public void deteteTest(){
    ArrayList<Long> longs = new ArrayList<>();
    longs.add(2L);
    longs.add(3L);
    int i = userDao.deleteBatchIds(longs);  
    System.out.println(i);
}
```

### 条件删除

```java
    @Test
    public void deteteTest(){
        HashMap<String, Object> map = new HashMap<>();
        map.put("name", "Jack");
        int i = userDao.deleteByMap(map);
        System.out.println(i);
    }
```

## 逻辑删除和物理删除

<font color=red>物理删除：</font>真实删除，将对应数据从数据库中删除，之后查询不到此条被删除数据

<font color=red>逻辑删除：</font>假删除，将对应数据中代表是否被删除字段状态修改为“被删除状态”，之后在数据库中仍旧能看到此条数据记录

数据库中添加 deleted字段

```sql
ALTER TABLE `user` ADD COLUMN `deleted` boolean
```

实体类添加deleted 字段，并加上 @TableLogic 注解 和 @TableField(fill = FieldFill.INSERT) 注解

```java
@TableLogic
@TableField(fill = FieldFill.INSERT)
public Integer deleted ;
```

元对象处理器接口添加 delete 的 insert 默认值：（可以没有）

```java
@Override
public void insertFill(MetaObject metaObject) {
    this.setFieldValByName("age", 18, metaObject);
    this.setFieldValByName("version", 1, metaObject);
    this.setFieldValByName("deleted ", 0, metaObject);
}
```

application.properties 加入配置

此为默认值，如果你的默认值和 mybatis-plus 的默认值一样，该配置可无

```yml
mybatis-plus:
  global-config:
    db-config:
      logic-delete-value:1
      logic-not-delete-value:0
```

测试：

```java
@Test
public void deteteTest(){
    HashMap<String, Object> map = new HashMap<>();
    map.put("name", "Jone");
    int i = userDao.deleteByMap(map);
    System.out.println(i);
}
```

发现 delete 语句变成了 update

```
JDBC Connection [HikariProxyConnection@1437977899 wrapping com.mysql.cj.jdbc.ConnectionImpl@40bb4f87] will not be managed by Spring
==>  Preparing: UPDATE user SET deleted=1 WHERE name = ? AND deleted=0
==> Parameters: Jone(String)
<==    Updates: 1
Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@6993c8df]
```

数据库 deleted 字段随之修改

![image-20220523163930822](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220523163930822.png)

**<font color=red>注意：</font>**被删除数据的 deleted 字段的值必须为未删除状态，才能被选取出来执行逻辑删除的操作，就是说 deleted 字段必须有初始值

测试逻辑删除后的查询：MyBatis Plus 中查询操作也会自动添加逻辑删除字段的判断

逻辑删除后的字段查询时不会出现

```java
@Test
public void selectTest(){
    userDao.selectList(null).forEach(System.out::println);
}
/*************************************** 结果为 **************************************/
User{id=3, name='Tom', age=28, email='test3@baomidou.com', version=1, deleted=0}
User{id=4, name='Sandy', age=21, email='test4@baomidou.com', version=1, deleted=0}
User{id=5, name='Billie', age=24, email='test5@baomidou.com', version=1, deleted=0}
```

## SQL 执行分析插件

相当于慢 sql 日志，不推荐生产环境使用

引入依赖：

```xml
<dependency>
    <groupId>p6spy</groupId>
    <artifactId>p6spy</artifactId>
    <version>3.9.1</version>
</dependency>
```

修改数据源中的驱动和数据库 url：

```yml
spring:
  datasource:
    driver-class-name:  com.p6spy.engine.spy.P6SpyDriver	# 照着写
    # url 需要在 jdbc 和 mysql 之间添加 p6spy
    url: jdbc:p6spy:mysql://121.37.217.252:3306/test?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=UTC
    username: root
    password: 123456
```

在 resourses 下创建 spy.properties 配置文件

```properties
#3.2.1以上使用
modulelist=com.baomidou.mybatisplus.extension.p6spy.MybatisPlusLogFactory,com.p6spy.engine.outage.P6OutageFactory
#3.2.1以下使用或者不配置
#modulelist=com.p6spy.engine.logging.P6LogFactory,com.p6spy.engine.outage.P6OutageFactory
# 自定义日志打印
logMessageFormat=com.baomidou.mybatisplus.extension.p6spy.P6SpyLogger
#日志输出到控制台
appender=com.baomidou.mybatisplus.extension.p6spy.StdoutLogger
# 使用日志系统记录 sql
#appender=com.p6spy.engine.spy.appender.Slf4JLogger
# 设置 p6spy driver 代理
deregisterdrivers=true
# 取消JDBC URL前缀
useprefix=true
# 配置记录 Log 例外,可去掉的结果集有error,info,batch,debug,statement,commit,rollback,result,resultset.
excludecategories=info,debug,result,commit,resultset
# 日期格式
dateformat=yyyy-MM-dd HH:mm:ss
# 实际驱动可多个
#driverlist=org.h2.Driver
# 是否开启慢SQL记录
outagedetection=true
# 慢SQL记录标准 2 秒
outagedetectioninterval=2
```

执行 SQL 语句，即可得到慢 SQL 日志：

```java
@Test
public void selectTest(){
    userDao.selectList(null).forEach(System.out::println);
}
```

![image-20220523170956029](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220523170956029.png)

## 使用条件构造器  Wapper 进行复杂操作

### wapper介绍

![image-20220523173042699](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220523173042699.png)

Wrapper ： 条件构造抽象类，最顶端父类
AbstractWrapper ： 用于查询条件封装，生成 sql 的 where 条件
QueryWrapper ： Entity 对象封装操作类，不是用 lambda 语法
UpdateWrapper ： Update 条件封装，用于 Entity 对象更新操作
AbstractLambdaWrapper ： Lambda 语法使用 Wrapper 统一处理解析 lambda 获取 column。
LambdaQueryWrapper ：看名称也能明白就是用于 Lambda 语法使用的查询 Wrapper
LambdaUpdateWrapper ： Lambda 更新封装 Wrapper

### 使用

更多使用方式参考：https://baomidou.com/pages/10c804/

```java
// eq 
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.eq("name","Billie");		//相当于 name = Billie
userDao.selectList(wrapper).forEach(System.out::println);

// ne ： 不等于 ！=
// gt ： 大于 >
// ge ： 大于等于 >=
// lt ： 小于 <
// le ： 小于等于 <=

QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.ne("name","Billie");		//相当于 name != Billie
userDao.selectList(wrapper).forEach(System.out::println);

// allEq
QueryWrapper<User> wrapper = new QueryWrapper<>();
HashMap<String, Object> map = new HashMap<>();
map.put("name","Sandy");
map.put("age",21);
wrapper.allEq(map);		// 传入一个 map，相当于 and 操作，name = Sandy and age = 21
userDao.selectList(wrapper).forEach(System.out::println);

// between ： 在某一个范围
// notBetween ： 不在某一个范围
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.between("age", 20, 30);		// 相当于 age BETWEEN 20 AND 30
userDao.selectList(wrapper).forEach(System.out::println);

// like ： 模糊查询，在这个范围，两边都是 %
// notLike ： 不在这个范围，两边都是 %
// likeLeft ： 左边匹配 %
// likeRight ： 右边匹配 %
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.like("name","T");		// 相当于 name LIKE '%T%'
userDao.selectList(wrapper).forEach(System.out::println);

// isNull ： 指定字段为 null
// isNotNull ： 指定字段不为 null
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.isNull("name");
userDao.selectList(wrapper).forEach(System.out::println);

// in ： 在几个值之中
// notIn ： 不在几个值之中
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.in("age",18,19,21);
userDao.selectList(wrapper).forEach(System.out::println);

// inSql ： 在某一个 SQL 中
// notInSql ：不某一个 SQL 中
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.inSql("age", "select age from user where id < 10");
userDao.selectList(wrapper).forEach(System.out::println);

// groupBy ： 分组
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.groupBy("id","name","age");
userDao.selectList(wrapper).forEach(System.out::println);

// orderByAsc ： 升序排序
// orderByDesc ： 降序排序
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.orderByAsc("id");
userDao.selectList(wrapper).forEach(System.out::println);

// orderBy ： 多个字段，不同的排序规则
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.orderBy(true,true,"id","name");
userDao.selectList(wrapper).forEach(System.out::println);

// having ： 分组之后操作，使用 sql 语句进行匹配
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.groupBy("id").having("id < 4");
userDao.selectList(wrapper).forEach(System.out::println);

// select ： 指定查询的列
QueryWrapper<User> wrapper = new QueryWrapper<>();
wrapper.select("id","name","age");
userDao.selectList(wrapper).forEach(System.out::println);
```

## 代码生成器

```java
package com.dong.generator;

import com.baomidou.mybatisplus.annotation.FieldFill;
import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.baomidou.mybatisplus.generator.FastAutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.OutputFile;
import com.baomidou.mybatisplus.generator.config.converts.MySqlTypeConvert;
import com.baomidou.mybatisplus.generator.config.po.LikeTable;
import com.baomidou.mybatisplus.generator.config.querys.MySqlQuery;
import com.baomidou.mybatisplus.generator.config.rules.DateType;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;
import com.baomidou.mybatisplus.generator.engine.FreemarkerTemplateEngine;
import com.baomidou.mybatisplus.generator.fill.Column;
import com.baomidou.mybatisplus.generator.keywords.MySqlKeyWordsHandler;

import java.util.Collections;

/**
 * @author Administrator
 */
public class CodeGenerator {

    /**
     * 数据源配置
     */
    private static final DataSourceConfig.Builder DATA_SOURCE_CONFIG = new DataSourceConfig
        .Builder("jdbc:mysql://localhost:3306/seconds?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai",
            "root", "root")
        //数据库查询
        .dbQuery(new MySqlQuery())
        //数据库schema(部分数据库适用)
        .schema("mybatis-plus")
        //数据库类型转换器
        .typeConvert(new MySqlTypeConvert())
        //数据库关键字处理器
        .keyWordsHandler(new MySqlKeyWordsHandler());


    /**
     * 执行 run
     */
    public static void main(String[] args) {
        /** 1、配置数据源 */
        FastAutoGenerator.create(DATA_SOURCE_CONFIG)
                /** 2、全局配置 */
                .globalConfig(builder -> builder
                        .author("dong") // 设置作者名
                        .outputDir(System.getProperty("user.dir") + "/src/main/java")   //设置输出路径：项目的 java 目录下
                        .commentDate("yyyy-MM-dd hh:mm:ss")   //注释日期
                        .dateType(DateType.ONLY_DATE)   //定义生成的实体类中日期的类型 TIME_PACK=LocalDateTime;ONLY_DATE=Date;
                        .fileOverride()   //覆盖之前的文件
                        //.enableSwagger()   //开启 swagger 模式
                        .disableOpenDir()   //禁止打开输出目录，默认打开
                        .build() //加入构建队列
                )
                /** 3、包配置 */
                .packageConfig(builder -> {
                    builder.parent("com.dong.seconds") // 设置父包名
                            .entity("entity")   //pojo 实体类包名
                            .service("service") //Service 包名
                            .serviceImpl("service.impl") // ***ServiceImpl 包名
                            .mapper("mapper")   //Mapper 包名
                            .xml("mapper.xml")  //Mapper XML 包名
                            .controller("controller") //Controller 包名
                            .other("utils") //自定义文件包名
                            .pathInfo(Collections.singletonMap(OutputFile.mapperXml, System.getProperty("user.dir")+"/src/main/resources/mapper"));    //配置 mapper.xml 路径信息：项目的 resources 目录下
                })
                /** 4、策略配置 */
                .strategyConfig(builder -> builder
                        .enableCapitalMode() // 开启大写命名，默认值:false
                        .enableSkipView() // 开启跳过视图，默认值:false
                        .disableSqlFilter() // 禁用 sql 过滤，默认值:true，语法不能支持使用 sql 过滤表的话，可以考虑关闭此开关
                        .likeTable(new LikeTable("t_user")) //模糊表匹配(sql 过滤)	likeTable 与 notLikeTable 只能配置一项
                        .addInclude("t_user") // 设置需要生成的数据表名
                        .addTablePrefix("t_") // 设置过滤表前缀
                        //.addFieldSuffix("_") // 增加过滤表后缀
                        //.addFieldPrefix("ul_") // 增加过滤字段前缀  本人不建议使用，去掉后缀，会导致驼峰命名实体类的变量名不带前缀，去掉后,错误：Username，正确:ulUsername
                        //.addFieldSuffix("_") // 增加过滤字段后缀
                        /**4.1、mapper策略配置 */
                        .mapperBuilder()
                        .superClass(BaseMapper.class)   // 设置父类
                        .formatMapperFileName("%sMapper")   // 格式化 mapper 文件名称
                        .enableMapperAnnotation() // 开启 @Mapper 注解
                        .formatXmlFileName("%sMapper") // 格式化 Xml 文件名称
                        .enableBaseResultMap() // 启用 BaseResultMap 生成，默认值:false
                        .enableBaseColumnList() // 启用 BaseColumnList，默认值:false
                        /** 4.2、service 策略配置 */
                        .serviceBuilder()
                        .formatServiceFileName("%sService") //格式化 service 接口文件名称，%s进行匹配表名，如 UserService
                        .formatServiceImplFileName("%sServiceImpl") //格式化 service 实现类文件名称，%s进行匹配表名，如 UserServiceImpl
                        /** 4.3、entity策略配置 */
                        .entityBuilder() // 实体策略配置
                        .enableLombok() // 开启 Lombok
                        .enableChainModel() // 开启链式模型，默认值:false
                        .enableRemoveIsPrefix() // 开启 Boolean 类型字段移除 is 前缀，默认值:false
                        .enableTableFieldAnnotation() // 开启生成实体时生成字段注解，默认值:false
                        .enableActiveRecord() // 开启 ActiveRecord 模型，默认值:false
                        .disableSerialVersionUID() //不实现 Serializable 接口，不生产 SerialVersionUID
                        //.logicDeleteColumnName("deleted")   //逻辑删除字段名
                        .naming(NamingStrategy.underline_to_camel)  //数据库表映射到实体的命名策略：下划线转驼峰命
                        .columnNaming(NamingStrategy.underline_to_camel)    //数据库表字段映射到实体的命名策略：下划线转驼峰命
                        .idType(IdType.AUTO) // 全局主键类型
                        .formatFileName("%sBean") // 格式化文件名称，生成实体的后缀，建议这样使用，生成后：UserBean
                        .addTableFills(
                                new Column("create_time", FieldFill.INSERT),
                                new Column("modify_time", FieldFill.INSERT_UPDATE)
                        )   //添加表字段填充，"create_time"字段自动填充为插入时间，"modify_time"字段自动填充为插入修改时间
                        .enableTableFieldAnnotation()       // 开启生成实体时生成字段注解
                        /** 4.4、controller策略配置 */
                        .controllerBuilder() // controller 策略配置
                        .enableHyphenStyle() // 开启驼峰转连字符，默认值:false
                        .formatFileName("%sController") // 格式化 Controller 类文件名称，%s进行匹配表名，如 UserController
                        .enableRestStyle()  // 开启生成 @RestController 控制器
                        .build() // 加入构建队列
                )
                /** 5、模板配置 */
                .templateConfig(builder -> builder
                        .disable()
                        .entity("/templates/entity.java")
                        .service("/templates/service.java")
                        .serviceImpl("/templates/serviceImpl.java")
                        .mapper("/templates/mapper.java")
                        .mapperXml("/templates/mapper.xml")
                        .controller("/templates/controller.java")
                        .build()
                )
                /** 6、模板引擎 */
                //Velocity
                //.templateEngine(new VelocityTemplateEngine())
                //Beetl
                //.templateEngine(new BeetlTemplateEngine())
                //Freemarker
                .templateEngine(new FreemarkerTemplateEngine())
                /** 7、执行 */
                .execute();
    }
}
```



























