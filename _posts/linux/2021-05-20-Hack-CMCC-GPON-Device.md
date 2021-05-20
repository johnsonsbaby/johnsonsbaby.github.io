---
category: linux
published: true
layout: post
title: 『 Linux 』自己动手修改HG6201M移动光猫“桥接”网络模式
description: 自己动手，通过简单几步就能轻松获取光猫的超管权限，并修改为桥接网络模式
---

# 1、开启光猫的telnet服务

首先需要连接到光猫的网络，在浏览器执行 http://192.168.1.1/cgi-bin/telnetenable.cgi?telnetenable=1 开启服务

# 2、通过终端telnet登录光猫

```shell
telnet 192.168.1.1

username: root
password: hg2x0

```

# 3、查看光猫默认配置

```shell
~ # cat /flash/cfg/agentconf/factory.conf 
TelecomAccount=CMCCAdmin
TelecomPasswd=xxxxxxxx
UserAccount=useradmin
UserPasswd=XXXXXXX
……
……
……

```
前两行就是光猫超管的账号和密码

# 4、使用获取的账号和密码登录光猫进行配置