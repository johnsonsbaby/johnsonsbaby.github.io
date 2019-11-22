---
category: linux
published: true
layout: post
title: 『 Linux 』在CentOS7.2上安装MySQL5.7.16数据库
description: Install MySQL Database 5.7.16 on CentOS 7.2
---

# Install MySQL Database 5.7.16 on CentOS 7.2

## 1. Change root user

```shell
su -
## OR ##
sudo -i
```

## 2. Install MySQL YUM repository

```shell
## CentOS 7 ##
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
```

## 3. Update or Install MySQL 5.7.16

```shell
yum install mysql-community-server
```

## 4. Start MySQL server and autostart MySQL on boot

```shell
systemctl start mysqld.service ## use restart after update
systemctl enable mysqld.service
```

## 5. Get Your Generated Random root Password

```shell
grep 'A temporary password is generated for root@localhost' /var/log/mysqld.log |tail -1

# example result
2015-11-20T21:11:44.229891Z 1 [Note] A temporary password is generated for root@localhost: -et)QoL4MLid
```

## 6. MySQL Secure Installation

* Change root password
* Remove anonymous users
* Disallow root login remotely
* Remove test database and access to it
* Reload privilege tables

Start MySQL Secure Installation with following command

```shell
 /usr/bin/mysql_secure_installation
```

Output

```shell
Securing the MySQL server deployment.

Enter password for user root:

The 'validate_password' plugin is installed on the server.
The subsequent steps will run with the existing configuration of the plugin.
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) : Y

New password:

Re-enter new password:

Estimated strength of the password: 100
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : Y
By default, a MySQL installation has an anonymous user, allowing anyone to log into MySQL without having to have a user account created for them. This is intended only for testing, and to make the installation go a bit smoother. You should remove them before moving into a production environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : Y
Success.

Normally, root should only be allowed to connect from 'localhost'. This ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y
Success.

By default, MySQL comes with a database named 'test' that anyone can access. This is also intended only for testing, and should be removed before moving into a production environment.

Remove test database and access to it? (Press y|Y for Yes, any other key for No) : Y
- Dropping test database...
Success.

- Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y
Success.

All done!
```

Note: If you don’t want some reason, do a “MySQL Secure Installation” then at least it’s very important to change the root user’s password

```shell
mysqladmin -u root password [your_password_here
## Example ##
mysqladmin -u root password myownsecrectpass
```

## 7. Connect to MySQL database \(localhost\) with password

```shell
mysql -u root -p
## OR ##
mysql -h localhost -u root -p
```

## 8. Create Database, Create MySQL User and Enable Remote Connections to MySQL Database

_**This example uses following parameters:**_

* DB\_NAME = webdb
* USER\_NAME = webdb\_user
* REMOTE\_IP = 10.0.15.25
* PASSWORD = password123
* PERMISSIONS = ALL

```shell
## CREATE DATABASE ##
mysql> CREATE DATABASE webdb;

## CREATE USER ##
mysql> CREATE USER 'webdb_user'@'10.0.15.25' IDENTIFIED BY 'password123';

## GRANT PERMISSIONS ##
mysql> GRANT ALL ON webdb.* TO 'webdb_user'@'10.0.15.25';

## FLUSH PRIVILEGES, Tell the server to reload the grant tables ##
mysql> FLUSH PRIVILEGES;
```

## 9. Enable Remote Connection to MySQL Server –&gt; Open MySQL Port \(3306\) on Iptables Firewall \(as root user again\)

```shell
firewall-cmd --permanent --zone=public --add-service=mysql
## OR ##
firewall-cmd --permanent --zone=public --add-port=3306/tcp

## restart firewalld.service ##
systemctl restart firewalld.service

## Test remote connection ##
mysql -h 10.0.15.25 -u myusername -p

```
