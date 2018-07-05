title: xampp安装注意事项
author: gsal
tags:
  - php
categories:
  - php
date: 2018-06-26 20:43:00
---
## 配置php环境
@(php)[php，开发]
环境安装：  

XAMPP：v3.2.2  
(集成了PHP、Apache、Mysql)
 
数据库(工具使用：Navicat for mysql)：  
1. 建立连接的时候不要设置密码；  
2. 连接服务器上的数据库：
    连接信息：

3. 在本地建立连接运行sql文件greenLand1.sql；
<!--more-->
Apache部分的使用;  
    两个配置文件需要修改：(注意：**这两个文件都不是origin文件夹下的**)
 1. http.conf  
		在localhost:80下面添加localhost:8081修改端口号；  
 2. http-vhost.conf
    地址在D:\Program Files\XAMPP\apache\conf\extra
    需要添加的内容是：
    
``` xml
    <VirtualHost *:8081>
    	DocumentRoot "E:/project/greenland_web/public"
    	<Directory  "E:/project/greenland_web/public">
    		Require all granted
    	</Directory>
    </VirtualHost>
```
问题：  
    Apache和项目的关系，怎么丢给Apache。  

项目怎么跑起来：  
    1. 现在Apache的配置文件里配置要暴露的端口  
    2. 在vhost文件里配置好资源的位置。  
    外界因此可以通过端口访问到那个资源。  

逻辑：  
    浏览器——>Apache——>php——>(将php设置环境变量，即可以在全局调用，在项目的public文件夹执行命令：；)——>public下面有一个index.php文件(入口文件)——>Controller——>Controller经过一系列处理返回经过渲染后的页面。  
    
PhpStrom 2018版，到网上自己找破解码；（需要将hosts更改使得其无法连接验证网站）  
    调试安装：xDebug  
    1. 在Chrome里安装xDebug插件。  
    2. 在PhpStrom里配置xDebug。  
    用于断点调试。  
    
还要学会在PhpStrom中进行拉取项目
            监听状态

gitWindows桌面程序：
    TortoiseGit  

问题1：  
    那个命令是什么？  
    在public文件夹下执行：  
        php -S localhost:8080   

问题2：  
    浏览器中的http://localhost:8081/index/register/index.html  
    无法解析访问；  
    原因：  
        1. 项目根目录下有个文件：.htaccess里有正则式；  
        2. httpd.conf中有个配置：  
        LoadModule rewrite_module modules/mod_rewrite.so  
        3. 要使得那个正则式可读，还要将httpd.conf中的以下文件做修改(修改None为All)  
        4.  
        ``` 
        <Directory />
            AllowOverride all
            Require all denied
        </Directory>
    ```
问题3：  
    在linux下，运行项目会有Access Forbidden.  
    解决方案如上  
![clipboard.png](https://i.loli.net/2018/06/26/5b31f00372181.png)

    
每一次更改配置文件之后都要重启服务器；  

问题4：install模块缺失   
在E:\project\greenland_web\data下新建一个空白文件install.lock  
        
问题5：更改了http-vhost.conf之后不生效。原因是在httpd.conf中有一个include http-vhost.conf的配置在Linux下被default注释掉了。  

问题6：在windows下安装报无法找到-n文件错误，提示安装Microsoft visual C++ 2018，安装了之后还是不行。就**按照默认路径安装**即可解决此问题。  

问题7：
Apache Service detected with wrong path.Change XAMPP Apache and Control Panel settings orUninstall/disable the other service manually first  
Found Path: "H:\program files\xampp\apache\bin\httpd.exe" -k runservice
Expected Path: "h:\xampp\apache\bin\httpd.exe" -k runservice  
解决方案：首先，检查一下自己的环境变量的path中是否还残留以前自己添加的xampp路径，如果有就删掉，如果没有就进行下一步。
然后就要进入自己的注册表。（regedit）点击`HKEY_LOCAL_MACHINE---->SYSTEM---->currentControlSet---->Services---->找到Apache2.4`
ImagePath看看那个路径是不是以前那个版本的路径，如果是就选中ImagePath点击右键修改，把xampp里面提示的Expected Path后面的路径粘贴进去，然后确定。
其他的同样。
 
