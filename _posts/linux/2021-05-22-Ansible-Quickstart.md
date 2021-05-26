---
category: linux
published: true
layout: post
title: 『 Linux 』Ansible Quickstart
description: 快速起步学习ansible
---

# 1、安装

```shell

1.1 管理节点安装ansible组件

$ pip3 install ansible
$ ansible --version

1.2 管理节点与被管理节点建立信任关系

$ ssh-keygen -t rsa
$ ssh-copy-id root@localhost -p 5022
$ ssh-copy-id root@localhost -p 5023
$ ssh-copy-id root@localhost -p 5024
$ ssh-copy-id root@localhost -p 5025

```

# 2、资产和PlayBook

创建项目文件夹`ansible-project`，并编写如下三个文件。
```shell
2.1 编写inventory资产列表
$ cat hosts
1.1.1.1
3.3.3.[1:5]
test01.xxx.com
test02.xxx.com
test[03:08].xxx.com

[web_servers]
lab01 ansible_host=127.0.0.1 ansible_ssh_port=5022
lab02 ansible_host=127.0.0.1 ansible_ssh_port=5023

[db_servers]
lab03 ansible_host=127.0.0.1 ansible_ssh_port=5024
lab04 ansible_host=127.0.0.1 ansible_ssh_port=5025

2.2 编写ansible.cfg
$ cat ansible.cfg
[defaults]
inventory = hosts
remote_user = root


2.3 编写playbook 
$ cat ansible.yml
---
- name: Ansible Quickstart
  hosts: all
  tasks:
    - name: Test Network Connection
      shell: ping -c 10 www.baidu.com

- name: Update Yum Repository
  hosts: all
  tasks:
    - name: Update Yum
      shell: yum update
...

2.4 执行脚本
ansible all -m ping
ansible all -m command -a uptime
ansible all -a uptime
ansible all -a "ls /var/log"

2.5 执行剧本Playbook
ansible-playbook ansible.yml -vvv

```
