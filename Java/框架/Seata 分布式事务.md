# AT 模式介绍

官方文档：http://seata.io/zh-cn/docs/overview/what-is-seata.html

linux 子系统：https://www.jianshu.com/p/b7a978ed1e77

## 前提

- 支持本地 ACID 事务的关系型数据库
- Java 应用，通过 JDBC 访问数据库。

## 整体机制（两个阶段提交协议）

- 一阶段

    业务数据和回滚日志记录在同一个本地事务中提交，释放本地锁和连接资源。

    在 undo_log 表中，会分别记录事务提交前后的数据，称之为前镜像和后镜像，Seata框架会根据前后镜像以及当前SQL的类型，动态分析、计算出反向的回滚SQL。

- 二阶段

	如果需要提交，则会删除undolog；如果需要回滚，则Seata框架会执行底层自动生成的回滚SQL。
	
	- 提交：提交异步化，非常快速地完成。
	- 回滚：通过一阶段的回滚日志进行反向补偿。

## 写隔离

一阶段本地事务提交前，需要确保先拿到 **全局锁** 。
拿不到 **全局锁** ，不能提交本地事务。
拿 **全局锁** 的尝试被限制在一定范围内，超出范围将放弃，并回滚本地事务，释放本地锁。

```java
=========== 一阶段 ==========
两个全局事务 tx1 和 tx2，分别对 a 表的 m 字段进行更新操作，m 的初始值 1000。
开启本地事务 tx1
拿到本地锁
执行操作 m = 1000 - 100 = 900
拿到全局锁
本地 tx1 提交释放本地锁

开启本地事务 tx2
拿到本地锁
更新操作 m = 900 - 100 = 800
重复尝试拿该记录的 全局锁，此时全局锁被 tx1 持有
```

```java
=========== 二阶段 ==========
事务 tx1 全局提交，释放全局锁 
事务 tx2 拿到 全局锁 ，本地提交
```

> 注意：
>
> 如果 tx1 的二阶段全局回滚，则 tx1 需要重新获取该数据的本地锁，进行反向补偿的更新操作，实现分支的回滚。
>
> 此时，如果 tx2 仍在等待该数据的 全局锁，同时持有本地锁，则 tx1 的分支回滚会失败。分支的回滚会一直重试，直到 tx2 的 全局锁 等锁超时，放弃 全局锁 并回滚本地事务释放本地锁，tx1 的分支回滚最终成功。
>
> 因为整个过程 全局锁 在 tx1 结束前一直是被 tx1 持有的，所以不会发生 脏写 的问题。

## 读隔离

在数据库本地事务隔离级别 **读已提交** 或以上的基础上，Seata（AT 模式）的默认全局隔离级别是 **读未提交**。

如果应用在特定场景下，必需要求全局的 **读已提交** ，目前 Seata 的方式是通过 SELECT FOR UPDATE 语句的代理。

SELECT FOR UPDATE 语句的执行会申请 **全局锁** ，如果 **全局锁** 被其他事务持有，则释放本地锁（回滚 SELECT FOR UPDATE 语句的本地执行）并重试。这个过程中，查询是被 block 住的，直到 **全局锁** 拿到，即读取的相关数据是 **已提交** 的，才返回。

> 出于总体性能上的考虑，Seata 目前的方案并没有对所有 SELECT 语句都进行代理，仅针对 FOR UPDATE 的 SELECT 语句。

# 使用（seata1.4.2）

参考：https://www.cnblogs.com/windowsphones/p/15534084.html

参考：https://blog.csdn.net/ybb_ymm/article/details/126040012

## 安装

参考：http://events.jianshu.io/p/fda8f616ba22

### 下载

官网链接：http://seata.io/zh-cn/blog/download.html

将下载的压缩文件进行解压，得到 Seata 服务端

### 在本地 seata 中配置注册中心、配置中心和数据库

进入 seata 的 conf 目录，修改 `registry.conf` 文件，在其中设置 seata 的注册中心和配置中心，这里以 nacos 为例

```json
registry {
  # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
  type = "nacos"
  nacos {
    application = "seata-server"
    serverAddr = "127.0.0.1:8848"
    group = "SEATA_GROUP"
    namespace = ""
    cluster = "default"
    username = ""
    password = ""
  }
}
config {
  # file、nacos 、apollo、zk、consul、etcd3
  type = "nacos"
  nacos {
    serverAddr = "127.0.0.1:8848"
    namespace = ""
    group = "SEATA_GROUP"
    username = ""
    password = ""
    dataId = "seataServer.properties"
  }
}
```

修改 seata 的数据库配置 `mode = "db"` 指定 seata 的数据保存到 db 中，并在对应位置修改数据库驱动、url、户名、密码等。

```json
## transaction log store, only used in seata-server
store {
  ## store mode: file、db、redis
  mode = "db"
  ## rsa decryption public key
  publicKey = ""
  ## file store property
  file {
    ## store location dir
    dir = "sessionStore"
    # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
    maxBranchSessionSize = 16384
    # globe session size , if exceeded throws exceptions
    maxGlobalSessionSize = 512
    # file buffer size , if exceeded allocate new buffer
    fileWriteBufferCacheSize = 16384
    # when recover batch read size
    sessionReloadReadSize = 100
    # async, sync
    flushDiskMode = async
  }

  ## database store property
  db {
    ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp)/HikariDataSource(hikari) etc.
    datasource = "druid"
    ## mysql/oracle/postgresql/h2/oceanbase etc.
    dbType = "mysql"
    driverClassName = "com.mysql.cj.jdbc.Driver"
    ## if using mysql to store the data, recommend add rewriteBatchedStatements=true in jdbc connection param
    url = "jdbc:mysql://loaclhost:3306/mySeata?useUnicode=true&characterEncoding=utf8&useSSL=false&&serverTimezone=Asiz/Shanghai"
    user = "root"
    password = "zxc001"
    minConn = 5
    maxConn = 100
    globalTable = "global_table"
    branchTable = "branch_table"
    lockTable = "lock_table"
    queryLimit = 100
    maxWait = 5000
  }
}
```

### 创建数据库表

数据库表建表语句参考：[官网](https://github.com/seata/seata/blob/develop/script/server/db/mysql.sql)

TCC 模式需要额外添加 `undo_log` 表，语句如下

```sql
-- 注意此处0.3.0+ 增加唯一索引 ux_undo_log
CREATE TABLE `undo_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `branch_id` bigint(20) NOT NULL,
  `xid` varchar(100) NOT NULL,
  `context` varchar(128) NOT NULL,
  `rollback_info` longblob NOT NULL,
  `log_status` int(11) NOT NULL,
  `log_created` datetime NOT NULL,
  `log_modified` datetime NOT NULL,
  `ext` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `ux_undo_log` (`xid`,`branch_id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
```

### 在 nacos 中添加配置

启动 nacos，在 nacos 中添加必要的配置，具体配置可以参考:[config.txt](https://github.com/seata/seata/blob/develop/script/config-center/config.txt)

- `service.vgroupMapping 的内容为 seata 的分组`
- `service.default.grouplist 的内容为 seata 的外网地址和端口号`

具体内容如下所示：

![image-20220930103854710](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220930103854710.png)

<div >
    <center>
        <img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220930103601214.png"/>
    	<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220930103612765.png"/>
    </center>
</div>

### 启动 seata

进入 seata 中的 bin 目录，可以看到 ` seata-server.sh` 脚本

执行命令 `./seata-server.sh -h 192.168.60.2 -p 8091` 启动 seata

- -h 指定 seata 外网地址
- -p 指定 seata 端口号

# 客户端使用

## 创建消费者工程

### 添加依赖

**<font color=red>注意：openfeign 3.x.x 版本可能和 seata 1.4.2 版本有依赖冲突</font>**

```xml
<properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
    <spring-cloud-alibaba.version>2.2.7.RELEASE</spring-cloud-alibaba.version>
    <springboot.version>2.2.12.RELEASE</springboot.version>
    <junit.version>4.11</junit.version>
    <mybatis-plus.version>3.5.2</mybatis-plus.version>
    <mysql-connection.version>8.0.29</mysql-connection.version>
    <druid.version>1.2.11</druid.version>
    <openfeign.version>2.2.6.RELEASE</openfeign.version>
</properties>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit.version}</version>
            <type>pom</type>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
            <version>${mybatis-plus.version}</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>${druid.version}</version>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql-connection.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
            <version>${openfeign.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>${springboot.version}</version>
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
    </dependencies>
</dependencyManagement>
```

### 编写配置文件

**<font color=red>注意：seata 1.4.0 之前需要编写 registry.conf 配置文件进行 Seata 的配置</font>**

```yaml
spring:
  application:
    name: seata-consumer
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource
    druid:
      url: jdbc:mysql://localhost:3306/mySeata
      username: root
      password: zxc001
  cloud:
    nacos:
      discovery:
        register-enabled: true
        server-addr: 192.168.60.2:8848
    alibaba:
      seata:
        tx-service-group: my_seata_group
seata:
  # seata 服务端所在注册中心配置
  registry:
    type: nacos
    nacos:
      server-addr: 192.168.60.2:8848
      username: nacos
      password: nacos
      group: SEATA_GROUP
  service:
    vgroup-mapping:
      # seata 服务端配置的 vgroup-mapping
      my_seata_group: default
# 开启 DEBUG 日志
debug: true
```

### 编写业务方法实现类

Mapper 等接口或代码由 Generatoy 自动生成

添加 `@GlobalTransactional(rollbackFor = Throwable.class)` 分布式事务注解

```java
@Service
public class TtServiceImpl extends ServiceImpl<TtMapper, Tt>
    implements TtService{

    @Autowired
    private ProducerFeign producerFeign;

    @Override
    @GlobalTransactional(rollbackFor = Throwable.class)
    public void saveTow(){
        producerFeign.save();
        Tt tt1 = baseMapper.selectById(03);
        System.out.println(tt1);
        if (tt1.getId() != 0){
            throw new RuntimeException("打断你");
        }

        Tt tt = new Tt();
        tt.setId(02);
        tt.setName("pink");
        baseMapper.insert(tt);
    }
}
```

### 编写 Feign 接口

```java
@FeignClient("seata-producer")
@Component
public interface ProducerFeign {
    @GetMapping("/save")
    public String save();
}
```

### 编写 Controller

```java
@RestController
public class ConsumerController {

    @Autowired
    private TtService ttServiceImpl;


    @GetMapping("/save")
    public String save(){
        // 调用 Service 操作数据库
        ttServiceImpl.saveTow();
        return "添加成功";
    }
}
```

### 编写 SpringBoot 启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
@EnableTransactionManagement // 开启全局事务
@MapperScan("com.zym.producer.mapper")
public class StartConsumer
{
    public static void main( String[] args )
    {
        SpringApplication.run(StartConsumer.class, args);
    }
}
```

## 创建提供者工程

提供者与消费者 seata 配置相同，其余配置按照自身要求进行配置

### 编写 Controller 

```java
@RestController
public class ProducerController {

    @Autowired
    private TtService ttServiceImpl;

    @GetMapping("/save")
    public String save(){
        Tt tt = new Tt();
        tt.setId(03);
        tt.setName("xxy");
        boolean save = ttServiceImpl.save(tt);
        if (save){ return "添加成功"; }
        return "添加失败";
    }
}
```



# 流程分析

官方文档：https://seata.io/zh-cn/docs/user/appendix/isolation.html

MySQL 锁的介绍：https://blog.csdn.net/TABE_/article/details/122351648

参考：https://cloud.tencent.com/developer/article/1781081

参考：https://blog.csdn.net/m0_60491538/article/details/121405252

参考：https://www.cnblogs.com/milton/p/13755862.html

参考：https://blog.csdn.net/qq_43437874/article/details/122966810

参考：https://blog.csdn.net/chyh741/article/details/124415176

## SeataAutoConfiguration 配置

Seat 的配置类为 `io.seata.spring.boot.autoconfigure.SeataAutoConfiguration`

### GlobalTransactionScanner

配置类中向容器注入了 `GlobalTransactionScanner` 全局事务扫描器

全局事务扫描器继承了很多 Bean 的生命周期接口，如下

```java
public class GlobalTransactionScanner extends AbstractAutoProxyCreator
    implements InitializingBean, ApplicationContextAware,
    DisposableBean {
```

InitializingBean 的实现方法 afterPropertiesSet 中，开启了两个重要的角色 TM 和 RM，他们的主要作用就是用 Netty 和 Seata 服务端进行通信。

```java
@Override
public void afterPropertiesSet() {
    if (disableGlobalTransaction) {
        if (LOGGER.isInfoEnabled()) {
            LOGGER.info("Global transaction is disabled.");
        }
        return;
    }
    // 重点
    initClient();
}

private void initClient() {
    // 必须配置 applicationId 和 txServiceGroup
    // spring.application.name=seata-consumer
    // spring.application.cloud.alibaba.seata.tx-service-group=my_seata_group

    if (StringUtils.isNullOrEmpty(applicationId) || StringUtils.isNullOrEmpty(txServiceGroup)) {
        throw new IllegalArgumentException(String.format("applicationId: %s, txServiceGroup: %s", applicationId, txServiceGroup));
    }
    //init TM
    TMClient.init(applicationId, txServiceGroup);

    //init RM
    RMClient.init(applicationId, txServiceGroup);

    // 注册容器关闭时的钩子
    registerSpringShutdownHook(); 
}
```

> 三个重要角色
>
> - **Transaction Coordinator (TC)**：事务协调器，维护全局事务的运行状态，负责协调并驱动全局事务的提交或回滚。
> - **Transaction Manager (TM)**：控制全局事务的边界，负责开启一个全局事务，并最终发起全局提交或全局回滚的决议。
> - **Resource Manager (RM)**：控制分支事务，负责分支注册、状态汇报，并接收事务协调器的指令，驱动分支（本地）事务的提交和回滚。

AbstractAutoProxyCreator (自动代理创建者)的实现方法有下面两个

- wrapIfNecessary：代理增强的前置判断处理，表示是否该Bean需要增强，如果增强的话创建代理类；主要是对存在全局事务注解的 Bean，添加相应的拦截器。
- postProcessAfterInitialization：当一个spring的Bean已经初始化完毕的时候，后置处理一些东西

```java
@Override
protected Object wrapIfNecessary(Object bean, String beanName, Object cacheKey) {
    if (disableGlobalTransaction) {
        return bean;
    }
    try {
        synchronized (PROXYED_SET) {
            // 1. 如果已被代理，则跳过该Bean ,PROXYED_SET是一个Set<String> 集合
            if (PROXYED_SET.contains(beanName)) {
                return bean;
            }
            interceptor = null;
            //check TCC proxy
            //  2. 判断是否开启 TCC 模式，是则添加TCC 拦截器
            if (TCCBeanParserUtils.isTccAutoProxy(bean, beanName, applicationContext)) {
                //TCC interceptor, proxy bean of sofa:reference/dubbo:reference, and LocalTCC
                interceptor = new TccActionInterceptor(TCCBeanParserUtils.getRemotingDesc(beanName));
            } else {
                // 3. 查询Bean的 Class 类型
                Class<?> serviceInterface = SpringProxyUtils.findTargetClass(bean);
                Class<?>[] interfacesIfJdk = SpringProxyUtils.findInterfaces(bean);

                if (!existsAnnotation(new Class[]{serviceInterface})
                    && !existsAnnotation(interfacesIfJdk)) {
                    return bean;
                }

                // 4. 初始化GlobalTransactionalInterceptor
                if (interceptor == null) {
                    if (globalTransactionalInterceptor == null) {
                        globalTransactionalInterceptor = new GlobalTransactionalInterceptor(failureHandlerHook);
                        ConfigurationCache.addConfigListener(
                            ConfigurationKeys.DISABLE_GLOBAL_TRANSACTION,
                            (ConfigurationChangeListener)globalTransactionalInterceptor);
                    }
                    interceptor = globalTransactionalInterceptor;
                }
            }

            LOGGER.info("Bean[{}] with name [{}] would use interceptor [{}]", bean.getClass().getName(), beanName, interceptor.getClass().getName());
            // 5. 判断是否已经是AOP代理类，不是则走一遍父类的包装
            if (!AopUtils.isAopProxy(bean)) {
                bean = super.wrapIfNecessary(bean, beanName, cacheKey);
            } else {
                // 6. 代理对象添加拦截器
                AdvisedSupport advised = SpringProxyUtils.getAdvisedSupport(bean);
                Advisor[] advisor = buildAdvisors(beanName, getAdvicesAndAdvisorsForBean(null, null, null));
                for (Advisor avr : advisor) {
                    advised.addAdvisor(0, avr);
                }
            }
            // 添加到已处理的集合中，标识该bean已经处理过了
            PROXYED_SET.add(beanName);
            return bean;
        }
    } catch (Exception exx) {
        throw new RuntimeException(exx);
    }
}
```

### SeataAutoDataSourceProxyCreator

创建对DataSource对象的代理

```java
public SeataAutoDataSourceProxyCreator seataAutoDataSourceProxyCreator(SeataProperties seataProperties) {
    return new SeataAutoDataSourceProxyCreator(seataProperties.isUseJdkProxy(),seataProperties.getExcludesForAutoProxying());
}
```

SeataProperties 就是我们配置文件中 spring.cloud.alibaba.seata 和 seata  下指定的参数 

```java
@ConfigurationProperties(prefix = SEATA_PREFIX)
@EnableConfigurationProperties(SpringCloudAlibabaConfiguration.class)
public class SeataProperties {
    /**
     * 是否启用自动配置
     */
    private boolean enabled = true;
    /**
     * 应用程序id
     */
    private String applicationId;
    /**
     * 事务服务集团
     */
    private String txServiceGroup;
    /**
     * 是否启用数据源bean的自动代理
     */
    private boolean enableAutoDataSourceProxy = true;
    /**
     * 是否使用JDK代理而不是CGLIB代理
     */
    private boolean useJdkProxy = false;
    /**
     * 指定哪些数据源bean不适合进行自动代理
     */
    private String[] excludesForAutoProxying = {};
```

SeataAutoDataSourceProxyCreator

```java
public class SeataAutoDataSourceProxyCreator extends AbstractAutoProxyCreator {
    private static final Logger LOGGER = LoggerFactory.getLogger(SeataAutoDataSourceProxyCreator.class);
    private final String[] excludes;
    private final Advisor advisor = new DefaultIntroductionAdvisor(new SeataAutoDataSourceProxyAdvice());

    public SeataAutoDataSourceProxyCreator(boolean useJdkProxy, String[] excludes) {
        this.excludes = excludes;
        setProxyTargetClass(!useJdkProxy);
    }

    @Override
    protected Object[] getAdvicesAndAdvisorsForBean(Class<?> beanClass, String beanName, TargetSource customTargetSource) throws BeansException {
        if (LOGGER.isInfoEnabled()) {
            LOGGER.info("Auto proxy of [{}]", beanName);
        }
        return new Object[]{advisor};
    }
    @Override
    protected boolean shouldSkip(Class<?> beanClass, String beanName) {
        //对DataSource对象进行代理，支持配置排除
        return SeataProxy.class.isAssignableFrom(beanClass) ||
            !DataSource.class.isAssignableFrom(beanClass) ||
                Arrays.asList(excludes).contains(beanClass.getName());
    }
```

SeataAutoDataSourceProxyAdvice 将数据源替换成代理数据源对象

```java
public class SeataAutoDataSourceProxyAdvice implements MethodInterceptor, IntroductionInfo {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        
        // 获取原数据源，然后用原数据源创建数据源代理对象。
        // DataSourceProxyHolder中，对每一个数据源都会用一个唯一的代理对象。 如果没有就新建。
        DataSourceProxy dataSourceProxy = DataSourceProxyHolder.get().putDataSource((DataSource) invocation.getThis());
        Method method = invocation.getMethod();
        Object[] args = invocation.getArguments();
        Method m = BeanUtils.findDeclaredMethod(DataSourceProxy.class, method.getName(), method.getParameterTypes());
        if (m != null) {
            // 调用时用数据源代理对象调用
            return m.invoke(dataSourceProxy, args);
        } else {
            return invocation.proceed();
        }
    }
```

## 自定义代理数据源

> 这里的 DataSourceProxy 指的是 AT 模式的数据源
>
> Seata 并没有对 XA 做数据源代理，所以如果需要使用 XA 模式，需要关闭代理数据源，然后自己代码实现代理数据源。

```properties
seata.enable-auto-data-source-proxy=false
```

```java

@Configuration
public class SeataDataSourceConfig {
 
    @Resource
    private DataSourceProperties dataSourceProperties;
 
    @Bean
    public DataSource dataSource() {
        DruidDataSource druidDataSource = createDruidDataSource();
        //这里可以实现动态切换数据源为XA还是AT模式，只需要返回不同的代理即可
        return new DataSourceProxyXA(druidDataSource);
        //AT 模式下的数据源代理
        //return new DataSourceProxy(druidDataSource);
    }
    
    private DruidDataSource createDruidDataSource() {
        DruidDataSource druidDataSource = new DruidDataSource();
        druidDataSource.setUrl(dataSourceProperties.getUrl());
        druidDataSource.setUsername(dataSourceProperties.getUsername());
        druidDataSource.setPassword(dataSourceProperties.getPassword());
        druidDataSource.setDriverClassName(dataSourceProperties.getDriverClassName());
        druidDataSource.setMaxActive(180);
        druidDataSource.setMaxWait(60000);
        druidDataSource.setMinIdle(0);
        druidDataSource.setValidationQuery("SELECT 1 FROM DUAL");
        druidDataSource.setTestOnBorrow(false);
        druidDataSource.setTestOnReturn(false);
        druidDataSource.setTestWhileIdle(true);
        druidDataSource.setTimeBetweenEvictionRunsMillis(60000);
        druidDataSource.setMinEvictableIdleTimeMillis(25200000);
        druidDataSource.setRemoveAbandoned(true);
        druidDataSource.setRemoveAbandonedTimeout(1800);
        druidDataSource.setLogAbandoned(true);
        return druidDataSource;
    }
 
    @Bean
    public SqlSessionFactory sqlSessionFactory(DataSource dataSource,
                                               @Value("${mybatis.mapper-locations}")
                                                       String mapperLocations) throws Exception {
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        factoryBean.setDataSource(dataSource);
        factoryBean.setMapperLocations(new PathMatchingResourcePatternResolver()
                .getResources(mapperLocations));
        return factoryBean.getObject();
    }
```

## 代理数据源 DataSourceProxy

参考官网：https://seata.io/zh-cn/docs/user/appendix/isolation.html

DataSourceProxy能帮助我们获得几个重要的代理对象

- 通过 `DataSourceProxy.getConnection()` 获得 `ConnectionProxy`
- 通过 `ConnectionProxy.prepareStatement(...)` 获得 `StatementProxy`

Seata 的如何实现事务隔离，就藏在这 2 个 Proxy 中，我先概述下实现逻辑。

