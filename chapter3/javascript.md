## javascript

[javascript菜鸟教程](http://www.runoob.com/js/js-tutorial.html)

#### 前端三大块

1.html 负责页面结构

2.css 负责页面的显示，颜色，大小等

3.javsscript 负责页面的行为，用户交互等



#### javaScript组成概述

1.ECMAscript的语法，变量，函数，循环语句等语法

2.DOM文档对象模型 提供操作html和css的方法

```html
var x=document.getElementById("body1").title; 
```

3.BOM浏览器对象模型 提供操作浏览器的方法

```html
window.onload = function(){}
alert();
```



#### 嵌入页面的方式

1.行间事件（主要用于事件）不推荐使用

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
</head>

<body> 
	<div> body </div>
	<input type="button" name="" onclick="alert('ok!');">
</body>

</html>
```

2.页面script标签嵌入

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 


</head>

<body> 
	<div> body </div>
	<script type="text/javascript">
	var a="你好！";
	alert(a);
	</script>
</body>

</html>
```

3.外部引入

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
</head>

<body> 
	<div> body </div>
	<script type="text/javascript" src="./b.js">
	</script>
</body>

</html>
```

b.js

```
var a="你好！";
alert(a);
```

#### 注释

```
//单行注释
/*
	多行注释
*/
```

#### 变量

```
var a="hello world";
```

JavaScript 拥有动态类型,数据类型有：

基本数据类型: 字符串（String）、数字(Number)、布尔(Boolean)、空（Null）、未定义（Undefined）

复合类型:  对象(Object)



#### 如何获取元素

**通过document对象获取**

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
</head>

<body> 
	<div id="body1" title="this is a world"> body </div>

	<script type="text/javascript">
		var x=document.getElementById("body1").title; 
		alert(x);	
	</script>
</body>

</html>
```

**注意**： script 标签写在head里面时会有个错误，因为html顺序执行到script时，还没有对应的id.

**匿名函数的方式**

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script>
	window.onload = function(){
		var x=document.getElementById("body1").title; 
		alert(x);
	}
</script>

</head>

<body> 
	<div id="body1" title="this is a world!"> body </div>
</body>

</html>
```

#### 操作元素属性

获取到元素之后，可以对页面属性进行操作。

**操作方法**

1. "."操作
2. "[]"操作

**属性写法**

1.html属性和js的属性写法相同

2.class属性写成className

3.style属性里面的属性，有横杠的修改为驼峰式 （例如　font-size）

**修改属性**

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	window.onload = function(){
		document.getElementById('body1').title = "haha!";
		var x = document.getElementById('body1').title;
		alert(x);
	}
</script>
</head>
<body> 
	<div id="body1" title="this is a world!"> body </div>
</body>

</html>
```

**修改属性** 注意:font-size

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	window.onload = function(){
		var oDiv = document.getElementById('body1');
        oDiv.style['color']= 'red'; //使用[]访问
        oDiv.style.fontSize = "30px";
        
		var x = document.getElementById('link1').href;
		document.getElementById('link1').href = "http://www.qq.com";	
	}
</script>
</head>
<body> 
	<div id="body1" title="this is a world!"> body </div>
	<a href="http://www.baidu.com" id="link1">baidu</a>
</body>
</html>
```

**修改属性**　注意：class

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<title>Document</title>
<style type="text/css">
	.box01{
		width: 200px;
		height:200px;
		background-color: gold;	
	}

	.box02{
		width: 300px;
		height:300px;
		background-color: red;	
	}
</style>
<script type="text/javascript">
	
	window.onload = function(){
		var oDiv = document.getElementById("div1");
		oDiv.className = 'box02';
	}

</script>
</head>

<body> 
	<div id="div1" class="box01">  </div>

</body>

</html>
```

#### innerHTML

innerHTML可以读取或者写入标签包裹的内容

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	window.onload = function(){
		var oDiv = document.getElementById('body1');
		var x = oDiv.innerHTML;
		alert(x);
		var oDiv2 = document.getElementById('body2');
		oDiv2.innerHTML ="this is body 2";
		var oDiv2 = document.getElementById('body3');
		oDiv2.innerHTML ="<a href='http://www.baidu.com'>baidu</a>";

}
</script>

</head>

<body> 
	<div id="body1" title="this is a world!"> body </div>
	<div id="body2" title="this is a world!">  </div>
	<div id="body3" title="this is a world!">  </div>
</body>
</html>
```

#### 函数

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	function aa(){
		alert('hello!');
	}
</script>
</head>
<body> 
	<input type="button" value="alertWindow" onclick="aa()">
</body>
</html>
```

**提取行间事件**　

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	window.onload = function(){
		var oBtn1 = document.getElementById("btn1");	
		oBtn1.onclick = aa;
	}
	
	function aa() {
		alert('hello!');
	}
</script>

</head>

<body> 
	<input type="button" value="alertWindow" id="btn1">
</body>

</html>
```



### javascript初级

#### 变量与预解析

javascript解析过程分为两个阶段:先编译，后执行，在编译阶段会将function定义的函数提前，并将var定义的变量

声明提前，并赋值为undefined.

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	aa(); //调用可以放到定义之前
	function aa() {
		alert('hello!');
	}
</script>

</head>

<body> 
	<input type="button" value="alertWindow" id="btn1">
</body>

</html>
```



#### 匿名函数

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	window.onload = function(){
		var oDiv = document.getElementById('div1');
		oDiv.onclick = function(){
			alert('hello');
		}
	}
</script>
</head>
<body> 
	<div  id="div1" >这是一个div元素</div>
</body>
</html>
```

#### 函数传参

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	

	window.onload = function(){
		var oInput1 = document.getElementById('input1');
		var oInput2 = document.getElementById('input2');
		var obtn = document.getElementById('btn1');

		obtn.onclick = function(){
			var a = oInput1.value;
			var b = oInput2.value;
			//alert(a);
			var val = add(a, b);
			//alert(val);
		}

		function add(a, b){
			var c = parseInt(a) + parseInt(b) ;
			alert(c);
			return c;
		}

	}

</script>

</head>

<body> 
    <input type="text" name ="" id="input1">
    <input type="text" name="" id="input2">
    <input type="button" name="" value="add" id="btn1">
</body>

</html>
```

#### 数组

```html
var arr = new Array();
var arr = [1,2,"abc"];
//方法
arr.length
//访问元素
arr[0]
```

#### 字符串

```html
parseInt()
parseFloat()

var f=0.1
var g=0.2
//相加等于0.300000000004的问题解决方法是先乘100在除以100
alert((parseFloat(f)*100 + parseFloat(g)*100)/100)
//将一个字符串分割成一个数组
split()
//获取字符串中某一个字符
charAt()
//查找字符串是否包含某字符
indexOf()
//截取字符串
substring()
 
toUpperCase()
toLowerCase()
```

#### 程序调试方法

1. alert


2. console log
3. document.title

#### 定时器

定时器在javascript中的作用

1. 制作动画
2. 异步操作
3. 函数缓冲与节流

```html
/*
	setTimeOut();   只执行一次的定势器
	clearTimeout(); 关闭只执行一次的定时器
	setInterval()    反复执行的定时器
	clearInterval()  关闭反复执行的定时器
*/

<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	
    setTimeout(function(){
        alert('hello');
    }, 1000);

</script>

</head>
<body>    
</body>
</html>
```

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript">
	
	var timer = setTimeout(function(){
        alert('hello');
    }, 3000);

	clearTimeout(timer);
</script>
</head>
<body>  
</body>
</html>
```

**简单动画**

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<style type="text/css">
	.box{
		width:100px;
		height: 100px;
		background: gold;
		position: fixed;
		left: 20px;
		top: 20px;
	}
	
</style>
<script type="text/javascript">
	
window.onload = function(){

	var oBox = document.getElementById('box');
	var left = 20;

	var timer = setInterval(function(){
		left++;
		oBox.style.left = left + 'px';

		if (left > 700){
			clearInterval(timer);
		}
	}, 20);
}
</script>
</head>
<body> 
    <div class="box" id="box"></div>
</body>

</html>
```

#### 类型转换

1.直接转换

```html
parseInt()
parseFloat()
```

2.隐式转换

3.NaN 和 isNaN

NaN的含义是Not a Number

isNaN可以判断是否是数字

```html
var a = '123abc'
var c = 'abc123'
var i = 12
var j = '12'

var e= parseInt(a);  //alert弹出123
var g = parseInt(c); //alert() 弹出NaN
//可以用isNaN来判断
isNaN(a) //true
isNaN(c) //true
isNaN(i) //false
isNaN(j) //true
```

#### 变量作用域

注意：

* 函数内部如果不用var关键字定义变量，变量可能会变成全局变量

#### 封闭函数

实际就是匿名函数的另一种写法,目的是防止和其他的函数或者变量冲突。

```html
(function(){
 alert('hello');
})();

//其他写法  "!" 或者 “～”
!function(){
 alert('hello');
}();
```



#### 闭包

什么是闭包？

函数嵌套函数，内部函数可以引用外部函数的参数和变量，参数和变量不会被垃圾回收机制收回。