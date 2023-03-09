---
category: linux
published: true
layout: post
title: 『 Linux 』Nginx中Https证书和Gzip压缩配置
description: 从nginx日志计算活动的PV和UV，并持久化到数据库中
---

# Https证书配置

```shell
upstream yourdomain {
        server 127.0.0.1:6001 weight=1;
}

server {
        keepalive_timeout 300;
        listen 80;
        listen [::]:80;

        server_name yourdomain;

        location / {
                proxy_pass http://yourdomain;
                proxy_set_header X-Real-IP $remote_addr;
                client_max_body_size 20m;
        }
        #error_page 404 501 502 500 = /error.htm;
	# 强制将http请求转化为https请求
	return 301 https://$server_name$request_uri;
}

server {
      listen 443 ssl;
      server_name yourdomain;

      keepalive_timeout  300;

      ssl_certificate          /data/ssl/server.crt;
      ssl_certificate_key      /data/ssl/server.key;

      ssl_protocols       TLSv1 TLSv1.1 TLSv1.2;
      ssl_ciphers         AES128-SHA:AES256-SHA:RC4-SHA:DES-CBC3-SHA:RC4-MD5;
      ssl_session_cache   shared:SSL:10m;
      ssl_session_timeout 10m;
      ssl_prefer_server_ciphers on;

      location / {
          proxy_pass http://yourdomain;
          proxy_set_header X-Real-IP $remote_addr;
          client_max_body_size    100m;
      }
      #error_page  404 501 502 500 = /error.htm;
}

```


# Gzip压缩配置

```shell
# /etc/nginx/conf.d/gzip.conf

##
# CentOS Nginx Settings
# `gzip` Settings
#
#
gzip on;
gzip_disable "msie6";

gzip_vary on;
gzip_proxied any;
gzip_comp_level 6;
gzip_http_version 1.1;
gzip_buffers 4 16k;
gzip_min_length 256;
gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript application/javascript application/vnd.ms-fontobject application/x-font-ttf font/opentype image/svg+xml image/x-icon;


## Ubuntu Nginx Settings
gzip on;
gzip_disable "msie6";

gzip_min_length  1k;
gzip_vary on;
# gzip_proxied any;
gzip_comp_level 6;
gzip_buffers 4 16k;
#gzip_http_version 1.1;
# gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;
gzip_types  text/plain application/json application/javascript application/x-javascript text/css application/xml text/javascript image/jpeg image/gif image/png image/x-icon;

```