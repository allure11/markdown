# fastDFS 分布式文件上传服务器

适合中小文件：4K - 500M

三个角色：

**Tracker Server：**跟踪服务器，主要做调度工作，起到均衡的作用；负责管理所有的 storage server和 group，每个 storage 在启动后会连接 Tracker，告知自己所属 group 等信息，并保持周期性心跳。

**Storage Server：**存储服务器，主要提供容量和备份服务；以 group 为单位，每个 group 内可以有多台 storage server，数据互为备份。

**Client：**客户端，上传下载数据的服务器，也就是我们自己的项目所部署在的服务器。

![image-20220413091332994](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220413091332994.png)

## FastDFS的存储策略

为了支持大容量，存储节点（服务器）采用了分卷（或分组）的组织方式。存储系统由一个或多个卷组成，卷与卷之间的文件是相互独立的，所有卷的文件容量累加就是整个存储系统中的文件容量。一个卷可以由一台或多台存储服务器组成，一个卷下的存储服务器中的文件都是相同的，卷中的多台存储服务器起到了冗余备份和负载均衡的作用。

在卷中增加服务器时，同步已有的文件由系统自动完成，同步完成后，系统自动将新增服务器切换到线上提供服务。当存储空间不足或即将耗尽时，可以动态添加卷。只需要增加一台或多台服务器，并将它们配置为一个新的卷，这样就扩大了存储系统的容量。

增加组就是增加容量，增加组中的服务器节点可以提高访问量

## FastDFS的上传过程

![image-20220413091720546](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220413091720546.png)

## FastDFS的文件同步

写文件时，客户端将文件写至group内一个storage server即认为写文件成功，storage server写完文件后，会由后台线程将文件同步至同group内其他的storage server。

每个storage写文件后，同时会写一份binlog，binlog里不包含文件数据，只包含文件名等元信息，这份binlog用于后台同步，storage会记录向group内其他storage同步的进度，以便重启后能接上次的进度继续同步；进度以时间戳的方式进行记录，所以最好能保证集群内所有server的时钟保持同步。

storage的同步进度会作为元数据的一部分汇报到tracker上，tracke在选择读storage的时候会以同步进度作为参考。

## FastDFS的文件下载

<img src="https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220413091924089.png" style="border: 2px solid #000;"/>

## 安装 fastDFS 

### 安装 fastDFS 之前需要先安装 gcc 等一些环境

```shell
[root@localhost ~]# yum -y install zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools wget  
```

### 安装 fastDFS 的依赖库 libevent

```sh
[root@localhost ~]# yum -y install libevent
```

### 安装libfastcommon-master

libfastcommon是FastDFS官方提供的，libfastcommon包含了FastDFS运行所需要的一些基础库。

安装前需要先将 `libfastcommonV1.0.7.tar.gz` 下载到服务器

```
下载：
wget https://github.com/happyfish100/libfastcommon/archive/V1.0.7.tar.gz
```

```shell
[root@localhost mydata]# tar -zxvf libfastcommonV1.0.7.tar.gz 
[root@localhost mydata]# cd libfastcommonV1.0.7
[root@localhost libfastcommon-1.0.7]# ./make.sh 
[root@localhost libfastcommon-1.0.7]# ./make.sh install
```

libfastcommon 安装好后会自动将库文件放至 /usr/lib64 下，但是 FastDFS 主程序设置的lib目录是 /usr/lib，我们可以使用软连接或直接复制的方式将文件移动至 /usr/lib

```shell
```

### 安装 fastDFS

先将 `FastDFS_v5.05.tar.gz`下载到服务器

```shell
[root@localhost mydata]# tar -zxvf FastDFS_v5.08.tar.gz
[root@localhost mydata]# cd FastDFS
[root@localhost FastDFS]# ./make.sh 
[root@localhost FastDFS]# ./make.sh install
```

安装成功后，会看到 /etc/init.d/ 下看到提供的脚本文件：

```shell
[root@localhost FastDFS]# ll /etc/init.d/ | grep fdfs
-rwxr-xr-x. 1 root root  1186 4月  13 09:52 fdfs_storaged
-rwxr-xr-x. 1 root root  1186 4月  13 09:52 fdfs_trackerd
```

- `fdfs_trackerd` 是 tracker 启动脚本
- `fdfs_storaged` 是 storage 启动脚本

能够在 /etc/fdfs/ 目录下看到默认的配置文件模板：

```shell
[root@localhost FastDFS]# ll /etc/fdfs/
总用量 20
-rw-r--r--. 1 root root 1461 4月  13 09:52 client.conf.sample
-rw-r--r--. 1 root root 7829 4月  13 09:52 storage.conf.sample
-rw-r--r--. 1 root root 7102 4月  13 09:52 tracker.conf.sample
```

- `tarcker.conf.sample` 是 tracker 的配置文件模板
- `storage.conf.sample` 是 storage 的配置文件模板
- `client.conf.sample` 是客户端的配置文件模板

安装成功后，需要将所有的 脚本复制到 /usr/local/bin/

```shell
[root@localhost FastDFS]# cp *.sh /usr/local/bin/
```

### 配置并启动tracker服务

`FastDFS` 的 `tracker` 和 `storage` 在刚刚的安装过程中，都已经被安装了，因此我们安装这两种角色的方式是一样的。不同的是，两种需要不同的配置文件。

我们要启动 `tracker`，就需要修改刚刚看到的 `tarcker.conf`，并且启动 `fdfs_trackerd` 脚本即可。

```shell
cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf
```

修改 `vi tracker.conf` ：

```shell
# the base path to store data and log files
base_path=/home/fastdfs   # 存储日志和数据的根目录

# HTTP port on this tracker server
http.server_port=80   # http 服务的端口
```

新建目录 `/home/fastdfs`:

```shell
[root@localhost fdfs]# mkdir /home/fastdfs
```

启用 `tracker` 服务：

```shell
service fdfs_trackerd start # 启动fdfs_trackerd服务，停止用stop
```

设置tracker服务开机启动:

```shell
[root@localhost FastDFS]# chkconfig fdfs_trackerd on
```

### 配置并启动storage服务

先复制配置文件模板：

```shell
cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf
```

修改配置文件 `/etc/fdfs/storage.conf`：

```shell
group_name=group1 	# 组名
base_path=/home/fastdfs 	# 数据和日志文件存储根目录 
store_path0=/home/fastdfs/fdfs_storage 	 # 第一个存储目录 
tracker_server=192.168.1.88:22122	#  tracker服务器IP和端口 
http.server_port=80
```

将解压缩目录中的 `/mydata/FastDFS/storage/fdfs_storaged` 移动到 `/usr/local/bin/`

```shell
[root@localhost storage]# cp fdfs_storaged /usr/local/bin/
```

新建 `/home/fastdfs/fdfs_storage`:

```shell
mkdir -p /home/fastdfs/fdfs_storage 
```

启动服务：

```shell
service fdfs_storaged start
```

设置开机自启动：

```shell
[root@localhost home]# chkconfig fdfs_storaged on
```

### 使用nginx访问FastDFS

#### 安装 fastdfs-nginx-module

将 `fastdfs-nginx-module_v1.16.tar.gz`  下载到服务器

解压缩：

```shell
[root@localhost mydata]# tar -zxvf fastdfs-nginx-module_v1.16.tar.gz 
```

修改配置文件 `/mydata/fastdfs-nginx-module/src/config`：

![image-20220413111941110](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220413111941110.png)

将 `FastDFS-nginx-module/src` 下的` mod_FastDFS.conf` 拷贝至` /etc/fdfs/` 下

```shell
[root@localhost src]# cp mod_fastdfs.conf /etc/fdfs/
```

修改 `/etc/fdfs/mod_fastdfs.conf`

```shell
base_path=/home/fastdfs
tracker_server=192.168.1.88:22122 
url_have_group_name=true 
store_path0=/home/fastdfs/fdfs_storage 
```

将 `libfdfsclient.so` 拷贝至 `/usr/lib` 下 :

```shell
[root@localhost lib64]#  cp /usr/lib64/libfdfsclient.so /usr/lib/
```

复制 FastDFS的部分配置文件到/etc/fdfs目录

```shell
cp /mydata/FastDFS/conf/http.conf mime.types /etc/fdfs/
```

#### 安装nginx

将 `nginx-1.8.1.tar.gz` 下载到服务器

解压缩：

```shell
[root@localhost mydata]# tar -zxvf nginx-1.8.1.tar.gz 
```

安装依赖（前面装过，可以在确认一下）：

```shell
[root@localhost mydata]# yum -y install pcre pcre-devel zlib zlib-devel openssl openssl-devel
```

进入 nginx 安装目录：

```shell
[root@localhost mydata]# cd nginx-1.8.1
```

配置 nginx 安装包，并指定 fastdfs-nginx-model

```shell
[root@localhost nginx-1.8.1]# ./configure --prefix=/opt/nginx --sbin-path=/usr/bin/nginx --add-module=/mydata/fastdfs-nginx-module/src
```

编译并安装：

```shell
make
make install
```

执行：

```shell
[root@localhost nginx-1.8.1]# useradd -s /sbin/nologin -M nginx
```

查看 nginx 信息：

```shell
[root@localhost nginx-1.8.1]# id nginx
```

启动 nginx ：

```shell
[root@localhost nginx-1.8.1]# nginx
```

停止 nginx ：

```shell
[root@localhost nginx-1.8.1]# ps -ef|grep nginx
```

修改 nginx 配置文件 `vi /opt/nginx/conf/nginx.conf`：

```shell

# 监听域名中带有group的，交给FastDFS模块处理
location ~/group([0-9])/ {
	ngx_fastdfs_module;
}
```

![image-20220413114939689](https://zym-notes.oss-cn-shenzhen.aliyuncs.com/img/image-20220413114939689.png)

