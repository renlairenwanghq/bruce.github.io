##1.概述

Django是一个开放源代码的Web应用框架，由Python写成。采用了MTV的框架模式，即模型M，模板T和视图V。

[Django中MVC与MVT设计模式的区别系列之一](https://blog.csdn.net/u014745194/article/details/73718041)

### MVC各部分的功能

- M全拼为Model，主要封装对数据库层的访问，对数据库中的数据进行增、删、改、查操作。
- V全拼为View，用于封装结果，生成页面展示的html内容。
- C全拼为Controller，用于接收请求，处理业务逻辑，与Model和View交互，返回结果。



### MVT各部分的功能

- M全拼为Model，与MVC中的M功能相同，负责和数据库交互，进行数据处理。
- V全拼为View，与MVC中的C功能相同，接收请求，进行业务处理，返回应答。
- T全拼为Template，与MVC中的V功能相同，负责封装构造要返回的html。

##2.入门

###2.1 安装虚拟环境

[virtualenv](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000)

* 创建　mkvirtualenv [虚拟环境名称]


*  删除　rmvirtualenv [虚拟环境名称]
* 进入　workon  [虚拟环境名称]
* 退出　deactivate

所有的虚拟环境都位于~/.virtualenvs

**创建虚拟环境**

```
$ mkvirtualenv env
New python executable in /home/milo/.virtualenvs/env/bin/python
Installing setuptools, pip, wheel...done.
```

**进入**

```
$ workon env
```

**退出**

```
$ deavtive
```

**删除**

```
$ rmvirtualenv env
Removing env...
```

pip list 和　pip freeze可以列出当前有那些包，不过显示的格式不同

```
$ pip list
DEPRECATION: The default format will switch to columns in the future. You can use --format=(legacy|columns) (or define a format=(legacy|columns) in your pip.conf under the [list] section) to disable this warning.
Django (1.8.2)
pip (9.0.3)
setuptools (39.0.1)
wheel (0.31.0)
$ pip freeze
Django==1.8.2
```

###2.2 创建项目

* 命令django-admin startproject test1
* 进入test1目录，目录结构如下

```
$ django-admin startproject test1
$ cd test1 && tree
.
├── manage.py
└── test1
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py

1 directory, 5 files

```

**目录介绍**

* `manage.py:` 一个实用的命令行工具，可让你以各种方式与该 Django 项目进行交互。 
* `__init__.py:` 一个空文件，告诉 Python 该目录是一个 Python 包。
* `settings.py:` 该 Django 项目的设置/配置。
* `urls.py:` Django 项目的 URL 声明; 一份由 Django 驱动的网站"目录"。
* `wsgi.py` 一个 WSGI 兼容的 Web 服务器的入口，以便运行你的项目。

**启动服务器**

0.0.0.0 让其它电脑可连接到开发服务器，8000 为端口号。如果不说明，那么端口号默认为 8000

```
python manage.py runserver 0.0.0.0:8000
```

###url() 函数

Django url() 可以接收四个参数，分别是两个必选参数：regex、view 和两个可选参数：kwargs、name，接下来详细介绍这四个参数。

- regex: 正则表达式，与之匹配的 URL 会执行对应的第二个参数 view。
- view: 用于执行与正则表达式匹配的 URL 请求。
- kwargs: 视图使用的字典类型的参数。
- name: 用来反向获取 URL。




### 2.3 模型

* 配置数据库
* 创建应用
* 创建模型类

#### 2.3.1 数据库的配置

在`settings.py`文件中，通过DATABASE项进行数据库设置。默认使用的是sqlite数据库。

#### 2.3.2 创建应用

```
$ python manage.py startapp booktest
$ tree
.
├── booktest
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   │   └── __init__.py
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── dj1
│   ├── __init__.py
│   ├── __init__.pyc
│   ├── settings.py
│   ├── settings.pyc
│   ├── urls.py
│   └── wsgi.py
└── manage.py

3 directories, 14 files
```

#### 2.3.3 运行应用

```
$ python manage.py runserver
```

#### 2.3.4 创建模型

修改`booktest/models.py`文件

[Django中model的字段类型 ](https://blog.csdn.net/gavinking0110/article/details/54412590)

```
from django.db import models

# Create your models here.
class BookInfo(models.Model):
	btitle = models.CharField(max_length=20)
	bpub_date=models.DateTimeField()
    def __str__(self):
    	return self.btitle.encode('utf-8')
	
class HeroInfo(models.Model):
	hname=models.CharField(max_length=30)
	hbook=models.ForeignKey(BookInfo)
```

#### 2.3.5 注册APP

修改settings.py文件

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'booktest'
]

```

#### 2.3.6 生成迁移

执行命令`python manage.py makemigrations`生成迁移，会在migrations生成新文件`0001_initial.py`。

```
$ python manage.py makemigrations
Migrations for 'booktest':
  booktest/migrations/0001_initial.py
    - Create model BookInfo
    - Create model Hero
```

#### 2.3.7 执行迁移

执行`python manage.py migrate`命令执行迁移

```
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, booktest, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying booktest.0001_initial... OK
  Applying sessions.0001_initial... OK

```

#### 2.3.8 进入shell

执行`python manager shell`进入shell

下面的警告是时区问题。

```
$ python manage.py shell
Python 2.7.12 (default, Nov 20 2017, 18:23:56) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
(InteractiveConsole)
>>> from booktest.modes import *
Traceback (most recent call last):
  File "<console>", line 1, in <module>
ImportError: No module named modes
>>> from booktest.models import *
>>> b=BookInfo()
>>> b.btitle='abc'
>>> form datetime import datetime
  File "<console>", line 1
    form datetime import datetime
                ^
SyntaxError: invalid syntax
>>> from datetime import datetime
>>> b.bpub_date=datetime(year=1990, month=1, day=2)
>>> b.save()
/home/milo/.virtualenvs/test2/local/lib/python2.7/site-packages/django/db/models/fields/__init__.py:1451: RuntimeWarning: DateTimeField BookInfo.bpub_date received a naive datetime (1990-01-02 00:00:00) while time zone support is active.
  RuntimeWarning)
>>> BookInfo.object.all()
Traceback (most recent call last):
  File "<console>", line 1, in <module>
AttributeError: type object 'BookInfo' has no attribute 'object'
>>> BookInfo.objects.all()
<QuerySet [<BookInfo: BookInfo object>]>
```

**注意:**

```
b=BookInfo.objects.get(pk=1)　//pk是主键
```

###2.4 管理操作

####2.4.1 创建管理员

```
python manage.py createsuperuser
```

执行`python manage.py runserver`后通过网址http://127.0.0.1:8000/admin访问。

#### 2.4.2 开启后台服务

修改`urls.py`文件

```
urlpatterns = [
    #url(r'^$', view.hello),
    url(r'^admin/', include(admin.site.urls)),
]
```



#### 2.4.3 管理界面本地化

修改`setting.py`文件

```
LANGUAGE_CODE = 'en-hans' #'en-us'
TIME_ZONE = 'Asia/Shanghai' #'UTC'
```

####2.4.4 向admin注册模型

修改booktest/admin.py文件，添加代码

```
from django.contrib import admin

# Register your models here.
from models import *

admin.site.register(BookInfo)
admin.site.register(HeroInfo)
```

重新执行执行`python manage.py runserver`访问http://127.0.0.1:8000/admin。

**注意:** 修改模型中encode: self.btitle.encode('utf-8')

####2.4.5 自定义管理界面

[Django之model admin自定义后台管理](http://www.cnblogs.com/ccorz/p/Django-zhimodel-admin-zi-ding-yi-hou-tai-guan-li.html)

* Djanjo提供了admin.ModelAdmin对象。
* 通过定义admin.ModelAdmin对象的子类，可以实现自定义管理界面。
* admin.site.register(类名,　admin.ModelAdmin对象的子类)

**ModelAdmin选项**



#### 2.4.6 关联注册　

对于HeroInfo既可以像BookInfo那样注册，也可以关联注册。StackInline的意思是停靠，指的是HeroInfo停靠在

BookInfo上注册。model表明那个类，extra表明停靠的个数。

```
from django.contrib import admin

# Register your models here.
from models import *

class HeroInfoInline(admin.StackedInline):
    model = HeroInfo
    extra = 2

class BookInfoAdmin(admin.ModelAdmin):
    inline = [HeroInfoInline]

admin.site.register(BookInfo, BookInfoAdmin)
```

### 2.5 视图

#### 2.5.1 在views.py文件中添加函数

修改`booktest/views.py`文件

```
from django.shortcuts import render
from django.http import *

# Create your views here.
def index(request):
    return HttpResponse('hello world!!!')
```

#### 2.5.2 修改urls.py文件

修改`mytest/urls.py`

```
urlpatterns = [
    url(r'^admin/', include(admin.site.urls)),
    url(r'^', include('booktest.urls')),　　#新增
]
```

在`booktest`目录下创建urls.py文件

```
from django.conf.urls import include, url
from . import views
  
urlpatterns = [
    url(r'^$', views.index),
]
```

这样就可以访问到views.py文件下的index函数。

#### 2.6 模板

* 在项目目录下创建templates目录
* 在templates目录下新建一个和app同名的文件夹

#### 2.6.1 修改views.py文件 

```
#from django.shortcuts import render
from django.http import *
from django.template import RequestContext,loader

# Create your views here.
def index(request):
    temp = loader.get_template("booktest/hello.html");
    return HttpResponse(temp.render())
```

也可以按如下方式书写：

```
from django.shortcuts import render
from django.http import *
#from django.template import RequestContext,loader

# Create your views here.
def index(request):
    #temp = loader.get_template("booktest/hello.html");
    #return HttpResponse(temp.render())
    return render(request, 'booktest/hello.html')
```

#### 2.6.2 修改setting.py文件

修改`mytest/setting.py`TEMPLATES中DIRS项。

```
TEMPLATES = [ 
    {   
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },  
]
```

这样访问时就可以获取到`booktest/hello.html`文件。

#### 2.6.3 使用模型中的数据

修改`booktest/view.py`

```
from django.shortcuts import render
from django.http import *
#from django.template import RequestContext,loader

# Create your views here.
def index(request):
    booklist = BookInfo.object.all() 
    Context = {'list':booklist}
    return render(request, 'booktest/hello.html', Context)
```

对应的`hello.html`,注意在html中使用python的语法。

```
<!DOCTYPE html> 
<html>
<head> 
<meta charset="utf-8"> 
<title>test</title>
</head>
<body> 
<ul>
	{%for book in list%}
	<li>{{book.btitle}}</li>
	{%endfor%}
</ul>
</body>
</html
```



