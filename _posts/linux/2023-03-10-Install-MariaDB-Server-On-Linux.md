---
category: linux
published: true
layout: post
title: 『 Linux 』安装MariaDB Community Server最佳实践
description: 写作背景：记一次从MySQL平台迁移到MariaDB的测试环境准备
---


@[TOC](文章目录)

---

在主流Linux上安装`MariaDB Community Server`，官方当前（2023年3月10日）最新版本为10.11.2-GA，本文就以这个版本为例，进行安装测试。

# 安装

根据自己的主机平台`YUM (RHEL, CentOS), APT (Debian, Ubuntu), and ZYpp (SLES)`，选择以下不同的安装方式

## YUM (CentOS, RHEL)

```commandline
$ sudo yum install wget

$ wget https://r.mariadb.com/downloads/mariadb_repo_setup

$ echo "ad125f01bada12a1ba2f9986a21c59d2cccbe8d584e7f55079ecbeb7f43a4da4 mariadb_repo_setup" | sha256sum -c -

$ chmod +x mariadb_repo_setup

$ sudo ./mariadb_repo_setup --mariadb-server-version="mariadb-10.11"

$ sudo yum install MariaDB-server MariaDB-backup
```

## APT (Debian, Ubuntu)

```commandline
$ sudo apt install wget

$ wget https://r.mariadb.com/downloads/mariadb_repo_setup

$ echo "ad125f01bada12a1ba2f9986a21c59d2cccbe8d584e7f55079ecbeb7f43a4da4 mariadb_repo_setup" | sha256sum -c -

$ chmod +x mariadb_repo_setup

$ sudo ./mariadb_repo_setup --mariadb-server-version="mariadb-10.11"

$ sudo apt update

$ sudo apt install mariadb-server mariadb-backup
```

## ZYpp (SLES)

```commandline
$ sudo zypper install wget

$ wget https://r.mariadb.com/downloads/mariadb_repo_setup

$ echo "ad125f01bada12a1ba2f9986a21c59d2cccbe8d584e7f55079ecbeb7f43a4da4 mariadb_repo_setup" | sha256sum -c -

$ chmod +x mariadb_repo_setup

$ sudo ./mariadb_repo_setup --mariadb-server-version="mariadb-10.11"

$ sudo zypper install MariaDB-server MariaDB-backup
```

# 启动

操作     | 命令
:--------: | :-----
查看服务状态  | sudo systemctl status mariadb
启动  | sudo systemctl start mariadb
停止  | sudo systemctl stop mariadb
重启  | sudo systemctl restart mariadb
开机自启动  | sudo systemctl enable mariadb
开机不启动  | sudo systemctl disable mariadb

这里建议设置开机自启动，以省去服务器将来发生重启需要再登录启动数据库的操作。

# 配置

启动完毕，接着我们需要进行以下简单的配置，才能让应用程序远程连接。首先看一下`MariaDB Server`安装后的配置文件。

`RHEL、CentOS、SLES`系统上`MariaDB`的默认配置目录和文件如下：

* /etc/my.cnf
* /etc/my.cnf.d/enable_encryption.preset
* /etc/my.cnf.d/mysql-clients.cnf
* /etc/my.cnf.d/server.cnf
* /etc/my.cnf.d/spider.cnf

`Debian and Ubuntu`系统上`MariaDB`的默认配置目录和文件如下：

* /etc/mysql/my.cnf
* /etc/mysql/debian.cnf
* /etc/mysql/mariadb.cnf
* /etc/mysql/conf.d/
* /etc/mysql/mariadb.conf.d/50-client.cnf
* /etc/mysql/mariadb.conf.d/50-mysql-clients.cnf
* /etc/mysql/mariadb.conf.d/50-mysqld_safe.cnf
* /etc/mysql/mariadb.conf.d/50-server.cnf
* /etc/mysql/mariadb.conf.d/60-galera.cnf
* /etc/mysql/mariadb.conf.d/99-enable-encryption.cnf.preset/enable_encryption.preset

默认的配置文件肯定是不满足实际使用需求的，还需要进行参数设置和调优。官方不建议直接修改默认文件，而是自己创建一个自定义的配置文件，将需要修改的参数配置放在自定义文件中。

那么存放自定义文件的文件夹在哪里呢？官方建议如下：

`RHEL、CentOS、SLES`系统自定义文件存放位置
* /etc/my.cnf.d/

`Debian and Ubuntu`系统可以根据自己的偏好任选以下两个文件夹之一
* /etc/mysql/conf.d/
* /etc/mysql/mariadb.conf.d/

自定义文件名，要好记，容易区分如`z-custom-my.cnf`，自定义文件配置好如下

* /etc/my.cnf.d/z-custom-my.cnf

或者 

* /etc/mysql/mariadb.conf.d/z-custom-my.cnf

文件建好之后，就可以开始配置了。MariaDB会读取以`[mariadb]`或`[server]`开头的变量集合，这里任选一个就可以。

```commandline
[mariadb]
bind-address              = 0.0.0.0
key_buffer_size           = 128M
max_allowed_packet        = 10G
log_error                 = mariadb-err.log
innodb_buffer_pool_size   = 1G
```

更多自定义参数可以参考[system variables](https://mariadb.com/docs/server/ref/cs10.6/system-variables/)和[options](https://mariadb.com/docs/server/ref/cs10.6/cli/mariadbd/)。

保存好自定义文件，然后重启数据库`sudo systemctl restart mariadb`，接下来我们就可以进行登录测试了。


# 测试

## 本机登录

```commandline
$ sudo mariadb

Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 57
Server version: 10.11.2-MariaDB-1:10.11.2+maria~ubu2204 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```

## 远程登录

如果远程登录还需要进行以下简单的配置

```commandline
MariaDB [(none)]> create user root@'%' identified by 'root';
MariaDB [(none)]> grant all on *.* to 'root'@'%';
MariaDB [(none)]> flush privileges;
```

执行完以上命令后，赶紧去试试看吧。

如果远程连接不上，可能是防火墙没有打开对应的端口，需要修改防火墙规则。