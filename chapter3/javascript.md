## javascript

[javascript菜鸟教程](http://www.runoob.com/js/js-tutorial.html)

#### 前端三大块

1.html 负责页面结构

2.css 负责页面的显示，颜色，大小等

3.javsscript 负责页面的行为，用户交互等



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

