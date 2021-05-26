---
category: linux
published: true
layout: post
title: 『 Linux 』Mac下安装Docker并创建CentOS8
description: Docker实战，并创建资产供ansible学习使用
---

1. 安装／升级Docker客户端

对于10.10.3以下的用户 推荐使用Docker Toolbox

Mac安装文件：http://mirrors.aliyun.com/docker-toolbox/mac/docker-toolbox/

对于10.10.3以上的用户 推荐使用Docker for Mac

Mac安装文件：http://mirrors.aliyun.com/docker-toolbox/mac/docker-for-mac/

2. 配置镜像加速器

针对安装了Docker Toolbox的用户，您可以参考以下配置步骤：

创建一台安装有Docker环境的Linux虚拟机，指定机器名称为default，同时配置Docker加速器地址。

docker-machine create --engine-registry-mirror=https://5g3x4zth.mirror.aliyuncs.com -d virtualbox default
查看机器的环境配置，并配置到本地，并通过Docker客户端访问Docker服务。

docker-machine env default
eval "$(docker-machine env default)"
docker info
针对安装了Docker for Mac的用户，您可以参考以下配置步骤：

在任务栏点击 Docker Desktop 应用图标 -> Perferences，在左侧导航菜单选择 Docker Engine，在右侧输入栏编辑 json 文件。

将https://5g3x4zth.mirror.aliyuncs.com加到"registry-mirrors"的数组里，点击 Apply & Restart按钮，等待Docker重启并应用配置的镜像加速器。

3. 设置Mac宿主机网络访问到Docker中的镜像

由于使用了`Docker for Mac.dmg`安装的docker，从宿主机上访问docker容器就变成了一个大工程；具体解决方案可以参考：https://blog.csdn.net/cool0725/article/details/101271622

4. 安装centos8资产并访问

```shell
ssh默认的端口为22,我们将docker中centos的22端口映射到宿主机的5022端口
docker run -d -p 5022:22 --name lab01 --privileged=true centos /usr/sbin/init

进入容器
docker exec -it lab01 /bin/bash

安装常用工具
yum install -y passwd openssh-server vim lrzsz wget gcc-c++ pcre pcre-devel zlib zlib-devel ruby openssl openssl-devel patch bash-completion zlib.i686 libstdc++.i686 lsof unzip zip net-tools

更新系统
yum update

查看sshd服务
systemctl status sshd

如果没有则需要安装
yum install openssh-server

开机启动
systemctl enable sshd

开启服务
systemctl start sshd

验证
netstat -antp | grep sshd

配置ssh远程链接
vim /etc/ssh/sshd_config
#打开注释 PermitRootLogin yes, 允许密码登录,保存退出

设置root用户密码
passwd root

远程登录
ssh root@宿主机ip -p 5022

查看当前所有容器
docker ps -a

将已经安装好包的容器制作成自己的镜像
docker commit 容器ID  自己的用户名/镜像名称

查看本地镜像
docker images

将本地镜像推送到docker-hub
docker login
docker push 自己的用户名/镜像名称

删除本地镜像
docker rmi jsbd/lab_centos8

从服务器拉取镜像
docker pull jsbd/lab_centos8

新建其他资产
docker run -d -p 5023:22 --name lab02 --privileged=true jsbd/lab_centos8 /usr/sbin/init
docker run -d -p 5024:22 --name lab03 --privileged=true jsbd/lab_centos8 /usr/sbin/init
docker run -d -p 5025:22 --name lab04 --privileged=true jsbd/lab_centos8 /usr/sbin/init

```