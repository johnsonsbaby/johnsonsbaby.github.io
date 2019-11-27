---
category: linux
published: true
layout: post
title: 『 Linux 』CentOS7安装中文支持及中文字体
description: Linux中文支持及中文字体的安装方法
---

# CentOS7安装中文支持及中文字体

## 1. 查看系统正在使用的语言

```shell
# echo $LANG 
en_US.UTF-8 
```

## 2. 查看系统当下所有语言环境

```shell
# locale
LANG=en_US.UTF-8
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

## 3. 查看系统是否安装过那些中文语言

```shell
# locale -a |grep "zh_CN"
zh_CN
zh_CN.gb18030
zh_CN.gb2312
zh_CN.gbk
zh_CN.utf8 
```

## 4. 若是执行 locale -a |grep "zh_CN" 后未出现上述字符请先行安装以上字体包代码如下

```shell
# yum groupinstall "fonts"
```

## 5. 安装完成之后修改 locale.conf 文件

```shell
# vim /etc/locale.conf
LANG="en_US.UTF-8"  修改为如下：
LANG="zh_CN.UTF-8"
```

## 6. 保存后执行如下命令

```shell
 # source /etc/locale.conf
```

## 7. 安装中文字体库

```shell
# yum -y install fontconfig
```
安装完成后，你会发现在 /usr/share/ 目录下有一个 fonts 目录

## 8. 进入 fonst 目录在此新建一个chinese 然后给予权限755

```shell
# mkdir chinese
# chmod -R 755 /usr/share/fonts/chinese
```
接下来就是做最重要的一步，把自己Windows系统中的所有字库弄到 /usr/share/fonts/chinese/ 里面
直接就可以把 C:\Windows\Fonts 的所有文件复制过去即可

## 9. 上述弄完之后，安装搜索目录字库 ttmkfdir

```shell
# yum -y install ttmkfdir
```

## 10. 找到fonts.conf文件并修改

```shell
#vim /etc/fonts/fonts.conf
找到：
<dir prefix="xdg">fonts</dir> 这一行下面加入：

<dir>/usr/share/fonts/chinses</dir> 
```

## 11. 保存文件后执行

```shell
# fc-cache
```
这时就可以验证系统的中文支持及字体支持啦。