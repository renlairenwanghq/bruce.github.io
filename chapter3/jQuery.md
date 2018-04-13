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

#### jQuery属性操作

1.html取出或者设置html的内容

```html
//取出html内容
var $htm = $('#div1').html();

//设置html内容
$('#div1').html('<span>添加文字</span>');
```

2.text()取出活着设置text的内容

```html
//取出文本内容
var $htm = $('#div1').text()

//设置文本内容
$('#div1').text('<span>添加文字</span>')
```

3.attr()取出或者设置某个属性的值

```html
//取出
var $src = $('#img1').attr('src');

//设置图片地址
$('#img1').attr({src:"test.jpg", alt:"Test image"});
```

#### jQuery特殊效果

```html
fadeIn()淡入
fadeOUt()淡出
fadeToggle()切换淡入淡出
hide() 隐藏元素
show() 显示元素
toggle() 切换隐藏和显示
slideDown()向下展开
slideUp()向上卷起
slideToggle() 切换展开和卷起

//或者添加一些控制参数
$('#div1').fadeIn(1000, 'swing', function(){
	alert('done');
})
```



#### jQuery链式调用

```html
$('#div1') //id为div1的元素
.children('ul') //该元素下面的ul子元素
.slideDown('fast') //高度从零到实际高度来显示ul元素
.parent() //跳到ul的父元素
.slideUp('fast') //高度从实际高度变换到零来隐藏ul元素
```

#### jQuery动画
jQuery animate() 方法允许您创建自定义的动画。
####jQuery尺寸

```
Query 提供多个处理尺寸的重要方法：
    width()
    height()
    innerWidth()
    innerHeight()
    outerWidth()
    outerHeight()
```

####jQuery事件
[jQuery 参考手册 - 事件](http://www.w3school.com.cn/jquery/jquery_ref_events.asp)

#### jQuery主动触发与自定义事件

主动触发

可以使用jquery对象上的trigger方法来触发对象上绑定的事件。

自定义事件
除了系统事件外，可以通过bind方法自定义事件，然后用triggle方法触发这些事件。

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>

<script type="text/javascript">
	/*
	$(function(){
		$('#btn').bind('click mouseover', function(){
			alert('hello');

			$(this).unbind('mouseover');
		});
		
	})

	*/
	$(function(){
		$('#btn').bind('hello', function(){
			alert('hello');

		});

		$('#btn').trigger('hello');
	})
	
</script>

<style type="text/css">
</style>
</head>
<body> 
	<input type="button" name="" value="切换" id="btn"></input>
</body>
</html>
```

####事件冒泡
[事件冒泡、阻止事件冒泡以及阻止默认行为(https://www.cnblogs.com/jsanntq/p/7681942.html)

事件冒泡 ：当一个元素接收到事件的时候 会把他接收到的事件传给自己的父级，一直到window 。
（注意这里传递的仅仅是事件 \并不传递所绑定的事件函数。所以如果父级没有绑定事件函数，就算传递了事件 也不会有什么表现 但事件确实传递了。）

有时需要阻止事件冒泡，来避免一些问题。

####事件委托（事件代理）

事件委托就是利用冒泡的原理，把事件加到父级上，通过判断事件来源的子集执行相应的操作，事件委托首先

可以极大减少事件绑定次数，提高性能，其次可以让新加入的子元素也可以拥有相同的事件。

```html
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<title>事件委托</title>
<script type="text/javascript" src="js/jquery-3.3.1.min.js"></script>

<script type="text/javascript">
	$(function(){	
/*
		$('.list li').click(function(){
			alert($(this).html());
		})	
*/
		$('.list').delegate('li', 'click', function(){
			alert($(this).html());

			$('.list').undelegate();
		})
	})

</script>

<style type="text/css">
	.list{
		list-style:none;
	}

	.list li{
		height:30px;
		background-color:gold;
		margin-bottom:10px;
		color:#fff;
	}
</style>
</head>

<body> 
<ul class="list">
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>4</li>
	<li>5</li>
	<li>6</li>
	<li>7</li>
	<li>8</li>
	<li>9</li>
</ul>
</body>
</html>
```

####节点操作

[总结一下jQuery操作元素节点的方法（创建、选择、插入节点）](https://blog.csdn.net/xinghuo0007/article/details/71437066)

#### 滚轮事件与函数节流

jquery.mousewheel插件使用

jquery中没有鼠标滚轮事件，原生js中的鼠标滚轮事件不兼容，可以使用jquery的滚轮事件插件jquery.mousewheel.js

函数节流

[JavaScript 函数节流](https://blog.csdn.net/XIAOZHUXMEN/article/details/51555842)

javascript中有些事件的触发频率非常高，比如滚轮事件，对于在短时间内多次触发执行绑定的函数，可以巧妙地使用定时器来减少触发的次数，实现函数节流。

## Ajax
ajax技术的目的是让javascript发送http请求，与后台通信，获取数据和信息。ajax技术的实现原理是xmlhttp对象，使用此对象与后台通信，ajax通信的过程不会影响后续javascript的执行，从而实现异步。

**局部刷新和无刷新**

ajax可以实现局部刷新也叫无刷新，无刷新是指整个页面不刷新，而是局部刷新，ajax可以自己实现http请求，不用通过浏览器的刷新操作，ajax获取到后台数据后，更新页面上显示对应数据的部分，实现局部刷新。

**同源策略**
ajax请求的页面资源只能是同一个域下面的资源，不能是其他域的资源，这是ajax处于安全的考虑作出的设计。
[jQuery ajax的基本使用](https://blog.csdn.net/q547550831/article/details/52904922)

```html
function checkUsername(username) {
    var value = username.value;
    $.ajax({
        type : "POST",  //请求方式
        url : "${pageContext.request.contextPath}/RegisterServlet",  //请求路径
        data : {  //请求参数
            username : value
        },
        success : function(msg) {  //异步请求成功执行的回调函数
            alert("成功了: " + msg);
            $("#usernameinfo").html(msg);
        },//ajax引擎一般用不到；状态信息；抛出的异常信息
        error : function(XMLHttpRequest, textStatus, errorThrown) {
            alert(textStatus);
            alert("失败了"+errorThrown)
        }
    });
}
```

## jsonP

[ajax和jsonp使用总结](https://www.cnblogs.com/cwp-bg/p/7668840.html)

[jsonP　跨域请求](https://www.cnblogs.com/chiangchou/p/jsonp.html)

[jQuery版本的jsonp](http://www.cnblogs.com/xiaozhumaopao/p/7093144.html)

##本地存储

[Javascript本地存储小结](https://segmentfault.com/a/1190000007506189)

##jQueryUI
jQuery UI 是建立在 jQuery JavaScript 库上的一组用户界面交互、特效、小部件及主题。
[jQueryUI](http://www.runoob.com/jqueryui/jqueryui-tutorial.html)

##swiper
[swiper教程以及文档](http://www.swiper.com.cn/usage/index.html)

Swiper是纯javascript打造的滑动特效插件，面向手机、平板电脑等移动终端.
Swiper能实现触屏焦点图、触屏Tab切换、触屏多图切换等常用效果。
Swiper开源、免费、稳定、使用简单、功能强大，是架构移动终端网站的重要选择

##BootStrap
Bootstrap，来自 Twitter，是目前最受欢迎的前端框架。Bootstrap 是基于 HTML、CSS、JAVASCRIPT 的，它简洁灵活，使得 Web 开发更加快捷。
[Bootstrap菜鸟教程](http://www.runoob.com/bootstrap/bootstrap-tutorial.html)
