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

###安装虚拟环境

[virtualenv](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000)

* 创建　mkvirtualenv [虚拟环境名称]


*  删除　rmvirtualenv [虚拟环境名称]
* 进入　workon  [虚拟环境名称]
* 退出　deavtive

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

###创建项目

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



### 设计模型

#### 设置数据库

#### 添加应用

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

#### 注册

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



#### 生成迁移

执行命令`python manage.py makemigrations`生成迁移，会在migrations生成新文件。

```
$ python manage.py makemigrations
Migrations for 'booktest':
  booktest/migrations/0001_initial.py
    - Create model BookInfo
    - Create model Hero
```

#### 执行迁移

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

#### 进入shell

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

**get**

pk是主键

```
b=BookInfo.objects.get(pk=1)
```



#### 管理站点

```
python manage.py createsuperuser
```

**注册模型**

修改booktest/admin.py文件，添加代码

```
from .models import *
admin.site.register(BookInfo)
```

执行

python manage.py createsuperuser

访问http://127.0.0.1:8000/admin就可以登录

注意修改模型中encode('utf-8')



####自定义管理界面