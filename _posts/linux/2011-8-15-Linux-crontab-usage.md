---
category: linux
published: true
layout: post
title: 『 运维 』Linux crontab 任务调度的使用
description:
---

## 任务调度的使用

 1. 设置定时任务命令 # crontab -e
 2. 每分钟去执行 * * * * * data >> /home/date.log
 3. 如何配置调度多个任务
     1. 命令行直接输入 # crontab  -e
     2. 把要执行的任务shell按行依次输入
     3. ESC退出编辑模式并保存任务调度文件
 4. 取消当前所有任务 # crontab -r
 5. 查看当前用户的调度任务列表 # crontab -l
 6. 重启任务调度服务 # service crond start
 7. 查看任务执行日志 # tail -f /var/log/cron


如有错误，敬请指正！
