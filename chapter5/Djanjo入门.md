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