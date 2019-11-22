---
category: python
published: true
layout: post
title: 『 Python 』Python flask模块快速入门
description: Flask Quickstart.
---

# Python flask模块快速入门

安装`flask`模块

```shell
$ pip install flask
```

保存代码到`api_server.py`

```python
#THIS IS A WEBSERVER FOR DEMONSTRATING THE TYPES OF RESPONSES WE SEE FROM AN API ENDPOINT
from flask import Flask
app = Flask(__name__)

#GET REQUEST
@app.route('/readHello')
def getRequestHello():
	return "Hi, I got your GET Request!"

#POST REQUEST
@app.route('/createHello', methods = ['POST'])
def postRequestHello():
	return "I see you sent a POST message :-)"

#UPDATE REQUEST
@app.route('/updateHello', methods = ['PUT'])
def updateRequestHello():
	return "Sending Hello on an PUT request!"

#DELETE REQUEST
@app.route('/deleteHello', methods = ['DELETE'])
def deleteRequestHello():
	return "Deleting your hard drive.....haha just kidding! I received a DELETE request!"

if __name__ == '__main__':
    app.debug = True
    app.run(host='0.0.0.0', port=5000)
```

启动服务

```shell
$ python api_server.py
```

使用`curl`命令测试效果

```shell
GET        $ curl http://localhost:5000/readHello
POST       $ curl -d '' http://localhost:5000/createHello
PUT        $ curl -X PUT -d '' http://localhost:5000/updateHello
DELETE     $ curl -X DELETE -d '' http://localhost:5000/deleteHello
```

至此，一个使用flask模块的python web服务器开发完毕。
