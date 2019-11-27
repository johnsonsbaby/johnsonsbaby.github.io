---
category: linux
published: true
layout: post
title: 『 Linux 』MySQL数据库备份脚本
description: shell about mysql backup
---

## 备份MySQL数据库脚本合集

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
#####    开发服务器数据库全备脚本    #####
######################################

/usr/bin/mysqldump -f --single-transaction --master-data=2 --opt -uroot -proot --add-drop-table -R --all-databases |gzip > /var/dbbackup/dev/`date +%Y%m%d`_db.sql.gz
```

### 第三种：备份指定的几个数据库

```shell
#!/bin/sh
##################################
###   根据具体需求备份每个数据库   ###
##################################

for db_name in {"db_name_1","db_name_2","db_name_3","db_name_4","db_name_5","db_name_6","db_name_7"};
do
 echo "$db_name backup done on `date +%Y%m%d%H%M%S`. " >> ~/backup.log
 /usr/bin/mysqldump -uroot -proot $db_name |gzip > /var/dbbackup/db/$db_name`date +%Y%m%d%H%M%S`.sql.gz
 sleep 600
done
```

## 成品shell展示

```shell
#!/bin/bash
#Description:备份开发数据库

backupDir="/root/backup/dev/db_dev_`date +%Y%m%d`"
backupdb=("db1" "db2" "db3" "db4" "db5" "db6" "db7" "db8" "db9")

echo "Backup all DB : "${backupdb[@]} 
echo "total : ${#backupdb[*]}"

if [ ! -d ${backupDir} ];then
    mkdir -p ${backupDir}
else
    echo "backup dir is already exsits."
fi

for db in ${backupdb[@]} 
do
    dbname="${db}.sql.gz"
    echo "Current backup [$db] to [${backupDir}/${dbname}]."
    mysqldump --skip-secure-auth -uroot -proot $db | gzip > ${backupDir}/${dbname}
done

echo "OK, backup db successfully."
```


```shell
#!/bin/bash
#Description:从文件中恢复数据库

backupDir="`pwd`/dev/`date +%Y%m%d`"
backupdb=("db1" "db2" "db3" "db4" "db5" "db6" "db7" "db8" "db9")

if [ ! -d ${backupDir} ];then
    echo "backup dir not exsits."
    exit 0
fi

echo "Restore all DB : "${backupdb[@]} 
echo "total : ${#backupdb[*]}"

for db in ${backupdb[@]} 
do
    dbname="${db}.sql"
    echo "Current restore from [${backupDir}/${dbname}] to [$db]."
    mysql -uroot -proot ${db} < ${backupDir}/${dbname}
done

echo "OK, restore db successfully."
```


## 其他备份需求脚本
```shell

# 导出某个数据库－－结构+数据
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt db_name |gzip -9 > /db_bakup/db_name.gz

# 导出某个数据库的表－－结构+数据+函数+存储过程
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt -R db_name |gzip -9 > /db_backup/db_name.gz

 # 导出多个数据库
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --databases db_name1 db_name2 db_name3 |gzip -9 > /db_backup/mul_db.gz

 # 导出所有的数据库
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --all-databases |gzip -9 > /db_bak/all_db.gz

 # 导出某个数据库的结构
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-data db_name|gzip -9 > /db_bak/db_name.strcut.gz

 # 导出某个数据库的数据
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-create-info db_name|gzip -9 > /db_bak/db_naem.data.gz

 # 导出某个数据库的某张表
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt db_name tbl_name |gzip -9 > /db_bak/db_name.tal_name.gz

 # 导出某个数据库的某张表的结构
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-data db_name tal_name | gzip -9 > /db_bak/db_name.tal_name.struct.gz

 # 导出某个数据库的某张表的数据
shell> mysqldump -h192.168.161.124 -uroot -pxxxxxx --opt --no-create-info db_name tbl_name | gzip -9 > /db_bak/db_name.tbl_name.data.gz

#--opt==--add-drop-table + --add-locks + --create-options + --disables-keys + --extended-insert + --lock-tables + --quick + --set+charset
#默认使用--opt，--skip-opt禁用--opt参数

```


## MySQL备份远程数据库通用脚本

```shell

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
```


## MySQL备份本机数据库通用脚本

```shell
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
```


## MySQL按天建表通用存储过程

```shell
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
```