title: Node.js part2
author: gsal
tags:
  - nodejs
categories:
  - 区块链
date: 2018-04-18 16:28:00
---
#### 引用模块
1. 某一个js文件中，提供了函数，供别人使用。 只需要暴露函数就行了； exports.msg=msg;
2. 某一个js文件，描述了一个类。   module.exports = 构造函数名;
 一个JavaScript文件，可以向外exports无数个变量、函数。但是require的时候，仅仅需要require这个JS文件一次。使用的它的变量、函数的时候，用点语法即可。所以，无形之中，增加了一个顶层命名空间。
``` js
	var msg = "你好";
	var info = "呵呵";
	
	function showInfo(){
	    console.log(info);
	}
	
	exports.msg = msg;
	exports.info = info;
	exports.showInfo = showInfo;
//在使用者中，只需要require一次。
	var foo = require("./test/foo.js");
//相当于增加了顶层变量。所有的函数、变量都要从这个顶层变量走：
	console.log(foo.msg);
	console.log(foo.info);
	foo.showInfo();
```
<!--more-->
如果require的时候没有路径也没有.js，就去找node_modules里面的文件夹，默认找index.js，如果名字不是这个要在package.json中配置。如果没有路径有js就去node_modules里面找对应的js。
使用`npm init`命令可以自动生成package.json。


#### post请求
原生post
``` js
var alldata = "";
//下面是post请求接收的一个公式
//node为了追求极致，它是一个小段一个小段接收的。
//接受了一小段，可能就给别人去服务了。防止一个过大的表单阻塞了整个进程
    req.addListener("data",function(chunk){
    alldata += chunk;
});
//全部传输完毕
    req.addListener("end",function(){
    console.log(alldata.toString());
    res.end("success");
});

```
如果要文件上传，那还是用第三方formidable比较好，能够更好地处理post请求封装到fields和files中。
form标签要加个属性：
```
<form action="http://127.0.0.1/dopost" method="post" enctype="multipart/form-data">

```
并且form中type=file的那个input还要加上name属性。这里给出源码：
```
 if(req.url == "/dopost" && req.method.toLowerCase() == "post"){
        //Creates a new incoming form.
        var form = new formidable.IncomingForm();
        //设置文件上传存放地址
        form.uploadDir = "./uploads";
        //执行里面的回调函数的时候，表单已经全部接收完毕了。
        form.parse(req, function(err, fields, files) {
            //if(err){
            //    throw err;
            //}
            //console.log(util.inspect({fields: fields, files: files}));

            //时间，使用了第三方模块，silly-datetime
            var ttt = sd.format(new Date(), 'YYYYMMDDHHmmss');
            var ran = parseInt(Math.random() * 89999 + 10000);
            var extname = path.extname(files.tupian.name);
            //执行改名
            var oldpath = __dirname + "/" + files.tupian.path;
            //新的路径由三个部分组成：时间戳、随机数、拓展名
            var newpath = __dirname + "/uploads/" + ttt + ran + extname;

            //改名
            fs.rename(oldpath,newpath,function(err){
                if(err){
                    throw Error("改名失败");
                }
                res.writeHead(200, {'content-type': 'text/plain'});
                res.end("成功");
            });
        });
    }else if(req.url == "/"){
        //呈递form.html页面
        fs.readFile("./form.html",function(err,data){
            res.writeHead(200, {'content-type': 'text/html'});
            res.end(data);
        })
    }else{
        res.writeHead(404, {'content-type': 'text/html'});
        res.end("404");
    }
```

#### 模板引擎
数据绑定
类似`<a href="<%= url %>"><img src="<%= imageURL %>" alt=""></a>`。后台模板引擎主要两个，ejs，jade。  
这里给一个简单的示例
``` js
var ejs = require("ejs");

//模板
var string = "好高兴啊，今天我买了iphone<%= a %>s";
//数据
var data = {
    a : 6
};
//数据绑定
var html = ejs.render(string, data);
//输出
console.log(html);
```
输出：好高兴啊，今天我买了iphone6s。 

当然也可以把string另外写到一个.ejs文件中去，data是一个对象，里面是键值对，然后用render传入data，string就从ejs里面读，最后输出html到response中即可。在服务器上组成页面，这样显示、数据没分清楚，不是发展的趋势。