# 注册中心zookeeper

## 概述

​		对于服务提供方，它需要发布服务，服务的数量，类型也不断膨胀；对于消费方，它关心如何霍获取它所需要的服务，而面对复杂的应用系统，需要管理大量的服务调用

​		对于服务的提供者和消费者来说，他们还可能兼具这两种角色，即一个模块即需要提供服务，也需要消费服务。通过服统一管理起来，可以有效的优化内部应用对服务的发布和使用流程的流程和管理。服务注册中心可以通过特定的协议来完成服务的对外统一。

## zookeeper

zookeeper（简称：zk）是一个高性能，分布式，开放源码的分布式应用程序协调服务，可以理解成windows的注册表，他是一个属性结构，这种树形结构和标准文件系统相似，zookeeper中的每个节点被称为Zonde，和文件系统的目录树一样，zookeeper树中的每个节点都能拥有子节点，每个节点表示一个唯一的服务资源。zookeeper的运行需要java环境

官网：[https://zookeeper.apache.org/](https://zookeeper.apache.org/)

## zookeeper的安装

### 下载

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714115222465.png" alt="image-20210714115222465"  />

![image-20210714115332424](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714115332424.png)

![image-20210714115543387](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714115543387.png)

**下载的压缩包在windows和linux系统都能使用**

### 解压

![image-20210714115843487](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714115843487.png)

### 配置文件

![image-20210714115952749](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714115952749.png)

将默认配置复制，并改名为：zoo.cnf

![image-20210714121009730](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714121009730.png)

重要的配置信息

```text
# The number of milliseconds of each tick（每隔2000毫秒发个消息）
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take（初始化连接大小）
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement（同步的大小）
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.（临时数据存放的文件夹）
dataDir=/tmp/zookeeper
# the port at which the clients will connect（zookeeper服务的端口号，默认为2181,admin.serverPort的默认端口号是8080，需要使用tomcat，就修改为8888）
clientPort=2181
admin.serverPort=8888
```

### 启动服务

![image-20210714121225906](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20210714121225906.png)

# zookeeper的使用

