---
category: linux
published: true
layout: post
title: 『 Linux 』Linux Shell In Action
description: 关于iptables和MySQL数据库建表及备份数据的一些linux命令
---

## iptables允许指定IP访问指定端口

{% highlight shell %}

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

{% endhighlight %}

## MySQL备份远程数据库通用脚本

{% highlight shell %}

#!/bin/bash

MYSQL_CSV_FORMAT="fields terminated by ',' optionally enclosed by '\"' escaped by '\"' lines terminated by '\r\n'"
echo "MYSQL_CSV_FORMAT=$MYSQL_CSV_FORMAT"

month=`date +%Y%m`
day=`date +%Y%m%d`
preDay=`date +%Y%m%d -d "-1 days"`
cur=`date "+%Y-%m-%d %H:%M:%S.%N" `

rm -rf "/opt/cron/data/log/jsbd_app_status_rec_$preDay.csv"

/usr/bin/mysql -uUSERNAME -hHOSTNAME -P3306 -pPASSWORD --database db_log -ss -e "SELECT IFNULL(column_a, ''),
LOWER(column_b, ''), create_time FROM log_rec_$preDay" | sed 's/\t/","/g;s/^/"/g;s/$/"\r/g' > /opt/cron/data/log/log_rec_$preDay.csv

echo "$cur export log_rec_$preDay.csv done"

/usr/bin/mysql -uUSERNAME -hHOSTNAME -P3306 -pPASSWORD -A <<EOF
use jsbd_warehouse;
load data local infile '/opt/cron/data/log/log_rec_$preDay.csv' into table log_rec $MYSQL_CSV_FORMAT;

EOF
echo "$cur import log_rec_$preDay.csv done"

{% endhighlight %}


## MySQL备份本机数据库通用脚本

{% highlight shell %}

#!/bin/bash

MYSQL_CSV_FORMAT="fields terminated by ',' optionally enclosed by '\"' escaped by '\"' lines terminated by '\r\n'"
echo "MYSQL_CSV_FORMAT=$MYSQL_CSV_FORMAT"

month=`date +%Y%m`
day=`date +%Y%m%d`
preDay=`date +%Y%m%d -d "-1 days"`
cur=`date "+%Y-%m-%d %H:%M:%S.%N" `

rm -rf "/data/cron/data/log/log_rec_$preDay.csv"

/usr/local/mysql/bin/mysql -uUSERNAME -pPASSWORD -S /tmp/mysqld.sock -A <<EOF
use db_log;

SELECT IFNULL(column_a, ''), IFNULL(column_b, ''), create_time FROM log_rec_$preDay
into outfile '/data/cron/data/log/log_rec_$preDay.csv' $MYSQL_CSV_FORMAT;

EOF
echo "$cur export log_rec_$preDay.csv done"

/usr/local/mysql/bin/mysql -uUSERNAME -hHOSTNAME -P3306 -pPASSWORD -A <<EOF
use jsbd_warehouse;
load data local infile '/data/cron/data/log/log_rec_$preDay.csv' into table log_rec $MYSQL_CSV_FORMAT;

EOF
echo "$cur import log_rec_$preDay.csv done"

{% endhighlight %}


## MySQL按天建表通用存储过程

{% highlight shell %}

CREATE PROCEDURE `prc_log_rec_day_rec`(in_num int)
begin
DECLARE  t_sql varchar(4000);
DECLARE  t_idx_1 varchar(4000);
DECLARE  t_date date;
DECLARE  t_partion varchar(300);
DECLARE  table_part varchar(50);
DECLARE  num int;
set num=0;
set t_date= date_add(now(), interval 0 day);
while num < in_num do
select date_format (t_date,'%Y%m%d') into t_partion;
set table_part=concat('log_rec_', t_partion);
select count(1) from information_schema.tables where table_name = table_part into @cnt;
if @cnt = 0 then
    set t_sql= concat('create table ',table_part,
'(',
 '`column_a` varchar(50) NOT NULL,                           ',
 '`column_b` varchar(50) NOT NULL,                           ',
 '`create_time` datetime NOT NULL                            ',
')'
  );

   set t_idx_1 = concat('create index idx_log_rec_', t_partion, ' on log_rec_', t_partion, ' (create_time)');

SET @sql=t_sql;
PREPARE s1 FROM @sql;
EXECUTE s1;
DEALLOCATE PREPARE s1;

SET @sql=t_idx_1;
PREPARE s1 FROM @sql;
EXECUTE s1;
DEALLOCATE PREPARE s1;
end if;
set num=num+1;
select date_add(now(), interval num day) into t_date;
end while;
end

{% endhighlight %}
