## HTML

### 基本结构

```
<!DOCTYPE html> //文档声明
<html>
<head> //头部
<meta charset="utf-8"> //编码声明
</head>

<body> //内容体
</body>

</html>
```



#### 文档类型

* xhtml 1.0
* html5



**alt 属性**



**注释**

```
<!--注释 -->
```



### 标签

[标签列表](http://www.w3school.com.cn/tags/index.asp)

eg.

标题标签 

```
<h1> 一级标题 </h1>
```

段落标签

```
<p> 段落 </p>
```

换行标签

```
<br />
```

**字符实体** 

```
<!-- 空格 -->
&nbsp;
<!-- 大于号 -->
&gt;
<!-- 大于号 -->
&lt;
```

**无明确语义标签**

```
div标签
span标签
```

**含样式和语义的标签**

```
<em> </em>
<i> </i>
<b></b>
<strong> </strong>
```

**语义化的标签**

可以帮助搜索引擎理解这个网页

**图片**

alt属性：描述图片，图片获取不到时，显示在网页上

```
<img width=0 height=0 src='' border='0' alt='' />
```

**链接**

```html
<a herf="地址" title="光标放到该链接时的提示内容"> 网站名 </a>

<!--herf="#" 代表链接到页面顶部-->
<a herf="#" title="光标放到该链接时的提示内容"> 网站名 </a> 

<!--什么操作都不做-->
<a herf="javascript:;"> 默认操作 </a> 

<!--页面内跳转-->
<a herf="#biaoti1"> 默认操作 </a> 
<!--设置页面内跳转的锚点-->
<h1 id="biaoti1">标题1</h1>
```

**有序列表**

```html
<ol>
	<li></li>
	<li></li>
</ol>
```

**无序列表**

```html
<ul>
	<li></li>
	<li></li>
</ul>
```

**定义列表**

```html
<dl>
	<dt>定义标题</dt>
	<dd>定义解释</dd>
</dl>
```

**表格**

```html
<!--border 边框线粗 width 宽  height 高-->
<table border="1"> 
	<tr>
		<!--表格 头部-->
		<th></th>
		<th></th>
	</tr>
	
	<tr>
		<!--表格 内容 align 表示水平对齐 vlign 垂直对齐-->
        <!-- colspan 水平合并 rowspan 垂直合并-->
		<td align="center"></td>
		<td></td>
	</tr>	
	
</table>
```

**表单**

注意 input的属性：type＝hidden， type＝text，type＝img等

form的属性：method、action

**get提交**

直接将数据在地址栏上显示出来

**post提交**

将数据放到报文当中

```html
<!-- action 属性表示表单数据提交的地址 -->
<!-- input type: text password radio checkbox  -->
<!-- methon属性代表提交的方式 -->
<form action="http://..." method＝"post">
    <div>
        <!-- 
			 input中设置id，然后lable的for属性中设置同一个id，这样点击用户名这几个文字时
        	 也能将光标放到输入框中  
		-->
        <lable for="username">用户名</lable>
        <input type="text" id="username">
    </div>

    <div>
        <lable>密码</lable>
        <input type="password">
    </div>

 	<div>
        <!-- name 相同，可以防止都被选择 -->
        <lable>性别</lable>
         <!-- 下面这两个input的区别是，第一个点击男这个文字的时候也可以选中 这个就是id和for
			  属性的作用 
		  -->
        <input type="radio" name="gender" id="male"><lable for="male">男</lable>
  		<input type="radio" name="gender">女
    </div>
    
    <div>
        <!-- select标签 表示选择 -->
        <lable>籍贯</lable>
        <select>
            <option>北京</option>
            <option>上海</option>
        </select>
    </div>
    <!-- submit是提交操作 -->
    <input type="submit" name="" value="提交">
     <!-- reset是重置操作 -->
    <input type="reset" name="" value="提交">
     <!-- hidden可以帮忙存储一些值 可以传到后台 -->
    <input type="hidden" name="hid" value="10000">
</form>
```

### html内嵌框架

iframe标签会创建另外一个html文件的内联框架。src属性指定另一个html文件的引用地址。

```html
<!DOCTYPE html>
<html lang=en>
<head>
	<meta charset="utf-8">
	<title>n内嵌框架</title>
</head>

<body>
    <!-- scrolling为no表示无滚动条 -->
	<iframe src="http://www.baidu.com" width="900" height="500" frameborder="0" scrolling="no"></iframe>
</body>
```

通过在iframe中添加属性name 和 在a标签中设置target可以将两者关联起来

```html
<!DOCTYPE html>
<html lang=en>
<head>
	<meta charset="utf-8">
	<title>n内嵌框架</title>
</head>

<body>
	<a href="http://www.baidu.com" target="myframe">百度网</a>
	<a href="http://www.qq.com" target="myframe">腾讯网</a>
	<iframe src="http://www.baidu.com" width="900" height="500" frameborder="0" name="myframe" scrolling="no"></iframe>
</body>
```



##CSS(样式)

[css教程](http://www.runoob.com/css/css-tutorial.html)

####CSS基本语法

选择器{属性:值; 属性:值; 属性:值;}

div{width:800;height:900;color:red}

#### CSS引入页面方法

[html引入css的三种方法](https://blog.csdn.net/ldz1997106/article/details/59106778)

1.外联式 head中通过link标签

通过外部的css文件引入

2.嵌入式 通过style标签放到头部

3.内联式 通过style属性设置

#### CSS样式选择器

[css选择器](http://www.runoob.com/cssref/css-selectors.html)

1.标签选择器

2.id选择器 （一般不怎么用）

3.类选择器 （id选择器的权重比类选择器大）

class里面可以包含多个元素

4.层级选择器

5.组选择器

注意：标签中的id最好保持不同

####CSS元素类型

在CSS中，`html`中的标签元素大体被分为三种不同的类型：

块状元素、内联元素(又叫行内元素)和内联块状元素。				
[css元素类型](https://www.imooc.com/article/2031)

#### 样式选择器

#### 表格样式

传统布局

#### CSS盒子模型

[css盒子模型](http://www.runoob.com/css/css-boxmodel.html)

[css盒模型详解](https://www.cnblogs.com/smyhvae/p/7256371.html)

浮动

[CSS 浮动](http://www.w3school.com.cn/css/css_positioning_floating.asp)