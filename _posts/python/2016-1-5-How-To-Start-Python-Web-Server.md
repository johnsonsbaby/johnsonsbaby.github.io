---
category: python
published: true
layout: post
title: 『 Python 』用Python快速优雅的搭建web服务器
description:
---

日常开发需要访问上传的图片、APK等资源文件，这时就需要搭建一个Http的服务器

比较流行的如Nginx、Apache等，但是又不需要那么重量级，就是能够快速搭建，快速访问就OK了

这里推荐使用简单优雅的Python自带功能，进入资源文件所在目录，一句话命令就可以搞定

<pre><code>
$ # python2
$ python –m SimpleHTTPServer 8080

$ # python3
$ python -m http.server 8080

</code></pre>

![Screen Shot 2015-12-31 at 12.15.48.png](../images/py_web/a0f3149b937071e8.png)

![Screen Shot 2015-12-31 at 12.43.41.png](../images/py_web/59f946df4e5261d7.png)

如有错误，敬请指正！
