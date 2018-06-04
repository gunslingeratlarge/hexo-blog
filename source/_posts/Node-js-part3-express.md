title: Node.js part3 express
author: gsal
tags:
  - nodejs
categories:
  - 区块链
date: 2018-04-24 10:56:00
---
### 安装与overview
`npm install express --save`安装express框架并自动改变package.json文件。  
#### 功能一： 路由
```  js
   var express = require("express");
   var app = express();
   
   app.get(path,function(req, res){});
```
path可以写路径，也可以写正则，还可以通过`/student/:number`来直接传入，用`req.params/number`来获得，或者通过正则表达式的小括号来获得。如
``` js
app.get(/^\/student\/([\d]{10})$/,function(req,res){
    res.send("学生信息，学号" + req.params[0]);
});
```
<!--more-->
注意，要使用正则要么就完全正则匹配，不要加双引号，整个路径就是一个正则。易犯的错误是加号搞成了逗号，弄成两个参数了。  
动手写代码的意义在于：
1. 明确自己可能犯的错误
2. 强化自己记忆不清楚的地方，确保自己需要的时候能复现
除此之外的练习其实也不是必要的。

#### 功能二：提供静态服务
```js
app.use(express.static("./public"));
```
可以直接用`localhost:3000/`访问public文件夹下的index.html，或者加上具体地路径如`localhost:3000/abc.html`就是访问public文件夹下的abc.html

#### 功能三：与ejs配合使用
先要设置view engine为ejs，将ejs模板文件放到views文件夹下，这样express会自动去views文件夹下找对应的ejs文件。与通常的路由不同的就是`res.render`部分了。
``` js
app.set("view engine","ejs");
app.get("/ejsdemo",function (req,res) {
    res.render("haha.ejs",{
        "news":["news1","newstwo","newsThree"]
    });
});
```

### 中间件
匹配按照从上往下，如果有多个匹配，那么只匹配上面的，如果想下面的也一起匹配，需要多写一个next()方法。
``` js
app.get("/",function(req,res,next){
    console.log("1");
    next();
});
app.get("/",function(req,res){
    console.log("2");
});
```
下面这个路由会发生冲突：
``` js 
app.get("/:username/:id",function(req,res){
    console.log("1");
    res.send("用户信息" + req.params.username);
});

app.get("/admin/login",function(req,res){
    console.log("2");
    res.send("管理员登录");
});
 
```
因为admin可以当做用户名，login可以当做id，解决方法有两个
1. 交换位置，匹配上第一个，就不会往下匹配了，具体的往上写，抽象的往下写。
``` js
app.get("/admin/login",function(req,res){
    console.log("2");
    res.send("管理员登录");
});

app.get("/:username/:id",function(req,res){
    console.log("1");
    res.send("用户信息" + req.params.username);
});

```
2. 检索数据库，如果没有这个用户名那么就调用`next()`
``` js
app.get("/:username/:id",function(req,res,next){
    var username = req.params.username;
    //检索数据库，如果username不存在，那么next()
    if(检索数据库){
        console.log("1");
        res.send("用户信息");
    }else{
       next();
   }
});

app.get("/admin/login",function(req,res){
    console.log("2");
    res.send("管理员登录");
});
```

#### app.use()
举例，app.use("/admin",callback),/admin可以匹配，/admin/aa可以，/admin/aa/bb也可以。而get和post都是精确的路由匹配。而use是只要/admin开头就可以.

``` js
app.use("/admin",function(req,res){ 
    res.write(req.originalUrl + "\n");   //    /admin/aa/bb/cc/dd
    res.write(req.baseUrl + "\n");  //   /admin
    res.write(req.path + "\n");   //    /aa/bb/cc/dd
    res.end("你好");
});
```
一些需要注意的小点：
1. 大多数情况下，渲染内容用res.render()，将会根据views中的模板文件进行渲染。如果不想使用views文件夹，想自己设置文件夹名字，那么app.set("views","aaaa");
2. 如果想写一个快速测试页，当然可以使用res.send()。这个函数将根据内容，自动帮我们设置了Content-Type头部和200状态码。send()只能用一次，和end一样。和end不一样在哪里？能够自动设置MIME类型。
3. 如果想使用不同的状态码，可以：
	res.status(404).send('Sorry, we cannot find that!');
4. 如果想使用不同的Content-Type，可以：
	res.set('Content-Type', 'text/html');

### GET与POST请求
对于get请求可以用req.query来获得请求语句。并且express会自动帮你转换为一个对象。对于post请求博旭引入bodyParser,但是如果有文件上传还是得用formidable
``` js
//bodyParser API
app.use(bodyParser.urlencoded({ extended: false }))

app.post("/",function(req,res){
    console.log(req.body);
});

```