title: Nodejs写相册
author: gsal
tags:
  - nodejs
categories: []
date: 2018-04-19 20:52:00
---
[相册首页](/album/views/index.html)
#### 需求
需求：
用户输入127.0.0.1时候，首页，就显示所有的文件夹列表：这些相册，都是真实文件夹的名字。uploads里面有aaa、bbb、ccc文件夹  
点击每个相册之后，进入的地址：  
127.0.0.1/aaa  
就能看见这个文件夹中的所有图片  
管理员界面  
127.0.0.1/admin，不需要密码即可登录  
有一个表单， 上传图片， 传到哪个文件夹里面。  当然，也有“新建相册”功能(fs.mkdir()函数)。
<!--more-->
#### 设计
主要是路由设计，先判断是不是文件，因为所有的请求，不管是对页面的请求（html）还是对文件的请求（如图片），都是以同样的方式处理的，因此先判断pathname中有没有'.'，如果有，代表是文件，读取文件并返回，如果没有，代表是文件夹或者是页面，因此就用多个if...else...语句对pathname进行判断，然后或者返回html，或者进行相关的操作比如创建文件夹等等。   
每一个页面都有相对应的ejs，在这个项目中前端和后端很难分开，前端也要先写成html，然后改成ejs，再在nodejs中render，有点像写jsp，感觉不是很舒服。  
而且node的异步是需要一定的时间来适应的，有时候你就是需要读完这些再去进行下一个操作，幸好有了`readFileSync`函数，但是总感觉写起来不得劲。总感觉牵牵扯扯，模块之间分得不清楚，前后端分得不清楚，命名也是乱命，css写得也乱，id满天飞。总而言之还是对web开发、node不熟悉。   
#### 实现
具体的代码已经传到了我的github上，这里记录几个小点：
1. js可以通过`window.location.href = url`来发送get请求，我就是用这个来处理创建dir的需求的。
1. 对返回的数据最好还是设置好它的mime类型，操作思路是这样的：
 - 有一个mime.json，它里面是所有的后缀名对应的mime类型，我们可以写一个函数，叫getMime(extname,callback)，在这个函数里面使用JSON.parse把mime.json读取成key-value形式并根据extname查到对应的mime值，然后callback(mime)，即把这个mime值传给回调函数。
 - 在调用这个getMime的时候我们就可以getMime(".jpg",function(mime){操作...})异步地使用到这个mime值了。