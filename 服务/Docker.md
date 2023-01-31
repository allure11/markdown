# Docker 安装

cantos 安装文档：https://docs.docker.com/engine/install/centos/

## 安装 gcc 相关

```shell
[root@ecs-352864 soft]# yum -y install gcc
[root@ecs-352864 soft]# yum -y install gcc-c++
```

## 安装 yum-utils 并设置稳定存储库

```shell
[root@ecs-352864 soft]# yum install -y yum-utils
# 官方使用存储库为 https://download.docker.com/linux/centos/docker-ce.repo ，这里我们使用 aliyun 的存储库
[root@ecs-352864 soft]# yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

## 安装 Docker

```shell
[root@ecs-352864 soft]# yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
# 此命令为安装docker，但不会启动 Docker。它还会创建一个 docker组，但是默认情况下它不会将任何用户添加到该组中
```

## 启动 Docker

```shell
[root@ecs-352864 soft]# systemctl start docker
# 验证docker是否正确安装
[root@ecs-352864 soft]# docker run hello-world
```

## 创建配置文件，使用阿里云镜像加速

参考：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors

```shell
mkdir -p /etc/docker

tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://4jb3frsm.mirror.aliyuncs.com"]
}
EOF

systemctl daemon-reload

systemctl restart docker
```

# 常用命令

```
启动docker： systemctl start docker
停止docker： systemctl stop docker
重启docker： systemctl restart docker
查看docker状态： systemctl status docker
开机启动： systemctl enable docker
查看docker概要信息： docker info
查看docker总体帮助文档： docker --help
查看docker命令帮助文档： docker 具体命令 --help
```

> docker images ：列出本地主机上的镜像
>
> > -a :列出本地所有的镜像（含历史映像层）
> > -q :只显示镜像ID。
>
> docker search 某个XXX镜像名字：列出镜像
>
> > --limit : 只列出N个镜像，默认25个
> > 例如：docker search --limit 5 redis
>
> docker pull 某个XXX镜像名字 ：下载镜像
>
> > docker pull 镜像名字[:TAG]
> > 没有TAG就是最新版
> > 等价于
> > docker pull 镜像名字:latest
> > docker pull ubuntu
>
> docker system df ：查看镜像/容器/数据卷所占的空间
>
> docker rmi 某个XXX镜像名字ID ：删除镜像
>
> > 删除单个
> > 		docker rmi  -f 镜像ID
> >
> > 删除多个
> > 		docker rmi -f 镜像名1:TAG 镜像名2:TAG 
> >
> > 删除全部
> > 		docker rmi -f $(docker images -qa)
>
> docker ps [OPTIONS]：列出当前所有正在运行的容器
>
> > -a :列出当前所有正在运行的容器+历史上运行过的
> >
> > -l :显示最近创建的容器。
> >
> > -n：显示最近n个创建的容器。
> >
> > -q :静默模式，只显示容器编号。
>
> docker start 容器ID或者容器名 ：启动容器
>
> docker restart 容器ID或者容器名：重启容器
>
> docker stop 容器ID或者容器名 ：停止容器
>
> docker kill 容器ID或容器名：强制停止容器
>
> docker rm 容器ID：删除停止的容器
>
> > 一次性删除多个容器实例
> > docker rm -f $(docker ps -a -q)
> > docker ps -a -q | xargs docker rm

## 示例

下载 Ubuntu 镜像：` [root@ecs-352864 ~]# docker pull ubuntu `

运行并进入容器：` [root@ecs-352864 ~]# docker run -it ubuntu /bin/bash `

![image-20220517125659810](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517125659810.png)

退出容器：`exit ` 和 `ctrl+p+q` 快捷键

````shell
# run进去容器， exit退出，容器停止
root@53d1e211af38:/# exit 
# run进去容器，ctrl+p+q退出，容器不停止
ctrl+p+q 
````

## 操作示例

下载 redis：` [root@ecs-352864 ~]# docker pull redis `

运行容器：

​		后台启动：` [root@ecs-352864 ~]# docker run -d redis `

​		前台启动：` [root@ecs-352864 ~]# docker run -it redis `

查看容器日志：` docker logs 容器ID `

![image-20220517132241703](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517132241703.png)

查看容器内运行的进程：` docker top 容器ID `

![image-20220517132359003](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517132359003.png)

查看容器内部细节：` docker inspect 容器ID `

![image-20220517132513321](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517132513321.png)

## 和正在运行的容器交互

进入容器：` docker exec -it 容器ID [bashShell] `
重新进入：` docker attach 容器ID `

> 案例演示，用centos或者unbuntu都可以
>
> [root@ecs-352864 ~]# docker exec -it 53d1e211af38 /bin/bash
>
> [root@ecs-352864 ~]# docker attach 53d1e211af38 

上述两个区别
		attach 直接进入容器启动命令的终端，不会启动新的进程，用exit退出，会导致容器的停止。
		exec 是在容器中打开新的终端，并且可以启动新的进程，用exit退出，不会导致容器的停止。

推荐大家使用 docker exec 命令，因为退出容器终端，不会导致容器的停止。

> 用之前的redis容器实例进入试试
> 		进入redis服务
> 				docker exec -it 容器ID /bin/bash
>
> <center><img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517134741072.png"/></center>
>
> ​				docker exec -it 容器ID redis-cli
>
> ![image-20220517134833001](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517134833001.png)
>
> 一般用 -d 后台启动的程序，再用exec进入对应容器实例

从容器拷贝文件到主机上：`docker cp  容器ID:容器内路径 目的主机路径`

```shell
[root@ecs-352864 ~]# docker cp ffdb0668985a:/etc/init.d redisinit.d
```

![image-20220517141920182](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517141920182.png)

## 导入和导出容器

export：导出容器的内容留作为一个tar归档文件	[对应import命令]

import：从tar包中的内容创建一个新的文件系统再导入为镜像	[对应export]

案例：

​		docker export 容器ID > 文件名.tar

![image-20220517144132132](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517144132132.png)

​		docker import 导出的容器tar包 镜像名称:版本

![image-20220517144153206](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517144153206.png)

## commit 操作

docker commit 提交容器副本使之成为一个新的镜像

语法：` docker commit -m="提交的描述信息" -a="作者" 容器ID 要创建的目标镜像名:[标签名] `

`docker commit -m "测试提交" -a "zym" 7428cac562d5 my/ubuntu:v0.0.1`

![image-20220517174434230](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517174434230.png)

## 容器跟随 docker 服务自动启动

部署项目服务器时，为了应对停电等情况影响正常web项目的访问，会把Docker容器设置为开机自动启动。

- 在使用docker run启动容器时，使用--restart参数来设置：

```
# docker run -m 512m --memory-swap 1G -it -p 58080:8080 --restart=always 
--name bvrfis --volumes-from logdata mytomcat:4.0 /root/run.sh
```

> restart具体参数值详细信息： 
>
> ​	no - 容器退出时，不重启容器；
>
> ​	on-failure - 只有在非0状态退出时才从新启动容器；
>
> ​	always - 无论退出状态是如何，都重启容器；

- 还可以在使用 on-failure 策略时，指定 Docker 将尝试重新启动容器的最大次数。默认情况下，Docker 将尝试永远重新启动容器。

```
docker run --restart=on-failure:10 redis
```

- 如果创建时未指定 --restart=always ,可通过 update 命令

```
docker update --restart=always redis
```

## 将镜像推送到阿里云镜像库

参考：https://cr.console.aliyun.com/repository/cn-hangzhou/zym_test/test01/details

![image-20220517180820087](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517180820087.png)

![image-20220517180726055](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517180726055.png)

## 将阿里云上的镜像下载到本地

![image-20220517181540492](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220517181540492.png)

## 发布镜像到私有库

可参考：http://t.zoukankan.com/ztone-p-10582195.html

安装私有库：

```shell
# 下载镜像 Docker Registry
[root@ecs-352864 ~]# docker pull registry
# 运行 Docker Registry 
[root@ecs-352864 ~]# docker run -d --name=registry -p 5000:5000 -v /tmp/registry:/var/lib/registry --privileged=true registry
```

使用 commit 创建新的镜像（ubuntu 安装 ifconfig 命令）

​		进入 ubuntu ，安装 ifconfig ：

```shell
# 更新 apt-get
root@916bb3085166:/# apt-get update
# 安装 net-tools 命令
root@916bb3085166:/# apt-get install net-tools
```

将安装 net-tools 的 ubuntu 打包成镜像

```shell
[root@ecs-352864 ~]# docker commit -m "安装ifconfig" -a "zym" 916bb3085166 my/ubuntu:v0.0.1
```

查看私服上的镜像：` [root@ecs-352864 ~]# curl -XGET http://127.0.0.1:5000/v2/_catalog `

修改 docker 配置，让 docker 信任 registry 地址

```shell
[root@ecs-352864 ~]# vi /etc/docker/daemon.json 

# 添加 "insecure-registries":["121.37.217.252:5000"]
{
  "registry-mirrors": ["https://4jb3frsm.mirror.aliyuncs.com"],
  "insecure-registries":["121.37.217.252:5000"]
}

# 重启 docker,重启后需要重新运行 registry
[root@ecs-352864 ~]# vi /etc/docker/daemon.json 
```

将镜像打包成 tag

```shell
#  docker tag 镜像名称:Tag   镜像库IP:镜像库Port/镜像库中的名称:Tag
[root@ecs-352864 ~]# docker tag my/ubuntu:v0.0.1 121.37.217.252:5000/my/ubuntu:v0.0.2
```

将 tag 推送到私有库

```shell
[root@ecs-352864 ~]# docker push 121.37.217.252:5000/my/ubuntu:v0.0.1
```

![image-20220518124855997](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518124855997.png)

在次查看私有库中的镜像：

![image-20220518124951338](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518124951338.png)

拉取私服中的镜像并运行：

```shell
# 拉取镜像
[root@ecs-352864 ~]# docker pull 121.37.217.252:5000/my/ubuntu:v0.0.1
# 运行
[root@ecs-352864 ~]# docker run -it 89cc1e6ec767 bash
```

![image-20220518125507159](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518125507159.png)

# 容器数据卷

将docker容器内的数据保存进宿主机的磁盘中

运行一个带有容器卷存储功能的容器实例，通过 run 命令的参数 -v 进行添加

docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录      镜像名

<font color=red>Docker 挂载主机目录 Docker 访问出现 cannot open directory : Permission denied 解决办法：在挂载目录后多加一个 --privileged=true 参数即可</font>

查看数据卷是否挂载成功：` docker inspect 容器ID  `

![image-20220518130632526](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518130632526.png)

## 作用

将运用与运行的环境打包镜像，run后形成容器实例运行 ，但是我们对数据的要求希望是持久化的 Docker容器产生的数据，如果不备份，那么当容器实例删除后，容器内的数据自然也就没有了。

为了能保存数据在docker中我们使用卷。 

特点：

> 数据卷可在容器之间共享或重用数据
>
> 卷中的更改可以直接实时生效，爽
>
> 数据卷中的更改不会包含在镜像的更新中
>
> 数据卷的生命周期一直持续到没有容器使用它为止 

## docker 和主机之间的数据共享流程：

> docker修改，主机同步获得 
>
> 主机修改，docker同步获得
>
> docker容器stop，主机修改
>
> docker容器重启看数据是否同步。

## 读写规则映射添加说明

读写(默认)
```shell
# 默认同上案例，默认就是 rw
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:rw 镜像名
```

只读
```shell
# 容器实例内部被限制，只能读取不能写 ro
docker run -it --privileged=true -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
```

## 卷的继承和共享

容器1完成和宿主机的映射

```shell
docker run -it --privileged=true -v /mydocker/u:/tmp --name u1 ubuntu 
```

容器2继承容器1的卷规则

```shell
docker run -it  --privileged=true --volumes-from 父类  --name u2 ubuntu
```

# 常规软件的安装

总体步骤：搜索镜像 -- > 拉取镜像 -- > 查看镜像 -- > 启动镜像（服务端口映射）-- > 停止容器 -- > 移除容器

## 安装 tomcat

查找 tomcat 镜像：` [root@ecs-352864 ~]# docker search tomcat `

![image-20220518134436236](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518134436236.png)

拉取 tomcat 镜像：`[root@ecs-352864 ~]# docker pull tomcat`

![image-20220518134953545](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518134953545.png)

查看是否拉取成功：`[root@ecs-352864 ~]# docker images`

![image-20220518135240723](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518135240723.png)

使用tomcat镜像创建容器实例（运行镜像）：

```shell
[root@ecs-352864 test]# docker run -d -v /tmp/docker/tomcat/webapps:/usr/local/tomcat/webapps -p 8080:8080 --name=tomcat tomcat
```

参数：

> -p：小写，主机端口:docker容器端口
>
> -P：大写，随机分配端口
>
> i：交互
>
> t：终端
>
> d：后台
>
> v：挂载数据卷
>
> --name：指定容器名

<font color=red>注意：直接通过镜像运行 tomcat 得到的容器中 webapps 目录是空的，直接访问没有内容会报 404</font>

可以下载非官方的，有初始内容的免修改版：`docker pull billygoo/tomcat8-jdk8`

![image-20220518142336631](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518142336631.png)

在数据卷挂载目录添加文件后进行访问：

![image-20220518142455296](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518142455296.png)

![image-20220518142652897](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518142652897.png)

## 安装 mysql

可参考：https://hub.docker.com/_/mysql

查找 mysql 镜像：`[root@ecs-352864 ~]# docker search mysql`

拉取 mysql 8.0 版本的镜像：`[root@ecs-352864 ~]# docker pull mysql:8.0`

运行镜像，生成容器：

```shell
[root@ecs-352864 mysql]# docker run -d --name mysql8.0 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=zxc001 -v /tmp/docker/mysql/conf:/etc/mysql/conf.d -v /tmp/docker/mysql/data:/var/lib/mysql -v /tmp/docker/mysql/logs:/var/log/mysql mysql
```

> 登录报错：ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
>
> 使用命令 `/usr/bin/mysql_secure_installation` 根据提示进行操作 

## 安装redis

查找 redis 镜像：这步操作可以在 docker hub 中完成

![image-20220518174300313](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220518174300313.png)

拉取 redis 镜像：`[root@ecs-352864 redis]# docker pull redis`

找到一个 redis.conf 主配置文件（最好找最原始的配置文件，否者无法成功），并做好配置：这里是 `/tmp/docker/redis/redis.conf`

找一个启动模板文件，并做好配置：`/tmp/docker/redis/redis-service-conf/redis6379/redis6379.conf`

运行镜像，生成 redis 容器（可以直接使用参数代替配置文件）：

```shell
[root@ecs-352864 redis]# docker run -p 6379:6379 --name redis --privileged=true -v /tmp/docker/redis/redis.conf:/etc/redis/redis.conf  -v /tmp/docker/redis/data/:/data -d  redis:6.2 redis-server /etc/redis/redis.conf --appendonly yes 
```

# 复杂软件的安装

## mysql 主从复制

### 安装主机（使用前面安装的mysql）

修改主机配置文件 my.conf

```shell
#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

port = 3306
character_set_server = utf8mb

[mysqld]
# 设置server_id，同一局域网中需要唯一
server_id=1001
# 指定不需要同步的数据库名称
binlog-ignore-db=mysql
# 开启二进制日志功能
log-bin=mall-mysql-bin
# 设置二进制日志使用内存大小（事务）
binlog_cache_size=1M
# 设置使用的二进制日志格式（mixed,statement,row）
binlog_format=mixed
# 二进制日志过期清理时间。默认值为0，表示不自动清理。
expire_logs_days=7
# 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
#  如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062

# include all files from the config directory

#!includedir /etc/my.cnf.d
```

重启主机

```shell
[root@ecs-352864 conf]# docker restart mysql8.0
```

进入容器，连接到 mysql

```shell
[root@ecs-352864 conf]# docker exec -it mysql8.0 bash
root@52d5b5472914:/# mysql -u root -p
Enter password: 
```

创建数据同步用户，并授权

```shell
mysql> CREATE USER 'slave'@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.01 sec)

mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'slave'@'%';
Query OK, 0 rows affected (0.01 sec)
```

### 安装从机

运行镜像，得到从机容器

```shell
[root@ecs-352864 docker]# docker run -d --name mysql_slave -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 -v /tmp/docker/mysql_slave3307/conf:/etc/mysql/conf.d -v /tmp/docker/mysql_slave3307/data:/var/lib/mysql -v /tmp/docker/mysql_slave3307/logs:/var/log/mysql mysql:8.0
```

在从机的 conf 数据卷加载目录创建配置文件 my.cnf

```shell
#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

port = 3306
character_set_server = utf8mb

[mysqld]
# 设置server_id，同一局域网中需要唯一
server_id=1002
# 指定不需要同步的数据库名称
binlog-ignore-db=mysql
# 开启二进制日志功能
log-bin=mall-mysql-slave-bin
# 设置二进制日志使用内存大小（事务）
binlog_cache_size=1M
# 设置使用的二进制日志格式（mixed,statement,row）
binlog_format=mixed
# 二进制日志过期清理时间。默认值为0，表示不自动清理。
expire_logs_days=7
# 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
#  如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
slave_skip_errors=1062
# relay_log配置中继日志
relay_log=mall-mysql-relay-bin
# log_slave_updates表示slave将复制事件写进自己的二进制日志
log_slave_updates=1
# slave设置为只读（具有super权限的用户除外）
read_only=1

# include all files from the config directory

#!includedir /etc/my.cnf.d

```

重启从机：

```shell
[root@ecs-352864 conf]# docker restart mysql_slave
```

连接主数据库，在主数据库中查看数据同步状态

```shell
[root@ecs-352864 docker]# docker exec -it mysql8.0 bash
root@52d5b5472914:/# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 8.0.27 MySQL Community Server - GPL

Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show master status;
+-----------------------+----------+--------------+------------------+-------------------+
| File                  | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+-----------------------+----------+--------------+------------------+-------------------+
| mall-mysql-bin.000001 |      711 |              | mysql            |                   |
+-----------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

进入从机容器，连接 mysql

```shell
[root@ecs-352864 docker]# docker exec -it mysql_slave bash
root@d66a3d0552a9:/# mysql -uroot -p
Enter password: 
```

在从机中配置主从复制

```shell
mysql> change master to master_host='121.37.217.252', master_user='slave', master_password='123456', master_port=3306, master_log_file='mall-mysql-bin.000001', master_log_pos=905, master_connect_retry=30;  

# 参数说明：
	master_host：主数据库的IP地址;
	master_port：主数据库的运行端口;
	master_user：在主数据库创建的用于同步数据的用户账号;
	master_password：在主数据库创建的用于同步数据的用户密码;
	master_log_file：指定从数据库要复制数据的日志文件，通过查看主数据的状态，获取File参数;
	master_log_pos：指定从数据库从哪个位置开始复制数据，通过查看主数据的状态，获取Position参数;
	master_connect_retry：连接失败重试的时间间隔，单位为秒。 
```

在从数据库中开启主从复制

```shell
mysql> start slave;
```

在从数据库中查看同步状态

```shell
mysql> show slave status \G;
```

![image-20220519105934510](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220519105934510.png)

### <font color=red>遇到的问题</font>

#### Last_IO_Errno: 2061

```shell
mysql> show slave status \G;
*************************** 1. row ***************************
             Slave_IO_Running: Connecting
            Slave_SQL_Running: Yes
                Last_IO_Errno: 2061
                Last_IO_Error: error connecting to master 'slave@121.37.217.252:3306' - retry-time: 30 retries: 8 message: Authentication plugin 'caching_sha2_password' reported error: Authentication requires secure connection.

```

这是由于 mysql 8 之前的版本中加密规则为 mysql_native_password，而在 mysql 8 以后的加密规则为 caching_sha2_password 导致的。

当出现 `Slave_IO_Running` 为 `Connecting` 时，查看错误日志 ` Last_IO_Error` 

**<font color=red>解决办法：</font>**如果是上面的这种情况，需要在主机中使用命令修改 slave 用户的加密规则和密码

```shell
# ALTER USER '从机连接主机的用户名'@'指定的ip地址' IDENTIFIED WITH 加密规则 BY '新密码';
mysql> ALTER USER 'slave'@'%' IDENTIFIED WITH mysql_native_password BY '123456';
```

#### Last_Errno: 1008

```shell
mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for source to send event
                  Master_Host: 121.37.217.252
                  Master_User: slave
                  Master_Port: 3306
                Connect_Retry: 30
              Master_Log_File: mall-mysql-bin.000001
          Read_Master_Log_Pos: 924
               Relay_Log_File: mall-mysql-relay-bin.000002
                Relay_Log_Pos: 523
        Relay_Master_Log_File: mall-mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: No
                   Last_Errno: 1008
                   Last_Error: Coordinator stopped because there were error(s) in the worker(s). The most recent failure being: Worker 1 failed executing transaction 'ANONYMOUS' at master log mall-mysql-bin.000001, end_log_pos 924. See error log and/or performance_schema.replication_applier_status_by_worker table for more details about this failure or others, if any.
```

主从复制配置完成后，从机只能同步主机在配置成功之后的操作，例如：

配置成功时，主机已经有了如下数据库（从机只有出厂的四个数据库）：

```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test               |
| test001            |
+--------------------+
6 rows in set (0.00 sec)
```

配置成后，从机不会同步在此之前的，主机中的 test 和 test001 这两个数据库

如果此时主机操作这两个数据库，将其删除，从机就会出现 `Last_Errno: 1008` 错误

**<font color=red>解决方法：</font>**在从机操作

```shell
# 停止从机
mysql> stop slave;
Query OK, 0 rows affected, 1 warning (0.00 sec)

# 将同步指针向下移动一个，可以重复操作
mysql> set global sql_slave_skip_counter = 1;
Query OK, 0 rows affected, 1 warning (0.01 sec)

# 开启从机
mysql> start slave;
Query OK, 0 rows affected, 1 warning (0.01 sec)
```

## redis 集群

### 运行 6 个 redis 容器

```shell
docker run -d --name redis-node-1 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-1:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6381 
docker run -d --name redis-node-2 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-2:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6382 
docker run -d --name redis-node-3 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-3:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6383 
docker run -d --name redis-node-4 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-4:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6384 
docker run -d --name redis-node-5 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-5:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6385 
docker run -d --name redis-node-6 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-6:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6386
```

```shell
#命令分步解释
    docker run				#创建并运行docker容器实例
    --name redis-node-6		#容器名字
    --net host				#使用宿主机的IP和端口，默认
    --privileged=true		#获取宿主机root用户权限
    -v /data/redis/share/redis-node-6:/data		#容器卷，宿主机地址:docker内部地址
    redis:6.0.8				#redis镜像和版本号
    --cluster-enabled yes	#开启redis集群
    --appendonly yes		#开启持久化
    --port 6386				#redis端口号
```

**运行结果：**

![image-20220519130705384](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220519130705384.png)

### 构建主从关系

```shell
# 进入一个容器
[root@ecs-352864 docker]# docker exec -it redis-node-1 bash
# 使用 redis-cli 客户端构建主从关系
root@ecs-352864:/data# redis-cli --cluster create --cluster-replicas 1 121.37.217.252:6381 121.37.217.252:6382 121.37.217.252:6383 121.37.217.252:6384 121.37.217.252:6385 121.37.217.252:6386
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 121.37.217.252:6385 to 121.37.217.252:6381
Adding replica 121.37.217.252:6386 to 121.37.217.252:6382
Adding replica 121.37.217.252:6384 to 121.37.217.252:6383
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: ef024f9be2287cd152ef52ab88a7fa38ed7905e5 121.37.217.252:6381
   slots:[0-5460] (5461 slots) master
M: f8e6a081b4c84070a75e784f59b3ec5fb271d5b0 121.37.217.252:6382
   slots:[5461-10922] (5462 slots) master
M: bea1fe7c3d33749fdcebb3a5a498e246c7c3a0d6 121.37.217.252:6383
   slots:[10923-16383] (5461 slots) master
S: 796e223f31a7c683ff34514e5c14445bc535e8f0 121.37.217.252:6384
   replicates f8e6a081b4c84070a75e784f59b3ec5fb271d5b0
S: 059131fe03d1ea60791c80c9ec286e4a5d1d9199 121.37.217.252:6385
   replicates bea1fe7c3d33749fdcebb3a5a498e246c7c3a0d6
S: 8dcf40c5fbb127ae4456e01d4b7ce101eb890e82 121.37.217.252:6386
   replicates ef024f9be2287cd152ef52ab88a7fa38ed7905e5
Can I set the above configuration? (type 'yes' to accept): yes		## 注意：这里必须输 yes ，不能简写成 y
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join

>>> Performing Cluster Check (using node 121.37.217.252:6381)
# 主从信息
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385
   slots: (0 slots) slave
   replicates afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6
S: b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
S: 9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386
   slots: (0 slots) slave
   replicates acd5e1e3fd6dad6861818a2b81366264834e7e92
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
root@ecs-352864:/data# 
```

**<font color=red>注意：如果在构建主从关系时，出现一只卡在 `Waiting for the cluster to join` 的情况，如下</font>**

```shell
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
............................................................................................................................................................................................................................................................................................................................................
```

这是由于 redis 集群之间的通信有一个集群总线，我们需要开放这个集群总线的防火墙，集群中的服务器之间才能正常通信

这个总线通常是 redis 服务的端口 +10000：例如，服务器端口为 6379 ，他的总线就是 16379

解决办法：开放其总线端口

![image-20220519140911774](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220519140911774.png)

进入一个服务器，查看集群信息：

```shell
root@ecs-352864:/data# redis-cli -c -p 6381
127.0.0.1:6381> cluster info
···
127.0.0.1:6381> cluster nodes		#主从信息
acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383@16383 master - 0 1652940027000 3 connected 10923-16383
afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382@16382 master - 0 1652940026876 2 connected 5461-10922
da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385@16385 slave afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 0 1652940027880 2 connected
7bfd9c9f43941ce2a8a0587325c6dbfff5659437 192.168.0.231:6381@16381 myself,master - 0 1652940023000 1 connected 0-5460
b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384@16384 slave 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 0 1652940027000 1 connected
9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386@16386 slave acd5e1e3fd6dad6861818a2b81366264834e7e92 0 1652940028883 3 connected
```

查看集群状态：

```shell
#  redis-cli --cluster check IP地址：本机redis服务的端口
root@ecs-352864:/data#  redis-cli --cluster check 121.37.217.252:6381 
121.37.217.252:6381 (7bfd9c9f...) -> 0 keys | 5461 slots | 1 slaves.
121.37.217.252:6383 (acd5e1e3...) -> 1 keys | 5461 slots | 1 slaves.
121.37.217.252:6382 (afc37f7e...) -> 0 keys | 5462 slots | 1 slaves.
[OK] 1 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385
   slots: (0 slots) slave
   replicates afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6
S: b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
S: 9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386
   slots: (0 slots) slave
   replicates acd5e1e3fd6dad6861818a2b81366264834e7e92
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

### 主从扩容案例

<font color=red>记得开放服务端口和总线端口</font>

新建两个容器实例

```shell
docker run -d --name redis-node-7 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-7:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6387
docker run -d --name redis-node-8 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-8:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6388
```

进入 6387 容器内部

```shell
[root@ecs-352864 redis_share]# docker exec -it 31f9910f1689 bash
```

将新增的 6387 作为 master 节点加入集群

```shell
# redis-cli --cluster add -node 自己实际IP地址:6387 自己实际IP地址:6381
# 6387 就是将要作为 master 新增节点 6381 就是原来集群节点里面的领路人，相当于6387拜拜6381的码头从而找到组织加入集群 
root@ecs-352864:/data# redis-cli --cluster add-node 121.37.217.252:6387 121.37.217.252:6381
···
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
>>> Send CLUSTER MEET to node 121.37.217.252:6387 to make it join the cluster.
[OK] New node added correctly.
```

检查集群情况：

```shell
root@ecs-352864:/data# redis-cli --cluster check 121.37.217.252:6381 
121.37.217.252:6381 (7bfd9c9f...) -> 0 keys | 5461 slots | 1 slaves.
121.37.217.252:6387 (2e70b787...) -> 0 keys | 0 slots | 0 slaves.
121.37.217.252:6383 (acd5e1e3...) -> 1 keys | 5461 slots | 1 slaves.
121.37.217.252:6382 (afc37f7e...) -> 0 keys | 5462 slots | 1 slaves.
[OK] 1 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
M: 2e70b787b7c641b3915121606e306032e8145255 121.37.217.252:6387
   slots: (0 slots) master
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385
   slots: (0 slots) slave
   replicates afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6
S: b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
S: 9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386
   slots: (0 slots) slave
   replicates acd5e1e3fd6dad6861818a2b81366264834e7e92
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

重新分配插槽号：

```shell
# redis-cli --cluster reshard IP地址:端口号
# 端口号可以随便写，但是必须是集群中的一个主库
root@ecs-352864:/data# redis-cli --cluster reshard 121.37.217.252:6381
>>> Performing Cluster Check (using node 121.37.217.252:6381)
···
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 3000		# 为这个服务器分配多少个插槽数量
What is the receiving node ID? 7bfd9c9f43941ce2a8a0587325c6dbfff5659437		# 迁移目标的 ID 号
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
Source node #1: all 	# 从哪个主节点分配插槽，all 表示所有主节点，也可以指定节点 ID，选择 all 会从每个节点中分出一部分，组合到新节点中
···
Do you want to proceed with the proposed reshard plan (yes/no)? yes 		# 提示我们是否继续分配，选择yes
···
Moving slot 12287 from 121.37.217.252:6383 to 121.37.217.252:6387: 
Moving slot 12288 from 121.37.217.252:6383 to 121.37.217.252:6387: 
root@ecs-352864:/data# 
```

在次查看集群情况：

```shell
root@ecs-352864:/data# redis-cli --cluster check 121.37.217.252:6381 
121.37.217.252:6381 (7bfd9c9f...) -> 0 keys | 4095 slots | 1 slaves.
121.37.217.252:6387 (2e70b787...) -> 0 keys | 4099 slots | 0 slaves.
121.37.217.252:6383 (acd5e1e3...) -> 1 keys | 4095 slots | 1 slaves.
121.37.217.252:6382 (afc37f7e...) -> 0 keys | 4095 slots | 1 slaves.
[OK] 1 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[1366-5460] (4095 slots) master
   1 additional replica(s)
M: 2e70b787b7c641b3915121606e306032e8145255 121.37.217.252:6387
   slots:[0-1365],[5461-6827],[10923-12288] (4099 slots) master
   # 为什么6387是3个新的区间？
   # 重新分配成本太高，所以前 3 家各自匀出来一部分，从 6381/6382/6383 三个旧节点分别匀出 1364 个坑位给新节点 6387 
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[12289-16383] (4095 slots) master
   1 additional replica(s)
...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

为新的主节点 6387 分配从节点 6388：

```shell
#redis-cli --cluster add-node ip:新slave端口 ip:master端口 --cluster-slave --cluster-master-id 主机节点ID 
#2e70b787b7c641b3915121606e306032e8145255  这个是6387的编号，按照自己实际情况 
root@ecs-352864:/data# redis-cli --cluster add-node 121.37.217.252:6388 121.37.217.252:6387 --cluster-slave --cluster-master-id 2e70b787b7c641b3915121606e306032e8145255
>>> Adding node 121.37.217.252:6388 to cluster 121.37.217.252:6387
>>> Performing Cluster Check (using node 121.37.217.252:6387)
M: 2e70b787b7c641b3915121606e306032e8145255 121.37.217.252:6387
   slots:[0-1365],[5461-6827],[10923-12288] (4099 slots) master
···
>>> Configure node as replica of 121.37.217.252:6387.
[OK] New node added correctly.
```

查看集群信息：

```shell
root@ecs-352864:/data# redis-cli --cluster check 121.37.217.252:6381 
121.37.217.252:6381 (7bfd9c9f...) -> 0 keys | 4095 slots | 1 slaves.
121.37.217.252:6387 (2e70b787...) -> 0 keys | 4099 slots | 1 slaves.
121.37.217.252:6383 (acd5e1e3...) -> 1 keys | 4095 slots | 1 slaves.
121.37.217.252:6382 (afc37f7e...) -> 0 keys | 4095 slots | 1 slaves.
[OK] 1 keys in 4 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[1366-5460] (4095 slots) master
   1 additional replica(s)
M: 2e70b787b7c641b3915121606e306032e8145255 121.37.217.252:6387
   slots:[0-1365],[5461-6827],[10923-12288] (4099 slots) master
   1 additional replica(s)
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[12289-16383] (4095 slots) master
   1 additional replica(s)
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[6828-10922] (4095 slots) master
   1 additional replica(s)
S: 99af697229b8def5886fe4ec6e80631d196d4694 121.37.217.252:6388
   slots: (0 slots) slave
   replicates 2e70b787b7c641b3915121606e306032e8145255
S: da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385
   slots: (0 slots) slave
   replicates afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6
S: b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
S: 9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386
   slots: (0 slots) slave
   replicates acd5e1e3fd6dad6861818a2b81366264834e7e92
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

### 主从索容案例

检查集群情况，获得需要缩容的服务器  ID ：上面已经将其查出，可以直接使用

将从节点 6388 从集群中删除：

```shell
# redis-cli --cluster del-node ip:从机端口 从机6388节点ID 
root@ecs-352864:/data# redis-cli --cluster del-node 121.37.217.252:6388 99af697229b8def5886fe4ec6e80631d196d4694
>>> Removing node 99af697229b8def5886fe4ec6e80631d196d4694 from cluster 121.37.217.252:6388
>>> Sending CLUSTER FORGET messages to the cluster...
>>> Sending CLUSTER RESET SOFT to the deleted node.
# 检查一下发现，6388被删除了，只剩下7台机器了。  
root@ecs-352864:/data# redis-cli --cluster check 121.37.217.252:6381 
···
>>> Performing Cluster Check (using node 121.37.217.252:6381)
···
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[6828-10922] (4095 slots) master
   1 additional replica(s)
S: da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385
   slots: (0 slots) slave
   replicates afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6
S: b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
S: 9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386
   slots: (0 slots) slave
   replicates acd5e1e3fd6dad6861818a2b81366264834e7e92
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
```

清空 6387 的插槽，本例将其全部给 6381：直接使用分配插槽的命令

```shell
# redis-cli --cluster reshard IP地址:端口号
# 端口号可以随便写，但是必须是集群中的一个主库
root@ecs-352864:/data# redis-cli --cluster reshard 121.37.217.252:6381
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[1366-5460] (4095 slots) master
   1 additional replica(s)
M: 2e70b787b7c641b3915121606e306032e8145255 121.37.217.252:6387
   slots:[0-1365],[5461-6827],[10923-12288] (4099 slots) master
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[12289-16383] (4095 slots) master
   1 additional replica(s)
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[6828-10922] (4095 slots) master
   1 additional replica(s)
···
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 4099		# 为这个服务器分配多少个插槽数量
What is the receiving node ID? 7bfd9c9f43941ce2a8a0587325c6dbfff5659437		# 迁移目标的 ID 号
Please enter all the source node IDs.
  Type 'all' to use all the nodes as source nodes for the hash slots.
  Type 'done' once you entered all the source nodes IDs.
# 从哪个主节点分配插槽，all 表示所有主节点，也可以指定节点 ID，选择 all 会从每个节点中分出一部分，组合到新节点中
Source node #1: 2e70b787b7c641b3915121606e306032e8145255 	
Source node #1: done		# 完成
···
Do you want to proceed with the proposed reshard plan (yes/no)? yes 		# 提示我们是否继续分配，选择yes
···
Moving slot 12288 from 121.37.217.252:6387 to 121.37.217.252:6381: 
Node 121.37.217.252:6387 replied with error:
ERR Please use SETSLOT only with masters.
root@ecs-352864:/data# 
```

检查集群情况，发现 6387 的插槽为 0 ，并变成了从节点：

```shell
root@ecs-352864:/data# redis-cli --cluster check 121.37.217.252:6381 
121.37.217.252:6381 (7bfd9c9f...) -> 0 keys | 8194 slots | 2 slaves.
121.37.217.252:6383 (acd5e1e3...) -> 1 keys | 4095 slots | 1 slaves.
121.37.217.252:6382 (afc37f7e...) -> 0 keys | 4095 slots | 1 slaves.
[OK] 1 keys in 3 masters.
0.00 keys per slot on average.
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[0-6827],[10923-12288] (8194 slots) master
   2 additional replica(s)
S: 2e70b787b7c641b3915121606e306032e8145255 121.37.217.252:6387
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
```

将 6387 从集群中删除：

```shell
root@ecs-352864:/data# redis-cli --cluster del-node 121.37.217.252:6387 2e70b787b7c641b3915121606e306032e8145255
>>> Removing node 2e70b787b7c641b3915121606e306032e8145255 from cluster 121.37.217.252:6387
>>> Sending CLUSTER FORGET messages to the cluster...
>>> Sending CLUSTER RESET SOFT to the deleted node.
root@ecs-352864:/data# 
```

在次查看集群信息，发现删除成功：

```shell
root@ecs-352864:/data# redis-cli --cluster check 121.37.217.252:6381 
···
>>> Performing Cluster Check (using node 121.37.217.252:6381)
M: 7bfd9c9f43941ce2a8a0587325c6dbfff5659437 121.37.217.252:6381
   slots:[0-6827],[10923-12288] (8194 slots) master
   1 additional replica(s)
M: acd5e1e3fd6dad6861818a2b81366264834e7e92 121.37.217.252:6383
   slots:[12289-16383] (4095 slots) master
   1 additional replica(s)
M: afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6 121.37.217.252:6382
   slots:[6828-10922] (4095 slots) master
   1 additional replica(s)
S: da858e2655169e85dbbbb0028ea430660422888c 121.37.217.252:6385
   slots: (0 slots) slave
   replicates afc37f7e889aa2fab7dc1cbcb4ab443af0d90bf6
S: b044471079be2a9e53ab89c5c680eb4d4c9496cd 121.37.217.252:6384
   slots: (0 slots) slave
   replicates 7bfd9c9f43941ce2a8a0587325c6dbfff5659437
S: 9ae6dfeb47d387f669f8141df2b08904874be556 121.37.217.252:6386
   slots: (0 slots) slave
   replicates acd5e1e3fd6dad6861818a2b81366264834e7e92
[OK] All nodes agree about slots configuration.
···
```

# DockerFile

Dockerfile 是用来构建 Docker 镜像的文本文件，是由一条条构建镜像所需的指令和参数构成的脚本。

语法参考：https://docs.docker.com/engine/reference/builder/#from

构建三步骤：
		编写 Dockerfile 文件 -- > docker build 命令构建镜像 -- > docker run 依镜像运行容器实例

## Dockerfile内容基础知识

> 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
> 指令按照从上到下，顺序执行
> #表示注释
> 每条指令都会创建一个新的镜像层并对镜像进行提交

## Docker执行Dockerfile的大致流程

> docker从基础镜像运行一个容器
> 执行一条指令并对容器作出修改
> 执行类似docker commit的操作提交一个新的镜像层
> docker再基于刚提交的镜像运行一个新容器
> 执行dockerfile中的下一条指令直到所有指令都执行完成

 从应用软件的角度来看，Dockerfile、Docker 镜像与 Docker 容器分别代表软件的三个不同阶段，Dockerfile 是软件的原材料；Docke r镜像是软件的交付品，Docker 容器则可以认为是软件镜像的运行态；

也即依照镜像运行的容器实例 Dockerfile 面向开发，Docker 镜像成为交付标准，Docker 容器则涉及部署与运维，三者缺一不可，合力充当 Docker 体系的基石。

Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程等等；(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计 namespace 的权限控制)

Docker 镜像，在用 Dockerfile 定义一个文件之后，docker build 时会产生一个 Docker 镜像，当运行 Docker 镜像运行时会真正开始提供服务；

Docker容器，容器是直接提供服务的。

## 常用保留字指令

DockerFile常用保留字指令
	参考tomcat8的dockerfile入门
		https://github.com/docker-library/tomcat
FROM

> 基础镜像，当前新镜像是基于哪个镜像的，指定一个已经存在的镜像作为模板，第一条必须是from

MAINTAINER

> 镜像维护者的姓名和邮箱地址

RUN

> 容器构建时需要运行的命令
>
> 两种格式
>
> > shell格式
> >
> > exec格式
>
> RUN是在 docker build时运行

EXPOSE

> 当前容器对外暴露出的端口

WORKDIR

> 指定在创建容器后，终端默认登陆的进来工作目录，一个落脚点

USER

> 指定该镜像以什么样的用户去执行，如果都不指定，默认是root

ENV

> 用来在构建镜像过程中设置环境变量

ADD

> 将宿主机目录下的文件拷贝进镜像且会自动处理URL和解压tar压缩包

COPY

> 类似ADD，拷贝文件和目录到镜像中。
> 将从构建上下文目录中 <源路径> 的文件/目录复制到新的一层的镜像内的 <目标路径> 位置
> 			COPY src dest
> 			COPY ["src", "dest"]
> 			<src源路径>：源文件或者源目录
> 			<dest目标路径>：容器内的指定路径，该路径不用事先建好，路径不存在的话，会自动创建。

VOLUME

> 容器数据卷，用于数据保存和持久化工作

CMD

> 指定容器启动后的要干的事情
> 注意
>
> > Dockerfile 中可以有多个 CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换
> > 		参考官网Tomcat的dockerfile演示讲解
> > 		官网最后一行命令
> > 				![image-20220520094201725](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220520094201725.png)
> > 		我们演示自己的覆盖操作
> > 				![image-20220520094253183](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220520094253183.png)
> > 它和前面RUN命令的区别
> > 		CMD是在docker run 时运行。
> > 		RUN是在 docker build时运行。

ENTRYPOINT

> 也是用来指定一个容器启动时要运行的命令
> 类似于 CMD 指令，但是 ENTRYPOINT 不会被 docker run 后面的命令覆盖，
> 而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序
>
> > 命令格式和案例说明
> > 优点
> > 		在执行docker run的时候可以指定 ENTRYPOINT 运行所需的参数。
> > 注意
> > 		如果 Dockerfile 中如果存在多个 ENTRYPOINT 指令，仅最后一个生效。

## 自定义镜像

创建包含 vim + ifconfig + jdk8 的 Centos 7 镜像

下载 jdk 到指定目录：`/tmp/DockerFile/centos_jdk8_vim_ifconfig`

编写 DockerFile 文件：`[root@ecs-352864 centos_jdk8_vim_ifconfig]# vim Dockerfile `

```shell
 FROM centos:7
 MAINTAINER zym
 ENV MYPATH /usr/local
 WORKDIR $MYPATH
 #安装vim编辑器
 RUN yum -y install vim
 #安装ifconfig命令查看网络IP
 RUN yum -y install net-tools
 #安装java8及lib库
 RUN yum -y install glibc.i686
 RUN mkdir /usr/local/java
 #ADD 是相对路径jar,把jdk-8u333-linux-x64.tar.gz添加到容器中,安装包必须要和Dockerfile文件在同一位置
 ADD jdk-8u333-linux-x64.tar.gz /usr/local/java/
 #配置java环境变量
 ENV JAVA_HOME /usr/local/java/jdk1.8.0_333
 ENV JRE_HOME $JAVA_HOME/jre
 ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
 ENV PATH $JAVA_HOME/bin:$PATH
 EXPOSE 80
 CMD echo $MYPATH
 CMD echo "success--------------ok"
 CMD /bin/bash
```

构建新镜像：

```shell
# docker build -t 新镜像名字:TAG .
# 注意，上面TAG后面有个空格，有个点
[root@ecs-352864 centos_jdk8_vim_ifconfig]# docker build -t my/centos .
Sending build context to Docker daemon    148MB
Step 1/17 : FROM centos:7
 ---> eeb6ee3f44bd
Step 2/17 : MAINTAINER zym
···
Removing intermediate container f1356395cf2b
 ---> 605c0af7d894
Successfully built 605c0af7d894
Successfully tagged my/centos:latest
[root@ecs-352864 centos_jdk8_vim_ifconfig]#
```

运行新镜像：

```shell
[root@ecs-352864 centos_jdk8_vim_ifconfig]# docker run -it my/centos bash
[root@212e250d2288 local]# ls
bin  etc  games  include  java  lib  lib64  libexec  sbin  share  src
[root@212e250d2288 local]# vim aa
[root@212e250d2288 local]# ifconfig 
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.4  netmask 255.255.0.0  broadcast 172.17.255.255
```

## 虚悬镜像

仓库名、标签都是 \<none\> 的镜像，俗称 dangling image（虚悬镜像）

```
[root@ecs-352864 test01]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
<none>       <none>    677ce4db07dd   5 seconds ago    72.8MB
```

查看虚悬镜像：

```shell
[root@ecs-352864 test01]# docker image ls -f dangling=true
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
<none>       <none>    677ce4db07dd   About a minute ago   72.8MB
```

删除虚悬镜像：虚悬镜像已经失去存在价值，可以删除 

```shell
[root@ecs-352864 test01]#  docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y		#是否确定删除
Deleted Images:
deleted: sha256:677ce4db07dd837204f8287b050c39d9cde1e6bc018f17e7de504895fb3b381b

Total reclaimed space: 0B
```

## Docker 微服务实战

创建 Java 服务（修改端口为 80）：需要达成 war 包或 jar 包，并上传至服务器

![image-20220520142907389](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220520142907389.png)

在 war 包所在路径创建 Dockerfile 文件：写入如下内容 

```shell
# 基础镜像使用java
FROM my/centos
# 作者
MAINTAINER zym
# VOLUME 指定临时文件目录为/tmp，在主机/var/lib/docker目录下创建了一个临时文件并链接到容器的/tmp
VOLUME /tmp
# 将jar包添加到容器中并更名为zzyy_docker.jar
ADD demo-0.0.1-SNAPSHOT.war demo.war
ENTRYPOINT ["java","-jar","demo.war"]
#暴露6001端口作为微服务
EXPOSE 80
```

运行 build 命令，构建镜像

```shell
[root@ecs-352864 test01]# docker build -t java_demo .
Sending build context to Docker daemon  17.61MB
Step 1/6 : FROM my/centos:0.1
 ---> 605c0af7d894
···
Step 6/6 : EXPOSE 80
 ---> Running in 5f2f2a1c0e61
Removing intermediate container 5f2f2a1c0e61
 ---> b2bb92c30add
Successfully built b2bb92c30add
Successfully tagged java_demo:latest
[root@ecs-352864 test01]# 
```

查看镜像

```shell
[root@ecs-352864 test01]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
java_demo    latest    b2bb92c30add   12 minutes ago   1.2GB
```

运行镜像

```shell
# 前台运行示例
[root@ecs-352864 test01]# docker run -it --name demo -p 80:80 java_demo

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v2.7.0)
2022-05-20 06:22:27.714  INFO 1 --- [p-nio-80-exec-1] o.s.web.servlet.DispatcherServlet        : Completed initialization in 1 ms


# 后台运行示例
[root@ecs-352864 test01]# docker run -d --name demo -p 80:80 java_demo
a83f9752442a3785fa1e12c9d6b9ffa133b35f56b2f06d852eb9ec1f3a164084
[root@ecs-352864 test01]# 
```

查看容器实例

```shell
[root@ecs-352864 test01]# docker ps
CONTAINER ID   IMAGE       COMMAND                  CREATED         STATUS         PORTS                                  NAMES
a83f9752442a   java_demo   "java -jar demo.jar"     4 seconds ago   Up 3 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp      demo
```

访问 index 请求，测试是否部署成功

```html
[root@ecs-352864 test01]# curl http://localhost:80/index
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>test</title>
</head>
<body>
<h1>Hello World</h1>
</body>
</html>[root@ecs-352864 test01]# 

```

# Docker 网络

可参考：https://blog.csdn.net/succing/article/details/122433770

docker 网络的作用
		容器间的互联和通信以及端口映射
		容器IP变动时候可以通过服务名直接网络通信而不受到影响

## 常用基本命令

自定义网络本身是一块很大的知识点，先熟悉网络方面的命令：

| 命令                                                    | 作用                         |
| ------------------------------------------------------- | ---------------------------- |
| docker network create -d [mode_type] 自定义网络名称     | 新建自定义网络               |
| docker network rm  [ id \| 自定义网络名称]              | 移除自定义网络模式           |
| docker network connect   [ id \|自定义网络名称]         | 连接到特定网络               |
| docker network disconnect  [net_name] [contain_id/name] | 给指定容器，取消某个网络模式 |
| docker network disconnect  [net_name] [contain_id/name] | 查看网络模式的详细网络信息   |

删除网络：docker network rm XXX网络名字

查看网络：docker network ls

```shell
[root@ecs-352864 DockerFile]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
fc88ac9a837d   bridge    bridge    local
ee7b7f6ac1ad   host      host      local
ccc28a5a90ec   none      null      local
```

查看网络源数据：docker network inspect  XXX网络名字

```shell
[root@ecs-352864 DockerFile]# docker network inspect host
[
    {
        "Name": "host",
        "Id": "ee7b7f6ac1ad73a78c22c4b75e66be7dcf0d660da9bf7b740637eaaf092b2aa7",
        "Created": "2022-05-17T12:16:44.518540578+08:00",
        "Scope": "local",
        "Driver": "host",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": []
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "032ca183fc902ed1ee660db9eb18bf8ef1469ca0271031faa4689a188c49754e": {
                "Name": "redis-node-1",
                "EndpointID": "e96dacd63a8999b16b0bc8a4808de5663e6dac67bbfb3413cc9381c1404043bf",
                "MacAddress": "",
                "IPv4Address": "",
                "IPv6Address": ""
            },
            ···
            "862de288751789a231fe0ce47b2e6e77125eade933dddf2f2de11b5ef44900bb": {
                "Name": "redis-node-3",
                "EndpointID": "d01a569f0f5bc33fa401580d0528f2faa121242717a0db99519f351d6697f054",
                "MacAddress": "",
                "IPv4Address": "",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
[root@ecs-352864 DockerFile]#
```

## 默认三种网络模式

### bridge：桥接模式

```
桥接模式是 docker 的默认网络设置，当 Docker 服务启动时，会在主机上创建一个名为 docker0 的虚拟网桥，并选择一个和宿主机不同的 IP 地址和子网分配给 docker0 网桥
如下：docker0 就是 docker 服务的虚拟网桥

[root@ecs-352864 DockerFile]# ifconfig
docker0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.1  netmask 255.255.0.0  broadcast 172.17.255.255
        inet6 fe80::42:57ff:fe76:18a0  prefixlen 64  scopeid 0x20<link>
        ···
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.0.231  netmask 255.255.255.0  broadcast 192.168.0.255
        ···
lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        ···
veth1e63b7f: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::9832:29ff:fe8f:8126  prefixlen 64  scopeid 0x20<link>
        ether 9a:32:29:8f:81:26  txqueuelen 0  (Ethernet)
        ···
veth4e6a4d3: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::ac1e:7cff:fedc:771d  prefixlen 64  scopeid 0x20<link>
        ether ae:1e:7c:dc:77:1d  txqueuelen 0  (Ethernet)
        ···
veth6e27dbc: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet6 fe80::1c71:5dff:fe98:d6b4  prefixlen 64  scopeid 0x20<link>
        ether 1e:71:5d:98:d6:b4  txqueuelen 0  (Ethernet)
        ···

```

桥接拓补图：

![image-20220520172656408](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220520172656408.png)

网络模式选择桥接模式的容器，就会连接上docker0这个网桥，在通过nat的转换，通过宿主机的网卡，连接外网，就能达到上外网的目的。

上面的显示的信息中，`veth1e63b7f`，`veth4e6a4d3`，`veth6e27dbc` 就是容器的虚拟网卡，他们桥接到 docker0 上，然后通过 nat 技术连接上物理机的网卡

### host：主机模式

该模式下容器是不会拥有自己的ip地址，而是使用宿主机的ip地址和端口。这种模式的好处就是网络性能比桥接模式的好。缺点就是会占用宿主机的端口，网络的隔离性不太好

以 redis 为例，运行镜像创造容器时，使用 `--net host` 即表示使用 host 方式的连接网络，不填这个参数就是使用桥接方式

```shell
# 运行 redis 镜像的命令示例：
docker run -d --name redis-node-1 --net host --privileged=true -v /tmp/docker/redis_share/redis-node-1:/data redis:6.2 --cluster-enabled yes --appendonly yes --port 6381 
```



![image-20220520174252964](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220520174252964.png)

### none：无网络模式

加上后面的参数就行了 `--net none` 即表示无网络模式

<font color=red>none 模式没有 IP 地址，无法连接外网，等于就是断网的状态，作用就是用于测试，生产环境一般不会用到这种</font>

## container 网络模式和自定义网络模式

### container 模式

该模式相当于 bridge + host 的混合模式，指定一个容器以 bridge 方式启动，后面容器启动时指定网络模式为 container，它们自动共享第一个容器的网络资源。

```shell
--net container:容器名称（ID）		# 指定连接到哪一个容器
```

特点

> 新创建的容器，仅同前面已存在的容器，共享网络空间，不与宿主机共享网络。
> 新创建的容器，不会有自己的虚拟网卡和IP，后面新创建容器的网络资源用的是上一个容器的。
> 新创建的容器，仅仅是网络和第一个容器共享，其他资源彼此还都是相互隔离的。

弊端

> 第一个指定容器服务一旦停掉，后续的容器也没有办法继续运行 

![image-20220520180151492](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220520180151492.png)

### 自定义网络

<font color=red>自定义网络本身就维护好了主机名和ip的对应关系（ip和域名都能通）</font>

#### 示例：不使用自定义网络的情况

下载 alpine 镜像：

```shell
[root@ecs-352864 tmp]# docker pull alpine
Using default tag: latest
···
Digest: sha256:21a3deaa0d32a8057914f36584b5288d2e5ecc984380bc0118285c70fa8c9300
Status: Downloaded newer image for alpine:latest
```

 运行 alpine 镜像，创建两个实例：并查看 ip 地址

```shell
#运行第一个实例 alpine1 ，查看其 IP 地址
[root@ecs-352864 tmp]# docker run --name alpine1 -it alpine /bin/sh
/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:07  
          inet addr:172.17.0.7  Bcast:172.17.255.255  Mask:255.255.0.0
          ···
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          ···
```

```shell
#运行第二个实例 alpine2 ，查看其 IP 地址
[root@ecs-352864 ~]# docker run --name alpine2 -it alpine /bin/sh
/ # ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:AC:11:00:08  
          inet addr:172.17.0.8  Bcast:172.17.255.255  Mask:255.255.0.0
          ···
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          ···
```

使用 ping 命令相互 ping 两个实例的 IP 和容器名

```shell
/ # ping 172.17.0.8		#在第一个容器 ping 第二个容器的 ip，可以 ping 通
PING 172.17.0.8 (172.17.0.8): 56 data bytes
64 bytes from 172.17.0.8: seq=0 ttl=64 time=0.115 ms
···
--- 172.17.0.8 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.073/0.086/0.115 ms
/ # ping alpine2		#在第一个容器 ping 第二个容器的容器名称，不能 ping 通
ping: bad address 'alpine2'
```

```shell
/ # ping 172.17.0.7		#在第二个容器 ping 第一个容器的 ip，可以 ping 通
PING 172.17.0.7 (172.17.0.7): 56 data bytes
64 bytes from 172.17.0.7: seq=0 ttl=64 time=0.062 ms
···
--- 172.17.0.7 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max = 0.062/0.077/0.096 ms
/ # ping alpine1		#在第二个容器 ping 第一个容器的容器名称，不能 ping 通
ping: bad address 'alpine1'
```

<font color=red>注意：由于容器的ip不是固定的，在容器关闭后，再打开，由于在这之间可能会有其他操作导致ip变化，所以我们需要反复修改ip，很不有哦好</font>

使用自定义网络模式，可以让我们通过容器名称连接网络。

#### 示例：使用自定义网络的情况

创建一个自定义网络：

```shell
# 新建网络
[root@ecs-352864 tmp]# docker network create new_network
8309736a241e3611f032c5ffe754615673679285cd6307335746577992f30fef
# 查看网络列表，发现我们刚刚新建的网络，新建网络默认是 bridge 模式
[root@ecs-352864 tmp]# docker network ls
NETWORK ID     NAME          DRIVER    SCOPE
fc88ac9a837d   bridge        bridge    local
ee7b7f6ac1ad   host          host      local
8309736a241e   new_network   bridge    local
ccc28a5a90ec   none          null      local
```

新建两个容器，让其使用刚刚新建的自定义网络：此时，我们发现两个容器之间可以通过容器的名称进行通信了

```shell
# 新建容器 alpine1 ， --net new_network 指定其连接到新建的网络
[root@ecs-352864 tmp]# docker run --name alpine1 --net new_network -it alpine /bin/sh
/ # ifconfig
eth0      Link encap:Ethernet  HWaddr 02:42:AC:12:00:02  
          inet addr:172.18.0.2  Bcast:172.18.255.255  Mask:255.255.0.0
          ···
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          ···
# ping 第二个容器的 ip
/ # ping 172.18.0.3
PING 172.18.0.3 (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.117 ms
···
--- 172.18.0.3 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.074/0.089/0.117 ms
# ping 第二个容器的名称
/ # ping alpine2
PING alpine2 (172.18.0.3): 56 data bytes
64 bytes from 172.18.0.3: seq=0 ttl=64 time=0.061 ms
···
--- alpine2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round
```

```shell
# 新建容器 alpine2 ， --net new_network 指定其连接到新建的网络
[root@ecs-352864 ~]# docker run --name alpine2 --net new_network -it alpine /bin/sh
/ # ifconfig 
eth0      Link encap:Ethernet  HWaddr 02:42:AC:12:00:03  
          inet addr:172.18.0.3  Bcast:172.18.255.255  Mask:255.255.0.0
          ···
lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          ···
# ping 第一个容器的 ip
/ # ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.073 ms
···
--- 172.18.0.2 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.073/0.079/0.088 ms
# ping 第一个容器的名称
/ # ping alpine1
PING alpine1 (172.18.0.2): 56 data bytes
64 bytes from 172.18.0.2: seq=0 ttl=64 time=0.079 ms
···
--- alpine1 ping statistics ---
3 packets transmitted, 3 packets received, 0% packet loss
round-trip min/avg/max = 0.074/0.084/0.100 ms
/ # 
```

# Docker-compose 容器编排

Docker-Compose 是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。

当我们需要部署一个项目时，往往需要使用 mysql 数据库，redis 数据库等更多的工具，这时，我们启动项目所在的容器就需要在这之前启动好 mysql，redis 等项目中需要使用的工具。如果项目大，使用的东西多，一个个运行起来会很麻烦，docker-compose 就是为了帮助我们解决这些问题，编写一个脚本，然后 Docker 按照脚本的顺序，给我们自动运行容器和其所需的依赖。

官方文档：https://docs.docker.com/compose/compose-file/compose-file-v3/

## 安装

参考官网（这个安装步骤有问题）：https://docs.docker.com/compose/install

```shell
[root@ecs-352864 bin]# curl -L "https://github.com/docker/compose/releases/download/1.28.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
100 11.6M  100 11.6M    0     0  1234k      0  0:00:09  0:00:09 --:--:-- 1124k
[root@ecs-352864 bin]#  chmod +x /usr/local/bin/docker-compose
[root@ecs-352864 bin]# docker-compose --version
docker-compose version 1.28.5, build c4eb3a1f
```

## 核心概念

一文件

> docker-compose.yml

两要素

> 服务（service）
> 		一个个应用容器实例，比如订单微服务、库存微服务、mysql容器、nginx容器或者redis容器
> 工程（project）
> 		由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

## 常用命令：

```shell
docker-compose -h              	# 查看帮助
docker-compose up              	# 启动所有docker-compose服务
docker-compose up -d            # 启动所有docker-compose服务并后台运行
docker-compose down             # 停止并删除容器、网络、卷、镜像。
docker-compose exec yml文件中写的服务id         # 进入容器实例内部 
docker-compose exec yml文件中写的服务id /bin/bashdocker-compose ps           # 展示当前docker-compose编排过的运行的所有容器
docker-compose top           	# 展示当前docker-compose编排过的容器进程 
docker-compose logs yml文件中写的服务id   # 查看容器输出日志
docker-compose config   		# 检查配置
docker-compose config -q 		# 检查配置，有问题才有输出
docker-compose restart  		# 重启服务
docker-compose start   			# 启动服务
docker-compose stop   			# 停止服务 
```

## 部署步骤：

### 不使用 Docker-compose

编写 Dockerfile 定义各个微服务应用并构建出对应的镜像文件，本次改造上次 Dockerfile 中的项目

整合了 redis 、mysql

```java
@Api(tags = "测试接口")
@Controller
public class Demo01 {

    @Resource
    private RedisTemplate redisTemplate;
    @Resource
    private UserDao userDao;

    @ApiOperation(value = "返回index.html")
    @GetMapping("/index")
    public String index() {
        return "/html/index.html";
    }


    @ApiOperation(value = "添加用户")
    @GetMapping("/addUser")
    @ResponseBody
    public String addUser(@ApiParam(name = "用户对象",required = true) User user) {
        int i = userDao.addUser(user);
        return String.valueOf(i);
    }

    @ApiOperation(value = "查询用户")
    @GetMapping("/getUsers")
    @ResponseBody
    public List getUsers() {
        return userDao.getUsers();
    }

    @ApiOperation(value = "添加数据到redis")
    @GetMapping("/setRedis")
    @ResponseBody
    public String setRedis(@Param("key")@ApiParam(name = "key",required = true) String key, @Param("key")@ApiParam(name = "value",required = true) String value) {
        redisTemplate.opsForValue().set(key, value);
        return "success";
    }

    @ApiOperation(value = "查询redis中的数据")
    @GetMapping("/getRedis")
    @ResponseBody
    public String getRedis(@Param("key")@ApiParam(name = "key",required = true) String key) {
        System.out.println(key);
        return (String) redisTemplate.opsForValue().get(key);
    }
}
```

部署之前，我们需要先安装 mysql 数据库和 redis 数据库

然后才能通过 Dockerfile 对项目进行部署（Dockerfile 与之前一样）

### 使用 Docker-compose

<font color=red>**注意：需要先运行 Dockerfile 将项目 jar（war）包构建成 my_demo 镜像**</font>

编写 docker-compose.yml 定义一个完整业务单元，安排好整体应用中的各个容器服务。

```shell
version: "3"
services:
  my_demo:
    image: my_demo		# 容器名称（前面会加上当前文件夹的名称）
    ports:		# 端口映射
      - "80:80"		
    networks:		# 网络模式
      - my_net
    depends_on:		# 依赖项
      - "mysql"
      - "redis"
  mysql:
    container_name: "mysql8.0"
    image: mysql:8.0
    restart: always
    ports:
      - "3306:3306"
    volumes: 		# 挂载数据卷
      - /tmp/docker/mysql/conf:/etc/mysql/conf.d
      - /tmp/docker/mysql/data:/var/lib/mysql
      - /tmp/docker/mysql/logs:/var/log/mysql
    environment:		# 参数
      - TZ=Asia/Shanghai 		# 设置时区
      - MYSQL_ROOT_PASSWORD=123456 		# 设置 root 用户密码
    networks:
      - my_net
  redis:
    container_name: "redis"
    image: redis:6.2
    ports:
      - "6379:6379"
    volumes:
      - /tmp/docker/redis/redis.conf:/etc/redis/redis.conf
      - /tmp/docker/redis/data/:/data
    restart: always
    command: redis-server --appendonly yes
    networks:
      - my_net
networks:
  my_net:

```

最后，执行 docker-compose up 命令 来启动并运行整个应用程序，完成一键部署上线。<font color=red>注意：使用 -d 才是后台启动</font>

```shell
[root@ecs-352864 test01]# docker-compose up -d
Creating mysql8.0 ... done
Creating redis    ... done
Creating test01_my_demo_1 ... done
[root@ecs-352864 test01]# 
```

使用 swagger 进行测试：

![image-20220522130350574](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522130350574.png)

# 轻量级可视化工具 Protainer

 Portainer 是一款轻量级的应用，它提供了图形化界面，用于方便地管理 Docker 环境，包括单机环境和集群环境。

官网：https://www.portainer.io/

## 安装

```shell
[root@ecs-352864 docker]# docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /temp/docker/portainer/data:/data portainer/portainer-ce:2.9.3
Unable to find image 'portainer/portainer-ce:2.9.3' locally
2.9.3: Pulling from portainer/portainer-ce
0ea73420e2bb: Pull complete 
c367f59be2e1: Pull complete 
d11cdfc3c2c7: Pull complete 
Digest: sha256:84676dfce8ab328e51990797cceff5131c1ff63c3a73f5ebf1397cad9aa42e3c
Status: Downloaded newer image for portainer/portainer-ce:2.9.3
a9438192d3c8c314c55ef7ad0dd1242daefdc2433da85ec281835e710297fc9f
```

通过 https 请求访问 9443 端口，打开图形化界面：`https://121.37.217.252:9443/`

第一次登陆需要创建用户：这里密码是 12345678

![image-20220522183315784](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522183315784.png)

添加环境：

![image-20220522200254615](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522200254615.png)

![image-20220522200333644](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522200333644.png)

在次进入主页即可看到我们的容器

![image-20220522200423392](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522200423392.png)

点进去可以看到详细信息

![image-20220522200448538](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522200448538.png)

## 使用图形化界面安装容器

设置好后，点击部署即可

![image-20220522201458976](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220522201458976.png)

# Docker 容器监控之 CIG
CIG 即 CAdvisor+InfluxDB+Granfana，是三个工具组成的容器监控的重量级可视化工具

CAdvisor 用来收集监控数据

InfluxDB 是一个数据库，用来存储 CAdvisor  收集来的数据

Granfana 是一个图表展示的网页

## 使用 Docker Compose 集成部署

参考：https://www.bianchengquan.com/article/589980.html

docker-compose.yml 文件如下

```shell
version: '3.1'
volumes:
  grafana_data: {}
services:
  influxdb:
    image: influxdb
    restart: always
    environment:
      - PRE_CREATE_DB=cadvisor
    ports:
      - "8086:8086"
    expose:
      - "8090"
      - "8099"
    volumes:
      - ./data/influxdb:/data
  cadvisor:
    image: google/cadvisor
    links:
      - influxdb:influxdb-host
    command: -storage_driver=influxdb -storage_driver_db=cadvisor -storage_driver_host=influxdb-host:8086
    restart: always
    ports:
      - "8080:8080"
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:rw
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
  grafana:
    user: "104"
    image: grafana/grafana
    restart: always
    links:
      - influxdb:influxdb-host
    ports:
      - "3000:3000"
    volumes:
      - grafana_data:/var/lib/data
    environment:
      - HTTP_USER=admin
      - HTTP_PASS=admin
      - INFLUXDB_HOST=influxdb-host
      - INFLUXDB_PORT=8086
      - INFLUXDB_NAME=cadvisor
      - INFLUXDB_USER=root
      - INFLUXDB_PASS=root
```

服务正常启动后就可以 http://ip:3000 访问 Granafa，在 Home Dashboard 页面点击添加 data source

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d90997a.jpeg)

配置InfluxDB连接信息，当然在配置连接信息前需要进入InfluxDB容器创建相应的cadvisor数据库和用户root/root

在容器中创建cadvisor数据库和root用户

```
docker exec -it influxdb-contianer-id influx
> CREATE DATABASE "cadvisor"
> CREATE USER "root" WITH PASSWORD 'root' WITH ALL PRIVILEGES
```

配置连接InfluxDB连接：

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d89f9fa.jpeg)

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d850694.jpeg)

数据源配好之后可以回到Home Dashboard添加添加dashboard图表展示监控信息，Grafana提供了丰富的图片模板对监控数据进行展示。

添加dashboard：

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d81707d.jpeg)

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d7cd938.jpeg)

可选模板：

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d7929d1.jpeg)

编辑图表：

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d768810.jpeg)

配置监控cadvisor容器的内存使用情况的图表展示，配置好之后点击保存就可以了。

![img](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/600909d74a8b0.jpeg)
