title: vue2.0 + Nodejs + MongoDB
author: gsal
date: 2018-05-03 07:57:32
tags:
---
### 整体架构

![整体架构](/images/imagefake.png)

#### MVVM

[对MVC的介绍](https://liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001434501628911140e1cb6ce7d42e5af81480f7ecd5802000)



[MVVM介绍](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/001475449022563a6591e6373324d1abd93e0e3fa04397f000)



#### RESTful API
关于RESTful API这部分转载自[廖雪峰的博客](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014735914606943e2866257aa644b4bdfe01d26d29960b000)。  
编写REST API，实际上就是编写处理HTTP请求的async函数，不过，REST请求和普通的HTTP请求有几个特殊的地方：
REST请求仍然是标准的HTTP请求，但是，除了GET请求外，POST、PUT等请求的body是JSON数据格式，请求的Content-Type为application/json；
REST响应返回的结果是JSON数据格式，因此，响应的Content-Type也是application/json。
REST规范定义了资源的通用访问格式，虽然它不是一个强制要求，但遵守该规范可以让人易于理解。

例如，商品Product就是一种资源。获取所有Product的URL如下：
```
GET /api/products
```
而获取某个指定的Product，例如，id为123的Product，其URL如下：
```
GET /api/products/123
```
新建一个Product使用POST请求，JSON数据包含在body中，URL如下：
```
POST /api/products
```
更新一个Product使用PUT请求，例如，更新id为123的Product，其URL如下：
```
PUT /api/products/123
```
删除一个Product使用DELETE请求，例如，删除id为123的Product，其URL如下：
```
DELETE /api/products/123
```
资源还可以按层次组织。例如，获取某个Product的所有评论，使用：
```
GET /api/products/123/reviews
```
当我们只需要获取部分数据时，可通过参数限制返回的结果集，例如，返回第2页评论，每页10项，按时间排序：
```
GET /api/products/123/reviews?page=2&size=10&sort=time
```

### VUE介绍与初步使用
核心思想：数据驱动、组件化 
不用去操作DOM，只用考虑数据层面，不用关注怎么去替换文本，操作dom，添加class等等。可以组件化，一个人做登录的模块，一个人做购物车的模块，很多页面有东西是共用的。

#### vue的引入
引入js并创建Vue的实例化对象，或者用npm安装vue,然后src改成node_modules/vue/dist/...之类的
``` js
<head>
    <meta charset="utf-8">
    <title>vuedemo</title>
    <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <div id="app">
        <span>{{message}}</span>
    </div>
    <script>
        var app = new Vue({
            el:"#app",
            data: {
                message:"hello vue"
            }
        });
    </script>
</body>
```

`npm i -g vue-cli`安装vue命令行工具，`vue init webpack-simple Demo5`初始化一个模板，或者用`vue init webpack Demo6`来初始化一个更加复杂一点的项目初始化模板。

#### vue的配置
##### build.js
chalk:日志高亮  
semver：版本校验  
通常来说只需要更改config文件夹中index.js，最多只需要更改webpack.base.conf.js就可以了。
[webpack的介绍](https://segmentfault.com/a/1190000006178770#articleHeader8)

#### Vue基础语法
```
模板语法：Mustache语法：{{msg}}  
Html赋值：v-html=""  
绑定属性： v-bind:id=""
使用表达式：{{ok?'YES':'NO'}}
文本赋值：v-text=""
指令：v-show，v-if区别在于v-show不会销毁dom再重新创建，v-if
```
##### 属性绑定
v-bind：是属性绑定的意思，比如v-bind:title="title",这里"title"就不是字符串了，而是js的表达式了，可以在""中直接写js内容了
##### 双向数据绑定
单向绑定：数据决定页面里的内容，而页面的内容改变却对数据没有影响。
v-model="content"，双向绑定其实只用于input，因为只有input里面的内容才可以改变（改变视图中的内容）  

体验todolist
 ``` js
 <!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript" src="node_modules/vue/dist/vue.js"></script>
</head>
<body>
<div id="root">
    <label for="addTodo"></label>
    <input  id="addTodo" type="text" v-model="inputValue">
    <button @click="addTodo">添加</button>
    <ul>
        <li v-for="todo in todos">{{todo}}</li>
    </ul>
</div>
    <script>
        var vm = new Vue({
            el:"#root",
            data: {
                inputValue:"",
                todos: []
            },
            methods: {
                addTodo: function() {
                    this.todos.push(this.inputValue);
                    this.inputValue = "";
                }
            }

        });

    </script>
</body>
</html>
 ```
 全局组件与局部组件的注册
 ``` js
 //全局组件的注册
 //       Vue.component("todo-item",{
//            template:"<li>item</li>"
//        });

//局部组件的注册
       var TodoItem = {
            template:"<li>item local</li>"
        };



        var vm = new Vue({
            el:"#root",
            components:{
                "todo-item":TodoItem
            },
            data: {
                inputValue:"",
                todos: []
            },
            methods: {
                addTodo: function() {
                    this.todos.push(this.inputValue);
                    this.inputValue = "";
                }
            }

        });
 ```
 
 每一个组件都是一个实例，而模板可以用挂载点的形式，也可以用template：""的形式写在实例位置。 父组件向子组件传值是通过props即属性的形式来传递的。子组件通过发布订阅的形式，子组件发布一个事件，而父组件正好会响应这个事件。因为要想在子组件里面删除list中的内容是不行的，只能在父组件里面删。
``` js
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8">
    <title></title>
    <script type="text/javascript" src="node_modules/vue/dist/vue.js"></script>
</head>
<body>
<div id="root">
    <label for="addTodo"></label>
    <input  id="addTodo" type="text" v-model="inputValue">
    <button @click="addTodo">添加</button>
    <ul>
        <todo-item  v-for="(todo,index) in todos"  :content="todo" :key="index" :index="index" @delete="deleteItem"></todo-item>
    </ul>
</div>
    <script>

//        Vue.component("todo-item",{
//            template:"<li>item</li>"
//        });

        var TodoItem = {
            template:"<li @click='clickItem'>{{content}}</li>",
            props:['content','index'],
            methods: {
                clickItem: function() {
                    this.$emit('delete',this.index);
                }
            }

        };



        var vm = new Vue({
            el:"#root",
            components:{
                "todo-item":TodoItem
            },
            data: {
                inputValue:"",
                todos: []
            },
            methods: {
                addTodo: function() {
                    this.todos.push(this.inputValue);
                    this.inputValue = "";
                },
                deleteItem: function(index) {
                    this.todos.splice(index,1);
                }
            }

        });

    </script>
</body>
</html>
```
#### vue-cli
这个工具就是一个官方的脚手架工具，
