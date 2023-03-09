---
category: linux
published: true
layout: post
title: 『 Linux 』从命令行登录FTP服务器下载文件
description: Linux Cheat Sheet
---

## 从命令行登陆FTP并下载文件

```shell
#!/bin/bash

PRE_DAY=`date +%Y-%m-%d -d "-1 days"`
HOST="ftp_server_ip"
PORT=ftp_server_port
USER="username"
PASS="password"

ftp -niv $HOST $PORT<<EOF
user $USER $PASS
bin
prompt off
get log_$PRE_DAY.tar.bz2
bye
EOF

```