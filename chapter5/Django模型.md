## Django模型

[Django模型](https://blog.csdn.net/u010505059/article/details/78641208#%E6%AF%94%E8%BE%83%E8%BF%90%E7%AE%97%E7%AC%A6)

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

#### 1.3 创建数据库

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

#### 1.4 创建应用

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

#### 1.5 定义模型和属性

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

#### 1.6 元选项

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

#### 1.7生成迁移和执行迁移

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

##2. 类的管理器属性

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

##3. 查询数据
* 查询集
* 字段查询：比较运算符，F对象，Q对象

[Django数据操作](http://www.cnblogs.com/pemp/p/6066727.html)

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

#### 限制查询集

- 查询集返回列表，可以使用下标的方式进行限制，等同于sql中的limit和offset子句
- 注意：不支持负数索引
- 使用下标后返回一个新的查询集，不会立即执行查询
- 如果获取一个对象，直接使用[0]，等同于[0:1].get()，但是如果没有数据，[0]引发IndexError异常，[0:1].get()引发DoesNotExist异常

####查询集缓存

- 每个查询集都包含一个缓存来最小化对数据库的访问
- 在新建的查询集中，缓存为空，首次对查询集求值时，会发生数据库查询，django会将查询的结果存在查询集的缓存中，并返回请求的结果，接下来对查询集求值将重用缓存的结果
- 情况一：这构成了两个查询集，无法重用缓存，每次查询都会与数据库进行一次交互，增加了数据库的负载

```
print([e.title for e in Entry.objects.all()])
print([e.title for e in Entry.objects.all()])12
```

- 情况二：两次循环使用同一个查询集，第二次使用缓存中的数据

```
querylist=Entry.objects.all()
print([e.title for e in querylist])
print([e.title for e in querylist])123
```

- 何时查询集不会被缓存：当只对查询集的部分进行求值时会检查缓存，但是如果这部分不在缓存中，那么接下来查询返回的记录将不会被缓存，这意味着使用索引来限制查询集将不会填充缓存，如果这部分数据已经被缓存，则直接使用缓存中的数据

### 字段查询

- 实现where子名，作为方法filter()、exclude()、get()的参数
- 语法：属性名称__比较运算符=值
- **表示两个下划线，左侧是属性名称，右侧是比较类型**
- 对于外键，使用“属性名_id”表示外键的原始值
- 转义：like语句中使用了%与，匹配数据中的%与，在过滤器中直接写，例如：filter(title__contains=”%”)=>where title like ‘%\%%’，表示查找标题中包含%的

#### 比较运算符

- exact：表示判等，大小写敏感；如果没有写“ 比较运算符”，表示判等

```
filter(isDelete=False)1
```

- contains：是否包含，大小写敏感

```
exclude(btitle__contains='传')1
```

- startswith、endswith：以value开头或结尾，大小写敏感

```
exclude(btitle__endswith='传')1
```

- isnull、isnotnull：是否为null

```
filter(btitle__isnull=False)1
```

- 在前面加个i表示不区分大小写，如iexact、icontains、istarswith、iendswith
- in：是否包含在范围内

```
filter(pk__in=[1, 2, 3, 4, 5])1
```

- gt、gte、lt、lte：大于、大于等于、小于、小于等于

```
filter(id__gt=3)1
```

- year、month、day、week_day、hour、minute、second：对日期间类型的属性进行运算

```
filter(bpub_date__year=1980)
filter(bpub_date__gt=date(1980, 12, 31))12
```

- 跨关联关系的查询：处理join查询 

  ​

  - 语法：模型类名 <属性名> <比较>
  - 注：可以没有__<比较>部分，表示等于，结果同inner join
  - 可返向使用，即在关联的两个模型中都可以使用

```
filter(heroinfo_ _hcontent_ _contains='八')1
```

- 查询的快捷方式：pk，pk表示primary key，默认的主键是id

```
filter(pk__lt=6)1
```

#### 聚合函数

- 使用aggregate()函数返回聚合函数的值
- 函数：Avg，Count，Max，Min，Sum

```
from django.db.models import Max
maxDate = list.aggregate(Max('bpub_date'))12
```

- count的一般用法：

```
count = list.count()1
```

#### F对象

- 可以使用模型的字段A与字段B进行比较，如果A写在了等号的左边，则B出现在等号的右边，需要通过F对象构造

```
list.filter(bread__gte=F('bcommet'))1
```

- django支持对F()对象使用算数运算

```
list.filter(bread__gte=F('bcommet') * 2)1
```

- F()对象中还可以写作“模型类__列名”进行关联查询

```
list.filter(isDelete=F('heroinfo__isDelete'))1
```

- 对于date/time字段，可与timedelta()进行运算

```
list.filter(bpub_date__lt=F('bpub_date') + timedelta(days=1))1
```

#### Q对象

- 过滤器的方法中关键字参数查询，会合并为And进行
- 需要进行or查询，使用Q()对象
- Q对象(django.db.models.Q)用于封装一组关键字参数，这些关键字参数与“比较运算符”中的相同

```
from django.db.models import Q
list.filter(Q(pk_ _lt=6))12
```

- Q对象可以使用&（and）、|（or）操作符组合起来
- 当操作符应用在两个Q对象时，会产生一个新的Q对象

```
list.filter(pk_ _lt=6).filter(bcommet_ _gt=10)
list.filter(Q(pk_ _lt=6) | Q(bcommet_ _gt=10))12
```

- 使用~（not）操作符在Q对象前表示取反

```
list.filter(~Q(pk__lt=6))1
```

- 可以使用&|~结合括号进行分组，构造做生意复杂的Q对象
- 过滤器函数可以传递一个或多个Q对象作为位置参数，如果有多个Q对象，这些参数的逻辑为and
- 过滤器函数可以混合使用Q对象和关键字参数，所有参数都将and在一起，Q对象必须位于关键字参数的前面

