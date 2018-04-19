title: Node.js part1
author: gsal
tags:
  - nodejs
categories:
  - 区块链
date: 2018-04-17 19:01:00
---
### 特点
单线程、非阻塞I/O、事件驱动。这三点其实就是一点：因为其为单线程，如果是阻塞的话就没有办法处理多个请求，所以需要非阻塞I/O，而在处理某一个请求时如果之前的一个I/O操作做完了，怎么通知它？就要用到事件，所以需要事件驱动。
### 适合
I/O密集型的应用，无非代替php,java等传统后端语言，只是一个小工具。可以处理数万个并发请求。通常在大型网站中用来处理部分模块。
<!--more-->
### Hello World
``` js
var http = require("http");
http.createServer(
    function(req,res) {
        res.writeHead(200,{"Content-type":"text/html;charset=UTF-8"});
        res.end("Hello World");
    }
).listen(3000,"127.0.0.1");

```
http是`node`的一个模块，用`require`来引入，`res.writeHead`用来写响应头，`res.write`用来写响应，res.end用来写入并且结束响应。(不写res.end客户端会一直等待，就网页显示一直在加载中）。

#### 路由
nodejs是没有web容器的，也就是它自己内置了一个轻服务器（粗暴理解），而且它的path也不是真正的文件夹，而是用if...else...语句写的路由。
比如下面这段代码：
``` js
var http = require("http");
http.createServer(function(req,res) {
	var questurl = req.url;
	
	res.writeHead(200,{"Content-type":"text/html;charset=UTF-8"});
	if (questurl.substr(0,9) == "/student/") {
	
		var studentid = questurl.substr(9);
		if (/^\d{10}$/.test(studentid)) {
			res.write("查询学生：" + studentid);
		} else {
			res.write("学生id格式不正确");
		}
	} else  if (questurl.substr(0,9)=="/teacher/"){
		var teacherid = questurl.substr(9);
		if (/^\d{6}$/.test(teacherid)) {
			res.write("check teacher:" + teacherid);
		} else {
			res.write("teacher id wrong");
		}
	
	} else {
		res.write("身份类型错误");
	};
	res.end;
}).listen(3000,"127.0.0.1");
```

#### url
url模块用来处理url，有`url.parse(req.url,true)`，写true代表要将其转换为一个对象。一个url对象里面有很多东西，包括path，pathname（path去掉query），query，host，port，因此就可以用这个方法来处理GET请求了。
``` js
var http = require("http");
var url = require("url");

var server = http.createServer(function(req,res){
	//url.parse()可以将一个完整的URL地址，分为很多部分：
	//host、port、pathname、path、query
	var pathname = url.parse(req.url).pathname;
	//url.parse()如果第二个参数是true，那么就可以将所有的查询变为对象
	//就可以直接打点得到这个参数
	var query = url.parse(req.url,true).query;
	//直接打点得到这个参数
	var age = query.age;
	res.end();
});

server.listen(3000,"127.0.0.1");
```

#### 文件处理
用fs模块。
##### 文件读取
`fs.readFile("./test/1.txt",{"charset":"utf-8"},callback)`，读取文件。注意要用./表示路径，option可以不写，callback为function(err,data){}
``` js
var http = require("http");
var fs = require("fs");

var server = http.createServer(function(req,res){
	//不处理小图标
	if(req.url == "/favicon.ico"){
		return;
	}
	//给用户加一个五位数的id
	var userid = parseInt(Math.random() * 89999) + 10000;
	
	console.log("欢迎" + userid);

	res.writeHead(200,{"Content-Type":"text/html;charset=UTF8"});
	//两个参数，第一个是完整路径，当前目录写./
	//第二个参数，就是回调函数，表示文件读取成功之后，做的事情
	fs.readFile("./test/1.txt",function(err,data){
		if(err){
			throw err;
		}
		console.log(userid + "文件读取完毕");
		res.end(data);
	});
	
});

server.listen(3000,"192.168.41.30");
```
##### 创建文件夹
fs.mkdir(路径)
``` js
fs.mkdir("./test",function(err){
		if(err)
			throw err;
	});
```
也有回调函数，只不过只接受一个错误信息，要注意目录不可以多级创建，只能一级一级创建。
##### 判断是否是文件夹
``` js
fs.stat(path,function(err,stats) {
    stats.isDirectory();
    stats.isFile();
});
```
fs.stat方法给回调函数传入一个stats参数，这个参数可以用来判断是不是文件夹、文件等等，还可以查看各种信息。

##### 读取文件夹中有哪些文件
读取得到一个包含文件名的数组，使用`fs.readdir(path,callback)` 即可。
``` js
fs.readdir("./test",function (err,files) {
		console.log(files);
	});
//[ '111.txt', '222.txt', 'aaa', 'bbb', 'ccc' ]
```    
现在我只想读取其中的文件夹的信息（下面为错误示范）
``` js
	fs.readdir("./test",function (err,files) {
		console.log("所有文件：" + files);
		//现在我想输出所有的文件夹的名称
		var dirs = [];
		for (var i = 0; i < files.length; i++) {
			var filename = files[i];
			console.log(i);
			fs.stat("./test/" + filename, function(err,stats) {
				console.log(filename);
				console.log(stats.birthtimeMs);
				if (stats.isDirectory()) {
					dirs.push(filename);
				} 
				console.log(dirs);
			})
		}
		
	})
```
输出结果为:
```
0
1
2
3
4
ccc
1523971858776.6848
[]
ccc
1523971862328.894
[]
ccc
1523971849602.3806
[ 'ccc' ]
ccc
1523971852809.615
[ 'ccc', 'ccc' ]
ccc
1523971855378.7153
[ 'ccc', 'ccc', 'ccc' ]
```
原因是这样的，由于是异步的，所以fs.stat是在所有的循环完成之后才完成的（可以看到0 1 2 3 4在输出的最上面），而这个时候filename已经变成了ccc了，所以在stat的回调函数中去拿filename（比如输出和加入到dirs中）时，只能拿到ccc，但是本身在传入fs.stat()的第一个路径参数的时候还是正确的文件名，因此回调函数的参数`stats`还是正确的，所以最后dirs里有三个ccc，对应三个文件夹。  
正确的做法：将异步变为同步。很经常遇到。使用一个iterator函数，在异步的回调函数（这里的stat的回调）中调用iterator。
``` js
fs.readdir("./test",function (err,files) {
		console.log("所有文件：" + files);
		//现在我想输出所有的文件夹的名称
		var dirs = [];
		(function iterator(i) {		
			if (i == files.length) {
				res.end();
				console.log(dirs);
				return;
			}
			var filename = files[i];
			fs.stat("./test/" + filename, function(err,stats) {
				console.log(filename);
				if (stats.isDirectory()) {
					dirs.push(filename);			
				} 
				iterator(i + 1);
			})
		})(0);
	})
```

#### 静态资源文件管理
将static文件夹作为根目录，有路径对应关系的路由。要把mime类型写到res的头里面去。通过path.extname(pathname)获得扩展名。因此要引入path模块。显示的文件都是通过readFile真实读取来实现的。
``` js
var http = require("http");
var url = require("url");
var fs = require("fs");
var path = require("path");

http.createServer(function(req,res){
	//得到用户的路径
	var pathname = url.parse(req.url).pathname;
	//默认首页
	if(pathname == "/"){
		pathname = "index.html";
	}
	//拓展名
	var extname = path.extname(pathname);

	//真的读取这个文件
	fs.readFile("./static/" + pathname,function(err,data){
		if(err){
			//如果此文件不存在，就应该用404返回
			fs.readFile("./static/404.html",function(err,data){
				res.writeHead(404,{"Content-type":"text/html;charset=UTF8"});
				res.end(data);
			});
			return;
		};
		//MIME类型，就是
		//网页文件：  text/html
		//jpg文件 :   image/jpg
		var mime = getMime(extname);
		res.writeHead(200,{"Content-type":mime});
		res.end(data);
	});

}).listen(3000,"127.0.0.1");

function getMime(extname){
	switch(extname){
		case ".html" :
			return "text/html";
			break;
		case ".jpg" : 
			return "image/jpg";
			break;
		case ".css":
			return "text/css";
			break;
	}
}
```