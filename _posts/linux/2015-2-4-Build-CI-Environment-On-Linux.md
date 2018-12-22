---
category: linux
published: true
layout: post
title: 『 运维 』Linux下持续集成环境(CI)的搭建
description:
---

本文分两部分介绍持续集成(Continuous Integration)环境中所用到的各种常用软件的搭建和基础配置，目的是让开发人员快速了解并掌握持续集成环境的搭建及部署。其中第一部分主要是相关软件包在Linux系统中的搭建和设置，第二部分主要介绍持续集成环境的配置。

# 1)基础搭建部分

## 所需材料

### 操作系统

- 服务器：CentOS-6.6-x86_64位

### 软件包

- jdk-7u60-linux-x64.tar.gz
- apache-ant-1.8.2-bin.tar.gz
- apache-maven-3.0.3-bin.zip
- nginx-1.1.11.tar.gz
- haproxy-1.4.20.tar.gz
- redis-2.8.13.tar.gz
- libevent-2.0.16-stable.tar.gz
- memcached-1.4.7.tar.gz
- nexus-2.5.1-bundle.zip
- jenkins.tar.gz
- MySQL-client-5.6.19-1.el6.x86_64.rpm
- MySQL-devel-5.6.19-1.el6.x86_64.rpm
- MySQL-embedded-5.6.19-1.el6.x86_64.rpm
- MySQL-server-5.6.19-1.el6.x86_64.rpm
- MySQL-shared-5.6.19-1.el6.x86_64.rpm
- MySQL-shared-compat-5.6.19-1.el6.x86_64.rpm
- MySQL-test-5.6.19-1.el6.x86_64.rpm

## JDK、MAVEN、ANT

这几个软件包安装都比较简单，只需将下载好的软件包解压到指定文件夹，再设置系统环境变量就OK了；这里我默认都放在服务器的/opt/software文件夹下，如下命令所示

{% highlight shell %}

[root@localhost ~]# tar -zxvf jdk-7u60-linux-x64.tar.gz
[root@localhost ~]# tar -zxvf apache-ant-1.8.2-bin.tar.gz
[root@localhost ~]# unzip apache-maven-3.0.3-bin.zip
[root@localhost ~]# vi /etc/profile
export JAVA_HOME=/opt/software/jdk1.7.0_60
export ANT_HOME=/opt/software/apache-ant-1.8.2
export M3_HOME=/opt/software/apache-maven-3.0.3
export PATH=$JAVA_HOME/bin:$ANT_HOME/bin:$M3_HOME/bin:$PATH
[root@localhost ~]# source /etc/profile

{% endhighlight %}


## SVN

{% highlight shell %}

[root@localhost ~]# yum install subversion
[root@localhost ~]# svnserve -d -r /opt/svn

{% endhighlight %}

安装好后的访问地址：svn://yourip/project

## Nexus

{% highlight shell %}

[root@localhost ~]# unzip nexus-2.5.1-bundle.zip
[root@localhost ~]# /opt/software/nexus/nexus-2.5.1-01/bin/jsw/linux-x86-64/nexus start

{% endhighlight %}

安装好后的访问地址：http://yourip:8081/nexus/index.html

## Nginx

{% highlight shell %}

[root@localhost ~]# yum -y install zlib zlib-devel openssl openssl--devel pcre pcre-devel
[root@localhost ~]# tar -zxvf nginx-1.1.11.tar.gz
[root@localhost ~]# cd nginx-1.1.11 && ./configure && make && make install
[root@localhost ~]# /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

{% endhighlight %}

## HAProxy

{% highlight shell %}

[root@localhost ~]# tar -zxvf haproxy-1.4.20.tar.gz
[root@localhost ~]# cd haproxy-1.4.20
[root@localhost ~]# make TARGET=linux26 PREFIX=/usr/local/haprpxy
[root@localhost ~]# make install PREFIX=/usr/local/haproxy
[root@localhost ~]# /usr/local/haproxy/sbin/haproxy -f /usr/local/haproxy/conf/haproxy.cfg

{% endhighlight %}

安装好后的访问地址：http://yourip:port/haproxy_stats admin/admin

## Memcached

{% highlight shell %}

[root@localhost ~]# tar -zxvf libevent-2.0.16-stable.tar.gz
[root@localhost ~]# cd libevent-2.0.16-stable && ./configure && make && make install
[root@localhost ~]# whereis libevent-2.0.so.5
[root@localhost ~]# ldd /usr/local/bin/memcached
[root@localhost ~]# ln -s /usr/local/lib/libevent-2.0.so.5 /lib64/libevent-2.0.so.5
[root@localhost ~]# tar -zxvf memcached-1.4.7.tar.gz
[root@localhost ~]# cd memcached-1.4.7 && ./configure && make && make install
[root@localhost ~]# /usr/local/bin/memcached -d -u root -p 11211 -c 4096 -m 256
[root@localhost ~]# telnet localhost 11211
set key1 0 60 4
aaa
get key1

{% endhighlight %}

## Redis

{% highlight shell %}

[root@localhost ~]# tar -zxvf redis-2.8.7.tar.gz
[root@localhost ~]# cd redis-2.8.7 && ./configure && make && make install
[root@localhost ~]# ./src/redis-server redis.conf
[root@localhost ~]# redis-cli
redis 127.0.0.1:6379>
redis 127.0.0.1:6379> info  #查看server版本内存使用连接等信息
redis 127.0.0.1:6379> client list  #获取客户连接列表
redis 127.0.0.1:6379> client kill 127.0.0.1:33441 #终止某个客户端连接
redis 127.0.0.1:6379> dbsize #当前保存key的数量
redis 127.0.0.1:6379> save #立即保存数据到硬盘
redis 127.0.0.1:6379> bgsave #异步保存数据到硬盘
redis 127.0.0.1:6379> flushdb #当前库中移除所有key
redis 127.0.0.1:6379> flushall #移除所有key从所有库中
redis 127.0.0.1:6379> lastsave #获取上次成功保存到硬盘的unix时间戳
redis 127.0.0.1:6379> monitor #实时监测服务器接收到的请求
redis 127.0.0.1:6379> slowlog len #查询慢查询日志条数
(integer) 3
redis 127.0.0.1:6379> slowlog get #返回所有的慢查询日志，最大值取决于slowlog-max-len配置
redis 127.0.0.1:6379> slowlog get 2 #打印两条慢查询日志
redis 127.0.0.1:6379> slowlog reset #清空慢查询日志信息

{% endhighlight %}

## MySQL

- 删除系统包以防冲突 yum -y remove mysql-libs*

- 安装 rpm -ivh MySQL-server-5.6.15*.rpm
- 启动 service mysql start
- 查看root账号密码 cat /root/.mysql_secret
- 登录 mysql -uroot –pqKTaFZnl
- 修改密码 SET PASSWORD = PASSWORD('123456');


- 允许远程登录

  {% highlight mysql %}

  GRANT ALL PRIVILEGES ON . TO 'root'@'%' IDENTIFIED BY 'password' WITH GRANT OPTION;
  flush privileges;

  {% endhighlight %}


- 打开创建函数功能

  {% highlight mysql %}

  show variables like '%func%';
  set global log_bin_trust_function_creators=1;

  {% endhighlight %}


- 设置开机自启动

  {% highlight shell %}

  chkconfig mysql on
  chkconfig --list | grep mysql

  {% endhighlight %}


- MySQL的默认安装位置

  {% highlight shell %}

  /var/lib/mysql/               #数据库目录
  /usr/share/mysql              #配置文件目录
  /usr/bin                     #相关命令目录
  /etc/init.d/mysql              #启动脚本

  {% endhighlight %}

---

## 2)系统配置部分

2.1 要安装的插件列表如下
![plugin_list](../images/jenkins/config_5.png)
![plugin_list](../images/jenkins/config_6.png)

2.2 Jenkins系统配置
![sysconfig](../images/jenkins/config_1.png)
![sysconfig](../images/jenkins/config_2.png)
![sysconfig](../images/jenkins/config_3.png)
![sysconfig](../images/jenkins/config_4.png)

2.3 构建一个项目
![project](../images/jenkins/project_1.png)
![project](../images/jenkins/project_2.png)
![project](../images/jenkins/project_3.png)
![project](../images/jenkins/project_4.png)

2.4 Maven仓库之Nexus配置
![nexus](../images/jenkins/nexus_1.png)
![nexus](../images/jenkins/nexus_2.png)
