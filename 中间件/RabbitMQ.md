# 消息队列

## `MQ` 的相关概念
### 什么是 `MQ`
`MQ(message queue)`，从字面意思上看，本质是个队列，`FIFO` 先入先出，只不过队列中存放的内容是 `message` 而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，`MQ` 是一种非常常见的上下游 `“逻辑解耦+物理解耦”` 的消息通信服务。使用了 `MQ` 之后，消息发送上游只需要依赖 `MQ`，不用依赖其他服务。

### 为什么要用 `MQ`

#### 流量消峰

举个例子，如果订单系统最多能处理一万次订单，这个处理能力应付正常时段的下单时绰绰有余，正常时段我们下单一秒后就能返回结果。但是在高峰期，如果有两万次下单操作系统是处理不了的，只能限制订单超过一万后不允许用户下单。使用消息队列做缓冲，我们可以取消这个限制，把一秒内下的订单分散成一段时间来处理，这时有些用户可能在下单十几秒后才能收到下单成功的操作，但是比不能下单的体验要好。

#### 应用解耦

以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流系统恢复后，继续处理订单信息即可，中单用户感受不到物流系统的故障，提升系统的可用性。

![image-20220224094745866](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220224094745866.png) 

#### 异步处理
有些服务间调用是异步的，例如A调用B，B需要花费很长时间执行，但是A需要知道B什么时候可以执行完，以前一般有两种方式，A过一段时间去调用B的查询 `api`查询。或者A提供一个 `callback api`，B执行完之后调用 `api` 通知A服务。这两种方式都不是很优雅，使用消息总线，可以很方便解决这个问题，A调用B服务后，只需要监听B处理完成的消息，当B处理完成后，会发送一条消息给 `MQ`，`MQ` 会将此消息转发给A服务。这样A服务既不用循环调用B的查询 `api`，也不用提供 `callback api`。同样B服务也不用做这些操作。A服务还能及时的得到异步处理成功的消息。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220224094940725.png" alt="image-20220224094940725"  /> 

## `MQ` 的分类

### `ActiveMQ`
优点：单机吞吐量万级，时效性 `ms` 级，可用性高，基于主从架构实现高可用性，消息可靠性较低的概率丢失数据
缺点：官方社区现在对 `ActiveMQ 5.x` 维护越来越少，高吞吐量场景较少使用。

### `Kafka`
大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开 `Kafka`，这款为大数据而生的消息中间件，以其百万级 `TPS` 的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥着举足轻重的作用。目前已经被 `LinkedIn`，`Uber`， `Twitter`， `Netflix` 等大公司所采纳。

优点： 性能卓越，单机写入 `TPS` 约在百万条/秒，最大的优点，就是吞吐量高。时效性ms级可用性非常高，`kafka` 是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用，消费者采用Pull方式获取消息， 消息有序， 通过控制能够保证所有消息被消费且仅被消费一次;有优秀的第三方 `Kafka Web` 管理界面 `Kafka-Manager`；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持：功能较为简单，主要支持简单的 `MQ` 功能，在大数据领域的实时计算以及日志采集被大规模使用

缺点：`Kafka` 单机超过 `64` 个队列/分区，`Load`会发生明显的飙高现象，队列越多，`load`越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢；

### `RocketMQ`

`RocketMQ` 出自阿里巴巴的开源产品，用 `Java` 语言实现，在设计时参考了 `Kafka`，并做出了自己的一些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，`binglog`分发等场景。

优点：单机吞吐量十万级，可用性非常高，分布式架构，消息可以做到 `0` 丢失，`MQ`功能较为完善，还是分布式的，扩展性好，支持10亿级别的消息堆积，不会因为堆积导致性能下降，源码是 `java` 我们可以自己阅读源码，定制自己公司的 `MQ`

缺点：支持的客户端语言不多，目前是 `java` 及 `c++`，其中 `c++` 不成熟；社区活跃度一般，没有在 `MQ` 核心中去实现 `JMS` 等接口，有些系统要迁移需要修改大量代码

### `RabbitMQ`

`2007` 年发布，是一个在 `AMQP` (高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。

优点：由于 `erlang` 语言的高并发特性，性能较好；吞吐量到万级，`MQ` 功能比较完备，健壮、稳定、易用、跨平台、支持多种语言 如：`Python`、`Ruby`、`.NET`、`Java`、`JMS`、`C`、`PHP`、`ActionScript`、`XMPP`、`STOMP` 等，支持 `AJAX` 文档齐全；开源提供的管理界面非常棒，用起来很好用，社区活跃度高；更新频率相当高，官网：https://www.rabbitmq.com/news.html

缺点：商业版需要收费，学习成本较高

## `MQ` 的选择

`Kafka`

`Kafka` 主要特点是基于 `Pull` 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，如果有日志采集功能，肯定是首选 `kafka` 了。尚硅谷官网 `kafka` 视频连接：http://www.gulixueyuan.com/course/330/tasks

`RocketMQ`
天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。`RoketMQ` 在稳定性上可能更值得信赖，这些业务场景在阿里双十一已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择 `RocketMQ`。

`RabbitMQ`
结合 `erlang` 语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的数据量没有那么大，中小型公司优先选择功能比较完备的 `RabbitMQ`。

# RabbitMQ

## `RabbitMQ` 的概念

`RabbitMQ` 是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑 `RabbitMQ` 是一个快递站，一个快递员帮你传递快件。`RabbitMQ` 与快递站的主要区别在于，它不处理快件而是接收，存储和转发消息数据。

## 四大核心概念

### 生产者

产生数据发送消息的程序是生产者

### 交换机
交换机是 `RabbitMQ` 非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定

### 队列

队列是 `RabbitMQ` 内部使用的一种数据结构，尽管消息流经 `RabbitMQ` 和应用程序，但它们只能存储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式

### 消费者

消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

## `RabbitMQ` 核心部分

参考文档：https://www.rabbitmq.com/getstarted.html

`hello world`、工作队列、发布订阅、路由、主题交换机、远程过程调用

![image-20220224104305795](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220224104305795.png) 

## 各个名词介绍

![image-20220224104600088](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220224104600088.png) 

**Broker：**接收和分发消息的应用，`RabbitMQ Server` 就是 `Message Broker`

**Virtual host：**出于多租户和安全因素设计的，把 `AMQP` 的基本组件划分到一个虚拟的分组中，类似于网络中的 `namespace` 概念。当多个不同的用户使用同一个  `RabbitMQ server` 提供的服务时，可以划分出多个 `vhost` ，每个用户在自己的 `vhost`  创建  `exchange／queue` 等

**Connection：**`publisher／consumer` 和 `broker` 之间的 `TCP` 连接

**Channel：**如果每一次访问 `RabbitMQ` 都建立一个 `Connection`，在消息量大的时候建立 `TCP Connection` 的开销将是巨大的，效率也较低。`Channel` 是在 `connection` 内部建立的逻辑连接，如果应用程序支持多线程，通常每个 `thread` 创建单独的 `channel` 进行通讯，`AMQP method` 包含了 `channel id`帮助客户端和 `message broker` 识别 `channel`，所以 `channel` 之间是完全隔离的。`Channe` l作为轻量级的 `Connection` 极大减少了操作系统建立 `TCP connection`的开销

**Exchange：**`message`  到达 `broker` 的第一站，根据分发规则，匹配查询表中的 `routing key`，分发消息到 `queue` 中去。常用的类型有：`direct` (point-to-point)，`topic` (publish-subscribe) and `fanout` (multicast)

**Queue：**消息最终被送到这里等待 `consumer `取走

**Binding：**`exchange` 和 `queue` 之间的虚拟连接，`binding` 中可以包含 `routing key` ，`Binding` 信息被保存到 `exchange` 中的查询表中，用于 `message` 的分发依据

## 安装

### 官网地址

https://www.rabbitmq.com/download.html

### 文件上传

![image-20220224195146099](C:\Users\86159\AppData\Roaming\Typora\typora-user-images\image-20220224195146099.png) 

将 RabbitMQ 的安装所需要的文件下载到服务器 `/usr/RabbitMQ`

### 安装文件(分别按照以下顺序安装)

```
#安装rabbit依赖的环境，erlang，socat
yum install -y erlang
yum install socat -y
rpm -ivh rabbitmq-server-3.8.8-1.el7.noarch.rpm 
```

### 常用命令(按照以下顺序执行)

#### 添加开机启动RabbitMQ服务

chkconfig rabbitmq-server on

#### 启动服务

/sbin/service rabbitmq-server start

#### 查看服务状态

/sbin/service rabbitmq-server status

#### 停止服务(选择执行)

/sbin/service rabbitmq-server stop

## 开启web管理插件

rabbitmq-plugins enable rabbitmq_management

用默认账号密码 `（guest）` 访问地址：http://192.168.83.128:15672/ 出现权限问题，需要重新创建用户登录。

![image-20220224205552173](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220224205552173.png) 

### 添加一个新的用户

#### 创建账号 admin 密码 123

```shell
rabbitmqctl add_user admin 123
```

#### 设置用户角色 administrator（超级管理员）

```shell
rabbitmqctl set_user_tags admin administrator
```

#### 设置用户权限

```shell
用法：rabbitmqctl  set_permissions [-p <vhostpath>] <user> <conf> <write> <read>
示例：rabbitmqctl set_permissions -p "/" admin ".*" ".*" ".*"
```

用户user_admin具有/vhost1这个virtual host中所有资源的配置、写、读权限

#### 当前用户和角色

```shell
rabbitmqctl list_users
```

#### 再次使用新添加的用户登录

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220224214815026.png" alt="image-20220224214815026" style="zoom: 50%;" /> 

#### 重置命令

##### 关闭应用的命令为

```shell
rabbitmqctl stop_app
```

##### 清除的命令为

```shell
rabbitmqctl reset
```

##### 重新启动命令为

```shell
rabbitmqctl start_app
```

## Hello World

在本教程的这一部分中，我们将用Java编写两个程序。发送单个消息的生产者和接收消息并打印出来的消费者。

我们将介绍Java API中的一些细节。

在下图中，“ P”是我们的生产者，“ C”是我们的消费者。中间的框是一个队列-RabbitMQ代表使用者保留的消息缓冲区

<font color="red">生产者往队列中发送信息，消费者接收。</font>

![image-20220225214521558](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220225214521558.png) 

### 添加 pom 依赖

```xml
<!--rabbitmq依赖客户端-->
<dependency>
    <groupId>com.rabbitmq</groupId>
    <artifactId>amqp-client</artifactId>
    <version>5.8.0</version>
</dependency>
<!--操作文件流的一个依赖-->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.6</version>
</dependency>
<build>
    <plugins>
        <!--指定jdk编译版本-->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <configuration>
                <source>8</source>
                <target>8</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### 生产者代码（发送消息）

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 生产者：发送消息
 */
public class Producer {

    //队列名称
    public static final String QUEUE_NAME = "hello";
    //发消息
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //工厂 IP 连接 RabbitMQ 队列
        factory.setHost("192.168.83.128");
        //设置用户名
        factory.setUsername("admin");
        //设置密码
        factory.setPassword("admin");
        //创建连接
        Connection connection = factory.newConnection();
        //获取信道
        Channel channel = connection.createChannel();
        /**
         * 创建一个队列
         * 参数：
         *      队列名称
         *      队列里面的消息是否需要持久化，默认情况下（false）是不持久化的
         *      该队列是否只供一个消费者进行消费，是否进行消息共享，true 可以允许多个消费者消费
         *      是否自动删除，最后一个消费者断开连接后，该队列是否自动删除，true 表示自动删除
         *      其他参数
         */
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        //发消息
        String message = "Hello World";
        /**
         * 发送消息
         * 参数：
         *      发送到哪一个交换机
         *      路由的 key 值是什么，本次是队列的名称
         *      其他参数
         *      发送的消息
         */
        channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
        System.out.println("消息发送成功");
    }
}
```

执行成功后，可以在 web 管理页面看到队列中的消息

![image-20220227200859898](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220227200859898.png) 

### 消费者代码（接收消息）

```java
import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 消费者代码；接收消息
 */
public class Consumer {
    //队列名称
    public static final String QUEUE_NAME = "hello";
    //接收消息
    public static void main(String[] args) throws IOException, TimeoutException {
        //创建链接工厂
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("192.168.83.128");
        factory.setUsername("admin");
        factory.setPassword("admin");
        Connection connection = factory.newConnection();
        Channel channel = connection.createChannel();

        //未成功消费的回调
        DeliverCallback deliverCallback = (consumerTag,message)->{
            System.out.println(new String(message.getBody()));
        };
        //取消消费的回调
        CancelCallback cancelCallback = consumerTag->{
            System.out.println("消费被中断！！！");
        };
        /**
         * 消费者接收消息
         * 参数：
         *      消费哪个队列
         *      消费成功后是否要自动应答，true代表自动应答
         *      消费者未成功消费的回调
         *      消费者取消消费的回调
         */
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}
```

## Work Queues（工作队列）

![image-20220227210142345](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220227210142345.png) 

工作队列（又称任务队列）的主要思想是避免立即执行资源密集型任务，而不得不等待它完成。相反我们安排任务在之后执行。我们把任务封装为消息并将其发送到队列。在后台运行的工作进程将弹出任务并最终执行作业。当有多个工作线程时，这些工作线程将一起处理这些任务。

### 轮训分发消息

在这个案例中我们会启动两个工作线程，一个消息发送线程，我们来看看他们两个工作线程是如何工作的。

#### 抽取工具类（获取信道）

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;
import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 创建信道的工具类
 */
public class RabbitMqUtils {

    public static Channel getChannel() throws IOException, TimeoutException {
        //创建一个连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        //工厂 IP 连接 RabbitMQ 队列
        factory.setHost("192.168.83.128");
        //设置用户名
        factory.setUsername("admin");
        //设置密码
        factory.setPassword("admin");
        //创建链接
        Connection connection = factory.newConnection();
        //获取信道
        Channel channel = connection.createChannel();
        return channel;
    }
}
```

#### 编写工作线程代码

```java
import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zym.Utils.RabbitMqUtils;
import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * 工作线程，相当于消费者
 */
public class Woker01 {
    //队列名称
    public static final String QUEUE_NAME = "hello";

    public static void main(String[] args) throws IOException, TimeoutException {
        //通过util类获取信道
        Channel channel = RabbitMqUtils.getChannel();
        DeliverCallback deliverCallback = (consumerTag,message)->{
            String callbackMessage = new String(message.getBody());
            System.out.println("接收到消息："+callbackMessage);
        };
        CancelCallback cancelCallback = (consumerTag)->{
            System.out.println("消费者取消消费接口回调逻辑");
        };
        channel.basicConsume(QUEUE_NAME,true,deliverCallback,cancelCallback);
    }
}
```

#### 设置允许多开，运行两个Woker01

![image-20220228104214049](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228104214049.png) 

#### 编写生产者代码

```java
import com.rabbitmq.client.Channel;
import com.zym.Utils.RabbitMqUtils;
import java.io.IOException;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

/**
 * 生产者：发送消息
 */
public class Task01 {
    //队列名称
    public static final String QUEUE_NAME = "hello";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        Scanner sc = new Scanner( System.in);
        System.out.println("请输入消息内容：");
        while (sc.hasNext()){
            String message = sc.next();
            channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
            System.out.println("发送消息完成："+message);
        }
    }
}
```

#### 结果展示

![image-20220228110559538](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228110559538.png) 

![image-20220228110817291](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228110817291.png) 

### 消息应答

消费者完成一个任务可能需要一段时间，如果其中一个消费者处理一个长的任务并仅只完成了部分突然它挂掉了，会发生什么情况。

RabbitMQ一旦向消费者传递了一条消息，便立即将该消息标记为删除。在这种情况下，突然有个消费者挂掉了，我们将丢失正在处理的消息。以及后续发送给该消费这的消息，因为它无法接收到。

为了保证消息在发送过程中不丢失，rabbitmq引入消息应答机制，消息应答就是:消费者在接收到消息并且处理该消息之后，告诉rabbitmq它已经处理了，rabbitmq可以把该消息删除了。

#### 自动应答

消息发送后立即被认为已经传送成功，这种模式需要在高吞吐量和数据传输安全性方面做权衡；

因为这种模式如果消息在接收到之前，消费者那边出现连接或者channel关闭，那么消息就丢失了；

当然另一方面这种模式消费者那边可以传递过载的消息，没有对传递的消息数量进行限制，当然这样有可能使得消费者这边由于接收太多还来不及处理的消息，导致这些消息的积压，最终使得内存耗尽，最终这些消费者线程被操作系统杀死；

所以这种模式仅适用在消费者可以高效并以某种速率能够处理这些消息的情况下使用。

#### 消息应答的方法

- Channel.basicAck（用于肯定确认）

  RabbitMQ已知道该消息并且成功的处理消息，可以将其丢弃了

- Channel.basicNack（用于否定确认）

- Channel.basicReject（用于否定确认）

  与Channel.basicNack相比少一个参数，不处理该消息了直接拒绝，可以将其丢弃了

#### Multiple的解释

手动应答的好处是可以批量应答并且减少网络拥堵

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228111958787.png" alt="image-20220228111958787" style="zoom: 50%;" /> 

`multiple` 的 `true` 和 `false` 代表不同意思

`true` 代表批量应答 `channel` 上未应答的消息

​		比如说 `channel`上有传送 `tag` 的消息 5、6、7、8，当前 `tag` 是8，那么此时 5-8 的这些还未应答的消息都会被确认收到消息应答

`false` 同上面相比，只会应答 `tag=8` 的消息 5、6、7这三个消息依然不会被确认收到消息应答

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228112357898.png" alt="image-20220228112357898" style="zoom: 67%;" /> 

#### 消息自动重新入队

如果消费者由于某些原因失去连接（其通道已关闭，连接已关闭或TCP连接丢失），导致消息未发送ACK确认，RabbitMQ将了解到消息未完全处理，并将对其重新排队。

如果此时其他消费者可以处理，它将很快将其重新分发给另一个消费者。

这样，即使某个消费者偶尔死亡，也可以确保不会丢失任何消息。

![image-20220228113516042](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228113516042.png) 

#### 消息手动应答代码

默认消息采用的是自动应答，所以我们要想实现消息消费过程中不丢失，需要把自动应答改为手动应答。

##### 消费者代码如下：

```java
import com.rabbitmq.client.CancelCallback;
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.DeliverCallback;
import com.zym.Utils.RabbitMqUtils;
import java.io.IOException;
import java.util.concurrent.TimeoutException;

public class Woker02 {

    private static final String QUEUE_NAME = "ack_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        DeliverCallback deliverCallback = (consumerTag,message)->{
            String str = new String(message.getBody());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("接收到消息："+str);
            /**
             * 消息应答
             *      消息标记tag
             *      是否批量应答未应答消息
             */
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        };
        CancelCallback cancelCallback = (consumerTag)->{
            System.out.println("消费者取消消费："+consumerTag);
        };
        //设置预取值
        channel.basicQos(1);
        //autoAck=false 采取手动应答的方式
        channel.basicConsume(QUEUE_NAME,false,deliverCallback,cancelCallback);
    }
}
```

##### 生产者代码如下：

```java
import com.rabbitmq.client.Channel;
import com.zym.Utils.RabbitMqUtils;
import java.io.IOException;
import java.util.Scanner;
import java.util.concurrent.TimeoutException;

public class Task02 {

    private static final String QUEUE_NAME = "ack_queue";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.queueDeclare(QUEUE_NAME,false,false,false,null);
        Scanner sc = new Scanner(System.in);
        System.out.println("请输入消息内容：");
        while (sc.hasNext()){
            String message = sc.next();
            channel.basicPublish("",QUEUE_NAME,null,message.getBytes());
            System.out.println("发送消息完成："+message);
        }
    }
}
```

#### 手动应答效果演示

正常情况下消息发送方发送两个消息 `C1` 和 `C2` 分别接收到消息并进行处理

![image-20220228143343915](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228143343915.png) 

![image-20220228143411640](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228143411640.png) 

在发送者发送消息 `dd`，发出消息之后的把 `C2` 消费者停掉，按理说该 `C2` 来处理该消息，但是由于它处理时间较长，在还未处理完，也就是说 `C2` 还没有执行 `ack` 代码的时候，`C2` 被停掉了，此时会看到消息被 `C1` 接收到了，说明消息 `dd` 被重新入队，然后分配给能处理消息的 `C1` 处理了

![image-20220228143740073](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228143740073.png) 

![image-20220228143854065](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228143854065.png) 

## RabbitMQ持久化

刚刚我们已经看到了如何处理任务不丢失的情况，但是如何保障当RabbitMQ服务停掉以后消息生产者发送过来的消息不丢失。

默认情况下RabbitMQ退出或由于某种原因崩溃时，它忽视队列和消息，除非告知它不要这样做。

确保消息不会丢失需要做两件事：我们需要将队列和消息都标记为持久化。

### 队列如何实现持久化

之前我们创建的队列都是非持久化的，rabbitmq如果重启的化，该队列就会被删除掉。

如果 要队列实现持久化 需要在声明队列的时候把durable参数设置为持久化

![image-20220228145329618](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228145329618.png) 

但是需要注意的就是如果之前声明的队列不是持久化的，需要把原先队列先删除，或者重新创建一个持久化的队列，不然就会出现错误

![image-20220228145623089](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228145623089.png) 

以下为控制台中持久化与非持久化队列的UI显示区、

![image-20220228145745732](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228145745732.png) 

这个时候即使重启rabbitmq队列也依然存在

### 消息实现持久化

要想让消息实现持久化需要在消息生产者修改代码，`MessageProperties.PERSISTENT_TEXT_PLAIN` 添加这个属性。

![image-20220228151741725](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228151741725.png) 

将消息标记为持久化并不能完全保证不会丢失消息。

尽管它告诉RabbitMQ将消息保存到磁盘，但是这里依然存在当消息刚准备存储在磁盘的时候但是还没有存储完，消息还在缓存的一个间隔点。

此时并没有真正写入磁盘。持久性保证并不强，但是对于我们的简单任务队列而言，这已经绰绰有余了。

如果需要更强有力的持久化策略，参考后边课件发布确认章节。

### 不公平分发

在最开始的时候我们学习到 `RabbitMQ` 分发消息采用的轮训分发，但是在某种场景下这种策略并不是很好;

比方说有两个消费者在处理任务，其中有个消费者 1 处理任务的速度非常快，而另外一个消费者 2 处理速度却很慢，这个时候我们还是采用轮训分发的化就会到这处理速度快的这个消费者很大一部分时间处于空闲状态，而处理慢的那个消费者一直在干活;

这种分配方式在这种情况下其实就不太好，但是 `RabbitMQ` 并不知道这种情况它依然很公平的进行分发。

为了避免这种情况，我们可以在消费者中，进行消费之前设置参数 `channel.basicQos(1);`

![image-20220228153222316](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228153222316.png) 

![image-20220228153108858](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228153108858.png)

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228153245562.png" alt="image-20220228153245562" style="zoom: 40%;" /> 

意思就是如果这个任务我还没有处理完或者我还没有应答你，你先别分配给我，我目前只能处理一个任务;

然后 `rabbitmq` 就会把该任务分配给没有那么忙的那个空闲消费者;

当然如果所有的消费者都没有完成手上任务，队列还在不停的添加新任务，队列有可能就会遇到队列被撑满的情况，这个时候就只能添加新的  `worker` 或者改变其他存储任务的策略。

#### 例如

`woker02` 工作线程执行较快，只需要 `3` 秒，`woker03` 工作线程执行较慢，需要 `30` 秒，在第一轮分配给 `woker03` 后，在 `woker03` 工作的时间内，`woker02` 已经可以完成好几轮工作，就会将消息优先分配给 `woker02`，如下：

![image-20220228223954527](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228223954527.png) 

### 预取值

预取值即 `channel.basicQos(1);` 中的值，它表示当前消费者最多可以积压几条消息待处理。

本身消息的发送就是异步发送的，所以在任何时候，channel上肯定不止只有一个消息。

另外来自消费者的手动确认本质上也是异步的。因此这里就存在一个未确认的消息缓冲区，因此希望开发人员能限制此缓冲区的大小，以避免缓冲区里面无限制的未确认消息问题。

这个时候就可以通过使用 `basic.qos` 方法设置 “预取计数” 值来完成的。该值定义通道上允许的未确认消息的最大数量。一旦数量达到配置的数量，`RabbitMQ` 将停止在通道上传递更多消息，除非至少有一个未处理的消息被确认；

例如，假设在通道上有未确认的消息5、6、7、8，并且通道的预取计数设置为4，此时 `RabbitMQ` 将不会在该通道上再传递任何消息，除非至少有一个未应答的消息被 `ack`。

比方说 `tag=6` 这个消息刚刚被确认 `ACK`，`RabbitMQ` 将会感知这个情况到并再发送一条消息。消息应答和 `QoS` 预取值对用户吞吐量有重大影响。通常，增加预取将提高向消费者传递消息的速度。

虽然自动应答传输消息速率是最佳的，但是，在这种情况下已传递但尚未处理的消息的数量也会增加，从而增加了消费者的 `RAM` 消耗（随机存取存储器）应该小心使用具有无限预处理的自动确认模式或手动确认模式。

消费者消费了大量的消息如果没有确认的话，会导致消费者连接节点的内存消耗变大，所以找到合适的预取值是一个反复试验的过程，不同的负载该值取值也不同，100到300范围内的值通常可提供最佳的吞吐量，并且不会给消费者带来太大的风险。预取值为1是最保守的。当然这将使吞吐量变得很低，特别是消费者连接延迟很严重的情况下，特别是在消费者连接等待时间较长的环境中。对于大多数应用来说，稍微高一点的值将是最佳的。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220228155953847.png" alt="image-20220228155953847" style="zoom: 80%;" /> 

## 发布确认

发布确认是帮助我们解决消息不丢失的重要环节，消息持久化才能不丢失，下面是消息持久化的必要步骤

![image-20220301194937257](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301194937257.png) 

### 发布确认原理

生产者将信道设置成 `confirm` 模式，一旦信道进入 `confirm` 模式，所有在该信道上面发布的消息都将会被指派一个唯一的ID(从1开始)，一旦消息被投递到所有匹配的队列之后，`broker` 就会发送一个确认给生产者(包含消息的唯一ID)，这就使得生产者知道消息已经正确到达目的队列了，如果消息和队列是可持久化的，那么确认消息会在将消息写入磁盘之后发出，`broker` 回传给生产者的确认消息中 `delivery-tag` 域包含了确认消息的序列号，此外 `broker` 也可以设置 `basic.ack` 的 `multiple` 域，表示到这个序列号之前的所有消息都已经得到了处理。

`confirm` 模式最大的好处在于他是异步的，一旦发布一条消息，生产者应用程序就可以在等信道返回确认的同时继续发送下一条消息，当消息最终得到确认之后，生产者应用便可以通过回调方法来处理该确认消息，如果 `RabbitMQ` 因为自身内部错误导致消息丢失，就会发送一条 `nack` 消息，生产者应用程序同样可以在回调方法中处理该 `nack` 消息。

### 发布确认的策略

#### 开启发布确认的方法

发布确认默认是没有开启的，如果要开启需要调用方法 `confirmSelect`，每当你要想使用发布确认，都需要在 `channel` 上调用该方法。

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301092659422.png" alt="image-20220301092659422" style="zoom:50%;" /> 

#### 单个确认发布

这是一种简单的确认方式，它是一种同步确认发布的方式，也就是发布一个消息之后只有它被确认发布，后续的消息才能继续发布，`waitForConfirmsOrDie(long)` 这个方法只有在消息被确认的时候才返回，如果在指定时间范围内这个消息没有被确认那么它将抛出异常。

这种确认方式有一个最大的缺点就是：发布速度特别的慢，因为如果没有确认发布的消息就会阻塞所有后续消息的发布，这种方式最多提供每秒不超过数百条发布消息的吞吐量。当然对于某些应用程序来说这可能已经足够了。

![image-20220301145546043](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301145546043.png) 

```java
/**
 * 发布确认
 */
public class Task03_Publish {
    //队列名称
    public static final String QUEUE_NAME = "ack_queue";
    //发送几条消息
    public static final Integer MESSAGE_COUNT = 100;

    public static void main(String[] args) throws Exception {
        publishMessageIndividually();
    }

    /**
     * 单个发布确认
     */
    public static void publishMessageIndividually() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        //开启发布确认
        channel.confirmSelect();
        //开始发布时间
        long begin = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i+"";
            channel.basicPublish("",QUEUE_NAME,MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes());
            //服务端返回false或超时时间内未返回，生产者可以消息重发
            boolean flag = channel.waitForConfirms();
            if (flag){
                System.out.println("发送消息成功："+message);
            }
        }
        //结束发布时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个单独确认消息,耗时" + (end - begin) + "ms");
    }
}
```



#### 批量确认发布

上面那种方式非常慢，与单个等待确认消息相比，先发布一批消息然后一起确认可以极大地提高吞吐量

当然这种方式的缺点就是：当发生故障导致发布出现问题时，不知道是哪个消息出现问题了，我们必须将整个批处理保存在内存中，以记录重要的信息而后重新发布消息。当然这种方案仍然是同步的，也一样阻塞消息的发布。

![image-20220301145446644](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301145446644.png) 

```java
/**
 * 发布确认
 */
public class Task03_Publish {
    //队列名称
    public static final String QUEUE_NAME = "ack_queue";
    //发送几条消息
    public static final Integer MESSAGE_COUNT = 100;

    public static void main(String[] args) throws Exception {
        publishMessageBatch();
    }

    /**
     * 批量发布确认
     */
    public static void publishMessageBatch() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        //开启发布确认
        channel.confirmSelect();
        //批量确认消息大小
        int batchSize = 100;
        //未确认消息个数
        int outstandingMessageCount = 0;
        //发布开始时间
        long begin = System.currentTimeMillis();

        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = i+"";
            channel.basicPublish("",QUEUE_NAME,MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes());
            System.out.println("消息发送成功："+message);
            outstandingMessageCount++;
            //当未确认消息个数等于批量确认消息大小时，进行一次发布确认
            if (batchSize == outstandingMessageCount){
                channel.waitForConfirms();
                outstandingMessageCount = 0;
            }
        }
        //消息发布完成后，如果还有未确认的消息，再进行一次发布确认
        if (outstandingMessageCount > 0){
            channel.waitForConfirms();
            outstandingMessageCount = 0;
        }
        //结束发布时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个批量确认消息,耗时" + (end - begin) + "ms");
    }
}
```

#### 异步确认发布

异步确认虽然编程逻辑比上两个要复杂，但是性价比最高，无论是可靠性还是效率都没得说.

他是利用回调函数来达到消息可靠性传递的，这个中间件也是通过函数回调来保证是否投递成功，下面就让我们来详细讲解异步确认是怎么实现的。

![image-20220301112052721](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301112052721.png) 

![image-20220301205622677](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301205622677.png) 

```java
/**
 * 发布确认示例
 */
public class Task03_Publish {
    //队列名称
    public static final String QUEUE_NAME = "ack_queue";
    //发送几条消息
    public static final Integer MESSAGE_COUNT = 100;

    public static void main(String[] args) throws Exception {
        publishMessageAsync();
    }

    /**
     * 异步发布确认
     */
    public static void publishMessageAsync() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        /****************************************************异步确认核心部分↓*************************************************/
        //开启发布确认
        channel.confirmSelect();

		/**
         * 消息确认成功的回调
         *      参数1：消息的标记
         *      参数2：是否为批量确认
         */
        ConfirmCallback ackCallback = (deliveryTag,multiple)->{
            System.out.println("确认的消息："+deliveryTag);
        };
        /**
         * 消息确认失败的回调
         *      参数1：消息的标记
         *      参数2：是否为批量确认
         */
        ConfirmCallback nackCallback = (deliveryTag, multiple) -> {
            System.out.println("发布的消息"+message+"未被确认，序列号"+deliveryTag);
        };
        /**
         * 添加一个异步确认的监听器
         * 	1.确认收到消息的回调
         * 	2.未收到消息的回调
         */
        channel.addConfirmListener(ackCallback, nackCallback);
        /****************************************************异步确认核心部分↑*************************************************/
        //发布消息前的时间
        long begin = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
        }
        //发布完成后的时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息,耗时" + (end - begin) + "ms");
    }
}
```

### 如何处理异步未确认消息

最好的解决的解决方案就是把未确认的消息放到一个基于内存的能被发布线程访问的队列。

比如说用 `ConcurrentLinkedQueue` 这个队列在 `confirm callbacks` 与发布线程之间进行消息的传递。

```java
/**
 * 发布确认示例，异步发布确认，处理未确认的消息
 */
public class Task03_Publish {
    //队列名称
    public static final String QUEUE_NAME = "ack_queue";
    //发送几条消息
    public static final Integer MESSAGE_COUNT = 100;

    public static void main(String[] args) throws Exception {
        publishMessageAsync();
    }
    
    /**
     * 异步发布确认
     */
    public static void publishMessageAsync() throws Exception {
        Channel channel = RabbitMqUtils.getChannel();
        channel.queueDeclare(QUEUE_NAME,true,false,false,null);
        /****************************************************异步确认核心部分↓*************************************************/
        channel.confirmSelect();
        /**
         * 线程安全有序的一个哈希表，适用于高并发的情况，用于存放未确认的序列号与消息体的关联
         * 	1.轻松的将序号与消息进行关联
         * 	2.轻松批量删除条目 只要给到序列号
         * 	3.支持并发访问
         */
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();

        /**
         * 消息确认成功的回调
         *      参数1：消息的标记
         *      参数2：是否为批量确认
         */
        ConfirmCallback ackCallback = (deliveryTag,multiple)->{
            if (multiple){
                //批量确认的情况，返回的是小于等于当前序列号的未确认消息 是一个map
                ConcurrentNavigableMap<Long, String> confirmed = outstandingConfirms.headMap(deliveryTag, true);
                //清除该部分未确认消息
                confirmed.clear();
            }else {
                //单个确认的情况
                outstandingConfirms.remove(deliveryTag);
            }
        };
        /**
         * 消息确认失败的回调
         *      参数1：消息的标记
         *      参数2：是否为批量确认
         */
        ConfirmCallback nackCallback = (sequenceNumber, multiple) -> {
            String message = outstandingConfirms.get(sequenceNumber);
            System.out.println("发布的消息"+message+"未被确认，序列号"+sequenceNumber);
        };
        /**
         * 添加一个异步确认的监听器
         * 	1.确认收到消息的回调
         * 	2.未收到消息的回调
         */
        channel.addConfirmListener(ackCallback, nackCallback);
        /****************************************************异步确认核心部分↑*************************************************/
        //发布消息前的时间
        long begin = System.currentTimeMillis();
        for (int i = 0; i < MESSAGE_COUNT; i++) {
            String message = "消息" + i;
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
            /**
             * channel.getNextPublishSeqNo()获取下一个消息的序列号
             * 通过序列号与消息体进行一个关联
             * 用于存放未确认的消息体
             */
            outstandingConfirms.put(channel.getNextPublishSeqNo(), message);
        }
        //发布完成后的时间
        long end = System.currentTimeMillis();
        System.out.println("发布" + MESSAGE_COUNT + "个异步确认消息,耗时" + (end - begin) + "ms");
    }
}

```



### 以上3种发布确认速度对比

单独发布消息：

​	同步等待确认，简单，但吞吐量非常有限。

批量发布消息：

​	批量同步等待确认，简单，合理的吞吐量，一旦出现问题但很难推断出是哪条消息出现了问题。

异步处理：

​	最佳性能和资源使用，在出现错误的情况下可以很好地控制，但是实现起来稍微难些

## 交换机

在上一节中，我们创建了一个工作队列。我们假设的是工作队列背后，每个任务都恰好交付给一个消费者（工作进程）。在这一部分中，我们将做一些完全不同的事情——我们将消息传达给多个消费者。这种模式称为 ”发布/订阅”。

为了说明这种模式，我们将构建一个简单的日志系统。它将由两个程序组成:第一个程序将发出日志消息，第二个程序是消费者。其中我们会启动两个消费者，其中一个消费者接收到消息后把日志存储在磁盘，

另外一个消费者接收到消息后把消息打印在屏幕上，事实上第一个程序发出的日志消息将广播给所有消费者

![image-20220302202312513](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302202312513.png) 

### Exchanges

`RabbitMQ` 消息传递模型的核心思想是: 生产者生产的消息从不会直接发送到队列。实际上，通常生产者甚至都不知道这些消息传递传递到了哪些队列中。

相反，生产者只能将消息发送到交换机（`exchange`），交换机工作的内容非常简单，一方面它接收来自生产者的消息，另一方面将它们推入队列。交换机必须确切知道如何处理收到的消息。是应该把这些消息放到特定队列，还是说把他们放到许多队列中，还是说应该丢弃它们。这就的由交换机的类型来决定。

![image-20220301174121772](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301174121772.png) 

### Exchanges的类型

总共有以下类型：直接(direct)、主题(topic)、标题(headers) 、扇出(fanout)

### 无名exchange

在本教程的前面部分我们对 `exchange` 一无所知，但仍然能够将消息发送到队列。之前能实现的原因是因为我们使用的是默认交换，我们通过空字符串(“”)进行标识。

```java
channel.basicPublish("", QUEUE_NAME, null, message.getBytes());
```

第一个参数是交换机的名称。空字符串表示默认或无名称交换机：消息能路由发送到队列中其实是由 `routingKey`（bindingkey）绑定 `key` 指定的。如果它存在的话

### 临时队列

之前的章节我们使用的是具有特定名称的队列（还记得 `hello` 和 `ack_queue` 吗？）。队列的名称我们来说至关重要，我们需要指定我们的消费者去消费哪个队列的消息。

每当我们连接到 `Rabbit` 时，我们都需要一个全新的空队列，为此我们可以创建一个具有随机名称的队列，或者能让服务器为我们选择一个随机队列名称那就更好了。

其次一旦我们断开了消费者的连接，队列将被自动删除。

创建临时队列的方式如下:

```java
String queueName = channel.queueDeclare().getQueue();
```

创建出来之后长成这样：

![image-20220301212647099](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220301212647099.png)

### 绑定（`bindings`）

什么是 `bingding` 呢，`binding` 其实是 `exchange` 和 `queue` 之间的桥梁，它告诉我们 `exchange` 和那个队列进行了绑定关系。

比如说下面这张图告诉我们的就是 `X` 与 `Q1` 和 `Q2` 进行了绑定:

![image-20220302092331732](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302092331732.png) 

### Fanout（扇出）

`Fanout` 这种类型非常简单。正如从名称中猜到的那样，它是将接收到的所有消息广播到它知道的所有队列中。

系统中默认有些 `exchange` 类型：

![image-20220302093008364](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302093008364.png) 

#### Fanout实战

![image-20220302093051957](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302093051957.png) 

Logs和临时队列的绑定关系如下图：

![image-20220302093106190](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302093106190.png) 

#### `ReceiveLogs01` 将接收到的消息打印在控制台

```java
public class ReceiveLogs01 {
    //交换机名称
    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        /**
         * 声明交换机
         *      参数1：交换机名称
         *      参数2：交换机类型
         */
        channel.exchangeDeclare(EXCHANGE_NAME, "fanout");
        /**
         * 生成一个临时队列，队列名称是随机的，当消费者断开和该队列的连接时，队列会自动删除
         */
        String queueName = channel.queueDeclare().getQueue();
        /**
         * 把该临时队列绑定我们的exchange 其中routingkey(也称之为binding key)为空字符串
         */
        channel.queueBind(queueName, EXCHANGE_NAME, "");
        System.out.println("等待接收消息，把消息打印在主屏幕......");
        //设置预取值为1
        channel.basicQos(1);
        channel.basicConsume(queueName,false,(consumerTag,message)->{
            System.out.println(new String(message.getBody()));
            //应答消息
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },(consumerTag)->{
            System.out.println("消费者取消消费："+consumerTag);
        });
    }
}
```

#### `ReceiveLogs02` 将接收到的消息存储在磁盘

```java
public class ReceiveLogs02 {
    //交换机名称
    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        //获取临时队列
        String queueName = channel.queueDeclare().getQueue();
        //声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME,"fanout");
        //绑定队列与交换机
        channel.queueBind(queueName,EXCHANGE_NAME,"");
        //设置预取值为1
        channel.basicQos(1);
        System.out.println("等待接收消息,把接收到的消息写到文件.....");
        //接收消息，设置手动应答
        channel.basicConsume(queueName,false,(consumerTag, message) -> {
            //将消息保存到文件
            BufferedWriter bufferedWriter = new BufferedWriter(new FileWriter(new File("E:\\IDEA_work\\RabbitMQ\\rabbitMQ-hello\\src\\main\\resources\\rabbitmq_info.txt"),true));
            bufferedWriter.write(new String(message.getBody()));
            bufferedWriter.newLine();
            System.out.println("写入成功："+new String(message.getBody()));
            bufferedWriter.flush();
            bufferedWriter.close();
            //应答消息
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消费："+consumerTag);
        });
    }
}
```

#### `EmitLog` 发送消息给两个消费者接收

```java
public class EmitLog {
    //交换机名称
    public static final String EXCHANGE_NAME = "logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        //声明交换机，生产者不需要绑定队列
        channel.exchangeDeclare(EXCHANGE_NAME,"fanout");
        //开启发布确认
        channel.confirmSelect();
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();
        //发布确认监听器
        channel.addConfirmListener((deliveryTag, multiple) -> {
            if (multiple){
                ConcurrentNavigableMap<Long, String> longStringConcurrentNavigableMap = outstandingConfirms.headMap(deliveryTag);
                longStringConcurrentNavigableMap.clear();
            }else {
                outstandingConfirms.remove(deliveryTag);
            }
        },(deliveryTag, multiple) -> {
            System.out.println("消费者取消消费消息："+outstandingConfirms.get(deliveryTag));
        });
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()){
            System.out.println("请输入消息内容：");
            String message = scanner.next();
            channel.basicPublish(EXCHANGE_NAME,"", MessageProperties.PERSISTENT_TEXT_PLAIN,message.getBytes());
            outstandingConfirms.put(channel.getNextPublishSeqNo(), message);
        }
    }
}
```

### Direct exchange（直接）

在上一节中，我们构建了一个简单的日志记录系统。我们能够向许多接收者广播日志消息。

在本节我们将向其中添加一些特别的功能，比方说我们只让某个消费者订阅发布的部分消息。例如我们只把严重错误消息定向存储到日志文件(以节省磁盘空间)，同时仍然能够在控制台上打印所有日志消息。

我们再次来回顾一下什么是 `bindings`，绑定是交换机和队列之间的桥梁关系。

也可以这么理解：队列只对它绑定的交换机的消息感兴趣。

绑定用参数：`routingKey` 来表示也可称该参数为 `binding key`，创建绑定我们用代码：`channel.queueBind(queueName, EXCHANGE_NAME, "routingKey");`绑定之后的意义由其交换类型决定。

#### Direct exchange介绍

上一节中的我们的日志系统将所有消息广播给所有消费者，对此我们想做一些改变;

例如我们希望将日志消息写入磁盘的程序仅接收严重错误（`errros`），而不存储哪些警告（`warning`）或信息（`info`）日志消息避免浪费磁盘空间。

`Fanout` 这种交换类型并不能给我们带来很大的灵活性，它只能进行无意识的广播，在这里我们将使用 `direct` 这种类型来进行替换，这种类型的工作方式是，消息只去到它绑定的 `routingKey` 队列中去。

![image-20220302152240639](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302152240639.png) 

在上面这张图中，我们可以看到 `X` 绑定了两个队列，绑定类型是 `direct` 。

队列 `Q1` 绑定键为 `orange`，队列 `Q2` 绑定键有两个：一个绑定键为 `black`，另一个绑定键为 `green`。

在这种绑定情况下，生产者发布消息到 `exchange` 上，绑定键为 `orange` 的消息会被发布到队列 `Q1` 。绑定键为 `black` 和 `green` 的消息会被发布到队列 `Q2`，其他消息类型的消息将被丢弃。

#### 多重绑定

![image-20220302152855348](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302152855348.png) 

当然如果 `exchange` 的绑定类型是 `direct`，但是它绑定的多个队列的 `key` 如果都相同，在这种情况下虽然绑定类型是 `direct` 但是它表现的就和 `fanout` 有点类似了，就跟广播差不多，如上图所示。

#### 实战

![image-20220302154120230](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302154120230.png) 

![image-20220302181520942](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302181520942.png) 

#### `ReceiveLogsDirect01` 接收 `routingkey` 为 `error` 的消息

```java
public class ReceiveLogsDirect01 {
    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        //声明交换机
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT,true);
        String queueName = "disk";
        //创建队列
        channel.queueDeclare(queueName,true,false,false,null);
        //绑定
        channel.queueBind(queueName,EXCHANGE_NAME,"error");
        //设置预取值
        channel.basicQos(1);
        System.out.println("等待接收消息......");
        channel.basicConsume(queueName,false,(consumerTag, message) -> {
            String str = "接收绑定键:"+message.getEnvelope().getRoutingKey()+",消息:"+new String(message.getBody());
            FileWriter fileWriter = new FileWriter(new File("E:\\IDEA_work\\RabbitMQ\\rabbitMQ-hello\\src\\main\\resources\\rabbitmq_info.txt"));
            fileWriter.write(str);
            fileWriter.flush();
            fileWriter.close();
            System.out.println("保存成功："+ str);
            //消息应答
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消费："+consumerTag);
        });
    }
}
```

#### `ReceiveLogsDirect02` 接收  `routingkey` 为 `info` 和 `warning`的消息

```java
public class ReceiveLogsDirect02 {
    public static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        String queueName = "console";
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT,true);
        channel.queueDeclare(queueName,true,false,false,null);
        channel.queueBind(queueName,EXCHANGE_NAME,"info");
        channel.queueBind(queueName,EXCHANGE_NAME,"warning");
        System.out.println("等待接收消息......");
        channel.basicConsume(queueName,false,(consumerTag, message) -> {
            System.out.println("接收到绑定键："+message.getEnvelope().getRoutingKey()+"，消息："+new String(message.getBody()));
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消费："+consumerTag);
        });
    }
}
```

#### `EmitLogDirect` 发送消息

```java
public class EmitLogDirect {
    private static final String EXCHANGE_NAME = "direct_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.confirmSelect();
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.DIRECT,true);
        Map<String,String> bindingKeyMap = new HashMap<>(3);
        bindingKeyMap.put("info","info警告信息");
        bindingKeyMap.put("warning","warning警告信息");
        bindingKeyMap.put("error","error错误信息");
        bindingKeyMap.put("debug","debug调试信息");
        //监听消息，进行发布确认
        channel.addConfirmListener((deliveryTag, multiple) -> {
            if (multiple){
                ConcurrentNavigableMap<Long, String> longStringConcurrentNavigableMap = outstandingConfirms.headMap(deliveryTag);
                longStringConcurrentNavigableMap.clear();
            }else {
                outstandingConfirms.remove(deliveryTag);
            }
        },(deliveryTag, multiple) -> {
            System.out.println("消费者取消消费消息："+outstandingConfirms.get(deliveryTag));
        });
        for (Map.Entry<String, String> stringStringEntry : bindingKeyMap.entrySet()) {
            String key = stringStringEntry.getKey();
            String value = stringStringEntry.getValue();
            channel.basicPublish(EXCHANGE_NAME,key, MessageProperties.PERSISTENT_TEXT_PLAIN,value.getBytes());
            outstandingConfirms.put(channel.getNextPublishSeqNo(),value);
            System.out.println("发出消息："+value);
        }
    }
}
```

### Topics（主题）

在上一个小节中，我们改进了日志记录系统。我们没有使用只能进行随意广播的 `fanout` 交换机，而是使用了 `direct` 交换机，从而有能实现有选择性地接收日志。

尽管使用direct交换机改进了我们的系统，但是它仍然存在局限性，比方说我们想接收的日志类型有 `info.base` 和 `info.advantage`，某个队列只想 `info.base` 的消息，那这个时候 `direct` 就办不到了。这个时候就只能使用 `topic` 类型

#### Topic的要求

发送到类型是topic交换机的消息的routing_key不能随意写，必须满足一定的要求：

- 它必须是一个单词列表，以点号分隔开。
- 这些单词可以是任意单词，比如说："stock.usd.nyse", "nyse.vmw", "quick.orange.rabbit".这种类型的。
- 当然这个单词列表最多不能超过255个字节。

在这个规则列表中，其中有两个替换符是大家需要注意的：

- *(星号)可以代替一个单词
- #(井号)可以替代零个或多个单词

#### Topic匹配案例

下图绑定关系如下

Q1-->绑定的是：

- 中间带orange带3个单词的字符串(\*.orange.\*)

Q2-->绑定的是：

- 最后一个单词是rabbit的3个单词(\*.\*.rabbit)
- 第一个单词是lazy的多个单词(lazy.#)

![image-20220302212356571](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302212356571.png) 

上图是一个队列绑定关系图，我们来看看他们之间数据接收情况是怎么样的

|           key            |                  被谁接收                  |
| :----------------------: | :----------------------------------------: |
|   quick.orange.rabbit    |              被队列Q1Q2接收到              |
|   lazy.orange.elephant   |              被队列Q1Q2接收到              |
|     quick.orange.fox     |               被队列Q1接收到               |
|      lazy.brown.fox      |               被队列Q2接收到               |
|     lazy.pink.rabbit     |    虽然满足两个绑定但只被队列Q2接收一次    |
|     quick.brown.fox      | 不匹配任何绑定不会被任何队列接收到会被丢弃 |
| quick.orange.male.rabbit |      是四个单词不匹配任何绑定会被丢弃      |
| lazy.orange.male.rabbit  |             是四个单词但匹配Q2             |

**当队列绑定关系是下列这种情况时需要引起注意**

- 当一个队列绑定键是 `#`，那么这个队列将接收所有数据，就有点像 `fanout` 了
- 如果队列绑定键当中没有 `#` 和 `*` 出现，那么该队列绑定类型就是 `direct` 了

#### 实战

![image-20220302213238211](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220302213238211.png) 

#### `EmitLogTopic` 发送消息

```java
public class EmitLogTopic {
    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC,true);
        channel.confirmSelect();
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();
        channel.addConfirmListener((deliveryTag, multiple) -> {
            if (multiple){
                ConcurrentNavigableMap<Long, String> longStringConcurrentNavigableMap = outstandingConfirms.headMap(deliveryTag);
                longStringConcurrentNavigableMap.clear();
            }else{
                outstandingConfirms.remove(deliveryTag);
            }
        },(deliveryTag, multiple) -> {
            String message = outstandingConfirms.get(deliveryTag);
            System.out.println("发布的消息"+message+"未被确认，序列号"+deliveryTag);
        });
        /**
         * Q1-->绑定的是
         *      中间带orange带3个单词的字符串(*.orange.*)
         * Q2-->绑定的是
         *      最后一个单词是rabbit的3个单词(*.*.rabbit)
         *      第一个单词是lazy的多个单词(lazy.#)
         */
        Map<String,String> bindingKeyMap = new HashMap<>();
        bindingKeyMap.put("quick.orange.rabbit","被队列Q1Q2接收到");
        bindingKeyMap.put("lazy.orange.elephant","被队列Q1Q2接收到");
        bindingKeyMap.put("quick.orange.fox","被队列Q1接收到");
        bindingKeyMap.put("lazy.brown.fox","被队列Q2接收到");
        bindingKeyMap.put("lazy.pink.rabbit","虽然满足两个绑定但只被队列Q2接收一次");
        bindingKeyMap.put("quick.brown.fox","不匹配任何绑定不会被任何队列接收到会被丢弃");
        bindingKeyMap.put("quick.orange.male.rabbit","是四个单词不匹配任何绑定会被丢弃");
        bindingKeyMap.put("lazy.orange.male.rabbit","是四个单词但匹配Q2");

        for (Map.Entry<String, String> stringStringEntry : bindingKeyMap.entrySet()) {
            channel.basicPublish(EXCHANGE_NAME,
                    stringStringEntry.getKey(),
                    MessageProperties.PERSISTENT_TEXT_PLAIN,
                    stringStringEntry.getValue().getBytes());
            outstandingConfirms.put(channel.getNextPublishSeqNo(),stringStringEntry.getValue());
            System.out.println("发送消息，键："+stringStringEntry.getKey()+" 值："+stringStringEntry.getValue());
        }
    }
}
```

#### `ReceiveLogsTopic01` 接收 `key` 与 `*.orange.*` 匹配的消息

```java
public class ReceiveLogsTopic01 {
    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        String queueName = "Q1";
        channel.basicQos(1);
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC,true);
        channel.queueDeclare(queueName,true,false,false,null);
        channel.queueBind(queueName,EXCHANGE_NAME,"*.orange.*");
        channel.basicConsume(queueName,false,(consumerTag, message) -> {
            String str = new String(message.getBody());
            System.out.println("接收队列:"+queueName+"绑定键:"+message.getEnvelope().getRoutingKey()+",消息:"+str);
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消费："+consumerTag);
        });
    }
}
```

#### `ReceiveLogsTopic02` 接收 `key` 与 `*.*.rabbit` 和 `lazy.#` 匹配的消息

```java
public class ReceiveLogsTopic02 {
    public static final String EXCHANGE_NAME = "topic_logs";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(EXCHANGE_NAME, BuiltinExchangeType.TOPIC,true);
        String queueName="Q2";
        channel.basicQos(1);
        channel.queueDeclare(queueName,true,false,false,null);
        channel.queueBind(queueName,EXCHANGE_NAME,"*.*.rabbit");
        channel.queueBind(queueName,EXCHANGE_NAME,"lazy.#");
        channel.basicConsume(queueName,false,(consumerTag, message) -> {
            String str = new String(message.getBody());
            System.out.println("接收队列:"+queueName+"绑定键:"+message.getEnvelope().getRoutingKey()+",消息:"+str);
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消息消费者取消消费："+consumerTag);
        });
    }
}
```

## 死信队列

先从概念解释上搞清楚这个定义，死信，顾名思义就是无法被消费的消息；

字面意思可以这样理解，一般来说，`producer` 将消息投递到 `broker` 或者直接到 `queue` 里了，`consumer` 从 `queue` 取出消息进行消费，但某些时候由于特定的原因导致 `queue` 中的某些消息无法被消费，这样的消息如果没有后续的处理，就变成了死信，有死信自然就有了死信队列。

应用场景：为了保证订单业务的消息数据不丢失，需要使用到 `RabbitMQ` 的死信队列机制，当消息消费发生异常时，将消息投入死信队列中；

还有比如说：用户在商城下单成功并点击去支付后在指定时间未支付时自动失效

### 死信的来源

- 消息TTL过期
- 队列达到最大长度(队列满了，无法再添加数据到mq中)
- 消息被拒绝(basic.reject或basic.nack)并且requeue=false.

### 死信实战

代码构图：

![image-20220303144228159](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303144228159.png) 

### TTL 消息过期

#### 生产者代码

```java
/**
 * 发送消息，并设置TTL过期时间
 */
public class Producer {
    //普通交换机名称
    private static final String NORMAL_EXCHANGE = "normal_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        channel.confirmSelect();
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();
        channel.addConfirmListener((deliveryTag, multiple) -> {
            if (multiple){
                ConcurrentNavigableMap<Long, String> longStringConcurrentNavigableMap = outstandingConfirms.headMap(deliveryTag);
                longStringConcurrentNavigableMap.clear();
            }else {
                outstandingConfirms.remove(deliveryTag);
            }
        },(deliveryTag, multiple) -> {
            System.out.println("消费者取消消费："+outstandingConfirms.get(deliveryTag));
        });
        /**
         * 设置消息的TTL时间,在发送消息时放到基本属性参数中
         */
        AMQP.BasicProperties basicProperties = new AMQP.BasicProperties().builder().expiration("10000").build();
        for (int i = 0; i < 10; i++) {
            String message = "info "+ i;
            channel.basicPublish(NORMAL_EXCHANGE,"zhangsan", basicProperties,message.getBytes());
            outstandingConfirms.put(channel.getNextPublishSeqNo(),message);
            System.out.println("发送消息："+message+" bundingKey：zhangsan");
        }
    }
}
```

#### 普通队列消费者代码

普通队列消费者默认接收到生产者发出的消息，<font color="red">在运行后马上关闭，模拟普通队列消费者接收不到消息的情况</font>

```java
/**
 * 接收普通交换机的消息
 */
public class Consumer01 {
    //普通交换机名称
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    //死信交换机名称
    private static final String DEAD_EXCHANGE = "dead_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        //声明普通交换机和死信交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        channel.exchangeDeclare(DEAD_EXCHANGE,BuiltinExchangeType.TOPIC,true);

        //声明死信队列，并绑定死信交换机
        String deadQueue = "dead-queue";
        channel.queueDeclare(deadQueue,true,false,false,null);
        channel.queueBind(deadQueue,DEAD_EXCHANGE,"lisi");

        /**
         * 正常队列绑定死信队列信息
         *      map中添加死信交换机名、死信队列routingKey的信息
         *      声明普通队列时，把记录是死信队列和key信息的 Map 放到最后一个参数
         * 这就完成了对死信队列的绑定
         */
        Map<String,Object> parems = new HashMap();
        parems.put("x-dead-letter-exchange",DEAD_EXCHANGE);
        parems.put("x-dead-letter-routing-key","lisi");
        //声明普通队列，绑定普通交换机
        String normalQueue = "normal-queue";
        //把记录是死信队列和key信息的 Map 放到最后一个参数
        channel.queueDeclare(normalQueue,true,false,false,parems);
        channel.queueBind(normalQueue,NORMAL_EXCHANGE,"zhangsan");
        channel.basicConsume(normalQueue,false,(consumerTag, message) -> {
            System.out.println("接收到消息："+new String(message.getBody()));
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消息接收："+consumerTag);
        });
    }
}
```

**生产者未发送消息：**

![image-20220303202047652](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303202047652.png) 

**生产者发送了十条消息：**

![image-20220303202153167](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303202153167.png) 

**普通消费者在 TTL 过期前未接收消息：**消息转到死信队列

![image-20220303202323674](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303202323674.png) 

#### 此时打开死信队列消费者，消费死信队列中的消息

```java
/**
 * 接收死信交换机的消息
 */
public class Consumer02 {
    //死信交换机名称
    private static final String DEAD_EXCHANGE = "dead_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        String deadQueue = "dead-queue";
        channel.queueBind(deadQueue,DEAD_EXCHANGE,"lisi");
        channel.basicConsume(deadQueue,false,(consumerTag, message) -> {
            System.out.println("接收到死信队列消息："+new String(message.getBody()));
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消费");
        });
    }
}
```

![image-20220303202511288](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303202511288.png) 

![image-20220303202541453](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303202541453.png) 

### 队列达到最大长度

#### 生产者中去掉 `TTL` 代码，直接发送消息到交换机

```java
/**
 * 发送消息
 */
public class Producer {
    //普通交换机名称
    private static final String NORMAL_EXCHANGE = "normal_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        channel.confirmSelect();
        ConcurrentSkipListMap<Long, String> outstandingConfirms = new ConcurrentSkipListMap<>();
        channel.addConfirmListener((deliveryTag, multiple) -> {
            if (multiple){
                ConcurrentNavigableMap<Long, String> longStringConcurrentNavigableMap = outstandingConfirms.headMap(deliveryTag);
                longStringConcurrentNavigableMap.clear();
            }else {
                outstandingConfirms.remove(deliveryTag);
            }
        },(deliveryTag, multiple) -> {
            System.out.println("消费者取消消费："+outstandingConfirms.get(deliveryTag));
        });

        for (int i = 0; i < 10; i++) {
            String message = "info "+ i;
            channel.basicPublish(NORMAL_EXCHANGE,"zhangsan",null,message.getBytes());
            outstandingConfirms.put(channel.getNextPublishSeqNo(),message);
            System.out.println("发送消息："+message+" bundingKey：zhangsan");
        }
    }
}
```

#### 普通队列消费者

在绑定信息的 `Map` 中添加最大长度 `parems.put("x-max-length",6);`，<font color="red">在运行后马上关闭，模拟普通队列消费者接收不到消息的情况</font>

```java
/**
 * 接收普通交换机的消息
 */
public class Consumer01 {
    //普通交换机名称
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    //死信交换机名称
    private static final String DEAD_EXCHANGE = "dead_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        //声明普通交换机和死信交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        channel.exchangeDeclare(DEAD_EXCHANGE,BuiltinExchangeType.TOPIC,true);

        //声明死信队列，并绑定死信交换机
        String deadQueue = "dead-queue";
        channel.queueDeclare(deadQueue,true,false,false,null);
        channel.queueBind(deadQueue,DEAD_EXCHANGE,"lisi");

        /**
         * 正常队列绑定死信队列信息
         *      map中添加死信交换机名、死信队列routingKey的信息
         *      声明普通队列时，把记录是死信队列和key信息的 Map 放到最后一个参数
         * 这就完成了对死信队列的绑定
         */
        Map<String,Object> parems = new HashMap();
        parems.put("x-dead-letter-exchange",DEAD_EXCHANGE);
        parems.put("x-dead-letter-routing-key","lisi");
        /**设置正常队列的长度**/
        parems.put("x-max-length",6);
        //声明普通队列，绑定普通交换机
        String normalQueue = "normal-queue";
        //把记录是死信队列和key信息的 Map 放到最后一个参数
        channel.queueDeclare(normalQueue,true,false,false,parems);
        channel.queueBind(normalQueue,NORMAL_EXCHANGE,"zhangsan");
        channel.basicConsume(normalQueue,false,(consumerTag, message) -> {
            System.out.println("接收到消息："+new String(message.getBody()));
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消息接收："+consumerTag);
        });
    }
}
```

可以看到，当普通队列消费者无法接收到消息后，其队列中仍然有 `6` 条消息。

![image-20220303214541298](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303214541298.png) 

#### 打开死信队列消费者，消费死信队列中的消息

```java
/**
 * 接收死信交换机的消息
 */
public class Consumer02 {
    //死信交换机名称
    private static final String DEAD_EXCHANGE = "dead_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        channel.exchangeDeclare(DEAD_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        String deadQueue = "dead-queue";
        channel.queueBind(deadQueue,DEAD_EXCHANGE,"lisi");
        channel.basicConsume(deadQueue,false,(consumerTag, message) -> {
            System.out.println("接收到死信队列消息："+new String(message.getBody()));
            channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
        },consumerTag -> {
            System.out.println("消费者取消消费");
        });
    }
}
```

打开死信队列消费者，也只能接收到四条消息

![image-20220303214640369](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303214640369.png) 

### 消息被拒

#### 消息生产者代码同上生产者一致

#### 普通消费者代码

设置 `channel.basicReject(message.getEnvelope().getDeliveryTag(), false);` 拒绝接收消息。

```java
/**
 * 接收普通交换机的消息
 */
public class Consumer01_2 {
    //普通交换机名称
    private static final String NORMAL_EXCHANGE = "normal_exchange";
    //死信交换机名称
    private static final String DEAD_EXCHANGE = "dead_exchange";

    public static void main(String[] args) throws IOException, TimeoutException {
        Channel channel = RabbitMqUtils.getChannel();
        //声明普通交换机和死信交换机
        channel.exchangeDeclare(NORMAL_EXCHANGE, BuiltinExchangeType.TOPIC,true);
        channel.exchangeDeclare(DEAD_EXCHANGE,BuiltinExchangeType.TOPIC,true);

        //声明死信队列，并绑定死信交换机
        String deadQueue = "dead-queue";
        channel.queueDeclare(deadQueue,true,false,false,null);
        channel.queueBind(deadQueue,DEAD_EXCHANGE,"lisi");

        /**
         * 正常队列绑定死信队列信息
         *      map中添加死信交换机名、死信队列routingKey的信息
         *      声明普通队列时，把记录是死信队列和key信息的 Map 放到最后一个参数
         * 这就完成了对死信队列的绑定
         */
        Map<String,Object> parems = new HashMap();
        parems.put("x-dead-letter-exchange",DEAD_EXCHANGE);
        parems.put("x-dead-letter-routing-key","lisi");
        //声明普通队列，绑定普通交换机
        String normalQueue = "normal-queue";
        //把记录是死信队列和key信息的 Map 放到最后一个参数
        channel.queueDeclare(normalQueue,true,false,false,parems);
        channel.queueBind(normalQueue,NORMAL_EXCHANGE,"zhangsan");
        channel.basicConsume(normalQueue,false,(consumerTag, message) -> {
            if (new String(message.getBody()).equals("info 5")){
                System.out.println("拒绝接收消息："+new String(message.getBody()));
                /**requeue设置为false 代表拒绝重新入队 该队列如果配置了死信交换机将发送到死信队列中**/
                channel.basicReject(message.getEnvelope().getDeliveryTag(), false);
            }else {
                System.out.println("接收到消息："+new String(message.getBody()));
                channel.basicAck(message.getEnvelope().getDeliveryTag(),false);
            }
        },consumerTag -> {
            System.out.println("消费者取消消息接收："+consumerTag);
        });
    }
}
```

当消息为 `info 5` 时，普通消费者拒绝接收消息，转到死信队列

 ![image-20220303220416892](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303220416892.png)

 ![image-20220303220437635](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303220437635.png)

#### 死信队列消费者代码不变，打开死信队列消费者，接收死信队列消息

![image-20220303221015394](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303221015394.png) 

![image-20220303220958883](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303220958883.png) 

## 延迟队列

延时队列，队列内部是有序的，最重要的特性就体现在它的延时属性上，延时队列中的元素是希望在指定时间到了以后或之前取出和处理；

简单来说，延时队列就是用来存放需要在指定时间被处理的元素的队列。

### 延迟队列使用场景

1. 订单在十分钟之内未支付则自动取消
2. 新创建的店铺，如果在十天内都没有上传过商品，则自动发送消息提醒。
3. 用户注册成功后，如果三天内没有登陆则进行短信提醒。
4. 用户发起退款，如果三天内没有得到处理则通知相关运营人员。
5. 预定会议后，需要在预定的时间点前十分钟通知各个与会人员参加会议

```
这些场景都有一个特点，需要在某个事件发生之后或者之前的指定时间点完成某一项任务；

如：发生订单生成事件，在十分钟之后检查该订单支付状态，然后将未支付的订单进行关闭；

看起来似乎使用定时任务，一直轮询数据，每秒查一次，取出需要被处理的数据，然后处理不就完事了吗？如果数据量比较少，确实可以这样做，

比如：对于 `”如果账单一周内未支付则进行自动结算“` 这样的需求，如果对于时间不是严格限制，而是宽松意义上的一周，那么每天晚上跑个定时任务检查一下所有未支付的账单，确实也是一个可行的方案。

但对于数据量比较大，并且时效性较强的场景，如：“订单十分钟内未支付则关闭“，短期内未支付的订单数据可能会有很多，活动期间甚至会达到百万甚至千万级别，对这么庞大的数据量仍旧使用轮询的方式显然是不可取的，很可能在一秒内无法完成所有订单的检查，同时会给数据库带来很大压力，无法满足业务要求而且性能低下。
```

![image-20220303221740023](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303221740023.png) 

### RabbitMQ中的TTL

`TTL` 是 `RabbitMQ` 中一个消息或者队列的属性，表明一条消息或者该队列中的所有消息的最大存活时间，

单位是毫秒。换句话说，如果一条消息设置了 `TTL` 属性或者进入了设置 `TTL` 属性的队列，那么这条消息如果在 `TTL` 设置的时间内没有被消费，则会成为 ”死信"。如果同时配置了队列的 `TTL` 和消息的 `TTL`，那么较小的那个值将会被使用，有两种方式设置 `TTL`。

#### 队列设置TTL

第一种是在创建队列的时候设置队列的“x-message-ttl”属性

![image-20220303222213426](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303222213426.png) 

#### 消息设置TTL

另一种方式便是针对每条消息设置TTL

![image-20220303222234987](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220303222234987.png) 

#### 两者的区别

如果设置了队列的 `TTL` 属性，那么一旦消息过期，就会被队列丢弃（如果配置了死信队列被丢到死信队列中）

而第二种方式，消息即使过期，也不一定会被马上丢弃，因为消息是否过期是在即将投递到消费者之前判定的，如果当前队列有严重的消息积压情况，则已过期的消息也许还能存活较长时间；另外，还需要注意的一点是，如果不设置 `TTL`，表示消息永远不会过期，如果将 `TTL` 设置为 `0`，则表示除非此时可以直接投递该消息到消费者，否则该消息将会被丢弃。

前一小节我们介绍了死信队列，刚刚又介绍了 `TTL`，至此利用 `RabbitMQ` 实现延时队列的两大要素已经集齐，接下来只需要将它们进行融合，再加入一点点调味料，延时队列就可以新鲜出炉了。想想看，延时队列，不就是想要消息延迟多久被处理吗，`TTL` 则刚好能让消息在延迟多久之后成为死信，另一方面，成为死信的消息都会被投递到死信队列里，这样只需要消费者一直消费死信队列里的消息就完事了，因为里面的消息都是希望被立即处理的消息。

## 整合springboot

### 创建SpringBoot项目

不需要勾选任何依赖

![image-20220304094510126](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304094510126.png) 

### 添加依赖

```xml
<dependencies>
        <!--SpringBoot集成RabbitMQ依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-amqp</artifactId>
        </dependency>
        <!--RabbitMQ测试依赖-->
        <dependency>
            <groupId>org.springframework.amqp</groupId>
            <artifactId>spring-rabbit-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!--swagger 依赖-->
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger2</artifactId>
            <version>2.9.2</version>
        </dependency>
        <dependency>
            <groupId>io.springfox</groupId>
            <artifactId>springfox-swagger-ui</artifactId>
            <version>2.9.2</version>
        </dependency>
        <!--JSON库依赖-->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
        </dependency>
        <!--lombok 依赖-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

### 配置RabbitMQ

在 `application.properties` 文件中添加如下配置

```properties
spring.rabbitmq.host=192.168.83.128
spring.rabbitmq.port=5672 
spring.rabbitmq.username=admin
spring.rabbitmq.password=admin
```

### 配置Swagger《这一块有问题》

```java
@SpringBootConfiguration
@EnableSwagger2
public class SwaggerConfig {

    @Bean
    public Docket webApiConfig(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("webApi")
                .apiInfo(webApiInfo())
                .select()
                .build();
    }

    private ApiInfo webApiInfo() {
        return new ApiInfoBuilder()
                .title("rabbitmq接口文档")
                .description("本文档描述了rabbitmq微服务接口定义")
                .version("1.0")
                .contact(new Contact("enjoy6288", "http://zym.com", "1551388580@qq.com"))
                .build();
    }
}
```

## 延迟队列TTL

### 代码架构图

创建两个队列 `QA` 和 `QB`，两者队列 `TTL` 分别设置为 `10S` 和 `40S`，然后在创建一个交换机 `X` 和死信交换机 `Y`，它们的类型都是 `direct`，创建一个死信队列 `QD`，它们的绑定关系如下：

![image-20220304103121570](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304103121570.png) 

### 队列配置类代码

队列、交换机的声明，绑定都在这里完成

```java
@SpringBootConfiguration
public class TtlQueueConfig {
    public static final String X_EXCHANGE = "X";
    public static final String Y_DEAD_LETTER_EXCHANGE = "Y";
    public static final String QUEUE_A = "QA";
    public static final String QUEUE_B = "QB";
    public static final String DEAD_LETTER_QUEUE = "QD";

    //声明交换机 X
    @Bean("xExchange")
    public TopicExchange xExchange(){
        return new TopicExchange(X_EXCHANGE,true,false);
    }
    //声明交换机 Y
    @Bean("yExchange")
    public TopicExchange yExchange(){
        return new TopicExchange(Y_DEAD_LETTER_EXCHANGE,true,false);
    }

    //声明队列A ttl为10s并绑定到对应的死信交换机
    @Bean("queueA")
    public Queue queueA(){
        Map<String,Object> map = new HashMap<>();
        //声明单亲队列绑定的死信交换机
        map.put("x-dead-letter-exchange",Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列绑定的死信 key
        map.put("x-dead-letter-routing-key","YD");
        //声明当前队列消息的 TTL
        map.put("x-message-ttl",10000);
        return QueueBuilder.durable(QUEUE_A).withArguments(map).build();
    }

    //queueA 队列绑定交换机 xExchange ，routingKey = "XA"
    @Bean
    public Binding queueaBindingX(@Qualifier("queueA") Queue queueA,
                                  @Qualifier("xExchange") TopicExchange xExchange){
        return BindingBuilder.bind(queueA).to(xExchange).with("XA");
    }
    
    //声明队列A ttl为10s并绑定到对应的死信交换机
    @Bean("queueB")
    public Queue queueB(){
        Map<String,Object> map = new HashMap<>();
        //声明单亲队列绑定的死信交换机
        map.put("x-dead-letter-exchange",Y_DEAD_LETTER_EXCHANGE);
        //声明当前队列绑定的死信 key
        map.put("x-dead-letter-routing-key","YD");
        //声明当前队列消息的 TTL
        map.put("x-message-ttl",40000);
        return QueueBuilder.durable(QUEUE_B).withArguments(map).build();
    }

    //queueB 队列绑定交换机 xExchange ，routingKey = "XB"
    @Bean
    public Binding queuebBindingX(@Qualifier("queueB") Queue queueB,
                                  @Qualifier("xExchange") TopicExchange xExchange){
        return BindingBuilder.bind(queueB).to(xExchange).with("XB");
    }

    //声明死信队列 queueD
    @Bean("queueD")
    public Queue queueD(){
        return new Queue(DEAD_LETTER_QUEUE,true,false,false);
    }

    //queueD 绑定死信队列 yExchange，routingKey = "YD"
    @Bean
    public Binding queuedBindingY(@Qualifier("queueD") Queue queueD,
                                  @Qualifier("yExchange") TopicExchange yExchange){
        return BindingBuilder.bind(queueD).to(yExchange).with("YD");
    }
}
```

### 消息生产者代码

通过 `rabbitTemplate` 发送消息

```java
@Slf4j
@RestController
@RequestMapping("ttl")
public class SendMsgController {

    @Autowired
    RabbitTemplate rabbitTemplate;

    @GetMapping("sendMsg/{message}")
    public void sendMsg(@PathVariable String message){
        log.info("当前时间："+new Date()+",发送一条信息给两个TTL队列:"+message);
        rabbitTemplate.convertAndSend("X","XA","消息来自ttl为10S的队列:"+message);
        rabbitTemplate.convertAndSend("X","XB","消息来自ttl为40S的队列:"+message);
    }
}
```

### 消息消费者代码

不写普通消息消费者代码，一段时间后，TTL 过期后，就会成为死信消息。

这时，死信队列消费者就会消费死信队列中的消息

```java
/**
 * 死信队列消费者
 */
@Slf4j
@Component
public class DeadLetterQueueConsumer {

    @RabbitListener(queues = "QD")
    public void receiveD(Message message, Channel channel){
        String msg = new String(message.getBody());
        log.info("当前时间："+new Date() + "接收到死信队列消息："+msg);
    }
}
```

### 发起请求

```
http://localhost:8080/ttl/sendMsg/你好啊
```

![image-20220304143736215](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304143736215.png) 

第一条消息在 `10S` 后变成了死信消息，然后被消费者消费掉，第二条消息在 `40S` 之后变成了死信消息，然后被消费掉，这样一个延时队列就打造完成了。

不过，如果这样使用的话，岂不是每增加一个新的时间需求，就要新增一个队列，这里只有 `10S` 和 `40S` 两个时间选项，如果需要一个小时后处理，那么就需要增加 `TTL` 为一个小时的队列，如果是预定会议室然后提前通知这样的场景，岂不是要增加无数个队列才能满足需求？

### 延时队列优化

#### 代码架构图

![image-20220304144228703](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304144228703.png) 

#### 队列配置类代码

队列 C 为普通队列，D 为死信队列

```java
@SpringBootConfiguration
public class TtlQueueConfig {
    public static final String X_EXCHANGE = "X";
    public static final String Y_DEAD_LETTER_EXCHANGE = "Y";
    public static final String QUEUE_A = "QA";
    public static final String QUEUE_B = "QB";
    public static final String QUEUE_C = "QC";
    public static final String DEAD_LETTER_QUEUE = "QD";

    //声明交换机 X
    @Bean("xExchange")
    public TopicExchange xExchange(){
        return new TopicExchange(X_EXCHANGE,true,false);
    }
    //声明死信交换机 Y
    @Bean("yExchange")
    public TopicExchange yExchange(){
        return new TopicExchange(Y_DEAD_LETTER_EXCHANGE,true,false);
    }

    //声明死信队列 queueD
    @Bean("queueD")
    public Queue queueD(){
        return new Queue(DEAD_LETTER_QUEUE,true,false,false);
    }

    //queueD 绑定死信队列 yExchange，routingKey = "YD"
    @Bean
    public Binding queuedBindingY(@Qualifier("queueD") Queue queueD,
                                  @Qualifier("yExchange") TopicExchange yExchange){
        return BindingBuilder.bind(queueD).to(yExchange).with("YD");
    }

    //创建队列C，不设置 TTL
    @Bean("queueC")
    public Queue queueC(){
        Map<String,Object> map = new HashMap<>();
        map.put("x-dead-letter-exchange",Y_DEAD_LETTER_EXCHANGE);
        map.put("x-dead-letter-routing-key","YD");
        return QueueBuilder.durable(QUEUE_C).withArguments(map).build();
    }

    //绑定队列 C 和交换机 X ，routingKey = XC
    @Bean
    public Binding queuecBindingX(@Qualifier("queueC") Queue queueC,
                                  @Qualifier("xExchange") TopicExchange xExchange){
        return BindingBuilder.bind(queueC).to(xExchange).with("XC");
    }
}
```

#### 消息生产者代码

```java
@Slf4j
@RestController
@RequestMapping("ttl")
public class SendMsgController {

    @Autowired
    RabbitTemplate rabbitTemplate;

    @GetMapping("sendTTLMessage/{message}/{ttlTime}")
    public void sendTTLMessage(@PathVariable String message, @PathVariable String  ttlTime){
        /**
         * 在最后的函数中设置消息的 TTL
         * setExpiration 是为 TTL 消息设置过期时间
         */
        rabbitTemplate.convertAndSend("X","XC","消息来自队列C："+message,correlationData ->{
            correlationData.getMessageProperties().setExpiration(ttlTime);
            return correlationData;
        });
        log.info("当前时间："+new Date()+",发送一条信息给非TTL队列:"+message+" 并设置消息的TTL时间为："+ttlTime+" ms");
    }
}
```

#### 消费者代码与之前系统

#### 发起请求

```
http://localhost:8080/ttl/sendTTLMessage/修沟/10000
http://localhost:8080/ttl/sendTTLMessage/修沟2/20000
```

![image-20220304175418262](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304175418262.png)

看起来似乎没什么问题，但是在最开始的时候，就介绍过如果使用在消息属性上设置TTL的方式，消息可能并不会按时“死亡“，因为RabbitMQ只会检查第一个消息是否过期，如果过期则丢到死信队列

如果第一个消息的延时时长很长，而第二个消息的延时时长很短，第二个消息并不会优先得到执行，他还是会等待前面的消息被消费后才能被消费。

### Rabbitmq插件实现延迟队列

上文中提到的问题，确实是一个问题，如果不能实现在消息粒度上的 `TTL`，并使其在设置的 `TTL` 时间及时死亡，就无法设计成一个通用的延时队列。那如何解决呢，接下来我们就去解决该问题。

#### 安装延时队列插件

官网（https://www.rabbitmq.com/community-plugins.html）下载插件：rabbitmq_delayed_message_exchange ![image-20220304165143402](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304165143402.png) 

然后解压放置到RabbitMQ的插件目录

执行下面命令让该插件生效，然后重启RabbitMQ

```
cd /usr/lib/rabbitmq//lib/rabbitmq_server-3.8.8/plugins
rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

![image-20220304165402884](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304165402884.png) 

![image-20220304165524381](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304165524381.png) 

#### 代码架构图

在这里新增了一个队列delayed.queue,一个自定义交换机delayed.exchange，绑定关系如下:

![image-20220304170129732](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304170129732.png) 

#### 队列配置类代码

```java
@SpringBootConfiguration
public class pluginDelayQueue {
    public static final String DELAYED_QUEUE_NAME = "delayed.queue";
    public static final String DELAYED_EXCHANGE_NAME = "delayed.exchange";
    public static final String DELAYED_ROUTING_KEY = "delayed.routingkey";

    //创建队列
    @Bean("delayedQueue")
    public Queue delayedQueue(){
        return new Queue(DELAYED_QUEUE_NAME,true,false,false);
    }

    //声明交换机
    @Bean("delayedExchange")
    public CustomExchange delayedExchange(){
        Map<String, Object> args = new HashMap<>();
        //自定义交换机的类型
        args.put("x-delayed-type", "direct");
        return new CustomExchange(DELAYED_EXCHANGE_NAME,"x-delayed-message",true,false,args);
    }

    //绑定 delayedQueue 队列与 delayedExchange 交换机，routingkey = DELAYED_ROUTING_KEY
    @Bean
    public Binding bindingQE(@Qualifier("delayedQueue") Queue queue,
                             @Qualifier("delayedExchange") CustomExchange exchange){
        return BindingBuilder.bind(queue).to(exchange).with(DELAYED_ROUTING_KEY).noargs();
    }
}
```

#### 消息生产者代码

在使用插件的延迟队列中，我们直接将消息发送到延迟队列，不需要先发送到普通队列后再等待消息过期

```java
@Slf4j
@RestController
@RequestMapping("ttl")
public class SendMsgController {

    @Autowired
    RabbitTemplate rabbitTemplate;

    @GetMapping("sendDelayMsg/{message}/{delayTime}")
    public void sendMessage(@PathVariable String message, @PathVariable Integer  delayTime){
        rabbitTemplate.convertAndSend("delayed.exchange","delayed.routingkey",message,correlationData -> {
            /**
             * setDelay 是为使用延迟队列插件的队列设置延迟时间
             */
            correlationData.getMessageProperties().setDelay(delayTime);
            return correlationData;
        });
        log.info("当前时间："+new Date()+",发送一条信息给非TTL队列:"+message+" 并设置消息的TTL时间为："+delayTime+" ms");
    }
}
```

#### 消息消费者代码

```java
@Slf4j
@Component
public class DeadLetterQueueConsumer {

    @RabbitListener(queues = "delayed.queue")
    public void receiveDelayedQueue(Message message, Channel channel){
        String msg = new String(message.getBody());
        log.info("当前时间："+new Date() + "接收到延迟队列消息："+msg);
    }
}
```

#### 发起请求

```
http://localhost:8080/ttl/sendDelayMsg/修沟2/20000
http://localhost:8080/ttl/sendDelayMsg/修沟/10000
```

![image-20220304180331079](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220304180331079.png) 

可以发现，在我们发送需要等待 20 秒的消息后，在发送需要等待 10 秒的消息，10 秒的消息会在 10 延迟到 10 秒后直接被消费，不管他前面的队列是否被消费

### 总结

延时队列在需要延时处理的场景下非常有用，使用 `RabbitMQ` 来实现延时队列可以很好的利用 `RabbitMQ` 的特性

如：消息可靠发送、消息可靠投递、死信队列来保障消息至少被消费一次以及未被正确处理的消息不会被丢弃。

另外，通过 `RabbitMQ` 集群的特性，可以很好的解决单点故障问题，不会因为单个节点挂掉导致延时队列不可用或者消息丢失。
当然，延时队列还有很多其它选择，比如利用 `Java` 的 `DelayQueue`，利用 `Redis` 的 `zset`，利用 `Quartz` 或者利用 `kafka` 的时间轮，这些方式各有特点,看需要适用的场景

## 发布确认高级

在生产环境中由于一些不明原因，导致 `rabbitmq` 重启，在 `RabbitMQ` 重启期间生产者消息投递失败，导致消息丢失，需要手动处理和恢复。

于是，我们开始思考，如何才能进行 `RabbitMQ` 的消息可靠投递呢？特别是在这样比较极端的情况，`RabbitMQ` 集群不可用的时候，无法投递的消息该如何处理呢：

### 发布确认的springboot版本

#### 确认机制方案

![image-20220307093118503](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220307093118503.png) 

#### 代码架构图

![image-20220307093150204](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220307093150204.png) 

#### 配置文件

在配置文件当中需要添加：`spring.rabbitmq.publisher-confirm-type=correlated`

- NONE
  禁用发布确认模式，是默认值

- CORRELATED
  发布消息成功到交换器后会触发回调方法

- SIMPLE

  经测试有两种效果：

  其一效果和 `CORRELATED`值一样会触发回调方法，

  其二在发布消息成功后使用 `rabbitTemplate` 调用 `waitForConfirms` 或 `waitForConfirmsOrDie` 方法 等待 `broker` 节点返回发送结果，根据返回结果来判定下一步的逻辑，要注意的点是 `waitForConfirmsOrDie` 方法如果返回 `false` 则会关闭 `channel`，则接下来无法发送消息到 `broker`

```properties
spring.rabbitmq.host=192.168.83.128
spring.rabbitmq.port=5672 
spring.rabbitmq.username=admin
spring.rabbitmq.password=admin
spring.rabbitmq.publisher-confirm-type=correlated
```

#### 添加配置类

```java
@SpringBootConfiguration
public class ConfirmConfig {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    public static final String CONFIRM_QUEUE_NAME = "confirm.queue";

    @Bean("confirmExchange")
    public CustomExchange confirmExchange(){
        Map<String, Object> args = new HashMap<>();
        //自定义交换机的类型
        args.put("x-delayed-type", "direct");
        return new CustomExchange(CONFIRM_EXCHANGE_NAME,"x-delayed-message",false,false,args);
    }

    @Bean("confirmQueue")
    public Queue confirmQueue(){
        return QueueBuilder.durable(CONFIRM_QUEUE_NAME).build();
    }

    @Bean
    public Binding eBindingQ(@Qualifier("confirmExchange") CustomExchange confirmExchange,
                             @Qualifier("confirmQueue") Queue confirmQueue){
        return BindingBuilder.bind(confirmQueue).to(confirmExchange).with("key1").noargs();
    }

}
```

#### 编写回调接口

```java
@Slf4j
@Component
public class MyCallBack implements RabbitTemplate.ConfirmCallback {
    /**
     * 交换机不管是否接收到消息都回调这个方法
     * CorrelationData  消息相关的数据
     * ack  是否接收到消息，true为接收到消息
     */
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id=correlationData!=null?correlationData.getId():"";
        if(ack){
            log.info("交换机已经收到id为:{}的消息",id);
        }else{
            log.info("交换机还未收到id为:{}消息,由于原因:{}",id,cause);
        }
    }
}
```

#### 编写生产者代码

```java
@Slf4j
@Controller
public class Producer {

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Autowired
    private MyCallBack myCallBack;
    @PostConstruct
    public void init(){
        rabbitTemplate.setConfirmCallback(myCallBack);
    }
    @GetMapping("sendMessage/{message}")
    public void sendMessage(@PathVariable String message){
        CorrelationData correlationData1 = new CorrelationData(UUID.randomUUID().toString());
        String routingKey="key1";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME,routingKey,message+routingKey,correlationData -> {
            /**
             * setDelay 是为使用延迟队列插件的队列设置延迟时间
             */
            correlationData.getMessageProperties().setDelay(10000);
            return correlationData;
        },correlationData1);

        CorrelationData correlationData2=new CorrelationData(UUID.randomUUID().toString());
        String routingKey2="key2";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME,routingKey2,message+routingKey,correlationData2);
        log.info("发送消息内容:{}",message);
    }
}

```

#### 编写消费者代码

```java
@Slf4j
@Component
public class DeadLetterQueueConsumer {

    @RabbitListener(queues = "confirm.queue")
    public void receiveMsg(Message message, Channel channel){
        String msg = new String(message.getBody());
        log.info("当前时间："+new Date() + "接收到队列消息："+msg);
    }
}
```

#### 发起请求

`http://localhost:8080/sendMessage/哈哈`

![image-20220307111554062](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220307111554062.png)

可以看到，发送了两条消息，第一条消息的 RoutingKey 为 "key1"，第二条消息的 RoutingKey 为 "key2"，两条消息都成功被交换机接收，也收到了交换机的确认回调。

但消费者只收到了一条消息，因为第二条消息的 RoutingKey 与队列的 BindingKey 不一致，也没有其它队列能接收这个消息，所以第二条消息被直接丢弃了。

### 回退消息

#### Mandatory参数

在仅开启了生产者确认机制的情况下，交换机接收到消息后，会直接给消息生产者发送确认消息，如果发现该消息不可路由，那么消息会被直接丢弃，此时生产者是不知道消息被丢弃这个事件的。

那么如何让无法被路由的消息帮我想办法处理一下？最起码通知我一声，我好自己处理啊。通过设置 `mandatory` 参数可以在当消息传递过程中不可达目的地时将消息返回给生产者。

#### 消息生产者代码

本次两个回调接口的实现也可以和生产者分开写，例如上一步写到专门的回调接口实现类中。

```java
@Slf4j
@Controller
public class Producer implements RabbitTemplate.ConfirmCallback,RabbitTemplate.ReturnsCallback{

    public static final String CONFIRM_EXCHANGE_NAME = "confirm.exchange";
    @Autowired
    private RabbitTemplate rabbitTemplate;
    //设置回调对象
    @PostConstruct
    public void init(){
        rabbitTemplate.setConfirmCallback(this);
        /**
         * true：
         * 交换机无法将消息进行路由时，会将该消息返回给生产者
         * false：
         * 如果发现消息无法进行路由，则直接丢弃
         */
         rabbitTemplate.setMandatory(true);
         //设置回退消息交给谁处理
         rabbitTemplate.setReturnsCallback(this);
    }
    
    //发送消息
    @GetMapping("sendMessage/{message}")
    public void sendMessage(@PathVariable String message){
        CorrelationData correlationData1 = new CorrelationData(UUID.randomUUID().toString());
        String routingKey="key1";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME,routingKey,message+routingKey,correlationData1);
        log.info("发送消息id为:{}内容为{}",correlationData1.getId(),message+"key1");

        CorrelationData correlationData2=new CorrelationData(UUID.randomUUID().toString());
        String routingKey2="key2";
        rabbitTemplate.convertAndSend(CONFIRM_EXCHANGE_NAME,routingKey2,message+routingKey,correlationData2);
        log.info("发送消息id为:{}内容为{}",correlationData2.getId(),message+"key2");
    }

    //消息确认回调
    @Override
    public void confirm(CorrelationData correlationData, boolean ack, String cause) {
        String id=correlationData!=null?correlationData.getId():"";
        if(ack){
            log.info("交换机已经收到id为:{}的消息",id);
        }else{
            log.info("交换机还未收到id为:{}消息,由于原因:{}",id,cause);
        }
    }

    /**
     * 处理回退消息的回调
     */
    @Override
    public void returnedMessage(ReturnedMessage returned) {
        log.info(returned.getReplyCode()+"消息:{}被服务器退回，退回原因:{}, 交换机是:{}, 路由key:{}", new String(returned.getMessage().getBody()),returned.getReplyText(), returned.getExchange(), returned.getRoutingKey());
    }
}
```

#### 消费者代码与上一步相同，都是接收 `confirm.queue` 队列的消息

### 备份交换机

有了` mandatory` 参数和回退消息，我们获得了对无法投递消息的感知能力，有机会在生产者的消息无法被投递时发现并处理。

但有时候，我们并不知道该如何处理这些无法路由的消息，最多打个日志，然后触发报警，再来手动处理。

而通过日志来处理这些无法路由的消息是很不优雅的做法，特别是当生产者所在的服务有多台机器的时候，手动复制日志会更加麻烦而且容易出错。而且设置 `mandatory` 参数会增加生产者的复杂性，需要添加处理这些被退回的消息的逻辑。

如果既不想丢失消息，又不想增加生产者的复杂性，该怎么做呢？

前面在设置死信队列的文章中，我们提到，可以为队列设置死信交换机来存储那些处理失败的消息，可是这些不可路由消息根本没有机会进入到队列，因此无法使用死信队列来保存消息。在RabbitMQ中，有一种备份交换机的机制存在，可以很好的应对这个问题。

什么是备份交换机呢？

备份交换机可以理解为 `RabbitMQ` 中交换机的“备胎”，当我们为某一个交换机声明一个对应的备份交换机时，就是为它创建一个备胎，当交换机接收到一条不可路由消息时，将会把这条消息转发到备份交换机中，由备份交换机来进行转发和处理，通常备份交换机的类型为 `Fanout `，这样就能把所有消息都投递到与其绑定的队列中，然后我们在备份交换机下绑定一个队列，这样所有那些原交换机无法被路由的消息，就会都进入这个队列了。当然，我们还可以建立一个报警队列，用独立的消费者来进行监测和报警。

#### 代码架构图

![image-20220307134332639](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220307134332639.png) 

