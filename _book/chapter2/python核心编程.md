##python核心编程

#### is 和 ==

[Python中is和==的区别](http://www.cnblogs.com/CheeseZH/p/5260560.html)

is判断 id是否相同

== 判断是否值相同

```
# -*- coding:utf8 -*-
a = 1
c = 1
if a is c :
    print('True')
else:
    print('False')

e = 'nihao'
f = 'nihao'

if e is f :
    print('True')
else:
    print('False')
h=10000
m=10000
if h is m :
    print('True')
else:
    print('False')
```

结果： 注意并不是所有的数字的id都一样

```
True
True
False
```



#### 深拷贝、浅拷贝

[Python中深拷贝与浅拷贝的区别](https://blog.csdn.net/u014745194/article/details/70271868)

[Python-copy()与deepcopy()区别](https://blog.csdn.net/qq_32907349/article/details/52190796)

```
    在Python中对象的赋值其实就是对象的引用。当创建一个对象，把它赋值给另一个变量的时候，python并没有拷贝这个对象，只是拷贝了这个对象的引用而已。

    浅拷贝：拷贝了最外围的对象本身，内部的元素都只是拷贝了一个引用而已。也就是，把对象复制一遍，但是该对象中引用的其他对象我不复制

    深拷贝：外围和内部元素都进行了拷贝对象本身，而不是引用。也就是，把对象复制一遍，并且该对象中引用的其他对象我也复制。
```

```
$ python
Python 2.7.12 (default, Nov 20 2017, 18:23:56) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> a = [1,2,3]
>>> b = a
>>> id(a)
139971109799912
>>> id(b)
139971109799912
>>> import copy #导入copy模块
>>> c=copy.deepcopy(a)
>>> id(c)
139971109910576
>>> id(a)
139971109799912
>>> a is c #深拷贝
False
>>> c.append(5)
>>> print c
[1, 2, 3, 5]
>>> print a
[1, 2, 3]
```

**对于可变对象深浅拷贝**:

> =浅拷贝：值相等，地址相等 
>   copy浅拷贝：值相等，地址不相等 
>   deepcopy深拷贝：值相等，地址不相等

**对于不可变对象的深浅拷贝**：

> 不可变对象类型，没有被拷贝的说法，即便是用深拷贝，查看id的话也是一样的，如果对其重新赋值，也只是新创建一个对象，替换掉旧的而已。
>
> 一句话就是，不可变类型，不管是深拷贝还是浅拷贝，地址值和拷贝后的值都是一样的。



**copy 和 deepcopy的区别**

```
$ python
Python 2.7.12 (default, Nov 20 2017, 18:23:56) 
[GCC 5.4.0 20160609] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> a = [1,2,3]
>>> b = [4, 5, 6]
>>> c = [a,b]
>>> print c
[[1, 2, 3], [4, 5, 6]]
>>> a.append(9)
>>> print c
[[1, 2, 3, 9], [4, 5, 6]]
>>> import copy
>>> e= copy.deepcopy(c)
>>> print e
[[1, 2, 3, 9], [4, 5, 6]]
>>> a.append(7)
>>> print e				 # a修改了，e没有被修改
[[1, 2, 3, 9], [4, 5, 6]]
>>> f = copy.copy(c)
>>> print c
[[1, 2, 3, 9, 7], [4, 5, 6]]
>>> a.append(8)
>>> print c
[[1, 2, 3, 9, 7, 8], [4, 5, 6]]
>>> print f 					# a修改了，f也修改了
[[1, 2, 3, 9, 7, 8], [4, 5, 6]]
>>> 
```



#### 私有化

[python下划线](http://python.jobbole.com/81129/

* xx : 公有变量
* _xx: 单前置下划线，私有化属性或方法， form somemodule import * 禁止倒入，类对象和子类可以访问
* __xx: 双前置下划线，避免与子类中的属性命名冲突，无法在外部直接访问(名字重整机制所以访问不到)
* `__xx__`:python内置属性或者方法



注意对于`_xx` 变量，from somemodule import *   禁止导入，但是导入时按照 import somemodule 的方式导入的时候可以使用。 



**dir()**

dir(对象名) 可以打印出该对象的成员



#### property的使用

1.对于私有属性，如果想修改，应该添加setter 和 getter方法

```
#!/usr/bin/python3

class A(object):
	def __init__(self):
		self.__num = 0
	def getNum(self):
		return self.__num
	def setNum(self, value):
		self.__num = value;
```



2. 使用property升级getter和setter方法

```
#!/usr/bin/python3

class A(object):
	def __init__(self):
		self.__num = 0
	def getNum(self):
		print('----getNum-----')
		return self.__num
	def setNum(self, value):
	    print('----setNum-----')
		self.__num = value;
	
	num = property(getNum, setNum) #此时可以通过 . 操作符来访问函数
	
a = A()
a.num = 100
print(a.num)
```

结果

```
$ python b.py 
----setNum-----
----getNum-----
100
```



3.使用property的第二种方式 装饰器

```
#!/usr/bin/python3

class A(object):
	def __init__(self):
		self.__num = 0
	@property
	def num(self):
		print('----getNum-----')
		return self.__num
	@num.setter
	def num(self, value):
	    print('----setNum-----')
	    self.__num = value;
	
	
a = A()
a.num = 100
print(a.num)
```

结果

```
$ python b.py 
----setNum-----
----getNum-----
100
```

#### 装饰器

[廖雪峰python教程](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014318435599930270c0381a3b44db991cd6d858064ac0000)

在讨论装饰器之前先明确一点，如下方式可以给函数添加打印check的功能：

```
#!/usr/bin/python3

def func(f):
    def inner():
        print('--------check--------')
        f()
    return inner

def test():
    print('-------heihei------')

test=func(test)
test()
```

结果：

```
$ python f.py 
--------check--------
-------heihei------
```

在python中函数外面添加`@func`等价于 test=func(test)， 所以可以使用`@`符号替代原来代码：

```
#!/usr/bin/python3

def func(f):
    def inner():
        print('--------check--------')
        f()
    return inner

@func
def test():
    print('-------heihei------')

#test=func(test)
test()
```

结果：

```
$ python f.py 
--------check--------
-------heihei------
```





#### 对有参数的函数装饰

```
#!/usr/bin/python3

def func(f):
    def inner(i):
        print('--------check--------')
        f(i)
    return inner

@func
def test(i):
    print(i)

#test=func(test)
test(1)
```

结果

```
$ python g.py 
--------check--------
1
```

**通用的形式**

```
$ cat h.py 
#!/usr/bin/python3

def func(f):
    def inner(*arg, **kw):
        print('--------check--------')
        f(*arg, **kw)
    return inner

@func
def test(i,j,h):
    print(i)
    print(j)
    print(h)

#test=func(test)
test(1,2,3)
```

结果：

```
$ python h.py 
--------check--------
1
2
3
```



#### types.MethodType方法

动态添加方法时，给实例对象添加一个方法使用 types.MethodType()接口。

而不能使用如下这种方式：

```
class A(object):
	pass
	
def run(self):
	print(self)

p1 = A()
p1.run=run # 虽然p1对象中run属性已经指向了run函数，但是这句代码是错误的，
		   # 因为run属性指向的函数是后来添加的，当p1.run()执行时，并没有把p1当作参数产地进去
```

所以采用使用types.MethodType的方式

```
#!/usr/bin/python3
import types

class A(object):
    pass

def run(self):
    print('-----run----')

p = A()
p.run = types.MethodType(run, p)

p.run()
```

执行结果：

```
$ python k.py 
-----run----
```

####`__slots__`

[使用`__slots__`](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868200605560b1bd3c660bf494282ede59fee17e781000)

当我们定义了一个class，由于python是一个动态语言，所以创建了一个class的实例后，我们仍然可以给该实例绑定任何属性和方法.所以Python允许在定义class的时候，定义一个特殊的`__slots__`变量，来限制该class能添加的属性。

```
$ cat k.py 
#!/usr/bin/python3
import types

class A(object):
    __slots__ = {'test'}
    pass

def run(self):
    print('-----run----')

p = A()
p.run = types.MethodType(run, p)

p.run()
$ python k.py       # 执行错误，因为不允许__slots__中不包含run
Traceback (most recent call last):
  File "k.py", line 15, in <module>
    p.run = types.MethodType(run, p)
AttributeError: 'A' object has no attribute 'run'
```

