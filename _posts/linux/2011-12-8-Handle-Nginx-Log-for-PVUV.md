---
category: linux
published: true
layout: post
title: 『 Linux 』从Nginx日志中计算活动访问量的PV和UV
description: 从nginx日志计算活动的PV和UV，并持久化到数据库中
---

# 从Nginx日志中计算活动访问量的PV和UV

```shell
#!/bin/bash
#
# configure parameters
MYSQL_CSV_FORMAT="fields terminated by ',' optionally enclosed by '\"' escaped by '\"' lines terminated by '\n'"

# month=`date +%Y%m`
# day=`date +%Y%m%d`
# default get yesterday's date string like yyyy-mm-dd
# online nginx log date formate
LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8

# MacOSX
# nginxLogDay=`date -v-1d +%d/%b/%G`
# preDay=`date -v-1d +%Y-%m-%d`
# cur=`date "+%Y-%m-%d %H:%M:%S.%s"`

# Ubuntu|CentOS
nginxLogDay=`date +%d/%b/%G -d "-1 days"`
preDay=`date +%Y-%m-%d -d "-1 days"`
cur=`date "+%Y-%m-%d %H:%M:%S.%N"`

# nginx log file
srcLogFile="/var/log/nginx/doc-jump.log"
destLogFile="/tmp/doc-jump.$preDay.log"
tmpUvFile="/tmp/log2uv_$preDay.csv"
tmpPvFile="/tmp/log2pv_$preDay.csv"

echo "MYSQL_CSV_FORMAT=$MYSQL_CSV_FORMAT"

cat $srcLogFile | grep -i ".*$nginxLogDay" > $destLogFile

rm -rf $tmpUvFile $tmpPvFile

cat $destLogFile | grep -i ".*jump\.htm?aid=[0-9]*" | awk '{for(i=12;i<=NF;i++) printf("%s",$i); print " "$1,$7;}' | awk -F 'aid=' '{print $2}'| sort -n |sed 's/[?&].*//g' | uniq -c | awk '{printf "'"$preDay,"'"; print $2","$1}' > $tmpPvFile
cat $destLogFile | grep -i ".*jump\.htm?aid=[0-9]*" | awk '{for(i=12;i<=NF;i++) printf("%s",$i); print " "$1,$7;}' | uniq | awk -F 'aid=' '{print $2}'| sort -n |sed 's/[?&].*//g' | uniq -c | awk '{printf "'"$preDay,"'"; print $2","$1}' > $tmpUvFile

echo "$cur export $tmpUvFile and $tmpPvFile done"

/usr/bin/mysql --local-infile -h 192.168.1.1  -uroot -proot -A <<EOF

use mydb;
load data local infile "$tmpPvFile" into table mydb.qrcode_activity_pv $MYSQL_CSV_FORMAT;
load data local infile "$tmpUvFile" into table mydb.qrcode_activity_uv $MYSQL_CSV_FORMAT;
EOF

echo "$cur mysql import data $tmpUvFile and $tmpPvFile done"

```