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
```
mysql> show tables;
+----------------------------+
| Tables_in_test3            |
+----------------------------+
| auth_group                 |
| auth_group_permissions     |
| auth_permission            |
| auth_user                  |
| auth_user_groups           |
| auth_user_user_permissions |
| bookinfo                   |
| booktest_heroinfo          |
| django_admin_log           |
| django_content_type        |
| django_migrations          |
| django_session             |
+----------------------------+
12 rows in set (0.00 sec)

mysql> desc bookinfo;
+----------+-------------+------+-----+---------+----------------+
| Field    | Type        | Null | Key | Default | Extra          |
+----------+-------------+------+-----+---------+----------------+
| id       | int(11)     | NO   | PRI | NULL    | auto_increment |
| btitle   | varchar(20) | NO   |     | NULL    |                |
| pub_date | datetime(6) | NO   |     | NULL    |                |
| bread    | int(11)     | NO   |     | NULL    |                |
| bcommet  | int(11)     | NO   |     | NULL    |                |
| idDelete | tinyint(1)  | NO   |     | NULL    |                |
+----------+-------------+------+-----+---------+----------------+
6 rows in set (0.00 sec)

```

##类的属性

* object: 是Manager类型的对象，用于与数据库进行交互
* 当定义模型类时没有指定管理器，则Django会为模型类提供一个名为object的管理器
* 支持明确指定模型类的管理器

```
class BookInfo(models.Model):
	...
	books = models.Manager();
```
当为模型类制定管理器之后，django就不再为模型类生成objects的默认管理器。

### 管理器Manager
* 管理器Django的模型进行数据库的查询操作的接口，Django应用的每个模型都拥有至少一个管理器
* 自定义管理器主要用于两种情况
* 情况一：向管理器类中添加额外的方法
* 情况二：修改管理器返回的原始查询集：重写get_queryset()方法

```
class BookInfoManager(models.Manager):
	def get_query(self):
		return super(BookInfoManager, self).get_queryset().filter(isDelete=False)

class BookInfo(models.Model):
	...
	books = BookInfoManager();
```

### 创建模型类对象的新方式
* 可以使用类方法来创建新对象
* 使用管理Manager来创建新对象，在管理Manager添加一个创建模型类的方法即可。

##查询数据
* 查询集
* 字段查询：比较运算符，F对象，Q对象

###查询集

* 在管理器上调用过滤器方法会返回查询集
* 查询集景观过滤器筛选后返回的新的查询集，因此可以写成链式过滤
* **惰性执行** 创建查询集不会带来任何数据库的访问，直到调用数据时才会访问数据库
* 何时对查询集求值：迭代，序列化，与if合并
* 返回查询集的方法，称之为过滤器

```
all()
filter()
exclude()
order_by()
values() # 一个对象构成一个字典，然后构成一个列表返回｀

#返回单个值的方法
get()
count()
first()
last()
exist()
```

####限制查询集
 按照列表操作来获取部分结果，但是不支持负索引。
####查询集缓存

###字段查询
* 语法 属性名称__比较运算符＝值
* 对于外键，使用属性名__id表示外键的原始值。
####比较运算符
#### 跨关联关系的查询
* 语法： 模型类名__<属性名>__<比较>
```

###聚合函数
* 使用aggregate()函数返回聚合函数的值
filter(heroinfo__hcontent__contains='八')
```
