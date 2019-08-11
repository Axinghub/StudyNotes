---
typora-copy-images-to: images
typora-root-url: ./
---

## 工具包下载：

ruby-2.4.4.tar.gz：https://cache.ruby-lang.org/pub/ruby/

redis-4.0.10.tar.gz：http://download.redis.io/releases/

redis-4.1.0.gem：https://rubygems.org/downloads/redis-4.1.0.gem

## 安装：

redis安装：

```shell
yum install gcc-c++
wget http://download.redis.io/releases/redis-4.0.10.tar.gz
tar -xzvf redis-4.0.10.tar.gz
cd redis-4.0.10
make && make install
```

关于gcc离线安装：

![1564140913895](/images/1564140913895.png)

![1564140953131](/images/1564140953131.png)

```shell
#在这个网站找到对应的rom文件
#http://vault.centos.org/6.5/os/x86_64/Packages/
#
#到这个目录底下安装
#rpm -Uvh *.rpm --nodeps --force
#gcc -v
#gcc-c++ -v
rpm -q gcc
rpm -q gcc-c++
```

## 安装ruby：

```shell
tar -xvzf ruby-2.4.4.tgz    
cd ruby-2.4.4
./configure
make
sudo make install
ruby -v
```

## 安装redis-4.1.0.gem：

```shell
#在线
gem install redis
#离线
gem install –l redis-4.1.0.gem
```

## 集群搭建 

创建一个文件夹redis-cluster，然后在其下分别创建6个文件夹：

```shell
mkdir /usr/local/redis-cluster

mkdir 7001 mkdir 7002 mkdir 7003 mkdir 7004 mkdir 7005 mkdir 7006
```

把redis.conf文件copy到700*文件加下，并修改其内容：（可以先修改一个，其欲再copy到其他文件夹，在修改端口号即可）

```shell
daemonize yes

port 700* //(端口号设置)

bind 192.168.1.187   //（ip地址，必须要绑定当前机器的IP）

dir /usr/local/redis-cluster/700*   //指定数据文件存放位置

cluster-enabled yes    //开启集群模式

cluster-config-file nodes-700*.conf     //最好和port对应

cluster-node-timeout 5000       //设置时间

appendonly yes   //开启日志

pidfile /var/run/redis/redis_700*.pid
```

## 分别启动6个实例

```shell
/usr/local/redis/redis-cluster/redis-server /usr/local/redis/redis-cluster/7001/redis.conf
/usr/local/redis/redis-cluster/redis-server /usr/local/redis/redis-cluster/7002/redis.conf
/usr/local/redis/redis-cluster/redis-server /usr/local/redis/redis-cluster/7002/redis.conf

/usr/local/redis/redis-cluster/redis-server /usr/local/redis/redis-cluster/7004/redis.conf
/usr/local/redis/redis-cluster/redis-server /usr/local/redis/redis-cluster/7005/redis.conf
/usr/local/redis/redis-cluster/redis-server /usr/local/redis/redis-cluster/7006/redis.conf
```

## 开启端口

```shell
/sbin/iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
service iptables save
service iptables restart
```



## 开启集群

```shell
cd /usr/local/redis/redis-4.0.10/src


./redis-trib.rb create --replicas 1 101.154.185.168:7001 101.154.185.168:7002 101.154.185.168:7003 101.154.185.168:7004 101.154.185.168:7005 101.154.185.168:7006
```





节点测试

```shell
#连接任意客户端

/usr/local/redis/redis-cli -c -h 123.206.135.116 -p 700*
```