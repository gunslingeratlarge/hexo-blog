title: ThinkPHP5.1开发初体验
author: gsal
tags:
  - php
categories:
  - php
date: 2018-06-30 15:24:00
---
## 结构
开始一个thinkphp应用，我们需要从github上clone两部分。一个是整个的应用结构的脚手架，另外一个是thinkphp的核心部分。   
首先克隆下载应用项目仓库   
`git clone https://github.com/top-think/think tp5`
然后切换到 tp5 目录下面，再克隆核心框架仓库：   
`git clone https://github.com/top-think/framework thinkphp` 

两个仓库克隆完成后，就完成了 ThinkPHP 的 Git 方式下载，如果需要更新核心框架的时候，只需要切换到thinkphp核心目录下面，然后执行：   
`git pull https://github.com/top-think/framework`     
这里面有很多文件夹，其中application是我们放模块的地方，config放配置，route放路由设置，5.1与5.0不同的地方是config和route都单独拿出来放到一个文件夹中去了。 
<!--more-->
## 控制器
控制器中返回视图有很多种方式，比如使用view()助手函数等等。常用的还是控制器继承Controller类，然后$this->fetch()即可。我们只需要掌握一种就可以了。目的只是能够实现这个效果就行。不用全都搞清楚。 
### 控制器处理请求
控制器有很多种处理请求的方式，但是以这种获得请求与拿参数的方式最为舒服。一开始的时候掌握一种即可， 之后有需要再掌握其他的。  
再处理完了请求之后，一种常用的方式是通过`$this->error()`或者`$this->success()`的方式
``` php
    public function add(){
        if ($this->request->isPost()) {
            $param = $this->request->param();
            $insertResult = Db::name('admin')->insert($param);
            if ($insertResult) {
                $this->success('添加管理员成功',url('lst'));
            } else{
                $this->error('添加失败');
            }
        } else {
            return view();
        }
    }
```
## 数据库
数据库查询也有很多方式。
但是最常用的还是Db::name('数据库名')然后跟上查询也好或者-\>的方式进行查询。  
可能需要注意的一个点就是批量查询的时候使用select()而非find()
## 模板
### 模板位置
目前我使用的方式是在同一模块下，建立与controller同级的文件夹view，view中的子文件夹和子文件夹下的html文件就分别代表控制器和对应的方法。一个方法对应一个html文件，这样在方法中可以直接使用$this->fetch()就可以找到对应的这个html文件而无需为fetch添加参数。
### 显示列表
在模板中怎样添加列表呢？遍历admins，每一个元素为admin   
``` html
{volist name="admins" id="admin" }
	{$admin.id} <br/>
	{$admin.name}
{/volist}
``` 
模板中的跳转路径，`action="{:url('admin/add')}"`表示Admin控制器的add方法。这里冒号的意思我猜大概表示后面是个url?跟$表示后面是个变量差不多？如果就是同一个控制器的同一个方法，那么action后面直接留空也成。不过还是建议都写上避免出错。
## 小结
总体而言，php开发简单快速，效率高，特别适合开发中小型网站并进行快速迭代。尤其配合框架的使用，让没有怎么接触php开发的人也能够快速上手从事web应用的开发。