---
category: linux
published: true
layout: post
title: 『 运维 』从源代码编译安装Apache-httpd-2.4.23及虚拟主机和反向代理的配置
description:
---


在上一篇文章[CentOS 6.8_x64 Linux下基础环境搭建](http://blog.csdn.net/hz_sencha/article/details/52229470)介绍了通过`yum`来安装`Apache Http Server`。

本文将介绍通过源码方式编译安装多个`Apache Http Server`，并配置`基于名称的虚拟主机`和`反向代理`，编译安装的好处就是可以自定义一些配置，可以更好的学习和配置`Apache Http Server`。

## 源码下载

[All-In-One(apr-1.5.2.tar.gz apr-util-1.5.4.tar.gz httpd-2.4.23.tar.gz pcre-8.39.tar.gz)](http://download.csdn.net/detail/wcb2003/9617387)

## 安装步骤

下载源码及依赖包

首先需要安装依赖包`pcre-8.39.tar.gz`，然后将`apr-1.5.2.tar.gz`及`apr-util-1.5.4.tar.gz`分别解压至源码`httpd-2.4.23`目录下的`srclib/apr`和`srclib/apr-util`目录，确保依赖包都已经正确安装之后开始安装`ApacheHttpServer`。

{% highlight shell %}

# tar -zxvf pcre-8.39.tar.gz
# cd pcre-8.39
# ./configure --prefix=/opt/pcre
# make && make install

# tar -zxvf httpd-2.4.23.tar.gz
# tar -zxvf apr-1.5.2.tar.gz
# mv apr-1.5.2 httpd-2.4.23/srclib/apr

# tar -zxvf apr-util-1.5.4.tar.gz
# mv apr-util-1.5.4 httpd-2.4.23/srclib/apr-util

# cd httpd-2.4.23
# ./configure --prefix=/opt/apache80 --with-port=80 --with-pcre=/opt/pcre
# make
# make install

# ./configure --prefix=/opt/apache91 --with-port=91 --with-pcre=/opt/pcre
# make clean
# make
# make install

# ./configure --prefix=/opt/apache92 --with-port=92 --with-pcre=/opt/pcre
# make clean
# make
# make install

{% endhighlight %}

如果启动时报错`AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using 220.250.64.20. Set the 'ServerName' directive globally to suppress this message`，请修改文件`vi /opt/apache80/conf/httpd.conf`，将`ServerName www.example.com:80` 修改为 `ServerName localhost:80`之后再重新启动就OK了。

关于`configure`的更多配置项请移步参考[configure官方文档](http://httpd.apache.org/docs/2.4/programs/configure.html)。

## 启动服务器

首先修改三个web服务器的首页内容，以便于区分是来自不同的服务器内容。

{% highlight shell %}

# vi /opt/apache80/htdocs/index.html
# vi /opt/apache91/htdocs/index.html
# vi /opt/apache92/htdocs/index.html

{% endhighlight %}


分别启动三台Apache服务器

{% highlight shell %}

# /opt/apache80/bin/apachectl -k start
# /opt/apache91/bin/apachectl -k start
# /opt/apache92/bin/apachectl -k start

{% endhighlight %}

## 访问服务器

浏览器访问`http://yourip:port/`如果显示`It works!`则表明从源码编译安装的`Apache Http Server`服务器已经成功安装。

## 基于名称的虚拟主机配置和反向代理配置

### 服务规划

规划使用域名`www.example.com`使用80端口服务器的静态内容，一般用于网站首页；

规划使用域名`api.example.com`使用80端口服务器做反向代理，将请求映射到91和92服务器上；

下面开始配置80服务器的设置，打开`/opt/apache80/conf/extra/httpd-vhosts.conf`，配置如下内容:

{% highlight shell %}

# vi /opt/apache80/conf/extra/httpd-vhosts.conf
<VirtualHost *:80>
    ServerAdmin jingsibingdian@gmail.com
    ServerName www.example.com
    ServerAlias www.example.com
    DocumentRoot "/opt/apache80/htdocs"
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin jingsibingdian@gmail.com
    ServerName api.example.com
    ServerAlias example.com
    <Proxy balancer://apiCluster>
        BalancerMember http://localhost:91 loadfactor=1
        BalancerMember http://localhost:92 loadfactor=1
    </Proxy>
    ProxyRequests Off
    ProxyPass / balancer://apiCluster/ stickySession=JSESSIONID nofailover=Off
    ProxyPassReverse / balancer://apiCluster/
    ErrorLog "logs/error.log"
    CustomLog "logs/access.log" common
</VirtualHost>

{% endhighlight %}

然后配置`/opt/apache80/conf/httpd.conf`文件，去掉如下列表前面的`#`号:


{% highlight shell %}

Include conf/extra/httpd-vhosts.conf

LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule session_module modules/mod_session.so
LoadModule slotmem_shm_module modules/mod_slotmem_shm.so
LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so

{% endhighlight %}

至此，关于基于名称的虚拟主机和反向代理服务器端配置就配置完成了，如果需要测试的话，需要通过我们指定的域名进行访问查看效果，还要修改本机的`/etc/hosts`文件，将我们设置的域名`www.example.com`和`api.example.com`配置进去，然后就可以访问查看效果了。
