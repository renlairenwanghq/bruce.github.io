# MySQL 

## 安装

```
sudo apt-get install mysql-server mysql-client1
```

### 管理服务

#### 启动

```
service mysql start1
```

#### 停止

```
service mysql stop1
```

#### 重启

```
service mysql restart1
```

#### 连接mysql,回车后输入密码

```
mysql -u用户名 -p1
```

#### 退出登录

```
quit 或 exit1
```

#### 远程连接

```
mysql -hip地址 -u用户名 -p1
```

> -h 后面写要连接的主机ip地址 
>   -u 后面写连接的用户名 
>   -p 回车后写密码

## 备份与恢复

### 数据备份

- 进入超级管理员

```
sudo -s1
```

- 进入mysql库目录

```
cd /var/lib/mysql1
```

- 运行mysqldump命令

```
mysqldump –uroot –p 数据库名 > ~/Desktop/备份文件.sql;
按提示输入mysql的密码12
```

### 数据恢复

- 连接mysql，创建数据库
- 退出连接，执行如下命令

```
mysql -uroot –p 数据库名 < ~/Desktop/备份文件.sql
根据提示输入mysql密码
```



## 库操作

#### 创建数据库

```
create database 数据库名 charset=utf8;1
```

#### 删除数据库

```
drop database 数据库名;1
```

#### 切换数据库

```
use 数据库名;1
```

#### 查看当前选择的数据库

```
select database();1
```

## 表操作

#### 查看当前数据库的所有表

```
show tables;1
```

#### 创建表

```
create table 表名(列名 类型);
    表示自动增长 auto_increment 
    主键 primary key 
    外键 foreign key(列名) references 外键所在表名(外表列名) 1234
```

> 例： 
>   create table students( 
>   id int auto_increment primary key, 
>   sname varchar(10) not null 
>   );

#### 修改表

```
alter table 表名 add|change|drop 列名 类型;1
```

#### 删除表

```
drop table 表名;1
```

#### 查看表结构

```
desc 表名;1
```

#### 更改表名称

```
rename table 原表名 to 新表名;1
```

#### 查看表的创建语句

```
show create table 表名;1
```

## 数据操作

### 增删改

#### 增加

```
全列插入    insert into 表名 values(...);
缺省插入    insert into 表名 (列...) values (值...);
同时插入多条数据
    insert into 表名 values(...),(...);
   或insert into 表名 (列...) values (值..),(值..)..;12345
```

- 主键列是自动增长，但是在全列插入时需要占位，通常使用0，插入成功后以实际数据为准

#### 修改

```
 update 表名 set 列=值,... where 条件;1
```

#### 删除

```
delete from 表名 where 条件; 1
```

- 最好使用逻辑删除

### 查询

```
select 列名 from 表名;1
```

- 列名是*的话，表示在结果中显示表中所有列
- 查询多个列用 , 隔开
- 在select后面列前使用distinct可以**消除重复的行**

#### 条件

```
select 列名 from 表名 where 条件;1
```

##### 比较运算符

> 等于 = 
>   大于 > 
>   大于等于 >= 
>   小于 < 
>   小于等于 <= 
>   不等于 != 或 <>

##### 逻辑运算符

> and 
>   or 
>   not

##### 模糊查询

> like 
>   %表示任意多个任意字符 
>   _ 表示一个任意字符 
>   例：`select * from students where sname like '陈%';`

##### 范围查询

> in表示在一个非连续的范围内 
>   例：`select * from students where id in(1,3,8);`
>
> between … and …表示在一个连续的范围内 
>   例：`select * from students where id between 3 and 8;`

##### 空判断

> 注意：null与”是不同的 
>   判空is null 
>   例：`select * from students where hometown is null;`
>
> 判非空is not null 
>   例：`select * from students where hometown is not null;`

##### 优先级

> 小括号  > not > 比较运算符 > 逻辑运算符 
>   and 比 or 先运算，如果同时出现并希望先算or，需要结合()使用

#### 聚合

##### count(列) 计算总行数

> count(*)表示计算总行数，括号中写星与列名，结果是相同的 
>   例：`select count(*) from students;`

##### max(列) 求最大值

> 例：`select max(id) from students where gender=0;`

##### min(列) 求最小值

> 例：`select min(id) from students where isdelete=0;`

##### sum(列) 求和

> 例：``select sum(id) from students where gender=1;`

##### avg(列) 求平均值

> 例：`select avg(id) from students where isdelete=0 and gender=0;`

#### 分组

- 按照字段分组，表示此字段相同的数据会被放到一个组中
- 分组后，只能查询出相同的数据列，对于有差异的数据列无法出现在结果集中
- 可以对分组后的数据进行统计，做聚合运算

```
select 列1,列2,聚合... from 表名 group by 列1,列2,列3...;1
```

##### 分组后的数据筛选

```
select 列1,列2,聚合... from 表名
group by 列1,列2,列3...
having 列1,...聚合...;123
```

- having后面的条件运算符与where的相同

##### 对比where与having

> where是对 from 后面指定的表进行数据筛选，属于对原始数据的筛选 
>   having是对 group by 的结果进行筛选

#### 排序

```
select * from 表名 
order by 列1 asc|desc,列2 asc|desc,...;12
```

- 将行数据按照列1进行排序，如果某些行列1的值相同时，则按照列2排序，以此类推
- 默认按照列值从小到大排列
- asc从小到大排列，即升序
- desc从大到小排序，即降序

#### 获取部分行

当数据量过大时，在一页中查看数据是一件非常麻烦的事情

```
select * from 表名
limit start,count;12
```

- 从start开始，获取count条数据
- start索引从0开始

#### 完整的select语句

```
select distinct *
from 表名
where ....
group by ... having ...
order by ...
limit star,count;123456
```

- 执行顺序为：

```
from 表名
where ....
group by ...
select distinct *
having ...
order by ...
limit star,count1234567
```

- 实际使用中，只是语句中某些部分的组合，而不是全部

## 内置函数

### 字符串函数

#### 查看字符的ascii码值ascii(str)，str是空串时返回0

```
select ascii('a');1
```

#### 查看ascii码值对应的字符char(数字)

```
select char(97);1
```

#### 拼接字符串concat(str1,str2…)

```
select concat(12,34,'ab');1
```

#### 包含字符个数length(str)

```
select length('abc');1
```

#### 截取字符串

```
left(str,len)返回字符串str的左端len个字符
right(str,len)返回字符串str的右端len个字符
substring(str,pos,len)返回字符串str的位置pos起len个字符
select substring('abc123',2,3);1234
```

#### 去除空格

```
ltrim(str)返回删除了左空格的字符串str
rtrim(str)返回删除了右空格的字符串str
trim([方向 remstr from str)返回从某侧删除remstr后的字符串str，方向词包括both、leading、trailing，表示两侧、左、右
select trim('  bar   ');
select trim(leading 'x' FROM 'xxxbarxxx');
select trim(both 'x' FROM 'xxxbarxxx');
select trim(trailing 'x' FROM 'xxxbarxxx');1234567
```

#### 返回由n个空格字符组成的一个字符串space(n)

```
select space(10);1
```

#### 替换字符串replace(str,from_str,to_str)

```
select replace('abc123','123','def');1
```

#### 大小写转换

```
lower(str)
upper(str)
select lower('aBcD');123
```

### 数学函数

#### 求绝对值abs(n)

```
select abs(-32);1
```

#### 求m除以n的余数mod(m,n)，同运算符%

```
select mod(10,3);
select 10%3;12
```

#### floor(n)，表示不大于n的最大整数

```
select floor(2.3);1
```

#### ceiling(n)，表示不小于n的最大整数

```
select ceiling(2.3);1
```

#### 求四舍五入值round(n,d)，n表示原数，d表示小数位置，默认为0

```
select round(1.6);1
```

#### 求x的y次幂pow(x,y)

```
select pow(2,3);1
```

#### 获取圆周率PI()

```
select PI();1
```

#### 随机数rand()，值为0-1.0的浮点数

```
select rand();1
```

还有其它很多三角函数，使用时可以查询文档

### 日期时间函数

#### year(date)返回date的年份(范围在1000到9999)

#### month(date)返回date中的月份数值

#### day(date)返回date中的日期数值

#### hour(time)返回time的小时数(范围是0到23)

#### minute(time)返回time的分钟数(范围是0到59)

#### second(time)返回time的秒数(范围是0到59)

```
select year('2016-12-21');1
```

- 日期计算，使用+-运算符，数字后面的关键字为year、month、day、hour、minute、second

```
select '2016-12-21'+interval 1 day;1
```

- 日期格式化date_format(date,format)，format参数可用的值如下

> 获取年%Y，返回4位的整数 
>   获取年%y，返回2位的整数 
>   获取月%m，值为1-12的整数 
>   获取日%d，返回整数 
>   获取时%H，值为0-23的整数 
>   获取时%h，值为1-12的整数 
>   获取分%i，值为0-59的整数 
>   获取秒%s，值为0-59的整数

```
select date_format('2016-12-21','%Y %m %d');1
```

#### 当前日期current_date()

```
select current_date();1
```

#### 当前时间current_time()

```
select current_time();1
```

#### 当前日期时间now()

```
select now();1
```

### 视图

对于复杂的查询，在多次使用后，维护是一件非常麻烦的事情 
解决：定义视图 
视图本质就是对查询的一个封装 
定义视图

```
create view stuscore as 
select students.*,scores.score from scores
inner join students on scores.stuid=students.id;123
```

视图的用途就是查询

```
select * from stuscore;1
```

### 事务

使用事务可以完成退回的功能，保证业务逻辑的正确性 
事务四大特性(简称ACID)

- 原子性(Atomicity)：事务中的全部操作在数据库中是不可分割的，要么全部完成，要么均不执行
- 一致性(Consistency)：几个并行执行的事务，其执行结果必须与按某一顺序串行执行的结果相一致
- 隔离性(Isolation)：事务的执行不受其他事务的干扰，事务执行的中间结果对其他事务必须是透明的
- 持久性(Durability)：对于任意已提交事务，系统必须保证该事务对数据库的改变不被丢失，即使数据库出现故障

要求：表的类型必须是innodb或bdb类型，才可以对此表使用事务 
查看表的创建语句

```
show create table students;1
```

修改表的类型

```
alter table '表名' engine=innodb;1
```

事务语句

```
开启begin;
提交commit;
回滚rollback;
```