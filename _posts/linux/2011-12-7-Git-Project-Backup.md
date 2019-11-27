---
category: linux
published: true
layout: post
title: 『 Linux 』Git项目备份和迁移
description: git项目的备份和迁移
---

### 备份项目

```shell
#!/bin/bash

BASE_DIR=/Users/jsbd/Desktop/git_`date +%Y%m%d`/
mkdir -p $BASE_DIR
PROJECT=(\
	'git@gitee.com:jsbd/project1.git' \
	'git@gitee.com:jsbd/project2.git' \
	'git@gitee.com:jsbd/project3.git' \
)

for project in ${PROJECT[@]};
do
	echo $project
	cd $BASE_DIR
	git clone $project
	echo "Git project $project cloned."
done

```

### 迁移项目

```shell
#!/bin/bash
# 迁移gitlab项目使用
# 全部下载下来后进入到目录中执行 git push --mirror XXX 就可以把项目推送到新的地址中去了

BASE_DIR=/Users/jsbd/Desktop/git_`date +%Y%m%d`/
mkdir -p $BASE_DIR
PROJECT=(\
	'git@my-gitlab.com:jsbd/project1.git' \
	'git@my-gitlab.com:jsbd/project2.git' \
	'git@my-gitlab.com:jsbd/project3.git' \
)

for project in ${PROJECT[@]};
do
	echo $project
	cd $BASE_DIR
	git clone --bare $project
	echo "Git project $project cloned."
done

```
