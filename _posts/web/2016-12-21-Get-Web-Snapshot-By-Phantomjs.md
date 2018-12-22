---
category: web
published: true
layout: post
title: 『 前端 』简单网页截屏功能实现
description: PhantomJS组件简单应用
---

## snapshot.js

```js
var page = require('webpage').create(),
    system = require('system'),
    address, output;
var _width = 1440, _height = 1920;

if (system.args.length < 3 || system.args.length > 5) {
    console.log('Usage: rasterize.js URL filename');
    phantom.exit(1);
} else {
    address = system.args[1];
    output = system.args[2];
    if (system.args[3] != undefined) {
      _width = system.args[3];
    }
    if (system.args[4] != undefined) {
      _height = system.args[4];
    }
    page.viewportSize = { width: _width, height: _height };
    page.open(address, function start(status) {
      page.evaluate(function() {
       //此函数在目标页面执行的，上下文环境非本phantomjs，所以不能用到这个js中其他变量

       window.scrollTo(0,100000);//滚动到底部
       //window.document.body.scrollTop = document.body.scrollHeight;

      //  window.setTimeout(function()
      //  {
      //      var plist = document.querySelectorAll("a");
      //      var len = plist.length;
      //      while(len)
      //      {
      //          len--;
      //          var el = plist[len];
      //          el.style.border = "1px solid red";
      //      }
      //  },5000);
      });

      window.setTimeout(function (){
        page.render(output);
        console.log('success');
        phantom.exit();
      }, 5000+500);
    });
}

```


## WebSnapshot.java

```java
import java.io.IOException;
import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.Date;

public class WebSnapshot {

	public static void main(String[] args) {

		SimpleDateFormat format = new SimpleDateFormat("yyyyMMddHHmmss");
		String currentGmt = format.format(new Date());
		System.out.println("currentGmt -> " + currentGmt);

		String domain = "www.baidu.com";
		// String domain = "www.163.com";
		// if (args != null) {
		// 	domain = args[0];
		// }
		String pngFile = "/Users/jsbd/Desktop/" + domain + "." + currentGmt + ".png";

//		WebSnapshot.saveSnapshot(domain, pngFile);
		WebSnapshot.saveSnapshot(domain, pngFile, "1440", "1920");
	}

	public static String saveSnapshot(String domain, String webPngFile) {
		return saveSnapshot(domain, webPngFile, "1440", "1920");
	}

	public static String saveSnapshot(String domain, String webPngFile, String width, String height) {
		if (!domain.startsWith("http://")) {
			domain = "http://" + domain;
		}
		String result = "failure";
		String phantomjsHome = "/usr/local/bin/";
		String executeCommand = "/Users/jsbd/Documents/phantomjs/snapshot.js";

		StringBuffer command = new StringBuffer();
		command
		// .append(phantomjsHome)
		.append("phantomjs --ignore-ssl-errors=yes").append(" ");
		command.append(executeCommand).append(" ");
		command.append(domain).append(" ");
		command.append(webPngFile);

		if (width != null && height != null) {
			command.append(" ").append(width);
			command.append(" ").append(height);
		}

		System.out.println(command.toString());

		try {
			Process process = Runtime.getRuntime().exec(command.toString());
			InputStream eis = process.getErrorStream();
			byte[] buf = new byte[1024];
			int len = 0;
			while ((len = eis.read(buf)) != -1) {
				System.out.println(new String(buf, 0, len));
			}
			eis.close();

			InputStream is = process.getInputStream();
			buf = new byte[1024];
			while ((len = is.read(buf)) != -1) {
				result = new String(buf, 0, len);
				System.out.println(result);
			}
			is.close();

		} catch (IOException e) {
			e.printStackTrace();
		}
		return result;
	}

}
```
