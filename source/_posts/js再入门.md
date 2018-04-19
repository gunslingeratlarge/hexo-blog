title: js再入门
author: gsal
tags:
  - 前端
  - js
categories:
  - 前端
date: 2018-03-31 17:06:00
---
#### 一、输出语句
console.log("hello");  
document.write("hello");  
alert("hello");  
#### 二、全局变量
在函数外声明的变量  
没有加var的变量（注意）
#### 三、类型
##### 基本类型
String  
Number  
关注NaN  
not a number，console.log("abc"/18);结果是NaN，但实际上是一个number（很尴尬）  
undefined和任何数值计算都为NaN  
NaN与任何数值都不相等  
<!--more-->
##### Boolean  
""，undefined，null为false       
undefined == null    
与任何数值计算，结果仍为undefined     
null    
与任何数值计算，将null看作是0来计算    
null表示对象不存在，undefined表示未定义，比如var a;此时a的值就是undefined    
比如document.getElementById("xxelement");若元素不存在，则返回null    
##### 对象类型
Object  
function  
Array  
Date  
RegExp  
Error  
#### 四、数据类型转换
##### 转换成String  
变量+""  
String(变量)  
变量.toString()  
##### 转换为Number  
变量-\*/一个数字，如"11"-0  
Number(变量)  
parseInt(变量)：取整  
parseFloat(变量)：取浮点数  
##### 转换为Boolean  
Boolean(变量)
!!变量：对变量取反再取反
#### 五、函数
函数也是一种数据类型，归根结底，它还是属于Object。
##### 声明
function foobar() {}  
var foobar = function() {}  
##### 返回值  
函数如果没有return一个值，则返回值为undefined  
函数名=整个函数  
JS在加载的时候，只加载函数名，不加载函数体  
可以将函数赋给一个变量  
##### 变量声明提升和预解析
如果一个变量没有声明，直接console.log(a);会报错，引用未定义  
但是如果定义了var a;但是没赋值，不会报错，会打出undefined   
###### 预解析
1. 查看语法错误
2. 变量声明提升和函数整体提升  
只提升变量名，不提升变量值（相当于提前声明变量，但是赋值语句位置不变）
方法是整体提升的（用function直接定义的方法，即function foobar() {}）
3. 函数范围内也适用变量声明提升
4. 函数内只要自己局部有变量声明，就算需要变量声明提升，也是用自己的

##### 匿名函数
``` javascript
(function() {
    do something.
})();//执行该匿名函数
```
前面的括号包含函数体，后面的括号就是给匿名函数传递参数并立即执行之。匿名函数的作用是避免全局变量的污染以及函数名的冲突。还有其他写法的匿名函数，不一一例出。  
匿名函数通常在设置定时器(setInteval)，绑定事件(document.onclick = function(){})，和直接调用（自动执行）时使用。

##### 回调函数
简单理解：函数作为参数进行传递和使用
``` javascript
fn(test);
function fn(demo) {
    demo();
}

function test() {
    alert("1");
}
```
执行函数就等于函数名+();  整个函数+(); （函数名就等于整个函数）  
什么情况下使用回调函数？  
一般用于定义一个规则。
规则的传递只能通过函数实现，通过变量无法达成。（被传递的函数叫回调函数）
``` javascript
//对两个数按照某个规则进行运算
fn(10,5,demoAdd);
function fn(num1,num2.demo) {
     return demo(num1,num2);
}
//定义四个规则，加减乘除
function demoAdd(a,b) {
	return a + b;
}
function demoSub(a,b) {
	return a - b;
}
function demoTimes(a,b) {
	return a * b;
}
function demoOver(a,b) {
	return a / b;
}
```
#### 五、面向对象编程
js基于对象，没有类的概念，只能new出Object对象。
``` javascript
    //创建空白对象hero
    var hero = new Object();
    //绑定属性
    hero.money = 10000;
    hero.level = 6;
    hero.attack = function () {
        console.log("英雄攻击");
    }
```
当这样创建单个自定义对象时非常繁琐，因为要不停地绑定属性和方法。如果用for循环又不能修改名字的值。  
##### 函数创建对象
``` javascipt
var aaa = new Stu();
console.log(aaa);
function Stu() {
}
```
new做了这样几件事情：
1. 开辟了内存空间，存放新的对象
1. 把this设置为当前对象
1. 执行内部代码，设置对象属性和方法
1. 返回新创建的对象。正是如此stu中才没有return语句，aaa也可以拿到对象。
实际上一开始调用这个构造函数的是window，也就是说这个构造函数的this是window，new这个关键字将this改为了新的这个实例，然后再执行构造函数代码

如果要给aaa绑定属性和方法:
``` javascript
    function Stu(name) {
        this.name = name;
        this.sayHi = function () {
            console.log(this.name + "hi");
        }
    }
```
构造函数一般首字母大写。这里的Stu()就是构造函数。
但是typeof aaa还是Object。所以我们说js中无法创建自定义类型。
##### 对象属性绑定
``` javascript
    var xiaoming = new Object();
    xiaoming.name = "shuaige";//name:shuaige
    var aaa = "age";
    xiaoming[aaa] = 19; //age:19 
    xiaoming[0] = "abc"//'0':abc
```
对象名.属性或对象名\[变量\]或对象名\[值\]  
注意，如果方括号中是数字，如0或者'0'都会在map中存为'0':abc，但是引用的时候只能通过xiaoming[0]或者xiaoming['0']，而如果是用的xiaoming['name']或者xiaoming[name]最后都会是name:xiaoming，不会加引号

##### json
js可以直接使用json对象
``` javascript
var foo = {"aaa":1,"bbb":2,"ccc":3}
for(var key in foo){
    console.log(key);//aaa bbb ccc
    console.log(foo[key]);//使用方括号取值
}
```

#### 六、数组API
##### 判断数组与转换数组
1. instanceof:  是一个关键字。判断A是否是B类型。  
 布尔类型值 = A instanceof B ;  
2. Array.isArray()	//HTML5中新增    
 布尔类型值 = Array.isArray(变量) ;  
3. toString()	  
字符串  =  数组.toString();  
4. valueOf()		//返回数组对象本身  
数组本身 = 数组.valueOf();  
5. Join			//根据每个字符把数组元素连起来变成字符串  
字符串  =  数组.join(变量);  
变量可以有可以没有。不写默认用逗号分隔，无缝连接用空字符串。  

##### 数组增删和换位置
1. push()  //在数组最后面插入项，返回数组的长度  
数组1改后的长度  =  数组1.push(元素1);  
2. pop()    //取出数组中的最后一项，返回最后一项  
被删除的元素  =  数组1.pop();  
3. unshift()   //在数组最前面插入项，返回数组的长度  
数组1改后的长度  =  数组1.unshift(元素1);  
4. shift()        //取出数组中的第一个元素  
被删除的元素  =  数组1.shift();  
5. reverse()	//翻转数组（原数组将被反转，返回值也是被反转后的数组）  
反转后的数组  =  数组1.reverse();  
6. sort();    //给数组排序，返回排序后的数组。如何排序看参数。 
```
从小到大排序后的数组  =  数组1.sort(function(a,b){  
    return a-b;  
});
```
无参：按照数组元素的首字符对应的Unicode编码值从小到大排列数组元素。    
带参：必须为函数（回调函数--callback）。函数中带有两个参数，代表数组中的前后元素。如果计算后（a-b），返回值为负数，a排b前面。等于0不动。返回值为正数，a排b后面。  
 
##### 了解方法  
1. concat()  //把参数拼接到当前数组  
新数组 = 数组1.concat(数组2);  
2. slice() //从当前数组中截取一个新的数组，不影响原来的数组，参数start从0开始,end从1开始  
新数组 = 数组1.slice(索引1，索引2);
3. splice()//删除或替换当前数组的某些项目，参数start,deleteCount,options(要替换的项目)
新数组 = 数组1.splice(起始索引，结束索引，替换内容);
4. indexOf()、lastIndexOf()   //如果没找到返回-1
索引值 = 数组.indexOf/lastIndexOf(数组中的元素);

##### 迭代方法 
不会修改原数组  
```
every()、filter()、forEach()、map()、some()
数组/boolean/无 = 数组.every/filter/forEach/map/some(  
    function(element,index,arr){  
    程序和返回值；				          
   }
);
```
对数组中的每一个元素都进行一些操作，每个关键字都有自己的含义。
``` javascript

//对数组中每一项运行以下函数，如果都返回true，every返回true，如果有一项返回false，则停止遍历 every返回false；不写默认返回false
array.every(function(item,index,arr) {
})

//对数组中每一项运行以下函数，该函数返回结果是true的项组成的新数组
var arr = array.filter(function(item,index,arr) {
});
console.log(arr);  

//遍历数组
array.forEach(function(item,index,arr){
});

//对数组中每一项运行以下函数，返回该函数的结果组成的新数组
var arr = array.map(function(item,index,arr) {
    return "\"" + item + "\"";
})

//对数组中每一项运行以下函数，如果该函数对某一项返回true，则some返回true
var b =  array.some(function(item,index,arr) {
    if (item == "ww") {
        return true;
    }
    return false;
});
```

##### 清空数组
``` js
var array = [1,2,3,4,5,6];   
array.splice(0,array.length); //删除数组中所有项目   
array.length = 0; //length属性可以赋值，其它语言中length是只读  
array = [];  //推荐   
```
###### 构造函数的一个小细节
Number是包装类型,可以用`var i = new Number(111)`创建包装类型，此时i就是`Number {[[PrimitiveValue]]: 111}`，也可以`var i = Number(111)`，此时i就为基本数字111，原因是这样的，Number有一个构造函数，而这个函数有返回值，直接调用Number而不new的话就取的是返回值，有new就得到的是一个新的对象。  
``` js
function  Num(aaa){
    this["[[PrimitiveValue]]"] = num/1;
    return  aaa/1;
}
```