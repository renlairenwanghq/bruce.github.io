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

3.style属性里面的属性，有横杠的修改为驼峰式



