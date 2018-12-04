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

“单下划线” 开始的成员变量叫做保护变量，意思是只有类对象和子类对象自己能访问到这些变量；
“双下划线” 开始的是私有成员，意思是只有类对象自己能访问，连子类对象也不能访问到这个数据。

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

python核心编程2中是如下描述的：

装饰器仅仅是用来“装饰“(或者修饰)函数的包装,返回一个修改后的函数对象,将其重新赋值原来的标识符,并永久失去对原始函数对象的访问。



简言之，python装饰器就是用于**拓展原来函数功能的一种函数**，这个函数的特殊之处在于它的返回值也是一个函数，使用python装饰器的好处就是在不用更改原函数的代码前提下给函数增加新的功能。

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

 #### 生成器

**列表生成式**

```
>>> L = [x*2 for x in range(5)]
>>> print L
[0, 2, 4, 6, 8]
```

**生成器**

通过列表生成式，我们可以直接创建一个列表。但是，受到内存限制，列表容量肯定是有限的。而且，创建一个包含100万个元素的列表，不仅占用很大的存储空间，如果我们仅仅需要访问前面几个元素，那后面绝大多数元素占用的空间都白白浪费了。

所以，如果列表元素可以按照某种算法推算出来，那我们是否可以在循环的过程中不断推算出后续的元素呢？这样就不必创建完整的list，从而节省大量的空间。在Python中，这种一边循环一边计算的机制，称为生成器（Generator）。

* next()
* send()

**将列表生成式的［］改成（），即可以创建一个生成器。**

generator保存的是算法，每次调用`next()`，就会计算出下一个元素的值，直到计算到最后一个元素，没有更多的元素时，抛出StopIteration的错误。也可以通过for循环来迭代输出。

```
>>> g = (x*2 for x in range(5))
>>> g
<generator object <genexpr> at 0x10ba67aa0>
>>> g.next()
0
>>> g.next()
2
>>> g.next()
4
>>> g.next()
6
>>> g.next()
8
>>> g.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

**如果一个函数定义中包含`yield`关键字，那么这个函数就不再是一个普通函数，而是一个generator**

generator的函数，在每次调用`next()`的时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

普通函数：

```
#!/usr/bin/python3

def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        print b
        a, b = b, a + b
        n = n + 1

fib(5)
```

结果

```
$ python a.py 
1
1
2
3
5
```

修改print为yield后，该函数则变为一个生成器，而且在调用next()时，会从yield返回

```
#!/usr/bin/python3

def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1

m = fib(10)

print(m.next()) ＃ 打印的是从yield返回的值
print(m.next()) ＃ 再次执行时从上次返回的yield语句处继续执行，然后又从yield处返回
print(m.next())
print('-'*20)
for x in m: ＃此处的m也是从上次的yield语句处继续执行。
	print x
```

结果

```
$ python b.py 
1
1
2
--------------------
3
5
8
13
21
34
55
```

**生成器调用send**

同next()，在每次调用时候执行，遇到`yield`语句返回，再次执行时从上次返回的`yield`语句处继续执行。

**注意**  在yield处阻塞，表示下面代码中 temp = yield i 这条赋值语句并没有执行，只是通过yield返回了i的值。

​	   且生成器如果没有调用next就想调用send，则第一次传入的参数必须是None。

```
#!/usr/bin/python3

def func():
    i = 0
    while i < 5:
        print('11111')
        temp = yield i
        print('------', temp)
        i+=1

t = func()
t.send(None)
print('='*20)
t.send('Haha')
print('='*20)
print(t.next())
print('='*20)
print(t.next())
```

结果

```
$ python c.py 
11111                # 没有调用next，所以在 yield处阻塞
====================
('------', 'Haha')   # 从yield处开始执行，因为调用了send，所以temp的值为Haha
11111				 #  经过一次循环后又阻塞在yield处
====================
('------', None)     # 只是调用了next，没有调用send，所以相当于传入了None
11111				 # 所以执行一次循环后阻塞在yield处，但是yield i 语句返回了2,所以输出2
2
====================
('------', None)
11111
3
```

**生成器的应用**

```
#!/usr/bin/python3

def test1():
    while True:
        print('----------1')
        yield None

def test2():
    while True:
        print('----------2')
        yield None

t1 = test1()
t2 = test2()

while True:
    t1.next()
    t2.next()
```

上述代码会一直输出 1, 2。

使用生成器可以做到，同时在执行 test1 和 test2 中的两个循环中代码。

####`类做装饰器`

定义一个类的时候，添加`__call__方法`可以通过对象名调用。

**`__call__方法`**

```
#!/usr/bin/python3

class Test(object):
    def __call__(self):
        print('----------test')

a = Test()
a()
```

结果

```
----------test
```

**类做装饰器**

```
# -*- coding:utf8 -*-
#!/usr/bin/python3

class Test(object):
    def __init__(self,func):
        print('----------初始化')
    def __call__(self):
        print('----------装饰')
        print('----------test')

@Test  #此处会调用__init__方法 相对于 test=Test(test)
def test():
    print('in test func')
print('-------------------------------')
test() # 此时的test已经经过装饰，所以会调用的__call__方法
```

结果

```
$ python d.py 
----------初始化
-------------------------------
----------装饰
----------test
```

#### 元类

通常，我们定义类来创建对象，但是现在我们知道类也是对象。那么是通过什么来创建类呢？答案就是元类。

type可以用来创建类，所以type()即是元类。

元类的主要作用是在创建类的时候自动改变类。

[廖雪峰python教程 元类](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/001386820064557c69858840b4c48d2b8411bc2ea9099ba000)

[非常好的元类介绍](https://www.jianshu.com/p/cec91b9ef2a4)

**类可以动态创建**

**使用type可以创建类**

type()既可以返回对象的类型，也可以创建类

返回对象类型

```
>>> type(100)
<type 'int'>
```

创建类

type(‘类名’ (父类的集合)), {成员的字典}) 可以创建一个类

```
>>> def test(self):
...     print('-----test')
... 
>>> ts = type('ts', (object,), {"test":test})
>>> a=ts()
>>> a.test()
-----test
```

**`__metaclass__属性`**

如果类中存在`__metaclass__`属性，则会根据该属性定义的元类在内存中创建一个类对象。



#### `__getattribute__`

[python3中__get__,__getattr__,__getattribute__的区别](http://www.cnblogs.com/Vito2008/p/5280216.html)

__getattribute__是访问属性的方法，我们可以通过方法重写来扩展方法的功能。

对于python来说，属性或者函数都可以被理解成一个属性，当获取属性或者方法时会调用到`__getattribute__`。

```
# -*-coding:utf8 -*-
class C(object):
    a = 'abc'
    def __getattribute__(self, *args, **kwargs):
        print("__getattribute__() is called")
        if args[0] == 'a':
            return object.__getattribute__(self, *args, **kwargs)
        else:
            print '调用函数foo()'
            return object.__getattribute__(self,'foo')
    def foo(self):
        return "hello"
    

c=C()
print(c.foo())
print(c.a)
```

结果

```
$ python f.py 
__getattribute__() is called
调用函数foo()
hello
__getattribute__() is called
abc
```



####python垃圾回收 gc

* 小整数对象池 [-5 257]
* intern机制
* 引用计数
* 隔代回收

#### 偏函数/functools

* 偏函数
* wraps    去除装饰的时候可能的副作用

#### 闭包

[python闭包](https://www.cnblogs.com/JohnABC/p/4076855.html)

#### `__name__ == '__main__'`

[如何简单地理解Python中的if __name__ == '__main__'](https://blog.csdn.net/yjk13703623757/article/details/77918633/)

通俗的理解`__name__ == '__main__'`：假如你叫小明.py，在朋友眼中，你是小明`(__name__ == '小明')`；在你自己眼中，你是你自己`(__name__ == '__main__')`。

`if __name__ == '__main__'`的意思是：当.py文件被直接运行时，`if __name__ == '__main__'`之下的代码块将被运行；当.py文件以模块形式被导入时，`if __name__ == '__main__'`之下的代码块不被运行。