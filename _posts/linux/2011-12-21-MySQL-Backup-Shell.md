---
category: linux
published: true
layout: post
title: 『 Linux 』MySQL数据库备份脚本
description: shell about mysql backup
---

## 自动备份数据库脚本

### 第一种：备份指定数据库

```shell
#!/bin/sh

###########################
## 备份指定数据库脚本 ##
###########################

/usr/bin/mysqldump -uroot -proot db_name > /var/dbbackup/db_name_`date +%Y%m%d%H%M%S`.sql
```

### 第二种：全备数据库（无主从模式）

```shell
#!/bin/sh

######################################
##### 开发服务器数据库全备脚本 #####
######################################

/usr/bin/mysqldump -f --single-transaction --master-data=2 --opt -uroot -proot --add-drop-table -R --all-databases |gzip > /var/dbbackup/dev/`date +%Y%m%d`_db.sql.gz
```

### 第三种：备份指定的几个数据库

```shell
#!/bin/sh
##################################
### 根据具体需求备份每个数据库 ###
##################################

for db_name in {"db_name_1","db_name_2","db_name_3","db_name_4","db_name_5","db_name_6","db_name_7"};
do
 echo "$db_name backup done on `date +%Y%m%d%H%M%S`. " >> ~/backup.log
 /usr/bin/mysqldump -uroot -proot $db_name |gzip > /var/dbbackup/db/$db_name`date +%Y%m%d%H%M%S`.sql.gz
 sleep 600
done
```

## mysqldump导出－－数据+结构+(函数+存储过程)

导出某个数据库－－结构+数据

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt db\_name \|gzip -9 &gt; \/db\_bakup\/db\_name.gz

## 导出某个数据库的表－－结构+数据+函数+存储过程

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt -R db\_name \|gzip -9 &gt; \/db\_backup\/db\_name.gz

## 导出多个数据库

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --databases db\_name1 db\_name2 db\_name3 \|gzip -9 &gt; \/db\_backup\/mul\_db.gz

## 导出所有的数据库

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --all-databases \|gzip -9 &gt; \/db\_bak\/all\_db.gz

## 导出某个数据库的结构

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-data db\_name\|gzip -9 &gt; \/db\_bak\/db\_name.strcut.gz

## 导出某个数据库的数据

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-create-info db\_name\|gzip -9 &gt; \/db\_bak\/db\_naem.data.gz

## 导出某个数据库的某张表

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt db\_name tbl\_name \|gzip -9 &gt; \/db\_bak\/db\_name.tal\_name.gz

## 导出某个数据库的某张表的结构

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-data db\_name tal\_name \| gzip -9 &gt; \/db\_bak\/db\_name.tal\_name.struct.gz

## 导出某个数据库的某张表的数据

shell&gt;mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-create-info db\_name tbl\_name \| gzip -9 &gt; \/db\_bak\/db\_name.tbl\_name.data.gz

```shell
#--opt==--add-drop-table + --add-locks + --create-options + --disables-keys + --extended-insert + --lock-tables + --quick + --set+charset
#默认使用--opt，--skip-opt禁用--opt参数
```
