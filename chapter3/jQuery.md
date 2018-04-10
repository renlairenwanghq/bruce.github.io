## jQuery

jquery是目前使用最广泛的javascript函数库。

jquery是一个函数库，一个js文件，页面用script标签引入这个js文件即可使用。
```html
<script type="text/javascript" src="js/jquery-1.12.2.js"> </script>
```

用法思想1：选择某个网页元素，然后对它进行某种操作

用法思想2： 同一个函数既可以取值也可以赋值

#### 基本使用

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>

<script type="text/javascript">
 	alert($);
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
<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>

<script type="text/javascript">
/*
	$(document).ready(function(){
		var $div = $('#div');
		alert($div.html() + 'jquery');
	})
*/
	//上面ready的简写方式
	$(function(){
		var $div = $('#div');
		alert($div.html() + 'jquery');
	})

 	window.onload = function(){
		var oDiv = document.getElementById('div');
		alert(oDiv.innerHTML);
	}


</script>

</head>

<body> 
	<div id="div">这是一个div元素</div>
</body>

</html>
```

预期结果是先弹出 `这是一个div元素jquery`，结果我测试的时候先弹出的是`这是一个div元素`。

按说应该是ready执行的比onload快才对。

[谈谈document.ready和window.onload的区别](http://www.cnblogs.com/a546558309/p/3478344.html)



#### jQuery选择器

jquery选择器可以快速的选择元素，选择规则和css样式相同，使用length属性判断是否选择成功。

[jquery选择器](http://www.w3school.com.cn/jquery/jquery_ref_selectors.asp)

[jQuery总结](http://www.cnblogs.com/pig1314/p/8460987.html)

#####选择器

```
$(document) //选择整个文档
$('li') //选择所有的li元素
$('#myId') //选择id为myId的网页元素
$('.myClass') //选择class为myClass的元素
$('input[name first]') //选择name属性等于first的input元素
$('#ul1 li span') //选择id为ul1元素下的所有li的span元素
```

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<title>Document</title>
<style type="text/css">
	#div1{
		color:red
	}

	.box{
		color:green
	}

	.list li{
		background-color:gold;
		margin-bottom:10px;	
	}

</style>
<script type="text/javascript" src="js/jquery-3.3.1.min.js">
</script>
<script>
	$(function(){
		$('#div1').css({color:'pink'});
		$('.box').css({fontSize:'30px'});
		$('.list li').css({background:'green',color:'#fff'});
	});
</script>
</head>

<body> 
	<div id="div1"> 这是一个div元素 </div>
	<div class='box'>这是第二个div元素</div>
	<ul class='list'>
		<li>1</li>
		<li>2</li>
		<li>3</li>
		<li>4</li>
		<li>5</li>
		<li>6</li>
		<li>7</li>
		<li>8</li>
	</ul>
</body>
</html>
```

#####对选择集进行修饰过滤（类似css伪类)

```
$('#ul1 li:first') //选择id为ul1元素下的第一个li
$('#ul1 li:odd') //选择id为ul1元素下的li的奇数行
$('#ul1 li:eq(2)') //选择id为ul1元素下的第3个li
$('#ul1 li:gt(2)') //选择id为ul1元素下的前三个之后的li
$('#myForm : input') // 选择表单中的input元素
$('div:visible') //选择可见的div元素
```

#####对选择集进行函数过滤

```
$('div).has('p'); //选择包含p元素的div元素
$('div').not('.myClass'); // 选择class不等于myClass的div元素
$('div').filter('.myClass'); //选择class等于myClass的div元素
$('div').first(); //选择第一个div元素
$('div').eq(5); //选择第六个div元素
```

#####选择集转移

```
$('div').prev(); //选择div元素前面的第一个p元素
$('div').next(); //选择div元素后面的第一个p元素
$('div').nextAll('p'); //选择div元素后面的第一个p元素
$('div').closest('form'); //选择离div最近的那个form父元素
$('div').parent(); //选择div元素的父元素
$('div').children(); //选择div元素的所有子元素
$('div').siblings(); //选择div的同级元素
$('div').find('.myClass'); //选择div内的class等于myClass的元素
```

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>

<script type="text/javascript">
	$(function(){
        //读取样式
        alert( $('#div1').css('fontSize'))
        //设置样式（写入）
		$('#div1').nextAll('p').css({color:'red'});
		$('#span1').parent().css({width:'100px', height:'100px', background:'gold'})	
	})
	
</script>

</head>

<body> 
	<div id="div1">这是一个div元素</div>
	<div id="div2">这是一个div2元素</div>
	<p> 这是一个p元素</p>
	<div id="div3">
	 <a href="#">baidu</a>
	<span id="span1">span元素</span>
	</div>
</body>

</html>
```

#####操作行间样式

```
//获取div的样式
$('div').css('width');

//设置div的样式
$('div').css({width:'30px',color:'red'});
```

#####操作样式类名

```
$('#div1').addClass('divClass2'); //为id为div1的对象追加样式divClass2
$('#div1').removeClass('divClass'); //移除id为div1的对象的class名为divClass的样式
$('#div1').removeClass("divClass divClass2"); //移除多个样式
$('#div1').toggleClass("anotherClass"); //重复切换anotherClass样式
```

#### 绑定click事件

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>

<script type="text/javascript">
	$(function(){
		$('#btn').click(function(){
			$('.box').toggleClass("sty"); //注意是“sty”而不是“.sty”
		});
	})
	
</script>

<style type="text/css">
	.box{
		width:200px;
		height:200px;
		background-color:green;
		
	}
	
	.sty{
		background-color:gold;
	}
</style>
</head>

<body> 
	<input type="button" name="" value="切换" id="btn"></input>
	<div class="box"></div>
</body>

</html>
```

**实现的页面选择器**

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<style type="text/css">
	.btns{
		width: 500px;
		height: 50px;
	}

	.btns input{
		width:100px;
		height:50px;
		background-color#ddd;
		color:#666;
		border:0px;

	}

	.btns input.cur{
		background-color: gold;

	}

	.contents div{
		width:500px;
		height:300px;
		display: none;
		background-color: gold;
		line-height:300px;
		text-align:center;
	}

	.contents div.active{
		display:block;
	}
	
</style>
<script type="text/javascript"  src="js/jquery.js"> </script>

<script type="text/javascript"> 
	
$(function(){
	$('#btns input').click(function(){
		//this 是原生对象, $(this)指的是点击的那个对像
		$(this).addClass('cur').siblings().removeClass('cur');

		//获取当前按钮所在层级范围的索引值
		//alert($(this).index());

		$('#contents div').eq($(this).index()).addClass('active').siblings().removeClass('active');
	})
})
</script>
</head>
<body> 
	<div class="btns" id="btns">
		<input type="button" value="tab01" class="cur">
		<input type="button" value="tab02">
		<input type="button" value="tab03">
	</div>

	<div class="contents" id="contents">
		<div class="active">tab文字内容一</div>
		<div>tab文字内容二</div>
		<div>tab文字内容三</div>

	</div>
</body>
</html>
```

