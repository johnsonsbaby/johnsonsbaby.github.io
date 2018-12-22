---
category: linux
published: true
layout: post
title: 『 运维 』CentOS 6.8_x64 Linux下基础环境搭建
description:
---


## 0x01 纪录Linux用户操作日志

root下将如下语句追加到 `# vi /etc/profile`文件中，然后 `# source /etc/profile`使配置生效。

{% highlight shell %}

USER_IP=`who am i 2>/dev/null| awk '{print $NF}'|sed -e 's/[()]//g'`
USER=`whoami`
export PROMPT_COMMAND="history -a"
if [ "$USER_IP" = "" ]; then
	USER_IP=$HOSTNAME
fi
if [ ! -d /tmp/.bash_history/$USER/ ]; then
	mkdir -p /tmp/.bash_history/$USER/
fi
export HISTSIZE=4096
export HISTTIMEFORMAT="%Y-%m-%d_%H:%M:%S "
DT=`date +%Y%m%d`
export HISTFILE="/tmp/.bash_history/$USER/${DT}_$USER_IP.log"
chattr +a /tmp/.bash_history/$USER/$DT_$USER_IP.log 2>/dev/null

{% endhighlight %}

## 0x02 MySQL-5.7

{% highlight shell %}

# rpm -ivh https://raw.githubusercontent.com/jsbd/learn/master/mysql/mysql57-community-release-el6-8.noarch.rpm
# yum repolist enabled |grep mysql
# yum -y install mysql-community-server
# sudo service mysqld start
# sudo service mysqld status
# sudo grep 'temporary password' /var/log/mysqld.log
# mysql -uroot -p
# mysql> use mysql;
# mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'yourNewPass!@3306';
# mysql> FLUSH PRIVILEGES;

# 新建普通用户
# mysql> use mysql;
# mysql> create user biz_offline IDENTIFIED by 'bizPass4Db@';
# mysql> show grants for biz_offline;
# mysql> grant all on *.* to biz_offline@’%’;
# mysql> FLUSH PRIVILEGES;

# 设置开机启动
# chkconfig mysqld on

{% endhighlight %}

## 0x03 Java1.8

{% highlight shell %}

# yum -y list java*
# yum -y install java-1.8.0-openjdk*
# java -version
openjdk version "1.8.0_101"
OpenJDK Runtime Environment (build 1.8.0_101-b13)
OpenJDK 64-Bit Server VM (build 25.101-b13, mixed mode)

{% endhighlight %}

## 0x04 Ant

{% highlight shell %}

# yum -y install ant
# ant -version
Apache Ant version 1.7.1 compiled on May 10 2016

{% endhighlight %}

## 0x05 Memcached

{% highlight shell %}

# yum search memcached
# yum -y install memcached

# 查看memcached配置文件
# cat /etc/sysconfig/memcached

# 设置开机启动memcached
# chkconfig memcached on
# service memcached start

{% endhighlight %}

## 0x06 Redis

{% highlight shell %}

# yum -y install epel-release
# yum -y install redis

# Redis配置文件
# cat /etc/redis.conf

# 设置开机启动redis
# chkconfig redis on
# service redis start

{% endhighlight %}

## 0x07 Haproxy

{% highlight shell %}

# yum -y install haproxy

#haproxy配置文件
# cat /etc/haproxy/haproxy.cfg

# 设置开机启动haproxy
# chkconfig haproxy on
# service haproxy start

{% endhighlight %}

## 0x08 Apache

{% highlight shell %}

# yum -y install httpd

# 设置开机启动apache
# chkconfig httpd on
# service httpd start

# apache配置文件
# cat /etc/httpd/conf/httpd.conf

{% endhighlight %}

## 0x09 Nginx

{% highlight shell %}

# rpm -ivh https://raw.githubusercontent.com/jsbd/learn/master/nginx/nginx-release-centos-6-0.el6.ngx.noarch.rpm
# yum -y install nginx

# nginx配置文件
# cat /etc/nginx/nginx.conf

# 设置开机启动nginx
# chkconfig nginx on
# service nginx start

{% endhighlight %}

## 0x0A 其他配置

- [关闭selinux设置](http://blog.csdn.net/hz_sencha/article/details/18410153)


- [禁用swap](http://www.huzs.net/?p=1683)


- [设置open files limit](http://www.cnblogs.com/moxiaopeng/articles/4593160.html)
