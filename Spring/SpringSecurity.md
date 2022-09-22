# SpringSecurity

## 入门程序

将一个普通的 web 程序添加上 security 的依赖即可，项目结构如下

![image-20220727093338259](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220727093338259.png)

依赖如下

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
<!-- security 相关依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

此时，前端发起请求，会被 security 要求登录，此页面是 security 自带的

> 默认用户名是：user
>
> 默认密码需要在控制台中找：`Using generated security password: 27f5000a-f036-4d7b-a8fd-3f1f687cfa41`

![image-20220727093624649](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220727093624649.png)

登录成功后，会得到请求的数据

## 基本原理

security 的本质是一个由多个过滤器组成的过滤器链

### 核心过滤器

> FilterSecurityInterceptor ：一个方法级的权限过滤器，位于所有过滤器的最底部
>
> ExceptionTranslationFilter ：异常过滤器，用来处理在认证和授权过程中抛出的异常
>
> UsernamePasswordAuthenticationFilter ：对 /login 页面的 POST 请求做拦截，验证提交的用户名和密码

### 两个重要接口

> UserDetailsService ：用户登录查询用户名和密码的过程
>
> PasswordEncoder ：加密接口，主要完成对 User 对象中密码的加密操作

## 用户认证信息

### 通过编写配置文件设置用户名和密码

![image-20220727100814502](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220727100814502.png) 

### 通过编写配置类，在配置类中设置用户名和密码

可参考：https://developer.51cto.com/article/702252.html

> 注意：使用配置类，必须使用 PasswordEncoder 对密码进行加密

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        auth.inMemoryAuthentication()
                .withUser("user").password(bCryptPasswordEncoder.encode("123456")).roles("USER");
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

### 编写自定义实现类，从数据库中查询用户信息，完成验证

先实现 `UserDetailsService` 接口，在此类中完成用户的查询工作，将查询到的用户信息和其角色信息封装进 `org.springframework.security.core.userdetails.User`，并进行返回

> 注意：这里一般调用持久层方法，查询数据库得到用户信息

```java
@Service
public class MyUserDetailesService implements UserDetailsService {

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        // 权限列表
        List<GrantedAuthority> grantedAuthorities = AuthorityUtils.commaSeparatedStringToAuthorityList("ROLE_USER");
        return new User("user", new BCryptPasswordEncoder().encode("user"), grantedAuthorities);
    }
}
```

然后修改配置文件，指定 `userDetailsService` 的具体实现为我们刚刚写的 `MyUserDetailesService`

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private MyUserDetailesService myUserDetailesService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        auth.userDetailsService(myUserDetailesService).passwordEncoder(bCryptPasswordEncoder);
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

## 通过数据库查询用户完成认证

整合 mybatis-plus，创建实体类、Dao 接口、Mapper 文件等，实现数据库操作（也可以通过 jdbc 等其他工具操作数据库）

整合完成后，目录结构如下：

![idea64_BsFaSSEBfK](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/idea64_BsFaSSEBfK.png)

编写配置文件，配置数据库信息：

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://xuxingyu.xyz:3306/Test?useUnicode=true&characterEncoding=utf8&useSSL=false
spring.datasource.username=root
spring.datasource.password=zxc001
```

在上面自定义实现类的基础上，修改 `MyUserDetailesService`，通过他查询用户数据，并返回

> 注意区分 `org.springframework.security.core.userdetails.User` 和自己写的 `User` 实体类

```java
@Service
public class MyUserDetailesService implements UserDetailsService {

    private UserDao userDao;
    private RolesDao rolesDao;

    @Autowired
    public MyUserDetailesService(UserDao userDao, RolesDao rolesDao) {
        this.userDao = userDao;
        this.rolesDao = rolesDao;
    }

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        QueryWrapper<com.zym.pojo.User> userQueryWrapper = new QueryWrapper<>();
        userQueryWrapper.eq("name", username);
        com.zym.pojo.User user = userDao.selectOne(userQueryWrapper);
        if (user == null) {
            throw new UsernameNotFoundException("用户不存在");
        }
        // 权限列表
        HashSet<GrantedAuthority> authorities = getAuthorities(user.getId());
        return new User(user.getName(), new BCryptPasswordEncoder().encode(user.getPassword()), authorities);
    }

    /**
     * 查询权限列表
     * @param userId
     * @return
     */
    private HashSet<GrantedAuthority> getAuthorities(String userId) {
        List<Roles> roles = rolesDao.getRolesByUserId(userId);
        HashSet<GrantedAuthority> grantedAuthoritys = new HashSet<>();

        roles.stream().forEach(role -> {
            grantedAuthoritys.add(new SimpleGrantedAuthority(role.getName()));
        });

        return grantedAuthoritys;
    }
}
```

## 自定义登录页面

编写登录页面，在 `/resources/static/html` 下编写 `login.html`，文件内容如下

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/login" method="post">
    用户名：<input type="text" name="username"><br/>
    密码：<input type="password" name="password"><br/>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

配置静态资源：

```properties
# 配置静态资源访问前缀
spring.mvc.static-path-pattern=/static/**
# 配置静态资源访问路径
spring.web.resources.static-locations=classpath:/static/
```

修改 `security` 配置文件 `SecurityConfig`，重写 `configure(HttpSecurity http)` 方法，对自定义登录页面进行设置

```java
@Configuration
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Autowired
    private MyUserDetailesService myUserDetailesService;

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        BCryptPasswordEncoder bCryptPasswordEncoder = new BCryptPasswordEncoder();
        auth.userDetailsService(myUserDetailesService).passwordEncoder(bCryptPasswordEncoder);
    }

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.formLogin().loginPage("/static/html/login.html")	// 设置登录页面
                .loginProcessingUrl("/login")	// 登陆请求
                .successForwardUrl("/hello")	// 登录成功后回调地址
                .and().authorizeRequests()
                .antMatchers("/login","/static/html/login.html")	// security 不拦截的页面
                .permitAll()
                .anyRequest().authenticated().and().csrf().disable();	// 关闭csrf防护
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

## 基于角色或权限进行访问控制

访问权限的配置需要写到 `SecurityConfig` 配置类的 `configure(HttpSecurity http)` 方法中，如下

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.formLogin().loginPage("/static/html/login.html")   // 自定义登录页面
        .loginProcessingUrl("/login")   // 登录提交的url
        .successForwardUrl("/hello")    // 登陆成功跳转页面
        .and().authorizeRequests()
        .antMatchers("/login","/static/html/login.html").permitAll()    // 允许所有人不登录就可以访问的页面
        // .antMatchers("/admin").hasAuthority("admin")    // 只有admin权限才能访问admin路径
        // .antMatchers("/adminAndUser").hasAnyAuthority("admin","user")   // 有admin或user权限都能访问adminAndUser路径
        .antMatchers("/admin").hasRole("admin")    // 只有admin角色才能访问admin路径
        .antMatchers("/adminAndUser").hasAnyRole("admin","user")    // 有admin或user角色都能访问adminAndUser路径
        .anyRequest().authenticated().and().csrf().disable();
}
```

>  设置访问控制后，没有权限会报错 403

### 基于权限（Authority）

> hasAuthority：指定一个权限
>
> 当前请求具有指定的权限才能访问

```java
.antMatchers("/admin").hasAuthority("admin")    // 只有admin权限才能访问admin路径
```

> hasAnyAuthority：指定多个权限，使用 `,` 分隔
>
> 当前请求具有指定的任何一个权限都能访问

```java
.antMatchers("/adminAndUser").hasAnyAuthority("admin","user")
```

### 基于角色（Role）

> hasRole：指定一个角色
>
> 当前请求具有指定的角色才能访问

```java
.antMatchers("/admin").hasRole("admin")    // 只有admin角色才能访问admin路径
```

> hasAnyRole：指定多个角色，使用 `,` 分隔
>
> 当前请求具有指定的任何一个角色都能访问

```java
.antMatchers("/adminAndUser").hasAnyRole("admin","user")    // 有admin或user角色都能访问adminAndUser路径
```

**<font color=red>注意：基于角色的权限管理中，security 默认会为我们制定的角色加上 `ROLE_` 前缀。所以，我们在查询出角色后需要为其拼接上前缀才能正常访问</font>**

在 `MyUserDetailesService` 中为角色添加浅前缀：

```java
    /**
     * 查询权限列表
     * @param userId
     * @return
     */
    private HashSet<GrantedAuthority> getAuthorities(String userId) {
        List<Roles> roles = rolesDao.getRolesByUserId(userId);
        HashSet<GrantedAuthority> grantedAuthoritys = new HashSet<>();

        roles.stream().forEach(role -> {
            // 为角色添加 ROLE_ 前缀
            grantedAuthoritys.add(new SimpleGrantedAuthority("ROLE_"+role.getName()));
        });

        return grantedAuthoritys;
    }
```

## 配置自定义 403 页面

编写好 403 页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1 align="center">权限不足！！！</h1>
</body>
</html>
```

修改 `SecurityConfig` 配置类的 `configure(HttpSecurity http)` 方法，指定权限不足时跳转的页面

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.exceptionHandling().accessDeniedPage("/static/html/403.html");	// 权限不足跳转的页面
    http.formLogin().loginPage("/static/html/login.html")   // 自定义登录页面
        .loginProcessingUrl("/login")   // 登录提交的url
        .successForwardUrl("/hello")    // 登陆成功跳转页面
        .and().authorizeRequests()
        .antMatchers("/login","/static/html/login.html").permitAll()
        .antMatchers("/admin").hasRole("admin")    // 只有admin角色才能访问admin路径
        .anyRequest().authenticated().and().csrf().disable();
}
```

## 授权注解的使用

### @Secured

具有指定的权限才能访问，可以设置多个值，放在 `{}` 中，用逗号 `,` 分隔

> 注意：权限和角色需要与登录时设置的一致，不可以省略 `ROLE_`
>
> 需要在启动类或者配置类上添加启动注解 `@EnableGlobalMethodSecurity(securedEnabled = true)` 启用 `@Secured`

```java
@RequestMapping("/admin")
@Secured({"admin","ROLE_user"})
public String admin(){
    return "Admin";
}
```

### @PreAuthorize

在方法执行之前进行权限校验

> 注意：此注解可以使用基于配置类的四种方法，与配置类中作用相同
>
> 需要在启动类或者配置类上添加启动注解 `@EnableGlobalMethodSecurity(securedEnabled = true,prePostEnabled = true)` 启用 `@PreAuthorize`

```java
@PreAuthorize("hasAnyRole('admin')")
@RequestMapping("/adminAndUser")
public String adminAndUser(){
    return "adminAndUser";
}
```

### @PostAuthorize

在方法执行之后进行权限校验

> 注意：使用方法与上面 @PreAuthorize 一样

```java
@PostAuthorize("hasAnyRole('admin')")
@RequestMapping("/adminAndUser")
public String adminAndUser(){
    System.out.println("方法执行了。。。。。。");
    return "adminAndUser";
}
```

### @PostFilter

对返回值进行过滤

如下，返回值中添加了两个用户 `lisi` 和 `lisi1`，`@PostFilter` 对 `lisi` 进行过滤，返回的结果只有 `lisi`

![image-20220728104851181](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220728104851181.png)

```java
@PostFilter("filterObject.name == 'lisi'")
@RequestMapping("/tetsPostFilter")
public List<User> tetsPostFilter(){
    System.out.println("方法执行了。。。。。。");
    User user = new User();
    user.setName("lisi1");
    List<User> users = new ArrayList<>();
    users.add(user);
    
    User user1 = new User();
    user1.setName("lisi");
    users.add(user1);
    return users;
}
```

### @PreFilter

对请求参数进行过滤，使用方式与上面的 `@PostFilter` 相同，只不过是过滤请求参数

## 注销

可以在配置类中指定注销请求，注销后跳转的页面

```java
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.exceptionHandling().accessDeniedPage("/static/html/403.html");
        // 配置注销请求，和注销成功后的跳转页面
        http.logout().logoutUrl("/logout").logoutSuccessUrl("/static/html/login.html").permitAll();
        http.formLogin().loginPage("/static/html/login.html")   // 自定义登录页面
                .loginProcessingUrl("/login")   // 登录提交的url
                .successForwardUrl("/hello")    // 登陆成功跳转页面
                .and().authorizeRequests()
                .antMatchers("/login","/static/html/login.html").permitAll()
                .anyRequest().authenticated().and().csrf().disable();
    }

```

## 记住用户，xx 天内免登陆

<div align="center">
    <img style="border: 1px solid black;" src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220728105842199.png">
    <br/>
    <font>记住用户实现流程</font>
</div>

### 在配置类中注入数据源，配置操作的数据库对象

> 注意：`jdbcTokenRepository.setCreateTableOnStartup(true);` 语句会在启动项目时创建对应的数据库，如果不是第一次启动，数据库中有该表，重复创建会报错

```java
@Autowired
private DataSource dataSource;

/**
 * 配置操作的数据库对象
 */
@Bean
public PersistentTokenRepository persistentTokenRepository(){
    JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
    jdbcTokenRepository.setDataSource(dataSource);
    // 第一次自动创建数据库表
    jdbcTokenRepository.setCreateTableOnStartup(true);
    return jdbcTokenRepository;
}
```

### 在 `configure(HttpSecurity http)` 中配置自动登录

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
    http.exceptionHandling().accessDeniedPage("/static/html/403.html");
    http.logout().logoutUrl("/logout").logoutSuccessUrl("/static/html/login.html").permitAll();
    http.formLogin().loginPage("/static/html/login.html")   // 自定义登录页面
        .loginProcessingUrl("/login")   // 登录提交的url
        .successForwardUrl("/hello")    // 登陆成功跳转页面
		/***************添加此处代码***************/
        .and().rememberMe()
        .tokenRepository(persistentTokenRepository())   // 设置操作数据库的对象 PersistentTokenRepository
        .tokenValiditySeconds(60) // 记住我的时间
        .userDetailsService(myUserDetailesService)
		/****************************************/
        .and().authorizeRequests()
        .antMatchers("/login","/static/html/login.html").permitAll()
        .anyRequest().authenticated().and().csrf().disable();
}
```

### 在登录页面添加上是否记住的单选框

> 注意：单选框的 name 属性值必须是 `remember-me`

```java
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/login" method="post">
    用户名：<input type="text" name="username"><br/>
    密码：<input type="password" name="password"><br/>
    <input type="checkbox" name="remember-me">记住我<br/>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

# 微服务权限方案





























