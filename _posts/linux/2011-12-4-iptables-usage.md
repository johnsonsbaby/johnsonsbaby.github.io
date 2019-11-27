---
category: linux
published: true
layout: post
title: 『 Linux 』iptables常见用法
description: 关于iptables的用法
---

## 允许指定IP访问指定端口

```shell

#!/bin/sh
#iptables example
iptables -F
iptables -P INPUT ACCEPT
iptables -P OUTPUT ACCEPT
iptables -P FORWARD ACCEPT

iptables -A INPUT -p tcp --dport 80 -j ACCEPT

iptables -A INPUT -p tcp -s 192.168.0.0/16 --dport 3306 -j ACCEPT
iptables -A INPUT -p tcp -m multiport --dport 3306,91,92 -j DROP

iptables -A INPUT -p tcp -s 192.168.1.111 --dport 8080 -j ACCEPT
iptables -A INPUT -p tcp --dport 8080 -j DROP

iptables-save
```