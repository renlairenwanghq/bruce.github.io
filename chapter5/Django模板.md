#Django模板

- 模板介绍
  - - [模板处理](https://blog.csdn.net/u010505059/article/details/78650597#模板处理)
    - [快捷函数](https://blog.csdn.net/u010505059/article/details/78650597#快捷函数)
  - 定义模板
    - [变量](https://blog.csdn.net/u010505059/article/details/78650597#变量)
    - [在模板中调用对象的方法](https://blog.csdn.net/u010505059/article/details/78650597#在模板中调用对象的方法)
    - [标签](https://blog.csdn.net/u010505059/article/details/78650597#标签)
    - [过滤器](https://blog.csdn.net/u010505059/article/details/78650597#过滤器)
    - [注释](https://blog.csdn.net/u010505059/article/details/78650597#注释)
  - 模板继承
    - - [说明](https://blog.csdn.net/u010505059/article/details/78650597#说明)
    - 三层继承结构
      - [创建根级模板](https://blog.csdn.net/u010505059/article/details/78650597#1创建根级模板)
      - [创建分支模版](https://blog.csdn.net/u010505059/article/details/78650597#2创建分支模版)
      - [为具体页面创建模板继承自分支模板](https://blog.csdn.net/u010505059/article/details/78650597#3为具体页面创建模板继承自分支模板)
      - [视图调用具体页面并传递模板中需要的数据](https://blog.csdn.net/u010505059/article/details/78650597#4视图调用具体页面并传递模板中需要的数据)
      - [配置url](https://blog.csdn.net/u010505059/article/details/78650597#5配置url)
  - HTML转义
    - [会被自动转义的字符](https://blog.csdn.net/u010505059/article/details/78650597#会被自动转义的字符)
    - [关闭转义](https://blog.csdn.net/u010505059/article/details/78650597#关闭转义)
    - [字符串字面值](https://blog.csdn.net/u010505059/article/details/78650597#字符串字面值)
  - csrf
    - [防csrf的使用](https://blog.csdn.net/u010505059/article/details/78650597#防csrf的使用)
    - [取消保护](https://blog.csdn.net/u010505059/article/details/78650597#取消保护)
    - [保护原理](https://blog.csdn.net/u010505059/article/details/78650597#保护原理)
  - 验证码
    - [验证码视图](https://blog.csdn.net/u010505059/article/details/78650597#验证码视图)
    - [配置url](https://blog.csdn.net/u010505059/article/details/78650597#配置url)
    - [显示验证码](https://blog.csdn.net/u010505059/article/details/78650597#显示验证码)
    - [验证](https://blog.csdn.net/u010505059/article/details/78650597#验证)
    - [第三方](https://blog.csdn.net/u010505059/article/details/78650597#第三方)

# 模板介绍

- 作为Web框架，Django提供了模板，可以很便利的动态生成HTML

- 模版系统致力于表达外观，而不是程序逻辑

- 模板的设计实现了业务逻辑(view)与显示内容（template）的分离，一个视图可以使用任意一个模板，一个模板可以供多个视图使用

- 模板包含 

  ​

  - HTML的静态部分
  - 动态插入内容部分

- Django模板语言，简写DTL，定义在django.template包中

- 由startproject命令生成的settings.py定义关于模板的值： 

  ​

  - DIRS定义了一个目录列表，模板引擎按列表顺序搜索这些目录以查找模板源文件
  - APP_DIRS告诉模板引擎是否应该在每个已安装的应用中查找模板

- 常用方式：在项目的根目录下创建templates目录，设置DIRS值

```
DIRS=[os.path.join(BASE_DIR,"templates")]1
```

### 模板处理

- Django处理模板分为两个阶段
- Step1 加载：根据给定的标识找到模板然后预处理，通常会将它编译好放在内存中

```
loader.get_template(template_name)，返回一个Template对象1
```

- Step2 渲染：使用Context数据对模板插值并返回生成的字符串

```
Template对象的render(RequestContext)方法，使用context渲染模板1
```

- 加载渲染完整代码：

```
from django.template import loader, RequestContext
from django.http import HttpResponse

def index(request):
    tem = loader.get_template('temtest/index.html')
    context = RequestContext(request, {})
    return HttpResponse(tem.render(context))1234567
```

### 快捷函数

- 为了减少加载模板、渲染模板的重复代码，django提供了快捷函数
- render_to_string(“”)
- render(request,’模板’,context)

```
from django.shortcuts import render

def index(request):
    return render(request, 'temtest/index.html')1234
```

## 定义模板

- 模板语言包括 
  - 变量
  - 标签 { % 代码块 % }
  - 过滤器
  - 注释{# 代码或html #}

### 变量

- 语法：

```
{{ variable }}1
```

- 当模版引擎遇到一个变量，将计算这个变量，然后将结果输出

- 变量名必须由字母、数字、下划线（不能以下划线开头）和点组成

- 当模版引擎遇到点(“.”)，会按照下列顺序查询： 

  ​

  1. 字典查询，例如：foo[“bar”]
  2. 属性或方法查询，例如：foo.bar
  3. 数字索引查询，例如：foo[bar]

- 如果变量不存在， 模版系统将插入” (空字符串)

- 在模板中调用方法时不能传递参数

### 在模板中调用对象的方法

- 在models.py中定义类HeroInfo

```
from django.db import models

class HeroInfo(models.Model):
    ...
    def showName(self):
        return self.hname123456
```

- 在views.py中传递HeroInfo对象

```
from django.shortcuts import render
from models import *

def index(request):
    hero = HeroInfo(hname='abc')
    context = {'hero': hero}
    return render(request, 'temtest/detail.html', context)1234567
```

- 在模板detail.html中调用

```
{{hero.showName}}1
```

### 标签

- 语法：{ % tag % }

- 作用 

  ​

  - 在输出中创建文本
  - 控制循环或逻辑
  - 加载外部信息到模板中供以后的变量使用

- for标签

```
{ %for ... in ...%}
循环逻辑
{{forloop.counter}}表示当前是第几次循环
{ %empty%}
给出的列表为或列表不存在时，执行此处
{ %endfor%}123456
```

- if标签

```
{ %if ...%}
逻辑1
{ %elif ...%}
逻辑2
{ %else%}
逻辑3
{ %endif%}1234567
```

- comment标签

```
{ % comment % }
多行注释
{ % endcomment % }123
```

- include：加载模板并以标签内的参数渲染

```
{ %include "foo/bar.html" % }1
```

- url：反向解析

```
{ % url 'name' p1 p2 %}1
```

- csrf_token：这个标签用于跨站请求伪造保护

```
{ % csrf_token %}1
```

- 布尔标签：and、or，and比or的优先级高
- block、extends：详见“模板继承”
- autoescape：详见“HTML转义”

### 过滤器

- 语法：{ { 变量|过滤器 }}，例如{ { name|lower }}，表示将变量name的值变为小写输出
- 使用管道符号 (|)来应用过滤器
- 通过使用过滤器来改变变量的计算结果
- 可以在if标签中使用过滤器结合运算符

```
if list1|length > 11
```

- 过滤器能够被“串联”，构成过滤器链

```
name|lower|upper1
```

- 过滤器可以传递参数，参数使用引号包起来

```
list|join:", "1
```

- default：如果一个变量没有被提供，或者值为false或空，则使用默认值，否则使用变量的值

```
value|default:"什么也没有"1
```

- date：根据给定格式对一个date变量格式化

```
value|date:'Y-m-d'1
```

- escape：详见“HTML转义”
- 点击查看详细的过滤器

### 注释

- 单行注释

```
{#...#}1
```

- 注释可以包含任何模版代码，有效的或者无效的都可以

```
{# { % if foo % }bar{ % else % } #}1
```

- 使用comment标签注释模版中的多行内容

## 模板继承

- 模板继承可以减少页面内容的重复定义，实现页面内容的重用
- 典型应用：网站的头部、尾部是一样的，这些内容可以定义在父模板中，子模板不需要重复定义
- block标签：在父模板中预留区域，在子模板中填充
- extends继承：继承，写在模板文件的第一行
- 定义父模板base.html

```
{ %block block_name%}
这里可以定义默认值
如果不定义默认值，则表示空字符串
{ %endblock%}1234
```

- 定义子模板index.html

```
{ % extends "base.html" %}1
```

- 在子模板中使用block填充预留区域

```
{ %block block_name%}
实际填充内容
{ %endblock%}123
```

#### 说明

- 如果在模版中使用extends标签，它必须是模版中的第一个标签
- 不能在一个模版中定义多个相同名字的block标签
- 子模版不必定义全部父模版中的blocks，如果子模版没有定义block，则使用了父模版中的默认值
- 如果发现在模板中大量的复制内容，那就应该把内容移动到父模板中
- 使用可以获取父模板中block的内容
- 为了更好的可读性，可以给endblock标签一个名字

```
{ % block block_name %}
区域内容
{ % endblock block_name %}123
```

### 三层继承结构

- 三层继承结构使代码得到最大程度的复用，并且使得添加内容更加简单
- 如下图为常见的电商页面 
  ![这里写图片描述](https://img-blog.csdn.net/20171127232210816?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 1.创建根级模板

- 名称为“base.html”
- 存放整个站点共用的内容

```
<!DOCTYPE html>
<html>
<head>
    <title>{%block title%}{%endblock%} 水果超市</title>
</head>
<body>
top--{{logo}}
<hr/>
{%block left%}{%endblock%}
{%block content%}{%endblock%}
<hr/>
bottom
</body>
</html>1234567891011121314
```

#### 2.创建分支模版

- 继承自base.html
- 名为“base_*.html”
- 定义特定分支共用的内容
- 定义base_goods.html

```
{%extends 'temtest/base.html'%}
{%block title%}商品{%endblock%}
{%block left%}
<h1>goods left</h1>
{%endblock%}12345
```

- 定义base_user.html

```
{%extends 'temtest/base.html'%}
{%block title%}用户中心{%endblock%}
{%block left%}
<font color='blue'>user left</font>
{%endblock%}12345
```

- 定义index.html，继承自base.html，不需要写left块

```
{%extends 'temtest/base.html'%}
{%block content%}
首页内容
{%endblock content%}1234
```

#### 3.为具体页面创建模板，继承自分支模板

- 定义商品列表页goodslist.html

```
{%extends 'temtest/base_goods.html'%}
{%block content%}
商品正文列表
{%endblock content%}1234
```

- 定义用户密码页userpwd.html

```
{%extends 'temtest/base_user.html'%}
{%block content%}
用户密码修改
{%endblock content%}1234
```

#### 4.视图调用具体页面，并传递模板中需要的数据

- 首页视图index

```
logo='welcome to itcast'
def index(request):
    return render(request, 'temtest/index.html', {'logo': logo})123
```

- 商品列表视图goodslist

```
def goodslist(request):
    return render(request, 'temtest/goodslist.html', {'logo': logo})12
```

- 用户密码视图userpwd

```
def userpwd(request):
    return render(request, 'temtest/userpwd.html', {'logo': logo})12
```

#### 5.配置url

```
from django.conf.urls import url
from . import views
urlpatterns = [
    url(r'^$', views.index, name='index'),
    url(r'^list/$', views.goodslist, name='list'),
    url(r'^pwd/$', views.userpwd, name='pwd'),
]1234567
```

## HTML转义

- Django对字符串进行自动HTML转义，如在模板中输出如下值：

```
视图代码：
def index(request):
    return render(request, 'temtest/index2.html',
                  {
                      't1': '<h1>hello</h1>'
                  })
模板代码：
{{t1}}12345678
```

- 显示效果如下图： 
  ![这里写图片描述](https://img-blog.csdn.net/20171127232545626?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 会被自动转义的字符

- html转义，就是将包含的html标签输出，而不被解释执行，原因是当显示用户提交字符串时，可能包含一些攻击性的代码，如js脚本
- Django会将如下字符自动转义：

```
< 会转换为&lt;

> 会转换为&gt;

' (单引号) 会转换为&#39;

" (双引号)会转换为 &quot;

& 会转换为 &amp;123456789
```

- 当显示不被信任的变量时使用escape过滤器，一般省略，因为Django自动转义

```
{{t1|escape}}1
```

### 关闭转义

- 对于变量使用safe过滤器

```
{{ data|safe }}1
```

- 对于代码块使用autoescape标签

```
{ % autoescape off %}
{{ body }}
{ % endautoescape %}123
```

- 标签autoescape接受on或者off参数
- 自动转义标签在base模板中关闭，在child模板中也是关闭的

### 字符串字面值

- 手动转义

```
{ { data|default:"<b>123</b>" }}1
```

- 应写为

```
{ { data|default:"&lt;b&gt;123&lt;/b&gt;" }}1
```

## csrf

- 全称Cross Site Request Forgery，跨站请求伪造
- 某些恶意网站上包含链接、表单按钮或者JavaScript，它们会利用登录过的用户在浏览器中的认证信息试图在你的网站上完成某些操作，这就是跨站攻击
- 演示csrf如下
- 创建视图csrf1用于展示表单，csrf2用于接收post请求

```
def csrf1(request):
    return render(request,'booktest/csrf1.html')
def csrf2(request):
    uname=request.POST['uname']
    return render(request,'booktest/csrf2.html',{'uname':uname})12345
```

- 配置url

```
url(r'^csrf1/$', views.csrf1),
url(r'^csrf2/$', views.csrf2),12
```

- 创建模板csrf1.html用于展示表单

```
<html>
<head>
    <title>Title</title>
</head>
<body>
<form method="post" action="/crsf2/">
    <input name="uname"><br>
    <input type="submit" value="提交"/>
</form>
</body>
</html>1234567891011
```

- 创建模板csrf2用于展示接收的结果

```
<html>
<head>
    <title>Title</title>
</head>
<body>
{{ uname }}
</body>
</html>12345678
```

- 在浏览器中访问，查看效果，报错如下： 
  ![这里写图片描述](https://img-blog.csdn.net/20171127232308074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- 将settings.py中的中间件代码’django.middleware.csrf.CsrfViewMiddleware’注释
- 查看csrf1的源代码，复制，在自己的网站内建一个html文件，粘贴源码，访问查看效果

### 防csrf的使用

- 在django的模板中，提供了防止跨站攻击的方法，使用步骤如下：
- step1：在settings.py中启用’django.middleware.csrf.CsrfViewMiddleware’中间件，此项在创建项目时，默认被启用
- step2：在csrf1.html中添加标签

```
<form>
{% csrf_token %}
...
</form>1234
```

- step3：测试刚才的两个请求，发现跨站的请求被拒绝了，效果如下图 
  ![这里写图片描述](https://img-blog.csdn.net/20171127232323936?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 取消保护

- 如果某些视图不需要保护，可以使用装饰器csrf_exempt，模板中也不需要写标签，修改csrf2的视图如下

```
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def csrf2(request):
    uname=request.POST['uname']
    return render(request,'booktest/csrf2.html',{'uname':uname})123456
```

- 运行上面的两个请求，发现都可以请求

### 保护原理

- 加入标签后，可以查看源代码，发现多了如下代码

```
<input type='hidden' name='csrfmiddlewaretoken' value='nGjAB3Md9ZSb4NmG1sXDolPmh3bR2g59' />1
```

- 在浏览器的调试工具中，通过network标签可以查看cookie信息
- 本站中自动添加了cookie信息，如下图 
  ![这里写图片描述](https://img-blog.csdn.net/20171127232339440?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- 查看跨站的信息，并没有cookie信息，即使加入上面的隐藏域代码，发现又可以访问了
- 结论：django的csrf不是完全的安全
- 当提交请求时，中间件’django.middleware.csrf.CsrfViewMiddleware’会对提交的cookie及隐藏域的内容进行验证，如果失败则返回403错误

## 验证码

- 在用户注册、登录页面，为了防止暴力请求，可以加入验证码功能，如果验证码错误，则不需要继续处理，可以减轻一些服务器的压力
- 使用验证码也是一种有效的防止crsf的方法
- 验证码效果如下图： 
  ![这里写图片描述](https://img-blog.csdn.net/20171127233842687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

### 验证码视图

- 新建viewsUtil.py，定义函数verifycode
- 此段代码用到了PIL中的Image、ImageDraw、ImageFont模块，需要先安装Pillow（3.4.1）包，详细文档参考<http://pillow.readthedocs.io/en/3.4.x/>
- Image表示画布对象
- ImageDraw表示画笔对象
- ImageFont表示字体对象，ubuntu的字体路径为“/usr/share/fonts/truetype/freefont”
- 代码如下：

```
from django.http import HttpResponse
def verifycode(request):
    #引入绘图模块
    from PIL import Image, ImageDraw, ImageFont
    #引入随机函数模块
    import random
    #定义变量，用于画面的背景色、宽、高
    bgcolor = (random.randrange(20, 100), random.randrange(
        20, 100), 255)
    width = 100
    height = 25
    #创建画面对象
    im = Image.new('RGB', (width, height), bgcolor)
    #创建画笔对象
    draw = ImageDraw.Draw(im)
    #调用画笔的point()函数绘制噪点
    for i in range(0, 100):
        xy = (random.randrange(0, width), random.randrange(0, height))
        fill = (random.randrange(0, 255), 255, random.randrange(0, 255))
        draw.point(xy, fill=fill)
    #定义验证码的备选值
    str1 = 'ABCD123EFGHIJK456LMNOPQRS789TUVWXYZ0'
    #随机选取4个值作为验证码
    rand_str = ''
    for i in range(0, 4):
        rand_str += str1[random.randrange(0, len(str1))]
    #构造字体对象
    font = ImageFont.truetype('FreeMono.ttf', 23)
    #构造字体颜色
    fontcolor = (255, random.randrange(0, 255), random.randrange(0, 255))
    #绘制4个字
    draw.text((5, 2), rand_str[0], font=font, fill=fontcolor)
    draw.text((25, 2), rand_str[1], font=font, fill=fontcolor)
    draw.text((50, 2), rand_str[2], font=font, fill=fontcolor)
    draw.text((75, 2), rand_str[3], font=font, fill=fontcolor)
    #释放画笔
    del draw
    #存入session，用于做进一步验证
    request.session['verifycode'] = rand_str
    #内存文件操作
    import cStringIO
    buf = cStringIO.StringIO()
    #将图片保存在内存中，文件类型为png
    im.save(buf, 'png')
    #将内存中的图片数据返回给客户端，MIME类型为图片png
    return HttpResponse(buf.getvalue(), 'image/png')12345678910111213141516171819202122232425262728293031323334353637383940414243444546
```

### 配置url

- 在urls.py中定义请求验证码视图的url

```
from . import viewsUtil

urlpatterns = [
    url(r'^verifycode/$', viewsUtil.verifycode),
]12345
```

### 显示验证码

- 在模板中使用img标签，src指向验证码视图

```
<img id='verifycode' src="/verifycode/" alt="CheckCode"/>
启动服务器，查看显示成功
扩展：点击“看不清，换一个”时，可以换一个新的验证码
<script type="text/javascript" src="/static/jquery-1.12.4.min.js"></script>
<script type="text/javascript">
    $(function(){
        $('#verifycodeChange').css('cursor','pointer').click(function() {
            $('#verifycode').attr('src',$('#verifycode').attr('src')+1)
        });
    });
</script>
<img id='verifycode' src="/verifycode/?1" alt="CheckCode"/>
<span id='verifycodeChange'>看不清，换一个</span>12345678910111213
```

- 为了能够实现提交功能，需要增加form和input标签

```
<form method='post' action='/verifycodeValid/'>
    <input type="text" name="vc">
    <img id='verifycode' src="/verifycode/?1" alt="CheckCode"/>
<span id='verifycodeChange'>看不清，换一个</span>
<br>
<input type="submit" value="提交">
</form>1234567
```

### 验证

- 接收请求的信息，与session中的内容对比

```
from django.http import HttpResponse

def verifycodeValid(request):
    vc = request.POST['vc']
    if vc.upper() == request.session['verifycode']:
        return HttpResponse('ok')
    else:
        return HttpResponse('no')12345678
```

- 配置验证处理的url

```
urlpatterns = [
    url(r'^verifycodeValid/$', views.verifycodeValid),
]123
```

### 第三方

- 可以在网上搜索“验证码”，找到一些第三方验证码提供网站，阅读文档，使用到项目中