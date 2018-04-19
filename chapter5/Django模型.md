## Django模型

### 1. ORM(对象－关系－映射)

[Django之ORM操作](http://www.cnblogs.com/sss4/p/7070942.html)

MVC框架中一个重要的组成部分，就是ORM,通过ORM实现了数据模型和数据库的解耦合。

ORM的主要任务是：

* 根据对象的类型生成表结构
* 将对象，列表的操作转为sql语句
* 将sql语句查询的列表转换为对象，列表

#### 1.1 使用mysql数据库

**虚拟环境中使用mysql**

```
$ pip install mysql-python
```

#### 1.2 修改数据库配置

[在django中使用MySQL数据库（一）](http://www.cnblogs.com/fengri/articles/django5.html)

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'test2', #数据库名
        'USER':'root',
        'PASSWORD':'123456',
        'HOST':'',
        'PORT':'',
    }
}    
```

#### 1.2 创建数据库

[ubuntu安装mysql数据库](https://www.linuxidc.com/Linux/2017-06/144805.htm)

```
$ mysql -uroot -p
Enter password: 
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.02 sec)

mysql> create database test2 charset=utf8;
Query OK, 1 row affected (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test2              |
+--------------------+
5 rows in set (0.00 sec)
```

#### 1.3 创建应用

```
$ python manage.py startapp booktest
```

**并在`setting.py`添加应用**

```
INSTALLED_APPS = ( 
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'booktest'
)
```

#### 1.4 定义模型和属性

**注意：**

属性的命名不能存在两个连续的下划线。

[Django中model的字段类型、选项、关系及操作](https://blog.csdn.net/gavinking0110/article/details/54412590)                            

关系的类型:

1. 一对多:models.ForeignKey(其他表)
2. 多对多:models.ManyToManyField(其他表)
3. 一对一:models.ManyToManyField(其他表)
* 一访问多　：对象.模型类小写_set


```
bookinfo.heroinfo_set
```

* 一访问一　：对象.模型类小写


```
heroinfo.bookinfo
```

* 访问id　：对象.属性_id


```
heroinfo.book_id
heroinfo.book.id
```

#### 1.5 元选项

[模型元选项](http://wiki.jikexueyuan.com/project/django-chinese-docs-18/2_1_3_Meta-options.html)

* 在模型类中定义类Meta，用于设置元信息
* 元信息db_table:定义数据表名称，推荐使用小写字母.


```
from django.db import models

# Create your models here.
class BookInfo(models.Model):
    btitle = models.CharField(max_length=20)
    bpub_date=models.DateTimeField(db_column='pub_date')
    bread=models.IntegerField(default=0)
    bcommet=models.IntegerField(null=False)
    idDelete=models.BooleanField(default=False)
    class Meta:
        db_table='bookinfo'

class HeroInfo(models.Model):
    hname=models.CharField(max_length=10)
    hgender=models.BooleanField(default=True)
    hcontent=models.CharField(max_length=1000)
    isDelete=models.BooleanField(default=False)
    book=models.ForeignKey(BookInfo)
```

* ordering 对象默认的顺序，获取一个对象的列表时使用：

```
ordering = ['-order_date']
```

它是一个字符串的列表或元组。每个字符串是一个字段名，前面带有可选的“-”前缀表示倒序。前面没有“-”的字段表示正序。使用"?"来表示随机排序。

#### 1.6 生成迁移和执行迁移

```
$ python manage.py makemigrations
$ python manage.py migrate
```

